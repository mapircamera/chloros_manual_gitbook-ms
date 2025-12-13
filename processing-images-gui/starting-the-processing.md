# Permulaan pemprosesan

Sebaik sahaja anda telah mengimport imej anda, menandakan sasaran penentukuran anda, dan mengkonfigurasi tetapan projek anda, anda sudah bersedia untuk memulakan pemprosesan. Halaman ini akan membimbing anda melalui memulakan proses pemprosesan kloros.

## senarai semak pra-pemprosesan

Sebelum mengklik butang Mula, periksa bahawa semuanya sudah siap:

*[] ** Fail yang diimport **: Semua imej muncul dalam Fail Explorer.
*[] ** Imej sasaran yang ditandakan **: Lajur sasaran yang ditandakan untuk imej penentukuran.
*[] ** model kamera dikesan ** - lajur model kamera menunjukkan kamera yang betul
*[] ** Tetapan yang dikonfigurasikan **: Tetapan projek disemak dan diselaraskan
*[] ** indeks terpilih **: indeks multispektral yang dikehendaki ditambah (jika perlu)
*[] ** Format eksport yang dipilih **: Format output sesuai untuk aliran kerja anda

{% petunjuk gaya = & quot; info & quot; %}
** Petua **: Klik pada beberapa imej dalam Fail Explorer untuk mengesahkan bahawa mereka telah dimuat dengan betul sebelum diproses.
{ % endhint %}

***

## Permulaan pemprosesan

### cari butang rumah

Butang Mula/Play terletak di bar atas kloros:

* Kedudukan: pusat tingkap atas
*Ikon: ** Butang main/rumah ** <img src = "../. Gitbook/aset/imej (2) .png" alt = "" data-size = "line">
* Status: Butang diaktifkan (cerah) apabila bersedia untuk membuat

### Klik untuk memulakan

1. Klik butang ** Play/Start ** di bar atas
2. Pemprosesan bermula dengan segera
3. Butang dilumpuhkan (kelabu keluar) semasa pemprosesan
4. Kemas kini bar kemajuan dan menunjukkan status pemprosesan

{% petunjuk gaya = & quot; kejayaan & quot; %}
** Pemprosesan Bermula **: Setelah diklik, Chloros secara automatik mengendalikan semua langkah pemprosesan: pengesanan sasaran, debayering, penentukuran, pengiraan indeks dan eksport.
{ % endhint %}

***

## Memahami mod pemprosesan

Chloros berfungsi dalam dua mod pemprosesan yang berbeza, bergantung pada lesen anda:

### mod percuma (pemprosesan berurutan)

** Tersedia untuk semua pengguna **

** Bagaimana ia berfungsi: **

* Memproses imej satu demi satu, secara berurutan.
* Operasi berulir tunggal.
* Penggunaan memori yang lebih rendah.

** Bar Kemajuan menunjukkan dua peringkat: **

1. ** Pengesanan Sasaran **: Cari sasaran penentukuran.
2. ** Pemprosesan **: Penggunaan penentukuran dan eksport imej.

** Masa Pemprosesan: **

* Lebih perlahan daripada Mode Chloros+ Parallel.
* Sesuai untuk set data kecil dan sederhana (<200 imej).

### chloros+ mod (pemprosesan selari)

** Memerlukan Lesen Chloros+. **

** Bagaimana ia berfungsi: **

* Memproses pelbagai gambar secara serentak
* Operasi pelbagai threaded (sehingga 16 pekerja selari)
* Menggunakan teras CPU berganda
* Percepatan GPU pilihan (CUDA) dengan kad grafik NVIDIA

** Bar Kemajuan menunjukkan 4 peringkat: **

1. ** Pengesanan **: Carian sasaran penentukuran
2. ** Analisis **: Pemeriksaan metadata imej dan penyediaan proses
3. ** Penentukuran **: Penggunaan pembetulan dan penentukuran
4. ** Eksport **: Menyimpan imej dan indeks yang diproses

** Interaksi dengan Bar Kemajuan: **

