# Menyesuaikan tetapan projek

Sebelum memproses imej anda, penting untuk mengkonfigurasi tetapan projek anda untuk memenuhi keperluan aliran kerja anda. Tetapan Projek <IMG SRC = "..

## Mengakses Tetapan Projek

1. Buka projek anda di kloros
2. Klik tetapan projek ** ** <img src="../. Gitbook/assets/icon_project-settings.jpg" alt="" data-size="line"> ikon di bar sisi kiri
3. Panel Tetapan Projek memaparkan semua pilihan konfigurasi

{% hint style="info" %}
** Tetapan disimpan secara automatik ** dengan projek anda. Apabila anda membuka semula projek, semua tetapan dipulihkan.
{ % endhint %}

***

## Persediaan cepat untuk aliran kerja biasa

### Tetapan lalai (disyorkan untuk kebanyakan pengguna)

Untuk aliran kerja kamera Mapir Survey3 biasa, tetapan lalai berfungsi dengan baik:

*✅ ** pembetulan vignette **: diaktifkan
*✅ ** Penentukuran Refleksi **: Diaktifkan (memerlukan imej sasaran Mapir)
*✅ ** kaedah debayer **: berkualiti tinggi (lebih cepat)
*✅ ** Format Eksport **: TIFF (16-bit)

Hanya mengimport imej anda dan mula memproses dengan lalai ini.

***

## Gambaran keseluruhan tetapan projek

Panel Tetapan Projek dianjurkan ke dalam beberapa kategori. Berikut adalah ringkasan setiap bahagian. Untuk dokumentasi lengkap, lihat [Tetapan Projek](../ Project-Settings/Project-Settings.md).

### Pengesanan sasaran

Mengawal bagaimana kloros mengenal pasti sasaran penentukuran dalam imej anda.

** Tetapan utama: **

* **Kawasan sampel penentukuran minimum**: ambang saiz untuk pengesanan sasaran (lalai: 25 piksel)
* **kluster sasaran minimum**: ambang persamaan untuk mengumpulkan kawasan sasaran (lalai: 60)

** Bila hendak menyesuaikan: **

* Meningkatkan kawasan sampel jika mendapat pengesanan palsu
* Kurangkan jika sasaran tidak dikesan
* Laraskan clustering jika sasaran dibahagikan kepada pelbagai pengesanan

### Pemprosesan

Pilihan pemprosesan imej utama dan penentukuran.

** Tetapan utama: **

* **Pembetulan Vignette**: Mengimbangi lensa gelap di tepi ✅ disyorkan
* **Penentukuran Refleksi**: Menormalkan nilai menggunakan sasaran penentukuran ✅ Disyorkan
* **kaedah debayer**: Algoritma untuk menukar mentah ke 3-channels multi-spectral
* **Selang pengubahsuaian minimum**: Masa antara menggunakan sasaran penentukuran (0 = Gunakan semua)

** Tetapan Lanjutan: **

* **Offset Zon Time Sensor Light**: Untuk Penyegerakan Masa PPK (Lalai: 0)
* **Memohon pembetulan PPK**: Menggunakan data pin GPS/pendedahan dari fail .daq
* **PIN Pendedahan 1/2**: Menetapkan kamera ke pin pendedahan untuk persediaan dwi-kamera

### indeks (indeks multispektral)

Konfigurasikan indeks tumbuh -tumbuhan yang hendak dikira dan dieksport.

** Cara menambah indeks: **

1. Klik ** "Tambah Indeks" ** Butang
2. Pilih indeks dari menu dropdown (NDVI, NDRE, GNDVI, dll.)
3. Konfigurasikan tetapan visualisasi (warna LUT, julat nilai)
4. Tambah indeks berganda seperti yang diperlukan

** indeks popular: **

* **ndvi**: Kesihatan tumbuh -tumbuhan umum (yang paling biasa)
* **ndre**: Pengesanan tekanan awal dengan rededge
* **gndvi**: kepekatan klorofil sensitif
* **osavi**: berfungsi dengan baik dengan tanah yang kelihatan
* **Evi**: Kawasan Indeks Kawasan Daun Tinggi (LAI)

** Formula tersuai (kloros+ sahaja): **

* Buat formula indeks multispektral tersuai
* Gunakan matematik band dengan semua saluran imej
* Simpan formula tersuai untuk digunakan semula

Untuk semua indeks dan formula yang ada, lihat [Formula Indeks Multispectral](../ Project-Settings/Multispectral-Index-Formulas.md).

### Eksport

Mengawal format dan kualiti fail output.

** Format yang ada: **

* **TIFF (16-bit)**: Disyorkan untuk analisis GIS dan saintifik (0-65,535 julat)
* **TIFF (32-bit, peratus)**: Nilai pemantulan terapung (0.0-1.0 julat)
* **png (8-bit)**: mampatan tanpa kehilangan untuk visualisasi (julat 0-255)
* **jpg (8-bit)**: fail terkecil, mampatan lossy (julat 0-255)

***

## Menyimpan dan memuatkan tetapan

### Simpan templat projek

Buat templat yang boleh diguna semula untuk aliran kerja yang konsisten:

