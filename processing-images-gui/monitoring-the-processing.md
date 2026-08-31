# Memantau Pemprosesan

Setelah pemprosesan bermula,Chloros

menyediakan beberapa cara untuk memantau kemajuan, menyemak isu, dan memahami apa yang berlaku dengan set data anda. Halaman ini menerangkan cara menjejaki pemprosesan anda dan mentafsir maklumat yang disediakan olehChloros

.

## Gambaran Keseluruhan Bar Kemajuan

Bar kemajuan di tajuk utama bahagian atas memaparkan status pemprosesan masa nyata dan peratusan penyiapan. Kemajuan disiarkan secara langsung dari backend melalui Acara Hantar Pelayan (SSE), jadi bar tersebut mencerminkan apa yang sebenarnya dilakukan oleh saluran pemprosesan.

### Bar Kemajuan Mod Percuma

Untuk pengguna tanpa lesenChloros

+:

**Paparan Kemajuan 2-Tahap:**

1.**Pengesanan Sasaran** - Menemukan sasaran penentukuran dalam imej
2. **Pemprosesan** - Mengaplikasikan pembetulan dan mengeksport**Bar kemajuan menunjukkan:**

* Peratusan penyiapan keseluruhan (0-100%)
* Nama peringkat semasa
* Visualisasi bar mendatar ringkas

### Bar KemajuanChloros

+

Untuk pengguna dengan lesenChloros

+:

**Paparan Kemajuan 4-Peringkat:**

1.**Mengesani** - Mencari sasaran penentukuran
2. **Menganalisis** - Menyemak imej dan menyediakan saluran
3. **Menentukurkan** - Mengaplikasikan pembetulan viniet dan pantulan
4. **Mengeksport** - Menyimpan fail yang diproses**Ciri Interaktif:*** **Tudingkan tetikus** ke atas bar kemajuan untuk melihat panel 4-peringkat yang diperluas
* **Klik** bar kemajuan untuk membekukan/menetap panel yang diperluas
* **Klik sekali lagi** untuk membuka beku dan menyembunyikan secara automatik apabila tetikus dikeluarkan
* Setiap peringkat menunjukkan kemajuan individu (0-100%)

{% hint style="info" %}
**ParitiCLI**: semasa pelaksanaan `chloros-cli process`, empat thread yang sama melaporkan sebagai Mengesan, Menganalisis, Pemprosesan, Pengeksportan, dan `chloros-cli export-status` memaparkan kemajuan eksport Thread-4 secara langsung daripada terminal lain. Lihat [RujukanCLI

](../reference/cli-reference.md).
{% endhint %}

***

## Memahami Setiap Tahap Pemprosesan

{% hint style="info" %}
**Senibina Saluran**: 4 peringkat GUI ini sepadan dengan [saluran pemprosesan 4-benang](../processing-architecture/processing-pipeline.md). Pada sistem dengan pecutan GPU, Thread 3 (Kalibrasi) mendapat manfaat daripada [Penyesuaian Komputasi Dinamik](../processing-architecture/dynamic-compute-adaptation.md) yang mengoptimumkan pemprosesan untuk perkakasan khusus anda.
{% endhint %}

### Fasa 1: Pengesanan (Pengesanan Sasaran)

**Apa yang berlaku:**

*Chloros

mengimbas imej yang anda semak dengan kotak semak Sasaran (semua imej hanya apabila tiada yang disemak)
* Algoritma penglihatan komputer mengenal pasti panel penentukuran
* Nilai pantulan diekstrak daripada setiap panel
* Cap masa sasaran direkodkan untuk penjadualan penentukuran yang betul

**Tempoh:**

* Dengan sasaran yang ditandakan: 10-60 saat
* Tanpa sasaran yang ditandakan: 5-30+ minit (mengimbas semua imej)

**Penunjuk kemajuan:**

* Mengesan: 0% → 100%
* Bilangan imej yang diimbas (mengira hanya imej yang sebenarnya diimbas)
* Bilangan sasaran yang ditemui

**Apa yang perlu diperhatikan:**

* Seharusnya selesai dengan cepat jika sasaran ditandakan dengan betul
* Jika mengambil masa terlalu lama, mungkin sasaran tidak ditandakan
* Semak Log Ralat untuk mesej &quot;Sasaran ditemui&quot;

### Fasa 2: Menganalisis

**Apa yang berlaku:**

* Membaca metadata EXIF imej (cap masa, tetapan pendedahan)
* Menentukan strategi penentukuran berdasarkan cap masa sasaran dan data DAQ downwelling yang tersedia
* Mengatur barisan pemprosesan imej
* Menyediakan pekerja pemprosesan selari (Chloros

+ sahaja)

**Tempoh:** 5-30 saat**Penunjuk kemajuan:**

