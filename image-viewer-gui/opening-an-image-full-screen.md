# Membuka Skrin Penuh Imej

Chloros Image Viewer menyediakan antara muka skrin penuh khusus untuk melihat, menganalisis dan memanipulasi imej berbilang spektrum anda. Sama ada melihat imej asal atau output yang diproses, Pemapar Imej menawarkan alat yang berkuasa untuk pemeriksaan dan analisis.

## Mengakses Pemapar Imej

### Daripada Pelayar Fail

Cara paling biasa untuk membuka imej dalam Pemapar Imej:

1. Pastikan anda berada dalam tab **Pelayar Fail** <img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line">
2. Klik mana-mana **gambar kecil imej** dalam grid imej
3. Imej dibuka dalam **kawasan pratonton utama** (tengah skrin)
4. Imej kini dimuatkan dan sedia untuk tontonan skrin penuh

### Membuka Tab Pemapar Imej

Sebaik sahaja imej dimuatkan dalam kawasan pratonton:

1. Klik ikon **Pemapar Imej** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> di bar sisi kiri
2. Tab Pemapar Imej dibuka, memaparkan imej skrin penuh yang dipilih
3. Alat tontonan dan analisis lanjutan tersedia di bar sisi kiri

***

## Gambaran Keseluruhan Antara Muka Pemapar Imej

### Kawasan Paparan Utama

Bahagian terbesar skrin menunjukkan imej anda:

* **Leraian penuh**: Imej dipaparkan pada resolusi asli
* **Boleh Zum**: Gunakan kawalan atau roda tetikus untuk mengezum
* **Boleh Pann**: Klik dan seret untuk bergerak apabila dizum
* **Nisbah aspek dikekalkan**: Skala imej secara berkadar***

## Pilihan Melihat

### Navigasi Imej Asas

#### Semak imbas Imej

Navigasi melalui set imej anda menggunakan pintasan papan kekunci atau butang:

* **Imej seterusnya**: Klik butang → atau tekan kekunci**→** (Anak Panah Kanan).
* **Imej sebelumnya**: Klik butang ← atau tekan kekunci**←** (Anak Panah Kiri)
* **Lompat ke imej tertentu**: Kembali ke Pelayar Fail dan klik lakaran kecil yang diingini

#### Kawalan Zum

Laraskan pembesaran untuk memeriksa butiran imej:

**Zum Masuk:*** Klik butang **+** (Plus).
* Tekan kekunci ******atau**=*** Tatal roda tetikus **ke atas**

**Zum Keluar:*** Klik butang **−** (Tolak).
* Tekan kekunci **−** (Tolak).
* Tatal roda tetikus **ke bawah**

#### Sorot Apabila Dizum

Apabila dizum masuk melebihi saiz skrin:

1. Gerakkan kursor tetikus ke atas imej
2. Klik dan **tahan butang kiri tetikus**

3.**Seret** untuk mengalihkan imej
4. Lepaskan untuk berhenti menyorot

**Alternatif**: Gunakan kekunci anak panah untuk menyorot dalam kenaikan kecil***

## Pemeriksaan Nilai Piksel

### Melihat Nilai Piksel pada Kursor

Semasa anda menggerakkan kursor tetikus anda ke atas imej, nilai piksel dipaparkan dalam masa nyata:**Lokasi paparan nilai:*** **Nombor terapung dan garis merah dalam indeks sebelah kanan LUT lagenda kecerunan*** **Apabila dizum masuk lebih jauh, nilai terapung berhampiran kursor dan piksel yang diserlahkan*** Menunjukkan nilai untuk piksel **di bawah kursor atau diserlahkan*** Kemas kini semasa anda menggerakkan tetikus

***

## Jenis Imej Anda Boleh Lihat

### JPG

**Imej JPG daripada kamera:**

* Paparkan data JPG seperti yang dipratonton
* Tunjukkan nilai asal yang tidak diperbetulkan
* Berguna untuk menyemak kualiti imej sebelum diproses

### RAW (Asal)

### RAW (Pantulan)

**Selepas diproses:**

* Vignette diperbetulkan
* Pantulan ditentukur
* TIFF berbilang jalur (Red, Green, NIR, dsb.)
* Data saintifik sedia untuk dianalisis

### RAW (Indeks)

**NDVI, NDRE, GNDVI, dsb. (\_NDVI.tif files):**

