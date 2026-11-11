# API: Python SDK

{% hint style="info" %}
**Mencari API lengkap?** Halaman ini adalah tutorial praktikal. Setiap kelas awam, kaedah, tandatangan tepat, dan contoh yang boleh disalin-tampal terdapat dalam [Rujukan SDK](reference/sdk-reference.md), yang dioptimumkan untuk pembantu AI.**Bekerja dengan pembantu AI?** Tampal URL ini ke dalam sembang supaya ia mempunyai Chloros 1.3.0 penuh dan semasa:

`https://mapir.gitbook.io/chloros/reference/sdk-reference.md`

Setiap halaman manual ini tersedia sebagai markdown mentah di slug kecil hurufnya + `.md`, dan keseluruhan manual diindeks di `https://mapir.gitbook.io/chloros/llms.txt`.
{% endhint %}

**Chloros Python SDK** (`chloros-sdk` di PyPI) menggerakkan segala yang boleh dilakukan oleh aplikasi desktop daripada Python: pemprosesan imej secara pukal, kawalan kamera LATTICE secara langsung dan kawalan tatasusunan, sesi penderia cahaya DAQ, dan automasi projek yang disimpan. Ia adalah lapisan nipis di atas backend tempatan yang sama digunakan oleh GUI dan CLI (HTTP pada `127.0.0.1:5000`), jadi kelakuannya adalah sama di ketiga-tiga antaramuka tersebut.

## Pasang

SDK disertakan dengan pakej desktop Chloros, yang juga menyediakan backend pemprosesan dan runtime perkakasan, dan sentiasa sepadan versinya.

