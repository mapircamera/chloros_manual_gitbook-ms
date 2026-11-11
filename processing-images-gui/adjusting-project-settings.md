# Mengatur Tetapan Projek

Sebelum memproses imej anda, adalah penting untuk mengkonfigurasi tetapan projek anda agar sesuai dengan keperluan aliran kerja anda. Panel Tetapan Projek <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> menyediakan kawalan menyeluruh ke atas penentukuran, pilihan pemprosesan, indeks multispektral, dan format eksport.

## Mengakses Tetapan Projek

1. Buka projek anda dalam Chloros
2. Klik ikon **Project Settings** <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> di bar sisi kiri
3. Panel Tetapan Projek memaparkan semua pilihan konfigurasi

<figure><img src="../.gitbook/assets/image (28).png" alt=""><figcaption><p>Panel Tetapan Projek — Paparan, Pengesanan Sasaran dan Pemprosesan</p></figcaption></figure>

{% hint style="info" %}
**Tetapan disimpan secara automatik** bersama projek anda. Apabila anda membuka semula projek, semua tetapan akan dipulihkan.
{% endhint %}

***

## Persediaan Cepat untuk Aliran Kerja Biasa

### Tetapan Lalai (Disyorkan untuk Kebanyakan Pengguna)

Tetapan lalai berfungsi dengan baik untuk aliran kerja **Survey3** dan LATTICE tipikal:

* ✅ **Pembetulan Vignette**: Diaktifkan
* ✅ **Kalibrasi pantulan / imbangan putih**: Diaktifkan (menggunakan sasaran MAPIR dan/atau data penderia cahaya DAQ)
* ✅ **Kaedah Debayer**: Standard (Pantas, Kualiti Sederhana)
* ✅ **Format Eksport**: TIFF (16-bit) (TIFF (8-bit) untuk projek sepenuhnya 8-bit)
* ✅ **Semua produk eksport**: Diaktifkan (LATTICE menangkap fan out ke dalam debayered, pratonton, radiance, dan reflectance secara automatik)

Cuma import imej anda dan mula memproses dengan tetapan lalai ini.

***

## Gambaran Keseluruhan Tetapan Projek

Panel Tetapan Projek disusun ke dalam seksyen di bawah. Dua seksyen tambahan — **Penderia Cahaya DAQ**dan**Penjajaran Susunan** — akan muncul secara automatik apabila projek anda mengandungi fail yang berkaitan. Untuk dokumentasi lengkap, lihat [Project Settings](../project-settings/project-settings.md).

### Paparan

* **Resolusi Miniatur Imej**: Resolusi miniatur grid-imej. Pilihan:**Laras (512 px)**,**1024 px**,**2048 px**,**Resolusi penuh**. Paparan sahaja — tidak pernah menjejaskan pemprosesan. Nilai yang lebih tinggi kelihatan lebih tajam apabila diperbesarkan tetapi memuat lebih perlahan.

### Pengesanan Sasaran

Mengawal cara Chloros mengenal pasti sasaran penentukuran dalam imej anda.

**Tetapan utama:*** **Keluasan sampel penentukuran minimum (px)**: Ambang saiz untuk pengesanan sasaran (lalai:**25**, julat 0–10000)
* **Pengelompokan Sasaran Minimum (0-100)**: Ambang keserupaan untuk pengelompokan kawasan sasaran (lalai:**60**)**Bilakah hendak melaraskan:**

*   Tingkatkan kawasan sampel jika terdapat pengesanan palsu
*   Kurangkan jika sasaran tidak dikesan
*   Laraskan pengklusteran jika sasaran dipecahkan kepada beberapa pengesanan

{% hint style="info" %}
Pilihan ini diwarnakan kelabu apabila **Penentukuran Reflektan / Imbangan Putih** dilumpuhkan — jika dilumpuhkan, pengesanan sasaran langsung tidak akan dijalankan.
{% endhint %}

### Pemprosesan

Pilihan utama pemprosesan imej dan penentukuran.

**Pilihan utama:*** **Pembetulan vignette**: Mengimbangi penggelapan lensa di tepi ✅ Disyorkan
* **Kalibrasi pantulan / imbangan putih**: Mengkalibrasi imej menggunakan sasaran yang dikesan (Survey3) dan/atau data penderia cahaya DAQ (LATTICE) ✅ Disyorkan
* **Kaedah Debayer**: Algoritma untuk menukar RAW kepada multispektral 3-saluran
* **Jarak semakan semula minimum**: Masa minimum dalam saat antara penggunaan sasaran penentukuran (lalai:**0** = gunakan semua, julat 0–3600)**Produk sandaran tanpa penentukuran:**Apabila satu bingkai tidak dapat ditentukur reflektans (tiada sasaran tersedia, atau penentukuran dilumpuhkan), ia dieksport sebagai salah satu daripada dua produk sandaran —**hanya satu daripada pasangan itu wujud bagi setiap larian**, dipilih oleh suis pembetulan Vignette:

* **Eksport tindak balas sensor**: menulis `Sensor_Response_Images` — digunakan apabila pembetulan Vignette**dimatikan*** **Eksport pembetulan vignet**: menulis `Vignette_Corrected_Images` — digunakan apabila pembetulan Vignette di**aktifkan**Petak semak yang tidak aktif akan diwarnakan kelabu. Menyahsemak petak aktif akan menyebabkan fail tersebut langsung tidak ditulis.**Produk eksport LATTICE** (ditunjukkan untuk setiap projek; ia terpakai pada tangkapan LATTICE):

* **Eksport debayered**: imej debayered linear (`Debayered_Images`): offset gelap pek kalibrasi dialihkan, kemudian demosaic, tanpa flat field atau unmix — fail yang sama yang disimpan oleh capture pada tahap debayerednya — dalam orientasi asal sensor. Terpakai untuk modul RGB dan multispektral.
* **Previu Eksport**: previun paparan (`Preview_Images`). RGB = imbangan putih (DAQ-illuminant apabila tersedia, jika tidak dunia-kelabu) + gamma; multispektral = regangan warna palsu.
* **Eksport radiasi**: float32 radiasi spektral (`Radiance_Images`, W/m²/sr/nm). Modul multispektral sahaja — tidak terpakai untuk induk RGB.
* ****Eksport pantulan**: uint16 pantulan (`Reflectance_Calibrated_Images`, DN 32768 = ρ 1.0) apabila bacaan `.daq` ke bawah atau sasaran dalam bingkai menutupi bingkai. Modul multispektral sahaja.

Kesemuanya **diaktifkan secara lalai**— satu bingkai mentah LATTICE yang diimport disalurkan ke setiap produk yang diaktifkan dan terpakai dalam satu pusingan pemprosesan. Petak semak**Eksport pantulan** akan diwarnakan kelabu apabila Kalibrasi pantulan dimatikan. Seting yang dimatikan oleh suis induk akan sentiasa dipaparkan dalam warna kelabu dengan petua alat yang menamakan suis yang perlu diubah.**Seting lanjutan:*** **Perbezaan zon waktu penderia cahaya**: Jam dari UTC untuk padanan zon waktu penderia cahaya (lalai: 0, julat −12 hingga +12)
* **Terapkan pembetulan PPK**: Menggunakan data GPS/pin pendedahan daripada fail `.daq` (lalai: tidak diaktifkan)
* **Pin Pendedahan 1/2**: Menugaskan kamera kepada pin pendedahan untuk susunan dwi-kamera

{% hint style="info" %}
**Tahap input LATTICE adalah automatik.** Rakaman LATTICE membawa tahap pemprosesan mereka dalam metadata XMP, dan pemprosesan sentiasa memasuki aliran kerja pada bingkai mentah — tiada apa-apa yang perlu dikonfigurasikan dalam GUI. (Penanda `--input-level` CLI wujud sebagai keutamaan pengguna lanjutan untuk rakaman yang kehilangan metadata; lihat [Rujukan CLI](../reference/cli-reference.md).)
{% endhint %}

### Kaedah Debayer

Kami kini menawarkan 2 kaedah debayer dalam Chloros:

#### Standard (Pantas, Kualiti Sederhana)

Debayer Standard memproses dengan pantas tetapi menunjukkan hingar warna debayer, menghasilkan imej yang kurang tepat dan lebih berhingar.

#### Sedar Tekstur (Lambat, Kualiti Tertinggi) \[Hanya Chloros+]

Sedar Tekstur menggunakan debayer sedar-tepi berkualiti tinggi yang digabungkan dengan model penyah-gangguan AI/ML yang membuang hampir semua bunyi debayering. Model ini memerlukan memori GPU (VRAM) untuk dijalankan: dengan **7 GB VRAM atau lebih** ia boleh memproses berbilang imej serentak; di bawah 7 GB ia menjalankan satu imej pada satu masa (jelas lebih perlahan). Lihat [Penyesuaian Komputasi Dinamik](../processing-architecture/dynamic-compute-adaptation.md).