*** hover ** di atas bar untuk melihat panel drop-down 4-peringkat terperinci
*** Klik ** pada bar kemajuan untuk membekukan panel dropdown di tempatnya
*** Klik Lagi ** untuk membuka kunci dan menyembunyikan panel.

** Masa Pemprosesan: **

* Jauh lebih cepat daripada mod percuma.
* Menyesuaikan diri dengan bilangan teras CPU.
* Percepatan GPU terus meningkatkan kelajuan.

{% petunjuk gaya = & quot; info & quot; %}
** Chloros+ Speed ​​**: Pemprosesan selari boleh 5-10 kali lebih cepat daripada mod berurutan untuk set data yang besar. Projek imej 500 yang mengambil masa 2 jam dalam mod percuma boleh disiapkan dalam 15-20 minit dengan Chloros+.
{ % endhint %}

***

## Apa yang berlaku semasa pemprosesan

### Peringkat 1: Pengesanan Sasaran

** Apa yang dilakukan oleh kloros: **

* Mengimbas imej sasaran yang ditandakan (atau semua imej jika tidak ada yang ditandakan).
*Mengenal pasti 4 panel penentukuran pada setiap objektif
* Mengekstrak nilai refleksi panel sasaran
* Rekod sasaran cap waktu untuk menjadualkan penentukuran

** Tempoh: ** 1-30 saat (dengan sasaran ditandakan), 5-30+ minit (tidak bertanda)

### Peringkat 2: Debayering (penukaran mentah)

** Apa yang dilakukan oleh kloros: **

* Menukar data corak mentah Bayer ke imej RGB penuh.
* Menggunakan algoritma demosaik berkualiti tinggi.
* Mengekalkan kualiti dan perincian imej maksimum.

** Tempoh: ** Berbeza bergantung kepada bilangan imej dan kelajuan CPU.

### Peringkat 3: Penentukuran

** Apa yang dilakukan oleh kloros: **

*** pembetulan vignette **: menghilangkan gelap dari tepi lensa.
*** Penentukuran Refleksi **: Menormalkan menggunakan nilai refleksi sasaran.
* Menggunakan pembetulan kepada semua band/saluran.
* Menggunakan sasaran penentukuran yang sesuai untuk setiap imej berdasarkan cap waktu.

** Tempoh: ** Kebanyakan masa pemprosesan

### Peringkat 4: Pengiraan Indeks

** Apa yang dilakukan oleh kloros: **

* Mengira indeks multispektral yang dikonfigurasikan (NDVI, NDRE, dll.)
*Menggunakan matematik band untuk imej yang ditentukur
* Menjana imej indeks untuk setiap indeks yang dipilih

** Tempoh: ** Beberapa saat setiap gambar

### Peringkat 5: Eksport

** Apa yang dilakukan oleh kloros: **

* Simpan imej yang dikalibrasi dalam format yang dipilih
* Imej indeks eksport dengan warna lut yang dikonfigurasikan
* Tulis fail ke subfolder model kamera
* Memelihara nama fail asal dengan akhiran.

** Tempoh: ** Berbeza bergantung pada format eksport dan saiz fail.

***

## Tingkah laku pemprosesan

### saluran paip pemprosesan automatik

Setelah bermula, keseluruhan saluran paip berjalan secara automatik:

*Tiada interaksi pengguna diperlukan.
* Semua langkah yang dikonfigurasikan dilaksanakan secara urutan.
* Kemas kini kemajuan dipaparkan dalam masa nyata.

### menggunakan komputer semasa pemprosesan

** Mod Percuma: **

* Penggunaan CPU yang agak rendah (benang tunggal).
* Komputer terus bertindak balas terhadap tugas lain.
* Adalah selamat untuk meminimumkan kloros dan bekerja dalam aplikasi lain.

** Kloros+ mod selari: **

* Penggunaan CPU Tinggi (multi-threaded, sehingga 16 teras).
* Dengan pecutan GPU: Penggunaan GPU yang tinggi.
*Komputer mungkin kurang responsif semasa pemprosesan.
* Elakkan memulakan tugas intensif CPU yang lain.

