# Memulakan pemprosesan

Sebaik sahaja anda telah mengimport imej anda, menandakan sasaran penentukuran anda, dan mengkonfigurasi tetapan projek anda, anda sudah bersedia untuk memulakan pemprosesan. Halaman ini membimbing anda melalui memulakan saluran paip pemprosesan kloros.

## senarai semak pra-pemprosesan

Sebelum mengklik butang Mula, sahkan bahawa semuanya sudah siap:

*[] ** Fail yang diimport ** - Semua imej muncul dalam penyemak imbas fail
*[] ** Imej sasaran ditandakan ** - Lajur sasaran diperiksa untuk imej penentukuran
*[] ** Model kamera dikesan ** - Lajur Model Kamera menunjukkan kamera yang betul
*[] ** Tetapan dikonfigurasi ** - Tetapan projek ditinjau dan diselaraskan
*[] ** indeks dipilih ** - indeks multispektral yang dikehendaki ditambah (jika diperlukan)
*[] ** Format eksport dipilih ** - Format output sesuai untuk aliran kerja anda

{ % petunjuk gaya = "info" %}
** Petua **: Klik melalui beberapa imej dalam penyemak imbas fail untuk mengesahkannya dimuat dengan betul sebelum diproses.
{ % endhint %}

***

## Memulakan pemprosesan

### Cari butang Mula

Butang Start/Play terletak di bar kepala kloros atas:

* Kedudukan: pusat atas tingkap
*Ikon: ** Butang Main/Mula ** <img src = "../. Gitbook/aset/imej (2) .png" alt = "" data-size = "line">
* Status: Butang diaktifkan (cerah) apabila bersedia untuk diproses

### Klik untuk memulakan

1. Klik butang ** Play/Start ** di tajuk atas
2. Pemprosesan bermula dengan segera
3. Butang menjadi dilumpuhkan (kelabu keluar) semasa pemprosesan
4. Kemas kini bar kemajuan, menunjukkan status pemprosesan

{ % petunjuk gaya = "kejayaan" %}
** Pemprosesan Bermula **: Setelah diklik, Chloros secara automatik mengendalikan semua langkah pemprosesan - pengesanan sasaran, debayering, penentukuran, pengiraan indeks, dan eksport.
{ % endhint %}

***

## Memahami mod pemprosesan

Chloros beroperasi dalam dua mod pemprosesan yang berbeza bergantung pada lesen anda:

### mod percuma (pemprosesan berurutan)

** Tersedia untuk semua pengguna **

** Bagaimana ia berfungsi: **

* Memproses gambar satu demi satu, secara berurutan
* Operasi Single-Threaded
* Penggunaan memori yang lebih rendah

** Bar Kemajuan menunjukkan 2 peringkat: **

1. ** Sasaran Mengesan ** - Mengimbas sasaran penentukuran
2. ** Pemprosesan ** - Memohon penentukuran dan mengeksport imej

** Masa Pemprosesan: **

* Jauh lebih perlahan daripada kloros+ mod selari
* Sesuai untuk dataset kecil dan sederhana (<200 gambar)

### chloros+ mod (pemprosesan selari)

** Memerlukan Lesen Chloros+ **

** Bagaimana ia berfungsi: **

* Memproses pelbagai imej secara serentak
* Operasi pelbagai threaded (sehingga 16 pekerja selari)
* Menggunakan teras CPU berganda
* Percepatan GPU (CUDA) Pilihan dengan kad grafik NVIDIA

** Bar Kemajuan menunjukkan 4 peringkat: **

1. ** Mengesan ** - Mencari sasaran penentukuran
2. ** Menganalisis ** - Memeriksa metadata imej dan menyediakan saluran paip
3. ** Menentukur ** - Memohon pembetulan dan penentukuran
4. ** Mengeksport ** - Menyimpan imej dan indeks yang diproses

** Interaksi Bar Kemajuan: **

*** Hover Mouse ** Over Bar untuk melihat panel dropdown 4-peringkat terperinci
*** Klik ** Bar Kemajuan untuk membekukan panel dropdown di tempatnya
*** Klik Lagi ** untuk Membuat dan Sembunyikan Panel

** Masa Pemprosesan: **

* Jauh lebih cepat daripada mod percuma
* Skala dengan kiraan teras CPU
* Percepatan GPU terus meningkatkan kelajuan

{ % petunjuk gaya = "info" %}
** Chloros+ Speed ​​**: Pemprosesan selari boleh menjadi 5-10x lebih cepat daripada mod berurutan untuk dataset besar. Projek 500-imej yang mengambil masa 2 jam dalam mod percuma boleh disiapkan dalam 15-20 minit dengan Chloros+.
{ % endhint %}

***

## Apa yang berlaku semasa pemprosesan

### Peringkat 1: Pengesanan Sasaran

** Apa yang dilakukan oleh kloros: **

* Mengimbas imej sasaran yang ditandakan (atau semua imej jika tiada yang ditandakan)
* Mengenal pasti 4 panel penentukuran dalam setiap sasaran
* Mengekstrak nilai refleksi dari panel sasaran
* Rekod sasaran cap waktu untuk penjadualan penentukuran

