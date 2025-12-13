---
description: This page lists some multispectral indices that Chloros uses.
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/multispectral-index-formulas
---
# Formula indeks multispektral

Formula indeks berikut menggunakan gabungan penapis Survey3 bermakna julat penghantaran:

<able> <tr> <th align = "center"> color filter survey3 </th> <th width = "196.1999951171875" align = "center"> name filter </th> <th> Media </th> </tr> </thead> <tbody> <tr> <td align = "center"> blue </td> <td align = "center"> ngb - blue </td> <td align = "center"> 468-483 nm </td> Cyan </td> <td align = "center"> ocn- cyan </td> <td align = "center"> 476-512 nm </td> <td align = "center"> 494 nm </td> Ngb - hijau </td> <td align = "center"> 543-558 nm </td> <td align = "center"> 547 nm </td> </tr> <tr> nm </td> <td align = "center"> 619 nm </td> </tr> <tr> <td align = "center"> merah </td> <td align = "center"> rgn - merah </td> Nm </td> </tr> <tr> <td align = "center"> rededge </td> <td align = "center"> re - rededge </td align = "center"> 712-735 nm </td> align = "center"> nir1 </td> <td align = "center"> ocn - nir1 </td> <td aragn = "center"> 798-848 nm </td> <td align = "center"> 823 nm </td> NGB | Nir - nir2 </td> <td align = "center"> 835-865 nm </td> <td align = "center"> 850 nm </td> </tr> </tbody> </meja>

Apabila menggunakan formula ini, nama itu boleh berakhir dengan "\ _1" atau "\ _2", yang sepadan dengan penapis NIR, sama ada NIR1 atau NIR2.

***

## evi - Indeks tumbuh -tumbuhan yang dipertingkatkan

Indeks ini pada asalnya dibangunkan untuk digunakan dengan data MODIS sebagai peningkatan ke atas NDVI, mengoptimumkan isyarat tumbuh -tumbuhan di kawasan dengan indeks kawasan daun yang tinggi (LAI). Ia paling berguna di kawasan dengan LAI yang tinggi, di mana NDVI boleh tepu. Ia menggunakan rantau pemantulan biru untuk membetulkan isyarat latar belakang tanah dan mengurangkan pengaruh atmosfera, termasuk penyebaran aerosol.

$$
Evi = 2.5 * {(nir - merah) \ over (nir + 6 * merah - 7.5 * biru + 1)}
$$

Nilai EVI harus berkisar antara 0 dan 1 untuk piksel tumbuh -tumbuhan. Ciri -ciri terang, seperti awan dan bangunan putih, bersama -sama dengan ciri -ciri gelap, seperti air, boleh menghasilkan nilai piksel yang tidak normal dalam imej EVI. Sebelum membuat imej EVI, anda mesti menutup awan dan ciri -ciri cerah dari imej refleksi dan secara pilihan ambang nilai piksel dari 0 hingga 1.

_Reference: Huete, A., et al. "Gambaran keseluruhan prestasi radiometrik dan biophysical indeks tumbuh -tumbuhan MODIS." Penginderaan Alam Sekitar Jauh 83 (2002): 195-213.

***

## FCI1 - Indeks Perlindungan Hutan 1

Indeks ini membezakan kanopi hutan dari jenis tumbuh -tumbuhan lain menggunakan imej refleksi multispektral yang termasuk jalur tepi merah.

$$
FCI1 = Rangkaian * Rededge
$$

Kawasan berhutan akan mempunyai nilai FCI1 yang lebih rendah disebabkan oleh pemantulan yang lebih rendah dari pokok -pokok dan kehadiran bayang -bayang di dalam kanopi.

_Reference: Becker, Sarah J., Craig S.T. Daughtry dan Andrew L. Russ. "Indeks perlindungan hutan yang teguh untuk imej multispektral." Kejuruteraan Photogrammetric & amp; Remote Sensing 84.8 (2018): 505-512._

***

## FCI2 - Indeks Perlindungan Hutan 2

Indeks ini membezakan kanopi pokok dari jenis tumbuh -tumbuhan lain menggunakan imej refleksi multispektral yang tidak termasuk jalur tepi merah.

$$
Fci2 = rangkaian * nir
$$

Kawasan berhutan akan mempunyai nilai FCI2 yang lebih rendah disebabkan oleh pemantulan yang lebih rendah dari pokok -pokok dan kehadiran bayang -bayang di dalam kanopi.

