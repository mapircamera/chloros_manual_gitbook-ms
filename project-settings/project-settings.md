# Tetapan Projek

Bar sisi Tetapan Projek <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line">

dalam Chloros

membolehkan anda mengkonfigurasi semua aspek pemprosesan imej, pengesanan sasaran penentukuran, pengiraan indeks multispektral, dan pilihan eksport untuk projek anda. Tetapan ini disimpan bersama projek anda dan boleh disimpan sebagai templat untuk digunakan semula dalam pelbagai projek.

## Mengakses Tetapan Projek

Untuk mengakses Tetapan Projek:

1. Buka projek dalam Chloros


2. Klik tab **Project Settings** <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line">

di bar sisi kiri
3. Panel tetapan akan memaparkan semua pilihan konfigurasi yang tersedia yang disusun mengikut kategori

<!-- SCREENSHOT-NEEDED: Full Project Settings sidebar of a LATTICE project, scrolled so the Processing category is visible showing the per-product export checkboxes (Export sensor response, Export vignette corrected, Export debayered, Export preview, Export radiance, Export reflectance) and the Debayer method row. -->



{% hint style="info" %}
**Seting yang bergantung pada seting lain akan diwarnakan kelabu.** Apabila suis ibu bapa menjadikan sesuatu tetapan mustahil (contohnya, mencentang *Kalibrasi pantulan / imbangan putih* menjadikan *Eksport pantulan* mustahil), kawalan bergantung akan dilumpuhkan dan petua alatnya akan menamakan suis yang perlu diubah.
{% endhint %}

***

## Paparan

### Resolusi Miniatur Imej

* **Jenis**: Pilihan turun-ke-bawah
* **Pilihan**: `Default (512 px)`, `1024 px`, `2048 px`, `Full resolution`
* **Lalai**: Lalai (512 px)
* **Keterangan**: Resolusi (sisi terpanjang, dalam piksel) di mana pratonton grid-imej dipaparkan. Nilai yang lebih tinggi kelihatan lebih tajam apabila diperbesarkan tetapi memuat lebih perlahan dan menggunakan lebih banyak memori. Resolusi penuh digunakan untuk saiz imej asal.
* **Nota**: Hanya untuk paparan — ini tidak pernah menjejaskan pemprosesan atau fail yang dieksport.***

## Pengesanan Sasaran

Pilihan ini mengawal cara Chloros

mengesan dan memproses sasaran penentukuran dalam imej anda. Kedua-duanya hanya aktif semasa **Penentukuran pantulan / imbangan putih** diaktifkan (ia akan kelihatan kelabu jika tidak, kerana pengesanan sasaran akan dilangkau sepenuhnya).

### Kawasan sampel penentukuran minimum (px)

* **Jenis**: Nombor
* **Julat**: 0 hingga 10,000 piksel
* **Lalai**: 25 piksel
* **Keterangan**: Menetapkan kawasan minimum (dalam piksel) yang diperlukan untuk sesuatu kawasan yang dikesan dianggap sebagai sampel sasaran penentukuran yang sah. Nilai yang lebih kecil akan mengesan sasaran yang lebih kecil tetapi mungkin meningkatkan positif palsu. Nilai yang lebih besar memerlukan kawasan sasaran yang lebih besar dan lebih jelas untuk dikesan.
* **Bilakah hendak melaraskan**:
  * Tingkatkan jika anda mendapat pengesanan palsu pada artifak imej kecil
  * Kurangkan jika sasaran penentukuran anda kelihatan kecil dalam imej anda dan tidak dikesan

### Pengelompokan Sasaran Minimum (0-100)

* **Jenis**: Nombor
* **Julat**: 0 hingga 100
* **Lalai**: 60
* **Keterangan**: Mengawal ambang pengelompokan untuk mengelompokkan kawasan berwarna serupa semasa mengesan sasaran penentukuran. Nilai yang lebih tinggi memerlukan warna yang lebih serupa untuk dikelompokkan bersama, mengakibatkan pengesanan sasaran yang lebih konservatif. Nilai yang lebih rendah membenarkan lebih banyak variasi warna dalam kumpulan sasaran.
* **Bilakah hendak melaraskan**:
  * Tingkatkan jika sasaran penentukuran dipecahkan kepada beberapa pengesanan
  * Kurangkan jika sasaran penentukuran dengan variasi warna tidak dikesan sepenuhnya

***

## Pemprosesan

Tetapan ini mengawal cara Chloros

memproses dan menentukur imej anda.

### Pembetulan Vignette

* **Jenis**: Petak semak
* **Lalai**: Diaktifkan (disemak)
* **Keterangan**: Terapkan pembetulan vignet untuk mengimbangi penggelapan lensa di tepi imej. Vignetting adalah fenomena optik biasa di mana sudut dan tepi imej kelihatan lebih gelap daripada bahagian tengah disebabkan ciri-ciri lensa.
* **Kesan sampingan**: Suis ini juga memilih *produk sandaran tanpa kalibrasi* yang ditulis oleh sesuatu sesi (lihat di bawah).

### Kalibrasi pantulan / imbangan putih

