---
description: This page lists some multispectral indices that Chloros uses
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/multispectral-index-formulas
---

# Formula Indeks Multispektral

Formula indeks di bawah menggunakan gabungan julat penghantaran purata penapisSurvey3:

<table><thead><tr><th align="center">Survey3 Warna Penapis</th><th width="196.199951171875" align="center">Survey3 Nama Penapis</th><th width="159.800048828125" align="center">Julat Pemindahan (FWHM)</th><th align="center">Purata Penyerahan</th></tr></thead><tbody><tr><td align="center">Blue</td><td align="center">NGB - Blue</td><td align="center">468-483nm</td><td align="center">475nm</td></tr><tr><td align="center">Cyan</td><td align="center">OCN- Cyan</td><td align="center">476-512nm</td><td align="center">494nm</td></tr><tr><td align="center">Green</td><td align="center">RGN | NGB - Green</td><td align="center">543-558nm</td><td align="center">547nm</td></tr><tr><td align="center">Orange</td><td align="center">OCN - Orange</td><td align="center">598-640nm</td><td align="center">619nm</td></tr><tr><td align="center">Red</td><td align="center">RGN - Red</td><td align="center">653-668nm</td><td align="center">661nm</td></tr><tr><td align="center">RedEdge</td><td align="center">Re - RedEdge</td><td align="center">712-735nm</td><td align="center">724nm</td></tr><tr><td align="center">NIR1</td><td align="center">OCN - NIR1</td><td align="center">798-848nm</td><td align="center">823nm</td></tr><tr><td align="center">NIR2</td><td align="center">RGN | NGB | NIR - NIR2</td><td align="center">835-865nm</td><td align="center">850nm</td></tr></tbody></table>

Apabila formula-formula ini digunakan, nama mungkin berakhir dengan &quot;\_1&quot; atau &quot;\_2&quot;, yang sepadan dengan penapis NIR yang digunakan, sama ada NIR1 atau NIR2.

Untuk kamera LATTICE M3C (Bayer triple-bandpass), enjin indeks yang sama menggunakan jalur penapis M3C:

| Penapis M3C | Jalur 1 (pusat/ FWHM) | Jalur 2 (pusat/ FWHM) | Jalur 3 (pusat/ FWHM) |
| --- | --- | --- | --- |
| FRGB | Blue 475 nm / 30 nm | Green 550 nm / 30 nm | Red 625 nm / 30 nm |
| FRGN | Red 660 nm / 21 nm | Green 550 nm / 30 nm | NIR 850 nm / 30 nm |
| FOCN | Orange 615 nm / 21 nm | Cyan 490 nm / 38 nm | NIR 808 nm / 14 nm |
| FNGB | Blue 475 nm / 30 nm | Green 550 nm / 30 nm | NIR 850 nm / 30 nm |

Kamera LATTICE M3M adalah jalur tunggal (satu penapis jalur sempit bagi setiap kamera), jadi indeks jalur berganda tidak dikira untuk imej M3M tunggal. Untuk mengira indeks dengan M3M, gabungkan dua atau lebih kamera ke dalam susunan berbilang jalur yang selari dan gunakan enjin indeks LATTICE (`chloros-cli lattice index`, atau Pengira Indeks langsung GUI).

***

## Di mana setiap nama indeks berfungsi

Chloros mempunyai **tiga** permukaan indeks, dan senarai pratetapnya tidak sama. Gunakan bahagian ini untuk menyemak sama ada nama akan berfungsi di tempat anda merancang untuk menggunakannya.

| Di mana anda berada | Senarai yang terpakai | Bilangan |
| --- | --- | --- |
| Tetapan Projek → Indeks → Tambah indeks (GUI) | Permukaan 1 | 27 |
| Pemapar Imej [Sandbox Indeks/LUT](../image-viewer-gui/index-lut-sandbox.md) (GUI) | Permukaan 1 | 27 |
| `chloros-cli process --indices NDVI,NDRE` | Permukaan 2 | 22 |
| SDK `process_folder(indices=[...])` | Permukaan 2 | 22 |
| `chloros-cli lattice index --preset` | Permukaan 3 | 22 (22 yang berbeza) |
| Kalkulator Indeks Tab Kamera secara langsung | Permukaan 3 | 22 (22 yang berbeza) |

Permukaan 1 dan 2 berfungsi pada **satu imej pada satu masa daripada satu kamera**, menggunakan slot simbol `x`/`y`/`z`(/`a`) yang diikat kepada saluran penapis kamera tersebut. Permukaan 3 berfungsi pada**susunan jalur pelbagai yang selari** — beberapa kamera LATTICE yang diselaraskan ke dalam satu kiub — dan merujuk kepada saluran mengikut nama huruf kecil.

### 1. Tetapan Projek GUI / menu lungsur sandbox Pemapar Imej — 27 formula

