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

## Tetapan Pantas untuk Aliran Kerja Biasa

### Tetapan Lalai (Disyorkan untuk Kebanyakan Pengguna)

Tetapan lalai berfungsi dengan baik untuk aliran kerjaSurvey3
dan LATTICE tipikal:

* ✅ **Pembetulan Vignette**: Diaktifkan
* ✅ **Kalibrasi pantulan / imbangan putih**: Diaktifkan (menggunakan sasaranMAPIR
dan/atau data penderia cahaya DAQ)
* ✅ **Kaedah Debayer**: Standard (Pantas, Kualiti Sederhana)
* ✅ **Format eksport**:TIFF
(16-bit)
* ✅ **Semua produk eksport**: Diaktifkan (LATTICE secara automatik menangkap fan out ke dalam debayered, pratonton, radiance, dan reflectance)

Cukup import imej anda dan mulakan pemprosesan dengan tetapan lalai ini.

***

## Gambaran Keseluruhan Tetapan Projek

Panel Tetapan Projek disusun ke dalam seksyen di bawah. Dua seksyen tambahan — **Penderia Cahaya DAQ**dan**Penjajaran Susunan** — akan muncul secara automatik apabila projek anda mengandungi fail yang berkaitan. Untuk dokumentasi lengkap, lihat [Tetapan Projek](../project-settings/project-settings.md).

### Paparan

* **Resolusi Miniatur Imej**: Resolusi miniatur grid-imej. Pilihan:**Laras lalai (512 px)**,**1024 px**,**2048 px**,**Resolusi penuh**. Hanya untuk paparan — tidak pernah menjejaskan pemprosesan. Nilai yang lebih tinggi kelihatan lebih tajam apabila diperbesarkan tetapi memuat lebih perlahan.

### Pengesanan Sasaran

Mengawal caraChloros
mengenal pasti sasaran penentukuran dalam imej anda.

**Pilihan utama:*** **Keluasan sampel penentukuran minimum (px)**: Ambang saiz untuk pengesanan sasaran (lalai:**25**, julat 0–10000)
* **Pengelompokan Sasaran Minimum (0-100)**: Ambang keserupaan untuk pengelompokan kawasan sasaran (lalai:**60**)**Bilakah hendak melaraskan:**

*   Tingkatkan kawasan sampel jika terdapat pengesanan palsu
*   Kurangkan jika sasaran tidak dikesan
*   Laraskan pengklusteran jika sasaran dipecahkan kepada beberapa pengesanan

{% hint style="info" %}
Pilihan ini diwarnakan kelabu apabila **Penentukuran pantulan / imbangan putih** dilumpuhkan — jika dilumpuhkan, pengesanan sasaran langsung tidak akan dijalankan.
{% endhint %}

### Pemprosesan

Pilihan utama pemprosesan imej dan penentukuran.

**Pilihan utama:*** **Pembetulan vignet**: Mengimbangi penggelapan lensa di tepi ✅ Disyorkan
* **Kalibrasi reflektansi / imbangan putih**: Mengkalibrasi imej menggunakan sasaran yang dikesan (Survey3
) dan/atau data penderia cahaya DAQ (LATTICE) ✅ Disyorkan
* **Kaedah Debayer**: Algoritma untuk menukar RAW kepada multispektral 3-saluran
* **Jarak antara penentukuran semula minimum**: Masa minimum dalam saat antara penggunaan sasaran penentukuran (laluan:**0** = gunakan semua, julat 0–3600)**Produk sandaran tanpa penentukuran:**Apabila sesebuah bingkai tidak dapat dikalibrasi pantulan (tiada sasaran tersedia, atau penentukuran dilumpuhkan), ia akan dieksport sebagai salah satu daripada dua produk sandaran —**hanya satu daripada pasangan itu wujud bagi setiap pelaksanaan**, dipilih oleh suis pembetulan Vignette:

* **Eksport tindak balas sensor**: menulis `Sensor_Response_Images` — digunakan apabila pembetulan Vignette**dimatikan*** **Eksport pembetulan vignet**: menulis `Vignette_Corrected_Images` — digunakan apabila pembetulan Vignette di**aktifkan**Petak semak yang tidak aktif akan diwarnakan kelabu. Menyahsemak petak aktif akan menyebabkan fail tersebut langsung tidak ditulis.**Produk eksport LATTICE** (ditunjukkan untuk setiap projek; ia terpakai pada tangkapan LATTICE):

* **Eksport debayered**: imej linear debayered (`Debayered_Images`). Terpakai pada modulRGB
dan multispektral.
* **Previu Eksport**: pratonton paparan (`Preview_Images`).RGB
= imbangan putih (DAQ-illuminant apabila tersedia, jika tidak dunia-kelabu) + gamma; multispektral = regangan warna palsu.
* **Eksport radiasi**: float32 radiasi spektral (`Radiance_Images`, W/m²/sr/nm). Modul multispektral sahaja — tidak terpakai untuk indukRGB
.
* ****Eksport pantulan**: uint16 pantulan (`Reflectance_Calibrated_Images`, DN 32768 = ρ 1.0) apabila terdapat bacaan `.daq` ke bawah atau sasaran dalam bingkai yang menutupi bingkai. Modul multispektral sahaja.

Kesemuanya **diaktifkan secara lalai**— satu bingkai mentah LATTICE yang diimport disalurkan ke setiap produk yang diaktifkan dan berkenaan dalam satu langkah pemprosesan. Petak semak**Eksport pantulan** akan diwarnakan kelabu apabila Kalibrasi pantulan dimatikan. Seting yang dimatikan oleh suis induk akan sentiasa dipaparkan dalam warna kelabu dengan petua alat yang menyatakan nama suis yang perlu diubah.**Seting Lanjutan:*** **Perbezaan zon waktu penderia cahaya**: Jam dari UTC untuk padanan zon waktu penderia cahaya (lalai: 0, julat −12 hingga +12)
* **Terapkan pembetulan PPK**: Menggunakan data GPS/pin pendedahan daripada fail `.daq` (lalai: tidak diaktifkan)
* **Pin Pendedahan 1/2**: Menugaskan kamera kepada pin pendedahan untuk susunan dwi-kamera

{% hint style="info" %}
**Tahap input LATTICE adalah automatik.** Rakaman LATTICE membawa tahap pemprosesan mereka dalam metadata XMP, dan pemprosesan sentiasa memasuki aliran kerja pada bingkai mentah — tiada apa-apa untuk dikonfigurasikan dalam GUI. (PenandaCLI
`--input-level` wujud sebagai override untuk pengguna mahir bagi rakaman yang kehilangan metadata; lihat [RujukanCLI
](../reference/cli-reference.md).)
{% endhint %}

### Kaedah Debayer

Kami kini menawarkan 2 kaedah debayering dalamChloros
:

#### Standard (Pantas, Kualiti Sederhana)

Debayer Standard memproses dengan pantas tetapi menunjukkan hingar warna debayering, menghasilkan imej yang kurang tepat dan lebih berhingar.

#### Sedar Tekstur (Lambat, Kualiti Tertinggi) \[HanyaChloros
+]

Sedar Tekstur menggunakan debayer sedar-tepi berkualiti tinggi yang digabungkan dengan model penyahbisuan AI/ML yang membuang hampir semua bunyi debayering. Model ini memerlukan memori GPU (VRAM) untuk dijalankan: dengan **7 GB VRAM atau lebih** ia boleh memproses berbilang imej serentak; di bawah 7 GB ia menjalankan satu imej pada satu masa (lebih perlahan dengan ketara). Lihat [Penyesuaian Komputasi Dinamik](../processing-architecture/dynamic-compute-adaptation.md).

{% hint style="info" %}
**Penangkapan LATTICE sentiasa menggunakan demosaik Standard.** Tiada model Texture Aware yang dilatih untuk LATTICE, jadi pilihan ini tidak ditawarkan untuk imej LATTICE — imejSurvey3
dalam projek yang sama masih boleh menggunakannya.
{% endhint %}

