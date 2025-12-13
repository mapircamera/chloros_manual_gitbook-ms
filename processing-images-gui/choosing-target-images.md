# Memilih gambar sasaran

Menandai imej mana yang mengandungi sasaran penentukuran adalah langkah penting yang mempercepatkan saluran paip pemprosesan kloros. Dengan pra-memilih imej sasaran, anda menghapuskan keperluan kloros untuk mengimbas setiap imej dalam dataset anda untuk sasaran penentukuran.

## Mengapa menandakan imej sasaran?

### kelajuan pemprosesan

Tanpa menandakan imej sasaran, kloros mesti:

* Imbas setiap gambar dalam projek anda
* Jalankan algoritma pengesanan sasaran pada setiap gambar
* Periksa beratus -ratus atau ribuan gambar yang tidak perlu

** Hasil **: Pemprosesan boleh mengambil masa yang lebih lama, terutamanya untuk dataset yang besar.

### dengan imej sasaran yang ditandakan

Apabila anda menyemak lajur sasaran untuk imej tertentu:

* Kloros hanya mengimbas imej yang diperiksa untuk sasaran
* Pengesanan sasaran melengkapkan lebih cepat
* Masa pemprosesan keseluruhan dikurangkan

{% hint style="success" %}
** Penambahbaikan kelajuan **: Menandai 2-3 imej sasaran dalam dataset 500-imej dapat mengurangkan masa pengesanan sasaran dari 30+ minit hingga bawah 1 minit.
{ % endhint %}

***

## Cara menandakan gambar sasaran

### Langkah 1: Kenal pasti gambar sasaran anda

Lihat melalui imej yang diimport anda dalam penyemak imbas fail dan mengenal pasti imej mana yang mengandungi sasaran penentukuran.

** Senario biasa: **

* **Target Pra-Capture**: Ditangkap Sebelum Memulakan Sesi
* **sasaran pasca menangkap**: ditangkap setelah menyelesaikan sesi
* **sasaran dalam bidang**: sasaran yang diletakkan di kawasan penangkapan
* **Pelbagai sasaran**: 2-3 Imej sasaran setiap sesi (disyorkan)

### Langkah 2: Periksa lajur sasaran

Untuk setiap imej yang mengandungi sasaran penentukuran:

1. Cari gambar dalam jadual penyemak imbas fail
2. Cari lajur ** sasaran ** (lajur paling kanan)
3. Klik kotak semak dalam lajur sasaran untuk gambar itu
4. Ulangi semua imej yang mengandungi sasaran

### Langkah 3: Sahkan pilihan anda

Sebelum diproses, semakan semula:

* [] Semua imej dengan sasaran penentukuran diperiksa
* [] Tidak ada gambar yang tidak sasaran yang disemak secara tidak sengaja
* [] Sasaran jelas kelihatan dalam imej yang diperiksa

***

## Amalan terbaik untuk imej sasaran

### Garis Panduan Tangkapan Sasaran

** Masa: **

* Tangkap imej sasaran segera sebelum dan sepanjang sesi penangkapan anda
* Dalam keadaan pencahayaan yang sama seperti sensor cahaya daq anda
* Idealnya menangkap imej sasaran sekerap mungkin untuk hasil yang terbaik. Jika tidak, data sensor cahaya akan digunakan untuk menyesuaikan penentukuran dari masa ke masa.

** Kedudukan Kamera: **

* Pegang kamera di atas sasaran seperti yang dipusatkan dan mengisi sekitar 40-60% dari pusat imej.
* Simpan kamera selari/nadir ke permukaan menargetkan

** pencahayaan: **

* Pencahayaan ambien yang sama seperti sensor cahaya daq anda
* Elakkan bayang -bayang di permukaan sasaran
* Jangan menyekat sumber cahaya anda dengan badan, kenderaan atau tumbuh -tumbuhan anda
* Keadaan mendung memberikan hasil yang paling konsisten

** Keadaan sasaran: **

* Pastikan panel sasaran bersih dan kering
* Semua 4 panel harus kelihatan jelas dan tidak terhalang
* Sasaran tegak lurus/nadir ke sumber cahaya jika boleh

### Berapa banyak imej sasaran?

** Minimum: ** 1 Imej sasaran setiap sesi. ** Disyorkan: ** 3-5 Imej sasaran setiap sesi.

** Jadual Amalan Terbaik: **

* 3-5 gambar yang ditangkap sejurus selepas sensor cahaya sedang merakam
* Putar kamera antara tangkapan untuk hasil terbaik
* Pilihan: Secara pertengahan sesi secara berkala jika keadaan pencahayaan sentiasa berubah

***

## Bekerja dengan pelbagai kamera

### setup dwi-kamera

Jika menggunakan dua kamera MAPIR secara serentak (mis., Survey3W RGN + Survey3N OCN):

