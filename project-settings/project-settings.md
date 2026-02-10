# Tetapan Projek

Bar sisi Tetapan Projek <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> dalam Chloros membolehkan anda mengkonfigurasi semua aspek pemprosesan imej, pengesanan sasaran penentukuran, pilihan pengekstrakan berbilang spektrum, pengekstrakan projek. Tetapan ini disimpan dengan projek anda dan boleh disimpan sebagai templat untuk digunakan semula merentas berbilang projek.

## Mengakses Tetapan Projek

Untuk mengakses Tetapan Projek:

1. Buka projek dalam Chloros
2. Klik tab **Tetapan Projek** <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> di bar sisi kiri
3. Panel tetapan akan memaparkan semua pilihan konfigurasi tersedia yang disusun mengikut kategori

***

## Pengesanan Sasaran

Tetapan ini mengawal cara Chloros mengesan dan memproses sasaran penentukuran dalam imej anda.

### Kawasan sampel penentukuran minimum (px)

* **Jenis**: Nombor
* **Julat**: 0 hingga 10,000 piksel
* **Lalai**: 25 piksel
* **Penerangan**: Menetapkan kawasan minimum (dalam piksel) yang diperlukan untuk kawasan yang dikesan untuk dianggap sebagai sampel sasaran penentukuran yang sah. Nilai yang lebih kecil akan mengesan sasaran yang lebih kecil tetapi boleh meningkatkan positif palsu. Nilai yang lebih besar memerlukan kawasan sasaran yang lebih besar dan lebih jelas untuk pengesanan.
* **Bila untuk melaraskan**:
  * Tingkatkan jika anda mendapat pengesanan palsu pada artifak imej kecil
  * Kurangkan jika sasaran penentukuran anda kelihatan kecil dalam imej anda dan tidak dikesan

### Pengelompokan Sasaran Minimum (0-100)

* **Jenis**: Nombor
* **Julat**: 0 hingga 100
* **Lalai**: 60
* **Penerangan**: Mengawal ambang pengelompokan untuk mengumpulkan kawasan berwarna serupa apabila mengesan sasaran penentukuran. Nilai yang lebih tinggi memerlukan lebih banyak warna serupa untuk dikumpulkan bersama, menghasilkan pengesanan sasaran yang lebih konservatif. Nilai yang lebih rendah membolehkan lebih banyak variasi warna dalam kumpulan sasaran.
* **Bila untuk melaraskan**:
  * Tingkatkan jika sasaran penentukuran dipecahkan kepada berbilang pengesanan
  * Kurangkan jika sasaran penentukuran dengan variasi warna tidak dikesan sepenuhnya

***

## Pemprosesan

Tetapan ini mengawal cara Chloros memproses dan menentukur imej anda.

### Pembetulan vignet

* **Jenis**: Kotak semak
* **Lalai**: Didayakan (ditandai)
* **Penerangan**: Menggunakan pembetulan vignet untuk mengimbangi kegelapan kanta di tepi imej. Vignetting ialah fenomena optik biasa di mana sudut dan tepi imej kelihatan lebih gelap daripada bahagian tengah kerana ciri kanta.
* **Bila untuk melumpuhkan**: Hanya lumpuhkan jika kombinasi kamera/kanta anda telah menggunakan pembetulan vignet, atau jika anda ingin membetulkan vignetting secara manual dalam pemprosesan pasca.

### Penentukuran pantulan / imbangan putih

* **Jenis**: Kotak semak
* **Lalai**: Didayakan (ditandai)
* **Penerangan**: Mendayakan penentukuran pantulan automatik menggunakan sasaran penentukuran yang dikesan dalam imej anda. Ini menormalkan nilai pantulan merentas set data anda dan memastikan pengukuran yang konsisten tanpa mengira keadaan pencahayaan.
* **Bila untuk melumpuhkan**: Lumpuhkan hanya jika anda ingin memproses imej mentah, tidak ditentukur atau jika anda menggunakan aliran kerja penentukuran yang berbeza.

### Kaedah debayer

* **Jenis**: Pilihan lungsur turun
* **Pilihan**:
  * Standard (Pantas, Kualiti Sederhana)
  * Sedar Tekstur (Lambat, Kualiti Tertinggi) \[Chloros+]
