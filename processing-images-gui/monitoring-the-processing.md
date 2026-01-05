# Memantau Pemprosesan

Setelah pemprosesan dimulakan, Chloros menyediakan beberapa cara untuk memantau kemajuan, menyemak isu dan memahami perkara yang berlaku dengan set data anda. Halaman ini menerangkan cara menjejak pemprosesan anda dan mentafsir maklumat yang diberikan oleh Chloros.

## Gambaran Keseluruhan Bar Kemajuan

Bar kemajuan dalam pengepala atas menunjukkan status pemprosesan masa nyata dan peratusan penyiapan.

### Bar Kemajuan Mod Percuma

Untuk pengguna tanpa lesen Chloros+:

**Paparan Kemajuan 2 Peringkat:**

1.**Target Detect** - Mencari sasaran penentukuran dalam imej
2. **Pemprosesan** - Menggunakan pembetulan dan mengeksport**Bar kemajuan menunjukkan:**

* Peratusan penyiapan keseluruhan (0-100%)
* Nama pentas semasa
* Penggambaran bar mendatar mudah

### Chloros+ Bar Kemajuan

Untuk pengguna dengan lesen Chloros+:

**Paparan Kemajuan 4 Peringkat:**

1.**Mengesan** - Mencari sasaran penentukuran
2. **Menganalisis** - Memeriksa imej dan menyediakan saluran paip
3. **Menentukur** - Menggunakan pembetulan vignet dan pantulan
4. **Mengeksport** - Menyimpan fail yang diproses**Ciri Interaktif:*** **Tuding pada** bar kemajuan untuk melihat panel 4 peringkat yang dikembangkan
* **Klik** bar kemajuan untuk membekukan/pin panel yang dikembangkan
* **Klik sekali lagi** untuk menyahbeku dan menyembunyikan secara automatik pada cuti tetikus
* Setiap peringkat menunjukkan kemajuan individu (0-100%)

***

## Memahami Setiap Peringkat Pemprosesan

### Peringkat 1: Pengesanan (Pengesanan Sasaran)

**Apa yang berlaku:**

* Chloros mengimbas imej yang ditanda dengan kotak semak Sasaran
* Algoritma penglihatan komputer mengenal pasti 4 panel penentukuran
* Nilai pantulan diekstrak daripada setiap panel
* Cap masa sasaran direkodkan untuk penjadualan penentukuran yang betul

**Tempoh:**

* Dengan sasaran yang ditanda: 10-60 saat
* Tanpa sasaran yang ditanda: 5-30+ minit (mengimbas semua imej)

**Penunjuk kemajuan:**

* Mengesan: 0% → 100%
* Bilangan imej yang diimbas
* Sasaran yang ditemui dikira

**Perkara yang perlu diperhatikan:**

* Hendaklah selesai dengan cepat jika sasaran ditanda dengan betul
* Jika mengambil masa terlalu lama, sasaran mungkin tidak ditanda
* Semak Log Nyahpepijat untuk mesej "Sasaran ditemui".

### Peringkat 2: Menganalisis

**Apa yang berlaku:**

* Membaca metadata EXIF ​​imej (cap masa, tetapan pendedahan)
* Menentukan strategi penentukuran berdasarkan cap masa sasaran
* Mengadakan baris gilir pemprosesan imej
* Menyediakan pekerja pemprosesan selari (Chloros+ sahaja)

**Tempoh:** 5-30 saat**Penunjuk kemajuan:**

* Menganalisis: 0% → 100%
* Peringkat cepat, biasanya cepat selesai

**Perkara yang perlu diperhatikan:**

* Hendaklah maju secara berterusan tanpa jeda
* Amaran tentang kehilangan metadata akan muncul dalam Log Nyahpepijat

### Peringkat 3: Penentukuran

**Apa yang berlaku:*** **Debayering**: Menukar corak RAW Bayer kepada 3 saluran
* **Pembetulan vignet**: Menanggalkan kegelapan tepi kanta
* **Penentukuran pantulan**: Menormalkan dengan nilai sasaran
* **Pengiraan indeks**: Mengira indeks berbilang spektrum
* Memproses setiap imej melalui saluran paip penuh

**Tempoh:** Majoriti jumlah masa pemprosesan (60-80%)**Penunjuk kemajuan:**

* Penentukuran: 0% → 100%
* Imej semasa sedang diproses
* Imej selesai / Jumlah imej

**Tingkah laku pemprosesan:*** **Mod percuma**: Memproses satu imej pada satu masa secara berurutan
* **Chloros+ mod**: Memproses sehingga 16 imej serentak
* **Pecutan GPU**: Mempercepatkan peringkat ini dengan ketara**Perkara yang perlu diperhatikan:**

* Kemajuan mantap melalui kiraan imej
* Semak Log Nyahpepijat untuk mesej penyiapan setiap imej
* Amaran tentang kualiti imej atau isu penentukuran

