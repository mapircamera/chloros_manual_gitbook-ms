# Menyesuaikan tetapan projek

Sebelum memproses imej, adalah penting untuk mengkonfigurasi tetapan projek untuk memenuhi keperluan aliran kerja anda. Tetapan Projek <IMG SRC = "..

## akses ke tetapan projek

1. Buka projek anda di kloros
2. Klik tetapan projek ** ** <img src = "../. Gitbook/aset/icon_project-settings.jpg" alt = "" data-size = "line"> ikon di bar sisi kiri
3. Panel Tetapan Projek memaparkan semua pilihan konfigurasi

{% petunjuk gaya = & quot; info & quot; %}
** Tetapan disimpan secara automatik ** dengan projek. Apabila anda membuka semula projek, semua tetapan dipulihkan.
{ % endhint %}

***

## Persediaan cepat untuk aliran kerja biasa

### Tetapan lalai (disyorkan untuk kebanyakan pengguna)

Untuk aliran kerja kamera Mapir Survey3 biasa, tetapan lalai berfungsi dengan baik:

*✅ ** pembetulan vignette **: diaktifkan
*✅ ** penentukuran refleksi **: diaktifkan (memerlukan pengimejan sasaran MAPIR)
*✅ ** kaedah debayer **: berkualiti tinggi (lebih cepat)
*✅ ** Format Eksport **: TIFF (16 bit)

Hanya mengimport imej anda dan mula memprosesnya dengan nilai lalai ini.

***

## Gambaran Keseluruhan Konfigurasi Projek

Panel Tetapan Projek dianjurkan ke dalam beberapa kategori. Berikut adalah ringkasan setiap bahagian. Untuk dokumentasi lengkap, lihat [Tetapan Projek] (../ Project-Settings/Project-Settings.md).

### Pengesanan sasaran

Mengawal bagaimana kloros mengenal pasti sasaran penentukuran dalam imej anda.

** Tetapan utama: **

*** Kawasan sampel penentukuran minimum **: Saiz ambang untuk pengesanan sasaran (lalai: 25 piksel).
*** Clustering Sasaran Minimum **: Ambang Persamaan untuk Kawasan Sasaran Clustering (Lalai: 60).

** Bila hendak menyesuaikan: **

* Meningkatkan kawasan sampel jika anda mendapat pengesanan palsu.
* Kurangkannya jika sasaran tidak dikesan.
* Laraskan kumpulan jika sasaran dibahagikan kepada pelbagai pengesanan.

### Pemprosesan

Pilihan pemprosesan imej utama dan penentukuran.

** Tetapan utama: **

*** pembetulan vignette ** - mengimbangi lensa gelap di tepi ✅ disyorkan
*** penentukuran refleksi ** - menormalkan nilai menggunakan sasaran penentukuran ✅ disyorkan
*** kaedah debayer **: algoritma untuk menukar mentah ke 3-saluran multispectral
*** Selang pengubahsuaian minimum **: Masa antara menggunakan sasaran penentukuran (0 = Gunakan semua)

** Tetapan Lanjutan: **

*** Zon Masa Sensor Cahaya Offset **: Untuk Penyegerakan Masa PPK (Lalai: 0)
*** Memohon Pembetulan PPK **: Menggunakan data pin/GPS pendedahan dari fail .daq
*** PIN PENDEDAHAN 1/2 **: Berikan kamera ke pin pendedahan untuk persediaan kamera dwi

### indeks (indeks multispektral)

Konfigurasikan indeks tumbuh -tumbuhan yang hendak dikira dan dieksport.

** Cara menambah indeks: **

1. Klik butang ** «Tambah» **
2. Pilih indeks dari menu drop-down (NDVI, NDRE, GNDVI, dan lain-lain).
3. Konfigurasi tetapan paparan (warna LUT, julat nilai).
4. Tambah pelbagai indeks seperti yang diperlukan.

** indeks popular: **

*** ndvi **: Kesihatan tumbuh -tumbuhan umum (yang paling biasa).
*** ndre **: Pengesanan tekanan awal dengan Rededge.
*** GNDVI **: Sensitif terhadap kepekatan klorofil.
*** osavi **: berfungsi dengan baik dengan tanah yang kelihatan
*** Evi **: Kawasan dengan Indeks Kawasan Daun Tinggi (LAI)

** Formula tersuai (kloros+ sahaja): **

* Buat formula indeks multispektral tersuai
* Gunakan matematik band dengan semua saluran imej
* Simpan formula tersuai untuk digunakan semula

Untuk melihat semua indeks dan formula yang tersedia, lihat [Formula Indeks Multispectral] (../ Project-Settings/Multispectral-Index-Formulas.md).

### Eksport

Mengawal format dan kualiti fail output.

** Format yang ada: **

*** TIFF (16-bit) **: Disyorkan untuk GIS dan analisis saintifik (julat 0 hingga 65,535).
*** TIFF (32-bit, peratusan) **: Nilai refleksi terapung (julat 0.0 hingga 1.0).
*** PNG (8-bit) **: Mampatan Lossless untuk Paparan (Julat 0 hingga 255).
*** jpg (8-bit) **: fail yang lebih kecil, mampatan lossy (julat 0 hingga 255).

***

## Simpan dan muatkan tetapan

### Simpan templat projek

Buat templat yang boleh diguna semula untuk aliran kerja yang konsisten:

1. Konfigurasikan semua tetapan yang dikehendaki dalam panel Tetapan Projek.
2. Tatal ke bahagian "Simpan Templat Projek" ** di bahagian bawah.
3. Masukkan nama deskriptif untuk templat (contohnya, "Survey3n \ _rgn \ _agriculture").
4. Klik ikon Simpan.

** Kelebihan: **

* Memohon tetapan yang sama pada pelbagai projek.
* Kongsi tetapan dengan ahli pasukan.
* Mengekalkan konsistensi merentasi tinjauan berulang.

### Templat beban ke dalam projek baru

Semasa membuat projek baru:

1. Pilih ** «Projek Baru» ** dari menu utama.
2. Pilih pilihan ** «Beban dari Templat» **.
3. Pilih templat yang disimpan.
4. Semua tetapan akan digunakan secara automatik.

### Direktori kerja

** "Simpan Folder Projek" ** Menetapkan Menentukan Di mana Projek Baru Dibuat Secara Lalai:

*** Lokasi lalai **: ___inline0000___.
*** Tukar Lokasi **: Klik ikon Edit dan pilih folder baru.
*** Bila hendak mengubahnya **:
  * Pemacu rangkaian untuk kerjasama pasukan.
  * Unit yang berbeza dengan lebih banyak ruang penyimpanan.
  * Struktur folder yang dianjurkan oleh tahun/klien.

***

## ppk (kinematik pasca diproses)

Jika anda menggunakan pembalak Daq Mapir dengan GPS untuk geolokasi yang tepat:

### Prasyarat

* Daq Mapir dengan Modul GPS (GNSS)
* .daq fail log dengan entri pin pendedahan
* Kamera yang disambungkan ke pin pendedahan DAQ semasa sesi penangkapan

### Langkah Konfigurasi

1. Letakkan fail log .daq dalam folder projek anda
2. Dalam tetapan projek, periksa kotak ** «Sapukan ppk fixes» **
3. Tetapkan ** «Offset Masa Sensor Cahaya» ** Jika perlu (lalai: 0 untuk UTC)
4. Berikan kamera ke pin pendedahan:
   *** Kamera tunggal **: Secara automatik ditugaskan ke pin 1.
   *** Dua kamera **: Secara manual berikan setiap kamera ke pin yang betul.

** Tugasan Pin Pameran: **

*** PIN PENDEDAHAN 1 **: Pilih model kamera dari menu lungsur.
*** PIN PENDEDAHAN 2 **: Pilih kamera kedua atau "jangan gunakan".
* Kamera yang sama tidak boleh diberikan kepada kedua -dua pin.

{% petunjuk gaya = & quot; Amaran & quot; %}
** PENTING **: Pin pendedahan mesti ditugaskan dengan betul ke kamera masing -masing. Pemetaan yang salah akan mengakibatkan data geolokasi yang salah.
{ % endhint %}

***

## Senario lanjutan

### projek dengan pelbagai kamera

Semasa memproses imej dari pelbagai kamera Mapir dalam projek:

1. Kloros secara automatik mengesan setiap model kamera
2. Setiap kamera mendapat profil pemprosesan yang betul
3. PPK: Secara manual berikan setiap kamera ke pin pendedahan yang betul.
4. Semua kamera menggunakan format dan indeks eksport yang sama.

** Contoh **: Survey3W RGN + Survey3N OCN Peralatan Kamera Dual

### Kajian dengan masa lenyap atau pada beberapa tarikh

Untuk kajian berulang di kawasan yang sama dari masa ke masa:

1. Buat templat dengan tetapan standard anda.
2. Gunakan penetapan sasaran penentukuran yang konsisten setiap sesi.
3. Proses setiap tarikh sebagai projek berasingan.
4. Gunakan tetapan yang sama untuk hasil yang setanding.
5. Eksport dalam format yang sama untuk analisis temporal.

### set data besar

Untuk projek dengan banyak imej (lebih daripada 500):

* Pertimbangkan untuk memecahkannya ke dalam projek yang lebih kecil mengikut tarikh atau kawasan.
* Gunakan kloros+ pemprosesan selari untuk hasil yang lebih cepat.
* Pertimbangkan CLI atau API untuk automasi batch.
* Laraskan selang pengubahsuaian minimum untuk mengurangkan masa pengesanan sasaran.

***

## pengesahan konfigurasi

Sebelum anda mula memproses, semak tetapan utama ini:

* [] Model Kamera dengan betul dikesan dalam File Explorer.
* [] Pembetulan vignette diaktifkan.
* [] Penentukuran refleksi diaktifkan.
* [] Sekurang -kurangnya satu imej sasaran penentukuran yang diimport.
* [] Menambah indeks multispektral yang dikehendaki.
* [] Format eksport sesuai untuk aliran kerja anda.
* [] Tetapan PPK yang dikonfigurasikan (jika menggunakan .daq dengan peristiwa pendedahan).

***

## Langkah seterusnya

Setelah tetapan dikonfigurasikan:

1. ** Periksa imej sasaran penentukuran **: lihat [memilih imej sasaran] (memilih sasaran-imej.md)
2. ** Mulakan pemprosesan **: lihat [Memulakan Pemprosesan] (permulaan-pemproses.md)
3. ** Memantau Kemajuan **: Lihat [Memantau pemprosesan] (Pemantauan-The-Processing.md).

Untuk maklumat terperinci mengenai semua tetapan yang tersedia, lihat dokumentasi rujukan [Tetapan Projek] (../ Project-Settings/Project-Settings.md).