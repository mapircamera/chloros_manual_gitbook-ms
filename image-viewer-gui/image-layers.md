# Lapisan imej

Lapisan imej jatuh dalam penonton imej kloros membolehkan anda dengan cepat menukar antara versi yang berbeza dari imej yang sama - dari tangkapan asal ke output refleksi yang diproses dan imej indeks yang dikira.

## Apakah lapisan gambar?

Di kloros, **lapisan** Rujuk kepada output imej yang berbeza yang tersedia untuk imej sumber tunggal. Apabila anda memproses imej, Chloros mencipta pelbagai versi:

* **Imej Asal** (JPG dan fail mentah dari kamera anda)
* **Refleksi yang ditentukur** output (jika penentukuran refleksi diaktifkan)
* **Imej sasaran** (jika imej mengandungi sasaran penentukuran)
* **Imej Indeks**(NDVI, NDRE, GNDVI, dll. Jika indeks dikonfigurasi)

Dropdown Layer **Layer**di bahagian atas kanan penonton imej membolehkan anda dengan serta-merta menukar antara versi ini tanpa meninggalkan penonton.***

## Jenis lapisan yang ada

### JPG

* Imej pratonton JPG asal dari kamera anda
* Sentiasa tersedia untuk semua gambar
* Tidak diproses, seperti yang ditangkap oleh kamera
* Terpantas untuk memuatkan dan memaparkan

**Bila hendak melihat:**

* Pratonton cepat penangkapan asal
* Memeriksa komposisi imej dan pembingkaian
* Mengesahkan kualiti menangkap sebelum diproses

### mentah (asal)

* Data sensor mentah asal dari kamera anda
* Ditebah tanpa pemprosesan pos yang digunakan
* Kedalaman bit yang lebih tinggi daripada JPG (biasanya data sensor 12-bit atau 14-bit)

**Bila hendak melihat:**

* Memeriksa kualiti data sensor asal
* Memeriksa isu atau artifak sensor
* Membandingkan sebelum/selepas memproses hasil

### mentah (sasaran)

* Hanya muncul untuk imej yang dikenal pasti sebagai sasaran penentukuran
* Menunjukkan imej mentah asal dengan sasaran yang dikesan
* Digunakan untuk mengesahkan pengesanan sasaran berjaya

**Bila hendak melihat:**

* Mengesahkan sasaran penentukuran dikesan dengan betul
* Memeriksa kualiti gambar sasaran
* Menyelesaikan masalah masalah penentukuran

{% hint style="info" %}
**Lapisan Sasaran**: Lapisan ini hanya muncul dalam lungsur turun untuk imej yang mengandungi sasaran penentukuran. Imej menangkap tetap tidak akan mempunyai pilihan ini.
{% endhint %}

### mentah (reflektif)

* Imej output refleksi yang dikalibrasi
* Vignette diperbetulkan (jika didayakan dalam pemprosesan)
* Reflectance ditentukur menggunakan data sasaran (jika diaktifkan)
* Tiff berbilang band dengan semua saluran kamera
* Nilai piksel mewakili pemantulan peratus (semasa menggunakan mod peratus)
* Bersedia untuk memanipulasi dengan [Indeks/Lut Sandbox](Index-Lut-Sandbox.md)

**Bila hendak melihat:**

* Memeriksa hasil yang dikalibrasi
* Mengesahkan kualiti penentukuran
* Memeriksa nilai piksel untuk ketepatan saintifik
* Membandingkan dengan asal untuk melihat kesan penentukuran

{% hint style="success" %}
**Disyorkan**: Gunakan lapisan RAW (Refleksi) apabila memeriksa nilai piksel untuk pengukuran dan analisis saintifik.
{% endhint %}

### mentah (indeks ndvi) ... dan serupa

* Imej Indeks Vegetasi Dikira (NDVI Dalam contoh ini)
* Nama indeks berubah berdasarkan indeks mana yang dikonfigurasikan semasa pemprosesan
* Contoh: mentah (indeks NDVI), mentah (indeks ndre), mentah (indeks gndvi), dll.
* Imej kelabu-band tunggal menunjukkan hasil pengiraan indeks
* Satu lapisan muncul untuk setiap indeks yang dikonfigurasikan dalam tetapan projek

