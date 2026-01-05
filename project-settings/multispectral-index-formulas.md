---
description: This page lists some multispectral indices that Chloros uses
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/multispectral-index-formulas
---

# Formula Indeks Berbilang Spektrum

Formula indeks di bawah menggunakan gabungan julat penghantaran purata penapis Survey3:

<table><thead><tr><th align="center">Survey3 Warna Penapis</th><th width="196.199951171875" align="center">Survey3 Nama Penapis</th><th width="159.8001048828Julat Transmisi" (FWHM)</th><th align="center">Purata Penghantaran</th></tr></thead><tbody><tr><td align="center">Blue</td><td align="center">NGB - XPROTX00009d8 align="center">468-483nm</td><td align="center">475nm</td></tr><tr><td align="center">Cyan</td><td align="center">OCN- XPROTX00011t4 align="center">476-512nm</td><td align="center">494nm</td></tr><tr><td align="center">Green</td><td align="center">RGN | NGB - Green</td><td align="center">543-558nm</td><td align="center">547nm</td></tr><tr><td align="center">Oranged</td><td align="center">OCN - Orange</td><td align="center">598-640nm</td><td align="center">619nm</td></tr><tr><td align="center">XPROXt><PROTXd align="center">RGN - Red</td><td align="center">653-668nm</td><td align="center">661nm</td></tr><tr><td align="center">XPROXt><tdPROTX000109 align="center">Re - RedEdge</td><td align="center">712-735nm</td><td align="center">724nm</td></tr><tr><td align="center">NIR1</td><td align="center">OCN - NIR1</td><td align="center">798-848nm</td><td align="center">823nm</td></tr><tr><td align="center">XPROTX00010td6 align="center">RGN | NGB | NIR - NIR2</td><td align="center">835-865nm</td><td align="center">850nm</td></tr></tbody></table>

Apabila formula ini digunakan nama mungkin berakhir dengan "\_1" atau "\_2", yang sepadan dengan penapis NIR, sama ada NIR1 atau NIR2 digunakan.

***

## EVI - Indeks Tumbuhan Dipertingkat

Indeks ini pada asalnya dibangunkan untuk digunakan dengan data MODIS sebagai penambahbaikan berbanding NDVI dengan mengoptimumkan isyarat tumbuh-tumbuhan di kawasan indeks kawasan daun tinggi (LAI). Ia paling berguna di kawasan LAI tinggi di mana NDVI mungkin tepu. Ia menggunakan kawasan pemantulan biru untuk membetulkan isyarat latar belakang tanah dan untuk mengurangkan pengaruh atmosfera, termasuk penyerakan aerosol.

$$
EVI = 2.5 *  {(NIR - Red) \over (NIR + 6 * Red - 7.5 * Blue + 1)}
$$

Nilai EVI hendaklah berjulat dari 0 hingga 1 untuk piksel tumbuh-tumbuhan. Ciri terang seperti awan dan bangunan putih, bersama-sama dengan ciri gelap seperti air, boleh menghasilkan nilai piksel anomali dalam imej EVI. Sebelum mencipta imej EVI, anda harus menutup awan dan ciri terang daripada imej pemantulan, dan ambang nilai piksel secara pilihan daripada 0 hingga 1.

_Rujukan: Huete, A., et al. "Tinjauan Keseluruhan Prestasi Radiometrik dan Biofizik bagi Indeks Tumbuhan MODIS." Penderiaan Jauh Persekitaran 83 (2002):195–213._

***

## FCI1 - Indeks Tutupan Hutan 1

Indeks ini membezakan kanopi hutan daripada jenis tumbuh-tumbuhan lain menggunakan imejan pantulan berbilang spektrum yang merangkumi jalur tepi merah.

$$
FCI1 = Red * RedEdge
$$

Kawasan berhutan akan mempunyai nilai FCI1 yang lebih rendah disebabkan oleh pantulan pokok yang lebih rendah dan kehadiran bayang-bayang dalam kanopi.

_Rujukan: Becker, Sarah J., Craig S.T. Daughtry, dan Andrew L. Russ. "Indeks litupan hutan yang teguh untuk imej berbilang spektrum." Kejuruteraan Fotogrametrik & Penderiaan Jauh 84.8 (2018): 505-512._

***

## FCI2 - Indeks Tutupan Hutan 2

Indeks ini membezakan kanopi hutan daripada jenis tumbuh-tumbuhan lain menggunakan imejan pantulan berbilang spektrum yang tidak termasuk jalur tepi merah.

$$
FCI2 = Red * NIR
$$

