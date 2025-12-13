# Tambahkan fail ke projek

Sebaik sahaja anda telah membuat atau membuka projek di Chloros, langkah seterusnya adalah untuk menambah imej multispektral anda untuk memulakan pemprosesan. Penyemak imbas fail <img src = "../. Gitbook/aset/icon_file-browser.jpg" alt = "" data-size = "line"> menjadikannya mudah untuk mengimport imej dan menguruskan set data anda.

## Akses ke Fail Explorer

1. Buka atau buat projek di kloros
2. Klik penyemak imbas fail ** <img src = "../.
3. Panel Explorer File akan menunjukkan senarai fail dalam projek anda

{% petunjuk gaya = & quot; info & quot; %}
** Jenis fail yang disokong **: Chloros menyokong fail imej RAW+JPG dan JPG dari kamera Survey3W dan Survey3N MAPIR. Hanya fail RAW+JPG yang disyorkan.
{ % endhint %}

***

## Tambahkan gambar ke projek anda

Terdapat dua cara utama untuk menambah imej ke projek anda:

### Kaedah 1: Tambahkan fail

Gunakan pilihan ini untuk mengimport fail imej individu atau pemilihan fail kecil.

1. Klik butang ** "Tambah Fail" ** di bahagian atas panel Explorer File.
2. Navigasi ke folder yang mengandungi imej anda.
3. Pilih satu atau lebih fail imej (tahan ** ctrl ** untuk memilih pelbagai fail).
4. Klik ** «Buka» ** untuk mengimport fail yang dipilih.

### Kaedah 2: Tambah folder

Gunakan pilihan ini untuk mengimport semua imej dalam folder sekaligus.

1. Klik butang ** "Tambah Folder" ** di bahagian atas panel Explorer File.
2. Navigasi ke folder yang mengandungi imej dari sesi penangkapan anda dan pilihnya.
3. Klik ** «Pilih Folder» ** Untuk mengimport semua imej yang disokong dari folder itu.

***

## File Explorer Jadual Penerangan

Sebaik sahaja imej diimport, mereka muncul dalam jadual dengan lajur berikut:

### Thumbnail

* Pratonton kecil setiap imej.
* Klik pada gambar kecil untuk melihat imej penuh di kawasan pratonton utama.

### Nama fail

* Nama fail kamera asal.
* Mengekalkan konvensyen penamaan kamera (mis. IMG \ _0001.RAW).

### timestamp

* Tarikh dan masa imej ditangkap.
* Diekstrak dari metadata EXIF ​​imej.
* Digunakan untuk pengesanan sasaran penyegerakan dan penentukuran PPK

### Model kamera

* Tetapan kamera dan penapis dikesan secara automatik
* Contoh: survey3w \ _rgn, survey3n \ _ocn, survey3w \ _rgb
* Digunakan untuk menggunakan profil pemprosesan yang betul

### lajur objektif (kotak semak)

* Periksa kotak ini untuk imej yang mengandungi sasaran penentukuran
* Sangat mempercepat pengesanan sasaran semasa pemprosesan
* Lihat [Memilih Imej Sasaran] (memilih sasaran-imej.md) untuk maklumat lanjut

***

## Pengurusan fail dalam projek anda

### memadam fail

Untuk membuang gambar yang tidak diingini dari projek anda:

1. Pilih satu atau lebih gambar dalam jadual penjelajah fail
2. Klik ** «padam dipilih» ** butang
3. Sahkan penghapusan (fail tidak dipadam dari cakera, hanya dipadam dari projek)

### menyusun dan menapis

*** Sort mengikut lajur ** - Klik tajuk lajur untuk menyusun gambar
*** Sort oleh Timestamp **: Berguna untuk menganjurkan urutan penangkapan kronologi.
*** Penapis Model Kamera **: Imej Kumpulan oleh Jenis Kamera Jika anda menggunakan pelbagai kamera.

***

## Pratonton imej

### Lihat gambar penuh

Klik mana -mana gambar kecil imej dalam Fail Explorer untuk memaparkannya di kawasan pratonton utama:

1. Imej muncul di panel Pratonton Pusat.
2. Gunakan kawalan zum untuk memeriksa butiran imej.
3. Navigasi antara imej menggunakan kekunci anak panah.

