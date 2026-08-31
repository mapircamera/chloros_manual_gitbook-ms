# API : Python SDK

{% hint style="info" %}
**Mencari API lengkap?** Halaman ini adalah tutorial praktikal. Setiap kelas awam, kaedah, tandatangan tepat, dan contoh yang boleh disalin-tampal terdapat dalam [Rujukan SDK](reference/sdk-reference.md), yang dioptimumkan untuk pembantu AI.**Bekerja dengan pembantu AI?** Tampal URL ini ke dalam sembang supaya ia mempunyai Chloros 1.2.0 API penuh dan semasa:

`https://mapir.gitbook.io/chloros/reference/sdk-reference.md`

Setiap halaman manual ini tersedia sebagai markdown mentah pada slug huruf kecil + `.md`, dan keseluruhan manual diindeks di `https://mapir.gitbook.io/chloros/llms.txt`.
{% endhint %}

**Chloros Python SDK** (`chloros-sdk` di PyPI) menggerakkan segala yang boleh dilakukan oleh aplikasi desktop daripada Python: pemprosesan imej secara pukal, kawalan kamera LATTICE secara langsung dan kawalan tatasusunan, sesi penderia cahaya DAQ, dan automasi projek yang disimpan. Ia adalah lapisan nipis di atas backend tempatan yang sama yang digunakan oleh GUI dan CLI (HTTP pada `127.0.0.1:5000`), jadi kelakuannya adalah serupa di ketiga-tiga antaramuka tersebut.

## Pemasangan

Pemasangan terdiri daripada dua langkah: pertama pakej desktop Chloros (ia menyediakan backend pemprosesan dan runtime perkakasan), kemudian pakej Python.

