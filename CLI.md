# CLI: Baris Perintah

> **Rujukan lengkap:**[Rujukan CLI](reference/cli-reference.md) mendokumentasikan**setiap penanda bagi setiap subperintah** dan dioptimumkan untuk pembantu AI — tampal URL ke dalam pembantu anda dan minta perintah yang berfungsi: `https://mapir.gitbook.io/chloros/reference/cli-reference`
>
> **Petua untuk alat AI:** mana-mana halaman manual ini boleh didapati sebagai Markdown mentah dengan menambah `.md` pada pautan URL (contohnya `https://mapir.gitbook.io/chloros/reference/cli-reference.md`), dan `https://mapir.gitbook.io/chloros/llms.txt` mengindeks keseluruhan manual untuk penggunaan LLM.

<figure><img src=".gitbook/assets/cli.JPG" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: banner shows CLI 1.1.0; reshoot the CLI welcome/banner output on the 1.2.0 build so the version line reads "Chloros CLI 1.2.0" -->


## Apa ituCLI


`chloros-cli` adalah antaramuka baris perintah kepada enjin pemprosesan yang sama digunakan oleh aplikasi desktopChloros
. Ia adalah klien ringanHTTP
di atas backendChloros
(pelayan tempatan pada `127.0.0.1:5000`) — kebanyakan arahan memulakan backend secara automatik, jadi satu panggilan `chloros-cli process …` sahaja sudah mencukupi untuk skrip.

Ia berjalan pada **Windows
10/11 (x64)**dan**Linux
(x86_64, dan NVIDIA Jetson arm64 pada JetPack 6)**, dalam mana-mana terminal, tanpa GUI diperlukan. Semak pemasangan anda dengan:

```bash
chloros-cli --version    # prints "Chloros CLI 1.2.0"
```

Keluarga-keluarga arahan, sekilas pandang:

* **Pemprosesan &amp; akaun** — `process`, `login`, `logout`, `status`, `export-status`, `language` (38 bahasa — lihat [Bahasa Disokong](supported-languages.md)), `set-project-folder` / `get-project-folder` / `reset-project-folder`, `selftest`, `update` (Linux
/hanya Jetson)
* **Perisian langsung** — `lattice` (kawalan kamera LATTICE, 45+ subperintah), `daq pool-*` (penderia cahaya DAQ), `time-sync` (PTP)
* **Automasi** — `project` (mengendalikan projekChloros
yang disimpan secara tanpa kepala, termasuk resipi tangkapan YAML)

Pilihan global yang perlu diketahui: `--port N` (port backend, lalai `5000`), `-v/--verbose`, `--restart` (paksa mulakan semula backend), `--backend-exe PATH`. Lihat [RujukanCLI
](reference/cli-reference.md) untuk senarai penuh.

***

## Pemasangan

CLI
disertakan dalam pemasangChloros
di setiap platform — tiada muat turunCLI
berasingan. Dapatkan pemasang daripada halaman [Muat Turun](download.md).

###Windows


Pemasang meletakkanCLI
di:

```

C:\Program Files\Chloros\cli\chloros-cli.exe
```

dan menambah folder tersebut ke sistem anda `PATH` — **buka terminal baru**selepas pemasangan supaya `PATH` yang dikemas kini dikesan. Pemuat juga meletakkan skrip pelancar (`Chloros_CLI.bat` / `Chloros_CLI.ps1`) di dalam direktori akar pemasangan serta satu**Chloros
CLI
** pautan pintas menu Mula, masing-masing membuka terminal dengan `chloros-cli` sedia untuk digunakan.

###Linux


Pasang `.deb` untuk seni bina anda:

```bash
# Linux x86_64
sudo dpkg -i chloros-amd64.deb

# NVIDIA Jetson (arm64, JetPack 6)
sudo dpkg -i chloros-arm64-jp6.deb
```

