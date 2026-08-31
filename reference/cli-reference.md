# Chloros Rujukan CLI

**Versi:**

1.2.0**Dijana:**2026-07-29 19:19 ·**Disemak:** 2026-08-30**Kumpulan Sasaran:** Disesuaikan untuk penggunaan LLM; manusia-boleh dibaca oleh manusia.**Skop:** Setiap subperintah `chloros-cli` yang berdepan dengan pengguna, beserta pilihan dan contoh yang boleh disalin dan ditampal.

Dokumen ini adalah rujukan lengkap untuk perintah `chloros-cli`alat baris arahan yang disertakan dengan MAPIRChloros. Ia sengaja disusun secara menyeluruh supaya LLM (atau manusia) dapat menyusun sebarang aliran kerja yang disokong daripada senarai di bawah tanpa memeriksa kod sumber.

Jika anda hanya memerlukan perkara utama, lompat ke:
- [Permulaan Pantas Lima Minit](#five-minute-quickstart)
- [Aliran Kerja Sambungan Pertama Kamera LATTICE](#lattice-camera-first-connect-workflow)
- [Aliran Kerja Sambungan Pertama Penderia DAQ](#daq-sensor-first-connect-workflow)
- [Smart-AE / Smart-Capture](#smart-ae--smart-capture)
- [Mod Rakaman, Perekod &amp; Pemprosesan Semula Luar Talian](#capture-modes-recorders--offline-reprocess)

---

## Konvensyen

- Semua arahan dimulakan dengan `chloros-cli`. Pada Windows, binari ialah `chloros-cli.exe`; pada Linux/Jetson ia ialah `chloros-cli`.
- Argumen pilihan ditunjukkan sebagai `--flag`. Argumen posisi yang diperlukan ditunjukkan tanpa kurungan.
- Apabila nilai lalai diberikan, mengabaikan penanda akan menggunakan nilai tersebut.
- CLI adalah klien HTTP nipis ke atas backend Chloros (pelayan Flask pada `127.0.0.1:5000`). Backend dimulakan secara automatik oleh kebanyakan arahan. `CHLOROS_BACKEND_URL=<url>` menunjuk kepada **`lattice`**,**`project`**, dan *keluarga arahan **`daq pool-*`** pada backend jauh — arahan teras (`process`, `login`, `logout`, `status`, `export-status`, `time-sync`, `selftest`) sengaja menetapkan `http://127.0.0.1:<port>` dan abaikannya (literal IPv4 mengelakkan Windows&#x27; `localhost`→`::1` ~2 s-denda per-permintaan). Lihat [Pembolehubah Persekitaran](#environment-variables).
- Log masuk akaun Chloros+ diperlukan untuk semua panggilan SDK / CLI (jalankan `chloros-cli login` sekali setiap mesin; disimpan dalam cache di `~/.chloros/`).
- Contoh menggunakan Linux jalur; pada Windows gantikan `/home/user/...` dengan `C:/Users/.../...`.

---

## Sinopsis Peringkat Atas

```
chloros-cli [global options] COMMAND [command options]
```

### Pilihan Global

| Bendera | Deskripsi |
| --- | --- |
| `--backend-exe PATH` | Menimpa fail boleh laku backend yang dikesan secara automatik. |
| `--port N` | Port belakang HTTP (lalai: `5000`). |
| `-v, --verbose` | Aktifkan keluaran terperinci. |
| `--restart` | Mulakan semula backend secara paksa (membunuh mana-mana  yang sedang berjalan0073). |
| `--version` | Cetak versi (`Chloros CLI 1.2.0`). |
| `--help` | Tunjukkan bantuan aras atas. |

### Indeks Perintah

| Perintah | Tujuan |
| --- | --- |
| [`process`](#chloros-cli-process) | Proses satu folder perekodan Survey3 atau LATTICE secara menyeluruh. |
| [`login`](#chloros-cli-login) | Autentikasi mesin ini dengan akaun Chloros+. |
| [`logout`](#chloros-cli-logout) | Padam maklumat log masuk yang disimpan. |
| [`status`](#chloros-cli-status) | Tunjukkan status lesen / pengesahan semasa. |
| [`export-status`](#chloros-cli-export-status) | Benang Langsung-4 kemajuan eksport semasa pelaksanaan `process`. |
| [`language`](#chloros-cli-language) | Tetapkan atau senaraikan bahasa paparan CLI (38 disokong). |
| [`set-project-folder`](#project-folder-commands) / [`get-project-folder`](#project-folder-commands) / [`reset-project-folder`](#project-folder-commands) | Folder projek lalai (dikongsi dengan GUI). |
| [`update`](#chloros-cli-update) | Semak dan pasang kemas kini CLI (Linux/Jetson). |
| [`selftest`](#chloros-cli-selftest) | Diagnostik sistem + ujian asap. |
| [`time-sync`](#chloros-cli-time-sync) | Status/kawalan grandmaster PTP. |
| [`lattice`](#chloros-cli-lattice) | Kawalan &amp; tangkapan kamera LATTICE (45+ subperintah). |
| [`daq`](#chloros-cli-daq) | Kawalan penderia spektral DAQ (DAQ-U / DAQ-M / DAQ-E). |
| [`project`](#chloros-cli-project) | Buka dan jalankan projek Chloros yang disimpan (kamera + DAQ). |

---

## Pemasangan

`chloros-cli` disertakan dalam pemasang desktop Chloros di setiap platform yang disokong — tiada muat turun berasingan untuk CLI. Memasang pakej platform menambah `chloros-cli` ke dalam `PATH` anda bersama aplikasi desktop dan binari backend yang diendalikannya.

Muat turun terkini: [`https://mapir.gitbook.io/chloros/download`](https://mapir.gitbook.io/chloros/download)

> Pemuat juga menyertakan skrip pelancar kemudahan (`Chloros_CLI.bat` / `Chloros_CLI.ps1`, `Launch_CLI.*`, `chloros-cli.sh`) yang membuka satu yang sedia-to-use CLI shell; mereka dibincangkan dalam [Panduan Pengguna CLI](../CLI.md) dan tidak diulang di sini.

### Windows (.exe)

1. Muat turun pemasang Windows daripada halaman muat turun.
2. Jalankan `Chloros-Setup-x.y.z.exe` dan ikuti penolong. Laluan pemasangan lalai ialah `C:\Program Files\Chloros\` (CLI diletakkan di `C:\Program Files\Chloros\cli\`, yang ditambah ke PATH oleh pemasang).
3. Buka terminal baru (`cmd.exe`, PowerShell, atau Terminal Windows) supaya `PATH` yang dikemas kini dikesan.

```powershell
chloros-cli --version
```

Pemasang secara automatik menambah `chloros-cli.exe` ke sistem `PATH` anda dan membungkus runtime Arena SDK yang diperlukan untuk kamera LATTICE.

### Linux amd64 (.deb)

Untuk stesen kerja x86_64 berasaskan Ubuntu 22.04 LTS atau lebih baru / Debian.

> **Ubuntu 20.04 tidak disokong.** Senarai kebergantungan pek ini diperoleh daripada
> apa yang sebenarnya dipautkan oleh backend, dan itu termasuk `libc6 (>= 2.34)`;
> focal membekalkan glibc 2.31. `apt` menolak pemasangan daripada membiarkannya gagal semasa
> runtime.

```bash
sudo dpkg -i chloros-amd64.deb
sudo apt-get install -f         # only if dpkg reports missing dependencies
chloros-cli --version
```

Pemasangan .deb memasang:
- `chloros-cli` ke `/usr/bin/chloros-cli`
- Backend yang disusun kepada `/usr/lib/chloros/chloros-backend`
- Persekitaran runtime Arena SDK (untuk kamera LATTICE)
- Model Denoiser, pakej penentukuran, dan konfigurasi saluran kemas kini

### Linux arm64 — Jetson (JetPack 6)

```bash
sudo dpkg -i chloros-arm64-jp6.deb
sudo apt-get install -f
chloros-cli --version
```

Susun atur yang sama seperti .deb amd64, dengan binaan CUDA yang disesuaikan untuk Jetson Orin / Orin NX / Orin Nano.

### Pengesahan Sekali Saja Bagi Setiap Mesin

Setiap platform memerlukan log masuk Chloros+ sekali sahaja sebelum panggilan SDK / CLI berfungsi:

```bash
chloros-cli login user@example.com 'YourPassword'
```

Butiran pengesahan disimpan dalam `~/.chloros/user_session.json`.

### Semak Pasang

```bash
chloros-cli --version           # prints "Chloros CLI 1.2.0"
chloros-cli selftest            # full 7-step diagnostic (backend, GPU, models, CUDA)
chloros-cli status              # shows license tier + logged-in user
```

> **Langganan Chloros+ diperlukan.**CLI memerlukan pelan Chloros+ aktif.**Copper**ialah peringkat kemasukan Chloros+ — setiap peringkat berbayar Chloros+ mempunyai akses CLI / SDK; hanya percuma**tingkat **Iron** tidak. (Peta ID pelan: `0`=Iron/percuma, `1`=Copper, `2`=Bronze, `3`=Silver, `4`=Gold.) Tingkatkan di [`https://cloud.mapir.camera/pricing`](https://cloud.mapir.camera/pricing).
>
> Lantai ini dikuatkuasakan oleh backend, bukan hanya oleh CLI: sebuah SDK / CLI permintaan yang ditandakan -flagged tanpa pelan berbayar ditolak dengan `403 PLAN_UPGRADE_REQUIRED`, sama ada ia datang daripada `chloros-cli`, Python SDK, atau klien HTTP yang dibangunkan sendiri. Log masukpanggilan yang gagal mendapatkan `401 AUTH_REQUIRED` sebaliknya. Akses berfungsi secara luar talian untuk pelan tersebut tempoh kurnia (30 hari sebulan, sehingga tamat tempoh untuk tahunan) dan berhenti apabila ia tamat; `chloros-cli status` terus berfungsi supaya sebabnya dapat dilihat (ia adalah satu-satunya laluan SDK / CLI yang dikecualikan daripada pintu peringkat — `GET /api/license-status`).

---

## Panduan Permulaan Cepat Lima Minit

```bash
# 1. Sign in once on this machine
chloros-cli login user@example.com 'YourPassword'

# 2. Survey3 / LATTICE folder → finished radiance + NDVI in one call
chloros-cli process "/home/user/captures/flight_001" \
  --vignette --reflectance --indices NDVI NDRE GNDVI

# 3. Take a single LATTICE photo with the first camera found
chloros-cli lattice capture -o output/

# 4. Connect a 4-cam LATTICE array with the GUI's smart-prep flow
chloros-cli lattice array-connect \
  --serials 213800234,214000533,214701288,214701292

# 5. Read a spectrum from a connected DAQ-U
chloros-cli daq pool-connect --port COM3
chloros-cli daq pool-latest --sensor-id CB-7C-A8-2E-5F   # id from 'daq pool-list'
```

---

## `chloros-cli process`

Proses satu folder imej melalui keseluruhan saluran paip Chloros (pengesanan sasaran → penentukuran → vignette → pantulan → eksport indeks).

### Sinopsis

```
chloros-cli process INPUT [OPTIONS]
```

### Argumen Posisi

| Argumen | Deskripsi |
| --- | --- |
| `INPUT` | Laluan ke folder input yang mengandungi `.raw + .jpg` (Survey3), `.tif/.tiff` (LATTICE), atau fail `.dng`. |

### Pilihan Umum

| Bendera | Lalai | Keterangan |
| --- | --- | --- |
| `-o, --output PATH` | satu folder baru yang diberi cap masa di bawah laluan projek lalai anda (`~/Chloros Projects` melainkan dikonfigurasikan) | Folder projek untuk dibuat atau digunakan semula. Jika folder tersebut sudah mengandungi `project.json`, `_1`/`_2` adik-beradik akan dibuat sebaliknya daripada menimpa. |
| `-n, --project-name NAME` | auto (cap masa) | Nama projek. |
| `--debayer {standard,texture-aware}` | `standard` | `texture-aware` menggunakan Chloros+ debayer neural; lebih perlahan tetapi kualiti lebih tinggi. |
| `--vignette / --no-vignette` | `--vignette` | Pembetulan vignet. |
| `--reflectance / --no-reflectance` | `--reflectance` | Kalibrasi reflektansi (menggunakan sasaran panel jika ditemui, kalibrasi per-siri NIST untuk LATTICE). Untuk LATTICE multispektral, ini juga berfungsi sebagai suis **produk** reflektansi — lihat [Perintah Eksport Per-Produk](#per-product-export-toggles-lattice-multispectral). |
| `--ppk` | off | Terapkan pembetulan PPK GNSS daripada fail sidecar. |
| `--exposure-pin-1 MODEL` | off | Menetapkan model &quot;pin-1&quot; rig kamera dwi &quot;Survey3&quot;. |
| `--exposure-pin-2 MODEL` | off | Menetapkan model &quot;pin-2&quot;. |
| `--recal-interval SECONDS` | 0 | Memaksa semula-melakukan matematik kalibrasi setiap N saat masa rakaman. |
| `--timezone-offset HOURS` | local | Menimpa offset zon waktu yang disematkan dalam metadata output. |
| `--format FORMAT` | `TIFF (16-bit)` | Salah satu daripada `TIFF (16-bit)`, `TIFF (32-bit, Percent)`, `PNG (8-bit)`, `JPG (8-bit)`. |
| `--indices NAME [NAME ...]` | tiada | Indeks vegetasi (`NDVI`, `NDRE`, `GNDVI`, `EVI`, `SAVI`, `OSAVI`, `CIG`, …). |
| `--input-level {auto,raw,debayered,processed}` | `auto` | Memaksa titik kemasukan saluran paip untuk LATTICE TIFFs (Survey3.raw tidak terjejas). Juga pintu kecemasan yang membolehkan tangkapan tanpa **raw** diproses sama sekali — lihat [Rupa folder tangkapan](#what-a-captures-folder-looks-like). |
| `--debayered / --no-debayered` | on | Keluarkan produk debayered linear (`Debayered_Images`). Lihat [Per-Product Export Toggles](#per-product-export-toggles-lattice-multispectral). |
| `--preview / --no-preview` | dihidupkan | Keluarkan pratonton paparan (`Preview_Images`): RGB = imbangan putih (DAQ-illuminant apabila tersedia, jika tidak dunia-kelabu) + gamma; multispec = regangan warna palsu. |
| `--radiance / --no-radiance` | on | Keluarkan radiasi float32 (`Radiance_Images`, W/m²/sr/nm). |
| `--reflectance-source {daq,target,auto}` | `auto` | Rujukan untuk produk pantulan LATTICE: `auto` = QA-sasaran dalam bingkai yang dilalui adalah rujukan mutlak, sandaran DAQ-downwelling (ρ = π·L/E); `target` = ketat (tiada penggantian DAQ); `daq` = DAQ-berkuasa. Lihat [Per-Product Export Toggles](#per-product-export-toggles-lattice-multispectral). |
| `--target-reflectance-dir DIR` | tiada | Direktori imbasan pantulan sasaran **diukur** bagi setiap unit (`<serial>.csv`); kembali kepada spektra T3/T4P nominal jika tiada padanan. |
| `--array-alignment / --no-array-alignment` | on | Susunan LATTICE: terapkan penjajaran modul-ke-modul yang dicop dalam  setiap tangkapan000178 XMP kepada setiap produk yang diproses (debayered / pratonton / sinaran / pantulan / indeks). Tiada tindakan untuk imej tanpa tag. |
| `--array-alignment-crop / --no-array-alignment-crop` | crop | Eksport selari dipangkas ke kawasan tumpang tindih biasa matriks supaya semua modul berkongsi jejak yang sama; `--no-…` mengekalkan kanvas penderia penuh (isi hitam di luar sumber). |
| `--array-alignment-interp {bilinear,nearest,cubic}` | `bilinear` | Pengsampelan semula untuk pemerosotan penjajaran. `nearest` mengekalkan DN sumber yang tepat (tiada pencampuran antara piksel nilai radiometrik). |

### Pilihan Pengesanan Sasaran

| Penanda | Deskripsi |
| --- | --- |
| `--min-target-size PIXELS` | Saiz minimum panel-sasaran (px) untuk pengesan. |
| `--target-clustering 0-100` | Sensitiviti pengelompokan. |
| `--target / --targets` | Layan folder input sebagai panel-sasaran sahaja (langkau pengesanan tinjauan). |

### Contoh

```bash
# Simplest: defaults are good for most surveys
chloros-cli process "/home/user/images/survey_001"

# Multi-index with explicit format
chloros-cli process "/home/user/images/survey_001" \
  --vignette \
  --reflectance \
  --format "TIFF (32-bit, Percent)" \
  --indices NDVI NDRE GNDVI OSAVI

# Texture-aware debayer for highest quality (Chloros+ only)
chloros-cli process "/home/user/images/survey_001" \
  --debayer texture-aware \
  --indices NDVI

# Process LATTICE captures explicitly (auto-detects from EXIF normally)
chloros-cli process "/home/user/captures/lattice_flight" \
  --input-level processed

# LATTICE multispectral → float32 radiance only (no DAQ downwelling needed)
chloros-cli process "/home/user/captures/lattice_flight" \
  --no-debayered --no-preview --no-reflectance

# LATTICE reflectance anchored to an in-frame target (strict, no DAQ fallback),
# with per-unit measured target scans looked up by serial
chloros-cli process "/home/user/captures/lattice_flight" \
  --reflectance-source target --target-reflectance-dir "/home/user/target_scans"

# LATTICE array capture: keep native geometry (ignore stamped alignment)
chloros-cli process "/home/user/captures/array_flight" \
  --no-array-alignment

# Aligned, uncropped, value-preserving resampling
chloros-cli process "/home/user/captures/array_flight" \
  --no-array-alignment-crop --array-alignment-interp nearest

# Save to a custom output location with a project name
chloros-cli process "C:/input" -o "C:/output" -n "Field_A_2026-05-26"
```

### Suis Eksport Per-Produk (LATTICE multispektral)

Pemprosesan LATTICE berkembang ke **setiap produk yang berkenaan dalam satu langkah**. Empat suis per-jenis — `--debayered`, `--preview`, `--radiance`, `--reflectance` — semuanya**DIHIDUPKAN secara lalai**; gunakan bentuk `--no-<type>` untuk mematikan satu. RGB master kamera hanya mengeluarkan data debayered + pratonton (tiada radiasi/refleksi bagi setiap jalur), jadi `--radiance`/`--reflectance` adalah no-op untuk mereka. Penukar diabaikan untuk Survey3 `.raw` (yang mengikuti laluan pantulan/sasaran standard). *(Penanda `--radiometric-output {reflectance,radiance,sensor-response}` lama telah **dibuang** dan digantikan dengan suis ini; tiada lagi tahap `sensor-response`.)*

| Produk | Keluaran | DAQ downwelling diperlukan? |
| --- | --- | --- |
| `--debayered` | Demosaik linear (`Debayered_Images`). | Tidak. |
| `--preview` | Pratonton paparan (`Preview_Images`): RGB = WB + gamma; multispec = regangan warna palsu. | Tidak. |
| `--radiance` | float32 W/m²/sr/nm daripada rantaian radiometrik penuh (`Radiance_Images`). | Tidak. |
| `--reflectance` | uint16 pantulan ρ (`32768` = 1.0), Pix4D-ready. | **Ya**, melainkan sasaran dalam bingkai yang lulus QA menjadikannya rujukan (lihat di bawah). |

`--reflectance-source` memilih rujukan pantulan:**`auto`**(lalai) menjadikan sasaran dalam bingkai yang lulus QA sebagai**rujukan mutlak**— rantaian garis empirik berpaut sasaran disemak silang pada panel yang diketepikan dan pemenang yang diukur diterapkan — kembali kepada pembahagi DAQ menuruni (ρ = π·L/E) apabila tiada sasaran atau QA gagal;**`target`**adalah ketat (tiada penggantian DAQ);**`daq`**memilih untuk menggunakan tingkah laku autoritatif DAQ. Geometri sasaran (ArUco / ROI tetap / jalur) diambil daripada konfigurasi sasaran projek; `--target-reflectance-dir DIR` menyimpan imbasan**diukur** bagi setiap unit (`<serial>.csv`) yang dicari berdasarkan nombor siri/QR unit sasaran, dengan T3 nominal/spektra T4P sebagai pilihan sandaran.

Laluan pantulan DAQ menyelesaikan **sinar yang turun ke bawah yang sepadan cap masa**secara automatik daripada**`.daq`* yang dirakam* (DAQ-U/M/E) **atau `.csv` asli DAQ-M**yang ditemui bersama imej. Jika bundel kalibrasi per-kamera atau DAQ tidak disimpan secara tempatan, paip akan**memuat turun secara automatik dari AWS** pada penggunaan pertama (perlu internet sekali sahaja; disimpan dalam cache di bawah `~/.chloros/`).

#### Membaca piksel pantulan (Pix4D / Metashape / skrip anda sendiri)

Refleksans disimpan sebagai DN integer, dan **DN yang bermaksud ρ = 1.0 bergantung pada kamera sumber**:

| Sumber | ρ = 1.0 adalah | Cara untuk mengetahui |
| --- | --- | --- |
| LATTICE (M3C / M3M) | `32768` (ruang lebihan sehingga ρ 2.0) | XMP `Chloros:PixelScale=32768` dicop pada fail. |
| Survey3 | `65535` (dipotong pada ρ 1.0) | Tiada tag XMP `Chloros:*` — ketiadaan itu *ialah* isyaratnya. |

**Baca `Chloros:PixelScale` dan bahagikannya dengannya** daripada menganggap ia sebagai pemalar. Tag ini ditakrifkan dalam domain uint16, jadi ia kekal `32768` merentasi format keluaran yang menimbang semula — `TIFF (16-bit)`, `PNG (8-bit)`, `JPG (8-bit)` dan `TIFF (32-bit, Percent)` semuanya menerangkan sendiri (normalkan jenis data yang disimpan kembali ke uint16 terlebih dahulu: ×257 daripada 8-bit, ×65535 daripada float).

> **Satu kes tidak mempunyai skala, mengikut reka bentuk.**Apabila tangkapan sumber 8-bit (BayerRG8) ditulis sebagai TIFF 8-bit, paip pemprosesan memotong kepada 0..255 bukannya menaiktaraf semula, jadi setiap nilai melebihi ρ≈0.008 meratakan kepada 255 dan tiada skala menerangkan fail tersebut. Chloros sengaja mengabaikan kedua-dua tuple `Chloros:PixelScale` dan `MicaSense:RadiometricCalibration` di sana, dan merekodkan sebabnya.**Jika tag itu tiada pada fail pantulan LATTICE, jangan anggap ada skala — eksport semula pada 16-bit atau 32-bit** daripada membahagikan piksel yang tidak pernah boleh dibahagikan.

#### EXIF dibawa ke dalam eksport

`process` menyalin tangkapan sumberGPS block dan ExifIFD-nya ke setiap produk, jadi eksport membawa `FocalLength`, `FNumber`, `ExposureTime`, `ISO`, `DateTimeOriginal` dan `CameraSerialNumber` bersama georujukan.

**`FocalLength` bukan pilihan untuk fotogrametri.** Pix4D menyelesaikan jarak sampel tanah daripada panjang fokus ditambah ketinggian; tanpa tag ini, ia kembali kepada skala yang sangat salah. Pada satu penerbangan kebun oren yang merangkumi 49 tangkapan, tag yang hilang telah mengubah tapak bersaiz 411 m × 160 m menjadi satu yang dibina semula bersaiz 47.8 km × 13 km — sebuah orto 455 MP yang kebanyakannya nodata, yang kemudiannya dibaca sebagai masalah penilehan dan
masalah BigTIFF sebelum sesiapa pun menyemak GSD. Jika orto anda menghasilkan skala yang tidak munasabah, jalankan `exiftool -FocalLength` terlebih dahulu ke atas produk yang dieksport.

Salinan ini sengaja **tidak** `-all:all`: tag struktur IFD0 memecahkan keluaran LATTICE apabila disalin, dan `ExifImageWidth` / `ExifImageHeight` dikecualikan kerana ia menerangkan tangkapan *sumber* — eksport yang pernah diubah saiznya akan membawa dimensi yang bercanggah dengan rasternya sendiri. XMP ditulis terus dan bukannya disalin, kerana ExifTool
membuang tag XMP yang sama dalam invokasi apabila blok XMP disalin (yang akan menyebabkan tag kalibrasi MAPIR hilang).

### Di manakah keluaran disimpan

Produk ditulis **di bawah folder projek, dikumpulkan mengikut kamera dan kemudian mengikut format fail**:

```
<project>/
└── LATT-M3M-L41-F550/                  # one folder per camera model+lens+filter
    ├── tiff16/
    │   ├── Reflectance_Calibrated_Images/
    │   ├── Debayered_Images/
    │   ├── Preview_Images/
    │   └── <INDEX>_Index_Images/        # e.g. NDVI_Index_Images
    └── tiff32/
        └── Radiance_Images/             # float32 radiance always lands here
```

Folder kamera ialah `LATT-<sensor>-<lens>-F<filter>` untuk LATTICE (menyesuaikan dengan EXIF tangkapan `Model`) dan `<model>_<filter>` untuk Survey3 — dua kamera yang berkongsi sensor dan penapis tetapi berbeza dalam lensa mengekalkan pokok berasingan, kerana vignet, medan pandangan dan distorsi berbeza. Format
folder mengikuti `--format`: `tiff16`, `tiff8`, `png8`, `jpg8`, atau `tiff32` untuk `TIFF (32-bit, Percent)`.

> **Setiap produk yang dieksport mengekalkan nama fail SUMBER.** Eksport radiance bagi
> `capture_…_raw.tif` masih dipanggil `capture_…_raw.tif` — ia hanya terletak di
> `tiff32/Radiance_Images/`. **Folder mengenal pasti produk, bukan nama fail**, jadi globbing
> untuk `*radiance*.tif` tidak menemui apa-apa; padankan pada direktori sebaliknya.

### Rakaman penderia cahaya — `.daq` yang telah ditala + `.csv`

`process` juga mengendalikan rakaman `.daq` dalam folder input anda, dan ia **tidak**
tidak memerlukan sebarang imej untuk melakukannya: DAQ-U / DAQ-M / DAQ-E yang diterbangkan bersendirian merupakan tangkapan lengkap, dan satu folder yang mengandungi hanya fail `.daq` adalah input yang sah.

Sebuah DAQ boleh dirakam **tanpa** kalibrasinya — itulah yang dimaksudkan oleh orang awam
[`chloros_scripts`](https://github.com/mapircamera/chloros_scripts) perekod
(`record_daq.py`) melakukan perkara ini secara lalai: mereka menulis kiraan sensor mentah dan menandakan fail tersebut supaya
Chloros boleh mengambil kalibrasi kilang bagi sensor tersebut **secara bersiri** (cache tempatan dahulu,
kemudian MAPIR Cloud) dan menerapkannya. `process` menulis semula keputusan:

```
<project>/
└── Light Sensor/
    ├── <name>_calibrated.daq        # reprocessable archive, declares its bundle
    └── <name>_calibrated.csv        # W/m^2/nm per reading + photometric columns
```

`.csv` membawa satu baris bagi setiap bacaan: cop masa UTC, masa integrasi, jumlah kuasa, lux fotopik/skotopik, PPFD (dan pembahagian biru/hijau/merah), panjang gelombang puncak, kemudian spektrum penuh pada grid panjang gelombang sensor itu sendiri. `.daq` mengimport semula tanpa dikalibrasi buat kali kedua.

Apabila berjaya, pelaksanaan melaporkan `Light-sensor products written: N (calibrated .daq + .csv)`.
Yang dalam kurungan menggambarkan apa yang sebenarnya ditulis, jadi ia berbunyi
`(RAW COUNTS — this sensor has no calibration bundle)` untuk sensor tanpa bundel dan
`(N calibrated, M raw counts)` untuk folder yang memegang kedua-duanya. `[DAQ-EXPORT]` dan `[RUN-SUMMARY]` backend itu sendiri memetik istilah mereka dengan cara yang sama — tiada satu pun daripada tiga boleh menyifatkan eksport mentah sebagai dikalibrasi.

DAQ-U / DAQ-M / DAQ-E rakaman yang bundel penentukuran tidak dapat diambil — anda tidak dalam talian, atau sensor itu tiada penentukuran dalam fail — adalah **dilepaskan dengan sebab** pada baris `[DAQ-EXPORT]`, dan tidak pernah ditulis sebagai fail &quot;telah dikalibrasi&quot; yang mengandungi kiraan mentah.
Sambungkan ke internet dan jalankan semula. Sebabnya adalah sebab yang sebenarnya ditetapkan oleh pembaca untuk fail tersebut (skema tidak dapat dibaca, tiada bundel, ralat penulisan), dan ringkasan pelaksanaan menyenaraikan sebab **berbeza** — dua puluh fail yang dilangkau kerana satu sebab akan dibaca sebagai satu sebab, bukan dua puluh ulangan sebab yang sama.

#### Rakaman DAQ-A dieksport sebagai kiraan mentah

Keluarga **DAQ-A**wujud sebelum sistem bundel bersiri dan tiada bundel penentukuran untuk diambil — ia dikalibrasi di lapangan menentang sasaran pantulan sebaliknya, itulah sebabnya ia tidak pernah memerlukannya. Penolakan rakaman-rakaman tersebut menyebabkan mereka tiada cara langsung untuk mendapatkan nombor mereka, jadi ia dieksport di bawah**nama yang berbeza**:

```
<project>/
└── Light Sensor/
    ├── <name>_raw.daq        # NOT _calibrated
    └── <name>_raw.csv        # raw spectral sensor counts, NOT irradiance
```

Nama fail yang berbeza dan bukannya penanda di dalam fail, kerana tuntutan itu perlu kekal
dihantar melalui e-mel sebagai nama kosong. Header `.csv` menyatakan `raw spectral sensor counts (NOT irradiance)` dan memberi amaran bahawa nilai-nilai tersebut boleh dibandingkan **dalam** fail itu — yang memang itulah kegunaannya dalam penentukuran berasaskan sasaran — dan
bukan merentasi sensor. Baris fotometrik bergantung kuasa (kuasa total, lux fotopik dan skotopik, PPFD) ditulis **NULL** dan bukannya diintegrasikan daripada kiraan, dan ringkasan larian menyatakan `RAW COUNTS` jadi &quot;dieksport&quot; dalam log tidak boleh dibaca sebagai iradiasi.

Legasi **v1.01 / rekodan**v1.02** (yang ditulis oleh DAQ-A-SD) tidak mempunyai epoch bagi setiap bacaan, hanya masa penulisan fail. Pencocok imej↔downwelling masih menolaknya — memadankan a
frame menentang masa penulisan akan salah secara tidak kelihatan — tetapi pengeksport membacanya, dan pencetak CSV mencetak `clock=daq_created_on` supaya produk menyatakan jam mana yang digunakannya.

### Nota

- `process` secara automatik mengesan sama ada folder anda adalah Survey3, LATTICE, atau campuran.
- Aliran kemajuan melalui Server-Sent Events; CLI memaparkan kemajuan secara langsung bagi setiap benang (Mengesan, Menganalisis, Memproses, Mengeksport).
- Untuk Linux/Jetson, CLI memeriksa swap dan mungkin memberi amaran sebelum memproses folder besar. Debayer sedar tekstur juga secara automatik menerapkan had kekerapan GPU pada Jetson berkuasa rendah (Nano, Orin Nano).
- Sekiranya berjaya, larian melaporkan berapa banyak produk imej yang ditulisnya (`Image products written: N`).

#### RANYAHAN YANG TIDAK MENULIS SEBARANG IMEJ GAGAL

Jika anda meminta produk dan larian menulis **tiada** — hanya `project.json` dan
`calibration_data.json` — `process` menganggap itu sebagai kegagalan: ia mencetak
`Processing finished but wrote no image products.` dan **keluar tidak sifar**, supaya skrip boleh
mengkesannya. Mesej itu menamakan folder projek dan punca-punca biasa:

- folder input tidak diiktiraf sebagai tangkapan (semak susun atur dan `--input-level`), atau
- setiap produk yang diminta diabaikan kerana tidak terpakai untuk kamera tersebut (contohnya meminta
  radiasi/refleksan daripada kamera yang hanya mempunyai RGB).

Jalankan semula dengan `--verbose` dan semak log backend untuk `[LATTICE-EXPORT]` / baris `[EXPORT-CHECK]`,
yang menerangkan tentang lompatan bagi setiap kamera yang tidak sampai ke output CLI.

Pelaksanaan khusus hanya untuk metadata — semua produk dilumpuhkan dan tiada `--indices` — masih merupakan
**kejayaan**, kerana output imej kosong adalah hasil yang betul di situ.

Begitu juga dengan **jalanan hanya-penderia-cahaya**: satu folder rakaman `.daq` tidak mempunyai imej untuk dieksport
oleh definisi, dan larian itu dinilai berdasarkan `.daq` / `.csv` yang dikalibrasi yang ditulisnya sebagai ganti.

---

## `chloros-cli login`

Authentikasi mesin ini dengan akaun awan Chloros+. Butiran log masuk disimpan dalam cache dengan selamat di `~/.chloros/user_session.json`.

```
chloros-cli login EMAIL PASSWORD
```

### Contoh

```bash
chloros-cli login user@example.com 'YourPassword'

# Passwords containing $ should use SINGLE quotes
chloros-cli login user@example.com 'my$ecret$pass'
```

> **PowerShell `$$` mangling is auto-corrected.** In double quotes PowerShell expands `$$` (mengelupasnya daripada, atau menggandakan bahagian-bahagian kata laluan). Pada ralat 401, CLI secara automatik mencuba semula dengan `$$` ditampal semula, kemudian dengan separuh kata laluan yang digandakan dikeluarkan; jika percubaan semula berjaya, ia akan log masuk anda dan mencetak sintaks petik tunggal yang betul untuk digunakan pada kali akan datang.

> **Penggunaan tanpa papan kekal/ber skrip: tiada sesi yang disimpan bermakna prompt interaktif, bukan kegagalan pantas.** Sebarang backend- arahan spawn (`process`, `status`, `export-status`, `time-sync`, …) dijalankan tanpa lesen cache/session akan memasuki prompt interaktif `Email:` / `Password:` pada stdin sebelum meneruskan. Oleh itu, kerja tanpa pengawasan tanpa sesi cache akan tergantung menunggu input — jalankan `chloros-cli login EMAIL PASSWORD` sekali setiap mesin sebelum menjadualkan kerja tanpa kepala.

---

## `chloros-cli logout`

Membersihkan sesi yang disimpan dan memaksa log masuk semula pada panggilan seterusnya.

```bash
chloros-cli logout
```

---

## `chloros-cli status`

Menunjukkan peringkat lesen semasa (Iron/Copper/Bronze/Silver/Gold), pengguna yang disahkan, dan bilangan pengikatan peranti.

```bash
chloros-cli status
```

---

## `chloros-cli export-status`

Semak kemajuan eksport Thread-4 secara langsung. Selamat untuk dipanggil **semasa** pelaksanaan `process` dari shell lain.

```bash
chloros-cli export-status
```

---

## `chloros-cli language`

Tetapkan bahasa paparan CLI (38 disokong, termasuk CJK, RTL, dan Indic). Secara automatik bertukar kepada Bahasa Inggeris pada konsol lama yang tidak dapat memaparkan skrip.

```
chloros-cli language [LANG_CODE] [--list]
```

### Contoh

```bash
# List all available languages
chloros-cli language --list

# Switch to Spanish
chloros-cli language es

# Show the currently-active language
chloros-cli language
```

---

## Arahan Folder Projek

Ini menguruskan lokasi lalai folder projek (dikongsi dengan GUI).

```bash
chloros-cli set-project-folder "/home/user/Chloros Projects"
chloros-cli get-project-folder
chloros-cli reset-project-folder
```

---

## `chloros-cli update` hanya untuk

Linux/ Jetson. Semak `version_url` daripada `/etc/chloros/update.conf` dan menawarkan untuk memuat turun + memasang `.deb` yang sepadan.X.

```bash
chloros-cli update            # check + install
chloros-cli update --check    # check only
```

Pada Linux/Jetson, CLI juga menjalankan **semakan kemas kini automatik pada setiap permulaan** (tidak menyekat, tidak pernah menangguhkan arahan): ia membaca `/etc/chloros/update.conf`, menyimpan hasil dalam cache selama 1 jam dalam `~/.chloros/update_cache.json`, dan mencetak `Update available: vX.Y.Z / Run: chloros-cli update` apabila terdapat versi yang lebih baru. Mengabaikan sebarang ralat secara senyap dan pada Windows.

---

## `chloros-cli selftest`

Melakukan ujian asap 7 langkah: versi, ketersediaan port, permulaan backend, `/api/test`, `/api/system-info` (GPU/CUDA/PyTorch), kewujudan model denoiser, kesediaan CUDA+denoiser.

```bash
chloros-cli selftest
```

---

## `chloros-cli time-sync`

Status dan kawalan grandmaster PTP. HostChloros menjalankan grandmaster PTP; kamera LATTICE dan unit DAQ-E menjadi hamba kepadanya untuk cap masa merentas peranti.

| Subperintah | Deskripsi |
| --- | --- |
| `status` | Tunjukkan status grandmaster, keutamaan BMCA, identiti jam. |
| `peers` | Senaraikan peranti hamba yang dilihat melalui Delay_Req (kamera + penderia DAQ-E). |
| `cameras` | Perkesihatan PTP-kamera (`PtpStatus`, `PtpOffsetFromMaster`, `PtpMeanPathDelay`). |
| `restart` | Mulakan semula proses grandmaster. |
| `set-priority --priority1 N --priority2 N` | Gantikan Keutamaan BMCA. |

### Contoh

```bash
chloros-cli time-sync status
chloros-cli time-sync peers
chloros-cli time-sync cameras
chloros-cli time-sync restart
chloros-cli time-sync set-priority --priority1 1 --priority2 1
```

---

## `chloros-cli lattice`

Kawalan kamera LATTICE. Setiap subperintah dialihkan melalui backend Chloros; backend memiliki kolam kamera jadi panggilan CLI seterusnya menggunakan semula handle terbuka yang sama.

### Pilihan Umum (dikongsi oleh kebanyakan subperintah)

| Bendera | Deskripsi |
| --- | --- |
| `-d, --device N` | Indeks kamera (lalai: 0). |
| `-s, --serial SN` | Siri khusus; keutamaan lebih tinggi daripada `--device`. |
| `--serials SN1,SN2,…` | Siri yang dipisahkan dengan koma untuk operasi berbilang kamera. |
| `--all` | Beroperasi pada setiap kamera yang ditemui. |
| `--exposure US` | Masa pendedahan dalam mikrosaat. |
| `--gain DB` | Keuntungan dalam dB. |
| `--pixel-format FMT` | contohnya `BayerRG8`, `BayerRG12`. |
| `--width N` / `--height N` | Dimensi imej. |
| `--preset {default,high_quality,high_speed,triggered}` | Terapkan pratetapan tetapan. Semua berjalan bebas kecuali `triggered`, yang mengaktifkan kamera untuk edge perkakasan pada Baris 2 — tanpa apa-apa yang memacu talian itu ia akan menunggu selama-lamanya daripada menangkap. |
| `-o, --output DIR` | Direktori output (lalai: `output`). |
| `--packet-size {auto,jumbo,standard,N}` | Saiz paket GVSP. `auto` menjalankan prob ICMP+GVSP; `jumbo` = 9000; `standard` = 1500. |

### Aliran Kerja Sambungan Pertama Kamera LATTICE

```bash
# 1. Discover cameras on the network
chloros-cli lattice info

# 2. Single-cam smoke test: capture one frame.
#    By default this saves EVERY export type applicable to the cam
#    (raw, debayered, radiance, reflectance, preview). Pass e.g.
#    `--processing debayered` to save just one.
chloros-cli lattice capture -o output/

# 3. Connect a synchronized array (RECOMMENDED ENTRY POINT for arrays).
#    This is the same "smart-prep" flow the Chloros GUI uses:
#      - Network capability probe (ICMP DF ping + GVSP probe)
#      - Tier auto-pick (sim-emit / ftd-stagger / slip)
#      - Auto-shrink frame size to fit the wire
#      - PTP enabled by default
#      - Per-cam pixel format auto-pick
#      - AE seeding from the cam's saved state
#      - GPIO trigger config on Line2
chloros-cli lattice array-connect \
  --serials 213800234,214000533,214701288,214701292

# 4. Capture one synced frame group from the live array.
#    Defaults to --processing all (one file per export type per cam);
#    pass a single level to narrow it, e.g. --processing reflectance.
chloros-cli lattice array-capture --processing reflectance -o output/

# 5. Live-preview one cam in your browser
chloros-cli lattice viewer --serial 213800234

# 6. Tear down when done
chloros-cli lattice array-disconnect
```

### Rujukan Subperintah

#### Penemuan &amp; Maklumat

| Subperintah | Tujuan |
| --- | --- |
| `lattice info` | Senaraikan kamera yang disambungkan (pembekal, model, siri, IP, MAC). |
| `lattice probe [--pixel-format FMT] [--json] [--no-discover]` | Analisis sistem hos untuk konfigurasi kamera yang optimum. `--no-discover` melangkau penemuan kamera (lebih pantas, analisis hanya NIC). |
| `lattice network [--fix] [--estimate] [--cameras N]` | Semak/betulkan tetapan NIC; anggaran jalur lebar/FPS. |
| `lattice network-analysis --master SN --slaves SN1,SN2,… [--width N] [--height N] [--pixel-format FMT] [--binning N] [--force-tier TIER] [--backend-url URL] [--json]` | Stabil-skema backend kebolehan rangkaian + cadangan susunan (mengembalikan `status` ∈ `ok` / `auto_shrunk` / `auto_capped_fps` / `needs_force_slip` / `error`). `auto_capped_fps` mengekalkan resolusi yang diminta tetapi mengehadkan fps sasaran — baca `recommended.recommended_target_fps` dan hantarkannya sebagai sasaran sambungan; anggap ia sebagai kejayaan, bukan ralat. |
| `lattice analyze-array [--models M1,M2,…] [--binning N] [--n-active N] [--width N] [--height N] [--pixel-format FMT] [--force-tier TIER] [--json]` | Analisis &#x27;what-if&#x27; tanpa membuka kamera. **`--n-active` adalah jumlah keseluruhan kamera pada rangkaian, bukan hanya bagi susunan ini**— timbulkan ralat ini apabila kamera berdiri sendiri mengalirkan secara serentak, atau apabila bajet rangkaian dikira berdasarkan permintaan yang mengira kurang bilangannya (lalai: `len(--models)`). Sentiasa mencetak agregat `Wire budget:` (MB/s yang diminta vs had selamat bertembung) dan baris `Max cameras:`, dan menandakan `** OVER-SUBSCRIBED**` apabila susunan melebihi langganan wayar — lihat [Array fps &amp; model burst](#array-fps--burst-model). |
| `lattice gpu` | Tunjukkan status GPU. |
| `lattice firmware [--update] [--force] [-y\|--yes]` | Semak atau kemas kini firmware kamera. Pilihan `.fwa` tempatan dipin: fail dalam `firmware/<MODEL_PREFIX>/` yang sepadan dengan `MIN_FIRMWARE_VERSION` binaan akan diflash apabila wujud (versi tertinggi sahaja sebagai sandaran), jadi imej vendor yang lebih baru disimpan pada disk tidak aktif sehingga pin itu diaktifkan — secara sengaja, keluaran yang lebih baru sampai ke unit melalui manifest AWS yang disahkan, yang diutamakan apabila ia lebih baru. |
| `lattice presets [--apply NAME]` | Senaraikan atau terapkan pratetap kamera. |
| `lattice status` | Status kamera langsung. |

#### Rakaman

| Subperintah | Tujuan |
| --- | --- |
| `lattice capture [--format tiff\|png\|jpg] [--jpeg-quality N] [--processing LEVEL] [--levels L1,L2,…] [--force-daq]` | Satu bingkai. **Menyimpan setiap jenis eksport secara lalai** (`--processing all`); lihat [Tahap Eksport Rakaman](#capture-export-levels-the-all-default). `--levels` menyimpan subset eksplisit (menggantikan `--processing`); `--force-daq` menulis bacaan DAQ yang ditetapkan sebagai `.daq` sisikereta walaupun pada tangkapan mentah sahaja. `--jpeg-quality` = kualiti JPEG 1–100 (lalai 95). |
| `lattice continuous [--format tiff\|png\|jpg] [--jpeg-quality N] [--queue-depth N]` | Aliran ke cakera sehingga Ctrl+C. |
| `lattice viewer [--brightness N] [--ae-damping F] [--frame-rate FPS]` | Pratonton MJPEG langsung berasaskan pelayar. `--ae-damping` menetapkan peredaan pendedahan automatik (0.4–100). |

#### Penyetelan Sensor

| Subperintah | Tujuan |
| --- | --- |
| `lattice configure [--get N1 N2…] [--set N=V N=V…] [--dump] [--json]` | Baca/tulis mana-mana nod GenICam. |
| `lattice exposure [--auto] [--auto-once] [--off] [--set US] [--brightness N] [--damping F] [--upper-limit US]` | Pendedahan &amp; AE. |
| `lattice gain [--auto] [--off] [--set DB]` | Penguatan &amp; penguatan automatik. |
| `lattice resolution [--set WxH] [--offset X,Y] [--binning N] [--binning-mode Sum\|Average]` | ROI sensor &amp; binning. |
| `lattice format [--set FMT] [--list]` | Format piksel. |
| `lattice trigger [--mode On\|Off] [--source SRC] [--delay-us US] [--activation EDGE] [--list-sources] [--software]` | Picu perkakasan/perisian. |
| `lattice white-balance [--auto] [--off] [--red R] [--blue B]` (tiada penanda aras = WB satu tembakan) | Operasi WB. RGB/kamera Bayer sahaja; tiada tindakan (dilangkau) pada M3M mono. |
| `lattice color-profile [--set raw\|linear\|natural\|enhanced\|custom_temp] [--cct K] [--get]` | Saluran warna paparan RGB. `natural` (lalai) ialah penyempurnaan langsung murah; `enhanced` menambah defringe + vibrance + kontras tempatan CLAHE untuk rupa pariti-pusat penuh pada kira-kira 2× kos penyempurnaan setiap bingkai, jadi kadar bingkai **langsung** yang lebih rendah — rakaman yang disimpan sentiasa mendapat kemasan penuh dalam apa jua keadaan. RGB /kamera Bayer sahaja; dilangkau pada mono M3M. |
| `lattice color [--saturation N] [--contrast N] [--reset] [--get]` | Saturasi/kontras paparan (kamera penapis RGB). Dilangkau pada mono M3M. |
| `lattice filter [--set NAME] [--list]` | Tetapkan model penapis kamera (`RGN-IMX265`, `OCN`, `NGB`, …). |
| `lattice power [--sleep]` | Uji nod kuasa/termal; togol lalai kuasa rendah. |

#### Kalibrasi &amp; Penderia

| Subperintah | Tujuan |
| --- | --- |
| `lattice calibrate [--filter NAME] [--attempts N] [--save PATH]` | Kalibrasi daripada sasaran pantulan. |
| `lattice dls [--connect] [--spectrum] [--irradiance] [--mac MAC] [--filter NAME] [--json]` | Sinaran ke bawah terbina dalamperintah sensor cahaya-malap. |
| `lattice vignette --input DIR --output DIR [--lens-model KEY]` | Terapkan pembetulan vignet pada imej sedia ada. |

#### Pelbagai Kamera (Sesi Sementara)

| Subperintah | Tujuan |
| --- | --- |
| `lattice multi-info` | Senaraikan semua kamera dengan peranan penyelarasan. |
| `lattice multi-capture [--format FMT] [--jpeg-quality N] [--processing LEVEL]` | Satu bingkai diselaraskan daripada setiap kamera. Menyimpan **semua jenis eksport secara lalai**apabila tatasusunan kekal disambungkan; sandaran sementara tanpa tatasusunan hanya**mendebayer sahaja** (jalankan `array-connect` terlebih dahulu untuk yang lain). |
| `lattice multi-stream [--fps F] [--count N] [--format FMT] [--jpeg-quality N]` | Menyampaikan bingkai yang diselaraskan (seketika). |
| `lattice multi-test [--count N]` | Ujian masa selarasan GPIO. |
| `lattice multi-detect [--line LINE] [--json]` | Pengesanan automatik pendawaian GPIO tuan/hamba. |

#### Penyelarasan

| Subperintah | Tujuan |
| --- | --- |
| `lattice align-calibrate [--method orb\|akaze\|phase\|checkerboard\|manual] [--model translation\|rigid\|affine\|homography] [--frames N] [--checkerboard RxC] [--points PATH] [--reference SN] [--save PATH] [--preview] [--vignette] [--prefilter none\|gradient\|clahe\|blur\|hist_match] [--rms-threshold-px N]` — tombol detektor/pemadanan `[--max-features N] [--ratio-threshold F] [--matcher bf\|flann] [--knn-k N]`, tombol RANSAC `[--ransac-threshold-px F] [--ransac-iters N] [--ransac-confidence F]`, gabungan berbilang bingkai `[--averaging mean\|median\|inlier_weighted]`, kekangan geometri `[--lock-rotation] [--lock-scale] [--lock-axis x\|y]`, sekatan ruang `[--roi X0,Y0,X1,Y1] [--mask PATH]`, dan per-slave mengatasi `[--per-cam-override SN:KEY=VALUE]` (boleh diulang) | Mengira profil penjajaran daripada kamera langsung. `--prefilter` secara lalai menggunakan `gradient` (peta sempadan; memadankan GUI/penjajar tatasusunan — tepi kekal merentasi jalur spektral). `--matcher flann` memberi pulangan di atas ~5000 ciri; `--averaging median` tahan terhadap satu tangkapan buruk, `inlier_weighted` menimbang mengikut bilangan padanan; `--lock-scale` memaparkan ke putaran terdekat (tanpa skala), `--lock-axis` menetapkan sifar satu komponen terjemahan; `--mask` terpakai pada setiap kamera (gunakan `--per-cam-override` untuk tetapan bagi setiap kamera, contohnya `--per-cam-override 214701292:method=phase`). `--rms-threshold-px` menolak untuk menyimpan penentukuran yang RMS reprojeksi melebihi pintu. |
| `lattice align-apply --profile PATH [--format tiff\|png] [--bit-depth 8\|12\|16] [--bands NAMES] [--order NAMES] [--gpu\|--no-gpu] [--no-crop] [--per-camera] [--per-band] [--vignette] [--interpolation nearest\|linear\|cubic\|lanczos] [--border-mode constant\|replicate\|reflect\|wrap] [--border-value N]` | Tangkap satu bingkai berbilang jalur yang selari. `--bit-depth` secara lalai memadankan kamera; `--no-crop` mengekalkan bingkai penuh (pad dengan hitam); `--interpolation` (lalai `linear`) dan `--border-mode`/`--border-value` (default `constant`/0) mengawal warpan CPU — laluan GPU tetap bilinear tanpa mengira apa-apa. |
| `lattice align-stream --profile PATH [--fps F] [--count N] [--bit-depth 8\|12\|16] [--bands NAMES] [--order NAMES] [--gpu\|--no-gpu] [--no-crop] [--per-band] [--vignette] [--interpolation nearest\|linear\|cubic\|lanczos] [--border-mode MODE] [--border-value N]` | Menyampaikan bingkai berbilang jalur selari (warpan kn yang samaseperti `align-apply`). |
| `lattice align-info --profile PATH [--json]` | Tunjukkan butiran profil. |
| `lattice align-reorder --profile PATH [--order NAMES] [--enable SERIALS] [--disable SERIALS]` | Ubah susunan lapisan. |

#### Indeks / Matematik Vegetasi

```bash
# Offline: compute NDVI from an aligned multi-band TIFF
chloros-cli lattice index --input aligned.tif --preset NDVI \
  --output ndvi.tif --colorize --gradient RdYlGn

# Live: discover array, calibrate alignment, capture, compute index, in one go
chloros-cli lattice index --live --profile align.json --preset NDVI \
  --save-multiband -o output/
```

Set bendera penuh: `--input PATH | --live --profile PATH`, `--preset NAME` (NDVI / NDRE / EVI / SAVI / GNDVI /…), `--formula EXPR`, `--channel SYM=BAND` (boleh diulang), `--capture-level raw|debayered|radiance|reflectance|unknown` (tetapkan semula tahap tangkapan yang direkodkan dalam sumber TIFF; lalai: baca daripada metadata TIFF), `--output PATH`, `--output-format all|raw|tif|colorized|lut|png`, `--gradient NAME|JSON`, `--vmin/--vmax/--percentile LO,HI`, `--bg-mode clip|transparent|indexColor|backgroundColor`, `--colorize`, `--list-presets`, `--list-gradients`. Dengan `--live` tombol warp penjajaran juga terpakai: `--save-multiband`, `--gpu/--no-gpu`, `--no-crop`, `--bit-depth 8|12|16`, `--vignette`, `--interpolation nearest|linear|cubic|lanczos`, `--border-mode constant|replicate|reflect|wrap`, `--border-value N`.

> **Simbol `--channel` adalah sensitif huruf besar/kecil.** Simbol sisi mesti sepadan dengan nama saluran preset dengan tepat (preset menggunakan huruf kecil, contohnya,contohnya NDVI = `red`,`nir` — periksa `--list-presets`), dan sisi band mesti sepadan dengan nama band dalam susunan yang sejajar (atau menjadi indeks band berasaskan 0 dalam mod luar talian). `--channel red=Red_660 --channel nir=NIR_850` berfungsi; `--channel RED=660` gagal dengan ralat `channel_map missing entries`.

#### Sambungan Berterusan (Aliran Setara GUI Smart-Prep)

Perintah-perintah ini mengekalkan kamera terbuka dalam kolam backend merentasi invokasi CLI.

| Subperintah | Tujuan |
| --- | --- |
| `lattice cam-connect [--serial SN]` | Tambah satu kamera ke dalam kolam (satu kamera, tiada susunan). |
| `lattice cam-disconnect [--serial SN] [--all]` | Lepaskan. |
| `lattice cam-list` | Senaraikan kamera dalam kolam. |
| **`lattice array-connect`**|**Menyambungkan susunan seragam yang berterusan (titik permulaan yang disyorkan).** Mengjalankan aliran penyediaan pintar GUI sepenuhnya. |
| `lattice array-disconnect [--array-id ID] [--all]` | Melepaskan satu array. |
| `lattice array-list` | Menyenaraikan array yang disambungkan. |
| `lattice array-status [--array-id ID]` | Fps langsung, PTP, ralat terakhir. |
| `lattice array-capture [--processing LEVEL\|all] [--levels L1,L2,…] [--aligned\|--no-aligned] [--index\|--no-index] [--force-daq] [--smart] [--fastest] [--compression deflate\|none] [--continuous\|--interval S] [--count N] [--duration S]` | Satu tangkapan diselaraskan daripada susunan langsung — Satu / Berterusan / Jeda / Terpantas. **Lalai kepada `all`** (satu fail bagi setiap jenis eksport yang berkenaan per kamera). Kamera yang dilangkau (contohnya RGB dikecualikan daripada radiance/reflectance) dilaporkan dengan `Skipped: SN:<serial> (<reason>)`; bacaan DAQ yang digunakan untuk reflectance disimpan bersama dan dilaporkan dengan `DAQ: <path>`. Lihat [Mod Rakaman, Perakam &amp; Pemprosesan Semula Luar Talian](#capture-modes-recorders--offline-reprocess). |
| `lattice array-record [--fps F] [--duration S] [--gif] [--gif-only]` | Rakam gabungan langsung-papan pemuka indeks ke video/GIF (darjah pemantauan; memerlukan aliran gabungan dibuka). |
| `lattice array-burst [--duration S] [--max-frames N] [--build] [--products …]` | Letupan Bayer mentah berfps tinggi (darjah analisis; diproses semula secara luar talian). |
| `lattice array-build-video --burst-dir DIR [--products …] [--fps F] [--save-tiffs] [--gif]` | Memproses semula letupan mentah yang disimpan menjadi video(s) yang dikalibrasi. |

##### `array-connect` Pilihan

| Bendera | Lalai | Keterangan |
| --- | --- | --- |
| `--serials SN1,SN2,…` | cari secara automatik semua kamera LATTICE (perlu ≥2) | Siri pertama adalah MASTER. Apabila diabaikan, penemuan akan menapis kepada model LATTICE (`TRI032*`) dan menyambungkan kesemuanya. |
| `--line {Line0,Line2,Line3}` | `Line2` | GPIO talian selari. |
| `--target-fps F` | auto | Kadar picu pemicu Utama. |
| `--force-tier {sim-capture-sim-emit, sim-capture-ftd-stagger, slip-emit-and-capture}` | auto | Ungguli pemilih lapisan. |
| `--wire-ceiling-mbps MB_PER_S` | dikesan secara automatik | **Belanjawan wayar berterusan hos, dalam MB/s — nilai yang menjadi asas peruntukan keseluruhan tatasusunan.** Kurangkannya apabila tatasusunan melaporkan bingkai rosak GVSP: nilai automatik diperoleh daripada kadar pautan yang diiklankan oleh NIC, yang melebihi-mengesan penyesuai USB, lorong PCIe nipis dan fabrik kongsi yang sibuk. Disimpan dalam blok tangkapan array projek, jadi pembukaan semula / CLI / penyambungan semula SDK memulihkannya. Lihat [Kesihatan Array](#array-health--which-subsystem-is-losing-frames). |
| `--binning {1,2,4}` | auto | Pemeringkatan perkakasan. |
| `--no-recommend` | off | Langkau langkah analisis rangkaian. |
| `--no-ptp` | off | Matikan PTP (cap masa merentas kamera kemudian **tidak** boleh dibandingkan). |

### Smart-AE / Smart-Capture

Susunan LATTICE menjalankan AE berterusan di latar belakang sebaik sahaja ia disambungkan, tetapi babak yang baru diarahkan mengambil masa sedikit untuk mencapai kesepaduan. `array-capture --smart` ialah **kemudahan terbungkus**: ia menunggu AE menstabilkan di setiap kamera dalam susunan, kemudian mencetuskan tangkapan. Gunakan ia apabila anda menukar babak di tengah-tengah sesi.

```bash
# Connect once, then take settled captures whenever you re-point the rig
chloros-cli lattice array-connect --serials SN1,SN2,SN3,SN4
chloros-cli lattice array-capture --smart --processing reflectance -o pose_a/
# (move the rig)
chloros-cli lattice array-capture --smart --processing reflectance -o pose_b/
```

Dasar penyelesaian adalah konservatif secara lalai: had masa tamat 5 saat, tetingkap kestabilan 1.5 saat, toleransi penyebaran pendedahan ±5%. Laras melalui SDK (`ArrayHandle.capture_smart(settle_timeout_s=…, stability_window_s=…, exposure_tolerance_pct=…)`) jika anda memerlukan tingkah laku yang berbeza daripada automasi.

### Tahap Eksport Rakaman (default `all`)

Mulai keluaran ini, `lattice capture`, `lattice multi-capture`, dan `lattice array-capture` **secara lalai adalah `--processing all`** — satu fail yang disimpan bagi setiap jenis eksport yang terpakai pada setiap kamera, sepadan dengan tingkah laku GUI &quot;Capture All&quot;. Tahap-tahap adalah:

| Tahap | Output | Terpakai kepada |
| --- | --- | --- |
| `raw` | Bayer saluran tunggal (kamera mono: jalur tunggal) terus dari sensor. | Semua kamera. |
| `debayered` | Demosaik BGR 3-saluran (kamera mono: skala kelabu 1-saluran). | Semua kamera. |
| `radiance` | float32 W/m²/sr/nm melalui keseluruhan rantaian radiometrik. | Multispektral (M3C/M3M) sahaja — **dilepaskan untuk kamera penapis RGB**. |
| `reflectance` | uint16 ρ (`32768` = 1.0), sedia untuk Pix4D. | Multispektral sahaja, dan **hanya apabila DAQ diikat + kamera telah dikalibrasi**; jika tidak, diabaikan. |
| `preview` / `display` | GUI penuh-rantaian pratonton (CCM + WB + gamma mengikut profil kamera). `lattice capture` menamakan ini `preview`; `array-capture`/`multi-capture` menggunakan `display`. | Semua kamera. |

Serahkan satu tahap untuk menyimpan hanya satu itu (`--processing debayered`). Apabila anda meminta `all`, tahap yang tidak terpakai pada kamera tertentu akan dilangkau (dan dilaporkan), bukan dikesan ralat — kamera yang tidak bersambung atau tidak dikalibrasi masih menerima `raw` / `debayered` / `preview`.

Untuk mana-mana bingkai pantulan, bacaan DAQ ke bawah yang sebenarnya digunakan ditulis ke dalam fail sampingan **`.daq`** di sebelah imej (supaya tangkapan boleh diproses semula kemudian) dan dilaporkan pada baris `DAQ:`.

### Rupa folder tangkapan

Setiap jenis eksport diletakkan dalam **subfolder tersendiri** di bawah `-o`, jadi tangkapan berbilang-tingkat tidak pernah mencampurkan jenis:

```
output/
├── raw/           capture_<ts>_SN<serial>_raw.tif
├── debayered/     capture_<ts>_SN<serial>_debayered.tif
├── radiance/      capture_<ts>_SN<serial>_radiance.tif
├── reflectance/   capture_<ts>_SN<serial>_reflectance.tif
├── preview/       capture_<ts>_SN<serial>_display.tif
├── index/         per-camera vegetation-index (LUT) render, when --index is on
├── composite/     array foreground/background live-view composite, when produced
└── *.daq          the downwelling reading matched to the capture
```

`<ts>` adalah cap masa tangkapan dan `<serial>` siri kamera, jadi satu kumpulan diselaraskan berkongsi cap masa merentas kamera. **Perhatikan satu ketidaksimetrian:** tahap `display` disimpan dalam folder
bernama `preview/` manakala fail itu sendiri mengekalkan `_display` dalam namanya — folder dan sambungan berbeza
hanya untuk tahap itu. Tahap yang tidak diketahui akan menggunakan folder dengan nama mereka sendiri, dan jika subfolder tidak dapat dibuat, fail itu akan ditulis ke root output dan bukannya hilang.

**Pemprosesan semula folder captures:**arahkan `chloros-cli process` ke**captures root**
(`output/`). `process` biasanya hanya mengimport folder yang anda namakan, tetapi apabila folder itu tidak mengandungi sebarang imej dan mempunyai subfolder, ia akan menelusuri secara automatik — jadi subfolder aras akar dan XPROT akarX000522 diambil sekaligus. Setiap tahap tangkapan diimport sebagai satu imej dengan tahap-tahap lain tersedia sebagai mod, bukannya satu imej bagi setiap tahap.

Menamakan **subfolder aras** secara langsung (contohnya `output/raw/`) juga berfungsi. Berbuat demikian akan meninggalkan root
`.daq`, oleh itu salin atau tujukan bacaan DAQ bersebelahan apabila anda menghasilkan semula produk radiometrik daripada `raw/` — jika tidak, padanan cap masa tidak mempunyai apa-apa untuk dirujuk.

**Pemprosesan sentiasa bermula dari `raw`.** Dalam setiap tangkapan, bingkai mentah adalah sumber saluran;
`debayered`, `radiance`, `reflectance` dan `preview` disertakan sebagai mod boleh dilihat tetapi tidak pernah dimasukkan semula ke dalam saluran pemprosesan. Memproses semula produk terbitan akan menerapkan semula vignette, CCM dan
matematik sinaran yang sudah tertanam dalam pikselnya, jadiChloros menolaknya daripada memproses dua kali. Dua akibat yang perlu diketahui:

- Render `index/` dan `composite/` **tidak pernah** diproses. Mereka adalah keluaran, bukan tangkapan —
  render LUT NDVI tidak mempunyai tafsiran sinaran yang bermakna.
- Folder captures yang dieksport **tanpa** `raw` (contohnya `array-capture --processing reflectance`) tidak mempunyai sumber saluran yang sah. Rakaman tersebut diimport dan dipaparkan dengan normal, tetapi `process` melangkau mereka dan memberitahu demikian:

  ```
  [IMPORT-LEVEL] Skipping 4 already-processed file(s) with no raw source: capture_…_reflectance.tif
  [IMPORT-LEVEL] Processing starts from raw. Re-capture with --processing raw, or force an entry
                 point with --input-level.
  ```

  Jika anda benar-benar perlu memajukan produk terbitan — sesi hab yang dirakam dengan
  `demosaic` dihidupkan, atau folder warisan — `--input-level {raw,debayered,processed}` memaksa titik kemasukan dan menafikan arahan langkau. Penanda itu adalah pintu keluar yang disengajakan; `auto` (lalai) tidak pernah memproses tangkapan yang tiada data mentah.

### Rakaman yang Dilangkau dalam Susunan Penapis Campuran

Apabila anda mencampurkan kamera RGB dan multispektral dalam satu susunan, `array-capture --processing radiance` (atau `reflectance`) menyimpan bingkai multispektral dan **melangkau** kamera RGB — sinaran per-Bayer tidak bermakna untuk penderia jalur lebar. CLI mencetak setiap fail yang disimpan (dengan tahap eksportnya), setiap `.daq` yang ditulis, dan setiap pelepasan secara eksplisit, jadi bilangan fail tidak mengejutkan:

```
  Saved: output/sync_…_SN213800234.tif [reflectance] (SN:213800234, fid:1)
  Saved: output/sync_…_SN214000533.tif [reflectance] (SN:214000533, fid:1)
  Saved: output/sync_…_SN214701288.tif [reflectance] (SN:214701288, fid:1)
  DAQ:   output/sync_…_daq-e-54b5e0.daq
  Skipped: SN:214701292 (reflectance-not-applicable-to-rgb-cam filter=RGB)

  3 synchronized frames captured. (1 skipped)
```

Token sebab-melangkau mengikuti corak `<level>-not-applicable-to-rgb-cam`. Reflektansi juga boleh melangkau dengan `reflectance-skipped-no-fresh-dls` / `reflectance-skipped-bound-daq-unavailable (…)`, dan dengan `dls-uncalibrated-band-<nm>` apabila jalur itu kebanyakannya terletak di luar julat yang dikalibrasi secara radiometrik bagi penderia cahaya DAQ (~374–974 nm) — antara SKU penghantaran hanya F988, yang laluan disokong ialah aliran kerja panel pantulan.

Gunakan `--processing debayered` (atau `display`) untuk memasukkan setiap kamera tanpa mengira jenis penapis, atau `all` lalai untuk mendapatkan setiap tahap yang terpakai bagi setiap kamera dalam satu sesi.

---

## Mod Rakaman, Perakam &amp; Pemprosesan Semula Luar Talian

Kesemuanya beroperasi pada **susunan berterusan** (jalankan `array-connect` terlebih dahulu). Ia mencerminkan panel tangkapan GUI.

### Mod `array-capture`

`array-capture` adalah satu perintah dengan empat mod rana serta satu set penukar eksportles:

| Mod | Bendera | Tingkah laku |
| --- | --- | --- |
| **Tunggal** *(lalai)* | (tiada) | Satu kumpulan tangkapan diselaraskan, kemudian keluar. |
| **Berterusan** | `--continuous` | Balik-hingga-balik sehingga `Ctrl+C`, `--count N`, atau `--duration S`. |
| **Interval** | `--interval S` | Satu kali larian setiap `S` saat (diukur dari permulaan setiap larian), had yang sama. |
| **Terpantas** | `--fastest` | Hanya mentah + bacaan DAQ yang ditetapkan + komposit indeks gabungan; melangkau radiasi/reflektan/matematik paparan supaya bingkai tiba dengan pantas. Menyiratkan `--processing raw --force-daq`. Proses semula `.daq` yang disimpan ke dalam produk yang dikalibrasi kemudian. |

Togol eksport (gabungkan dengan mana-mana mod; semua berkongsi GUI/SDK titik akhir):

| Penanda | Kesan |
| --- | --- |
| `--processing LEVEL` | Tahap eksport tunggal, atau `all` (laluan lalai). |
| `--levels L1,L2,…` | Subset eksplisit jenis eksport (contohnya `raw,radiance,reflectance`); **menimpa `--processing`**. |
| `--aligned` / `--no-aligned` | Memutar setiap eksport bukan mentah setiap ahli ke [profil penjajaran](#alignment) tatasusunan (terdaftar bersama). Mentah kekal tidak diwarp tetapi membawa transformasi dalam metadata. Berpaling kepada tidak diselaraskan (dengan amaran) jika susunan tiada profil. |
| `--index` / `--no-index` | Simpan / langkau per-indeks vegetasi-kamera tumpang tindih di mana ia dikonfigurasikan. Lalai: renderkannya. |
| `--force-daq` | Simpan bacaan DAQ/DLS yang ditetapkan sebagai fail sampingan `.daq` walaupun tiada aras yang dipilih memerlukannya (contohnya, tangkapan mentah sahaja), supaya bingkai boleh diproses semula menjadi reflektansi/indeks secara luar talian. |
| `--smart` | Tunggu AE menstabilkan merentasi semua kamera sebelum mencetuskan (lihat [Smart-AE / Smart-Capture](#smart-ae--smart-capture)). |
| `--compression {deflate,none}` | Pemampatan pikselTIFF. `deflate` (lalai) = tanpa kehilangan zlib L1 + peramal mendatar, ~4.1 MB setiap penuh-res frame; `none` = tidak dipadatkan, ~5× lebih pantas menulis pada ~6.3 MB setiap bingkai — gunakan untuk kadar terpelihara maksimum apabila cakera membenarkan. Kedua-duanya tidak kehilangan maklumat dan dibaca secara identik semasa import. |

> **TIFF satu-tulis + model kadar terpelihara.**Sesi tangkapan ditulis dalam**satu**pergerakan fail tifffile yang membawa piksel + XMP + IFD0 Make/Model (diukur pada resolusi penuh Mono12: 36 ms mampat / 6.5 ms tidak mampat, berbanding ~148 ms untuk kaedah lama menulis-kemudian-menulis semula dengan ExifTool); satu-satunya kerja ExifTool yang tinggal (penyempurnaan sub-IFD EXIF) dijalankan oleh pekerja latar belakang asenkron, dan satu bingkai akan lengkap dan sedia untuk diimport walaupun ia tidak pernah dijalankan. Perlu diingat bahawa pemampatan DEFLATE memegang GIL Python, jadi penulisan terkompresi**tidak**dijalankan secara selari merentasi benang penulis per-kamera — merakam 8 kamera pada resolusi penuh secara berterusan pada kadar sensor (~10.4 fps) memerlukan `--compression none`**dan** cakera kelas NVMe (~500 MB/s penulisan berterusan). Suis yang sama didedahkan sebagai `compression` pada `POST /api/camera/array/capture`.

```bash
# Interval timelapse: one reflectance pass every 10 s for 5 minutes
chloros-cli lattice array-capture --interval 10 --duration 300 \
  --processing reflectance -o timelapse/

# Fastest grab for a moving rig — raw + .daq now, calibrate later
chloros-cli lattice array-capture --fastest -o flightline/

# Co-registered multi-band export (drop the index overlay)
chloros-cli lattice array-capture --processing reflectance --aligned --no-index -o out/
```

### `array-record` — video/GIF indeks gabungan (darjah pemantauan)

Merekod apa sahaja yang dipaparkan oleh **pemandangan indeks gabungan langsung** ke dalam `.avi` (dan secara pilihan ke `.gif`). Kerana ia menggunakan komposit langsung, aliran gabungan mesti dibuka (e.contohnya, tatasusunan sedang dipaparkan pratonton dalam GUI) untuk bingkai disimpan. Ia memeriksa kemajuan setiap 2 saat dan berhenti pada `--duration`, `Ctrl+C`, atau apabila perakam menamatkan sendiri.

```bash
# 30-second combined-index clip at 10 fps, plus a GIF
chloros-cli lattice array-record --duration 30 --fps 10 --gif -o monitoring/
```

| Bendera | Lalai | Keterangan |
| --- | --- | --- |
| `--array-id ID` | hanya tatasusunan | Tatasusunan sasaran (abaikan jika hanya satu yang disambungkan). |
| `-o, --output DIR` | `output` | Direktori keluaran (backend-local). |
| `--fps F` | `10` | Kadar bingkai rakaman. |
| `--duration S` | sehingga Ctrl+C | Berhenti secara automatik selepas `S` saat. |
| `--gif` | off | Juga tulis GIF beranimasi. |
| `--gif-only` | off | Hanya tulis GIF (tiada `.avi`). |

### `array-burst` — burst Bayer mentah berfps tinggi (darjah analisis)

Membaca buffer kumpulan diselaraskan gelung tangkapan secara langsung — **tidak memerlukan rantaian penentukuran, exiftool, atau paparan langsung** — jadi ia berjalan pada kadar tangkapan penuh kamera. Menulis bingkai mentah + manifest bagi setiap bingkai + satu `.daq` bagi setiap bacaan DLS yang berbeza di bawah `<output>/bursts/<base>/`. Proses semula secara luar talian (perintah seterusnya), atau masukkan `--build` untuk melakukannya serta-merta apabila berhenti.

```bash
# 5-second raw burst, then build the combined index video in one shot
chloros-cli lattice array-burst --duration 5 --build \
  --products combined:index --fps 10 -o capture/
```

| Bendera | Lalai | Keterangan |
| --- | --- | --- |
| `--array-id ID` | hanya tatasusunan | Tatasusunan sasaran. |
| `-o, --output DIR` | `output` | Direktori output (burst mendarat di `<DIR>/bursts/<base>/`). |
| `--duration S` | sehingga Ctrl+C | Henti automatik selepas `S` saat. |
| `--max-frames N` | tidak terhad | Henti automatik selepas `N` bingkai mentah. |
| `--build` | mati | Selepas menghentikan, segera memproses semula gelombang (sama seperti `array-build-video`). |
| `--products …` | `combined:index` | Dengan `--build`: video mana untuk dibina (lihat di bawah). |
| `--fps F` | `10` | Dengan `--build`: keluar video fps. |
| `--save-tiffs` | off | Dengan `--build`: juga menyimpan TIFF yang dikalibrasi setiap bingkai. |
| `--gif` | off | Dengan `--build`: juga menulis GIF animasi. |

### `array-build-video` — memproses semula burst yang disimpan secara luar talian

Menjodokkan setiap bingkai mentah dengan bacaan `.daq` yang disimpan terdekat dan menolaknya melalui **radiasi / rantaian pantulan / indeks seperti saluran import**, menjana satu atau lebih video.

`--products` ialah senarai koma item `kind:level`, di mana `kind` ∈ `per_cam` | `combined` dan `level` ∈ `radiance` | `reflectance` | `index`. `level` kosong (tanpa `kind:`) secara lalai adalah `per_cam`. Lalai adalah `combined:index`.

```bash
# Per-cam reflectance video for every member + one combined NDVI video
chloros-cli lattice array-build-video \
  --burst-dir "capture/bursts/2026-06-24_141500" \
  --products per_cam:reflectance,combined:index \
  --fps 10 --save-tiffs
```

| Penanda | Lalai | Keterangan |
| --- | --- | --- |
| `--burst-dir DIR` | (diperlukan) | Laluan ke folder burst (`…/bursts/<base>/`). |
| `--products …` | `combined:index` | `kind:level` senarai, seperti di atas. |
| `--fps F` | `10` | fps video keluaran. |
| `--save-tiffs` | off | Juga simpan per-TIFF yang dikalibrasi per frame bersama video(e)s. |
| `--gif` | off | Juga tulis GIF animasi. |

> **Pilih perekod yang tepat.** `array-record` adalah *darjah pemantauan* — ia merakam komposit langsung seperti yang dipaparkan dan memerlukan aliran dibuka. `array-burst` → `array-build-video` adalah *darjah analisis* — ia menyimpan data mentah sensor pada kadar penuh dan membina semula video radiasi/reflektan/indeks yang dikalibrasi kemudian, tanpa memerlukan paparan langsung.

### Kamera Jalur Tunggal Mono (M3M)

Barisan **M3M**ialah saudara mono kepada Bayer**M3C**: satu penapis interferens jalur sempit bagi setiap kamera (`M3M-<lens>-F<wavelength>`, contohnya `M3M-L87-F685`), jadi sensor menghasilkan**satu jalur skala kelabu** tanpa mozek Bayer. Tiada apa yang perlu didemosai, tiada silangan antara saluranstalk untuk mencampurkan semula, dan tiada imbangan putih untuk ditetapkan — keseluruhan aliran paip warna RGB-paparan sememangnya tidak terpakai.

Apa maksudnya pada CLI:

- **`lattice white-balance`, `lattice color-profile`, `lattice color`**mengesan kamera mono dan**melangkau dengan mesej satu baris** bukannya menghantar tetapan yang tidak bermakna. Mereka masih berjalan dengan normal pada kamera RGB/Bayer M3C dalam sesi yang sama.
- **`lattice calibrate` / `process --reflectance` / `array-capture --processing radiance`** masih berfungsi — radiasi dan pantulan adalah peta radiometrik *per-band* dan ditakrifkan dengan sempurna untuk satu jalur. Bingkai mono membawa matriks tindak balas sensor **identiti** (tiada pencampuran semula 3×3), jadi satah itu melalui matematik penentukuran tanpa terjejas.
- **Satu kamera mono tidak boleh menghasilkan indeks vegetasi.**NDVI / NDRE / dan lain-lain memerlukan sekurang-kurangnya dua jalur (contohnya Red + NIR). Untuk mendapatkan indeks daripada perkakasan mono, tujukan**beberapa** kamera M3M ke panjang gelombang yang berbeza, selaraskan mereka ke dalam satu susunan berbilang-jalur, dan hasilkan indeks daripadanya:

```bash
# Red (660) + NIR (850) mono pair -> aligned 2-band stack -> NDVI
chloros-cli lattice array-connect --serials SN_RED,SN_NIR
chloros-cli lattice index --live --profile align.json \
  --preset NDVI --channel red=Red_660 --channel nir=NIR_850 \
  --save-multiband -o output/
```

Simbol `--channel` mesti sepadan dengan nama saluran pratetap **persis** (peka huruf besar/kecil; NDVI adalah huruf kecil `red`,`nir` — lihat `--list-presets`), dan nama sisi jalur menamakan satu jalur dalam susunan yang selari (mod luar talian juga menerima indeks jalur berasaskan 0, contohnya `--channel red=0 --channel nir=1`).

Diskriminator di seluruh susunan ialah token `M3M` dalam rentetan model (ia tidak pernah muncul dalam rentetan `M3C`), dipaparkan ke GUI/SDK sebagai `is_mono`.

---

## Penyediaan &amp; Penalaan NIC Host (susunan LATTICE)

Kamera LATTICE menyalurkan GVSP melalui penyesuai Ethernet hos, jadi untuk susunan berbilang kamera, **pemacu**dan**saiz cincin penerimaan** penyesuai itu sama pentingnya dengan kadar pautan. Tetapan yang salah akan muncul sebagai `FRAMES WILL DROP` / pintu `Reduce ROI to enable` dalam panel Tetapan Susunan (dan dalam `lattice network-analysis` / `analyze_array_network()` pada SDK), walaupun kamera itu sendiri berfungsi dengan baik.

### Penyesuai USB 10GbE — Realtek RTL8157 (&quot;Pengawal Keluarga USB 10GbE Realtek&quot;)

| Item | Nilai yang diperlukan | Mengapa ia penting |
| --- | --- | --- |
| **Versi pemacu**|**≥ v10.67 (Jan 2026)**, INF `rtump64x64sta.inf` | Versi lama**2016**pemacu (v10.65, `rtump64x64.inf`) mengendalikan penutupan kuasa dengan salah dan menghasilkan ralat `0x9F` semasa penutupan/pengesahan semula/tidur. Peralihan ini menyebabkan kelewatan (~5 minit masa tamat),`DRIVER_POWER_STATE_FAILURE` (BSOD `0x9F`)** semasa penutupan/mula semula/tidur. Peralihan terhenti (~5 minit tamat masa), pengguna mematikan kuasa secara paksa, dan penutupan tidak bersih yang berulang**merosakkan repositori WMI**(PowerShell/alat mula gagal dengan `Invalid class`) dan**menyangkut susunan USB** pada but seterusnya (kad rangkaian tidak akan diaktifkan; pemacu USB berhenti menyenaraikan). Kemas kini daripada realtek.com (atau pembekal dongle) sebelum bergantung pada permulaan semula yang bersih. |
| **Buffer Penerimaan**— kata kunci `ReceiveBufferLen` |**256**(maksimum pemacu) | Gelang RX NIC. Lalai pemacu ialah**32**meninggalkan hanya ~0.26 MB cincin yang boleh digunakan — terlalu kecil untuk letupan multi-kamera — jadi panel susunan melaporkan `Sim-emit burst … exceeds NIC RX ring usable capacity 0.26 MB` dan menyekat sambungan. Pada**256**cincin itu besar (**~13.5 MB diukur pada hos 10GbE makmal**), memberikan saluran RX ruang yang mencukupi untuk letupan GVSP berbilang-kamera. (Sama ada konfigurasi tertentu sebenarnya *berjaya disambungkan* ditentukan oleh dua pemeriksaan — **drain-aware**semakan kebolehditerima dan semakan**kelebihan langganan agregat** — bukan perbandingan mentah burst-vs-ring; lihat [Array fps &amp; burst model](#array-fps--burst-model).) |
| **URB Terima**— kata kunci `PendingReceives` |**64** (maksimum) | Blok permintaan USB dalam penerbangan; dinaikkan bersama Penimbal Terima untuk penyerapan gelombang. |
| **Jumbo Frame** — kata kunci `*JumboPacket` | **9014** | Diperlukan untuk paket GVSP 9000 bait (6× kurang paket/bingkai berbanding 1500). |

> ⚠️ **Kemas kini pemacu NIC akan RESET sifat-sifat lanjutan ini kepada lalai.**Selepas mengemas kini atau menggantikan pemacu penyesuai,**terapkan semula** `ReceiveBufferLen=256` dan `PendingReceives=64`, jika tidak panel tatasusunan akan berkedip semula walaupun &quot;tiada apa yang berubah pada perkakasan.&quot; Ini adalah punca nombor 1 mengapa rig yang sebelum ini berfungsi tiba-tiba enggan menyambung.

Terapkan dari PowerShell **tingkat tinggi** (gantikan dengan nama penyesuai anda, contohnya `"Ethernet 5"`):

```powershell
Set-NetAdapterAdvancedProperty -Name "Ethernet 5" -RegistryKeyword ReceiveBufferLen -RegistryValue 256
Set-NetAdapterAdvancedProperty -Name "Ethernet 5" -RegistryKeyword PendingReceives  -RegistryValue 64
Get-NetAdapterAdvancedProperty  -Name "Ethernet 5" -RegistryKeyword ReceiveBufferLen,PendingReceives   # verify
```

> **`lattice network --fix` merangkumi penyesuai USB 10GbE.** Ia kini mengesan jenis penyesuai dan melaras kata kunci receive-ring yang betul: `*ReceiveBuffers`→2048 untuk NIC PCIe (Intel I219, dll.), atau `ReceiveBufferLen`→256 + `PendingReceives`→64 untuk Realtek **pengawal USB** 10GbE (yang tidak mendedahkan `*ReceiveBuffers`). Sasaran dikekang kepada maksima yang dilaporkan oleh setiap pemacu (`NumericParameterMaxValue`), jadi ia tidak pernah menulis nilai di luar julat. Jalankan ia dari terminal **tingkat tinggi**; seperti sebarang penalaan berasaskan pendaftaran, perubahan akan berkuat kuasa selepas memulakan semula penyesuai atau but semula. Perintah `Set-NetAdapterAdvancedProperty` manual di atas kekal sebagai alternatif yang baik — ia digunakan secara langsung (mengikat semula penyesuai) tanpa perlu memulakan semula.

### Asas rangkaian (semua pautan LATTICE)

- **Pemberian alamat:** link-local `169.254.0.0/16` (GigE Vision LLA). Host mengambil `169.254.x.x/16` statik; kamera + DAQ-E menetapkan sendiri dalam julat yang sama. Tiada DHCP/gateway diperlukan.
- **Saiz paket:**lebih suka jumbo (9000), tetapi biarkan auto-probe menemuinya — ia mengukur semula pada setiap sambungan dan sudah melihat melebihi had ICMP 1500 bait kamera melalui probe GVSP, jadi ia akan menggunakan jumbo di mana sahaja kabel benar-benar menyokongnya. Pin dengan `CHLOROS_GVSP_PACKET_SIZE_FORCE=9000` hanya apabila anda tahu lebih baik daripada probe, dan lebih suka per-perintah berbanding kekal: pin melangkau probe, jadi jika laluan sebenarnya tidak dapat membawa 9000**setiap** tangkapan tamat masa dengan `SC_ERR_TIMEOUT -1011` (lihat [Pembolehubah Persekitaran](#environment-variables)).
- **Gelung RX berskala dengan `ReceiveBufferLen`:**pada `32` lalai, cincin yang boleh digunakan adalah ~0.26 MB (terlalu kecil untuk sebarang burst multi-kamera); pada `256` maksimum ia besar (~13.5 MB diukur pada hos 10GbE makmal), memberikan ruang yang mencukupi. Sama ada konfigurasi akan disambungkan kemudian ditentukan oleh pemeriksaan kemasukan yang sedar aliran keluar**dan** pemeriksaan lebihan langganan agregat di bawah — bukan perbandingan mentah burst-vs-ring.

### fps Array &amp; model burst

Cara membaca panel Tetapan Susunan (dan `lattice analyze-array` / `analyze_array_network` pada SDK):

- **Burst dijumlahkan bagi setiap kamera pada format piksel sebenar kamera tersebut.**Kamera Mono**M3M**menyalurkan**Mono12 (2 B/px)**; kamera Bayer**M3C**menyalurkan 8- atau 12-bit (TRI032S secara senyap mengeluarkan BayerRG12 walaupun BayerRG8 diminta). Jadi, satu bingkai penuh-resolusi 4-kamera adalah**~12.6 MB jika semuanya 8-bit tetapi ~25 MB dengan tiga 12kamera mono **bit**. Projeksinya menentukan format setiap kamera daripada modelnya (cache identiti), jadi burst sepadan dengan apa yang sebenarnya dipindahkan oleh talian — bukan andaian satu saiz BayerRG8.
- **Penyesuai Ethernet USB dihadkan pada 200 MB/s tanpa mengira label namanya.** Jadual kecekapan yang menukarkan kadar pautan kepada angka berterusan adalah berasal daripada PCIe; NIC USB mengiklankan kadar pautan *Ethernet*-nya tetapi dihadkan oleh bas USB dan pemandunya. Dongle USB 10GbE pernah mencatat kira-kira 1063 MB/s &quot;terjaga&quot; — satu angka yang tidak pernah disiasat — dan kadar yang terhasil merosakkan 6–18% bingkai sambil masih melaporkan fps sasaran yang sihat. NIC yang disambungkan melalui USB kini dihadkan pada **200 MB/s** sebagai nilai mutlak (hadnya adalah bas, jadi ia tidak meningkat seiring dengan papan nama; penyesuai USB 1 GbE menghasilkan ~80 MB/s dan tidak terjejas). `wire_ceiling_source` pada rekod keupayaan menyatakan demikian secara jelas, dan `nic_is_usb` menandakannya. Gunakan `--wire-ceiling-mbps` untuk menimpa sama ada cara.
- **Admittance sedar tentang drain, bukan keseluruhan burst berbanding-ring.** Satu burst serentak hanya perlu muat dengan *backlog sementara* = `max(0, Σ per-cam arrival − host drain) × emit_window`, bukan keseluruhan burst. Pada fabrik hos pantas / kad tangkapan perlahan (hos **PCIe**10G + 4× kad tangkapan 1 GbE: ketibaan ≈ 320 MB/s, drain ≈ 1063 MB/s) hos mengosongkan lebih pantas daripada kamera diisi, backlog ≈ 0, jadi simulasi pelepasan penuh**mengakui**walaupun burst 25 MB melebihi cincin 13.5 MB. Letakkan empat kamera yang sama di belakang penyesuai**USB**10GbE dan kadar saliran ialah 200 MB/s, bukan 1063 — kadar ketibaan melebihi keupayaannya, dan kehilangan itu muncul sebagai bingkai rosak, bukannya kadar bingkai yang lebih rendah. Pada hos 1 GbE, ambang bawah DLThr 31.25 MB/s kamera menyebabkan ketibaan melebihi pengosongan → ia dengan betul**menyekat** (untuk kelas sekatan *ini*, kurangkan ROI atau gunakan binning ≥ 2). Kebenaran masuk adalah salah satu daripada **dua** pintu sambungan — satu lagi ialah pemeriksaan agregat keterpanjangan di bawah.
- **fps yang dijangka adalah had konservatif untuk pengambilan bersiri.**Gelung pengambilan hos pada masa ini menarik buffer setiap kamera secara**bersiri**(~satu tingkap emit per-kamera setiap satu), jadi kitaran itu dihadkan oleh `max(readout+emit, N × emit)` dengan emit per-kamera dikekang pada kamera**pautan akses**(1 GbE ≈ 80 MB/s), bukan pautan naik hos. Untuk susunan 4-kamera resolusi penuh 12-bit, itu adalah**~2.8 fps**, sepadan dengan ukuran ~2.7–3.0 fps yang sengaja**tidak bergantung pada pendedahan**, jadi dalam adegan malap, nilai sebenar boleh sedikit jatuh di bawah had atas apabila pendedahan memanjang. Pengambilan bersiri adalah penentu had fps sebenar; memparalelkannya akan menaikkan had ke arah kadar emit tunggal.
- **Pengagihan berlebihan agregat adalah penghalang sambungan yang ketat.**Ambang peruntukan jalur lebar bagi setiap kamera pada**8 MB/s**(`ARRAY_PER_CAM_FLOOR_BPS`), jadi sebaik sahaja lantai terkunci, permintaan agregat (`per_cam × N`) boleh melebihi**had kabel selamat-langgaran* (`sustained × sim_emit_factor`). Had praktikal penuh-resolusi pada 1 GbE: **6 kamera pada 1500 MTU, 9 dengan jumbo**. Had ini adalah sifat kabel dan lantai sahaja — ia**tanpa mengira saiz bingkai**, jadi**penyusunan (binning) dan ROI yang lebih kecil TIDAK membantu** (mereka mengurangkan bait per *frame*, bukan bait per *saat* yang dipacu oleh GevSCPD); satu-satunya penyelesaian ialah mengurangkan bilangan kamera, menggunakan bingkai jumbo dari hujung ke hujung, atau NIC yang lebih pantas. Gejalanya ialah kehilangan paket GVSP, bukan pengurangan fps secara berhemah, jadi `analyze-array` menetapkan kepada sifar nilai fps yang boleh dicapai dan mencetak `**OVER-SUBSCRIBED**`, dan `array-connect` dengan resolusi tetap **menolak sambungan** (walk-down sebaliknya akan membin semula bingkai ke bawah, yang juga tidak menyelesaikan kelas blok ini). `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED=1` menurunkannya penolakan kepada amaran nyaring untuk kerja bangku — lihat [Pembolehubah Persekitaran](#environment-variables).

### Kesihatan tatasusunan — subsistem mana yang kehilangan bingkai

`GET /api/camera/array/<array_id>/capability` pada tatasusunan yang bersambung membawa blok `health` langsung, yang dinilai semula secara berterusan **tetingkap**10-saat**. Ia memisahkan kehilangan bingkai kepada dua punca yang memerlukan pembetulan bertentangan, daripada melaporkan satu kadar &quot;tidak lengkap&quot; yang tidak menyatakan kedua-duanya:

| Medan | Apa maksudnya | Subsistem mana |
| --- | --- | --- |
| `gvsp_corrupt_rate_pct` (per serial) | Bingkai **telah tiba dan bermasalah secara struktur**— kehilangan pek GVSP. |**Rangkaian**: bajet wayar, pacing, cincin NIC RX, MTU |
| `never_arrived_rate_pct` (per serial) | Bingkai **tidak pernah tiba sama sekali**— kamera tidak mencetuskan, atau tiada apa-apa yang keluar daripadanya. |**Pemicu / selari**: Kabel M8, `--line`, `TriggerMode` |
| `worst_gvsp_corrupt_pct` / `worst_never_arrived_pct` | Kadar kamera terburuk bagi setiap satu. | — |
| `per_cam_rate_pct` | Kadar tidak lengkap gabungan bagi setiap kamera (kedua-dua punca digabungkan). | — |
| `stable_for_seconds` | Berapa lama setiap kamera kekal di bawah 0.01 %. | — |

Di atas 5 % backend mencatat baris `[array-health <id>] WARN` yang menamakan perpecahan — pada pelanggaran pertama, pada tahap keparahanperubahan jalur, sekali seminit selagi ia berterusan, dan sekali apabila ia selesai. Separuh yang rosak mencetak `[gvsp-corrupt <SN>]` pada kesan pertama bagi setiap kamera dan sebab, kemudian pengagregatan setiap 60 saat. Setiap penilaian masih direkodkan dalam fail log backend; pengira bergerak pada setiap penimbal tanpa mengira apa yang dicetak.

Rekod yang sama melaporkan nombor yang menjadi asas bagi keseluruhan peruntukan:

| Medan | Apa maksudnya |
| --- | --- |
| `wire_ceiling_mbps` | Belanjawan wayar terpelihara hos, MB/s. |
| `wire_ceiling_source` | Daripada mana nombor itu datang, dalam kata-kata — contohnya `USB-capped 200 MB/s (was theoretical 1062; PnPDeviceID=USB\VID_0BDA&PID_815A)` atau `user override 120 MB/s (auto said 200)`. |
| `wire_ceiling_is_user_set` | `true` apabila `--wire-ceiling-mbps` (atau medan **Wire Budget** GUI) menetapkannya. |
| `nic_is_usb` | `true` untuk penyesuai Ethernet USB — lihat had 200 MB/s di atas. |

**Membacanya:** `gvsp_corrupt_rate_pct` bukan sifar dengan `never_arrived_rate_pct` pada 0
bermakna pencetus dan penyegerakan kabel adalah sempurna dan 100 % kehilangan adalah pada laluan rangkaian — kurangkan `--wire-ceiling-mbps` dan sambung semula. Corak sebaliknya menunjukkan pada
kabel penyegerakan atau garis pencetus sebaliknya.

> **`--target-fps` bukan pemicu untuk bingkai yang rosak.** Pacing GevSCPD ditulis
> sekali semasa sambungan, jadi menurunkan kadar pencetus mengubah kitaran tugas dan bukan kadar letusan pelepasan serentak. Pengurangan permintaan 5× yang diukur tidak memberikan sebarang penambahbaikan; menurunkan had wayar dari 240 ke 200 MB/s menjadikan rig yang sama berkurang daripada 10.4 % rosak kepada 0.00 %.

> **Pengecilan automatik pertengahan aliran tidak tersedia pada firmware TRI032S.** Susunan yang sedang berjalan tidak dapat membetulkan ini sendiri; putuskan sambungan dan sambung semula supaya pemilih masa sambungan dapat merancang semula dengan had baru.

### Gejala → penyelesaian

| Gejala (Tetapan Susunan / sambung / `analyze_array_network`) | Punca | Penyelesaian |
| --- | --- | --- |
| `FRAMES WILL DROP … exceeds NIC RX ring usable capacity 0.26 MB`, `Reduce ROI to enable` | `ReceiveBufferLen` ditetapkan semula kepada 32 (biasanya selepas kemas kini pemacu) | Tetapkan `ReceiveBufferLen`→256, `PendingReceives`→64; buka semula panel (mulakan semula backend jika ia menyimpan saiz cincin lama dalam cache) |
| But semula/tutup terhenti; kemudian ralat WMI `Invalid class`, NIC tidak dapat diaktifkan, pemacu USB hilang | Pemandu Realtek USB 10GbE lama 2016 → BSOD `0x9F` → pemadaman paksa | Kemas kini pemandu penyesuai ke ≥ v10.67 (2026), kemudian terapkan semula tetapan receive-ring di atas |
| Sambungan berjaya tetapi memaparkan resolusi sub-lokal | Smart-prep secara automatik mengecilkan bingkai untuk muat pada talian | Tingkatkan pautan / terima pengecilan / `--force-tier slip-emit-and-capture` |
| Susunan melaporkan fps sasaran yang sihat tetapi hanya menyampaikan sebahagian daripadanya; `health.gvsp_corrupt_rate_pct` bukan sifar, `never_arrived_rate_pct` 0 | Belanjawan wayar hos yang dianggarkan melebihi-nyatakan apa yang sebenarnya dapat dikekalkannya (biasanya pada penyesuai Ethernet USB, laluan PCIe nipis, atau tisu kongsi) | Sambung semula dengan `--wire-ceiling-mbps` yang lebih rendah dan semak semula blok kesihatan. **Bukan** `--target-fps` — Gpengatur cara evSCPD ditetapkan semasa sambungan |
| Kamera hilang daripada kumpulan yang diterbitkan; `health.never_arrived_rate_pct` tidak sifar, `gvsp_corrupt_rate_pct` 0 | Picu / jalur selari — kamera tidak mencetuskan, bukan masalah rangkaian | Periksa kabel selari M8 dan `--line`; sahkan setiap ahli telah diaktifkan (`TriggerMode=On`) |
| `**OVER-SUBSCRIBED**` / `Wire budget` melebihi had dalam `analyze-array`, atau penolakan sambungan dengan resolusi tetap (`array over-subscribes the wire`) | Jumlah permintaan bagi setiap kamera (kelajuan lantai 8 MB/s × N kamera) melebihi had wayar selamat-langgaran — 6 kamera resolusi penuh pada 1 GbE @1500 MTU, 9 dengan jumbo | Lebih sedikit kamera, bingkai jumbo hujung ke hujung, atau NIC yang lebih pantas. **ROI/binning TIDAK akan membantu** (had atas tidak bergantung pada saiz bingkai). `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED=1` memintas di bangku ujian (menerima kehilangan paket) |

---

## `chloros-cli daq`

Arahan sensor spektral. Dua kelas:
- **`pool-*`**— klien HTTP nipis yang mengendalikan sensor melalui kolam kekal backend.**Ini adalah laluan yang disokong, dan satu-satunya yang terdapat dalam CLI yang dihantar.** Backend memiliki pengangkutan, jadi GUI, skrip CLI dan SDK semuanya berkongsi satu pemegang langsung (live handle) berbanding berlawan untuk port siri.
- **Segala yang lain**(`test`, `record`, `live`, `stream`, `connect`, `info`, `net`, `ota`, `sample-rate`, `calibrate`, `serve`, `ws`, `udp`, `mqtt`, `reflectance`, `login`, `logout`, `status`) — akses perkakasan terus, didokumenkan di bawah untuk kelengkapan. Ini memerlukan pakej Python `daq`, yang**tidak disertakan dalam mana-mana artifak yang dihantar**: CLI yang disusun mengecualikannya (`scripts/Build-CLI.ps1` menetapkan `--nofollow-import-to=daq`, dan pengangkut `pyserial` / `bleak` / `zeroconf` dengan ia), dan pakej PyPI SDK juga tidak mengandungkannya. Ia hanya berjalan daripada sumber semak keluar, jadi anggap ia sebagai laluan pembangunan dalaman MAPIR dan bukannya sesuatu yang patut digunakan.
- **`discover` / `list`** menjembatan kedua-duanya: ia adalah arahan perkakasan terus dari sumber, tetapi pada binaan yang dihantar ia beralih kepada `pool-discover` dan backend menjalankan imbasan. Jadi imbasan berfungsi di mana-mana — yang penting kerana ia satu-satunya cara untuk mempelajari BLE MAC DAQ-M.

> **`chloros-cli daq --help`** (dan `-h` / `help`) menyenaraikan subperintah `pool-*` — bantuan sengaja dialihkan ke klien pool supaya ia mencerminkan arahan yang benar-benar dijalankan. Jika anda memanggil sub-arahan perkakasan terus pada binaan yang dihantar, ia akan keluar dengan ralat eksplisit yang menamakan pakej yang hilang dan menunjukkan anda kembali ke `pool-*`; tiada apa yang gagal secara senyap. (`discover` / `list` adalah pengecualian — mereka dialihkan semula ke `pool-discover` dan berfungsi sahaja.)
>
> **Segala yang diperlukan pelanggan boleh diakses melalui `pool-*`** — sambung, streaming, rakam fail `.daq` yang telah dikalibrasi, dan tukar profil topi. DAQ juga boleh dikendalikan daripada Python dengan `chloros_sdk.connect_daq_sensor()`, yang menggunakan laluan berkongsi yang sama.

### Aliran Kerja Sambungan Pertama Sensor DAQ

```bash
# 1. Smart-detect any DAQ on this machine (Ethernet → BLE → USB precedence)
chloros-cli daq connect

# 2. Detailed scan: every transport, showing the address to connect with.
#    This is how you find a DAQ-M's BLE MAC — unlike a DAQ-E hostname or a
#    DAQ-U COM port, a MAC isn't printed on the device or listed by the OS.
chloros-cli daq discover                      # or: daq pool-discover
chloros-cli daq discover --only ble           # BLE only
chloros-cli daq discover --json               # machine-readable

# 3. Open a persistent pool session (handle stays alive across CLI calls)
chloros-cli daq pool-connect           # smart-detect
chloros-cli daq pool-connect --port COM3                       # DAQ-U on a specific COM port
chloros-cli daq pool-connect --mac AA:BB:CC:DD:EE:FF           # DAQ-M by BLE MAC
chloros-cli daq pool-connect --eth-host daq-e-xxx.local        # DAQ-E by hostname

# 4. List what's in the pool, including the sensor_id you'll use next
#    (DAQ-U ids look like 'CB-7C-A8-2E-5F'; DAQ-E ids like 'daq-e-def330')
chloros-cli daq pool-list

# 5. Read the latest spectrum frame
chloros-cli daq pool-latest --sensor-id CB-7C-A8-2E-5F

# 6. Record a calibrated .daq file for 60s
chloros-cli daq pool-record --sensor-id CB-7C-A8-2E-5F --duration 60 \
  -o ~/Documents/spectra --device-name "field-A"

# 7. Release
chloros-cli daq pool-disconnect --sensor-id CB-7C-A8-2E-5F
```

### Rujukan `pool-*`

| Subperintah | Tujuan |
| --- | --- |
| `daq pool-connect` (smart-detect) | Buka sensor dalam kolam backend. |
| `daq pool-connect --port PORT` | DAQ-U pada port siri tertentu. |
| `daq pool-connect --ble` | DAQ-M melalui BLE, MAC diimbas secara automatik. |
| `daq pool-connect --mac MAC` | DAQ-M melalui BLE pada MAC yang diketahui (menunjukkan `--ble`). |
| `daq pool-connect --eth-host HOST` | DAQ-E melalui Ethernet pada hos yang diketahui. |
| `daq pool-connect --eth` | DAQ-E melalui Ethernet, hos dikesan secara automatik (mDNS + fallback ARP; berfungsi daripada cache ARP kosong pada Windows dan Linux). |
| `daq pool-connect --integration-time MS --frame-avg N --no-ae` | Laras tetingkap integrasi / status AE. |
| `daq pool-connect --no-stream` | Sambung tetapi jangan mula penstriman lagi (lanjutan dengan `pool-stream --start`). |
| `daq pool-connect --cap-id {none, fov_15, fov_30, fov_45, fov_60, fov_90, sunshine_cosine}` | Profil pembetulan kapasiti. Lalai di backend ialah `sunshine_cosine`. |
| `daq pool-discover [--only usb,ble,eth] [--timeout SEC] [--json]` | Imbas setiap pengangkut untuk sensor yang boleh anda sambungkan, tanpa menyambung. **Begitulah cara anda mencari BLE MAC DAQ-M.** `daq discover` / `daq list` akan secara automatik mengalihkan laluan di sini dalam binaan yang dihantar. Penderia yang sudah dibuka dalam kolam tidak disenaraikan — DAQ-M yang disambungkan berhenti mengiklankan — jadi gunakan `pool-list` untuk yang tersebut. |
| `daq pool-list` | Tunjukkan setiap penderia dalam kolam backend. |
| `daq pool-disconnect --sensor-id ID [--all]` | Bebaskan. |
| `daq pool-latest --sensor-id ID [--recent N] [--json]` | N bingkai spektrum terkini. |
| `daq pool-stream --sensor-id ID [--start \| --stop]` | Sambung / hentikan penstriman. |
| `daq pool-record --sensor-id ID [--duration SEC] [--output DIR] [--device-name NAME] [--stop]` | Mulakan / hentikan rakaman .daq. |
| `daq pool-set-cap --sensor-id ID --cap-id CAP` | Tukar profil pembetulan kapasiti pada masa runtime. |

### Subperintah Perkakasan Terus (sumber semakan sahaja — tidak dalam binaan yang dihantar)

> Dinyatakan untuk kelengkapan. Ini memerlukan pakej `daq` Python serta `pyserial` / `bleak` / `zeroconf`, yang mana kesemuanya tidak disertakan dalam binaan kompilasi CLI atau PyPI SDK — ia hanya berjalan daripada sumber MAPIR yang diambil. **Jika anda menggunakan binaan Chloros yang dikeluarkan, gunakan arahan `pool-*` di atas sebaliknya**; ia merangkumi sambungan, penstriman, rakaman dan pemilihan cap.

```bash
chloros-cli daq test --port COM3                           # Verify connection
chloros-cli daq connect --eth                              # Smart-detect over ETH
chloros-cli daq info --eth-host daq-e-xxx.local            # Device summary as JSON
chloros-cli daq discover --only usb,ble --timeout 5        # Scan local interfaces
chloros-cli daq list                                       # Alias of discover
# ^ discover/list are the exception in this section: in a shipped build they
#   fall back to `pool-discover` (the backend does the scan), so they work
#   without a source checkout. The only difference is that the fallback needs
#   the Chloros backend running, as all pool-* commands do.

# Streaming JSON Lines to stdout (pipeable)
chloros-cli daq stream --port COM3 --format jsonl --photometrics

# Record to .daq for 60 seconds
chloros-cli daq record --port COM3 --duration 60 -o ~/Documents/spectra/

# Live spectrum visualization in a window
chloros-cli daq live --port COM3 --record

# Dual-sensor reflectance (ambient + object) → JSON Lines
chloros-cli daq reflectance \
  --ambient-eth-host daq-e-field.local \
  --object-eth-host daq-e-canopy.local \
  --record -o ~/Documents/reflectance/

# Convenience: pick integration_time + frame_avg for a target rate
chloros-cli daq sample-rate --port COM3 --target-hz 5

# Calibration profile management
chloros-cli daq calibrate --port COM3 --list
chloros-cli daq calibrate --port COM3 --set field_calibration_2026_05

# DAQ-E network config (mDNS auto-discovers the host)
chloros-cli daq net --eth-host daq-e-xxx.local set-ip --mode static --ip 192.168.2.20
chloros-cli daq net --eth-host daq-e-xxx.local set-name "sky-sensor"
chloros-cli daq net --eth-host daq-e-xxx.local set-ptp --enabled true --domain 0
chloros-cli daq net --eth-host daq-e-xxx.local set-auto-stream true          # auto-stream on boot
chloros-cli daq net --eth-host daq-e-xxx.local set-require-signature         # require factory-signed cal (fw v1.6.0+; refused while the held cal is unsigned)
chloros-cli daq net --eth-host daq-e-xxx.local set-time                      # push host clock (refused when PTP SLAVE)
chloros-cli daq net --eth-host daq-e-xxx.local set-auth-token --current "" --new "s3cret"   # control-channel auth ("" new = disable)
chloros-cli daq net --eth-host daq-e-xxx.local set-ota-password "newpass"    # change OTA password (min 4 chars)
chloros-cli daq net --eth-host daq-e-xxx.local factory-reset                 # clear all NVS settings and reboot
chloros-cli daq net --eth-host daq-e-xxx.local reboot

# OTA firmware update
chloros-cli daq ota --eth-host daq-e-xxx.local \
  --firmware daq_e_1.21.bin --password mapir-daq-e

# Bridge spectra to other protocols
chloros-cli daq serve --port COM3 --tcp-port 9000           # TCP JSON-lines
chloros-cli daq ws    --port COM3 --ws-port 9001            # WebSocket
chloros-cli daq udp   --port COM3 --udp-port 9002           # UDP broadcast
chloros-cli daq mqtt  --port COM3 --broker mqtt.example.com --topic daq/spectrum
```

---

## `chloros-cli project`

Buka, sambungkan ke, dan jalankan projek Chloros yang disimpan (sebuah folder dengan `cameras.json` + `sensors.json` + `project.json`). Segalanya dialihkan melalui backend supaya GUI dan CLI menghasilkan keadaan perkakasan yang sama.

### Rujukan Subperintah

| Subperintah | Tujuan |
| --- | --- |
| `project open PATH` | Cetak manif peranti projek (kamera, susunan, sensor). |
| `project devices PATH [--reconnect]` | Senaraikan atau jalankan semula penemuan. |
| `project connect PATH [--cameras-only] [--sensors-only]` | Sambungkan setiap kamera / susunan / sensor yang disimpan. |
| `project capture PATH NAME [-o DIR] [--format FMT] [--exposure US] [--gain DB] [--prefix P]` | Rakaman tunggal daripada kamera atau susunan yang dinamakan. |
| `project burst PATH NAME [-n N] [-i S] [-o DIR] [--format FMT] [--exposure US] [--gain DB] [--prefix P]` | Ledakan N-bingkai daripada kamera atau susunan bernama (`-n/--count` lalai 5; `-i/--interval` saat antara bingkai,  lalai 0). Letupan susunan menyingkirkan dwi salinan kumpulan yang diselaraskan berulang (pengawasan ketinggalan) supaya susunan separa kitaran tidak dapat menyerahkan N salinan satu bingkai; mencetak keputusan bagi setiap pengulangan. |
| `project stream PATH NAME [-n N] [--fps F] [-o DIR] [--format FMT] [--exposure US] [--gain DB] [--poll-interval S]` | Aliran ke cakera melalui kerja belakang. `--poll-interval` = saat antara tinjauan `/stats` (lalai 2.0). |
| `project sensor read PATH NAME [--json]` | Bingkai spektrum terkini. |
| `project sensor log PATH NAME --seconds SEC [-o DIR] [--device-name NAME]` | Rekod .daq. |
| `project run PATH RECIPE.yaml` | Jalankan resipi tangkapan YAML/JSON. `--dry-run` mengesahkan tanpa menjalankan. |
| `project align calibrate PATH NAME [--method M] [--model M] [--frames N] [--reference SN] [--max-features N] [--ratio-threshold F] [--ransac-threshold-px F] [--min-matches N] [--max-reproj-err-px F] [--checkerboard RxC] [--name PROFILE]` | Mengira penjajaran untuk satu tatasusunan — lihat [jadual bendera di bawah](#project-align-calibrate-options). |
| `project align status PATH NAME [--json]` | Cetak profil penjajaran semasa. |
| `project align clear PATH NAME` | Buang profil yang disimpan dalam cache. |
| `project align tweak PATH NAME --serial SN --dx N --dy N --rotation-deg N --scale N` | Gesa transformasi satu hamba. |
| `project align export PATH NAME --to FILE` | Simpan profil ke dalam pJSON. |
| `project align import PATH NAME --from FILE [--no-validate]` | Muat profil yang disimpan. |

#### `project align calibrate` Pilihan

| Bendera | Lalai | Keterangan |
| --- | --- | --- |
| `--method {feature_orb, feature_akaze, phase_correlation, checkerboard, manual}` | `feature_orb` | Kaedah penjajaran. **Ejaan ini berbeza daripada `lattice align-calibrate`**, yang menggunakan bentuk ringkas `orb` / `akaze` / `phase`; kedua-dua arahan tidak boleh ditukar pada bendera ini. |
| `--model {translation, rigid, affine, homography}` | `affine` | Transform model untuk memadan. |
| `--frames N` | `1` | Snek bingkai diselaraskan kepada purata. |
| `--reference SN` | tuan rumah | Rujukan siri kamera; setiap ahli lain dipaparkan ke atasnya. |
| `--max-features N` | `5000` | Had kiraan ciri ORB. |
| `--ratio-threshold F` | `0.75` | Ujian nisbah Lowe. |
| `--ransac-threshold-px F` | `3.0` | Ambang inlier RANSAC. |
| `--min-matches N` | `15` | **Pagar kualiti** — tolak penyelesaian di bawah bilangan padanan inlier sebanyak ini. |
| `--max-reproj-err-px F` | `4.0` | **Pintu kualiti** — tolak penyelesaian di atas ralat reprojeksi RMS ini. |
| `--checkerboard RxC` | — | Geometri papan untuk `--method checkerboard`, contohnya `9x6`. |
| `--name PROFILE` | kosong | Nama profil yang disematkan dalam JSON yang disimpan. **Bukan nama tatasusunan** — itu adalah `NAME` berposisi. |

Dua pintu kualiti ini adalah sebab mengapa kalibrasi boleh berjaya dalam penyelesaian dan masih
degaris cerun untuk menyimpan: profil yang gagal pada salah satu daripadanya akan secara senyap menyalahdaftar setiap tangkapan kemudian, jadi ia ditolak daripada disimpan.

### Contoh

```bash
# Open a project and see what it knows about
chloros-cli project open "/home/user/Chloros Projects/Field_A"

# Connect everything saved in the project
chloros-cli project connect "/home/user/Chloros Projects/Field_A"

# Capture from a named camera (defined in cameras.json)
chloros-cli project capture "/home/user/Chloros Projects/Field_A" FrontLeft \
  -o output/ --format tiff

# Capture from a named array
chloros-cli project capture "/home/user/Chloros Projects/Field_A" main_rig \
  -o output/ --format tiff

# Capture with overrides
chloros-cli project capture "/home/user/Chloros Projects/Field_A" main_rig \
  --exposure 5000

# Read a spectrum
chloros-cli project sensor read "/home/user/Chloros Projects/Field_A" Sky --json

# Record a DAQ log
chloros-cli project sensor log "/home/user/Chloros Projects/Field_A" Sky \
  --seconds 120 -o ~/Documents/spectra/

# Align an array (live)
chloros-cli project align calibrate "/home/user/Chloros Projects/Field_A" main_rig
chloros-cli project align status "/home/user/Chloros Projects/Field_A" main_rig

# Run a recipe
chloros-cli project run "/home/user/Chloros Projects/Field_A" recipe.yaml
```

### DSL Resipi

`project run RECIPE.yaml` menerima fail YAML atau JSON yang menerangkan satu siri tindakan:

```yaml
# recipe.yaml
overrides:
  cameras:
    FrontLeft:
      exposure_us: 5000
      target_brightness: 80

stop_on_error: true
actions:
  - apply:
      name: FrontLeft
      settings:
        exposure_auto: "Off"
        gain: 6.0
        gain_auto: "Off"
  - wait: 2s
  - capture:
      name: FrontLeft
      output: pose_a/
      format: tiff
  - stream:
      name: main_rig
      count: 60
      fps: 5
      output: stream/
  - burst:
      name: main_rig
      count: 10
      interval: 0.5
      output: burst_a/
      format: tiff
  - sensor:
      name: Sky
      action: read
```

Tindakan yang disokong: `apply`, `wait`, `capture`, `stream`, `burst`, `sensor`. Tindakan `burst` mengambil `name` (diperlukan), `count` (lalai 5), `interval` (saat, lalai 0), `output`, `format`, dan `settings` (bentuk tetapan setiap kamera yang sama seperti `apply`); susunan burst menggunakan watchdog fresh-synced-group yang sama seperti `project burst`.

Jalankan:

```bash
chloros-cli project run "/path/to/project" recipe.yaml

# Dry-run to validate without firing hardware
chloros-cli project run "/path/to/project" recipe.yaml --dry-run
```

---

## Pembolehubah Persekitaran

| Pembolehubah | Kesan |
| --- | --- |
| `CHLOROS_BACKEND_URL` | Gantikan backend URL (lalai `http://127.0.0.1:5000`) — **hanya diiktiraf oleh keluarga arahan `lattice`, `project`, dan `daq pool-*`.** Arahan teras (`process`, `login`, `logout`, `status`, `export-status`, `time-sync`, `selftest`) pin `http://127.0.0.1:<port>` dan mengabaikan pembolehubah ini (literal IPv4 memintas Windows `localhost`→`::1` ~2 s-per-permintaan penalti), jadi mereka sentiasa menyasarkan mesin tempatan. |
| `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED` | `1` menurunkan taraf susunan melaluipenolakan sambungan langganan (permintaan agregat per-kamera &gt; had wayar selamat-langgaran dengan `pin_resolution`) kepada amaran kuat-dan-teruskan, menerima kehilangan pekelangan GVSP. Guna di bangku sahaja — lihat [Array fps &amp; model letupan](#array-fps--burst-model). |
| `CHLOROS_CLI_MODE` | Ditetapkan oleh CLI itu sendiri; memberitahu backend untuk mengaktifkan pemprosesan selari. |
| `CHLOROS_GVSP_PROBE_FALLBACK` | `0` melangkau probe fallback GVSP (hanya keputusan ICMP). **Ini mematikan jumbo, ia tidak sekadar menenangkan log** — kamera hanya menjawab ping DF sehingga 1500 pada setiap laluan, jadi prob ini adalah satu-satunya yang boleh mengesan jumbo. Menjimatkan ~1 s bagi setiap kamera bagi setiap sambungan; menelan kos ~1.45× had wayar jika rangkaian *boleh* membawa jumbo. SDK memberi amaran apabila anda menetapkannya. |
| `CHLOROS_GVSP_PACKET_SIZE_FORCE` | Menetapkan saiz paket GVSP kepada N bait; melangkau pengesanan sepenuhnya. Utamakan per-perintah (`CHLOROS_GVSP_PACKET_SIZE_FORCE=9000 chloros-cli …`) berbanding menetapkannya secara kekal: saiz yang dipasang berhenti menyesuaikan diri dengan rangkaian di hadapannya, dan memasang 9000 pada laluan yang tidak dapat membawa jumbo menyebabkan **setiap** tangkapan tamat masa dengan `SC_ERR_TIMEOUT -1011`. |
| `TMPDIR` (Linux) | Gantikan direktori ekstraksi onefile Nuitka. CLI secara automatik menggunakan `/mnt/ssd/tmp` jika wujud. |

---

## Kod Keluar

| Kod | Maksud |
| --- | --- |
| `0` | Berjaya. |
| `1` | Kegagalan umum (kebanyakan ralat subperintah). |
| `2` | Ralat hujah. |
| `130` | Diganggu oleh Ctrl+C. |

---

## Petunjuk Penyelesaian Masalah

- **&quot;Log masuk diperlukan&quot;** → Jalankan `chloros-cli login EMAIL PASSWORD` sekali pada mesin ini.
- **&quot;backend tidak dapat dicapai&quot;** → Mulakan aplikasi desktop Chloros, atau jalankan binari backend secara langsung (`chloros-backend`), atau semak `CHLOROS_BACKEND_URL` jika jauh.
- **Perintah `lattice` gagal dengan &quot;pemandu kamera LATTICE tidak ditemui&quot;** → Persekitaran masa larian Arena SDK tidak dipasang; CLI dihantar dengan `win32api` disertakan pada Windows tetapi C runtime adalah sebahagian daripada pemasang GUI.
- **Array connect / Tetapan Array memaparkan &quot;FRAMES WILL DROP&quot; atau &quot;Reduce ROI to enable&quot;** → Cincin penerimaan NIC hos terlalu kecil (biasanya ditetapkan semula kepada 32 selepas kemas kini pemacu NIC). Lihat [Pemasangan &amp; Penyelarasan NIC Host](#host-nic-setup--tuning-lattice-arrays) — tetapkan `ReceiveBufferLen=256`, `PendingReceives=64`.
- **Mesin tergantung semasa but semula/tutup, kemudian WMI `Invalid class` / NIC tidak akan diaktifkan / pemacu USB hilang** → Pemacu penyesuai USB 10GbE lapuk menyebabkan `DRIVER_POWER_STATE_FAILURE` (BSOD `0x9F`). Kemas kini pemacu penyesuai — lihat [Host NIC Setup &amp; Tuning](#host-nic-setup--tuning-lattice-arrays).
- **Amaran pertukaran Jetson** → Tambah pertukaran berasaskan fail; CLI mencetak arahan `fallocate` / `swapon` yang tepat.
- **Arahan langsung DAQ hilang** → Yang dijangkakan: `chloros-cli` yang disertakan sengaja mengecualikan pakej `daq`, jadi hanya `pool-*` yang ada (SDK di PyPI juga tidak menyediakannya). Gunakan `pool-*`, yang memacu sensor yang sama melalui backend, atau `chloros_sdk.connect_daq_sensor()` dari Python.

---

## Lihat Juga

- [Python SDK Rujukan](sdk-reference.md) — setara berprogram bagi setiap arahan CLI.
- [Panduan Sensor DAQ](../daq/README.md) — pendawaian + kalibrasi khusus sensor.
- Dokumen dalam talian: `https://mapir.gitbook.io/chloros/cli`
