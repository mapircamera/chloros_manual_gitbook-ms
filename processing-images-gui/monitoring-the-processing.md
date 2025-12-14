# Memantau pemprosesan

Sebaik sahaja pemprosesan telah bermula, Chloros menyediakan beberapa cara untuk memantau kemajuan, periksa isu, dan memahami apa yang berlaku dengan dataset anda. Halaman ini menerangkan cara menjejaki pemprosesan anda dan mentafsirkan maklumat yang disediakan oleh Chloros.

## Gambaran Keseluruhan Bar Kemajuan

Bar kemajuan di tajuk teratas menunjukkan status pemprosesan masa nyata dan peratusan penyelesaian.

### bar kemajuan mod percuma

Bagi pengguna tanpa lesen Chloros+:

**Paparan kemajuan 2 peringkat:**
1.** Sasaran Mengesan**- Mencari sasaran penentukuran dalam imej
2.**Pemprosesan**- Memohon pembetulan dan mengeksport
**Kemajuan bar menunjukkan:**

* Peratusan siap secara keseluruhan (0-100%)
* Nama Peringkat Semasa
* Visualisasi bar mendatar sederhana

### chloros+ bar kemajuan

Bagi pengguna dengan Lesen Chloros+:

**Paparan kemajuan 4 peringkat:**
1.** Mengesan**- Mencari sasaran penentukuran
2.**Menganalisis**- Memeriksa imej dan menyediakan saluran paip
3.**Menentukur**- Memohon pembetulan vignette dan refleksi
4.**Mengeksport**- Menyimpan fail yang diproses
**Ciri Interaktif:***
**Hover over ** Bar Progress untuk melihat panel 4-peringkat yang diperluas
* **Klik** Bar Kemajuan untuk membekukan/pin panel yang diperluaskan
* **Klik Lagi** untuk Membongkar dan Mengumpulkan Auto pada Cuti Tetikus
* Setiap peringkat menunjukkan kemajuan individu (0-100%)

***

## Memahami setiap peringkat pemprosesan

### Peringkat 1: Mengesan (Pengesanan Sasaran)**Apa yang berlaku:**

* Kloros mengimbas gambar yang ditandai dengan kotak semak sasaran
* Algoritma penglihatan komputer mengenal pasti 4 panel penentukuran
* Nilai refleksi yang diekstrak dari setiap panel
* Timestamp sasaran yang direkodkan untuk penjadualan penentukuran yang betul

**Tempoh:**

* Dengan sasaran yang ditandakan: 10-60 saat
* Tanpa sasaran yang ditandakan: 5-30+ minit (mengimbas semua imej)

**Petunjuk Kemajuan:**

* Mengesan: 0% → 100%
* Bilangan gambar yang diimbas
* Sasaran yang dijumpai dikira

**Apa yang perlu ditonton:**

* Harus diselesaikan dengan cepat jika sasaran ditandakan dengan betul
* Sekiranya terlalu lama, sasaran mungkin tidak ditandakan
* Periksa log debug untuk mesej "sasaran dijumpai"

### Peringkat 2: Menganalisis

**Apa yang berlaku:**

* Membaca imej EXIF ​​Metadata (cap waktu, tetapan pendedahan)
* Menentukan strategi penentukuran berdasarkan cap waktu sasaran
* Mengatur barisan pemprosesan imej
* Menyediakan pekerja pemprosesan selari (hanya Chloros+ sahaja)

**Tempoh:**5-30 saat**Petunjuk Kemajuan:**

* Menganalisis: 0% → 100%
* Panggung pantas, biasanya selesai dengan cepat

**Apa yang perlu ditonton:**

* Harus maju dengan mantap tanpa jeda
* Amaran mengenai Metadata Hilang akan muncul dalam log debug

### Peringkat 3: Menentukur

**Apa yang berlaku:*** ** debayering**: Menukar corak bayer mentah ke 3 saluran
* **Pembetulan Vignette**: Mengeluarkan kelebihan lensa gelap
* **Penentukuran Refleksi**: Menormalkan dengan nilai sasaran
* **Pengiraan indeks**: pengkomputeran indeks multispektral
* Memproses setiap gambar melalui saluran paip penuh

**Tempoh:** Majoriti jumlah masa pemprosesan (60-80%)**Petunjuk Kemajuan:**

* Menentukur: 0% → 100%
* Imej semasa diproses
* Imej selesai / jumlah gambar

**Tingkah laku pemprosesan:*** ** mod percuma**: memproses satu imej pada satu masa secara berurutan
* **CHLOROS+ MODE**: Proses sehingga 16 imej secara serentak
* **Percepatan GPU**: Mempercepatkan tahap ini **Apa yang perlu ditonton:**

* Kemajuan yang mantap melalui kiraan imej
* Periksa log debug untuk mesej penyelesaian per-imej
* Amaran mengenai kualiti imej atau masalah penentukuran