_Reference: Becker, Sarah J., Craig S.T. Daughtry dan Andrew L. Russ. "Indeks perlindungan hutan yang teguh untuk imej multispektral." Kejuruteraan Photogrammetric & amp; Remote Sensing 84.8 (2018): 505-512._

***

## Gemi - Indeks Pemantauan Alam Sekitar Global

Indeks tumbuh-tumbuhan bukan linear ini digunakan untuk pemantauan alam sekitar global dari imej satelit dan percubaan untuk membetulkan kesan atmosfera. Ia sama dengan NDVI, tetapi kurang sensitif terhadap kesan atmosfera. Ia dipengaruhi oleh tanah kosong, jadi penggunaannya tidak disyorkan di kawasan dengan tumbuh -tumbuhan yang jarang atau sederhana.

$$
Gemi = ETA (1 - 0.25 * ETA) - {merah - 0.125 \ lebih 1 - merah}
$$

Di mana:

$$
ETA = {2 (nir^{2} -red^{2}) + 1.5 * nir + 0.5 * merah \ over nir + merah + 0.5}
$$

_Reference: Pinty, B. dan M. Verstraete. GEMI: Indeks bukan linear untuk memantau tumbuh-tumbuhan global dari satelit. Vegetation 101 (1992): 15-20._

***

## gari - indeks tahan suasana hijau

Indeks ini lebih sensitif terhadap pelbagai kepekatan klorofil dan kurang sensitif terhadap kesan atmosfera daripada NDVI.

$$
Gari = {nir - [hijau - \ gamma (biru - merah)] \ over nir + [hijau - \ gamma (biru - merah)]}
$$

Pemalar gamma adalah fungsi pemberat yang bergantung kepada keadaan aerosol di atmosfera. Envi menggunakan nilai 1.7, iaitu nilai yang disyorkan oleh Gitelson, Kaufman, dan Merzylak (1996, halaman 296).

_Reference: Gitelson, A., Y. Kaufman dan M. Merzylak. «Penggunaan saluran hijau dalam penderiaan jauh tumbuh-tumbuhan global dari EOS-Modis». Pengesan Alam Sekitar Jauh 58 (1996): 289-298._

***

## GCI - Indeks klorofil hijau

Indeks ini digunakan untuk menganggarkan kandungan klorofil daun dalam pelbagai spesies tumbuhan.

$$
Gci = {nir \ over green} - 1
$$

Mempunyai panjang NIR dan panjang gelombang hijau memberikan ramalan yang lebih baik mengenai kandungan klorofil, sambil membolehkan kepekaan yang lebih besar dan nisbah isyarat-ke-bunyi yang lebih tinggi.

_Reference: Gitelson, A., Y. Gritz dan M. Merzlyak. "Hubungan antara kandungan klorofil dalam daun dan pemantulan spektrum dan algoritma untuk penilaian klorofil yang tidak merosakkan di daun tumbuhan yang lebih tinggi." Jurnal Fisiologi Loji 160 (2003): 271-282._

***

## GLI - Indeks daun hijau

Indeks ini pada asalnya direka untuk digunakan dengan kamera digital RGB untuk mengukur penutup gandum, di mana nombor digital merah, hijau dan biru (DN) berkisar dari 0 hingga 255.

$$
Gli = {(hijau - merah) + (hijau - biru) \ over (2 * hijau) + merah + biru}
$$

Nilai GLI berkisar antara -1 dan +1. Nilai negatif mewakili unsur-unsur tanah dan tidak hidup, manakala nilai positif mewakili daun dan batang hijau.

_Reference: Loaichi, M., M. Borman dan D. Johnson. "Platform spasial dan fotografi udara untuk dokumentasi kesan ragut pada gandum." Geocarto International 16, no. 1 (2001): 65-70._

***

## GNDVI - Indeks Vegetasi Perbezaan Normal Perbezaan Hijau

Indeks ini sama dengan NDVI, kecuali ia mengukur spektrum hijau dari 540 hingga 570 nm dan bukannya spektrum merah. Indeks ini lebih sensitif terhadap kepekatan klorofil daripada NDVI.

$$
Gndvi = {(nir - hijau) \ over (nir + hijau)}
$$

_Reference: Gitelson, A. dan M. Merzlyak. "Penginderaan jauh kepekatan klorofil di daun tumbuhan yang lebih tinggi." Kemajuan dalam Penyelidikan Angkasa 22 (1998): 689-692._