Kawasan berhutan akan mempunyai nilai FCI2 yang lebih rendah disebabkan oleh pantulan pokok yang lebih rendah dan kehadiran bayang-bayang dalam kanopi.

_Rujukan: Becker, Sarah J., Craig S.T. Daughtry, dan Andrew L. Russ. "Indeks litupan hutan yang teguh untuk imej berbilang spektrum." Kejuruteraan Fotogrametrik & Penderiaan Jauh 84.8 (2018): 505-512._

***

## GEMI - Indeks Pemantauan Alam Sekitar Global

Indeks tumbuh-tumbuhan bukan linear ini digunakan untuk pemantauan alam sekitar global daripada imejan satelit dan percubaan untuk membetulkan kesan atmosfera. Ia serupa dengan NDVI tetapi kurang sensitif kepada kesan atmosfera. Ia dipengaruhi oleh tanah kosong; oleh itu, ia tidak disyorkan untuk digunakan di kawasan tumbuh-tumbuhan yang jarang atau sederhana padat.

$$
GEMI = eta (1 - 0.25 * eta) - {Red - 0.125 \over 1 - Red}
$$

di mana:

$$
eta = {2(NIR^{2}-Red^{2}) + 1.5 * NIR + 0.5 *  Red \over NIR + Red + 0.5}
$$

_Rujukan: Pinty, B., dan M. Verstraete. GEMI: Indeks Bukan Linear untuk Memantau Tumbuhan Global Daripada Satelit. Tumbuhan 101 (1992): 15-20._

***

## GARI - Green Indeks Tahan Atmosfera

Indeks ini lebih sensitif kepada julat luas kepekatan klorofil dan kurang sensitif terhadap kesan atmosfera berbanding NDVI.

$$
GARI = {NIR - [Green - \gamma(Blue - Red)] \over NIR + [Green - \gamma(Blue - Red)]   }
$$

Pemalar gamma ialah fungsi pemberat yang bergantung kepada keadaan aerosol di atmosfera. ENVI menggunakan nilai 1.7, iaitu nilai yang disyorkan daripada Gitelson, Kaufman, dan Merzylak (1996, halaman 296).

_Rujukan: Gitelson, A., Y. Kaufman, dan M. Merzylak. "Penggunaan Saluran Green dalam Penderiaan Jauh Tumbuhan Global daripada EOS-MODIS." Penderiaan Jauh Persekitaran 58 (1996): 289-298._

***

## GCI - Green Indeks Klorofil

Indeks ini digunakan untuk menganggarkan kandungan klorofil daun merentas pelbagai spesies tumbuhan.

$$
GCI = {NIR \over Green} - 1
$$

Mempunyai NIR yang luas dan panjang gelombang hijau memberikan ramalan kandungan klorofil yang lebih baik sambil membenarkan lebih sensitiviti dan nisbah isyarat-ke-bunyi yang lebih tinggi.

_Rujukan: Gitelson, A., Y. Gritz, dan M. Merzlyak. "Hubungan Antara Kandungan Klorofil Daun dan Pantulan Spektrum dan Algoritma untuk Penilaian Klorofil Tidak Memusnahkan dalam Daun Tumbuhan Tinggi." Jurnal Fisiologi Tumbuhan 160 (2003): 271-282._

***

## GLI - Green Leaf Index

Indeks ini pada asalnya direka bentuk untuk digunakan dengan kamera RGB digital untuk mengukur penutup gandum, di mana nombor digital (DN) merah, hijau dan biru berjulat dari 0 hingga 255.

$$
GLI = {(Green - Red) + (Green - Blue)  \over (2 * Green) + Red + Blue }
$$

Nilai GLI berjulat dari -1 hingga +1. Nilai negatif mewakili ciri tanah dan bukan hidup, manakala nilai positif mewakili daun dan batang hijau.

_Rujukan: Louhaichi, M., M. Borman, dan D. Johnson. "Platform Bertempat dan Fotografi Udara untuk Dokumentasi Kesan Ragut Terhadap Gandum." Geocarto International 16, No. 1 (2001): 65-70._

***

## GNDVI - Green Indeks Tumbuhan Perbezaan Ternormal

Indeks ini serupa dengan NDVI kecuali ia mengukur spektrum hijau dari 540 hingga 570 nm dan bukannya spektrum merah. Indeks ini lebih sensitif kepada kepekatan klorofil daripada NDVI.

$$
GNDVI = {(NIR - Green) \over (NIR + Green)  }
$$

