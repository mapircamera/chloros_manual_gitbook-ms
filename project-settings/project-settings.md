# Tetapan Projek

Tetapan Projek <img src=".. Tetapan ini disimpan dengan projek anda dan boleh disimpan sebagai templat untuk digunakan semula di pelbagai projek.

## Mengakses Tetapan Projek

Untuk mengakses tetapan projek:

1. Buka projek di kloros
2. Klik tetapan projek ** **<img src="../. Gitbook/assets/icon_project-settings.jpg" alt="" data-size="line"> di bar sisi kiri
3. Panel Tetapan akan memaparkan semua pilihan konfigurasi yang disediakan oleh kategori***

## Pengesanan sasaran

Tetapan ini mengawal bagaimana kloros mengesan dan memproses sasaran penentukuran dalam imej anda.

### Kawasan sampel penentukuran minimum (PX)

* **Jenis**: Nombor
* **julat**: 0 hingga 10,000 piksel
* **lalai**: 25 piksel
* **Penerangan**: Menetapkan kawasan minimum (dalam piksel) yang diperlukan untuk rantau yang dikesan untuk dianggap sebagai sampel sasaran penentukuran yang sah. Nilai yang lebih kecil akan mengesan sasaran yang lebih kecil tetapi boleh meningkatkan positif palsu. Nilai yang lebih besar memerlukan kawasan sasaran yang lebih besar dan lebih jelas untuk pengesanan.
* **Bila hendak menyesuaikan**:
  * Meningkatkan jika anda mendapat pengesanan palsu pada artifak imej kecil
  * Kurangkan jika sasaran penentukuran anda kelihatan kecil dalam imej anda dan tidak dikesan

### clustering sasaran minimum (0-100)

* **Jenis**: Nombor
* **julat**: 0 hingga 100
* **lalai**: 60
* **Penerangan**: Mengawal ambang kluster untuk mengumpulkan kawasan berwarna yang sama apabila mengesan sasaran penentukuran. Nilai yang lebih tinggi memerlukan lebih banyak warna yang serupa untuk dikumpulkan bersama, menghasilkan pengesanan sasaran yang lebih konservatif. Nilai yang lebih rendah membolehkan lebih banyak variasi warna dalam kumpulan sasaran.
* **Bila hendak menyesuaikan**:
  * Meningkatkan jika sasaran penentukuran dibahagikan kepada pelbagai pengesanan
  * Kurangkan jika sasaran penentukuran dengan variasi warna tidak dikesan sepenuhnya

***

## Pemprosesan

Tetapan ini mengawal bagaimana kloros memproses dan menentukur imej anda.

### pembetulan vignette

* **Jenis**: kotak semak
* **Lalai**: didayakan (diperiksa)
* **Penerangan**: Memohon pembetulan vignette untuk mengimbangi lensa gelap di tepi imej. Vignetting adalah fenomena optik biasa di mana sudut dan tepi imej kelihatan lebih gelap daripada pusat kerana ciri -ciri lensa.
* **Bila hendak melumpuhkan**: Hanya melumpuhkan jika kombinasi kamera/kanta anda telah menggunakan pembetulan vignette, atau jika anda ingin membetulkan vignetting secara manual dalam pemprosesan pasca.

### penentukuran refleksi / keseimbangan putih

* **Jenis**: kotak semak
* **Lalai**: didayakan (diperiksa)
* **Penerangan**: Membolehkan penentukuran pemantulan automatik menggunakan sasaran penentukuran yang dikesan dalam imej anda. Ini menormalkan nilai refleksi merentasi dataset anda dan memastikan pengukuran yang konsisten tanpa mengira keadaan pencahayaan.
* **Bila hendak melumpuhkan**: Lumpuhkan hanya jika anda ingin memproses imej mentah, tidak teralibrasi atau jika anda menggunakan aliran kerja penentukuran yang berbeza.

### kaedah debayer

* **Jenis**: Pemilihan dropdown
* **Pilihan**:
  * Berkualiti tinggi (lebih cepat) - Pada masa ini satu -satunya pilihan yang tersedia
* **Lalai**: Kualiti Tinggi (lebih cepat)
* **Keterangan**: Memilih algoritma demosaiking yang digunakan untuk menukar data sensor corak bayer mentah ke dalam imej warna penuh. Kaedah "berkualiti tinggi (lebih cepat)" menyediakan keseimbangan optimum antara kelajuan pemprosesan dan kualiti imej.
* **Nota**: Kaedah debayer tambahan boleh ditambah dalam versi kloros masa depan.