{% petunjuk gaya = & quot; Amaran & quot; %}
** Petua Prestasi **: Untuk mendapatkan prestasi terbaik dari Chloros+, tutup aplikasi lain dan biarkan kloros menggunakan semua sumber sistem.
{ % endhint %}

### Pemprosesan tidak boleh dijeda

** Batasan penting: **

* Sekali bermula, pemprosesan tidak boleh dijeda.
* Anda boleh membatalkan pemprosesan, tetapi kemajuan akan hilang.
* Keputusan separa tidak disimpan.
* Jika dibatalkan, ia mesti dimulakan semula dari awal.

** Petua Perancangan: ** Untuk projek yang sangat besar, pertimbangkan batching atau menggunakan CLI untuk kawalan yang lebih baik.

***

## pemantauan pemprosesan

Semasa pemprosesan sedang berjalan, anda boleh:

*** Lihat Bar Kemajuan ** - Lihat Peratusan Penyelesaian Keseluruhan.
*** Lihat Peringkat Semasa **: Mengesan, Menganalisis, Kalibrasi atau Eksport.
*** Periksa tab log ** - Lihat mesej pemprosesan terperinci dan amaran.
*** Pratonton Imej Selesai **: Beberapa fail eksport mungkin muncul semasa pemprosesan.

Untuk butiran mengenai pemantauan, lihat [Pemantauan Pemprosesan] (Pemantauan-The-Processing.md).

***

## Batal pemprosesan

Sekiranya anda perlu berhenti memproses:

### cara membatalkan

1. Cari butang ** Stop/Batal ** (menggantikan butang Mula semasa pemprosesan).
2. Klik butang STOP.
3. Pemprosesan berhenti dengan segera.
4. Keputusan separa dibuang.

### bila hendak membatalkan

** Sebab yang sah untuk membatalkan: **

* Ia telah dikesan bahawa tetapan yang salah telah digunakan.
* Anda lupa menandakan imej destinasi.
* Imej yang salah telah diimport.
* Sistem ini berjalan terlalu perlahan atau tidak bertindak balas.

** Setelah membatalkan: **

* Mengkaji dan membetulkan sebarang masalah.
* Laraskan tetapan seperti yang diperlukan.
* Mulakan semula pemprosesan dari awal.
* Untuk pengalaman terbaik, tutup kloros sepenuhnya dan mulakan semula.

{% petunjuk gaya = & quot; Amaran & quot; %}
** Tiada hasil separa **: Pembatalan membuang semua kemajuan. Chloros tidak menyimpan imej yang diproses sebahagiannya.
{ % endhint %}

***

## anggaran masa pemprosesan

Masa pemprosesan sebenar sangat berbeza bergantung kepada:

* Bilangan gambar
* Resolusi imej
* Format input mentah vs jpg
* Mod Pemprosesan (Percuma vs. Chloros+)
* Kelajuan CPU dan bilangan teras
* Ketersediaan GPU (kloros+ sahaja)
* Bilangan indeks untuk dikira
* Kerumitan format eksport

### Anggaran kasar (chloros+, imej 12mp, cpu moden)

| Bilangan gambar | Mod Percuma | Chloros+ (CPU) | Chloros+ (GPU) |
| ----------- | --------- | -------------- | -------------- |
| 50 Imej | 15-20 min | 5-8 min | 3-5 min |
| 100 Imej | 30-40 min | 10-15 min | 5-8 min |
| 200 imej | 1-1.5 jam | 20-30 min | 10-15 min |
| 500 Imej | 2-3 jam | 45-60 min | 20-30 min |
| 1000 Imej | 4-6 jam | 1.5-2 jam | 40-60 min |

{% petunjuk gaya = & quot; info & quot; %}
** Run First **: Pemprosesan awal mungkin mengambil masa lebih lama apabila Chloros mencipta cache dan profil. Post-pemprosesan set data yang serupa akan lebih cepat.
{ % endhint %}

