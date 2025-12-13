# Lapisan imej

Menu drop-down lapisan imej di Viewer Imej Chloros membolehkan anda dengan cepat menukar antara versi yang berbeza dari imej yang sama, dari tangkapan asal ke hasil pemantulan yang diproses dan imej indeks yang dikira.

## Apakah lapisan gambar?

Di kloros, ** lapisan ** Rujuk kepada hasil imej yang berbeza untuk imej sumber tunggal. Apabila anda memproses imej, Chloros mencipta beberapa versi:

*** Imej Asal ** (JPG dan fail mentah dari kamera anda)
*Keputusan ** Refleksi ditentukur ** (jika penentukuran refleksi diaktifkan)
*** Imej sasaran ** (jika imej mengandungi sasaran penentukuran)
*** Imej Indeks ** (NDVI, NDRE, GNDVI, dan lain -lain, jika indeks dikonfigurasi)

** Layer Selector Drop-Down Menu ** Terletak di sebelah kanan atas Pemapar Imej membolehkan anda untuk bertukar dengan serta-merta di antara versi ini tanpa meninggalkan penonton.

***

## jenis lapisan yang ada

### JPG

* Imej pratonton JPG asal dari kamera anda
* Sentiasa tersedia untuk semua gambar
* Mentah, seperti yang ditangkap pada kamera
* Terpantas untuk memuatkan dan memaparkan

** Bila hendak melihatnya: **

* Pratonton cepat penangkapan asal
* Memeriksa komposisi imej dan pembingkaian
* Pengesahan kualiti menangkap sebelum diproses

### mentah (asal)

* Data asal dari sensor mentah kamera anda.
* Dilancarkan tanpa memohon sebarang pemprosesan pasca.
* Kedalaman bit yang lebih tinggi daripada JPG (biasanya data sensor 12 atau 14 bit).

** Bila hendak melihatnya: **

* Memeriksa kualiti data sensor asal.
* Periksa masalah dengan sensor atau artifak.
* Bandingkan keputusan sebelum dan selepas pemprosesan.

### mentah (lensa)

*Hanya muncul untuk imej yang dikenal pasti sebagai sasaran penentukuran
* Menunjukkan imej mentah asal dengan sasaran yang dikesan
* Digunakan untuk mengesahkan bahawa pengesanan sasaran berjaya

** Bila hendak melihatnya: **

* Sahkan bahawa sasaran penentukuran telah dikesan dengan betul
* Periksa kualiti imej lensa
* Betulkan masalah penentukuran

{% petunjuk gaya = & quot; info & quot; %}
** Lapisan Sasaran **-Lapisan ini hanya muncul dalam menu lungsur untuk imej yang mengandungi sasaran penentukuran. Imej penangkapan biasa tidak akan mempunyai pilihan ini.
{ % endhint %}

### mentah (reflektif)

* Refleksi imej output ditentukur.
* Vignette diperbetulkan (jika didayakan dalam rendering).
* Reflectance ditentukur menggunakan data sasaran (jika diaktifkan)
* Multiband tiff dengan semua saluran kamera
*Nilai piksel mewakili pemantulan peratus (semasa menggunakan mod peratus)
* Bersedia untuk memanipulasi dengan [Indeks/Lut Sandbox] (Index-Lut-Sandbox.md)

** Bila hendak menonton: **

* Memeriksa hasil yang dikalibrasi
* Periksa kualiti penentukuran
* Periksa nilai piksel untuk memastikan ketepatan saintifik
* Bandingkan dengan asal untuk melihat kesan penentukuran

{% petunjuk gaya = & quot; kejayaan & quot; %}
** Disyorkan **: Gunakan lapisan mentah (reflektif) apabila memeriksa nilai piksel untuk pengukuran dan analisis saintifik.
{ % endhint %}

### mentah (indeks ndvi) ... dan serupa

*Imej Indeks Vegetasi Dikira (NDVI Dalam contoh ini)
* Nama indeks berubah bergantung pada indeks yang dikonfigurasikan semasa pemprosesan
* Contoh: mentah (indeks NDVI), mentah (indeks ndre), mentah (indeks gndvi), dll.
*Imej skala kelabu band tunggal menunjukkan hasil pengiraan indeks
* Satu lapisan muncul untuk setiap indeks yang dikonfigurasikan dalam tetapan projek.