**Nama indeks yang mungkin:**

* Mentah (indeks NDVI)
* Mentah (indeks ndre)
* Mentah (indeks gndvi)
* Mentah (indeks osavi)
* Mentah (indeks evi)
* Mentah (indeks savi)
* Dan banyak lagi ... (lihat [formula indeks multispectral](../ Project-Settings/Multispectral-index-Formulas.md))

**Bila hendak melihat:**

* Memeriksa hasil pengiraan indeks
* Memeriksa julat nilai indeks
* Mengenal pasti bidang minat
* Mengesahkan gambar indeks sebelum menggunakan dalam GI atau analisis

***

## Menggunakan pemilih lapisan

### Membuka dropdown

1. Buka gambar dalam mod skrin penuh (klik mana -mana lakaran kecil dalam penonton imej)
2. Cari lekuk**Layer **di sudut kanan atas penonton
3. Dropdown menunjukkan lapisan yang dipilih sekarang (mis., "JPG")
4. Klik jatuh turun untuk melihat semua lapisan yang ada

### Lapisan menukar

1. Klik Layer Dropdown untuk membuka senarai
2. Semua lapisan yang ada untuk imej semasa ditunjukkan
3. Klik nama lapisan untuk beralih ke versi itu
4. Kemas kini imej segera untuk menunjukkan lapisan yang dipilih**Tukar cepat:**

* Dropdown mengingati pilihan terakhir anda
* Apabila menavigasi ke imej seterusnya, Chloros cuba menunjukkan jenis lapisan yang sama
* Jika lapisan itu tidak wujud pada imej seterusnya, ia mungkir ke jpg

### ketersediaan lapisan

Tidak semua lapisan disediakan untuk setiap imej:

**Sentiasa tersedia:***✅ jpg (Setiap gambar mempunyai pratonton JPG)**tersedia secara syarat:**

* ⚠️ mentah (asal) - Hanya jika imej ditangkap dalam mod mentah atau mentah+jpg
* ⚠️ mentah (sasaran) - Hanya jika imej mengandungi sasaran penentukuran yang dikesan
* ⚠️ mentah (reflektif) - Hanya selepas diproses dengan penentukuran refleksi diaktifkan
* ⚠️ Raw ([index] index) - Hanya selepas diproses dengan indeks yang dikonfigurasikan

***

## Layer kegigihan

### menavigasi antara gambar

Apabila anda menavigasi ke imej yang berbeza (menggunakan kekunci anak panah atau mengklik gambar kecil):**Keutamaan lapisan dipelihara:**

* Jika melihat "mentah (reflektif)", imej seterusnya menunjukkan "mentah (reflektif)" (jika ada)
* Jika melihat "mentah (indeks ndvi)", imej seterusnya menunjukkan "mentah (indeks ndvi)" (jika ada)
* Sekiranya lapisan yang sama tidak wujud, lalai ke jpg

**Contoh aliran kerja:**

1. Buka imej 1, beralih ke mentah (indeks NDVI)
2. Tekan → untuk melihat gambar 2
3. Imej 2 memaparkan lapisan Raw (NDVI) secara automatik
4. Teruskan menavigasi - semua imej menunjukkan lapisan NDVI
5. Sangat berkesan untuk mengkaji hasil indeks di banyak imej***

## Aliran kerja biasa

### aliran kerja 1: sebelum/selepas perbandingan
**Matlamat**: Bandingkan imej asal dan ditentukur

1. Buka gambar yang diproses dalam penonton imej
2. Pilih **mentah (asal)**dari dropdown
3. Perhatikan nilai vignetting dan tidak terjejas
4. Beralih ke**mentah (reflektif)**dari dropdown
5. Bandingkan - vignetting dikeluarkan, nilai ditentukur

### aliran kerja 2: ulasan indeks**Matlamat **: Cepat semak keputusan NDVI di seluruh dataset

