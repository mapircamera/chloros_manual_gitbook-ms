# Pemilihan gambar sasaran

Menandai imej mana yang mengandungi sasaran penentukuran adalah langkah penting yang mempercepatkan proses kloros. Dengan merangka imej sasaran, anda menghapuskan keperluan kloros untuk mengimbas semua imej dalam set data untuk sasaran penentukuran.

## Mengapa menandakan imej sasaran?

### kelajuan pemprosesan

Tanpa menandakan imej sasaran, kloros mesti:

* Imbas setiap imej dalam projek anda.
* Jalankan algoritma pengesanan sasaran pada setiap imej.
* Semak beratus -ratus atau beribu -ribu imej yang tidak perlu.

** Hasil **: Pemprosesan boleh mengambil masa yang lebih lama, terutamanya untuk set data yang besar.

### dengan imej sasaran yang ditandakan

Apabila anda menyemak lajur sasaran untuk imej tertentu:

* Chloros hanya mengimbas imej yang ditandakan untuk sasaran.
* Pengesanan sasaran selesai lebih cepat.
* Jumlah masa pemprosesan sangat dikurangkan.

{% petunjuk gaya = & quot; kejayaan & quot; %}
** Penambahbaikan kelajuan **: Menandai 2-3 imej sasaran dalam set data 500 imej dapat mengurangkan masa pengesanan sasaran dari lebih dari 30 minit hingga kurang dari 1 minit.
{ % endhint %}

***

## Cara menandakan gambar sasaran

### Langkah 1: Kenal pasti gambar sasaran anda

Semak imej yang diimport dalam penjelajah fail dan mengenal pasti yang mengandungi sasaran penentukuran.

** Senario biasa: **

*** Sasaran pra-menangkap **: Ditangkap sebelum memulakan sesi.
*** Post Capture Objective **: Ditangkap setelah menyelesaikan sesi.
*** Sasaran di lapangan **: Sasaran yang diletakkan di dalam kawasan penangkapan.
*** Pelbagai sasaran **: 2-3 Imej sasaran setiap sesi (disyorkan).

### Langkah 2: Periksa lajur objektif.

Untuk setiap imej yang mengandungi sasaran penentukuran:

1. Cari imej dalam jadual Explorer File.
2. Cari lajur ** sasaran ** (lajur paling kanan).
3. Klik kotak semak dalam lajur sasaran untuk imej itu.
4. Ulangi proses untuk semua imej yang mengandungi sasaran.

### Langkah 3: Semak pilihan anda.

Sebelum memproses, periksa perkara berikut:

* [] Semua imej dengan sasaran penentukuran ditandakan.
* [] Tiada imej yang tidak sasaran secara tidak sengaja ditandakan.
* [] Sasaran jelas kelihatan dalam imej yang ditandakan.

***

## Amalan terbaik untuk imej sasaran

### Garis Panduan Tangkapan Sasaran

** Momen: **

* Menangkap imej sasaran segera sebelum dan semasa sesi penangkapan.
* Di bawah keadaan pencahayaan yang sama seperti sensor cahaya DAQ.
* Sebaik -baiknya anda harus menangkap imej sasaran seberapa kerap mungkin untuk hasil yang terbaik. Jika tidak, data sensor cahaya akan digunakan untuk menyesuaikan penentukuran dari masa ke masa.

** Kedudukan Kamera: **

*Pegang kamera di atas lensa supaya ia berpusat dan mengambil 40-60% pusat imej.
* Pastikan kamera selari/nadir ke permukaan kanta.

** Kilat: **

* Pencahayaan ambien yang sama seperti sensor cahaya daq anda.
* Elakkan bayang -bayang pada permukaan sasaran.
*Jangan menyekat sumber cahaya dengan badan, kenderaan atau tumbuh -tumbuhan anda.
*Keadaan mendung memberikan hasil yang paling konsisten.

** Keadaan kanta: **

* Pastikan panel sasaran bersih dan kering.
* Semua 4 panel mesti kelihatan jelas dan bebas daripada halangan.
*Sasaran hendaklah berserenjang/nadir ke sumber cahaya, jika boleh.

### Berapa banyak imej sasaran?

** Minimum: ** 1 Imej sasaran setiap sesi. ** Disyorkan: ** 3-5 Imej sasaran setiap sesi.

** Kalendar amalan yang baik: **

* Menangkap 3-5 imej sejurus selepas sensor cahaya mula merakam.
* Putar kamera antara tangkapan untuk hasil terbaik.
*Pilihan: secara berkala pertengahan sesi jika keadaan pencahayaan sentiasa berubah.

***

## Bekerja dengan pelbagai kamera

### setup kamera dwi

Jika anda menggunakan dua kamera MAPIR secara serentak (mis. SURVEY3W RGN + SURVEY3N OCN):