1. Tangkap imej sasaran dengan ** kedua -dua kamera ** pada masa yang sama
2. Gunakan sasaran fizikal ** yang sama ** untuk kedua -dua kamera
3. Tanda gambar sasaran untuk ** kedua -dua jenis kamera ** dalam penyemak imbas fail
4. Chloros akan menggunakan sasaran yang sesuai untuk setiap penentukuran kamera

Lajur Model Kamera ###

Model kamera ** ** lajur membantu mengenal pasti imej mana yang datang dari kamera mana:

* SURVEY3W \ _RGN
* SURVEY3N \ _OCN
* SURVEY3W \ _RGB
* dll.

Gunakan lajur ini untuk mengesahkan anda telah menandakan sasaran untuk setiap jenis kamera dalam projek anda.

***

## Tetapan Pengesanan Sasaran

### menyesuaikan kepekaan pengesanan

Jika kloros tidak mengesan sasaran anda dengan betul, laraskan tetapan ini dalam [tetapan projek](menyesuaikan-projek-penyediakan.md):

** Kawasan sampel penentukuran minimum: **

* **lalai**: 25 piksel
*** Meningkatkan ** Sekiranya mendapat pengesanan palsu pada artifak kecil
*** Kurangkan ** Sekiranya sasaran tidak dikesan

** Clustering Sasaran Minimum: **

* **lalai**: 60
*** Meningkatkan ** Sekiranya sasaran dibahagikan kepada pelbagai pengesanan
*** Kurangkan ** Sekiranya sasaran dengan variasi warna tidak dikesan sepenuhnya

***

## masalah sasaran biasa

### Masalah: Tiada sasaran yang dikesan

** Kemungkinan Punca: **

* Imej sasaran tidak ditandakan dalam penyemak imbas fail
* Sasaran terlalu kecil dalam bingkai (<30% imej)
* Pencahayaan yang lemah (bayang -bayang, silau)
* Tetapan pengesanan sasaran terlalu ketat

** Penyelesaian: **

1. Sahkan lajur sasaran diperiksa untuk imej yang betul
2. Kaji semula kualiti imej sasaran dalam pratonton
3. Menarik balik sasaran jika kualiti miskin
4. Laraskan tetapan pengesanan sasaran jika diperlukan

### Masalah: Pengesanan sasaran palsu

** Kemungkinan Punca: **

* Bangunan putih, kenderaan, atau penutup tanah tersilap untuk sasaran
* Tompok cerah dalam tumbuh -tumbuhan
* Kepekaan pengesanan terlalu rendah

** Penyelesaian: **

1. Tandakan hanya imej sasaran sebenar untuk mengehadkan skop pengesanan
2. Meningkatkan kawasan sampel penentukuran minimum
3. Meningkatkan nilai clustering sasaran minimum
4. Pastikan imej sasaran hanya menunjukkan sasaran (kekacauan latar belakang yang minimum)

***

## senarai semak pengesahan

Sebelum memulakan pemprosesan, sahkan pemilihan imej sasaran anda:

* [] Sekurang -kurangnya 1 imej sasaran ditandakan setiap sesi
* [] Kotak semak lajur sasaran diperiksa untuk semua imej sasaran
* [] Imej sasaran yang ditangkap dalam jangka masa yang sama seperti tinjauan
* [] Sasaran jelas kelihatan dalam pratonton apabila diklik
* [] Semua 4 panel penentukuran dapat dilihat dalam setiap gambar sasaran
* [] Tidak ada bayang -bayang atau halangan pada sasaran
* [] Untuk dwi-kamera: sasaran ditandakan untuk kedua-dua jenis kamera

***

## pemprosesan bebas sasaran

### Pemprosesan tanpa sasaran penentukuran

Walaupun tidak disyorkan untuk kerja saintifik, anda boleh memproses tanpa sasaran:

1. Tinggalkan semua kotak semak lajur sasaran yang tidak diselesaikan
2. ** Lumpuhkan ** "Penentukuran Refleksi" dalam tetapan projek
3. Pembetulan vignette masih akan digunakan
4. output tidak akan dikalibrasi untuk pemantulan mutlak

{% hint style="warning" %}
** Tidak disyorkan **: Tanpa penentukuran pemantulan, nilai piksel mewakili kecerahan relatif sahaja, bukan pengukuran refleksi saintifik. Gunakan sasaran penentukuran untuk keputusan yang tepat dan boleh diulang.
{ % endhint %}

***

## Langkah seterusnya

Sebaik sahaja anda menandakan gambar sasaran anda:

1. ** Mengkaji tetapan anda **-lihat [menyesuaikan tetapan projek](menyesuaikan-projek-penyetempatan.md)
2. ** Mula Pemprosesan **-Lihat [Memulakan Pemprosesan](permulaan-pemproses.md)
3. ** Memantau Kemajuan **-Lihat [Memantau pemprosesan](Pemantauan-The-Processing.md)

Untuk maklumat lanjut mengenai penentukuran mensasarkan diri mereka, lihat [sasaran penentukuran](../ penentukuran targets.md).
