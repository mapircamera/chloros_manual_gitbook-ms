# Penanda Peta

Tab Peta meletakkan imej anda pada peta 2D interaktif berdasarkan koordinat GPS mereka. Ia memberikan anda gambaran geografi sesi tangkapan dan merupakan cara terpantas, sejurus selepas pengimportan, untuk membuang imej yang anda tidak mahu diproses.

<figure><img src="../.gitbook/assets/chloros_map_markers.gif" alt=""><figcaption></figcaption></figure>

## Mengakses Tab Peta

1. Buka atau buat projek dalam Chloros
2. Impor imej yang mengandungi metadata GPS
3. Klik tab **Peta** <img src="../.gitbook/assets/image (3) (1).png" alt="" data-size="line"> di bar sisi kiri
4. Peta memaparkan penanda di setiap lokasi GPS imej

{% hint style="info" %}
**GPS diperlukan**: hanya imej yang mempunyai koordinat GPS dalam metadata EXIF mereka akan muncul di peta. Imej tanpa koordinat masih ada dalam projek dan masih diproses seperti biasa — ia hanya tidak mempunyai penanda.
{% endhint %}

***

## Mengatur Imej daripada Tab Peta

Tab **Peta**<img src="../.gitbook/assets/image (3) (1).png" alt="" data-size="line"> mempunyai butang fail yang sama untuk menambah <img src="../.gitbook/assets/image (3).png" alt="" data-size="line"> <img src="../.gitbook/assets/image (1) (1).png" alt="" data-size="line"> dan membuang <img src="../.gitbook/assets/image (2) (1).png" alt="" data-size="line"> seperti tab [**Penyemak Imbas Fail**](../processing-images-gui/adding-files-to-a-project.md) <img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line">. Ia memaparkan senarai fail projek yang sama, dengan lajur geografi:

| Lajur        | Kandungan                                                           |
| ------------- | ------------------------------------------------------------------ |
| **Nama**      | Nama fail seperti yang dikeluarkan dari kamera                        |
| **Lintang**  | Darjah perpuluhan, enam perpuluhan tempat                            |
| **Bujur**    | Darjah perpuluhan, enam perpuluhan tempat                            |
| **Altitud** | Meter, satu perpuluhan — `-` apabila imej tidak mempunyai altitud |

{% hint style="info" %}
Klik mana-mana tajuk lajur untuk menyusun mengikutnya; klik sekali lagi untuk membalikkan susunan.
{% endhint %}

{% hint style="warning" %}
**Altitud ialah ketinggian di atas paras laut, bukan ketinggian di atas tanah.** Nilai ini diperoleh daripada tag EXIF `GPSAltitude` imej, yang dirujuk kepada paras laut rata-rata. Ia bukan ketinggian penerbangan di atas kawasan, dan Chloros tidak akan mengira jarak sampel tanah daripadanya — di atas padang yang 300 m di atas paras laut, sebuah dron pada 100 m AGL merekodkan kira-kira 400 m di sini. Gunakan lajur ini untuk mengesan nilai luar biasa dan mengesahkan ketinggian penerbangan yang konsisten, bukan sebagai ukuran AGL.
{% endhint %}

***

## Penanda Imej

Setiap imej dengan data GPS mendapat penanda pada koordinatnya.

### Paparan penanda

* Penanda terletak pada koordinat tepat yang direkodkan untuk setiap tangkapan
* Penanda yang rapat mungkin bertindih secara visual apabila zum keluar — zum masuk untuk memisahkannya
* Penanda yang dipilih dan diserlahkan dilukis di atas yang lain

### Pratonton tetikus terapung

* **Tetikus terapung** pada mana-mana penanda untuk memaparkan lakaran kecil imej tersebut dengan nama failnya
* **Klik**penanda untuk memilih imej dan**menancapkan** tetingkap timbul — ia akan kekal sehingga anda klik di tempat lain. Semasa tetingkap timbul ditancapkan, mengapungkan tetikus ke atas penanda lain tidak akan menutupnya
* Ini adalah cara terpantas untuk mencari satu bingkai tertentu dalam sesi yang besar tanpa meninggalkan peta

<figure><img src="../.gitbook/assets/image (36).png" alt=""><figcaption><p>Tab Peta memplot setiap imej bergeotag dalam projek</p></figcaption></figure>

### Super-zoom

{% hint style="success" %}
**SUPER-ZOOM**: apabila anda mencapai tahap zum maksimum yang disokong oleh penyedia jubin, zum masuk selanjutnya akan membesarkan jubin tersebut dan bukannya berhenti, supaya anda dapat memisahkan penanda yang terletak hampir bertindih.
{% endhint %}

