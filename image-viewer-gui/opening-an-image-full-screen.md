# Membuka Imej Penuh Skrin

<figure><img src="../.gitbook/assets/image (34).png" alt=""><figcaption><p>Imej dibuka penuh skrin, dengan pemilih lapisan di sudut atas kanan</p></figcaption></figure>

Chloros Imbas Imej ialah antara muka skrin penuh untuk melihat, memeriksa dan mengukur imej anda. Di sinilah anda membaca **nilai piksel sebenar** — DN setiap saluran, peratus pantulan, atau radiasi dalam W/m²/sr/nm — dan bukannya pratonton yang diregangkan yang dipaparkan oleh skrin.

## Mengakses Pemapar Imej

### Daripada Pelayar Fail

1. Buka tab **Pelayar Fail** <img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line">
2. Klik mana-mana **thumbnail** dalam [grid imej](image-grid.md)
3. Imej dibuka penuh skrin dalam tab **Image Viewer**

Imej dibuka pada produk mana pun yang dipaparkan oleh grid tersebut. Jika grid ditetapkan kepada `RAW (Reflectance)`, itulah lapisan yang akan dibuka.

### Membuka bar sisi **Image Viewer**Klik ikon**Image Viewer** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> di bar sisi kiri untuk menggeser keluar panel analisis. Ia mengandungi, dari atas ke bawah:

* nama imej dan model kameranya
* butang **Eksport/Simpan Imej(j)** (hanya apabila satu Indeks atau LUT diaktifkan)
* kotak semak **Index**dan**LUT** serta panel konfigurasi indeks — lihat [Index/LUT Sandbox](index-lut-sandbox.md)
* panel **Nilai Penunjuk**: bacaan setiap saluran, histogram lapisan, dan kawalan GSD***

## Menavigasi dan zum

### Menelusuri imej

* **Imej seterusnya**: butang →, atau kekunci**→** (Panah Kanan)
* **Imej sebelumnya**: butang ←, atau kekunci**←** (Panah Kiri)
* **Lompat ke imej tertentu**: kembali ke grid dan klik lakbaknya

Zoom dan pan kekal semasa anda bergerak antara imej, jadi anda boleh menelusuri satu set sambil kekal pada bahagian bingkai yang sama.

### Zoom

Zoom dikawal oleh **roda tetikus**, dalam langkah 15%, berpandukan kursor — titik di bawah penunjuk kekal di bawah penunjuk. Julatnya dihadkan oleh saiz imej dan tetingkap: anda tidak boleh zum keluar melebihi saiz tetingkap, dan had atas ditetapkan oleh resolusi asal imej.

Tiada kekunci zum khusus dalam pemapar skrin penuh. (Dalam grid, **Ctrl + `+` / `−`** menukar saiz lakaran kecil — kawalan yang berbeza.)

### Mengalih semasa zum

Klik dan tahan butang tetikus kiri di atas imej dan seret. Pergerakan imej terhad jadi imej tidak boleh diseret keluar dari skrin.

### Pemeriksaan piksel demi piksel pada pembesaran tinggi

Sebaik sahaja pembesaran efektif melebihi **60×**, Chloros akan melukis kotak sorotan di sekitar piksel individu yang dipaparkan di bawah penuding dan nilai terapung di sebelahnya.

Perbesaran &quot;berkesan&quot; mengambil kira saiz blok GSD: dengan saiz blok 8, petunjuk akan muncul pada pembesaran 7.5× dan bukannya 60×, kerana satu piksel yang dipaparkan sudah mengandungi 8 × 8 piksel sumber. Kurangkan pembesaran kembali di bawah ambang dan petunjuk itu akan hilang.

### Pintasan papan kekunci

