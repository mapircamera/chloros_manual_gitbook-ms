# Penyiapan pemprosesan

Sebaik sahaja Chloros telah menyelesaikan pemprosesan, sudah tiba masanya untuk mengkaji semula hasilnya, periksa kualiti output, dan sediakan imej yang diproses untuk digunakan dalam alur kerja. Halaman ini membimbing anda melalui langkah terakhir dan tindakan seterusnya.

## Pemprosesan petunjuk selesai

Apabila pemprosesan berjaya selesai, anda akan melihat beberapa petunjuk:

*✅ ** Bar Progress **: Jangkau 100% Penyiapan
*✅ ** Log Debug ** - Menunjukkan mesej "Pemprosesan Selesai"
*✅ ** Butang Laman Utama ** - Dibolehkan semula (siap untuk dijalankan seterusnya)
*✅ ** Output Files **: Semua imej yang diproses disimpan dalam subfolder model kamera

***

## Lokasi gambar yang diproses

### Membuka folder output

1. Klik ** menu utama ** <img src = "../. Gitbook/aset/imej (1) (1) .png" alt = "" data-size = "line"> ikon (kiri atas).
2. Pilih ** «Folder Projek Terbuka» **.
3. Penjelajah fail akan dibuka dalam direktori projek.
4. Cari projek anda dengan nama.

***

## Ulasan gambar yang diproses

### Pratonton cepat dalam Fail Explorer

** Pratonton Bersepadu di Windows: **

1. Navigasi ke subfolder model kamera.
2. Pilih fail imej.
3. Pratonton akan muncul dalam panel pratonton Windows Explorer.
4. Gunakan kekunci anak panah untuk menavigasi imej.

### Pratonton dalam penonton imej luaran

** Penonton yang disyorkan: **

*** QGIS **: Perisian GIS percuma (sesuai untuk analisis multispektral georeferenced).
*** Irfanview ** - Penonton imej yang cepat dan ringan (Tiff serasi).
*** Adobe Photoshop **: Penyuntingan Profesional (Tiff Serasi).
*** GIMP **: Alternatif percuma untuk Photoshop.
*** Foto Windows **: Paparan Asas (mungkin tidak menyokong tiff 16-bit).

### Pratonton dalam penonton imej kloros

Gunakan penonton imej terbina dalam di kloros untuk visualisasi lanjutan:

1. Klik imej gambar kecil dalam penjelajah fail.
2. Imej dibuka di kawasan pratonton utama.
3. Klik ** Image Viewer ** <img src = "../. Gitbook/aset/icon_image-viewer.jpg" alt = "" data-size = "line"> di bar sisi kiri.
4. Gunakan [Indeks/LUT Sandbox] (../ Image-Viewer-Gui/Index-Lut-Sandbox.md) untuk melakukan analisis interaktif.

Lihat [Image Viewer] (../ Image-Viewer-Gui/Opening-An-Image-full-screen.md) untuk arahan terperinci.

***

## ulasan log debug

### periksa amaran atau kesilapan

1. Buka log debug ** <img src = "../. Gitbook/aset/icon_log.jpg" alt = "" data-size = "line"> tab.
2. Tatal melalui mesej.
3. Cari amaran kuning atau kesilapan merah.
4. Mengkaji sebarang masalah yang dikesan.
5. Hubungi Mapir Helpdesk untuk mendapatkan bantuan.

### Simpan rekod

Untuk menyimpan rekod pemprosesan atau hantar ke Mapir Helpdesk:

1. Klik ** «Copy» ** atau ** «Muat turun» ** butang.
2. Simpan fail sebagai fail teks dalam folder projek.
3. Sertakan dokumentasi projek.
4. Hantar ke Sokongan Mapir jika anda menemui sebarang masalah.

***

## Masalah dan penyelesaian output biasa

### Masalah: fail output yang hilang

** Kemungkinan Punca: **

* Fail tidak memenuhi kriteria pemprosesan.
* Sasaran hanya imej (dikecualikan daripada eksport).
* Ruang cakera habis semasa eksport.
* Rasuah fail semasa pemprosesan.

** Penyelesaian: **

1. Semak log debug untuk mesej peninggalan/ralat.
2. Periksa bahawa terdapat ruang cakera yang cukup.
3. Mengira fail: Mereka mesti sepadan (kiraan asal - kiraan destinasi) × (indeks + 1).
4. Mengimport semula dan memproses fail yang hilang.

### Masalah: tepi gelap atau cerah (vignetting masih kelihatan)

** Kemungkinan Punca: **

