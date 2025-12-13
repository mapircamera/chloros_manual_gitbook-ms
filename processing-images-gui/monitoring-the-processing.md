# Pemantauan pemantauan

Sebaik sahaja pemprosesan telah bermula, Chloros menawarkan beberapa cara untuk memantau kemajuan, periksa masalah, dan memahami apa yang berlaku dengan set data anda. Halaman ini menerangkan cara mengesan pemprosesan dan mentafsirkan maklumat yang disediakan oleh Chloros.

## Gambaran Keseluruhan Bar Kemajuan

Bar kemajuan di bahagian atas tajuk menunjukkan status pemprosesan masa nyata dan peratusan lengkap.

### bar kemajuan dalam mod percuma

Bagi pengguna tanpa lesen Chloros+:

** Paparan kemajuan dalam dua peringkat: **

1. ** Pengesanan Sasaran **: Cari sasaran penentukuran dalam imej.
2. ** Pemprosesan **: Penggunaan pembetulan dan eksport.

** Kemajuan bar menunjukkan: **

* Jumlah peratusan siap (0-100%)
* Nama peringkat semasa
* Paparan mudah dengan bar mendatar

### chloros+ bar kemajuan

Bagi pengguna dengan lesen Chloros+:

** Kemajuan visualisasi dalam 4 peringkat: **

1. ** Pengesanan **: Cari sasaran penentukuran.
2. ** Analisis **: Pemeriksaan imej dan penyediaan proses.
3. ** Penentukuran **: Penggunaan vignette dan pembetulan refleksi.
4. ** Eksport **: Menyimpan fail yang diproses.

** Ciri Interaktif: **

*** Hover ke atas ** Bar Kemajuan untuk melihat panel 4-peringkat yang diperluaskan.
*** Klik ** pada bar kemajuan untuk membekukan/pin panel yang diperluaskan
*** Klik Lagi ** untuk Membongkar dan Sembunyikan Secara Automatik Apabila anda mengeluarkan tetikus
* Setiap peringkat menunjukkan kemajuan individu (0-100%)

***

## Memahami setiap peringkat pemprosesan

### Peringkat 1: Pengesanan (Pengesanan Sasaran)

** Apa yang berlaku: **

* Chloros mengimbas imej yang ditandai dengan kotak sasaran
* Algoritma penglihatan mesin mengenal pasti 4 panel penentukuran
* Nilai refleksi diekstrak dari setiap panel
*Timestamp sasaran direkodkan untuk menjadualkan penentukuran dengan betul

** Tempoh: **

* Dengan objektif yang ditandakan: 10-60 saat
*Tiada sasaran yang ditandakan: 5-30+ minit (imbas semua imej)

** Petunjuk Kemajuan: **

* Pengesanan: 0% → 100%.
* Bilangan imej yang diimbas.
* Mengira sasaran yang dijumpai.

** Apa yang perlu diperhatikan: **

*Harus diselesaikan dengan cepat jika objektif ditandakan dengan betul.
*Jika terlalu lama, sasaran mungkin tidak ditandakan.
* Semak log debug untuk mesej "Target Found".

### Peringkat 2: Analisis

** Apa yang berlaku: **

* Membaca metadata EXIF ​​dari imej (setem masa, tetapan pendedahan).
* Penentuan strategi penentukuran berdasarkan cap waktu sasaran.
* Organisasi giliran pemprosesan imej.
* Penyediaan pekerja pemprosesan selari (chloros+ sahaja).

** Tempoh: ** 5-30 saat.

** Petunjuk Kemajuan: **

* Analisis: 0% → 100%
* Peringkat cepat, biasanya selesai dengan cepat

** Apa yang perlu diperhatikan: **

* Mesti maju dengan mantap tanpa jeda
* Amaran mengenai Metadata Hilang akan muncul dalam log debug

### Peringkat 3: Penentukuran

** Apa yang berlaku: **

*** de-bayerization **: Penukaran corak mentah Bayer ke 3 saluran
*** Pembetulan Vignette **: Pembuangan kelebihan lensa gelap.
*** Penentukuran Refleksi **: Normalisasi dengan nilai sasaran.
*** Pengiraan Indeks **: Pengiraan indeks multispektral.
* Pemprosesan setiap imej sepanjang keseluruhan proses.

** Tempoh: ** Kebanyakan jumlah masa pemprosesan (60-80%).

** Petunjuk Kemajuan: **

* Penentukuran: 0% → 100%
* Imej semasa yang sedang berjalan
* Imej / jumlah gambar yang lengkap

** Tingkah laku pemprosesan: **

*** Mod Percuma **: Proses satu gambar pada satu masa secara berurutan
*** mod kloros+**: proses sehingga 16 imej secara serentak
*** Percepatan GPU **: Mempercepatkan tahap ini.