* **Lalai**: Standard (Pantas, Kualiti Sederhana)
* **Penerangan**: Memilih algoritma demosaicing yang digunakan untuk menukar data penderia corak Bayer mentah kepada imej berwarna penuh. Kaedah "Standard (Pantas, Kualiti Sederhana)" menyediakan keseimbangan optimum antara kelajuan pemprosesan dan kualiti imej. "Texture Aware (Slow, Highest Quality)" \[Chloros+] menggunakan debayer sedar tepi berkualiti tinggi digabungkan dengan model denoising AI/ML yang menghilangkan hampir semua hingar debayering. Model Texture Aware memerlukan memori GPU (VRAM) untuk dijalankan. Kami mengesyorkan menggunakannya apabila anda mempunyai >4GB VRAM tersedia untuk pemprosesan yang lebih pantas.
* **Nota**: Kaedah debayer tambahan boleh ditambah dalam versi masa depan Chloros.

### Selang penentukuran semula minimum

* **Jenis**: Nombor
* **Julat**: 0 hingga 3,600 saat
* **Lalai**: 0 saat
* **Penerangan**: Menetapkan selang masa minimum (dalam saat) antara menggunakan sasaran penentukuran. Apabila ditetapkan kepada 0, Chloros akan menggunakan setiap sasaran penentukuran yang dikesan. Apabila ditetapkan kepada nilai yang lebih tinggi, Chloros hanya akan menggunakan sasaran penentukuran yang dipisahkan sekurang-kurangnya beberapa saat ini, mengurangkan masa pemprosesan untuk set data dengan tangkapan sasaran penentukuran yang kerap.
* **Bila untuk melaraskan**:
  * Tetapkan kepada 0 untuk ketepatan penentukuran maksimum apabila keadaan pencahayaan berbeza-beza
  * Tingkatkan (cth., kepada 60-300 saat) untuk pemprosesan yang lebih pantas apabila pencahayaan konsisten dan anda mempunyai imej sasaran penentukuran yang kerap

### Zon waktu penderia cahaya mengimbangi

* **Jenis**: Nombor
* **Julat**: -12 hingga +12 jam
* **Lalai**: 0 jam
* **Penerangan**: Menentukan offset zon waktu (dalam jam dari UTC) untuk cap waktu data sensor cahaya. Ini digunakan semasa memproses fail data PPK (Post-Processed Kinematic) untuk memastikan penyegerakan masa yang betul antara tangkapan imej dan data GPS.
* **Masa untuk melaraskan**: Tetapkan ini kepada zon waktu setempat anda diimbangi jika data PPK anda menggunakan waktu tempatan dan bukannya UTC. Contohnya:
  * Waktu Pasifik: -8 atau -7 (bergantung pada DST)
  * Waktu Timur: -5 atau -4 (bergantung kepada DST)
  * Waktu Eropah Tengah: +1 atau +2 (bergantung pada DST)

### Gunakan pembetulan PPK

* **Jenis**: Kotak semak
* **Lalai**: Dilumpuhkan (tidak ditandai)
* **Penerangan**: Mendayakan penggunaan pembetulan Post-Processed Kinematic (PPK) daripada perakam MAPIR DAQ yang mengandungi GPS (GNSS). Apabila didayakan, Chloros akan menggunakan mana-mana fail log .daq yang mengandungi data pin pendedahan dalam direktori projek anda dan menggunakan pembetulan geolokasi yang tepat pada imej anda.
* **Keperluan**: Fail log .daq dengan entri pin pendedahan mesti ada dalam direktori projek anda
* **Bila untuk mendayakan**: Adalah disyorkan untuk sentiasa mendayakan pembetulan PPK jika anda mempunyai entri maklum balas pendedahan dalam fail log .daq anda.

### Pin Pendedahan 1

* **Jenis**: Pilihan lungsur turun
* **Keterlihatan**: Hanya kelihatan apabila "Gunakan pembetulan PPK" didayakan DAN data pendedahan tersedia untuk Pin 1
* **Pilihan**:
  * Nama model kamera dikesan dalam projek
  * "Jangan Gunakan" - Abaikan pin pendedahan ini
* **Lalai**: Auto-dipilih berdasarkan konfigurasi projek
* **Penerangan**: Berikan kamera khusus kepada Pin Dedahan 1 untuk penyegerakan masa PPK. Pin pendedahan merekodkan masa yang tepat apabila pengatup kamera dicetuskan, yang penting untuk geolokasi PPK yang tepat.
* **Tingkah laku pemilihan automatik**:
  * Kamera tunggal + pin tunggal: Memilih kamera secara automatik
  * Kamera tunggal + dua pin: Pin 1 diperuntukkan secara automatik kepada kamera
  * Berbilang kamera: Pemilihan manual diperlukan

### Pin Pendedahan 2

* **Jenis**: Pilihan lungsur turun
* **Keterlihatan**: Hanya kelihatan apabila "Gunakan pembetulan PPK" didayakan DAN data pendedahan tersedia untuk Pin 2
* **Pilihan**:
  * Nama model kamera dikesan dalam projek
  * "Jangan Gunakan" - Abaikan pin pendedahan ini
