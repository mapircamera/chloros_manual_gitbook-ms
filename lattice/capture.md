# Tetapan &amp; Mod Rakaman

Proses merakam di tab Kamera dikawal oleh satu butang merah **Rakam Semua**dan satu panel**Tetapan Rakaman** yang menentukan hasil butang tersebut: kamera mana yang akan turut serta, jenis eksport yang disimpan oleh setiap kamera, dan sama ada shutter akan diambil sekali, secara berterusan, atau pada selang masa. Halaman ini menerangkan keseluruhan aliran — konfigurasi, proses merakam itu sendiri, lokasi simpanan fail pada cakera, dan cara memprosesnya semula menjadi produk yang dikalibrasi kemudian. Kawalan kamera dan susunan sendiri berada di [Tetapan Kamera](camera-settings.md).

{% hint style="info" %}
**Sesi tangkapan memerlukan projek yang dibuka.** Butang &#x27;Capture All&#x27; dan gear Tetapan Tangkapan dilumpuhkan sehingga projek dibuka (&quot;Buat atau buka projek untuk menyimpan tangkapan&quot;). Setiap tangkapan disimpan di bawah folder projek dalam `captures/`.
{% endhint %}

## Panel Tetapan Rakaman

Bukanya dengan **gear di sebelah Rakam Semua**dalam senarai kamera bar sisi, atau dengan butang**&quot;Buka Tetapan Rakaman…&quot;** di bahagian bawah mana-mana panel tetapan setiap kamera. Headernya tertulis &quot;Tetapan Rakaman&quot; dengan butang kembali ←.

<!-- SCREENSHOT-NEEDED: the full Capture Settings pane — Single/Continuous/Interval mode buttons at top, the bulk export-type toggle rows (All Raw … All Index), the orange Fastest Capture toggle, an array group card with the Aligned checkbox and Record buttons, and an expanded per-camera row showing per-type checkboxes. -->

Pilihan anda di sini — termasuk kamera yang disertakan, kotak semak mengikut jenis, dan mod tangkapan — disimpan **setiap projek** dan dipulihkan apabila anda membukanya semula.

### Mod tangkapan

Tiga butang mod di bahagian atas tetingkap:

| Mod | Fungsinya | Tetapan anak (lalai) |
| --- | --- | --- |
| **Tunggal** *(lalai)* | Satu tangkapan untuk setiap kamera yang dipilih. | — |
| **Berterusan**| Rakaman berturut-turut sehingga memenuhi syarat henti. | Henti oleh**Bilangan rakaman** (lalai 1) *atau* **Tempoh rakaman** (lalai 10 s; unit: saat / minit / jam / hari). |
| **Selang**(timelapse) | Rangkaian tangkapan mengikut pemasa. |**Jumlah tangkapan / selang**(lalai 1) ·**Setiap**N unit (lalai 5 saat) ·**Untuk** N unit (lalai 1 minit). |

Dalam mod Berterusan atau Jeda, butang Rakam Semua akan menjadi butang **Hentikan (N)** semasa berjalan, mengira bilangan tangkapan yang diterima.

<!-- SCREENSHOT-NEEDED: the capture-mode area of Capture Settings with Interval selected — showing the "Captures / interval", "Every N (unit)" and "For N (unit)" rows with their defaults (1, 5 s, 1 m). -->




### Memilih kamera dan jenis eksport

Teks bantuan panel meringkaskan perkara ini: pilih kamera dan jenis eksport yang akan dihasilkan oleh &quot;Capture All&quot; — semuanya diaktifkan secara lalai, dan pilihan akan disimpan bersama projek ini.

* Butang **Pilih Semua / Pilih Tiada** menukar kotak semak termasuk setiap kamera sekaligus.
* **Togol jenis eksport pukal**(dua baris butang):**Semua Mentah / Semua Debayered / Semua Pratonton / Semua Radiance / Semua Refleksi / Semua Indeks**. Setiap satu diberi rona tiga keadaan: hijau ✓ = dihidupkan untuk setiap kamera yang menyokongnya, jingga – = dihidupkan untuk beberapa, kelabu = tiada. Suis akan dilumpuhkan apabila tiada kamera yang disambungkan menyokong jenis tersebut. Kesemuanya akan bertukar kelabu semasa Fastest Capture dihidupkan.
* **Baris bagi setiap kamera**: satu kotak semak untuk memasukkan, serta satu senarai boleh-tangani (▸/▾) bagi jenis eksport yang terpakai untuk kamera tersebut dengan kotak semak individu. Baris itu memaparkan kiraan terpasang seperti &quot;4/6&quot;.

