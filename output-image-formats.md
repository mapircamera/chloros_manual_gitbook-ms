---
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/output-image-formats
---

# Format Imej Keluaran

Chloros mengeksport produk yang diproses dalam empat format fail. Pilih format dalam Tetapan Projek (GUI), dengan `--format` (CLI), atau dengan `export_format` (SDK). CLI dan SDK menerima rentetan tepat di bawah.

| Rentetan format | Sambungan | Jenis piksel | Julat piksel | Nota |
| --- | --- | --- | --- | --- |
| `TIFF (16-bit)` *(laluan lalai)* | `.tif` | uint16 nombor digital | 0 – 65535 | Disyorkan untuk fotogrametri / GIS. |
| `TIFF (8-bit)` | `.tif` | uint8 nombor digital | 0 – 255 | Untuk semua tangkapan 8-bit, yang hanya menawarkan format 8-bit: Chloros tidak menaikkan kedalaman bit. |
| `TIFF (32-bit, Percent)` | `.tif` | float32 | 0.0 – 1.0 | 1.0 = 100% pantulan. Sesetengah aplikasi tidak dapat membaca TIFF titik terapung; saiz fail adalah lebih besar. |
| `PNG (8-bit)` | `.png` | uint8 nombor digital | 0 – 255 | Pemampatan tanpa kehilangan, sesuai untuk tontonan web dan visualisasi. |
| `JPG (8-bit)` | `.jpg` | uint8 nombor digital | 0 – 255 | Pemampatan berkehilangan, fail terkecil. |

## Lokasi fail keluaran

Produk ditulis di bawah folder projek, dikumpulkan mengikut kamera dan kemudian mengikut format fail:

```
<project>/
└── LATT-M3M-L41-F550/                  # one folder per camera (model+lens+filter)
    ├── tiff16/                          # follows --format: tiff16, tiff8, png8, jpg8, or tiff32
    │   ├── Reflectance_Calibrated_Images/
    │   ├── Debayered_Images/
    │   ├── Preview_Images/
    │   └── NDVI_Index_Images/           # one <INDEX>_Index_Images/ folder per requested index
    └── tiff32/
        └── Radiance_Images/             # float32 radiance always lands here
```

Folder kamera adalah `LATT-<sensor>-<lens>-F<filter>` untuk LATTICE dan `<model>_<filter>` (contohnya `Survey3N_RGN`) untuk Survey3. **Setiap produk yang dieksport mengekalkan nama fail sumber — folder mengenal pasti produk, bukan sambungan nama fail.** Lihat [Where the outputs land](reference/cli-reference.md) dalam Rujukan CLI untuk peraturan penuh.

## Produk LATTICE (peringkat tangkapan dan eksport)

Satu bingkai mentah LATTICE berkembang menjadi setiap produk yang diminta dalam satu kali larian. Setiap jenis produk mempunyai suis sendiri (kotak semak GUI, atau CLI `--debayered` / `--preview` / `--radiance` / `--reflectance`, semuanya DIHIDUPKAN secara lalai):

| Tahap | Kandungan | Jenis data |
| `raw` | Data Bayer terus dari sensor (kamera mono: jalur tunggal). Pemprosesan sentiasa bermula daripada data mentah. | Seperti dirakam |
| `debayered` | Demosaik linear — 3-saluran untuk M3C, 1-saluran skala kelabu untuk M3M: offset gelap pek penentukuran telah dialihkan, kemudian demosaik peka-tepi, tanpa pembetulan lineariti, medan rata atau pencampuran semula. Keluaran `process` menulis fail yang sama seperti yang disimpan oleh tangkapan pada tahap ini, dalam setiap mod pemprosesan. | DN Linear |
| `radiance` | Radiasi spektral mutlak daripada keseluruhan rantaian radiometrik, dalam **W/m²/sr/nm**. Sentiasa ditulis sebagai TIFF 32-bit (`tiff32/Radiance_Images/`), tanpa mengira format eksport yang dipilih. | float32 |
| `reflectance` | Pantulan ρ, di mana **DN 32768 = ρ 1.0 (100%)** dengan ruang lebih sehingga ρ 2.0. Sedia untuk Pix4D. | uint16 |
| `preview` | Render sedia-pameran: RGB = imbangan putih + gamma; multispektral = regangan warna palsu. | paparan 8-bit |

