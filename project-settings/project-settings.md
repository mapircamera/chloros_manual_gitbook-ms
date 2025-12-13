# Tetapan Projek

The Chloros <IMG Src = ".. Tetapan ini disimpan dengan projek anda dan boleh disimpan sebagai templat untuk digunakan semula di pelbagai projek.

## akses ke tetapan projek

Untuk mengakses tetapan projek:

1. Buka projek di kloros
2. Klik Tetapan Projek ** ** <img src = "../. Gitbook/Asset/icon_Project-Settings.jpg" alt = "" Data-Size = "Line"> di bar sisi kiri.
3. Panel Tetapan akan memaparkan semua pilihan tetapan yang tersedia yang dianjurkan oleh kategori.

***

## Pengesanan sasaran

Tetapan ini mengawal bagaimana kloros mengesan dan memproses sasaran penentukuran dalam imej anda.

### Kawasan sampel penentukuran minimum (PX)

*** Jenis **: Nombor
*** julat **: 0 hingga 10,000 piksel
*** lalai **: 25 piksel
*** Penerangan **: Menetapkan kawasan minimum (dalam piksel) yang diperlukan untuk rantau yang dikesan untuk dianggap sebagai sampel yang sah dari sasaran penentukuran. Nilai yang lebih kecil akan mengesan sasaran yang lebih kecil, tetapi boleh meningkatkan positif palsu. Nilai yang lebih besar memerlukan kawasan sasaran yang lebih besar dan lebih jelas untuk pengesanan.
*** Bila hendak menyesuaikan **:
  * Meningkatkan jika anda mendapat pengesanan palsu pada artifak imej kecil.
  * Kurangkan jika sasaran penentukuran anda kelihatan kecil dalam imej anda dan tidak dikesan.

### Pengumpulan sasaran minimum (0-100)

*** Jenis **: Nombor.
*** Range **: Dari 0 hingga 100.
*** lalai **: 60.
*** Penerangan **: Mengawal ambang kumpulan ke kawasan kumpulan warna yang sama apabila mengesan sasaran penentukuran. Nilai yang lebih tinggi memerlukan lebih banyak warna yang serupa untuk dikumpulkan bersama, menghasilkan pengesanan sasaran yang lebih konservatif. Nilai yang lebih rendah membolehkan variasi warna yang lebih besar dalam kumpulan sasaran.
*** Bila hendak menyesuaikan **:
  * Meningkatkan jika sasaran penentukuran dibahagikan kepada pelbagai pengesanan.
  * Kurangkan jika sasaran penentukuran dengan variasi warna tidak dikesan sepenuhnya.

***

## Pemprosesan

Tetapan ini mengawal bagaimana kloros memproses dan menentukur imej anda.

### pembetulan vignetting

*** Jenis **: kotak semak
*** Lalai **: didayakan (diperiksa)
*** Penerangan **: Memohon pembetulan vignetting untuk mengimbangi lensa gelap di tepi imej. Vignetting adalah fenomena optik biasa di mana sudut dan tepi imej kelihatan lebih gelap daripada pusat kerana ciri -ciri lensa.
*** Bila hendak melumpuhkan **: Lumpuhkan pilihan ini hanya jika kombinasi kamera dan lensa telah menggunakan pembetulan vignetting atau jika anda ingin membetulkan secara manual vignetting dalam pemprosesan pasca.

### refleksi/penentukuran keseimbangan putih

*** Jenis **: kotak semak
*** Lalai **: didayakan (diperiksa)
*** Penerangan **: Membolehkan penentukuran pemantulan automatik menggunakan sasaran penentukuran yang dikesan dalam imej. Ini menormalkan nilai refleksi di seluruh set data dan memastikan pengukuran yang konsisten tanpa mengira keadaan pencahayaan.
*** Bila hendak melumpuhkan **: Lumpuhkan hanya jika anda ingin memproses imej yang tidak diselaraskan atau jika anda menggunakan aliran kerja penentukuran yang berbeza.

### Kaedah demosaik

*** Jenis **: Pemilihan dropdown
*** Pilihan **:
  * Berkualiti tinggi (lebih cepat) - pada masa ini satu -satunya pilihan yang tersedia
*** Lalai **: Kualiti Tinggi (lebih cepat)
*** Penerangan **: Pilih algoritma demosaik yang digunakan untuk menukar data sensor corak bayer mentah ke dalam imej warna penuh. Kaedah "berkualiti tinggi (lebih cepat)" menyediakan keseimbangan optimum antara kelajuan pemprosesan dan kualiti imej.
*** Nota **: Kaedah debayer tambahan boleh ditambah dalam versi kloros masa depan.

