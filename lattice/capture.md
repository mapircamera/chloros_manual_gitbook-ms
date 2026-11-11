# Tetapan & Mod Rakaman

Proses merakam di tab Kamera dikawal oleh satu butang merah **Rakam Semua**dan satu panel**Tetapan Rakaman** yang menentukan hasil butang tersebut: kamera mana yang akan turut serta, jenis eksport yang disimpan oleh setiap kamera, dan sama ada shutter akan diambil sekali, secara berterusan, atau pada selang masa. Halaman ini menerangkan keseluruhan aliran — konfigurasi, proses pengambilan itu sendiri, lokasi simpanan fail pada cakera, dan cara memprosesnya semula menjadi produk yang dikalibrasi kemudian. Kawalan kamera dan susunan terletak di [Camera Settings](camera-settings.md).

{% hint style="info" %}
**Perekodan memerlukan projek yang dibuka.** 'Capture All' dan gear Tetapan Rakaman dilumpuhkan sehingga projek dibuka ("Buat atau buka projek untuk menyimpan rakaman"). Setiap rakaman disimpan di bawah folder projek dalam `captures/`.
{% endhint %}

## Panel Tetapan Rakaman

Bukanya dengan **ikon gear di sebelah Rakam Semua**dalam senarai kamera bar sisi, atau dengan butang**"Buka Tetapan Rakaman…"** di bahagian bawah mana-mana panel tetapan setiap kamera. Tajuknya ialah "Tetapan Rakaman" dengan butang kembali ←.

<!-- SCREENSHOT-NEEDED: the full Capture Settings pane — Single/Continuous/Interval mode buttons at top, the bulk export-type toggle rows (All Raw … All Index), the orange Fastest Capture toggle, an array group card with the Aligned checkbox and Record buttons, and an expanded per-camera row showing per-type checkboxes. -->



Pilihan anda di sini — termasuk kamera yang disertakan, kotak semak mengikut jenis, dan mod tangkapan — disimpan **setiap projek** dan dipulihkan apabila anda membukanya semula.

### Mod tangkapan

Tiga butang mod di bahagian atas tetingkap:

| Mod | Fungsinya | Tetapan anak (lalai) |
| --- | --- | --- |
| **Tunggal** *(lalai)* | Satu tangkapan untuk setiap kamera yang dipilih. | — |
| **Berterusan**| Tangkapan berturut-turut sehingga memenuhi syarat henti. | Hentikan mengikut**Bilangan tangkapan** (lalai 1) *atau* **Tempoh tangkapan** (lalai 10 s; unit: saat / minit / jam / hari). |
| **Interval**(timelapse) | Rangkaian tangkapan mengikut pemasa. |**Tangkapan / selang**(lalai 1) ·**Setiap**N unit (lalai 5 s) ·**Untuk** N unit (lalai 1 m). |