### Orientasi

Fail `raw` dan `debayered` sentiasa kekal dalam orientasi **asal** sensor; tetapan pembalikan/pusingan kamera hanya digunakan pada produk lain (pratinjau, sinaran, pantulan, indeks). Simpanan susunan mengekalkan fail setiap ahli dalam orientasi asal melainkan anda meminta orientasi (`--orient` dalam CLI, `orient=True` dalam SDK). Setiap fail merekod orientasi yang dimiliki oleh pikselnya dalam XMP — `ChlorosRotation`, `ChlorosFlipHorizontal`, `ChlorosFlipVertical` — dan `ChlorosOrientationApplied` menyatakan sama ada pikselnya sendiri membawanya (`false` pada fail mentah dan debayered). Pemandang Imej Chloros memusingkan fail mentah dan fail yang telah debayered mengikut rekod tersebut hanya untuk paparan; fail itu sendiri tidak pernah ditulis semula. Fail yang tiada rekod (ditulis sebelum 1.3.0, atau oleh alat lain) dipaparkan seperti yang disimpan.

## Topeng clamp

Produk radiasi dan pantulan kamera multispektral M3C menjalankan pencampuran semula jalur 3×3. Apabila pencampuran semula menghasilkan nilai negatif dalam sesuatu jalur, nilai tersebut dipotong kepada sifar (pemandangan langsung memotong dengan cara yang sama). Baru dalam 1.3.0, produk di mana sekurang-kurangnya satu piksel telah dipotong mendapat **topeng clamp** pendamping yang menyatakan dengan tepat piksel dan jalur mana:

* **Di mana:**dalam folder `clamp_masks/`**di sebelah** folder produk, tidak pernah di dalamnya: `tiff16/Reflectance_Calibrated_Images/x.tif` → `tiff16/clamp_masks/Reflectance_Calibrated_Images/x_clampmask.tif`, dan `tiff32/Radiance_Images/x.tif` → `tiff32/clamp_masks/Radiance_Images/x_clampmask.tif`.
* **Apa:** satu fail TIFF jalur tunggal `uint8` pada grid piksel produk itu sendiri (termasuk orientasi, ROI, binning dan penjajaran tatasusunan). Bit 0 (`mask & 1`) ditetapkan di mana jalur dalam saluran R produk telah dipotong, bit 1 (`& 2`) saluran G, bit 2 (`& 4`) saluran B. Bahagian piksel yang dipotong bagi setiap jalur turut dicop pada produk sebagai `Chloros:BandRUnmixClampFraction`, `Chloros:BandGUnmixClampFraction` dan `Chloros:BandBUnmixClampFraction`.
* **Bagaimana produk memberitahu demikian:** tag XMP `Chloros:UnmixClampMask` menyimpan laluan topeng berbanding folder produk (`../clamp_masks/<product folder>/<name>_clampmask.tif`, sentiasa dipisahkan dengan `/`), `none` apabila tiada piksel dipotong (tiada topeng ditulis), atau `unavailable` apabila topeng tidak dapat ditulis. Sebuah produk tanpa tag tidak pernah menjalankan unmix (kamera RGB dan mono, tahap raw dan debayered).

Kerana tiada folder produk yang mengandungi topeng, anda boleh menyerahkan folder produk seperti `Reflectance_Calibrated_Images/` kepada Pix4D, Metashape atau perisian fotogrametri lain seperti sedia ada, termasuk subfolder — serahkan folder produk itu, bukan folder format di atasnya. Import Chloros sendiri melangkau topeng clamp. CLI mengiranya secara berasingan: `Image products written: N (plus M clamp masks)`.

## Produk indeks

Setiap indeks disimpan dalam folder `<INDEX>_Index_Images/` tersendiri (contohnya `NDVI_Index_Images/`).

