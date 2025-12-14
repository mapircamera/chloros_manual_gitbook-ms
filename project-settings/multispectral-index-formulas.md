---
Penerangan: Halaman ini menyenaraikan beberapa indeks multispektral yang digunakan oleh kloros
Metalinks:
  Ganti:
    - >-
      https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/multispectral-index-formulas
---

# Formula indeks multispektral

Formula indeks di bawah menggunakan gabungan julat penghantaran purata penapis Survey3:

<table><thead><tr><th align="center">Survey3 Filter Color</th><th width="196.199951171875" align="center">Survey3 Filter Name</th><th width="159.800048828125" align="center">Transmission Range (FWHM)</th><th align="center">Average Transmission</th></tr></thead><tbody><tr><td align="center">Blue</td><td align="center">NGB - Blue</td><td align="center">468-483nm</td><td align="center">475nm</td></tr><tr><td align="center">Cyan</td><td align="center">OCN- Cyan</td><td align="center">476-512nm</td><td align="center">494nm</td></tr><tr><td align="center">Green</td><td align="center">RGN | NGB - Green</td><td align="center">543-558nm</td><td align="center">547nm</td></tr><tr><td align="center">Orange</td><td align="center">OCN - Orange</td><td align="center">598-640nm</td><td align="center">619nm</td></tr><tr><td align="center">Red</td><td align="center">RGN - Red</td><td align="center">653-668nm</td><td align="center">661nm</td></tr><tr><td align="center">RedEdge</td><td align="center">Re - RedEdge</td><td align="center">712-735nm</td><td align="center">724nm</td></tr><tr><td align="center">NIR1</td><td align="center">OCN - NIR1</td><td align="center">798-848nm</td><td align="center">823nm</td></tr><tr><td align="center">NIR2</td><td align="center">RGN | NGB | NIR - NIR2</td><td align="center">835-865nm</td><td align="center">850nm</td></tr></tbody></table>

Apabila formula ini digunakan nama mungkin berakhir dalam "\ _1" atau "\ _2", yang sepadan dengan penapis NIR, sama ada NIR1 atau NIR2 digunakan.

***

## evi - Indeks tumbuh -tumbuhan yang dipertingkatkan

Indeks ini pada asalnya dibangunkan untuk digunakan dengan data MODIS sebagai peningkatan ke atas NDVI dengan mengoptimumkan isyarat tumbuh -tumbuhan di kawasan Indeks Kawasan Daun Tinggi (LAI). Ia paling berguna di kawasan LAI yang tinggi di mana NDVI boleh menembusi. Ia menggunakan rantau pemantulan biru untuk membetulkan isyarat latar belakang tanah dan untuk mengurangkan pengaruh atmosfera, termasuk penyebaran aerosol.

$$
EVI = 2.5 *  {(NIR - Red) \over (NIR + 6 * Red - 7.5 * Blue + 1)}
$$

Nilai EVI harus berkisar dari 0 hingga 1 untuk piksel tumbuh -tumbuhan. Ciri -ciri terang seperti awan dan bangunan putih, bersama -sama dengan ciri -ciri gelap seperti air, boleh menghasilkan nilai piksel anomali dalam imej EVI. Sebelum mencipta imej EVI, anda harus menutup awan dan ciri -ciri cerah dari imej refleksi, dan secara opsyen ambang nilai piksel dari 0 hingga 1.

_Reference: Huete, A., et al. "Gambaran keseluruhan prestasi radiometrik dan biophysical indeks tumbuh -tumbuhan MODIS." Pengesan Alam Sekitar Jauh 83 (2002): 195-213._

***

## FCI1 - Indeks Perlindungan Hutan 1

Indeks ini membezakan kanopi hutan dari jenis tumbuh -tumbuhan lain yang menggunakan imejan refleksi multispektral yang merangkumi band tepi merah.

$$
FCI1 = Red * RedEdge
$$

Kawasan berhutan akan mempunyai nilai FCI1 yang lebih rendah disebabkan oleh pemantulan pokok yang lebih rendah dan kehadiran bayang -bayang dalam kanopi.

_Reference: Becker, Sarah J., Craig S.T. Daughtry, dan Andrew L. Russ. "Indeks perlindungan hutan yang teguh untuk imej multispektral." Kejuruteraan Photogrammetric & Remote Sensing 84.8 (2018): 505-512._

***

## FCI2 - Indeks Perlindungan Hutan 2

