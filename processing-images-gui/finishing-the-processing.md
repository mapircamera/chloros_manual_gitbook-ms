# Menyelesaikan Pemprosesan

Setelah Chloros menyelesaikan pemprosesan, tiba masanya untuk menyemak hasil anda, mengesahkan kualiti output dan menyediakan imej yang diproses untuk digunakan dalam aliran kerja anda. Halaman ini membimbing anda melalui langkah terakhir dan tindakan seterusnya.

## Memproses Petunjuk Lengkap

Apabila pemprosesan selesai dengan jayanya, anda akan melihat beberapa penunjuk:

* ✅ **Bar kemajuan**: Mencapai 100% siap
* ✅ **Log Nyahpepijat**: Menunjukkan mesej "Pemprosesan Selesai".
* ✅ **Butang Mula**: Didayakan semula (bersedia untuk pemprosesan seterusnya)
* ✅ **Fail output**: Semua imej yang diproses disimpan ke subfolder model kamera***

## Mencari Imej Diproses Anda

### Membuka Folder Output

1. Klik ikon **Menu Utama** <img src="../.gitbook/assets/image (1) (1) (1) (1).png" alt="" data-size="line"> (kiri atas)
2. Pilih **"Buka Folder Projek"**

3. Penjelajah fail anda dibuka ke direktori projek
4. Cari projek anda mengikut nama

***

## Menyemak Imej Diproses

### Pratonton Pantas dalam Penjelajah Fail

**Windows pratonton terbina dalam:**

1. Navigasi ke subfolder model kamera
2. Pilih fail imej
3. Pratonton muncul dalam anak tetingkap pratonton Windows Explorer
4. Gunakan kekunci anak panah untuk menyemak imbas imej

### Pratonton dalam Pemapar Imej Luaran

**Penonton yang disyorkan:*** **QGIS** - Perisian GIS percuma (terbaik untuk analisis pelbagai spektrum georujukan)
* **IrfanView** - Pemapar imej yang pantas dan ringan (menyokong TIFF)
* **Adobe Photoshop** - Penyuntingan profesional (sokongan TIFF)
* **GIMP** - Alternatif percuma kepada Photoshop
* **Windows Photos** - Paparan asas (mungkin tidak menyokong 16-bit TIFF)

### Pratonton dalam Pemapar Imej Chloros

Gunakan Pemapar Imej terbina dalam Chloros untuk visualisasi lanjutan:

1. Klik lakaran kecil imej dalam Pelayar Fail
2. Imej dibuka dalam kawasan pratonton utama
3. Klik tab **Pemapar Imej** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> dalam bar sisi kiri
4. Gunakan [Index/LUT Sandbox](../image-viewer-gui/index-lut-sandbox.md) untuk analisis interaktif

Lihat [Pemapar Imej](../image-viewer-gui/opening-an-image-full-screen.md) untuk arahan terperinci.

***

## Menyemak Log Nyahpepijat

### Semak Amaran atau Ralat

1. Buka **Debug Log** tab <img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line">
2. Tatal melalui mesej
3. Cari amaran kuning atau ralat merah
4. Semak sebarang isu yang dinyatakan
5. Hubungi sokongan MAPIR untuk mendapatkan bantuan

### Menyimpan Log

Untuk menyimpan rekod pemprosesan atau menghantar kepada Sokongan MAPIR:

1. Klik butang **"Salin"**atau**"Muat Turun"**

2. Simpan sebagai fail teks dalam folder projek
3. Sertakan bersama dokumentasi projek
4. Hantar kepada sokongan MAPIR jika masalah dihadapi

***

## Isu dan Penyelesaian Output Biasa

### Isu: Fail Output Tiada

**Punca yang mungkin:**

* Fail tidak memenuhi kriteria pemprosesan
* Imej sasaran sahaja (dikecualikan daripada eksport)
* Ruang cakera kehabisan semasa eksport
* Fail rasuah semasa pemprosesan

**Penyelesaian:**

1. Semak Log Nyahpepijat untuk mesej langkau/ralat
2. Sahkan ruang cakera mencukupi
3. Kira fail: Harus sepadan (kiraan asal - kiraan sasaran) × (indeks + 1)
4. Import semula dan proses semula mana-mana fail yang hilang

