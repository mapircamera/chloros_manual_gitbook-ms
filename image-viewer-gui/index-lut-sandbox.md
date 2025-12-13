# Index/Lut Sandbox

Kotak pasir Indeks/LUT adalah ruang kerja interaktif dalam penonton imej kloros yang membolehkan anda bereksperimen dengan pengiraan indeks multispektral dan visualisasi warna masa nyata. Alat yang berkuasa ini membantu anda menguji indeks yang berbeza, memperbaiki julat nilai, dan membuat visualisasi yang sedia ada penerbitan tanpa perlu memproses semula keseluruhan set data anda.

## Apakah kotak pasir indeks/lut?

### tujuan

Tawaran Kotak pasir:

*** Pengiraan Indeks Masa Nyata **: Sapukan sebarang indeks tumbuh-tumbuhan dengan serta-merta.
*** Pelarasan LUT Interaktif **: Kecerunan halus dan julat warna.
*** Pengoptimuman aliran kerja **: Tentukan tetapan terbaik sebelum pemprosesan batch.

### kotak pasir vs pemprosesan projek

** Indeks/Lut Sandbox (Interaktif): **

* Gambar tunggal pada satu masa
* Respons segera
* Eksperimen dan berulang
* Tiada perubahan kekal pada fail
* Sesuai untuk meneroka dan menguji

** Pemprosesan Projek (Batch): **

* Keseluruhan data yang ditetapkan sekaligus.
* Tetapan yang telah dikonfigurasikan.
* Fail output kekal.
* Memerlukan banyak masa.
* Ideal apabila tetapan dimuktamadkan.

{% petunjuk gaya = & quot; kejayaan & quot; %}
** Aliran kerja yang lebih baik **: Gunakan kotak pasir untuk mencuba dan cari indeks optimum dan tetapan LUT, dan kemudian gunakan tetapan tersebut semasa pemprosesan projek untuk keseluruhan set data.
{ % endhint %}

***

## Bekerja dengan kotak pasir indeks/lut

### Memahami indeks yang dipertikaikan

Di kloros, indeks boleh digunakan semasa pemprosesan projek. Untuk menentukan tetapan indeks dan LUT yang anda mahu memohon kepada eksport anda, paling mudah untuk menggunakan kotak pasir penonton imej.

Kotak pasir membolehkan anda:

*** Memohon indeks baru dan kecerunan warna (LUT) ** untuk memvisualisasikan data.
*** Laraskan Tetapan Paparan ** Interaksi.
*** Lihat ** Imej indeks yang telah dikira.
*** Memeriksa ** Nilai piksel di semua tahap zum.

### Buka persekitaran ujian

Kotak pasir indeks/lut diakses pada ** Image Viewer ** <img src = "../. Gitbook/aset/icon_image-viewer.jpg" alt = "" data-size = "line"> tab:

1. Klik pada imej dalam grid imej Fail Explorer dan ia akan dibuka di ** Image Viewer ** <img src = "../. Gitbook/aset/icon_image-viewer.jpg" alt = "" data-size = "line"> tab.
2. Klik ** Image Viewer ** <img src = "../. Gitbook/Aset/icon_image-viewer.jpg" alt = "" data-size = "line"> untuk membuka bar sisi pop timbul di sebelah kiri jika belum dibuka

### Pilih imej untuk memohon indeks/lut ke

Untuk bekerja dengan indeks dalam kotak pasir penonton imej <img src = "../. Gitbook/aset/icon_image-viewer.jpg" alt = "" data-size = "line">:

1. ** Buka gambar ** dari grid imej utama dengan mengklik padanya
2. ** Image Viewer ** <img src = "../. Gitbook/aset/icon_image-viewer.jpg" alt = "" tab data = "line"> akan dibuka.
3. Klik menu drop-down ** Layer ** (kanan atas penonton).
4. Pilih lapisan dari menu dropdown:
   * Mentah (refleksi)

### memohon indeks ke gambar