### selang pengubahsuaian minimum

*** Jenis **: Nombor
*** Julat **: 0 hingga 3600 saat
*** lalai **: 0 saat
*** Penerangan **: Menetapkan selang waktu minimum (dalam saat) antara menggunakan sasaran penentukuran. Apabila ditetapkan kepada 0, kloros akan menggunakan semua sasaran penentukuran yang dikesan. Apabila ditetapkan kepada nilai yang lebih tinggi, kloros hanya akan menggunakan sasaran penentukuran yang dipisahkan oleh sekurang -kurangnya bilangan saat ini, mengurangkan masa pemprosesan untuk set data dengan penangkapan sasaran penentukuran yang kerap.
*** Bila hendak menyesuaikan **:
  * Tetapkan nilai kepada 0 untuk ketepatan penentukuran maksimum apabila keadaan pencahayaan berbeza -beza.
  * Meningkatkan nilai (contohnya, hingga 60-300 saat) untuk mempercepatkan pemprosesan apabila pencahayaan adalah imej sasaran penentukuran yang tetap dan kerap tersedia.

### lag masa sensor cahaya

*** Jenis **: Nombor
*** julat **: -12 hingga +12 jam
*** lalai **: 0 jam
*** Penerangan **: Menentukan perbezaan masa (dalam jam dari UTC) untuk cap waktu data sensor cahaya. Digunakan semasa memproses fail data PPK (pasca diproses kinematik) untuk memastikan penyegerakan yang betul antara penangkapan imej dan data GPS.
*** Bila untuk menyesuaikan **: Tetapkan nilai ini ke perbezaan masa zon waktu tempatan anda jika data PPK anda menggunakan masa tempatan dan bukannya UTC. Contohnya:
  * Masa Pasifik: -8 atau -7 (bergantung pada waktu penjimatan siang)
  * Waktu Timur: -5 atau -4 (bergantung pada waktu penjimatan siang)
  * Masa Eropah Tengah: +1 atau +2 (bergantung pada waktu penjimatan siang)

### memohon pembetulan ppk

*** Jenis **: kotak semak
*** Lalai **: Dilumpuhkan (tidak terkawal)
*** Penerangan **: Membolehkan penggunaan pembetulan kinematik pasca (PPK) perakam Daq Mapir yang mengandungi GPS (GNSS). Apabila diaktifkan, kloros akan menggunakan sebarang fail log .daq yang mengandungi data pin pendedahan dalam direktori projek anda dan memohon pembetulan geolokasi yang tepat pada imej anda.
*** Keperluan **: mesti ada fail log .daq dengan entri pin pendedahan dalam direktori projek
*** Bila Mengaktifkan **: Adalah disyorkan untuk sentiasa mengaktifkan pembetulan PPK jika terdapat entri maklum balas pendedahan dalam fail log .daq.

### pin pendedahan 1

*** Jenis **: Pemilihan dropdown
*** Keterlihatan **: Hanya kelihatan apabila "memohon pembetulan PPK" diaktifkan dan data pendedahan tersedia untuk pin 1
*** Pilihan **:
  * Nama model kamera yang dikesan dalam projek
  * "Jangan gunakan": Abaikan pin pendedahan ini
*** lalai **: dipilih secara automatik berdasarkan tetapan projek
*** Keterangan **: Menetapkan kamera khusus untuk pin pendedahan 1 untuk penyegerakan masa PPK. PIN pendedahan merekodkan momen yang tepat pengatup kamera dikeluarkan, yang penting untuk geolokasi PPK yang tepat.
*** tingkah laku pemilihan automatik **:
  * Kamera tunggal + pin tunggal: Secara automatik pilih kamera
  * Kamera tunggal + dua pin: pin 1 secara automatik diberikan ke kamera
  * Kamera berganda: pemilihan manual diperlukan

### pin pendedahan 2

*** Jenis **: Pemilihan dropdown
*** Keterlihatan **: Hanya dapat dilihat apabila "memohon pembetulan PPK" diaktifkan dan data pendedahan tersedia untuk pin 2
*** Pilihan **:
  * Nama model kamera yang dikesan dalam projek
  * "Jangan gunakan": Abaikan pin pendedahan ini