| Kekunci                             | Di mana       | Tindakan                              |
| ------------------------------- | ----------- | ----------------------------------- |
| **→**                           | Skrin penuh | Imej seterusnya                          |
| **←**                           | Skrin penuh | Imej sebelumnya                      |
| **Ctrl + R**                    | Skrin penuh | Seting semula kotak pasir indeks/LUT         |
| **Ctrl + `+`**/**Ctrl + `=`** | Grid        | Thumbnail yang lebih besar (4 px setiap tekan)  |
| **Ctrl + `−`**                  | Grid        | Thumbnail lebih kecil (4 px setiap tekan) |***

## Nilai Penunjuk

Gerakkan penunjuk ke atas imej dan panel **Nilai Penunjuk** akan melaporkan nilai setiap saluran di bawahnya.

{% hint style="success" %}
**Ini adalah nombor sebenar fail tersebut.** Kanvas pada skrin adalah pratonton regangan 8-bit dan tidak dapat menyediakannya, jadi Chloros mengambil sampel fail produk sebenar untuk bacaan. Itulah sebabnya bingkai mentah 12-bit melaporkan nilai melebihi 255, dan mengapa lapisan sinaran float32 melaporkan unit fizikal.
{% endhint %}

### Apa maksud lajur-lajur tersebut

Panel akan menyesuaikan diri mengikut lapisan yang sedang anda lihat:

| Lapisan yang sedang anda lihat      | Lajur yang dipaparkan    | Nota                                                                                           |
| ---------------------------------- | ---------------- | ----------------------------------------------------------------------------------------------- |
| Reflektan                          | **DN**dan**%** | Peratus dikira dengan skala fail tersebut — lihat di bawah                                      |
| Radiasi                           | **W/m²/sr/nm**   | Nilai fizikal titik terapung; tiada lajur DN, kerana DN tidak bermakna di sini                           |
| Mentah / Debayered / pratonton / JPG | **DN**           | Nombor digital integer                                                                         |
| Eksport pantulan peratus 32-bit | **%** sahaja       | Nilai terapung yang disimpan bukan DN, jadi membundarkannya kepada integer akan mencetak `0` atau `1` yang tidak bermakna |

Setiap baris dilabel dengan nama saluran penapis kamera anda — `Red / Green / NIR` untuk RGN, `Orange / Cyan / NIR` untuk OCN, `NIR / Green / Blue` untuk NGB, `Red / Green / Blue` untuk RGB, dan nama jalur tunggal untuk kamera RE, NIR dan mono M3M. Setiap label memaparkan titik berwarna yang sepadan dengan bulatan saluran yang digunakan dalam penyunting formula indeks.

Indeks dan LUT yang disimpan Imej adalah kes khas: ia mengandungi komponen peta warna dan bukannya jalur spektral, jadi barisnya dilabelkan `Red / Green / Blue` (atau `Index` untuk fail indeks saluran tunggal) dan bukannya dengan nama penapis kamera.

Apabila satu indeks aktif di dalam kotak pasir, satu baris tambahan akan muncul di bawah saluran-saluran yang memaparkan **nilai indeks** pada kursor, bersama nama indeks dan titik putih yang sepadan dengan penandanya pada histogram.

### Peratus pantulan menggunakan skala setiap fail sendiri

{% hint style="warning" %}
**Jangan anggap 65535 = 100%.** Chloros menyimpan pantulan pada skala berbeza bergantung pada kamera yang menjana ia, dan pemapar menentukan skala yang betul bagi setiap fail.
{% endhint %}

| Sumber                  | DN yang sama dengan pantulan 1.0 | Cara ia dikenal pasti                                                                                                                               |
| ----------------------- | ------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| **LATTICE**(M3C / M3M) |**32768**                      | Tag XMP `Chloros:PixelScale=32768` ditulis ke dalam setiap eksport pantulan LATTICE. Ruang lebihan 2× membolehkan fail membawa ρ melebihi 1.0 tanpa pemotongan |
| **Survey3**|**65535**                      | Tiada tag skala XMP Chloros — penulisan kalibrasi Survey3 menulis ρ × dtype-max dan memotong pada 1.0                                                               |

Pemapar, kotak pasir indeks/LUT dan eksport indeks semuanya menyelesaikan skala melalui satu pelaksanaan tunggal yang sama, jadi nilai yang anda baca pada penuding adalah nilai yang sama yang digunakan dalam matematik indeks.

Dua kesan yang perlu diketahui:

* **Peratus 32-bit**TIFF menyimpan DN/65535 sebagai titik apung, dan eksport**8-bit** PNG/JPG menyimpan DN × 255/65535 — pemapar menukarkan kedua-duanya kembali sebelum mencetak peratusan.
* Satu kes tidak dapat dipulihkan: eksport **TIFF 8-bit daripada tangkapan sumber 8-bit** dipotong kepada 0–255 dan bukannya diskala semula, dan sengaja tidak mempunyai tag skala. Untuk fail-fail tersebut, panel hanya mencetak DN, tanpa lajur peratus. Ini adalah jawapan yang jujur, bukan pepijat.***

## Histogram lapisan

Di bawah baris kursor terdapat histogram langsung bagi lapisan yang sedang anda lihat, dalam **256 bin**. Secara lalai ia melukis satu lengkung gabungan, yang diberi berat `(R + 2G + B) / 4` — ruang pengukuran yang sama seperti yang digunakan oleh histogram kamera LATTICE. Mengaktifkan**RGB** menggantikannya dengan lengkung bagi setiap saluran dalam warna saluran masing-masing, yang dicampurkan secara penambahan supaya tumpang tindih kekal boleh dibaca. Lapisan Mono sentiasa melukis satu lengkung tunggal.

Paksis mendatar adalah dalam unit lapisan itu sendiri:

| Lapisan       | Unit paksis  | Maksimum paksis                                               |
| ----------- | ---------- | ---------------------------------------------------------- |
| Reflektan | peratus    | 125% — ruang lebih produk membenarkan ρ di atas 1.0           |
| Radian    | W/m²/sr/nm | Puncak bingkai itu sendiri, dibundarkan ke atas kepada dua digit penting |
| data 8-bit | DN         | 255                                                        |
| data 12-bit | DN         | 4095                                                       |
| data 16-bit | DN         | 65535                                                      |

Apabila paksi berada dalam DN dan terletak pada salah satu daripada tiga had tersebut, Chloros juga mengetahui kedalaman bit bagi apa yang anda lihat.

Tiga butang terletak di atas histogram:

| Butang     | Lalai | Kesan                                                                                                                                                                                                                                                                                   |
| ---------- | ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **PETUNJUK** | Diaktifkan | Melukis garisan penanda pada histogram pada nilai tepat yang ditunjukkan dalam baris di atas, supaya anda dapat melihat di mana piksel di bawah penunjuk anda terletak dalam pengagihan bingkai. Dalam mod RGB, terdapat satu penanda bagi setiap saluran dalam warna tersendiri; jika tidak, satu penanda putih tunggal pada nilai gabungan |
| **INDEKS**| Aktif      | Muncul hanya semasa sesuatu indeks aktif. Menukar histogram daripada jalur sumber kepada**agihan nilai indeks**, dengan dua ambang klip dilukis sebagai garisan putus-putus jingga dan nilai indeks kursor sebagai garisan putih                                                          |
| **RGB**| Padam    | Menukar daripada lengkung gabungan kepada lengkung bagi setiap saluran. Pada sensor mono, butang ini tertulis**MONO** dan dilumpuhkan — hanya terdapat satu saluran untuk dipaparkan                                                                                                                                  |

Histogram dikira pada **blok yang boleh anda lihat**, bukan piksel sumber di belakangnya: ubah saiz blok GSD dan pengagihan dikira semula, supaya histogram, penanda tetikus dan imej yang dipaparkan sentiasa sepadu.***

## Saiz blok GSD

Di bahagian bawah panel terdapat kawalan **GSD (px)**: satu kotak nombor, satu gelangsar dari**1 hingga 256**, dan butang**RESET**.

Ia memperkasar imej _yang dipaparkan_ dengan mengambil purata blok piksel sumber N × N kepada satu piksel yang dipaparkan. `1` ialah resolusi asli.

* Ia menjejaskan **pemandangan skrin penuh, pratonton grid, pembacaan kursor, dan kedua-dua histogram** — segala yang memaparkan imej bersetuju pada resolusi asas yang sama.
* Ia **hanya untuk paparan**. Pemprosesan dan eksport tidak terjejas. Satu-satunya pengecualian adalah sengaja: eksport [Index/LUT Sandbox](index-lut-sandbox.md) menyimpan apa yang anda lihat, jadi ia membawa saiz blok semasa, dan panel eksport akan memberi amaran apabila saiz blok melebihi 1.
* Nilai disimpan **per projek** sebagai `viewer_display.gsd_bin` dalam `project.json`, jadi ia kekal walaupun ditutup dan dibuka semula.
* Output kursor melaporkan blok, bukan piksel sumber, setiap kali saiz blok melebihi 1 — nilai yang dipaparkan adalah purata blok di bawah penuding anda.

{% hint style="info" %}
**Mengapa &quot;saiz blok&quot; dan bukan sentimeter per piksel?** Nilai cm/px memerlukan ketinggian di atas tanah. EXIF satu bingkai mengandungi ketinggian GPS di atas paras laut purata, bukan di atas kawasan yang diarahkan kepadanya, jadi Chloros tidak akan mencetak jarak darat yang tidak dapat diperoleh dengan tepat. Saiz blok dalam piksel sumber adalah pilihan lalai yang sama yang digunakan oleh alat awan MAPIR apabila jarak pensampelan darat tidak diketahui.
{% endhint %}

***

## Jenis imej yang boleh anda lihat

Senarai lungsur lapisan di bahagian atas kanan pemapar menyenaraikan setiap versi imej semasa. Masukan yang muncul bergantung pada kamera dan apa yang telah diproses — lihat [Lapisan Imej](image-layers.md) untuk senarai penuh dan bagaimana menu lungsur berkelakuan.

### Survey3

* **JPG** — fail pratonton kamera itu sendiri
* **RAW (Asal)** — `.RAW` sumber, telah menjalani debayering untuk dipaparkan, tiada pembetulan
* **RAW (Sasaran)** — satu bingkai yang dikenal pasti mengandungi sasaran penentukuran
* **RAW (Reflektan)** — produk reflektan yang dikalibrasi (65535 = ρ 1.0)
* **Betulkan Vignet**/**Respon Sensor** — produk sandaran tanpa kalibrasi
* **Imbangan Putih** — produk imbangan putih
* **RAW (Indeks `<INDEX>`)**dan**LUT `<INDEX>`** — imej indeks yang dikira