Ini memasang `chloros-cli` ke `/usr/bin/chloros-cli` (sudah ada pada `PATH`) dan backend ke `/usr/lib/chloros/chloros-backend`, bersama runtime ArenaSDK
yang diperlukan untuk kamera LATTICE. Rujuk [PemasanganLinux
](linux/linux-installation.md) untuk butiran.

### Verifikasi

```bash
chloros-cli --version    # "Chloros CLI 1.2.0"
chloros-cli selftest     # 7-step diagnostic: backend, API, GPU/CUDA, denoiser models
chloros-cli status       # license tier + logged-in user
```

***

## Log Masuk &amp; Pelesenan

CLI
(dan capaianPython
SDK
) memerlukan **pelanChloros
+ berbayar**— mana-mana peringkat berbayar memilikinya; peringkat percuma tidak. Had ini dikuatkuasakan**di pihak pelayan** oleh backend, bukan oleh binariCLI
: panggilan tanpa log masuk ditolak dengan `401 AUTH_REQUIRED`, dan panggilan pengguna log masuk pada peringkat percuma dengan `403 PLAN_UPGRADE_REQUIRED`, sama ada ia datang daripada `chloros-cli`,SDK
, atau klienHTTP
yang dibangunkan sendiri. Tingkatkan di [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing).

Log masuk **sekali bagi setiap mesin**:

```bash
chloros-cli login user@example.com 'YourPassword'
chloros-cli status
```

<figure><img src=".gitbook/assets/cli login_w.JPG" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: login success output predates 1.2.0; reshoot `chloros-cli login` followed by `chloros-cli status` on the 1.2.0 build showing the license tier line -->


{% hint style="warning" %}
**Kata laluan dengan aksara khas**(`$`, `!`, spaces): wrap the password in**single quotes**, as shown above. In PowerShell double quotes, `$$` diubah suai oleh shell (CLI
mengesan perkara ini pada 401 dan mencuba semula secara automatik, tetapi petik tunggal mengelakkan masalah ini sepenuhnya).
{% endhint %}

Sesi disimpan dalam cache di `~/.chloros/user_session.json` dan terus berfungsi secara luar talian untuk tempoh lanjutan pelan (30 hari untuk pelan bulanan, sehingga tamat tempoh untuk pelan tahunan). `chloros-cli status` berfungsi walaupun tanpa pelan berbayar, jadi sebab penolakan sentiasa dapat dilihat.

{% hint style="danger" %}
**Menjadualkan kerja tanpa kepala? Log masuk dahulu.**Perintah penciptaan backend (`process`, `status`, `export-status`, …) dijalankan dengan**tiada sesi yang disimpan**tidak gagal dengan cepat — ia memasuki prompt interaktif `Email:` / `Password:` pada stdin. Kerana itu, kerja cron tanpa pengawasan atau langkah CI akan**tergantung menunggu input**. Jalankan `chloros-cli login EMAIL 'PASSWORD'` sekali pada mesin sebelum menjadualkan apa-apa.
{% endhint %}

***

## Jalankan Proses Pertama Anda

Arahkan `process` ke folder tangkapan — ia mengesan secara automatikSurvey3
(`.raw` + `.jpg`), LATTICE (`.tif`/`.tiff`), `.dng`, atau campuran:

```bash
chloros-cli process "C:\Images\flight_001"          # Windows
chloros-cli process ~/images/flight_001              # Linux
```

Aliran kemajuan dijalankan secara berasingan bagi setiap benang paip (Mengesan, Menganalisis, Memproses, Mengeksport), dan pelaksanaan yang berjaya diakhiri dengan melaporkan berapa banyak produk imej yang telah ditulis (`Image products written: N`).



<!-- SCREENSHOT-NEEDED: terminal capture of a `chloros-cli process` run on a LATTICE captures folder completing successfully — per-thread progress lines visible and the final "Image products written: N" summary line -->
### Tempat keluaran disimpan

`process` menulis ke dalam **folder projek**, bukan ke dalam folder input anda:

* Tanpa `-o`: projek akan dibuat di bawah folder projek lalai anda (dikongsi dengan GUI; uruskannya dengan `get-project-folder` / `set-project-folder`, `~/Chloros Projects` sandaran), dinamakan oleh `-n/--project-name` atau cap masa (`YYYYMMDD_HHMMSS`) apabila diabaikan.
* Dengan `-o PATH`: folder itu **adalah** folder projek. Jika ia sudah mengandungi `project.json`, `_1`/`_2`… akan dibuat sebagai nama saudari dengan awalan `LATT-<sensor>-<lens>-F<filter>`, bukannya menimpa.

Di dalam projek, produk digruppkan **mengikut kamera, kemudian mengikut format fail**:

```
<project>/
├── project.json
├── calibration_data.json
└── LATT-M3M-L41-F550/                  # one folder per camera model+lens+filter
    ├── tiff16/
    │   ├── Reflectance_Calibrated_Images/
    │   ├── Debayered_Images/
    │   ├── Preview_Images/
    │   └── NDVI_Index_Images/           # one folder per requested index
    └── tiff32/
        └── Radiance_Images/             # float32 radiance always lands here
```

Folder kamera ialah `LATT-<sensor>-<lens>-F<filter>` untuk LATTICE (menyesuaikan dengan EXIF `Model` rakaman) dan `<model>_<filter>` (contohnya `Survey3N_RGN`) untukSurvey3
. Folder format mengikuti `--format`: `tiff16`, `tiff8`, `png8`, `jpg8`, atau `tiff32` untuk `TIFF (32-bit, Percent)`.

{% hint style="info" %}
**Setiap produk yang dieksport mengekalkan nama fail SUMBER.**Eksport radiance `capture_..._raw.tif` masih dinamakan `capture_..._raw.tif` — ia hanya terletak di `tiff32/Radiance_Images/`.**Folder mengenal pasti produk, bukan nama fail**, jadi gunakan glob untuk direktori, bukan untuk suku kata `*radiance*`.
{% endhint %}

### Pilihan yang sebenarnya akan anda gunakan

