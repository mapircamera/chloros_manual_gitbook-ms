# Buka gambar di skrin penuh

Penonton Imej Chloros menyediakan antara muka skrin penuh yang berdedikasi untuk melihat, menganalisis dan memanipulasi imej multispektral anda. Sama ada imej asal atau hasil yang diproses, penonton imej menawarkan alat yang berkuasa untuk pemeriksaan dan analisis.

## akses ke penonton imej

### dari penjelajah fail

Cara yang paling biasa untuk membuka imej dalam penonton imej:

1. Pastikan anda berada di tab ** File Explorer **. <img src = "../. gitbook/aset/icon_file-browser.jpg" alt = "" data-size = "line">
2. Klik mana -mana ** gambar kecil gambar ** dalam grid imej
3. Imej dibuka di kawasan pratonton utama ** (pusat skrin)
4. Imej kini dimuat dan siap untuk tontonan skrin penuh

### Tab Penonton Imej Terbuka

Sebaik sahaja imej telah dimuatkan ke kawasan pratonton:

1. Klik ** Image Viewer ** <img src = "../. Gitbook/aset/icon_image-viewer.jpg" alt = "" data-size = "line"> ikon di bar sisi kiri.
2. Tab Penonton Imej akan dibuka, memaparkan imej yang dipilih dalam skrin penuh.
3. Visualisasi lanjutan dan alat analisis akan tersedia di bar sisi kiri.

***

## Gambaran Keseluruhan Antara Muka Imej

### Kawasan paparan utama

Kebanyakan skrin menunjukkan imej:

*** Resolusi penuh **: Imej dipaparkan pada resolusi asli mereka.
*** Zoomable **: Gunakan kawalan atau roda tetikus untuk mengezum
*** scrollable ** - Klik dan seret ke tatal ketika dizum
*** Nisbah aspek yang dikekalkan **: Imej berskala secara proporsional

***

## Pilihan paparan

### navigasi imej asas

#### Lihat gambar

Navigasi set gambar anda menggunakan pintasan atau butang papan kekunci:

*** Imej di bawah **: Klik butang → atau tekan kekunci ** → ** (anak panah kanan)
*** Imej di atas **: Klik butang ← atau tekan kekunci ** ← ** (anak panah kiri)
*** Pergi ke imej tertentu ** - Kembali ke Penjelajah Fail dan klik gambar kecil yang dikehendaki

#### Kawalan zum

Laraskan pembesaran untuk memeriksa butiran imej:

** Zum masuk: **

*Klik butang **+** (plus).
*Tekan kekunci **+** atau ** = **.
*Tatal dengan roda tetikus ** ke **.

** Keluarkan: **

*Klik butang ** - ** (tolak).
*Tekan kekunci ** - ** (tolak).
*Tatal dengan roda tetikus ** ke bawah **.

** sesuai untuk skrin: **

*Klik butang ** ↔ ** (Laraskan).
*Tekan kekunci ** 0 ** (sifar).
* Klik dua kali pada imej.

#### tatal ketika zum

Apabila berkembang di luar saiz skrin:

1. Pindahkan kursor tetikus ke atas imej.
2. Klik dan ** Pegang butang tetikus kiri **.
3. ** Seret ** untuk memindahkan imej.
4. Melepaskan untuk berhenti menatal.

** Alternatif **: Gunakan kekunci anak panah untuk menatal dengan kenaikan kecil.

***

## Pemeriksaan nilai piksel

### memaparkan nilai piksel di kursor

Semasa anda menggerakkan kursor tetikus ke atas imej, nilai piksel dipaparkan dalam masa nyata:

** Lokasi Paparan Nilai: **

*** Nombor terapung dan garis merah di indeks sebelah kanan LUT Legend Gradien **
*** Apabila dizum lebih jauh, nilai terapung berhampiran kursor dan piksel yang diserlahkan **
*Memaparkan nilai piksel ** di bawah kursor atau diserlahkan **
* Kemas kini semasa anda menggerakkan tetikus

***

## Jenis imej yang dapat dilihat

### Imej Asal (Preprocessing)

** RAW + JPG Imej dari Kamera: **

* Menunjukkan data mentah seperti yang dipratonton
* Menunjukkan nilai asal tanpa pembetulan
* Berguna untuk memeriksa kualiti gambar sebelum diproses

### gambar refleksi yang dikalibrasi

** Setelah diproses: **

* Vignette diperbetulkan.
* Refleksi yang dikalibrasi.
* Multiband tiff (merah, hijau, nir, dll.).
* Data saintifik bersedia untuk analisis.

### Imej indeks

** ndvi, ndre, gndvi, dan lain -lain (\ _ndvi.tif fail): **