### Jenis Eksport dan kamera yang menyokongnya

Terdapat enam jenis eksport: **Raw, Debayered, Radiance, Reflectance, Preview, Index**. Hanya jenis yang terpakai akan muncul pada setiap baris kamera:

| Jenis Eksport | Kandungan |RGB

(FRGB) | Bayer multispektral (FRGN/FOCN/FNGB) | Mono (M3M) |
| --- | --- | --- | --- | --- |
| **Raw** | Mozaik Bayer (mono: jalur tunggal) terus dari sensor | ✓ | ✓ | ✓ |
| **Debayered** | Demosaik linear (mono: skala kelabu 1-saluran) | ✓ | ✓ | ✓ |
| **Prviu** | Rantaian paparan penuh (imbangan putih + gamma mengikut profil kamera; multispektral: regangan warna palsu) | ✓ | ✓ | ✓ |
| **Radiasi** | float32 W/m²/sr/nm melalui rantaian radiometrik penuh | — (tidak ditawarkan) | ✓ | ✓ |
| **Reflektansi** | uint16 ρ (32768 = 1.0) | — (tidak ditawarkan) | ✓ — hanya dipaparkan apabila kamera mempunyai penderia cahaya DAQ (miliknya sendiri atau diwarisi daripada susunannya) | sama seperti multispektral |
| **Indeks** | Render Indeks Vegetasi (LUT) | — | ✓ — memerlukan ekspresi indeks yang diaktifkan dan tidak kosong pada kamera, dan tidak ditawarkan kepada ahli susunan gabungan (susunan itu memiliki satu indeks kongsi) | — (indeks memerlukan ≥2 jalur; lihat [Kamera Mono &amp; Indeks Vegetasi](mono-indices.md)) |

Radiasi dan pantulan tidak pernah ditawarkan untuk kameraRGB

— radiasi per-Bayer tidak bermakna untuk penderia fotometrik jalur lebar.

### Rakaman Terpantas

Togol **⚡ Rakaman Terpantas — mentah sahaja**(warna jingga apabila diaktifkan) akan menimpa semua pilihan eksport kepada**mentah sahaja** — serta satu komposit indeks gabungan percuma untuk susunan — supaya bingkai disimpan secepat mungkin: kiraan radiasi/pantulan/paparan diabaikan sepenuhnya semasa pengambilan.

