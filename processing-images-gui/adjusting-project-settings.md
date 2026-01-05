# Melaraskan Tetapan Projek

Sebelum memproses imej anda, adalah penting untuk mengkonfigurasi tetapan projek anda agar sepadan dengan keperluan aliran kerja anda. Panel Tetapan Projek <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> menyediakan kawalan menyeluruh ke atas penentukuran, pilihan pemprosesan, indeks berbilang spektrum dan format eksport.

## Mengakses Tetapan Projek

1. Buka projek anda dalam Chloros
2. Klik ikon **Tetapan Projek** <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> di bar sisi kiri
3. Panel Tetapan Projek memaparkan semua pilihan konfigurasi

{% hint style="info" %}
**Tetapan disimpan secara automatik** dengan projek anda. Apabila anda membuka semula projek, semua tetapan dipulihkan.
Petua {% %}

***

## Persediaan Pantas untuk Aliran Kerja Biasa

### Tetapan Lalai (Disyorkan untuk Kebanyakan Pengguna)

Untuk aliran kerja kamera MAPIR biasa Survey3, tetapan lalai berfungsi dengan baik:

* ✅ **Pembetulan vignet**: Didayakan
* ✅ **Penentukuran pantulan**: Didayakan (memerlukan imej sasaran MAPIR)
* ✅ **Kaedah Debayer**: Kualiti Tinggi (Lebih Cepat)
* ✅ **Format eksport**: TIFF (16-bit)

Hanya import imej anda dan mula memproses dengan lalai ini.

***

## Gambaran Keseluruhan Tetapan Projek

Panel Tetapan Projek disusun ke dalam beberapa kategori. Di bawah adalah ringkasan setiap bahagian. Untuk dokumentasi lengkap, lihat [Tetapan Projek](../project-settings/project-settings.md).

### Pengesanan Sasaran

Mengawal cara Chloros mengenal pasti sasaran penentukuran dalam imej anda.

**Tetapan utama:*** **Kawasan sampel penentukuran minimum**: Ambang saiz untuk pengesanan sasaran (lalai: 25 piksel)
* **Pengkelompokan sasaran minimum**: Ambang kesamaan untuk mengumpulkan wilayah sasaran (lalai: 60)**Bila untuk melaraskan:**

* Tingkatkan kawasan sampel jika mendapat pengesanan palsu
* Kurangkan jika sasaran tidak dikesan
* Laraskan pengelompokan jika sasaran dipecahkan kepada berbilang pengesanan

### Memproses

Pilihan pemprosesan dan penentukuran imej utama.

**Tetapan utama:*** **Pembetulan vignet**: Mengimbangi kegelapan kanta di tepi ✅ Disyorkan
* **Penentukuran pantulan**: Menormalkan nilai menggunakan sasaran penentukuran ✅ Disyorkan
* **Kaedah Debayer**: Algoritma untuk menukar RAW kepada 3-saluran berbilang spektrum
* **Selang penentukuran semula minimum**: Masa antara menggunakan sasaran penentukuran (0 = gunakan semua)**Tetapan lanjutan:*** **Zon waktu sensor cahaya mengimbangi**: Untuk penyegerakan masa PPK (lalai: 0)
* **Gunakan pembetulan PPK**: Menggunakan data GPS/pin pendedahan daripada fail .daq
* **Pin Pendedahan 1/2**: Berikan kamera kepada pin pendedahan untuk persediaan dwi-kamera

### Indeks (Indeks Berbilangspek)

Konfigurasikan indeks tumbuh-tumbuhan untuk dikira dan dieksport.

**Cara menambah indeks:**

1. Klik butang**"Tambah indeks"**

2. Pilih indeks daripada menu lungsur (NDVI, NDRE, GNDVI, dsb.)
3. Konfigurasikan tetapan visualisasi (warna LUT, julat nilai)
4. Tambah berbilang indeks mengikut keperluan

**Indeks popular:*** **NDVI**: Kesihatan tumbuh-tumbuhan umum (paling biasa)
* **NDRE**: Pengesanan tekanan awal dengan RedEdge
* **GNDVI**: Kepekatan klorofil sensitif
* **OSAVI**: Berfungsi dengan baik dengan tanah yang boleh dilihat
* **EVI**: Kawasan indeks kawasan daun tinggi (LAI)**Formula tersuai (Chloros+ sahaja):**

* Cipta formula indeks berbilang spektrum tersuai
* Gunakan matematik band dengan semua saluran imej
* Simpan formula tersuai untuk digunakan semula

Untuk semua indeks dan formula yang tersedia, lihat [Formula Indeks Berbilang Spektrum](../project-settings/multispectral-index-formulas.md).

### Eksport

Mengawal format dan kualiti fail output.

**Format yang tersedia:*** **TIFF (16-bit)**: Disyorkan untuk GIS dan analisis saintifik (julat 0-65,535)
* **TIFF (32-bit, Peratus)**: Nilai pemantulan titik terapung (julat 0.0-1.0)
* **PNG (8-bit)**: Mampatan tanpa rugi untuk visualisasi (julat 0-255)
* **JPG (8-bit)**: Fail terkecil, mampatan lossy (julat 0-255)***

## Tetapan Menyimpan dan Memuatkan

### Simpan Templat Projek

Cipta templat boleh guna semula untuk aliran kerja yang konsisten:

1. Konfigurasikan semua tetapan yang dikehendaki dalam panel Tetapan Projek
2. Tatal ke bahagian **"Simpan Templat Projek"** di bahagian bawah
3. Masukkan nama templat deskriptif (cth., "Survey3N\_RGN\_Agriculture")
4. Klik ikon simpan

**Faedah:**

* Gunakan tetapan yang sama merentas berbilang projek
* Kongsi konfigurasi dengan ahli pasukan
* Kekalkan konsistensi untuk tinjauan berulang

### Muatkan Templat pada Projek Baharu

Apabila membuat projek baharu:

1. Pilih **"Projek Baharu"** daripada menu utama
2. Pilih pilihan **"Muat daripada templat"**

3. Pilih templat anda yang disimpan
4. Semua tetapan digunakan secara automatik

### Direktori Kerja

Tetapan **"Simpan Folder Projek"** menentukan tempat projek baharu dibuat secara lalai:

* **Lokasi lalai**: `C:\Users\[Username]\Chloros Projects`
* **Tukar lokasi**: Klik ikon edit dan pilih folder baharu
* **Bila untuk menukar**:
  * Pemacu rangkaian untuk kerjasama pasukan
  * Pemacu yang berbeza dengan lebih banyak ruang storan
  * Struktur folder tersusun mengikut tahun/pelanggan

***

## Persediaan PPK (Kinematik Pasca Diproses).

Jika menggunakan perakam MAPIR DAQ dengan GPS untuk geolokasi yang tepat:

### Prasyarat

* MAPIR DAQ dengan modul GPS (GNSS).
* Fail log .daq dengan entri pin pendedahan
* Kamera disambungkan ke pin pendedahan DAQ semasa sesi tangkapan

### Langkah Konfigurasi

1. Letakkan fail log .daq dalam folder projek anda
2. Dalam Tetapan Projek, dayakan kotak pilihan **"Gunakan pembetulan PPK"**

3. Tetapkan**"Zon waktu penderia cahaya mengimbangi"** jika perlu (lalai: 0 untuk UTC)
4. Tetapkan kamera pada pin pendedahan:
   * **Kamera tunggal**: Ditugaskan secara automatik kepada Pin 1
   * **Kamera dwi**: Tetapkan setiap kamera secara manual untuk membetulkan pin**Tugas Pin Pendedahan:*** **Pin Pendedahan 1**: Pilih model kamera daripada lungsur turun
* **Pin Pendedahan 2**: Pilih kamera kedua atau "Jangan Gunakan"
* Kamera yang sama tidak boleh diberikan kepada kedua-dua pin

{% gaya petunjuk="amaran" %}
**Penting**: Pin pendedahan mesti ditetapkan dengan betul pada kamera masing-masing. Tugasan yang salah akan mengakibatkan data geolokasi yang salah.
Petua {% %}

***

## Senario Lanjutan

### Projek Berbilang Kamera

Apabila memproses imej daripada berbilang kamera MAPIR dalam satu projek:

1. Chloros secara automatik mengesan setiap model kamera
2. Setiap kamera mendapat profil pemprosesan yang sesuai
3. PPK: Tetapkan setiap kamera secara manual untuk membetulkan pin pendedahan
4. Semua kamera menggunakan format dan indeks eksport yang sama

**Contoh**: Survey3W RGN + Survey3N OCN pelantar dwi-kamera

### Tinjauan Selang Masa atau Berbilang Tarikh

Untuk tinjauan berulang di kawasan yang sama dari semasa ke semasa:

1. Buat templat dengan tetapan standard anda
2. Gunakan persediaan sasaran penentukuran yang konsisten setiap sesi
3. Proses setiap tarikh sebagai projek berasingan
4. Gunakan tetapan yang sama untuk hasil yang setanding
5. Eksport dalam format yang sama untuk analisis temporal

### Set Data Besar

Untuk projek dengan banyak imej (500+):

* Pertimbangkan untuk memecahkan projek yang lebih kecil mengikut tarikh atau kawasan
* Gunakan Chloros+ pemprosesan selari untuk hasil yang lebih pantas
* Pertimbangkan CLI atau API untuk automasi kelompok
* Laraskan selang penentukuran semula minimum untuk mengurangkan masa pengesanan sasaran

***

## Mengesahkan Tetapan Anda

Sebelum mula memproses, semak tetapan utama ini:

* [ ] Model kamera dikesan dengan betul dalam Pelayar Fail
* [ ] Pembetulan vignet didayakan
* [ ] Penentukuran pantulan didayakan
* [ ] Sekurang-kurangnya satu imej sasaran penentukuran diimport
* [ ] Indeks berbilang spektrum yang dikehendaki ditambah
* [ ] Format eksport yang sesuai untuk aliran kerja anda
* [ ] Tetapan PPK dikonfigurasikan (jika menggunakan .daq dengan peristiwa pendedahan)

***

## Langkah Seterusnya

Setelah tetapan anda dikonfigurasikan:

1. **Tandakan imej sasaran penentukuran** - Lihat [Memilih Imej Sasaran](choosing-target-images.md)
2. **Mulakan pemprosesan** - Lihat [Memulakan Pemprosesan](starting-the-processing.md)
3. **Pantau kemajuan** - Lihat [Memantau Pemprosesan](monitoring-the-processing.md)

Untuk butiran lengkap tentang semua tetapan yang tersedia, lihat dokumentasi rujukan [Tetapan Projek](../project-settings/project-settings.md).