* **Jenis**: Petak semak
* **Lalai**: Diaktifkan (disemak)
* **Keterangan**: Mengaktifkan penentukuran pantulan — daripada sasaran penentukuran yang dikesan dalam bingkai dan/atau data sinaran bawah penderia cahaya DAQ, bergantung pada kamera dan apa yang tersedia. Ini menormalkan nilai pantulan merentasi set data anda dan memastikan ukuran yang konsisten tanpa mengira keadaan pencahayaan.
* **Apabila dilumpuhkan**: Pengesanan sasaran diabaikan sepenuhnya, dan**tiada produk pantulan boleh dihasilkan oleh mana-mana kamera** — sama ada yang dipacu sasaranSurvey3

atau yang dipacu DAQ LATTICE. Tetapan bergantung (*Eksport pantulan*, *Jeda pengesahan semula minimum*, dan ambang Pengesanan Sasaran) akan diwarnakan kelabu.

### Produk sandaran tanpa kalibrasi: Eksport tindak balas penderia / Eksport yang diperbetulkan vignet

* **Jenis**: Dua kotak semak
* **Lalai**: Kedua-duanya diaktifkan (disemak)
* **Keterangan**: Apabila satu bingkai tidak dapat dikalibrasi reflektans (tiada sasaran kalibrasi ditemui, atau kalibrasi reflektans dilumpuhkan), ia akan ditulis sebagai *produk sandaran tidak dikalibrasi* sebaliknya. **Hanya satu daripada dua produk sandaran wujud bagi setiap larian, untuk setiap model kamera**, dipilih oleh suis *Pembetulan Vignette*:
  * Pembetulan Vignette **aktif**→ `Vignette_Corrected_Images/` (dikawal oleh**Eksport vignette yang dibetulkan**)
  * Pembetulan Vignette **pasif**→ `Sensor_Response_Images/` (dikawal oleh**Eksport tindak balas sensor**)
* Produk sandaran yang tidak digunakan akan diwarnakan kelabu. Mengosongkan kotak pilihan untuk produk yang digunakan akan menghentikan penulisan fail tersebut sama sekali.

### Produk eksport LATTICE

Untuk projek yang mengandungi tangkapan LATTICE, setiap bingkai LATTICE yang diimport akan diedarkan kepada setiap produk **dan terpakai**yang diaktifkan dalam satu pusingan pemprosesan. Empat kotak semak mengawal pengembangan (semua lalai**diaktifkan**):

| Tetapan | Folder keluaran | Apa yang dieksport |
| --- | --- | --- |
| **Eksport debayered** | `Debayered_Images/` | Imej debayered linear: offset gelap pek penentukuran dialihkan, kemudian demosaik sedar-tepi, tanpa pembetulan lineariti, medan rata atau pencampuran semula — fail yang sama yang disimpan oleh tangkapan pada tahap debayerednya. Disimpan dalam orientasi asal sensor (direkodkan dalam XMP). Terpakai pada kamera RGB

dan multispektral. |
| **Keluaran pratonton** | `Preview_Images/` | Pratonton paparan. RGB

= imbangan putih (DAQ-illuminant apabila tersedia, jika tidak dunia-kelabu) + gamma; multispektral = regangan warna palsu. |
| **Eksport radiasi** | `Radiance_Images/` | Radiasi spektral Float32 dalam W/m²/sr/nm. Multispektral (M3C/M3M) sahaja — tidak terpakai untuk imej induk RGB

. Sentiasa ditulis sebagai TIFF

32-bit tanpa mengira tetapan *Format imej Terkalibrasi*. |
| **Eksport pantulan**| `Reflectance_Calibrated_Images/` | Pantulan Uint16, diskalakan supaya**32768 = pantulan 1.0** (ditandakan sebagai XMP `Chloros:PixelScale`). Multispektral sahaja, ditulis apabila rekod `.daq` downwelling yang sepadan (atau sasaran dalam bingkai yang lulus QA) menutupi bingkai. |

* Kamera induk RGB

memancarkan bayaran semula + pratonton; sinaran/pantulan diabaikan untuknya kerana tidak terpakai.
* Kedalaman bit bayaran semula/pratonton mengikut tetapan *Format imej dikalibrasi*; sinaran sentiasa float32.
* Pemprosesan Survey3

tidak terjejas oleh empat suis ini.
* Produk radiasi dan pantulan kamera multispektral M3C juga boleh menulis per piksel **topeng clamp** ke dalam folder `clamp_masks/` di sebelah folder produk, merekodkan di mana unmix jalur memotong nilai negatif kepada sifar. Lihat [Format Imej Keluaran](../output-image-formats.md#clamp-masks).

Empat suis yang sama wujud secara headless sebagai `chloros-cli process --debayered / --preview / --radiance / --reflectance`. Mereka menggantikan suai leraian CLI

`--radiometric-output` yang lama, yang tidak wujud lagi.

{% hint style="warning" %}
**Menutup semua produk yang terpakai menyebabkan pelaksanaan gagal.** Mulai versi 1.2.0, pelaksanaan pemprosesan yang diminta untuk produk tetapi tidak menulis sebarang produk imej akan melaporkan kegagalan dan CLI

akan keluar dengan nilai bukan sifar, bukannya melaporkan kejayaan senyap. Log akan menyenaraikan produk yang tidak dapat ditulis dan sebabnya. Pelaksanaan yang disengajakan hanya untuk metadata (tiada yang diminta) masih dianggap berjaya.
{% endhint %}

### Sumber pantulan (tetapan projek, ditetapkan melalui CLI

)