_Rujukan: Gitelson, A., dan M. Merzlyak. "Penderiaan Jauh Kepekatan Klorofil dalam Daun Tumbuhan Tinggi." Kemajuan dalam Penyelidikan Angkasa 22 (1998): 689-692._

***

## GOSAVI - Green Indeks Tumbuhan Dilaraskan Tanah Dioptimumkan

Indeks ini pada asalnya direka dengan fotografi inframerah warna untuk meramalkan keperluan nitrogen untuk jagung. Ia serupa dengan OSAVI, tetapi ia menggantikan jalur hijau dengan merah.

$$
GOSAVI = {NIR - Green \over NIR + Green + 0.16)  }
$$

_Rujukan: Sripada, R., et al. "Menentukan Keperluan Nitrogen Dalam Musim untuk Jagung Menggunakan Fotografi Inframerah Warna Udara." Ph.D. disertasi, North Carolina State University, 2005._

***

## GRVI - Green Nisbah Indeks Tumbuhan

Indeks ini sensitif kepada kadar fotosintesis dalam kanopi hutan, kerana pantulan hijau dan merah sangat dipengaruhi oleh perubahan pigmen daun.

$$
GRVI = {NIR \over Green }
$$

_Rujukan: Sripada, R., et al. "Fotografi Inframerah Warna Udara untuk Menentukan Keperluan Nitrogen Awal Dalam Musim dalam Jagung." Jurnal Agronomi 98 (2006): 968-977._

***

## GSAVI - Green Indeks Tumbuhan Dilaraskan Tanah

Indeks ini pada asalnya direka dengan fotografi inframerah warna untuk meramalkan keperluan nitrogren untuk jagung. Ia serupa dengan SAVI, tetapi ia menggantikan jalur hijau dengan merah.

$$
GSAVI = 1.5 * {(NIR - Green) \over (NIR + Green + 0.5)  }
$$

_Rujukan: Sripada, R., et al. "Menentukan Keperluan Nitrogen Dalam Musim untuk Jagung Menggunakan Fotografi Inframerah Warna Udara." Ph.D. disertasi, North Carolina State University, 2005._

***

## LAI - Indeks Luas Daun

Indeks ini digunakan untuk menganggarkan litupan dedaun dan meramalkan pertumbuhan dan hasil tanaman. ENVI mengira LAI hijau menggunakan formula empirikal berikut dari Boegh et al (2002):

$$
LAI = 3.618 * EVI - 0.118
$$

Di mana EVI ialah:

$$
EVI = 2.5 *  {(NIR - Red) \over (NIR + 6 * Red - 7.5 * Blue + 1)}
$$

Nilai LAI yang tinggi biasanya berjulat daripada kira-kira 0 hingga 3.5. Walau bagaimanapun, apabila adegan itu mengandungi awan dan ciri terang lain yang menghasilkan piksel tepu, nilai LAI boleh melebihi 3.5. Sebaik-baiknya, anda harus menutup awan dan ciri terang daripada pemandangan anda sebelum mencipta imej LAI.

_Rujukan: Boegh, E., H. Soegaard, N. Broge, C. Hasager, N. Jensen, K. Schelde, dan A. Thomsen. "Data Berbilang Spektrum Bawaan Udara untuk Mengukur Indeks Luas Daun, Kepekatan Nitrogen dan Kecekapan Fotosintesis dalam Pertanian." Penderiaan Jauh Persekitaran 81, no. 2-3 (2002): 179-193._

***

## LCI - Indeks Klorofil Daun

Indeks ini digunakan untuk menganggarkan kandungan klorofil dalam tumbuhan yang lebih tinggi, sensitif kepada variasi dalam pemantulan yang disebabkan oleh penyerapan klorofil.

$$
LCI = {NIR2 - RedEdge \over NIR2 + Red}
$$

_Rujukan: Datt, B. "Penderiaan Jauh Kandungan Air dalam Daun Eucalyptus." Jurnal Fisiologi Tumbuhan 154, no. 1 (1999): 30-36._

***

## MNLI - Indeks Bukan Linear Ubahsuai

Indeks ini adalah peningkatan kepada Indeks Bukan Linear (NLI) yang menggabungkan Indeks Tumbuhan Terlaras Tanah (SAVI) untuk mengambil kira latar belakang tanah. ENVI menggunakan nilai faktor pelarasan latar belakang kanopi (_L_) sebanyak 0.5.

$$
MNLI = {(NIR^{2} - Red) * (1 + L) \over (NIR^{2} + Red + L)  }
$$