* Menganalisis: 0% → 100%
* Fasa pantas, biasanya selesai dengan cepat

**Apa yang perlu diperhatikan:**

* Seharusnya berkemajuan dengan mantap tanpa henti
* Amaran tentang metadata yang hilang akan muncul dalam Log Ralat

### Fasa 3: Kalibrasi

**Apa yang berlaku:*** **Debayering**: Menukar corak Bayer RAW kepada 3 saluran (dilangkau untuk modul mono LATTICE, dengan nota)
* **Pembetulan Vignette**: Menghilangkan penggelapan di tepi lensa
* **Kalibrasi reflektansi**: Menormalisasi dengan nilai sasaran dan/atau DAQ downwelling
* **Pengiraan indeks**: Mengira indeks multispektral
* Memproses setiap imej melalui keseluruhan saluran

**Tempoh:** Kebanyakan daripada jumlah masa pemprosesan (60-80%)**Penunjuk kemajuan:**

* Kalibrasi: 0% → 100%
* Imej semasa diproses
* Imej siap / Jumlah imej

**Tingkah laku pemprosesan:*** **Mod bebas**: Memproses satu imej pada satu masa secara bersiri
* **ModChloros

+**: Menggunakan kolam pekerja yang menyesuaikan mengikut perkakasan — 1-4 pekerja serentak pada sistem GPU (mengikut VRAM), satu pekerja bagi setiap teras fizikal (dikurangkan satu) pada sistem CPU sahaja. Lihat [Penyesuaian Komputasi Dinamik](../processing-architecture/dynamic-compute-adaptation.md)
* **Pecutan GPU**: Mempercepat peringkat ini dengan ketara**Apa yang perlu diperhatikan:**

* Kemajuan yang sekata melalui kiraan imej
* Semak Log Ralat untuk mesej penyempurnaan bagi setiap imej
* Amaran tentang kualiti imej atau isu penentukuran

### Fasa 4: Eksport

**Apa yang berlaku:**

* Menulis imej yang diproses ke cakera dalam format yang dipilih, sebaik sahaja ia siap
* **LATTICE**: setiap bingkai disalurkan ke setiap produk yang diaktifkan (debayered / pratonton / radiance / pantulan)
* Mengeksport imej indeks multispektral dengan warna LUT
* Mencipta pokok keluaran `<project>/<camera>/<format>/<Product>_Images/` — fail yang dieksport mengekalkan nama fail sumber; folder mengenal pasti produk

**Tempoh:** 10-20% daripada jumlah masa pemprosesan**Penunjuk kemajuan:**

* Pengeksportan: 0% → 100%
* Fail sedang ditulis
* Format dan destinasi eksport

**Apa yang perlu diperhatikan:**

* Amaran ruang cakera
* Ralat penulisan fail
* Penyempurnaan semua keluaran yang dikonfigurasikan

***

## Tab Log Ralat

Log Ralat menyediakan maklumat terperinci tentang kemajuan pemprosesan dan sebarang isu yang dihadapi. Mesej permulaan backend juga dimainkan semula ke dalam konsol log, jadi log tersebut menceritakan keseluruhan cerita walaupun anda membukanya lewat.

### Mengakses Log Ralat

1. Klik ikon **Debug Log**<img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line">

di bar sisi kiri
2. Panel log dibuka, memaparkan mesej pemprosesan masa nyata
3. Menatal secara automatik untuk memaparkan mesej terkini

<!-- SCREENSHOT-NEEDED: Debug Log tab open at the end of a completed run, showing real backend log lines including the [RUN-SUMMARY] lines (images / camera groups / targets / calibrated / files written) -->

### Memahami Mesej Log  Baris log

Chloros

diawali dengan tag bersarang yang menamakan subsistem — contohnya `[PROCESSING]`, `[RUN-SUMMARY]`, `[LATTICE-EXPORT]`, `[EXPORT-CHECK]`, `[IMPORT-LEVEL]`. Yang paling penting untuk diketahui ialah **ringkasan larian**, dicetak di akhir setiap larian (termasuk larian yang dihentikan):

```
[RUN-SUMMARY] 49 image(s) in 2 camera group(s); 4 target(s) detected; 45 image(s) calibrated; 180 file(s) written.
```

Baris petunjuk tambahan `[RUN-SUMMARY]` akan muncul apabila sesuatu perlu dijelaskan — contohnya larian yang tidak menghasilkan apa-apa, atau kamera yang produk yang diminta diabaikan kerana tidak terpakai. Baris `[EXPORT-CHECK]` menerangkan tentang pengabaian bagi setiap kamera (contohnya mengapa kameraRGB

tidak menerima produk sinaran).

Keparahan mesej umum (contoh di bawah adalah untuk ilustrasi, bukan secara harfiah):

#### Mesej Maklumat (Putih/Kelabu)