Projek juga menyimpan **rujukan pantulan** yang digunakan oleh produk pantulan LATTICE. Tiada kawalan khusus dalam panel tetapan; nilai disimpan dalam konfigurasi projek sebagai `Processing → "Target reflectance source"` dan ditetapkan dengan `chloros-cli process --reflectance-source {auto,target,daq}` (SDK

&#x27;s `configure()` tidak mempunyai kata kunci untuknya):

* **`auto`** (lalai): sasaran penentukuran dalam bingkai yang lulus QA menjadi rujukan mutlak, beralih kepada pembahagi DAQ downwelling (ρ = πL/E) apabila tiada sasaran atau QA gagal. Sasaran yang lulus QA dengan penanda (panel yang terbuang, atau jalur yang tidak teratur) tidak secara automatik diutamakan: rantaian sasaran, sasaran+DAQ dan hanya-DAQ dicuba mengikut susunan skor yang diukur pada panel yang disisihkan.
* **`target`**: reflektansi berpandu sasaran yang ketat — tiada penggantian DAQ.
* **`daq`**: reflektansi berkuasa DAQ; sasaran dalam bingkai tidak digunakan sebagai rujukan.

Nilai yang disimpan dipadankan tanpa mengira huruf besar-kecil dan beberapa ejaan diterima sebagai nama lain: `target`, `target_image`, `empirical` dan `empirical_line` kedua-duanya bermaksud **sasaran**; `daq`, `dls`, `light_sensor` dan `sensor` semuanya bermaksud**daq**. Segala yang lain — termasuk kunci yang tiada — diselesaikan sebagai**auto**.

Imbasan sasaran **diukur** bagi setiap unit dicari berdasarkan nombor siri/QR unit sasaran, seperti `<serial>.csv`, di tiga tempat: direktori yang diberikan dengan `--target-reflectance-dir` (disimpan sebagai `Processing → "Target reflectance dir"`), folder `target_reflectance/` projek itu sendiri, dan laluan dalam pembolehubah persekitaran `CHLOROS_TARGET_REFLECTANCE_DIR`. Apabila tiada imbasan yang diukur wujud untuk unit tersebut, lengkung terbitan nominal untuk model sasaran akan digunakan sebaliknya.

### Kaedah Debayer

* **Jenis**: Pilihan turun-ke bawah
* **Pilihan**:
  * Standard (Pantas, Kualiti Sederhana)
  * Sedar Tekstur (Lambat, Kualiti Tertinggi) \[Chloros

+]
* **Lalai**: Standard (Pantas, Kualiti Sederhana)
* **Keterangan**: Memilih algoritma demosaicing yang digunakan untuk menukar data sensor corak Bayer mentah kepada imej berwarna penuh. Kaedah &quot;Standard (Pantas, Kualiti Sederhana)&quot; menyediakan keseimbangan optimum antara kelajuan pemprosesan dan kualiti imej. Kaedah &quot;Texture Aware (Lambat, Kualiti Tertinggi)&quot; [Chloros

+] menggunakan debayer berkualiti tinggi yang peka kepada sempadan digabungkan dengan model penyahbisuan AI/ML yang membuang hampir semua bunyi debayering. Model Texture Aware memerlukan memori GPU (VRAM) untuk dijalankan. Kami mengesyorkan menggunakannya apabila anda mempunyai &gt;4GB VRAM tersedia untuk pemprosesan yang lebih pantas.
* **Apabila baris itu adalah menu lungsur sama sekali**: menu lungsur dua pilihan akan muncul hanya apabila**kedua-duanya** benar — anda telah log masuk dengan langganan Chloros

+ yang layak, **dan** projek tidak mengandungi sebarang tangkapan LATTICE. Jika tidak, baris tersebut akan dipaparkan sebagai teks biasa yang membaca `Standard (Fast, Medium Quality)` tanpa apa-apa untuk dipilih.
* **Nota LATTICE**: Tiada model Texture Aware yang dilatih oleh LATTICE, dan pipeline memaksa demosaic standard untuk bingkai LATTICE tanpa mengira nilai yang disimpan. Jika anda menambah folder LATTICE ke dalam projek yang sudah memilih Texture Aware, Chloros

menulis semula tetapan kepada Standard dan bukannya mengekalkan nilai lapuk dalam `project.json`.

### Jarak semakan semula minimum

* **Jenis**: Nombor
* **Julat**: 0 hingga 3,600 saat
* **Lalai**: 0 saat
* **Keterangan**: Menetapkan selang masa minimum (dalam saat) antara penggunaan sasaran penentukuran. Apabila ditetapkan kepada 0, Chloros

akan menggunakan setiap sasaran penentukuran yang dikesan. Apabila ditetapkan kepada nilai yang lebih tinggi, Chloros

hanya akan menggunakan sasaran penentukuran yang dipisahkan oleh sekurang-kurangnya jumlah saat ini, sekali gus mengurangkan masa pemprosesan untuk set data yang kerap merakam sasaran penentukuran.
* **Bilakah hendak laras**:
  * Tetapkan kepada 0 untuk ketepatan penentukuran maksimum apabila keadaan pencahayaan berubah-ubah
  * Tingkatkan (contohnya, kepada 60-300 saat) untuk pemprosesan yang lebih pantas apabila pencahayaan konsisten dan anda mempunyai imej sasaran penentukuran yang kerap

### Ofset zon waktu penderia cahaya

* **Jenis**: Nombor
* **Julat**: -12 hingga +12 jam
* **Lalai**: 0 jam
* **Keterangan**: Menentukan selisih zon waktu (dalam jam dari UTC) untuk cap waktu data penderia cahaya, digunakan apabila memadankan log penderia cahaya dengan masa tangkapan imej. Rakaman `.daq` yang lebih baru membawa asal zon waktu mereka sendiri, jadi ini terutamanya diperlukan untuk log lama yang dirakam dalam waktu tempatan.

### Terapkan pembetulan PPK

* **Jenis**: Petak semak
* **Lalai**: Dilumpuhkan (tidak dipilih)
* **Keterangan**: Mengaktifkan penggunaan pembetulan Kinematik Pascaproses (PPK) daripada perekod DAQ MAPIR

yang mengandungi GPS (GNSS). Apabila diaktifkan, Chloros

akan menggunakan sebarang fail log .daq yang mengandungi data pin pendedahan dalam direktori projek anda dan menerapkan pembetulan geolokasi tepat pada imej anda.
* **Keperluan**: Fail log .daq dengan entri pin pendedahan mesti ada dalam direktori projek anda
* **Bilakah untuk diaktifkan**: Disyorkan untuk sentiasa mengaktifkan pembetulan PPK jika anda mempunyai entri maklum balas pendedahan dalam fail log .daq anda.

### Pin Pendedahan 1

* **Jenis**: Pilihan turun-bawah
* **Kelihatan**: Hanya kelihatan apabila &quot;Terapkan pembetulan PPK&quot; diaktifkan DAN data pendedahan tersedia untuk Pin 1
* **Pilihan**:
  * Nama model kamera yang dikesan dalam projek
  * &quot;Jangan Gunakan&quot; - Abaikan pin pendedahan ini
* **Lalai**: Dipilih secara automatik berdasarkan konfigurasi projek
* **Keterangan**: Menugaskan kamera tertentu kepada Pin Eksposur 1 untuk penyelarasan masa PPK. Pin eksposur merekodkan masa tepat apabila shutter kamera dicetuskan, yang sangat penting untuk geolokasi PPK yang tepat.
* **Tingkah laku pemilihan automatik**:
  * Satu kamera + satu pin: Memilih kamera secara automatik
  * Satu kamera + dua pin: Pin 1 ditetapkan secara automatik kepada kamera
  * Pelbagai kamera: Pemilihan manual diperlukan

### Pin Pendedahan 2

* **Jenis**: Pilihan turun-bawah
* **Kelihatan**: Hanya kelihatan apabila &quot;Terapkan pembetulan PPK&quot; diaktifkan DAN data pendedahan tersedia untuk Pin 2
* **Pilihan**:
  * Nama model kamera yang dikesan dalam projek
  * &quot;Jangan Gunakan&quot; - Abaikan pin pendedahan ini
* **Lalai**: Pilihan automatik berdasarkan konfigurasi projek
* **Keterangan**: Menugaskan kamera tertentu kepada Pin Pendedahan 2 untuk penyelarasan masa PPK apabila menggunakan tetapan dwi-kamera.
* **Tingkah laku pemilihan automatik**:
  * Satu kamera + satu pin: Pin 2 secara automatik ditetapkan kepada &quot;Jangan Gunakan&quot;
  * Satu kamera + dua pin: Pin 2 secara automatik ditetapkan kepada &quot;Jangan Gunakan&quot;
  * Pelbagai kamera: Pemilihan manual diperlukan
* **Nota**: Kamera yang sama tidak boleh ditugaskan kepada Pin 1 dan Pin 2 pada masa yang sama.***

## Penderia Cahaya DAQ

Bahagian ini muncul dalam Tetapan Projek dan menyenaraikan setiap fail DAQ downwelling dalam projek — rakaman `.daq` dan DAQ-M log `.csv` downwelling. Rakaman yang dibuat di tab Light Sensors akan ditambah ke dalam projek terbuka secara automatik.

<!-- SCREENSHOT-NEEDED: Project Settings "DAQ Light Sensor" section of a project containing at least one .daq file, showing the "Cap override (all files)" dropdown and a per-file row with its resolved cap. -->



Setiap baris memaparkan fail, model sensor, dan pembetulan diffuser-cap yang sedang digunakan untuk fail tersebut. Di atas baris-baris tersebut terdapat satu kawalan untuk seluruh projek:

### Keutamaan topi (semua fail)

* **Jenis**: Pilihan turun-ke bawah
* **Pilihan**: `Auto` serta profil pembetulan topi yang sah untuk jenis sensor yang terdapat dalam projek
* **Lalai**: Auto
* **Disimpan sebagai**: `Processing → "DAQ cap id"` (default `auto`)
* **Keterangan**: `Auto` menggunakan topi yang direkodkan bagi setiap fail (topi Sunshine dianggap digunakan apabila tiada apa-apa yang direkodkan, tetapi DAQ-U, DAQ-M dan DAQ-E dihantar tanpa topi dan Chloros

tidak dapat melihat apa yang dipasang, oleh itu pemprosesan memberi amaran tentang topi yang diandaikan: pilih topi yang dipasang). Memilih topi tertentu akan menimpa **setiap** fail downwelling dalam projek kecuali DAQ-E-S, yang penyebarnya adalah sebahagian daripada penentukuran; projek yang hanya mengandungi DAQ-E-S tidak memaparkan menu lungsur: rakaman mentah diperbetulkan dengannya, dan rakaman yang sudah mempunyai cap akan dirujuk semula (pembetulan yang dirakam dibatalkan dan yang dipilih digunakan).
* **Penting**: Cap yang dipilih mesti sepadan dengan cap yang dipasang secara fizikal semasa rakaman. Sama ada sensor mahupun perisian tidak dapat mengesan cap fizikal — ID cap yang tidak sepadan akan membetulkan spektra dengan salah.

Secara sengaja terdapat **satu** kawalan untuk seluruh projek dan bukannya menu lungsur bagi setiap fail: tetapan ini akan sampai ke setiap sumber yang mengalir ke bawah dalam projek.***

## Penyelarasan Susunan

Bahagian ini hanya akan muncul apabila sekurang-kurangnya satu imej dalam projek mengandungi transformasi penjajaran modul-ke-modul yang dicop oleh susunan LATTICE semasa pengambilan (tag XMP `Chloros:Alignment*`). Ia menunjukkan berapa banyak imej mempunyai tag penjajaran, kamera mana yang menjadi rujukan (lencana `REF`), dan jadual bagi setiap kamera yang mengandungi bilangan imej.

<!-- SCREENSHOT-NEEDED: Project Settings "Array Alignment" section for an imported LATTICE array capture set, showing the tagged-image count, the per-camera rows with the REF badge, and the three controls (Apply array alignment, Crop to common overlap, Resampling). -->

### Terapkan penjajaran susunan

* **Jenis**: Petak semak
* **Lalai**: Diaktifkan (disemak)
* **Disimpan sebagai**: `Processing → "Array alignment"`
* **Keterangan**: Memutarbelitkan setiap produk yang diproses (debayered / pratonton / radiance / pantulan / indeks) ke dalam geometri rujukan bersama tatasusunan menggunakan transformasi yang dicap semasa pengambilan. Matikan = mengeksport dalam geometri asli bagi setiap sensor.

### Potong ke tumpang tindih biasa

* **Jenis**: Petak semak (hanya aktif semasa *Terapkan penjajaran tatasusunan* diaktifkan)
* **Lalai**: Diaktifkan (disemak)
* **Disimpan sebagai**: `Processing → "Array alignment crop"`
* **Keterangan**: Memotong eksport yang diselaraskan ke kawasan yang dikongsi oleh semua modul kamera, supaya setiap jalur mempunyai jejak yang sama. Matikan mengekalkan kanvas sensor penuh (isian hitam di luar sumber).

### Pengsampelan Semula

* **Jenis**: Pilihan turun-bawah (hanya aktif apabila *Terapkan penjajaran tatasusunan* diaktifkan)
* **Pilihan**: `Bilinear (smooth, default)`, `Nearest (preserve exact values)`, `Cubic (sharpest)`
* **Lalai**: Bilinear
* **Disimpan sebagai**: `Processing → "Array alignment interpolation"`
* **Keterangan**: Interpolasi yang digunakan oleh warp penjajaran. Nearest mengekalkan nilai sumber sebenar (tiada pencampuran antara piksel) untuk analisis radiometrik yang ketat; Bilinear adalah yang terbaik untuk pemetaan dan kegunaan visual.

Tiga pilihan yang sama wujud tanpa kepala sebagai `chloros-cli process --array-alignment`, `--array-alignment-crop`, dan `--array-alignment-interp {bilinear,nearest,cubic}`.

***

## Indeks

Tetapan ini membolehkan anda mengkonfigurasi indeks multispektral untuk analisis dan visualisasi.

### Tambah indeks

* **Jenis**: Panel konfigurasi indeks khas
* **Keterangan**: Membuka panel interaktif di mana anda boleh memilih dan mengkonfigurasi indeks vegetasi multispektral (NDVI

, NDRE

, EVI

, dan lain-lain) untuk dikira semasa pemprosesan imej. Anda boleh menambah berbilang indeks, setiap satu dengan tetapan visualisasi tersendiri.
* **Indeks yang tersedia**: Senarai lungsur GUI termasuk**27** formula indeks multispektral yang telah ditetapkan (lihat [Formula Indeks Multispektral](multispectral-index-formulas.md) untuk senarai penuh, termasuk nama yang juga diterima oleh CLI

/SDK

`--indices` pilihan).
* **Ciri-ciri**:
  * Pilih daripada formula indeks yang telah ditetapkan
  * Seret saluran penapis kamera anda ke dalam slot jalur formula
  * Konfigurasikan gradien warna visualisasi (LUT - Jadual Rujukan)
  * Tetapkan nilai ambang dan mod pemotongan
  * Buat formula indeks tersuai
* **Nota**: Indeks tidak dikira untuk kamera mono LATTICE M3M jalur tunggal — indeks jalur-pelbagai tidak ditakrifkan pada satu jalur. Survey3

dan LATTICE M3C tidak terjejas.

<!-- SCREENSHOT-NEEDED: Project Settings > Index section with one index added and expanded: the filter dropdown, the formula dropdown open showing preset names, the coloured channel circles above the rendered formula, and the "+ Add LUT" button below it. -->



Setiap indeks yang anda tambah memaparkan formula matematiknya, dengan bulatan berwarna bagi setiap slot jalur: merah = Red

, hijau = Green

, biru = Blue

, jingga = Orange

, sian = Cyan

, ungu = NIR

, magenta = RE. Seret bulatan dari baris di atas formula ke atas slot untuk mengikatnya; klik dua kali slot yang terikat untuk membersihkannya. Indeks hanya akan dikira apabila setiap slot yang digunakan oleh formula mempunyai saluran.

### Formula Tersuai (Ciri Chloros

+)