| Penanda | Lalai | Fungsinya |
| --- | --- | --- |
| `-o, --output PATH` | folder projek lalai | Lokasi folder projek (lihat di atas). |
| `-n, --project-name NAME` | cap masa | Nama projek. |
| `--format FMT` | `TIFF (16-bit)` | Salah satu daripada `TIFF (16-bit)`, `TIFF (32-bit, Percent)`, `PNG (8-bit)`, `JPG (8-bit)`. |
| `--indices NAME [NAME ...]` | tiada | Indeks vegetasi untuk dieksport (lihat [Indeks Vegetasi](#vegetation-indices)). |
| `--debayer {standard,texture-aware}` | `standard` | `texture-aware` = debayer neural, lebih perlahan, kualiti tertinggi (Chloros
+, NVIDIA GPU). |
| `--vignette / --no-vignette` | on | Pembetulan vignet. |
| `--reflectance / --no-reflectance` | on | Kalibrasi reflektansi; untuk LATTICE ini juga suis produk reflektansi. |
| `--input-level {auto,raw,debayered,processed}` | `auto` | Memaksa titik kemasukan saluran paip untuk LATTICE TIFFs. |

Untuk semua perkara lain — penyetelan pengesanan sasaran, PPK, pin pendedahan, bendera penjajaran tatasusunan — lihat [seksyen `process` RujukanCLI
](reference/cli-reference.md).

***

## Memilih Apa yang Akan Dieksport (Produk LATTICE)

Pemprosesan LATTICE berkembang ke dalam **setiap produk yang berkenaan dalam satu langkah**. Empat suis bagi setiap produk semuanya**DIHIDUPKAN secara lalai**; gunakan borang `--no-` untuk mematikan salah satu:

| Penukar | Produk |
| --- | --- |
| `--debayered` | Demosaik linear → `Debayered_Images/` |
| `--preview` | Pratonton paparan (imbangan putih + gamma; regangan warna palsu untuk multispektral) → `Preview_Images/` |
| `--radiance` | float32 radiasi, W/m²/sr/nm → `Radiance_Images/` (sentiasa `tiff32/`) |
| `--reflectance` | uint16 pantulan, sedia untuk Pix4D → `Reflectance_Calibrated_Images/` |

RGB
kamera utama hanya mengeluarkan bayaran debayered + pratonton — radiasi/refleksi setiap jalur tidak bermakna untuk sensor jalur lebar, jadi suis tersebut tidak memberi kesan kepada mereka.Survey3
`.raw` mengabaikan suis tersebut dan mengikuti laluan reflektansi/sasaran standard.

```bash
# Radiance only — no DAQ downwelling needed
chloros-cli process ~/captures/lattice_flight --no-debayered --no-preview --no-reflectance
```

**`--reflectance-source {auto,target,daq}`** (default `auto`) memilih rujukan pantulan: `auto` melakukan QA-passing dalam bingkai [sasaran kalibrasi](calibration-targets.md) rujukan mutlak dan kembali kepada pembahagi sinaran ke bawah penderia cahaya DAQ (ρ = π·L/E) apabila tiada sasaran hadir; `target` adalah ketat (tiada penggantian DAQ); `daq` adalah berkuasa DAQ. Imbasan sasaran yang diukur per-unit boleh dibekalkan dengan `--target-reflectance-dir`.

{% hint style="info" %}
**Membaca piksel pantulan:**DN yang bermaksud ρ = 1.0 adalah**per-sumber** — Fail LATTICE menandakan `Chloros:PixelScale=32768` dalam XMP; failSurvey3
menggunakan 65535 (dan tidak mempunyai tag `Chloros:*`). Bacalah tag tersebut dan bahagilah dengan nilainya, bukannya menganggapnya sebagai pemalar. Butiran dan satu kes tepi tanpa skala yang disengajakan terdapat dalam [RujukanCLI
](reference/cli-reference.md).
{% endhint %}

Pemprosesan sentiasa bermula dari `raw`. Produk terbitan (eksport debayered/radiance/reflectance) tidak pernah dimasukkan semula ke dalam saluran — mengimport semula dan memprosesnya akan menerapkan matematik penentukuran dua kali, jadiChloros
melangkauinya dan menyatakan demikian. `--input-level` adalah pintu kecemasan yang disengajakan apabila anda benar-benar perlu memaksa titik kemasukan.

***

## Apabila Jalankan Gagal

Mulai versi 1.2.0, `process` akan gagal dengan bunyi nyaring dan bukannya &quot;berjaya&quot; tanpa sebarang hasil:

* Pelaksanaan yang **meminta produk tetapi tidak menulis sebarang produk**— hanya `project.json` dan `calibration_data.json` — mencetak `Processing finished but wrote no image products.` dan**keluar dengan nilai bukan sifar**, supaya skrip dapat mengesaninya. Punca biasa: folder input tidak diiktiraf sebagai tangkapan (semak susun atur dan `--input-level`), atau setiap produk yang diminta tidak terpakai untuk kamera tersebut (contohnya meminta radiasi/reflektan daripada kameraRGB
sahaja).
* **Jalankan semula secara khusus hanya untuk metadata** (semua produk dilumpuhkan, tiada `--indices`) masih dianggap berjaya — output imej kosong adalah keputusan yang betul di situ.
* Jalankan semula dengan `--verbose` dan semak log backend untuk baris `[LATTICE-EXPORT]` / `[EXPORT-CHECK]`, yang menerangkan tentang kamera yang dilangkau.

Kod keluar: `0` berjaya · `1` kegagalan umum · `2` ralat hujah · `130` terganggu oleh Ctrl+C.

***

## Indeks Vegetasi

Jalankan `--indices` dengan satu atau lebih nama pratetap; setiap indeks disimpan dalam folder `<INDEX>_Index_Images/` tersendiri:

```bash
chloros-cli process ~/images/flight_001 --indices NDVI NDRE GNDVI
```

22 nama pratetap yang diterima oleh `process --indices`:

`NDVI` `GNDVI` `NDRE` `OSAVI` `SAVI` `MSAVI2` `EVI` `MSR` `TDVI` `LAI` `GCI` `GRVI` `GSAVI` `GOSAVI` `NLI` `MNLI` `RDVI` `WDRVI` `CVI` `ENDVI` `GLI` `VARI`

{% hint style="warning" %}
**Terdapat tiga senarai indeks — jangan kelirukannya.**Senarai lungsur Tetapan Projek GUI mempunyai 27 formula (menambah `FCI1`, `FCI2`, `GARI`, `GEMI`, `LCI` — kelima-limanya hanya untuk GUI dan**tidak** sah untuk `--indices`). Perintah live/offline `lattice index --preset` menggunakan senarai 22 pratetanya sendiri yang berasingan. Formula dan matematik jalur didokumenkan dalam [Formula Indeks Multispektral](project-settings/multispectral-index-formulas.md).
{% endhint %}

***

## Penderia Cahaya DAQ: Perkenalan Ringkas

Keluarga `daq pool-*` mengendalikan penderia spektral DAQ-MAPIR
(DAQ-U melalui USB, DAQ-M melalui BLE, DAQ-E melalui Ethernet) melalui kolam kekal backend — GUI,CLI
, danSDK
semuanya berkongsi satu pemegang langsung. `pool-*` ialah laluan DAQ yang disokong dalamCLI
yang disertakan; subperintah `daq` lain yang mungkin anda lihat dirujuk adalah aMAPIR
-internal source-only surface dan exit dengan ralat eksplisit yang merujuk anda kepada `pool-*`.

```bash
# 1. Open a pooled session (pick the line matching your sensor)
chloros-cli daq pool-connect                              # smart-detect
chloros-cli daq pool-connect --port COM3                  # DAQ-U on a specific COM port
chloros-cli daq pool-connect --mac AA:BB:CC:DD:EE:FF      # DAQ-M by BLE MAC
chloros-cli daq pool-connect --eth-host daq-e-xxx.local   # DAQ-E by hostname (reliable)

# 2. List pooled sensors and their ids
#    (DAQ-U ids look like 'CB-7C-A8-2E-5F'; DAQ-E ids like 'daq-e-def330')
chloros-cli daq pool-list

# 3. Read the latest calibrated spectrum (W/m²/nm)
chloros-cli daq pool-latest --sensor-id CB-7C-A8-2E-5F

# 4. Record a calibrated .daq file for 60 s
chloros-cli daq pool-record --sensor-id CB-7C-A8-2E-5F --duration 60 \
  -o ~/Documents/spectra --device-name "field-A"

# 5. Release
chloros-cli daq pool-disconnect --sensor-id CB-7C-A8-2E-5F
```

`pool-record` tanpa `--duration` berjalan sehingga `pool-record --stop`; direktori output lalai ialah `~/Documents/DAQ Live View/` **pada mesin backend**. Profil pembetulan topi dipilih semasa sambungan (`--cap-id`, lalai backend `sunshine_cosine`) dan boleh ditukar secara langsung dengan `pool-set-cap` — profil cap dan julat kalibrasi penderia dibincangkan dalam bab DAQ manual ini.

{% hint style="warning" %}
**DAQ-E pada hos berbilang NIC:** penemuan automatik `pool-connect --eth` pertama selepas but boleh gagal walaupun sensor dalam keadaan baik. `--eth-host <ip-or-hostname>` adalah bentuk yang boleh dipercayai — gunakannya setiap kali penemuan tidak menemui apa-apa.
{% endhint %}

***

## Kamera LATTICE, PTP &amp; Automasi Projek

Keluarga `lattice` (45+ subperintah) merangkumi kerja kamera LATTICE dari awal hingga akhir: penemuan, tangkapan tunggal, susunan bersepadu berterusan dengan aliran penyambungan smart-prep GUI, pratonton pelayar langsung, penjajaran, matematik indeks, dan diagnostik NIC hos. Sekilas:

```bash
chloros-cli lattice info                                          # discover cameras
chloros-cli lattice capture -o output/                            # one frame, all export types
chloros-cli lattice array-connect --serials SN1,SN2,SN3,SN4       # persistent synced array
chloros-cli lattice array-capture --processing reflectance -o out/
```

Di sampingnya: `chloros-cli time-sync` melaporkan mengenai PTP grandmaster yang dijalankan oleh hosChloros
(kamera LATTICE dan penderia DAQ-E menjadi hamba kepadanya untuk cap masa merentas peranti), dan `chloros-cli project` membuka projekChloros
yang disimpan dan mengendalikan kameranya, arainya, dan sensanya secara tanpa papan pemuka — termasuk resipi tangkapan YAML berskrip.

Ketiga-tiga keluarga ini (`lattice`, `project`, `daq pool-*`) juga merupakan satu-satunya yang mematuhi `CHLOROS_BACKEND_URL` untuk memacu backend **jauh**; arahan teras sentiasa menyasarkan mesin tempatan.

Panduan langkah demi langkah penuh terdapat dalam bab LATTICE manual ini; setiap penanda terdapat dalam [RujukanCLI
](reference/cli-reference.md).

***

## Penyelesaian Masalah: 5 Teratas

| Gejala | Penyelesaian |
| --- | --- |
| `Login required`, atau kerja yang dijadualkan terperangkap pada arahan  | Jalankan `chloros-cli login EMAIL 'PASSWORD'` sekali pada mesin ini — arahan tanpa prompt sesi yang disimpan akan berinteraksi secara interaktif dan bukannya gagal dengan cepat. |
| `backend unreachable` | Mulakan aplikasi desktopChloros
, atau jalankan binari backend secara langsung (`chloros-backend`). Jika anda menunjuk `lattice`/`project`/`daq pool-*` ke backend jauh, periksa `CHLOROS_BACKEND_URL`. |
| Sambungan array dihalang: `FRAMES WILL DROP` / `Reduce ROI to enable` | Tetapan semula cincin penerima NIC hos kepada lalai — punca nombor 1 rig yang sebelum ini berfungsi enggan menyambung, biasanya selepas kemas kini pemacu NIC. Jalankan `chloros-cli lattice network --fix` daripada terminal **ditingkatkan** (atau tetapkan `ReceiveBufferLen=256`, `PendingReceives=64`); lihat *Host NIC Setup &amp; Tuning* dalam rujukan. |
| Subperintah `daq` keluar: &quot;memerlukan pakej daq penuh…&quot; | Dijangka pada binaan yang dihantar —CLI
yang disusun hanya menyertakan keluarga `daq pool-*`, yang merangkumi sambungan, aliran, rakaman, dan pemilihan cap. Gunakan `pool-*` (atau `chloros_sdk.connect_daq_sensor()` daripadaPython
). |
| Jetson mencetak amaran pertukaran sebelum folder besar | Tambah pertukaran disokong fail —CLI
mencetak arahan `fallocate`/`swapon` yang tepat untuk dijalankan. |

***

## Mendapatkan Bantuan

```bash
chloros-cli --help              # top-level help
chloros-cli process --help      # per-command help
chloros-cli lattice --help
chloros-cli daq --help          # lists the pool-* subcommands
```

* **Setiap penanda, setiap subperintah:** [RujukanCLI
](reference/cli-reference.md)
* **SetaraPython
:** [Python
SDK
](api-python-sdk.md) dan [RujukanSDK
](reference/sdk-reference.md)
* **Sokongan:** info@mapir.camera · [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
