# Lapisan Imej

Menu lungsur Lapisan Imej dalam Pemapar Imej Chloros membolehkan anda bertukar dengan cepat antara versi berbeza bagi imej yang sama - daripada tangkapan asal kepada output pemantulan yang diproses dan imej indeks yang dikira.

## Apakah itu Lapisan Imej?

Dalam Chloros, **lapisan** merujuk kepada output imej berbeza yang tersedia untuk imej sumber tunggal. Apabila anda memproses imej, Chloros mencipta berbilang versi:

* **Imej asal** (Fail JPG dan RAW daripada kamera anda)
* **Kalibrasi pantulan ditentukur** keluaran (jika penentukuran pantulan didayakan)
* **Imej sasaran** (jika imej mengandungi sasaran penentukuran)
* **Imej indeks** (NDVI, NDRE, GNDVI, dsb. jika indeks telah dikonfigurasikan)**Layer Selector dropdown** di bahagian atas sebelah kanan Pemapar Imej membolehkan anda bertukar serta-merta antara versi ini tanpa meninggalkan pemapar.***

## Jenis Lapisan Tersedia

### JPG

* Imej pratonton JPG asal daripada kamera anda
* Sentiasa tersedia untuk semua imej
* Tidak diproses, seperti yang ditangkap oleh kamera
* Paling cepat untuk dimuatkan dan dipaparkan

**Bila untuk melihat:**

* Pratonton pantas tangkapan asal
* Menyemak komposisi imej dan pembingkaian
* Mengesahkan kualiti tangkapan sebelum diproses

### RAW (Asal)

* Data sensor RAW asal daripada kamera anda
* Debayered tanpa pemprosesan pos digunakan
* Kedalaman bit yang lebih tinggi daripada JPG (biasanya data penderia 12-bit atau 14-bit)

**Bila untuk melihat:**

* Memeriksa kualiti data sensor asal
* Menyemak isu atau artifak penderia
* Membandingkan keputusan sebelum/selepas pemprosesan

### RAW (Sasaran)

* Hanya muncul untuk imej yang dikenal pasti mengandungi sasaran penentukuran
* Menunjukkan imej RAW asal dengan sasaran dikesan
* Digunakan untuk mengesahkan pengesanan sasaran berjaya

**Bila untuk melihat:**

* Mengesahkan sasaran penentukuran telah dikesan dengan betul
* Menyemak kualiti imej sasaran
* Menyelesaikan masalah penentukuran

{% gaya petunjuk="info" %}
**Lapisan Sasaran**: Lapisan ini hanya muncul dalam lungsur turun untuk imej yang mengandungi sasaran penentukuran. Imej tangkapan biasa tidak akan mempunyai pilihan ini.
Petua {% %}

### RAW (Pantulan)

* Imej keluaran pemantulan yang ditentukur
* Vignette diperbetulkan (jika didayakan dalam pemprosesan)
* Pantulan ditentukur menggunakan data sasaran (jika didayakan)
* TIFF berbilang jalur dengan semua saluran kamera
* Nilai piksel mewakili pantulan peratus (apabila menggunakan mod peratus)
* Sedia untuk memanipulasi dengan [Index/LUT Sandbox](index-lut-sandbox.md)

**Bila untuk melihat:**

* Memeriksa keputusan yang ditentukur
* Mengesahkan kualiti penentukuran
* Menyemak nilai piksel untuk ketepatan saintifik
* Membandingkan dengan asal untuk melihat kesan penentukuran

{% gaya petunjuk="berjaya" %}
**Disyorkan**: Gunakan lapisan RAW (Reflectance) apabila menyemak nilai piksel untuk pengukuran dan analisis saintifik.
Petua {% %}

### RAW (NDVI Index)... dan seumpamanya

* Imej indeks tumbuh-tumbuhan yang dikira (NDVI dalam contoh ini)
* Nama indeks berubah berdasarkan indeks yang telah dikonfigurasikan semasa pemprosesan
* Contoh: RAW (NDVI Index), RAW (NDRE Index), RAW (GNDVI Index), dsb.
* Imej skala kelabu jalur tunggal yang menunjukkan hasil pengiraan indeks
* Satu lapisan muncul untuk setiap indeks yang dikonfigurasikan dalam Tetapan Projek

