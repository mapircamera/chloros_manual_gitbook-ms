# Pemantauan Pemprosesan

Setelah pemprosesan bermula, Chloros

menyediakan beberapa cara untuk memantau kemajuan, memeriksa isu, dan memahami apa yang berlaku dengan set data anda. Halaman ini menerangkan cara menjejak pemprosesan anda dan mentafsir maklumat yang disediakan oleh Chloros

.

## Gambaran Keseluruhan Bar Kemajuan

Bar kemajuan di tajuk utama atas memaparkan status pemprosesan masa nyata dan peratusan penyiapan. Kemajuan disiarkan secara langsung dari backend melalui Server-Sent Events (SSE), jadi bar tersebut mencerminkan apa yang sebenarnya dilakukan oleh saluran pemprosesan.

### Bar Kemajuan Mod Bebas

Untuk pengguna tanpa lesen Chloros

+:

**Paparan Kemajuan 2-Tahap:**

1.**Pengesanan Sasaran** - Menemui sasaran penentukuran dalam imej
2. **Pemprosesan** - Mengaplikasikan pembetulan dan mengeksport**Bar kemajuan menunjukkan:**

* Peratusan penyiapan keseluruhan (0-100%)
* Nama peringkat semasa
* Visualisasi bar mendatar ringkas

### Bar Kemajuan Chloros

+

Untuk pengguna dengan lesen Chloros

+:

**Paparan Kemajuan 4-Peringkat:**

1.**Mengesani** - Mencari sasaran penentukuran
2. **Menganalisis** - Menyemak imej dan menyediakan saluran paip
3. **Kalibrasi** - Mengaplikasikan pembetulan vignet dan pantulan
4. **Eksport** - Menyimpan fail yang diproses**Ciri Interaktif:*** **Tudingkan tetikus** ke atas bar kemajuan untuk melihat panel 4-peringkat yang diperluas
* **Klik** bar kemajuan untuk membekukan/menetap panel yang diperluas
* **Klik sekali lagi** untuk membuka beku dan menyembunyikan secara automatik apabila tetikus dikeluarkan
* Setiap peringkat menunjukkan kemajuan individu (0-100%)

{% hint style="info" %}
**Pariti CLI**: semasa pelaksanaan `chloros-cli process`, empat thread yang sama melaporkan sebagai Mendedeksi, Menganalisis, Memproses, Mengeksport, dan `chloros-cli export-status` memaparkan kemajuan eksport Thread-4 secara langsung dari terminal lain. Rujuk [Rujukan [CLI

]](../reference/cli-reference.md).
{% endhint %}

***

## Memahami Setiap Tahap Pemprosesan

{% hint style="info" %}
**Senibina Saluran**: Ke-4 peringkat GUI ini sepadan dengan [saluran pemprosesan 4-benang](../processing-architecture/processing-pipeline.md). Pada sistem dengan pecutan GPU, Benang 3 (Kalibrasi) memanfaatkan [Penyesuaian Komputasi Dinamik](../processing-architecture/dynamic-compute-adaptation.md) yang mengoptimumkan pemprosesan untuk perkakasan khusus anda.
{% endhint %}

### Peringkat 1: Pengesanan (Pengesanan Sasaran)

**Apa yang berlaku:**

* Chloros

mengimbas imej yang anda semak dengan kotak semak Sasaran (semua imej hanya apabila tiada yang disemak)
* Algoritma penglihatan komputer mengenal pasti panel penentukuran
* Nilai pantulan diekstrak daripada setiap panel
* Cap masa sasaran direkodkan untuk penjadualan penentukuran yang betul

**Tempoh:**

* Dengan sasaran yang ditandakan: 10-60 saat
* Tanpa sasaran yang ditandakan: 5-30+ minit (menyerasah semua imej)

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
* Menyiapkan pekerja pemprosesan selari (Chloros

+ sahaja)

**Tempoh:** 5-30 saat**Penunjuk kemajuan:**

* Menganalisis: 0% → 100%
* Fasa pantas, biasanya selesai dengan cepat

**Apa yang perlu diperhatikan:**

* Seharusnya berjalan lancar tanpa henti
* Amaran tentang metadata yang hilang akan muncul dalam Log Ralat

### Fasa 3: Kalibrasi

**Apa yang berlaku:*** **Debayering**: Menukar corak Bayer RAW kepada 3 saluran (dilangkau untuk modul mono LATTICE, dengan nota)
* **Pembetulan Vignette**: Menghilangkan penggelapan pada tepi lensa
* **Kalibrasi Reflektan**: Menormalkan dengan nilai sasaran dan/atau DAQ downwelling
* **Pengiraan Indeks**: Mengira indeks multispektral
* Memproses setiap imej melalui keseluruhan pipeline

