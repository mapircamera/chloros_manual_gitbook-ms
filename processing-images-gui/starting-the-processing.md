# Memulakan Pemprosesan

Setelah anda mengimport imej anda, menandakan sasaran penentukuran anda dan mengkonfigurasikan tetapan projek anda, anda sudah bersedia untuk memulakan pemprosesan. Halaman ini membimbing anda untuk memulakan saluran pemprosesan Chloros.

## Senarai Semak Pra-Pemprosesan

Sebelum mengklik butang Mula, sahkan bahawa semuanya sudah sedia:

* [ ] **Fail diimport** - Semua imej muncul dalam Pelayar Fail
* [ ] **Imej sasaran ditanda** - Lajur sasaran disemak untuk imej penentukuran
* [ ] **Model kamera dikesan** - Lajur Model Kamera menunjukkan kamera yang betul
* [ ] **Tetapan dikonfigurasikan** - Tetapan Projek disemak dan dilaraskan
* [ ] **Indeks dipilih** - Indeks berbilang spektrum yang dikehendaki ditambah (jika perlu)
* [ ] **Format eksport dipilih** - Format output sesuai untuk aliran kerja anda

{% hint style="info" %}
**Petua**: Klik beberapa imej dalam Pelayar Fail untuk mengesahkan ia dimuatkan dengan betul sebelum memproses.
Petua {% %}

***

## Memulakan Pemprosesan

### Cari Butang Mula

Butang Mula/Main terletak di bar pengepala atas Chloros:

* Kedudukan: Tengah atas tingkap
* Ikon: **Butang Main/Mula** <img src="../.gitbook/assets/image (2) (1).png" alt="" data-size="line">
* Status: Butang didayakan (terang) apabila sedia untuk diproses

### Klik untuk Mula

1. Klik butang **Main/Mula** dalam pengepala atas
2. Pemprosesan bermula serta-merta
3. Butang menjadi dilumpuhkan (kelabukan) semasa pemprosesan
4. Kemas kini bar kemajuan, menunjukkan status pemprosesan

{% gaya petunjuk="berjaya" %}
**Pemprosesan Dimulakan**: Setelah diklik, Chloros secara automatik mengendalikan semua langkah pemprosesan - pengesanan sasaran, debayering, penentukuran, pengiraan indeks dan eksport.
Petua {% %}

***

## Memahami Mod Pemprosesan

Chloros beroperasi dalam dua mod pemprosesan berbeza bergantung pada lesen anda:

### Mod Percuma (Pemprosesan Berjujukan)

**Tersedia untuk semua pengguna**

**Cara ia berfungsi:**

* Memproses imej satu demi satu, secara berurutan
* Operasi berulir tunggal
* Penggunaan memori yang lebih rendah

**Bar kemajuan menunjukkan 2 peringkat:**

1.**Target Detect** - Mengimbas untuk sasaran penentukuran
2. **Memproses** - Menggunakan penentukuran dan mengeksport imej**Masa pemprosesan:**

* Jauh lebih perlahan daripada mod selari Chloros+
* Sesuai untuk set data kecil hingga sederhana (< 200 imej)

### Mod Chloros+ (Pemprosesan Selari)

**Memerlukan lesen Chloros+**

**Cara ia berfungsi:**

* Memproses berbilang imej serentak
* Operasi berbilang benang (sehingga 16 pekerja selari)
* Menggunakan berbilang teras CPU
* Pilihan pecutan GPU (CUDA) dengan kad grafik NVIDIA

**Bar kemajuan menunjukkan 4 peringkat:**

1.**Mengesan** - Mencari sasaran penentukuran
2. **Menganalisis** - Memeriksa metadata imej dan menyediakan saluran paip
3. **Menentukur** - Menggunakan pembetulan dan penentukuran
4. **Mengeksport** - Menyimpan imej dan indeks yang diproses**Interaksi bar kemajuan:*** **Tuding tetikus** di atas bar untuk melihat panel lungsur turun 4 peringkat terperinci
* **Klik** bar kemajuan untuk membekukan panel lungsur di tempatnya
* **Klik sekali lagi** untuk menyahbeku dan menyembunyikan panel**Masa pemprosesan:**

* Jauh lebih pantas daripada mod percuma
* Skala dengan kiraan teras CPU
* Pecutan GPU meningkatkan lagi kelajuan

{% gaya pembayang="info" %}
**Chloros+ Speed**: Pemprosesan selari boleh 5-10x lebih pantas daripada mod berjujukan untuk set data yang besar. Projek 500 imej yang mengambil masa 2 jam dalam mod percuma mungkin selesai dalam 15-20 minit dengan Chloros+.
Petua {% %}

***

## Perkara Yang Berlaku Semasa Pemprosesan

### Peringkat 1: Pengesanan Sasaran

**Apa yang dilakukan oleh Chloros:**

* Mengimbas imej sasaran bertanda (atau semua imej jika tiada bertanda)
* Mengenal pasti 4 panel penentukuran dalam setiap sasaran
* Mengekstrak nilai pantulan daripada panel sasaran
* Merekod cap masa sasaran untuk penjadualan penentukuran

