# API: Python SDK

** Chloros Python SDK ** menyediakan akses programatik kepada enjin pemprosesan imej kloros, membolehkan automasi, aliran kerja tersuai, dan integrasi lancar dengan aplikasi Python dan proses penyelidikan anda.

### Ciri -ciri utama

*🐍 ** Python asli ** - API Pythonic Bersih untuk Pemprosesan Imej
*🔧 ** Akses API Penuh ** - Kawalan penuh ke atas pemprosesan kloros
*🚀 ** Automasi ** - Buat aliran kerja pemprosesan batch tersuai
*🔗 ** Integrasi ** - Embed chloros ke dalam aplikasi python sedia ada.
*📊 ** Penyelidikan Sedia **: Sempurna untuk proses analisis saintifik.
*⚡ ** Pemprosesan selari **: menyesuaikan diri dengan teras CPU anda (chloros+).

Keperluan ###

| Keperluan | Butiran |
| -------------------- | --------------------------------------------------------------------------- |
| ** Desktop Chloros ** | Mesti dipasang secara tempatan |
| ** Lesen ** | Chloros+ ([pelan pembayaran diperlukan] (https://cloud.mapir.camera/pricing)) |
| ** Sistem Operasi ** | Windows 10/11 (64-bit) |
| ** Python ** | Python 3.7 atau lebih tinggi |
| ** Memory ** | 8GB RAM Minimum (16GB disyorkan) |
| ** Internet ** | Diperlukan untuk Pengaktifan Lesen |

{% petunjuk gaya = & quot; Amaran & quot; %}
** Keperluan Lesen **: Python SDK memerlukan langganan berbayar untuk chloros+ untuk mengakses API. Pelan standard (percuma) tidak mempunyai akses API/SDK. Lawati [https://cloud.mapir.camera/pricingś(https://cloud.mapir.camera/pricing) untuk mengemas kini.
{ % endhint %}

## Permulaan cepat

### kemudahan

Pasang melalui PIP:

___Code0000___

{% petunjuk gaya = & quot; info & quot; %}
** Persediaan Awal **: Sebelum menggunakan SDK, aktifkan lesen Chloros+ anda dengan membuka Chloros, Chloros (penyemak imbas), atau chloros CLI dan log masuk dengan kelayakan anda. Ini hanya perlu dilakukan sekali.
{ % endhint %}

### Penggunaan Asas

Proses folder dengan hanya beberapa baris:

___Code0001___

### Kawalan penuh

Untuk aliran kerja lanjutan:

___Code0002___

***

## Panduan pemasangan

### Prasyarat

Sebelum memasang SDK, pastikan anda mempunyai:

1. ** Desktop Chloros ** Dipasang ([muat turun] (muat turun.md))
2. ** Python 3.7+** Dipasang ([python.org] (https://www.python.org))
3. ** Lesen AKTIF CHLOROS+ ** ([UPDATE] (https://cloud.mapir.camera/pricing))

### Pemasangan menggunakan PIP

** Pemasangan Standard: **

___Code0000___

** Dengan sokongan pemantauan kemajuan: **

___Code0004___

** Pemasangan Pembangunan: **

___Code0005___

### Sahkan pemasangan

Periksa bahawa SDK dipasang dengan betul:

___Code0006___

***

## Persediaan awal

### Pengaktifan lesen

SDK menggunakan lesen yang sama seperti chloros, chloros (penyemak imbas) dan chloros cli. Aktifkannya sekali melalui GUI atau CLI:

1. Buka ** Chloros atau Chloros (penyemak imbas) ** dan log masuk ke pengguna <img src = ". Gitbook/aset/icon_user.jpg" alt = "" data-size = "line"> tab. Atau, buka ** cli **.
2. Masukkan kelayakan kloros+ anda dan log masuk
3. Lesen disimpan dalam cache tempatan (berterusan selepas reboot)

{% petunjuk gaya = & quot; kejayaan & quot; %}
** Persediaan satu kali **: Selepas anda log masuk melalui GUI atau CLI, SDK secara automatik menggunakan lesen cache. Tiada pengesahan tambahan diperlukan!
{ % endhint %}

### Sambungan ujian

Semak bahawa SDK boleh menyambung ke kloros:

___Code0007___

***

## Rujukan API

### kelas chloroslocal

Kelas utama untuk pemprosesan imej kloros tempatan.

#### pembina

___Code0008___

** Parameter: **

| Parameter | Jenis | Lalai | Penerangan |
| ------------------------- | ---- | ------------------------- | ------------------------------------- |
| ___Inline0000___ | str | ___Inline0001___ | URL backend tempatan Chloros |
| ___Inline0002___ | Bool | ___Inline0003___ | Secara automatik memulakan backend jika perlu |
| ___Inline0004___ | str | ___Inline0005___ (auto-detect) | Laluan ke Backend Executable |
| ___Inline0006___ | int | ___Inline0007___ | Permintaan masa dalam beberapa saat |
| ___Inline0008___ | int | ___Inline0009___ | Backend Startup Timeout (Seconds) |

** Contoh: **

___Code0009___

***

### Kaedah

#### ___inline0010___

Buat projek kloros baru.

** Parameter: **

| Parameter | Jenis | Mandatori | Penerangan |
| -------------- | ---- | -------- | -------------------------------------------------------- |
| ___Inline0011___ | str | Ya | Nama Projek |
| ___Inline0012___ | str | Tidak | Templat Kamera (mis. "Survey3n \ _rgn", "Survey3w \ _Ocn") |

** Pulangan: ** ___inline0013___: Respons Penciptaan Projek

** Contoh: **

___Code0010___

***

#### ___inline0014___

Import imej dari folder.

** Parameter: **

| Parameter | Jenis | Mandatori | Penerangan |
| ------------- | -------- | -------- | ---------------------------------- |
| ___Inline0015___ | str/path | Ya | Laluan ke folder dengan imej |
| ___Inline0016___ | Bool | Tidak | Subfolder Cari (lalai: palsu) |

** Pulangan: ** ___inline0017___: Import Hasil dengan kiraan fail.

** Contoh: **

___Code0011___

***

#### ___inline0018___

Konfigurasikan tetapan pemprosesan.

** Parameter: **

| Parameter | Jenis | Lalai | Penerangan |
| ------------------------- | ---- | ----------------------- | ---------------------------- |
| ___Inline0019___ | str | «Berkualiti tinggi (lebih cepat)» | Kaedah Debayer |
| ___Inline0020___ | Bool | ___Inline0021___ | Dayakan Pembetulan Vignette |
| ___Inline0022___ | Bool | ___Inline0023___ | Dayakan penentukuran refleksi |
| ___Inline0024___ | Senarai | ___Inline0025___ | Indeks Vegetasi untuk Mengira |
| ___Inline0026___ | str | «Tiff (16 bit)» | Format output |
| ___Inline0027___ | Bool | ___Inline0028___ | Dayakan Pembetulan PPK |
| ___Inline0029___ | dict | ___Inline0030___ | Tetapan Advanced Custom |

** Format Eksport: **

* ___Inline0031___: Disyorkan untuk GIS/Photogrammetry
* ___Inline0032___: Analisis saintifik
* ___Inline0033___: Pemeriksaan visual
* ___Inline0034___: output termampat

** indeks tersedia: **

NDVI, NDRE, GNDVI, OSAVI, CIG, EVI, SAVI, MSAVI, MTVI2 dan banyak lagi.

** Contoh: **

___Code0012___

***

#### ___inline0035___

Imej projek proses.

** Parameter: **

| Parameter | Jenis | Lalai | Penerangan |
| ------------------- | -------- | ------------ | ----------------------------------------- |
| ___Inline0036___ | str | ___Inline0037___ | Mod Pemprosesan: "Selari" atau "Serial" |
| ___Inline0038___ | Bool | ___Inline0039___ | Tunggu ia selesai |
| ___Inline0040___ | Callable | ___Inline0041___ | Fungsi Panggilan Kembali Kemajuan (Kemajuan, Mesej) |
| ___Inline0042___ | Float | ___Inline0043___ | Pengundian Selang untuk Kemajuan (Seconds) |

** Pulangan: ** ___inline0044___ - Hasil pemprosesan

{% petunjuk gaya = & quot; Amaran & quot; %}
** Mod Paralel **: Memerlukan Lesen Chloros+. Secara automatik menyesuaikan diri dengan teras CPU (sehingga 16 pekerja).
{ % endhint %}

** Contoh: **

___Code0013___

***

#### ___inline0045___

Mendapat konfigurasi projek semasa.

** Pulangan: ** ___inline0046___: Konfigurasi Projek Semasa.

** Contoh: **

___Code0014___

***

#### ___inline0047___

Dapatkan maklumat mengenai status backend.

** Pulangan: ** ___inline0048___ - Status backend.

** Contoh: **

___Code0015___

***

#### ___inline0049___

Tutup backend (jika dimulakan dengan SDK).

** Contoh: **

___Code0016___

***

### Ciri -ciri kemudahan

#### ___inline0050___

Fungsi kemudahan satu baris untuk memproses folder.

** Parameter: **

| Parameter | Jenis | Lalai | Penerangan |
| ------------------------- | -------- | --------------- | --------------------------- |
| ___Inline0051___ | str/path | Mandatori | Laluan ke folder dengan imej |
| ___Inline0052___ | str | Dihasilkan secara automatik | Nama Projek |
| ___Inline0053___ | str | ___Inline0054___ | Templat Kamera |
| ___Inline0055___ | Senarai | ___Inline0056___ | Indeks untuk mengira |
| ___Inline0057___ | Bool | ___Inline0058___ | Dayakan Pembetulan Vignette |
| ___Inline0059___ | Bool | ___Inline0060___ | Dayakan penentukuran refleksi |
| ___Inline0061___ | str | «Tiff (16 bit)» | Format output |
| ___Inline0062___ | str | ___Inline0063___ | Mod Pemprosesan |
| ___Inline0064___ | Callable | ___Inline0065___ | Panggilan balik kemajuan |

** Pulangan: ** ___inline0066___ - Hasil pemprosesan.

** Contoh: **

___Code0017___

***

## Sokongan Pengurus Konteks

SDK menyokong pengurus konteks untuk pembersihan automatik:

___Code0018___

***

## Contoh lengkap

### Contoh 1: Pemprosesan Asas

Proses folder dengan tetapan lalai:

___Code0019___

***

### Contoh 2: aliran kerja tersuai

Kawalan penuh ke atas proses pemprosesan:

___Code0020___

***

### Contoh 3: pemprosesan batch pelbagai folder

Proses pelbagai set data penerbangan:

___Code0021___

***

### Contoh 4: Integrasi proses penyelidikan

Mengintegrasikan kloros dengan analisis data:

___Code0022___

***

### Contoh 5: Pemantauan Kemajuan Peribadi

Penjejakan kemajuan lanjutan dengan pembalakan:

___Code0023___

***

### Contoh 6: Pengendalian ralat

Pengurusan ralat yang teguh untuk kegunaan pengeluaran:

___Code0024___

***

### Contoh 7: Alat baris arahan

Buat alat CLI tersuai dengan SDK:

___Code0025___

** Gunakan: **

___Code0026___

***

## Pengurusan Pengecualian

SDK menyediakan kelas pengecualian khusus untuk pelbagai jenis kesilapan:

### hierarki pengecualian

___Code0027___

### Contoh pengecualian

___Code0028___

***

## Topik lanjutan

### Konfigurasi backend tersuai

Gunakan lokasi backend tersuai atau konfigurasi:

___Code0029___

### pemprosesan bebas blok

Mula memproses dan teruskan dengan tugas lain:

___Code0030___

### Pengurusan memori

Untuk set data yang besar, proses batch:

___Code0031___

***

## Penyelesaian masalah

### backend tidak bermula

** Masalah: ** SDK tidak memulakan backend.

** Penyelesaian: **

1. Periksa bahawa desktop kloros dipasang:

___Code0032___

2. Periksa bahawa Windows Firewall tidak menghalang anda.
3. Cuba laluan backend manual:

___Code0033___

***

### Lesen tidak dikesan

** Masalah: ** SDK memberi amaran bahawa lesen itu hilang.

** Penyelesaian: **

1.
2. Periksa bahawa lesen itu cache:

___Code0034___

3. Sokongan Hubungi: info@mapir.camera

***

### Kesalahan import

** Masalah: ** ___inline0067___

** Penyelesaian: **

___Code0035___

***

### tamat masa pemprosesan

** Masalah: ** Pemprosesan masa keluar.

** Penyelesaian: **

1. Meningkatkan masa menunggu:

___Code0036___

2. Proses kelompok yang lebih kecil
3. Periksa ruang cakera yang ada
4. Memantau sumber sistem

***

### port sudah digunakan

** Masalah: ** port backend 5000 sibuk

** Penyelesaian: **

___Code0037___

Atau mencari dan menutup proses yang bercanggah:

___Code0038___

***

## Petua Prestasi

### mengoptimumkan kelajuan pemprosesan

1. ** Gunakan mod selari ** (memerlukan chloros+)

___Code0039___

2. ** Kurangkan resolusi output ** (jika boleh diterima)

___Code0040___

3. ** Lumpuhkan indeks yang tidak perlu **

___Code0041___

4. ** Proses pada SSD ** (bukan HDD)

***

### Pengoptimuman memori

Untuk set data yang besar:

___Code0042___

***

### Pemprosesan latar belakang

Python percuma untuk tugas lain:

___Code0043___

***

## Contoh integrasi

### Integrasi Django

___Code0044___

### Flask API

___Code0045___

### Jupyter Notebook

___Code0046___

***

## Soalan yang sering ditanya

### Q: Adakah SDK memerlukan sambungan internet?

** A: ** Untuk pengaktifan lesen awal sahaja. Selepas anda log masuk melalui chloros, chloros (pelayar), atau chloros CLI, lesen itu di -cache secara tempatan dan berfungsi di luar talian selama 30 hari.

***

### Q: Bolehkah saya menggunakan SDK pada pelayan tanpa GUI?

** A: ** Ya! Keperluan:

* Windows Server 2016 atau Lagi
* Dipasang kloros (satu masa sahaja)
* Lesen diaktifkan pada mana -mana mesin (lesen cache disalin ke pelayan)

***

### Q: Apakah perbezaan antara desktop, CLI dan SDK?

| Ciri | Desktop GUI | CLI COMMAND LINE | Python SDK |
| --------------- | ----------- | ---------------- | ----------- |
| ** Interface ** | Titik dan klik | Perintah | Python Api |
| ** Ideal untuk ** | Kerja Visual | SCRIPTING | Integrasi |
| ** Automasi ** | Terhad | Baik | Cemerlang |
| ** Fleksibiliti ** | Asas | Baik | Maksimum |
| ** Lesen ** | Chloros+ | Chloros+ | Chloros+ |

***

### Q: Bolehkah saya mengedarkan aplikasi yang dibuat dengan SDK?

** A: ** Kod SDK boleh diintegrasikan ke dalam aplikasi anda, tetapi:

*Pengguna akhir perlu memasang kloros.
*Pengguna akhir perlu mempunyai lesen kloros+ aktif.
*Pengedaran komersial memerlukan lesen OEM.

Sila hubungi info@mapir.camera untuk pertanyaan OEM.

***

### Q: Bagaimana saya mengemas kini SDK?

___Code0047___

***

### Q: Di manakah imej diproses disimpan?

Secara lalai, di jalan projek:

___Code0048___

***

### Q: Bolehkah saya memproses imej dari skrip python yang berjalan secara programatik?

** A: ** Ya! Gunakan Windows Tugas Scheduler dengan skrip Python:

___Code0049___

Jadual pelaksanaan harian menggunakan penjadual tugas.

***

### Q: Adakah SDK menyokong async/menunggu?

** A: ** Versi semasa adalah segerak. Untuk tingkah laku asynchronous, gunakan ___inline0068___ atau jalankannya pada benang yang berasingan:

___Code0050___

***

## Dapatkan bantuan

### Dokumentasi

*** rujukan API **: Halaman ini

### saluran sokongan

*** E -mel **: info@mapir.camera
*** Laman web **: [https://www.mapir.camera/community/contact n(https://www.mapir.camera/community/contact)
*** Harga **: [https://cloud.mapir.camera/pricingś(https://cloud.mapir.camera/pricing)

### Contoh kod

Semua contoh yang disertakan di sini telah diuji dan bersedia untuk kegunaan pengeluaran. Salin dan menyesuaikannya dengan kes penggunaan anda.

***

## Lesen

** Perisian Proprietari ** - Hak Cipta (c) 2025 Mapir Inc.

SDK memerlukan langganan kloros+ aktif. Penggunaan, pengedaran atau pengubahsuaian yang tidak dibenarkan adalah dilarang.