**Nama indeks yang mungkin:**

* MENTAH (Indeks NDVI)
* MENTAH (Indeks NDRE)
* MENTAH (GNDVI Index)
* MENTAH (Indeks OSAVI)
* MENTAH (Indeks EVI)
* MENTAH (Indeks SAVI)
* Dan banyak lagi... (lihat [Formula Indeks Berbilangspek](../project-settings/multispectral-index-formulas.md))

**Bila untuk melihat:**

* Meneliti keputusan pengiraan indeks
* Menyemak julat nilai indeks
* Mengenal pasti bidang yang diminati
* Mengesahkan imej indeks sebelum digunakan dalam GIS atau analisis

***

## Menggunakan Pemilih Lapisan

### Membuka Dropdown

1. Buka imej dalam mod skrin penuh (klik mana-mana lakaran kecil dalam Pemapar Imej)
2. Cari **lapisan lungsur turun** di penjuru kanan sebelah atas pemapar
3. Menu lungsur menunjukkan lapisan yang sedang dipilih (cth., "JPG")
4. Klik menu lungsur untuk melihat semua lapisan yang tersedia

### Menukar Lapisan

1. Klik lungsur turun lapisan untuk membuka senarai
2. Semua lapisan yang tersedia untuk imej semasa ditunjukkan
3. Klik mana-mana nama lapisan untuk bertukar kepada versi itu
4. Imej dikemas kini serta-merta untuk menunjukkan lapisan yang dipilih

**Penukaran pantas:**

* Dropdown mengingati pilihan terakhir anda
* Apabila menavigasi ke imej seterusnya, Chloros cuba untuk menunjukkan jenis lapisan yang sama
* Jika lapisan itu tidak wujud pada imej seterusnya, ia lalai kepada JPG

### Ketersediaan Lapisan

Tidak semua lapisan tersedia untuk setiap imej:

**Sentiasa tersedia:*** ✅ JPG (setiap imej mempunyai pratonton JPG)

**Tersedia dengan syarat:**

* ⚠️ RAW (Asal) - Hanya jika imej telah ditangkap dalam mod RAW atau RAW+JPG
* ⚠️ RAW (Sasaran) - Hanya jika imej mengandungi sasaran penentukuran yang dikesan
* ⚠️ RAW (Reflectance) - Hanya selepas pemprosesan dengan penentukuran pantulan didayakan
* ⚠️ RAW (\[Index] Index) - Hanya selepas pemprosesan dengan indeks dikonfigurasikan

***

## Kegigihan Lapisan

### Menavigasi Antara Imej

Apabila anda menavigasi ke imej lain (menggunakan kekunci anak panah atau mengklik lakaran kenit):**Pilihan lapisan dikekalkan:**

* Jika melihat "RAW (Reflectance)", imej seterusnya menunjukkan "RAW (Reflectance)" (jika ada)
* Jika melihat "RAW (NDVI Index)", imej seterusnya menunjukkan "RAW (NDVI Index)" (jika ada)
* Jika lapisan yang sama tidak wujud, lalai kepada JPG

**Contoh aliran kerja:**

1. Buka Imej 1, tukar kepada RAW (NDVI Index)
2. Tekan → untuk melihat Imej 2
3. Imej 2 secara automatik memaparkan lapisan RAW (NDVI Index).
4. Teruskan menavigasi - semua imej menunjukkan lapisan NDVI
5. Sangat cekap untuk menyemak hasil indeks merentas banyak imej

***

## Aliran Kerja Biasa

### Aliran Kerja 1: Sebelum/Selepas Perbandingan

**Matlamat**: Bandingkan imej asal berbanding imej yang ditentukur

1. Buka imej yang diproses dalam Pemapar Imej
2. Pilih **RAW (Asal)** daripada lungsur turun
3. Perhatikan nilai vignetting dan tidak ditentukur
4. Tukar kepada **RAW (Reflectance)** daripada dropdown
5. Bandingkan - vignetting dikeluarkan, nilai ditentukur

### Aliran Kerja 2: Semakan Indeks