* **Jenis**: Susunan definisi formula tersuai
* **Ketersediaan**: Memerlukan log masuk dengan langganan Chloros

+ yang layak.
* **Keterangan**: Membolehkan anda mencipta dan menyimpan formula indeks multispektral tersuai menggunakan matematik jalur. Formula tersuai disimpan bersama tetapan projek anda dan boleh digunakan seperti indeks terbina dalam.
* **Cara mencipta**:
  1. Dalam panel konfigurasi Indeks, buka pengira formula tersuai
  2. Tulis formula menggunakan **simbol slot jalur**, bukan nama jalur
  3. Simpan formula dengan nama yang menerangkan — ia kemudian akan muncul di bahagian bawah menu lungsur formula, dan anda boleh menyeret bulatan saluran kamera anda ke dalam slotnya dengan tepat seperti pratetap terbina dalam
* **Tata bahasa formula**:
  * Slot jalur: `x`, `y`, `z`, `a`, `b`, `c` — enam kedudukan yang anda peta ke saluran sebenar dengan menyeret
  * Pengendali: `+`, `-`, `*`, `/`, `^`, dan `()` untuk pengelompokan
  * Fungsi: `sqrt()`, `log()`, `ln()`, `abs()`, `sign()`, `log1p()`, `log2()`