Sebaik sahaja imej itu skrin penuh dan ** penonton imej ** <img src = "../. Gitbook/aset/icon_image-viewer.jpg" alt = "" data-size = "line"> sidebar tab dibuka:

1. Semak kotak indeks di bahagian atas bar sisi.
2. Pilih penapis kamera anda dari menu lungsur di sebelah kiri.
3. Pilih formula indeks yang dikehendaki dari menu lungsur di sebelah kanan.
4. Seret bulatan warna saluran penapis ke lokasi dalam formula indeks di bawah.
5. Sebaik sahaja formula itu sah, imej akan mengemas kini dan memaparkan nilai indeks.
6. Gerakkan kursor tetikus untuk melihat nilai -nilai di lokasi kursor.
7. Zum dalam imej untuk melihat piksel individu dan nilai yang berkaitan.

Setiap indeks mempunyai pelbagai nilai dan makna tertentu:

#### NDVI Contoh

___Code0000___

Untuk dokumentasi formula indeks lengkap, lihat [Formula Indeks Multispectral] (../ Project-Settings/Multispectral-Index-Formulas.md).

***

## Bekerja dengan LUTS (jadual carian)

### Apa itu LUT?

A ** Jadual Look-Up (LUT) ** Peta Nilai Indeks Numerik ke Warna Untuk Paparan:

*** Input **: Nilai Pixel Indeks (mis. NDVI 0.65)
*** output **: warna RGB (mis. Hijau cerah)
*** Tujuan **: Memudahkan visualisasi dan tafsiran corak

** Grayscale Lut vs Warna Lut: **

* Grayscale: Saintifik dan Neutral, menunjukkan data mentah
* Warna LUT: intuitif dan berkesan, menyoroti corak dan perbezaan

{% petunjuk gaya = & quot; kejayaan & quot; %}
** Viewability ** - Memohon warna LUT ke imej indeks skala kelabu menjadikannya mudah untuk mengenal pasti corak, anomali, dan bidang yang menarik.
{ % endhint %}

### memohon LUT ke gambar indeks

Sebaik sahaja anda mempunyai imej indeks yang menunjukkan

1. Klik <img src = "../. Gitbook/aset/image.png" alt = "" data-size = "line"> butang «+tambah lut»
2. Pilih kecerunan warna
3. Tetapkan titik akhir tanaman minimum/maksimum
4. Laraskan mod tanaman
5. Periksa kotak indeks di bar sisi ** Image Viewer ** <img src = "../. Gitbook/Asset/icon_image-viewer.jpg" alt = "" data-size = "line"> untuk memohon LUT.

### memilih kecerunan warna

** Memilih Kecerunan: **

1. Dalam panel LUT, cari bar kecerunan warna ** **.
2. Bergerak untuk melihat kecerunan pratetap yang ada.
3. Pilih kecerunan yang dikehendaki.
4. Imej ** segera mengemas kini ** dengan warna baru apabila kotak indeks diperiksa.

{% petunjuk gaya = & quot; kejayaan & quot; %}
** Amalan terbaik **: Untuk indeks tumbuh-tumbuhan seperti NDVI, kecerunan merah-kuning-hijau adalah yang paling intuitif, kerana ia mengikuti persatuan warna semulajadi (hijau = sihat, kuning = sederhana, merah = ditekankan).
{ % endhint %}

### menetapkan kelas warna

Kelas ** mengawal ** menentukan berapa banyak langkah warna diskret yang muncul dalam kecerunan:

** Pilihan kiraan kelas: **

*** 2-5 kelas **: Kategori yang sangat luas, kawasan yang berbeza.
*** 6-10 kelas **: seimbang, baik untuk klasifikasi.
*** 11-20 kelas **: Kecerunan lancar, penampilan berterusan.
*** Lebih daripada 20 kelas **: Hampir berterusan, kelembutan maksimum.

** Cara menyesuaikan: **

