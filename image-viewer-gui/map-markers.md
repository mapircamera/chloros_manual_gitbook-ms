# Penanda Peta

Tab Peta memaparkan imej anda pada peta 2D interaktif berdasarkan koordinat GPS mereka. Ini memberikan gambaran keseluruhan geografi bagi sesi tangkapan anda dan membantu anda menggambarkan liputan spatial. Ia juga berguna apabila mula-mula mengimport imej anda untuk mengalih keluar mana-mana imej yang anda tidak perlu proses dengan cepat.

<figure><img src="../.gitbook/assets/chloros_map_markers.gif" alt=""><figcaption></figcaption></figure>

## Mengakses Tab Peta

1. Buka atau cipta projek dalam Chloros
2. Import imej yang mengandungi metadata GPS
3. Klik tab **Peta** <img src="../.gitbook/assets/image (3).png" alt="" data-size="line"> dalam bar sisi kiri
4. Peta akan memaparkan penanda pada setiap lokasi GPS imej

{% hint style="info" %}
**GPS Diperlukan**: Hanya imej dengan koordinat GPS terbenam dalam metadata EXIF mereka akan muncul pada peta. Pastikan kamera anda mendayakan GPS semasa tangkapan.
{% endhint %}

***

## Melaraskan Imej daripada Tab Peta

Tab **Map**<img src="../.gitbook/assets/image (3).png" alt="" data-size="line"> mempunyai penambahan yang sama <img src="../.gitbook/assets/image.png" XPROTX000020X0PROTX0XPROTX000020X0PROTX0XPROTX2TX000020X0PROTX01XPROTX000020X0PROTX0100020X0PROTX01XPROTX000020X0PROTX01XPROTX000020X0PROTX01XPROTX000020X0PROTX01XPROTX000020X0PROTX01XPROTX src="../.gitbook/assets/image (1).png" alt="" data-size="line"> dan alih keluar <img src="../.gitbook/assets/image (2).png" alt="" data-size="line" the** button [File assets/image (2).png"**Pelayar**](../processing-images-gui/adding-files-to-a-project.md) tab <img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line"> boleh. Ia juga menunjukkan senarai jadual fail projek yang sama tetapi dengan pengepala lajur yang berbeza:

### Nama Fail

* Nama fail asal daripada kamera
* Mengekalkan konvensyen penamaan kamera (cth., IMG\_0001.RAW)

### Latitud

* Latitud imej

### Longitud

* Longitud imej

### Ketinggian

* Ketinggian imej

{% hint style="info" %}
Mengklik pengepala lajur jadual juga mengisih data baris
{% endhint %}

***

## Penanda Imej

Setiap imej dengan data GPS diwakili oleh penanda pada peta:

### Paparan Penanda

* Penanda menunjukkan koordinat GPS yang tepat di mana setiap imej telah ditangkap
* Penanda berkelompok mungkin berkumpul bersama apabila dizum keluar
* Zum masuk untuk melihat lokasi imej individu

{% hint style="success" %}
SUPER-ZOOM: Apabila anda mencapai tahap zum maksimum daripada pembekal jubin peta, jubin kemudiannya dibesarkan apabila zum selanjutnya, membolehkan anda melihat penanda yang berdekatan.
{% endhint %}

### Pratonton Tuding

* **Tuding tetikus anda** pada mana-mana penanda untuk melihat pratonton lakaran kecil imej itu
* Ini membolehkan pengenalan visual pantas tanpa meninggalkan paparan peta
* Berguna untuk mencari imej tertentu dalam sesi tangkapan besar

***

## Pembekal Jubin Peta

{% hint style="success" %}
**Pemilihan Automatik**: Chloros secara automatik memilih perkhidmatan jubin yang menyediakan tahap zum terbaik untuk lokasi peta semasa anda. Anda boleh bertukar secara manual antara pembekal jika mahu.
{% endhint %}

Tab Peta menyokong dua pembekal jubin untuk imejan peta latar belakang:

### Peta Google

* Imej satelit dan peta standard daripada Google
* Terbaik untuk liputan umum di seluruh dunia

### ESRI

* Imej satelit dan udara daripada ESRI ArcGIS
* Selalunya memberikan imejan resolusi lebih tinggi di kawasan tertentu

***

## Jenis Jubin Peta

Anda boleh memilih jenis lapisan peta (dari kiri ke kanan):

&#x20;<img src="../.gitbook/assets/image (23).png" alt="" data-size="original">

### Rupa bumi

Menunjukkan profil ketinggian dan jubin peta dengan butiran (jalan raya, dll)

### Peta

Menunjukkan jubin peta standard (jalur lebar lebih rendah) dengan butiran (jalan raya, dll)

### Satelit

Menunjukkan jubin peta satelit terperinci (jalur lebar lebih tinggi).

### Hibrid

Menunjukkan jubin peta satelit dengan butiran tambahan (jalan raya, dll)

***

## Navigasi Peta

### Kawalan Zum

* **Zum Masuk/Keluar**: Gunakan roda skrol tetikus atau butang zum
* **Skrin penuh**: Skrin penuh peta

### Kawalan Kuali

* **Sorot**: Klik dan seret untuk bergerak di sekitar peta***

## Kes Penggunaan

### Visualisasi Laluan Penerbangan

* Lihat kawasan liputan sesi tangkapan dron
* Kenal pasti jurang dalam liputan imej
* Sahkan pelaksanaan laluan penerbangan

### Kajian Tinjauan Tanah

* Lihat taburan spatial tangkapan berasaskan darat
* Cari imej sasaran penentukuran berbanding dengan kawasan tinjauan
* Rancang lokasi tangkapan tambahan

### Kawalan Kualiti

* Mengenal pasti imej yang ditangkap dengan cepat di lokasi yang tidak dijangka
* Sahkan ketepatan GPS merentas set data
* Lokasi imej rujukan silang dengan nota lapangan

***

## Menyelesaikan masalah

### Tiada Penanda Muncul

**Punca yang mungkin:**

* Imej tidak mengandungi metadata GPS
* GPS telah dilumpuhkan pada kamera semasa tangkapan
* Data EXIF ​​dilucutkan oleh perisian luaran

**Penyelesaian**: Sahkan GPS didayakan pada kamera anda dan import semula fail asal

### Penanda di Lokasi yang Salah

**Punca yang mungkin:**

* GPS kamera mempunyai pembetulan satelit yang lemah
* GPS hanyut semasa tangkapan

**Penyelesaian**: Ini biasanya isu masa tangkapan; pertimbangkan untuk menggunakan GPS PPK/RTK untuk aplikasi ketepatan