Indeks ini membezakan kanopi hutan dari jenis tumbuh -tumbuhan lain yang menggunakan imejan refleksi multispektral yang tidak termasuk band tepi merah.

$$
FCI2 = Red * NIR
$$

Kawasan berhutan akan mempunyai nilai FCI2 yang lebih rendah disebabkan oleh pemantulan pokok yang lebih rendah dan kehadiran bayang -bayang dalam kanopi.

_Reference: Becker, Sarah J., Craig S.T. Daughtry, dan Andrew L. Russ. "Indeks perlindungan hutan yang teguh untuk imej multispektral." Kejuruteraan Photogrammetric & Remote Sensing 84.8 (2018): 505-512._

***

## Gemi - Indeks Pemantauan Alam Sekitar Global

Indeks tumbuh-tumbuhan bukan linear ini digunakan untuk pemantauan alam sekitar global dari imejan satelit dan percubaan untuk membetulkan kesan atmosfera. Ia sama dengan NDVI tetapi kurang sensitif terhadap kesan atmosfera. Ia dipengaruhi oleh tanah kosong; Oleh itu, tidak disyorkan untuk digunakan di kawasan tumbuh -tumbuhan yang jarang atau sederhana.

$$
GEMI = eta (1 - 0.25 * eta) - {Red - 0.125 \over 1 - Red}
$$

Di mana:

$$
eta = {2(NIR^{2}-Red^{2}) + 1.5 * NIR + 0.5 *  Red \over NIR + Red + 0.5}
$$

_Reference: Pinty, B., dan M. Verstraete. GEMI: Indeks bukan linear untuk memantau tumbuh-tumbuhan global dari satelit. Vegetation 101 (1992): 15-20._

***

## gari - indeks tahan atmosfera hijau

Indeks ini lebih sensitif terhadap pelbagai kepekatan klorofil dan kurang sensitif terhadap kesan atmosfera daripada NDVI.

$$
GARI = {NIR - [Green - \gamma(Blue - Red)] \over NIR + [Green - \gamma(Blue - Red)]   }
$$

Pemalar gamma adalah fungsi pemberat yang bergantung kepada keadaan aerosol di atmosfera. Envi menggunakan nilai 1.7, yang merupakan nilai yang disyorkan dari Gitelson, Kaufman, dan Merzylak (1996, halaman 296).

_Reference: Gitelson, A., Y. Kaufman, dan M. Merzylak. "Penggunaan saluran hijau dalam penderiaan jauh tumbuh-tumbuhan global dari EOS-Modi." Pengesan Alam Sekitar Jauh 58 (1996): 289-298._***

## GCI - Indeks klorofil hijau

Indeks ini digunakan untuk menganggarkan kandungan klorofil daun merentasi pelbagai spesies tumbuhan.

$$
GCI = {NIR \over Green} - 1
$$

Mempunyai panjang NIR dan panjang gelombang hijau memberikan ramalan yang lebih baik mengenai kandungan klorofil sambil membolehkan lebih banyak kepekaan dan nisbah isyarat-ke-bunyi yang lebih tinggi.

_Reference: Gitelson, A., Y. Gritz, dan M. Merzlyak. "Hubungan antara kandungan klorofil daun dan pemantulan spektrum dan algoritma untuk penilaian klorofil yang tidak merosakkan di daun tumbuhan yang lebih tinggi." Jurnal Fisiologi Loji 160 (2003): 271-282._

***

## GLI - Indeks daun hijau

Indeks ini pada asalnya direka untuk digunakan dengan kamera RGB digital untuk mengukur penutup gandum, di mana nombor digital merah, hijau, dan biru (DNS) berkisar antara 0 hingga 255.

$$
GLI = {(Green - Red) + (Green - Blue)  \over (2 * Green) + Red + Blue }
$$

Nilai GLI berkisar dari -1 hingga +1. Nilai negatif mewakili ciri tanah dan tidak hidup, manakala nilai positif mewakili daun dan batang hijau.

_Reference: Loaichi, M., M. Borman, dan D. Johnson. "Platform spasial dan fotografi udara untuk dokumentasi kesan ragut pada gandum." Geocarto International 16, No. 1 (2001): 65-70._

***

## GNDVI - Indeks Vegetasi Perbezaan Normal Perbezaan Hijau

Indeks ini sama dengan NDVI kecuali ia mengukur spektrum hijau dari 540 hingga 570 nm dan bukannya spektrum merah. Indeks ini lebih sensitif terhadap kepekatan klorofil daripada NDVI.

$$
GNDVI = {(NIR - Green) \over (NIR + Green)  }
$$