* **Mengapa simbol, bukan nama kumpulan**: satu formula yang ditulis sebagai `(y-x)/(y+x)` berfungsi pada mana-mana kamera, kerana pemetaan seret-dan-lepas menentukan sama ada `y` adalah NIR

850 nm bagi penapis RGN

atau NIR

808 nm bagi penapis OCN

. The preset terbina dalam disimpan dengan cara yang sama — lihat [Formula Indeks Multispektral](multispectral-index-formulas.md) untuk bentuk simbol tepat bagi kesemua 27.
* **Di mana ia berfungsi**: formula tersuai disimpan bersama tetapan projek dan boleh digunakan dalam [Index/LUT Sandbox](../image-viewer-gui/index-lut-sandbox.md) serta dalam pemprosesan. Nama-nama ini**tidak** diterima oleh senarai nama CLI

/SDK

`--indices`, yang hanya mengembangkan 22 nama pratetap terbina dalam.

***

## Eksport

Pilihan ini mengawal format dan kualiti imej terproses yang dieksport.

### Format imej yang dikalibrasi

* **Jenis**: Pilihan turun-ke bawah
* **Pilihan**:
  * **TIFF

(16-bit)** - Format TIFF

16-bit tanpa pemampatan
  * **TIFF

(8-bit)** - Format TIFF