1. Buka gambar yang diproses pertama
2. Pilih**RAW (Indeks NDVI)**dari Dropdown
3. Gunakan → anak panah untuk menavigasi ke gambar seterusnya
4. Lapisan NDVI berterusan secara automatik
5. Teruskan melalui semua imej, memeriksa corak NDVI
6. Tukar ke**mentah (indeks ndre)**untuk membandingkan

### aliran kerja 3: pengesahan sasaran**Matlamat **: Sahkan semua imej sasaran dikesan dengan betul

1. Navigasi ke gambar sasaran
2. Pilih**mentah (sasaran)**dari dropdown
3. Sahkan sasaran penentukuran jelas dan dikesan
4. Navigasi ke gambar sasaran seterusnya
5. Ulangi pengesahan untuk semua sasaran

### aliran kerja 4: pemeriksaan nilai piksel**Matlamat **: Periksa nilai refleksi untuk ketepatan saintifik

1. Buka gambar yang diproses
2. Pilih**mentah (reflektif)**lapisan
3. Dayakan**Pixel peratus **mod (butang di bar alat kanan atas)
4. Pindahkan kursor ke kawasan tumbuh -tumbuhan
5. Sahkan nilai piksel berada dalam julat yang dijangkakan (30-70% untuk NIR, 5-15% untuk merah)
6. Periksa kawasan tanah dan air untuk nilai yang sesuai***

## Memahami nilai piksel mengikut lapisan

Lapisan yang berbeza menunjukkan julat nilai piksel yang berbeza:

### JPG lapisan

* **Range**: 0-255 (8-bit)
* **Arti**: Nilai paparan, diperbetulkan gamma
* **Gunakan**: pemeriksaan visual sahaja, bukan untuk pengukuran saintifik

### mentah (asal)

* **Range**: 0-65535 (16-bit)
* **Arti**: Nombor digital sensor mentah
* **Gunakan**: Memeriksa prestasi sensor, tidak ditentukur

### mentah (reflektif)

* **Range**: 0-65,535 (tiff 16-bit) atau 0.0-1.0 (32-bit peratus)
* **Maksudnya**: Refleksi peratus yang dikalibrasi
* **Gunakan**: Pengukuran dan analisis saintifik **Untuk tiff 16-bit:** Bahagikan sebanyak 65,535 untuk mendapatkan pemantulan peratus**untuk peratus 32-bit:** Nilai secara langsung mewakili peratus (0.5 = 50% refleksi)

### mentah (imej indeks)

* **julat**: Berbeza mengikut indeks (biasanya -1.0 hingga +1.0 untuk indeks normal)
* **Arti**: Hasil Pengiraan Indeks
* **Contoh**:
  * NDVI: -1 hingga +1 (tumbuh -tumbuhan biasanya 0.4 hingga 0.9)
  * Ndre: -1 hingga +1 (pengesanan tekanan)
  * EVI: 0 hingga 1 (tumbuh -tumbuhan yang dipertingkatkan)

***

## petua dan amalan terbaik

### Beralih lapisan yang cekap

* **Kesedaran Pintasan Papan Kekunci**: Walaupun tidak ada pintasan papan kekunci untuk lapisan, anak panah navigasi (←/→) bekerja di semua lapisan
* **aliran kerja yang konsisten**: Pilih satu lapisan (mis., NDVI) dan semak semula dataset sebelum beralih ke yang lain
* **perbandingan cepat**: togol antara asal dan refleksi untuk mengesahkan kualiti pemprosesan

### Pertimbangan prestasi

* **JPG memuat terpantas**: Gunakan untuk navigasi cepat melalui banyak gambar
* **Lapisan mentah memuat lebih perlahan**: resolusi yang lebih tinggi dan kedalaman bit
* **Lapisan Indeks**: Kelajuan yang serupa dengan lapisan refleksi
* **Beban pertama adalah paling lambat**: Pandangan seterusnya lapisan yang sama cache dan lebih cepat

### pengesahan kualiti