** Nama indeks yang mungkin: **

* Mentah (indeks NDVI)
* Mentah (indeks ndre)
* Mentah (indeks gndvi)
* Mentah (indeks osavi)
* Mentah (indeks evi)
* Mentah (indeks savi)
* Dan banyak lagi ... (lihat [formula indeks multispectral] (../ Project-Settings/Multispectral-index-Formulas.md))

** Bila hendak melihat: **

* Periksa hasil pengiraan indeks.
* Semak julat nilai indeks.
* Mengenal pasti bidang kepentingan.
* Sahkan imej indeks sebelum menggunakannya dalam GIS atau analisis.

***

## Menggunakan pemilih lapisan

### Buka Menu Dropdown

1. Buka imej dalam mod skrin penuh (klik mana -mana lakaran kecil dalam penonton imej).
2. Cari menu drop-down ** Lapisan ** di sudut kanan atas penonton.
3. Menu drop-down menunjukkan lapisan yang dipilih sekarang (contohnya, "JPG").
4. Klik menu lungsur untuk melihat semua lapisan yang tersedia.

### Tukar lapisan

1. Klik menu drop-down lapisan untuk membuka senarai.
2. Semua lapisan yang tersedia untuk imej semasa dipaparkan.
3. Klik mana -mana nama lapisan untuk beralih ke versi itu.
4. Kemas kini imej segera untuk menunjukkan lapisan yang dipilih.

** Perubahan cepat: **

* Menu dropdown mengingati pilihan terakhir anda.
* Apabila menavigasi ke imej seterusnya, kloros cuba memaparkan jenis lapisan yang sama.
*Jika lapisan itu tidak wujud dalam imej di bawah, ia mungkir kepada JPG.

### ketersediaan lapisan

Tidak semua lapisan disediakan untuk semua imej:

** Sentiasa tersedia: **

* ✅ JPG (semua imej dipratonton dalam JPG).

** tersedia secara syarat: **

* ⚠️ Raw (asal): Hanya jika imej telah ditangkap dalam mod RAW atau RAW+JPG.
* ⚠️ mentah (sasaran) - Hanya jika imej mengandungi sasaran penentukuran yang dikesan.
* ⚠️ RAW (Refleksi): Hanya selepas pemprosesan dengan penentukuran reflektif diaktifkan.
* ⚠️ Raw (\ [index] index): Hanya selepas diproses dengan indeks yang dikonfigurasikan.

***

## Layer kegigihan

### navigasi antara gambar

Apabila anda menavigasi ke imej yang berbeza (menggunakan kekunci anak panah atau mengklik pada gambar kecil):

** Keutamaan lapisan dipelihara: **

* Jika "mentah (reflektif)" dipaparkan, imej berikut menunjukkan "mentah (reflektif)" (jika ada).
* Jika "RAW (Indeks NDVI)" dipaparkan, imej berikut menunjukkan "RAW (Indeks NDVI)" (jika tersedia).
* Jika lapisan yang sama tidak wujud, ia mungkir kepada JPG.

** Contoh aliran kerja: **

1. Buka imej 1, beralih ke mentah (indeks NDVI).
2. Tekan → untuk melihat imej 2.
3. Imej 2 secara automatik memaparkan lapisan RAW (NDVI).
4. Teruskan melayari: Semua imej menunjukkan lapisan NDVI.
5. Sangat berkesan untuk mengkaji hasil indeks pada banyak imej.

***

## Aliran kerja biasa

### aliran kerja 1: sebelum/selepas perbandingan

** Objektif **: Bandingkan imej asal dengan imej yang dikalibrasi.

1. Buka imej yang diproses dalam penonton imej.
2. Pilih ** RAW (asal) ** dari menu lungsur.
3. Perhatikan nilai vignetting dan tidak tercelari.
4. Tukar ke ** mentah (refleksi) ** dalam menu lungsur.
5. Bandingkan: Vignetting telah dikeluarkan dan nilai telah ditentukur.

### Aliran Kerja 2: Semak Indeks

** Matlamat **: Cepat semak keputusan NDVI di seluruh set data.