8-bit tanpa pemampatan
  * **TIFF

(32-bit, Peratus)** - TIFF

32-bit titik terapung dengan nilai pantulan sebagai peratusan
  * **PNG

(8-bit)** - Format PNG

8-bit terkompresi
  * **JPG (8-bit)** - Format JPEG

8-bit yang dimampatkan
* **Lalai**: TIFF

(16-bit); projek sepenuhnya 8-bit akan memaparkan TIFF

(8-bit), dan hanya menawarkan format 8-bit
* **Penerangan**: Memilih format fail untuk menyimpan imej yang diproses dan dikalibrasi. Mengeksport data ke dalam subfolder mengikut format di dalam folder setiap kamera (`tiff16`, `tiff32`, `png8`, `jpg8`), dengan satu folder `<Product>_Images/` bagi setiap produk. Fail yang dieksport mengekalkan nama fail asal — folder, bukan sambungan nama fail, mengenal pasti produk.
* **Cadangan format**:
  * **TIFF

(16-bit)**: Disyorkan untuk analisis saintifik dan aliran kerja profesional. Memelihara kualiti data maksimum tanpa sebarang artifak pemampatan. Terbaik untuk analisis multispektral dan pemprosesan lanjut dalam perisian GIS.
  * **TIFF

(32-bit, Peratusan)**: Terbaik untuk aliran kerja yang memerlukan nilai pantulan sebagai peratusan (0-100%). Menawarkan ketepatan maksimum untuk pengukuran radiometrik.
  * **PNG

(8-bit)**: Baik untuk tontonan web dan visualisasi umum. Saiz fail yang lebih kecil dengan pemampatan tanpa kehilangan data, tetapi julat dinamik yang dikurangkan.
  * **JPG (8-bit)**: Saiz fail terkecil, terbaik untuk pratonton dan paparan web sahaja. Menggunakan pemampatan lossy yang tidak sesuai untuk analisis saintifik.
* **Nota**: Radiasi LATTICE sentiasa dieksport sebagai TIFF

32-bit float tanpa mengira tetapan ini.

***

## Simpan Templat Projek

Ciri ini membolehkan anda menyimpan tetapan projek semasa anda sebagai templat yang boleh digunakan semula.