Menu lungsur menyenaraikannya dalam susunan ini (ia adalah susunan penyisipan, bukan abjad):

`NDVI, GNDVI, CVI, ENDVI, EVI, MSR, OSAVI, TDVI, LAI, FCI1, FCI2, GARI, GCI, GEMI, GLI, GOSAVI, GRVI, GSAVI, LCI, MNLI, MSAVI2, NDRE, NLI, RDVI, SAVI, VARI, WDRVI`

Dalam GUI anda menyeret saluran penapis kamera anda ke dalam slot jalur formula, jadi mana-mana formula boleh digunakan dengan mana-mana penugasan jalur yang disokong oleh kamera anda. Formula tersuai yang telah anda simpan akan ditambah di bawah senarai ini.

Lima formula **hanya GUI** — iaitu yang tidak diterima oleh senarai CLI / SDK `--indices` — dilaksanakan seperti berikut:

| Preset hanya GUI | Formula (seperti yang dilaksanakan) | Slot |
| --- | --- | --- |
| FCI1 | `x*y` | x, y |
| FCI2 | `x*y` | x, y |
| GARI | `(y-(x-1.7*(z-a)))/(y+(x-1.7*(z-a)))` | x, y, z, a (empat slot) |
| GEMI | `((2*(y*y-x*x)+1.5*y+0.5*x)/(y+x+0.5))*(1-0.25*((2*(y*y-x*x)+1.5*y+0.5*x)/(y+x+0.5)))-((x-0.125)/(1-x))` | x, y |
| LCI | `(y-x)/(y+z)` | x, y, z |

Pemetaan yang dimaksudkan untuk setiap satu diberikan dalam seksyen tersendiri di bahagian bawah halaman ini (contohnya GARI menjangkakan x= Green, y= NIR, z= Blue, a= Red). GARI adalah satu-satunya formula dalam Chloros yang menggunakan slot keempat.

### 2. CLI / SDK pengembangan nama `--indices` — 22 pratetap

Pilihan `chloros-cli process --indices` (dan parameter `indices` SDK) menerima nama-nama pratetap ini:

`NDVI, GNDVI, NDRE, OSAVI, SAVI, MSAVI2, EVI, MSR, TDVI, LAI, GCI, GRVI, GSAVI, GOSAVI, NLI, MNLI, RDVI, WDRVI, CVI, ENDVI, GLI, VARI`

{% hint style="warning" %}
**Nama indeks yang tidak diketahui diabaikan secara senyap.** Nama di luar senarai ini (termasuk lima formula GUI sahaja `FCI1`, `FCI2`, `GARI`, `GEMI`, `LCI`, dan mana-mana formula tersuai yang anda simpan dalam GUI) akan diabaikan dengan hanya notis log — pelaksanaan diteruskan tanpa indeks tersebut, dan pelaksanaan itu sendiri masih melaporkan kejayaan. Notis itu dicetak sebagai:

```
[INDEX_EXPAND] skipping unknown preset 'LCI'; known: ['CVI', 'ENDVI', 'EVI', ...]
```

Nama dipadankan tanpa mengira huruf besar-kecil selepas memotong ruang kosong, jadi `ndvi`, `NDVI` dan ` NDVI ` adalah preset yang sama. Preset juga akan dilangkau jika ia memerlukan jalur yang tidak disediakan oleh penapis kamera anda.
{% endhint %}

Formula tepat seperti yang dilaksanakan (simbol `x`/`y`/`z` adalah slot jalur; pemetaan lalai ditunjukkan bagi setiap pratetap):

| Preset | Formula (sebagaimana dilaksanakan) | Penapis lalai | Slot (x, y, z) |
| --- | --- | --- | --- |
| NDVI | `(y-x)/(y+x)` | RGN | Red, NIR |
| GNDVI | `(y-x)/(y+x)` | RGN | Green, NIR |
| NDRE | `(y-x)/(y+x)` | RE | RE, NIR |
| OSAVI | `(y-x)/(y+x+0.16)` | RGN | Red, NIR |
| SAVI | `1.5*(y-x)/(y+x+0.5)` | RGN | Red, NIR |
| MSAVI2 | `(2*y+1-sqrt((2*y+1)*(2*y+1)-8*(y-x)))/2` | RGN | Red, NIR |
| EVI | `2.5*(y-x)/(y+6*x-7.5*z+1)` | RGN | Red, NIR, Blue |
| MSR | `((y/x)-1)/(sqrt(y/x)+1)` | RGN | Red, NIR |
| TDVI | `1.5*(y-x)/sqrt(y*y+x+0.5)` | RGN | Red, NIR |
| LAI | `3.618*(2.5*(y-x)/(y+6*x-7.5*z+1))-0.118` | RGN | Red, NIR, Blue |
| GCI | `(y/x)-1` | RGN | Green, NIR |
| GRVI | `y/x` | RGN | Green, NIR |
| GSAVI | `1.5*(y-x)/(y+x+0.5)` | RGN | Green, NIR |
| GOSAVI | `(y-x)/(y+x+0.16)` | RGN | Green, NIR |
| NLI | `((y*y)-x)/((y*y)+x)` | RGN | Red, NIR |
| MNLI | `((y*y-x)*(1+0.5))/((y*y)+x+0.5)` | RGN | Red, NIR |
| RDVI | `(y-x)/sqrt(y+x)` | RGN | Red, NIR |
| WDRVI | `(0.2*y-x)/(0.2*y+x)` | RGN | Red, NIR |
| CVI | `(z/y)/(x/y)` | RGB | Red, Green, Blue |
| ENDVI | `((x+y)-(2*z))/((x+y)+(2*z))` | RGB | Red, Green, Blue |
| GLI | `((y-x)+(y-z))/((2*y)+x+z)` | RGB | Red, Green, Blue |
| VARI | `(y-x)/(y+x-z)` | RGB | Red, Green, Blue |