1. Konfigurasikan semua tetapan yang dikehendaki dalam panel Tetapan Projek
2. Tatal ke ** "Simpan Templat Projek" ** Seksyen di bahagian bawah
3. Masukkan nama templat deskriptif (mis., "Survey3n \ _rgn \ _agriculture")
4. Klik ikon Simpan

** Faedah: **

* Memohon tetapan yang sama di pelbagai projek
* Kongsi konfigurasi dengan ahli pasukan
* Mengekalkan konsistensi untuk tinjauan berulang

### Templat beban pada projek baru

Semasa membuat projek baru:

1. Pilih ** "Projek Baru" ** dari Menu Utama
2. Pilih ** "Beban Dari Templat" ** Pilihan
3. Pilih templat yang disimpan
4. Semua tetapan digunakan secara automatik

### Direktori kerja

** "Simpan Folder Projek" ** Menetapkan Menentukan Di mana Projek Baru Dibuat Secara Lalai:

* **Lokasi lalai**: `c: \ users [username] \ chloros projects`
* **Tukar Lokasi**: Klik edit ikon dan pilih folder baru
* **bila hendak berubah**:
  * Pemacu rangkaian untuk kerjasama pasukan
  * Pemacu yang berbeza dengan lebih banyak ruang penyimpanan
  * Struktur folder teratur mengikut tahun/pelanggan

***

## ppk (kinematik pasca diproses)

Jika menggunakan perakam Mapir Daq dengan GPS untuk geolokasi yang tepat:

### Prasyarat

* Modul Mapir Daq dengan GPS (GNSS)
* .daq fail log dengan entri pin pendedahan
* Kamera yang disambungkan ke pin pendedahan DAQ semasa sesi penangkapan

### Langkah Konfigurasi

1. Letakkan fail log .daq dalam folder projek anda
2. Dalam Tetapan Projek, aktifkan ** "Gunakan pembetulan PPK" ** kotak semak
3. Tetapkan ** "Offset Zon Time Sensor Cahaya" ** Jika diperlukan (lalai: 0 untuk UTC)
4. Berikan kamera ke pin pendedahan:
   *** Kamera tunggal **: Secara automatik ditugaskan ke pin 1
   *** Dua kamera **: Secara manual berikan setiap kamera untuk membetulkan pin

** Tugasan PIN Pendedahan: **

* **PIN PENDEDAHAN 1**: Pilih Model Kamera dari Dropdown
* **PIN PENDEDAHAN 2**: Pilih Kamera Kedua atau "Jangan Gunakan"
* Kamera yang sama tidak dapat diberikan kepada kedua -dua pin

{% hint style="warning" %}
** PENTING **: Pin pendedahan mesti ditugaskan dengan betul ke kamera masing -masing. Tugasan yang tidak betul akan mengakibatkan data geolokasi yang salah.
{ % endhint %}

***

## Senario lanjutan

### Projek Multi-Camera

Semasa memproses imej dari pelbagai kamera Mapir dalam satu projek:

1. Kloros secara automatik mengesan setiap model kamera
2. Setiap kamera mendapat profil pemprosesan yang sesuai
3. PPK: Berikan secara manual setiap kamera untuk membetulkan pin pendedahan
4. Semua kamera menggunakan format dan indeks eksport yang sama

** Contoh **: survey3w rgn + survey3n ocn dual-camera rig

### tinjauan masa atau pelbagai tarikh

Untuk tinjauan berulang di kawasan yang sama dari masa ke masa:

1. Buat templat dengan tetapan standard anda
2. Gunakan persediaan sasaran penentukuran yang konsisten setiap sesi
3. Proses setiap tarikh sebagai projek berasingan
4. Gunakan tetapan yang sama untuk hasil yang setanding
5. Eksport dalam format yang sama untuk analisis temporal

### dataset besar

Untuk projek dengan banyak imej (500+):

* Pertimbangkan untuk memecah projek yang lebih kecil mengikut tarikh atau kawasan
* Gunakan kloros+ pemprosesan selari untuk hasil yang lebih cepat
* Pertimbangkan CLI atau API untuk automasi batch
* Laraskan selang pengubahsuaian minimum untuk mengurangkan masa pengesanan sasaran

***

## Mengesahkan tetapan anda

Sebelum mula memproses, semak tetapan utama ini:

* [] Model kamera dikesan dengan betul dalam penyemak imbas fail
* [] Pembetulan vignette diaktifkan
* [] Penentukuran refleksi diaktifkan
* [] Sekurang -kurangnya satu imej sasaran penentukuran yang diimport
* [] Indeks multispektral yang dikehendaki ditambah
* [] Format eksport sesuai untuk aliran kerja anda
* [] Tetapan PPK dikonfigurasi (jika menggunakan .daq dengan peristiwa expure)

***

## Langkah seterusnya

Setelah tetapan anda dikonfigurasikan:

1. ** Tandakan imej sasaran penentukuran **-lihat [memilih imej sasaran](memilih sasaran-imej.md)
2. ** Mula Pemprosesan **-Lihat [Memulakan Pemprosesan](permulaan-pemproses.md)
3. ** Memantau Kemajuan **-Lihat [Memantau pemprosesan](Pemantauan-The-Processing.md)

Untuk butiran lengkap mengenai semua tetapan yang tersedia, lihat dokumentasi rujukan [Projek-Settings/Project-Settings.md) [Project Settings/Project-Settings.md).