1. Dalam panel LUT, cari kotak swatch warna ** di bawah bar kecerunan **.
2. Laraskan bilangan kelas dengan menambah dengan butang +.
3. Padamkan bilangan kelas dengan mengklik dua kali ganda warna.
4. Kemas kini kecerunan ** Masa nyata ** pada imej.

** Kesan pada paparan: **

*** Kelas yang lebih sedikit ** (3-5): Buat kawasan yang dibezakan, klasifikasi mudah, lebih mudah untuk membezakan kategori.
*** kelas sederhana ** (6-10): Pendekatan seimbang, sesuai untuk kebanyakan aplikasi.
*** Lebih banyak kelas ** (15-20): Peralihan lancar, variasi terperinci, rupa fotografi.

** Bila hendak menggunakannya: **

*** Beberapa kelas (3-5) **: slaid persembahan, peta ranking, laporan mudah.
*** Kelas Sederhana (6-10) **: Analisis Umum, Perincian Seimbang, Laporan Standard.
*** Banyak kelas (15-20) **: Analisis saintifik, pemeriksaan terperinci, hasil kualiti penerbitan.

### Menetapkan julat nilai

Kawalan rangkaian ** ** Tentukan nilai indeks mana peta yang warna dalam kecerunan:

** Kawalan pelbagai di panel LUT: **

*** Nilai minimum **: Had bawah skala warna.
*** Nilai maksimum **: Had atas skala warna.
*** Nilai pertengahan **: diedarkan secara automatik antara minimum dan maksimum (berdasarkan kiraan kelas).

#### Menetapkan nilai minimum/maksimum

** Untuk menyesuaikan julat nilai: **

1. Dalam panel LUT, cari nilai minimum ** ** dan ** Nilai maksimum ** medan input.
2. Klik medan ** minimum ** medan.
3. Taipkan nilai minimum yang dikehendaki (contohnya, ___inline0000___).
4. Tekan ** masukkan ** atau klik di luar medan.
5. Ulangi proses untuk medan ** maksimum ** (contohnya, ___inline0001___).
6. Paparan ** kemas kini dengan segera **.

{% petunjuk gaya = & quot; info & quot; %}
** Auto Scaling ** - Apabila LUT digunakan untuk kali pertama, kloros secara automatik menetapkan min/max ke julat data imej sebenar. Anda kemudian dapat menyempitkan julat ini untuk memberi tumpuan kepada nilai nilai tertentu yang menarik.
{ % endhint %}

** NDVI Range Tetapan Contoh: **

*** julat penuh **: ___inline0002___ ke ___inline0003___ (Tunjukkan semua nilai yang mungkin)
*** Vegetasi Berfokus **: ___inline0004___ ke ___inline0005___ (tidak termasuk tanah dan air yang kosong)
*** Vegetasi Sihat Sahaja **: ___inline0006___ hingga ___inline0007___ (Sorot hanya tumbuh -tumbuhan yang kuat)
*** Saringan tekanan **: ___inline0008___ ke ___inline0009___ (menekankan kawasan masalah)
*** Range Custom ** - Laraskan berdasarkan nilai piksel yang diperhatikan

** Mengapa menyesuaikan julat? **

*** Meningkatkan kontras ** di kawasan minat
*** Tidak termasuk nilai yang tidak relevan ** (mis. Badan air, tanah kosong)
*** Menyeragamkan paparan ** merentasi pelbagai imej atau tarikh
*** Sorot perbezaan halus ** dalam julat nilai sempit

### memangkas nilai julat

Apabila nilai piksel jatuh di luar julat min/max yang ditakrifkan, anda boleh mengawal bagaimana ia dipaparkan menggunakan ** mod tanaman **.

#### ** pilihan mod tanaman yang ada: **

#### 1. Minimum dan maksimum

