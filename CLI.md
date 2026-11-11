# CLI: Baris Perintah

> **Rujukan lengkap:**[Rujukan CLI](reference/cli-reference.md) mendokumentasikan**setiap penanda bagi setiap subperintah** dan dioptimumkan untuk pembantu AI — tampal URLnya ke dalam pembantu anda dan minta perintah yang berfungsi: `https://mapir.gitbook.io/chloros/reference/cli-reference`
>
> **Petua untuk alat AI:** mana-mana halaman manual ini boleh didapati sebagai Markdown mentah dengan menambah `.md` pada URLnya (contohnya `https://mapir.gitbook.io/chloros/reference/cli-reference.md`), dan `https://mapir.gitbook.io/chloros/llms.txt` mengindeks keseluruhan manual untuk penggunaan LLM.

> **Mahu pembantu anda menjalankan Chloros itu sendiri?** `chloros-mcp`, sebuah pelayan MCP yang dipasang dengan Python SDK, berfungsi dengan Claude Desktop, Claude Code, Cursor dan klien MCP lain: lihat [Menggunakan Chloros dengan Pembantu AI](ai-assistants.md).

<figure><img src=".gitbook/assets/cli.JPG" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: banner shows CLI 1.1.0; reshoot the CLI welcome/banner output on the 1.3.0 build so the version line reads "Chloros CLI 1.3.0" -->


## Apa Itu CLI


`chloros-cli` ialah antaramuka baris perintah untuk enjin pemprosesan yang sama digunakan oleh aplikasi desktop Chloros. Ia adalah klien HTTP ringan di atas backend Chloros (pelayan tempatan pada `127.0.0.1:5000`) — kebanyakan arahan memulakan backend secara automatik, jadi hanya satu panggilan `chloros-cli process …` diperlukan oleh skrip. Backend kemudian terus berjalan untuk arahan seterusnya: ia berhenti pada `chloros-cli logout` (kecuali jika aplikasi dibuka atau proses jalankan atau bina sedang dijalankan), pada `chloros-cli backend stop`, atau secara automatik selepas 30 minit tanpa klien, proses jalankan atau peranti yang disambungkan. `chloros-cli backend status` menunjukkannya.

Ia berjalan pada **Windows 10/11 (x64)**dan**Linux (x86_64, dan NVIDIA Jetson arm64 pada JetPack 6)**, dalam mana-mana terminal, tanpa memerlukan GUI. Periksa pemasangan anda dengan:

```bash
chloros-cli --version    # prints "Chloros CLI 1.3.0"
```

Keluarga arahan, sekilas:

* **Pemprosesan & akaun** — `process`, `login`, `logout`, `status`, `export-status`, `language` (38 bahasa — lihat [Bahasa Disokong](supported-languages.md)), `set-project-folder` / `get-project-folder` / `reset-project-folder`, `selftest`, `update` (Linux/Jetson sahaja)
* **Perisian langsung** — `lattice` (kawalan kamera LATTICE, 45+ subperintah), `daq pool-*` (penderia cahaya DAQ), `time-sync` (PTP)
* **Automasi** — `project` (mengendalikan projek Chloros yang disimpan secara tanpa kepala, termasuk resipi tangkapan YAML)

Pilihan global yang perlu diketahui: `--port N` (port backend, lalai `5000`), `-v/--verbose`, `--restart` (memaksa mulakan semula backend), `--backend-exe PATH`. Lihat [Rujukan CLI](reference/cli-reference.md) untuk senarai penuh.

***

## Pemasangan

CLI **disertakan dalam pemasang Chloros di setiap platform** — tiada muat turun CLI berasingan. Dapatkan pemasang daripada halaman [Muat Turun](download.md).

### Windows


Pemuat turun meletakkan CLI di:

```

C:\Program Files\Chloros\cli\chloros-cli.exe
```

