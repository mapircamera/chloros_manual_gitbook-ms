# Membuka Skrin Penuh Imej

Penonton Imej Chloros menyediakan antara muka skrin penuh yang berdedikasi untuk melihat, menganalisis, dan memanipulasi imej multispektral anda. Sama ada melihat imej asal atau output yang diproses, penonton imej menawarkan alat yang berkuasa untuk pemeriksaan dan analisis.

## mengakses penonton imej

### dari penyemak imbas fail

Cara yang paling biasa untuk membuka imej dalam penonton imej:

1. Pastikan anda berada di ** pelayar fail ** tab <img src = "../. Gitbook/aset/icon_file-browser.jpg" alt = "" data-size = "line">
2. Klik mana -mana ** gambar kecil gambar ** dalam grid imej
3. Imej dibuka di kawasan pratonton utama ** (pusat skrin)
4. Imej kini dimuatkan dan siap untuk melihat skrin penuh

### Membuka tab Penonton Imej

Setelah imej dimuatkan di kawasan pratonton:

1. Klik ** Image Viewer ** <img src = "../. Gitbook/Aset/icon_image-viewer.jpg" alt = "" data-size = "line"> ikon di bar sisi kiri
2. Tab Penonton Imej dibuka, memaparkan skrin penuh imej yang dipilih
3. Alat tontonan dan analisis lanjutan boleh didapati di bar sisi kiri

***

## Gambaran Keseluruhan Antara Muka Imej

### Kawasan paparan utama

Bahagian terbesar skrin menunjukkan imej anda:

*** Resolusi Penuh **: Imej yang dipaparkan pada resolusi asli
*** Zoomable **: Gunakan kawalan atau roda tetikus untuk mengezum
*** Pannable **: Klik dan seret untuk bergerak ketika dizum
*** nisbah aspek dikekalkan **: Skala imej secara proporsional

***

## Pilihan tontonan

### navigasi imej asas

#### melayari gambar

Navigasi melalui set gambar anda menggunakan pintasan atau butang papan kekunci:

*** Imej Seterusnya **: Klik → Butang atau tekan ** → ** (anak panah kanan)
*** gambar sebelumnya **: klik ← butang atau tekan ** ← ** (anak panah kiri)
*** Lompat ke imej tertentu **: Kembali ke pelayar fail dan klik gambar kecil yang dikehendaki

#### Kawalan zum

Laraskan pembesaran untuk memeriksa butiran imej:

** Zum masuk: **

*Klik **+** (ditambah) butang
*Tekan **+** atau ** = ** kekunci
*Tatal roda tetikus ** up **

** Zum keluar: **

*Klik ** - ** (tolak) butang
*Tekan ** - ** (tolak) kekunci
*Tatal roda tetikus ** ke bawah **

** sesuai untuk skrin: **

*Klik ** ↔ ** (Fit) Butang
*Tekan ** 0 ** (sifar) kekunci
* Klik dua kali pada gambar

#### kuali ketika diperbaiki

Apabila dizoom di luar saiz skrin:

1. Pindahkan kursor tetikus ke atas gambar
2. Klik dan ** Tahan butang tetikus kiri **
3. ** Seret ** untuk menggerakkan gambar di sekitar
4. Siaran untuk menghentikan panning

** Alternatif **: Gunakan kekunci anak panah untuk pan dengan kenaikan kecil

***

## Pemeriksaan nilai piksel

### Melihat nilai piksel di kursor

Semasa anda menggerakkan kursor tetikus anda ke atas imej, nilai piksel dipaparkan dalam masa nyata:

** Lokasi Paparan Nilai: **

*** Nombor terapung dan garis merah di indeks sebelah kanan LUT Legend Gradien **
*** Apabila dizum lebih jauh, nilai terapung berhampiran kursor dan piksel yang diserlahkan **
*Menunjukkan nilai untuk piksel ** di bawah kursor atau diserlahkan **
* Kemas kini semasa anda memindahkan tetikus

***

## Jenis gambar yang boleh anda lihat

### Imej asal (pra-pemprosesan)

** RAW + JPG Imej dari Kamera: **

* Paparkan data mentah seperti yang dipratonton
* Tunjukkan nilai asal dan tidak dikesan
* Berguna untuk memeriksa kualiti gambar sebelum diproses

### gambar refleksi yang dikalibrasi

** Setelah diproses: **

* Vignette diperbetulkan
* Refleksi ditentukur
* Tiff multi-band (merah, hijau, nir, dll.)
* Data saintifik bersedia untuk dianalisis

### Imej indeks

** ndvi, ndre, gndvi, dan lain -lain (\ _ndvi.tif fail): **