**Tempoh:** Kebanyakan daripada jumlah masa pemprosesan (60-80%)**Petunjuk kemajuan:**

* Kalibrasi: 0% → 100%
* Imej semasa sedang diproses
* Imej siap / Jumlah imej

**Tingkah laku pemprosesan:*** **Mod Bebas**: Memproses satu imej pada satu masa secara bersiri
* **Mod Chloros

+**: Menggunakan kolam pekerja yang menyesuaikan perkakasan — 1-4 pekerja serentak pada sistem GPU (bergantung pada VRAM), seorang pekerja bagi setiap teras fizikal (dikurangkan satu) pada sistem CPU sahaja. Lihat [Penyesuaian Komputasi Dinamik](../processing-architecture/dynamic-compute-adaptation.md)
* **Pecutan GPU**: Mempercepat peringkat ini dengan ketara**Apa yang perlu diperhatikan:**

* Kemajuan yang mantap melalui kiraan imej
* Semak Log Ralat untuk mesej penyempurnaan bagi setiap imej
* Amaran tentang kualiti imej atau isu penentukuran

### Fasa 4: Eksport

**Apa yang berlaku:**

* Menulis imej yang diproses ke cakera dalam format yang dipilih, sebaik sahaja ia selesai
* **LATTICE**: setiap bingkai disalurkan ke setiap produk yang diaktifkan (debayered / pratonton / sinaran / pantulan)
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

1. Klik ikon **Log Ralat** <img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line">

di bar sisi kiri
2. Panel log dibuka memaparkan mesej pemprosesan masa nyata
3. Menggelangsar secara automatik untuk memaparkan mesej terkini

<!-- SCREENSHOT-NEEDED: Debug Log tab open at the end of a completed run, showing real backend log lines including the [RUN-SUMMARY] lines (images / camera groups / targets / calibrated / files written), with the 1.3.0 bug icon (Report a bug) visible at the top of the tab -->

### Memahami Mesej Log  Baris log

Chloros

diawali dengan tag dalam kurungan yang menamakan subsistem — contohnya `[PROCESSING]`, `[RUN-SUMMARY]`, `[LATTICE-EXPORT]`, `[EXPORT-CHECK]`, `[IMPORT-LEVEL]`. Yang paling penting untuk diketahui ialah **ringkasan larian**, dicetak di akhir setiap larian (termasuk larian yang dihentikan):

```
[RUN-SUMMARY] 49 image(s) in 2 camera group(s); 4 target(s) detected; 45 image(s) calibrated; 180 file(s) written.
```

Baris petunjuk tambahan `[RUN-SUMMARY]` akan muncul apabila sesuatu perlu dijelaskan — contohnya larian yang tidak menghasilkan apa-apa, atau kamera yang produk yang diminta diabaikan kerana tidak terpakai. Baris `[EXPORT-CHECK]` menerangkan tentang pengabaian bagi setiap kamera (contohnya mengapa kamera RGB

tidak menerima sebarang produk sinaran).

Keterukan mesej umum (contoh di bawah adalah untuk ilustrasi, bukan secara harfiah):

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
| Sasaran dikesan dalam \[nama_fail]        | Sasaran penentukuran berjaya dikesan         | Tiada - normal                                         |
| Baris kemajuan setiap imej              | Kemas kini kemajuan semasa                       | Tiada - normal                                         |
| Tiada sasaran ditemui                      | Tiada sasaran penentukuran dikesan               | Tanda imej sasaran atau nyahdayakan penentukuran pantulan |
| Ruang cakera tidak mencukupi               | Penyimpanan tidak mencukupi untuk keluaran                | Bebaskan ruang cakera                                    |
| Melangkau fail yang rosak               | Fail imej rosak                                 | Salin semula fail dari kad SD                             |
| `[IMPORT-LEVEL] Skipping ... no raw source` | Rakaman tanpa bingkai mentah tidak dapat diproses | Rakam semula dengan raw, atau gunakan CLI

`--input-level`  |
| `[RUN-SUMMARY] ... 0 file(s) written` | Jalankan tidak menghasilkan sebarang produk imej — dilaporkan sebagai kegagalan dengan petunjuk | Baca baris petunjuk; semak apa yang dilangkau dan mengapa |
| `[PROCESS] refused: The project folder is too deep for this computer…` | Laluan panjang Windows