### Navigasi cepat

*** Imej di atas **: Klik anak panah kiri atau tekan kekunci ←.
*** Imej di bawah **: Klik anak panah kanan atau tekan kekunci →.
*** Zum masuk/keluar **: Gunakan butang roda tetikus atau zum.
*** Panorama **: Klik dan seret pada imej apabila diperbesarkan.

***

## Pengurusan fail pendua

Chloros secara automatik mengesan dan mengabaikan fail pendua:

* Fail dengan nama yang sama diabaikan.
* Elakkan pemprosesan berganda yang tidak disengajakan.
* Mesej amaran dipaparkan apabila pendua dikesan.

{% petunjuk gaya = & quot; Amaran & quot; %}
** PENTING **: Jangan ubah semula atau ubah suai fail imej asal sebelum mengimportnya. Chloros bergantung pada nama fail asal dan metadata untuk pemprosesan yang betul.
{ % endhint %}

***

## dataset kamera bercampur

Jika projek anda mengandungi imej dari pelbagai kamera Mapir:

1. Chloros secara automatik mengesan setiap model kamera.
2. Setiap jenis kamera diproses dengan profil penentukuran yang sesuai.
3. Penjelajah fail memaparkan model kamera dalam lajur model kamera.
4. Pemprosesan menggunakan tetapan yang betul untuk setiap jenis kamera.

** Contoh Senario **: Survey3W RGN + Survey3N OCN Dual Camera Persediaan.

***

## Amalan terbaik

### Susun sebelum import

* Simpan imej penentukuran dalam folder yang sama seperti imej kajian.
* Simpan struktur folder asal kamera/kad SD anda.
* Jangan campurkan set data dari sesi yang berbeza dalam projek yang sama.

### Nama fail

* Simpan nama fail kamera asal (IMG \ _0001.raw, dll.).
* Jangan menamakan semula fail sebelum mengimportnya.
* Nama asal mengandungi metadata penting.

### imej penentukuran

*Sentiasa sertakan 1-2 imej penentukuran setiap sesi.
* Menangkap sasaran sebelum dan selepas sesi penangkapan.
*Tempat sasaran dalam keadaan pencahayaan yang sama seperti kawasan penangkapan.
* Semak imej penentukuran dengan kotak sasaran untuk mempercepatkan pemprosesan.

***

## Masalah dan penyelesaian biasa

### gambar tidak muncul setelah import

** Kemungkinan Punca: **

* Format fail tidak disokong (hanya RAW+JPG dan JPG dari kamera MAPIR).
* Imej adalah dari kamera bukan mapir (lihat [kamera yang disokong] (../ disokong cameras.md)).
* Fail yang rosak atau pemindahan tidak lengkap dari kad SD.

** Penyelesaian **: Semak format fail dan keserasian model kamera.

### Model kamera tidak dikesan

** Kemungkinan Punca: **

* Mengubah metadata Exif.
* Imej diedit dalam perisian luaran.
* Pemindahan fail yang tidak lengkap.

** Penyelesaian **: Mengadakan semula fail yang tidak diubahsuai asal dari kamera atau kad SD.

### Timestamps hilang

** Kemungkinan Punca: **

* Jam kamera tidak betul ditetapkan
* Data EXIF ​​dikeluarkan oleh perisian luaran

** Penyelesaian **: Periksa bahawa tetapan masa kamera adalah betul semasa penangkapan.

***

## Langkah seterusnya

Setelah fail diimport:

1. ** Semak Senarai Fail ** - Pastikan semua imej telah dimuat naik dengan jayanya.
2. ** Semak Model Kamera **: Sahkan bahawa pengesanan kamera betul.
3. ** Periksa imej sasaran **: lihat [memilih imej sasaran] (memilih sasaran-imej.md).
4. ** Laraskan Tetapan **: Tetapkan pilihan pemprosesan dalam [Tetapan Projek] (menyesuaikan-projek-penyepit.md).
5. ** Mula Pemprosesan **: Lihat [Memulakan Pemprosesan] (permulaan-pemproses.md).

Untuk butiran mengenai tetapan projek, lihat [menyesuaikan Tetapan Projek] (menyesuaikan-projek-penyetempatan.md).