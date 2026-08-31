# Menyiapkan Pemprosesan

Setelah pemprosesan diChloros
selesai, sudah tiba masanya untuk menyemak keputusan anda, mengesahkan kualiti keluaran, dan menyediakan imej yang telah diproses untuk digunakan dalam aliran kerja anda. Halaman ini membimbing anda melalui langkah-langkah akhir dan tindakan seterusnya.

## Petunjuk Pemprosesan Selesai

Apabila pemprosesan selesai dengan jayanya, anda akan melihat beberapa petunjuk:

* ✅ **Bar kemajuan**: Mencapai 100% siap
* ✅ **Log Ralat**: Menunjukkan baris `[RUN-SUMMARY]` terakhir dengan kiraan (imej, kumpulan kamera, sasaran, imej dikalibrasi, fail yang ditulis)
* ✅ **Butang Mula**: Diaktifkan semula (sedia untuk larian pemprosesan seterusnya)
* ✅ **Fail keluaran**: Semua imej yang diproses disimpan ke dalam pokok keluaran projek (di bawah)

{% hint style="warning" %}
**Sebarang pelaksanaan yang tidak menulis sebarang imej adalah satu kegagalan.** Jika anda meminta produk imej tetapi pelaksanaan tidak menulis sebarang imej,Chloros
akan melaporkannya sebagai kegagalan — petunjuk `[RUN-SUMMARY]` dalam nama log menunjukkan punca yang mungkin (tiada yang diimport, tiada sasaran dikesan, atau setiap produk yang diminta diabaikan kerana tidak terpakai). SetaraCLI
keluar dengan nilai bukan sifar. Pelaksanaan khusus hanya untuk metadata (semua produk eksport dimatikan, tiada indeks) masih dianggap berjaya. Rujuk [RujukanCLI
](../reference/cli-reference.md#a-run-that-writes-no-images-fails).
{% endhint %}

***

## Menemui Imej Anda yang Telah Diproses

### Membuka Folder Keluaran

1. Klik ikon **Menu Utama**<img src="../.gitbook/assets/image (1) (1) (1) (1).png" alt="" data-size="line">
(atas kiri)
2. Pilih **&quot;Buka Folder Projek&quot;**

3. Pengimbas fail anda akan dibuka ke direktori projek
4. Cari projek anda mengikut nama

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

* **Folder kamera**: `LATT-<sensor>-<lens>-F<filter>` untuk LATTICE (menyesuaikan dengan EXIF tangkapan `Model`), `<model>_<filter>` untukSurvey3
(contohnya `Survey3N_RGN`). Dua kamera yang berkongsi sensor dan penapis tetapi berbeza dari segi lensa mengekalkan pokok yang berasingan — vignette, medan pandangan dan distorsi berbeza.
* **Format folder**: mengikut tetapan format eksport anda — `tiff16`, `tiff8`, `png8`, `jpg8`, atau `tiff32` untukTIFF
(32-bit, Peratus). Radiance sentiasa float32 dan sentiasa berada di bawah `tiff32`.
* **Folder produk**:
  * `Reflectance_Calibrated_Images/` — pantulan kalibrasi
  * `Debayered_Images/` — debayered linear (LATTICE)
  * `Preview_Images/` — pratonton paparan (LATTICE)
  * `Radiance_Images/` — sinaran spektral float32, W/m²/sr/nm (LATTICE multispektral)
  * `Vignette_Corrected_Images/` **atau** `Sensor_Response_Images/` — nilai sandaran tanpa kalibrasi untuk bingkai tanpa rujukan pantulan; hanya satu daripada kedua-duanya wujud bagi setiap pelaksanaan, dipilih oleh tetapan pembetulan Vignette
  * `<INDEX>_Index_Images/` — satu folder bagi setiap indeks yang dipilih (contohnya `NDVI_Index_Images`)

{% hint style="info" %}
**Setiap produk yang dieksport mengekalkan nama fail SUMBER.**Eksport radiance `capture_..._raw.tif` masih dipanggil `capture_..._raw.tif` — ia hanya terletak di `tiff32/Radiance_Images/`.**Folder mengenal pasti produk, bukan nama fail**, jadi mencari `*radiance*.tif` tidak akan menemui apa-apa; padankan pada direktori sebaliknya.
{% endhint %}



<!-- SCREENSHOT-NEEDED: Windows Explorer open on a processed project folder showing the tree: a LATT-… camera folder expanded with tiff16 (Reflectance_Calibrated_Images, Debayered_Images, Preview_Images, NDVI_Index_Images) and tiff32 (Radiance_Images) subfolders visible -->
### Berapa Banyak Fail yang Perlu Ada?

Jangan kira menggunakan formula — bilangan keluaran bergantung pada produk mana yang diaktifkan dan mana yang terpakai untuk setiap kamera (contohnya, kameraRGB
tidak mendapat radiance/reflectance). Bilangan yang sah terdapat dalam log: baris terakhir `[RUN-SUMMARY]` melaporkan dengan tepat berapa banyak fail yang telah ditulis, dan baris petunjuk menerangkan apa sahaja yang dilangkau.

***

## Menyemak Imej yang Diproses

### Pratonton Pantas dalam Penjelajah Fail

**Pratonton terbina dalamWindows
:**

1. Layari ke dalam folder produk (contohnya `tiff16/Reflectance_Calibrated_Images/`)
2. Pilih fail imej
3. Pratonton akan muncul di panel pratontonWindows
Explorer
4. Gunakan kekunci anak panah untuk menelusuri imej

### Pratonton dalam Pemapar Imej Luaran

**Pemapar yang disyorkan:*** **QGIS** - Perisian GIS percuma (terbaik untuk analisis multispektral bergeoreferens)
* **IrfanView** - Pemapar imej pantas dan ringan (menyokongTIFF
)
* **Adobe Photoshop** - Penyuntingan profesional (menyokongTIFF
)
* **GIMP** - Alternatif percuma kepada Photoshop
* **Windows
Photos** - Pemaparan asas (mungkin tidak menyokong 16-bitTIFF
)

### Pratonton di Pemapar Imej [Index/LUT Sandbox]

Gunakan Pemapar Imej terbina dalamChloros
untuk visualisasi lanjutan:

1. Klik lakaran kecil imej di Pelayar Fail
2. Imej dibuka di kawasan pratonton utama
3. Klik tab **Pemapar Imej**<img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line">
di bar sisi kiri
4. Gunakan [Index/LUT Sandbox](../image-viewer-gui/index-lut-sandbox.md) untuk analisis interaktif

Lihat [Image Viewer](../image-viewer-gui/opening-an-image-full-screen.md) untuk arahan terperinci.

***

## Membaca Nilai Pigmen Reflektan (GIS / Pix4D / Skrip)

Reflektan disimpan sebagai DN integer, dan **DN yang bermaksud ρ = 1.0 bergantung pada kamera sumber**:

| Sumber          | ρ = 1.0 adalah | Cara untuk mengetahui                                        |
| --------------- | ---------- | -------------------------------------------------- |
| LATTICE (M3C/M3M) | **32768** (ruang lebih sehingga ρ 2.0) | Tag XMP `Chloros:PixelScale=32768` dicop pada fail |
|Survey3
         | **65535** (dipotong pada ρ 1.0)     | Tiada tag XMP `Chloros:*` — ketiadaan itulah isyaratnya |

**Baca tag `Chloros:PixelScale` dan bahagikannya** daripada menganggap secara umum 65535 — membahagikan pantulan LATTICE dengan 65535 secara senyap akan memotong setiap nilai kepada separuh. Satu kes sempadan tidak mempunyai skala mengikut reka bentuk: perekodan sumber 8-bit yang ditulis sebagai keluaran 8-bit dipotong, bukan diskala semula, dan sengaja tidak diberikan tag skala — eksport semula pada 16-bit atau 32-bit bukannya membahagikannya. Lihat [Format Imej Keluaran](../output-image-formats.md) untuk maklumat penuh.***

## Metadata yang Dibawa ke Eksport

Setiap produk mengekalkan **blok GPS**tangkapan sumber dan**sub-IFD EXIF**nya, jadi satu
eksport membawa `FocalLength`, `FNumber`, `ExposureTime`, `ISO`, `DateTimeOriginal` dan
`CameraSerialNumber` serta georujukan.

{% hint style="warning" %}
**Jika ortomosaik terhasil pada skala yang tidak munasabah, semak `FocalLength` terlebih dahulu.**
Pix4D menyelesaikan jarak sampel tanah daripada panjang fokus ditambah ketinggian. Tanpa tag itu, ia kembali kepada skala yang sangat salah — dalam satu penerbangan 49-capture yang diukur, sebuah kebun oren bersaiz 411 m × 160 m dibina semula sebagai 47.8 km × 13 km, menghasilkan orto 455 megapiksel yang kebanyakannya ruang kosong. Proses penilean yang perlahan dan fail yang besar secara tidak dijangka adalah simptom masalah ini, bukan masalah berasingan.

```bash
exiftool -FocalLength -GPSLatitude "YourProject/.../some_export.tif"
```
{% endhint %}

Tidak semua tag disalin. Tag struktur IFD0 sengaja ditinggalkan (menyalinnya merosakkan keluaran LATTICE), dan `ExifImageWidth` / `ExifImageHeight` dikecualikan kerana ia menerangkan tangkapan asal — satu eksport yang telah diubah saiznya sebaliknya
menuntut dimensi yang bertentangan dengan rasternya sendiri.

***

## Menyemak Log Ralat

### Semak Amaran atau Ralat

1. Buka tab **Debug Log**<img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line">

2. Tatal melalui mesej
3. Cari amaran kuning atau ralat merah
4. Baca baris `[RUN-SUMMARY]` dan sebarang petunjuk
5. Hubungi sokonganMAPIR
untuk bantuan

### Menyimpan Log

Untuk menyimpan rekod pemprosesan atau untuk menghantar kepada SokonganMAPIR
:

1. Klik butang **&quot;Salin&quot;**atau**&quot;Muat turun&quot;**

2. Simpan sebagai fail teks dalam folder projek
3. Sertakan bersama dokumentasi projek
4. Hantar kepada sokonganMAPIR
jika menghadapi masalah

***

## Masalah Keluaran Biasa dan Penyelesaian

### Masalah: Fail Output Hilang

**Punca yang mungkin:**

* Produk tersebut tidak terpakai untuk kamera tersebut (contohnya, radiasi/pantulan untuk kameraRGB
— seperti yang dinyatakan dalam log)
* Rujukan yang diperlukan hilang (contohnya pantulan tanpa sasaran dan tanpa `.daq` downwelling)
* Kotak semak eksport produk dilumpuhkan dalam Tetapan Projek
* Ruang cakera habis semasa eksport

**Penyelesaian:**

1. Semak petunjuk `[RUN-SUMMARY]` dan baris `[EXPORT-CHECK]` dalam Log Ralat — ia menerangkan tentang langganan bagi setiap kamera
2. Semak kotak pilihan produk eksport dalam [Project Settings](adjusting-project-settings.md)
3. Semak sama ada ruang cakera mencukupi
4. Proses semula selepas membetulkan puncanya

### Isu: Tepi Gelap atau Terang (Vignetting Masih Nampak)

**Punca yang mungkin:**

* Pembetulan vignet dilumpuhkan
* Kamera/lensa tidak dalam pangkalan data profil EChloros

* Vignet yang melampau melebihi keupayaan pembetulan

**Penyelesaian:**

1. Semak sama ada pembetulan vignet telah diaktifkan dalam Tetapan Projek
2. Semak sama ada model kamera dikesan dengan betul
3. Hubungi sokonganMAPIR
jika vignet berterusan

### Isu: Warna atau Nilai Tidak Betul

**Punca yang mungkin:**

* Tiada sasaran penentukuran dikesan
* Model sasaran penentukuran yang salah dipilih
* Penentukuran pantulan dilumpuhkan
* Imej sasaran berkualiti rendah

**Penyelesaian:**

1. Semak sama ada penentukuran pantulan telah diaktifkan
2. Periksa mesej &quot;Target found&quot; dalam Log Ralat
3. Semak kualiti imej sasaran
4. Proses semula dengan menandakan sasaran yang betul

### Isu: NilaiNDVI
Nampak Salah

**JulatNDVI
yang dijangkakan:*** **Air, batu, tanah**: -0.1 hingga 0.2
* **Tumbuhan jarang/tidak sihat**: 0.2 hingga 0.4
* **Tumbuhan sederhana**: 0.4 hingga 0.6
* **Tumbuhan sihat, lebat**: 0.6 hingga 0.9**Jika nilai berada di luar julat ini:**

1. Semak sama ada penentukuran pantulan telah digunakan
2. Semak sama ada log penderia cahaya disertakan
3. Semak sama ada sasaran penentukuran dikesan
4. Pastikan model kamera yang betul dikesan
5. Semak semula masa dan keadaan tangkapan imej sasaran
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
2. **Kekalkan metadata EXIF**: Pastikan data GPS terpelihara untuk penandaan geografi
3. **Aliran kerja yang dikalibrasi**: Gunakan imej pantulan untuk ketepatan saintifik — pantulan LATTICE membawa tag kalibrasi XMP yang dibaca oleh Pix4D
4. **Proses mozek indeks**: Cipta ortomozek**NDVI
** daripada imej indeks individu
5. **Eksport**GeoTIFF
**yang georujuk**: Untuk kegunaan dalam aplikasi GIS

### Untuk Analisis GIS

**Aliran kerja yang disyorkan:**

1.**Muat ke dalam QGIS, ArcGIS, atau yang serupa**

2.**Gunakan imej pantulanTIFF
16-bit** untuk analisis berbilang jalur (bahagikan dengan `Chloros:PixelScale` fail tersebut)
3. **Gunakan imej indeks** (NDVI
,NDRE
) sebagai lapisan vegetasi yang siap digunakan
4. **Pengira raster**: Gabungkan jalur untuk analisis tersuai
5. **Eksport**: Buat peta klasifikasi, pendedahan perubahan, peta kesihatan vegetasi

### Untuk Analisis Langsung / Pelaporan

**Aliran kerja yang disyorkan:**

1.**Gunakan imej indeks dengan warna LUT** untuk laporan visual
2. **Ekstrak statistik**: PurataNDVI
bagi setiap lapangan/plot
3. **Siri masa**: Bandingkan indeks merentas pelbagai sesi
4. **Jana laporan**: Sertakan peta, statistik, dan visualisasi***

## Arkib dan Sandaran

### Strategi Sandaran Yang Disyorkan

**Apa yang perlu disimpan:*** ✅ **Imej asal RAW/JPG atau tangkapan mentah LATTICE** - Arkibkan pada pemacu/awan berasingan; raw adalah sumber saluran paip dan segala-galanya yang lain boleh dijana semula daripadanya
* ✅ **Fail penderia cahaya `.daq` / `.csv`** - Diperlukan untuk mengira semula pantulan kemudian
* ✅ **Keluaran yang diproses** - Simpan imej dan indeks yang telah dikalibrasi
* ✅ **Folder projek** (`project.json` dan rakan-rakannya) - Mengandungi semua tetapan untuk pemprosesan semula jika perlu
* ✅ **Log Ralat** - Mencatat butiran pemprosesan
* ✅ **Imej sasaran penentukuran** - Untuk pengesahan dan pemprosesan semula**Cadangan penyimpanan:*** **Simpanan segera**: Cakera keras luaran
* **Arkib jangka panjang**: Penyimpanan awan (Google Drive, Dropbox, dll.)
* **Data kritikal**: Simpan 2-3 salinan di lokasi yang berbeza***

## Proses Seterusnya

### Menggunakan Semula Tetapan Projek

Jika memproses set data yang serupa pada masa hadapan:

1. **Simpan Templat Projek** (jika belum dilakukan)
2. **Buat projek baru** menggunakan templat yang disimpan
3. **Import imej baru**

4.**Proses**dengan tetapan yang sama untuk keseragaman

### Pemprosesan Berbatch untuk Beberapa Sesi

Untuk beberapa sesi/set data:**Pilihan 1: GUI - Beberapa Projek**

* Buat projek berasingan untuk setiap sesi
* Gunakan tetapan templat yang konsisten
* Proses satu persatu

**Pilihan 2: Pemprosesan Berbatch (Chloros
)CLI
(Chloros
+ sahaja)**

* Automasi pemprosesan pukal
* Proses berbilang folder dengan skrip
* Lihat [DokumentasiCLI
](../CLI.md) dan [RujukanCLI
](../reference/cli-reference.md)

**Pilihan 3:Python
SDK
(Chloros
+ sahaja)**

* Kawalan berprogram
* Integrasi dengan saluran analisis
* Lihat [DokumentasiAPI
](../api-python-sdk.md) dan [RujukanSDK
](../reference/sdk-reference.md)

***

## Penyelesaian Masalah Pascakprosesan

### Memproses Semula dengan Tetapan Berbeza

Jika keputusan tidak memuaskan:

1. Simpan imej asal (jangan padam)
2. Buka projek yang sama dalamChloros
3. Laras tetapan dalam panel Tetapan Projek
4. Proses semula — keluaran akan berada dalam folder produk yang sama, jadi fail dengan nama yang sama daripada larian sebelumnya akan digantikan

### Memproses Kumpulan Imej Tertentu

Untuk memproses semula hanya imej tertentu:

1. Buat projek baru
2. Import hanya imej yang perlu diproses semula
3. Gunakan templat tetapan yang sama
4. Proses set data yang lebih kecil

### Memperoleh Bantuan

Jika anda menghadapi masalah:

* 📧 **E-mel**: info@mapir.camera (sertakan Log Ralat)
* 🌐 **Sokongan**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* 📚 **Soalan Lazim**: [Soalan Lazim](../faq.md)
* 📖 **Dokumentasi**: [ManualChloros
](../)

***

## Ringkasan: Aliran Kerja Lengkap

Anda kini telah melengkapkan keseluruhan aliran kerja pemprosesanChloros
:

1. ✅ **Dicipta projek** - Lihat [Projek](../projects.md)
2. ✅ **Tambah fail** - Lihat [Menambah Fail](adding-files-to-a-project.md)
3. ✅ **Melaras tetapan** - Lihat [Melaras Tetapan Projek](adjusting-project-settings.md)
4. ✅ **Menandakan sasaran** - Lihat [Memilih Imej Sasaran](choosing-target-images.md)
5. ✅ **Memulakan pemprosesan** - Lihat [Memulakan Pemprosesan](starting-the-processing.md)
6. ✅ **Memantau kemajuan** - Lihat [Memantau Pemprosesan](monitoring-the-processing.md)
7. ✅ **Menyemak keputusan** - Halaman ini**Imej multispektral anda yang telah dikalibrasi dan diperbetulkan cerminan sudah sedia untuk dianalisis!**

***

## Sumber Tambahan

### Ciri Lanjutan

* [**Pemerhati Imej**](../image-viewer-gui/opening-an-image-full-screen.md) - Visualisasi interaktif dan analisis
* [**Sandbox Indeks/LUT**](../image-viewer-gui/index-lut-sandbox.md) - Ujian indeks tersuai
* [**Formula Indeks Multispektral**](../project-settings/multispectral-index-formulas.md) - Rujukan indeks lengkap

### Automasi &amp; Integrasi

* [**DokumentasiCLI**](../CLI.md) - Pemprosesan pukal baris perintah
* [**SDK
Python
**](../api-python-sdk.md) - Automasi berprogram
* [**Chloros
+ Ciri-ciri**](../#chloros) - Keupayaan pemprosesan lanjutan

### Sokongan &amp; Pembelajaran

* [**Soalan Lazim**](../faq.md) - Soalan biasa dijawab
* [**Sasaran Kalibrasi**](../calibration-targets.md) - Memahami kalibrasi reflektansi
* [**Kamera yang Disokong**](../supported-cameras.md) - Perkakasan serasi