#### Bagaimana nama pratet menjadi kedudukan jalur

Apabila anda memberikan nama kosong seperti `NDVI`, Chloros perlu memutuskan saluran mana dalam fail mana setiap simbol dibaca. Ia menggunakan jadual ini, yang memetakan kod penapis kepada kedudukan tatasusunan setiap saluran:

| Kod penapis | Saluran → indeks tatasusunan |
| --- | --- |
| OCN | Orange 0, Cyan 1, NIR 2 (`Red` diterima sebagai nama lain untuk Orange, juga 0) |
| RGN | Red 0, Green 1, NIR 2 |
| NGB | NIR 0, Green 1, Blue 2 |
| RGB | Red 0, Green 1, Blue 2 |
| RE | RE 0 |
| NIR | NIR 0 |

**Penapis lalai** pratetap (ruangan &quot;Penapis Lalai&quot; di atas) digunakan apabila projek mengandungi imej dengan penapis tersebut. Jika tidak, Chloros memeriksa penapis yang sebenarnya terdapat dalam projek mengikut susunan `RGN, OCN, NGB, RGB, RE, NIR` dan memilih penapis pertama yang dapat membekalkan setiap saluran yang diperlukan oleh pratetap. Jika tiada yang dapat, pratetap itu akan diabaikan untuk larian tersebut. Inilah sebabnya `NDVI` yang diminta pada set data OCN sahaja masih menghasilkan keputusan yang munasabah — ia mengikat pada posisi Orange dan NIR milik OCN.

Rentetan model LATTICE M3C membawa penapis dengan awalan `F` (`LATT-M3C-L41-FRGN`), tetapi awalan itu dibuang apabila kod penapis dibaca daripada imej, sehingga kamera FRGN menyelesaikan melalui baris `RGN` di atas dan tidak memerlukan pengendalian khas.

### 3. Enjin indeks LATTICE (`lattice index --preset`, Pengira Indeks langsung) — 22 pratetap

Enjin LATTICE berfungsi pada susunan berbilang jalur yang selari (susunan langsung atau TIFF berbilang jalur yang dieksport) dan menggunakan nama saluran huruf kecil (`red`, `green`, `blue`, `red_edge`, `nir`). Senarai pratetnya berbeza daripada kedua-dua yang di atas:

| Pratet | Formula | Saluran |
| --- | --- | --- |
| NDVI | `(nir - red) / (nir + red)` | merah, nir |
| GNDVI | `(nir - green) / (nir + green)` | hijau, nir |
| BNDVI | `(nir - blue) / (nir + blue)` | biru, nir |
| NDRE | `(nir - red_edge) / (nir + red_edge)` | merah\_tepi, nir |
| ENDVI | `((nir + green) - 2*blue) / ((nir + green) + 2*blue)` | biru, hijau, nir |
| SAVI | `1.5 * (nir - red) / (nir + red + 0.5)` | merah, nir |
| OSAVI | `1.5 * (nir - red) / (nir + red + 0.16)` | merah, nir |
| MSAVI | `(2*nir + 1 - sqrt((2*nir + 1)**2 - 8*(nir - red))) / 2` | merah, nir |
| EVI | `2.5 * (nir - red) / (nir + 6*red - 7.5*blue + 1)` | biru, merah, nir |
| EVI2 | `2.5 * (nir - red) / (nir + 2.4*red + 1)` | merah, nir |
| CVI | `(nir / green) - (red / green)` | merah, hijau, nir |
| MSR | `((nir/red) - 1) / (sqrt(nir/red) + 1)` | merah, nir |
| TDVI | `sqrt((nir - red) / (nir + red) + 0.5)` | merah, nir |
| LAI | `3.618 * ((nir - red) / (nir + 6*red - 7.5*green + 1)) - 0.118` | merah, hijau, nir |
| GLI | `(2*green - red - blue) / (2*green + red + blue)` | merah, hijau, biru |
| NGRDI | `(green - red) / (green + red)` | merah, hijau |
| VARI | `(green - red) / (green + red - blue)` | merah, hijau, biru |
| TGI | `green - 0.39*red - 0.61*blue` | merah, hijau, biru |
| EXG | `2*green - red - blue` | merah, hijau, biru |
| CIRE | `(nir / red_edge) - 1` | merah\_tepi, nir |
| CIGREEN | `(nir / green) - 1` | hijau, nir |
| NDWI | `(green - nir) / (green + nir)` | hijau, nir |