### selang pengubahsuaian minimum

* **Jenis**: Nombor
* **Range**: 0 hingga 3,600 saat
* **lalai**: 0 saat
* **Penerangan**: Menetapkan selang waktu minimum (dalam saat) antara menggunakan sasaran penentukuran. Apabila ditetapkan kepada 0, kloros akan menggunakan setiap sasaran penentukuran yang dikesan. Apabila ditetapkan kepada nilai yang lebih tinggi, kloros hanya akan menggunakan sasaran penentukuran yang dipisahkan oleh sekurang -kurangnya ini banyak saat, mengurangkan masa pemprosesan untuk dataset dengan penangkapan sasaran penentukuran yang kerap.
* **Bila hendak menyesuaikan**:
  * Tetapkan ke 0 untuk ketepatan penentukuran maksimum apabila keadaan pencahayaan berbeza -beza
  * Peningkatan (mis., Hingga 60-300 saat) untuk pemprosesan lebih cepat apabila pencahayaan konsisten dan anda mempunyai imej sasaran penentukuran yang kerap

### Offset zon sensor cahaya

* **Jenis**: Nombor
* **julat**: -12 hingga +12 jam
* **lalai**: 0 jam
* **Penerangan**: Menentukan Offset Zon Time (dalam jam dari UTC) untuk cap waktu data sensor cahaya. Ini digunakan apabila memproses fail data PPK (pasca diproses kinematik) untuk memastikan penyegerakan masa yang betul antara penangkapan imej dan data GPS.
* **Bila hendak menyesuaikan**: Tetapkan ini ke zon waktu setempat anda diimbangi jika data PPK anda menggunakan masa tempatan dan bukannya UTC. Contohnya:
  * Masa Pasifik: -8 atau -7 (bergantung kepada DST)
  * Waktu Timur: -5 atau -4 (bergantung kepada DST)
  * Masa Eropah Tengah: +1 atau +2 (bergantung kepada DST)

### memohon pembetulan ppk

* **Jenis**: kotak semak
* **Lalai**: Dilumpuhkan (tidak terkawal)
* **Penerangan**: Membolehkan penggunaan pembetulan kinematik pasca (PPK) dari perakam Mapir DAQ yang mengandungi GPS (GNSS). Apabila diaktifkan, kloros akan menggunakan sebarang fail log .daq yang mengandungi data pin pendedahan dalam direktori projek anda dan memohon pembetulan geolokasi yang tepat pada imej anda.
* **Keperluan**: .daq fail log dengan entri pin pendedahan mesti hadir di direktori projek anda
* **Bila Mengaktifkan**: Adalah disyorkan untuk sentiasa mengaktifkan pembetulan PPK jika anda mempunyai entri maklum balas pendedahan dalam fail log .daq anda.

### pin pendedahan 1

* **Jenis**: Pemilihan dropdown
* **Keterlihatan**: Hanya kelihatan apabila "memohon pembetulan PPK" diaktifkan dan data pendedahan tersedia untuk pin 1
* **Pilihan**:
  * Nama model kamera yang dikesan dalam projek
  * "Jangan gunakan" - Abaikan pin pendedahan ini
* **lalai**: dipilih secara automatik berdasarkan konfigurasi projek
* **Keterangan**: Menetapkan kamera khusus kepada pin pendedahan 1 untuk penyegerakan masa PPK. PIN pendedahan merekodkan masa yang tepat apabila pengatup kamera dicetuskan, yang penting untuk geolokasi PPK yang tepat.
* **tingkah laku pemilihan automatik**:
  * Kamera tunggal + pin tunggal: memilih kamera secara automatik
  * Kamera tunggal + dua pin: pin 1 secara automatik diberikan ke kamera
  * Kamera berganda: pemilihan manual diperlukan

### pin pendedahan 2

* **Jenis**: Pemilihan dropdown
* **Keterlihatan**: Hanya kelihatan apabila "memohon pembetulan PPK" diaktifkan dan data pendedahan tersedia untuk pin 2
* **Pilihan**:
  * Nama model kamera yang dikesan dalam projek
  * "Jangan gunakan" - Abaikan pin pendedahan ini