_Reference: Gitelson, A., dan M. Merzlyak. "Penginderaan jauh kepekatan klorofil di daun tumbuhan yang lebih tinggi." Kemajuan dalam Penyelidikan Angkasa 22 (1998): 689-692._***

## Gosavi - Indeks tumbuh -tumbuhan diselaraskan tanah yang dioptimumkan hijau

Indeks ini pada asalnya direka dengan fotografi inframerah warna untuk meramalkan keperluan nitrogen untuk jagung. Ia sama dengan Osavi, tetapi ia menggantikan band hijau untuk merah.

$$
GOSAVI = {NIR - Green \over NIR + Green + 0.16)  }
$$

_Reference: Sripada, R., et al. "Menentukan Keperluan Nitrogen Dalam Musim Untuk Jagung Menggunakan Fotografi Inframerah Warna Udara." Ph.D. Disertasi, North Carolina State University, 2005._

***

## GRVI - Indeks Vegetasi Nisbah Hijau

Indeks ini sensitif terhadap kadar fotosintesis di kanopi hutan, kerana refleksi hijau dan merah sangat dipengaruhi oleh perubahan pigmen daun.

$$
GRVI = {NIR \over Green }
$$

_Reference: Sripada, R., et al. "Fotografi inframerah warna udara untuk menentukan keperluan nitrogen awal musim dalam jagung." Jurnal Agronomi 98 (2006): 968-977._***

## gsavi - indeks tumbuh -tumbuhan diselaraskan tanah hijau

Indeks ini pada asalnya direka dengan fotografi inframerah warna untuk meramalkan keperluan nitrogren untuk jagung. Ia sama dengan Savi, tetapi ia menggantikan jalur hijau untuk merah.

$$
GSAVI = 1.5 * {(NIR - Green) \over (NIR + Green + 0.5)  }
$$

_Reference: Sripada, R., et al. "Menentukan Keperluan Nitrogen Dalam Musim Untuk Jagung Menggunakan Fotografi Inframerah Warna Udara." Ph.D. Disertasi, North Carolina State University, 2005._

***

## lai - indeks kawasan daun

Indeks ini digunakan untuk menganggarkan perlindungan dedaunan dan meramalkan pertumbuhan dan hasil tanaman. Envi mengira Green Lai menggunakan formula empirikal berikut dari Boegh et al (2002):

$$
LAI = 3.618 * EVI - 0.118
$$

Di mana Evi adalah:

$$
EVI = 2.5 *  {(NIR - Red) \over (NIR + 6 * Red - 7.5 * Blue + 1)}
$$

Nilai LAI yang tinggi biasanya berkisar dari kira -kira 0 hingga 3.5. Walau bagaimanapun, apabila tempat kejadian mengandungi awan dan ciri -ciri terang lain yang menghasilkan piksel tepu, nilai LAI boleh melebihi 3.5. Anda sepatutnya menutupi awan dan ciri -ciri cerah dari tempat kejadian sebelum membuat imej LAI.

_Reference: Boegh, E., H. Soegaard, N. Broge, C. Hasager, N. Jensen, K. Schelde, dan A. Thomsen. "Data pelbagai spektrum udara untuk mengukur indeks kawasan daun, kepekatan nitrogen dan kecekapan fotosintesis dalam bidang pertanian." Pengesan Alam Sekitar Jauh 81, no. 2-3 (2002): 179-193._

***

## LCI - Indeks klorofil daun

Indeks ini digunakan untuk menganggarkan kandungan klorofil dalam tumbuhan yang lebih tinggi, sensitif terhadap variasi dalam refleksi yang disebabkan oleh penyerapan klorofil.

$$
LCI = {NIR2 - RedEdge \over NIR2 + Red}
$$

_Reference: Datt, B. "Penginderaan jauh kandungan air di daun kayu putih." Jurnal Fisiologi Loji 154, no. 1 (1999): 30-36._***

## MNLI - Indeks bukan linear yang diubahsuai

Indeks ini merupakan peningkatan kepada indeks bukan linear (NLI) yang menggabungkan indeks tumbuh-tumbuhan yang diselaraskan tanah (SAVI) untuk menyumbang latar belakang tanah. ENVI menggunakan faktor pelarasan latar belakang kanopi (_L_) nilai 0.5.

$$
MNLI = {(NIR^{2} - Red) * (1 + L) \over (NIR^{2} + Red + L)  }
$$