### LATTICE

LATTICE menggunakan menu lungsur yang sama, dengan nama aras paip:

| Lapisan                 | Kandunganannya                                                        |
| --------------------- | -------------------------------------------------------------------- |
| **RAW (Asli)**    | Rangka mentah sumber seperti yang dirakam                                     |
| **RAW (Debayered)**   | Imej linear debayered                                           |
| **RAW (Prviu)**     | Prviu paparan — regangan warna palsu untuk kamera multispektral |
| **Imbangan Putih**    | Pratonton paparan untuk kamera RGB (imbangan putih + gamma)   |
| **RAW (Radiasi)**    | Radiasi spektral Float32 dalam W/m²/sr/nm                              |
| **RAW (Refleksan)** | Refleksan uint16, 32768 = ρ 1.0                                    |

Radiasi dan pantulan hanya untuk multispektral: kamera induk RGB tidak mempunyai radiometri setiap jalur, jadi lapisan tersebut tidak dihasilkan untuknya.

***

## Aplikasi Indeks dan LUT

Terapkan indeks multispektral dan Jadual Rujukan Warna (LUT) daripada bar sisi:

1. Buka bar sisi **Pemandang Imej** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line">
2. Centang **Indeks**

3. Pilih penapis kamera anda dan formula indeks, kemudian seret bulatan saluran ke dalam ruang kosong formula tersebut
4. Tambah LUT dan pilih gradien, ambang dan mod pemangkasan
5. Baca nilai pada kursor, dan simpan hasilnya dengan **Eksport/Simpan Imej(e)**Lihat [Sandbox Indeks/LUT](index-lut-sandbox.md) untuk panduan lengkap.***