* **lalai**: dipilih secara automatik berdasarkan konfigurasi projek
* **Keterangan**: Menetapkan kamera khusus untuk pin pendedahan 2 untuk penyegerakan masa PPK apabila menggunakan persediaan dwi-kamera.
* **tingkah laku pemilihan automatik**:
  * Kamera tunggal + pin tunggal: pin 2 ditetapkan secara automatik ke "Jangan gunakan"
  * Kamera tunggal + dua pin: pin 2 ditetapkan secara automatik ke "Jangan gunakan"
  * Kamera berganda: pemilihan manual diperlukan
* **NOTA**: Kamera yang sama tidak boleh diberikan kepada PIN 1 dan PIN 2 serentak.***

## indeks

Tetapan ini membolehkan anda mengkonfigurasi indeks multispektral untuk analisis dan visualisasi.

### Tambah indeks

* **Jenis**: Panel Konfigurasi Indeks Khas
* **Penerangan**: Membuka panel interaktif di mana anda boleh memilih dan mengkonfigurasi indeks tumbuh -tumbuhan multispektral (NDVI, NDRE, EVI, dll.) Untuk mengira semasa pemprosesan imej. Anda boleh menambah pelbagai indeks, masing -masing dengan tetapan visualisasi sendiri.
* **indeks yang tersedia**: Sistem ini merangkumi 30+ indeks multispektral yang telah ditetapkan termasuk:
  * NDVI (Indeks Vegetasi Perbezaan Normal)
  * Ndre (perbezaan normal rededge)
  * EVI (Indeks Vegetasi Dipertingkatkan)
  * GNDVI, SAVI, OSAVI, MSAVI2
  * Dan banyak lagi (lihat [formula indeks multispectral](multispectral-index-formula.md) untuk senarai lengkap)
* **Ciri**:
  * Pilih dari formula indeks yang telah ditetapkan
  * Konfigurasikan kecerunan warna visualisasi (LUT - jadual paparan)
  * Tetapkan nilai ambang untuk analisis
  * Buat formula indeks tersuai

### Formula tersuai (chloros+ ciri)

* **Jenis**: Arahan definisi formula tersuai
* **Penerangan**: Membolehkan anda membuat dan menyimpan formula indeks multispektral tersuai menggunakan matematik band. Formula tersuai disimpan dengan tetapan projek anda dan boleh digunakan seperti indeks terbina dalam.
* **Cara membuat**:
  1. Dalam panel konfigurasi indeks, cari pilihan formula tersuai
  2. Tentukan formula anda menggunakan pengenal band (mis., NIR, merah, hijau, biru)
  3. Simpan formula dengan nama deskriptif
* **Sintaks Formula**: Operasi matematik standard disokong, termasuk:
  * Aritmetik: `+`, `-`,`* `,`/`
  * Kurungan untuk pesanan operasi
  * Rujukan Band: NIR, Merah, Hijau, Biru, Rededge, Cyan, Orange, NIR1, NIR2

***

## Eksport

Tetapan ini mengawal format dan kualiti imej diproses yang dieksport.

### Format imej yang dikalibrasi

* **Jenis**: Pemilihan dropdown
* **Pilihan**:***TIFF (16-bit)**-Format TIFF 16-bit yang tidak dikompresi***TIFF (32-bit, peratus)**-Tiff terapung 32-bit dengan nilai refleksi sebagai peratusan***png (8-bit)**-Format PNG 8-bit yang dimampatkan***jpg (8-bit)**-format jpeg 8-bit yang dimampatkan
* **Lalai**: TIFF (16-bit)
* **Penerangan**: Memilih format fail untuk menyimpan imej yang diproses dan ditentukur.
* **Cadangan Format**:***TIFF (16-bit)**: Disyorkan untuk analisis saintifik dan aliran kerja profesional. Memelihara kualiti data maksimum tanpa artifak mampatan. Terbaik untuk analisis multispektral dan pemprosesan selanjutnya dalam perisian GIS.***TIFF (32-bit, peratus)**: Terbaik untuk aliran kerja yang memerlukan nilai refleksi sebagai peratusan (0-100%). Menawarkan ketepatan maksimum untuk pengukuran radiometrik.***PNG (8-bit)**: Baik untuk tontonan web dan visualisasi umum. Saiz fail yang lebih kecil dengan mampatan lossless, tetapi mengurangkan julat dinamik.***JPG (8-bit)**: Saiz fail terkecil, terbaik untuk pratonton dan paparan web sahaja. Menggunakan mampatan lossy yang tidak sesuai untuk analisis saintifik.***

## Simpan templat projek

Ciri ini membolehkan anda menyimpan tetapan projek semasa anda sebagai templat yang boleh diguna semula.