* **Format integer**(`TIFF (16-bit)`, `TIFF (8-bit)`, `PNG (8-bit)`, `JPG (8-bit)`) menyimpan**regangan linear per-imej**, bukan peta −1…1: DN 0 ialah nilai indeks terendah bagi bingkai itu dan skala penuh ialah yang tertinggi. Fail ini mengandungi `Chloros:IndexScaling=linear_minmax`, `Chloros:IndexMin` dan `Chloros:IndexMax`, jadi `value = IndexMin + DN / full_scale × (IndexMax − IndexMin)`. Kerana setiap bingkai mempunyai titik hujungnya sendiri, bandingkan bingkai sebagai nilai, bukan sebagai DN.
* **`TIFF (32-bit, Percent)`** menyimpan nilai indeks itu sendiri sebagai float32, tanpa penskalaan semula — pilihan terbaik untuk kerja kuantitatif merentasi penerbangan.

Baru dalam 1.3.0, produk indeks menerangkan **dirinya sendiri** dan bukannya mengulangi tag sumber mentah. Tag GPS dan kameranya disalin daripada produk yang digunakan untuk mengiranya (produk pantulan apabila wujud), dan kemudian:

| Tag | Nilai |
| --- | --- |
| `Chloros:ProcessingLevel` | `index` (supaya fail tidak pernah diimport semula sebagai tangkapan) |
| `Chloros:IndexApplied` / `Chloros:IndexExpression` | `True` / formula yang digunakan |
| `Chloros:IndexLevel` | `reflectance` atau `radiance` — apa yang digunakan untuk mengira indeks (tidak ada apabila kedua-duanya tiada) |
| `Chloros:IlluminationInvariant` | `True` untuk indeks yang dikira daripada pantulan, `False` daripada sinaran atau nilai penderia |
| `Chloros:ChlorosPipelineVersion` | versi Chloros yang menulis indeks |

`Chloros:PixelScale` dikeluarkan daripada produk indeks — gunakan `IndexMin` / `IndexMax` di atas. Resipi bacaan balik penuh terdapat di bawah &quot;Raster indeks integer adalah regangan kontras setiap imej&quot; dalam [Rujukan CLI](reference/cli-reference.md).

## Membaca nilai piksel pantulan

Reflektan disimpan sebagai nombor digital bulat, dan **DN yang bermaksud ρ = 1.0 (100% reflektan) bergantung pada kamera sumber**:

| Kamera sumber | ρ = 1.0 adalah DN | Cara untuk mengetahui |
| --- | --- | --- |
| LATTICE (M3C / M3M) | `32768` (ruang lebih sehingga ρ 2.0) | Tag XMP `Chloros:PixelScale=32768` dicop pada fail. |
| Survey3 | `65535` (dipotong pada ρ 1.0) | Tiada tag `Chloros:PixelScale` — ketiadaannya itulah isyaratnya. (Produk Survey3 memang membawa tag pemprosesan `Chloros:*` lain, seperti `Chloros:CalibrationApplied`, dalam setiap mod pemprosesan sejak versi 1.3.0.) |

**Baca tag `Chloros:PixelScale` XMP dan bahagikannya dengannya** daripada menganggap ia sebagai pemalar. Tag ini ditakrifkan dalam domain uint16, jadi ia kekal `32768` merentasi format keluaran yang menormalkan semula — normalkan jenis data yang disimpan kembali ke uint16 terlebih dahulu (×257 daripada 8-bit, ×65535 daripada float32).

{% hint style="warning" %}
**Sampulan 8-bit.** Eksport sampulan 8-bit adalah 8-bit. Reflektansnya diskalakan semula dan ditandakan `Chloros:PixelScale=32768`, jadi skala itu terpelihara; Produk DN 8-bit, dan fail pantulan yang ditulis sebelum 1.3.0, dipotong kepada 0–255 dan tidak mempunyai tag skala. Jika tag tiada pada fail pantulan LATTICE, jangan anggap ada skala; eksport semula pada 16-bit atau 32-bit sebaliknya.
{% endhint %}

Untuk peraturan lengkap (termasuk tag yang serasi dengan MicaSense), lihat **&quot;Membaca piksel pantulan&quot;** dalam [Rujukan CLI](reference/cli-reference.md).