* **Lalai**: Auto-dipilih berdasarkan konfigurasi projek
* **Penerangan**: Berikan kamera khusus kepada Pin Pendedahan 2 untuk penyegerakan masa PPK apabila menggunakan persediaan dwi-kamera.
* **Tingkah laku pemilihan automatik**:
  * Kamera tunggal + pin tunggal: Pin 2 ditetapkan secara automatik kepada "Jangan Gunakan"
  * Kamera tunggal + dua pin: Pin 2 ditetapkan secara automatik kepada "Jangan Gunakan"
  * Berbilang kamera: Pemilihan manual diperlukan
* **Nota**: Kamera yang sama tidak boleh ditetapkan pada kedua-dua Pin 1 dan Pin 2 secara serentak.***

## Indeks

Tetapan ini membolehkan anda mengkonfigurasi indeks berbilang spektrum untuk analisis dan visualisasi.

### Tambah indeks

* **Jenis**: Panel konfigurasi indeks khas
* **Penerangan**: Membuka panel interaktif di mana anda boleh memilih dan mengkonfigurasi indeks tumbuh-tumbuhan berbilang spektrum (NDVI, NDRE, EVI, dll.) untuk mengira semasa pemprosesan imej. Anda boleh menambah berbilang indeks, setiap satu dengan tetapan visualisasinya sendiri.
* **Indeks yang tersedia**: Sistem ini termasuk 30+ indeks berbilang spektrum yang dipratakrifkan termasuk:
  * NDVI (Indeks Tumbuhan Perbezaan Normal)
  * NDRE (Perbezaan Normal RedEdge)
  * EVI (Indeks Tumbuhan Dipertingkat)
  * GNDVI, SAVI, OSAVI, MSAVI2
  * Dan banyak lagi (lihat [Formula Indeks Berbilang Spektrum](multispectral-index-formulas.md) untuk senarai lengkap)
* **Ciri**:
  * Pilih daripada formula indeks yang telah ditetapkan
  * Konfigurasikan kecerunan warna visualisasi (LUT - Jadual Carian)
  * Tetapkan nilai ambang untuk analisis
  * Buat formula indeks tersuai

### Formula Tersuai (Ciri Chloros+)

* **Jenis**: Tatasusunan definisi formula tersuai
* **Penerangan**: Membolehkan anda mencipta dan menyimpan formula indeks berbilang spektrum tersuai menggunakan matematik jalur. Formula tersuai disimpan dengan tetapan projek anda dan boleh digunakan sama seperti indeks terbina dalam.
* **Cara mencipta**:
  1. Dalam panel konfigurasi Indeks, cari pilihan formula tersuai
  2. Tentukan formula anda menggunakan pengecam jalur (cth., NIR, Red, Green, Blue)
  3. Simpan formula dengan nama deskriptif
* **Sintaks formula**: Operasi matematik standard disokong, termasuk:
  * Aritmetik: `+`, `-`, `*`, `/`
  * Tanda kurung untuk susunan operasi
  * Rujukan jalur: NIR, Red, Green, Blue, RedEdge, Cyan, Orange0, Orange0, Orange0, Orange0, Orange0, Orange0 NIR2

***

## Eksport

Tetapan ini mengawal format dan kualiti imej yang diproses yang dieksport.

### Format imej yang ditentukur

* **Jenis**: Pilihan lungsur turun
* **Pilihan**:
  * **TIFF (16-bit)** - Format 16-bit TIFF tidak dimampatkan
  * **TIFF (32-bit, Peratus)** - 32-bit titik terapung TIFF dengan nilai pantulan sebagai peratusan
  * **PNG (8-bit)** - Format 8-bit PNG mampat
  * **JPG (8-bit)** - Format 8-bit JPEG mampat
* **Lalai**: TIFF (16-bit)
* **Penerangan**: Memilih format fail untuk menyimpan imej yang diproses dan ditentukur.
* **Syor format**:
  * **TIFF (16-bit)**: Disyorkan untuk analisis saintifik dan aliran kerja profesional. Mengekalkan kualiti data maksimum tanpa artifak mampatan. Terbaik untuk analisis multispektral dan pemprosesan selanjutnya dalam perisian GIS.
  * **TIFF (32-bit, Peratus)**: Terbaik untuk aliran kerja yang memerlukan nilai pantulan sebagai peratusan (0-100%). Menawarkan ketepatan maksimum untuk pengukuran radiometrik.
  * **PNG (8-bit)**: Baik untuk tontonan web dan visualisasi umum. Saiz fail yang lebih kecil dengan pemampatan tanpa kerugian, tetapi mengurangkan julat dinamik.
  * **JPG (8-bit)**: Saiz fail terkecil, terbaik untuk pratonton dan paparan web sahaja. Menggunakan mampatan lossy yang tidak sesuai untuk analisis saintifik.***