### Indeks (Indeks Multispektral)

Konfigurasikan indeks vegetasi yang ingin dikira dan dieksport. Tetingkap lungsur GUI menawarkan **27 formula indeks terbina dalam**.**Cara menambah indeks:**

1. Klik butang**&quot;Tambah indeks&quot;**

2. Pilih indeks daripada menu lungsur (NDVI
,NDRE
,GNDVI
, dan lain-lain)
3. Konfigurasikan tetapan visualisasi (warna LUT, julat nilai)
4. Tambah beberapa indeks mengikut keperluan

**Indeks Popular:*** **NDVI**: Kesihatan vegetasi umum (yang paling biasa)
* **NDRE**: Pengesanan tekanan awal denganRedEdge
* **GNDVI**: Sensitif terhadap kepekatan klorofil
* **OSAVI**: Berfungsi dengan baik pada tanah yang kelihatan
* **EVI**: Kawasan dengan indeks kawasan daun tinggi (LAI
)

**Formula tersuai:**

* Buat formula indeks multispektral tersuai dengan matematik jalur merentasi semua saluran imej
* Simpan formula tersuai untuk digunakan semula
* Formula tersuai adalah ciriChloros
+; ketersediaannya bergantung pada peringkat pelan anda

Untuk semua indeks dan formula yang tersedia — termasuk nama yang hanya terdapat dalam GUI dan yang juga berfungsi dalamCLI
/SDK
— lihat [Formula Indeks Multispektral](../project-settings/multispectral-index-formulas.md).

### Eksport

Mengawal format fail output.

**Format yang tersedia**(tetapan:**Format imej dikalibrasi**, lalai**TIFF
(16-bit)**):

* **TIFF
(16-bit)**: Disyorkan untuk GIS dan analisis saintifik
* **TIFF
(32-bit, Peratus)**: Nilai titik apung
* **PNG
(8-bit)**: Pemampatan tanpa kehilangan untuk visualisasi
* **JPG (8-bit)**: Fail terkecil, pemampatan berkehilangan

Keluaran ditulis di bawah folder projek, dikelompokkan mengikut kamera dan format: `<project>/<camera>/<format>/<Product>_Images/`. Radiance **sentiasa** ditulis sebagai float32 ke dalam folder `tiff32` tanpa mengira tetapan ini. Fail yang dieksport mengekalkan nama fail sumber — folder mengenal pasti produk. Lihat [Menyiapkan Pemprosesan](finishing-the-processing.md) untuk keseluruhan struktur output.

{% hint style="warning" %}
**Membaca nilai pantulan**: DN yang bermaksud ρ = 1.0 bergantung pada kamera sumber — LATTICE menggunakan 32768 (ditandakan sebagai XMP `Chloros:PixelScale`),Survey3
menggunakan 65535. Baca tag tersebut dan bukannya menganggapnya sebagai nilai malar. Lihat [Format Imej Keluaran](../output-image-formats.md).
{% endhint %}

### Penderia Cahaya DAQ

Bahagian ini menyenaraikan setiap fail DAQ downwelling (`.daq` / `.csv`) dalam projek anda, satu baris bagi setiap fail, memaparkan model sensor, nama fail, dan pembetulan **cap** diffuser yang digunakan untuk fail tersebut.

* **Cap keutamaan (semua fail)**: satu menu lungsur tunggal untuk seluruh projek.**Auto** (laluan lalai) menggunakan cap yang dirakam bagi setiap fail — cahaya matahari dianggap wujud jika tiada apa-apa yang dirakam, kerana semua DAQMAPIR
dihantar dengan pembetul cahaya matahari. Memilih cap akan menimpa setiap fail: rakaman mentah diperbetulkan dengannya, dan rakaman yang sudah mempunyai cap akan dirujuk semula (pembetulan yang dirakam dibatalkan, dan cap yang dipilih digunakan).
* Baris memberi amaran apabila cap yang direkodkan adalah lalai yang diandaikan oleh hab dan bukannya disahkan oleh pengendali, dan apabila cap yang dipilih tiada profil untuk model peranti tersebut (penguasaan ditolak untuk fail tersebut).

