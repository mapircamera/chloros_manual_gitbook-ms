# Memulakan Pemprosesan

Setelah anda mengimport imej anda, menandakan sasaran penentukuran anda, dan mengkonfigurasi tetapan projek anda, anda sedia untuk memulakan pemprosesan. Halaman ini membimbing anda melalui permulaan saluran pemprosesan Chloros.

## Senarai Semak Pra-Pemprosesan

Sebelum mengklik butang Mula, sahkan bahawa semuanya sudah siap:

* [ ] **Fail diimport** - Semua imej muncul dalam Pelayar Fail
* [ ] **Imej sasaran ditandakan** - Baris Sasaran disemak untuk imej penentukuran (atau rakaman `.daq` diimport untuk LATTICE)
* [ ] **Model kamera dikesan** - Ruang Model Kamera memaparkan kamera yang betul
* [ ] **Tetapan dikonfigurasikan** - Tetapan Projek disemak dan disesuaikan
* [ ] **Indeks terpilih** - Indeks multispektral yang diingini ditambah (jika perlu)
* [ ] **Format eksport dipilih** - Format output yang sesuai untuk aliran kerja anda

{% hint style="info" %}
**Petua**: Klik beberapa imej dalam Pelayar Fail untuk mengesahkan ia dimuat dengan betul sebelum diproses.
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

Chloros beroperasi dalam dua mod pemprosesan berbeza bergantung pada lesen anda:

### Mod Percuma (Pemprosesan Bersiri)

**Tersedia untuk semua pengguna**

**Bagaimana ia berfungsi:**

* Memproses imej satu persatu, secara bersiri
* Operasi berbenang tunggal
* Penggunaan memori yang lebih rendah

**Bar kemajuan menunjukkan 2 peringkat:**

1.**Pengesanan Sasaran** - Menyeken untuk sasaran penentukuran
2. **Pemprosesan** - Mengaplikasikan penentukuran dan mengeksport imej**Masa pemprosesan:**

*   Lebih perlahan berbanding mod selari Chloros+
*   Sesuai untuk set data kecil hingga sederhana (< 200 imej)

### Mod Chloros+ (Pemprosesan Selari)

**Perlukan lesen Chloros+**

**Bagaimana ia berfungsi:**

* Memproses berbilang imej serentak menggunakan [saluran pemprosesan 4-benang](../processing-architecture/processing-pipeline.md)
* [Penyesuaian Komputasi Dinamik](../processing-architecture/dynamic-compute-adaptation.md) secara automatik memilih strategi terbaik untuk perkakasan anda pada permulaan larian
* Pecutan GPU (CUDA) dengan kad grafik NVIDIA
* **Bilangan pekerja adalah adaptif mengikut perkakasan**: Strategi GPU menjalankan**2-4 pekerja serentak** (disesuaikan mengikut VRAM — 3 pada GPU 2-12GB, 2 hingga 4 pada GPU 12GB+) Sistem CPU sahaja menjalankan satu pekerja bagi setiap teras fizikal, tolak satu**Bar kemajuan menunjukkan 4 peringkat** (berkaitan dengan 4 benang paip):

1. **Mengesani** (Benang 1) - Mencari sasaran penentukuran
2. **Menganalisis** (Benang 2) - Menyemak metadata imej dan mengira penentukuran
3. **Menentukur** (Benang 3) - Debayering, pembetulan viniet, penentukuran, pengiraan indeks
4. **Mengeksport** (Benang 4) - Menyimpan imej yang diproses dan indeks

**Interaksi bar kemajuan:**

* **Tuding tetikus** ke atas bar untuk melihat panel lungsur 4 peringkat terperinci
* **Klik** bar kemajuan untuk membekukan panel lungsur di tempatnya
* **Klik sekali lagi** untuk membuka bekuan dan menyembunyikan panel**Masa pemprosesan:**

*   Lebih pantas dengan ketara berbanding mod percuma
*   Pecutan GPU meningkatkan kelajuan dengan lebih lanjut

{% hint style="info" %}
**Kelajuan Chloros+**: Pemprosesan selari boleh 5-10 kali lebih pantas daripada mod bersiri untuk set data yang besar. Projek 500 imej yang mengambil masa 2 jam dalam mod percuma mungkin siap dalam 15-20 minit dengan Chloros+.
{% endhint %}