**Langkah 1 — Pasang Chloros.** Windows: jalankan pemasang desktop (laluan lalai `C:\Program Files\Chloros\`) daripada halaman [Muat Turun](download.md). Linux: pasang pakej `.deb` dengan `sudo apt install ./chloros_1.3.0_<arch>.deb` ([Pemasangan Linux](linux/linux-installation.md)).**Langkah 2 — Semak **SDK** (Python 3.7+). Biasanya tiada apa-apa yang perlu dipasang:

* **Windows:**pemasang memasang roda**SDK** yang disertakan ke dalam Python sistem anda (jika tiada Python dipasang, ia akan melangkau langkah ini).
* **Linux:** `.deb` memasang SDK untuk sistem `python3`, ke dalam `/usr/lib/python3/dist-packages`. Perpustakaannya, `python3-numpy` dan `python3-requests`, disyorkan oleh pakej ini: `apt install` membawanya, `dpkg -i` tidak.
* **virtualenv** tidak melihat pakej sistem; pasang wheel yang disertakan ke dalamnya: `pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl` pada Linux, atau `chloros_sdk-*.whl` dalam folder `sdk` di folder pemasangan Windows (`C:\Program Files\Chloros\sdk\` secara lalai).

```bash
python3 -c "import chloros_sdk; print(chloros_sdk.__version__)"   # Windows: py -c "…"
```

Pada mesin yang tiada pakej Chloros, yang SDK-nya memacu backend pada mesin lain (lihat [What a pip-only install gets](#what-a-pip-only-install-gets)), pasangnya dari PyPI: `pip install chloros-sdk`. PyPI dikemas kini pada binaan rilis, jadi ia sepadan dengan rilis stabil terkini.

**Langkah 3 — Log masuk sekali setiap mesin:**

```bash
chloros-cli login user@example.com 'YourPassword'
```

Butiran log masuk disimpan dalam cache di `~/.chloros/` (kedua-dua platform). Pada Windows, anda boleh log masuk dengan cara yang sama melalui tab User <img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line"> pada aplikasi desktop. SDK memerlukan pelan berbayar Chloros+ — lihat [Keperluan lesen](#license-requirement) di bawah.

| Keperluan | Perincian |
| --- | --- |
| **Chloros dipasang** | Windows: pemasang desktop; Linux: pakej `.deb` (menyediakan binari backend dan SDK) |
| **Python** | 3.7 atau lebih tinggi (dibangunkan/diuji pada 3.10) |
| **Sistem pengendalian** | Windows 10/11 64-bit, Ubuntu 22.04 LTS atau lebih baru, atau NVIDIA Jetson (JetPack 6) |
| **Lesen** | Log masuk Chloros+ Aktif, mana-mana pelan berbayar (Tembaga atau ke atas) |

## Kemenangan 60 saat

Satu panggilan mencipta projek, mengimport folder, mengkonfigurasi pemprosesan, dan menjalankan saluran paip — memulakan backend secara automatik jika ia belum berjalan:

```python
import chloros_sdk

results = chloros_sdk.process_folder(
    "C:/DroneImages/Flight001",
    indices=["NDVI", "GNDVI", "OSAVI"],
)
```

(Pada Linux, gunakan laluan Linux: `/home/user/drone_images/flight001`. SDK berfungsi dengan cara yang sama pada kedua-dua platform.)

Proses folder tangkapan LATTICE? Gunakan pembungkus mesra-LATTICE — ia menerapkan tetapan lalai yang betul (tiada pengesanan panel-sasaran, debayer piawai):

```python
results = chloros_sdk.process_lattice_capture(
    folder_path="C:/Captures/2026-05-13_Field",
    indices=["NDVI"],
)
```

## `ChlorosLocal` — kawalan saluran penuh

Untuk apa-apa yang melebihi satu baris, gunakan `ChlorosLocal`. Ia menggunakan backend yang sudah berjalan (aplikasi desktop), atau memulakan yang dipasang pada penggunaan pertama (`auto_start_backend=True`; pada Windows daripada folder `backend` dalam folder pemasangan Chloros, `C:\Program Files\Chloros\backend\` secara lalai), mencipta dan mengkonfigurasi projek, memantau kemajuan, dan memaparkan ringkasan pasca-jalankan.

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
Tetap menggunakan `http://127.0.0.1:5000` lalai dan bukannya menggantikannya dengan `localhost` — pada Windows, `localhost` diselesaikan kepada `::1` terlebih dahulu dan mengambil masa ~2 saat setiap permintaan terhadap backend IPv4 sahaja.
{% endhint %}

Gunakan ia sebagai pengurus konteks. Meninggalkan blok ini menamatkan penggunaan backend oleh instans ini, bukan backend itu: ia kekal berjalan untuk skrip seterusnya dan menghentikan sendiri selepas 30 minit tidak digunakan. `shutdown_backend()` menghentikan satu ini apabila instans ini dimulakan.

```python
import chloros_sdk

with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("FieldA_2026-05-26", camera="Survey3N_RGN")
    cl.import_images("C:/DroneImages/Flight001")
    cl.configure(
        vignette_correction=True,
        reflectance_calibration=True,
        indices=["NDVI", "GNDVI", "OSAVI"],
        export_format="TIFF (16-bit)",
    )
    results = cl.process(mode="parallel", wait=True)
print(results["summary"])
```

`configure()` menerima kata kunci ini: `debayer`, `vignette_correction`, `reflectance_calibration`, `indices`, `export_format`, `ppk`, `daq_log_path`, `input_level`, `radiometric_output`, `array_alignment`, `array_alignment_crop`, `array_alignment_interpolation`, dan `custom_settings`. Nilai utama:

```python
# export_format
"TIFF (16-bit)"           # default, recommended
"TIFF (8-bit)"            # 8-bit TIFF; what all-8-bit images get for a deeper format
"TIFF (32-bit, Percent)"  # reflectance percentage as float32
"PNG (8-bit)"
"JPG (8-bit)"

# debayer
"High Quality (Faster)"                  # standard, default
"Texture Aware (Slow, Highest Quality)"  # neural debayer, Chloros+ only
```

Kenop khusus LATTICE (`input_level`, `radiometric_output`, keluarga `array_alignment*`) didokumenkan dengan jadual nilai penuh mereka dalam [Rujukan [SDK]](reference/sdk-reference.md#supported-values).

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

### Membaca ringkasan pasca-jalankan — dan menangkap pelaksanaan kosong

Apabila selesai, `process()` melampirkan ringkasan pemprosesan backend sebagai `result["summary"]`. Setiap entri dalam `summary["hints"]` adalah satu ayat penuh yang menerangkan apa-apa yang ketara — contohnya, mengapa sesuatu larian menghasilkan sifar output — dan setiap petunjuk juga dihantar semula sebagai `UserWarning` Python, jadi larian kosong mendiagnosis sendiri walaupun anda tidak pernah memeriksa dict:

```python
result = cl.process()
for hint in result.get("summary", {}).get("hints", []):
    print("HINT:", hint)
# hints also arrive on the warnings channel:
#   python -W always::UserWarning your_script.py
```

{% hint style="warning" %}
**`process()` tidak membangkitkan ralat apabila suatu pelaksanaan tidak menghasilkan sebarang imej.** Inilah satu-satunya tempat di mana SDK dan CLI dengan sengaja berbeza: `chloros-cli process` menganggap "produk telah diminta, tiada yang ditulis" sebagai kegagalan dan keluar dengan nilai bukan sifar, manakala SDK keluar seperti biasa dan melaporkan keadaan tersebut melalui `summary` / petunjuk. Jika pipeline anda sepatutnya berhenti pada larian kosong, semak sendiri — periksa `summary` (atau kira fail di bawah folder projek) daripada bergantung pada pengecualian.
{% endhint %}

## Smart Connect — perkakasan langsung

Tiga pembantu membuka sesi berterusan dalam kolam perkakasan backend — kolam yang sama digunakan oleh CLI, skrip lain dan tab kamera GUI, jadi skrip SDK wujud bersama mereka tanpa berebut port siri atau jalur lebar rangkaian. Ketiganya memulakan backend tempatan secara automatik jika tiada yang sedang berjalan. Panel DAQ GUI bukan sebahagian daripada kolam: tutup sensor cahaya dalam GUI sebelum membukanya daripada skrip.

### Kamera LATTICE tunggal — `connect_camera`

```python
import chloros_sdk

# Open by serial; reuses existing pool entry if one exists
with chloros_sdk.connect_camera("213800234") as cam:
    cam.set_settings(exposure_time=10000, gain=0.0)   # microseconds, dB
    cam.capture("output/")
```

{% hint style="info" %}
**Laluan relatif akan menuju ke lokasi skrip anda dijalankan (diperbaiki pada 1.3.0).** Dengan backend tempatan, `output_dir` relatif (termasuk `"output"` lalai) — dan laluan lain yang dibuka oleh backend (`burst_dir`, `profile_path`, `save_path`, `mask_path`) — diselesaikan terhadap direktori kerja skrip anda, jadi `cam.capture("output/")` menulis ke `<current folder>/output/`. Dalam 1.2.0, backend yang dipasang menyelesaikannya dalam folder pemasangan sendiri dan gagal dengan "Akses ditolak". Untuk backend jauh, laluan dihantar seperti yang ditaip.
{% endhint %}

### Susunan bersepadu — `connect_array`

`connect_array` adalah titik permulaan yang disyorkan untuk rig multi-kamera. Ia menjalankan aliran smart-prep yang sama seperti GUI: analisis rangkaian, pemilihan automatik lapisan penyelarasan, penyelarasan masa PTP, pemilihan format piksel bagi setiap kamera, penaburan AE, dan pengaktifan pencetus GPIO. **Siri pertama adalah induk** (ia menghasilkan denyut picu perkakasan); selebihnya adalah anak buah.

```python
with chloros_sdk.connect_array(
        ["213800234", "214000533", "214701288", "214701292"]) as arr:
    arr.capture("output/", processing="reflectance")
```

Tambah `smart=True` pada sebarang tangkapan tatasusunan untuk menunggu pendedahan automatik menstabilkan pada semua kamera sebelum mencetuskan.

Baharu dalam 1.3.0, `ArraySession` juga memberikan anda bingkai yang diselaraskan terus, tanpa menulis fail:

* `arr.grab()` mengembalikan satu bingkai bagi setiap kamera, semuanya daripada satu pencetus, sebagai susunan numpy (`{serial: array}`); `.group_id` adalah pencetus kongsi dan `.errors` menamakan mana-mana kamera yang terlepas.
* `arr.frame_stream(fps=5.0)` menghasilkan satu set sedemikian setiap tick, untuk pemprosesan secara langsung dalam saluran anda sendiri.
* `arr.continuous("output/")` menjalankan tangkapan berterusan backend sendiri (mod Berterusan GUI) dan mengembalikan `ContinuousHandle` dengan `status()`, `wait()` dan `stop()` — gunakan ia sebagai ganti gelung tangkapan apabila anda memerlukan setiap pencetus disimpan pada kadar yang tinggi.

Untuk mod tangkapan (Tunggal / Berterusan / Selang / Terpantas), perakam, burst-ke-video, dan penjajaran tatasusunan, lihat [Rujukan SDK](reference/sdk-reference.md#synchronized-array--arraysession-smart-prep).

### Penderia cahaya DAQ — `connect_daq_sensor`

Tanpa sebarang hujah, `connect_daq_sensor()` mengesan secara pintar pengangkutan (keutamaan: Ethernet → BLE → USB):

```python
with chloros_sdk.connect_daq_sensor() as daq:    # smart-detect USB / BLE / ETH
    for frame in daq.latest(n=5):
        print(frame["spectrum"][:10])
```

Setiap bingkai membawa `spectrum` 135-titik (W/m²/nm apabila dikalibrasi), sebuah bendera `is_saturated`, dan CIE `x`, `y`, `z`. Untuk menetapkan sensor atau pengangkutan tertentu, hantar satu petunjuk eksplisit:

```python
daq = chloros_sdk.connect_daq_sensor(transport="usb", port="COM3")
daq = chloros_sdk.connect_daq_sensor(mac="AA:BB:CC:DD:EE:FF")        # implies BLE
daq = chloros_sdk.connect_daq_sensor(eth_host="daq-e-xxx.local")     # implies Ethernet
```

Perhatikan bahawa profil pembetulan penutup (`cap_id`) **bukan** tombol dalam SDK — pilihnya melalui `chloros-cli daq pool-connect --cap-id …` / `pool-set-cap` sebaliknya.

### Projek yang disimpan — `open_project`

Projek Chloros yang disimpan mengekalkan perkakasan bersambungnya (`cameras.json` + `sensors.json` bersama `project.json`), dan `chloros_sdk.open_project(path)` boleh menyambung semula kameranya, larian dan penderia cahaya sekaligus serta mengawal tangkapan mengikut nama peranti. Penderia cahaya disambungkan semula melalui backend Chloros yang sedang berjalan, dengan tetapan dan cap yang disimpannya. Lihat [Automasi Projek](reference/sdk-reference.md#project-automation--chlorosproject) dalam rujukan.

## Apa yang diperoleh dengan pemasangan pip sahaja

Semak penanda ketersediaan peringkat modul sebelum menggunakan permukaan perkakasan:

```python
import chloros_sdk
print(chloros_sdk.__version__)
print("CAMERA_AVAILABLE =", chloros_sdk.CAMERA_AVAILABLE)    # True iff lattice_sdk imported cleanly
print("PROJECT_AVAILABLE =", chloros_sdk.PROJECT_AVAILABLE)  # True iff ChlorosProject deps available
```

Pada hos dengan **hanya** `pip install chloros-sdk` dan tiada pakej desktop Chloros:

* `ChlorosLocal`, `process_folder`, dan `process_lattice_capture` **tidak** berfungsi — mereka memerlukan binari backend yang disertakan dalam pemasang desktop.
* Pembantu sambungan pintar (`connect_camera`, `connect_array`, `connect_daq_sensor`) adalah klien HTTP tulen, jadi ia berfungsi dengan backend pada mesin lain — tetapi backend yang disertakan hanya mengikat pada gelung balik, jadi anda mesti meneruskan port sendiri (contohnya `ssh -N -L 5000:127.0.0.1:5000 user@chloros-host`) dan menghantar `backend_url="http://127.0.0.1:5000"` dengan `auto_start_backend=False`. Lihat [Mod Backend Jauh](reference/sdk-reference.md#remote-backend-mode-pip-only-host-via-tunnel).
* Kelas LATTICE perkakasan langsung (`LatticeCamera`, `CameraPool`, …) import, tetapi memerlukan runtime Arena SDK daripada pakej desktop — tanpa ia `CAMERA_AVAILABLE` adalah `False`.
* Penderia DAQ dikendalikan hanya melalui backend (terowong) — `connect_daq_sensor()`, atau `proj.sensors` projek; backend membuka penderia pada mesinnya sendiri.

## Pembantu AI — `chloros-mcp`

SDK merangkumi `chloros-mcp`, sebuah pelayan MCP yang membolehkan Claude Desktop, Claude Code, Cursor dan klien MCP lain mengendalikan Chloros: memproses imej, dan menjalankan kamera LATTICE, tatasusunan dan penderia cahaya DAQ. Alat-alatnya merangkumi apa yang dilakukan oleh SDK ini. Ia dipasang bersama SDK, jadi untuk Claude Code penyediaannya hanya satu baris:

```bash
claude mcp add chloros -- chloros-mcp
```

Klien lain: [Menggunakan Chloros dengan Pembantu AI](ai-assistants.md). Pilihan dan setiap alat: [MCP Server (Penolong AI)](reference/sdk-reference.md#mcp-server-ai-assistants).

## Keperluan lesen

Akses SDK memerlukan log masuk Chloros+ aktif pada mana-mana pelan berbayar — **Tembaga atau ke atas**(Tembaga / Gangsa / Perak / Emas); tingkat Iron percuma tidak mempunyai akses SDK/CLI. Penguatkuasaan adalah**di pihak pelayan**: setiap permintaan SDK mesti disertakan dengan sesi langsung dan pelan berbayar, atau backend akan mengembalikan `403` / `PLAN_UPGRADE_REQUIRED` (dibangkitkan sebagai `ChlorosLicenseError` oleh `ChlorosLocal`, dan sebagai `ChlorosConnectError` oleh pembantu `connect_*`). Pemanggil yang log keluar mendapat `401` / `AUTH_REQUIRED` (`ChlorosAuthenticationError`) sebaliknya — menjalankan semula `chloros-cli login` membetulkan kes pertama tetapi tidak kes kedua.

Penggunaan luar talian berfungsi dalam tempoh kurniaan pelan: peringkat dibaca daripada cache pengesahan pelayan (5 minit) atau cache lesen bertandatangan terikat mesin (30 hari untuk pelan bulanan; sehingga tamat langganan untuk tahunan). Apabila tempoh lanjutan tamat, pelan akan diubah kepada percuma dan akses SDK akan terhenti sehingga mesin berjaya mencapai pelayan sekali. `chloros-cli status` kekal boleh diakses pada peringkat percuma supaya sebabnya sentiasa dapat dilihat. Lihat [Chloros+ Login](chloros+-login.md).

## Pengecualian

Tangkap kelas asas untuk mengendalikan "apa sahaja yang salah dengan Chloros":

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

Semua pengecualian saluran (`ChlorosBackendError`, `ChlorosConnectionError`, `ChlorosLicenseError`, `ChlorosAuthenticationError`, `ChlorosConfigurationError`, `ChlorosProcessingError`) diwarisi daripada `ChlorosError`. Satu perangkap: `ChlorosConnectError` — hanya diangkat oleh `connect_camera` / `connect_array` / `connect_daq_sensor` — diwarisi daripada `Exception` biasa, **bukan** daripada `ChlorosError`, jadi `except ChlorosError` tidak akan menangkapnya. Hierarki penuh terdapat dalam [Rujukan [SDK]](reference/sdk-reference.md#exceptions).

## Lihat juga

* [Rujukan [SDK]](reference/sdk-reference.md) — keseluruhan permukaan API, dioptimumkan untuk pembantu AI.
* [Rujukan CLI](reference/cli-reference.md) — setiap subperintah CLI mencerminkan panggilan SDK.
* [Menggunakan Chloros dengan Pembantu AI](ai-assistants.md) — penyediaan `chloros-mcp` dan resipi arahan.
* [Muat Turun](download.md) — pemasang untuk Windows dan Linux.
