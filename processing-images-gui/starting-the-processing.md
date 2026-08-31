# Memulakan Pemprosesan

Setelah anda mengimport imej anda, menandakan sasaran penentukuran anda, dan mengkonfigurasi tetapan projek anda, anda sedia untuk memulakan pemprosesan. Halaman ini membimbing anda melalui permulaan saluran pemprosesan Chloros.

## Senarai Semak Pra-Pemprosesan

Sebelum mengklik butang Mulakan, sahkan bahawa semuanya sudah siap:

* [ ] **Fail diimport** - Semua imej muncul dalam Pelayar Fail
* [ ] **Imej sasaran ditandakan** - Baris Sasaran dicentang untuk imej penentukuran (atau rakaman `.daq` diimport untuk LATTICE)
* [ ] **Model kamera dikesan** - Ruang Model Kamera memaparkan kamera yang betul
* [ ] **Tetapan dikonfigurasikan** - Tetapan Projek telah disemak dan disesuaikan
* [ ] **Indeks terpilih** - Indeks multispektral yang diingini telah ditambah (jika perlu)
* [ ] **Format eksport dipilih** - Format keluaran yang sesuai untuk aliran kerja anda

{% hint style="info" %}
**Petua**: Klik pada beberapa imej dalam Pelayar Fail untuk mengesahkan ia dimuat dengan betul sebelum pemprosesan.
{% endhint %}

***

## Memulakan Pemprosesan

### Cari Butang Mula

Butang Mula/Main terletak di bar tajuk atas Chloros:

* Kedudukan: Tengah atas tetingkap
* Ikon: **Butang Main/Mula** <img src="../.gitbook/assets/image (2) (1) (1).png" alt="" data-size="line">
* Status: Butang diaktifkan (cerah) apabila sedia untuk diproses

### Klik untuk Memulakan

1. Klik **butang Main/Mula** di bar tajuk atas
2. Pemprosesan bermula serta-merta
3. Butang bertukar menjadi butang **Hentikan** semasa pemprosesan
4. Bar kemajuan dikemas kini, memaparkan status pemprosesan

{% hint style="success" %}
**Pemprosesan Dimulakan**: Setelah klik, Chloros secara automatik mengendalikan semua langkah pemprosesan - pengesanan sasaran, debayering, penentukuran, pengiraan indeks, dan eksport. Ia mengesan secara automatik sama ada projek anda adalah Survey3, LATTICE, atau campuran, dan menerapkan saluran yang betul untuk setiap kamera.
{% endhint %}

***

## Memahami Mod Pemprosesan

Chlorosberoperasi dalam dua mod pemprosesan berbeza bergantung pada lesen anda:

### Mod Percuma (Pemprosesan Bersiri)

**Tersedia untuk semua pengguna**

**Bagaimana ia berfungsi:**

* Memproses imej satu persatu, secara bersiri
* Operasi berbenang tunggal
* Penggunaan memori yang lebih rendah

**Bar kemajuan menunjukkan 2 peringkat:**

1.**Pengesanan Sasaran** - Menyeken untuk sasaran penentukuran
2. **Pemprosesan** - Memohon penentukuran dan mengeksport imej**Masa pemprosesan:**

* Lebih perlahan berbanding mod selari Chloros+
* Sesuai untuk set data kecil hingga sederhana (&lt; 200 imej)

### Mod Chloros+ (Pemprosesan Selari)

**Perlukan lesen Chloros+**

**Bagaimana ia berfungsi:**

* Memproses berbilang imej serentak menggunakan [saluran pemprosesan 4-benang](../processing-architecture/processing-pipeline.md)
* [Penyesuaian Komputasi Dinamik](../processing-architecture/dynamic-compute-adaptation.md) secara automatik memilih strategi terbaik untuk perkakasan anda pada permulaan larian
* Pecutan GPU (CUDA) dengan kad grafik NVIDIA (desktop dan Jetson)
* **Jumlah pekerja menyesuaikan mengikut perkakasan**: Strategi GPU menjalankan**1-4 pekerja serentak** (disesuaikan mengikut VRAM — Jetson dengan memori rendah menjalankan 1, GPU desktop 12GB+ menjalankan sehingga 4); Sistem hanya CPU menjalankan satu pekerja bagi setiap teras fizikal, tolak satu**Bar kemajuan menunjukkan 4 peringkat** (sepadan dengan 4 benang paip):