***

## Apa Yang Terjadi Semasa Pemprosesan

### Peringkat 1: Pengesanan Sasaran

**Apa yang dilakukan oleh Chloros:**

* Mengimbas imej yang anda tandakan dalam lajur Sasaran (semua imej jika tiada yang ditandakan)
* Mengecam panel penentukuran dalam setiap sasaran
* Mengambil nilai pantulan daripada panel sasaran
* Merekod cap masa sasaran untuk penjadualan kalibrasi

**Tempoh:** 1-30 saat (dengan sasaran yang ditandakan), 5-30+ minit (tidak ditandakan)

### Peringkat 2: Debayering (Penukaran RAW)

**Apa yang dilakukan oleh Chloros:**

* Menukar data corak Bayer RAW kepada imej 3-saluran penuh (modul LATTICE mono kekal satu-gelombang — debayering dilangkau untuknya dengan nota dalam log)
* Mengaplikasikan algoritma demosaicing yang dipilih
* Memelihara kualiti dan perincian imej maksimum

**Tempoh:** Bergantung pada bilangan imej dan kelajuan CPU/GPU

### Fasa 3: Kalibrasi

**Apa yang dilakukan oleh Chloros:**

* **Pembetulan Vignette**: Menghilangkan penggelapan lensa di tepi
* **Kalibrasi reflektansi**: Menormalkan menggunakan nilai reflektansi sasaran dan/atau data DAQ downwelling
* Menerapkan pembetulan pada semua jalur/saluran
* Menggunakan rujukan kalibrasi yang sesuai untuk setiap imej berdasarkan cop masa

**Tempoh:** Kebanyakan masa pemprosesan

### Fasa 4: Pengiraan Indeks

**Apa yang dilakukan oleh Chloros:**

* Mengira indeks multispektral yang dikonfigurasikan (NDVI, NDRE, dan lain-lain)
* Mengaplikasikan matematik jalur pada imej yang telah dikalibrasi
* Menghasilkan imej indeks untuk setiap indeks yang dipilih

**Tempoh:** Beberapa saat setiap imej

### Fasa 5: Eksport

**Apa yang dilakukan oleh Chloros:**

* Menyimpan imej yang diproses dalam format yang dipilih
* **LATTICE fan-out**: setiap bingkai LATTICE mentah dieksport sebagai setiap produk yang diaktifkan dalam satu kali proses — debayered, pratonton, sinaran (sentiasa float32), pantulan
* Menulis fail ke dalam pokok output projek: `<project>/<camera>/<format>/<Product>_Images/`
* **Menjaga nama fail sumber** — folder mengenal pasti produk, tiada sambungan ditambah**Tempoh:** Berbeza mengikut format eksport dan saiz fail***

## Kelakuan Pemprosesan

### Saluran Pemprosesan Automatik

Setelah dimulakan, keseluruhan saluran akan berjalan secara automatik:

* Tiada interaksi pengguna diperlukan
* Semua langkah yang dikonfigurasikan dijalankan secara bersiri
* Kemas kini kemajuan dipaparkan secara masa nyata
* Fail yang dieksport ditulis ke cakera sebaik sahaja ia siap — anda boleh membuka keluaran yang telah selesai sementara proses masih berjalan

### Penggunaan Komputer Semasa Pemprosesan

**Mod Bebas:**

* Penggunaan CPU yang agak rendah (berbenang tunggal)
* Komputer kekal responsif untuk tugas lain
* Selamat untuk meminimumkan Chloros dan bekerja dalam aplikasi lain

**Mod Paralel Chloros+:**

* Penggunaan CPU yang tinggi di seluruh kumpulan pekerja strategi
* Dengan pecutan GPU: Penggunaan GPU yang tinggi
* Komputer mungkin kurang responsif semasa pemprosesan
* Elakkan memulakan tugas lain yang memakan banyak CPU

{% hint style="warning" %}
**Petua Prestasi**: Untuk prestasi Chloros+ yang terbaik, tutup aplikasi lain dan biarkan Chloros menggunakan semua sumber sistem.
{% endhint %}