1. Buka imej yang diproses pertama.
2. Pilih ** Raw (Indeks NDVI) ** dari menu lungsur.
3. Gunakan kekunci anak panah → untuk menavigasi ke imej seterusnya.
4. Lapisan NDVI secara automatik berterusan.
5. Teruskan dengan semua imej, periksa corak NDVI.
6. Tukar ke ** RAW (indeks ndre) ** untuk membandingkan.

### aliran kerja 3: pengesahan sasaran

** Matlamat **: Sahkan bahawa semua imej sasaran telah dikesan dengan betul.

1. Navigasi ke imej sasaran.
2. Pilih ** mentah (kanta) ** dari menu lungsur.
3. Sahkan bahawa sasaran penentukuran jelas kelihatan dan telah dikesan.
4. Navigasi ke imej sasaran seterusnya.
5. Ulangi pengesahan untuk semua sasaran.

### aliran kerja 4: pemeriksaan nilai piksel

** Matlamat **: Semak nilai refleksi untuk memastikan ketepatan saintifik.

1. Buka imej yang diproses.
2. Pilih lapisan ** mentah (reflektif) **.
3. Aktifkan ** Peratus Pixel ** mod (butang pada bar alat kanan atas).
4. Pindahkan kursor ke atas kawasan tumbuh -tumbuhan.
5. Sahkan bahawa nilai piksel berada dalam julat yang dijangkakan (30-70% untuk NIR, 5-15% untuk merah).
6. Periksa kawasan tanah dan air mempunyai nilai yang mencukupi.

***

## Memahami nilai piksel setiap lapisan

Lapisan yang berbeza menunjukkan pelbagai nilai piksel:

### JPG lapisan

*** Range **: 0-255 (8 bit)
*** Arti **: Nilai paparan, dengan pembetulan gamma
*** Gunakan **: Pemeriksaan Visual Sahaja, Bukan untuk Pengukuran Saintifik

### mentah (asal)

*** Range **: 0-65535 (16 bit)
*** Maksudnya **: Nombor digital sensor mentah.
*** Gunakan **: Pemeriksaan prestasi sensor, tanpa penentukuran.

### mentah (reflektif)

*** Range **: 0-65,535 (tiff 16-bit) atau 0.0-1.0 (peratusan 32-bit)
*** Maksudnya **: Peratusan Refleksi yang Dilancarkan
*** Gunakan **: Pengukuran dan analisis saintifik

** Untuk tiff 16-bit: ** Bahagikan dengan 65,535 untuk mendapatkan pemantulan peratusan ** untuk peratus 32-bit: ** Nilai secara langsung mewakili peratusan (0.5 = 50% refleksi)

### mentah (imej indeks)

*** julat **: Berbeza mengikut indeks (biasanya -1.0 hingga +1.0 untuk indeks normal)
*** Arti **: Hasil Pengiraan Indeks
*** Contoh **:
  * NDVI: -1 hingga +1 (tumbuh -tumbuhan biasanya 0.4 hingga 0.9)
  * Ndre: -1 hingga +1 (pengesanan tekanan)
  * EVI: dari 0 hingga 1 (tumbuh -tumbuhan yang lebih baik)

***

## petua dan amalan terbaik

### Beralih lapisan yang cekap

*** Kesedaran Pintasan Papan Kekunci **: Walaupun tidak ada pintasan papan kekunci untuk lapisan, anak panah navigasi (←/→) berfungsi pada semua lapisan
*** aliran kerja yang konsisten ** - Pilih satu lapisan (mis. NDVI) dan semak keseluruhan data yang ditetapkan sebelum beralih ke yang lain
*** perbandingan cepat ** - togol antara asal dan refleksi untuk memeriksa kualiti pemprosesan

### Pertimbangan prestasi

*** JPG Loads lebih cepat ** - Gunakan ini untuk melayari dengan cepat banyak imej.
*** Lapisan mentah memuat lebih perlahan **: Resolusi yang lebih tinggi dan kedalaman bit.
*** Lapisan Indeks **: Kelajuan yang sama dengan lapisan refleksi.
*** Beban pertama adalah paling lambat ** - Pandangan seterusnya lapisan yang sama cache dan lebih cepat.

### pengesahan kualiti