1. **Mengesani** (Benang 1) - Menemukan sasaran penentukuran
2. **Menganalisis** (Thread 2) - Menyemak metadata imej dan mengira penentukuran
3. **Menentukur** (Thread 3) - Debayering, pembetulan vignette, penentukuran, pengiraan indeks
4. **Mengimport** (Thread 4) - Menyimpan imej yang diproses dan indeks**Interaksi bar kemajuan:*** **Tuding tetikus** ke atas bar untuk melihat panel lungsur 4 peringkat terperinci
* **Klik** bar kemajuan untuk membekukan panel lungsur di tempatnya
* **Klik lagi** untuk membuka bekuan dan menyembunyikan panel**Masa pemprosesan:**

*   Lebih pantas dengan ketara berbanding mod percuma
*   Pecutan GPU meningkatkan kelajuan dengan lebih lanjut

{% hint style="info" %}
**Kelajuan Chloros+**: Pemprosesan selari boleh 5-10x lebih pantas daripada mod bersiri untuk set data yang besar. Projek 500 imej yang mengambil masa 2 jam dalam mod percuma mungkin siap dalam 15-20 minit dengan Chloros+.
{% endhint %}

***

## Apa yang Terjadi Semasa Pemprosesan

### Peringkat 1: Pengesanan Sasaran

**Apa yang dilakukan oleh Chloros:**

*   Mengimbas imej yang anda tandakan dalam lajur Sasaran (semua imej jika tiada yang ditandakan)
*   Mengecam panel penentukuran dalam setiap sasaran
*   Mengasingkan nilai pantulan daripada panel sasaran
*   Merekod cap masa sasaran untuk penjadualan penentukuran

**Tempoh:** 1-30 saat (dengan sasaran yang ditandakan), 5-30+ minit (tanpa tanda)

### Fasa 2: Debayering (Penukaran RAW)

**Apa yang dilakukan oleh Chloros:**

* Menukar data corak Bayer RAW kepada imej 3-saluran penuh (modul mono LATTICE kekal satu-gelombang — debayering dilangkau untuknya dengan catatan dalam log)
* Mengaplikasikan algoritma demosaicing yang dipilih
* Memelihara kualiti dan perincian imej maksimum

**Tempoh:** Bergantung pada bilangan imej dan kelajuan CPU/GPU

### Fasa 3: Kalibrasi

**Apa yang dilakukan oleh Chloros:*** **Pembetulan Vignette**: Menghilangkan penggelapan lensa di tepi
* **Kalibrasi pantulan**: Menormalisasi menggunakan nilai pantulan sasaran dan/atau data DAQ downwelling
* Menerapkan pembetulan pada semua jalur/saluran
* Menggunakan rujukan penentukuran yang sesuai untuk setiap imej berdasarkan cop masa

**Tempoh:** Kebanyakan masa pemprosesan

### Tahap 4: Pengiraan Indeks

**Apa yang dilakukan oleh Chloros:**

* Mengira indeks multispektral yang dikonfigurasikan (NDVI, NDRE, dan lain-lain)
* Mengaplikasikan matematik jalur kepada imej yang telah dikalibrasi
* Menghasilkan imej indeks untuk setiap indeks yang dipilih

**Tempoh:** Beberapa saat setiap imej

### Peringkat 5: Eksport

**Apa yang dilakukan oleh Chloros:**

* Menyimpan imej yang diproses dalam format yang dipilih
* **LATTICE fan-out**: setiap bingkai LATTICE mentah dieksport sebagai setiap produk yang diaktifkan dalam satu kali proses — debayered, pratonton, radiance (sentiasa float32), pantulan
* Menulis fail ke dalam pokok keluaran projek: `<project>/<camera>/<format>/<Product>_Images/`
* **Menjaga nama fail sumber** — folder itu mengenal pasti produk, tiada sambungan ditambah**Tempoh:** Berbeza mengikut format eksport dan saiz fail***

## Kelakuan Pemprosesan

### Saluran Pemprosesan Automatik

Setelah dimulakan, keseluruhan saluran akan berjalan secara automatik:

* Tiada campur tangan pengguna diperlukan
* Semua langkah yang dikonfigurasikan dijalankan secara bersiri
* Kemas kini kemajuan dipaparkan secara masa nyata
* Fail yang dieksport ditulis ke cakera apabila ia siap — anda boleh membuka keluaran yang telah selesai sementara proses masih berjalan

### Penggunaan Komputer Semasa Pemprosesan

**Mod Bebas:**

* Penggunaan CPU yang agak rendah (berbenang tunggal)
* Komputer kekal responsif untuk tugas lain
* Selamat untuk meminimumkan Chloros dan bekerja dalam aplikasi lain

**Mod Selari Chloros+:**

* Penggunaan CPU yang tinggi merentasi kolam pekerja strategi
* Dengan pecutan GPU: Penggunaan GPU yang tinggi
* Komputer mungkin kurang responsif semasa pemprosesan
* Elakkan memulakan tugas lain yang memakan banyak CPU