**Langkah 1 — Pasang Chloros.** Windows: jalankan pemasang desktop (laluan lalai `C:\Program Files\MAPIR\Chloros\`) daripada halaman [Muat Turun](download.md). Linux: pasang pakej `.deb` ([Linux Installation](linux/linux-installation.md)).**Langkah 2 — Pasang SDK** (Python 3.7+):

```bash
pip install chloros-sdk
```

Anda mungkin tidak memerlukan pip: setiap pemasang disertakan dengan roda SDK yang sepadan. Pemasang Windows memasangnya secara automatik ke dalam sistem anda Python; pemasang Linux `.deb` meletakkannya di `/usr/lib/chloros/sdk/` dan mencetak arahan `pip install --user` yang tepat. PyPI dikemas kini pada binaan rilis, jadi `pip install chloros-sdk` sepadan dengan rilis stabil terkini.

**Langkah 3 — Log masuk sekali setiap mesin:**

```bash
chloros-cli login user@example.com 'YourPassword'
```

Butiran log masuk disimpan dalam `~/.chloros/` (kedua-dua platform). Pada Windows, anda boleh log masuk dengan cara yang sama melalui <img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line">tab Akaun Pengguna dalam aplikasi desktop. SDK memerlukan pelan berbayar Chloros+ — lihat [Keperluan Lesen](#license-requirement) di bawah.

| Keperluan | Perincian |
| --- | --- |
| **Chloros dipasang** | Windows: pemasang desktop; Linux: pek `.deb` (menyediakan binari backend) |
| **Python** | 3.7 atau lebih tinggi (dibangunkan/diuji pada 3.10) |
| **Sistem pengendalian** | Windows 10/11 64-bit, Ubuntu 22.04 LTS atau lebih baru, atau NVIDIA Jetson (JetPack 6) |
| **Lesen** | Log masuk Chloros+ Aktif, mana-mana pelan berbayar (Tembaga atau ke atas) |

## Kemenangan 60 saat

Satu panggilan mencipta projek, mengimport folder, mengkonfigurasi pemprosesan, dan menjalankan saluran paip — memulakan backend secara automatik jika ia belum berjalan:

```python
import chloros_sdk

results = chloros_sdk.process_folder(
    "C:/DroneImages/Flight001",
    indices=["NDVI", "NDRE", "GNDVI"],
)
```

(Pada Linux, gunakan laluan Linux: `/home/user/drone_images/flight001`. SDK berfungsi dengan cara yang sama pada kedua-dua platform.)

Proses folder tangkapan LATTICE? Gunakan pembungkus mesra LATTICE — ia menerapkan tetapan lalai yang betul (tiada pengesanan panel-sasaran, debayer piawai):

```python
results = chloros_sdk.process_lattice_capture(
    folder_path="C:/Captures/2026-05-13_Field",
    indices=["NDVI"],
)
```

## `ChlorosLocal` — kawalan saluran penuh

Untuk apa-apa yang melebihi satu baris, gunakan `ChlorosLocal`. Ia memulakan backend pada penggunaan pertama (`auto_start_backend=True`), mencipta dan mengkonfigurasi projek, memantau kemajuan, dan memaparkan ringkasan selepas pelaksanaan.

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

{% hint style="info" %}
Gunakan `http://127.0.0.1:5000` lalai dan bukannya menggantikannya dengan `localhost` — pada Windows, `localhost` diselesaikan kepada `::1` terlebih dahulu dan memakan masa kira-kira 2 saat setiap permintaan terhadap backend IPv4 sahaja.
{% endhint %}

Gunakan ia sebagai pengurus konteks untuk pembersihan yang dijamin:

```python
import chloros_sdk

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

`configure()` menerima kata kunci ini: `debayer`, `vignette_correction`, `reflectance_calibration`, `indices`, `export_format`, `ppk`, `daq_log_path`, `input_level`, `radiometric_output`, `array_alignment`, `array_alignment_crop`, `array_alignment_interpolation`, dan `custom_settings`. Nilai utama:

```python
# export_format
"TIFF (16-bit)"           # default, recommended
"TIFF (32-bit, Percent)"  # reflectance percentage as float32
"PNG (8-bit)"
"JPG (8-bit)"

# debayer
"High Quality (Faster)"                  # standard, default
"Texture Aware (Slow, Highest Quality)"  # neural debayer, Chloros+ only
```

Kenop khusus LATTICE (`input_level`, `radiometric_output`, keluarga `array_alignment*`) didokumenkan dengan jadual nilai penuh mereka dalam [Rujukan SDK](reference/sdk-reference.md#supported-values).

### Memantau kemajuan

```python
def show_progress(percent, message):
    print(f"[{percent:3d}%] {message}")

with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("FieldA")
    cl.import_images("C:/DroneImages/Flight001")
    cl.configure(indices=["NDVI"])
    cl.process(progress_callback=show_progress, poll_interval=1.0)
```

### Membaca ringkasan pasca-jalanan — dan menangkap jalanan kosong

Apabila selesai, `process()` melampirkan ringkasan pemprosesan backend sebagai `result["summary"]`. Setiap entri dalam `summary["hints"]` adalah satu ayat penuh yang menerangkan apa-apa yang ketara — contohnya, mengapa sesuatu larian menghasilkan sifar output — dan setiap petunjuk juga disiarkan semula sebagai `UserWarning`, jadi larian kosong mendiagnosis sendiri walaupun anda tidak pernah memeriksa dict:

```python
result = cl.process()
for hint in result.get("summary", {}).get("hints", []):
    print("HINT:", hint)
# hints also arrive on the warnings channel:
#   python -W always::UserWarning your_script.py
```

{% hint style="warning" %}
**`process()` tidak diaktifkan apabila satu larian tidak menghasilkan sebarang imej.** Inilah satu-satunya tempat di mana SDK dan CLI berbeza dengan sengaja: `chloros-cli process` menganggap &quot;produk telah diminta, tiada yang ditulis&quot; sebagai kegagalan dan keluar dengan nilai bukan sifar, manakala SDK keluar secara normal dan melaporkan keadaan tersebut melalui `summary` / petunjuk. Jika pipeline anda berhenti pada larian kosong, semak sendiri — periksa `summary` (atau kira fail di bawah folder projek) daripada bergantung pada pengecualian.
{% endhint %}

## Smart Connect — perkakasan langsung

Tiga pembantu membuka sesi berterusan dalam kolam perkakasan backend — kolam yang sama digunakan oleh GUI, jadi skrip SDK wujud bersama aplikasi desktop tanpa berebut port siri atau jalur lebar rangkaian. Ketiganya memulakan backend tempatan secara automatik jika tiada yang sedang berjalan.

### Kamera LATTICE tunggal — `connect_camera`

```python
import chloros_sdk

# Open by serial; reuses existing pool entry if one exists
with chloros_sdk.connect_camera("213800234") as cam:
    cam.set_settings(exposure_time=10000, gain=0.0)   # microseconds, dB
    cam.capture("output/")
```

### Susunan bersepadu — `connect_array`

`connect_array` adalah titik permulaan yang disyorkan untuk rig berbilang kamera. Ia menjalankan aliran penyediaan pintar yang sama seperti GUI: analisis rangkaian, pemilihan lapisan penyegerakan secara automatik, penyegerakan masa PTP, pemilihan format piksel bagi setiap kamera, penaburan AE, dan pengaktifan pencetus GPIO. **Siri pertama adalah induk** (ia menghasilkan denyut picu perkakasan); selebihnya adalah anak buah.

```python
with chloros_sdk.connect_array(
        ["213800234", "214000533", "214701288", "214701292"]) as arr:
    arr.capture("output/", processing="reflectance")
```

Tambah `smart=True` pada sebarang tangkapan susunan untuk menunggu pendedahan automatik menstabilkan pada semua kamera sebelum mencetuskan. Untuk mod tangkapan (Tunggal / Berterusan / Selang / Terpantas), perakam, burst-ke-video, dan penjajaran tatasusunan, lihat [Rujukan SDK](reference/sdk-reference.md#synchronized-array--arraysession-smart-prep).

### Penderia cahaya DAQ — `connect_daq_sensor`

Tanpa sebarang hujah, `connect_daq_sensor()` mengesan secara pintar pengangkutan (keutamaan: Ethernet → BLE → USB):

```python
with chloros_sdk.connect_daq_sensor() as daq:    # smart-detect USB / BLE / ETH
    for frame in daq.latest(n=5):
        print(frame["spectrum"][:10])
```

Setiap bingkai membawa 135-titik `spectrum` (W/m²/nm apabila dikalibrasi), sebuah bendera `is_saturated`, dan CIE `x`, `y`, `z`. Untuk menetapkan sensor atau pengangkutan tertentu — pilihan yang boleh dipercayai pada hos dengan pelbagai antara muka rangkaian, di mana penemuan automatik Ethernet boleh terlepas DAQ-E yang sihat pada percubaan pertama — hantar satu petunjuk eksplisit:

```python
daq = chloros_sdk.connect_daq_sensor(transport="usb", port="COM3")
daq = chloros_sdk.connect_daq_sensor(mac="AA:BB:CC:DD:EE:FF")        # implies BLE
daq = chloros_sdk.connect_daq_sensor(eth_host="daq-e-xxx.local")     # implies Ethernet
```

Perlu diingat bahawa profil pembetulan cap (`cap_id`) **bukanlah** tombol eSDK — pilihnya melalui `chloros-cli daq pool-connect --cap-id …` / `pool-set-cap` sebaliknya.

### Projek yang disimpan — `open_project`

Projek Chloros yang disimpan mengekalkan perkakasan bersambungnya (`cameras.json` + `sensors.json` bersama `project.json`), dan `chloros_sdk.open_project(path)` boleh menyambung semula semuanya sekaligus dan memacu tangkapan mengikut nama peranti. Lihat [Automasi Projek](reference/sdk-reference.md#project-automation--chlorosproject) dalam rujukan.

## Apa yang diperoleh dengan pemasangan pip sahaja

Semak penanda ketersediaan peringkat modul sebelum menggunakan permukaan perkakasan:

```python
import chloros_sdk
print(chloros_sdk.__version__)
print("CAMERA_AVAILABLE =", chloros_sdk.CAMERA_AVAILABLE)    # True iff lattice_sdk imported cleanly
print("DAQ_AVAILABLE    =", chloros_sdk.DAQ_AVAILABLE)       # True iff daq_sdk imported cleanly
print("PROJECT_AVAILABLE =", chloros_sdk.PROJECT_AVAILABLE)  # True iff ChlorosProject deps available
```

Pada hos dengan **hanya** `pip install chloros-sdk` dan tiada pakej desktop Chloros:

* `ChlorosLocal`, `process_folder`, dan `process_lattice_capture` **tidak** berfungsi — mereka memerlukan binari backend yang disertakan dalam pemasang desktop.
* Pembantu smart-connect (`connect_camera`, `connect_array`, `connect_daq_sensor`) adalah klien HTTP tulen, jadi ia berfungsi dengan backend pada mesin lain — tetapi backend yang disertakan hanya mengikat kepada gelung balik sahaja, jadi anda mesti meneruskan port itu sendiri (contohnya `ssh -N -L 5000:127.0.0.1:5000 user@chloros-host`) dan menghantar `backend_url="http://127.0.0.1:5000"` dengan `auto_start_backend=False`. Lihat [Remote-Backend Mode](reference/sdk-reference.md#remote-backend-mode-pip-only-host-via-tunnel).
* Kelas LATTICE perkakasan langsung (`LatticeCamera`, `CameraPool`, …) import, tetapi memerlukan runtime Arena SDK daripada pakej desktop — tanpanya `CAMERA_AVAILABLE` adalah `False`.
* `daq_sdk` (kelas DAQ langsung) disertakan dengan pemasangan desktop, bukan pakej PyPI, jadi `DAQ_AVAILABLE` adalah `False` pada hos yang hanya menggunakan pip — kendalikan penderia DAQ melalui `connect_daq_sensor()` ke belakang (tunneled) sebaliknya.

## Keperluan lesen

Akses SDK memerlukan log masuk Chloros+ aktif pada mana-mana pelan berbayar — **Copper atau ke atas**(Copper / Gangsa / Perak / Emas); pelan Iron percuma tidak mempunyai akses SDK / CLI. Penguatkuasaan adalah**di pihak pelayan**: setiap permintaan SDK mesti disertakan dengan sesi langsung dan pelan berbayar, jika tidak backend akan mengembalikan `403` / `PLAN_UPGRADE_REQUIRED` (dibangkitkan sebagai `ChlorosLicenseError` oleh `ChlorosLocal`, dan sebagai `ChlorosConnectError` oleh pembantu `connect_*`). Pemanggil yang log keluar mendapat `401` / `AUTH_REQUIRED` (`ChlorosAuthenticationError`) sebaliknya — menjalankan semula `chloros-cli login` membetulkan kes pertama tetapi tidak kes kedua.

Penggunaan luar talian berfungsi dalam tempoh kurniaan pelan: peringkat dibaca daripada cache pengesahan pelayan (5 minit) atau cache lesen bertandatangan terikat mesin (30 hari untuk pelan bulanan; sehingga tamat langganan untuk tahunan). Apabila tempoh lanjutan tamat, pelan akan bertukar kepada percuma dan akses &#x27;SDK&#x27; akan terhenti sehingga mesin menyambung ke pelayan sekali. `chloros-cli status` kekal boleh dihubungi pada peringkat percuma supaya sebabnya sentiasa dapat dilihat. Lihat [Chloros+ Login](chloros+-login.md).

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

Semua pengecualian saluran (`ChlorosBackendError`, `ChlorosConnectionError`, `ChlorosLicenseError`, `ChlorosAuthenticationError`, `ChlorosConfigurationError`, `ChlorosProcessingError`) berasal daripada `ChlorosError`. Satu perangkap: `ChlorosConnectError` — hanya diangkat oleh `connect_camera` / `connect_array` / `connect_daq_sensor` — berasal daripada `Exception` biasa, **bukan** daripada `ChlorosError`, jadi `except ChlorosError` tidak akan menangkanya. Hierarki penuh terdapat dalam [Rujukan SDK](reference/sdk-reference.md#exceptions).

## Lihat juga

* [Rujukan SDK](reference/sdk-reference.md) — permukaan API lengkap, dioptimumkan untuk pembantu AI.
* [CLI Rujukan](reference/cli-reference.md) — setiap subperintah CLI mencerminkan panggilan SDK.
* [Muat turun](download.md) — pemasang untuk Windows dan Linux.