** Apa yang perlu diperhatikan: **

* Kemajuan berterusan melalui pengiraan imej.
* Semak log debug untuk mesej penyelesaian per-imej.
* Amaran mengenai kualiti imej atau isu penentukuran.

### Peringkat 4: Eksport

** Apa yang berlaku: **

* Tulis imej yang dikalibrasi ke cakera dalam format yang dipilih
* Imej indeks multispektral eksport dengan warna lut
* Buat subfolder model kamera
* Memelihara nama fail asal dengan akhiran yang sesuai

** Tempoh: ** 10-20% daripada jumlah masa pemprosesan

** Petunjuk Kemajuan: **

* Eksport: 0% → 100%
* Fail yang ditulis
* Format dan destinasi eksport

** Apa yang perlu diperhatikan: **

*Amaran ruang cakera
* Kesalahan penulisan fail
* Penyiapan semua output yang dikonfigurasikan

***

## Tab Log Debug

Log debug memberikan maklumat terperinci mengenai kemajuan pemprosesan dan sebarang masalah yang dihadapi.

### mengakses log debug

1. Klik Log Debug ** <img src = "../. Gitbook/Assets/icon_log.jpg" alt = "" data-size = "line"> ikon di bar sisi kiri
2. Panel log dibuka, memaparkan mesej pemprosesan masa nyata
3. Skrol secara automatik untuk menunjukkan mesej paling terkini

### Memahami mesej log

#### Mesej maklumat (putih/kelabu)

Kemas kini pemprosesan biasa:

___Code0001___

#### Mesej amaran (kuning)

Isu bukan kritikal yang tidak berhenti diproses:

___Code0002___

** Tindakan: ** Kajian amaran selepas diproses, tetapi jangan mengganggu pemprosesan.

#### Mesej ralat (rangkaian)

Isu kritikal yang boleh menyebabkan pemprosesan gagal:

___Code0003___

** Tindakan: ** Berhenti memproses, menyelesaikan ralat, dan mulakan semula.

### mesej log biasa

| Mesej | Makna | Tindakan diperlukan |
| -------------------------------- | -------------------------------------- | ----------------------------------------------------- |
| «Sasaran dikesan di \ [Filename]» | Sasaran penentukuran dijumpai dengan betul | Tiada - Normal |
| «Pemprosesan Imej X of Y» | Kemas kini Kemajuan Semasa | Tiada - Normal |
| «Tiada sasaran dijumpai» | Tiada sasaran penentukuran dikesan | Tandakan imej sasaran atau melumpuhkan penentukuran refleksi |
| "Ruang cakera yang tidak mencukupi" | Penyimpanan yang tidak mencukupi untuk output | Ruang cakera percuma |
| «Melangkau fail yang rosak» | Fail imej rosak | Salin fail kembali dari kad SD |
| «Data PPK Gunaan» | .daq fail GPS FIXES DAPATKAN | Tiada - Normal |

### Salin data log

Untuk menyalin log untuk menyelesaikan masalah atau tujuan sokongan teknikal:

1. Buka panel log debug.
2. Klik butang ** «Copy Log» ** (atau klik kanan → Pilih Semua).
3. Tampalkan kandungan ke dalam fail teks atau e -mel.
4. Hantar ke Sokongan Teknikal Mapir jika perlu.

***

## sumber sistem pemantauan

### Penggunaan CPU

** Mod Percuma: **

* 1 teras CPU pada ~ 100%
* Teras lain tidak aktif atau tersedia
* Sistem masih bertindak balas

** Kloros+ mod selari: **

* Multi-teras pada 80-100% (sehingga 16 teras)
* Penggunaan CPU keseluruhan yang tinggi
* Sistem mungkin kelihatan kurang responsif.

** untuk memantau: **

* Pengurus Tugas Windows (Ctrl+Shift+ESC)
* Tab Prestasi → Bahagian CPU
* Cari proses "kloros" atau "kloros-backend".

### penggunaan memori (RAM)

** Penggunaan biasa: **

* Projek Kecil (<100 imej): 2-4 GB
* Projek Sederhana (100-500 imej): 4-8 GB
* Projek Besar (500+ gambar): 8-16 GB
* Chloros+ mod selari menggunakan lebih banyak ram

** Sekiranya memori tidak mencukupi: **

* Proses kelompok yang lebih kecil.
* Tutup aplikasi lain.
*Meningkatkan RAM Jika anda kerap memproses set data yang besar.

### Penggunaan GPU (Chloros+ dengan CUDA)

Apabila pecutan GPU didayakan:

* NVIDIA GPU menunjukkan penggunaan yang tinggi (60-90%).
* Penggunaan VRAM meningkat (memerlukan 4GB+ VRAM).
* Tahap penentukuran jauh lebih cepat.

** untuk memantau: **