### Peringkat 4: Mengeksport

**Apa yang berlaku:**

* Menulis imej yang dikalibrasi ke cakera dalam format yang dipilih
* Mengeksport imej indeks multispektral dengan warna lut
* Membuat subfolder model kamera
* Memelihara nama fail asal dengan akhiran yang sesuai

**Tempoh:**10-20% daripada jumlah masa pemprosesan**Petunjuk Kemajuan:**

* Mengeksport: 0% → 100%
* Fail yang ditulis
* Format dan destinasi eksport

**Apa yang perlu ditonton:**

* Amaran ruang cakera
* Kesalahan menulis fail
* Penyiapan semua output yang dikonfigurasikan

***

## Tab Log Debug

Log debug memberikan maklumat terperinci mengenai kemajuan pemprosesan dan sebarang isu yang dihadapi.

### mengakses log debug

1. Klik Log Debug**<img src="../. Gitbook/Assets/icon_log.jpg" alt="" data-size="line"> ikon di bar sisi kiri
2. Panel log terbuka menunjukkan mesej pemprosesan masa nyata
3. Auto-scrolls untuk menunjukkan mesej terkini

### Memahami mesej log

#### Mesej maklumat (putih/kelabu)

Kemas kini pemprosesan biasa:

```
[INFO] Pemprosesan bermula
[INFO] Sasaran yang dikesan dalam IMG_0015.RAW - 4 panel dijumpai
[INFO] Calibrating IMG_0234.RAW
[INFO] Imej NDVI yang dieksport: IMG_0234_ndvi.tif
[INFO] Pemprosesan Lengkap
```

#### Mesej amaran (kuning)

Isu bukan kritikal yang tidak berhenti diproses:

```
[WARN] Tiada data GPS yang terdapat di IMG_0145.RAW
[WARN] Sasaran Timestamp Gap Target> 30 minit
[WARN] Kontras rendah dalam panel penentukuran - Keputusan mungkin berbeza -beza
```

**Tindakan:** Kajian amaran selepas diproses, tetapi jangan mengganggu

#### Mesej ralat (merah)

Isu kritikal yang boleh menyebabkan pemprosesan gagal:

```
[Ralat] tidak dapat menulis fail - cakera penuh
[Ralat] Fail imej yang rosak: IMG_0299.RAW
[Ralat] Tiada sasaran yang dikesan - Membolehkan penentukuran refleksi atau menandakan imej sasaran
```**Tindakan:** berhenti memproses, menyelesaikan ralat, mulakan semula

### mesej log biasa

| Mesej | Makna | Tindakan diperlukan |
| -------------------------------- | -------------------------------------- | ----------------------------------------------------- |
| "Sasaran dikesan dalam [Filename]" | Sasaran penentukuran dijumpai berjaya | Tiada - Normal |
| "Pemprosesan Imej X of Y" | Kemas kini Kemajuan Semasa | Tiada - Normal |
| "Tiada sasaran dijumpai" | Tiada sasaran penentukuran dikesan | Tandakan imej sasaran atau melumpuhkan penentukuran refleksi |
| "Ruang cakera yang tidak mencukupi" | Tidak mencukupi penyimpanan untuk output | Ruang cakera percuma |
| "Melangkau fail yang rosak" | Fail imej rosak | Fail semula dari kad SD |
| "Data PPK digunakan" | Pembetulan GPS dari fail .daq digunakan | Tiada - Normal |

### menyalin data log

Untuk menyalin log untuk menyelesaikan masalah atau sokongan:

1. Buka panel log debug
2. Klik**"Log Salin"** Butang (atau klik kanan → Pilih Semua)
3. Tampal ke dalam fail teks atau e -mel
4. Hantar ke Sokongan Mapir jika diperlukan***

## Pemantauan Sumber Sistem

### Penggunaan CPU
**Mod Percuma:**

* 1 teras CPU di \ ~ 100%
* Teras lain terbiar atau tersedia
* Sistem tetap responsif

**Kloros+ mod selari:**

* Pelbagai teras di 80-100% (sehingga 16 teras)
* Penggunaan CPU keseluruhan yang tinggi
* Sistem mungkin merasa kurang responsif

**untuk memantau:**

* Pengurus Tugas Windows (Ctrl+Shift+ESC)
* Tab Prestasi → Bahagian CPU
* Cari proses "kloros" atau "kloros-backend"

### Memory (RAM) Penggunaan

**Penggunaan biasa:**

* Projek Kecil (<100 imej): 2-4 GB
* Projek Sederhana (100-500 imej): 4-8 GB
* Projek Besar (500+ gambar): 8-16 GB
* Chloros+ mod selari menggunakan lebih banyak ram

**Sekiranya memori rendah:**

* Proses kelompok yang lebih kecil
* Menutup aplikasi lain
* Menaik taraf RAM jika kerap memproses dataset besar

### Penggunaan GPU (Chloros+ dengan CUDA)