**Matlamat**: Semak keputusan NDVI merentas set data dengan pantas

1. Buka imej pertama yang diproses
2. Pilih **RAW (NDVI Index)** daripada lungsur turun
3. Gunakan → kekunci anak panah untuk menavigasi ke imej seterusnya
4. Lapisan NDVI berterusan secara automatik
5. Teruskan melalui semua imej, semak corak NDVI
6. Tukar kepada **RAW (NDRE Index)** untuk membandingkan

### Aliran Kerja 3: Pengesahan Sasaran

**Matlamat**: Sahkan semua imej sasaran telah dikesan dengan betul

1. Navigasi ke imej sasaran
2. Pilih **RAW (Sasaran)** daripada lungsur turun
3. Sahkan sasaran penentukuran jelas kelihatan dan dikesan
4. Navigasi ke imej sasaran seterusnya
5. Ulang pengesahan untuk semua sasaran

### Aliran Kerja 4: Pemeriksaan Nilai Piksel

**Matlamat**: Semak nilai pantulan untuk ketepatan saintifik

1. Buka imej yang diproses
2. Pilih lapisan **RAW (Reflectance)**

3. Dayakan mod**Peratus Piksel** (butang di bar alat sebelah kanan atas)
4. Gerakkan kursor ke atas kawasan tumbuh-tumbuhan
5. Sahkan nilai piksel berada dalam julat jangkaan (30-70% untuk NIR, 5-15% untuk Red)
6. Periksa kawasan tanah dan air untuk nilai yang sesuai

***

## Memahami Nilai Piksel mengikut Lapisan

Lapisan yang berbeza menunjukkan julat nilai piksel yang berbeza:

### Lapisan JPG

* **Julat**: 0-255 (8-bit)
* **Maksud**: Nilai paparan, diperbetulkan gamma
* **Penggunaan**: Pemeriksaan visual sahaja, bukan untuk pengukuran saintifik

### RAW (Asal)

* **Julat**: 0-65535 (16-bit)
* **Maksud**: Nombor digital penderia mentah
* **Penggunaan**: Memeriksa prestasi penderia, tidak ditentukur

### RAW (Pantulan)

* **Julat**: 0-65,535 (16-bit TIFF) atau 0.0-1.0 (32-bit Peratus)
* **Maksud**: Pemantulan peratusan yang ditentukur
* **Kegunaan**: Pengukuran dan analisis saintifik**Untuk TIFF 16-bit:**Bahagikan sebanyak 65,535 untuk mendapatkan pemantulan peratus**Untuk Peratusan 32-bit:** Nilai secara langsung mewakili peratus (0.5 = 50% pemantulan)

### RAW (Imej Indeks)

* **Julat**: Berbeza mengikut indeks (biasanya -1.0 hingga +1.0 untuk indeks ternormal)
* **Maksud**: Hasil pengiraan indeks
* **Contoh**:
  * NDVI: -1 hingga +1 (tumbuhan biasanya 0.4 hingga 0.9)
  * NDRE: -1 hingga +1 (pengesanan tekanan)
  * EVI: 0 hingga 1 (tumbuh-tumbuhan yang dipertingkatkan)

***

## Petua dan Amalan Terbaik

### Penukaran Lapisan yang Cekap

* **Kesedaran pintasan papan kekunci**: Walaupun tiada pintasan papan kekunci untuk lapisan, anak panah navigasi (←/→) berfungsi merentas semua lapisan
* **Aliran kerja yang konsisten**: Pilih satu lapisan (cth., NDVI) dan semak keseluruhan set data sebelum beralih kepada yang lain
* **Perbandingan pantas**: Togol antara Original dan Reflectance untuk mengesahkan kualiti pemprosesan

### Pertimbangan Prestasi

* **JPG dimuatkan paling cepat**: Gunakan untuk navigasi pantas melalui banyak imej
* **Lapisan RAW dimuatkan lebih perlahan**: Peleraian yang lebih tinggi dan kedalaman bit
* **Lapisan indeks**: Kelajuan yang sama dengan lapisan Reflectance
* **Pemuatan pertama adalah paling perlahan**: Paparan seterusnya pada lapisan yang sama dicache dan lebih pantas