* Pembetulan vignetting dilumpuhkan.
* Kamera/kanta tidak termasuk dalam pangkalan data profil kloros.
* Vignetting melampau yang melebihi kapasiti pembetulan.

** Penyelesaian: **

1. Sahkan bahawa pembetulan vignetting didayakan dalam tetapan projek.
2. Periksa bahawa model kamera telah dikesan dengan betul.
3. Hubungi Sokongan Mapir jika vignetting berterusan.

### Masalah: warna atau nilai yang salah

** Kemungkinan Punca: **

*Tiada sasaran penentukuran yang dikesan.
* Model sasaran penentukuran yang salah telah dipilih.
* Penentukuran refleksi dilumpuhkan.
*Imej sasaran berkualiti rendah.

** Penyelesaian: **

1. Semak bahawa penentukuran refleksi diaktifkan.
2. Semak mesej "sasaran yang dijumpai" dalam log debug.
3. Semak kualiti imej kanta.
4. Mengubah semula dengan sasaran yang sesuai.

### Masalah: Nilai NDVI kelihatan tidak betul.

** Jangkauan NDVI yang diharapkan: **

*** Air, batu, tanah **: dari -0.1 hingga 0.2.
*** tumbuh -tumbuhan miskin/tidak sihat **: 0.2 hingga 0.4.
*** Vegetasi sederhana **: dari 0.4 hingga 0.6.
*** tumbuh -tumbuhan yang sihat dan padat **: dari 0.6 hingga 0.9.

** Jika nilai berada di luar julat ini: **

1. Sahkan bahawa penentukuran refleksi telah digunakan.
2. Sahkan bahawa log sensor cahaya telah dimasukkan.
3. Sahkan bahawa sasaran penentukuran telah dikesan.
4. Pastikan model kamera yang betul telah dikesan.
5. Mengkaji masa dan syarat menangkap imej sasaran.

***

## Penggunaan imej yang diproses

### untuk photogrammetry/orthomosaik ciptaan

** aliran kerja yang disyorkan: **

1. ** Import imej refleksi yang dikalibrasi ** ke dalam perisian photogrammetry:
   * Pix4dmapper
   * Metashape Agisoft
   * Dronedeploy
   * Webodm
2. ** Memelihara EXIF ​​Metadata ** - Memastikan data GPS dipelihara untuk geotagging.
3. ** aliran kerja yang dikalibrasi ** - Gunakan pengimejan refleksi untuk ketepatan saintifik.
4. ** Proses Indeks Mosaik **: Buat NDVI Orthomosaics dari Imej Indeks Individu.
5. ** Eksport GeoTiff Geotiff **: Untuk digunakan dalam aplikasi GIS.

### untuk analisis GIS

** aliran kerja yang disyorkan: **

1. ** Muat naik ke QGIS, ArcGIS atau serupa **.
2. ** Gunakan imej refleksi 16-bit ** untuk analisis multiband.
3. ** Gunakan Imej Indeks ** (NDVI, NDRE) sebagai lapisan tumbuh-tumbuhan yang sedia untuk digunakan.
4. ** Kalkulator Raster **: Campurkan band untuk analisis tersuai.
5. ** Eksport **: Buat peta klasifikasi, perubahan pengesanan dan tumbuh -tumbuhan peta kesihatan.

### untuk analisis/pelaporan langsung

** aliran kerja yang disyorkan: **

1. ** Gunakan imej indeks dengan warna LUT ** untuk pelaporan visual.
2. ** Statistik Ekstrak **: Purata NDVI setiap bidang/plot.
3. ** Siri Masa **: Bandingkan indeks antara beberapa sesi.
4. ** Menjana laporan **: Sertakan peta, statistik dan visualisasi.

***

## Pengarkiban dan sandaran

### Strategi sandaran yang disyorkan

** Apa yang hendak disimpan: **

*✅ ** RAW/JPG Imej Asal **: Arkib ke pemacu/awan berasingan.
*✅ ** Hasil yang diproses ** - Simpan imej dan indeks yang dikalibrasi
*✅ ** Fail Projek ** - Mengandungi semua tetapan untuk diproses semula jika perlu
*✅ ** Log Debug ** - Dokumen Pemprosesan Butiran
*✅ ** imej penentukuran **: untuk pengesahan dan pemprosesan semula

** Cadangan Penyimpanan: **

*** sandaran segera **: cakera keras luaran
*** Arkib Jangka Panjang **: Penyimpanan Awan (Google Drive, Dropbox, dan lain-lain)
*** Data Kritikal **-Simpan 2-3 salinan di lokasi yang berbeza