{% hint style="info" %}
**Satu `.daq` masih disimpan.** Apabila penderia cahaya ditetapkan, Fastest Capture masih menulis bacaan DAQ downwelling di sebelah bingkai mentah — supaya produk sinaran, pantulan, dan indeks semua boleh dibina kemudian dengan memproses semula (lihat [Memproses semula tangkapan](#re-processing-captures-into-calibrated-products)). Fastest Capture juga tidak memusnahkan pilihan kotak semak anda: matikan ia dan ia akan kembali.
{% endhint %}

### Kawalan bagi setiap tatasusunan

Setiap tatasusunan yang disambungkan mendapat kad kumpulan sendiri dalam panel:

* **Sertakan kotak semak** (tiga keadaan merentasi ahli) dan nama susunan dengan mod paparan: &quot;(digabungkan | berasingan)&quot;.
* Kotak semak **Disesuaikan**(lanjutan**diaktifkan** secara lalai): menyejajarkan eksport ahli ke profil penjajaran susunan supaya eksport diselaraskan piksel merentasi kamera. Raw kekal tidak diwarp tetapi membawa transformasi dalam metadata-nya. (Profil itu sendiri dikira dalam [panel tetapan array](camera-settings.md#alignment-co-registration-combined-only).)
* Baris kamera ahli disarang di dalam kad.

Kad himpunan juga menempatkan dua perekod. Anggap ia sebagai **pemantauan berbanding analisis**:

| Perekod | Gred | Apa yang dirakam |
| --- | --- | --- |
| **● Rakam indeks video / ■ Hentikan rakaman** *(hanya untuk susunan gabungan)* | **Pemantauan** | Komposit indeks gabungan secara langsung ke video pada 10 fps — 8-bit, resolusi pratonton, LUT terbina dalam. Perlukan projek terbuka dan paparan langsung penstriman. Menunjukkan bingkai + masa berlalu semasa merakam. |
| **⦿ Rakam burst mentah / ■ Hentikan burst mentah** *(sebarang susunan)* | **Analisis**| Bingkai Bayer mentah pada kadar tangkapan langsung (tanpa pemprosesan) serta manifest setiap bingkai dan bacaan `.daq`, ke dalam `captures/bursts/`. Selepas burst, butang**Bina video** akan muncul: ia memproses semula burst secara luar talian menjadi video yang dikalibrasi — gabungan indeks dan/atau sinaran / pantulan / indeks bagi setiap kamera — serta TIFF pilihan. Pembinaan gabungan-indeks bermula secara automatik apabila anda menghentikan burst. |

<!-- SCREENSHOT-NEEDED: an array group card in Capture Settings while a raw burst is recording — the ⦿/■ burst button in its recording state with frame count, and (in a second capture) the Build video button that appears after stopping. -->

## Aliran

<!-- SCREENSHOT-NEEDED: the sidebar during a capture — Capture All showing live "Capturing… 3/6" progress text, and (second capture) the result flash "Saved N files". -->

Capture All Tekan **Capture All** dalam senarai kamera bar sisi:

1. Setiap kamera yang disertakan, kelihatan, dan tidak ditangguhkan merakam dengan jenis eksport yang dipilih. **Susunan mencetuskan sebagai satu pencetus selari** (satu kumpulan selari tunggal merentasi semua ahli — lihat [Susunan Multi-Kamera](arrays.md)); kamera berdiri sendiri merakam secara individu.
2. Kamera tersembunyi (ikon mata) atau yang ditangguhkan akan dilangkau. Susunan hanya akan dihalang sepenuhnya apabila *semua* ahlinya tersembunyi atau ditangguhkan.
3. Setiap kali penderia cahaya ditetapkan, bacaan DAQ downwelling yang sepadan disimpan sebagai fail `.daq` bersama imej — walaupun untuk tangkapan mentah sahaja — supaya produk radiometrik sentiasa boleh diperoleh kemudian.
4. Butang memaparkan kemajuan secara langsung — &quot;Merakam… siap/jumlah&quot; — dan dalam mod Berterusan/Selang menjadi **Hentikan (N)**. Setiap item tangkapan mempunyai had masa tamat 300 saat.
5. Apabila pass selesai, flash keputusan akan melaporkan **&quot;Disimpan N fail&quot;**atau**&quot;Disimpan N, F gagal&quot;**, serta &quot;(S tersembunyi/ditangguhkan dilangkau)&quot; apabila kamera dilangkau.

## Tempat simpanan tangkapan

Tangkapan disimpan di bawah projek terbuka di `<project>/captures/`. Setiap jenis eksport disimpan dalam **subfolder tersendiri**, jadi tangkapan berbilang-tingkat tidak pernah mencampurkan jenis:

```
<project>/captures/
├── raw/           capture_<ts>_SN<serial>_raw.tif
├── debayered/     capture_<ts>_SN<serial>_debayered.tif
├── radiance/      capture_<ts>_SN<serial>_radiance.tif
├── reflectance/   capture_<ts>_SN<serial>_reflectance.tif
├── preview/       capture_<ts>_SN<serial>_display.tif
├── index/         per-camera vegetation-index (LUT) render, when Index is selected
├── composite/     array foreground/background live-view composite, when produced
├── bursts/        raw-burst recordings (frames + manifest + .daq per burst)
└── *.daq          the downwelling reading matched to the capture
```

* `<ts>` ialah cap masa tangkapan dan `<serial>` ialah siri kamera. Rakaman berdiri sendiri dinamakan `capture_<ts>_SN<serial>_<level>`; rakaman susunan dari satu pencetus diselaras dinamakan `sync_<ts>_SN<serial>_<level>` dan **membahagi satu cap masa merentas semua kamera dalam kumpulan** (sufiks aras diabaikan apabila kamera hanya menyimpan satu aras).
* **Satu asimetri yang perlu diketahui:** tahap paparan disimpan dalam folder bernama `preview/` manakala fail mengekalkan `_display` dalam nama — folder dan suku kata berbeza hanya untuk tahap itu.
* Tahap yang tidak diketahui akan menggunakan folder dengan nama mereka sendiri; jika subfolder tidak dapat dibuat, fail itu akan ditulis ke dalam root captures dan bukannya hilang.
* Fail TIFF tangkapan dimampatkan tanpa kehilangan (DEFLATE) secara lalai dan membawa metadata penentukuran dan pemprosesan penuh mereka **di dalam XMP fail** — tangkapan menerangkan sendiri, tanpa fail sampingan selain bacaan `.daq`.

Ini adalah susun atur yang sama `chloros-cli lattice capture` / `array-capture` menulis ke dalam direktori `-o` mereka — didokumenkan dalam [RujukanCLI

§ Apa rupa folder captures](../reference/cli-reference.md#what-a-captures-folder-looks-like).

<!-- SCREENSHOT-NEEDED: OS file explorer showing a real <project>/captures/ folder after a multi-level array capture — the raw/debayered/radiance/reflectance/preview subfolders, a .daq file at the root, and sync_<ts>_SN<serial>_<level>.tif filenames visible inside one subfolder. -->

## Memproses semula tangkapan menjadi produk yang dikalibrasi

Rangka mentah yang dirakam serta `.daq` yang disimpan adalah segala yang diperlukan oleh saluran pemprosesan — inilah sebabnya Fastest Capture selamat untuk kerja sebenar.

* **GUI**: tambahkan folder captures ke dalam projek ([Menambah Fail ke dalam Projek](../processing-images-gui/adding-files-to-a-project.md)) dan proses seperti biasa.
* **CLI**: arahkan `process` ke **akar captures**:

```bash
chloros-cli process "C:/ChlorosProjects/MyField/captures"
```

`process` biasanya hanya mengimport folder yang anda namakan, tetapi apabila folder itu tidak mengandungi imej dan mempunyai subfolder, ia akan menelusuri secara automatik — jadi subfolder aras dan fail akar `.daq` diambil sekaligus. Setiap tangkapan diimpor sebagai **satu imej tunggal** dengan tahap-tahap lain dilampirkan sebagai mod tontonan, bukan sebagai satu imej bagi setiap tahap.

Memberi nama kepada subfolder tahap secara langsung (contohnya `…/captures/raw/`) juga berfungsi, tetapi meninggalkan fail akar `.daq` — salin mereka bersama apabila anda menghasilkan semula produk radiometrik daripada `raw/`, jika tidak padanan cap masa tidak akan ada apa-apa untuk dirujuk.

{% hint style="warning" %}
**Pemprosesan sentiasa bermula daripada `raw`.** Dalam setiap tangkapan, bingkai mentah adalah sumber saluran; `debayered`, `radiance`, `reflectance`, dan `preview` muncul sebagai mod tontonan tetapi tidak pernah dimasukkan semula ke dalam saluran — memproses semula produk terbitan akan menerapkan semula vinet, warna, dan matematik sinaran yang sudah tertanam dalam pikselnya, jadiChloros

menolaknya daripada memproses dua kali. Render `index/` dan `composite/` langsung tidak diproses (mereka adalah keluaran, bukan tangkapan). Folder &#x27;captures&#x27; disimpan **tanpa** import mentah dan dipaparkan secara normal, tetapi `process` melangkauinya dan memberitahu demikian; `--input-level {raw,debayered,processed}` adalah pintu pelarian yang disengajakan yang memaksa titik kemasukan. Rujuk [RujukanCLI

](../reference/cli-reference.md#what-a-captures-folder-looks-like) untuk mesej lompatan yang tepat.
{% endhint %}

Dua lagi tingkah laku yang perlu diketahui apabila menulis skrip pemprosesan semula:

* Pelaksanaan `chloros-cli process` yang meminta produk tetapi tidak menulis **sebarang produk imej akan gagal dengan bunyi kuat dan keluar dengan nilai bukan sifar** — anda tidak akan mendapat pelaksanaan kosong secara senyap. Pelaksanaan yang berjaya akan melaporkan jumlah produk mereka. (Pelaksanaan khusus hanya metadata masih dikira sebagai berjaya.)
* Eksport yang diproses dan diimport semula tidak pernah mengambil slot mentah tangkapan — mentah asal sentiasa kekal sebagai sumber saluran.

## SetaraCLI



Segala yang di halaman ini boleh dikendalikan secara tanpa kepala. Mod tangkapan GUI memetakan terus ke `chloros-cli lattice array-capture`:

| GUI |CLI

|
| --- | --- |
| Satu | `chloros-cli lattice array-capture` |
| Berterusan | `array-capture --continuous [--count N] [--duration S]` |
| Interval | `array-capture --interval S [--duration S]` |
| Rakaman Terpantas | `array-capture --fastest` |
| Kotak semak selari | `--aligned / --no-aligned` |
| Kotak semak jenis eksport | `--processing LEVEL` atau `--levels L1,L2,…` (lalai `all`) |
| Rakam indeks video | `chloros-cli lattice array-record` |
| Rakam letupan mentah / Bina video | `chloros-cli lattice array-burst` / `array-build-video` |

Jadual bendera penuh, pilihan smart-AE settled-capture (`--smart`), dan model kadar berterusan wujud dalam [CLI

Rujukan § Mod Rakaman, Perakam &amp; Pemprosesan Semula Luar Talian](../reference/cli-reference.md#capture-modes-recorders--offline-reprocess).