***

## Masalah biasa pada permulaan

### butang rumah dilumpuhkan (kelabu keluar)

** Kemungkinan Punca: **

*Tidak ada gambar yang diimport
* Backend belum bermula sepenuhnya
* Pemprosesan sebelumnya masih berjalan
* Projek itu belum dimuat sepenuhnya

** Penyelesaian: **

1. Tunggu backend untuk memulakan sepenuhnya (periksa ikon menu utama).
2. Periksa bahawa imej telah diimport dalam File Explorer.
3. Mulakan semula kloros jika butang masih dilumpuhkan.
4. Semak log debug untuk mesej ralat.

Pemprosesan ### bermula dan gagal segera

** Kemungkinan Punca: **

*Tidak ada gambar yang sah dalam projek
* Fail gambar yang rosak
* Ruang cakera yang tidak mencukupi
* Memori yang tidak mencukupi (RAM)

** Penyelesaian: **

1. Semak log debug <img src = "../. Gitbook/aset/icon_log.jpg" alt = "" data-size = "line"> untuk mesej ralat.
2. Semak ruang cakera yang ada.
3. Cuba memproses subset kecil imej.
4. Periksa imej untuk rasuah.

### Amaran "Tiada sasaran yang dikesan"

** Kemungkinan Punca: **

* Anda lupa menandakan imej sasaran.
*Imej sasaran tidak mengandungi sasaran yang kelihatan.
* Tetapan pengesanan sasaran terlalu ketat.

** Penyelesaian: **

1. Semak [memilih imej sasaran] (memilih-sasaran-imej.md).
2. Periksa imej yang sesuai dalam lajur sasaran.
3. Periksa bahawa sasaran dapat dilihat dalam imej yang ditandakan.
4. Laraskan tetapan pengesanan sasaran jika perlu.

***

## Petua untuk pemprosesan yang berjaya

### sebelum anda memulakan

1. ** Ujian dengan subset kecil pertama **: Proses 10-20 imej untuk mengesahkan tetapan.
2. ** Semak ruang cakera yang tersedia ** - Pastikan anda mempunyai 2-3 kali saiz data yang ditetapkan percuma.
3. ** Tutup aplikasi yang tidak perlu ** - Sumber sistem percuma.
4. ** Periksa imej sasaran ** - Pratonton sasaran yang ditandakan untuk memastikan kualiti.
5. ** Simpan projek **: Projek ini disimpan secara automatik, tetapi disyorkan untuk menyelamatkannya secara manual.

### semasa pemprosesan

1. ** Mencegah sistem daripada pergi ke mod tidur **: Lumpuhkan mod penjimatan kuasa.
2. ** Simpan kloros di latar depan **: atau sekurang -kurangnya kelihatan dalam bar tugas.
3. ** Periksa kemajuan dari semasa ke semasa **: Periksa amaran atau kesilapan.
4. ** Jangan memuatkan aplikasi berat lain **: Terutama dengan Mode Chloros+ selari.

### chloros+ pecutan GPU

Jika anda menggunakan pecutan GPU NVIDIA:

1. Kemas kini pemandu NVIDIA ke versi terkini.
2. Pastikan GPU mempunyai lebih daripada 4GB VRAM.
3. Aplikasi rapat yang menggunakan banyak sumber GPU (permainan, penyuntingan video).
4. Memantau suhu GPU (pastikan penyejukan mencukupi).

***

## Langkah seterusnya

Setelah pemprosesan telah bermula:

1. ** Memantau Kemajuan **: Lihat [Memantau pemprosesan] (Pemantauan-The-Processing.md).
2. ** Tunggu siap **: Pemprosesan berjalan secara automatik.
3. ** Semak hasil **: lihat [menyelesaikan pemprosesan] (penamat-the-processing.md).

Untuk maklumat mengenai apa yang perlu dilakukan semasa pemprosesan, lihat [memantau pemprosesan] (pemantauan-pemproses.md).