### Pengesahan Kualiti

* **Sentiasa semak RAW (Asal)**: Sahkan kualiti data sumber sebelum mempercayai output yang diproses
* **Bandingkan lapisan**: Gunakan penukaran lapisan untuk mengesahkan pemprosesan berfungsi dengan betul
* **Semak julat indeks**: Gunakan mod Peratusan Piksel dengan lapisan indeks untuk mengesahkan nilai adalah munasabah***

## Menyelesaikan masalah

### Lapisan Tidak Tersedia

**Masalah**: Lapisan yang dijangkakan tidak muncul dalam lungsur turun**Punca yang mungkin:**

* Imej tidak diproses (hanya JPG dan RAW (Asal) tersedia)
* Penentukuran pantulan telah dilumpuhkan semasa pemprosesan
* Indeks khusus tidak dikonfigurasikan dalam Tetapan Projek
* Imej ialah imej sasaran sahaja (tiada indeks dihasilkan untuk sasaran)

**Penyelesaian:**

1. Sahkan imej telah diproses (semak folder output untuk fail yang diproses)
2. Semak Tetapan Projek untuk mengesahkan indeks telah dikonfigurasikan
3. Proses semula dengan indeks yang dikehendaki didayakan

### Lapisan Salah Ditunjukkan

**Masalah**: Imej dibuka dalam lapisan yang tidak dijangka**Punca**: Keutamaan lapisan daripada imej sebelumnya dibawa ke hadapan, tetapi lapisan itu tidak wujud pada imej semasa**Penyelesaian**: Chloros secara automatik kembali ke JPG apabila lapisan pilihan tidak tersedia - ini adalah tingkah laku biasa

### Tidak Dapat Melihat Sasaran Penentukuran

**Masalah**: Lapisan RAW (Sasaran) tidak menunjukkan pengesanan sasaran**Punca yang mungkin:**

* Sasaran tidak dikesan semasa pemprosesan
* Imej sebenarnya tidak mengandungi sasaran
* Tetapan pengesanan sasaran terlalu ketat

**Penyelesaian:**

1. Semak Log Nyahpepijat untuk mesej "Sasaran ditemui".
2. Sahkan imej sebenarnya mengandungi sasaran penentukuran yang boleh dilihat
3. Laraskan tetapan pengesanan sasaran dalam Tetapan Projek
4. Lihat [Memilih Imej Sasaran](../processing-images-gui/choosing-target-images.md)

***

## Ciri-ciri Berkaitan

### Alat Pemapar Imej

Apabila melihat mana-mana lapisan, anda boleh menggunakan:

* **Kawalan zum**: Besarkan untuk memeriksa butiran
* **Sorot**: Klik dan seret untuk bergerak ke sekeliling imej yang dizum
* **Pemeriksaan nilai piksel**: Lihat nilai di lokasi kursor
* **Anak panah navigasi**: Beralih antara imej sambil mengekalkan lapisan
* **Mod Peratusan Pixel**: Togol antara paparan DN dan peratus

Lihat [Membuka Skrin Penuh Imej](opening-an-image-full-screen.md) untuk dokumentasi Pemapar Imej yang lengkap.

### Kotak Pasir Indeks/LUT

Untuk ujian dan visualisasi indeks interaktif:

* **Pengiraan indeks masa nyata**: Uji formula indeks yang berbeza
* **Pemetaan warna LUT**: Gunakan kecerunan warna pada indeks skala kelabu
* **Export visualisasi**: Simpan imej indeks berwarna

Lihat [Index/LUT Sandbox](index-lut-sandbox.md) untuk butiran.

***

## Langkah Seterusnya

Sekarang anda memahami lapisan imej:

* [**Membuka Skrin Penuh Imej**](opening-an-image-full-screen.md) - Panduan Lengkap Pemapar Imej
* [**Kotak Pasir Indeks/LUT**](index-lut-sandbox.md) - Visualisasi indeks interaktif
* [**Rumus Indeks Berbilangspek**](../project-settings/multispectral-index-formulas.md) - Rujukan indeks tersedia
* [**Menyelesaikan Pemprosesan**](../processing-images-gui/finishing-the-processing.md) - Memahami output yang diproses