*Piksel ** di bawah minimum ** → paparan menggunakan ** warna pertama ** kecerunan (mis.
*Piksel ** di atas maksimum ** → paparan menggunakan warna terakhir ** kecerunan (mis. Hijau)
*** Gunakan Kes **: Tekankan ekstrem, tunjukkan pelbagai data dengan warna tepu di sempadan
*** Contoh **: Nilai NDVI di bawah 0.2 muncul dalam warna merah, nilai di atas 0.9 muncul dalam hijau

#### 2. Latar belakang telus

*Piksel ** keluar dari jarak ** menjadi ** telus sepenuhnya **
*Hanya piksel ** dalam julat ** Tunjukkan kecerunan warna.
*** Gunakan kes **: GIS overlay, pengasingan julat nilai tertentu, menonjolkan hanya bidang yang menarik.
*** Contoh **: Tunjukkan hanya NDVI 0.4-0.7 dalam warna, segala-galanya telus.

{% petunjuk gaya = & quot; Amaran & quot; %}
** Batasan Ketelusan **: Piksel telus akan muncul sebagai warna latar belakang dalam penonton. Apabila dieksport semasa pemprosesan, ketelusan dipelihara dalam format PNG, tetapi tidak dalam JPG.
{ % endhint %}

#### 3. Dana indeks

*Pixels ** Out of Range ** dipaparkan dalam ** Grayscale ** (menunjukkan nilai indeks mentah).
*Piksel ** dalam julat ** Paparkan kecerunan warna ** **.
*** Gunakan Kes **: Sorotan halus, mengekalkan konteks sambil menekankan bidang kepentingan.
*** Contoh **: menyoroti tumbuh-tumbuhan yang ditekan dengan warna (NDVI 0.3-0.5) dan menunjukkan kawasan yang sihat dalam kelabu.

#### 4. Latar belakang asal

*Pixels ** Out of Range ** ditunjukkan dalam ** imej multispectral asal **.
*Piksel ** dalam julat ** Paparkan kecerunan warna ** **
*** Gunakan Kes **: Paling Intuitif: Menggabungkan Konteks Imej Semulajadi dengan Overlay Warna Analisis
*** Contoh **: Lihat apa bidang/tanaman sebenarnya kelihatan seperti kawasan tertekan yang dilapisi dan berkod warna

### Pilih mod tanaman yang betul

| Mod Tanaman | Ideal untuk | Gaya Paparan |
| -------------------------- | ------------------------------------------ | ---------------------------- |
| ** minimum dan maksimum ** | Visualisasi Data Lengkap, Analisis Saintifik | Semua piksel berwarna |
| ** Latar Belakang Telus ** | GIS Overlays, pengasingan julat tertentu | Warna dalam julat, kosong di luarnya |
| ** dana indeks ** | Penekanan halus, mengekalkan konteks data | Warna dalam julat, kelabu melampaui |
| ** Latar Belakang Asal ** | Laporan, persembahan, analisis intuitif | Warna dalam julat, gambar di luar |

### mencipta warna lut tersuai

Untuk kawalan penuh ke atas paparan, anda boleh membuat ** kecerunan warna tersuai ** dengan mengedit hentian warna individu.

** Untuk membuat kecerunan tersuai: **

1. Dalam panel LUT, cari bar pratonton kecerunan ** **.
2. Cari kotak swatch ** warna ** di bawah kecerunan.
3. ** Klik pada Warna Stop ** untuk memilihnya.
4. A ** pemetik warna ** akan dibuka.
5. Pilih warna baru menggunakan:
   *** Roda warna **: Pemilihan warna visual.
   *** Slider RGB/HSV **: Kawalan warna yang tepat.
   *** Kemasukan kod hexadecimal **: Spesifikasi warna yang tepat (mis. ___Inline0010___ untuk merah).
6. Klik di luar pemetik warna ** untuk memohon warna baru **.
7. Kecerunan ** Kemas kini dengan segera ** pada imej.

** Tambah atau keluarkan warna berhenti: **

*** Tambah Stop **: Klik ikon + untuk menambah sampel baru ke hujungnya.
*** Padam berhenti **: Klik dua kali persegi berwarna untuk memadam swatch.

** Strategi Penyesuaian: **

*** Masukkan kecerunan **: Balikkan urutan warna untuk membalikkan makna (mis. Hijau = rendah, merah = tinggi).
*** Warna Jenama ** - Padankan palet warna organisasi anda untuk laporan.
*** Warna Blind Friendly **: Gunakan kombinasi oren dan biru atau ungu dan kuning.
*** Pengoptimuman cetak ** - Pilih warna yang berfungsi dalam percetakan warna dan skala kelabu.
*** Pelbagai ambang **: Gunakan warna yang berbeza pada ambang nilai tertentu untuk klasifikasi.

{% petunjuk gaya = & quot; info & quot; %}
** Simpan Gradien Custom ** - Kecerunan tersuai boleh disimpan dan digunakan semula. Klik ikon Simpan di panel LUT untuk menyimpan skema warna tersuai anda untuk kegunaan masa depan.
{ % endhint %}

***

## alur kerja interaktif

### kemas kini masa nyata

Semua tetapan LUT di kotak pasir mengemas kini imej ** dengan serta -merta dan interaktif **:

*** Tukar lapisan ** → imej berubah segera
*** Pilih Kecerunan ** → Warna Kemas kini dengan serta -merta
*** Laraskan julat nilai ** → Perubahan kontras dalam masa nyata
*** Tukar kelas ** → Kemas kini kelancaran kecerunan segera
*** Ubah suai tanaman ** → paparan latar belakang berubah dengan serta -merta
*** edit warna ** → kecerunan tersuai digunakan dengan segera

** Tidak ada butang "Terapkan" yang diperlukan ** - Semua perubahan hidup dan interaktif!

{% petunjuk gaya = & quot; kejayaan & quot; %}
** Maklum balas langsung ** - Maklum balas visual segera membolehkan anda dengan cepat bereksperimen dengan tetapan yang berbeza sehingga anda dapati paparan optimum untuk keperluan analisis anda.
{ % endhint %}

### aliran kerja penghalusan berulang

** Aliran Kerja Pengoptimuman LUT biasa: **

1. ** Pilih lapisan indeks ** (contohnya, mentah (reflektif)).
2. ** Sapukan Indeks **: Pilih formula penapis dan indeks kamera, seret bulatan warna ke lokasi yang sesuai dalam formula indeks.
3. ** Memohon Lut Gradien **-Mulakan dengan pratetap merah-kuning-hijau.
4. ** Periksa nilai piksel **: Gerakkan kursor dan perhatikan julat nilai.
5. ** Laraskan Min/Max **: Kurangkan julat untuk memberi tumpuan kepada tumbuh -tumbuhan (contohnya, dari 0.2 hingga 0.9).
6. ** Pilih Tanaman ** - Cuba "Latar Belakang Asal" untuk konteks.
7. ** Memperbaiki Warna ** - Sesuaikan kecerunan jika perlu untuk menekankan sesuatu yang khusus.
8. ** Selesai Konfigurasi **: Dokumen konfigurasi dan salinnya ke konfigurasi projek untuk pemprosesan eksport.

Pemeriksaan nilai ### piksel

Memahami nilai piksel sebenar adalah penting untuk mewujudkan julat LUT yang berkesan:

** Cara Memeriksa Nilai: **

1. Nilai piksel dipaparkan apabila imej mempunyai indeks atau indeks dan kotak lut ** diperiksa **.
2. ** Gerakkan kursor ** di kawasan yang berbeza dari imej.
3. ** Nota nilai piksel ** yang dipaparkan dalam legenda apabila anda melayang ke atasnya.
4. Zum masuk pada imej untuk melihat piksel individu yang diserlahkan dengan nilai terapung.
5. ** Perhatikan ** julat nilai untuk ciri -ciri yang berbeza:
   *** Vegetation Sihat **: Sebagai contoh, NDVI 0.55-0.85.
   *** Menekan tumbuh-tumbuhan **: Sebagai contoh, NDVI 0.30-0.50.
   *** Tanah kosong **: mis. NDVI 0.05-0.25
   *** air ** (jika ada): mis. NDVI -0.05 hingga 0.10

** Menggunakan nilai piksel untuk menetapkan julat lut: **

Selepas memeriksa nilai piksel, laraskan minimum dan maksimum LUT dengan sewajarnya:

** Contoh Senario: **

*** pemerhatian **: nilai tanah = 0.05-0.25, ditekankan = 0.25-0.50, sihat = 0.50-0.85
*** Matlamat **: Lihat sahaja Kesihatan Loji (tidak termasuk tanah)
*** Tetapan LUT **: Min. = ___Inline0011___, Max. = ___Inline0012___
*** tanaman **: "latar belakang asal" untuk melihat lantai dengan warna semula jadi
*** Hasil **: Kecerunan warna hanya digunakan untuk tumbuh -tumbuhan, tanah ditunjukkan seperti dalam imej asal

{% petunjuk gaya = & quot; info & quot; %}
** Julat Dinamik **: Tanaman, musim dan peringkat pertumbuhan yang berbeza akan mempunyai pelbagai nilai yang berbeza. Sentiasa periksa nilai piksel dalam set data khusus anda sebelum menetapkan julat LUT.
{ % endhint %}

***

## indeks tersuai (chloros+)

### Membuat formula indeks tersuai

{% petunjuk gaya = & quot; info & quot; %}
** Di mana untuk membuat **: Indeks tersuai boleh dikonfigurasikan dalam ** Tetapan Projek ** Sebelum membuat, serta di bar sisi penonton imej.
{ % endhint %}

** Untuk membuat indeks tersuai: **

1. ** Buka Tetapan Projek ** (sebelum Pemprosesan) atau Sidebar Penonton Imej.
2. Pergi ke menu drop-down ** indeks **.
3. Cari pilihan ** «Custom» ** (anda mesti log masuk dengan Lesen Chloros+).
4. ** Tentukan formula anda ** menggunakan pembolehubah band:
   * Nama band: ___inline0013___, ___inline0014___, ___inline0015___, ___inline0016___, ___inline0017___, dll.
   * Pengendali: ___inline0018___, ___inline0019___, ___inline0020___, ___inline0021___, ___inline0022___ (eksponen)
   * Fungsi: ___inline0023___, ___inline0024___, dll (jika disokong)
   * Kurungan: ___inline0025___ untuk pesanan operasi
5. ** Namakan indeks anda ** (contohnya, "myindex" atau "customndvi").
6. ** Simpan tetapan **.

** Contoh formula tersuai: **

___Code0001___

{% petunjuk gaya = & quot; Amaran & quot; %}
** Pengesahan Formula **: Pastikan formula anda menggunakan band yang terdapat di kamera anda. Sebagai contoh, Rededge hanya boleh didapati di kamera dengan penapis Rededge.
{ % endhint %}

***

## Langkah seterusnya

Sekarang anda tahu kotak pasir indeks/lut:

*** Memohon kepada pemprosesan **: Gunakan tetapan yang ditemui dalam [Tetapan Projek] (../ Project-Settings/Project-Settings.md)
*** Pemprosesan Batch **: Sapukan indeks yang dioptimumkan ke keseluruhan set data
*** Maklumat lanjut **: Baca [formula indeks multispectral] (../ Project-Settings/Multispectral-index-Formulas.md)

Dokumentasi Berkaitan:

*[** Lapisan Imej **] (Image -Layers.md) - Pengurusan Lapisan dan Paparan
*[** Buka skrin penuh imej **] (pembukaan-an-imej-full-screen.md)-Asas Penonton Imej
*[** Pemprosesan Imej (GUI) **] (../ Pemprosesan-Images-Gui/Adding-Files-to-a-project.md)-Aliran Kerja Pemprosesan Lengkap