### Pemprosesan Tidak Boleh Dipause (Tetapi Pemberhentian Bersih)

* Setelah dimulakan, pemprosesan tidak boleh ditangguhkan dan disambung semula kemudian
* Mengklik **Hentikan** menghentikan pelaksanaan dengan sempurna pada klik pertama
* Produk yang telah dieksport sebelum dihentikan kekal di cakera
* Pelaksanaan yang dihentikan melaporkan dengan jujur apa yang telah disiapkannya (lihat baris `[RUN-SUMMARY]` dalam log)
* Satu larian baru memulakan saluran daripada awal

**Petua perancangan:** Untuk projek yang sangat besar, pertimbangkan untuk memproses secara bersiri atau menggunakan CLI untuk kawalan yang lebih baik.

***

## Memantau Pemprosesan Anda

Semasa pemprosesan dijalankan, anda boleh:

* **Pantau bar kemajuan** - Lihat peratusan penyempurnaan keseluruhan
* **Lihat peringkat semasa** - Mengesan, Menganalisis, Menentukur, atau Mengeksport
* **Semak tab log** - Lihat mesej pemprosesan terperinci dan amaran
* **Pratinjau imej yang telah selesai** - Fail eksport muncul di cakera semasa pemprosesan

Untuk maklumat terperinci mengenai pemantauan, lihat [Pemantauan Pemprosesan](monitoring-the-processing.md).

***

## Menghentikan Pemprosesan

Jika anda perlu menghentikan pemprosesan:

### Cara Menghentikan

1. Lokasikan butang **Henti** (menggantikan butang Mula semasa pemprosesan)
2. Klik sekali — bar akan memaparkan **"Berhenti..."** sementara imej yang sedang diproses selesai
3. Proses akan berakhir dalam keadaan berhenti sepenuhnya dan log akan mencetak laporan `[RUN-SUMMARY]` yang jujur tentang apa yang telah diselesaikan

### Bila hendak Berhenti

**Alasan sah untuk berhenti:**

* Sedar tetapan yang salah telah digunakan
* Terlupa menandakan imej sasaran
* Imej yang salah diimport
* Sistem berjalan terlalu perlahan atau tidak responsif

**Selepas berhenti:**

* Produk yang dieksport sebelum pemberhentian kekal di cakera
* Semak dan baiki sebarang isu, laras tetapan mengikut keperluan
* Mulakan semula pemprosesan — pelaksanaan bermula dari awal

***

## Anggaran Masa Pemprosesan

Masa pemprosesan sebenar berbeza dengan ketara berdasarkan:

* Bilangan imej
* Resolusi imej
* Format input RAW vs JPG
* Mod pemprosesan (Percuma vs Chloros+)
* Kelajuan CPU dan bilangan teras
* Ketersediaan GPU (Chloros+ sahaja)
* Bilangan indeks untuk dikira
* Bilangan produk eksport yang diaktifkan (LATTICE)

### Anggaran Kasar (Chloros+, imej 12MP, CPU moden)

| Bilangan Imej | Mod Percuma | Chloros+ (CPU) | Chloros+ (GPU) |
| ----------- | --------- | -------------- | -------------- |
| 50 imej   | 15-20 min | 5-8 min        | 3-5 min        |
| 100 imej  | 30-40 min | 10-15 min      | 5-8 min        |
| 200 imej   | 1-1.5 jam | 20-30 minit | 10-15 minit |
| 500 imej   | 2-3 jam  | 45-60 minit | 20-30 minit |
| 1000 imej | 4-6 jam   | 1.5-2 jam      | 40-60 min      |

{% hint style="info" %}
**Jalankan Pertama Kali**: Pemprosesan awal mungkin mengambil masa lebih lama kerana Chloros membina cache dan profil. Pemprosesan susulan bagi set data yang serupa akan menjadi lebih pantas.
{% endhint %}

***

## Isu Biasa Semasa Memulakan

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

1. Semak Log Ralat <img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line"> untuk mesej ralat
2. Semak ruang cakera yang tersedia
3. Cuba memproses subset imej yang lebih kecil
4. Semak imej tidak rosak