### Isu: Tepi Gelap atau Cerah (Vignetting Masih Kelihatan)

**Punca yang mungkin:**

* Pembetulan vignet dilumpuhkan
* Kamera/lensa tiada dalam pangkalan data profil Chloros
* Vignetting melampau melebihi keupayaan pembetulan

**Penyelesaian:**

1. Sahkan pembetulan vignet telah didayakan dalam Tetapan Projek
2. Periksa model kamera dikesan dengan betul
3. Hubungi sokongan MAPIR jika vignetting berterusan

### Isu: Warna atau Nilai yang Salah

**Punca yang mungkin:**

* Tiada sasaran penentukuran dikesan
* Model sasaran penentukuran yang salah dipilih
* Penentukuran pantulan dinyahdayakan
* Imej sasaran berkualiti rendah

**Penyelesaian:**

1. Sahkan penentukuran pantulan telah didayakan
2. Semak mesej "Sasaran ditemui" dalam Log Nyahpepijat
3. Semak kualiti imej sasaran
4. Proses semula dengan sasaran yang betul ditanda

### Isu: NDVI Nilai Nampak Salah

**Julat NDVI dijangka:*** **Air, batu, tanah**: -0.1 hingga 0.2
* **Tumbuhan jarang/tidak sihat**: 0.2 hingga 0.4
* **Tumbuhan sederhana**: 0.4 hingga 0.6
* **Tumbuhan yang sihat dan tebal**: 0.6 hingga 0.9**Jika nilai berada di luar julat ini:**

1. Sahkan penentukuran pemantulan telah digunakan
2. Sahkan log sensor cahaya telah disertakan
3. Periksa sasaran penentukuran telah dikesan
4. Pastikan model kamera yang betul telah dikesan
5. Semak masa dan keadaan tangkapan imej sasaran

***

## Menggunakan Imej Diproses Anda

### Untuk Fotogrametri / Penciptaan Orthomosaic

**Aliran kerja yang disyorkan:**

1.**Import imej pemantulan yang ditentukur** ke dalam perisian fotogrametri:
   * Pix4Dmapper
   * Agisoft Metashape
   * DroneDeploy
   * WebODM
2. **Kekalkan metadata EXIF**: Pastikan data GPS disimpan untuk pengeteg geo
3. **Aliran kerja yang ditentukur**: Gunakan imej pemantulan untuk ketepatan saintifik
4. **Mozek indeks proses**: Cipta orthomosaik NDVI daripada imej indeks individu
5. **Eksport GeoTIFF**: Untuk digunakan dalam aplikasi GIS

### Untuk Analisis GIS

**Aliran kerja yang disyorkan:**

1.**Muat ke dalam QGIS, ArcGIS atau yang serupa**

2.**Gunakan 16-bit TIFF** imej pemantulan untuk analisis berbilang jalur
3. **Gunakan imej indeks** (NDVI, NDRE) sebagai lapisan tumbuh-tumbuhan sedia untuk digunakan
4. **Kalkulator raster**: Gabungkan jalur untuk analisis tersuai
5. **Eksport**: Cipta peta klasifikasi, pengesanan perubahan, peta kesihatan tumbuh-tumbuhan

### Untuk Analisis / Laporan Langsung

**Aliran kerja yang disyorkan:**

1.**Gunakan imej indeks dengan warna LUT** untuk laporan visual
2. **Statistik ekstrak**: Min NDVI setiap medan/plot
3. **Siri masa**: Bandingkan indeks merentas berbilang sesi
4. **Jana laporan**: Sertakan peta, statistik dan visualisasi***

## Pengarkiban dan Sandaran

### Disyorkan Strategi Sandaran

**Apa yang perlu disimpan:*** ✅ **Imej RAW/JPG asal** - Arkibkan pada pemacu/awan yang berasingan
* ✅ **Output yang diproses** - Simpan imej dan indeks yang ditentukur
* ✅ **Fail projek** - Mengandungi semua tetapan untuk pemprosesan semula jika perlu
* ✅ **Log Nyahpepijat** - Butiran pemprosesan dokumen
* ✅ **Imej sasaran penentukuran** - Untuk pengesahan dan pemprosesan semula**Cadangan storan:*** **Sandaran segera**: Pemacu keras luaran
* **Arkib jangka panjang**: Storan awan (Google Drive, Dropbox, dll.)
* **Data kritikal**: Simpan 2-3 salinan di lokasi yang berbeza***