Jalankan `chloros-cli lattice index --list-presets` untuk mencetak jadual ini daripada binaan yang dipasang, dan `--list-gradients` untuk gradien warna yang tersedia. Simbol saluran adalah sensitif huruf besar/kecil dan mesti sepadan dengan nama huruf kecil pratetap (contohnya `--channel red=Red_660 --channel nir=NIR_850`).

***

## CVI

Seperti yang dilaksanakan dalam GUI dan senarai pratetap CLI / SDK, CVI adalah formula nisbah-daripada-nisbah:

$$
CVI = {(z / y) \over (x / y)}
$$

dengan pemetaan saluran lalai RGB x= Red, y= Green, z= Blue. Dalam GUI anda boleh menyeret mana-mana saluran kamera anda ke dalam slot x/y/z. Perhatikan bahawa preset `CVI` enjin indeks LATTICE menggunakan formula yang berbeza, `(NIR / Green) - (Red / Green)` — semak jadual di atas untuk permukaan yang anda gunakan.

***

## ENDVI - Indeks Vegetasi Perbezaan Normalized yang Ditingkatkan

Indeks ini menggunakan saluran biru selain daripada saluran NIR dan hijau, dan popular dengan kamera yang disaring oleh penapis NGB di mana jalur biru menggantikan merah.

$$
ENDVI = {(NIR + Green) - (2 * Blue) \over (NIR + Green) + (2 * Blue)}
$$

Pelaksanaannya adalah formula simbol `((x+y)-(2*z))/((x+y)+(2*z))` — tetapkan saluran NIR dan Green kamera anda ke slot x/y dan Blue ke z (untuk kamera NGB: x= NIR, y= Green, z= Blue).

***

## EVI - Indeks Vegetasi Terperingkat

Indeks ini pada asalnya dibangunkan untuk digunakan dengan data MODIS sebagai penambahbaikan ke atas NDVI dengan mengoptimumkan isyarat vegetasi di kawasan yang mempunyai indeks kawasan daun (LAI) yang tinggi. Ia paling berguna di kawasan LAI yang tinggi di mana NDVI mungkin menjadi tepu. Ia menggunakan julat pantulan biru untuk membetulkan isyarat latar belakang tanah dan mengurangkan pengaruh atmosfera, termasuk pencebaran aerosol.

$$
EVI = 2.5 *  {(NIR - Red) \over (NIR + 6 * Red - 7.5 * Blue + 1)}
$$

Nilai EVI sepatutnya berkisar antara 0 hingga 1 untuk piksel vegetasi. Ciri cerah seperti awan dan bangunan putih, serta ciri gelap seperti air, boleh mengakibatkan nilai piksel luar biasa dalam imej EVI. Sebelum mencipta imej EVI, anda perlu menutup awan dan ciri-ciri cerah daripada imej pantulan, dan secara pilihan menetapkan ambang nilai piksel daripada 0 hingga 1.

_Rujukan: Huete, A., et al. &quot;Overview of the Radiometric and Biophysical Performance of the MODIS Vegetation Indices.&quot; Remote Sensing of Environment 83 (2002):195–213._

***

## FCI1 - Indeks Tutupan Hutan 1

_GUI sahaja — tidak tersedia sebagai pratetap CLI / SDK `--indices`._

Indeks ini membezakan kanopi hutan daripada jenis vegetasi lain dengan menggunakan imej pantulan multispektral yang merangkumi jalur tepi merah.

$$
FCI1 = Red * RedEdge
$$

Kawasan berhutan akan mempunyai nilai FCI1 yang lebih rendah disebabkan oleh pantulan pokok yang lebih rendah dan kehadiran bayang-bayang dalam kanopi.

Rujukan: Becker, Sarah J., Craig S.T. Daughtry, dan Andrew L. Russ. &quot;Indeks liputan hutan yang mantap untuk imej multispektral.&quot; Photogrammetric Engineering &amp; Remote Sensing 84.8 (2018): 505-512._

***

## FCI2 - Indeks Tutupan Hutan 2

