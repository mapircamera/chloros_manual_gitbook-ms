---
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/supported-cameras
---

# Kamera yang disokong

Chloros

memproses imej daripada dua keluarga kameraMAPIR

pada **semua platform** (Windows

,Linux

amd64, danLinux

arm64/Jetson):

* **Survey3** — kameraSurvey3W

(luas) danSurvey3N

(sempit). Input: `RAW+JPG`.
* **LATTICE**— modul kamera multispektral M3C dan M3M. Input: `.tif`/`.tiff` captures. Kamera LATTICE juga boleh**dikawal secara langsung** daripadaChloros

— melalui tab GUI Cameras (Windows

) atau `chloros-cli lattice` /Python

SDK

(Windows

danLinux

) — termasuk susunan multi-kamera yang diselaraskan. Lihat [panduan LATTICE](lattice/).

Saluran pemprosesan juga menerima fail input `.dng`.

##Survey3

<table data-header-hidden><thead><tr><th width="156">Pengeluar</th><th width="250">Model Kamera</th><th width="138">Model Penapis</th><th width="187">Jenis Imej</th></tr></thead><tbody><tr><td><strong>Pengeluar</strong></td><td><strong>Model Kamera</strong></td><td><strong>Model Penapis</strong></td><td><strong>Jenis Imej</strong></td></tr><tr><td>MAPIR</td><td>Survey3W, Survey3N</td><td>RGB</td><td>RAW+JPG, JPG</td></tr><tr><td>MAPIR</td><td>Survey3W, Survey3N</td><td>RGN</td><td>RAW+JPG, JPG</td></tr><tr><td>MAPIR</td><td>Survey3W, Survey3N</td><td>OCN</td><td>RAW+JPG, JPG</td></tr><tr><td>MAPIR</td><td>Survey3W, Survey3N</td><td>NGB</td><td>RAW+JPG, JPG</td></tr><tr><td>MAPIR</td><td>Survey3W, Survey3N</td><td>RE</td><td>RAW+JPG, JPG</td></tr><tr><td>MAPIR</td><td>Survey3W, Survey3N</td><td>NIR</td><td>RAW+JPG, JPG</td></tr></tbody></table>## LATTICE

Barisan LATTICE adalah sistem kamera multispektral modular yang dibina berdasarkan sensor global-shutter Sony IMX265 (3.1 MP, piksel 3.45 µm). Setiap kamera menyimpan identitinya sebagai rentetan model:

```
<sensor>-<lens>-F<filter>        e.g.  M3C-L41-FRGN,  M3M-L87-F550
```

Chloros

memaparkannya dengan awalan `LATT-` (contohnya `LATT-M3M-L41-F550`), dan rentetan model menggerakkan segala-galanya seterusnya — profil sensor, susun atur jalur, dan penentukuran diselesaikan secara automatik; tiada apa-apa yang perlu dikonfigurasikan bagi setiap kamera. Nombor lensa ialah **sudut pandangan mendatar dalam darjah**: `L41` = sempit 41°, `L87` = lebar 87°.

Terdapat dua konfigurasi sensor:

| Konfigurasi | Sensor      | Jenis penapis                           | Jalur setiap kamera                                                        |
| ------------- | ----------- | ------------------------------------- | ----------------------------------------------------------------------- |
| **M3C**       | Warna Bayer | Pelbagai jalur pas | 3 jalur spektral daripada satu pendedahan tunggal                                |
| **M3M**       | Monochrom  | Penapis gangguan jalur sempit tunggal | 1 jalur kalibrasi — gabungkan beberapa kamera M3M untuk indeks vegetasi |

### Pilihan penapis M3C (Bayer)

| Penapis | Jalur (nama @ pusat nm /FWHM

nm)       |
| ------ | ---------------------------------------- |
| `FRGB` |Blue

475/30 ·Green

550/30 ·Red

625/30  |
| `FRGN` |Red

660/21 ·Green

550/30 ·NIR

850/30   |
| `FOCN` |Orange

615/21 ·Cyan

490/38 ·NIR

808/14 |
| `FNGB` |Blue

475/30 ·Green

550/30 ·NIR

850/30 |

### Katalog penapis M3M (mono) — 23 SKU

Nombor F ialah label SKU; jalur yang diukur (ditempa pada setiap eksport yang dikalibrasi) ialah imbasan penapis bagi setiap lot:

| SKU    | Pusat (nm, diukur) | TepiFWHM

(nm) | Lebar (nm) |
| ------ | --------------------- | --------------- | ---------- |
| F385   | 379.4                 | 367–392         | 25         |
| F405   | 403.9                 | 390–417         | 27         |
| F450   | 443.7                 | 430–458         | 28         |
| F485   | 489.7                 | 478–502         | 24         |
| F520   | 519.9                 | 504–536         | 32         |
| F550   | 548.4                 | 531–566         | 35         |
| F590   | 589.0                 | 570–608         | 38         |
| F615   | 623.8                 | 614–634         | 20         |
| F632   | 633.4                 | 616–651         | 35         |
| F650   | 651.1                 | 636–666         | 30         |
| F685   | 686.2                 | 675–698         | 23         |
| F715   | — (nominal)           | 706–724         | 18         |
| F725   | 725.2                 | 712–738         | 26         |
| F750   | 746.0                 | 729–763         | 34         |
| F780   | 775.1                 | 754–796         | 42         |
| F808   | 810.3                 | 789–832         | 43         |
| F832   | 826.1                 | 810–843         | 33         |
| F850   | 846.5                 | 828–865         | 37         |
| F880   | — (nominal)           | 867–893         | 26         |
| F905   | — (nominasi)           | 892–920         | 28         |
| F940   | 940.6                 | 923–958         | 35         |
| F950   | 945.1                 | 929–961         | 32         |
| F988 † | 985.3                 | 968–1003        | 35         |

&quot;Pinggir jalur diukur pada nilai lebar penuh pada separuh maksimum daripada imbasan penapis setiap lot olehMAPIR

— nilai yang sama yang dicop olehChloros

ke dalam setiap eksport yang dikalibrasi.&quot; &quot;— (nominal)&quot; = tiada imbasan lot lagi; bagi SKU tersebut, pusat yang dinyatakan ialah nombor SKU dan lebarnya ialah angka pengeluar.

† Reflektan F988 dikalibrasi menggunakan panel reflektansi dalam adegan: jalur itu terletak di luar julat kalibrasi penderia cahaya DAQ, jadiChloros

menggunakan tangkapan panel terkini anda dan menyimpannya di antara penjejakan panel. Lihat [Sasaran Kalibrasi](calibration-targets.md).

Untuk kawalan kamera secara langsung, susunan, penyediaan rangkaian, dan rantaian pemprosesan radiometrik, lihat [panduan LATTICE](lattice/).
