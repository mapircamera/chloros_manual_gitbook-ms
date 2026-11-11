# Menyiapkan Pemprosesan

Setelah Chloros menyiapkan pemprosesan, sudah tiba masanya untuk menyemak keputusan anda, mengesahkan kualiti keluaran, dan menyediakan imej yang telah diproses untuk digunakan dalam aliran kerja anda. Halaman ini membimbing anda melalui langkah-langkah akhir dan tindakan seterusnya.

## Petunjuk Selesai Pemprosesan

Apabila pemprosesan berjaya disiapkan, anda akan melihat beberapa petunjuk:

* ✅ **Bar kemajuan**: Mencapai 100% siap
* ✅ **Logs Debug**: Menunjukkan baris `[RUN-SUMMARY]` terakhir dengan kiraan (imej, kumpulan kamera, sasaran, imej kalibrasi, fail yang ditulis)
* ✅ **Butang Mula**: Diaktifkan semula (sedia untuk larian pemprosesan seterusnya)
* ✅ **Fail keluaran**: Semua imej yang diproses disimpan ke dalam pokok keluaran projek (di bawah)

{% hint style="warning" %}
**Sebuah proses yang tidak menulis sebarang imej adalah satu kegagalan.** Jika anda meminta produk imej dan pelaksanaan tidak menulis sebarang imej, Chloros melaporkannya sebagai kegagalan — petunjuk `[RUN-SUMMARY]` dalam nama log menunjukkan punca yang mungkin (tiada yang diimport, tiada sasaran dikesan, atau setiap produk yang diminta diabaikan kerana tidak terpakai). Setara CLI keluar dengan nilai bukan sifar. Sebuah larian khusus hanya untuk metadata (semua produk eksport dimatikan, tiada indeks) masih dikira berjaya. Rujuk [Rujukan [CLI]](../reference/cli-reference.md#a-run-that-writes-no-images-fails).
{% endhint %}

***

## Menemui Imej Anda yang Telah Diproses

### Membuka Folder Keluaran

1. Klik ikon **Menu Utama** <img src="../.gitbook/assets/image (1) (1) (1) (1).png" alt="" data-size="line">
(atas kiri)
2. Pilih **"Buka Folder Projek"**

3. Pengimbas fail anda akan dibuka ke direktori projek
4. Lokasikan projek anda mengikut nama

### Struktur Output

Produk disimpan **di bawah folder projek, dikumpulkan mengikut kamera dan kemudian mengikut format fail**:

```
<project>/
└── LATT-M3M-L41-F550/                  # one folder per camera
    ├── tiff16/
    │   ├── Reflectance_Calibrated_Images/
    │   ├── Debayered_Images/
    │   ├── Preview_Images/
    │   └── NDVI_Index_Images/           # one folder per selected index
    └── tiff32/
        └── Radiance_Images/             # float32 radiance always lands here
```

* **Folder kamera**: `LATT-<sensor>-<lens>-F<filter>` untuk LATTICE (menyesuaikan dengan EXIF tangkapan `Model`), `<model>_<filter>` untuk Survey3 (contohnya `Survey3N_RGN`). Dua kamera yang berkongsi sensor dan penapis tetapi berbeza dari segi lensa mengekalkan pokok berasingan — vignette, medan pandangan dan distorsi berbeza.
* **Format folder**: mengikut tetapan format eksport anda — `tiff16`, `tiff8`, `png8`, `jpg8`, atau `tiff32` untuk TIFF (32-bit, Peratus). Radiance sentiasa float32 dan sentiasa berada di bawah `tiff32`.
* **Folder produk**:
  * `Reflectance_Calibrated_Images/` — pantulan kalibrasi
  * `Debayered_Images/` — linear debayered (LATTICE): offset gelap dikeluarkan dan demosaic, tanpa flat field atau unmix, dalam orientasi asal sensor
  * `Preview_Images/` — pratonton paparan (LATTICE)
  * `Radiance_Images/` — radiasi spektral float32, W/m²/sr/nm (LATTICE multispektral)
  * `Vignette_Corrected_Images/` **atau** `Sensor_Response_Images/` — nilai lalai tanpa kalibrasi untuk bingkai tanpa rujukan pantulan; hanya satu daripada kedua-duanya wujud bagi setiap pelaksanaan, dipilih oleh tetapan pembetulan Vignette
  * `<INDEX>_Index_Images/` — satu folder bagi setiap indeks yang dipilih (contohnya `NDVI_Index_Images`)
* **`clamp_masks/`** (baru dalam 1.3.0, hanya untuk kamera multispektral M3C LATTICE): muncul dalam folder format, di sebelah folder produk, apabila nilai negatif yang dipotong dalam pencampuran semula jalur diubah kepada sifar dalam produk radiasi atau pantulan. Ia mengandungi satu topeng bagi setiap piksel bagi setiap produk yang terjejas, contohnya `tiff16/clamp_masks/Reflectance_Calibrated_Images/<name>_clampmask.tif`. Topeng tidak pernah dimasukkan ke dalam folder produk, jadi folder produk boleh diberikan kepada perisian fotogrametri seperti sedia ada. Lihat [Clamp masks](../output-image-formats.md#clamp-masks).

{% hint style="info" %}
**Setiap produk yang dieksport mengekalkan nama fail SUMBER.**Eksport radiasi `capture_..._raw.tif` masih dipanggil `capture_..._raw.tif` — ia hanya terletak di `tiff32/Radiance_Images/`.**Papan nama mengenal pasti produk, bukan nama fail**, jadi mencari `*radiance*.tif` tidak akan menemui apa-apa; padankan pada direktori sebaliknya.
{% endhint %}

<!-- SCREENSHOT-NEEDED: Windows Explorer open on a processed project folder showing the tree: a LATT-M3C multispectral camera folder expanded with tiff16 (Reflectance_Calibrated_Images, Debayered_Images, Preview_Images, NDVI_Index_Images, clamp_masks) and tiff32 (Radiance_Images, clamp_masks) subfolders visible -->


### Berapa Banyak Fail yang Perlu Ada?

Jangan kira menggunakan formula — bilangan keluaran bergantung pada produk yang diaktifkan dan yang terpakai untuk setiap kamera (contohnya, kamera RGB tidak mendapat sinaran/pantulan). Bilangan yang sah terdapat dalam log: baris terakhir `[RUN-SUMMARY]` melaporkan dengan tepat berapa banyak fail yang telah ditulis, dan baris petunjuk menerangkan apa sahaja yang telah dilangkau.

***

## Menyemak Imej yang Diproses

### Pratonton Pantas di File Explorer

**Pratonton terbina dalam Windows:**

1. Layari ke dalam folder produk (contohnya `tiff16/Reflectance_Calibrated_Images/`)
2. Pilih fail imej
3. Pratonton akan muncul di panel pratonton Peneroka Windows

4. Gunakan kekunci anak panah untuk menelusuri imej

### Pratonton dalam Pemapar Imej Luaran

**Pemapar yang disyorkan:*** **QGIS** - Perisian GIS percuma (terbaik untuk analisis multispektral bergeoreferens)
* **IrfanView** - Pemapar imej pantas dan ringan (menyokong TIFF)
* **Adobe Photoshop** - Penyuntingan profesional (menyokong TIFF)
* **GIMP** - Alternatif percuma kepada Photoshop
* **Windows Photos** - Pemaparan asas (mungkin tidak menyokong TIFF 16-bit)

### Pratonton di Pemapar Imej Chloros


Gunakan Pemapar Imej terbina dalam Chloros untuk visualisasi lanjutan:

1. Klik lakaran kecil imej di Pelayar Fail
2. Imej dibuka di kawasan pratonton utama
3. Klik tab **Pemapar Imej** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line">
di bar sisi kiri
4. Gunakan [Index/LUT Sandbox](../image-viewer-gui/index-lut-sandbox.md) untuk analisis interaktif

Lihat [Image Viewer](../image-viewer-gui/opening-an-image-full-screen.md) untuk arahan terperinci.

***

## Membaca Nilai Pigmen Pantulan (GIS / Pix4D / Skrip)

Reflectansi disimpan sebagai DN integer, dan **DN yang bermaksud ρ = 1.0 bergantung pada kamera sumber**:

| Sumber          | ρ = 1.0 adalah | Cara untuk mengetahui                                        |
| --------------- | ---------- | -------------------------------------------------- |
| LATTICE (M3C/M3M) | **32768** (ruang lebih sehingga ρ 2.0) | Tag XMP `Chloros:PixelScale=32768` dicop pada fail |
| Survey3          | **65535** (dipotong pada ρ 1.0)     | Tiada tag `Chloros:PixelScale` — ketiadaan itu adalah isyarat (produk Survey3 membawa tag pemprosesan `Chloros:*` lain) |

Baca tag `Chloros:PixelScale` dan bahagikannya dengannya** daripada menganggap secara seragam 65535 — membahagikan pantulan LATTICE dengan 65535 secara senyap akan mengurangkan setiap nilai kepada separuh. Fail reflektansi daripada versi sebelum 1.3.0 yang ditulis sebagai 8-bit telah dipotong (clipped), bukan diskala semula, dan tiada tag skala: eksport semula ia pada 16-bit atau 32-bit dan bukannya membahagikannya. Lihat [Format Imej Keluaran](../output-image-formats.md) untuk maklumat penuh.***

## Metadata yang Dibawa ke dalam Eksport

Setiap produk mengekalkan **blok GPS**sumber tangkapan dan**sub-IFD EXIF**nya, jadi sebuah
eksport membawa `FocalLength`, `FNumber`, `ExposureTime`, `ISO`, `DateTimeOriginal` dan `CameraSerialNumber` serta georujukan.

{% hint style="warning" %}
**Jika ortomosaik terhasil pada skala yang tidak munasabah, semak `FocalLength` terlebih dahulu.**
Pix4D menyelesaikan jarak sampel tanah daripada panjang fokus ditambah ketinggian. Tanpa tag itu ia kembali kepada skala yang sangat salah — pada satu penerbangan 49 tangkapan yang diukur, 411 m × 160 m
ladang oren dibina semula sebagai 47.8 km × 13 km, menghasilkan orto 455 megapiksel yang kebanyakannya ruang kosong. Proses penilean yang perlahan dan fail yang tidak dijangka besar adalah simptom masalah ini, bukan masalah berasingan.

```bash
exiftool -FocalLength -GPSLatitude "YourProject/.../some_export.tif"
```
{% endhint %}

Tidak semua tag disalin. Tag struktur IFD0 sengaja tidak disertakan (menyalinnya merosakkan keluaran LATTICE), dan `ExifImageWidth` / `ExifImageHeight` dikecualikan kerana ia menerangkan tangkapan asal — eksport yang bersaiz semula sebaliknya akan mendakwa dimensi yang bertentangan dengan rasternya sendiri.

Chloros juga menulis tag XMP `Chloros:*` tersendiri yang menerangkan apa yang telah dilakukan pada setiap produk
(contohnya `Chloros:ProcessingLevel`, `Chloros:CalibrationApplied`,
`Chloros:VignetteCorrected`). Mulai versi 1.3.0, produkSurvey3
membawanya dalam setiap pemprosesan
mod, produk indeks menerangkan indeks itu sendiri (`Chloros:ProcessingLevel=index`, formula, dan sama ada ia dikira daripada pantulan atau sinaran) daripada mengulangi tag sumber mentah, dan produk dalam folder yang lebih dalam daripada 260 aksara mengekalkan metadata mereka juga. Lihat [Format Imej Keluaran](../output-image-formats.md#index-products).

***

## Menyemak Log Ralat

### Semak Amaran atau Ralat

1. Buka tab **Debug Log** <img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line">

2. Layari mesej
3. Cari amaran kuning atau ralat merah
4. Baca baris `[RUN-SUMMARY]` dan sebarang petunjuk
5. Hubungi sokongan MAPIR untuk bantuan

### Menyimpan Log

Untuk menyimpan rekod pemprosesan atau untuk menghantar kepada Sokongan MAPIR:

1. Klik butang **"Salin"**atau**"Muat turun"**

2. Simpan sebagai fail teks dalam folder projek
3. Sertakan bersama dokumentasi projek
4. Hantar kepada sokongan MAPIR jika menghadapi masalah

Untuk menghantar masalah terus ke MAPIR, gunakan ikon pepijat (**Laporkan pepijat**) di bahagian atas tab Log Ralat — lihat [Melaporkan pepijat daripada tab log](monitoring-the-processing.md#reporting-a-bug-from-the-log-tab).***

## Isu Keluaran Biasa dan Penyelesaian

### Isu: Fail Keluaran Hilang

**Punca yang mungkin:**

* Produk itu tidak terpakai untuk kamera tersebut (contohnya, radiasi/reflektan untuk kamera RGB — log menyatakan demikian)
* Rujukan yang diperlukan hilang (contohnya pantulan tanpa sasaran dan tiada `.daq` downwelling)
* Kotak semak eksport produk dilumpuhkan dalam Tetapan Projek
* Ruang cakera habis semasa eksport

**Penyelesaian:**

1. Semak petunjuk `[RUN-SUMMARY]` dan baris `[EXPORT-CHECK]` dalam Log Ralat — ia menerangkan tentang langganan bagi setiap kamera
2. Semak kotak semak produk eksport dalam [Project Settings](adjusting-project-settings.md)
3. Semak ruang cakera mencukupi
4. Proses semula selepas membetulkan puncanya

### Isu: Tepi Gelap atau Terang (Vignetting Masih Nampak)

**Punca yang mungkin:**

* Pembetulan vignet dilumpuhkan
* Kamera/lensa tidak dalam pangkalan data profil Chloros

* Vignetting melampau melebihi keupayaan pembetulan

**Penyelesaian:**

1. Semak sama ada pembetulan vignet diaktifkan dalam Tetapan Projek
2. Semak sama ada model kamera dikesan dengan betul
3. Hubungi sokongan MAPIR jika vignetting berterusan

### Isu: Warna atau Nilai yang Tidak Betul

**Punca yang mungkin:**

* Tiada sasaran penentukuran dikesan
* Model sasaran penentukuran yang salah dipilih
* Penentukuran pantulan dilumpuhkan
* Imej sasaran berkualiti rendah

**Penyelesaian:**

1. Semak sama ada penentukuran pantulan telah diaktifkan
2. Semak mesej "Target found" dalam Log Ralat
3. Semak kualiti imej sasaran
4. Proses semula dengan sasaran yang betul ditandakan

### Isu: Nilai NDVI Nampak Tidak Betul

Julat NDVI yang dijangkakan:

* **Air, batu, tanah**: -0.1 hingga 0.2
* **Tumbuhan jarang/tidak sihat**: 0.2 hingga 0.4
* **Tumbuhan sederhana**: 0.4 hingga 0.6
* **Tumbuhan yang sihat dan lebat**: 0.6 hingga 0.9**Jika nilai berada di luar julat ini:**

1. Semak sama ada penentukuran pantulan telah diterapkan
2. Semak sama ada log penderia cahaya disertakan
3. Semak sama ada sasaran penentukuran dikesan
4. Pastikan model kamera yang betul dikesan
5. Semak masa dan keadaan pengambilan imej sasaran
6. Jika anda mengira indeks sendiri daripada fail reflektansi, pastikan anda membahagikan dengan `Chloros:PixelScale` fail tersebut (lihat di atas)

***

## Menggunakan Imej Anda yang Telah Diproses

### Untuk Fotogrametri / Penciptaan Orto-mozaik

**Aliran kerja yang disyorkan:**

1.**Import imej pantulan yang telah dikalibrasi** ke dalam perisian fotogrametri:
   * Pix4Dmapper
   * Agisoft Metashape
   * DroneDeploy
   * WebODM
2. **Simpan metadata EXIF**: Pastikan data GPS terpelihara untuk penandaan geografi
3. **Aliran kerja yang dikalibrasi**: Gunakan imej pantulan untuk ketepatan saintifik — pantulan LATTICE membawa tag kalibrasi XMP yang dibaca oleh Pix4D
4. **Proses mozek indeks**: Cipta ortomozek NDVI daripada imej indeks individu
5. **Eksport GeoTIFF bergeoreferens**: Untuk kegunaan dalam aplikasi GIS

### Untuk Analisis GIS

**Aliran kerja yang disyorkan:**

1.**Muat ke dalam QGIS, ArcGIS, atau yang serupa**

2.**Gunakan imej pantulan TIFF 16-bit untuk analisis berbilang jalur (bahagikan dengan `Chloros:PixelScale` fail tersebut)**

3.**Gunakan imej indeks** (NDVI, NDRE) sebagai lapisan vegetasi siap guna
4. **Pengira raster**: Gabungkan jalur untuk analisis tersuai
5. **Eksport**: Buat peta pengelasan, pengesanan perubahan, peta kesihatan vegetasi

### Untuk Analisis Langsung / Pelaporan

**Aliran kerja yang disyorkan:**

1.**Gunakan imej indeks dengan warna LUT** untuk laporan visual
2. **Ekstrak statistik**: Purata NDVI bagi setiap lapangan/plot
3. **Siri masa**: Bandingkan indeks merentasi pelbagai sesi
4. **Jana laporan**: Sertakan peta, statistik, dan visualisasi***

## Pengarkiban dan Sandaran

### Strategi Sandaran yang Disyorkan

**Apa yang perlu disimpan:*** ✅ **Imej asal RAW/JPG atau tangkapan mentah LATTICE** - Arkibkan pada pemacu/awan berasingan; mentah adalah sumber saluran paip dan segala-galanya yang lain boleh dijana semula daripadanya
* ✅ **Fail penderia cahaya `.daq` / `.csv`** - Diperlukan untuk menentukan semula pantulan kemudian
* ✅ **Keluaran yang diproses** - Simpan imej dan indeks yang telah dikalibrasi
* ✅ **Folder projek** (`project.json` dan rakan-rakannya) - Mengandungi semua tetapan untuk pemprosesan semula jika perlu
* ✅ **Log Ralat** - Mencatat butiran pemprosesan
* ✅ **Imej sasaran penentukuran** - Untuk pengesahan dan pemprosesan semula**Cadangan penyimpanan:*** **Sandaran segera**: Cakera keras luaran
* **Arkib jangka panjang**: Penyimpanan awan (Google Drive, Dropbox, dll.)
* **Data kritikal**: Simpan 2-3 salinan di lokasi yang berbeza***

## Jalankan Pemprosesan Seterusnya

### Menggunakan Semula Tetapan Projek

Jika memproses set data yang serupa pada masa hadapan:

1. **Simpan Templat Projek** (jika belum dilakukan)
2. **Buat projek baru** menggunakan templat yang disimpan
3. **Import imej baru**

4.**Proses**dengan tetapan yang sama untuk keseragaman

### Pemprosesan Batch untuk Pelbagai Sesi

Untuk pelbagai sesi/set data:**Pilihan 1: GUI - Pelbagai Projek**

* Buat projek berasingan untuk setiap sesi
* Gunakan tetapan templat yang konsisten
* Proses satu persatu

**Pilihan 2: Chloros CLI (Chloros+ sahaja)**

* Automasi pemprosesan pukal
* Proses berbilang folder dengan skrip
* Lihat [Dokumentasi CLI](../CLI.md) dan [Rujukan CLI](../reference/cli-reference.md)

**Pilihan 3: Python SDK (Hanya Chloros+)**

* Kawalan berprogram
* Integrasi dengan saluran analisis
* Lihat [Dokumentasi API](../api-python-sdk.md) dan [Rujukan SDK](../reference/sdk-reference.md)

***

## Penyelesaian Masalah Pascemprosesan

### Memproses Semula dengan Tetapan Berbeza

Jika keputusan tidak memuaskan:

1. Simpan imej asal (jangan padam)
2. Buka projek yang sama dalam Chloros
3. Laraskan tetapan dalam panel Tetapan Projek
4. Proses semula — keluaran akan berada dalam folder produk yang sama, jadi fail dengan nama yang sama daripada larian sebelumnya akan digantikan

### Memproses Subset Imej

Untuk memproses semula hanya imej tertentu:

1. Buat projek baru
2. Import hanya imej yang perlu diproses semula
3. Gunakan templat tetapan yang sama
4. Proses set data yang lebih kecil

### Memperoleh Bantuan

Jika anda menghadapi masalah:

* 📧 **E-mel**: info@mapir.camera (sertakan Log Ralat)
* 🌐 **Sokongan**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* 📚 **Soalan Lazim**: [Soalan yang sering ditanya](../faq.md)
* 📖 **Dokumentasi**: [Manual Chloros](../)

***

## Ringkasan: Aliran Kerja Lengkap

Anda kini telah melengkapkan keseluruhan aliran kerja pemprosesan Chloros:

1. ✅ **Mencipta projek** - Lihat [Projek](../projects.md)
2. ✅ **Menambah fail** - Lihat [Menambah Fail](adding-files-to-a-project.md)
3. ✅ **Melaras tetapan** - Lihat [Melaras Tetapan Projek](adjusting-project-settings.md)
4. ✅ **Menandakan sasaran** - Lihat [Memilih Imej Sasaran](choosing-target-images.md)
5. ✅ **Memulakan pemprosesan** - Lihat [Memulakan Pemprosesan](starting-the-processing.md)
6. ✅ **Memantau kemajuan** - Lihat [Memantau Pemprosesan](monitoring-the-processing.md)
7. ✅ **Menyemak keputusan** - Halaman ini**Imej multispektral anda yang telah dikalibrasi dan diperbetulkan pantulan sudah sedia untuk dianalisis!**

***

## Sumber Tambahan

### Ciri Lanjutan

* [**Pemerhati Imej**](../image-viewer-gui/opening-an-image-full-screen.md) - Visualisasi dan analisis interaktif
* [**Sandbox Indeks/LUT**](../image-viewer-gui/index-lut-sandbox.md) - Ujian indeks tersuai
* [**Formula Indeks Multispektral**](../project-settings/multispectral-index-formulas.md) - Rujukan indeks lengkap

### Automasi & Integrasi

* [**Dokumentasi CLI**](../CLI.md) - Pemprosesan pukal baris perintah
* [**Python SDK**](../api-python-sdk.md) - Automasi berprogram
* [**Ciri-ciri Chloros+**](../#chloros) - Keupayaan pemprosesan lanjutan

### Sokongan & Pembelajaran

* [**Soalan Lazim**](../faq.md) - Soalan biasa dijawab
* [**Sasaran Penentukuran**](../calibration-targets.md) - Memahami penentukuran pantulan
* [**Kamera Disokong**](../supported-cameras.md) - Perkakasan serasi