dan menambah folder tersebut ke dalam sistem `PATH` anda sekali (pengupayaan juga akan membuang entri duplikat yang ditinggalkan oleh pemasang terdahulu) — **buka terminal baru**selepas pemasangan supaya `PATH` yang dikemas kini dapat dikesan. Pemuat juga meletakkan skrip pelancar (`Chloros_CLI.bat` / `Chloros_CLI.ps1`) di direktori akar pemasangan serta**ChlorosCLI
** pautan pintas menu Mula, masing-masing membuka terminal dengan `chloros-cli` sedia untuk digunakan.

### Linux


Pasang `.deb` untuk seni bina anda dengan `apt` (bukan `dpkg -i`, yang melangkau keperluan yang disyorkan oleh Python SDK):

```bash
# Linux x86_64
sudo apt install ./chloros_1.3.0_amd64.deb

# NVIDIA Jetson (arm64, JetPack 6)
sudo apt install ./chloros_1.3.0_arm64_jp6.deb
```

Ini memasang `chloros-cli` ke `/usr/bin/chloros-cli` (sudah ada pada `PATH`), program backend dan CLI yang diurai dalam `/usr/lib/chloros/backend/` dan `/usr/lib/chloros/cli/` (`/usr/lib/chloros/chloros-backend` adalah pelancar yang memulakan backend), runtime Arena SDK yang diperlukan oleh backend untuk kamera LATTICE, dan Python SDK untuk sistem `python3`. Lihat [Pemasangan [Linux](linux/linux-installation.md)] untuk maklumat lanjut.

### Semak

```bash
chloros-cli --version    # "Chloros CLI 1.3.0"
chloros-cli selftest     # 7-step diagnostic: backend, API, GPU/CUDA, denoiser models
chloros-cli status       # license tier + logged-in user
```

***

## Log Masuk & Pelesenan

Akses CLI (dan Python SDK) memerlukan **pelan berbayar Chloros+**— mana-mana peringkat berbayar memilikinya; peringkat percuma tidak. Had ini dikuatkuasakan**di pihak pelayan** oleh backend, bukan oleh binari CLI: panggilan tanpa log masuk ditolak dengan `401 AUTH_REQUIRED`, dan panggilan dengan log masuk pada peringkat percuma dengan `403 PLAN_UPGRADE_REQUIRED`, sama ada ia datang daripada `chloros-cli`, SDK, atau klien HTTP yang dibangunkan sendiri. Tingkatkan di [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing).

Log masuk **sekali setiap mesin**:

```bash
chloros-cli login user@example.com 'YourPassword'
chloros-cli status
```

<figure><img src=".gitbook/assets/cli login_w.JPG" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: login success output predates 1.2.0; reshoot `chloros-cli login` followed by `chloros-cli status` on the 1.3.0 build showing the license tier line -->


{% hint style="warning" %}
**Kata laluan dengan aksara khas**(`$`, `!`, spaces): wrap the password in**single quotes**, as shown above. In PowerShell double quotes, `$$` akan rosak oleh shell (CLI mengesan ini pada ralat 401 dan mencuba semula secara automatik, tetapi tanda petik tunggal mengelakkan masalah ini sepenuhnya).
{% endhint %}

Sesi disimpan dalam cache di `~/.chloros/user_session.json` dan terus berfungsi secara luar talian untuk tempoh lanjutan pelan (30 hari untuk pelan bulanan, sehingga tamat tempoh untuk pelan tahunan). `chloros-cli status` berfungsi walaupun tanpa pelan berbayar, jadi sebab penolakan sentiasa dapat dilihat.

{% hint style="danger" %}
**Menjadualkan kerja tanpa kepala? Log masuk dahulu.**Perintah penciptaan backend (`process`, `status`, `export-status`, …) dijalankan dengan**tiada sesi yang disimpan**tidak gagal dengan cepat — ia memasuki prompt interaktif `Email:` / `Password:` pada stdin. Kerana itu, kerja cron tanpa pengawasan atau langkah CI akan**tergantung menunggu input**. Jalankan `chloros-cli login EMAIL 'PASSWORD'` sekali pada mesin sebelum menjadualkan apa-apa.
{% endhint %}