* **Jenis**: Masukan teks + butang Simpan
* **Keterangan**: Masukkan nama yang menerangkan untuk templat tetapan anda dan klik ikon simpan. Templat tersebut akan menyimpan semua tetapan projek semasa anda (pengesanan sasaran, pilihan pemprosesan, indeks, dan format eksport) untuk kegunaan semula yang mudah dalam projek akan datang. Templat disimpan dalam folder `Project Templates/` di dalam folder simpanan projek anda, dan juga boleh dipilih atau dieksport dari menu utama (*Pilih Templat* / *Simpan Templat* / *Eksport Templat*).
* **Kes penggunaan**:
  * Buat templat untuk sistem kamera yang berbeza (RGB

, multispektral, NIR

)
  * Simpan konfigurasi piawai untuk jenis tanaman tertentu atau aliran kerja analisis
  * Kongsi tetapan yang konsisten dalam kalangan pasukan
* **Cara penggunaan**:
  1. Konfigurasikan semua tetapan projek yang anda inginkan
  2. Masukkan nama templat (contohnya, &quot;RedEdge

Survey3

NDVI

Standard&quot;)
  3. Klik ikon simpan
  4. Templat tersebut kini boleh dimuat apabila mencipta projek baru

***

## Folder Simpan Projek

Penyetelan ini menentukan lokasi lalai untuk menyimpan projek baru.

* **Jenis**: Paparan laluan direktori + Butang Sunting
* **Lalai (Windows

)**: `Documents\MAPIR\Chloros Projects` dalam folder Dokumen anda — biasanya `C:\Users\[Username]\Documents\MAPIR\Chloros Projects`, atau di mana sahaja OneDrive atau profil merayap telah memindahkan Dokumen
* **Lalai (Linux

)**: `~/Documents/MAPIR/Chloros Projects`
* **Keterangan**: Menunjukkan direktori lalai semasa di mana projek Chloros

baru dibuat. Klik ikon suntingan untuk memilih direktori yang berbeza. Penggantian disimpan sebagai satu baris teks dalam `~/.chloros/working_directory.txt` — pada Windows

ia adalah `C:\Users\<Username>\.chloros\working_directory.txt`. Jika fail itu hilang, atau menamakan laluan yang tidak wujud lagi, Chloros

akan kembali kepada lalai di atas. CLI

membaca dan menulis fail yang sama, jadi `chloros-cli` dan GUI sentiasa bersetuju tentang lokasi projek.
* **Templat Projek** terletak dalam subfolder `Project Templates/` dalam direktori ini.
* **Bilakah hendak menukar**:
  * Tetapkan ke pemacu rangkaian untuk kerjasama pasukan
  * Tukar ke pemacu dengan ruang simpanan yang lebih besar untuk set data besar
  * Atur projek mengikut tahun, klien, atau jenis projek dalam folder yang berbeza
* **Nota**: Menukar tetapan ini hanya menjejaskan projek BARU. Projek sedia ada kekal di lokasi asal mereka.
* **Panjang laluan Windows**: melainkan laluan panjang Windows

diaktifkan (pilihan **Aktifkan laluan panjang Windows** pada pemasang), folder projek yang lebih dalam daripada kira-kira 130 aksara akan ditolak sebelum diproses, kerana hasilnya akan melebihi had laluan 260 aksara Windows