## Pemprosesan Seterusnya Dijalankan

### Menggunakan Semula Tetapan Projek

Jika memproses set data yang serupa pada masa hadapan:

1. **Simpan Templat Projek** (jika belum selesai)
2. **Buat projek baharu** menggunakan templat yang disimpan
3. **Import imej baharu**

4.**Proses**dengan tetapan yang sama untuk konsistensi

### Memproses Kelompok Berbilang Sesi

Untuk berbilang sesi/set data:**Pilihan 1: GUI - Pelbagai Projek**

* Buat projek berasingan untuk setiap sesi
* Gunakan tetapan templat yang konsisten
* Proses satu demi satu

**Pilihan 2: Chloros CLI (Chloros+ sahaja)**

* Automasi pemprosesan kelompok
* Proses berbilang folder dengan skrip
* Lihat [Dokumentasi CLI](../CLI.md)

**Pilihan 3: Python SDK (Chloros+ sahaja)**

* Kawalan program
* Integrasi dengan saluran paip analisis
* Lihat [Dokumentasi API](../api-python-sdk.md)

***

## Menyelesaikan Masalah Selepas Pemprosesan

### Memproses Semula dengan Tetapan Berbeza

Jika keputusan tidak memuaskan:

1. Simpan imej asal (jangan sekali-kali padam)
2. Buka projek yang sama dalam Chloros
3. Laraskan tetapan dalam panel Tetapan Projek
4. Proses semula - output akan menulis ganti hasil sebelumnya

### Memproses Subset Imej

Untuk memproses semula imej tertentu sahaja:

1. Buat projek baharu
2. Import hanya imej yang memerlukan pemprosesan semula
3. Gunakan templat tetapan yang sama
4. Memproses set data yang lebih kecil

### Mendapatkan Bantuan

Jika anda menghadapi masalah:

* 📧 **E-mel**: info@mapir.camera (termasuk Log Nyahpepijat)
* 🌐 **Sokongan**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* 📚 **Soalan Lazim**: [Soalan Lazim](../faq.md)
* 📖 **Dokumentasi**: [Chloros Manual](../)***

## Ringkasan: Aliran Kerja Lengkap

Anda kini telah melengkapkan aliran kerja pemprosesan Chloros penuh:

1. ✅ **Projek dibuat** - Lihat [Projek](../projects.md)
2. ✅ **Tambah fail** - Lihat [Menambah Fail](adding-files-to-a-project.md)
3. ✅ **Tetapan terlaras** - Lihat [Melaraskan Tetapan Projek](adjusting-project-settings.md)
4. ✅ **Sasaran yang ditanda** - Lihat [Memilih Imej Sasaran](choosing-target-images.md)
5. ✅ **Mula pemprosesan** - Lihat [Memulakan Pemprosesan](starting-the-processing.md)
6. ✅ **Kemajuan dipantau** - Lihat [Memantau Pemprosesan](monitoring-the-processing.md)
7. ✅ **Hasil disemak** - Halaman ini**Imej multispektral anda yang ditentukur dan diperbetulkan pemantulan sedia untuk dianalisis!**

***

## Sumber Tambahan

### Ciri Lanjutan

* [**Pemapar Imej**](../image-viewer-gui/opening-an-image-full-screen.md) - Visualisasi dan analisis interaktif
* [**Kotak Pasir Indeks/LUT**](../image-viewer-gui/index-lut-sandbox.md) - Ujian indeks tersuai
* [**Rumus Indeks Berbilangspek**](../project-settings/multispectral-index-formulas.md) - Rujukan indeks lengkap

### Automasi & Integrasi

* [**CLI Documentation**](../CLI.md) - Pemprosesan kelompok baris perintah
* [**Python SDK**](../api-python-sdk.md) - Automasi program
* [**Chloros+ Ciri**](../#chloros) - Keupayaan pemprosesan lanjutan

### Sokongan & Pembelajaran

* [**Soalan Lazim**](../faq.md) - Soalan biasa dijawab
* [**Sasaran Penentukuran**](../calibration-targets.md) - Memahami penentukuran pantulan
* [**Kamera Disokong**](../supported-cameras.md) - Perkakasan yang serasi