_GUI sahaja — tidak tersedia sebagai pratetap CLI / SDK `--indices`._

Indeks ini membezakan kanopi hutan daripada jenis vegetasi lain dengan menggunakan imej pantulan multispektral yang tidak termasuk jalur tepi merah.

$$
FCI2 = Red * NIR
$$

Kawasan berhutan akan mempunyai nilai FCI2 yang lebih rendah disebabkan oleh pantulan cahaya pokok yang lebih rendah dan kehadiran bayang-bayang dalam kanopi.

Rujukan: Becker, Sarah J., Craig S.T. Daughtry, dan Andrew L. Russ. &quot;Indeks liputan hutan yang mantap untuk imej multispektral.&quot; Photogrammetric Engineering &amp; Remote Sensing 84.8 (2018): 505-512._

***

## GEMI - Indeks Pemantauan Alam Sekitar Global

_GUI sahaja — tidak tersedia sebagai pratetap CLI / SDK `--indices`._

Indeks vegetasi bukan linear ini digunakan untuk pemantauan alam sekitar global daripada imej satelit dan cuba membetulkan kesan atmosfera. Ia serupa dengan NDVI tetapi kurang sensitif terhadap kesan atmosfera. Ia dipengaruhi oleh tanah terbuka; oleh itu, ia tidak disyorkan untuk digunakan di kawasan yang mempunyai vegetasi jarang atau sederhana padat.

$$
GEMI = eta (1 - 0.25 * eta) - {Red - 0.125 \over 1 - Red}
$$

Di mana:

$$
eta = {2(NIR^{2}-Red^{2}) + 1.5 * NIR + 0.5 *  Red \over NIR + Red + 0.5}
$$

_Rujukan: Pinty, B., dan M. Verstraete. GEMI: a Non-Linear Index to Monitor Global Vegetation From Satellites. Vegetation 101 (1992): 15-20._

***

## GARI - Green Indeks Tahan Atmosfera

_GUI sahaja — tidak tersedia sebagai pratetap CLI / SDK `--indices`._

Indeks ini lebih sensitif kepada pelbagai julat kepekatan klorofil dan kurang sensitif kepada kesan atmosfera berbanding NDVI.

$$
GARI = {NIR - [Green - \gamma(Blue - Red)] \over NIR + [Green - \gamma(Blue - Red)]   }
$$

Konstanta gamma adalah fungsi pemberat yang bergantung pada keadaan aerosol di atmosfera. ENVI menggunakan nilai 1.7, iaitu nilai yang disyorkan oleh Gitelson, Kaufman, dan Merzylak (1996, halaman 296).

_Rujukan: Gitelson, A., Y. Kaufman, dan M. Merzylak. &quot;Penggunaan Saluran Green dalam Pengesanan Jauh Vegetasi Global daripada EOS-MODIS.&quot; Remote Sensing of Environment 58 (1996): 289-298._

***

## Indeks Klorofil GCI - Green

Indeks ini digunakan untuk menganggarkan kandungan klorofil daun merentasi pelbagai spesies tumbuhan.

$$
GCI = {NIR \over Green} - 1
$$

Menggunakan panjang gelombang ungu dan hijau yang meluas memberikan ramalan kandungan klorofil yang lebih baik sambil membolehkan kepekaan yang lebih tinggi dan nisbah isyarat-ke-hingar yang lebih tinggi.

_Rujukan: Gitelson, A., Y. Gritz, dan M. Merzlyak. &quot;Hubungan Antara Kandungan Klorofil Daun dan Reflektan Spektral serta Algoritma untuk Penilaian Klorofil Tidak Merosakkan pada Daun Tumbuhan Tinggi.&quot; Jurnal Fisiologi Tumbuhan 160 (2003): 271-282._

***

## GLI - Indeks Daun Green

Indeks ini pada asalnya direka untuk digunakan dengan kamera digital RGB untuk mengukur liputan gandum, di mana nombor digital (DN) merah, hijau, dan biru berkisar dari 0 hingga 255.

$$
GLI = {(Green - Red) + (Green - Blue)  \over (2 * Green) + Red + Blue }
$$

GLI nilai berkisar dari -1 hingga +1. Nilai negatif mewakili tanah dan ciri bukan hidup, manakala nilai positif mewakili daun dan batang hijau.

_Rujukan: Louhaichi, M., M. Borman, dan D. Johnson. &quot;Platform Berlokasi Ruang dan Fotografi Udara untuk Dokumentasi Kesan Gembalaan pada Gandum.&quot; Geocarto International 16, No. 1 (2001): 65-70._

***

## GNDVI - Indeks Perbezaan Vegetasi Normalized Difference Vegetation Index

Indeks ini serupa dengan NDVI kecuali ia mengukur spektrum hijau dari 540 hingga 570 nm dan bukannya spektrum merah. Indeks ini lebih sensitif terhadap kepekatan klorofil berbanding NDVI.