dimatikan dan folder projek terlalu dalam untuk had laluan 260 aksara; tiada apa yang diproses | Gunakan folder projek yang lebih pendek (mesej ini memberikan panjang maksimum yang sesuai), atau aktifkan laluan panjang Windows

dan mulakan semula Chloros

— lihat [Memulakan Pemprosesan](starting-the-processing.md#the-project-folder-is-too-deep-for-this-computer) |
| `[unmix-clamp-mask] …` | Topeng clamp produk LATTICE tidak dapat ditulis; produk itu sendiri baik-baik sahaja dan memaparkan `Chloros:UnmixClampMask=unavailable` | Semak ruang cakera dan kebenaran folder, kemudian proses semula jika anda memerlukan topeng |

### Menyalin Data Log

Untuk menyalin log bagi penyelesaian masalah atau sokongan:

1. Buka panel Log Ralat
2. Klik butang **Salin**(menyalin log ke papan klip), atau**Muat turun** untuk menyimpannya sebagai fail teks
3. Tampal ke dalam fail teks atau e-mel
4. Hantar ke sokongan MAPIR

jika perlu

### Melaporkan pepijat daripada tab log

Baharu dalam 1.3.0: ikon pepijat (**Laporkan pepijat**) di bahagian atas tab Log Ralat menghantar laporan terus ke MAPIR

.

1. Klik ikon pepijat di bahagian atas tab Log Ralat
2. Huraikan isu tersebut: apa yang anda lakukan, apa yang anda jangkakan, dan apa yang berlaku sebaliknya (diperlukan)
3. Secara pilihan, tandakan **Tambah beberapa imej (sehingga 10)**dan pilih imej daripada projek yang dibuka, atau gunakan**Telusuri...** untuk mana-mana fail imej lain
4. Klik **Hantar**

Laporan tersebut merangkumi keterangan anda, sebarang imej yang anda lampirkan, log Chloros

, dan butiran sistem (versi Chloros

, OS, GPU, dan tetapan projek). Anda mesti log masuk ke Chloros

(tab Pengguna) untuk menghantarnya. Anda boleh menutup tetingkap semasa ia dihantar; apabila ia selesai, rujukan seperti `Bug report sent. Reference: BR-…` akan ditulis ke dalam log — sebutkan ia apabila anda menghubungi MAPIR

. Jika laporan tidak dapat dimuat naik (contohnya, tiada sambungan internet), Chloros

menyimpannya di komputer anda dan memaparkan laluan supaya anda boleh menghantarnya melalui e-mel ke support@mapir.camera.

***

## Pemantauan Sumber Sistem

### Penggunaan CPU

**Mod Percuma:**

* 1 teras CPU pada \~100%
* Teras lain tidak aktif atau tersedia
* Sistem kekal responsif

**Chloros

+ Mod Selari:**

* Pelbagai teras pada penggunaan tinggi — bergantung pada strategi yang dipilih oleh [Penyesuaian Komputasi Dinamik](../processing-architecture/dynamic-compute-adaptation.md)
* Sistem mungkin terasa kurang responsif

**Untuk memantau:**

* Pengurus Tugas Windows

(Ctrl+Shift+Esc)
* Tab Prestasi → bahagian CPU
* Cari proses &quot;Chloros

&quot; atau &quot;chloros-backend&quot;

### Penggunaan Memori (RAM)

**Penggunaan biasa:**

* Projek kecil (&lt; 100 imej): 2-4 GB
* Projek sederhana (100-500 imej): 4-8 GB
* Projek besar (500+ imej): 8-16 GB
* Mod selari Chloros

+ menggunakan lebih banyak RAM

**Jika memori rendah:**

* Proses kumpulan yang lebih kecil
* Tutup aplikasi lain
* Tingkatkan RAM jika kerap memproses set data yang besar

### Penggunaan GPU (Chloros

+ dengan CUDA)

Apabila pecutan GPU diaktifkan:

* GPU NVIDIA menunjukkan penggunaan yang tinggi (60-90%)
* Penggunaan VRAM meningkat (memerlukan 4GB+ VRAM; 7GB+ untuk debayering Texture Aware serentak)
* Fasa penentukuran menjadi lebih pantas dengan ketara

**Untuk memantau:**

* Ikon NVIDIA di System Tray
* Task Manager → Performance → GPU
* GPU-Z atau alat pemantauan yang serupa

### I/O Cakera

**Apa yang boleh dijangkakan:**