_Reference: Yang, Z., P. Willis, dan R. Mueller. "Kesan Band-Nisbah Meningkatkan Imej AWIF untuk Ketepatan Klasifikasi Tanaman." Prosiding Simposium Sensing Jauh Pecora 17 (2008), Denver, Co._

***

## msavi2 - Indeks tumbuh -tumbuhan yang diselaraskan tanah diubahsuai 2

Indeks ini adalah versi yang lebih mudah dari indeks MSAVI yang dicadangkan oleh Qi, et al (1994), yang meningkatkan indeks tumbuh -tumbuhan yang diselaraskan tanah (SAVI). Ia mengurangkan bunyi tanah dan meningkatkan pelbagai dinamik isyarat tumbuh -tumbuhan. MSAVI2 didasarkan pada kaedah induktif yang tidak menggunakan nilai _L_ yang tetap (seperti dengan SAVI) untuk menyerlahkan tumbuh -tumbuhan yang sihat.

$$
MSAVI2 = {2 * NIR + 1 - \sqrt{(2 * NIR + 1)^{2} - 8(NIR - Red)} \over 2}
$$

_Reference: Qi, J., A. Chehbouni, A. Huete, Y. Kerr, dan S. Sorooshian. "Indeks Vegetasi Larasan Tanah yang Diubahsuai." Pengesan Alam Sekitar Jauh 48 (1994): 119-126._

***

## ndre- perbezaan normal rededge

Indeks ini sama dengan NDVI tetapi membandingkan perbezaan antara NIR dengan Rededge dan bukannya merah, yang sering mengesan tekanan tumbuh -tumbuhan lebih awal.

$$
NDRE = {NIR - RedEdge \over NIR + RedEdge  }
$$***

## NDVI - Indeks Vegetasi Perbezaan Normal

Indeks ini adalah ukuran tumbuh -tumbuhan yang sihat dan hijau. Gabungan perumusan perbezaan normalnya dan penggunaan kawasan penyerapan dan refleksi tertinggi klorofil menjadikannya teguh dalam pelbagai keadaan. Walau bagaimanapun, ia boleh menembusi keadaan tumbuh -tumbuhan padat apabila LAI menjadi tinggi.

$$
NDVI = {NIR - Red \over NIR + Red  }
$$

Nilai indeks ini berkisar antara -1 hingga 1. Julat umum untuk tumbuh -tumbuhan hijau adalah 0.2 hingga 0.8.

_Reference: Rouse, J., R. Haas, J. Schell, dan D. Deering. Memantau sistem tumbuh -tumbuhan di dataran besar dengan ERTs. Simposium ERTS Ketiga, NASA (1973): 309-317._***

## NLI - Indeks bukan linear

Indeks ini mengandaikan bahawa hubungan antara indeks tumbuh-tumbuhan dan parameter biophysical permukaan tidak linear. Ia menyala hubungan dengan parameter permukaan yang cenderung tidak linear.

$$
NLI = {NIR^{2} - Red \over NIR^{2} + Red  }
$$

_Reference: Goel, N., dan W. Qin. "Pengaruh seni bina kanopi mengenai hubungan antara pelbagai indeks tumbuh -tumbuhan dan LAI dan FPAR: simulasi komputer." Ulasan Penginderaan Jauh 10 (1994): 309-347._ ***

## OSAVI - Indeks tumbuh -tumbuhan yang diselaraskan tanah yang dioptimumkan

Indeks ini didasarkan pada indeks tumbuh -tumbuhan yang diselaraskan tanah (SAVI). Ia menggunakan nilai standard 0.16 untuk faktor pelarasan latar belakang kanopi. Rondeaux (1996) menentukan bahawa nilai ini memberikan variasi tanah yang lebih besar daripada SAVI untuk perlindungan tumbuh -tumbuhan yang rendah, sambil menunjukkan kepekaan yang meningkat kepada tumbuh -tumbuhan meliputi lebih daripada 50%. Indeks ini lebih baik digunakan di kawasan dengan tumbuh -tumbuhan yang agak jarang di mana tanah dapat dilihat melalui kanopi.

$$
OSAVI = {(NIR - Red) \over (NIR + Red + 0.16)  }
$$

_Reference: Rondeaux, G., M. Steven, dan F. Baret. "Pengoptimuman indeks tumbuh-tumbuhan yang disesuaikan dengan tanah." Pengesan Alam Sekitar Jauh 55 (1996): 95-107._***

## RDVI - Indeks Vegetasi Perbezaan Renormalized