. Lihat [Memulakan Pemprosesan](../processing-images-gui/starting-the-processing.md#the-project-folder-is-too-deep-for-this-computer).

***

## Kekekalan Tetapan

Projek Chloros

ialah satu **folder**. Semua tetapan projek disimpan dalam `project.json` di dalamnya; peranti keras yang disambungkan diingati bersamanya dalam `cameras.json` dan `sensors.json`, jadi membuka semula projek juga menyambung semula kameranya dan penderia cahaya. Apabila anda membuka semula projek, semua tetapan dipulihkan dengan tepat seperti yang anda tinggalkan. Projek yang disimpan juga boleh dikendalikan secara headless dengan `chloros-cli project` atau `open_project` daripada SDK

.

### Hierarki Tetapan

Tetapan digunakan mengikut susunan berikut:

1. **Laras lalai sistem** - Laras lalai terbina dalam yang ditakrifkan oleh Chloros

2. **Tetapan templat** - Jika anda memuatkan templat semasa mencipta projek
3. **Tetapan projek yang disimpan** - Tetapan yang disimpan bersama fail projek
4. **Penyesuaian manual** - Sebarang perubahan yang anda buat semasa sesi semasa

### Tetapan dan Pemprosesan Imej

Tetapan pemprosesan dibaca apabila pelaksanaan pemprosesan bermula. Mengubah tetapan tidak akan mengubah secara retrospektif produk yang sudah ada di cakera — jalankan pemprosesan semula untuk menerapkan tetapan baru. Beberapa tetapan langsung tidak menjejaskan pemprosesan:

* Resolusi Miniatur Imej (pameran sahaja)
* Simpan Templat Projek
* Simpan Folder Projek

***

## Rujukan kunci konfigurasi

Untuk automasi (CLI

`--config`, SDK

`configure`, atau membaca `project.json` secara langsung), ini adalah kunci tepat di bawah `Project Settings`:

| Laluan kunci | Jenis | Lalai |
| --- | --- | --- |
| `Display → Image Thumbnail Resolution` | `"512" \| "1024" \| "2048" \| "full"` | `"512"` |
| `Target Detection → Minimum calibration sample area (px)` | nombor 0-10000 | `25` |
| `Target Detection → Minimum Target Clustering (0-100)` | nombor 0-100 | `60` |
| `Processing → Vignette correction` | bool | `true` |
| `Processing → Reflectance calibration / white balance` | bool | `true` |
| `Processing → Export sensor response` | bool | `true` |
| `Processing → Export vignette corrected` | bool | `true` |
| `Processing → Export debayered` | bool | `true` |
| `Processing → Export preview` | bool | `true` |
| `Processing → Export radiance` | bool | `true` |
| `Processing → Export reflectance` | bool | `true` |
| `Processing → Array alignment` | bool | `true` |
| `Processing → Array alignment crop` | bool | `true` |
| `Processing → Array alignment interpolation` | `"Bilinear" \| "Nearest" \| "Cubic"` | `"Bilinear"` |
| `Processing → Debayer method` | `"Standard (Fast, Medium Quality)" \| "Texture Aware (Slow, Highest Quality)"` | Standard |
| `Processing → Minimum recalibration interval` | nombor 0-3600 | `0` |
| `Processing → Light sensor timezone offset` | nombor -12..12 | `0` |
| `Processing → Apply PPK corrections` | bool | `false` |
| `Processing → DAQ cap id` | id profil cap atau `"auto"` | `"auto"` |
| `Processing → Target reflectance source` | `"auto" \| "target" \| "daq"` | `"auto"` |
| `Index → Add index` | senarai konfigurasi indeks | `[]` |
| `Export → Calibrated image format` | `"TIFF (16-bit)" \| "TIFF (8-bit)" \| "TIFF (32-bit, Percent)" \| "PNG (8-bit)" \| "JPG (8-bit)"` | `"TIFF (16-bit)"` |

Kunci `Array alignment` ditulis buat pertama kali apabila seksyen Penyelarasan Susunan dipaparkan atau panggilan automasi menetapkan mereka. Sementara mereka tiada, saluran menggunakan nilai yang sama seperti yang ditunjukkan di atas (`true`, `true`, bilinear), jadi project.json tanpa mereka berkelakuan sama seperti yang mempunyai mereka.

### Kunci yang disimpan dalam `project.json` tanpa kawalan dalam panel tetapan

Kunci-kunci ini disimpan di bawah pokok `Project Settings` yang sama dan dibaca oleh pemprosesan, tetapi anda tidak akan menemui widget untuknya di bar sisi:

| Laluan kunci | Jenis | Lalai | Ditetapkan oleh |
| --- | --- | --- | --- |
| `Processing → LATTICE input level` | `"auto" \| "raw" \| "debayered" \| "processed"` | `"auto"` | `chloros-cli process --input-level`, SDK

`input_level=`. Mengatasi cara TIFF

input LATTICE ditafsir; `auto` menganggar daripada tag XMP `Chloros:ProcessingLevel` setiap fail serta bilangan saluran. Diabaikan untuk tangkapan `.raw` Survey3

. Disengajakan bukan tetapan GUI — auto adalah betul dalam setiap kes biasa. |
| `Processing → Target reflectance dir` | rentetan laluan | `""` | `chloros-cli process --target-reflectance-dir`, atau sasaran projek API

|
| `Processing → Target reflectance config` | kamus berasaskan siri kamera | `{}` | Mendaftar sasaran dalam bingkai (mod `fixed_block` / `fixed_strip` / `aruco`) |
| `Processing → DAQ-U log path` | rentetan laluan | `""` | SDK

`process_folder(daq_log_path=…)`. Menunjuk ke arah rakaman `.daq` atau folder yang mengandungi rakaman tersebut |
| `Target Detection → Minimum calibration target squares` | nombor | `4` | Lalai lama; tiada kawalan dan tiada bendera CLI

|
| `UI → Grid thumbnail size` | nombor | `160` | Gelangsar zum gambar kecil imej itu sendiri |

Dua keutamaan pemapar disimpan **di peringkat teratas dalam `project.json`**, di luar `Project Settings` sepenuhnya, kerana ia adalah keadaan paparan dan bukannya tetapan pemprosesan:

| Laluan kunci | Jenis | Lalai | Ditetapkan oleh |
| --- | --- | --- | --- |
| `viewer_display → gsd_bin` | integer 1–256 | `1` | Kawalan GSD (px) tab imej — lihat [Membuka Imej Penuh Skrin](../image-viewer-gui/opening-an-image-full-screen.md) |

***

## Amalan Terbaik

1. **Mulakan dengan tetapan lalai**: Tetapan lalai berfungsi dengan baik untuk kebanyakan sistem kamera MAPIR

dan aliran kerja tipikal.
2. **Buat templat**: Setelah anda mengoptimumkan tetapan untuk aliran kerja atau kamera tertentu, simpanlah ia sebagai templat untuk memastikan keseragaman merentas projek.
3. **Uji sebelum pemprosesan penuh**: Apabila bereksperimen dengan tetapan baru, uji pada sebilangan kecil imej sebelum memproses keseluruhan set data anda.
4. **Dokumenkan tetapan anda**: Gunakan nama templat yang menerangkan sistem kamera, jenis pemprosesan, dan kegunaan yang dimaksudkan (contohnya, &quot;Survey3

_RGB_NDVI_Pertanian&quot;).
5. **Pilihan format eksport**: Pilih format eksport anda berdasarkan kegunaan akhir anda:
   * Analisis saintifik → TIFF (16-bit atau 32-bit)
   * Pemprosesan GIS → TIFF

(16-bit)
   * Visualisasi pantas → PNG

(8-bit)
   * Perkongsian web → JPG (8-bit)

***

Untuk maklumat lanjut mengenai indeks multispektral dalam Chloros

, lihat halaman [Formula Indeks Multispektral](multispectral-index-formulas.md).