* Gambar skala kelabu tunggal
* Nilai piksel mewakili hasil pengiraan indeks
* Julat biasanya -1 hingga +1 untuk indeks normal
* Boleh menggunakan luts warna untuk visualisasi

***

## Indeks dan aplikasi LUT

Sapukan indeks multispektral dan jadual paparan warna:

1. Cari ** Indeks/Lut Sandbox ** In ** Image Viewer ** <img src = "../. Gitbook/aset/icon_image-viewer.jpg" alt = "" data-size = "line">
2. Pilih Indeks Vegetasi (NDVI, NDRE, dll.)
3. Pilih formula multispektral, atau buat satu adat anda sendiri (chloros+ sahaja)
4. Gunakan kecerunan lut warna untuk visualisasi
5. Laraskan julat nilai dan ambang

Lihat [Index/Lut Sandbox] (Index-Lut-Sandbox.MD) untuk arahan terperinci.

***

## Pintasan papan kekunci

### navigasi

*** → ** (anak panah kanan): Gambar seterusnya
*** ← ** (anak panah kiri): gambar sebelumnya
*** Laman Utama **: Imej Pertama dalam Senarai
*** end **: Imej terakhir dalam senarai

### Zoom

***+** atau ** = **: Zum masuk
*** - **: Zum keluar
*** 0 ** (sifar): Sesuai dengan skrin
*** roda tetikus **: Zum masuk/keluar

### Lihat kawalan

*** p **: Mod peratus togol piksel
*** l **: panel lapisan togol
*** esc **: Tutup skrin penuh atau kembali ke penyemak imbas fail

### Lain -lain

*** ctrl+s **: simpan gambar semasa
*** f **: mod skrin penuh (jika ada)

***

### Mengesahkan pengiraan indeks

Periksa indeks yang dikira dengan betul:

1. Buka NDVI atau gambar indeks lain
2. Periksa kawasan tumbuh -tumbuhan:
   *** ndvi **: harus menunjukkan 0.4-0.9 untuk tanaman yang sihat
   *** ndre **: Nilai yang lebih tinggi untuk pertumbuhan yang kuat
   *** GNDVI **: Sama seperti NDVI tetapi sensitif klorofil
3. Periksa bukan vegetasi:
   *** tanah **: dekat 0 atau sedikit negatif
   *** air **: Nilai negatif (-0.5 hingga 0)

***

## masalah tontonan penyelesaian masalah

### Imej tidak akan dibuka

** Kemungkinan Punca: **

* Fail rosak semasa pemprosesan
* Format fail yang tidak disokong
* Memori yang tidak mencukupi untuk gambar yang besar

** Penyelesaian: **

1. Cuba buka di penonton luaran untuk mengesahkan integriti fail
2. Periksa format fail yang sesuai dengan jenis yang diharapkan
3. Tutup aplikasi lain untuk memori percuma
4. Cuba gambar yang lebih kecil/berbeza

### paparan gambar hitam atau putih

** Kemungkinan Punca: **

* Rentang nilai keupayaan paparan di luar
* Imej terapung 32-bit dengan nilai yang tidak biasa
* Ralat pengiraan indeks

** Penyelesaian: **

1. Semak nilai piksel - jika semua sangat rendah atau sangat tinggi, laraskan julat paparan
2. Cuba pembukaan di QGIS atau serupa dengan pelarasan auto-jarak
3. Periksa log debug dari pemprosesan untuk kesilapan

### Nilai piksel kelihatan salah

** Kemungkinan Punca: **

* Melihat gambar yang salah (asal vs diproses)
* Penentukuran tidak digunakan dengan betul
* Data sensor cahaya tidak termasuk dalam input
* Mod peratus bertukar dengan tidak betul

** Penyelesaian: **

1. Sahkan anda melihat output yang diproses (semak akhiran nama fail)
2. Periksa keadaan butang mod peratus
3. Bandingkan dengan imej yang diketahui-baik dari dataset yang sama

***

## Langkah seterusnya

Sekarang anda boleh melihat gambar skrin penuh:

*[** Lapisan Imej **] (Image-Layers.md)-Ketahui mengenai visualisasi multi-band
*[** Indeks/Lut Sandbox **] (Index-Lut-Sandbox.md)-Guna indeks tersuai dan pemetaan warna
*[** Formula Indeks Multispectral **] (../ Project-Settings/Multispectral-index-Formulas.md)-Memahami indeks yang tersedia

Untuk memproses aliran kerja, lihat:

*[** Imej Pemprosesan (GUI) **] (../ Pemprosesan-Images-Gui/Adding-Files-to-A-Project.md)-Panduan Pemprosesan Lengkap