* **Sentiasa periksa mentah (asal)**: Sahkan kualiti data sumber sebelum mempercayai output diproses
* **Bandingkan Lapisan**: Gunakan lapisan Beralih untuk Mengesahkan Pemprosesan Berfungsi dengan betul
* **Rentang Indeks Semak**: Gunakan mod peratus piksel dengan lapisan indeks untuk mengesahkan nilai adalah munasabah ***

## Penyelesaian masalah

Lapisan ### tidak tersedia**Masalah **: Lapisan yang diharapkan tidak muncul dalam lungsur turun**Kemungkinan Punca:**

* Imej tidak diproses (hanya jpg dan mentah (asal) tersedia)
* Penentukuran refleksi dilumpuhkan semasa pemprosesan
* Indeks khusus tidak dikonfigurasi dalam tetapan projek
* Imej adalah imej sasaran sahaja (tiada indeks yang dihasilkan untuk sasaran)

**Penyelesaian:**

1. Sahkan imej diproses (periksa folder output untuk fail yang diproses)
2. Periksa tetapan projek untuk mengesahkan indeks dikonfigurasikan
3. Pengkolot semula dengan indeks yang diingini diaktifkan

### Lapisan yang salah ditunjukkan**Masalah **: Imej dibuka di lapisan yang tidak dijangka**Sebab **: Keutamaan lapisan dari imej sebelumnya dibawa ke hadapan, tetapi lapisan itu tidak wujud pada imej semasa**Penyelesaian **: Kloros secara automatik akan kembali ke JPG apabila lapisan pilihan tidak tersedia - ini adalah tingkah laku biasa

### tidak dapat melihat sasaran penentukuran**Masalah **: Lapisan mentah (sasaran) tidak menunjukkan pengesanan sasaran**Kemungkinan Punca:**

* Sasaran tidak dikesan semasa pemprosesan
* Imej sebenarnya tidak mengandungi sasaran
* Tetapan pengesanan sasaran terlalu ketat

**Penyelesaian:**

1. Periksa log debug untuk mesej "sasaran dijumpai"
2. Sahkan imej sebenarnya mengandungi sasaran penentukuran yang kelihatan
3. Laraskan tetapan pengesanan sasaran dalam tetapan projek
4. Lihat [memilih imej sasaran](../ pemprosesan-images-gui/memilih-target-imag.md)***

## Ciri -ciri yang berkaitan

### alat penonton imej

Apabila melihat mana -mana lapisan, anda boleh menggunakan:

* **Kawalan Zum**: Magnify untuk memeriksa butiran
* **pan**: Klik dan seret untuk bergerak di sekitar gambar zum
* **Pemeriksaan nilai piksel**: Lihat nilai di lokasi kursor
* **anak panah navigasi**: bergerak antara imej sambil mengekalkan lapisan
* **mod peratus piksel**: togol antara paparan DN dan peratus

Lihat [Membuka Skrin Penuh Imej](pembukaan-image-full-screen.md) untuk dokumentasi penonton imej lengkap.

### Indeks/Lut Sandbox

Untuk ujian dan visualisasi indeks interaktif:

* **Pengiraan indeks masa nyata**: Uji formula indeks yang berbeza
* **Pemetaan Warna Lut**: Sapukan kecerunan warna ke indeks skala kelabu
* **Visualisasi Eksport**: Simpan Imej Indeks Berwarna

Lihat [Index/Lut Sandbox](Index-Lut-Sandbox.md) untuk butirannya.

***

## Langkah seterusnya

Sekarang anda memahami lapisan gambar:

* [ **Membuka Skrin Penuh Imej**](Pembukaan-An-Image-full-Screen.md)-Panduan Penonton Imej Lengkap
* [ **indeks/lut sandbox**](index-lut-sandbox.md)-visualisasi indeks interaktif
* [ **Formula Indeks Multispectral**](../ Project-Settings/Multispectral-index-Formulas.md)-Rujukan indeks yang tersedia
* [ **Menamatkan pemprosesan**](../ pemprosesan-images-gui/penamat-the-processing.md)-Memahami output yang diproses