Kemas kini pemprosesan biasa: pemprosesan bermula, sasaran dikesan (dengan kiraan panel), kemajuan penentukuran setiap imej, fail dieksport, pemprosesan selesai.

#### Mesej Amaran (Kuning)

Isu tidak kritikal yang tidak menghentikan pemprosesan — contohnya data GPS hilang dalam satu bingkai, jurang cap masa yang besar antara imej sasaran, atau kontras rendah dalam panel penentukuran.

**Tindakan:** Semak amaran selepas pemprosesan, tetapi jangan mengganggu

#### Mesej Ralat (Red

)

Isu kritikal yang mungkin menyebabkan pemprosesan gagal — contohnya cakera penuh, fail imej rosak, atau tiada sasaran dikesan semasa penentukuran pantulan diminta.

**Tindakan:** Hentikan pemprosesan, selesaikan ralat, mulakan semula

### Situasi Log Biasa

| Situasi                             | Maksud                                       | Tindakan yang Perlu Diambil                                         |
| ------------------------------------- | --------------------------------------------- | ----------------------------------------------------- |
| Sasaran dikesan dalam \[nama_fail]        | Sasaran penentukuran berjaya ditemui         | Tiada - normal                                         |
| Baris kemajuan setiap imej              | Kemas kini kemajuan semasa                      | Tiada - normal                                         |
| Tiada sasaran ditemui                      | Tiada sasaran penentukuran dikesan               | Tanda imej sasaran atau nyahdayakan penentukuran pantulan |
| Ruang cakera tidak mencukupi               | Penyimpanan tidak mencukupi untuk keluaran                 | Bebaskan ruang cakera                                    |
| Melangkau fail rosak               | Fail imej rosak                         | Salin semula fail dari kad SD                             |
| `[IMPORT-LEVEL] Skipping ... no raw source` | Rakaman tanpa bingkai mentah tidak dapat diproses | Rakam semula dengan mentah, atau gunakanCLI

`--input-level`  |
| `[RUN-SUMMARY] ... 0 file(s) written` | Jalankan tidak menghasilkan produk imej — dilaporkan sebagai kegagalan dengan petunjuk | Baca baris petunjuk; semak apa yang dilangkau dan mengapa |

### Menyalin Data Log

Untuk menyalin log bagi penyelesaian masalah atau sokongan:

1. Buka panel Log Ralat
2. Klik butang **&quot;Salin Log&quot;** (atau klik kanan → Pilih Semua)
3. Tampal ke dalam fail teks atau e-mel
4. Hantar ke sokonganMAPIR

jika perlu

***

## Pemantauan Sumber Sistem

### Penggunaan CPU

**Mod Bebas:**

* 1 teras CPU pada \~100%
* Teras lain tidak aktif atau tersedia
* Sistem kekal responsif

**Chloros

+ Mod Paralel:**

* Pelbagai teras pada penggunaan tinggi — berapa banyak bergantung pada strategi yang dipilih oleh [Dynamic Compute Adaptation](../processing-architecture/dynamic-compute-adaptation.md)
* Sistem mungkin terasa kurang responsif

**Untuk memantau:**

*Windows

Task Manager (Ctrl+Shift+Esc)
* Tab Prestasi → bahagian CPU
* Cari proses &quot;Chloros

&quot; atau &quot;chloros-backend&quot;

### Penggunaan Memori (RAM)

**Penggunaan biasa:**

* Projek kecil (&lt; 100 imej): 2-4 GB
* Projek sederhana (100-500 imej): 4-8 GB
* Projek besar (500+ imej): 8-16 GB
*Chloros

+ mod selari menggunakan lebih banyak RAM

**Jika memori rendah:**

* Proses kumpulan yang lebih kecil
* Tutup aplikasi lain
* Tingkatkan RAM jika kerap memproses set data besar

### Penggunaan GPU (Chloros

+ dengan CUDA)

Apabila pecutan GPU diaktifkan:

* GPU NVIDIA menunjukkan penggunaan yang tinggi (60-90%)
* Penggunaan VRAM meningkat (memerlukan 4GB+ VRAM; 7GB+ untuk debayering Texture Aware serentak)
* Tahap penentukuran adalah jauh lebih pantas

**Untuk memantau:**

* Ikon NVIDIA di System Tray
* Task Manager → Performance → GPU
* GPU-Z atau alat pemantauan serupa

### I/O Cakera

**Apa yang boleh dijangkakan:**

* Bacaan cakera yang tinggi semasa peringkat Menganalisis
* Penulisan cakera yang tinggi semasa peringkat Mengeksport
* SSD jauh lebih pantas daripada HDD

**Petua prestasi:**

* Gunakan SSD untuk folder projek apabila boleh
* Elakkan pemacu rangkaian untuk set data besar
* Pastikan cakera tidak hampir penuh (menjejaskan kelajuan menulis)

