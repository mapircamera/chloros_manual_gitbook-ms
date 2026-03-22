# Menambah Fail pada Projek

Setelah anda mencipta atau membuka projek dalam Chloros, langkah seterusnya ialah menambah imej berbilang spektrum anda untuk mula memproses. Tab Pelayar Fail<img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line"> memudahkan anda mengimport imej dan mengurus set data anda.

## Mengakses Pelayar Fail

1. Buka atau cipta projek dalam Chloros
2. Klik ikon **Pelayar Fail** <img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line"> di bar sisi kiri
3. Panel Pelayar Fail akan memaparkan senarai fail projek anda

{% hint style="info" %}
**Jenis Fail yang Disokong**: Chloros menyokong fail imej RAW+JPG dan JPG daripada kamera MAPIR Survey3W dan Survey3N. Hanya RAW+JPG disyorkan.
{% endhint %}

***

## Menambah Imej pada Projek Anda

Terdapat dua cara utama untuk menambah imej pada projek anda:

### Kaedah 1: Tambah Fail

Gunakan pilihan ini untuk mengimport fail imej individu atau pilihan kecil fail.

1. Klik butang **"Tambah Fail"** <img src="../.gitbook/assets/image.png" alt="" data-size="line"> di bahagian atas panel Penyemak Imbas Fail
2. Navigasi ke folder yang mengandungi imej anda
3. Pilih satu atau lebih fail imej (tahan **Ctrl** untuk memilih berbilang fail)
4. Klik **"Buka"** untuk mengimport fail yang dipilih

### Kaedah 2: Tambah Folder

Gunakan pilihan ini untuk mengimport semua imej dari folder sekaligus.

1. Klik butang **"Tambah Folder"** <img src="../.gitbook/assets/image (1).png" alt="" data-size="line"> di bahagian atas panel Penyemak Imbas Fail
2. Navigasi ke dan pilih folder yang mengandungi imej sesi tangkapan anda
3. Klik **"Pilih Folder"** untuk mengimport semua imej yang disokong daripada folder itu***

## Memahami Jadual Pelayar Fail

Setelah imej diimport, ia muncul dalam jadual dengan lajur berikut:

### Nama Fail

* Nama fail asal daripada kamera
* Mengekalkan konvensyen penamaan kamera (cth., IMG\_0001.RAW)

### Cap masa

* Tarikh dan masa imej ditangkap
* Diekstrak daripada metadata EXIF imej
* Digunakan untuk penyegerakan PPK dan pengesanan sasaran penentukuran

### Model Kamera

* Kamera dan konfigurasi penapis dikesan secara automatik
* Contoh: Survey3W\_RGN, Survey3N\_OCN, Survey3W\_RGB
* Digunakan untuk menggunakan profil pemprosesan yang betul

### Lajur Sasaran (Kotak Semak)

* Tandai kotak ini untuk imej yang mengandungi sasaran penentukuran
* Sangat mempercepatkan pengesanan sasaran semasa pemprosesan
* Lihat [Memilih Imej Sasaran](choosing-target-images.md) untuk butiran

### Melihat Metadata Imej

Mengklik butang togol di penjuru kanan sebelah atas di atas jadual menunjukkan metadata imej yang dipilih dalam kawasan grid imej.

<figure><img src="../.gitbook/assets/chloros_grid_meta.gif" alt=""><figcaption></figcaption></figure>

***

## Mengurus Fail dalam Projek Anda

### Mengalih keluar Fail

Untuk mengalih keluar imej yang tidak diingini daripada projek anda:

1. Pilih satu atau lebih imej dalam jadual Pelayar Fail
2. Klik butang **"Alih Keluar Dipilih"** <img src="../.gitbook/assets/image (2).png" alt="" data-size="line">
3. Sahkan penyingkiran (fail tidak dipadamkan daripada cakera, hanya dialih keluar daripada projek)

### Isih dan Penapisan

* **Isih mengikut lajur**: Klik mana-mana pengepala lajur untuk mengisih imej
* **Isih cap masa**: Berguna untuk mengatur urutan tangkapan kronologi
* **Penapis model kamera**: Himpunkan imej mengikut jenis kamera jika menggunakan berbilang kamera***

## Pratonton Imej

### Melihat Imej Penuh

Klik mana-mana imej kecil imej dalam Pelayar Fail untuk memaparkannya dalam kawasan pratonton utama:

1. Imej muncul dalam panel pratonton tengah
2. Gunakan kawalan zum untuk memeriksa butiran imej
3. Navigasi antara imej menggunakan kekunci anak panah

### Navigasi Pantas

* **Imej Sebelumnya**: Klik anak panah kiri atau tekan kekunci ←
* **Imej Seterusnya**: Klik anak panah kanan atau tekan kekunci →
* **Zum Masuk/Keluar**: Gunakan roda tetikus atau butang zum
* **Sorot**: Klik dan seret pada imej apabila dizum masuk***

## Pengendalian Fail Pendua

Chloros secara automatik mengesan dan mengabaikan fail pendua:

* Fail dengan nama fail yang sama dilangkau
* Mencegah pemprosesan dua kali secara tidak sengaja
* Mesej amaran dipaparkan apabila pendua dikesan

{% hint style="warning" %}
**Penting**: Jangan menamakan semula atau mengubah suai fail imej asal anda sebelum mengimport. Chloros bergantung pada nama fail dan metadata asal untuk pemprosesan yang betul.
{% endhint %}

***

## Set Data Kamera Bercampur

Jika projek anda mengandungi imej daripada berbilang kamera MAPIR:

1. Chloros secara automatik mengesan setiap model kamera
2. Setiap jenis kamera diproses dengan profil penentukuran yang sesuai
3. Pelayar Fail memaparkan model kamera dalam lajur Model Kamera
4. Pemprosesan menggunakan tetapan yang betul untuk setiap jenis kamera

**Senario contoh**: Survey3W RGN + Survey3N OCN persediaan dwi-kamera***

## Amalan Terbaik

### Susun Sebelum Import

* Simpan imej sasaran penentukuran dalam folder yang sama dengan imej tinjauan
* Kekalkan struktur folder asal daripada kamera/kad SD anda
* Jangan campurkan set data daripada sesi berbeza dalam satu projek

### Penamaan Fail

* Kekalkan nama fail kamera asal (IMG\_0001.RAW, dsb.)
* Jangan namakan semula fail sebelum diimport
* Nama asal mengandungi metadata penting

### Imej Sasaran Penentukuran

* Sentiasa sertakan 1-2 imej sasaran penentukuran setiap sesi
* Tangkap sasaran sebelum dan selepas sesi tangkapan
* Letakkan sasaran dalam keadaan pencahayaan yang sama seperti kawasan tangkapan
* Tandai imej sasaran menggunakan kotak semak Sasaran untuk mempercepatkan pemprosesan

***

## Isu dan Penyelesaian Biasa

### Imej Tidak Muncul Selepas Import

**Punca yang mungkin:**

* Format fail tidak disokong (hanya RAW+JPG dan JPG daripada kamera MAPIR)
* Imej adalah daripada kamera bukan MAPIR (lihat [Kamera Disokong](../supported-cameras.md))
* Fail rasuah atau pemindahan tidak lengkap daripada kad SD

**Penyelesaian**: Sahkan format fail dan keserasian model kamera

### Model Kamera Tidak Dikesan

**Punca yang mungkin:**

* Metadata EXIF yang diubah suai
* Imej disunting dalam perisian luaran
* Pemindahan fail tidak lengkap

**Penyelesaian**: Import semula fail asal yang tidak diubah suai daripada kamera/kad SD

### Cap Masa Tiada

**Punca yang mungkin:**

* Jam kamera tidak ditetapkan dengan betul
* Data EXIF dilucutkan oleh perisian luaran

**Penyelesaian**: Sahkan tetapan masa kamera adalah betul semasa tangkapan***

## Langkah Seterusnya

Setelah fail anda diimport:

1. **Semak senarai fail** - Pastikan semua imej dimuatkan dengan betul
2. **Semak model kamera** - Sahkan pengesanan kamera yang betul
3. **Tandai imej sasaran** - Lihat [Memilih Imej Sasaran](choosing-target-images.md)
4. **Laraskan tetapan** - Konfigurasikan pilihan pemprosesan dalam [Tetapan Projek](adjusting-project-settings.md)
5. **Mulakan pemprosesan** - Lihat [Memulakan Pemprosesan](starting-the-processing.md)

Untuk maklumat terperinci tentang konfigurasi projek, lihat [Melaraskan Tetapan Projek](adjusting-project-settings.md).