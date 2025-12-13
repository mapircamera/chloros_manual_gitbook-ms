# Menyelesaikan pemprosesan

Sebaik sahaja kloros menyelesaikan pemprosesan, sudah tiba masanya untuk mengkaji semula hasil anda, sahkan kualiti output, dan sediakan imej yang diproses anda untuk digunakan dalam aliran kerja anda. Halaman ini membimbing anda melalui langkah terakhir dan tindakan seterusnya.

## memproses petunjuk lengkap

Apabila pemprosesan berjaya, anda akan melihat beberapa petunjuk:

*✅ ** Bar Progress **: mencapai 100% penyelesaian
*✅ ** Log Debug **: Menunjukkan mesej "Pemprosesan Lengkap"
*✅ ** Butang Mula **: Menjadi Diaktifkan Again (siap untuk pemprosesan seterusnya)
*✅ ** Output Files **: Semua imej yang diproses disimpan ke subfolder model kamera

***

## Mencari gambar yang diproses anda

### Membuka folder output

1. Klik ** menu utama ** <img src = "../. Gitbook/aset/imej (1) (1) .png" alt = "" data-size = "line"> ikon (kiri atas)
2. Pilih ** "Folder Projek Buka" **
3. Penjelajah fail anda dibuka ke direktori projek
4. Cari projek anda dengan nama

***

## Mengkaji gambar yang diproses

### Pratonton cepat dalam Fail Explorer

** Pratonton terbina dalam Windows: **

1. Navigasi ke Subfolder Model Kamera
2. Pilih fail gambar
3. Pratonton muncul di Windows Explorer Preview Pane
4. Gunakan kekunci anak panah untuk melayari gambar

### Pratonton dalam penonton imej luaran

** Penonton yang disyorkan: **

*** QGIS ** - Perisian GIS PERCUMA (terbaik untuk analisis multispektral georeferenced)
*** Irfanview ** - Pemandangan imej yang cepat dan ringan (menyokong TIFF)
*** Adobe Photoshop ** - Penyuntingan Profesional (Sokongan TIFF)
*** Gimp ** - Alternatif percuma untuk Photoshop
*** Foto Windows ** - Tontonan Asas (mungkin tidak menyokong tiff 16 -bit)

### Pratonton dalam penonton imej kloros

Gunakan penonton imej terbina dalam Chloros untuk visualisasi lanjutan:

1. Klik gambar kecil imej dalam penyemak imbas fail
2. Imej dibuka di kawasan pratonton utama
3. Klik ** Penonton Imej ** <img src = "../. Gitbook/aset/icon_image-viewer.jpg" alt = "" data-size = "line"> tab di bar sisi kiri
4. Gunakan [indeks/lut sandbox] (../ image-viewer-gui/index-lut-sandbox.md) untuk analisis interaktif

Lihat [Image Viewer] (../ Image-Viewer-Gui/Opening-An-Image-full-screen.md) untuk arahan terperinci.

***

## Mengkaji log debug

### periksa amaran atau kesilapan

1. Buka ** log debug ** <img src = "../. Gitbook/aset/icon_log.jpg" alt = "" data-size = "line"> tab
2. Tatal melalui mesej
3. Cari amaran kuning atau kesilapan merah
4. Mengkaji sebarang isu yang dinyatakan
5. Hubungi Sokongan Mapir untuk mendapatkan bantuan

### menjimatkan log

Untuk menyimpan rekod pemprosesan atau dihantar ke Sokongan Mapir:

1. Klik ** "Salin" ** atau ** "Muat turun" ** Butang
2. Simpan sebagai fail teks dalam folder projek
3. Termasuk dengan dokumentasi projek
4. Hantar ke Sokongan Mapir jika isu yang dihadapi

***

## Masalah dan penyelesaian output biasa

### Isu: fail output yang hilang

** Kemungkinan Punca: **

* Fail tidak memenuhi kriteria pemprosesan
* Imej sasaran sahaja (dikecualikan daripada eksport)
* Ruang cakera habis semasa eksport
* Rasuah fail semasa pemprosesan

** Penyelesaian: **

1. Periksa log debug untuk mesej skip/ralat
2. Sahkan ruang cakera cukup
3. Kira Fail: Sekiranya sepadan (kiraan asal - kiraan sasaran) × (indeks + 1)
4. Mengimport semula dan memproses semula sebarang fail yang hilang

### Isu: tepi gelap atau cerah (vignetting masih kelihatan)

