# Lapisan Imej

**Senarai lungsur lapisan** di bahagian atas kanan Pemapar Imej menukar antara setiap versi imej yang anda lihat — daripada tangkapan sumber melalui setiap produk yang diproses hingga imej indeks yang dikira — tanpa meninggalkan pemapar.

## Apakah Lapisan Imej?

&quot;Lapisan&quot; dalamChloros

ialah satu **fail produk**yang didaftarkan terhadap satu imej sumber. Import memberikan anda fail sumber; pemprosesan menambah satu lapisan untuk setiap produk yang dihasilkan oleh larian. Fail yang dieksport mengekalkan nama fail sumber — adalah**folder** yang mengenal pasti produk, dan nama lapisan adalah labelChloros

untuk folder tersebut.

<!-- SCREENSHOT-NEEDED: Image Viewer full screen with the layer dropdown open on a processed LATTICE multispectral image, showing the full list: TIFF base, RAW (Original), RAW (Debayered), RAW (Preview), RAW (Radiance), RAW (Reflectance), and one RAW (NDVI Index) entry. -->

***

## Senarai lapisan

### Sentiasa ada

| Lapisan | Apa itu |
| --- | --- |
| **JPG**(atau**PNG

**/**TIFF

**) | Fail asas yang disertakan dengan tangkapan. ImportSurvey3

mengimport `.JPG` di sebelah setiap `.RAW`; tangkapan LATTICE membawa pratonton paparanPNG

atauTIFF

. Dilabel mengikut apa yang sebenarnya diimport |
| **RAW (Asli)** | Rangka mentah sumber, telah menjalani debayering untuk paparan tanpa sebarang pembetulan. Tersedia sejak saat diimport — tidak memerlukan pemprosesan |

Satu tangkapan LATTICE yang fail asasnya **ialah** bingkai mentahnya tidak mempunyai entri asas berasingan: `RAW (Original)` sudah merangkuminya.

### Produk pemprosesanSurvey3



| Lapisan | Ditulis ke | Wujud apabila |
| --- | --- | --- |
| **RAW (Sasaran)** | — | Bingkai dikenal pasti mengandungi sasaran penentukuran |
| **RAW (Refleksan)** | `Reflectance_Calibrated_Images/` | Penentukuran refleksan berjaya dijalankan pada bingkai ini |
| **Betul Vignette**| `Vignette_Corrected_Images/` | Bingkai tidak dapat dikalibrasi reflektansi**dan** *pembetulan Vignette* diaktifkan |
| **Respon Sensor**| `Sensor_Response_Images/` | Bingkai tidak dapat dikalibrasi reflektansi**dan** *pembetulan Vignette* dimatikan |
| **Imbangan Putih** | `White_Balanced_Images/` | Satu produk imbangan putih telah ditulis |

{% hint style="info" %}
**Pembetulan Vignette dan Tindak Balas Sensor adalah alternatif, bukan kedua-duanya.** Hanya satu produk sandaran tidak dikalibrasi wujud bagi setiap larian, untuk setiap model kamera, dan suis *Pembetulan Vignette* memilih yang mana satu. Lihat [Tetapan Projek](../project-settings/project-settings.md).
{% endhint %}

### Tahap LATTICE

LATTICE menangkap fan out ke dalam tahap-tahap ini dalam satu pas pemprosesan. Tahap yang wujud bergantung pada suis eksport bagi setiap produk dalam Tetapan Projek dan pada apa yang terpakai kepada kamera.

| Lapisan | Ditulis ke | Terpakai kepada |
| --- | --- | --- |
| **RAW (Debayered)** | `Debayered_Images/` |RGB

dan multispektral |
| **RAW (Prviakli)** | `Preview_Images/` | Multispektral (regangan warna palsu) |
| **Imbangan Putih** | `Preview_Images/` |RGB

kamera induk — pratontonRGB

didaftarkan di bawah nama ini supaya selari dengan lapisanSurvey3

yang bernama sama |
| **RAW (Radiance)** | `Radiance_Images/` | Multispektral sahaja |
| **RAW (Refleksan)** | `Reflectance_Calibrated_Images/` | Multispektral sahaja, dan hanya apabila rekod sinaran ke bawah `.daq` yang sepadan atau sasaran dalam bingkai yang lulus QA menutupi bingkai |

RGB

kamera induk tidak mempunyai radiometri bagi setiap jalur, jadi radiasi dan pantulan diabaikan untuknya sebagai **tidak terpakai** — log menyatakan demikian dan bukannya gagal secara senyap.

### Lapisan Indeks, LUT dan sandbox

| Corak lapisan | Contoh | Asalnya dari |
| --- | --- | --- |
| **RAW (Indeks `<INDEX>`)** | `RAW (NDVI Index)` | Satu bagi setiap indeks yang dikonfigurasikan dalam Tetapan Projek, dikira semasa pemprosesan |
| **`<INDEX>` LUT** | `NDVI LUT` | Versi peta warna bagi satu indeks |
| **Sandbox (`<Name>` `<Index\|LUT>` `<NNN>`)** | `Sandbox (NDVI LUT 003)` | Satu bagi setiap larian eksport [Index/LUT Sandbox](index-lut-sandbox.md) |

Jika nama indeks yang sama dikonfigurasikan lebih daripada sekali dengan tetapan berbeza, yang kedua dan seterusnya akan mendapat nombor dalam nama (`RAW (NDVI2 Index)`) supaya lapisan-lapisan tersebut masih dapat dibezakan.

***

## Menggunakan pemilih lapisan

1. Buka imej pada skrin penuh dengan mengklik lakaran kecil dalam grid
2. Klik **menu lungsur lapisan** di bahagian atas kanan pemapar
3. Pilih lapisan — imej akan dikemas kini serta-merta

Senarai lungsur meletakkan **JPG, RAW (Asal), RAW (Sasaran), RAW (Refleksan)** di tempat pertama, mengikut susunan itu, dan menyenaraikan semua yang lain selepasnya mengikut susunan produk didaftarkan.

### Keutamaan lapisan semasa anda menavigasi

Menekan **←**/**→** akan bergerak ke imej seterusnya dan cuba mengekalkan anda pada lapisan yang sama:

1. **Padanan tepat dahulu** — jika imej seterusnya mempunyai lapisan dengan nama yang sama, anda akan mendapatkannya. Inilah yang mengekalkan anda pada `RAW (NDVI Index)` semasa menelusuri satu set keseluruhan
2. **Kemudian padanan mengikut jenis** — lapisan indeks mencari mana-mana lapisan indeks, LUT untuk mana-mana LUT, pantulan untuk pantulan, sasaran untuk sasaran, asal untuk asal, asas untuk asas
3. **Kemudian, untuk lapisan eksport sahaja** — nama dikekalkan walaupun senarai lapisan belum mengemas kini, kerana fail tersebut sudah wujud di cakera. Ini membolehkan anda menyemak produk semasa sesi pelaksanaan masih menulisnya.
4. **Jika tidak** — lapisan pertama yang tersedia, yang biasanya adalah imej asas.

Fail sidecar `.daq` dan `.csv` dalam projek diabaikan oleh navigasi kekunci anak panah, jadi langkah demi langkah melalui imej tidak akan berhenti pada rakaman penderia cahaya.

Zoom dan pan turut dibawa ke imej seterusnya, yang memudahkan perbandingan sebelum/selepas pada kedudukan medan yang sama.

***

## Memahami nilai piksel mengikut lapisan

Panel [Nilai Penunjuk](opening-an-image-full-screen.md#cursor-values) melaporkan nilai sebenar bagi setiap saluran di bawah penunjuk anda, dalam unit yang digunakan oleh lapisan tersebut. Ruang lajunya berubah mengikut lapisan:

| Lapisan | Unit dilaporkan | Nota |
| --- | --- | --- |
| Asas (JPG /PNG

/ pratontonTIFF

) | DN, 0–255 | Nilai paparan, diperbetulkan gamma padaRGB

. Pemeriksaan visual sahaja |
| RAW (Asli) | DN | Nombor digital sensor mentah. Paksis histogram memberitahu anda kedalaman: 255 (8-bit), 4095 (12-bit) atau 65535 (16-bit) |
| RAW (Debayered) | DN | Linear, tiada regangan paparan |
| RAW (Prviu) / Imbangan Putih | DN | Paparan produk — diregangkan atau diperbetulkan gamma. Bukan untuk pengukuran |
| RAW (Radiasi) | **W/m²/sr/nm** | Radiasi fizikal Float32. Tiada lajur DN |
| RAW (Refleksansi) | DN **dan %** | Peratusan dikira dengan skala tersendiri fail tersebut — lihat di bawah |
| Eksport Indeks / LUT / sandbox | Nilai indeks, atau komponenRGB

| Fail indeks saluran tunggal melaporkan nilai indeks; fail LUT peta warna melaporkan komponenRed

/Green

/Blue

|

### Reflektan: skala adalah setiap fail

{% hint style="warning" %}
**&quot;Bahagi dengan 65,535&quot; hanya betul untukSurvey3

.** Reflektansi LATTICE disimpan pada skala yang berbeza, dan mencampurkan kedua-dua pembahagi adalah cara paling biasa untuk mendapatkan nilai reflektansi yang tepat separuh daripada nilai sebenar.
{% endhint %}

| Sumber | DN yang sama dengan pantulan 1.0 | Dikenal pasti oleh |
| --- | --- | --- |
| **LATTICE**(M3C / M3M) |**32768** | Tag XMP `Chloros:PixelScale=32768` yang dicop ke dalam setiap eksport pantulan LATTICE. Ruang lebih 2× bermaksud ρ di atas 1.0 boleh diwakili dan bukannya dipotong |
| **Survey3**| **65535** | Tiada tag skala XMPChloros

— penulisan kalibrasiSurvey3

menulis ρ × dtype-max dan memotong pada 1.0 |

Untuk GIS dan skrip: baca `Chloros:PixelScale` daripada fail dan bahagikannya. Jika tag tiada, fail itu adalah berskalaSurvey3

(65535). Pemapar, kotak pasir indeks/LUT dan eksport indeks semua mentafsir skala dengan cara yang sama, jadi nombor yang anda baca pada penuding adalah nombor yang digunakan dalam matematik indeks.

Penyimpanan khusus format di atas skala itu:

* **TIFF

(32-bit, Peratus)** menyimpan DN / 65535 sebagai titik terapung
* **PNG

(8-bit)**dan**JPG (8-bit)** menyimpan DN × 255 / 65535
* Eksport **TIFF

8-bit daripada tangkapan sumber 8-bit** dipangkas kepada 0–255 dan bukannya diskala semula, dan sengaja tidak mempunyai tag skala. Panel mencetak DN hanya untuk fail-fail tersebut, tanpa lajur peratus

### Julat nilai indeks

| Keluarga indeks | Julat tipikal | Bacaan |
| --- | --- | --- |
| Perbezaan normalisasi (NDVI

,GNDVI

,NDRE

, ENDVI…) | −1 hingga +1 | Tumbuhan sihat biasanya 0.4–0.9; tanah terbukak hampir 0; air bernilai negatif |
| Disesuaikan tanah (SAVI

,OSAVI

, MSAVI2…) | lebih kurang −1 hingga +1.5 | Bacaan serupa denganNDVI

dengan latar belakang tanah dipadamkan |
| Peratusan (GRVI

,GCI

, MSR, CIRE…) | tidak terhad ke atas | Peratusan meningkat tanpa had apabila pembilang bergerak ke arah sifar |
|EVI

/LAI

| 0 hingga ~1, 0 hingga ~3.5 | Awan dan piksel tepu lain mendorong kedua-duanya keluar daripada julat — topengkan dahulu |

Lihat [Formula Indeks Multispektral](../project-settings/multispectral-index-formulas.md) untuk formula tepat di sebalik setiap pratetap.

***

## Aliran kerja biasa

### Perbandingan sebelum / selepas

1. Pilih **RAW (Asli)** dan perhatikan kesan vignetting dan nilai yang belum dikalibrasi
2. Tukar kepada **RAW (Refleksan)**

3. Bandingkan — penyingkiran vignetting, nilai telah dikalibrasi. Zum dan pan dikekalkan, jadi anda melihat kawasan yang sama

### Semak satu indeks merentasi satu set keseluruhan

1. Buka imej pertama yang diproses dan pilih lapisan indeks
2. Tekan **→** berulang kali — lapisan indeks mengikut anda dari satu imej ke imej lain
3. Perhatikan histogram di bar sisi semasa anda menavigasi: bingkai yang pengagihan datanya melonjak patut diperiksa dengan lebih teliti

### Semak sasaran penentukuran

1. Pilih **RAW (Sasaran)** pada bingkai sasaran
2. Pastikan sasaran dapat dilihat dengan jelas dan dikesan
3. Beralih ke bingkai sasaran seterusnya — lapisan sasaran akan mengikutinya

### Semak nilai pantulan untuk ketepatan

1. Pilih **RAW (Pantulan)**

2. Baca lajur**%** dalam panel Nilai Penunjuk — ia sudah diskalakan dengan betul untuk fail tersebut
3. Semak logik terhadap bahan yang diketahui dalam bingkai: tumbuhan yang sihat mempunyai nilai pantulan yang tinggi dalam julat hijau (NIR

) dan rendah dalam julat merah; sasaran penentukuran sepatutnya menunjukkan bacaan yang hampir dengan nilai pantulan yang diterbitkan

***

## Penyelesaian Masalah

### Lapisan yang saya jangkakan tiada dalam senarai lungsur

**Punca yang mungkin**

* Imej tidak pernah diproses — hanya base dan `RAW (Original)` wujud
* Butang eksport produk tidak dicentang dalam Tetapan Projek
* Produk tidak terpakai untuk kamera tersebut (radiasi dan pantulan pada tuan rumahRGB

; sebarang indeks pada kamera mono M3M jalur tunggal)
* Kalibrasi reflektansi tiada data untuk diproses — tiada liputan sinaran bawah `.daq` dan tiada sasaran dalam bingkai yang lulus QA — jadi bingkai itu kembali kepada Betulkan Vignette atau Tindak Balas Sensor

**Apa yang perlu dilakukan**

1. Semak log larian:Chloros

menyatakan bila produk eksport yang diminta tidak dapat dihasilkan dan sebabnya
2. Semak suis eksport bagi setiap produk dalam [Seting Projek](../project-settings/project-settings.md)
3. Sahkan folder produk wujud dalam pokok output projek
4. Proses semula dengan produk diaktifkan

### Senarai lapisan kelihatan lapuk

Chloros

mengimbas semula folder produk projek semasa proses berjalan dan membaiki pendaftaran lapisan yang hilang berdasarkan apa yang sebenarnya ada pada cakera, jadi lapisan yang telah selesai dieksport akan muncul bersendirian dalam tinjauan. Beralih dari imej dan kembali memaksa penyelesaian baru.

### Nilai pantulan kelihatan separuh daripada nilai sepatutnya

Anda hampir pasti membahagikan fail LATTICE dengan 65535. Gunakan `Chloros:PixelScale` (32768), atau baca lajur **%**, yang telah pun menerapkannya.

### Lapisan indeks wujud tetapi imejnya kosong

Indeks memerlukan jalur yang tidak dimiliki oleh lapisan anda — contohnya indeks yang membaca saluran ketiga pada fail satu atau dua saluran. Tukar kepada lapisan berbilang-jalur (reflektan atau debayered), atau pilih indeks yang sesuai dengan penapis kamera.

***

## Langkah Seterusnya

* [**Membuka Imej Penuh Skrin**](opening-an-image-full-screen.md) — bacaan kursor, histogram dan kawalan GSD
* [**Sandbox Indeks/LUT**](index-lut-sandbox.md) — visualisasi dan eksport indeks interaktif
* [**Formula Indeks Multispektral**](../project-settings/multispectral-index-formulas.md) — rujukan indeks
* [**Menyiapkan Pemprosesan**](../processing-images-gui/finishing-the-processing.md) — pokok folder output yang ditunjukkan oleh lapisan-lapisan ini