$$
GNDVI = {(NIR - Green) \over (NIR + Green)  }
$$

Rujukan: Gitelson, A., dan M. Merzlyak. &quot;Remote Sensing of Chlorophyll Concentration in Higher Plant Leaves.&quot; Advances in Space Research 22 (1998): 689-692._

***

## GOSAVI - Indeks Vegetasi Disesuaikan Tanah yang Dioptimumkan

Indeks ini pada asalnya direka dengan fotografi warna-inframerah untuk meramalkan keperluan nitrogen bagi jagung. Ia serupa dengan OSAVI, tetapi ia menggantikan jalur hijau dengan merah.

$$
GOSAVI = {NIR - Green \over NIR + Green + 0.16)  }
$$

_Rujukan: Sripada, R., et al. &quot;Penentuan Keperluan Nitrogen Dalam Musim untuk Jagung Menggunakan Fotografi Inframerah-Warna Udara.&quot; Disertasi Ph.D., Universiti Negeri Carolina Utara, 2005._

***

## Indeks Vegetasi Nisbah GRVI - Green

Indeks ini sensitif terhadap kadar fotosintesis di kanopi hutan, kerana pantulan warna hijau dan merah dipengaruhi kuat oleh perubahan pigmen daun.

$$
GRVI = {NIR \over Green }
$$

_Rujukan: Sripada, R., et al. &quot;Fotografi Inframerah Warna Udara untuk Menentukan Keperluan Nitrogen Awal Musim dalam Jagung.&quot; Agronomy Journal 98 (2006): 968-977._

***

## GSAVI - Indeks Tumbuhan Disesuaikan Tanah Green

Indeks ini pada asalnya direka dengan fotografi warna-inframerah untuk meramalkan keperluan nitrogen bagi jagung. Ia serupa dengan SAVI, tetapi ia menggantikan jalur hijau bagi jalur merah.

$$
GSAVI = 1.5 * {(NIR - Green) \over (NIR + Green + 0.5)  }
$$

_Rujukan: Sripada, R., et al. &quot;Penentuan Keperluan Nitrogen Dalam Musim untuk Jagung Menggunakan Fotografi Inframerah Warna Udara.&quot; Disertasi Ph.D., North Carolina State University, 2005._

***

## LAI - Indeks Luas Daun

Indeks ini digunakan untuk menganggarkan liputan dedaunan dan meramalkan pertumbuhan tanaman serta hasil. ENVI mengira LAI hijau menggunakan formula empirik berikut daripada Boegh et al (2002):

$$
LAI = 3.618 * EVI - 0.118
$$

Di mana EVI ialah:

$$
EVI = 2.5 *  {(NIR - Red) \over (NIR + 6 * Red - 7.5 * Blue + 1)}
$$

Nilai LAI yang tinggi biasanya berkisar antara kira-kira 0 hingga 3.5. Walau bagaimanapun, apabila pemandangan mengandungi awan dan ciri-ciri cerah lain yang menghasilkan piksel tepu, nilai LAI boleh melebihi 3.5. Anda sebaiknya menapis awan dan ciri-ciri cerah daripada pemandangan anda sebelum mencipta imej LAI.

Rujukan: Boegh, E., H. Soegaard, N. Broge, C. Hasager, N. Jensen, K. Schelde, dan A. Thomsen. &quot;Data Multi-spektral Udara untuk Mengkuantifikasi Indeks Luas Daun, Kepekatan Nitrogen dan Kecekapan Fotosintesis dalam Pertanian.&quot; Remote Sensing of Environment 81, no. 2-3 (2002): 179-193._

***

## LCI - Indeks Klorofil Daun

_GUI sahaja — tidak tersedia sebagai pratetap CLI / SDK `--indices`._

Indeks ini digunakan untuk menganggarkan kandungan klorofil dalam tumbuhan tinggi, peka terhadap variasi pantulan yang disebabkan oleh penyerapan klorofil.

$$
LCI = {NIR2 - RedEdge \over NIR2 + Red}
$$

_Rujukan: Datt, B. &quot;Remote Sensing of Water Content in Eucalyptus Leaves.&quot; Journal of Plant Physiology 154, no. 1 (1999): 30-36._

***

## MNLI - Indeks Bukan Linear Yang Diubahsuai

Indeks ini adalah penambahbaikan kepada Indeks Bukan Linear (NLI) yang menggabungkan Indeks Vegetasi Disesuaikan Tanah (SAVI) untuk mengambil kira latar belakang tanah. ENVI menggunakan nilai faktor pelarasan latar belakang kanopi (_L_) sebanyak 0.5.

$$
MNLI = {(NIR^{2} - Red) * (1 + L) \over (NIR^{2} + Red + L)  }
$$