_Rujukan: Yang, Z., P. Willis, dan R. Mueller. "Kesan Imej AWIFS Dipertingkat Nisbah Jalur kepada Ketepatan Klasifikasi Pangkas." Prosiding Simposium Penderiaan Jauh Pecora 17 (2008), Denver, CO._

***

## MSAVI2 - Indeks Tumbuhan Terlaras Tanah Ubahsuai 2

Indeks ini ialah versi yang lebih ringkas bagi indeks MSAVI yang dicadangkan oleh Qi, et al (1994), yang menambah baik daripada Indeks Tumbuhan Terlaras Tanah (SAVI). Ia mengurangkan bunyi tanah dan meningkatkan julat dinamik isyarat tumbuh-tumbuhan. MSAVI2 adalah berdasarkan kaedah induktif yang tidak menggunakan nilai _L_ malar (seperti SAVI) untuk menyerlahkan tumbuh-tumbuhan yang sihat.

$$
MSAVI2 = {2 * NIR + 1 - \sqrt{(2 * NIR + 1)^{2} - 8(NIR - Red)} \over 2}
$$

_Rujukan: Qi, J., A. Chehbouni, A. Huete, Y. Kerr, dan S. Sorooshian. "Indeks Tumbuhan Terlaras Tanah Terubahsuai." Penderiaan Jauh Persekitaran 48 (1994): 119-126._

***

## NDRE- Perbezaan Normal RedEdge

Indeks ini serupa dengan NDVI tetapi membandingkan kontras antara NIR dengan RedEdge dan bukannya Red, yang sering mengesan tekanan tumbuh-tumbuhan lebih awal.

$$
NDRE = {NIR - RedEdge \over NIR + RedEdge  }
$$

***

## NDVI - Indeks Tumbuhan Perbezaan Normal

Indeks ini adalah ukuran tumbuh-tumbuhan hijau yang sihat. Gabungan perumusan perbezaan ternormalnya dan penggunaan kawasan penyerapan dan pemantulan tertinggi klorofil menjadikannya teguh dalam pelbagai keadaan. Walau bagaimanapun, ia boleh tepu dalam keadaan tumbuh-tumbuhan yang lebat apabila LAI menjadi tinggi.

$$
NDVI = {NIR - Red \over NIR + Red  }
$$

Nilai indeks ini berjulat dari -1 hingga 1. Julat biasa untuk tumbuh-tumbuhan hijau ialah 0.2 hingga 0.8.

_Rujukan: Rouse, J., R. Haas, J. Schell, dan D. Deering. Memantau Sistem Tumbuhan di Great Plains dengan ERTS. Simposium ERTS Ketiga, NASA (1973): 309-317._

***

## NLI - Indeks Bukan Linear

Indeks ini menganggap bahawa hubungan antara banyak indeks tumbuh-tumbuhan dan parameter biofizikal permukaan adalah bukan linear. Ia melinearkan hubungan dengan parameter permukaan yang cenderung tidak linear.

$$
NLI = {NIR^{2} - Red \over NIR^{2} + Red  }
$$

_Rujukan: Goel, N., dan W. Qin. "Pengaruh Seni Bina Kanopi terhadap Hubungan Antara Pelbagai Indeks Tumbuhan dan LAI dan Fpar: Simulasi Komputer." Ulasan Penderiaan Jauh 10 (1994): 309-347._

***

## OSAVI - Indeks Tumbuhan Dilaraskan Tanah Dioptimumkan

Indeks ini adalah berdasarkan Indeks Tumbuhan Terlaras Tanah (SAVI). Ia menggunakan nilai standard 0.16 untuk faktor pelarasan latar belakang kanopi. Rondeaux (1996) menentukan bahawa nilai ini memberikan variasi tanah yang lebih besar daripada SAVI untuk litupan tumbuh-tumbuhan yang rendah, sambil menunjukkan peningkatan kepekaan terhadap litupan tumbuh-tumbuhan lebih daripada 50%. Indeks ini paling baik digunakan di kawasan yang mempunyai tumbuh-tumbuhan yang agak jarang di mana tanah boleh dilihat melalui kanopi.

$$
OSAVI = {(NIR - Red) \over (NIR + Red + 0.16)  }
$$

_Rujukan: Rondeaux, G., M. Steven, dan F. Baret. "Pengoptimuman Indeks Tumbuhan Dilaraskan Tanah." Penderiaan Jauh Persekitaran 55 (1996): 95-107._

***

## RDVI - Indeks Tumbuhan Perbezaan Dinormalisasi Semula