Dalam mod Berterusan atau Jarak, butang Rakam Semua akan menjadi butang **Hentikan (N)** semasa berjalan, mengira bilangan rakaman yang diterima. Penyimpanan penuh menghentikan pelaksanaan (lihat [Aliran Ambil Semua](#the-capture-all-flow)).**Mod Berterusan pada susunan dijalankan di backend (baru dalam 1.3.0).** Apabila setiap kamera dalam senarai tergolong dalam satu susunan (kamera berdiri sendiri disembunyikan atau dikecualikan), Continuous tidak lagi menghantar satu permintaan bagi setiap tangkapan: backend Chloros menjalankan gelung tangkapan itu sendiri, menulis satu kumpulan selari sambil ia memilih yang seterusnya, dan butang menyemak kiraannya. Dengan kamera berdiri sendiri dalam senarai, tangkapan Chloros dilakukan satu persatu seperti sebelum ini.

{% hint style="warning" %}
**Perekodan pantas pada Windows: biarkan backend Chloros dikecualikan daripada imbasan Microsoft Defender.** Pengimbasan masa nyata pada setiap fail yang ditulis oleh perekod berkelajuan tinggi memakan kira-kira 30% daripada kadar perekodan dalam ujian MAPIR. (beratus fail sesaat pada ROI kecil). Pemuat turun Windows menawarkan **"Kecualikan backend Chloros daripada imbasan Microsoft Defender (perekodan kamera lebih pantas; disyorkan),"** yang dipilih secara lalai, dan membuang pengecualian semasa menyahpasang. Jika anda mencentangnya, jalankan semula pemasang dengan ia dicentang, atau tambah pengecualian proses Defender untuk `chloros-backend.exe` (dalam folder `backend` dalam folder pemasangan Chloros). Apabila Defender menggunakan satu teras CPU atau lebih semasa tangkapan berterusan satu susunan, backend **Chloros** mencatatnya sekali dalam lognya, dengan kedua-dua penyelesaian yang sama ini.
{% endhint %}

<!-- SCREENSHOT-NEEDED: the capture-mode area of Capture Settings with Interval selected — showing the "Captures / interval", "Every N (unit)" and "For N (unit)" rows with their defaults (1, 5 s, 1 m). -->




### Memilih kamera dan jenis eksport

Teks bantuan panel meringkaskan: pilih kamera dan jenis eksport yang "Capture All" hasilkan — semuanya diaktifkan secara lalai, dan pilihan disimpan bersama projek ini.

Butang **Pilih semua / Pilih tiada** membalikkan kotak semak termasuk setiap kamera sekaligus.
* **Togol jenis eksport pukal**(dua baris butang):**Semua Mentah / Semua Debayered / Semua Pratonton / Semua Radiance / Semua Refleksi / Semua Indeks**. Setiap satu mempunyai tiga keadaan warna: hijau ✓ = dihidupkan untuk setiap kamera yang menyokongnya, jingga – = dihidupkan untuk beberapa, kelabu = tiada. Suatu suis akan dilumpuhkan apabila tiada kamera yang disambungkan menyokong jenis tersebut. Kesemuanya akan bertukar kelabu semasa Fastest Capture diaktifkan.
* **Baris bagi setiap kamera**: satu kotak semak termasuk, serta satu senarai boleh-tayang (▸/▾) bagi jenis eksport yang terpakai untuk kamera tersebut dengan kotak semak individu. Baris menunjukkan kiraan on seperti "4/6".

### Jenis Eksport dan Kamera yang Mendukungnya

Terdapat enam jenis eksport: **Raw, Debayered, Radiance, Reflectance, Preview, Index**. Hanya jenis yang sesuai akan muncul pada setiap baris kamera:

| Jenis Eksport | Kandungan | RGB (FRGB) | Bayer multispektral (FRGN/FOCN/FNGB) | Mono (M3M) |
| --- | --- | --- | --- | --- |
| **Raw** | Mosaik Bayer (mono: jalur tunggal) terus dari sensor | ✓ | ✓ | ✓ |
| **Debayered** | Demosaik linear: offset gelap pek penentukuran (DSNU) dikeluarkan daripada imej mentah, kemudian demosaik sedar-tepi — tiada pembetulan lineariti, medan rata atau pencampuran semula. Disimpan sebagai RGB 3-saluran (mono: skala kelabu 1-saluran) | ✓ | ✓ | ✓ |
| **Prviakim** | Rantaian paparan penuh (imbangan putih + gamma mengikut profil kamera, serta tetapan Sorotan kamera RGB; multispektral: regangan warna palsu) | ✓ | ✓ | ✓ |
| **Radiasi** | float32 W/m²/sr/nm melalui rantaian radiometrik penuh | — (tidak ditawarkan) | ✓ | ✓ |
| **Refleksansi** | uint16 ρ (32768 = 1.0) | — (tidak ditawarkan) | ✓ — hanya dipaparkan apabila kamera mempunyai penderia cahaya DAQ (miliknya sendiri atau diwarisi daripada susunannya) | sama seperti multispektral |
| **Indeks** | Render Indeks Vegetasi (LUT) | — | ✓ — memerlukan ungkapan indeks yang diaktifkan dan tidak kosong pada kamera, dan tidak ditawarkan kepada ahli susunan gabungan (susunan itu memiliki satu indeks kongsi) | — (indeks memerlukan ≥2 jalur; lihat [Kamera Mono & Indeks Vegetasi](mono-indices.md)) |

Radiasi dan pantulan tidak pernah ditawarkan untuk kamera RGB — radiasi per-Bayer tidak bermakna bagi penderia fotometrik jalur lebar.

### Rakaman Terpantas

Togol **⚡ Rakaman Terpantas — hanya mentah**(warna jingga apabila diaktifkan) akan menimpa semua pilihan eksport kepada**hanya mentah** — serta satu komposit indeks gabungan percuma untuk susunan — supaya bingkai diperoleh secepat mungkin: pengiraan radiasi/pantulan/paparan diabaikan sepenuhnya semasa pengambilan.

{% hint style="info" %}
**Satu `.daq` masih disimpan.** Apabila penderia cahaya ditugaskan, Fastest Capture masih menulis bacaan DAQ downwelling (ke dalam folder `daq/` rakaman) — supaya produk sinaran, pantulan, dan indeks semua boleh dibina kemudian dengan memproses semula (lihat [Mengproses semula tangkapan](#re-processing-captures-into-calibrated-products)). Fastest Capture juga tidak memusnahkan pilihan kotak semak anda: matikan ia dan ia akan kembali.
{% endhint %}

### Kawalan bagi setiap susunan

Setiap susunan yang disambungkan mendapat kad kumpulan tersendiri dalam panel:

* **Sertakan kotak semak** (tiga keadaan bagi ahli) dan nama susunan dengan mod paparan: "(digabungkan | berasingan)".
* Petak semak **Diselar**(lalai**diaktifkan**): memutar eksport ahli mengikut profil penjajaran tatasusunan supaya eksport diselaraskan piksel demi piksel merentas kamera. Raw kekal tidak diwarp tetapi membawa transformasi dalam metadata-nya. (Profil itu sendiri dikira dalam [panel tetapan array](camera-settings.md#alignment-co-registration).) Fail ahli array disimpan dalam orientasi asal setiap kamera, dengan sebarang tetapan Cermin/Pusingan direkodkan dalam fail tersebut dan bukannya diterapkan.
* Baris kamera ahli disarang di dalam kad.

Kad tatasusunan juga menempatkan dua perekod. Anggap ia sebagai **pemantauan vs analisis**:

| Perekod | Gred | Apa yang dirakam |
| --- | --- | --- |
| **● Rakam indeks video / ■ Hentikan rakaman** *(hanya untuk susunan gabungan)* | **Pemantauan** | Menyusun komposit indeks gabungan secara langsung ke video pada 10 fps — 8-bit, resolusi pratonton, LUT terbina dalam. Perlukan projek terbuka dan pandangan langsung penstriman. Menunjukkan bingkai + masa berlalu semasa merakam. |
| **⦿ Rakam letupan mentah / ■ Hentikan burst mentah** *(sebarang susunan)* | **Analisis**| Bingkai Bayer mentah pada kadar tangkapan langsung (tanpa pemprosesan) serta manifest setiap bingkai dan bacaan `.daq`, ke dalam `captures/bursts/`. Selepas burst, butang**Bina video** akan muncul: ia memproses semula burst secara luar talian menjadi video yang dikalibrasi — gabungan indeks dan/atau sinaran / pantulan / indeks bagi setiap kamera — serta TIFF pilihan. Pembinaan gabungan-indeks bermula secara automatik apabila anda menghentikan burst. |

<!-- SCREENSHOT-NEEDED: an array group card in Capture Settings while a raw burst is recording — the ⦿/■ burst button in its recording state with frame count, and (in a second capture) the Build video button that appears after stopping. -->

## Aliran

<!-- SCREENSHOT-NEEDED: the sidebar during a capture — Capture All showing live "Capturing… 3/6" progress text, and (second capture) the result flash "Saved N files". -->

Tangkap Semua

Tekan **Tangkap Semua** dalam senarai kamera bar sisi:

1. Setiap kamera yang disertakan, kelihatan, dan tidak ditangguhkan akan merakam dengan jenis eksport yang dipilih. **Susunan mencetuskan sebagai satu pencetus selari** (satu kumpulan selari tunggal merentasi semua ahli — lihat [Multi-Camera Arrays](arrays.md)); kamera berdiri sendiri merakam secara individu.
2. Kamera tersembunyi (ikon mata) atau yang ditangguhkan akan dilangkau. Susunan hanya akan dihalang sepenuhnya apabila *semua* ahlinya tersembunyi atau ditangguhkan.
3. Setiap kali penderia cahaya ditetapkan, bacaan DAQ downwelling yang sepadan disimpan sebagai fail `.daq` dalam folder `daq/` rakaman — walaupun untuk rakaman mentah sahaja — supaya produk radiometrik sentiasa boleh diperoleh kemudian.
4. Butang memaparkan kemajuan secara langsung — "Merakam… siap/jumlah" — dan dalam mod Berterusan/Selang menjadi **Hentikan (N)**. Setiap item tangkapan mempunyai had masa tamat 300 saat.
5. Apabila pass selesai, flash keputusan akan memaparkan **"Disimpan N fail"**atau**"Disimpan N, F gagal"**, serta "(S tersembunyi/terhenti dilangkau)" apabila kamera dilangkau. Jika pemacu penuh, pelaksanaan akan berhenti dan melaporkan**"Berhenti: pemacu penuh. Disimpan N, Gagal F"** — setiap fail yang tidak dapat ditulis akan dikira sebagai gagal.

## Tempat simpanan tangkapan

Tangkapan disimpan di bawah projek terbuka di `<project>/captures/`. Setiap jenis eksport disimpan dalam **subfolder tersendiri**, jadi tangkapan berbilang-tingkat tidak akan mencampurkan jenis:

```
<project>/captures/
├── raw/           capture_<ts>_SN<serial>_raw.tif
├── debayered/     capture_<ts>_SN<serial>_debayered.tif
├── radiance/      capture_<ts>_SN<serial>_radiance.tif
├── reflectance/   capture_<ts>_SN<serial>_reflectance.tif
├── preview/       capture_<ts>_SN<serial>_display.tif
├── index/         per-camera vegetation-index (LUT) render, when Index is selected
├── composite/     array foreground/background live-view composite, when produced
├── clamp_masks/   radiance/…_clampmask.tif, reflectance/…, index/… — unmix clamp masks,
│                  one subfolder per product folder, only when a band was clamped
├── bursts/        raw-burst recordings (frames + manifest + .daq per burst)
└── daq/           the downwelling reading(s) matched to each capture (.daq), each with a
                   .daq.cameras.json naming the cameras it describes
```

* `<ts>` ialah cap masa tangkapan dan `<serial>` ialah siri kamera. Rakaman berdiri sendiri dinamakan `capture_<ts>_SN<serial>_<level>`; rakaman susunan dari satu pencetus diselaras dinamakan `sync_<ts>_SN<serial>_<level>` dan **membahagi satu cap masa merentas semua kamera dalam kumpulan** (sufiks aras diabaikan apabila kamera hanya menyimpan satu aras).
* **Satu asimetri yang perlu diketahui:** tahap paparan disimpan dalam folder bernama `preview/` manakala fail mengekalkan `_display` dalam nama — folder dan sambungan berbeza hanya untuk tahap itu.
* Tahap yang tidak diketahui akan menggunakan folder dengan nama mereka sendiri; jika subfolder tidak dapat dibuat, fail itu akan disimpan di akar folder tangkapan dan bukannya hilang.
* Fail TIFF tangkapan dimampatkan tanpa kehilangan (DEFLATE) secara lalai dan membawa semua metadata penentukuran dan pemprosesan mereka sepenuhnya **di dalam XMP fail tersebut** — tangkapan menerangkan sendiri, tanpa fail sampingan selain daripada bacaan `.daq` (dan `.daq.cameras.json`-nya).
* **Topeng had (baru dalam 1.3.0).**Di mana pencampuran semula spektral 3×3 terpaksa mengehadkan jalur kepada 0 pada sekurang-kurangnya satu piksel, produk sinaran, pantulan dan indeks mendapat topeng bagi setiap piksel, `clamp_masks/<product folder>/<product name>_clampmask.tif` (bit 0 = jalur dalam saluran R produk, bit 1 = G, bit 2 = B); tag XMP `Chloros:UnmixClampMask` produk menunjuk kepadanya. Topeng-topeng ini terletak**di sebelah** folder produk, bukan di dalamnya, jadi folder produk seperti `reflectance/` boleh diberikan kepada perisian fotogrametri seperti sedia ada. Import Chloros sendiri tidak mengambil kira topeng-topeng tersebut.
* **Orientasi (1.3.0).** Produk Pratinjau, Radiance, Reflectance dan Indeks kamera berdiri sendiri dipusingkan oleh tetapan Cermin/Pusingan; Raw dan Debayered kekal seperti dibaca oleh sensor. Semua fail ahli tatasusunan kekal dalam format asal. Setiap fail merekodkan tetapan dalam XMPnya, jadi pemprosesan semula menerapkannya dengan cara yang sama.
* **Kamera mana satu `.daq` miliknya (1.3.0).** `.daq` bagi satu tangkapan dipadankan, semasa pemprosesan, hanya dengan imej daripada kamera yang dinama oleh `.daq.cameras.json` miliknya. Kamera berdiri sendiri yang tiada penderia cahaya sendiri oleh itu tidak lagi meminjam bacaan kamera lain — ikat penderia kepadanya, atau rakam satu dalam tab Penderia Cahaya. Perekodan penderia cahaya (dan mana-mana `.daq` tanpa fail itu, termasuk tangkapan lama) masih berkhidmat untuk setiap kamera.

Ini adalah susun atur yang sama seperti yang ditulis oleh `chloros-cli lattice capture` / `array-capture` ke dalam direktori `-o` mereka — didokumenkan dalam [Rujukan CLI § Apa rupa folder captures](../reference/cli-reference.md#what-a-captures-folder-looks-like).

<!-- SCREENSHOT-NEEDED: OS file explorer showing a real <project>/captures/ folder after a multi-level array capture — the raw/debayered/radiance/reflectance/preview subfolders, the daq/ and clamp_masks/ folders, and sync_<ts>_SN<serial>_<level>.tif filenames visible inside one subfolder. -->

## Memproses semula tangkapan menjadi produk yang dikalibrasi

Rangka mentah yang dirakam serta `.daq` yang disimpan adalah segala yang diperlukan oleh saluran pemprosesan — inilah sebabnya Fastest Capture selamat untuk kerja sebenar.

* **GUI**: tambahkan folder captures ke dalam projek ([Adding Files to a Project](../processing-images-gui/adding-files-to-a-project.md)) dan proses seperti biasa.
* **CLI**: arahkan `process` ke **root captures**:

```bash
chloros-cli process "C:/ChlorosProjects/MyField/captures"
```

`process` biasanya hanya mengimport folder yang anda namakan, tetapi apabila folder itu tidak mengandungi imej dan mempunyai subfolder, ia akan menelusuri secara automatik — jadi subfolder aras dan folder `daq/` diambil sekaligus. Setiap tangkapan diimport sebagai **satu imej tunggal** dengan tahap-tahap lain dilampirkan sebagai mod tontonan, bukan satu imej bagi setiap tahap.

Menamakan subfolder aras secara langsung (contohnya `…/captures/raw/`) juga berfungsi, tetapi meninggalkan folder `daq/` — salin ia bersama apabila anda re-memperoleh produk radiometrik daripada `raw/`, jika tidak padanan cap masa tidak mempunyai apa-apa untuk dirujuk.

{% hint style="warning" %}
**Pemprosesan sentiasa bermula daripada `raw`.** Dalam setiap tangkapan, bingkai mentah adalah sumber saluran; `debayered`, `radiance`, `reflectance`, dan `preview` muncul sebagai mod tontonan tetapi tidak pernah dimasukkan semula ke dalam saluran — memproses semula produk terbitan akan menerapkan semula matematik vignette, warna, dan sinaran yang sudah tertanam dalam pikselnya, jadi Chloros menolaknya daripada memproses dua kali. Render `index/` dan `composite/` langsung tidak diproses (mereka adalah keluaran, bukan tangkapan). Folder captures disimpan **tanpa** import mentah dan dipaparkan dengan normal, tetapi `process` melangkauinya dan memaklumkannya; `--input-level {raw,debayered,processed}` adalah pintu pelarian yang disengajakan yang memaksa titik kemasukan. Rujuk [Rujukan [CLI]](../reference/cli-reference.md#what-a-captures-folder-looks-like) untuk mesej lompatan yang tepat.
{% endhint %}

Dua lagi tingkah laku yang perlu diketahui apabila menykript semakan semula:

* Pelaksanaan `chloros-cli process` yang meminta produk tetapi tidak menulis **sebarang produk imej akan gagal dengan bunyi nyaring dan keluar dengan nilai bukan sifar** — anda tidak akan pernah mendapat pelaksanaan sunyi yang kosong. Pelaksanaan yang berjaya akan melaporkan jumlah produk mereka. (Pelaksanaan khusus hanya metadata masih dikira sebagai berjaya.)
* Eksport yang diproses dan diimport semula tidak pernah mengambil slot mentah tangkapan — mentah asal sentiasa kekal sebagai sumber saluran.

## Setara CLI



Segala yang di halaman ini boleh dikendalikan tanpa paparan. Mod tangkapan GUI dipetakan terus ke `chloros-cli lattice array-capture`:

| GUI | CLI |
| --- | --- |
| Tunggal | `chloros-cli lattice array-capture` |
| Berterusan | `array-capture --continuous [--count N] [--duration S]` |
| Interval | `array-capture --interval S [--duration S]` |
| Rakaman Terpantas | `array-capture --fastest` |
| Kotak semak selari | `--aligned / --no-aligned` |
| Kotak semak jenis eksport | `--processing LEVEL` atau `--levels L1,L2,…` (lalai `all`) |
| Rakam indeks video | `chloros-cli lattice array-record` |
| Rakam letupan mentah / Bina video | `chloros-cli lattice array-burst` / `array-build-video` |

Jadual bendera penuh, pilihan tangkapan AE pintar (`--smart`), dan model kadar berterusan terdapat dalam [Rujukan CLI § Mod Rakaman, Perakam & Pemprosesan Semula Luar Talian](../reference/cli-reference.md#capture-modes-recorders--offline-reprocess).