***

## Gosavi - Indeks tumbuh -tumbuhan diselaraskan tanah yang dioptimumkan hijau

Indeks ini pada asalnya direka menggunakan fotografi inframerah warna untuk meramalkan keperluan nitrogen jagung. Ia sama dengan Osavi, tetapi menggantikan jalur hijau dengan yang merah.

$$
Gosavi = {nir - hijau \ over nir + hijau + 0.16)}
$$

_Reference: Sripada, R., et al. "Penentuan keperluan nitrogen jagung semasa musim menggunakan fotografi udara inframerah warna." Tesis PhD, North Carolina State University, 2005._

***

## GRVI - Indeks Vegetasi Nisbah Hijau

Indeks ini sensitif terhadap kadar fotosintesis di kanopi hutan, kerana refleksi hijau dan merah sangat dipengaruhi oleh perubahan pigmen daun.

$$
Grvi = {nir \ over hijau}
$$

_Reference: Sripada, R., et al. "Fotografi udara dalam warna inframerah untuk menentukan keperluan nitrogen jagung pada awal musim." Jurnal Agronomi 98 (2006): 968-977._

***

## gsavi - indeks tumbuh -tumbuhan diselaraskan tanah hijau

Indeks ini pada asalnya direka menggunakan fotografi inframerah warna untuk meramalkan keperluan nitrogen jagung. Ia sama dengan Savi, tetapi menggantikan jalur hijau dengan yang merah.

$$
Gsavi = 1.5 * {(nir - hijau) \ over (nir + hijau + 0.5)}
$$

_Reference: Sripada, R., et al. "Penentuan Keperluan Nitrogen Dalam Musim Untuk Jagung Menggunakan Fotografi Udara Inframerah Warna." Tesis PhD, North Carolina State University, 2005._

***

## lai - indeks kawasan daun

Indeks ini digunakan untuk menganggarkan perlindungan dedaunan dan meramalkan pertumbuhan dan hasil tanaman. Envi mengira lai hijau menggunakan formula empirikal berikut dari Boegh et al (2002):

$$
LAI = 3.618 * EVI - 0.118
$$

Di mana Evi adalah:

$$
Evi = 2.5 * {(nir - merah) \ over (nir + 6 * merah - 7.5 * biru + 1)}
$$

Nilai LAI yang tinggi biasanya berkisar dari kira -kira 0 hingga 3.5. Walau bagaimanapun, apabila adegan mengandungi awan dan unsur -unsur terang lain yang menghasilkan piksel tepu, nilai LAI boleh melebihi 3.5. Sebaik -baiknya, anda harus menutupi awan dan unsur -unsur cerah di tempat kejadian sebelum membuat imej LAI.

_Reference: Boegh, E., H. Soegaard, N. Broge, C. Hasager, N. Jensen, K. Schelde dan A. Thomsen. "Data multispektral udara untuk mengukur indeks kawasan daun, kepekatan nitrogen dan kecekapan fotosintesis dalam bidang pertanian." Pengesan Alam Sekitar Jauh 81, no. 2-3 (2002): 179-193._

***

## LCI - Indeks klorofil daun

Indeks ini digunakan untuk menganggarkan kandungan klorofil dalam tumbuhan yang lebih tinggi, sensitif terhadap variasi dalam refleksi yang disebabkan oleh penyerapan klorofil.

$$
Lci = {nir2 - rededge \ over nir2 + merah}
$$

_Reference: Datt, B. "Penginderaan jauh kandungan air di daun eucalyptus." Jurnal Fisiologi Loji 154, no. 1 (1999): 30-36._

***

## MNLI: Indeks bukan linear yang diubahsuai

Indeks ini adalah peningkatan pada indeks tak linear (NLI) yang menggabungkan indeks tumbuh-tumbuhan yang diselaraskan tanah (SAVI) untuk mengambil kira latar belakang tanah. ENVI menggunakan faktor pelarasan bawah kanopi (_L_) nilai 0.5.

$$
Mnli = {(nir^{2} - merah) * (1 + l) \ over (nir^{2} + merah + l)}
$$

_Reference: Yang, Z., P. Willis dan R. Mueller. "Impak Nisbah Band Meningkatkan Imej AWIF pada Ketepatan Klasifikasi Tanaman." Prosiding Simposium Sensing Jauh Pecora 17 (2008), Denver, Co._