*Gambar skala kelabu tunggal
*Nilai piksel mewakili hasil pengiraan indeks
*Julat tipikal -1 hingga +1 untuk indeks normal
* Luts warna boleh digunakan untuk paparan

***

## Memohon indeks dan luts

Sapukan indeks multispektral dan jadual carian warna:

1. Cari ** indeks/lut sandbox ** di <img src = "../. Gitbook/aset/icon_image-viewer.jpg" alt = "" data-size = "line"> sidebar
2. Pilih Indeks Vegetasi (NDVI, NDRE, dll.)
3. Pilih formula multispektral atau buat yang tersuai (hanya kloros+ sahaja)
4. Sapukan kecerunan jadual carian warna (LUT) untuk paparan
5. Laraskan julat nilai dan ambang

Lihat [Index/Lut Sandbox] (Index-Lut-Sandbox.MD) untuk arahan terperinci.

***

## Pintasan papan kekunci

### navigasi

*** → ** (anak panah kanan): Gambar seterusnya
*** ← ** (anak panah kiri): gambar sebelumnya
*** Laman Utama **: Imej pertama dalam senarai
*** end **: gambar terakhir dalam senarai

### Zoom

****** atau ** = **: Zum masuk
*** - **: Zum keluar
*** 0 ** (sifar): Sesuai dengan skrin
*** roda tetikus **: Zum masuk/keluar

### Kawalan paparan

*** p **: Mod peratusan togol piksel
*** l **: panel lapisan togol
*** ESC **: Tutup skrin penuh atau kembali ke Fail Explorer

### Lain -lain

*** ctrl+s **: simpan gambar semasa
*** F **: Mod Skrin Penuh (jika ada)

***

### Mengesahkan pengiraan indeks

Periksa bahawa indeks telah dikira dengan betul:

1. Buka NDVI atau imej indeks lain.
2. Periksa kawasan tumbuh -tumbuhan:
   *** ndvi **: harus memaparkan antara 0.4 dan 0.9 untuk tumbuhan yang sihat.
   *** ndre **: Nilai yang lebih tinggi untuk pertumbuhan yang kuat
   *** gndvi **: Sama seperti NDVI, tetapi sensitif terhadap klorofil
3. Periksa ketiadaan tumbuh -tumbuhan:
   *** tanah **: Dekat dengan 0 atau sedikit negatif.
   *** Air **: Nilai negatif (dari -0.5 hingga 0).

***

## Masalah paparan penyelesaian masalah

### Imej tidak dibuka

** Kemungkinan Punca: **

* Fail yang rosak semasa pemprosesan.
* Format fail tidak disokong.
* Memori yang tidak mencukupi untuk imej besar.

** Penyelesaian: **

1. Cuba buka dalam penonton luaran untuk mengesahkan integriti fail.
2. Sahkan bahawa format fail sepadan dengan jenis yang diharapkan.
3. Tutup aplikasi lain untuk membebaskan ingatan.
4. Cuba imej yang lebih kecil atau berbeza.

### gambar hitam dan putih

** Kemungkinan Punca: **

* Pelbagai nilai di luar kapasiti paparan.
* Imej terapung 32-bit dengan nilai yang luar biasa.
* Ralat dalam pengiraan indeks.

** Penyelesaian: **

1. Semak nilai piksel: Jika mereka terlalu rendah atau terlalu tinggi, laraskan julat paparan.
2. Cuba buka di QGIS atau serupa dengan pelarasan pelbagai auto.
3. Semak log debug pemprosesan untuk kesilapan.

### Nilai piksel kelihatan tidak betul

** Kemungkinan Punca: **

* Paparan imej yang salah (asal vs diproses).
* Penentukuran tidak digunakan dengan betul.
*Data sensor cahaya tidak termasuk dalam input.
* Mod peratusan diaktifkan dengan tidak betul.

** Penyelesaian: **

1. Sahkan bahawa anda melihat output yang diproses (periksa sambungan nama fail).
2. Semak status butang Mod Peratusan.
3. Bandingkan dengan imej yang betul yang diketahui dari set data yang sama.

***

## Langkah seterusnya

Sekarang anda dapat melihat imej dalam skrin penuh:

*[** Lapisan Imej **] (Image-Layers.md): Ketahui mengenai paparan multiband.
*[** Index Sandbox/LUT **] (Index-Lut-Sandbox.md): Guna indeks tersuai dan pemetaan warna.
*[** Formula Indeks Multispectral **] (../ Project-Settings/Multispectral-Index-Formulas.md): Memahami indeks yang tersedia.

Untuk memproses aliran kerja, lihat:

*[** Pemprosesan Imej (GUI) **] (../ Pemprosesan-Images-Gui/Adding-Files-to-A-Project.md): Panduan Pemprosesan Lengkap.