**Tempoh:** 1-30 saat (dengan sasaran yang ditanda), 5-30+ minit (tidak bertanda)

### Peringkat 2: Debayering (Penukaran RAW)

**Apa yang dilakukan oleh Chloros:**

* Menukar data corak RAW Bayer kepada imej RGB penuh
* Menggunakan algoritma demosaicing berkualiti tinggi
* Mengekalkan kualiti dan perincian imej maksimum

**Tempoh:** Berbeza mengikut kiraan imej dan kelajuan CPU

### Peringkat 3: Penentukuran

**Apa yang dilakukan oleh Chloros:*** **Pembetulan vignet**: Menghilangkan gelap kanta di tepi
* **Penentukuran pantulan**: Menormalkan menggunakan nilai pemantulan sasaran
* Menggunakan pembetulan merentas semua jalur/saluran
* Menggunakan sasaran penentukuran yang sesuai untuk setiap imej berdasarkan cap masa

**Tempoh:** Majoriti masa pemprosesan

### Peringkat 4: Pengiraan Indeks

**Apa yang dilakukan oleh Chloros:**

* Mengira indeks berbilang spektrum yang dikonfigurasikan (NDVI, NDRE, dsb.)
* Menggunakan matematik jalur pada imej yang ditentukur
* Menjana imej indeks untuk setiap indeks yang dipilih

**Tempoh:** Beberapa saat setiap imej

### Peringkat 5: Eksport

**Apa yang dilakukan oleh Chloros:**

* Menyimpan imej yang ditentukur dalam format yang dipilih
* Mengeksport imej indeks dengan warna LUT yang dikonfigurasikan
* Menulis fail ke subfolder model kamera
* Mengekalkan nama fail asal dengan akhiran

**Tempoh:** Berbeza mengikut format eksport dan saiz fail***

## Kelakuan Memproses

### Saluran Paip Pemprosesan Automatik

Sebaik sahaja dimulakan, keseluruhan saluran paip berjalan secara automatik:

* Tiada interaksi pengguna diperlukan
* Semua langkah yang dikonfigurasikan dilaksanakan mengikut urutan
* Kemas kini kemajuan ditunjukkan dalam masa nyata

### Penggunaan Komputer Semasa Pemprosesan

**Mod Percuma:**

* Penggunaan CPU yang agak rendah (berbenang tunggal)
* Komputer kekal responsif untuk tugasan lain
* Selamat untuk meminimumkan Chloros dan berfungsi dalam aplikasi lain

**Chloros+ Mod Selari:**

* Penggunaan CPU yang tinggi (berbilang benang, sehingga 16 teras)
* Dengan pecutan GPU: Penggunaan GPU yang tinggi
* Komputer mungkin kurang responsif semasa pemprosesan
* Elakkan memulakan tugas intensif CPU yang lain

{% gaya petunjuk="amaran" %}
**Petua Prestasi**: Untuk prestasi Chloros+ terbaik, tutup aplikasi lain dan biarkan Chloros menggunakan sumber sistem penuh.
Petua {% %}

### Pemprosesan Tidak Boleh Dijeda

**Had penting:**

* Setelah dimulakan, pemprosesan tidak boleh dijeda
* Anda boleh membatalkan pemprosesan, tetapi kemajuan hilang
* Keputusan separa tidak disimpan
* Mesti dimulakan semula dari awal jika dibatalkan

**Petua perancangan:** Untuk projek yang sangat besar, pertimbangkan untuk memproses secara berkelompok atau menggunakan CLI untuk kawalan yang lebih baik.***

## Memantau Pemprosesan Anda

Semasa pemprosesan berjalan, anda boleh:

* **Tonton bar kemajuan** - Lihat peratusan penyiapan keseluruhan
* **Lihat peringkat semasa** - Kesan, Analisis, Kalibrasi atau Eksport
* **Semak tab log** - Lihat mesej dan amaran pemprosesan terperinci
* **Pratonton imej yang telah siap** - Sesetengah fail eksport mungkin muncul semasa pemprosesan

Untuk maklumat terperinci tentang pemantauan, lihat [Memantau Pemprosesan](monitoring-the-processing.md).

***

## Membatalkan Pemprosesan

Jika anda perlu menghentikan pemprosesan:

### Cara Membatalkan

1. Cari **butang Berhenti/Batal** (menggantikan butang Mula semasa pemprosesan)
2. Klik butang Berhenti
3. Pemprosesan dihentikan serta-merta
4. Keputusan separa dibuang

### Bila Batal

**Sebab yang sah untuk membatalkan:**

* Menyedari tetapan yang salah telah digunakan
* Terlupa untuk menandakan imej sasaran
* Imej salah diimport
* Sistem berjalan terlalu perlahan atau tidak bertindak balas

**Selepas membatalkan:**

* Semak dan selesaikan sebarang isu
* Laraskan tetapan mengikut keperluan
* Mulakan semula pemprosesan dari awal
* Untuk pengalaman paling bersih, tutup sepenuhnya Chloros dan mulakan semula

{% gaya petunjuk="amaran" %}
**Tiada Keputusan Separa**: Pembatalan membuang semua kemajuan. Chloros tidak menyimpan imej yang diproses separa.
Petua {% %}