** Tempoh: ** 1-30 saat (dengan sasaran yang ditandakan), 5-30+ minit (tidak bertanda)

### Peringkat 2: Debayering (penukaran mentah)

** Apa yang dilakukan oleh kloros: **

* Menukar data corak bayer mentah ke gambar RGB penuh
* Menggunakan algoritma demosage berkualiti tinggi
* Mengekalkan kualiti dan perincian imej maksimum

** Tempoh: ** Berbeza dengan kiraan imej dan kelajuan CPU

### Peringkat 3: Penentukuran

** Apa yang dilakukan oleh kloros: **

*** Pembetulan Vignette **: Mengeluarkan kanta gelap di tepi
*** Penentukuran Refleksi **: Menormalkan menggunakan nilai refleksi sasaran
* Menggunakan pembetulan di semua kumpulan/saluran
* Menggunakan sasaran penentukuran yang sesuai untuk setiap imej berdasarkan cap waktu

** Tempoh: ** majoriti masa pemprosesan

### Peringkat 4: Pengiraan Indeks

** Apa yang dilakukan oleh kloros: **

* Mengira indeks multispektral yang dikonfigurasikan (NDVI, NDRE, dll.)
* Menggunakan matematik band untuk imej yang dikalibrasi
* Menjana imej indeks untuk setiap indeks yang dipilih

** Tempoh: ** Beberapa saat setiap gambar

### Peringkat 5: Eksport

** Apa yang dilakukan oleh kloros: **

* Menyimpan imej yang dikalibrasi dalam format yang dipilih
* Imej indeks eksport dengan warna LUT yang dikonfigurasikan
* Menulis fail ke subfolder model kamera
* Memelihara nama fail asal dengan akhiran

** Tempoh: ** Berbeza mengikut format eksport dan saiz fail

***

## Tingkah laku pemprosesan

### saluran paip pemprosesan automatik

Setelah bermula, keseluruhan saluran paip berjalan secara automatik:

* Tiada interaksi pengguna diperlukan
* Semua langkah yang dikonfigurasikan dilaksanakan mengikut urutan
* Kemas kini kemajuan yang ditunjukkan dalam masa nyata

### Penggunaan komputer semasa diproses

** Mod Percuma: **

* Penggunaan CPU yang agak rendah (Single-threaded)
* Komputer tetap responsif untuk tugas lain
* Selamat untuk meminimumkan kloros dan bekerja dalam aplikasi lain

** Kloros+ mod selari: **

* Penggunaan CPU Tinggi (pelbagai thread, sehingga 16 teras)
* Dengan pecutan GPU: penggunaan GPU yang tinggi
* Komputer mungkin kurang responsif semasa pemprosesan
* Elakkan memulakan tugas intensif CPU yang lain

{ % petunjuk gaya = "amaran" %}
** Petua Prestasi **: Untuk prestasi kloros+ terbaik, tutup aplikasi lain dan biarkan kloros menggunakan sumber sistem penuh.
{ % endhint %}

### Pemprosesan tidak boleh dijeda

** Batasan penting: **

* Sekali bermula, pemprosesan tidak boleh dijeda
* Anda boleh membatalkan pemprosesan, tetapi kemajuan hilang
* Keputusan separa tidak disimpan
* Mesti dimulakan semula dari awal jika dibatalkan

** Petua Perancangan: ** Untuk projek yang sangat besar, pertimbangkan pemprosesan dalam kelompok atau menggunakan CLI untuk kawalan yang lebih baik.

***

## Memantau pemprosesan anda

Semasa pemprosesan berjalan, anda boleh:

*** Watch Bar Progress ** - Lihat Peratusan Penyelesaian Keseluruhan
*** Lihat Peringkat Semasa ** - Mengesan, Menganalisis, Kalibrasi, atau Eksport
*** Tab Log Periksa ** - Lihat mesej dan amaran pemprosesan terperinci
*** Pratonton Imej Selesai ** - Beberapa fail eksport mungkin muncul semasa pemprosesan

Untuk maklumat terperinci mengenai pemantauan, lihat [Pemantauan Pemprosesan] (Pemantauan-The-Processing.md).

***

## Membatalkan pemprosesan

Sekiranya anda perlu berhenti memproses:

### cara membatalkan

1. Cari butang ** Stop/Batal ** (menggantikan butang Mula semasa pemprosesan)
2. Klik butang STOP
3. Pemprosesan berhenti segera
4. Keputusan separa dibuang

### Bila hendak membatalkan

** Sebab yang sah untuk membatalkan: **

* Tetapan yang salah direalisasikan telah digunakan
* Lupa menandakan gambar sasaran
* Imej yang salah diimport
* Sistem berjalan terlalu lambat atau tidak bertindak balas

** Setelah membatalkan: **

* Semak dan selesaikan sebarang masalah
* Laraskan tetapan yang diperlukan
* Mulakan semula pemprosesan dari awal
* Untuk pengalaman paling bersih, kloros sepenuhnya dan mulakan semula

{ % petunjuk gaya = "amaran" %}
** Tiada hasil separa **: Membatalkan membuang semua kemajuan. Chloros tidak menyimpan imej yang diproses sebahagiannya.
{ % endhint %}