* **Jenis**: Input Teks + Butang Simpan
* **Penerangan**: Masukkan nama deskriptif untuk templat tetapan anda dan klik ikon Simpan. Templat ini akan menyimpan semua tetapan projek semasa anda (pengesanan sasaran, pilihan pemprosesan, indeks, dan format eksport) untuk digunakan semula dengan mudah dalam projek masa depan.
* **Gunakan Kes**:
  * Buat templat untuk sistem kamera yang berbeza (RGB, Multispectral, NIR)
  * Simpan konfigurasi standard untuk jenis tanaman tertentu atau aliran kerja analisis
  * Kongsi tetapan yang konsisten di seluruh pasukan
* **Cara menggunakan**:
  1. Konfigurasikan semua tetapan projek yang anda inginkan
  2. Masukkan nama templat (mis., "Rededge Survey3 NDVI Standard")
  3. Klik ikon Simpan
  4. Templat kini boleh dimuatkan semasa membuat projek baru

***

## Simpan Folder Projek

Tetapan ini menentukan di mana projek baru disimpan secara lalai.

* **Jenis**: Paparan Laluan Direktori + Butang Edit
* **Lalai**: `C: \ Users [Nama Pengguna] \ Projek Chloros`
* **Penerangan**: Menunjukkan direktori lalai semasa di mana projek -projek kloros baru dibuat. Klik ikon Edit untuk memilih direktori yang berbeza.
* **bila hendak berubah**:
  * Tetapkan ke pemacu rangkaian untuk kerjasama pasukan
  * Tukar ke pemacu dengan lebih banyak ruang penyimpanan untuk dataset yang besar
  * Mengatur projek mengikut tahun, pelanggan, atau jenis projek dalam folder yang berbeza
* **Nota**: Menukar tetapan ini hanya mempengaruhi projek baru. Projek sedia ada kekal di lokasi asalnya.***

## Tetapan kegigihan

Semua tetapan projek disimpan secara automatik dengan fail projek anda (format projek `.mapir`). Apabila anda membuka semula projek, semua tetapan dipulihkan tepat seperti yang anda tinggalkan.

### Tetapan Hierarki

Tetapan digunakan mengikut urutan berikut:

1.**Default System**- Default terbina dalam yang ditakrifkan oleh kloros
2.**Tetapan templat**- Sekiranya anda memuatkan templat semasa membuat projek
3.**Tetapan projek yang disimpan**- Tetapan disimpan dengan fail projek
4.**Pelarasan Manual** - Sebarang perubahan yang anda buat semasa sesi semasa

### Tetapan dan pemprosesan imej

Kebanyakan tetapan berubah (terutamanya dalam kategori pemprosesan dan eksport) akan mencetuskan pemprosesan semula imej untuk mencerminkan tetapan baru. Walau bagaimanapun, sesetengah tetapan adalah "eksport sahaja" dan tidak memerlukan pemprosesan segera:

* Simpan templat projek
* Direktori kerja
* Format imej yang ditentukur (terpakai semasa mengeksport)

***

## Amalan terbaik

1.**Mulakan dengan lalai**: Tetapan lalai berfungsi dengan baik untuk kebanyakan sistem kamera Mapir dan aliran kerja biasa.
2.**Buat templat**: Sebaik sahaja anda telah mengoptimumkan tetapan untuk aliran kerja atau kamera tertentu, simpannya sebagai templat untuk memastikan konsistensi merentasi projek.
3.**Ujian sebelum pemprosesan penuh**: Apabila bereksperimen dengan tetapan baru, uji pada subset kecil imej sebelum memproses seluruh dataset anda.
4.**Dokumen tetapan anda**: Gunakan nama templat deskriptif yang menunjukkan sistem kamera, jenis pemprosesan, dan penggunaan yang dimaksudkan (mis., "SURVEY3 \ _RGB \ _NDVI \ _agriculture").
5.**Pemilihan Format Eksport**: Pilih format eksport anda berdasarkan penggunaan akhir anda:
   * Analisis saintifik → TIFF (16-bit atau 32-bit)
   * Pemprosesan GIS → TIFF (16-bit)
   * Visualisasi cepat → PNG (8-bit)
   * Perkongsian Web → JPG (8-bit)

***

Untuk maklumat lanjut mengenai indeks multispektral di kloros, lihat halaman [Multispectral Index Formula](multispectral-index-formula.md) halaman.
