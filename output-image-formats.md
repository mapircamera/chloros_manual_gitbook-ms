---
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/output-image-formats
---

# Format Imej Keluaran

Chlorosmengeksport produk yang diproses dalam empat format fail. Pilih format dalam Tetapan Projek (GUI), dengan `--format` (CLI), atau dengan `export_format` (SDK). CLI dan SDK menerima rentetan tepat di bawah.

| Rantaian format | Sambungan | Jenis piksel | Julat piksel | Nota |
| --- | --- | --- | --- | --- |
| `TIFF (16-bit)` *(default)* | `.tif` | uint16 nombor digital | 0 – 65535 | Disyorkan untuk fotogrametri / GIS. |
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

Satu bingkai mentah LATTICE diedarkan kepada setiap produk yang diminta dalam satu pusingan. Setiap jenis produk mempunyai suis sendiri (kotak semak GUI, atau CLI `--debayered` / `--preview` / `--radiance` / `--reflectance`, semuanya DIDALAMKAN secara lalai):

| Tahap | Kandungan | Jenis data |
| `raw` | Data Bayer terus dari sensor (kamera monokrom: jalur tunggal). Pemprosesan sentiasa bermula daripada data mentah. | Seperti dirakam |
| `debayered` | Demosaik linear — 3-saluran untuk M3C, 1-saluran skala kelabu untuk M3M. | DN linear |
| `radiance` | Radiasi spektral mutlak daripada keseluruhan rantaian radiometrik, dalam **W/m²/sr/nm**. Sentiasa ditulis sebagai TIFF 32-bit (`tiff32/Radiance_Images/`), tanpa mengira format eksport yang dipilih. | float32 |
| `reflectance` | Reflektan ρ, di mana **DN 32768 = ρ 1.0 (100%)** dengan ruang lebihan sehingga ρ 2.0. Sedia untuk Pix4D. | uint16 |
| `preview` | Render sedia-pameran: RGB = imbangan putih + gamma; multispektral = regangan warna palsu. | paparan 8-bit |

## Membaca nilai piksel pantulan

Reflektan disimpan sebagai nombor digital bulat, dan **DN yang bermaksud ρ = 1.0 (100% reflektan) bergantung pada kamera sumber**:

| Kamera sumber | ρ = 1.0 adalah DN | Cara untuk mengetahui |
| --- | --- | --- |
| LATTICE (M3C / M3M) | `32768` (ruang lebih sehingga ρ 2.0) | Tag XMP `Chloros:PixelScale=32768` dicop pada fail. |
| Survey3 | `65535` (dipotong pada ρ 1.0) | Tiada tag XMP `Chloros:*` — ketiadaannya itulah isyaratnya. |

**Baca tag XMP `Chloros:PixelScale` dan bahagikannya dengannya** daripada menganggap ia satu pemalar. Tag ini ditakrifkan dalam domain uint16, jadi ia kekal `32768` merentasi format keluaran yang menukar skala — normalisasikan dtype yang disimpan kembali kepada uint16 terlebih dahulu (×257 daripada 8-bit, ×65535 daripada float32).

{% hint style="warning" %}
**Satu kes tidak mempunyai skala, mengikut reka bentuk.** Apabila tangkapan sumber 8-bit (BayerRG8) ditulis sebagai TIFF 8-bit, aliran kerja memotong kepada 0–255 bukannya menormalkan semula, jadi tiada skala yang menerangkan fail itu — Chloros sengaja mengabaikan `Chloros:PixelScale` di situ. Jika tag itu tiada pada fail reflektansi LATTICE, jangan anggap ada skala; eksport semula pada 16-bit atau 32-bit sebaliknya.
{% endhint %}

Untuk peraturan lengkap (termasuk tag yang serasi dengan MicaSense), lihat **&quot;Membaca piksel pantulan&quot;** dalam [Rujukan CLI](reference/cli-reference.md).