***

## anggaran masa pemprosesan

Masa pemprosesan sebenar sangat berbeza berdasarkan:

* Bilangan gambar
* Resolusi imej
* Format input mentah vs jpg
* Mod Pemprosesan (Percuma vs Chloros+)
* Kelajuan CPU dan kiraan teras
* Ketersediaan GPU (kloros+ sahaja)
* Bilangan indeks untuk dikira
* Kerumitan format eksport

### Anggaran kasar (chloros+, imej 12mp, cpu moden)

| Kiraan imej | Mod Percuma | Chloros+ (CPU) | Chloros+ (GPU) |
| ----------- | --------- | -------------- | -------------- |
| 50 Imej | 15-20 min | 5-8 min | 3-5 min |
| 100 Imej | 30-40 min | 10-15 min | 5-8 min |
| 200 imej | 1-1.5 jam | 20-30 min | 10-15 min |
| 500 Imej | 2-3 jam | 45-60 min | 20-30 min |
| 1000 Imej | 4-6 jam | 1.5-2 jam | 40-60 min |

{ % petunjuk gaya = "info" %}
** Run First **: Pemprosesan awal mungkin mengambil masa lebih lama apabila Chloros membina cache dan profil. Pemprosesan data yang sama akan lebih cepat.
{ % endhint %}

***

## masalah biasa pada permulaan

### Butang Mula dilumpuhkan (kelabu keluar)

** Kemungkinan Punca: **

* Tiada gambar yang diimport
* Backend tidak dimulakan sepenuhnya
* Pemprosesan sebelumnya masih berjalan
* Projek tidak dimuat sepenuhnya

** Penyelesaian: **

1. Tunggu backend untuk memulakan sepenuhnya (periksa ikon menu utama)
2. Sahkan imej diimport dalam penyemak imbas fail
3. Mulakan semula kloros jika butang kekal dilumpuhkan
4. Periksa log debug untuk mesej ralat

Pemprosesan ### bermula kemudian segera gagal

** Kemungkinan Punca: **

* Tiada gambar yang sah dalam projek
* Fail gambar yang rosak
* Ruang cakera yang tidak mencukupi
* Memori yang tidak mencukupi (RAM)

** Penyelesaian: **

1. Periksa log debug <img src = "../. Gitbook/aset/icon_log.jpg" alt = "" data-size = "line"> untuk mesej ralat
2. Sahkan ruang cakera ada
3. Cuba memproses subset kecil gambar
4. Sahkan imej tidak rosak

### "Tiada sasaran dikesan" Amaran

** Kemungkinan Punca: **

* Lupa menandakan gambar sasaran
* Imej sasaran tidak mengandungi sasaran yang kelihatan
* Tetapan pengesanan sasaran terlalu ketat

** Penyelesaian: **

1. Ulasan [memilih imej sasaran] (memilih-sasaran-imej.md)
2. Tandakan gambar yang sesuai dalam lajur sasaran
3. Sahkan sasaran dapat dilihat dalam imej yang ditandakan
4. Laraskan tetapan pengesanan sasaran jika diperlukan

***

## Petua untuk pemprosesan yang berjaya

### Sebelum memulakan

1. ** Ujian dengan subset kecil pertama ** - Proses 10-20 imej untuk mengesahkan tetapan
2. ** Semak ruang cakera yang tersedia ** - Pastikan saiz dataset 2-3x percuma
3. ** Tutup aplikasi yang tidak perlu ** - Sumber sistem percuma
4. ** Sahkan imej sasaran ** - Pratonton sasaran yang ditandakan untuk memastikan kualiti
5. ** Simpan Projek ** - Projek Auto -Saves, tetapi amalan yang baik untuk menyelamatkan secara manual

### semasa pemprosesan

1. ** Elakkan tidur sistem ** - Lumpuhkan mod penjimatan kuasa
2. ** Simpan kloros di latar depan ** - atau sekurang -kurangnya kelihatan dalam bar tugas
3. ** Memantau kemajuan sekali -sekala ** - periksa amaran atau kesilapan
4. ** Jangan memuatkan aplikasi berat lain ** - terutamanya dengan mod kloros+ selari

### chloros+ pecutan GPU

Jika menggunakan pecutan GPU NVIDIA:

1. Kemas kini pemacu Nvidia ke versi terkini
2. Pastikan GPU mempunyai 4GB+ VRAM
3. Tutup aplikasi intensif GPU (permainan, penyuntingan video)
4. Memantau suhu GPU (pastikan penyejukan yang mencukupi)

***

## Langkah seterusnya

Setelah pemprosesan telah bermula:

1. ** Pantau kemajuan **-Lihat [Memantau pemprosesan] (Pemantauan-The-Processing.md)
2. ** Tunggu siap ** - Pemprosesan berjalan secara automatik
3. ** Hasil semakan **-lihat [menyelesaikan pemprosesan] (penamat-the-processing.md)

Untuk maklumat mengenai apa yang perlu dilakukan semasa pemprosesan, lihat [memantau pemprosesan] (pemantauan-pemproses.md).