Rujukan: Yang, Z., P. Willis, dan R. Mueller. &quot;Impak Imej AWIFS yang Diperbaiki dengan Nisbah Jalur ke Ketepatan Pengelasan Tanaman.&quot; Prosiding Simposium Penginderaan Jauh Pecora 17 (2008), Denver, CO.

***

## MSAVI2 - Indeks Vegetasi Disesuaikan Tanah Diperbaiki 2

Indeks ini adalah versi yang lebih mudah bagi indeks MSAVI yang dicadangkan oleh Qi, et al (1994), yang memperbaiki Indeks Vegetasi Disesuaikan Tanah (SAVI). Ia mengurangkan hingar tanah dan meningkatkan julat dinamik isyarat vegetasi. MSAVI2 berasaskan kaedah induktif yang tidak menggunakan nilai _L_ yang tetap (seperti dalam SAVI) untuk menyerlahkan vegetasi yang sihat.

$$
MSAVI2 = {2 * NIR + 1 - \sqrt{(2 * NIR + 1)^{2} - 8(NIR - Red)} \over 2}
$$

Rujukan: Qi, J., A. Chehbouni, A. Huete, Y. Kerr, dan S. Sorooshian. &quot;A Modified Soil Adjusted Vegetation Index.&quot; Remote Sensing of Environment 48 (1994): 119-126.

***

## MSR - Nisbah Ringkas Dimonopoli

Indeks ini adalah pemodifikasian nisbah ringkas NIR / Red yang direka untuk melinearkan hubungannya dengan parameter biofizikal, dan lebih sensitif berbanding NDVI pada ketumpatan vegetasi yang lebih tinggi.

$$
MSR = {(NIR / Red) - 1 \over \sqrt{NIR / Red} + 1}
$$

_Rujukan: Chen, J. &quot;Penilaian Indeks Vegetasi dan Nisbah Ringkas Dimodifikasi untuk Aplikasi Boreal.&quot; Canadian Journal of Remote Sensing 22 (1996): 229-242._

***

## NDRE - Perbezaan Normalized Difference RedEdge

Indeks ini serupa dengan NDVI tetapi membandingkan kontras antara NIR dengan RedEdge dan bukannya Red, yang sering mengesan tekanan vegetasi lebih awal.

$$
NDRE = {NIR - RedEdge \over NIR + RedEdge  }
$$

***

## NDVI - Indeks Perbezaan Normalized Vegetasi

Indeks ini adalah ukuran vegetasi hijau yang sihat. Gabungan formulasi perbezaan normalizasi dan penggunaan kawasan penyerapan dan pantulan tertinggi klorofil menjadikannya kukuh dalam pelbagai keadaan. Walau bagaimanapun, ia boleh menjadi tepu dalam keadaan vegetasi yang lebat apabila LAI menjadi tinggi.

$$
NDVI = {NIR - Red \over NIR + Red  }
$$

Nilai indeks ini berkisar dari -1 hingga 1. Julat biasa untuk vegetasi hijau ialah 0.2 hingga 0.8.

_Rujukan: Rouse, J., R. Haas, J. Schell, dan D. Deering. Pemantauan Sistem Vegetasi di Dataran Besar dengan ERTS. Simposium ERTS Ketiga, NASA (1973): 309-317._

***

## NLI - Indeks Bukan Linear

Indeks ini menganggap bahawa hubungan antara banyak indeks vegetasi dan parameter biofizikal permukaan adalah tidak linear. Ia melinearkan hubungan dengan parameter permukaan yang cenderung tidak linear.

$$
NLI = {NIR^{2} - Red \over NIR^{2} + Red  }
$$

_Rujukan: Goel, N., dan W. Qin. &quot;Pengaruh Seni Bina Kanopi ke atas Hubungan Antara Pelbagai Indeks Vegetasi dan LAI serta Fpar: Satu Simulasi Komputer.&quot; Remote Sensing Reviews 10 (1994): 309-347._

***

## OSAVI - Indeks Vegetasi Disesuaikan Tanah yang dioptimumkan

Indeks ini berdasarkan Indeks Vegetasi Disesuaikan Tanah (Soil Adjusted Vegetation Index - SAVI). Ia menggunakan nilai piawai 0.16 untuk faktor pelarasan latar belakang kanopi. Rondeaux (1996) menentukan bahawa nilai ini menyediakan variasi tanah yang lebih besar berbanding SAVI untuk liputan tumbuhan yang rendah, sambil menunjukkan kepekaan yang meningkat kepada liputan tumbuhan melebihi 50%. Indeks ini paling sesuai digunakan di kawasan dengan tumbuhan yang agak jarang di mana tanah kelihatan melalui kanopi.

$$
OSAVI = {(NIR - Red) \over (NIR + Red + 0.16)  }
$$