Rakaman DAQ yang dibuat dalam tab Light Sensors ditambah ke projek terbuka secara automatik, dan fail `.daq` / `.csv` yang diimport akan muncul di sini sebaik sahaja ia ditambah.

<figure><img src="../.gitbook/assets/image (32).png" alt=""><figcaption><p>Tetapan Projek Rendah — Indeks, format Eksport, bahagian Penderia Cahaya DAQ, dan kawalan templat/folder projek</p></figcaption></figure>### Penyelarasan Susunan

Bahagian ini muncul **hanya** apabila sekurang-kurangnya satu imej dalam projek mengandungi transformasi penyelarasan modul-ke-modul yang dicop oleh susunan LATTICE semasa pengambilan (`Chloros:Alignment*` XMP). Ia menunjukkan berapa banyak imej mempunyai tag dan kamera mana yang menjadi rujukan, dengan kawalan berikut:

* **Terapkan penjajaran tatasusunan** (lanjutan: aktif): memutarbelitkan setiap produk yang diproses (debayered / pratonton / sinaran / pantulan / indeks) ke dalam geometri rujukan bersama tatasusunan. Tidak aktif = mengeksport dalam geometri sensor asal.
* **Pot ke tumpang tindih biasa** (lalai: aktif): pot eksport yang selari ke kawasan yang dikongsi semua modul, supaya setiap jalur mempunyai jejak yang sama. Matikan mengekalkan kanvas penderia penuh (isi hitam di luar sumber).
* **Pengasingan semula**:**Bilinear (halus, lalai)**,**Terdekat (pelihara nilai tepat)**— tiada pencampuran antara piksel, untuk analisis radiometrik yang ketat — atau**Kubik (paling tajam)**.***

## Menyimpan dan Memuat Tetapan

### Simpan Templat Projek

Buat templat yang boleh digunakan semula untuk aliran kerja yang konsisten:

1. Konfigurasikan semua tetapan yang diingini dalam panel Tetapan Projek
2. Tatal ke bahagian **&quot;Simpan Templat Projek&quot;** di bahagian bawah
3. Masukkan nama templat yang menerangkan (contohnya, &quot;Survey3N

\_RGN\_Agriculture&quot;)
4. Klik ikon simpan

**Manfaat:**

* Terapkan tetapan yang sama merentas pelbagai projek
* Kongsi konfigurasi dengan ahli pasukan
* Kekalkan keseragaman untuk tinjauan berulang

### Memuat Templat pada Projek Baharu

Apabila mencipta projek baharu:

1. Pilih **&quot;Projek Baharu&quot;** daripada menu utama
2. Pilih templat projek dalam pemilih templat pilihan
3. Semua tetapan daripada templat akan digunakan secara automatik

### Direktori Kerja

Pengesetan **&quot;Direktori Kerja&quot;** menentukan di mana projek baharu dibuat secara lalai:

* **Lokasi lalai**: `C:\Users\[Username]\Chloros Projects`
* **Ubah lokasi**: Klik ikon sunting dan pilih folder baharu
* **Dikongsi denganCLI**: `chloros-cli` menggunakan tetapan folder projek lalai yang sama
* **Bilakah hendak menukar**:
  * Pemandu rangkaian untuk kerjasama pasukan
  * Pemandu berbeza dengan lebih ruang simpanan
  * Struktur folder yang teratur mengikut tahun/klien

***

## Penyediaan PPK (Kinematik Pascaproses)

Jika menggunakan perekod DAQMAPIR

dengan GPS untuk geolokasi tepat:

### Prasyarat

* DAQMAPIR

dengan modul GPS (GNSS)
* Fail log .daq dengan entri pin pendedahan
* Kamera disambungkan ke pin pendedahan DAQ semasa sesi pengambilan

### Langkah Konfigurasi

1. Letakkan fail log .daq dalam folder projek anda
2. Dalam Tetapan Projek, aktifkan kotak semak **&quot;Terapkan pembetulan PPK&quot;**