* Super-zoom diaktifkan hanya apabila anda **di** tahap zum maksimum penyedia untuk lokasi tersebut dan jubin telah selesai dimuat. Di bawahnya, zum bertindak seperti biasa
* Julatnya ialah **1× hingga 32×** di atas tahap zum maksimum penyedia itu sendiri
* Penunjuk di penjuru memaparkan super-zoom semasa sebagai peratusan, dan butang **×** di sebelahnya membawa anda kembali ke zoom biasa dengan satu klik
* Mengecilkan zoom sentiasa disalurkan terus ke peta itu sendiri, jadi anda tidak akan terperangkap dalam super-zoom
* Membesar dan mengalih semasa super-zoom menghantar semula offset yang terhasil kepada peta, jadi kawasan di luar pusat yang anda alihkan terus meminta jubin dan bukannya menjadi kosong
* Penanda dilukis sebagai elemen vektor dan bukannya dirasterkan, jadi ia kekal tajam pada setiap tahap super-zoom

***

## Penyedia Jubin Peta

{% hint style="success" %}
**Pilihan automatik**: Chloros memilih perkhidmatan jubin yang menawarkan tahap zum terbaik untuk lokasi imej anda. Anda boleh menukarnya secara manual pada bila-bila masa.
{% endhint %}

| Penyedia        | Nota                                                                                                                                                             |
| --------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Peta Google** | Liputan global meluas; menyokong keempat-empat jenis jubin                                                                                                            |
| **Esri ArcGIS**| Gambar udara beresolusi lebih tinggi di kawasan tertentu. Jenis jubin**Terrain** tidak ditawarkan untuk Esri dan butangnya dilumpuhkan semasa Esri dipilih |***

## Jenis Jubin Peta

Pilih jenis lapisan peta dengan butang (dari kiri ke kanan):

![](&lt;../.gitbook/assets/image (14).png&gt;)

| Jenis                 | Menunjukkan                                                                |
| -------------------- | -------------------------------------------------------------------- |
| **Terrain**          | Bayangan ketinggian dengan butiran peta (jalan, label). Google sahaja       |
| **Map**              | Jubin peta jalan standard — pilihan jalur lebar terendah              |
| **Satelit**        | Imej satelit terperinci, tiada label — pilihan jalur lebar tertinggi |
| **Hibrid** (laluan) | Imej satelit dengan jalan raya dan label yang dilukis di atasnya                |

Tab Peta dibuka pada **Hibrid**. Pilihan anda akan dibawa semasa menukar penyedia jika penyedia tersebut menyokongnya.***

## Navigasi Peta

* **Zoom**: roda tatal tetikus, atau butang zum pada peta
* **Alih**: klik dan seret
* **Penuh Skrin**: kawalan penuh skrin mengembangkan peta ke seluruh tetingkap***

## Kes Penggunaan

### Semakan laluan penerbangan

* Lihat kawasan liputan sesi dron dengan sekilas pandang
* Kenal pasti celah di mana laluan terlepas
* Sahkan penerbangan mengikut corak yang dirancang

### Semakan tinjauan darat

* Lihat bagaimana tangkapan berasaskan darat diedarkan
* Lokasikan bingkai sasaran penentukuran berbanding kawasan tinjauan
* Putuskan di mana tangkapan tambahan diperlukan

### Kawalan kualiti

* Cari imej yang dirakam di tempat yang tidak dijangka dan buang sebelum pemprosesan
* Susun mengikut Altitud untuk mengesan bingkai yang dirakam pada ketinggian yang salah, atau yang penetapan GPS-nya lemah
* Sahkan lokasi imej dengan nota lapangan

***

## Penyelesaian Masalah

### Tiada penanda muncul

**Punca yang mungkin**

* Imej tidak mempunyai metadata GPS
* GPS dilumpuhkan pada kamera semasa mengambil gambar
* Data EXIF telah dipadam oleh perisian lain sebelum diimport

**Apa yang perlu dilakukan**: sahkan GPS diaktifkan pada kamera dan import semula fail asal. Anda boleh menyemak sama ada fail tertentu mempunyai koordinat dengan mencarinya dalam jadual fail tab Peta — imej tanpa koordinat tidak mempunyai baris di sana.

### Penanda berada di tempat yang salah

**Punca yang mungkin**: penetapan satelit yang buruk semasa pengambilan, atau pergeseran GPS semasa sesi.**Apa yang perlu dilakukan**: ini adalah isu semasa pengambilan dan bukannya sesuatu yang boleh diperbetulkan oleh Chloros selepas itu. Untuk kerja yang memerlukan ketepatan tinggi, gunakan aliran kerja GPS PPK/RTK — lihat tetapan**Terapkan Pembetulan PPK** dalam [Tetapan Projek](../project-settings/project-settings.md).

### Peta kosong atau jubin berhenti dimuat

Penyedia jubin adalah perkhidmatan dalam talian. Jika jubin berhenti sampai, semak sambungan rangkaian mesin, kemudian cuba menukar penyedia. Jika anda telah memperbesarkan gambar dengan melampau, tekan butang **×** tetapkan semula untuk kembali ke tahap zum biasa dan biarkan peta meminta semula jubin.***

## Halaman berkaitan

* [**Grid Imej**](image-grid.md) — set imej yang sama seperti imej kecil
* [**Membuka Imej Penuh Skrin**](opening-an-image-full-screen.md) — memeriksa satu imej dengan terperinci
* [**Menambah Fail ke Projek**](../processing-images-gui/adding-files-to-a-project.md) — butang tambah/buang fail yang dikongsi oleh tab ini