** Kemungkinan Punca: **

* Pembetulan vignette dilumpuhkan
* Kamera/lensa tidak dalam pangkalan data profil kloros
* Vignetting melampau melampaui keupayaan pembetulan

** Penyelesaian: **

1. Sahkan pembetulan vignette didayakan dalam tetapan projek
2. Periksa model kamera dengan betul dikesan
3. Hubungi Sokongan Mapir jika Vignetting berterusan

### Isu: Warna atau nilai yang salah

** Kemungkinan Punca: **

* Tiada sasaran penentukuran yang dikesan
* Model sasaran penentukuran yang salah dipilih
* Penentukuran reflektif dilumpuhkan
* Imej sasaran berkualiti rendah

** Penyelesaian: **

1. Sahkan penentukuran refleksi diaktifkan
2. Periksa mesej "sasaran dijumpai" dalam log debug
3. Kaji semula kualiti imej sasaran
4. Mengubah semula dengan sasaran yang betul ditandakan

### Isu: Nilai NDVI kelihatan salah

** Jangkauan NDVI yang diharapkan: **

*** Air, batu, tanah **: -0.1 hingga 0.2
** tumbuh -tumbuhan yang jarang/tidak sihat **: 0.2 hingga 0.4
*** Vegetasi sederhana **: 0.4 hingga 0.6
*** tumbuh -tumbuhan yang sihat dan padat **: 0.6 hingga 0.9

** Jika nilai berada di luar julat ini: **

1. Sahkan penentukuran refleksi digunakan
2. Sahkan log sensor cahaya dimasukkan
3. Periksa sasaran penentukuran dikesan
4. Pastikan model kamera yang betul dikesan
5. Mengkaji sasaran Imej Tangkapan Masa dan Syarat

***

## Menggunakan gambar yang diproses anda

### untuk penciptaan photogrammetry / orthomosaik

** aliran kerja yang disyorkan: **

1. ** Import imej refleksi yang dikalibrasi ** ke dalam perisian photogrammetry:
   * Pix4dmapper
   * Metashape Agisoft
   * Dronedeploy
   * Webodm
2. ** Simpan EXIF ​​Metadata **: Pastikan data GPS dipelihara untuk geotagging
3. ** aliran kerja yang dikalibrasi **: Gunakan imej refleksi untuk ketepatan saintifik
4. ** Proses Indeks Mosaik **: Buat NDVI Orthomosaik dari Imej Indeks Individu
5. ** Eksport GeoTiff GeoTiff **: Untuk digunakan dalam aplikasi GIS

### untuk analisis GIS

** aliran kerja yang disyorkan: **

1. ** Beban ke QGIS, ArcGIS, atau serupa **
2. ** Gunakan imej refleksi 16-bit ** untuk analisis berbilang band
3. ** Gunakan Imej Indeks ** (NDVI, NDRE) sebagai lapisan tumbuh-tumbuhan yang sedia digunakan
4. ** Kalkulator Raster **: Campurkan Band untuk Analisis Kustom
5. ** Eksport **: Buat peta klasifikasi, pengesanan perubahan, peta kesihatan tumbuh -tumbuhan

### untuk analisis / pelaporan langsung

** aliran kerja yang disyorkan: **

1. ** Gunakan imej indeks dengan warna lut ** untuk laporan visual
2. ** Statistik Ekstrak **: Mean NDVI per medan/plot
3. ** Siri Masa **: Bandingkan indeks di pelbagai sesi
4. ** Menjana laporan **: Sertakan peta, statistik, dan visualisasi

***

## mengarkib dan sandaran

### Strategi sandaran yang disyorkan

** Apa yang hendak disimpan: **

*✅ ** Imej RAW/JPG Asal ** - Arkib pada pemacu/awan berasingan
*✅ ** output diproses ** - Pastikan imej dan indeks yang dikalibrasi
*✅ ** Fail Projek ** - Mengandungi semua tetapan untuk pemprosesan semula jika diperlukan
*✅ ** Log Debug ** - Dokumen Pemprosesan Butiran
*✅ ** Imej sasaran penentukuran ** - untuk pengesahan dan pemprosesan semula

** Cadangan Penyimpanan: **

*** sandaran segera **: cakera keras luaran
*** Arkib Jangka Panjang **: Penyimpanan Awan (Google Drive, Dropbox, dan lain-lain)
*** Data Kritikal **: Simpan 2-3 salinan di lokasi yang berbeza