Indeks ini menggunakan perbezaan antara panjang gelombang inframerah dekat dan merah, bersama-sama dengan NDVI, untuk menyerlahkan tumbuh-tumbuhan yang sihat. Ia tidak sensitif kepada kesan geometri melihat tanah dan matahari.

$$
RDVI = {(NIR- Red) \over \sqrt{(NIR + Red)}  }
$$

_Rujukan: Roujean, J., dan F. Breon. "Menganggarkan PAR yang Diserap oleh Tumbuhan daripada Pengukuran Pantulan Dwi Arah." Penderiaan Jauh Persekitaran 51 (1995): 375-384._

***

## SAVI - Indeks Tumbuhan Terlaras Tanah

Indeks ini serupa dengan NDVI, tetapi ia menyekat kesan piksel tanah. Ia menggunakan faktor pelarasan latar belakang kanopi, _L_, yang merupakan fungsi kepadatan tumbuh-tumbuhan dan selalunya memerlukan pengetahuan awal tentang jumlah tumbuh-tumbuhan. Huete (1988) mencadangkan nilai optimum _L_=0.5 untuk mengambil kira variasi latar belakang tanah urutan pertama. Indeks ini paling baik digunakan di kawasan yang mempunyai tumbuh-tumbuhan yang agak jarang di mana tanah boleh dilihat melalui kanopi.

$$
SAVI = {1.5 * (NIR- Red) \over (NIR + Red + 0.5)  }
$$

_Rujukan: Huete, A. "Indeks Tumbuhan Terlaras Tanah (SAVI)." Penderiaan Jauh Persekitaran 25 (1988): 295-309._

***

## TDVI - Indeks Tumbuhan Perbezaan Berubah

Indeks ini berguna untuk memantau litupan tumbuh-tumbuhan di persekitaran bandar. Ia tidak tepu seperti NDVI dan SAVI.

$$
TDVI = 1.5 * {(NIR- Red) \over \sqrt{NIR^{2} + Red + 0.5}  }
$$

_Rujukan: Bannari, A., H. Asalhi, dan P. Teillet. "Transformed Difference Vegetation Index (TDVI) for Vegetation Cover Mapping" Dalam Prosiding Geosains dan Simposium Penderiaan Jauh, IGARSS '02, IEEE International, Jilid 5 (2002)._

***

## VARI - Kelihatan Indeks Tahan Atmosfera

Indeks ini adalah berdasarkan ARVI dan digunakan untuk menganggarkan pecahan tumbuh-tumbuhan dalam pemandangan dengan kepekaan rendah terhadap kesan atmosfera.

$$
VARI = {Green - Red \over Green + Red - Blue  }
$$

_Rujukan: Gitelson, A., et al. "Tumbuhan dan Garisan Tanah dalam Ruang Spektrum Kelihatan: Konsep dan Teknik untuk Anggaran Jauh Pecahan Tumbuhan. Jurnal Antarabangsa Penderiaan Jauh 23 (2002): 2537−2562._

***

## WDRVI - Indeks Tumbuhan Julat Dinamik Luas

Indeks ini serupa dengan NDVI, tetapi ia menggunakan pekali pemberat (_a_) untuk mengurangkan jurang perbezaan antara sumbangan isyarat inframerah dekat dan merah kepada NDVI. WDRVI amat berkesan dalam adegan yang mempunyai ketumpatan tumbuh-tumbuhan sederhana hingga tinggi apabila NDVI melebihi 0.6. NDVI cenderung mendatar apabila pecahan tumbuh-tumbuhan dan indeks kawasan daun (LAI) meningkat, manakala WDRVI lebih sensitif kepada julat pecahan tumbuh-tumbuhan yang lebih luas dan kepada perubahan dalam LAI.

$$
WDRVI = {(\alpha * NIR- Red) \over (\alpha * NIR + Red)}
$$

Pekali pemberat (_a_) boleh berjulat dari 0.1 hingga 0.2. Nilai 0.2 disyorkan oleh Henebry, Viña, dan Gitelson (2004).

_Rujukan_

_Gitelson, A. "Indeks Tumbuhan Julat Dinamik Luas untuk Kuantifikasi Jauh Ciri Biofizik Tumbuhan." Jurnal Fisiologi Tumbuhan 161, No. 2 (2004): 165-173._

_Henebry, G., A. Viña, dan A. Gitelson. "Indeks Tumbuhan Julat Dinamik Luas dan Utiliti Potensinya untuk Analisis Jurang." Buletin Analisis Jurang 12: 50-56._