***

## Pemprosesan yang akan datang berjalan

### menggunakan semula konfigurasi projek

Jika anda akan memproses set data yang serupa pada masa akan datang:

1. ** Simpan templat projek ** (jika anda belum lagi)
2. ** Buat projek baru ** Menggunakan templat yang disimpan
3. ** Import imej baru **
4. ** Proses ** dengan tetapan yang sama untuk mengekalkan konsistensi

### pemprosesan batch multi-sesi

Untuk pelbagai sesi/set data:

** Pilihan 1: GUI - Pelbagai Projek **

* Buat projek berasingan untuk setiap sesi
* Gunakan konfigurasi templat yang konsisten
* Proses satu demi satu

** Pilihan 2: CLI CLI (chloros+ sahaja) **

* Automatikkan pemprosesan batch.
* Proses pelbagai folder dengan skrip.
* Lihat [Dokumentasi CLI] (../ Cli.md)

** Pilihan 3: Python SDK (chloros+ sahaja) **

* Kawalan Programatik
* Integrasi dengan proses analisis
* Lihat [Dokumentasi API] (../ API-PYTHON-SDK.MD)

***

## Penyelesaian masalah selepas pemprosesan

### diproses semula dengan tetapan yang berbeza

Sekiranya hasilnya tidak memuaskan:

1. Simpan gambar asal (tidak pernah memadamkannya)
2. Buka projek yang sama di kloros
3. Laraskan tetapan dalam panel Tetapan Projek
4. Reprocess - Hasilnya akan menimpa yang sebelumnya

### memproses subset gambar

Untuk memproses semula gambar tertentu:

1. Buat projek baru
2. Import hanya imej yang perlu diproses semula
3. Gunakan templat konfigurasi yang sama
4. Memproses set data yang lebih kecil

### Dapatkan bantuan

Sekiranya anda menghadapi masalah:

*📧 ** Email **: info@mapir.camera (termasuk log debug).
*🌐 ** Bantuan Teknikal **: [https://www.mapir.camera/community/contact n(https://www.mapir.camera/community/contact).
*📚 ** Soalan Lazim **: [Soalan Lazim] (../ FAQ.MD)
*📖 ** Dokumentasi **: [Manual Chloros] (../)

***

## Ringkasan: Aliran kerja lengkap

Anda kini telah menyelesaikan keseluruhan aliran kerja pemprosesan kloros:

1. ✅ ** Projek Dibuat **: Lihat [Projek] (../ Projects.md)
2. ✅ ** tambah fail **-lihat [tambah fail] (menambah fail-to-a-project.md)
3. ✅ ** Tetapan diselaraskan **-Lihat [Menyesuaikan Tetapan Projek] (menyesuaikan-projek-penstabil.md)
4. ✅ ** Sasaran yang diperiksa **: lihat [memilih imej sasaran] (memilih sasaran-imej.md)
5. ✅ ** Mula Pemprosesan **: Lihat [Memulakan Pemprosesan] (permulaan-pemproses.md)
6. ✅ ** Memantau Kemajuan **: Lihat [Memantau pemprosesan] (Pemantauan-The-Processing.md)
7. ✅ ** Hasil yang disemak **: Halaman ini

** Imej multispektral yang dikalibrasi dan refleksi anda siap untuk analisis! **

***

## Sumber tambahan

### Ciri -ciri lanjutan

*[** Image Viewer **] (../ image-viewer-gui/pembukaan-an-image-full-screen.md): visualisasi interaktif dan analisis.
*[** Indeks Sandbox/Lut **] (../ Image-Viewer-Gui/Index-Lut-Sandbox.md): Menguji Indeks Kustom.
*[** Formula Indeks Multispectral **] (../ Project-Settings/Multispectral-index-Formulas.md): Rujukan Indeks Lengkap

### Automasi dan integrasi

*[** Dokumentasi CLI **] (../ cli.md): pemprosesan batch dari baris arahan
*[** python sdk **] (../ api-python-sdk.md)-automasi programatik
*[** chloros+ ciri **] (../# chloros) - Keupayaan pemprosesan lanjutan

### Sokongan dan Pembelajaran

*[** Soalan Lazim **] (../ FAQ.MD) - Jawapan kepada Soalan Biasa
*[** sasaran penentukuran **] (../ penentukuran targets.md) - Memahami penentukuran refleksi
*[** Kamera yang disokong **] (../ disokong-cameras.md) - Perkakasan yang disokong