*** Default ** - dipilih secara automatik berdasarkan tetapan projek
*** Keterangan **: Menetapkan kamera khusus untuk pin pendedahan 2 untuk penyegerakan masa PPK apabila menggunakan persediaan kamera dwi.
*** tingkah laku pemilihan automatik **:
  * Kamera tunggal + pin tunggal: pin 2 ditetapkan secara automatik ke "Jangan gunakan"
  * Ruang tunggal + dua pin: pin 2 secara automatik ditetapkan untuk "jangan gunakan"
  * Kamera berganda: pemilihan manual diperlukan
*** NOTA **: Kamera yang sama tidak boleh ditugaskan untuk pin 1 dan pin 2 secara serentak.

***

## indeks

Tetapan ini membolehkan anda mengkonfigurasi indeks multispektral untuk analisis dan paparan.

### Tambah indeks

*** Jenis **: Panel Konfigurasi Indeks Khas
*** Penerangan **: Membuka panel interaktif di mana anda boleh memilih dan mengkonfigurasi indeks tumbuh -tumbuhan multispektral (NDVI, NDRE, EVI, dll.) Untuk mengira semasa pemprosesan imej. Anda boleh menambah pelbagai indeks, masing -masing dengan tetapan paparan sendiri.
*** indeks yang ada **: Sistem ini merangkumi lebih daripada 30 indeks multispektral yang telah ditetapkan, termasuk:
  * NDVI (Indeks Vegetasi Perbezaan Normal)
  * Ndre (perbezaan normal rededge)
  * EVI (Indeks Vegetasi Dipertingkatkan)
  * GNDVI, SAVI, OSAVI, MSAVI2
  * Dan banyak lagi (lihat [formula indeks multispectral] (multispectral-index-formula.md) untuk senarai penuh)
*** Ciri **:
  * Pilih dari formula indeks yang telah ditetapkan
  * Konfigurasikan kecerunan warna paparan (LUT - jadual carian)
  * Tetapkan nilai ambang untuk analisis
  * Buat formula indeks tersuai

### Formula tersuai (chloros+ ciri)

*** Jenis **: Arahan definisi formula tersuai
*** Penerangan **: Membolehkan anda membuat dan menyimpan formula indeks multispektral tersuai menggunakan matematik band. Formula tersuai disimpan dengan tetapan projek anda dan boleh digunakan seperti indeks terbina dalam.
*** Cara membuat **:
  1. Dalam panel Tetapan Indeks, cari pilihan formula tersuai.
  2. Tentukan formula anda menggunakan pengenal band (contohnya, NIR, merah, hijau, biru).
  3. Simpan formula dengan nama deskriptif.
*** Sintaks Formula **: Operasi matematik standard disokong, termasuk:
  * Aritmetik: ___inline0000___, ___inline0001___, ___inline0002___, ___inline0003___.
  * Kurungan untuk pesanan operasi
  * Rujukan Band: NIR, Merah, Hijau, Biru, Rededge, Cyan, Orange, NIR1, NIR2

***

## Eksport

Tetapan ini mengawal format dan kualiti imej diproses yang dieksport.

### Format imej yang dikalibrasi

*** Jenis **: Pemilihan dropdown
*** Pilihan **:
  *** TIFF (16-bit) **: Format TIFF 16-bit yang tidak dikompresi
  *** TIFF (32-bit, peratusan) **: 32-bit tiff dengan titik terapung dan peratusan nilai refleksi
  *** png (8-bit) **-8-bit format png termampat.
  *** JPG (8-bit) **: 8-bit format jpeg termampat.
*** Lalai **: TIFF (16-bit).
*** Penerangan **: Pilih format fail untuk menyimpan imej yang diproses dan ditentukur.
*** Cadangan Format **:
  *** TIFF (16-bit) **: Disyorkan untuk analisis saintifik dan aliran kerja profesional. Memelihara kualiti data maksimum tanpa artifak mampatan. Ideal untuk analisis multispektral dan pemprosesan pasca dalam perisian GIS.
  *** TIFF (32-bit, peratusan) **: Ideal untuk aliran kerja yang memerlukan nilai refleksi dalam bentuk peratusan (0-100%). Menawarkan ketepatan maksimum untuk pengukuran radiometrik.
  *** PNG (8-bit) **: Ideal untuk tontonan web dan tontonan umum. Saiz fail yang lebih kecil dengan mampatan lossless, tetapi mengurangkan julat dinamik.
  *** JPG (8-bit) **: Saiz fail yang lebih kecil, sesuai untuk pratonton dan tontonan web sahaja. Ia menggunakan mampatan lossy, yang tidak sesuai untuk analisis saintifik.

***

## Simpan templat projek