***

## Jalankan Processing Anda yang Pertama

Arahkan `process` ke folder hasil tangkapan — ia mengesan secara automatik Survey3 (`.raw` + `.jpg`), LATTICE (`.tif`/`.tiff`), `.dng`, atau gabungan:

```bash
chloros-cli process "C:\Images\flight_001"          # Windows
chloros-cli process ~/images/flight_001              # Linux
```

Aliran kemajuan secara langsung bagi setiap benang paip (Mengesan, Menganalisis, Memproses, Mengeksport), dan pelaksanaan yang berjaya diakhiri dengan melaporkan berapa banyak produk imej yang ditulis: `Image products written: N`, diikuti oleh `(plus M clamp masks)` apabila larian itu juga menulis [topeng clamp](output-image-formats.md#clamp-masks) (ia tidak dikira sebagai produk).

<!-- SCREENSHOT-NEEDED: terminal capture of a `chloros-cli process` run on a LATTICE captures folder completing successfully — per-thread progress lines visible and the final "Image products written: N (plus M clamp masks)" summary line -->


Jika backend Chloros sudah berjalan (contohnya backend aplikasi desktop), `process` menggunakannya dan membuka projeknya di dalamnya. Ia menolak selagi aplikasi dibuka pada projek yang dibuka oleh aplikasi: proses dari aplikasi, atau tutup projek tersebut. Jika larian sebelumnya masih dalam proses tamat, ia menunggu sehingga 15 saat sebelum menolak. `--restart` memaksa backend baharu. Pada Linux, backend yang dijalankan oleh systemd tidak akan dihidupkan semula: `process` menggunakannya seperti sedia ada, dan di mana ia akan dihidupkan semula (`--restart`, atau import yang tidak menemui apa-apa), ia mencetak `sudo systemctl restart <unit>` dan keluar `1`.

### Di manakah keluaran disimpan

`process` menulis ke dalam **folder projek**, bukan ke dalam folder input anda:

* Tanpa `-o`: projek akan dibuat di bawah folder projek lalai anda (dikongsi dengan GUI; uruskannya dengan `get-project-folder` / `set-project-folder`; apabila tiada ditetapkan, `Documents/MAPIR/Chloros Projects` dalam folder pengguna anda), dinamakan oleh `-n/--project-name` atau cap masa (`YYYYMMDD_HHMMSS`) apabila diabaikan.
* Dengan `-o PATH`: folder itu **ialah** folder projek. Jika ia sudah mengandungi `project.json`, `_1`/`_2`… adik beradik yang diberi awalan itu dicipta dan bukannya menimpa. `PATH` relatif diambil secara relatif kepada folder di mana anda menjalankan `chloros-cli`.

{% hint style="info" %}
**Laluan relatif (diperkenalkan dalam 1.3.0).** Setiap laluan yang dibuka oleh backend — `-o`/`--output` pada mana-mana arahan (termasuk `output` lalai bagi arahan tangkapan), `--input`, `--burst-dir`, `--profile`, `--save` dan `--mask` — diselesaikan terhadap folder di mana anda menjalankan `chloros-cli`, jadi `-o captures/` menulis ke `<current folder>/captures/`. Dalam 1.2.0 backend menyelesaikannya dalam folder pemasangan sendiri, yang gagal dengan "Akses ditolak". Untuk backend jauh (`CHLOROS_BACKEND_URL`) laluan dihantar seperti yang ditaip dan diselesaikan pada mesin tersebut.
{% endhint %}

{% hint style="warning" %}
**Windows: jaga supaya folder projek ringkas melainkan laluan panjang diaktifkan.** Dengan laluan panjang Windows dimatikan (laluan panjang Windows dimatikan secara lalai), tiada laluan fail boleh melebihi 260 aksara, dan produk diletakkan sehingga kira-kira 130 aksara di bawah folder projek. `process` memeriksa perkara ini sebelum ia bermula: folder projek yang terlalu dalam akan ditolak dengan **"Folder projek terlalu dalam untuk komputer ini"**, folder projek terpanjang yang muat, dan cara untuk mengaktifkan laluan panjang — tiada apa-apa yang diproses. Gunakan `-o` (atau `set-project-folder`) yang lebih pendek, atau aktifkan laluan panjang Windows (pilihan **Activate Windows long paths** pada pemasang, yang diaktifkan secara lalai) dan mulakan semula Chloros.
{% endhint %}

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

Folder kamera adalah `LATT-<sensor>-<lens>-F<filter>` untuk LATTICE (menyesuaikan dengan EXIF tangkapan `Model`) dan `<model>_<filter>` (contohnya `Survey3N_RGN`) untuk Survey3. Folder format mengikuti `--format`: `tiff16`, `tiff8`, `png8`, `jpg8`, atau `tiff32` untuk `TIFF (32-bit, Percent)`.

Untuk kamera multispektral M3C, folder `clamp_masks/` juga boleh muncul dalam folder format, di samping folder produk (contohnya `tiff16/clamp_masks/Reflectance_Calibrated_Images/` dan `tiff32/clamp_masks/Radiance_Images/`). Ia mengandungi topeng piksel demi piksel bagi produk radiasi dan pantulan di mana pencampuran semula jalur menetapkan nilai negatif kepada sifar — lihat [Clamp masks](output-image-formats.md#clamp-masks). Tiada folder produk pernah mengandungi topeng.

{% hint style="info" %}
**Setiap produk yang dieksport mengekalkan nama fail SUMBER.**Eksport radiasi `capture_..._raw.tif` masih dinamakan `capture_..._raw.tif` — ia hanya terletak di `tiff32/Radiance_Images/`.**Folder mengenal pasti produk, bukan nama fail**, jadi gunakan glob untuk direktori, bukan untuk sambungan `*radiance*`.
{% endhint %}

### Pilihan yang sebenarnya akan anda gunakan

| Penanda | Lalai | Fungsinya |
| --- | --- | --- |
| `-o, --output PATH` | folder projek lalai | Lokasi folder projek (lihat di atas). |
| `-n, --project-name NAME` | cap masa | Nama projek. |
| `--format FMT` | `TIFF (16-bit)` | Salah satu `TIFF (16-bit)`, `TIFF (8-bit)`, `TIFF (32-bit, Percent)`, `PNG (8-bit)`, `JPG (8-bit)`. Chloros tidak melakukan penyesuaian saiz: pada semua imej 8-bit, format yang lebih mendalam ditulis sebagai `TIFF (8-bit)`, dan ia dinyatakan sekali. |
| `--indices NAME [NAME ...]` | tiada | Indeks vegetasi untuk dieksport (lihat [Indeks Vegetasi](#vegetation-indices)). |
| `--debayer {standard,texture-aware}` | `standard` | `texture-aware` = debayer neural, lebih perlahan, kualiti tertinggi (Chloros+, memerlukan GPU CUDA: tanpa GPU tersebut, pelaksanaan akan menggunakan `standard`; ditolak untuk tangkapan LATTICE, yang sentiasa menggunakan `standard`). |
| `--vignette / --no-vignette` | on | Pembetulan vignet. |
| `--reflectance / --no-reflectance` | on | Kalibrasi reflektansi; untuk LATTICE ini juga suis produk reflektansi. |
| `--input-level {auto,raw,debayered,processed}` | `auto` | Memaksa titik kemasukan paip untuk TIFF LATTICE. |

Untuk semua perkara lain — penyetelan pengesanan sasaran, PPK, pin pendedahan, penanda aras penjajaran susunan — lihat [seksyen `process` Rujukan CLI](reference/cli-reference.md).

***

## Memilih Apa yang Akan Dieksport (Produk LATTICE)

Pemprosesan LATTICE disalurkan ke **setiap produk yang berkenaan dalam satu langkah**. Empat suis bagi setiap produk semuanya**DIHIDUPKAN secara lalai**; gunakan borang `--no-` untuk menamatkan satu:

| Suis | Produk |
| --- | --- |
| `--debayered` | Demosaik linear → `Debayered_Images/`: offset gelap pek penentukuran dialihkan, kemudian de-mosaik peka tepi, tanpa pembetulan lineariti, padang rata atau unmix — fail yang sama yang disimpan pada tahap `debayered`. Disimpan dalam orientasi asal sensor (direkodkan dalam XMP). |
| `--preview` | Pratonton paparan (imbangan putih + gamma; regangan warna palsu untuk multispektral) → `Preview_Images/` |
| `--radiance` | float32 radiasi, W/m²/sr/nm → `Radiance_Images/` (sentiasa `tiff32/`) |
| `--reflectance` | uint16 pantulan, sedia untuk Pix4D → `Reflectance_Calibrated_Images/` |

Kamera induk RGB hanya memancarkan data yang telah menjalani proses debayering dan pratonton — radiasi/refleksi bagi setiap jalur tidak bermakna untuk sensor jalur lebar, jadi suis tersebut tidak memberi kesan ke atasnya. Survey3 `.raw` mengabaikan suis tersebut dan mengikuti laluan standard reflektansi/sasaran.

```bash
# Radiance only — no DAQ downwelling needed
chloros-cli process ~/captures/lattice_flight --no-debayered --no-preview --no-reflectance
```

**`--reflectance-source {auto,target,daq}`** (default `auto`) memilih rujukan pantulan: `auto` menjadikan [sasaran kalibrasi](calibration-targets.md) dalam bingkai yang lulus QA sebagai rujukan mutlak dan beralih kepada pembahagi sinaran ke bawah sensor cahaya DAQ (ρ = π·L/E) apabila tiada sasaran hadir (sasaran yang lulus QA dengan penanda bersaing dengan DAQ pada skor yang diukur); `target` adalah ketat (tiada penggantian DAQ); `daq` adalah berkuasa DAQ. Imbasan sasaran yang diukur per-unit boleh dibekalkan dengan `--target-reflectance-dir`.

{% hint style="info" %}
**Membaca piksel reflektansi:**DN yang bermaksud ρ = 1.0 adalah**per-sumber** — fail LATTICE menandakan `Chloros:PixelScale=32768` dalam XMP; Fail Survey3 menggunakan 65535 dan tidak mempunyai tag `Chloros:PixelScale` (namun ia mengandungi tag pemprosesan `Chloros:*` yang lain). Baca tag tersebut dan bahagikannya dengannya daripada menganggap ia satu pemalar. Butiran dan satu kes sempadan tanpa skala yang disengajakan terdapat dalam [Rujukan [CLI]](reference/cli-reference.md).
{% endhint %}

Pemprosesan sentiasa bermula dari `raw`. Produk terbitan (eksport debayered/radiance/reflectance) tidak pernah dimasukkan semula ke dalam saluran pemprosesan — mengimport semula dan memprosesnya akan menerapkan matematik penentukuran dua kali, jadi Chloros melangkau mereka dan menyatakan demikian. `--input-level` adalah pintu kecemasan yang disengajakan apabila anda benar-benar perlu memaksa titik kemasukan.

***

## Apabila Jalankan Gagal

Mulai versi 1.2.0, `process` akan gagal dengan bunyi kuat bukannya "berjaya" tanpa sebarang hasil:

* Pelaksanaan yang **meminta produk tetapi tidak menulis sebarang produk**— hanya `project.json` dan `calibration_data.json` — mencetak `Processing finished but wrote no image products.` dan**keluar dengan nilai bukan sifar**, supaya skrip dapat mengesannya. Punca biasa: folder input tidak diiktiraf sebagai tangkapan (semak susun atur dan `--input-level`), atau setiap produk yang diminta tidak terpakai untuk kamera tersebut (contohnya meminta radiasi/reflektan daripada kamera RGB sahaja).
* **Jalankan semula dengan `--verbose`** dan semak log backend untuk `[LATTICE-EXPORT]` / `[EXPORT-CHECK]`. * **Jalankan secara khusus hanya untuk metadata** (togol semua produk ke posisi off, tiada `--indices`) masih dianggap berjaya — output imej kosong adalah keputusan yang betul di situ.
* Jalankan semula dengan `--verbose` dan periksa log backend untuk baris `[LATTICE-EXPORT]` / `[EXPORT-CHECK]`, yang menerangkan sebab setiap kamera dilangkau.
* Pada Windows apabila laluan panjang dilumpuhkan, folder projek yang terlalu dalam melebihi had laluan 260 aksara akan ditolak **sebelum** pemprosesan bermula, dengan folder projek terpanjang yang muat (lihat [Where the outputs land](#where-the-outputs-land)).
* Perintah tangkapan (`lattice capture`, `lattice array-capture` dan keluarga tangkapan lain) menyenaraikan setiap fail yang tidak dapat mereka tulis dan keluar dengan kod `1`; tangkapan berulang akan berhenti apabila pemacu penuh.

Kod keluar: `0` berjaya · `1` kegagalan umum · `2` ralat hujah · `130` terganggu oleh Ctrl+C.

***

## Indeks Vegetasi

Lalukan `--indices` dengan satu atau lebih nama pratetap; setiap indeks disimpan dalam folder `<INDEX>_Index_Images/` tersendiri:

```bash
chloros-cli process ~/images/flight_001 --indices NDVI GNDVI OSAVI
```

22 nama pratetap yang diterima oleh `process --indices`:

`NDVI` `GNDVI` `NDRE` `OSAVI` `SAVI` `MSAVI2` `EVI` `MSR` `TDVI` `LAI` `GCI` `GRVI` `GSAVI` `GOSAVI` `NLI` `MNLI` `RDVI` `WDRVI` `CVI` `ENDVI` `GLI` `VARI`

Nama yang belum disenaraikan oleh projek ditambah ke dalamnya. Nama yang bukan preset, atau yang tidak dapat dihasilkan oleh jalur kamera, akan dilangkau dengan amaran yang menamakannya. Jika CLI tidak dapat membaca senarai indeks semasa projek, ia akan membiarkan senarai itu tidak berubah dan memberi amaran daripada menggantikannya.

{% hint style="warning" %}
**Terdapat tiga senarai indeks — jangan kelirukannya.**Senarai lungsur Tetapan Projek GUI mempunyai 27 formula (menambah `FCI1`, `FCI2`, `GARI`, `GEMI`, `LCI` — kelima-limanya hanya untuk GUI dan**tidak** sah untuk `--indices`). Perintah live/offline `lattice index --preset` menggunakan senarai 22 pratetapnya sendiri yang berasingan. Formula dan matematik jalur didokumenkan dalam [Formula Indeks Multispektral](project-settings/multispectral-index-formulas.md).
{% endhint %}

***

## Penderia Cahaya DAQ: Rasa Ringkas

Keluarga `daq pool-*` memacu penderia spektral DAQ MAPIR (DAQ-U melalui USB, DAQ-M melalui BLE, DAQ-E melalui Ethernet) melalui kolam kekal backend — GUI, CLI, dan SDK semuanya berkongsi satu pemegang langsung. **`pool-*` adalah laluan DAQ yang disokong dalam CLI yang disertakan**; subperintah `daq` lain yang mungkin anda lihat dirujuk adalah permukaan sumber dalaman MAPIR sahaja dan keluar dengan ralat eksplisit yang menunjuk anda ke `pool-*`.

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

## Kamera LATTICE, PTP & Automasi Projek

Keluarga `lattice` (45+ subperintah) merangkumi kerja kamera LATTICE dari awal hingga akhir: penemuan, tangkapan tunggal, susunan bersepadu berterusan dengan aliran sambungan penyediaan pintar GUI, pratonton pelayar langsung, penjajaran, matematik indeks, dan diagnostik NIC hos. Sebagai contoh:

```bash
chloros-cli lattice info                                          # discover cameras
chloros-cli lattice capture -o output/                            # one frame, all export types
chloros-cli lattice array-connect --serials SN1,SN2,SN3,SN4       # persistent synced array
chloros-cli lattice array-capture --processing reflectance -o out/
```

Di sampingnya: `chloros-cli time-sync` melaporkan mengenai PTP grandmaster yang dijalankan oleh hos Chloros (kamera LATTICE dan penderia DAQ-E menjadi hamba kepadanya untuk cap masa merentas peranti), dan `chloros-cli project` membuka projek Chloros yang disimpan dan mengendalikan kameranya, arainya, dan penderianya secara tanpa papan pemuka — termasuk resipi tangkapan YAML berskrip.

Ketiga-tiga keluarga ini (`lattice`, `project`, `daq pool-*`) juga merupakan satu-satunya yang mematuhi `CHLOROS_BACKEND_URL` untuk memacu backend **jauh**; arahan teras sentiasa menyasarkan mesin tempatan.

Panduan lengkap terdapat dalam bab LATTICE manual ini; setiap penanda (flag) terdapat dalam [Rujukan [CLI]](reference/cli-reference.md).

***

## Penyelesaian Masalah: 5 Teratas

| Simptom | Penyelesaian |
| --- | --- |
| `Login required`, atau kerja terjadual terperangkap pada arahan `Email:` | Jalankan `chloros-cli login EMAIL 'PASSWORD'` sekali pada mesin ini — arahan tanpa prompt sesi yang disimpan akan berinteraksi secara interaktif dan bukannya gagal dengan cepat. |
| `backend unreachable` | Mulakan aplikasi desktop Chloros, atau mulakan backend sendiri: `"C:\Program Files\Chloros\backend\chloros-backend.exe"` pada Windows (folder `backend` dalam folder pemasangan anda; ia tidak ada pada `PATH`), `chloros-backend` pada Linux. `chloros-cli backend status` menunjukkan sama ada satu sedang berjalan; `lattice`/`project`/`daq pool-*` tidak pernah memulakan mana-mana. Jika anda menunjukkannya ke backend jauh, semak `CHLOROS_BACKEND_URL`. |
| Susunan sambungan disekat: `FRAMES WILL DROP` / `Reduce ROI to enable` | RING penerimaan NIC hos ditetapkan semula kepada lalai — punca #1 rig yang sebelum ini berfungsi enggan menyambung, biasanya selepas kemas kini pemacu NIC. Jalankan `chloros-cli lattice network --fix` daripada terminal **ditingkatkan** (atau tetapkan `ReceiveBufferLen=256`, `PendingReceives=64`); lihat *Host NIC Setup & Tuning* dalam rujukan. |
| Subperintah `daq` keluar: "memerlukan pakej daq penuh…" | Gunakan arahan `daq pool-*`, yang mengendalikan sensor yang sama melalui backend: connect, stream, record, cap selection, dan untuk DAQ-E tetapan rangkaiannya, permulaan semula dan kemas kini firmware (atau `chloros_sdk.connect_daq_sensor()` daripada Python). |
| Jetson mencetak amaran pertukaran sebelum folder besar | Tambah pertukaran disokong fail — CLI mencetak arahan `fallocate`/`swapon` yang tepat untuk dijalankan. |

***

## Mendapatkan Bantuan

```bash
chloros-cli --help              # top-level help
chloros-cli process --help      # per-command help
chloros-cli lattice --help
chloros-cli daq --help          # lists the pool-* subcommands
```

* **Setiap penanda, setiap subperintah:** [Rujukan CLI](reference/cli-reference.md)
* **Setara Python:** [Python SDK](api-python-sdk.md) dan [Rujukan SDK](reference/sdk-reference.md)
* **Sokongan:** info@mapir.camera · [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