***

## msavi2 - Indeks tumbuh -tumbuhan yang diselaraskan tanah diubahsuai 2

Indeks ini adalah versi yang lebih mudah dari indeks MSAVI yang dicadangkan oleh Qi et al (1994), yang meningkatkan indeks tumbuh-tumbuhan yang diselaraskan tanah (SAVI). Mengurangkan bunyi tanah dan meningkatkan julat dinamik isyarat tumbuh -tumbuhan. MSAVI2 didasarkan pada kaedah induktif yang tidak menggunakan nilai _L_ yang tetap (seperti dalam kes SAVI) untuk menyerlahkan tumbuh -tumbuhan yang sihat.

$$
Msavi2 = {2 * nir + 1 - \ sqrt {(2 * nir + 1)^{2} - 8 (nir - merah)} \ over 2}
$$

_Reference: Qi, J., A. Chehbouni, A. Huete, Y. Kerr dan S. Sorooshian. "Indeks Vegetasi Larasan Tanah yang Diubahsuai." Pengesan Alam Sekitar Jauh 48 (1994): 119-126._

***

## ndre: perbezaan normal rededge

Indeks ini sama dengan NDVI, tetapi membandingkan perbezaan antara NIR dan Rededge bukannya merah, yang sering mengesan tekanan tumbuh -tumbuhan lebih awal.

$$
Ndre = {nir - rededge \ over nir + rededge}
$$

***

## NDVI - Indeks Vegetasi Perbezaan Normal

Indeks ini adalah ukuran tumbuh -tumbuhan hijau dan sihat. Gabungan perumusan perbezaannya yang normal dan penggunaan kawasan penyerapan tertinggi dan pemantulan klorofil menjadikannya teguh dalam pelbagai keadaan. Walau bagaimanapun, ia boleh menjadi tepu di bawah keadaan tumbuh -tumbuhan yang padat apabila LAI menjadi tinggi.

$$
Ndvi = {nir - merah \ over nir + merah}
$$

Nilai indeks ini berkisar antara -1 dan 1. Julat umum untuk tumbuh -tumbuhan hijau adalah 0.2 hingga 0.8.

_Reference: Rouse, J., R. Haas, J. Schell dan D. Deering. Memantau sistem tumbuh -tumbuhan di dataran besar dengan ERTs. Simposium ERTS Ketiga, NASA (1973): 309-317._

***

## NLI-Indeks bukan linear

Indeks ini mengandaikan bahawa hubungan antara indeks tumbuh-tumbuhan dan parameter biophysical permukaan tidak linear. Linearizes hubungan dengan parameter permukaan yang cenderung tidak linear.

$$
Nli = {nir^{2} - merah \ over nir^{2} + merah}
$$

_Reference: Goel, N. dan W. Qin. "Pengaruh seni bina kanopi mengenai hubungan antara pelbagai indeks tumbuh -tumbuhan dan LAI dan FPAR: simulasi komputer." Ulasan Penginderaan Jauh 10 (1994): 309-347._

***

## OSAVI - Indeks tumbuh -tumbuhan yang diselaraskan tanah yang dioptimumkan

Indeks ini didasarkan pada indeks tumbuh-tumbuhan yang diselaraskan tanah (SAVI). Gunakan nilai standard 0.16 untuk faktor pelarasan bawah kanopi. Rondeaux (1996) menentukan bahawa nilai ini memberikan variasi tanah yang lebih besar daripada SAVI untuk perlindungan tumbuh -tumbuhan yang rendah, sambil menunjukkan kepekaan yang lebih besar kepada tumbuh -tumbuhan meliputi lebih besar daripada 50%. Indeks ini lebih baik digunakan di kawasan dengan tumbuh -tumbuhan yang agak jarang, di mana tanah dapat dilihat melalui kanopi.

$$
Osavi = {(nir - merah) \ over (nir + merah + 0.16)}
$$

_Reference: Rondeaux, G., M. Steven dan F. Baret. "Pengoptimuman indeks tumbuh-tumbuhan yang disesuaikan dengan tanah." Pengesan Alam Sekitar Jauh 55 (1996): 95-107._

***

## RDVI: Indeks Vegetasi Perbezaan Renormalized