* Ikon dulang sistem nvidia.
* Pengurus Tugas → Prestasi → GPU.
* GPU-Z atau alat pemantauan yang serupa.

### Disk I/O.

** Apa yang diharapkan: **

* Cakera tinggi dibaca semasa fasa analisis.
* Cakera tinggi menulis semasa fasa eksport.
* SSD jauh lebih cepat daripada HDD.

** Petua Prestasi: **

* Gunakan SSD untuk folder projek apabila mungkin.
* Elakkan pemacu rangkaian untuk set data yang besar.
* Pastikan pemacu tidak dekat dengan kapasiti maksimumnya (mempengaruhi kelajuan menulis).

***

## mengesan masalah semasa pemprosesan

Tanda -tanda amaran ###

** Kemajuan berhenti (tiada perubahan selama lebih dari 5 minit): **

* Semak log debug untuk kesilapan.
* Semak ruang cakera yang tersedia.
* Periksa Pengurus Tugas untuk memastikan kloros berjalan.

** Mesej ralat sering muncul: **

* Berhenti memproses dan periksa kesilapan.
* Penyebab umum: Ruang cakera, fail yang rosak, masalah memori.
* Lihat bahagian penyelesaian masalah di bawah.

** Sistem berhenti bertindak balas: **

* Chloros+ dalam mod selari menggunakan terlalu banyak sumber.
* Pertimbangkan untuk mengurangkan tugas serentak atau menaik taraf perkakasan.
* Mod percuma menggunakan kurang sumber.

### Bilakah berhenti memproses

Berhenti memproses jika anda melihat:

* ❌ "Cakera penuh" atau "tidak boleh menulis fail" ralat.
* ❌ Ralat Rasuah Fail Imej Berulang.
* ❌ Sistem ini telah terhempas sepenuhnya (tidak bertindak balas).
* ❌ Ia telah dikesan bahawa tetapan yang salah telah dikonfigurasikan.
* ❌ Imej yang salah telah diimport.

** Cara menghentikannya: **

1. Klik butang Stop/Batal ** ** (menggantikan butang Mula).
2. Perhentian pemprosesan dan kemajuan hilang
3. Betulkan masalah dan mulakan semula dari awal

***

## menyelesaikan masalah semasa pemprosesan

### pemprosesan sangat perlahan

** Kemungkinan Punca: **

* Imej sasaran yang tidak terkawal (imbas semua imej)
* Penyimpanan HDD dan bukannya SSD
*Sumber sistem yang tidak mencukupi
* Banyak indeks yang dikonfigurasikan
* Akses pemacu rangkaian

** Penyelesaian: **

1. Jika anda baru sahaja bermula dan berada dalam fasa pengesanan: Batalkan, tandakan objektif dan mulakan semula.
2. Untuk masa depan: Gunakan SSD, mengurangkan indeks dan menaik taraf perkakasan.
3. Pertimbangkan menggunakan CLI untuk pemprosesan batch set data besar.

### "Ruang cakera" amaran

** Penyelesaian: **

1. Percuma ruang cakera segera.
2. Pindahkan projek ke pemanduan dengan lebih banyak ruang.
3. Kurangkan bilangan indeks untuk mengeksport.
4. Gunakan format JPG dan bukannya TIFF (fail yang lebih kecil).

### Mesej "fail yang rosak"

** Penyelesaian: **

1. Salin gambar sekali lagi dari kad SD untuk memastikan integriti mereka.
2. Semak kad SD untuk kesilapan.
3. Padam fail yang rosak dari projek.
4. Teruskan memproses imej yang tinggal.

### Sistem terlalu panas/kelembapan

** Penyelesaian: **

1. Pastikan pengudaraan yang mencukupi.
2. Debu bersih dari lubang komputer.
3. Mengurangkan beban pemprosesan (gunakan mod percuma dan bukannya chloros+).
4. Proses semasa waktu paling keren pada hari itu.

***

## Pemberitahuan pemprosesan selesai

Semasa pemprosesan selesai:

* Bar kemajuan mencapai 100%.
*** "Pemprosesan Selesai" ** Mesej muncul dalam log debug.
* Butang rumah diaktifkan lagi.
* Semua fail output terletak di subfolder model kamera.

***

## Langkah seterusnya

Setelah pemprosesan selesai:

1. ** Semak hasil **: lihat [menyelesaikan pemprosesan] (penamat-the-processing.md).
2. ** Semak Folder Output ** - Sahkan bahawa semua fail telah dieksport dengan jayanya.
3. ** Periksa log debug ** - periksa amaran atau kesilapan.
4. ** Pratonton Imej yang diproses **: Gunakan penonton imej atau perisian luaran.

Untuk maklumat tentang cara mengkaji dan menggunakan hasil yang diproses, lihat [menyelesaikan pemprosesan] (penamat-the-processing.md).