Apabila pecutan GPU didayakan:

* NVIDIA GPU menunjukkan penggunaan yang tinggi (60-90%)
* Penggunaan VRAM meningkat (memerlukan 4GB+ VRAM)
* Tahap penentukuran jauh lebih cepat

**untuk memantau:**

* Ikon dulang sistem nvidia
* Pengurus Tugas → Prestasi → GPU
* GPU-Z atau alat pemantauan yang serupa

### Disk I/O.

**Apa yang diharapkan:**

* Cakera tinggi dibaca semasa menganalisis peringkat
* Cakera tinggi menulis semasa tahap pengeksport
* SSD lebih cepat daripada HDD

**Petua Prestasi:**

* Gunakan ssd untuk folder projek apabila mungkin
* Elakkan pemacu rangkaian untuk dataset besar
* Pastikan cakera tidak dekat kapasiti (mempengaruhi kelajuan menulis)

***

## Mengesan masalah semasa pemprosesan

Tanda -tanda amaran ###**Gerai Kemajuan (tiada perubahan selama 5+ minit):**

* Periksa log debug untuk kesilapan
* Sahkan ruang cakera ada
* Periksa pengurus tugas untuk memastikan kloros berjalan

**Mesej ralat sering muncul:**

* Berhenti memproses dan mengkaji kesilapan
* Punca Biasa: Ruang cakera, fail yang rosak, masalah ingatan
* Lihat bahagian penyelesaian masalah di bawah

**Sistem menjadi tidak responsif:**

* Chloros+ mod selari menggunakan terlalu banyak sumber
* Pertimbangkan untuk mengurangkan tugas serentak atau menaik taraf perkakasan
* Mod percuma kurang berintensifkan sumber

### Bilakah berhenti memproses

Berhenti memproses jika anda melihat:

* ❌ "cakera penuh" atau "tidak dapat menulis fail" kesilapan
* ❌ Kesalahan rasuah fail gambar berulang
* ❌ Sistem sepenuhnya dibekukan (tidak bertindak balas)
* ❌ Menyedari tetapan yang salah dikonfigurasikan
* ❌ Imej yang salah diimport

**Cara Berhenti:**
1. Klik** Butang Berhenti/Batal **(menggantikan butang Mula)
2. Pemprosesan Halts, Kemajuan hilang
3. Betulkan masalah dan mulakan semula dari awal***

## menyelesaikan masalah semasa pemprosesan

### pemprosesan sangat perlahan
**Kemungkinan Punca:**

* Imej sasaran yang tidak bertanda (mengimbas semua imej)
* HDD bukannya storan SSD
* Sumber sistem yang tidak mencukupi
* Banyak indeks yang dikonfigurasikan
* Akses pemacu rangkaian

**Penyelesaian:**

1. Sekiranya baru bermula dan dalam mengesan peringkat: Batalkan, tandakan sasaran, mulakan semula
2. Untuk Masa Depan: Gunakan SSD, Kurangkan Indeks, Meningkatkan Perkakasan
3. Pertimbangkan CLI untuk pemprosesan kumpulan dataset besar

### "ruang cakera" amaran**Penyelesaian:**

1. Percuma ruang cakera segera
2. Pindahkan projek untuk memandu dengan lebih banyak ruang
3. Mengurangkan bilangan indeks untuk mengeksport
4. Gunakan format jpg dan bukannya TIFF (fail yang lebih kecil)

### Mesej "fail yang rosak" kerap**Penyelesaian:**

1. Imej semula salinan dari kad SD untuk memastikan integriti
2. Ujian kad SD untuk kesilapan
3. Keluarkan fail yang rosak dari projek
4. Teruskan memproses gambar yang tinggal

### sistem terlalu panas / pendikit**Penyelesaian:**

1. Pastikan pengudaraan yang mencukupi
2. Debu bersih dari lubang komputer
3. Kurangkan beban pemprosesan (gunakan mod percuma dan bukannya Chloros+)
4. Proses semasa waktu sejuk***

## Pemprosesan pemberitahuan lengkap

Semasa pemprosesan selesai:

* Bar kemajuan mencapai 100%
* **"Pemprosesan Lengkap"** Mesej muncul dalam Log Debug
* Butang mula diaktifkan lagi
* Semua fail output berada dalam subfolder model kamera

***

## Langkah seterusnya

Setelah pemprosesan selesai:

1.**Hasil semakan**-lihat [menyelesaikan pemprosesan](penamat-the-processing.md)
2.**Periksa folder output**- Sahkan semua fail yang dieksport dengan betul
3.**Tinjau Log Debug**- Periksa sebarang amaran atau kesilapan
4.**Pratonton Imej diproses** - Gunakan penonton imej atau perisian luaran

Untuk maklumat mengenai mengkaji dan menggunakan hasil yang diproses anda, lihat [menyelesaikan pemprosesan](penamat-the-processing.md).