Indeks ini menggunakan perbezaan antara panjang gelombang inframerah dan merah, bersama dengan NDVI, untuk menyerlahkan tumbuh-tumbuhan yang sihat. Ia tidak sensitif terhadap kesan geometri tanah dan solar.

$$
Rdvi = {(nir- red) \ over \ sqrt {(nir + merah)}}
$$

_Reference: Roujean, J. dan F. Breon. "Anggaran par yang diserap oleh tumbuh -tumbuhan dari pengukuran refleksi dua arah." Pengesan Alam Sekitar Jauh 51 (1995): 375-384._

***

## Savi - Indeks tumbuh -tumbuhan yang diselaraskan tanah

Indeks ini sama dengan NDVI, tetapi menindas kesan piksel tanah. Ia menggunakan faktor pelarasan bawah kanopi, _l_, yang merupakan fungsi ketumpatan tumbuh -tumbuhan dan sering memerlukan pengetahuan terlebih dahulu tentang jumlah tumbuh -tumbuhan. Huete (1988) mencadangkan nilai optimum _L_ = 0.5 untuk mengambil kira variasi pesanan pertama dalam latar belakang tanah. Indeks ini lebih baik digunakan di kawasan dengan tumbuh -tumbuhan yang agak jarang, di mana tanah dapat dilihat melalui kanopi.

$$
Savi = {1.5 * (nir- merah) \ over (nir + merah + 0.5)}
$$

_Reference: huete, A. «Indeks tumbuh-tumbuhan yang disesuaikan tanah (Savi)». Pengesan Alam Sekitar Jauh 25 (1988): 295-309._

***

## TDVI - Indeks Vegetasi Perbezaan yang Diubah

Indeks ini berguna untuk memantau perlindungan tumbuh -tumbuhan di persekitaran bandar. Ia tidak tepu seperti NDVI dan SAVI.

$$
Tdvi = 1.5 * {(nir- red) \ over \ sqrt {nir^{2} + merah + 0.5}}
$$

_Reference: Bannari, A., H. Asalhi dan P. Teillet. "Indeks Vegetasi Berubah (TDVI) untuk Pemetaan Perlindungan Vegetasi." Dalam Prosiding Geosains dan Simposium Sensing Jauh, Igarss &#x27; 02, IEEE International, Volume 5 (2002) ._

***

## Vari - Indeks tahan suasana yang kelihatan

Indeks ini didasarkan pada ARVI dan digunakan untuk menganggarkan pecahan tumbuh -tumbuhan di tempat kejadian dengan kepekaan yang rendah terhadap kesan atmosfera.

$$
Vari = {hijau - merah \ over hijau + merah - biru}
$$

_Reference: Gitelson, A., et al. "Tumbuh -tumbuhan dan garisan tanah di ruang spektrum yang kelihatan: konsep dan teknik untuk anggaran jauh dari pecahan tumbuh -tumbuhan." Jurnal Antarabangsa Penginderaan Jauh 23 (2002): 2537-2562._

***

## WDRVI - Indeks Vegetasi Julat Dinamik yang luas

Indeks ini serupa dengan NDVI, tetapi menggunakan pekali berat (_a_) untuk mengurangkan perbezaan antara sumbangan isyarat inframerah dan merah kepada NDVI. WDRVI amat berkesan dalam adegan dengan ketumpatan tumbuh -tumbuhan yang sederhana dan tinggi apabila NDVI melebihi 0.6. NDVI cenderung menstabilkan sebagai pecahan tumbuh -tumbuhan dan indeks kawasan daun (LAI), manakala WDRVI lebih sensitif terhadap pelbagai pecahan tumbuh -tumbuhan yang lebih luas dan perubahan dalam LAI.

$$
Wdrvi = {(\ alpha * nir- red) \ over (\ alpha * nir + merah)}
$$

Koefisien berat (_a_) boleh berkisar antara 0.1 dan 0.2. Henebry, Viña dan Gitelson (2004) mencadangkan nilai 0.2.

_References_

_Gitelson, A. "Indeks tumbuh -tumbuhan pelbagai dinamik untuk kuantifikasi jauh ciri biophysical tumbuh -tumbuhan." Jurnal Fisiologi Loji 161, no. 2 (2004): 165-173._

_Henebry, G., A. Viña dan A. Gitelson. "Indeks tumbuh -tumbuhan pelbagai dinamik dan utiliti yang berpotensi untuk analisis jurang." Buletin Analisis Gap 12: 50-56._