## Penyelesaian Masalah

### Imej tidak dapat dibuka

**Punca yang mungkin**: fail telah dipindahkan atau dipadam selepas import; produk tidak pernah ditulis; memori tidak mencukupi untuk imej yang sangat besar.**Apa yang perlu dilakukan**:

1. Semak sama ada fail lapisan itu masih wujud dalam pokok keluaran projek
2. Buka fail tersebut dalam pemapar luaran untuk mengesahkan ia tidak rosak
3. Tutup aplikasi lain untuk membebaskan memori

### Imej berwarna hitam, putih, atau berwarna-warni secara keterlaluan

**Punca yang mungkin**: stretch paparan tiada bahan untuk diproses (bingkai yang hampir malar); lapisan float32 dengan nilai luar biasa; indeks yang tidak menghasilkan data sah.**Apa yang perlu dilakukan**:

1. Baca nilai kursor — jika setiap saluran berada pada atau hampir sifar, masalahnya pada data, bukan pada paparan
2. Semak histogram: satu lonjakan tunggal di salah satu hujung menunjukkan bingkai telah dipotong atau kosong
3. Semak log pemprosesan untuk larian yang menghasilkan lapisan tersebut

### Nilai kelihatan tidak betul

**Punca yang mungkin**: anda berada pada lapisan yang berbeza daripada yang anda sangka; anda membandingkan peratusan dengan DN mentah; anda membandingkan fail LATTICE dengan fail Survey3 menggunakan pembahagi yang sama.**Apa yang perlu dilakukan**:

1. Sahkan lapisan yang dipilih dalam menu lungsur — unit panel mengikut lapisan
2. Untuk pantulan, gunakan lajur **%** dan bukannya membahagikan DN sendiri; jika anda mesti membahagikan, gunakan `Chloros:PixelScale` fail tersebut (32768 untuk LATTICE, tiada bermaksud 65535 untuk Survey3)
3. Tetapkan saiz blok GSD kembali kepada 1 — melebihi 1 anda membaca purata blok, bukan piksel
4. Semak sama ada penentukuran pantulan benar-benar dijalankan untuk bingkai tersebut; produk sandaran yang tidak ditentukur (Sensor Response / Vignette Corrected) bukan pantulan

***

## Langkah Seterusnya

* [**Lapisan Imej**](image-layers.md) — setiap nama lapisan, apabila ia wujud, dan maksud nilai-nilainya
* [**Sandbox Indeks/LUT**](index-lut-sandbox.md) — bina, laras dan eksport visualisasi indeks
* [**Penanda Peta**](map-markers.md) — set imej yang sama pada peta
* [**Formula Indeks Multispektral**](../project-settings/multispectral-index-formulas.md) — rujukan indeks

Untuk aliran kerja pemprosesan, lihat [Pemprosesan Imej (GUI)](../processing-images-gui/adding-files-to-a-project.md).