{% hint style="info" %}
**Penangkapan LATTICE sentiasa menggunakan demosaik Standard.** Tiada model Texture Aware yang dilatih untuk LATTICE, jadi pilihan ini tidak ditawarkan untuk imej LATTICE — imej Survey3 dalam projek yang sama masih boleh menggunakannya.
{% endhint %}

### Indeks (Indeks Multispektral)

Konfigurasikan indeks vegetasi yang ingin dikira dan dieksport. Tetingkap lungsur GUI menawarkan **27 formula indeks terbina dalam**.**Cara menambah indeks:**

1. Klik butang**"Tambah indeks"**

2. Pilih indeks daripada menu lungsur (NDVI, NDRE, GNDVI, dan lain-lain)
3. Konfigurasikan tetapan visualisasi (warna LUT, julat nilai)
4. Tambah beberapa indeks mengikut keperluan

**Indeks Popular:*** **NDVI**: Kesihatan vegetasi umum (yang paling biasa)
* **NDRE**: Pengesanan tekanan awal dengan RedEdge
* **GNDVI**: Sensitif terhadap kepekatan klorofil
* **OSAVI**: Berfungsi dengan baik pada tanah yang kelihatan
* **EVI**: Kawasan indeks daun tinggi (LAI)

**Formula tersuai:**

* Buat formula indeks multispektral tersuai dengan matematik jalur merentasi semua saluran imej
* Simpan formula tersuai untuk digunakan semula
* Formula tersuai adalah ciri Chloros+; ketersediaannya bergantung pada peringkat pelan anda

Untuk semua indeks dan formula yang tersedia — termasuk nama yang hanya untuk GUI dan yang juga berfungsi dalam CLI/SDK — lihat [Multispectral Index Formulas](../project-settings/multispectral-index-formulas.md).

### Eksport

Mengawal format fail output.

**Format yang tersedia**(tetapan:**Format imej yang dikalibrasi**, lalai**TIFF (16-bit)**):

* **TIFF (16-bit)**: Disyorkan untuk GIS dan analisis saintifik
* **TIFF (8-bit)**: TIFF 8-bit* **TIFF (32-bit, Peratus)**: Nilai titik apung
* **PNG (8-bit)**: Pemampatan tanpa kehilangan untuk visualisasi
* **JPG (8-bit)**: Fail terkecil, pemampatan berkehilangan

Senarai ini menunjukkan apa yang boleh disimpan oleh imej projek: Chloros tidak menaik taraf kedalaman bit. Semua imej 8-bit hanya menawarkan TIFF (8-bit), PNG (8-bit) dan JPG (8-bit), dan secara lalai menggunakan TIFF (8-bit). Dalam projek campuran, setiap tangkapan dieksport pada kedalaman sendiri atau lebih rendah. Radiance kekal 32-bit float.

Keluaran ditulis di bawah folder projek, dikumpulkan mengikut kamera dan format: `<project>/<camera>/<format>/<Product>_Images/`. Radiance **sentiasa** ditulis sebagai float32 ke dalam folder `tiff32` tanpa mengira tetapan ini. Fail yang dieksport mengekalkan nama fail asal — folder mengenal pasti produk. Eksport multispektral LATTICE juga boleh menulis topeng clamp setiap piksel ke dalam folder `clamp_masks/` di sebelah folder produk. Lihat [Menyiapkan Pemprosesan](finishing-the-processing.md) untuk keseluruhan struktur output.

{% hint style="warning" %}
**Membaca nilai pantulan**: DN yang bermaksud ρ = 1.0 bergantung pada kamera sumber — LATTICE menggunakan 32768 (dicop sebagai XMP `Chloros:PixelScale`), Survey3 menggunakan 65535. Baca tag tersebut daripada menganggap ia adalah pemalar. Lihat [Format Imej Keluaran](../output-image-formats.md).
{% endhint %}

### Penderia Cahaya DAQ

Bahagian ini menyenaraikan setiap fail DAQ downwelling (`.daq` / `.csv`) dalam projek anda, satu baris bagi setiap fail, memaparkan model sensor, nama fail, dan **cap** diffuser pembetulan yang digunakan untuk fail tersebut.