***

## Pemprosesan seterusnya berjalan

### menggunakan semula tetapan projek

Sekiranya memproses dataset yang serupa pada masa akan datang:

1. ** Simpan Templat Projek ** (jika belum selesai)
2. ** Buat projek baru ** Menggunakan templat yang disimpan
3. ** Import imej baru **
4. ** Proses ** dengan tetapan yang sama untuk konsistensi

### Batch memproses pelbagai sesi

Untuk pelbagai sesi/dataset:

** Pilihan 1: GUI - Pelbagai Projek **

* Buat projek berasingan untuk setiap sesi
* Gunakan tetapan templat yang konsisten
* Proses satu demi satu

** Pilihan 2: CLI CLI (chloros+ sahaja) **

* Automatikkan pemprosesan batch
* Proses pelbagai folder dengan skrip
* Lihat [Dokumentasi CLI] (../ Cli.md)

** Pilihan 3: Python SDK (chloros+ sahaja) **

* Kawalan Programatik
* Integrasi dengan saluran paip analisis
* Lihat [Dokumentasi API] (../ API-PYTHON-SDK.MD)

***

## Penyelesaian masalah selepas pemprosesan

### pemprosesan semula dengan tetapan yang berbeza

Sekiranya keputusan tidak memuaskan:

1. Simpan gambar asal (tidak pernah padam)
2. Buka projek yang sama di kloros
3. Laraskan Tetapan dalam Panel Tetapan Projek
4. Proses lagi - Output akan menimpa hasil sebelumnya

### Memproses subset gambar

Untuk memproses semula gambar tertentu:

1. Buat projek baru
2. Import hanya imej yang memerlukan pemprosesan semula
3. Gunakan templat tetapan yang sama
4. Proses dataset yang lebih kecil

### Mendapatkan bantuan

Sekiranya anda menghadapi masalah:

*📧 ** e -mel **: info@mapir.camera (termasuk log debug)
*🌐 ** Sokongan **: [https://www.mapir.camera/community/contact n(https://www.mapir.camera/community/contact)
*📚 ** FAQ **: [Soalan Lazim] (../ FAQ.MD)
*📖 ** Dokumentasi **: [Manual Chloros] (../)

***

## Ringkasan: Aliran kerja lengkap

Anda kini telah menyelesaikan aliran kerja pemprosesan kloros penuh:

1. ✅ ** Projek yang dicipta ** - Lihat [Projek] (../ Projects.md)
2. ✅ ** tambah fail **-lihat [menambah fail] (menambah fail-to-a-project.md)
3. ✅ ** Tetapan diselaraskan **-Lihat [Menyesuaikan Tetapan Projek] (menyesuaikan-projek-penstabil.md)
4. ✅ ** sasaran bertanda **-lihat [memilih imej sasaran] (memilih sasaran-imej.md)
5. ✅ ** Memperkenalkan pemprosesan **-Lihat [Memulakan Pemprosesan] (permulaan-pemproses.md)
6. ✅ ** Memantau Kemajuan **-Lihat [Memantau Pemprosesan] (Pemantauan-The-Processing.md)
7. ✅ ** Hasil Semakan ** - Halaman ini

** Imej multispektral yang dikalibrasi dan refleksi anda siap untuk analisis! **

***

## Sumber tambahan

### Ciri -ciri lanjutan

*[** Image Viewer **] (../ image-viewer-gui/pembukaan-an-image-full-screen.md)-visualisasi dan analisis interaktif
*[** indeks/lut sandbox **] (../ image-viewer-gui/index-lut-sandbox.md)-ujian indeks tersuai
*[** Formula Indeks Multispectral **] (../ Project-Settings/Multispectral-index-Formulas.md)-Rujukan Indeks Lengkap

### Automasi & Integrasi

*[** Dokumentasi CLI **] (../ Cli.md) - Pemprosesan Batch Baris Perintah
*[** python sdk **] (../ api-python-sdk.md)-automasi programatik
*[** chloros+ ciri **] (../# chloros) - Keupayaan pemprosesan lanjutan

### Sokongan & Pembelajaran

*[** faq **] (../ faq.md) - Soalan biasa dijawab
*[** sasaran penentukuran **] (../ penentukuran-targets.md) - Memahami penentukuran refleksi
*[** Kamera yang disokong **] (../ disokong-cameras.md) - Perkakasan yang serasi