1. Tangkap imej sasaran dengan ** kedua -dua kamera ** pada masa yang sama.
2. Gunakan lensa fizikal yang sama ** untuk kedua -dua kamera.
3. Semak imej sasaran untuk ** kedua -dua jenis kamera ** dalam explorer fail.
4. Chloros akan menggunakan objektif yang sesuai untuk penentukuran setiap kamera.

Lajur Model ### Kamera

Model kamera ** ** lajur membantu mengenal pasti imej mana yang datang dari kamera mana:

* SURVEY3W \ _RGN
* SURVEY3N \ _OCN
* SURVEY3W \ _RGB
* dll.

Gunakan lajur ini untuk mengesahkan bahawa anda telah menandakan sasaran untuk setiap jenis kamera dalam projek anda.

***

## Tetapan Pengesanan Sasaran

### Laraskan kepekaan pengesanan

Jika kloros tidak mengesan sasaran anda dengan betul, sesuaikan parameter ini dalam [tetapan projek] (menyesuaikan-projek-settings.md):

** Kawasan sampel penentukuran minimum: **

*** lalai **: 25 piksel
*** Meningkatkan ** Sekiranya anda mendapat pengesanan palsu mengenai artifak kecil
*** Kurangkan ** Sekiranya sasaran tidak dikesan

** Pengumpulan sasaran minimum: **

*** lalai **: 60
*** Meningkatkan ** Jika sasaran dibahagikan kepada pelbagai pengesanan.
*** Kurangkan ** Jika sasaran dengan variasi warna tidak dikesan sepenuhnya.

***

## Masalah biasa dengan imej kanta

### Masalah: Tiada sasaran yang dikesan.

** Kemungkinan Punca: **

*Imej sasaran tidak ditandakan dalam File Explorer.
* Kanta terlalu kecil dalam bingkai (& lt; 30% imej).
* Pencahayaan yang lemah (bayang -bayang, pantulan)
* Tetapan pengesanan sasaran terlalu ketat

** Penyelesaian: **

1. Periksa bahawa lajur sasaran diperiksa untuk imej yang betul
2. Periksa kualiti imej sasaran dalam pratonton
3. Menarik balik sasaran jika kualiti miskin
4. Laraskan tetapan pengesanan sasaran jika perlu

### Isu: Pengesanan sasaran palsu

** Kemungkinan Punca: **

* Bangunan putih, kenderaan atau tumbuh -tumbuhan yang tersilap untuk sasaran.
* Tempat terang pada tumbuh -tumbuhan.
* Kepekaan pengesanan terlalu rendah.

** Penyelesaian: **

1. Tanda hanya imej sasaran sebenar untuk mengehadkan julat pengesanan.
2. Meningkatkan kawasan sampel penentukuran minimum.
3. Meningkatkan nilai kumpulan sasaran minimum.
4. Pastikan imej sasaran hanya menunjukkan sasaran (bunyi latar belakang yang minimum).

***

## senarai semak

Sebelum memulakan pemprosesan, periksa pemilihan imej sasaran:

* [] Sekurang -kurangnya 1 imej sasaran ditandakan setiap sesi.
* [] Kotak semak lajur sasaran diperiksa untuk semua imej sasaran.
* [] Imej sasaran yang ditangkap dalam selang masa yang sama seperti kajian.
* [] Sasaran jelas kelihatan dalam pratonton apabila diklik.
* [] Semua 4 panel penentukuran dapat dilihat dalam setiap imej sasaran.
* [] Tiada bayang -bayang atau penghalang pada kanta.
* [] Untuk kamera dua: kanta ditandakan untuk kedua -dua jenis kamera.

***

## pemprosesan tanpa sasaran

### Pemprosesan tanpa sasaran penentukuran

Walaupun tidak disyorkan untuk kerja saintifik, anda boleh memproses tanpa objektif:

1. Tinggalkan semua kotak semak dalam lajur matlamat yang tidak terkawal.
2. ** Lumpuhkan ** "Penentukuran Refleksi" dalam tetapan projek.
3. Pembetulan vignette masih akan digunakan.
4. Output tidak akan ditentukur untuk pemantulan mutlak.

{% petunjuk gaya = & quot; Amaran & quot; %}
** Tidak disyorkan **: Tanpa penentukuran pemantulan, nilai piksel mewakili kecerahan relatif sahaja, bukan pengukuran refleksi saintifik. Gunakan sasaran penentukuran untuk keputusan yang tepat dan boleh diulang.
{ % endhint %}

***

## Langkah seterusnya

Sebaik sahaja anda menandakan imej sasaran:

1. ** Tetapan Tinjauan **: Lihat [menyesuaikan tetapan projek] (menyesuaikan-projek-stetings.md)
2. ** Mulakan pemprosesan **-lihat [memulakan pemprosesan] (permulaan-pemproses.md)
3. ** Memantau Kemajuan **-Lihat [Memantau pemprosesan] (Pemantauan-The-Processing.md)

Untuk maklumat lanjut mengenai sasaran penentukuran, lihat [sasaran penentukuran] (../ calibration-targets.md).