{% hint style="warning" %}
**Petua Prestasi**: Untuk prestasi Chloros+ yang terbaik, tutup aplikasi lain dan biarkan Chloros menggunakan semua sumber sistem.
{% endhint %}

### Pemprosesan Tidak Boleh Dipause (Tetapi Pemberhentian Bersih)

* Setelah dimulakan, pemprosesan tidak boleh dipause dan disambung semula kemudian
* Mengklik **Hentikan** menghentikan pelaksanaan dengan sempurna pada klik pertama
* Produk yang telah dieksport sebelum dihentikan kekal di cakera
* Pelaksanaan yang dihentikan melaporkan dengan tepat apa yang telah disiapkannya (lihat baris `[RUN-SUMMARY]` dalam log)
* Satu larian baru memulakan saluran daripada awal

**Petua perancangan:** Untuk projek yang sangat besar, pertimbangkan untuk memproses secara pukal atau menggunakan CLI untuk kawalan yang lebih baik.***

## Memantau Pemprosesan Anda

Semasa pemprosesan dijalankan, anda boleh:

* **Menonton bar kemajuan** - Lihat peratusan penyiapan keseluruhan
* **Lihat peringkat semasa** - Mengesan, Menganalisis, Kalibrasi, atau Mengeksport
* **Semak tab log** - Lihat mesej pemprosesan terperinci dan amaran
* **Pratinjau imej yang telah selesai** - Fail eksport muncul di cakera semasa pemprosesan

Untuk maklumat terperinci mengenai pemantauan, lihat [Pemantauan Pemprosesan](monitoring-the-processing.md).

***

## Menghentikan Pemprosesan

Jika anda perlu menghentikan pemprosesan:

### Cara Menghentikan

1. Lokasikan butang **Henti** (menggantikan butang Mula semasa pemprosesan)
2. Klik sekali — bar akan memaparkan **&quot;Berhenti...&quot;** sementara imej yang sedang diproses selesai
3. Proses akan berakhir dalam keadaan berhenti sepenuhnya dan log akan mencetak `[RUN-SUMMARY]` yang jujur tentang apa yang telah diselesaikan

### Bila Perlu Berhenti

**Sebab sah untuk berhenti:**

* Sedar tetapan yang salah digunakan
* Terlupa menandakan imej sasaran
* Imej yang salah diimport
* Sistem berjalan terlalu perlahan atau tidak responsif

**Selepas berhenti:**

* Produk yang dieksport sebelum pemberhentian kekal di cakera
* Semak dan baiki sebarang isu, laras tetapan mengikut keperluan
* Mulakan semula pemprosesan — pelaksanaan bermula dari awal

***

## Anggaran Masa Pemprosesan

Masa pemprosesan sebenar berbeza-beza bergantung kepada:

* Bilangan imej
* Resolusi imej
* Format input RAW vs JPG
* Mod pemprosesan (Free vs Chloros+)
* Kelajuan CPU dan bilangan teras
* Ketersediaan GPU (Chloros+ sahaja)
* Bilangan indeks untuk dikira
* Bilangan produk eksport yang diaktifkan (LATTICE)

### Anggaran Kasar (Chloros+, imej 12MP, CPU moden)

| Bilangan Imej | Mod Percuma | Chloros+ (CPU) | Chloros+ (GPU) |
| ----------- | --------- | -------------- | -------------- |
| 50 imej   | 15-20 min | 5-8 min        | 3-5 min        |
| 100 imej  | 30-40 min | 10-15 min      | 5-8 min        |
| 200 imej   | 1-1.5 jam | 20-30 minit   | 10-15 minit   |
| 500 imej   | 2-3 jam   | 45-60 minit   | 20-30 minit   |
| 1000 imej | 4-6 jam   | 1.5-2 jam      | 40-60 min      |

{% hint style="info" %}
**Jalankan Pertama Kali**: Pemprosesan awal mungkin mengambil masa lebih lama kerana Chloros membina cache dan profil. Pemprosesan set data yang serupa selepas ini akan menjadi lebih pantas.
{% endhint %}

***

## Isu Lazim Semasa Memulakan

### Butang Mula Dilumpuhkan (Kelabu)

**Punca yang mungkin:**

* Tiada imej diimport
* Backend belum bermula sepenuhnya
* Pemprosesan sebelumnya masih berjalan
* Projek belum dimuat sepenuhnya

**Penyelesaian:**

1. Tunggu backend dimulakan sepenuhnya (semak ikon menu utama)
2. Semak sama ada imej telah diimport dalam Pelayar Fail
3. Mulakan semula Chloros jika butang masih tidak aktif
4. Semak Log Ralat untuk mesej ralat