***

## Mengesan Masalah Semasa Pemprosesan

### Tanda Amaran

**Kemajuan terhenti (tiada perubahan selama 5+ minit):**

* Semak Log Ralat untuk ralat
* Semak ruang cakera yang tersedia
* Semak Pengurus Tugas (Task Manager) untuk memastikanChloros

sedang berjalan

**Mesej ralat muncul dengan kerap:**

* Hentikan pemprosesan dan semak semula ralat
* Punca biasa: ruang cakera, fail rosak, masalah memori
* Lihat bahagian Penyelesaian Masalah di bawah

**Sistem menjadi tidak responsif:**

*Chloros

+ mod selari menggunakan terlalu banyak sumber
* Pertimbangkan untuk mengurangkan tugas serentak atau menaik taraf perkakasan
* Mod percuma menggunakan sumber yang kurang

### Bila hendak Hentikan Pemprosesan

Hentikan pemprosesan jika anda melihat:

* ❌ Ralat &quot;Cakera penuh&quot; atau &quot;Tidak dapat menulis fail&quot;
* ❌ Ralat kerosakan fail imej berulang
* ❌ Sistem beku sepenuhnya (tidak bertindak balas)
* ❌ Sedar bahawa tetapan yang salah telah dikonfigurasikan
* ❌ Imej yang salah diimport

**Cara menghentikan:**

1. Klik butang**Hentikan** (menggantikan butang Mula) — sekali sudah memadai
2. Bar akan memaparkan &quot;Berhenti...&quot; sementara imej yang sedang diproses selesai, kemudian pelaksanaan akan berakhir dalam keadaan berhenti
3. Produk yang telah dieksport akan kekal pada cakera; log akan mencetak laporan `[RUN-SUMMARY]` yang jujur tentang apa yang telah diselesaikan
4. Betulkan isu dan mulakan semula — pelaksanaan akan bermula dari awal

***

## Penyelesaian Masalah Semasa Pemprosesan

### Pemprosesan Sangat Lambat

**Punca yang mungkin:**

* Imej sasaran tidak ditandakan (mengimbas semua imej)
* Penyimpanan HDD bukannya SSD
* Sumber sistem tidak mencukupi
* Banyak indeks dikonfigurasikan
* Akses pemacu rangkaian

**Penyelesaian:**

1. Jika baru bermula dan berada dalam peringkat Mengesan: Hentikan, tandakan sasaran, mulakan semula
2. Untuk masa hadapan: Gunakan SSD, kurangkan indeks, naik taraf perkakasan
3. PertimbangkanCLI

untuk pemprosesan pukal set data besar

### Amaran &quot;Ruang Cakera&quot;

**Penyelesaian:**

1. Bebaskan ruang cakera dengan segera
2. Pindahkan projek ke pemacu dengan ruang yang lebih besar
3. Kurangkan bilangan indeks untuk dieksport
4. Lumpuhkan produk eksport LATTICE yang anda tidak perlukan (Penyediaan Projek → Pemprosesan)
5. Gunakan format JPG sebaliknyaTIFF

(fail yang lebih kecil)

### Mesej &quot;Fail Rosak&quot; Berulang-ulang

**Penyelesaian:**

1. Salin semula imej dari kad SD untuk memastikan integriti
2. Uji kad SD untuk ralat
3. Buang fail yang rosak daripada projek
4. Teruskan pemprosesan imej yang tinggal

### Sistem Terlalu Panas / Pelambatan

**Penyelesaian:**

1. Pastikan pengudaraan yang mencukupi
2. Bersihkan habuk dari lubang pengudaraan komputer
3. Kurangkan beban pemprosesan (gunakan Mod Percuma bukannyaChloros

+)
4. Proses semasa waktu yang lebih sejuk dalam sehari

***

## Notifikasi Pemprosesan Siap

Apabila pemprosesan selesai:

* Bar kemajuan mencapai 100%
* Baris `[RUN-SUMMARY]` muncul dalam Log Ralat dengan kiraan akhir
* Butang Mula diaktifkan semula
* Semua fail keluaran berada di struktur output per-kamera projek: `<project>/<camera>/<format>/<Product>_Images/`

***

## Langkah Seterusnya

Setelah pemprosesan selesai:

1. **Semak keputusan** - Lihat [Menyiapkan Pemprosesan](finishing-the-processing.md)
2. **Semak folder keluaran** - Sahkan semua fail dieksport dengan betul
3. **Semak Log Ralat** - Periksa sebarang amaran atau ralat
4. **Praperiksa imej yang diproses** - Gunakan Pemapar Imej atau perisian luaran

Untuk maklumat tentang menyemak dan menggunakan keputusan yang diproses, lihat [Menyiapkan Pemprosesan](finishing-the-processing.md).