* Imej skala kelabu jalur tunggal
* Nilai piksel mewakili hasil pengiraan indeks
* Julat biasanya -1 hingga +1 untuk indeks ternormal
* Boleh menggunakan LUT warna untuk visualisasi

***

## Aplikasi Indeks dan LUT

Gunakan indeks berbilang spektrum dan Jadual Carian berwarna:

1. Cari **Kotak Pasir Indeks/LUT**dalam**Pemapar Imej** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> bar sisi
2. Pilih indeks tumbuh-tumbuhan (NDVI, NDRE, dsb.)
3. Pilih formula berbilang spektrum, atau cipta formula tersuai anda sendiri (Chloros+ sahaja)
4. Gunakan kecerunan LUT warna untuk visualisasi
5. Laraskan julat nilai dan ambang

Lihat [Index/LUT Sandbox](index-lut-sandbox.md) untuk mendapatkan arahan terperinci.

***

## Pintasan Papan Kekunci

### Navigasi

* **→** (Anak Panah Kanan): Imej seterusnya
* **←** (Anak Panah Kiri): Imej sebelumnya
* **Rumah**: Imej pertama dalam senarai
* **Tamat**: Imej terakhir dalam senarai

### Zum

* ******atau**=**: Zum masuk
* **−**: Zum keluar
* **Roda Tetikus**: Zum masuk/keluar***

### Mengesahkan Pengiraan Indeks

Semak bahawa indeks dikira dengan betul:

1. Buka NDVI atau imej indeks lain
2. Semak kawasan tumbuh-tumbuhan:
   * **NDVI**: Harus menunjukkan 0.4-0.9 untuk tumbuhan yang sihat
   * **NDRE**: Nilai yang lebih tinggi untuk pertumbuhan yang cergas
   * **GNDVI**: Serupa dengan NDVI tetapi sensitif terhadap klorofil
3. Periksa bukan tumbuh-tumbuhan:
   * **Tanah**: Berhampiran 0 atau negatif sedikit
   * **Air**: Nilai negatif (-0.5 hingga 0)***

## Menyelesaikan Masalah Melihat

### Imej Tidak Akan Dibuka

**Punca yang mungkin:**

* Fail rosak semasa pemprosesan
* Format fail tidak disokong
* Memori tidak mencukupi untuk imej besar

**Penyelesaian:**

1. Cuba buka dalam pemapar luaran untuk mengesahkan integriti fail
2. Semak format fail sepadan dengan jenis yang dijangkakan
3. Tutup aplikasi lain untuk membebaskan memori
4. Cuba imej yang lebih kecil/berbeza

### Paparan Imej Hitam atau Putih

**Punca yang mungkin:**

* Julat nilai di luar keupayaan paparan
* Imej apungan 32-bit dengan nilai luar biasa
* Ralat pengiraan indeks

**Penyelesaian:**

1. Semak nilai piksel - jika semuanya sangat rendah atau sangat tinggi, laraskan julat paparan
2. Cuba buka dalam QGIS atau serupa dengan pelarasan julat automatik
3. Semak Log Nyahpepijat daripada pemprosesan untuk ralat

### Nilai Piksel Nampak Salah

**Punca yang mungkin:**

* Melihat imej yang salah (asal vs diproses)
* Penentukuran tidak digunakan dengan betul
* Data penderia cahaya tidak disertakan dalam input
* Mod peratusan ditogol secara tidak betul

**Penyelesaian:**

1. Sahkan anda melihat output yang diproses (semak akhiran nama fail)
2. Semak keadaan butang mod peratus
3. Bandingkan dengan imej yang diketahui baik daripada set data yang sama

***

## Langkah Seterusnya

Kini anda boleh melihat imej skrin penuh:

* [**Lapisan Imej**](image-layers.md) - Ketahui tentang visualisasi berbilang jalur
* [**Kotak Pasir Indeks/LUT**](index-lut-sandbox.md) - Gunakan indeks tersuai dan pemetaan warna
* [**Rumus Indeks Berbilangspek**](../project-settings/multispectral-index-formulas.md) - Fahami indeks yang tersedia

Untuk memproses aliran kerja, lihat:

* [**Memproses Imej (GUI)**](../processing-images-gui/adding-files-to-a-project.md) - Panduan pemprosesan lengkap