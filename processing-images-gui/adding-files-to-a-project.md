# Menambah fail ke projek

Sebaik sahaja anda telah membuat atau membuka projek di kloros, langkah seterusnya adalah untuk menambah imej multispektral anda untuk memulakan pemprosesan. Penyemak imbas fail <img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line"> menjadikannya mudah untuk mengimport imej dan menguruskan dataset anda.

## Mengakses penyemak imbas fail

1. Buka atau buat projek di kloros
2. Klik penyemak imbas fail ** <img src="../.
3. Panel penyemak imbas fail akan memaparkan senarai fail projek anda

{% hint style="info" %}
** Jenis fail yang disokong **: Chloros menyokong fail imej RAW+JPG dan JPG dari MAPIR Survey3W dan Kamera Survey3N. Hanya RAW+JPG yang disyorkan.
{ % endhint %}

***

## Menambah gambar ke projek anda

Terdapat dua cara utama untuk menambah imej ke projek anda:

### Kaedah 1: Tambahkan fail

Gunakan pilihan ini untuk mengimport fail imej individu atau pemilihan fail kecil.

1. Klik butang ** "Tambah Fail" ** di bahagian atas panel penyemak imbas fail
2. Navigasi ke folder yang mengandungi gambar anda
3. Pilih satu atau lebih fail imej (tahan ** ctrl ** untuk memilih pelbagai fail)
4. Klik ** "Buka" ** untuk mengimport fail yang dipilih

### Kaedah 2: Tambah folder

Gunakan pilihan ini untuk mengimport semua imej dari folder sekaligus.

1. Klik butang ** "Tambah Folder" ** di bahagian atas panel penyemak imbas fail
2. Navigasi ke dan pilih folder yang mengandungi gambar sesi tangkapan anda
3. Klik ** "Pilih Folder" ** untuk mengimport semua imej yang disokong dari folder tersebut

***

## Memahami jadual penyemak imbas fail

Sebaik sahaja imej diimport, mereka muncul dalam jadual dengan lajur berikut:

### Thumbnail

* Pratonton kecil setiap gambar
* Klik Thumbnail untuk melihat imej penuh di kawasan pratonton utama

### Nama fail

* Nama fail asal dari kamera
* Mengekalkan konvensyen penamaan kamera (mis., IMG \ _0001.RAW)

### timestamp

* Tarikh dan masa gambar ditangkap
* Diekstrak dari imej Exif Metadata
* Digunakan untuk pengesanan sasaran penyegerakan dan penentukuran PPK

### Model kamera

* Konfigurasi kamera dan penapis secara automatik mengesan
* Contoh: survey3w \ _rgn, survey3n \ _ocn, survey3w \ _rgb
* Digunakan untuk menggunakan profil pemprosesan yang betul

### lajur sasaran (kotak semak)

* Periksa kotak ini untuk imej yang mengandungi sasaran penentukuran
* Sangat mempercepat pengesanan sasaran semasa pemprosesan
* Lihat [memilih imej sasaran](memilih sasaran-imej.md) untuk maklumat lanjut

***

## Menguruskan fail dalam projek anda

### Membuang fail

Untuk membuang gambar yang tidak diingini dari projek anda:

1. Pilih satu atau lebih imej dalam jadual penyemak imbas fail
2. Klik butang ** "Buang Terpilih" **
3. Sahkan penyingkiran (fail tidak dipadam dari cakera, hanya dikeluarkan dari projek)

### menyusun dan menapis

* **Sort mengikut lajur**: Klik mana -mana tajuk lajur untuk menyusun gambar
* **timestamp sort**: berguna untuk mengatur urutan penangkapan kronologi
* **Penapis Model Kamera**: Imej Kumpulan dengan Jenis Kamera Jika menggunakan pelbagai kamera

***

## Pratonton imej

### Melihat gambar penuh

Klik mana -mana gambar kecil imej dalam penyemak imbas fail untuk memaparkannya di kawasan pratonton utama:

1. Imej muncul di panel Pratonton Pusat
2. Gunakan kawalan zum untuk memeriksa butiran imej
3. Navigasi antara gambar menggunakan kekunci anak panah