### Peringkat 4: Mengeksport

**Apa yang berlaku:**

* Menulis imej yang ditentukur ke cakera dalam format yang dipilih
* Mengeksport imej indeks berbilang spektrum dengan warna LUT
* Mencipta subfolder model kamera
* Mengekalkan nama fail asal dengan akhiran yang sesuai

**Tempoh:** 10-20% daripada jumlah masa pemprosesan**Penunjuk kemajuan:**

* Mengeksport: 0% → 100%
* Fail sedang ditulis
* Format eksport dan destinasi

**Perkara yang perlu diperhatikan:**

* Amaran ruang cakera
* Ralat menulis fail
* Penyiapan semua output yang dikonfigurasikan

***

## Tab Log Nyahpepijat

Log Nyahpepijat menyediakan maklumat terperinci tentang kemajuan pemprosesan dan sebarang isu yang dihadapi.

### Mengakses Log Nyahpepijat

1. Klik ikon **Log Nyahpepijat** <img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line"> di bar sisi kiri
2. Panel log terbuka menunjukkan mesej pemprosesan masa nyata
3. Tatal automatik untuk menunjukkan mesej terkini

### Memahami Mesej Log

#### Mesej Maklumat (Putih/Kelabu)

Kemas kini pemprosesan biasa:

```
[INFO] Processing started
[INFO] Target detected in IMG_0015.RAW - 4 panels found
[INFO] Calibrating IMG_0234.RAW
[INFO] Exported NDVI image: IMG_0234_NDVI.tif
[INFO] Processing complete
```

#### Mesej Amaran (Kuning)

Isu bukan kritikal yang tidak berhenti memproses:

```
[WARN] No GPS data found in IMG_0145.RAW
[WARN] Target image timestamp gap > 30 minutes
[WARN] Low contrast in calibration panel - results may vary
```

**Tindakan:** Semak amaran selepas pemprosesan, tetapi jangan ganggu

#### Mesej Ralat (Red)

Isu kritikal yang mungkin menyebabkan pemprosesan gagal:

```
[ERROR] Cannot write file - disk full
[ERROR] Corrupted image file: IMG_0299.RAW
[ERROR] No targets detected - enable reflectance calibration or mark target images
```

**Tindakan:** Hentikan pemprosesan, selesaikan ralat, mulakan semula

### Mesej Log Biasa

| Mesej | Maksudnya | Tindakan Diperlukan |
| -------------------------------- | ---------------------------------------------------- | ---------------------------------------------------- |
| "Sasaran dikesan dalam \[nama fail]" | Sasaran penentukuran berjaya ditemui | Tiada - biasa |
| "Memproses imej X daripada Y" | Kemas kini kemajuan semasa | Tiada - biasa |
| "Tiada sasaran ditemui" | Tiada sasaran penentukuran dikesan | Tandai imej sasaran atau lumpuhkan penentukuran pantulan |
| "Ruang cakera tidak mencukupi" | Storan tidak mencukupi untuk output | Kosongkan ruang cakera |
| "Melangkau fail rosak" | Fail imej rosak | Salin semula fail daripada kad SD |
| "Data PPK digunakan" | Pembetulan GPS daripada fail .daq digunakan | Tiada - biasa |

### Menyalin Data Log

Untuk menyalin log untuk penyelesaian masalah atau sokongan:

1. Buka panel Log Nyahpepijat
2. Klik butang **"Salin Log"** (atau klik kanan → Pilih Semua)
3. Tampal ke dalam fail teks atau e-mel
4. Hantar kepada sokongan MAPIR jika perlu

***

## Pemantauan Sumber Sistem

### Penggunaan CPU

**Mod Percuma:**

* 1 teras CPU pada \~100%
* Teras lain melahu atau tersedia
* Sistem kekal responsif

**Chloros+ Mod Selari:**

* Berbilang teras pada 80-100% (sehingga 16 teras)
* Penggunaan CPU keseluruhan yang tinggi
* Sistem mungkin berasa kurang responsif

**Untuk memantau:**

* Windows Pengurus Tugas (Ctrl+Shift+Esc)
* Tab prestasi → bahagian CPU
* Cari proses "Chloros" atau "chloros-backend".

### Penggunaan Memori (RAM).

**Penggunaan biasa:**

* Projek kecil (< 100 imej): 2-4 GB
* Projek sederhana (100-500 imej): 4-8 GB
* Projek besar (500+ imej): 8-16 GB
* Chloros+ mod selari menggunakan lebih banyak RAM

**Jika ingatan rendah:**

* Proses kelompok yang lebih kecil
* Tutup aplikasi lain
* Tingkatkan RAM jika kerap memproses set data yang besar

### Penggunaan GPU (Chloros+ dengan CUDA)

Apabila pecutan GPU didayakan:

* GPU NVIDIA menunjukkan penggunaan yang tinggi (60-90%)
* Penggunaan VRAM meningkat (memerlukan 4GB+ VRAM)
* Peringkat penentukuran adalah jauh lebih pantas

**Untuk memantau:**

* Ikon Dulang Sistem NVIDIA
* Pengurus Tugas → Prestasi → GPU
* GPU-Z atau alat pemantauan yang serupa

### Cakera I/O

**Apa yang diharapkan:**

* Bacaan cakera tinggi semasa peringkat Menganalisis
* Tulis cakera tinggi semasa peringkat Mengeksport
* SSD jauh lebih pantas daripada HDD

**Petua prestasi:**

* Gunakan SSD untuk folder projek apabila boleh
* Elakkan pemacu rangkaian untuk set data yang besar
* Pastikan cakera tidak hampir kapasiti (menjejaskan kelajuan tulis)

***

## Mengesan Masalah Semasa Pemprosesan

### Tanda Amaran

**Gerai kemajuan (tiada perubahan selama 5+ minit):**

* Semak Log Nyahpepijat untuk mencari ralat
* Sahkan ruang cakera tersedia
* Semak Pengurus Tugas untuk memastikan Chloros sedang berjalan

**Mesej ralat kerap muncul:**

* Berhenti memproses dan menyemak ralat
* Punca biasa: ruang cakera, fail rosak, masalah memori
* Lihat bahagian Penyelesaian masalah di bawah

**Sistem menjadi tidak bertindak balas:**

* Chloros+ mod selari menggunakan terlalu banyak sumber
* Pertimbangkan untuk mengurangkan tugas serentak atau menaik taraf perkakasan
* Mod percuma kurang intensif sumber

### Bila Hentikan Pemprosesan

Hentikan pemprosesan jika anda melihat:

* ❌ Ralat "Disk penuh" atau "Tidak boleh menulis fail".
* ❌ Ralat rasuah fail imej berulang
* ❌ Sistem beku sepenuhnya (tidak bertindak balas)
* ❌ Menyedari tetapan yang salah telah dikonfigurasikan
* ❌ Imej salah diimport

**Cara berhenti:**

1. Klik**Butang Berhenti/Batal** (menggantikan butang Mula)
2. Pemprosesan terhenti, kemajuan hilang
3. Selesaikan isu dan mulakan semula dari awal

***

## Penyelesaian Masalah Semasa Pemprosesan

### Pemprosesan Sangat Lambat

**Punca yang mungkin:**

* Imej sasaran yang tidak ditanda (mengimbas semua imej)
* HDD dan bukannya storan SSD
* Sumber sistem tidak mencukupi
* Banyak indeks dikonfigurasikan
* Akses pemacu rangkaian

**Penyelesaian:**

1. Jika baru bermula dan dalam peringkat Mengesan: Batal, tandakan sasaran, mulakan semula
2. Untuk masa hadapan: Gunakan SSD, kurangkan indeks, tingkatkan perkakasan
3. Pertimbangkan CLI untuk memproses kumpulan data yang besar

### Amaran "Ruang Cakera".

**Penyelesaian:**

1. Kosongkan ruang cakera serta-merta
2. Gerakkan projek untuk memandu dengan lebih banyak ruang
3. Kurangkan bilangan indeks untuk dieksport
4. Gunakan format JPG dan bukannya TIFF (fail yang lebih kecil)

### Mesej "Fail Rosak" yang kerap

**Penyelesaian:**

1. Salin semula imej daripada kad SD untuk memastikan integriti
2. Uji kad SD untuk ralat
3. Alih keluar fail rosak daripada projek
4. Teruskan memproses imej yang tinggal

### Sistem Terlalu Panas / Pendikit

**Penyelesaian:**

1. Pastikan pengudaraan yang mencukupi
2. Bersihkan habuk dari lubang komputer
3. Kurangkan beban pemprosesan (gunakan mod Percuma dan bukannya Chloros+)
4. Proses pada waktu hari yang lebih sejuk

***

## Memproses Pemberitahuan Lengkap

Apabila pemprosesan selesai:

* Bar kemajuan mencapai 100%
* **Mesej "Pemprosesan Selesai"** muncul dalam Log Nyahpepijat
* Butang mula didayakan semula
* Semua fail output berada dalam subfolder model kamera

***

## Langkah Seterusnya

Setelah pemprosesan selesai:

1. **Semakan keputusan** - Lihat [Menyelesaikan Pemprosesan](finishing-the-processing.md)
2. **Semak folder output** - Sahkan semua fail yang dieksport dengan betul
3. **Semak Log Nyahpepijat** - Semak sebarang amaran atau ralat
4. **Pratonton imej yang diproses** - Gunakan Pemapar Imej atau perisian luaran

Untuk mendapatkan maklumat tentang menyemak dan menggunakan hasil yang diproses anda, lihat [Menyelesaikan Pemprosesan](finishing-the-processing.md).