_Rujukan: Rondeaux, G., M. Steven, dan F. Baret. &quot;Pengoptimuman Indeks Vegetasi Disesuaikan Tanah.&quot; Remote Sensing of Environment 55 (1996): 95-107._

***

## RDVI - Indeks Vegetasi Perbezaan Terrenormal

Indeks ini menggunakan perbezaan antara panjang gelombang inframerah dekat dan merah, bersama-sama dengan Indeks Refleksi Tanah (NDVI), untuk menyerlahkan vegetasi yang sihat. Ia tidak peka terhadap kesan geometri pandangan tanah dan matahari.

$$
RDVI = {(NIR- Red) \over \sqrt{(NIR + Red)}  }
$$

_Rujukan: Roujean, J., dan F. Breon. &quot;Anggaran PAR Diserap oleh Vegetasi daripada Pengukuran Reflektansi Dua Arah.&quot; Remote Sensing of Environment 51 (1995): 375-384._

***

## SAVI - Indeks Vegetasi Terlerap Tanah

Indeks ini serupa dengan NDVI, tetapi ia menindas kesan piksel tanah. Ia menggunakan faktor pelarasan latar belakang kanopi, _L_, yang merupakan fungsi ketumpatan vegetasi dan sering memerlukan pengetahuan terdahulu tentang jumlah vegetasi. Huete (1988)  mencadangkan nilai optimum _L_=0.5 untuk mengambil kira variasi latar belakang tanah peringkat pertama. Indeks ini paling sesuai digunakan di kawasan dengan vegetasi yang agak jarang di mana tanah kelihatan melalui kanopi.

$$
SAVI = {1.5 * (NIR- Red) \over (NIR + Red + 0.5)  }
$$

Rujukan: Huete, A. &quot;A Soil-Adjusted Vegetation Index (SAVI).&quot; Remote Sensing of Environment 25 (1988): 295-309._

***

## TDVI - Indeks Vegetasi Perbezaan Tertransformasi

Indeks ini berguna untuk memantau liputan vegetasi dalam persekitaran bandar. Ia tidak menjadi tepu seperti NDVI dan SAVI.

$$
TDVI = 1.5 * {(NIR- Red) \over \sqrt{NIR^{2} + Red + 0.5}  }
$$

_Rujukan: Bannari, A., H. Asalhi, dan P. Teillet. &quot;Indeks Perbezaan Vegetasi Tertransform (TDVI) untuk Pemetaan Tutupan Vegetasi&quot; Dalam Prosiding Simposium Geosains dan Pengesanan Jauh, IGARSS &#x27;02, Antarabangsa IEEE, Jilid 5 (2002)._

***

## VARI - Indeks Terlihat Tahan Atmosfera

Indeks ini berdasarkan Indeks Perbezaan Vegetasi (ARVI) dan digunakan untuk menganggarkan fraksi vegetasi dalam sesuatu pemandangan dengan kepekaan yang rendah terhadap kesan atmosfera.

$$
VARI = {Green - Red \over Green + Red - Blue  }
$$

Rujukan: Gitelson, A., et al. &quot;Vegetation and Soil Lines in Visible Spectral Space: A Concept and Technique for Remote Estimation of Vegetation Fraction. International Journal of Remote Sensing 23 (2002): 2537−2562._

***

## WDRVI - Indeks Vegetasi Julat Dinamik Luas

Indeks ini serupa dengan NDVI, tetapi ia menggunakan pekali pemberatan (_a_) untuk mengurangkan perbezaan antara sumbangan isyarat inframerah dekat dan merah kepada NDVI. WDRVI amat berkesan dalam adegan yang mempunyai ketumpatan vegetasi sederhana hingga tinggi apabila NDVI melebihi 0.6. NDVI cenderung menjadi rata apabila fraksi vegetasi dan indeks kawasan daun (LAI) meningkat, manakala WDRVI lebih sensitif kepada julat fraksi vegetasi yang lebih luas dan kepada perubahan dalam LAI.

$$
WDRVI = {(\alpha * NIR- Red) \over (\alpha * NIR + Red)}
$$

Pendekatan pemberat (a) boleh berkisar antara 0.1 hingga 0.2. Nilai 0.2 dicadangkan oleh Henebry, Viña, dan Gitelson (2004).

_Rujukan_

_Gitelson, A. &quot;Indeks Vegetasi Julat Dinamik Luas untuk Pengkuantitian Jauh Ciri-ciri Biofizikal Vegetasi.&quot; Journal of Plant Physiology 161, No. 2 (2004): 165-173._

_Henebry, G., A. Viña, dan A. Gitelson. &quot;Indeks Vegetasi Julat Dinamik Luas dan Kegunaan Potensialnya untuk Analisis Jurang.&quot; Buletin Analisis Jurang 12: 50-56._