### Navigasi cepat

* **gambar sebelumnya**: Klik anak panah kiri atau tekan ← Kunci
* **Imej Seterusnya**: Klik anak panah kanan atau tekan → kekunci
* **Zum masuk/keluar**: Gunakan butang roda tetikus atau zum
* **pan**: Klik dan seret pada imej ketika dizum masuk

***

## Pengendalian fail pendua

Chloros secara automatik mengesan dan mengabaikan fail pendua:

* Fail dengan nama fail yang sama dilangkau
* Menghalang pemprosesan dua kali ganda
* Mesej amaran dipaparkan apabila pendua dikesan

{% hint style="warning" %}
** PENTING **: Jangan ubah semula atau ubah suai fail imej asal anda sebelum mengimport. Chloros bergantung pada nama fail asal dan metadata untuk pemprosesan yang betul.
{ % endhint %}

***

## dataset kamera bercampur

Jika projek anda mengandungi imej dari pelbagai kamera Mapir:

1. Kloros secara automatik mengesan setiap model kamera
2. Setiap jenis kamera diproses dengan profil penentukuran yang sesuai
3. Pelayar fail memaparkan model kamera dalam lajur model kamera
4. Pemprosesan menggunakan tetapan yang betul untuk setiap jenis kamera

** Contoh Senario **: Survey3W RGN + Survey3n OCN Dual-Camera Persediaan

***

## Amalan terbaik

### mengatur sebelum import

* Simpan imej sasaran penentukuran dalam folder yang sama seperti gambar tinjauan
* Mengekalkan struktur folder asal dari kad kamera/SD anda
* Jangan campurkan dataset dari sesi yang berbeza dalam satu projek

### Fail penamaan

* Memelihara nama fail kamera asal (IMG \ _0001.raw, dll.)
* Jangan menamakan semula fail sebelum diimport
* Nama asal mengandungi metadata penting

### imej sasaran penentukuran

* Sentiasa sertakan imej sasaran penentukuran 1-2 setiap sesi
* Menangkap sasaran sebelum dan selepas sesi penangkapan
* Tempat sasaran dalam keadaan pencahayaan yang sama seperti kawasan penangkapan
* Tandakan imej sasaran menggunakan kotak semak sasaran untuk mempercepat pemprosesan

***

## Isu dan penyelesaian biasa

### gambar tidak muncul selepas import

** Kemungkinan Punca: **

* Format fail tidak disokong (hanya mentah+jpg dan jpg dari kamera mapir)
* Imej berasal dari kamera bukan mapir (lihat [kamera yang disokong](../ disokong-cameras.md))
* Fail rasuah atau pemindahan tidak lengkap dari kad SD

** Penyelesaian **: Sahkan format fail dan keserasian model kamera

### Model kamera tidak dikesan

** Kemungkinan Punca: **

* Metadata EXIF ​​yang diubah suai
* Imej diedit dalam perisian luaran
* Pemindahan fail tidak lengkap

** Penyelesaian **: Re-import asal, fail yang tidak diubahsuai dari kad kamera/sd

### Hilang timestamps

** Kemungkinan Punca: **

* Jam kamera tidak ditetapkan dengan betul
* Data EXIF ​​dilucutkan oleh perisian luaran

** Penyelesaian **: Sahkan tetapan masa kamera betul semasa penangkapan

***

## Langkah seterusnya

Setelah fail anda diimport:

1. ** Semak senarai fail ** - Pastikan semua imej dimuat dengan betul
2. ** Semak Model Kamera ** - Sahkan pengesanan kamera yang betul
3. ** Tandakan imej sasaran **-lihat [memilih imej sasaran](memilih sasaran-imej.md)
4. ** Laraskan Tetapan **-Konfigurasikan Pilihan Pemprosesan Dalam [Tetapan Projek](menyesuaikan-projek-penstabil.md)
5. ** Mula Pemprosesan **-Lihat [Memulakan Pemprosesan](permulaan-pemproses.md)

Untuk maklumat terperinci mengenai konfigurasi projek, lihat [Menyesuaikan Tetapan Projek](menyesuaikan-projek-penyelesaian.md).