### Proses Berhenti Tetapi Tidak Menulis Imej

Sebuah proses yang meminta produk imej tetapi tidak menulis sebarang imej dianggap sebagai **kegagalan, bukan kejayaan** — Chloros melaporkannya dengan jelas:

* Log GUI mencetak petunjuk `[RUN-SUMMARY]` yang menamakan punca yang mungkin — tiada imej diimport, tiada sasaran dikesan, atau setiap produk yang diminta dilangkau kerana tidak terpakai (contohnya meminta radiasi/refleksi daripada kamera hanya RGB)
* Setara CLI (`chloros-cli process`) mencetak `Processing finished but wrote no image products.` dan **keluar dengan nilai tidak sifar**, supaya skrip boleh mengdeteksinya
* Pelaksanaan khusus hanya untuk metadata (setiap produk eksport dilumpuhkan, tiada indeks) masih dikira sebagai kejayaan

Rujuk [Rujukan [CLI]](../reference/cli-reference.md#a-run-that-writes-no-images-fails) untuk semantik penuh.

### Folder Projek Terlalu Dalam untuk Komputer Ini

**Hanya untuk Windows, baharu dalam 1.3.0.**Windows dihantar dengan laluan panjang dimatikan, dan tiada laluan fail boleh lebih panjang daripada 260 aksara. Produk Chloros terletak sehingga kira-kira 130 aksara di bawah folder projek, jadi folder projek yang lebih dalam daripada itu tidak dapat menerima produknya. Daripada menjalankan dan tidak menulis apa-apa, Chloros kini menyemak sebelum pemprosesan bermula:

* Mengklik Start tidak memulakan larian: bar kemajuan menunjukkan sebab, dan Log Ralat (Debug Log) memaparkan `[PROCESS] refused: The project folder is too deep for this computer…`. Mesej itu memberikan panjang folder projek dan folder projek terpanjang yang muat.
* Mencipta atau membuka projek dalam folder yang terlalu dalam walaupun untuk fail projek itu sendiri juga akan ditolak. Petak mesej menyatakan sebab, dengan panjang folder projek terpanjang yang sesuai dan cara untuk mengaktifkan laluan panjang, dan tiada apa-apa yang diwujudkan atau dibuka; Logs Debug merekodkannya sebagai `[PROJECT] refused: …`. Menduplikasi projek di bawah nama yang lebih panjang juga boleh melebihi had: salinan dibuat tetapi tidak dibuka, dan mesej yang sama menyatakan sebabnya.

**Penyelesaian:**

1. Simpan projek dalam folder yang lebih pendek — ubah**Direktori Kerja** dalam [Seting Projek](../project-settings/project-settings.md) dan cipta semula projek itu, atau
2. Aktifkan laluan panjang Windows dan mulakan semula Chloros: jalankan semula pemasang Chloros dengan kotak **Benarkan laluan panjang Windows** dicentang (ia dicentang secara lalai), atau gunakan Dasar Kumpulan: *Konfigurasi Komputer > Templat Pentadbiran > Sistem > Sistem Fail > Benarkan laluan panjang Win32*.

### Amaran "Tiada Sasaran Dikesan"

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
2. **Semak ruang cakera yang tersedia** - Pastikan terdapat ruang kosong bersaiz 2-3x saiz set data (lebih banyak jika semua produk LATTICE diaktifkan)
3. **Tutup aplikasi yang tidak perlu** - Bebaskan sumber sistem
4. **Semak imej sasaran** - Pratonton sasaran yang ditandakan untuk memastikan kualitinya
5. **Simpan projek** - Projek akan menyimpan secara automatik, tetapi adalah amalan yang baik untuk menyimpan secara manual

### Semasa Pemprosesan

1. **Elakkan sistem tidur** - Lumpuhkan mod penjimatan kuasa
2. **Jaga agar Chloros berada di latar hadapan** - Atau sekurang-kurangnya kelihatan di bar tugas
3. **Pantau kemajuan dari semasa ke semasa** - Semak amaran atau ralat
4. **Jangan muat turun aplikasi berat lain** - Terutamanya dalam mod selari Chloros+

### Pecutan GPU Chloros+

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