***

## Anggaran Masa Pemprosesan

Masa pemprosesan sebenar sangat berbeza berdasarkan:

* Bilangan imej
* Resolusi imej
* Format input RAW vs JPG
* Mod pemprosesan (Percuma lwn Chloros+)
* Kelajuan CPU dan kiraan teras
* Ketersediaan GPU (Chloros+ sahaja)
* Bilangan indeks untuk dikira
* Kerumitan format eksport

### Anggaran Kasar (Chloros+, imej 12MP, CPU moden)

| Kiraan Imej | Mod Percuma | Chloros+ (CPU) | Chloros+ (GPU) |
| ----------- | --------- | -------------- | -------------- |
| 50 imej | 15-20 min | 5-8 min | 3-5 min |
| 100 imej | 30-40 min | 10-15 min | 5-8 min |
| 200 imej | 1-1.5 jam | 20-30 min | 10-15 min |
| 500 imej | 2-3 jam | 45-60 min | 20-30 min |
| 1000 imej | 4-6 jam | 1.5-2 jam | 40-60 min |

{% hint style="info" %}
**Larian Pertama**: Pemprosesan awal mungkin mengambil masa lebih lama kerana Chloros membina cache dan profil. Pemprosesan seterusnya set data serupa akan menjadi lebih cepat.
{% petua %}

***

## Isu Biasa pada Mula

### Butang Mula Dilumpuhkan (Berkelabu)

**Punca yang mungkin:**

* Tiada imej diimport
* Bahagian belakang tidak dimulakan sepenuhnya
* Pemprosesan sebelumnya masih berjalan
* Projek tidak dimuatkan sepenuhnya

**Penyelesaian:**

1. Tunggu hujung belakang untuk dimulakan sepenuhnya (semak ikon menu utama)
2. Sahkan imej diimport dalam Pelayar Fail
3. Mulakan semula Chloros jika butang kekal dilumpuhkan
4. Semak Log Nyahpepijat untuk mendapatkan mesej ralat

### Pemprosesan Bermula Kemudian Gagal Serta-merta

**Punca yang mungkin:**

* Tiada imej yang sah dalam projek
* Fail imej rosak
* Ruang cakera tidak mencukupi
* Memori (RAM) tidak mencukupi

**Penyelesaian:**

1. Semak Log Nyahpepijat <img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line"> untuk mendapatkan mesej ralat
2. Sahkan ruang cakera tersedia
3. Cuba memproses subset imej yang lebih kecil
4. Sahkan imej tidak rosak

### Amaran "Tiada Sasaran Dikesan".

**Punca yang mungkin:**

* Terlupa untuk menandakan imej sasaran
* Imej sasaran tidak mengandungi sasaran yang boleh dilihat
* Tetapan pengesanan sasaran terlalu ketat

**Penyelesaian:**

1. Semak [Memilih Imej Sasaran](choosing-target-images.md)
2. Tandakan imej yang sesuai dalam lajur Sasaran
3. Sahkan sasaran kelihatan dalam imej bertanda
4. Laraskan tetapan pengesanan sasaran jika perlu

***

## Petua Pemprosesan yang Berjaya

### Sebelum Bermula

1. **Uji dengan subset kecil dahulu** - Proses 10-20 imej untuk mengesahkan tetapan
2. **Semak ruang cakera yang tersedia** - Pastikan saiz set data 2-3x percuma
3. **Tutup aplikasi yang tidak diperlukan** - Kosongkan sumber sistem
4. **Sahkan imej sasaran** - Pratonton sasaran bertanda untuk memastikan kualiti
5. **Simpan projek** - Projek simpan secara automatik, tetapi amalan yang baik untuk menyimpan secara manual

### Semasa Pemprosesan

1. **Elakkan sistem tidur** - Lumpuhkan mod penjimatan kuasa
2. **Kekalkan Chloros di latar depan** - Atau sekurang-kurangnya kelihatan dalam bar tugas
3. **Pantau kemajuan sekali-sekala** - Semak amaran atau ralat
4. **Jangan muatkan aplikasi berat lain** - Terutamanya dengan mod selari Chloros+

### Chloros+ Pecutan GPU

Jika menggunakan pecutan GPU NVIDIA:

1. Kemas kini pemacu NVIDIA kepada versi terkini
2. Pastikan GPU mempunyai 4GB+ VRAM
3. Tutup aplikasi intensif GPU (permainan, penyuntingan video)
4. Pantau suhu GPU (pastikan penyejukan yang mencukupi)

***

## Langkah Seterusnya

Setelah pemprosesan telah bermula:

1. **Pantau kemajuan** - Lihat [Memantau Pemprosesan](monitoring-the-processing.md)
2. **Tunggu sehingga selesai** - Pemprosesan berjalan secara automatik
3. **Semakan keputusan** - Lihat [Menyelesaikan Pemprosesan](finishing-the-processing.md)

Untuk maklumat tentang perkara yang perlu dilakukan semasa pemprosesan, lihat [Memantau Pemprosesan](monitoring-the-processing.md).