3. Tetapkan**&quot;Ofset zon waktu penderia cahaya&quot;** jika perlu (lalai: 0 untuk UTC)
4. Tugaskan kamera kepada pin pendedahan:
   * **Satu kamera**: Secara automatik ditugaskan kepada Pin 1
   * **Dua kamera**: Tugaskan setiap kamera secara manual kepada pin yang betul**Penugasan Pin Pendedahan:*** **Pin Pendedahan 1**: Pilih model kamera daripada senarai lungsur
* **Pin Pendedahan 2**: Pilih kamera kedua atau &quot;Jangan Gunakan&quot;
* Kamera yang sama tidak boleh diberikan kepada kedua-dua pin

{% hint style="warning" %}
**Penting**: Pin pendedahan mesti ditetapkan dengan betul kepada kamera masing-masing. Penetapan yang salah akan mengakibatkan data geolokasi yang tidak betul.
{% endhint %}

***

## Senario Lanjutan

### Projek Pelbagai Kamera

Apabila memproses imej daripada beberapa kameraMAPIR

dalam satu projek:

1.Chloros

mengesan secara automatik setiap model kamera (sama adaSurvey3

atau LATTICE)
2. Setiap kamera mendapat profil pemprosesan yang sesuai, dan setiap kamera mendapat pokok folder keluaran tersendiri
3. PPK: Tugaskan secara manual setiap kameraSurvey3

kepada pin pendedahan yang betul
4. Semua kamera menggunakan format eksport dan indeks yang sama

**Contoh**:Survey3W

RGN

+Survey3N

OCN

rig kamera dwi, atau susunan LATTICE yang menggabungkan indukRGB

dengan modul jalur sempit

### Tinjauan Lajur Masa atau Pelbagai Tarikh

Untuk tinjauan berulang di kawasan yang sama dari masa ke masa:

1. Buat templat dengan tetapan standard anda
2. Gunakan tetapan sasaran penentukuran yang konsisten setiap sesi
3. Proses setiap tarikh sebagai projek berasingan
4. Gunakan tetapan yang sama untuk keputusan yang boleh dibandingkan
5. Eksport dalam format yang sama untuk analisis temporal

### Set Data Besar

Untuk projek dengan banyak imej (500+):

* Pertimbangkan untuk membahagikan kepada projek yang lebih kecil mengikut tarikh atau kawasan
* Gunakan pemprosesan selari untuk keputusan yang lebih pantas
* PertimbangkanCLI

atauAPI

untuk automasi pukal
* Laraskan selang penalaan semula minimum untuk mengurangkan masa pengesanan sasaran

***

## Mengesahkan Tetapan Anda

Sebelum memulakan pemprosesan, semak tetapan utama ini:

* [ ] Model kamera dikesan dengan betul dalam Pelayar Fail
* [ ] Pembetulan vignet diaktifkan
* [ ] Kalibrasi reflektansi diaktifkan
* [ ] UntukSurvey3

: sekurang-kurangnya satu imej sasaran kalibrasi diimport dan diperiksa; untuk LATTICE: satu sasaran dan/atau rakaman `.daq` downwelling wujud
* [ ] Indeks multispektral yang diingini ditambah
* [ ] Format eksport sesuai untuk aliran kerja anda
* [ ] Tetapan PPK dikonfigurasikan (jika menggunakan .daq dengan acara pendedahan)

***

## Langkah Seterusnya

Setelah tetapan anda dikonfigurasikan:

1. **Tandakan imej sasaran penentukuran** - Lihat [Memilih Imej Sasaran](choosing-target-images.md)
2. **Mulakan pemprosesan** - Lihat [Memulakan Pemprosesan](starting-the-processing.md)
3. **Pantau kemajuan** - Lihat [Memantau Pemprosesan](monitoring-the-processing.md)

Untuk butiran lengkap mengenai semua tetapan yang tersedia, lihat dokumentasi rujukan [Tetapan Projek](../project-settings/project-settings.md).