* Baca cakera yang tinggi semasa peringkat Menganalisis
* Tulis cakera yang tinggi semasa peringkat Mengeksport
* SSD jauh lebih pantas daripada HDD

**Petua prestasi:**

* Gunakan SSD untuk folder projek apabila boleh
* Elakkan pemacu rangkaian untuk set data besar
* Pastikan cakera tidak hampir penuh (menjejaskan kelajuan penulisan)

***

## Mengesan Masalah Semasa Pemprosesan

### Tanda Amaran

**Kemajuan terhenti (tiada perubahan selama 5+ minit):**

* Semak Log Ralat untuk ralat
* Semak ruang cakera yang tersedia
* Semak Pengurus Tugas untuk memastikan Chloros

sedang berjalan

**Mesej ralat muncul dengan kerap:**

* Hentikan pemprosesan dan semak semula ralat
* Punca biasa: ruang cakera, fail rosak, masalah memori
* Lihat bahagian Penyelesaian Masalah di bawah

**Sistem menjadi tidak responsif:**

* Chloros

+ mod selari menggunakan terlalu banyak sumber
* Pertimbangkan untuk mengurangkan tugas serentak atau menaik taraf perkakasan
* Mod bebas menggunakan sumber yang kurang

### Bila Perlu Hentikan Pemprosesan

Hentikan pemprosesan jika anda melihat:

* ❌ Ralat &quot;Cakera penuh&quot; atau &quot;Tidak dapat menulis fail&quot;
* ❌ Ralat kerosakan fail imej berulang
* ❌ Sistem beku sepenuhnya (tidak bertindak balas)
* ❌ Sedar bahawa tetapan yang salah telah dikonfigurasikan
* ❌ Imej yang salah diimport

**Cara menghentikan:**

1. Klik**butang Henti** (menggantikan butang Mula) — sekali sudah memadai
2. Bar akan memaparkan &quot;Berhenti...&quot; sementara imej yang sedang diproses selesai, kemudian pelaksanaan akan berakhir dalam keadaan berhenti
3. Produk yang telah dieksport kekal pada cakera; log mencetak laporan `[RUN-SUMMARY]` yang jujur tentang apa yang telah diselesaikan
4. Betulkan isu dan mulakan semula — proses akan bermula dari awal

***

## Penyelesaian Masalah Semasa Pemprosesan

### Pemprosesan Sangat Perlahan

**Punca yang mungkin:**

* Imej sasaran tidak ditandakan (mengimbas semua imej)
* Penyimpanan HDD bukannya SSD
* Sumber sistem tidak mencukupi
* Banyak indeks dikonfigurasikan
* Akses pemacu rangkaian

**Penyelesaian:**

1. Jika baru bermula dan berada di peringkat Mengesan: Hentikan, tandakan sasaran, mulakan semula
2. Untuk masa hadapan: Gunakan SSD, kurangkan indeks, naik taraf perkakasan
3. Pertimbangkan CLI

untuk pemprosesan pukal set data besar

### Amaran &quot;Ruang Cakera&quot;

**Penyelesaian:**

1. Luangkan ruang cakera dengan segera
2. Pindahkan projek ke pemacu dengan ruang yang lebih besar
3. Kurangkan bilangan indeks untuk dieksport
4. Nyahdayakan produk eksport LATTICE yang anda tidak perlukan (Tetapan Projek → Pemprosesan)
5. Gunakan format JPG bukannya TIFF

(fail yang lebih kecil)

### Pesan &quot;Fail Rosak&quot; yang Kerap Muncul

**Penyelesaian:**

1. Salin semula imej dari kad SD untuk memastikan integriti
2. Uji kad SD untuk ralat
3. Buang fail yang rosak daripada projek
4. Sambung memproses imej yang tinggal

### Sistem Terlalu Panas / Penghadan

**Penyelesaian:**

1. Pastikan pengudaraan yang mencukupi
2. Bersihkan habuk dari lubang pengudaraan komputer
3. Kurangkan beban pemprosesan (gunakan Mod Percuma bukannya Chloros

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
2. **Semak folder output** - Sahkan semua fail dieksport dengan betul
3. **Semak Log Ralat (Debug Log)** - Periksa sebarang amaran atau ralat
4. **Praperiksa imej yang diproses** - Gunakan Pemapar Imej (Image Viewer) atau perisian luaran

Untuk maklumat tentang menyemak dan menggunakan keputusan yang diproses, lihat [Menyiapkan Pemprosesan](finishing-the-processing.md).