* **Tindasan topi (semua fail)**: satu menu lungsur untuk seluruh projek.**Auto** (lalai) menggunakan cap yang dirakam dalam setiap fail — cahaya matahari diandaikan apabila tiada apa-apa yang dirakam, tetapi DAQ-U, DAQ-M dan DAQ-E dihantar tanpa cap dan Chloros tidak dapat melihat apa yang dipasang, jadi pemprosesan memberi amaran tentang cap yang diandaikan: pilih cap yang telah dipasang. Memilih cap akan menimpa setiap fail kecuali DAQ-E-S, yang penyebarnya adalah sebahagian daripada penentukuran: rakaman mentah diperbetulkan dengannya, dan rakaman yang sudah mempunyai cap akan dirujuk semula (pembetulan yang dirakam dibatalkan, dan cap yang dipilih digunakan).
* Baris memberi amaran apabila topi yang dirakam adalah lalai yang diandaikan oleh hab dan bukannya disahkan oleh pengendali, dan apabila topi yang dipilih tiada profil untuk model peranti tersebut (pengecualian ditolak untuk fail tersebut).

Rakaman DAQ yang dibuat dalam tab Light Sensors ditambah ke projek terbuka secara automatik, dan fail `.daq` / `.csv` yang diimport akan muncul di sini sebaik sahaja ia ditambah.

<figure><img src="../.gitbook/assets/image (32).png" alt=""><figcaption><p>Tetapan Projek Rendah — Indeks, format Eksport, bahagian Penderia Cahaya DAQ, dan kawalan templat/folder projek</p></figcaption></figure>### Penyelarasan Susunan

Bahagian ini muncul **hanya** apabila sekurang-kurangnya satu imej dalam projek mengandungi transformasi penyelarasan modul-ke-modul yang dicop oleh susunan LATTICE semasa pengambilan (`Chloros:Alignment*` XMP). Ia menunjukkan berapa banyak imej mempunyai tag dan kamera mana yang menjadi rujukan, dengan kawalan berikut:

* **Terapkan penjajaran tatasusunan** (lalai: diaktifkan): memutarbelitkan setiap produk yang diproses (debayered / pratonton / sinaran / pantulan / indeks) ke dalam geometri rujukan bersama tatasusunan. Tidak diaktifkan = mengeksport dalam geometri sensor asal.
* **Pot ke tumpang tindih biasa** (lalai: aktif): memotong eksport yang selari ke kawasan yang dikongsi semua modul, supaya setiap jalur mempunyai jejak yang sama. Matikan mengekalkan kanvas penderia penuh (isi hitam di luar sumber).
* **Pengambilan Semula**:**Bilinear (halus, lalai)**,**Ter dekat (pelihara nilai tepat)**— tiada pencampuran antara piksel, untuk analisis radiometrik yang ketat — atau**Kubik (paling tajam)**.***

## Menyimpan dan Memuat Tetapan

### Simpan Templat Projek

Buat templat yang boleh digunakan semula untuk aliran kerja yang konsisten:

1. Konfigurasikan semua tetapan yang diingini dalam panel Tetapan Projek
2. Tatal ke bahagian **"Simpan Templat Projek"** di bahagian bawah
3. Masukkan nama templat yang menerangkan (contohnya, "Survey3N_RGN_Agriculture")
4. Klik ikon simpan

**Manfaat:**

* Terapkan tetapan yang sama merentas pelbagai projek
* Kongsi konfigurasi dengan ahli pasukan
* Kekalkan konsistensi untuk tinjauan berulang

### Memuatkan Templat pada Projek Baru

Apabila membuat projek baru:

1. Pilih **"Projek Baru"** daripada menu utama
2. Pilih templat projek dalam pemilih templat pilihan
3. Semua tetapan daripada templat akan digunakan secara automatik

### Direktori Kerja

Penyetelan **"Direktori Kerja"** menentukan di mana projek baharu dibuat secara lalai:

* **Lokasi lalai**: `Documents\MAPIR\Chloros Projects` dalam folder Dokumen Windows anda (biasanya `C:\Users\[Username]\Documents\MAPIR\Chloros Projects`; ia mengikuti folder Dokumen jika OneDrive atau profil merayau memindahkannya)
* **Ubah lokasi**: Klik ikon sunting dan pilih folder baru
* **Dikongsi dengan CLI**: `chloros-cli` menggunakan tetapan folder projek lalai yang sama
* **Pendekkan nama di Windows**: melainkan laluan panjang Windows diaktifkan, folder projek yang lebih dalam daripada kira-kira 130 aksara akan ditolak sebelum diproses — lihat [Memulakan Pemprosesan](starting-the-processing.md#the-project-folder-is-too-deep-for-this-computer)
* **Bilakah hendak menukar**:
  * Pemandu rangkaian untuk kerjasama pasukan
  * Pemandu berasingan dengan lebih banyak ruang simpanan
  * Struktur folder yang teratur mengikut tahun/klien

***

## Penyediaan PPK (Kinematik Pascaproses)

Jika menggunakan perekod DAQ MAPIR dengan GPS untuk geolokasi tepat:

### Prasyarat

* DAQ MAPIR dengan modul GPS (GNSS)
* Fail log .daq dengan entri pin pendedahan
* Kamera yang disambungkan ke pin pendedahan DAQ semasa sesi rakaman

### Langkah-langkah Konfigurasi

1. Letakkan fail log .daq dalam folder projek anda
2. Dalam Tetapan Projek, aktifkan kotak semak **"Terapkan pembetulan PPK"**

3. Tetapkan**"Light sensor timezone offset"** jika perlu (lalai: 0 untuk UTC)
4. Tugaskan kamera kepada pin pendedahan:
   * **Satu kamera**: Ditugaskan secara automatik kepada Pin 1
   * **Dua kamera**: Tugaskan setiap kamera secara manual kepada pin yang betul**Penugasan Pin Pendedahan:*** **Pin Pendedahan 1**: Pilih model kamera daripada senarai lungsur
* **Pin Pendedahan 2**: Pilih kamera kedua atau "Jangan Gunakan"
* Kamera yang sama tidak boleh ditugaskan kepada kedua-dua pin

{% hint style="warning" %}
**Penting**: Pin pendedahan mesti ditetapkan dengan betul kepada kamera masing-masing. Penetapan yang salah akan mengakibatkan data geolokasi yang tidak tepat.
{% endhint %}

***

## Senario Lanjutan

### Projek Pelbagai Kamera

Apabila memproses imej daripada pelbagai kamera MAPIR dalam satu projek:

1. Chloros secara automatik mengesan setiap model kamera (sama ada Survey3 dan LATTICE)
2. Setiap kamera mendapat profil pemprosesan yang sesuai, dan setiap kamera mendapat struktur folder keluaran tersendiri
3. PPK: Tentukan secara manual setiap kamera Survey3 kepada pin pendedahan yang betul
4. Semua kamera menggunakan format eksport dan indeks yang sama

**Contoh**: rig dwi-kamera Survey3WRGN

+ Survey3NOCN, atau susunan LATTICE yang menggabungkan kamera induk RGB dengan modul jalur sempit

### Tinjauan Lajur Masa atau Tarikh Pelbagai

Untuk tinjauan berulang di kawasan yang sama dari masa ke masa:

1. Buat templat dengan tetapan standard anda
2. Gunakan susunan sasaran penentukuran yang konsisten setiap sesi
3. Proses setiap tarikh sebagai projek berasingan
4. Gunakan tetapan yang sama untuk keputusan yang boleh dibandingkan
5. Eksport dalam format yang sama untuk analisis temporal

### Set Data Besar

Untuk projek dengan banyak imej (500+):

* Pertimbangkan untuk membahagikan kepada projek yang lebih kecil mengikut tarikh atau kawasan
* Gunakan pemprosesan selari Chloros+ untuk keputusan yang lebih pantas
* Pertimbangkan CLI atau API untuk automasi pukal
* Laraskan selang penalaan semula minimum untuk mengurangkan masa pengesanan sasaran

***

## Mengesahkan Tetapan Anda

Sebelum memulakan pemprosesan, semak tetapan utama ini:

* [ ] Model kamera dikesan dengan betul dalam Pelayar Fail
* [ ] Pembetulan vignet diaktifkan
* [ ] Kalibrasi reflektansi diaktifkan
* [ ] Untuk Survey3: sekurang-kurangnya satu imej sasaran kalibrasi diimport dan diperiksa; untuk LATTICE: satu sasaran dan/atau rakaman `.daq` downwelling wujud
* [ ] Indeks multispektral yang diingini telah ditambah
* [ ] Format eksport sesuai untuk aliran kerja anda
* [ ] Tetapan PPK telah dikonfigurasikan (jika menggunakan .daq dengan acara pendedahan)

***

## Langkah Seterusnya

Setelah tetapan anda dikonfigurasikan:

1. **Tandakan imej sasaran penentukuran** - Lihat [Memilih Imej Sasaran](choosing-target-images.md)
2. **Mulakan pemprosesan** - Lihat [Memulakan Pemprosesan](starting-the-processing.md)
3. **Pantau kemajuan** - Lihat [Memantau Pemprosesan](monitoring-the-processing.md)

Untuk butiran lengkap mengenai semua tetapan yang tersedia, lihat dokumentasi rujukan [Tetapan Projek](../project-settings/project-settings.md).
