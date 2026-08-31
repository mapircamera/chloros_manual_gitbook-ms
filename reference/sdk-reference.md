# Chloros Python SDK Rujukan

**Versi:**

1.2.0**Dijana:**2026-07-29 19:19 ·**Disemak:** 2026-08-30**Pakej:** `chloros-sdk` (PyPI)**Kumpulan Sasaran:** Dinaikkan taraf untuk penggunaan LLM; boleh dibaca manusia.**Skop:** Setiap kelas awam, fungsi, dan pembantu yang didedahkan oleh `import chloros_sdk`, dengan contoh yang boleh disalin-tampal merangkumi pemprosesan imej, kawalan kamera tunggal, tatasusunan selari, penderia DAQ, dan automasi projek.

Jika anda hanya memerlukan perkara utama, lompat ke:
- [Pemasangan &amp; Permulaan Cepat](#installation)
- [Smart-Connect untuk Susunan LATTICE](#smart-connect-for-lattice-cameras)
- [Sesi Penderia DAQ](#daq-sensor-sessions)
- [Automasi Projek](#project-automation--chlorosproject)
- [Smart-AE / Smart-Capture](#smart-ae--smart-capture)

---

## Seni Bina dalam 60 Saat

SDK adalah lapisan Python nipis di atas backend Chloros (pelayan Flask yang sama digunakan oleh GUI desktop dan CLI). Untuk automasi, anda mengimport `chloros_sdk` dan memanggil kaedah aras tinggi; di sebalik tabir, setiap panggilan menjadi permintaan HTTP ke backend tempatan pada port 5000 — `http://127.0.0.1:5000/api/...` (dengan sengaja bukan `localhost`, yang pertama diselesaikan ke `::1` pada Windows dan menelan kos ~2 s setiap permintaan terhadap backend IPv4 sahaja). Backend memiliki kolam perkakasan — kamera, penderia DAQ, profil penjajaran, penimbal bingkai — jadi skrip SDK boleh wujud bersama GUI tanpa berebut port bersiri atau jalur lebar NIC.

Terdapat tiga permukaan yang akan anda gunakan:

1. **`ChlorosLocal` + fungsi percuma** (`process_folder`, `process_lattice_capture`) — Saluran pemprosesan imej. Jalankan keseluruhan folder melalui penentukuran / debayer / eksport indeks daripada satu panggilan Python.
2. **Pemegang Smart-connect** (`connect_camera`, `connect_array`, `connect_daq_sensor`) — Membuka sesi backend berterusan untuk perkakasan secara langsung. Aliran &quot;smart-prep&quot; yang sama seperti GUI: pengesanan rangkaian, pemilihan lapisan automatik, PTP, penaburan AE, konfigurasi pencetus GPIO.
3. **`ChlorosProject` / `open_project`** — Memuatkan projek yang disimpan (folder dengan `cameras.json` + `sensors.json` + `project.json`), sambungkan semuanya sekaligus, dan jalankan tangkapan dengan pemegang bernama.

Permukaan 1 dan 2 **memulakan secara automatik backend tempatan** jika tiada yang sedang mendengar (binari terbungkus yang sama yang dicipta oleh GUI/CLI) — jadi skrip kosong berfungsi daripada shell baru tanpa anda memulakan backend terlebih dahulu. Pass `auto_start_backend=False` untuk tidak menyertainya (contohnya apabila menunjuk ke backend jauh, yang tidak pernah diaktifkan). Lihat [Permulaan Automatik Backend](#backend-auto-start). Surface 3 berkelakuan berbeza: `open_project()` tidak mengambil sebarang parameter `auto_start_backend`, dan `connect_all()` tidak pernah memulakan backend — ia menyemak `http://127.0.0.1:5000` sekali dan, jika tiada yang menjawab, secara senyap kembali kepada direct (tanpa backend) kawalan peranti `lattice_sdk`. Hanya `proj.process()` dan `stream(..., overlays=True)` secara malas membina `ChlorosLocal()` (yang secara automatik-start).

Ketiganya dikawal oleh kebenaran: jalankan `chloros-cli login` sekali pada mesin, atau log masuk melalui GUI desktop. PanggilanSDKtanpa sesi yang sah akan menimbulkan `ChlorosAuthenticationError`.

Keperluan:
- Python 3.7+ (seperti yang dinyatakan oleh pakej; dibangunkan/diuji pada 3.10)
- Desktop Chloros dipasang secara tempatan (binari backend disertakan dalam pemasang)
- Log masuk Chloros+ yang aktif. Tahap SDK / CLI adalah **Copper**atau lebih tinggi (Copper / Bronze / Silver / Gold); tahap**Iron**percuma tidak mempunyai akses SDK / CLI. Ini dikuatkuasakan**di pihak pelayan**: setiap permintaan yang ditandakan dengan SDK / CLI mesti membawa kedua-dua sesi langsung dan pelan berbayar, jika tidak backend akan mengembalikan `403` dengan `error_code: PLAN_UPGRADE_REQUIRED` (diperlihatkan sebagai `ChlorosLicenseError` oleh `ChlorosLocal`, dan sebagai `ChlorosConnectError` oleh pembantu `connect_*`). Pemanggil yang log keluar menerima `401` / `AUTH_REQUIRED` (`ChlorosAuthenticationError`) Sebaliknya — kedua-duanya berbeza kerana menjalankan semula `chloros-cli login` membetulkan yang pertama tetapi tidak dapat membetulkan yang kedua.
- Penggunaan luar talian disokong dalam tempoh moratorium pelan: peringkat dibaca daripada cache pengesahan pelayan (5 minit) atau cache lesen bertandatangan yang terikat pada mesin (30 hari untuk pelan bulanan, sehingga tamat langganan untuk tahunan). Apabila tempoh lanjutan itu tamat, pelan bertukar kepada percuma dan capaian SDK / CLI berhenti sehingga mesin dapat mencapai pelayan sekali. `chloros-cli status` (`GET /api/license-status`) kekal boleh diakses pada peringkat percuma supaya sebabnya dapat dilihat — ia adalah satu-satunya laluan SDK / CLI yang dikecualikan daripada pintu peringkat.
- Windows 10/11 64-bit, **Ubuntu 22.04 LTS atau lebih baru**, atau Jetson (JetPack 6). Ubuntu 20.04**tidak** disokong: kebergantungan `.deb` diperoleh daripada apa yang dikaitkan oleh backend, termasuk `libc6 (>= 2.34)`, dan focal dihantar dengan glibc 2.31.

---

## Pemasangan

SDK Python adalah lapisan nipis Python di atas backend Chloros. Untuk semua perkara melebihi beberapa aliran kerja DAQ sahaja, anda perlu memasang pakej desktop **Chloros secara tempatan** (pemasang Windows atau Linux `.deb`) — ia menyediakan binari backend, runtime Arena SDK untuk kamera LATTICE, dan bundel penentukuran.

Muat turun terkini: [`https://mapir.gitbook.io/chloros/download`](https://mapir.gitbook.io/chloros/download)

### Langkah 1 — Pasang pakej platform Chloros

#### Windows (.exe)

1. Muat turun `Chloros-Setup-x.y.z.exe` daripada halaman muat turun.
2. Jalankan pemasang dan ikut penolong. Laluan pemasangan lalai ialah `C:\Program Files\MAPIR\Chloros\`.
3. Buka Chloros sekurang-kurangnya sekali dan log masuk dengan akaun Chloros+ anda.

#### Linux amd64 (.deb)

```bash
sudo dpkg -i chloros-amd64.deb
sudo apt-get install -f         # only if dpkg reports missing dependencies
chloros-cli --version
chloros-cli login user@example.com 'YourPassword'
```

#### Linux arm64 — Jetson (JetPack 6)

```bash
sudo dpkg -i chloros-arm64-jp6.deb
sudo apt-get install -f
chloros-cli --version
chloros-cli login user@example.com 'YourPassword'
```

### Langkah 2 — Pasang Python SDK

**Pemasang Chloros disertakan dengan roda SDK yang sepadan.** Setiap pemasang Windows dan .deb Linux meletakkan `chloros_sdk-X.Y.Z-py3-none-any.whl` pada cakera yang tepat sepadan dengan versi GUI / CLI / backend. Anda tidak perlu mengejar PyPI untuk kekal selari.

#### Windows

Pemuat turun akan menjalankan `pip install` secara automatik terhadap wheel yang disertakan menggunakan Python sistem anda (pelancar `py.exe` diutamakan, jika tidak, akan menggunakan `python -m pip`). Tiada tindakan diperlukan — `import chloros_sdk` berfungsi dalam persekitaran Python anda selepas pemasangan berjaya. Jika tiada Python pada komputer, pemasang akan secara senyap melangkau langkah ini dan GUI + CLI terus berfungsi.

#### Linux (.deb)

Fail .deb meletakkan roda pada `/usr/lib/chloros/sdk/`. `postinst` mencetak arahan tepat — PEP 668 distros menolak penulisan global pip secara lalai, jadi kami tidak secara automatik-install:

```bash
pip install --user /usr/lib/chloros/sdk/chloros_sdk-*.whl
```

Untuk penyebaran Jetson yang terpisah secara udara, ini sepenuhnya luar talian — wheel sudah ada di cakera.

#### PyPI Awam

Untuk hos yang hanya menggunakan pip (tiada pakej desktop Chloros dipasang; aliran kerja belakang jauh atau DAQ sahaja):

```bash
pip install chloros-sdk
```

PyPI dikemas kini pada binaan pemuat turun versi-pelepasan, supaya roda yang diterbitkan sepadan dengan pelepasan stabil terkini. Binaan pembangunan (contohnya `1.1.4.dev1`) hanya dihantar melalui roda pemuat turun terbungkus.

#### Verifikasi

```python
import chloros_sdk
print(chloros_sdk.__version__)
print("CAMERA_AVAILABLE =", chloros_sdk.CAMERA_AVAILABLE)
print("DAQ_AVAILABLE    =", chloros_sdk.DAQ_AVAILABLE)
print("PROJECT_AVAILABLE =", chloros_sdk.PROJECT_AVAILABLE)
```

> **Chloros+ langganan diperlukan.** Semua panggilan SDK memerlukan log masuk Chloros+ aktif. Jalankan `chloros-cli login user@example.com 'YourPassword'` sekali setiap mesin; kelayakan disimpan dalam cache di `~/.chloros/`.

### Adakah Saya Perlukan Pakej Desktop?

Pakej pip sahaja **tidak** mencukupi untuk kebanyakan aliran kerja. Berikut adalah apa yang diperlukan oleh setiap permukaan SDK:

| Permukaan SDK | Perlukan Pakej Desktop? | Kenapa |
| --- | --- | --- |
| `ChlorosLocal`, `process_folder`, `process_lattice_capture` | **Ya** | Memulakan secara automatik binari backend di `/usr/lib/chloros/chloros-backend` (Linux) atau `C:\Program Files\MAPIR\Chloros\…` (Windows). |
| `connect_camera`, `connect_array`, `connect_daq_sensor`, `analyze_array_network`, `list_*`, `discover_*` | **Ya**(lokal)**/ Tidak**(jauh) | Klien HTTP tulen melalui backend. Backend tempatan → pakej desktop diperlukan. Backend jauh → `backend_url=`**melalui terowong** (lihat Mod Backend Jauh — backend yang disertakan hanya mengikat gelung balik). |
| `ChlorosProject` / `open_project` | **Ya** | Menggerakkan projek yang disimpan melalui backend. |
| Kelas LATTICE langsung (`LatticeCamera`, `CameraPool`, `Calibration`, `DLS`, …) | **Ya** | Perlu runtime asli Arena SDK yang disertakan dalam pakej desktop. `CAMERA_AVAILABLE` adalah `False` semasa import jika tidak. |
| Kelas DAQ langsung (`DAQUSensor`, `DAQMSensor`, `DAQESensor`, `SensorFleet`, `discover_all`) | **Tidak** | Pure Python melalui pyserial/bleak/zeroconf. Persekitaran pip-sahaja boleh mengendalikan DAQ dari hujung ke hujung. |

### Mod Backend Jauh (hos hanya pip, melalui terowong)

> **Backend yang disertakan tidak dapat dihubungi melalui LAN.** Versi
> produksi hanya mengikat sambungan ke loopback sahaja (kedua-dua keluarga loopback) dan secara tegas menolak
> satu-satunya mod bukan-loopback (`CHLOROS_CLOUD_MODE`), jadi `backend_url="http://<lan-ip>:5000"` **tidak boleh berfungsi dengan Chloros yang dipasang** — corak itu hanya pernah berfungsi dengan source/dev
> backend. Untuk memacu backend pada mesin lain, alihkan port loopback
> nya sendiri dan tujukan SDK ke terowong:

```bash
# on the pip-only host: forward local 5000 to the Chloros machine's loopback
ssh -N -L 5000:127.0.0.1:5000 user@chloros-host
```

```python
import chloros_sdk

BACKEND = "http://127.0.0.1:5000"   # the tunnel endpoint

chloros_sdk.connect_camera("213800234", backend_url=BACKEND)
chloros_sdk.connect_array(serials, backend_url=BACKEND)
chloros_sdk.connect_daq_sensor(eth_host="daq-e-1.local", backend_url=BACKEND)
```

Host tanpa kepala / CI / robotik boleh mengekalkan satu mesin dengan pemasangan desktop penuh sebagai &quot;pelayan Chloros&quot; dan `pip install chloros-sdk` di semua tempat lain — tetapi pengangkutan di antara mereka adalah pengguna-terowong yang diatur di atas, bukan URL LAN terus.

> **Had yang diketahui — `ChlorosLocal` tidak pip-hanya-mampu.** `ChlorosLocal(backend_url=BACKEND)` pada masa ini menyelesaikan binari backend tempatan dalam pembinaannya *sebelum* mengimbas URL, dan membangkitkan `ChlorosBackendError` (&quot;Belakang Chloros tidak ditemui…&quot;) apabila tiada pakej desktop dipasang — walaupun dengan belakang jauh yang boleh dicapai. Hanya permukaan smart-connect di atas (`connect_camera` / `connect_array` / `connect_daq_sensor`, ditambah `analyze_array_network` dan `list_*` / `discover_*`pembantu X) berfungsi daripada hos pip sahaja.

### Aliran Kerja Hanya DAQ (hos pip sahaja)

Jika anda hanya memerlukan sensor DAQ dan tidak menyentuh kamera LATTICE atau pemprosesan imej, pakej pip adalah lengkap sendiri:

```bash
pip install chloros-sdk
```

```python
from chloros_sdk import DAQUSensor, DAQMSensor, DAQESensor, discover_all

for d in discover_all(timeout=3.0):
    print(d.model, d.display, d.address)   # USB serials: d.extra.get("serial_number")

sensor = DAQUSensor(port="/dev/ttyUSB0")
sensor.connect()
sensor.start_streaming()
```

Tiada backend, tiada .deb, tiada log masuk Chloros+ diperlukan untuk kerja DAQ terus ke perkakasan.

---

## Mula Cepat

```python
import chloros_sdk

# === Image processing ===
results = chloros_sdk.process_folder(
    "C:/DroneImages/Flight001",
    indices=["NDVI", "NDRE", "GNDVI"],
)

# === Live LATTICE single-cam ===
with chloros_sdk.connect_camera("213800234") as cam:
    cam.set_settings(exposure_time=10000, gain=0.0)
    cam.capture("output/")

# === Live LATTICE synchronized array (GUI smart-prep flow) ===
with chloros_sdk.connect_array(
        ["213800234", "214000533", "214701288", "214701292"]) as arr:
    arr.capture("output/", processing="reflectance")

# === Live DAQ spectral sensor ===
with chloros_sdk.connect_daq_sensor() as daq:    # smart-detect USB / BLE / ETH
    for frame in daq.latest(n=5):
        print(frame["spectrum"][:10])

# === Drive a saved project end-to-end ===
proj = chloros_sdk.open_project("/path/to/project")
proj.connect_all()
proj.arrays["main_rig"].capture("output/", processing="reflectance")
proj.disconnect_all()
```

---

## Indeks Tahap Atas API

```python
import chloros_sdk

# === Image processing (full pipeline) ===
chloros_sdk.ChlorosLocal                          # class
chloros_sdk.process_folder(...)                   # one-shot helper
chloros_sdk.process_lattice_capture(...)          # LATTICE-friendly defaults
chloros_sdk.read_image_audit_tags(path)           # post-run audit

# === Live cameras (persistent backend pool) ===
chloros_sdk.connect_camera(serial, ...)           # → CameraSession
chloros_sdk.connect_array(serials, ...)           # → ArraySession (smart-prep)
chloros_sdk.attach_array(serials_or_id, ...)      # → ArraySession (attach without re-connecting)
chloros_sdk.list_cameras()
chloros_sdk.list_arrays()
chloros_sdk.discover_lattice_cameras()
chloros_sdk.analyze_array_network(...)            # network capability + recommendation
chloros_sdk.CaptureResult                         # list subclass returned by ArraySession.capture
chloros_sdk.RecorderHandle                        # handle for an array record()/burst() job

# === Live DAQ sensors (persistent backend pool) ===
chloros_sdk.connect_daq_sensor(...)               # → DAQSensorSession
chloros_sdk.discover_daq_sensors()                # scan USB/BLE/ETH (finds a DAQ-M MAC)
chloros_sdk.list_daq_sensors()

# === Project lifecycle ===
chloros_sdk.open_project(path)                    # → ChlorosProject
chloros_sdk.ChlorosProject                        # class
chloros_sdk.AlignmentSpec                         # dataclass
chloros_sdk.ArrayHandle, CameraHandle, SensorHandle

# === Direct-hardware (no-backend) classes (from lattice_sdk / daq_sdk) ===
chloros_sdk.LatticeCamera, CameraSettings, PRESETS, CameraPool
chloros_sdk.Calibration, CalibrationCoefficients, FilterModel, list_filters
chloros_sdk.DLS, NetworkDiagnostics
chloros_sdk.DAQUSensor, DAQMSensor, DAQESensor, SensorFleet, discover_all

# === Exceptions ===
chloros_sdk.ChlorosError                          # base
chloros_sdk.ChlorosBackendError
chloros_sdk.ChlorosLicenseError
chloros_sdk.ChlorosConnectionError
chloros_sdk.ChlorosProcessingError
chloros_sdk.ChlorosAuthenticationError
chloros_sdk.ChlorosConfigurationError
chloros_sdk.ChlorosConnectError                   # raised by smart-connect surface
chloros_sdk.LatticeError, CameraNotFoundError, ...  # from lattice_sdk

# === Availability flags ===
chloros_sdk.CAMERA_AVAILABLE     # True iff lattice_sdk imported cleanly
chloros_sdk.DAQ_AVAILABLE        # True iff daq_sdk imported cleanly
chloros_sdk.PROJECT_AVAILABLE    # True iff ChlorosProject deps available
```

---

## Pemprosesan Imej — `ChlorosLocal`

Kelas saluran utama. Memulakan backend pada penggunaan pertama, mencipta / mengkonfigurasi projek, memantau kemajuan, mengembalikan ringkasan selepas pelaksanaan.

### Pembina

```python
ChlorosLocal(
    api_url="http://127.0.0.1:5000",   # backend URL (also: backend_url=)
    auto_start_backend=True,            # spawn backend if not running
    backend_exe=None,                   # override backend binary path
    timeout=30,                         # request timeout seconds
    backend_startup_timeout=60,         # backend boot timeout
    processing_timeout=14400,           # hard cap on process() (4 h)
    processing_stuck_timeout=1800,      # no-progress threshold (30 min)
)
```

### Kaedah

| Kaedah | Deskripsi |
| --- | --- |
| `create_project(project_name, camera=None)` | Buat projek baru (secara pilihan dengan templat kamera seperti `"Survey3N_RGN"`). |
| `import_images(folder_path, recursive=False)` | Import imej RAW/TIF/JPG/DNG **dan rakaman sensor cahaya `.daq`**. Mengembalikan `count` (imej) dan `scan_count` (rakaman). Memberi amaran hanya jika folder tidak mengandungi kedua-duanya. |
| `export_light_sensor(daq=True, csv=True)` | Menulis kalibrasi `.daq` + `.csv` untuk setiap rakaman penderia cahaya dalam projek, ke dalam `<project>/Light Sensor/`. Lihat [Light-Sensor Recordings](#light-sensor-recordings--calibrated-daq--csv). |
| `configure(debayer=..., vignette_correction=..., reflectance_calibration=..., indices=[...], export_format=..., ppk=..., daq_log_path=..., input_level=..., radiometric_output=..., array_alignment=..., array_alignment_crop=..., array_alignment_interpolation=..., custom_settings=None)` | Tetapkan tombol pemprosesan. |
| `process(mode="parallel", wait=True, progress_callback=None, poll_interval=2.0)` | Jalankan paip. Mengembalikan `{"status": "complete", "async": False}`, serta kunci `summary` apabila backend menyediakan satu — lihat [Ringkasan &amp; Petunjuk Selepas-Jalankan](#post-run-summary--hints). |
| `get_config()` / `get_status()` / `status()` | Periksa status backend. |
| `logout()` | Padamkan kelayakan yang disimpan dalam cache. |
| `shutdown_backend()` | Mengakhiri backend (jika SDK -started). |
| `discover_cameras()` | Menemui kamera LATTICE **melalui backend instans ini** (`/api/camera/discover`). Mengembalikan senarai dict (`serial`, `model`, `ip`, …) — bentuk yang sama seperti GUI/CLI lihat. Senarai kosong jika tiada ditemui atau backend tidak dapat dicapai. |
| `camera_capture(output_dir, format="tiff", **settings)` | Tangkap satu bingkai tunggal**melalui backend**(dimulakan secara automatik oleh pemegang ini) supaya ia mendapat penyediaan yang sama seperti GUI/ CLI (lalai 12-bit, penggunaan semula kolam, metadata kal terbina dalam). Selesaikan sasaran dengan `serial=` atau `device_index=`; hantar `exposure`/`gain`/`pixel_format`/`preset` sebagai `**settings`. Mengembalikan dict metadata warisan (`filepath`, `width`, `height`, `pixel_format`, `exposure_time`, `gain`, `timestamp`). |
| `camera_stream(serial, *, fps=10.0, overlay=None, decode=True, connect_timeout=10.0, read_timeout=15.0)` | Menghasilkan bingkai pratonton yang digabungkan secara overlay daripada kamera yang dikumpulkan — klien MJPEG nipis melalui laluan `/api/camera/<serial>/stream-annotated` pada backend (zebra / grid / sasar silang / histogram / peaking / titik yang dilukis di pihak pelayan). `decode=True` menghasilkan susunan BGR; `False` menghasilkan bait mentah JPEG. Juga boleh diakses per-projek sebagai `ChlorosProject.stream(overlays=True)`. |

Gunakan sebagai pengurus konteks untuk pembersihan terjamin:

```python
with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("FieldA_2026-05-26", camera="Survey3N_RGN")
    cl.import_images("C:/DroneImages/Flight001")
    cl.configure(
        vignette_correction=True,
        reflectance_calibration=True,
        indices=["NDVI", "NDRE", "GNDVI"],
        export_format="TIFF (16-bit)",
    )
    results = cl.process(mode="parallel", wait=True)
print(results["summary"])
```

### Rakaman Penderia Cahaya — `.daq` + `.csv` yang dikalibrasi

DAQ-U / DAQ-M / DAQ-E boleh dirakam **tanpa** pek kalibrasinya. Itulah yang dilakukan oleh [`chloros_scripts`](https://github.com/mapircamera/chloros_scripts)
perekod (`record_daq.py`) melakukan secara lalai: mereka menulis kiraan sensor mentah dan menandakan fail supaya Chloros memuat turun penentukuran kilang sensor itu **mengikut siri** — cache tempatan
terdahulu, kemudian Awan MAPIR — dan menerapkannya semasa pengimportan.

Chloros menulis semula hasilnya sebagai dua produk bagi setiap rakaman, di bawah
`<project>/Light Sensor/`:

| Produk | Apa itu |
| --- | --- |
| `<name>_calibrated.daq` | Arkib yang boleh diproses semula — skema yang sama seperti rakaman langsung, kini menyatakan bundel yanghasilkannya. Mengimportnya semula **tidak** kalibrat sekali lagi. |
| `<name>_calibrated.csv` | Iradiasi spektral dalam W/m²/nm pada grid panjang gelombang sensor sendiri, satu baris bagi setiap bacaan, ditambah lajur fotometrik (kuasa total, fotopik/laks skotopik, PPFD dan pembahagian biru/hijau/merah serta panjang gelombang puncak). |
| `<name>_raw.daq` / `<name>_raw.csv` | **Hanya sensor tanpa bundel (DAQ-A).** Bilangan spektral mentah sensor — *bukan* iradiasi. Lihat di bawah. |

`process()` melakukan eksport ini sebagai salah satu peringkatnya. Ia **tidak** memerlukan imej:
penderia cahaya yang diterbangkan bersendirian adalah aliran kerja kelas pertama, dan projek seperti itu mempunyai sifar imej
dengan reka bentuknya.

**Rakaman DAQ-A dieksport sebagai kiraan mentah.** Keluarga DAQ-A wujud sebelum sistem bundel per-siri dan tiada bundel untuk diambil — ia dikalibrasi di lapangan terhadap sasaran pantulan sebaliknya, itulah sebabnya ia tidak pernah memerlukannya. Rakaman tersebut dieksport di bawah punca `_raw` dan bukannya `_calibrated`: nama fail yang berbeza dan bukannya penanda
dalam fail itu, kerana tuntutan itu perlu kekal walaupun dihantar melalui e-mel sebagai nama kosong. Header `.csv` menyatakan `raw spectral sensor counts (NOT irradiance)` dan memberi amaran bahawa nilai-nilai itu boleh dibandingkan **dalam**fail itu — tepat seperti yang digunakan oleh kalibrasi berasaskan sasaran — dan bukan merentas sensor. Baris fotometrik bergantung kuasa (kuasa total, lux fotopik/skotopik, PPFD) dikembalikan**NULL** dan bukannya diintegrasikan daripada kiraan.

DAQ-U / DAQ-M / DAQ-E yang bundlenya tidak dapat dimuat turun akan tetap **dilangkau**, bukan ditulis secara mentah: dalam kes itu, bundle tersebut wujud dan &quot;sambung semula dan proses semula&quot; adalah nasihat yang benar.

Rakaman warisan **v1.01 / v1.02** (yang ditulis oleh DAQ-A-SD) tidak mempunyai epoch bagi setiap bacaan, hanya masa penulisan fail. Pencocok imej↔downwelling masih menolaknya — memadankan a
frame terhadap masa tulis akan menjadi salah secara tidak kelihatan — tetapi pengeksport membacanya, dan CSV mencetak `clock=daq_created_on` supaya produk menyatakan jam mana yang digunakannya.

```python
import chloros_sdk

with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("DAQ-U_2026-08-26")
    cl.import_images("C:/Flights/raw_daq")     # .daq only — no camera involved
    result = cl.export_light_sensor()          # or just cl.process()

for rec in result["exported"]:
    print(rec["csv"])
for rec in result["skipped"]:
    print("skipped", rec["source"], "--", rec["reason"])
```

Sebuah rakaman yang bundel kalibrasinya tidak dapat diambil (luar talian, atau penderia tanpa kalibrasi dalam fail) dilaporkan di bawah `skipped` **dengan sebab**. Ia tidak pernah ditulis sebagai fail &quot;dikalibrasi&quot; yang mengandungi kiraan mentah — sambungkan ke internet dan jalankan semula, dan eksport akan selesai.

### Panggilan Balik Kemajuan

```python
def show_progress(percent, message):
    print(f"[{percent:3d}%] {message}")

with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("FieldA")
    cl.import_images("C:/DroneImages/Flight001")
    cl.configure(indices=["NDVI"])
    cl.process(progress_callback=show_progress, poll_interval=1.0)
```

### Ringkasan Selepas Jalankan &amp; Petunjuk

Setelah selesai, `process()` memuatkan `GET /api/processing-summary` dan melampirkan badan sebagai `result["summary"]`. Pemuatan ini adalah usaha terbaik dan tidak pernah menghalang pemulangan yang berjaya — jika ringkasan tidak tersedia, `process()` kembali ke bentuk `{"status": "complete", "async": False}` biasa. Setiap entri dalam `summary["hints"]` — ayat penuh dengan cadangan pembetulan, contohnya mengapa sesuatu larian menghasilkan keluaran sifar — juga dihantar semula sebagai Python `UserWarning`, jadi larian sifir-keluaran mendiagnosis sendiri walaupun anda tidak pernah memeriksa kamus:

```python
result = cl.process()
for hint in result.get("summary", {}).get("hints", []):
    print("HINT:", hint)
# hints also arrive on the warnings channel:
#   python -W always::UserWarning your_script.py
```

`summary["totals"]` adalah separuh yang boleh dibaca oleh mesin:

| Kunci | Apa yang dikira |
| --- | --- |
| `models` | Kumpulan kamera dalam larian. |
| `images_in_groups` | Imej sumber merentasi kumpulan-kumpulan tersebut. |
| `targets_found` | Sasaran pantulan yang dikesan. |
| `images_calibrated` | Imej yang dikalibrasi oleh larian. |
| `exported_files` | **Fail produk imej yang dihasilkan oleh larian.** |
| `daq_recordings_exported` / `daq_recordings_skipped` | Cahaya-rakaman sensor, dihitung secara berasingan dengan sengaja — ia berasal daripada peringkat yang berbeza dan wujud untuk larian tanpa sebarang imej, jadi menggabungkannya akan membuatkan larian hanya-DAQ kelihatan seperti mengeksport imej. |

Di sampingnya: `summary["output_dirs"]` (setiap direktori yang ditulis),
`summary["light_sensor_export"]`, `summary["stopped"]` (benar apabila pengguna memotong larian, supaya kiraan separa tidak terbaca sebagai larian yang selesai tetapi menghasilkan kurang daripada yang dijangkakan), dan
`summary["groups"]` (pecahan mengikut kumpulan).

`exported_files` direkodkan oleh saluran pemprosesan **semasa ia menulis**, bukan diimbas daripada objek imej projek kemudian. Strategi selari dan GPU membina objek imej mereka sendiri (dalam subproses pekerja untuk laluan GPU), jadi imbasan lama yang dilaporkan
`0 file(s) written` untuk setiap larian sedemikian dan kemudian mengeluarkan petunjuk sifar-eksport — pada larian di mana semuanya berjaya. Jika anda menulis skrip berdasarkan nombor ini, larian selari yang sihat kini melaporkan bilangan tidak sifar.

Laporan sensor cahaya melaporkan sebab sebenar yang ditetapkan oleh pembaca untuk setiap fail — skema yang tidak dapat dibaca, bundel yang hilang, ralat penulisan — **dihapuskan duplikat**, jadi dua puluh fail yang dilangkau kerana satu sebab dilaporkan sebagai satu sebab dan bukannya dua puluh ulangan sebab itu.

> **`process()` tidak diangkat apabila suatu pelaksanaan tidak menghasilkan sebarang imej.** Inilah satu-satunya tempat di mana SDK dan CLI secara sengaja berbeza: `chloros-cli process` menganggap &quot;produk telah diminta, tiada yang ditulis&quot; sebagai kegagalan dan keluar non-sifar, manakala SDK kembali dengan normal dan melaporkan keadaan tersebut melalui `summary` / petunjuk. Jika saluran paip anda sepatutnya berhenti pada larian kosong, semak sendiri — periksa `summary` (atau kira fail di bawah folder projek) daripada bergantung pada ketiadaan pengecualian. Punca biasa ialah folder input yang tidak diiktiraf sebagai tangkapan dan produk yang dilangkau kerana tidak terpakai untuk kamera yang ada (contohnya sinaran daripada kamera yang hanya mempunyai LATTICE).

### Fungsi Kemudahan

```python
# One-call process: project + import + configure + process
results = chloros_sdk.process_folder(
    folder_path="C:/DroneImages/Flight001",
    project_name="FieldA_2026-05-26",
    camera="Survey3N_RGN",
    indices=["NDVI", "NDRE", "GNDVI"],
    vignette_correction=True,
    reflectance_calibration=True,
    export_format="TIFF (16-bit)",
    mode="parallel",
    debayer="High Quality (Faster)",      # or "Texture Aware (Slow, Highest Quality)"
    ppk=False,
    recursive=False,
    processing_timeout=14400,
)

# LATTICE-friendly defaults (no panel-target detection, standard debayer)
results = chloros_sdk.process_lattice_capture(
    folder_path="C:/Captures/2026-05-13_Field",
    indices=["NDVI"],
)

# Audit which calibration sources were applied to a processed image
tags = chloros_sdk.read_image_audit_tags("output/Reflectance_Calibrated/x.tif")
print(tags["CalibrationSource"])   # 'per_serial' / 'legacy_lookup' / 'none'
print(tags["VignetteSource"])      # 'per_serial' / 'legacy_polynomial' / 'none'
```

### Nilai Disokong

```python
# export_format
"TIFF (16-bit)"           # default, recommended
"TIFF (32-bit, Percent)"  # reflectance percentage as float32
"PNG (8-bit)"
"JPG (8-bit)"

# debayer
"High Quality (Faster)"               # standard, default
"Texture Aware (Slow, Highest Quality)"  # neural debayer, Chloros+ only
"Standard (Fast, Medium Quality)"      # alias used internally for LATTICE

# input_level (LATTICE only; Survey3 .raw ignores)
"auto"        # default — infers from each file's XMP ProcessingLevel tag
"raw"         # force-treat as raw Bayer
"debayered"   # force-treat as already-debayered BGR
"processed"   # force-treat as already-calibrated radiance

# array_alignment / array_alignment_crop (LATTICE arrays; None = keep saved setting)
True          # backend default — apply the module-to-module transform stamped
              # in each capture's Chloros:Alignment* XMP to every product
False         # export in native sensor geometry / skip the common-overlap crop

# array_alignment_interpolation (alignment warp resampling)
"bilinear"    # backend default
"nearest"     # preserves exact source DNs (no inter-pixel value mixing)
"cubic"
```

#### Keluaran Radiometrik (saluran multispektral LATTICE)

Peringkat eksport multispektral LATTICE (M3C/M3M) saluran `process` — `reflectance` (lalai), `radiance`, `sensor-response`, atau `all` (setiap mod terpakai bagi setiap imej) — dipetakan kepada **&quot;Keluaran Radiometrik&quot;** projek tersebut tetapan pemprosesan. `configure()` mempunyai kata kunci khusus untuknya:

```python
with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("Field_A")
    cl.import_images("C:/Captures/lattice_flight")
    cl.configure(
        radiometric_output="radiance",   # reflectance (default) / radiance / sensor-response / all
        export_format="TIFF (32-bit, Percent)",
    )
    cl.process()
```

Lubang kecemasan lanjutan — menulis kunci `"Radiometric output"` projek melalui `custom_settings` — masih berfungsi, tetapi ingat ia menggantikan keseluruhan blok tetapan (lihat amaran di bawah):

```python
cl.configure(custom_settings={
    "Project Settings": {
        "Processing": {"Radiometric output": "radiance"},
        "Export": {"Calibrated image format": "TIFF (32-bit, Percent)"},
    }
})
```

`reflectance` (lalai) membahagikan sinaran kamera dengan **sinar ke bawah DAQ yang diselaraskan cap masa**, diselesaikan secara automatik daripada `.daq` yang dirakam (DAQ-U/M/E)**atau `.csv` asli DAQ-M**yang ditemui bersama imej; mana-mana bundel penentukuran per-kamera atau DAQ yang hilang secara tempatan akan**diperolehi secara automatik daripada AWS** pada penggunaan pertama. CLI mendedahkan ini seperti perproduk jenis -toggle pada `chloros-cli process`: `--radiance`/`--no-radiance`, `--reflectance`/`--no-reflectance`, `--debayered`, `--preview`.

> `custom_settings` **menggantikan** keseluruhan blok tetapan yang dikira (ia memintas kata kunci dan pengesahan `configure()` yang lain mengikut reka bentuk). Apabila anda menggunakannya, sertakan setiap kunci `Project Settings` yang anda ambil berat, seperti dalam contoh di atas.

---

## Smart-Connect untuk Kamera LATTICE

Sesi backend berterusan untuk perkakasan langsung. Titik hujung yang sama digunakan oleh GUI, jadi kelakuannya adalah sama di merentasi SDK / CLI / GUI.

### Kamera Tunggal — `CameraSession`

```python
import chloros_sdk

# Open by serial; reuses existing pool entry if one exists
with chloros_sdk.connect_camera("213800234") as cam:
    # cam is a CameraSession; supports context manager + manual disconnect
    cam.set_settings(
        exposure_time=10000,    # microseconds
        gain=0.0,               # dB
        pixel_format="BayerRG12",
        target_brightness=80,
        ae_damping=8.0,
    )
    cam.capture("output/", ext=".tiff")
```

#### Tandatangan `connect_camera()`

```python
connect_camera(
    serial,
    *,
    preset=None,                       # "default" | "high_quality" | "high_speed" | "triggered"
    settings=None,                     # dict overlaid on the preset
    backend_url="http://127.0.0.1:5000",  # deliberately not 'localhost' (::1-first on Windows ≈ 2 s/request)
    timeout=60.0,
    auto_start_backend=True,           # spawn a local backend if none is running
) -> CameraSession
```

#### `CameraSession` Kaedah

| Kaedah | Deskripsi |
| --- | --- |
| `read_nodes(names, enum_names=(), timeout=30.0)` | Baca nod GenICam; memulangkan `{nodes, errors, enums, device}`. |
| `set_settings(**kwargs)` | Menulis nod mengikut nama mesra (`exposure_time`, `gain`, `pixel_format`, `width`, `height`, `target_brightness`, `ae_damping`, `ae_upper_limit`, `trigger_mode`, `trigger_source`, …). |
| `capture(output_dir="output", ext=".tiff", jpeg_quality=95, processing=None, levels=None, force_daq=None, settings=None, timeout=None)` | Tangkap satu bingkai. Mengembalikan senarai satu elemen yang mengandungi kamus metadata bingkai. (Burst/perekodan berbilang bingkai telah dialihkan — panggil `capture()` dalam gelung jika anda memerlukan siri.) |
| `disconnect()` | Bebaskan daripada kolam. Tiada kesan jika kita disambungkan ke sesi yang sudah terbuka. |

`capture()` kawalan eksport (model yang sama seperti array + GUI):

- `processing` / `levels` — `processing="all"` menyimpan setiap jenis eksport yang terpakai; `levels=["raw","radiance"]` menyimpan hanya yang tersebut (mengatasi `processing`). Abaikan kedua-duanya untuk lalai backend.
- `force_daq=True` — simpan bacaan DAQ/DLS yang ditetapkan sebagai fail sampingan `.daq` walaupun pada tangkapan mentah sahaja, supaya bingkai boleh diproses semula menjadi pantulan/indeks kemudian. Tiada tindakan jika tiada DAQ yang dipautkan.

### Susunan Selaras — `ArraySession` (Smart-Prep)

`connect_array` adalah **titik permulaan yang disyorkan** untuk susunan pelbagai kamera. Ia menjalankan aliran penyediaan pintar GUI sepenuhnya di belakang tabir:

1. **Analisis rangkaian** (`/api/camera/array/recommend`) — mencari saiz bingkai terbesar yang muat dalam lapisan sim-emit tanpa menjatuhkan bingkai.
2. **Pilihan lapisan automatik** — `sim-capture-sim-emit` jika wayar boleh menanganinya; jika tidak `sim-capture-ftd-stagger` atau `slip-emit-and-capture`.
3. **Pengecilan automatik**— secara senyap mengecilkan saiz bingkai / meningkatkan binning apabila talian tidak dapat mengekalkan resolusi yang diminta.**Rangkaian keselamatan ini tidak merangkumi terlebih langganan agregat**: terlalu banyak kamera untuk wayar tidak boleh diperbaiki dengan mengecilkan bingkai — lihat [Over-Subscription](#over-subscription-the-per-cam-floor).
4. **PTP diaktifkan**secara lalai — cap masa merentas kamera mendarat pada satu jam bersamaan kepada**~1 ms**. Pendedahan serentak datang daripada pencetus perkakasan M8 (**&lt; 100 µs** antara modul), bukan daripada PTP: PTP menyelaras *cap masa*, bukan pendedahan.
5. **Pemilihan format piksel automatik bagi setiap kamera** — kamera RGB → `BayerRG8`, multispektral → `BayerRG12`.
6. **Penaburan AE** — mengambil imej keadaan AE semasa setiap kamera supaya sambungan tidak menetapkan semula pendedahan di tengah-tengah sesi.
7. **Konfigurasian pencetus GPIO** — `connect_array` mengaktifkan setiap kamera (`TriggerMode=On`, `TriggerSource=Line2`) supaya denyutan master memacu slave melalui kabel M8. Ini adalah langkah hanya untuk tatasusunan: Sebaliknya, satu kamera tunggal yang dibuka dengan `LatticeCamera` akan berjalan bebas.

```python
import chloros_sdk

# First serial is the MASTER (fires the trigger pulse); rest are slaves.
with chloros_sdk.connect_array(
        ["213800234", "214000533", "214701288", "214701292"]) as arr:
    print(arr.array_id, arr.sync_mode, arr.ptp_enabled)
    arr.capture("output/", processing="reflectance")
```

#### Tandatangan `connect_array()`

```python
connect_array(
    serials,                              # list[str]; serials[0] = master
    *,
    line="Line2",                         # GPIO sync line: Line0 | Line2 | Line3
    target_fps=None,                      # master trigger fire rate (auto if None)
    force_tier=None,                      # override tier picker; see below
    wire_ceiling_mbps=None,               # host sustained wire budget, MB/s (auto if None)
    width=None,                           # explicit frame size; skips network analysis
    height=None,
    pixel_format=None,
    binning=None,
    recommend=True,                       # set False to skip the recommend step
    ptp_enable=True,                      # set False to disable PTP
    backend_url="http://127.0.0.1:5000",  # same IPv6-avoidance default as connect_camera
    timeout=180.0,
    auto_start_backend=True,              # spawn a local backend if none is running
) -> ArraySession
```

Nilai `force_tier`:
- `"sim-capture-sim-emit"` — serentak sebenar (semua kamera mencetus pada tepi jam yang sama).
- `"sim-capture-ftd-stagger"` — penjajaran rentas masa yang fleksibel (kamera memancarkan pada masa yang sedikit berbeza supaya paket menjadi bersiri di talian).
- `"slip-emit-and-capture"` — tangkapan bersiri bagi setiap kamera (tiada penyelarasan masa; satu-satunya pilihan apabila tiada saiz bingkai sesuai untuk simulasi).

`wire_ceiling_mbps` mengatasi **belanjawan wayar berterusan hos** dalam MB/s — satu-satunya
nombor yang keseluruhan peruntukan tatasusunan bergantung padanya. Biarkan ia `None` untuk menggunakan nilai yang dikesan secara automatik. Turunkan ia apabila tatasusunan melaporkan bingkai rosak GVSP: nilai automatik itu diperoleh daripada kadar pautan yang diiklankan oleh NIC, yang melebih-lebihkan penyesuai USB, laluan PCIe yang sempit dan
fabrik kongsi yang sibuk — dan anggaran berlebihan itu muncul sebagai bingkai rosak dan bukannya sebagai
pautan yang perlahan secara nyata. Nilai ini disimpan dalam blok tangkapan tatasusunan projek, jadi a
membuka semula atau `connect_array` kemudian memulihkannya seperti mana-mana tetapan array lain.
Lihat [Kesihatan Array](#array-health--which-subsystem-is-losing-frames).

#### Melebihi Langganan (bawah per-kamera)

Sim-emit pacing memperuntukkan setiap kamera sebahagian daripada bajet wayar selamat perlanggaran, dengan had bawah **8 MB/s setiap kamera**(`per_cam_floor_bps`). Setelah `N × floor` melebihi had selamat-langgaran, susunan**melebihi langganan talian**— mod kegagalan adalah kehilangan pek GVSP, bukan kadar bingkai yang lebih rendah — dan tiada penawar saiz bingkai:**binning dan ROI menurunkan bait per bingkai, bukan bait per saat berpacu yang dibandingkan oleh pemeriksaan agregat**. Had penuh praktikal pada hos 1 GbE:**6 cams @ 1500 MTU, 9 dengan bingkai jumbo** (`max_cams_collision_safe` dalam laporan respons analisis melaporkan had untuk talian anda). Penyelesaian: kurangkan bilangan cam, gunakan bingkai jumbo dari hujung ke hujung, atau gunakan NIC yang lebih pantas.

- XPROTTindak balas X000320 dan `/api/camera/array/connect` membawa `oversubscribed`, `aggregate_demand_bps`, `collision_safe_ceiling_bps`, `max_cams_collision_safe`, dan `per_cam_floor_bps`. Apabila `oversubscribed` benar, projeksi **menyetingkan medan fps kepada sifar** (`achievable_fps_max` / `fps_bright` / `fps_dark`) daripada melaporkan yang mengelirukan perlahan-tetapi- kadar kerja.
- `POST /api/camera/array/connect` menerima param badan `pin_resolution` (**HTTP sahaja — bukan kwarg SDK**; `connect_array` tidak mendedahkannya). Pinning membuang rangkaian keselamatan penurunan peringkat binning, jadi sambungan yang terlebih langganan dengan `pin_resolution` ditetapkan**ditolak secara keras** dengan ralat yang menyenaraikan setiap penyelesaian. Tanpa pinning, pautan meneruskan walk-down tetapi memberi amaran bahawa pengecilan tidak dapat membersihkan agregat.
- Jalan keluar kerja makmal: tetapkan `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED=1` dalam persekitaran backend untuk menurunkannya kepada amaran kuat — anda terus membuat pautan dan menerima kehilangan paket.

#### Kesihatan Susunan — subsistem mana yang kehilangan bingkai

`GET /api/camera/array/<array_id>/capability` membawa blok `health` secara langsung pada susunan yang bersambung, dinilai semula pada tetingkap **10-saat** bergolek. Ia membahagikan kehilangan bingkai
ke dalam dua punca yang memerlukan pembetulan bertentangan, bukannya satu kadar &quot;tidak lengkap&quot; yang
tidak menyatakan kedua-duanya:

| Medan | Apa maksudnya | Subsistem mana |
| --- | --- | --- |
| `gvsp_corrupt_rate_pct` (per serial) | Bingkai **telah tiba dan bermasalah secara struktur**— kehilangan pekeliling GVSP. |**Rangkaian**: bajet wayar, pacing, cincin penerima NIC, MTU |
| `never_arrived_rate_pct` (per serial) | Bingkai **tidak pernah sampai langsung**— kamera tt api, atau tiada apa yang keluar daripadanya. |**Pemicu / selari**: kabel M8, `line=`, `TriggerMode` |
| `worst_gvsp_corrupt_pct` / `worst_never_arrived_pct` | Kadar terburuk bagi setiap kamera. | — |
| `per_cam_rate_pct` | Kadar tidak lengkap gabungan bagi setiap kamera (kedua-dua punca sekali). | — |
| `stable_for_seconds` | Berapa lama setiap kamera kekal di bawah 0.01 %. | — |

Di samping `health`, rekod yang sama melaporkan nombor yang menjadi pegangan keseluruhan peruntukan:

| Medan | Apa maksudnya |
| --- | --- |
| `wire_ceiling_mbps` | Belanjawan wayar terpelihara hos, MB/s. |
| `wire_ceiling_source` | Daripada mana angka itu datang, dalam kata-kata — contohnya `USB-capped 200 MB/s (was theoretical 1062; …)` atau `user override 120 MB/s (auto said 200)`. |
| `wire_ceiling_is_user_set` | `true` apabila `wire_ceiling_mbps=` menetapkannya. |
| `nic_is_usb` | `true` untuk penyesuai Ethernet USB. |

Tiada pembalut SDK untuk titik akhir ini — baca terus:

```python
import requests, chloros_sdk

arr = chloros_sdk.attach_array(["213800234", "214000533"])
h = requests.get(
    f"http://127.0.0.1:5000/api/camera/array/{arr.array_id}/capability",
    timeout=10).json()

health = h.get("health", {})
print("wire ceiling:", h["wire_ceiling_mbps"], "MB/s", h["wire_ceiling_source"])
print("corrupt (network) :", health.get("worst_gvsp_corrupt_pct"), "%")
print("absent  (trigger) :", health.get("worst_never_arrived_pct"), "%")

if (health.get("worst_gvsp_corrupt_pct") or 0) > 1.0:
    # Network path. Reconnect with a lower budget -- NOT a lower target_fps.
    arr.disconnect()
    arr = chloros_sdk.connect_array(serials, wire_ceiling_mbps=120)
```

**Membacanya:** `gvsp_corrupt_rate_pct` bukan sifar dengan `never_arrived_rate_pct` pada 0 bermaksud pencetus dan penyegerakan kabel adalah sempurna dan 100% kehilangan adalah pada laluan rangkaian — `wire_ceiling_mbps` dan sambung semula. Corak terbalik menunjukkan pada kabel penyelarasan atau
garis pencetus sebaliknya.

> **`target_fps` bukan pemicu untuk bingkai yang rosak.** Pacing GevSCPD ditulis sekali pada
> sambung, jadi menurunkan kadar pencetus mengubah kitaran tugas dan bukannya kadar letusan pelepasan serentak. Pengurangan permintaan 5× yang diukur tidak memberikan sebarang penambahbaikan, manakala
> menurunkan had wayar dari 240 ke 200 MB/s menjadikan rig yang sama berkurang daripada 10.4 % rosak kepada
> 0.00 %.

> **Pengecutan automatik pertengahan aliran tidak tersedia pada firmware TRI032S.** Susunan yang sedang berjalan tidak dapat
> membetulkan ini sendiri; putuskan sambungan dan sambungkan semula supaya pemilih masa sambungan merancang semula berdasarkan
> had baru.

Penyesuai Ethernet USB dihadkan pada 200 MB/s oleh probe tanpa mengira label namanya: jadual kecekapan yang menukarkan kadar pautan kepada angka berterusan adalah berasal daripada PCIe, dan NIC USB mengiklankan kadar pautan Ethernetnya sambil dibatasi oleh
pautan USB dan pemandunya. Had ini adalah mutlak, bukan pecahan — penyesuai USB 1 GbE menghasilkan ~80 MB/s dan tidak terjejas.

#### Kaedah `ArraySession`

| Kaedah | Deskripsi |
| --- | --- |
| `status(timeout=10.0)` | `{fps, ptp, frame_count, last_error, …}` Langsung. |
| `capture(output_dir="output", format="tiff", processing="debayered", levels=None, aligned=None, render_index=None, force_daq=None, smart=False, timeout=300.0)` | Satu kumpulan tangkapan diselaraskan. Mengembalikan `CaptureResult` (senarai frame dicts + `.skipped`). Kawalan eksport di bawah. |
| `capture(..., smart=True)` | **Perekodan pintar** — menunggu AE menstabilkan di semua kamera, kemudian mencetuskan. |
| `capture_fastest(output_dir="output", force_daq=True, render_index=True, timeout=120.0)` | Rakaman terpantas: hanya mentah + bacaan DAQ yang ditetapkan (+ indeks gabungan percuma). Meniru butang &quot;Fastest Capture&quot; GUI. |
| `capture_repeated(output_dir="output", count=None, duration_s=None, interval_s=0.0, on_capture=None, **capture_kwargs)` | Tunggal / Berterusan / Jarak dalam satu gelung terhad. Mengembalikan `list[CaptureResult]`.**Perlu `count` dan/atau `duration_s`** jadi ia menamatkan (SDK tidak mempunyai Ctrl+C). |
| `record(output_dir="output", fps=10.0, duration_s=None, video=True, gif=False, timeout=30.0)` | Mulakan rakaman paparan indeks gabungan secara langsung ke video/GIF → `RecorderHandle`. Satu perakam komposit bagi setiap susunan. |
| `burst(output_dir="output", duration_s=None, max_frames=None, index_config=None, serial_index_config=None, timeout=30.0)` | Mulakan burst raw-Bayer berkelajuan tinggi → `RecorderHandle`. Proses semula secara luar talian dengan `build_video()`. |
| `build_video(burst_dir, products=None, fps=10.0, video=True, gif=False, save_tiffs=False, wait=True, poll_s=2.0, timeout=1800.0)` | Proses semula secara luar talian burst raw yang disimpan menjadi video(s) yang dikalibrasi. Menghalang sehingga selesai (`wait=True`) dan memulangkan `{outputs, errors, combined}`. |
| `build_video_status(job_id, timeout=15.0)` | Semak kerja binaan luar talian: `{running, result, error, burst_dir}`. |
| `disconnect()` | Lepaskan keseluruhan tatasusunan. |

Kawalan eksport `capture()` (titik akhir yang sama digunakan oleh GUI/CLI):

- `processing` / `levels` — `processing="all"` (atau `levels=["raw","radiance",…]`) menyimpan setiap jenis eksport yang terpakai bagi setiap kamera; satu nilai `processing` hanya menyimpan tahap tersebut sahaja.
- `aligned=True` — memewarp setiap eksport bukan mentah setiap ahli ke [profil penjajaran](#array-alignment) (terdaftar bersama); data mentah kekal tidak diwarp tetapi membawa transformasi dalam metadata. Berpaling kepada tidak diselaraskan (dengan amaran yang dipaparkan dalam `alignment` hasil) jika susunan tiada profil.
- `render_index=False` — langkau lapisan indeks vegetasi bagi setiap kamera; lalai memaparkannya di mana ia dikonfigurasikan.
- `force_daq=True` — simpan bacaan DAQ/DLS yang diberikan sebagai fail sampingan `.daq` walaupun tiada aras yang dipilih memerlukannya.

**TIFF pemampatan (pomo hanya HTTP):**`ArraySession.capture()` tidak menghantar sebarang kunci `compression`, jadi lalai backend terpakai — `POST /api/camera/array/capture` membaca param badan `compression`, `"deflate"` secara lalai (tanpa kehilangan zlib L1 + peramal mendatar, ~4.1 MB setiap penuh-res frame). `"none"` menulis tanpa pemampatan (~6.3 MB/frame) dengan**penulisan ~5× lebih pantas** — kedua-duanya tanpa kehilangan dan dibaca secara identik semasa import. SDK tidak menyediakan sebarang kwarg untuknya; jalan keluar ialah `chloros-cli lattice array-capture --compression none` atau raw HTTP. DEFLATE juga memegang GIL Python, jadi penulisan mampat tidak dapat dijalankan secara selari merentasi per-benang penulis cam — tangkapan 8-cam penuh resolusi berterusan pada kadar sensor memerlukan `compression: "none"`. Butiran: [CLI Rujukan → array-capture](cli-reference.md).**Penimbal kuasa eksport per-ahli (HTTP sahaja):**titik akhir yang sama juga menerima `exclude_serials` (senarai — buang ahli daripada set yang disimpan; susunan masih mencetus sebagai satu kumpulan diselaraskan dan ahli yang dikecualikan dikembalikan dalam `excluded`), `serial_levels` (`{serial: [level tokens]}` penimbal aras per-kamera), dan `serial_index` (`{serial: bool}` keutamaan aras-kamera bagi indeks-overlay). Ini adalah parameter badan kesetaraan GUI dan**bukan kwargsSDK buat masa ini**; ahli yang tiada dalam peta akan kembali kepada `levels` / `render_index` merentasi tatasusunan.

##### Menyemak Kamera yang Dilangkau — `CaptureResult.skipped`

`ArraySession.capture()` mengembalikan `CaptureResult`, yang merupakan subkelas `list`: ulangi ia, indekskan ia, `len()` ia — setiap corak sedia ada terus berfungsi. Kod baru boleh memeriksa atribut `.skipped` untuk melihat kamera mana yang dikecualikan dan mengapa. Kes yang paling biasa ialah kamera XRGB dalam susunan penapis campuran apabila anda meminta `processing="radiance"` atau `"reflectance"` — sinaran per-Bayer tidak bermakna untuk sensor jalur lebar, jadi backend melangkau kamera-kamera tersebut daripada menghasilkan data yang tidak bermakna.

```python
with chloros_sdk.connect_array(serials) as arr:
    result = arr.capture("output/", processing="reflectance")

    # Back-compat: iterate as a plain list
    for frame in result:
        print(frame["filepath"], frame["serial"])

    # New: see why N-1 cams were saved
    for skip in result.skipped:
        print(f"skipped SN:{skip['serial']} reason={skip['reason']}")
        # e.g. {'serial': '214701292', 'level': 'reflectance',
        #       'reason': 'reflectance-not-applicable-to-rgb-cam',
        #       'filter': 'RGB'}
```

Token sebab mengikuti corak `<level>-not-applicable-to-rgb-cam` (satu entri bagi setiap tahap yang dilangkau, masing-masing membawa `level`). Lompatan khusus pantulan ialah `reflectance-skipped-no-fresh-dls` (tiada bacaan sinaran ke bawah baharu tersedia), `reflectance-skipped-bound-daq-unavailable (…)` (DAQ terikat tidak dapat dicapai), dan `dls-uncalibrated-band-<nm>` — jalur itu kebanyakannya terletak di luar julat kalibrasi radiometrik penderia cahaya DAQ (~374–974 nm), oleh itu, pembahagian pantulan mutlak berasaskan DAQ ditolak dan bingkai dengan kuat diturunkan ke tindak balas sensor. Antara SKU yang dikeluarkan, hanya F988 mencetuskan perkara ini; aliran kerja yang disokong oleh kamera itu ialah panel pantulan.

Peringkat `processing`:

| Tahap | Keluaran |
| --- | --- |
| `"raw"` | Bayer saluran tunggal (kamera mono: jalur tunggal) terus dari sensor. |
| `"debayered"` *(lalaiSDK)* | 3-saluran BGR melalui demosaik bilinear (kamera mono: skala kelabu 1-saluran). |
| `"radiance"` | float32 W/m²/sr/nm melalui rantaian radiometrik penuh. Multispektral sahaja — kamera RGB diabaikan. |
| `"reflectance"` | uint16 0..32768 (Sedia untuk Pix4D); memerlukan padanan DAQ secara langsung untuk rujukan mutlak. Multispektral sahaja. |
| `"display"` | Padanan rantaian penuh yang sepadan dengan Praperintian GUI (CCM + WB + gamma mengikut profil kamera). |
| `"all"` | **Satu fail bagi setiap tahap yang terpakai** untuk setiap kamera (menyesuaikan dengan lalai GUI &quot;Capture All&quot; / CLI). `CaptureResult` yang dikembalikan kemudian mengandungi satu frame dict bagi setiap `(cam, level)`, dengan tahap dalam setiap dict; tingkat yang tidak terpakai muncul dalam `.skipped`. Bacaan DAQ yang digunakan untuk mana-mana bingkai pantulan disimpan sebagai fail sampingan `.daq`. |

> **Nota — lalai berbeza daripada CLI.** `ArraySession.capture()` lalai kepada `processing="debayered"`; arahan `chloros-cli lattice array-capture` lalai kepada `processing="all"`. Hantar `processing="all"` secara eksplisit daripada SDK untuk mencerminkan simpanan pelbagai peringkat CLI/GUI.

### Mod Rakaman &amp; Perakam

Permukaan tatasusunan mencerminkan panel tangkapan GUI: mod Tunggal / Berterusan / Selang / Shutter Terpantas, serta dua perekod (video komposit langsung dan burst mentah → pemprosesan semula luar talian).

```python
import time, chloros_sdk

with chloros_sdk.connect_array(serials) as arr:
    # Single (default) — one synced group
    arr.capture("out/", processing="reflectance")

    # Fastest — raw + .daq + combined index now, calibrate later
    arr.capture_fastest("flightline/")

    # Interval — one reflectance pass every 2 s, 5 passes (bounded so it ends)
    arr.capture_repeated("timelapse/", count=5, interval_s=2.0,
                         processing="reflectance",
                         on_capture=lambda i, r: print(f"pass {i}: {len(r)} frames"))

    # Combined-index video/GIF recorder (needs the combined live view streaming)
    with arr.record("monitoring/", fps=10, gif=True) as rec:
        time.sleep(30)
    print(rec.result["video_path"])

    # Raw-Bayer burst → offline reprocess into calibrated video(s)
    with arr.burst("capture/", duration_s=5) as b:
        pass
    out = arr.build_video(b.result["out_dir"], products=[
        {"kind": "per_cam", "level": "reflectance"},
        {"kind": "combined", "level": "index"}])
    print(out["outputs"])
```

- **`capture_repeated`**ialah gelung Berterusan/Selang SDK. Kerana tiada `Ctrl+C` untuk menghentikannya daripada skrip, anda**mesti** menghantar `count` dan/atau `duration_s` (ia berhenti apabila salah satu daripadanya dicapai). `interval_s` diukur dari permulaan setiap pusingan (menyesuaikan dengan GUI). kwargs yang tinggal diteruskan terus ke `capture()`.
- **`record`** adalah *darjah pemantauan*: ia merakam komposit indeks gabungan langsung seperti yang dipaparkan, jadi aliran gabungan mesti dibuka untuk bingkai diterima. Satu perekod komposit bagi setiap tatasusunan (menghasilkan ralat jika sudah berjalan).
- **`burst` → `build_video`** adalah *penganalisisan-grade*: `burst` menulis bingkai mentah + manifest bagi setiap bingkai + satu `.daq` bagi setiap bacaan DLS berbeza di bawah `<output>/bursts/<base>/` pada kadar penuh gelung grab (tiada rantaian, tiada exiftool, tiada tontonan langsung). `build_video` memadankan masa setiap bingkai dengan `.daq` terdekat dan menjalankan semula paip import radiasi/refleksi/rantaian indeks. `products` adalah senarai `{"kind": "per_cam"|"combined", "level": "radiance"|"reflectance"|"index"}` (lalai: indeks gabungan). `burst().stop()` juga secara automatik memulakan pembinaan indeks gabungan secara usaha terbaik, yang dikembalikan sebagai `build_job` dalam keputusan henti.

#### `RecorderHandle`

Dipulangkan oleh `ArraySession.record()` dan `ArraySession.burst()`. Guna ia sebagai pengurus konteks untuk berhenti secara automatik apabila keluar daripada skop, atau kendalikannya secara manual.

| Ahli | Keterangan |
| --- | --- |
| `job_id` | ID kerja backend (str). |
| `kind` | `"composite"` (daripada `record`) atau `"raw"` (daripada `burst`). |
| `start_stats` | Dict yang dikembalikan oleh panggilan `start`. |
| `result` | `None` semasa berjalan; hasil henti akhir (stop-result dict) apabila dihentikan. |
| `stats(timeout=10.0)` | Statistik kerja langsung (bingkai yang ditulis, fps sebenar, masa berlalu). |
| `stop(timeout=60.0)` | Hentikan perakam; memulangkan dan menyimpan hasil akhir dalam cache. Idempotent (panggilan kedua akan memulangkan hasil yang disimpan dalam cache). |

```python
rec = arr.burst("capture/")
# ... drive manually ...
print(rec.stats()["frames"])
result = rec.stop()
print(result["out_dir"], result.get("build_job"))
```

### Menyambung ke Susunan yang Sudah Terhubung — `attach_array`

Jika susunan tersebut sudah berjalan (GUI membukanya, atau sesi SDK sebelum ini memanggil `connect_array`), gunakan `attach_array` untuk mendapatkan penangan kepadanya bukannya menyambung semula. `connect_array` sentiasa memberi ralat &quot;Kamera <sn>sudah berada dalam tatasusunan<id>&quot; dalam situasi itu, kerana menghantar POST `/array/connect` untuk member-in-pool tidak idempotent; `attach_array` membaca `/api/camera/array/list` dan memadankan sama ada melalui array_id atau nombor siri.

```python
import chloros_sdk

# By serials (matches if every serial is a member of one existing array)
arr = chloros_sdk.attach_array(
    ["213800234", "214000533", "214701288", "214701292"])

# By array_id (when you've already noted it down)
arr = chloros_sdk.attach_array("array-1779862544497")

# attach_array returns the same ArraySession as connect_array
arr.capture("output/", processing="reflectance")
```

Corak: Skrip SDK yang bersaing dengan GUI desktop harus mencuba `attach_array` dahulu dan beralih kepada `connect_array` jika tiada sebarang array lagi dalam pool.

```python
import chloros_sdk

try:
    arr = chloros_sdk.attach_array(serials)
except chloros_sdk.ChlorosConnectError:
    arr = chloros_sdk.connect_array(serials)
```

> **Penting — penamatan context-manager MEMANG memutuskan sambungan.**`ArraySession.disconnect()` sentiasa menghantar POST kepada `/array/disconnect`; tiada yang dilampirkan-not-owned guard seperti yang ada untuk `CameraSession` / `DAQSensorSession`. Jika anda berkongsi ruang dengan GUI dan tidak mahu membongkar array apabila keluar dari skop,**jangan gunakan blok `with`** — simpan pemegang dalam pembolehubah biasa dan biarkan `disconnect()` tersirat:
>
> ```python
> arr = chloros_sdk.attach_array(serials)
> arr.capture("output/", processing="reflectance")
> # … script ends; array stays up for the GUI
> ```

### Pembantu Analisis Rangkaian

Berguna sebelum membuka susunan — meramalkan sama ada tetapan yang dicadangkan akan muat:

```python
result = chloros_sdk.analyze_array_network(
    master_serial="214701288",
    slave_serials=["213800234", "214000533", "214701162"],
    width=2048, height=1536,
    pixel_format="BayerRG12",
    binning=1,
)

if result["status"] == "ok":
    print("Use the requested settings.")
elif result["status"] == "auto_capped_fps":
    r = result["recommended"]
    print(f"Keep the resolution; cap the trigger rate at {r['recommended_target_fps']} fps")
elif result["status"] == "auto_shrunk":
    r = result["recommended"]
    print(f"Shrink to {r['out_width']}x{r['out_height']} binning={r['binning']}")
elif result["status"] == "needs_force_slip":
    print("Sim-sync impossible on this wire; force_tier='slip-emit-and-capture' required")
```

`status` adalah salah satu daripada `ok` / `auto_capped_fps` / `auto_shrunk` / `needs_force_slip` (lainnya `error`). `auto_capped_fps` bermaksud resolusi yang diminta hanya sesuai dengan cincin RX pada kadar pencetus terhad — simpan resolusi dan pass `target_fps=result["recommended"]["recommended_target_fps"]` ke `connect_array` (lihat [Contoh 6](#6-capability-probe-before-connecting-a-4-cam-array)).

**Cara membaca projeksi** (model yang sama seperti panel Tetapan Susunan GUI):

- **Burst (`frame_bytes_total`) dijumlahkan bagi setiap kamera mengikut format piksel sebenar setiap kamera.**Kamera mono**M3M**menyalurkan Mono12 (2 B/px) tanpa mengira `pixel_format` yang anda hantar, jadi satu bingkai penuh-resolusi 4-kamera adalah**~25 MB** dengan tiga kamera mono, bukan ~12.6 MB seperti yang dijangka jika semua 8-bit. Backend menyelesaikan setiap kamera format daripada modelnya.
- **Admittance (`burst_fits_nic_ring`) sedar tentang saliran**, bukan keseluruhan-burst-berbanding-ring: sim-emit sesuai apabila hos mengosongkan RX ring lebih pantas daripada kamera-kamera mengisinya. Satu hos 10G + kamera 1 GbE**menerima* resolusi penuh walaupun burst melebihi cincin; hos 1 GbE menyekat (`needs_force_slip` / `auto_shrunk`).
- **`achievable_fps_max` adalah had konservatif pemulihan bersiri** — `max(readout+emit, N×emit)` dengan per-kamera emit dikekang pada pautan kamera 1 GbE, bebas daripada pendedahan. Contohnya ~2.8 fps untuk susunan 4-kamera penuh-resolusi 12-bit (menyesuaikan dengan anggaran runtime ~2.7–3.0). Model penuh: [CLI Rujukan → Model fps &amp; burst Array](cli-reference.md#array-fps--burst-model).
- **Pendaftaran berlebihan (`oversubscribed: true`) bermaksud N × lantai per-kamera melebihi siling selamat-langgaran** — medan fps (`achievable_fps_max` / `fps_bright` / `fps_dark`) dibaca 0, dan auto-shrink/binning tidak dapat membetulkannya (ia menurunkan bait per bingkai, bukan bait per saat yang dikawal kadar). Penyelesaiannya ialah mengurangkan bilangan kamera, menggunakan bingkai jumbo, atau menggunakan NIC yang lebih pantas; `max_cams_collision_safe` melaporkan had (6 kamera resolusi penuh pada 1 GbE @ 1500 MTU, 9 dengan jumbo). Respons juga membawa `aggregate_demand_bps`, `collision_safe_ceiling_bps`, dan `per_cam_floor_bps` (8 MB/s). Lihat [Over-Subscription](#over-subscription-the-per-cam-floor).

### Penemuan &amp; Penyenaraian

```python
chloros_sdk.discover_lattice_cameras()   # list all cams visible to the backend
chloros_sdk.list_cameras()               # cams currently in the pool
chloros_sdk.list_arrays()                # active arrays in the pool
```

---

## Smart-AE / Smart-Capture

Susunan LATTICE menjalankan AE berterusan di latar belakang sebaik sahaja ia disambungkan, tetapi babak yang baru diarahkan mengambil masa untuk mencapai konvergens. **Smart-capture** ialah kemudahan terbungkus: ia memantau pendedahan setiap kamera, menunggu sehingga susunan stabil merentasi tetingkap, kemudian mencetuskan tangkapan. Ia setara GUI: butang tangkapan &quot;pintar&quot; aplikasi desktop memanggil hujung titik belakang yang sama.

```python
import chloros_sdk

with chloros_sdk.connect_array([
        "213800234", "214000533", "214701288", "214701292"]) as arr:
    # Initial pose
    arr.capture("pose_a/", processing="reflectance", smart=True)
    input("Move the rig, then press Enter...")
    # New pose — smart-capture waits for AE to re-settle automatically
    arr.capture("pose_b/", processing="reflectance", smart=True)
```

Apabila memandu melalui `ChlorosProject` (seksyen seterusnya) anda mendapat lebih banyak tombol:

```python
proj.arrays["main_rig"].capture_smart(
    output_dir="out/",
    processing="reflectance",
    settle_timeout_s=5.0,           # max wait
    stability_window_s=1.5,         # exposure must hold steady this long
    exposure_tolerance_pct=5.0,     # %-spread allowed within the window
)
```

Dasar smart-AE secara lalai adalah konservatif. Ketatkan `exposure_tolerance_pct` untuk kerja radiometrik yang cerewet; longgarkan untuk babak yang berubah dengan pantas di mana anda hanya mahu &quot;cukup hampir.&quot;

---

## Sesi Sensor DAQ

Kolam backend berterusan untuk penderia spektral (DAQ-U melalui USB, DAQ-M melalui BLE, DAQ-E melalui Ethernet). Meniru permukaan kamera: pengesanan pintar, penggunaan semula kolam, sambungan idempoten.

### Smart-Detect (Konfigurasian Sifar)

```python
import chloros_sdk

with chloros_sdk.connect_daq_sensor() as daq:
    print(daq.model, daq.transport, daq.address)
    for frame in daq.latest(n=10):
        spectrum = frame["spectrum"]   # list[float] (W/m²/nm if calibrated)
        is_sat = frame["is_saturated"]
        x, y, z = frame["x"], frame["y"], frame["z"]
        print(len(spectrum), is_sat)
```

Keutamaan: Ethernet → BLE → USB. Nyatakan sebarang satu petunjuk eksplisit untuk menetapkan pengangkutan.

### Pengangkutan Terpaut

```python
# DAQ-U on a specific serial port
daq = chloros_sdk.connect_daq_sensor(transport="usb", port="COM3")

# DAQ-M over BLE by MAC (implies transport="ble")
daq = chloros_sdk.connect_daq_sensor(mac="AA:BB:CC:DD:EE:FF")

# DAQ-E over Ethernet by hostname (implies transport="eth")
daq = chloros_sdk.connect_daq_sensor(eth_host="daq-e-xxx.local")

# Tuning knobs
daq = chloros_sdk.connect_daq_sensor(
    port="COM3",
    integration_time=64,      # ms
    frame_avg=20,
    enable_ae=True,
    start_streaming=True,
)
```

### Kaedah `DAQSensorSession`

| Kaedah | Deskripsi |
| --- | --- |
| `status(timeout=10.0)` | Ringkasan kemasukan kolam (status penstriman/perekodan, julat panjang gelombang, sha penentukuran, masa integrasi, frame_avg, status AE). |
| `latest(n=1, timeout=10.0)` | Kembalikan sehingga N bingkai spektrum terkini. |
| `stream_start()` / `stream_stop()` | Sambung / rehatkan penstriman (tangan kekal terbuka). |
| `record_start(output_dir=None, device_name=None)` | Mulakan rakaman fail .daq. Mengembalikan laluan fail. Ditolak untuk DAQ-U/M tanpa bundel penentukuran AWS (DAQ-E dikecualikan). |
| `record_stop()` | Hentikan rakaman. Mengembalikan `{path, rows}`. |
| `disconnect()` | Lepaskan daripada kolam. Tiada tindakan untuk pemegang yang dilampirkan tetapi tidak dimiliki. |

> **Profil pembetulan had (`cap_id`) bukan tombol eSDK.** `connect_daq_sensor()` / `DAQSensorSession` tidak mendedahkan sebarang parameter `cap_id` atau kaedah `set_cap`. Pilih profil pembetulan had kapal melalui CLI (`chloros-cli daq pool-connect --cap-id …` / `chloros-cli daq pool-set-cap …`) atau laluan HTTP backend `/api/daq` (`/api/daq/connect` dan `/api/daq/<id>/cap-id` menerima `cap_id`).

### Penemuan — mencari alamat untuk disambungkan

`discover_daq_sensors()` mengimbas USB / BLE / ETH untuk penderia yang *boleh* anda buka. Ia adalah padanan DAQ kepada `discover_lattice_cameras()`, dan satu-satunya cara untuk mendapatkan **MAC BLE DAQ-M** — DAQ-E mempunyai nama hos dan DAQ-U mempunyai port COM, tetapi MAC tidak dicetak pada peranti mahupun disenaraikan oleh OS.

```python
for s in chloros_sdk.discover_daq_sensors():
    print(s["transport"], s["address"], s["model"], s["extra"])
# ble  C3:D8:85:E0:0A:19  DAQ-M  {'name': 'NSP32_SPECTRUM'}
# usb  COM3               None   {'manufacturer': 'Intel'}

# `address` is exactly what connect_daq_sensor wants:
for s in chloros_sdk.discover_daq_sensors(transports=["ble"]):
    if s["model"] == "DAQ-M":
        daq = chloros_sdk.connect_daq_sensor(mac=s["address"])
```

| Medan | Keterangan |
| --- | --- |
| `transport` | `usb` \| `ble` \| `eth`. |
| `address` | Port COM / MAC BLE / nama hos — lalui kepada `connect_daq_sensor` sebagai `port=` / `mac=` / `eth_host=`. |
| `display` | Label boleh dibaca manusia. |
| `model` | `DAQ-U` \| `DAQ-M` \| `DAQ-E`, atau `None` untuk port yang imbasan tidak dapat kenal pasti (penyesuai siri USB tidak dapat bezakan tanpa probe, jadi nilai tidak diketahui dipaparkan daripada disembunyikan). |
| `extra` | Butiran per-pengangkutan (nama diiklankan BLE, pengeluar USB, DAQ-E ip/fw/…). Nilai kosong diabaikan. |

| Parameter | Lalai | Keterangan |
| --- | --- | --- |
| `transports` | ketiga-tiganya | Susunan (atau rentetan csv) yang mengehadkan imbasan. Berbaloi digunakan apabila anda tahu apa yang anda mahukan — BLE adalah bahagian yang perlahan. |
| `scan_timeout` | 5 | Tetingkap imbasan bagi setiap pengangkutan dalam saat; backend mengehadkan kepada 1–20. |
| `timeout` | 60.0 | Had atas HTTP untuk keseluruhan panggilan (seperti di tempat lain dalam SDK). |
| `auto_start_backend` | `True` | Memulakan backend tempatan jika tiada yang sedang berjalan. Tidak pernah memulakan untuk `backend_url` jauh. |

> **Penderia yang sudah terbuka dalam kolam tidak akan muncul.** Periferal BLE yang disambungkan berhenti mengiklankan dan port COM terbuka boleht diimbas, jadi penemuan menyenaraikan apa yang *tersedia untuk disambungkan*. Keputusan kosong sejurus selepas anda menyambungkan sesuatu adalah dijangka — gunakan `list_daq_sensors()` untuk apa yang sudah anda pegang. Pengangkut yang imbasannya tidak dapat dijalankan (tiada bleak / zeroconf dipasang) akan dilangkau daripada menimbulkan ralat, jadi mesin tanpa Bluetooth masih menerima jawapan USB dan ETH.

### Penyenaraian

```python
for s in chloros_sdk.list_daq_sensors():
    print(s["sensor_id"], s["model"], s["transport"], s["wavelength_range"])
```

### Co-Tenancy dengan GUI / CLI

Jika GUI sudah mempunyai sensor terbuka, memanggil `connect_daq_sensor(port="COM3")` daripada Python mengembalikan handle yang ditandakan `already_connected=True`. `disconnect()` sesi itu kemudiannya menjadi no-op supaya skrip SDK anda tidak mencabut sensor daripada GUI semasa scope exit.

### Kelas Perkakasan Langsung (Tanpa Backend)

`daq_sdk` dieksport semula oleh `chloros_sdk` supaya anda juga boleh mengendalikan sensor secara menyeluruh dalam proses tanpa backend:

> **Ketersediaan:**`daq_sdk` disertakan dengan pemasangan desktop Chloros,**tidak** dengan pakej PyPI — `pip install chloros-sdk` memberikan anda `lattice_sdk` tetapi meninggalkan `chloros_sdk.DAQ_AVAILABLE == False`. Periksa penanda itu sebelum menggunakan kelas-kelas ini; pada hos yang hanya mempunyai pip, kendalikan sensor melalui [`connect_daq_sensor()`](#daq-sensor-sessions) sebaliknya, yang tidak memerlukan perpustakaan pengangkutan tempatan.

```python
from chloros_sdk import DAQUSensor, DAQMSensor, DAQESensor, discover_all

# Discovery
for d in discover_all(timeout=3.0):
    print(d.model, d.display, d.address)   # USB serials: d.extra.get("serial_number")

# Direct DAQ-U
sensor = DAQUSensor(port="COM3")
sensor.connect()
sensor.start_streaming()
# ... use sensor.add_spectrum_callback(...) ...
sensor.stop()
```

Utamakan laluan smart-connect (`connect_daq_sensor`) apabila anda mahu pemilikan bersama dengan GUI; gunakan kelas langsung untuk skrip tanpa papan pemuka yang memiliki sensor secara eksklusif.

---

## Automasi Projek — `ChlorosProject`

Projek Chloros yang disimpan adalah satu folder yang mengandungi `cameras.json` + `sensors.json` + `project.json`. `open_project` memuatkan manifes, dan `connect_all` memulihkan setiap peranti yang disimpan dalam talian dengan tetapan yang disimpannya — keadaan perkakasan yang sama seperti yang akan dihasilkan oleh GUI.

### Contoh Minimum

```python
import chloros_sdk

proj = chloros_sdk.open_project("/home/user/Chloros Projects/Field_A")
report = proj.connect_all(verbose=True)
print(report)  # {'cameras': {...}, 'arrays': {...}, 'sensors': {...}}

# Cameras and arrays are addressable by name OR serial / array_id
cam = proj.cameras["FrontLeft"]
cam.capture("./out", format="tiff", processing="reflectance")

arr = proj.arrays["main_rig"]
arr.capture("./out", format="tiff", processing="reflectance")

# Read a DAQ
spectrum = proj.sensors["Sky"].read()

# Trigger every device simultaneously
proj.capture_all("./out")

proj.disconnect_all()
```

Atau sebagai pengurus konteks:

```python
with chloros_sdk.open_project("/path/to/proj") as proj:
    proj.connect_all()
    proj.arrays["main_rig"].capture("./out", processing="reflectance")
```

### Kaedah `ChlorosProject`

| Kaedah | Deskripsi |
| --- | --- |
| `connect_all(cameras=True, arrays=True, sensors=True, verbose=False, align=None)` | Menemui + menyambung setiap peranti yang disimpan. Mengembalikan laporan sambungan bagi setiap kelas. Menggunakan backend yang sedang berjalan apabila ia mendengar pada `127.0.0.1:5000`; sekiranya tidak, secara senyap beralih kepada kawalan peranti `lattice_sdk` secara langsung (tanpa backend) — ia tidak pernah memulakan backend. |
| `disconnect_all()` | Membongkar semuanya. |
| `capture_all(output_dir=".")` | Satu bingkai daripada setiap kamera + tatasusunan + spektrum daripada setiap penderia. |
| `stream(camera, overlays=False, fps=10.0)` | Penjana yang menghasilkan bingkai BGR `numpy` daripada kamera (atau tatasusunan) yang dinamakan. `overlays=False` adalah gelung capai `lattice_sdk` secara langsung (deretan menghasilkan `{serial: frame}` dicts). `overlays=True` merutekan melalui `ChlorosLocal.camera_stream()` → aliran MJPEG `/api/camera/<serial>/stream-annotated` backend, dengan blok `ui.overlay` yang disimpan oleh kamera dihantar sebagai param pertanyaan. Memerlukan mod backend dan **kamera berdiri sendiri**: kamera mod langsung memicu `RuntimeError` (backend tidak dapat mengambil kamera yang dimiliki oleh proses ini) dan satu array memicu `NotImplementedError` (melapisi komposit bagi setiap kamera — siarkan ahli berdasarkan nama). Setara one-shot: `CameraHandle.capture(annotated=True)`. |
| `align_arrays(align=True, verbose=False)` | Jalankan penjajaran pada setiap yang kini-array yang disambungkan. |
| `process(mode="parallel", wait=True, progress_callback=None, poll_interval=2.0)` | Jalankan paip kalibrasi/indeks pada imej projek (meliputi `ChlorosLocal.process`; keempat-empat ini adalah satu-satunya kwargs yang diterima — `indices=` dan lain-lain memanggil `TypeError`; menetapkan indeks melalui `ChlorosLocal.configure()`). Secara malas membina `ChlorosLocal()`, yang secara automatik-memulakan backend. |

Atribut:
- `proj.cameras` — `Dict[str, CameraHandle]` berasaskan kunci oleh nama DAN serial.
- `proj.arrays` — `Dict[str, ArrayHandle]` berasaskan kunci oleh nama DAN array_id.
- `proj.sensors` — `Dict[str, SensorHandle]` kunci oleh nama DAN slot_id.
- `proj.config` — `project.json["config"]` dict.

### `CameraHandle`

```python
cam = proj.cameras["FrontLeft"]

# Save a frame to disk (processing-aware)
filepath = cam.capture(
    output_dir="./out",
    format="tiff",
    processing="radiance",           # see the level table below
    apply_calibration=True,          # DSNU + flat + 3x3 unmix + NIST
    apply_white_balance=True,        # DLS-aware WB
    apply_index=False,
    index_expression=None,
)

# In-memory grab (numpy array)
frame = cam.grab(processing="debayered")
frame, header = cam.grab(processing="radiance", with_metadata=True)

# Frame iterator (generator)
for arr in cam.frame_stream(processing="debayered", fps=5, count=100):
    my_analysis(arr)
```

**Tahap pemprosesan.** `capture()`, `grab()`, dan `frame_stream()` semuanya menggunakan token `processing` yang sama, dan rantaian adalah kumulatif — setiap tahap menjalankan semua yang di atasnya:

| Tahap | Keluaran | Nota |
| --- | --- | --- |
| `raw` | Bayer 1-saluran, asli sensor | Tiada demosaik. Tindihan tidak tersedia pada tahap ini. |
| `debayered` | BGR 3-saluran (**lalai**) | Demosaik bilinear. Satu-satunya tahap yang berfungsi tanpa mod backend. |
| `radiance` | float32, W/m²/sr/nm | Rantaian radiometrik penuh: demosaic + unmix 3×3 (multispektral) + DSNU + flat-field + skala NIST, dengan pendedahan × penguatan diasingkan supaya nilai adalah mutlak. |
| `reflectance` | uint16, 32768 = 1.0 | Radiasi dibahagikan oleh irradiasi ke bawah (ρ = π·L/E). Perlu bacaan DLS/DAQ — lihat nota di bawah. |
| `display` | 8-bit sRGB-ish | Render setara GUI: CCM + imbangan putih + gamma melalui profil warna aktif kamera. |

Apa-apa selain `debayered` memerlukan mod backend; a kamera mod-langsung membangkitkan
`NotImplementedError`. `reflectance` memerlukan bacaan downwelling yang boleh digunakan — titik akhir bingkai menarik
menarik DAQ yang dikumpulkan ke slot DLS kamera secara automatik, tetapi jika tiada DAQ yang diikat, rantaian menolak
keluar reflektansi dan secara jujur mencatat penurunan kualiti dalam metadata yang dikembalikan, bukannya
senyap-senyap menyerahkan hasil yang kurang baik.

> **Skala DN Refleksi — jangan hardkodkannya.** Refleksi LATTICE menggunakan `32768` = ρ 1.0 dan mencop
> XMP `Chloros:PixelScale=32768`; Survey3 reflectance menggunakan `65535` = ρ 1.0 dan tidak membawa sebarang tag `Chloros:*`. Baca tag tersebut dan bahagikannya. Ia ditakrifkan dalam domain uint16, jadi ia kekal `32768` untuk setiap format yang menormalkan semula (16-bit TIFF, 8-bit PNG/JPG, 32-bit peratus) — normalisasikan
> jenis data yang disimpan kembali ke uint16 terlebih dahulu (×257 daripada 8-bit, ×65535 daripada float). Satu pengecualian: tangkapan sumber 8-bit yang ditulis sebagai TIFF 8-bit adalah *dipotong*, bukan diskala semula, jadi tiada skala yang menerangkan
> ia — Chloros mengabaikan `PixelScale` dan tuple MicaSense sepenuhnya dalam kes itu. Anggap tag yang hilang
> pada fail pantulan LATTICE sebagai &quot;tiada skala sah&quot;, bukan sebagai lalai.

> **EXIF dibawa ke dalam eksport.** `process()` menyalin blok GPS tangkapan sumber
> **dan ExifIFD-nya** ke setiap produk, jadi eksport membawa `FocalLength`, `FNumber`,
> `ExposureTime`, `ISO`, `DateTimeOriginal` dan `CameraSerialNumber` serta georeferensinya. `FocalLength` adalah apa yang Pix4D gunakan untuk menentukan jarak sampel tanah — tanpa ia
> pembinaan semula kembali ke skala yang sangat salah (sebuah kes yang diukur menukar tapak setinggi 411 m
> menjadi 47.8 km). Salinan ini sengaja tidak menggunakan `-all:all`: tag struktur IFD0 rosak
> keluaran LATTICE, dan `ExifImageWidth`/`Height` dikecualikan kerana ia menerangkan tangkapan sumber
> dan bukannya raster yang dieksport.

Sub-penanda peringkat tangkapan (terpakai kepada tahap radiometrik — `radiance`, `reflectance`, `display`):

| Bendera | Lalai | Maksud |
| --- | --- | --- |
| `apply_calibration` | `True` | DSNU + flat-field + 3x3 unmix + skala radiometrik NIST. |
| `apply_white_balance` | `True` | LUT WB. Sedar DLS apabila DAQ diikat pada kamera. |
| `apply_index` | `False` | Penilaian indeks vegetasi. |
| `index_expression` | `None` | Gantikan formula. Tidak kosong → secara automatik mengaktifkan indeks. |
| `annotated` | `False` | Tindih hiasan GUI (zebra/grid/peaking). Tidak tersedia untuk `raw`. |

### `ArrayHandle`

```python
arr = proj.arrays["main_rig"]

# Single synced capture group
files = arr.capture("./out", format="tiff", processing="reflectance")
# → {"213800234": "/path/to/x.tif", "214000533": "/path/to/y.tif", ...}

# Multi-level: each serial's value becomes an ordered LIST, not a str
files = arr.capture("./out", processing="all")
# → {"213800234": ["/raw.tif", "/debayered.tif", ...], "combined": "/idx.tif"}

# Smart capture (wait for AE to settle)
result = arr.capture_smart(
    "./out", processing="reflectance",
    settle_timeout_s=5.0,
    stability_window_s=1.5,
    exposure_tolerance_pct=5.0,
)
print(result["frames"], result["settle"])

# In-memory grab: {serial: numpy array}
frames = arr.grab(processing="debayered")
frames = arr.grab(processing="radiance", with_metadata=True)

# Stream-to-disk loop
arr.stream(count=60, output_dir="./stream", fps=5, processing="raw")

# Frame-iterator (tolerates per-cam drops; great for downstream analysis pipelines)
for frames in arr.frame_stream(processing="radiance", fps=5, count=100):
    if "213800234" in frames:
        my_analysis_pipeline(frames["213800234"])

# Preview iterator (live MJPEG-equivalent; tolerates partial cycles)
counts = arr.preview_stream("./preview", fps=3.0, duration=30.0)
print(counts)  # frames written per serial
```

> **Jenis pulangan adalah `CapturePathMap`, bukan `Dict[str, str]`.**
> `chloros_sdk.CapturePathMap` adalah `Dict[str, Union[str, List[str]]]`: satu aras
> `processing` memberikan setiap siri satu laluan, manakala yang berbilang-tingkat (`"all"`, atau senarai `levels` eksplisit) memberikannya **senarai teratur** setiap produk yang disimpan untuk kamera itu. Komposit gabungan langsung, jika sedang penstriman, tiba di bawah kunci `"combined"` tambahan dan bukannya di bawah serial. Kod yang menganggap `str` akan gagal pada bentuk senarai tanpa sebarang jenis objek pemeriksa membantah — anotasi itu berkata `Dict[str, str]`
> untuk seketika selepas bentuk senarai dikeluarkan, itulah sebabnya alias itu wujud. Normalkan
> apabila anda mahukan bentuk rata:
>
> ```python
> paths = arr.capture(processing="all")
> flat = [p for v in paths.values()
>         for p in (v if isinstance(v, list) else [v])]
> ```

### Penyelarasan Susunan

`ArrayHandle` mendedahkan keseluruhan permukaan penjajaran. Profil secara lalai adalah untuk sesi sahaja — panggil `export_alignment()` secara eksplisit untuk mengekalkan.

```python
from chloros_sdk import AlignmentSpec

arr = proj.arrays["main_rig"]

# Defaults: ORB / affine / one synced snapshot — same as the GUI's auto-cal
result = arr.calibrate_alignment()
print(result["profile"]["rms_residual_px"])

# Custom spec for tough scenes (low-contrast canopy)
spec = AlignmentSpec(
    method="feature_orb",         # feature_orb / feature_akaze / phase_correlation / checkerboard / manual
    model="rigid",                # translation / rigid / affine / homography
    num_frames=5,
    max_features=8000,
    ratio_threshold=0.7,
    ransac_threshold_px=2.0,
    min_matches=30,
    max_reproj_err_px=2.0,
)
arr.calibrate_alignment(spec)

# Or tweak one knob at a time
arr.calibrate_alignment(num_frames=3, model="affine")

# Inspect / manipulate
status = arr.alignment_status()
arr.tweak_alignment("214701292", dx=2.5, dy=-1.0, rotation_deg=0.0, scale=1.0)
arr.export_alignment("/tmp/main_rig_alignment.json")
arr.import_alignment("/tmp/main_rig_alignment.json", validate=True)
arr.clear_alignment()
```

#### Penyelarasan Masa Penyambungan

`connect_all(align=...)` boleh menyelararas secara automatik setiap tatasusunan semasa penyambungan:

```python
# Align every array with defaults
proj.connect_all(align=True)

# Per-array control
proj.connect_all(align={
    "main_rig": AlignmentSpec(num_frames=5, model="affine"),
    "side_rig": True,             # use defaults
    "verify_rig": False,          # skip
})
```

Beralih kepada `project.json["config"]["auto_align_on_connect"]` apabila tidak dinyatakan.

### `SensorHandle`

```python
spectrum = proj.sensors["Sky"].read()
# (spectrum_list, is_saturated, integration_time, x, y, z) — matches the
# daq_sdk add_spectrum_callback signature.
```

---

## Perkakasan Langsung (Tanpa Backend)

Apabila anda mahukan sifar kebergantungan pada backend (CI, robot tanpa kepala, terbenam), import `lattice_sdk` dan `daq_sdk` secara langsung — kedua-duanya dieksport semula oleh `chloros_sdk`. Guard pada `CAMERA_AVAILABLE` / `DAQ_AVAILABLE`: `lattice_sdk` terdapat dalam pakej PyPI (tetapi memerlukan runtime Arena SDK), manakala `daq_sdk` hanya disertakan dengan pemasangan desktop.

```python
from chloros_sdk import (
    # cameras
    LatticeCamera, CameraSettings, PRESETS, CameraPool,
    Calibration, CalibrationCoefficients, FilterModel, list_filters,
    DLS, NetworkDiagnostics, gpu_info, gpu_available,
    # discovery
    discover_cameras, discover_cameras_via_backend,
    # exceptions
    LatticeError, CameraNotFoundError, StreamError, CaptureError,
    CalibrationError, NetworkError, DLSError,
)

# Find a camera and capture in one go
cams = discover_cameras(timeout_ms=3000)
print(cams)

settings = PRESETS["high_quality"]
with LatticeCamera(serial="213800234", settings=settings) as cam:
    result = cam.capture(output_dir="./out", format="tiff")
    print(result.filepath, result.width, result.height)
```

##### Preset dan pencetus

Tiga daripada empat preset **free-run**: kamera mengambil gambar secara berterusan dan
`capture()` memulangkan bingkai seterusnya. `triggered` adalah pengecualian — ia menyediakan kamera untuk edge perkakasan pada Baris 2, jadi ia tidak menangkap apa-apa sehingga satu tiba.

| Pratetap | Pencetus | Gunakan apabila |
| --- | --- | --- |
| `default` | bebas | kegunaan umum |
| `high_speed` | bebas | 8-bit, had 60 fps, pendedahan pendek |
| `high_quality` | bebas | 12-bit, tiada had fps — pilihan biasa untuk gambar pegun |
| `triggered` | **bersedia, Baris 2** | kamera disambungkan ke kabel penyegerakan M8 dan sesuatu yang lain mencetuskannya |

Jika anda memilih `triggered` (atau tetapkan `trigger_mode="On"` sendiri) tanpa apa-apa yang memacu Line 2, setiap `capture()` akan tamat masa — dengan betul, kerana anda meminta kamera untuk menunggu. SDK menerangkan perkara ini apabila ia berlaku; lihat
[SC_ERR_TIMEOUT semasa tangkapan](#direct-hardware-backend-free).

> **Nota — mesej &quot;GVSP probe&quot; / `SC_ERR_TIMEOUT -1011` semasa sambungan bukan ralat.**&gt; Semasa sambungan, SDK cuba merunding**bingkai jumbo** (9000-byte paket GVSP) untuk throughput yang lebih tinggi. Pada pautan NIC titik-ke-titik terus (contohnya alamat link-local `169.254.x.x`), rangkaian biasanya tidak dapat membawa bingkai jumbo, jadi prob ini akan tamat tempoh dan mencatat baris seperti:
>
> ```
> [Network] GVSP probe: unexpected error (TimeoutError: ... SC_ERR_TIMEOUT -1011)
> [Network] GVSP probe at 9000 did not deliver a complete buffer; reverting to ICMP-chosen size
> [Network] GVSP packet size: 1500 bytes (standard)
> ```
>
> Ini adalah **langkah sandaran yang direka**: SDK secara automatik kembali kepada paket standard 1500 bait dan kamera terus menyambung seperti biasa (baris `[chunk-enable …]` yang menyusul adalah sebahagian daripada urutan sambungan biasa). Perekodan masih berfungsi.
>
> Anda boleh melangkau probe ini, tetapi **ia bukan sekadar pendiam log — ia mematikan bingkai jumbo.** Kamera hanya menjawab ping Don&#x27;t-Fragment sehingga 1500 bait sahaja tanpa mengira betapa baiknya rangkaian anda, jadi ujian ping sahaja tidak akan pernah menemui jumbo; probe ini satu-satunya yang boleh. Nyahdayakannya dan kamera akan menggunakan paket 1500 bait standard selama-lamanya, pada mana-mana rangkaian:
>
> ```bash
> CHLOROS_GVSP_PROBE_FALLBACK=0   # gives up jumbo — see the warning it prints
> ```
>
> Ia hanya berbaloi pada rangkaian yang anda *tahu* tidak boleh membawa jumbo, di mana ia menjimatkan kira-kira satu saat masa penyambungan bagi setiap kamera. Oleh kerana iasatu pertukaran sebenar dan bukannya kosmetik, kini SDK akan memberitahu anda apabila anda menggunakannya:
>
> ```
> [Network] ⚠️ GVSP probe disabled (CHLOROS_GVSP_PROBE_FALLBACK=0) — staying at
> 1500 bytes, jumbo NOT tested. … if this network does carry it, you are giving
> up ~1.45x wire ceiling. Unset the variable to test for jumbo.
> ```
>
> **Biarkan sahaja melainkan anda mempunyai sebab.** Jika dibiarkan diaktifkan, setiap sambungan akan mengukur semula rangkaian yang anda gunakan: pasang pada suis yang menyokong jumbo dan sambungan seterusnya akan mengaktifkan jumbo secara automatik, tanpa perlu mengkonfigurasikan apa-apa dan tanpa memulakan semula.
>
> Jika anda *mahu* kelajuan penghantaran jumbo, aktifkan jumbo dari hujung ke hujung (NIC MTU 9000 + suis yang menyokongnya), atau tetapkan saiznya dengan `CHLOROS_GVSP_PACKET_SIZE_FORCE=9000` apabila anda tahu pautan tersebut menyokongnya — walaupun lebih baik menggunakan `CHLOROS_GVSP_PACKET_SIZE_FORCE=9000 python …` secara satu-satu arahan berbanding menetapkannya secara kekal, kerana saiz yang dipin akan melangkau probe dan berhenti menyesuaikan diri dengan rangkaian di hadapannya. **Setiap** peranti dalam laluan mesti menyokong jumbo — termasuk sebarang pemisah atau penyuntik PoE, yang biasanya menjadi sebab mengapa satu susunan yang sepatutnya menyokong jumbo tidak dapat memindahkannya.

> **`SC_ERR_TIMEOUT -1011` semasa `capture()` / `grab*()` adalah masalah yang berbeza — itu adalah ralat sebenar.**&gt; Nota di atas hanya mengenai `-1011` yang direkodkan oleh**probe masa sambungan**. Ralat yang sama yang diangkat daripada**perekodan** bermaksud kamera telah bersambung dengan baik tetapi tidak menghantar sebarang imej:
>
> ```
> File ".../lattice_sdk/camera.py", line ..., in grab_frame_with_metadata
>   buffer = self._get_buffer(timeout)
> lattice_sdk.exceptions.CaptureError: Capture failed: ... SC_ERR_TIMEOUT -1011
> ```
>
> Petunjuknya ialah kamera yang saluran *kawalan*nya sihat — penemuan berfungsi, tetapan dan penulisan `[chunk-enable …]` semuanya berjaya — manakala *setiap* bingkai mengalami tamat masa.
>
> **Punca biasa ialah kamera diaktifkan untuk pencetus perkakasan.** Dengan `trigger_mode="On"` dan `trigger_source="Line2"`, kamera tidak mengeluarkan apa-apa langsung sehingga tepi elektrik tiba pada kabel penyegerakan M8. Jika anda tidak mempunyai kabel yang memacu talian itu, setiap grab menunggu selama-lamanya. Kamera itu tidak rosak dan rangkaiannya baik-baik sahaja — ia melakukan tepat seperti yang diarahkan.
>
> `CameraSettings()` dan `default` / `high_speed` / `high_quality` menetapkan larian bebas, dan grab yang tamat masa semasa bersedia menerangkan dirinya sendiri bukannya mencetak `-1011` kosong. `PRESETS["triggered"]` mengaktifkan Line2, seperti yang direka.
>
> Untuk memaksa mana-mana kamera menjalankan bebas:
>
> ```python
> settings = PRESETS["high_quality"]
> settings.trigger_mode = "Off"        # free-run; don't wait for an M8 edge
> ```
>
> Jika ia masih tamat masa dengan `trigger_mode="Off"`, kamera itu benar-benar tidak menghantar data — hantarkan log dan `ip link show` kepada kami.

#### Profil Warna (pra-papar langsung RGB) — `set_color_profile`

`LatticeCamera.set_color_profile(profile, custom_cct_k=None)` memilih profil warna paparan untuk **pra-papar langsung** pada kamera RGB (kamera multispektral mengabaikan tetapan ini):

| Profil | Makna |
| --- | --- |
| `raw` | Mengelak sepenuhnya rantaian radiometrik. |
| `linear` | DSNU + rata + WB, tiada CCM, tiada gamma. |
| `natural` | Linear + CCM diukur + gamma sRGB, dengan kemasan murah sahaja (pelancaran kroma + penyusutan warna sorotan) — lalai realistik. |
| `enhanced` | `natural` ditambah kemasan pariti hab penuh (pengurangan sisiran, kecerahan warna, CLAHE kontras tempatan). Penampilan yang lebih kaya pada kira-kira **dua kali ganda kos kemasan setiap bingkai**, jadi kadar bingkai LIVE yang lebih rendah. |
| `custom_temp` | `natural` tetapi WB dipautkan ke `custom_cct_k` Kelvin (DLS diabaikan; dikunci pada 2000–10000 K di pihak belakang). |

Profil ini adalah **pra-tonton langsung sahaja** butang kelajuan/rupa: tangkapan yang disimpan sentiasa mendapat kemasan penuh yang kaya tanpa mengira profil yang dipilih, jadi memilih `natural` untuk membeli semula masa bingkai tidak menurunkan kualiti apa yang disimpan pada cakera. Profil yang tidak diketahui mencetuskan `ValueError`; apabila backend kloros boleh dihubungi, perubahan itu juga dihantar kepadanya supaya bingkai pratonton seterusnya mencerminkannya (pengguna direct-SDK tanpa backend masih menerima mutasi tetapan).

```python
with LatticeCamera(serial="214701292") as cam:   # RGB cam
    cam.set_color_profile("enhanced")            # richer look, lower LIVE fps
    cam.set_color_profile("custom_temp", custom_cct_k=5600)
```

#### Kamera Mono (M3M) dan `Calibration`

Mono **Kamera M3M** (`M3M-<lens>-F<wavelength>`) adalah jalur tunggal: satu satah skala kelabu, tiada mozek Bayer, tiada spektral 3×3-matriks crosstalk. `Calibration` mengenalinya dan mendedahkan bendera `is_mono`. Reflektan masih terpakai sebagai peta radiometrik setiap jalur (pencampuran semula adalah matriks identiti), tetapi matematik berbilang jalur pada satu kamera menghasilkan nilai yang lebih tinggi daripada nilai yang tidak masuk akal:

```python
from chloros_sdk import Calibration, CalibrationError

calib = Calibration("M3M-L87-F685")
print(calib.is_mono)        # True  (False for any M3C / RGN Bayer cam)
print(calib.filter_type)    # 'mono'  (sentinel; not a real crosstalk key)

# NDVI needs two bands (Red + NIR); one mono band can't supply both.
try:
    calib.compute_ndvi(reflectance_frame)
except CalibrationError as e:
    print(e)   # "...single-band mono (M3M) camera. Combine multiple..."
```

Untuk membina indeks vegetasi daripada perkakasan mono, gabungkan beberapa kamera M3M pada panjang gelombang berbeza ke dalam susunan berbilang jalur yang selari (lihat [Penjajaran Susunan](#penjajaran-array)) dan hitung indeks merentasi susunan itu bukannya pada satu kamera.

DAQ mod langsung:

```python
from chloros_sdk import (
    DAQUSensor, DAQMSensor, DAQESensor,
    SensorFleet, discover_all, DiscoveredSensor,
    apply_sensor_settings, SensorSettings,
)

for d in discover_all(timeout=3.0):
    print(d)

sensor = DAQUSensor(port="COM3")
sensor.connect()
apply_sensor_settings(sensor, settings={"integration_time_ms": 64, "frame_avg": 20})
sensor.start_streaming()
# ... sensor.add_spectrum_callback(your_callback) ...
sensor.stop()
```

> **`apply_sensor_settings` kunci yang diterima** — tepatnya `integration_time_ms`, `frame_avg`, `ae_enabled`, `sunshine_diffuser_installed` (DAQ-E; telah ditinggalkan dan digantikan dengan `cap_id`), `filter_model` (DAQ-M), dan `cap_id` (semua jenis DAQ; `None`/`""`/`"none"` = sensor mentah, tiada pembetulan cap). Kekunci yang tidak diketahui diabaikan secara senyap — contohnya `{"integration_time": 64}` tidak melakukan apa-apa (ia mesti `integration_time_ms`). Mengembalikan `{"applied": [...], "errors": {...}}` dan tidak pernah menimbulkan ralat.

`chloros_sdk` hanya mengeksport semula permukaan teras yang digunakan di atas. API awam penuh `daq_sdk` (22 nama) menambah perkara berikut — import mereka terus dari `daq_sdk`:

```python
from daq_sdk import (
    DAQULogger, DAQMLogger, DAQELogger,     # rotating-file recorders (the ones the GUI uses)
    ConnectResult, FleetRecordResult,       # SensorFleet result types
    discover_all_detailed, build_sensor,    # detailed discovery + build-by-descriptor
    scan_eth_devices, DaqEControl,          # DAQ-E Ethernet scan + control channel
    scan_ble_devices, detect_ble_device, list_ble_devices,   # DAQ-M BLE discovery
    detect_port, list_serial_ports,         # DAQ-U serial-port discovery
    TcpSerial,                              # serial-over-TCP transport shim
)
```

---

## Pengecualian

Tangkap kelas asas untuk mengendalikan &quot;apa sahaja yang salah dengan Chloros&quot;:

```python
import chloros_sdk

try:
    chloros_sdk.process_folder("/path/to/folder")
except chloros_sdk.ChlorosAuthenticationError:
    print("Run `chloros-cli login` first.")
except chloros_sdk.ChlorosLicenseError:
    print("Chloros+ subscription required.")
except chloros_sdk.ChlorosError as e:
    print(f"Chloros error: {e}")
```

> `ChlorosAuthenticationError` dan `ChlorosConfigurationError` dieksport di peringkat teratas bersama-sama dengan yang lain; mereka juga boleh dieksport dari `chloros_sdk.exceptions` seperti yang ditunjukkan.

Hierarki:

```

ChlorosError
├── ChlorosBackendError           (backend failed to start / unreachable)
├── ChlorosConnectionError        (HTTP transport failure)
├── ChlorosLicenseError           (subscription / tier gate)
├── ChlorosAuthenticationError    (login required)
├── ChlorosConfigurationError     (bad configure() / open_project() inputs)
└── ChlorosProcessingError        (pipeline failed)

ChlorosConnectError                (raised by connect_camera / connect_array /
                                    connect_daq_sensor only — derives from
                                    plain Exception, NOT from ChlorosError,
                                    so `except ChlorosError` will not catch it)

lattice_sdk exceptions:
LatticeError
├── CameraNotFoundError
├── CameraConnectionError
├── StreamError
├── CaptureError
├── CalibrationError
├── NetworkError
└── DLSError
```

---

## Contoh Dari A ke Z

### 1. Proses Folder dengan Bar Kemajuan Tersuai

```python
from chloros_sdk import ChlorosLocal

def progress(percent, message):
    bar = "#" * (percent // 5)
    print(f"\r[{bar:<20s}] {percent:3d}% {message}", end="", flush=True)

with ChlorosLocal() as cl:
    cl.create_project("FieldA_2026-05-26")
    cl.import_images("C:/DroneImages/Flight001", recursive=True)
    cl.configure(
        debayer="High Quality (Faster)",
        vignette_correction=True,
        reflectance_calibration=True,
        indices=["NDVI", "NDRE", "GNDVI", "SAVI"],
        export_format="TIFF (16-bit)",
    )
    cl.process(progress_callback=progress)
print()
```

### 2. Susunan LATTICE Langsung → Reflektan + Rujukan DAQ

```python
import chloros_sdk

# Open a paired sensor first so the array's reflectance step has an
# absolute reference. Smart-detect picks USB / BLE / ETH automatically.
with chloros_sdk.connect_daq_sensor() as daq:
    with chloros_sdk.connect_array([
            "213800234", "214000533", "214701288", "214701292"
    ]) as arr:
        # Smart capture: wait for AE to settle, then snap
        arr.capture("./out", processing="reflectance", smart=True)

        # Record the corresponding DAQ frames as ground truth
        daq.record_start(output_dir="./out", device_name="sky-reference")
        # ... do whatever capture campaign ...
        info = daq.record_stop()
        print(info["path"], info["rows"])
```

### 3. Kempen Rakaman Terarah Projek

```python
import time, chloros_sdk

with chloros_sdk.open_project("/home/user/Chloros Projects/Field_A") as proj:
    report = proj.connect_all(verbose=True, align=True)
    if report["arrays"]["errors"]:
        raise SystemExit(f"Array(s) failed to connect: {report['arrays']['errors']}")

    rig = proj.arrays["main_rig"]

    # Re-align right before the campaign
    rig.calibrate_alignment(num_frames=5)
    rig.export_alignment("./alignments/main_rig.json")

    # 50 sequential single-frame captures at 2 fps
    for i in range(50):
        frames = rig.capture(
            output_dir=f"./out/frame_{i:04d}",
            processing="reflectance",
            apply_calibration=True,
            apply_white_balance=True,
        )
        time.sleep(0.5)

    # End-of-day: process the captured folder. process() accepts only
    # mode/wait/progress_callback/poll_interval — indices come from the
    # project's saved config (or set them via ChlorosLocal.configure()).
    proj.process()
```

### 4. Saluran Bingkai Pelbagai Kamera → Saluran Paip NumPy

```python
import chloros_sdk
import numpy as np

with chloros_sdk.open_project("/path/to/proj") as proj:
    proj.connect_all()
    rig = proj.arrays["main_rig"]

    for frames in rig.frame_stream(
            processing="radiance",
            fps=5.0, count=300,
            apply_calibration=True,
            apply_white_balance=True):
        # frames is {serial: numpy_array}; cams not delivering this tick are omitted
        for serial, frame in frames.items():
            print(serial, frame.shape, frame.dtype, frame.mean())
```

### 5. Skrip Rakaman Perkakasan Langsung Tanpa Antaramuka (Tanpa Backend)

```python
from chloros_sdk import LatticeCamera, PRESETS, discover_cameras

cams = discover_cameras(timeout_ms=3000)
print(f"Found {len(cams)} cams")

settings = PRESETS["high_quality"]
for c in cams:
    with LatticeCamera(serial=c.serial, settings=settings) as cam:
        result = cam.capture(output_dir="./out", format="tiff")
        print(c.serial, result.filepath)
```

### 6. Ujian Kebolehan Sebelum Menyambungkan Susunan 4-Kamera

```python
import chloros_sdk

serials = ["214701288", "213800234", "214000533", "214701162"]

probe = chloros_sdk.analyze_array_network(
    master_serial=serials[0],
    slave_serials=serials[1:],
    width=2048, height=1536,
    pixel_format="BayerRG12",
)

if probe["status"] == "ok":
    arr = chloros_sdk.connect_array(
        serials, width=2048, height=1536, pixel_format="BayerRG12")
elif probe["status"] == "auto_capped_fps":
    r = probe["recommended"]
    print(f"Keeping resolution; capping trigger rate at "
          f"{r['recommended_target_fps']} fps")
    arr = chloros_sdk.connect_array(
        serials, width=2048, height=1536, pixel_format="BayerRG12",
        target_fps=r["recommended_target_fps"])
elif probe["status"] == "auto_shrunk":
    r = probe["recommended"]
    print(f"Auto-shrinking to {r['out_width']}x{r['out_height']} "
          f"binning={r['binning']} for sim-sync")
    arr = chloros_sdk.connect_array(
        serials,
        width=r["out_width"], height=r["out_height"],
        pixel_format=r["pixel_format"], binning=r["binning"])
elif probe["status"] == "needs_force_slip":
    print("Wire can't sustain sim-sync; falling back to slip mode")
    arr = chloros_sdk.connect_array(
        serials, force_tier="slip-emit-and-capture")
else:
    raise RuntimeError(f"Probe error: {probe.get('error')}")
```

### 7. Setara Resipi Rakaman (Python Murni)

DSL resipi CLI mempunyai setara Python secara langsung:

```python
import time, chloros_sdk

with chloros_sdk.open_project("/path/to/proj") as proj:
    proj.connect_all()
    cam = proj.cameras["FrontLeft"]
    rig = proj.arrays["main_rig"]
    sky = proj.sensors["Sky"]

    # apply
    # (CameraHandle has no direct apply method; use the underlying lattice_sdk
    #  helper or the backend's /api/camera/<sn>/apply-settings via requests)
    # For most cases just use cam.cam.set_exposure(...) in direct mode or
    # the GUI's saved settings via project.connect_all().

    # wait
    time.sleep(2)

    # capture
    cam.capture("pose_a/", format="tiff", processing="radiance")

    # stream
    rig.stream(count=60, fps=5, output_dir="stream/", processing="raw")

    # sensor read
    print(sky.read())
```

---

## Mula Automatik Backend

Titik permulaan sambungan pintar — `connect_camera`, `connect_array`, `connect_daq_sensor`, dan `discover_lattice_cameras` — adalah klien nipis HTTP yang menganggap backend sedang mendengar di `127.0.0.1:5000` (URL lalai bagi permukaan smart-connect). Apabila GUI atau CLI sudah berjalan, ia memang ada. Daripada skrip kosong, mungkin tiada — jadi fungsi-fungsi ini **memulakan secara automatik binari backend yang disertakan** (tanpa tetingkap, sama seperti `ChlorosLocal`) sebelum panggilan pertama mereka, kemudian menunggu sehingga `backend_startup_timeout` untuk ia muncul.

Peraturan:

- **Hanya satu URL tempatan akan diwujudkan.** `backend_url` yang menunjuk ke `localhost` / `127.0.0.1` / `[::1]` layak; mana-mana hos lain dianggap milik orang lain mesin dan tidak pernah diproses.
- **Backend dibiarkan berjalan untuk digunakan semula** (sama seperti CLI) — tiada penutupan secara automatik apabila skrip anda keluar. Re-menjalankan skrip menggunakan semula backend yang sedang berjalan.
- **Batal dengan `auto_start_backend=False`** pada mana-mana panggilan tersebut (contohnya apabila anda telah menunjuk ke backend jauh, atau anda mengurus kitar hayat backend sendiri).

```python
import chloros_sdk

# Fresh shell, no backend running, no GUI open — this still works:
with chloros_sdk.connect_camera("213800234") as cam:   # spawns the backend
    cam.capture("output/")

# Remote backend (via tunnel — see Remote-Backend Mode): don't spawn one locally
arr = chloros_sdk.connect_array(serials,
                                backend_url="http://127.0.0.1:5000",
                                auto_start_backend=False)
```

Jika binari yang disertakan tidak dapat ditemui atau dimulakan, panggilan HTTP seterusnya membangkitkan `ChlorosConnectError` yang boleh ditangani dan **sedar platform**, bukannya jejak penolakan sambungan kosong — pada Windows ia menunjuk anda ke aplikasi desktop atau arahan `chloros-cli`; pada Linux (tanpa GUI) ia menunjuk anda ke arahan `chloros-cli` atau `.deb`.

---

## Persekitaran &amp; Header

SDK menandakan setiap panggilan backend HTTP dengan `X-Chloros-Client: sdk`. Backend menerapkan peraturan pelesenan SDK / CLI (log masuk **dan** pelan Chloros+ berbayar diperlukan) dan bukannya laluan peringkat percuma GUI. Ini ditetapkan secara automatik semasa pengimportan — anda tidak perlu melakukan apa-apa.

`http://localhost` dan `http://127.0.0.1` dikesan sebagai backend tempatan. Panggilan ke hos (contohnya perkhidmatan analitik anda sendiri) tidak diubah.

Tetapkan semula backend URL dengan menghantar `backend_url=` (atau `api_url=` pada `ChlorosLocal`):

```python
chloros_sdk.connect_camera("213800234", backend_url="http://127.0.0.1:5000")
chloros_sdk.connect_array(serials, backend_url="http://127.0.0.1:5000")
chloros_sdk.connect_daq_sensor(eth_host="daq-e-1.local",
                                backend_url="http://127.0.0.1:5000")
chloros_sdk.ChlorosLocal(backend_url="http://127.0.0.1:5000")
```

(`backend_url` bukan loopback hanya akan mencapai backend source/dev — backend yang disertakan hanya mengikat loopback; lihat Mod Backend Jauh untuk corak terowong.)

---

## Versi &amp; Kebolehpadanan

- Versi SDK didedahkan sebagai `chloros_sdk.__version__`.
- SDK menetapkan tingkah laku kepada versi backend yang disertakan. Menggabungkan SDK yang lebih lama dengan backend yang lebih baru biasanya berfungsi (titik akhir yang serasi ke hadapan), tetapi menggabungkan SDK yang lebih baru dengan backend yang lebih lama mungkin akan menghasilkan XRalat  pada hujung baru — naik taraf aplikasi desktop untuk menyelaraskannya.
- Antara muka pintar-paut (smart-connect) (`connect_camera` / `connect_array` / `connect_daq_sensor`) dan endpoint analisis rangkaian memulangkan skema JSON yang stabil; medan baru adalah tambahan.

---

## Petunjuk Penyelesaian Masalah

- **`ChlorosAuthenticationError: Login required`** → Jalankan `chloros-cli login EMAIL PASSWORD` sekali pada mesin ini, atau log masuk melalui aplikasi desktop Chloros.
- **`ChlorosConnectError: No Chloros backend is running …`** → Panggilan smart-connect secara automatik memulakan backend tempatan, jadi ini hanya akan muncul apabila binari yang disertakan tidak dapat ditemui/dimulakan (contohnya hos hanya pip tanpa pakej desktop). Pesanannya sedar platform: pada Windows buka aplikasi desktop atau jalankan mana-mana arahan `chloros-cli`; pada Linux jalankan arahan `chloros-cli` (tiada GUI wujud) atau pasang `.deb`. Untuk backend jauh, hantar `backend_url=` (dan `auto_start_backend=False`).
- **`CAMERA_AVAILABLE == False`** semasa import → `lattice_sdk` gagal memuat (biasanya DLL runtime Arena SDK tidak dipasang). Permukaan bukan kamera masih berfungsi.
- **Array connect memulangkan resolusi sub-lokal**→ Smart-prep belakang secara automatik mengecilkan saiz bingkai untuk muat pada wayar. Gunakan `analyze_array_network()` untuk melihat sebabnya, kemudian sama ada naik taraf pautan, terima pengecilan, atau hantar `force_tier="slip-emit-and-capture"` untuk tangkapan bersiri. Rangkaian keselamatan pengecilan**tidak** merangkumi lebihan langganan agregat (`oversubscribed: true`, medan fps 0): terlalu banyak kamera untuk talian tidak dapat diperbaiki dengan binning/ROI — kurangkan bilangan kamera, aktifkan bingkai jumbo, atau beralih ke NIC yang lebih pantas (lihat [Over-Subscription](#over-subscription-the-per-cam-floor)).
- **`analyze_array_network()` melaporkan cincin RX NIC sebagai kecil (~0.26 MB) / sambungkan pintu dengan &quot;FRAMES WILL DROP&quot;** → Cincin penerima NIC hos berada pada lalai (selalunya ditetapkan semula kepada 32 selepas kemas kini pemacu NIC). Pada penyesuai Realtek USB 10GbE tetapkan `ReceiveBufferLen=256` dan `PendingReceives=64` (tingkatkan), kemudian mulakan semula backend supaya ia membaca semula cincin tersebut. Prosedur penuh: [CLI Rujukan → Tetapan &amp; Penyelarasan NIC Host](cli-reference.md#host-nic-setup--tuning-lattice-arrays).
- **Host tergantung semasa mula semula/tutup, kesilapan WMI `Invalid class` kemudian / NIC tidak akan diaktifkan** → Pemandu USB 10GbE lapuk menyebabkan `DRIVER_POWER_STATE_FAILURE` (BSOD `0x9F`). Kemas kini pemacu penyesuai ke versi semasa (≥ 2026) dan terapkan semula tetapan receive-ring. Rujuk [CLI Rujukan → Persediaan &amp; Penyelarasan NIC Host](cli-reference.md#host-nic-setup--tuning-lattice-arrays).
- **Reflectance ditolak** → DAQ langsung mesti diikat kepada kamera (atau susunan) untuk skala mutlak reflektansi. Sama ada ikat melalui GUI atau gunakan `processing="radiance"` (W/m²/sr/nm) yang tidak memerlukan sensor berpasangan.
- **Penangkapan `smart=True` mengambil masa lebih lama daripada yang dijangkakan** → Konvergens AE bergantung pada dinamik adegan; ketatkan `exposure_tolerance_pct` atau pendekkan `stability_window_s` jika anda mahukan pencetus yang lebih pantas (kurang stabil).

---

## Lihat Juga

- [RujukanCLI](cli-reference.md) — setiap subperintah CLI mencerminkan panggilan SDK.
- [Panduan Sensor DAQ](../daq/README.md) — peraturan pendawaian, penentukuran, dan rakaman khusus penderia.
- Dokumen dalam talian: `https://mapir.gitbook.io/chloros/api-python-sdk`</id></sn>
