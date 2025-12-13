# CLI: baris perintah

<Imige> <img src = ". gitbook/aset/cli.jpg" alt = ""> <igcapaption> </figcaption> </angka> ** chloros cli ** Menyediakan akses baris arahan yang kuat ke enjin pemprosesan imej kloros, yang membolehkan automasi, skrip, dan operasi kepala-ke-head.

### Ciri -ciri utama

*🚀 ** Automasi **: Pemprosesan batch skrip bagi pelbagai set data
*🔗 ** Integrasi **: Mengintegrasikan ke dalam aliran kerja dan proses yang ada
*💻 ** Operasi Bukan Gui **: Berlari Tanpa GUI
*🌍 ** berbilang bahasa **: Menyokong 38 bahasa
*⚡ ** Pemprosesan selari **: Dinamik menyesuaikan diri dengan CPU anda (sehingga 16 pekerja selari).

Keperluan ###

| Keperluan | Butiran |
| -------------------- | --------------------------------------------------------------------------- |
| ** Sistem Operasi ** | Windows 10/11 (64-bit) |
| ** Lesen ** | Chloros+ ([pelan pembayaran diperlukan] (https://cloud.mapir.camera/pricing)) |
| ** Memory ** | 8GB RAM Minimum (16GB disyorkan) |
| ** Internet ** | Diperlukan untuk Pengaktifan Lesen |
| ** ruang cakera ** | Berbeza bergantung kepada saiz projek |

{% petunjuk gaya = & quot; Amaran & quot; %}
** Keperluan Lesen **: CLI memerlukan langganan berbayar kepada Chloros+. Pelan standard (percuma) tidak mempunyai akses kepada CLI. Lawati [https://cloud.mapir.camera/pricingś(https://cloud.mapir.camera/pricing) untuk mengemas kini.
{ % endhint %}

## Permulaan cepat

### kemudahan

CLI secara automatik disertakan dengan pemasang kloros:

1. Muat turun dan jalankan ** chloros installer.exe **
2. Lengkapkan Wizard Pemasangan
3. CLI dipasang pada: ___inline0000___

{% petunjuk gaya = & quot; kejayaan & quot; %}
Pemasang secara automatik menambah ___inline0001___ ke laluan sistem anda. Mulakan semula terminal anda selepas pemasangan.
{ % endhint %}

### Persediaan awal

Sebelum menggunakan CLI, aktifkan lesen Chloros+ anda:

___Code0000___

### Penggunaan Asas

Proses folder dengan tetapan lalai:

___Code0001___

***

## Rujukan arahan

### Sintaks Umum

___Code0002___

***

## Perintah

### ___inline0002___: Proses gambar

Proses imej dalam folder dengan penentukuran.

** Sintaks: **

___Code0003___

** Contoh: **

___Code0004___

#### Pilihan arahan pemprosesan

| Pilihan | Jenis | Lalai | Penerangan |
| --------------------- | ------- | -------------- | ------------------------------------------------------------------------------ |
| ___Inline0003___ | Laluan | _Required_ | Folder yang mengandungi imej multispektral mentah/jpg |
| ___Inline0004___ | Laluan | Sama seperti entri | Folder Output untuk Imej yang Diproses |
| ___Inline0005___ | Rantai | Dihasilkan secara automatik | Nama Projek Kustom |
| ___Inline0006___ | Petunjuk | Didayakan | Dayakan Pembetulan Vignette |
| ___Inline0007___ | Petunjuk | - | Lumpuhkan Pembetulan Vignette |
| ___Inline0008___ | Petunjuk | Didayakan | Dayakan penentukuran refleksi |
| ___Inline0009___ | Petunjuk | - | Lumpuhkan penentukuran refleksi |
| ___Inline0010___ | Petunjuk | Dilumpuhkan | Sapukan pembetulan PPK dari data sensor cahaya .daq |
| ___Inline0011___ | Pilihan | TIFF (16 bit) | Format output: ___inline0012___, ___inline0013___, ___inline0014___, ___inline0015___ |
| ___Inline0016___ | Integer | Automatik | Saiz sasaran minimum dalam piksel untuk pengesanan panel penentukuran |
| ___Inline0017___ | Integer | Automatik | Ambang kumpulan sasaran (0-100) |
| ___Inline0018___ | Rantai | Tiada | Pendedahan kunci untuk model kamera (pin 1) |
| ___Inline0019___ | Rantai | Tiada | Pendedahan kunci untuk model kamera (pin 2) |
| ___Inline0020___ | Integer | Kereta | Selang pengubahsuaian dalam beberapa saat |
| ___Inline0021___ | Integer | 0 | Penyimpangan Masa dalam Jam |

***

### ___inline0022___ - Mengesahkan akaun

Log masuk dengan kelayakan kloros+ anda untuk membolehkan pemprosesan CLI.

** Sintaks: **

___Code0005___

** Contoh: **

___Code0006___

{% petunjuk gaya = & quot; Amaran & quot; %}
** Watak Khas **: Gunakan petikan tunggal di sekitar kata laluan yang mengandungi aksara seperti ___inline0023___, ___inline0024___, atau ruang.
{ % endhint %}

** Hasil: **

<retal> <img src = ". gitbook/aset/cli login_w.jpg" alt = ""> <igcaption> </figcaption> </angka> ***

### ___inline0025___: Padam kelayakan

Padam kelayakan yang disimpan dan log keluar dari akaun anda.

** Sintaks: **

___Code0007___

** Contoh: **

___Code0008___

** Keluar: **

___Code0009___

***

### ___inline0026___ - Periksa status lesen

Menunjukkan lesen semasa dan status pengesahan.

** Sintaks: **

___Code0010___

** Contoh: **

___Code0011___

** Keluar: **

___Code0012___

***

### ___inline0027___: Semak kemajuan eksport

Memantau kemajuan eksport Thread 4 semasa atau selepas diproses.

** Sintaks: **

___Code0013___

** Contoh: **

___Code0014___

** Gunakan Kes: ** Panggil arahan ini semasa pemprosesan sedang berjalan untuk memeriksa kemajuan eksport.

***

### ___inline0028___: mengurus bahasa antara muka

Lihat atau ubah bahasa antara muka CLI.

** Sintaks: **

___Code0015___

** Contoh: **

___Code0016___

#### bahasa yang disokong (38 secara keseluruhan)

| Kod | Bahasa | Nama asli |
| ------- | --------------------- | ---------------- |
| ___Inline0029___ | Bahasa Inggeris | Bahasa Inggeris |
| ___Inline0030___ | Sepanyol | Sepanyol |
| ___Inline0031___ | Portugis | Portugis |
| ___Inline0032___ | Perancis | Perancis |
| ___Inline0033___ | Jerman | Jerman |
| ___Inline0034___ | Itali | Itali |
| ___Inline0035___ | Jepun | 日本語 |
| ___Inline0036___ | Korea | 한국어 |
| ___Inline0037___ | Cina (dipermudahkan) | 简体中文 |
| ___Inline0038___ | Cina (tradisional) | 繁體中文 |
| ___Inline0039___ | Rusia | Rusia |
| ___Inline0040___ | Belanda | Belanda |
| ___Inline0041___ | Arab | العرangsang | |
| ___Inline0042___ | Poland | Polsky |
| ___Inline0043___ | Turki | Turkce |
| ___Inline0044___ | Hindi | हिंदी |
| ___Inline0045___ | Indonesia | Bahasa Indonesia |
| ___Inline0046___ | Vietnam | Tiếng việt |
| ___Inline0047___ | Thai | ไทย |
| ___Inline0048___ | Sweden | Svenska |
| ___Inline0049___ | Denmark | Dansk |
| ___Inline0050___ | Norway | Norsk |
| ___Inline0051___ | Finland | Suomi |
| ___Inline0052___ | Greek | Ελληνικά |
| ___Inline0053___ | Czech | CEština |
| ___Inline0054___ | Hungary | Magyar |
| ___Inline0055___ | Romania | Română |
| ___Inline0056___ | Ukraine | Ураїнса |
| ___Inline0057___ | Portugis Brazil | Portugis Brazil |
| ___Inline0058___ | Kanton | 粵語 |
| ___Inline0059___ | Melayu | Bahasa Melayu |
| ___Inline0060___ | Slovak | Slovenčina |
| ___Inline0061___ | Bulgaria | Bъъъаи |
| ___Inline0062___ | Croatian | Hrvatski |
| ___Inline0063___ | Lithuanian | Lietuvių |
| ___Inline0064___ | Latvian | Latviešu |
| ___Inline0065___ | Estonian | Eesti |
| ___Inline0066___ | Slovenian | Slovenščina |

{% petunjuk gaya = & quot; kejayaan & quot; %}
** Kegigihan Automatik **: Keutamaan bahasa anda disimpan dalam ___inline0067___ dan berterusan merentasi sesi.
{ % endhint %}

***

### ___inline0068___: Tetapkan folder projek lalai

Tukar lokasi lalai folder projek (dikongsi dengan GUI).

** Sintaks: **

___Code0017___

** Contoh: **

___Code0018___

***

### ___inline0069___: Tunjukkan folder projek

Menunjukkan lokasi lalai semasa folder projek.

** Sintaks: **

___Code0019___

** Contoh: **

___Code0020___

** Keluar: **

___Code0021___

***

### ___inline0070___: Tetapkan semula ke nilai lalai

Tetapkan semula folder projek ke lokasi lalai.

** Sintaks: **

___Code0022___

***

## Pilihan global

Pilihan ini dikenakan untuk semua arahan:

| Pilihan | Jenis | Lalai | Penerangan |
| --------------- | ------- | ------------- | ------------------------------------------------ |
| ___Inline0071___ | Laluan | Dikesan secara automatik | Laluan ke Backend Executable |
| ___Inline0072___ | Integer | 5000 | Nombor port backend API |
| ___Inline0073___ | Petunjuk | - | Pasukan Backend Restart (Membunuh Proses Sedia Ada) |
| ___Inline0074___ | Petunjuk | - | Tunjukkan maklumat versi dan keluar |
| ___Inline0075___ | Petunjuk | - | Tunjukkan Maklumat Bantuan dan Keluar |

** Contoh dengan pilihan global: **

___Code0023___

***

## Panduan Konfigurasi Pemprosesan

### Pemprosesan selari

Chloros+ cli ** skala secara automatik ** pemprosesan selari agar sesuai dengan keupayaan komputer anda:

** Bagaimana ia berfungsi: **

* Mengesan CPU dan teras RAM.
*Menetapkan pekerja: ** 2 × CPU teras ** (menggunakan hyperthreading).
*** Maksimum: 16 pekerja selari ** (untuk kestabilan yang lebih besar).

** Tahap Sistem: **

| Jenis Sistem | CPU | Ram | Pekerja | Prestasi |
| ------------- | ---------- | -------- | -------- | --------------- |
| ** julat tinggi ** | 16+ teras | 32+ GB | Sehingga 16 | Kelajuan maksimum |
| ** Mid-range ** | 8-15 teras | 16-31GB | 8-16 | Kelajuan yang sangat baik |
| ** julat rendah ** | 4-7 teras | 8-15GB | 4-8 | Kelajuan yang baik |

{% petunjuk gaya = & quot; kejayaan & quot; %}
** Pengoptimuman Auto **: CLI secara automatik mengesan spesifikasi sistem anda dan mengkonfigurasi pemprosesan selari yang optimum. Tiada konfigurasi manual yang diperlukan!
{ % endhint %}

### Kaedah debayer

CLI menggunakan ** berkualiti tinggi (lebih cepat) ** sebagai algoritma debayer lalai dan disyorkan:

| Kaedah | Kualiti | Kelajuan | Penerangan |
| --------------------------- | ------- | ----- | ------------------------------------------- |
| ** berkualiti tinggi (lebih cepat) ** ⭐ | ⭐⭐⭐⭐ | ⚡⚡⚡ | Algoritma sensitif tepi (lalai, disyorkan) |

### pembetulan vignette

** Apa yang dilakukannya: ** Membetulkan kehilangan cahaya di tepi imej (sudut gelap biasa dalam imej kamera).

*** Diaktifkan secara lalai **: Kebanyakan pengguna harus menyimpan pilihan ini diaktifkan.
* Gunakan ___inline0076___ untuk melumpuhkannya.

{% petunjuk gaya = & quot; kejayaan & quot; %}
** Cadangan **: Sentiasa hidupkan pembetulan vignette untuk memastikan kecerahan seragam merentasi seluruh bingkai.
{ % endhint %}

### penentukuran refleksi

Menukar nilai sensor mentah ke dalam peratusan refleksi standard menggunakan panel penentukuran.

*** Diaktifkan secara lalai ** - penting untuk analisis tumbuh -tumbuhan.
*Memerlukan panel penentukuran pada imej.
* Gunakan ___inline0077___ untuk melumpuhkannya.

{% petunjuk gaya = & quot; info & quot; %}
** Keperluan **: Pastikan panel penentukuran terdedah dengan betul dan dapat dilihat dalam imej anda untuk penukaran refleksi yang tepat.
{ % endhint %}

### ppk fixes

** Apa yang dilakukannya: ** Menggunakan pembetulan kinematik selepas diproses menggunakan data log DAQ-A-SD untuk meningkatkan ketepatan GPS.

*** dilumpuhkan secara lalai **
* Gunakan ___inline0078___ untuk mengaktifkannya
* Memerlukan fail .daq dalam folder Projek Sensor Light Mapir Daq-A-SD.

### Format output

<able> <thead> <th width = "197"> format </th> <th width = "130.20001220703125 "> kedalaman bit </th> <th width =" 116.599975859375 " (16-bit) </strong> ⭐ </td> <td> 16-bit integer </td> <td> large </td> <td> analisis GIS, photogrammetry (disyorkan) Besar </td> <td> Analisis saintifik, penyelidikan </td> </tr> <tr> <td> <strong> png (8-bit) </strong> </td> <td> 8-bit integer </td> </td> (8-bit) </strong> </td> <td> 8-bit integer </td> <td> kecil </td> <td> pratonton cepat, output dimampatkan </td> </tr> </tbody>

## Automasi dan skrip

### Pemprosesan batch PowerShell

Secara automatik memproses pelbagai folder set data:

___Code0024___

### skrip batch windows

Gelung mudah untuk pemprosesan batch:

___Code0025___

### skrip automasi python

Automasi lanjutan dengan pengurusan ralat:

___Code0026___

***

## Pemprosesan aliran kerja

### aliran kerja standard

1. ** entri **: folder yang mengandungi pasangan gambar mentah/jpg
2. ** Pengesanan **: CLI secara automatik mencari fail imej yang serasi
3. ** Pemprosesan **: Mod selari menyesuaikan diri dengan teras CPU anda (chloros+)
4. ** Output **: Buat subfolder setiap model kamera dengan imej yang diproses

Contoh Struktur Output ### Contoh

___Code0027___

### anggaran masa pemprosesan

Masa pemprosesan biasa untuk 100 imej (12 MP setiap satu):

| Mod | Cuaca | Perkakasan |
| ----------------- | --------- | -------------------------------------------- |
| ** Mod selari ** | 5-10 min | I7/Ryzen 7, 16GB RAM, SSD (sehingga 16 pekerja) |
| ** Mod selari ** | 10-15 min | I5/Ryzen 5, 8 GB RAM, HDD (sehingga 8 pekerja) |

{% petunjuk gaya = & quot; info & quot; %}
** Petua Prestasi **: Masa pemprosesan berbeza -beza bergantung kepada bilangan imej, resolusi, dan spesifikasi komputer.
{ % endhint %}

***

## Penyelesaian masalah

### CLI tidak dijumpai

** Kesalahan: **

___Code0028___

** Penyelesaian: **

1. Periksa lokasi pemasangan:

___Code0029___

2. Gunakan jalan penuh jika tidak berada di jalan:

___Code0030___

3. Tambahkannya ke jalan secara manual:
   *Buka sifat sistem → pembolehubah persekitaran.
   * Edit pemboleh ubah jalan.
   * Tambah: ___inline0079___
   * Mulakan semula terminal.

***

### Ralat bermula backend.

** Kesalahan: **

___Code0031___

** Penyelesaian: **

1. Periksa sama ada backend sudah berjalan (tutup terlebih dahulu).
2. Periksa bahawa firewall Windows tidak menyekatnya.
3. Cuba pelabuhan yang berbeza:

___Code0032___

4. Kekuatan Mulakan semula Backend:

___Code0033___

***

### Lesen/Isu Pengesahan

** Kesalahan: **

___Code0034___

** Penyelesaian: **

1. Periksa bahawa anda mempunyai langganan kloros+ aktif.
2. Log masuk dengan kelayakan anda:

___Code0035___

3. Periksa status lesen:

___Code0011___

4. Sokongan Hubungi: info@mapir.camera

***

### Tiada imej yang dijumpai.

** Kesalahan: **

___Code0037___

** Penyelesaian: **

1. Periksa bahawa folder mengandungi format yang disokong (.raw, .tif, .jpg).
2. Periksa bahawa laluan folder adalah betul (gunakan petikan untuk laluan dengan ruang).
3. Pastikan anda telah membaca kebenaran untuk folder.
4. Periksa bahawa sambungan fail betul.

***

### Pemprosesan berhenti atau menggantung

** Penyelesaian: **

1. Semak ruang cakera yang ada (pastikan terdapat cukup untuk output).
2. Tutup aplikasi lain untuk membebaskan ingatan.
3. Kurangkan bilangan imej (proses batch).

***

### port sudah digunakan

** Kesalahan: **

___Code0038___

** Penyelesaian: **

Tentukan port yang berbeza:

___Code0032___

***

## Soalan yang sering ditanya

### Q: Adakah saya memerlukan lesen untuk CLI?

** A: ** Ya! CLI memerlukan ** lesen Chloros+ yang dibayar **.

* ❌ Pelan standard (percuma): CLI dilumpuhkan
* ✅ kloros+ rancangan (dibayar): CLI diaktifkan sepenuhnya

Langgan AT: [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing)

***

### Q: Bolehkah saya menggunakan CLI pada pelayan tanpa GUI?

** A: ** Ya! CLI berfungsi sepenuhnya tanpa antara muka grafik. Keperluan:

* Windows Server 2016 atau Lagi
* Visual C ++ Redistributable Dipasang
* RAM yang mencukupi (minimum 8 GB, disyorkan 16 GB)
* Pengaktifan satu kali lesen GUI di mana-mana mesin

***

### Q: Di manakah imej diproses disimpan?

** A: ** Secara lalai, imej yang diproses disimpan dalam folder yang sama seperti input ** dalam subfolder model kamera (mis. ___Inline0080___).

Gunakan pilihan ___inline0081___ untuk menentukan folder output yang berbeza:

___Code0040___

***

### Q: Bolehkah saya memproses pelbagai folder sekaligus?

** A: ** Tidak langsung dengan satu arahan, tetapi anda boleh menggunakan skrip untuk memproses folder secara berurutan. Lihat bahagian [Automasi dan Skrip] (CLI.MD#Automasi-Scripting).

***

### Q: Bagaimana saya menyimpan output CLI ke fail log?

** PowerShell: **

___Code0041___

** batch: **

___Code0042___

***

### Q: Apa yang berlaku jika saya menekan Ctrl+C semasa pemprosesan?

** A: ** CLI akan melakukan perkara berikut:

1. Ia akan berhenti diproses secara teratur.
2. Ia akan menutup backend.
3. Ia akan keluar dengan kod 130.

Imej yang diproses sebahagiannya mungkin kekal dalam folder output.

***

### Q: Bolehkah saya mengautomasikan pemprosesan CLI?

** A: ** Sudah tentu! CLI direka untuk automasi. Lihat [Automasi dan Skrip] (CLI.MD#Automasi-Scripting) untuk contoh PowerShell, Batch, dan Python.

***

### Q: Bagaimana saya boleh menyemak versi CLI?

** A: **

___Code0043___

** Keluar: **

___Code0044___

***

## Dapatkan bantuan

### Bantuan baris arahan

Lihat maklumat bantuan secara langsung di CLI:

___Code0045___

### saluran sokongan

*** E -mel **: info@mapir.camera
*** Laman web **: [https://www.mapir.camera/community/contact n(https://www.mapir.camera/community/contact)
*** Harga **: [https://cloud.mapir.camera/pricingś(https://cloud.mapir.camera/pricing)

***

## Contoh lengkap

### Contoh 1: Pemprosesan Asas

Pemprosesan dengan Tetapan Lalai (Vignette, Refleksi):

___Code0046___

***

### Contoh 2: Hasil saintifik berkualiti tinggi

Tiff terapung 32-bit:

___Code0047___

***

### Contoh 3: Pemprosesan Pratonton Cepat

8-bit PNG tanpa penentukuran untuk semakan cepat:

___Code0048___

***

### Contoh 4: Pemprosesan yang diperbetulkan dengan PPK

Sapukan pembetulan PPK dengan pemantulan:

___Code0049___

***

### Contoh 5: Lokasi output tersuai

Proses pada pemacu yang berbeza dengan format tertentu:

___Code0050___

***

### Contoh 6: Aliran kerja pengesahan

Aliran pengesahan lengkap:

___Code0051___

***

### Contoh 7: Penggunaan berbilang bahasa

Tukar bahasa antara muka:

___Code0052___