Indeks ini menggunakan perbezaan antara panjang gelombang inframerah dan merah, bersama-sama dengan NDVI, untuk menyerlahkan tumbuh-tumbuhan yang sihat. Ia tidak sensitif terhadap kesan tanah dan matahari melihat geometri.

$$
RDVI = {(NIR- Red) \over \sqrt{(NIR + Red)}  }
$$

_Reference: Roujean, J., dan F. Breon. "Menganggarkan par yang diserap oleh tumbuh -tumbuhan dari pengukuran refleksi dua arah." Pengesan Alam Sekitar Jauh 51 (1995): 375-384._ ***

## Savi - Indeks tumbuh -tumbuhan yang diselaraskan tanah

Indeks ini sama dengan NDVI, tetapi ia menindas kesan piksel tanah. Ia menggunakan faktor pelarasan latar belakang kanopi, _l_, yang merupakan fungsi ketumpatan tumbuh -tumbuhan dan sering memerlukan pengetahuan terlebih dahulu tentang jumlah tumbuh -tumbuhan. Huete (1988) mencadangkan nilai optimum _L_ = 0.5 untuk menyumbang variasi latar belakang tanah pertama. Indeks ini lebih baik digunakan di kawasan dengan tumbuh -tumbuhan yang agak jarang di mana tanah dapat dilihat melalui kanopi.

$$
SAVI = {1.5 * (NIR- Red) \over (NIR + Red + 0.5)  }
$$

_Reference: Huete, A. "Indeks tumbuh-tumbuhan yang disesuaikan dengan tanah (Savi)." Pengesan Alam Sekitar Jauh 25 (1988): 295-309._

***

## TDVI - Indeks Vegetasi Perbezaan yang Diubah

Indeks ini berguna untuk memantau perlindungan tumbuh -tumbuhan di persekitaran bandar. Ia tidak tepu seperti NDVI dan SAVI.

$$
TDVI = 1.5 * {(NIR- Red) \over \sqrt{NIR^{2} + Red + 0.5}  }
$$

_Reference: Bannari, A., H. Asalhi, dan P. Teillet. "Indeks Vegetasi Perbezaan yang Diubah (TDVI) untuk Pemetaan Perlindungan Vegetasi" dalam Prosiding Geosains dan Simposium Penginderaan Jauh, Igarss '02, IEEE International, Jilid 5 (2002) ._

***

## Vari - Indeks tahan atmosfera yang kelihatan

Indeks ini didasarkan pada ARVI dan digunakan untuk menganggarkan pecahan tumbuh -tumbuhan di tempat kejadian dengan kepekaan yang rendah terhadap kesan atmosfera.

$$
VARI = {Green - Red \over Green + Red - Blue  }
$$

_Reference: Gitelson, A., et al. "Tumbuh -tumbuhan dan garisan tanah di ruang spektrum yang kelihatan: konsep dan teknik untuk anggaran jauh dari pecahan tumbuh -tumbuhan. Jurnal Antarabangsa Penginderaan Jauh 23 (2002): 2537-2562._***

## WDRVI - Indeks Vegetasi Julat Dinamik yang luas

Indeks ini sama dengan NDVI, tetapi ia menggunakan pekali berat (_a_) untuk mengurangkan perbezaan antara sumbangan isyarat inframerah dan merah kepada NDVI. WDRVI sangat berkesan dalam adegan yang mempunyai ketumpatan tumbuh-tumbuhan sederhana-ke-tinggi apabila NDVI melebihi 0.6. NDVI cenderung untuk tahap apabila pecahan tumbuh -tumbuhan dan indeks kawasan daun (LAI) meningkat, sedangkan WDRVI lebih sensitif terhadap pelbagai pecahan tumbuh -tumbuhan yang lebih luas dan perubahan dalam LAI.

$$
WDRVI = {(\alpha * NIR- Red) \over (\alpha * NIR + Red)}
$$

Koefisien berat (_a_) boleh berkisar antara 0.1 hingga 0.2. Nilai 0.2 disyorkan oleh Henebry, Viña, dan Gitelson (2004).

_References_

_Gitelson, A. "Indeks tumbuh -tumbuhan pelbagai dinamik untuk kuantifikasi jauh ciri biophysical tumbuh -tumbuhan." Jurnal Fisiologi Loji 161, No. 2 (2004): 165-173._

_Henebry, G., A. Viña, dan A. Gitelson. "Indeks tumbuh -tumbuhan pelbagai dinamik dan utiliti yang berpotensi untuk analisis jurang." Buletin Analisis Gap 12: 50-56._