### Pemprosesan Bermula Kemudian Segera Gagal

**Punca yang mungkin:**

* Tiada imej sah dalam projek
* Fail imej rosak
* Ruang cakera tidak mencukupi
* Memori (RAM) tidak mencukupi

**Penyelesaian:**

1. Semak Log Perbaikan (Debug Log) <img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line"> untuk mesej ralat
2. Semak ruang cakera yang tersedia
3. Cuba memproses subset imej yang lebih kecil
4. Semak imej tidak rosak

### Proses Berhenti Secara Tiba-Tiba

**Punca yang mungkin:*** Tiada imej sah dalam projek
* Fail imej rosak
* Ruang cakera tidak mencukupi
* Memori (RAM) tidak mencukupi

**Penyelesaian:**

1. Semak Log Ralat  untuk mesej ralat
2. Semak log GUI mencetak petunjuk `[RUN-SUMMARY]` yang menamakan punca yang mungkin — tiada imej diimport, tiada sasaran dikesan, atau setiap produk yang diminta diabaikan kerana tidak terpakai (contohnya meminta rad)

* Log GUI mencetak petunjuk `[RUN-SUMMARY]` yang menamakan punca yang mungkin — tiada imej diimport, tiada sasaran dikesan, atau setiap produk yang diminta diabaikan kerana tidak terpakai (contohnya meminta radiasi/refleksi daripada kamera yang hanya mempunyai RGB)
* Setara CLI (`chloros-cli process`) mencetak `Processing finished but wrote no image products.` dan **keluar dengan nilai tidak sifar**, supaya skrip boleh mengesaninya
* Pelaksanaan khusus hanya untuk metadata (setiap produk eksport dilumpuhkan, tiada indeks) masih dikira sebagai berjaya

Rujuk [Rujukan CLI](../reference/cli-reference.md#a-run-that-writes-no-images-fails) untuk semantik penuh.

### Amaran &quot;Tiada Sasaran Dikesan&quot;

**Punca yang mungkin:**

* Terlupa menandakan imej sasaran
* Imej sasaran tidak mengandungi sasaran yang boleh dilihat
* Tetapan pengesanan sasaran terlalu ketat

**Penyelesaian:**

1. Semak [Memilih Imej Sasaran](choosing-target-images.md)
2. Tandakan imej yang sesuai dalam lajur Sasaran
3. Semak sama ada sasaran dapat dilihat dalam imej yang ditandakan
4. Laras tetapan pengesanan sasaran jika perlu

***

## Petua untuk Pemprosesan Berjaya

### Sebelum Memulakan

1. **Uji dengan subset kecil terlebih dahulu** - Proses 10-20 imej untuk mengesahkan tetapan
2. **Semak ruang cakera yang tersedia** - Pastikan terdapat ruang kosong bersaiz 2-3 kali ganda saiz set data (lebih banyak jika semua produk LATTICE diaktifkan)
3. **Tutup aplikasi yang tidak perlu** - Bebaskan sumber sistem
4. **Semak imej sasaran** - Pratonton sasaran yang ditandakan untuk memastikan kualitinya
5. **Simpan projek** - Projek akan menyimpan secara automatik, tetapi adalah amalan baik untuk menyimpan secara manual

### Semasa Pemprosesan

1. **Elakkan sistem tidur** - Lumpuhkan mod penjimatan kuasa
2. **Jaga agar tetingkap Chloros kekal di latar hadapan** - Atau sekurang-kurangnya kelihatan di bar tugas
3. **Pantau kemajuan sekali-sekala** - Semak amaran atau ralat
4. **Jangan muat turun aplikasi berat lain** - Terutamanya dalam mod selari Chloros+

### Chloros+ Pecutan GPU

Jika menggunakan pecutan GPU NVIDIA:

1. Kemas kini pemacu NVIDIA ke versi terkini
2. Pastikan GPU mempunyai VRAM 4GB+ (7GB+ untuk debayering Texture Aware serentak)
3. Tutup aplikasi yang intensif GPU (permainan, penyuntingan video)
4. Pantau suhu GPU (pastikan penyejukan yang mencukupi)

***

## Langkah Seterusnya

Setelah pemprosesan bermula:

1. **Pantau kemajuan** - Lihat [Memantau Pemprosesan](monitoring-the-processing.md)
2. **Tunggu sehingga selesai** - Pemprosesan berjalan secara automatik
3. **Semak keputusan** - Lihat [Menyiapkan Pemprosesan](finishing-the-processing.md)

Untuk maklumat tentang apa yang perlu dilakukan semasa pemprosesan, lihat [Memantau Pemprosesan](monitoring-the-processing.md).