*** Sentiasa periksa mentah (asal) ** - periksa kualiti data sumber sebelum mempercayai hasil yang diproses.
*** Bandingkan lapisan ** - Gunakan lapisan berubah untuk mengesahkan pemprosesan telah berfungsi dengan betul.
*** Rentang Indeks Semak **: Gunakan mod piksel peratus dengan lapisan indeks untuk mengesahkan bahawa nilai -nilai itu munasabah.

***

## Penyelesaian masalah

Lapisan ### tidak tersedia

** Masalah **: Lapisan yang diharapkan tidak muncul dalam menu dropdown.

** Kemungkinan Punca: **

*Imej belum diproses (hanya JPG dan RAW (asal) boleh didapati).
*Penentukuran refleksi telah dilumpuhkan semasa pemprosesan.
* Tiada indeks khusus telah dikonfigurasikan dalam tetapan projek.
* Imej adalah imej sasaran sahaja (tiada indeks telah dihasilkan untuk sasaran).

** Penyelesaian: **

1. Sahkan bahawa imej telah diproses (periksa folder output untuk fail yang diproses).
2. Semak konfigurasi projek untuk mengesahkan bahawa indeks telah dikonfigurasikan.
3. Mengubah semula dengan indeks yang dikehendaki diaktifkan.

### Lapisan yang tidak betul dipaparkan

** Masalah **: Imej dibuka pada lapisan yang tidak dijangka.

** Punca **: Keutamaan lapisan telah dipindahkan dari imej sebelumnya, tetapi lapisan itu tidak wujud dalam imej semasa.

** Betulkan **: Chloros secara automatik kembali ke JPG apabila lapisan pilihan tidak tersedia; Ini adalah tingkah laku biasa.

### Sasaran penentukuran tidak dapat dilihat.

** Isu **: Lapisan mentah (sasaran) tidak menunjukkan pengesanan sasaran.

** Kemungkinan Punca: **

*Sasaran tidak dikesan semasa pemprosesan.
*Imej sebenarnya tidak mengandungi objektif.
* Tetapan pengesanan sasaran terlalu ketat.

** Penyelesaian: **

1. Semak log debug untuk mesej "Target Found".
2. Sahkan bahawa imej sebenarnya mengandungi sasaran penentukuran yang dapat dilihat.
3. Laraskan tetapan pengesanan sasaran dalam tetapan projek.
4. Lihat [Memilih Imej Sasaran] (../ Pemprosesan-Images-Gui/Memilih-Target-Immages.md).

***

## Fungsi berkaitan

### alat penonton imej

Apabila melihat mana -mana lapisan, anda boleh menggunakan:

*** Kawalan Zum **: Zum masuk untuk memeriksa butiran.
*** Panorama **: Klik dan seret untuk pan imej zoom.
*** Pemeriksaan nilai piksel **: Lihat nilai di lokasi kursor.
*** anak panah navigasi **: Gerakkan antara imej sambil mengekalkan lapisan.
*** Mod Peratus Pixel **: Togol antara DN dan paparan peratusan.

Lihat [Buka Skrin Penuh Imej] (pembukaan-image-full-screen.md) untuk dokumentasi penonton imej lengkap.

### Indeks/Lut Sandbox

Untuk ujian interaktif dan visualisasi indeks:

*** Pengiraan indeks masa nyata ** - Cuba formula indeks yang berbeza.
*** Pemetaan warna LUT **: Sapukan kecerunan warna ke indeks skala kelabu.
*** Visualisasi Eksport **: Simpan Imej Indeks dalam warna.

Lihat [Index Sandbox/LUT] (Index-Lut-Sandbox.md) untuk butirannya.

***

## Langkah seterusnya

Sekarang anda tahu lapisan gambar:

*[** Buka skrin penuh imej **] (pembukaan-an-image-full-screen.md): Panduan Penonton Imej Lengkap.
*[** indeks pasir/lut **] (index-lut-sandbox.md): paparan interaktif indeks
*[** Formula Indeks Multispectral **] (../ Project-Settings/Multispectral-index-Formulas.md): Rujukan indeks yang tersedia
*[** menamatkan pemprosesan **] (../ pemprosesan-images-gui/penamat-the-processing.md): Memahami hasil yang diproses