## Simpan Templat Projek

Ciri ini membolehkan anda menyimpan tetapan projek semasa anda sebagai templat boleh guna semula.

* **Jenis**: Input teks + Butang Simpan
* **Penerangan**: Masukkan nama deskriptif untuk templat tetapan anda dan klik ikon simpan. Templat akan menyimpan semua tetapan projek semasa anda (pengesanan sasaran, pilihan pemprosesan, indeks dan format eksport) untuk mudah digunakan semula dalam projek masa hadapan.
* **Kes penggunaan**:
  * Cipta templat untuk sistem kamera yang berbeza (RGB, multispektral, NIR)
  * Simpan konfigurasi standard untuk jenis tanaman tertentu atau aliran kerja analisis
  * Kongsi tetapan yang konsisten merentas pasukan
* **Cara menggunakan**:
  1. Konfigurasikan semua tetapan projek yang anda inginkan
  2. Masukkan nama templat (cth., "RedEdge Survey3 NDVI Standard")
  3. Klik ikon simpan
  4. Templat kini boleh dimuatkan semasa membuat projek baharu

***

## Simpan Folder Projek

Tetapan ini menentukan tempat projek baharu disimpan secara lalai.

* **Jenis**: Paparan laluan direktori + butang Edit
* **Lalai**: `C:\Users\[Username]\Chloros Projects`
* **Penerangan**: Menunjukkan direktori lalai semasa di mana projek Chloros baharu dicipta. Klik ikon edit untuk memilih direktori lain.
* **Bila untuk menukar**:
  * Tetapkan kepada pemacu rangkaian untuk kerjasama pasukan
  * Tukar kepada pemacu dengan lebih banyak ruang storan untuk set data yang besar
  * Atur projek mengikut tahun, pelanggan atau jenis projek dalam folder yang berbeza
* **Nota**: Menukar tetapan ini hanya mempengaruhi projek BAHARU. Projek sedia ada kekal di lokasi asalnya.***

## Tetapan Kegigihan

Semua tetapan projek disimpan secara automatik dengan fail projek anda (`.mapir` format projek). Apabila anda membuka semula projek, semua tetapan dipulihkan sama seperti anda meninggalkannya.

### Hierarki Tetapan

Tetapan digunakan dalam susunan berikut:

1. **Kelalaian sistem** - Kelalaian terbina dalam ditakrifkan oleh Chloros
2. **Tetapan templat** - Jika anda memuatkan templat semasa membuat projek
3. **Tetapan projek disimpan** - Tetapan disimpan dengan fail projek
4. **Pelarasan manual** - Sebarang perubahan yang anda buat semasa sesi semasa

### Tetapan dan Pemprosesan Imej

Kebanyakan perubahan tetapan (terutamanya dalam kategori Pemprosesan dan Eksport) akan mencetuskan pemprosesan semula imej untuk mencerminkan tetapan baharu. Walau bagaimanapun, sesetengah tetapan adalah "eksport sahaja" dan tidak memerlukan pemprosesan semula segera:

* Simpan Templat Projek
* Direktori Kerja
* Format imej yang ditentukur (digunakan semasa mengeksport)

***

## Amalan Terbaik

1. **Mulakan dengan lalai**: Tetapan lalai berfungsi dengan baik untuk kebanyakan sistem kamera MAPIR dan aliran kerja biasa.
2. **Buat templat**: Setelah anda mengoptimumkan tetapan untuk aliran kerja atau kamera tertentu, simpannya sebagai templat untuk memastikan konsistensi merentas projek.
3. **Uji sebelum pemprosesan penuh**: Apabila bereksperimen dengan tetapan baharu, uji pada subset kecil imej sebelum memproses keseluruhan set data anda.
4. **Dokumenkan tetapan anda**: Gunakan nama templat deskriptif yang menunjukkan sistem kamera, jenis pemprosesan dan penggunaan yang dimaksudkan (cth., "Survey3\_RGB\_NDVI\_Agriculture").
5. **Pemilihan format eksport**: Pilih format eksport anda berdasarkan penggunaan akhir anda:
   * Analisis saintifik → TIFF (16-bit atau 32-bit)
   * Pemprosesan GIS → TIFF (16-bit)
   * Visualisasi pantas → PNG (8-bit)
   * Perkongsian web → JPG (8-bit)

***

Untuk mendapatkan maklumat lanjut tentang indeks berbilangspek dalam Chloros, lihat halaman [Formula Indeks Berbilangspektrum](multispectral-index-formulas.md).