Ciri ini membolehkan anda menyimpan tetapan projek semasa anda sebagai templat yang boleh diguna semula.

*** Jenis **: Input Teks + Butang Simpan
*** Penerangan **: Masukkan nama deskriptif untuk templat konfigurasi anda dan klik ikon Simpan. Templat ini akan menyimpan semua tetapan semasa projek anda (pengesanan sasaran, pilihan pemprosesan, indeks, dan format eksport) untuk digunakan semula dengan mudah dalam projek masa depan.
*** Gunakan Kes **:
  * Buat templat untuk sistem kamera yang berbeza (RGB, Multispectral, NIR)
  * Simpan tetapan standard untuk jenis tanaman tertentu atau aliran kerja analisis
  * Kongsi tetapan yang konsisten di seluruh pasukan
*** Cara menggunakannya **:
  1. Konfigurasikan semua tetapan projek yang anda mahukan
  2. Masukkan nama untuk templat (contohnya, "Rededge Survey3 NDVI Standard").
  3. Klik ikon Simpan.
  4. Sekarang templat boleh dimuatkan apabila membuat projek baru.

***

## Simpan Folder Projek

Tetapan ini menentukan di mana projek baru disimpan secara lalai.

*** Jenis **: Paparan Laluan Direktori + Butang Edit
*** lalai **: ___inline0004___
*** Penerangan **: Menunjukkan direktori lalai semasa di mana projek -projek kloros baru dibuat. Klik ikon Edit untuk memilih direktori yang berbeza.
*** Bila hendak mengubahnya **:
  * Sediakan pemacu rangkaian untuk kerjasama pasukan.
  * Beralih ke pemacu dengan lebih banyak ruang penyimpanan untuk set data yang besar.
  * Mengatur projek mengikut tahun, pelanggan, atau jenis projek dalam folder yang berbeza.
*** Nota **: Menukar tetapan ini hanya mempengaruhi projek baru. Projek sedia ada kekal di lokasi asalnya.

***

## Konfigurasi Konfigurasi

Semua tetapan projek disimpan secara automatik dengan fail projek (__inline0005___ format projek). Apabila anda membuka semula projek, semua tetapan akan dipulihkan tepat seperti yang anda tinggalkan.

### Tetapan Hierarki

Tetapan digunakan mengikut urutan berikut:

1. ** Sistem lalai **: lalai terbina dalam yang ditakrifkan oleh kloros
2. ** Tetapan templat **: Sekiranya anda memuatkan templat semasa membuat projek
3. ** Tetapan projek disimpan **: Tetapan disimpan dengan fail projek
4. ** Tetapan Manual **: Sebarang perubahan yang anda buat semasa sesi semasa

### persediaan dan pemprosesan gambar

Kebanyakan tetapan berubah (terutamanya dalam kategori rendering dan eksport) akan menyebabkan imej diproses semula untuk mencerminkan tetapan baru. Walau bagaimanapun, sesetengah tetapan adalah "eksport sahaja" dan tidak memerlukan pemprosesan segera:

* Simpan templat projek
* Direktori kerja
* Format imej yang ditentukur (digunakan semasa mengeksport)

***

## Amalan terbaik

1. ** Mulakan dengan lalai ** - Tetapan lalai berfungsi dengan baik untuk kebanyakan sistem kamera Mapir dan aliran kerja biasa.
2. ** Buat templat ** - Sebaik sahaja anda telah mengoptimumkan tetapan untuk aliran kerja atau kamera tertentu, simpannya sebagai templat untuk memastikan konsistensi antara projek.
3. ** Ujian sebelum pemprosesan penuh **: Apabila bereksperimen dengan tetapan baru, uji mereka pada subset kecil imej sebelum memproses keseluruhan set data.
4. ** Dokumen tetapan anda **: Gunakan nama templat deskriptif yang menunjukkan sistem kamera, jenis pemprosesan, dan penggunaan yang dimaksudkan (contohnya, "SURVEY3 \ _RGB \ _NDVI \ _AGRICULTURE").
5. ** Pemilihan Format Eksport **: Pilih format eksport berdasarkan penggunaan akhir:
   * Analisis Saintifik → TIFF (16 bit atau 32 bit)
   * Pemprosesan GIS → TIFF (16 bit)
   * Pandangan Cepat → PNG (8 bit)
   * Kongsi di Web → JPG (8 bit)

***

Untuk maklumat lanjut mengenai indeks multispektral di kloros, lihat halaman [formula indeks multispectral] (multispectral-index-formula.md).