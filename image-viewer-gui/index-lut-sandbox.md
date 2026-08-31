# Kotak Pasir Indeks/LUT

Sandbox Indeks/LUT ialah ruang kerja interaktif dalam bar sisi Pemapar Imej Chloros. Anda memilih formula, mengikat saluran kamera anda kepadanya, mewarnakannya dengan gradien dan melaras julat nilai — dan imej akan dikemas kini secara langsung semasa anda melakukannya. Sejak versi 1.2.0, anda juga boleh **menyimpan apa yang telah anda bina**, untuk satu imej atau untuk keseluruhan projek, tanpa pemprosesan semula.

## Kegunaan Sandbox

| Sandbox Indeks/LUT (interaktif)        | Pemprosesan Projek (batch)       |
| -------------------------------------- | -------------------------------- |
| Satu imej pada satu masa, maklum balas segera  | Keseluruhan set data dalam satu larian     |
| Eksperimental dan berulang-ulang             | Tetapan yang telah ditetapkan          |
| Menghasilkan secara langsung; menyimpan hanya apabila diminta  | Sentiasa menulis fail produk      |
| Sesuai untuk mencari tetapan yang betul | Terbaik setelah tetapan muktamad |

{% hint style="success" %}
**Aliran kerja biasa**: laraskan di Sandbox sehingga visualisasinya memuaskan hati anda, kemudian sama ada eksport terus dari Sandbox, atau salin tetapan indeks dan LUT yang sama ke dalam [Tetapan Projek](../project-settings/project-settings.md) supaya larian pemprosesan seterusnya menyematkannya ke dalam setiap imej.
{% endhint %}

***

## Membuka Sandbox

1. Klik imej dalam grid — ia akan dibuka penuh skrin dalam tab **Pemandang Imej** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line">
2. Klik ikon **Image Viewer** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> untuk membuka bar sisi kiri jika ia belum terbuka
3. Pilih lapisan berbilang jalur daripada menu lungsur lapisan di bahagian atas kanan — **RAW (Reflectance)** adalah pilihan biasa, kerana nilai indeks yang dikira pada pantulan yang dikalibrasi boleh dibandingkan antara imej

Bar sisi memaparkan, dari atas ke bawah:

* nama imej dan model kameranya
* butang **Eksport/Simpan Imej(e)** — muncul setelah Indeks atau LUT dicentang
* kotak semak **Indeks**dan**LUT**
* panel konfigurasi indeks
* panel **Nilai Kursor** dengan bacaan, histogram dan kawalan GSD

{% hint style="warning" %}
**Tidak tersedia untuk kamera mono.** Pada imej LATTICE M3M jalur tunggal, kedua-dua kotak semak dilumpuhkan, dengan petua alat _&quot;Tidak tersedia untuk penderia mono (M3M)&quot;_ — indeks berbilang jalur tidak ditakrifkan pada satu jalur. Untuk mengira indeks daripada kamera M3M, gabungkan dua atau lebih menjadi susunan berbilang jalur yang selari dan gunakan enjin indeks LATTICE.
{% endhint %}

***

## Mengaplikasikan indeks

1. Centang kotak **Index** di bahagian atas bar sisi
2. Pilih penapis kamera anda daripada lungsur kiri (`RGN`, `OCN`, `NGB`, `RGB`, `RE`, `NIR`)
3. Pilih formula indeks daripada menu lungsur di sebelah kanan — 27 formula terbina dalam, serta sebarang formula tersuai yang telah anda simpan
4. Formula itu dipaparkan sebagai persamaan matematik di bawah, dengan bulatan kosong pada setiap slot jalur. **Seret bulatan saluran berwarna ke atas slot** untuk mengikatnya
5. Setelah setiap slot yang digunakan oleh formula diikat, imej akan dikemas kini dan memaparkan nilai indeks
6. Pindahkan penunjuk ke atas imej untuk membaca nilai; panel **Nilai Penunjuk** menambah baris indeks dengan nilai di bawah penunjuk

Klik dua kali slot yang terikat untuk membersihkannya. Formula tidak lengkap adalah keadaan tarikan pertengahan yang normal, bukan ralat — imej tidak akan dikemas kini sehingga formula itu lengkap.

Lingkaran saluran berwarna kod: merah = Red, hijau = Green, biru = Blue, jingga = Orange, sian = Cyan, ungu = NIR, magenta = RE. Warna yang sama digunakan untuk titik saluran dan lengkung histogram dalam panel Nilai Penunjuk.

### Contoh Indeks NDVI

```

Formula: (NIR - Red) / (NIR + Red)

For a Survey3W RGN camera:
  NIR = 850 nm band
  Red = 661 nm band

Result range:          -1.0 to +1.0
Typical vegetation:     0.4 to 0.9
Stressed vegetation:    0.2 to 0.4
Bare soil:              0.0 to 0.2
Water:                 -0.1 to 0.1
```

Untuk rujukan formula lengkap — ketiga-tiga senarai pratetap dan nama yang berfungsi di mana — lihat [Formula Indeks Multispektral](../project-settings/multispectral-index-formulas.md).

### Dengan Indeks dicentang tetapi tiada LUT

Imej dilukis dalam **skala kelabu**, diregangkan di antara dua nilai ambang. Ini disengajakan: imej indeks adalah data skalar, dan skala kelabu adalah pameran yang paling tepat untuknya. Tambah LUT apabila anda mahukan warna.***

## Menggunakan LUT (Jadual Rujukan)

Jadual Rujukan (Look-Up Table) memetakan nilai indeks kepada warna: input NDVI 0.65, output hijau tertentu. Ia tidak mengubah data — ia mengubah cara anda membacanya.

### Menambah LUT

1. Klik butang **<img src="../.gitbook/assets/image (1) (1) (1).png" alt="" data-size="line">&quot;+ Tambah LUT&quot;** di bawah formula
2. Pilih gradasi warna
3. Tetapkan minimum dan maksimum pemotongan
4. Pilih Mod Pemotongan
5. Centang kotak **LUT** di bar sisi untuk memaparkannya

Kotak semak **LUT** kekal dilumpuhkan sehingga LUT benar-benar telah dikonfigurasikan pada indeks.

### Memilih gradasi warna

Tudingkan tetikus pada **bar gradasi**untuk membuka senarai pratetap — Chloros disertakan dengan**tujuh** pratetap gradasi:

| # | Gradasi                            | Bentuk                                                               |
| - | ----------------------------------- | ------------------------------------------------------------------- |
| 1 | Red → Kuning → Green (**lalai**)  | Berpencar — menepati intuisi vegetasi biasa, hijau = sihat |
| 2 | Ungu → Kuning → Green             | Berpencar, dengan hujung gelap yang ketara                                  |
| 3 | Coklat → Putih → Blue                | Berpencar di sekitar titik tengah cerah                                   |
| 4 | Hitam → Ungu → Merah jambu → Kuning pucat | Bersiri, gelap ke cerah                                           |
| 5 | Red → Kuning → Blue                 | Berpencar di sekitar titik tengah yang terang                                   |
| 6 | Ungu → Blue → Green → Kuning      | Bersiri, gelap ke terang                                           |
| 7 | Orange → Putih → Ungu             | Berpencar di sekitar titik tengah yang terang                                   |

Gradien **menyebar**meletakkan warna neutral di tengah tetingkap anda, yang sesuai digunakan apabila titik tengah itu bermaksud sesuatu (seperti ambang atau tarikh asas). Gradien**berurutan** bergerak secara monoton daripada gelap ke terang, yang sesuai untuk kuantiti yang hanya mempunyai &quot;lebih&quot; dan &quot;kurang&quot;.

Setiap pratet mempunyai tujuh hentian warna. Klik pratet dan imej akan dikemas kini serta-merta (apabila kotak LUT dicentang).

### Mengedit hentian warna

Di bawah bar gradien terdapat satu baris sampel warna, satu untuk setiap hentian:

* **Menukar warna**: klik sampel warna untuk membuka pemilih warna (roda warna, gelangsar RGB/HSV, atau kod hex seperti `#FF0000`)
* **Tambah hentian**: klik butang**+** di hujung baris — satu hentian putih akan ditambah
* **Keluarkan hentian**:**klik dua kali** pada sampel warna
* **Simpan gradien yang disunting**: klik ikon simpan di sebelah bar gradien untuk menambah gradien yang telah anda sunting ke dalam senarai pratetap supaya anda boleh memilihnya semula

Gradien yang telah anda konfigurasikan pada indeks disimpan bersama indeks tersebut dalam tetapan projek, jadi ia kekal walaupun projek ditutup dan dibuka semula.

**Lebih sedikit hentian**menghasilkan zon yang jelas yang terbaca sebagai pengelasan;**lebih banyak hentian** menghasilkan peralihan yang lancar, hampir seperti fotografi. Tiga hingga lima hentian sesuai untuk slaid pembentangan dan peta pengelasan; enam hingga sepuluh sesuai untuk analisis umum; lima belas atau lebih sesuai untuk pemeriksaan terperinci dan carta penerbitan.

### Menetapkan julat nilai

Kawal selia ambang adalah **gelangsar dwi-pemegang**yang berjalan dari −1 hingga +1, dengan kotak teks boleh sunting di setiap hujung untuk nilai tepat, dan butang**AUTO**.

* Seret mana-mana pemegang, atau taip nombor ke dalam kotaknya dan tekan Enter
* **AUTO**menetapkan julat kepada**peratusan ke-2 dan ke-98** nilai indeks sah imej — satu titik permulaan yang baik yang mengabaikan nilai luar biasa. Chloros mengebulatkan hasil secara adaptif, kepada 4 tempat perpuluhan untuk julat yang sangat sempit, 3 untuk julat yang sempit, dan 2 sebaliknya
* Sebarang pelarasan manual diutamakan berbanding AUTO sehingga anda menekan AUTO semula

Contoh tetingkap NDVI:

| Matlamat                                    | Min  | Max |
| --------------------------------------- | ---- | --- |
| Tunjukkan semuanya                         | −1.0 | 1.0 |
| Vegetasi sahaja, kecualikan tanah dan air | 0.2  | 0.9 |
| Tumbuhan sihat sahaja                 | 0.5  | 0.9 |
| Tekankan tekanan                     | 0.2  | 0.5 |

Mengecilkan tetingkap meningkatkan kontras dalam kawasan minat anda dan menolak segala-galanya yang lain ke luar julat — di mana **Mod Pemotongan** menentukan apa yang berlaku kepadanya.***

## Mod pemotongan

Apabila nilai indeks piksel jatuh di luar tetingkap min/maks, Mod Pemotongan menentukan bagaimana ia dilukis.

| Label lungsur                  | Nilai yang disimpan      | Pigmen yang berada di luar julat dilukis sebagai                                                                                                |
| ------------------------------- | ----------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| **Minimum &amp; Maksimum** (lalai) | `clip`            | Warna hujung terdekat pada rona — nilai di bawah minimum mengambil warna pertama, nilai di atas maksimum mengambil warna terakhir |
| **Latar Belakang Telus**      | `transparent`     | Telus sepenuhnya (alpha sebenar)                                                                                                  |
| **Latar Belakang Indeks**| `indexColor`      | Skala kelabu, diregangkan merentasi julat indeks**penuh** imej, jadi struktur di luar julat masih kelihatan dalam warna kelabu                |
| **Latar Belakang Asal**         | `backgroundColor` | Imej asas itu sendiri, jadi lapisan warna terletak di atas pemandangan sebenar                                                |

| Mod                       | Terbaik untuk                               | Rupa                                      |
| -------------------------- | -------------------------------------- | ----------------------------------------- |
| **Minimum &amp; Maksimum**      | Paparan data penuh, analisis saintifik | Setiap piksel diberi warna                |
| **Latar Belakang Telus**    | Lapisan GIS, mengasingkan jalur nilai   | Warna di dalam tetingkap, tiada di luar |
| **Latar Belakang Indeks**       | Penekanan sambil mengekalkan konteks data    | Berwarna di dalam, kelabu di luar               |
| **Latar Belakang Asli**    | Laporan dan pembentangan              | Berwarna di dalam, gambar di luar         |

{% hint style="info" %}
Piksel tanpa data sentiasa telus, dalam setiap mod.Piksel yang indeksnya tidak terhingga (pembahagian 0/0) atau tepatnya −1.0 atau +1.0 (penjaga ketepuan, apabila satu jalur dibaca sifar manakala yang lain tidak) dianggap sebagai tiada data dan bukannya nilai melampau. Ini memastikan sorotan yang melampau dan bayang-bayang gelap tidak muncul dalam skala warna anda, bukannya diwarnakan sebagai bacaan paling melampau dalam bingkai. Peraturan yang sama menentukan piksel mana yang menyumbang kepada ambang AUTO dan histogram indeks, jadi ketiga-tiganya sepadan.
{% endhint %}

Keterbeningan dikekalkan apabila eksport ditulis sebagai PNG. Ia tidak dapat diwakili dalam JPG.

***

## Membaca nilai semasa melaras

Panel **Nilai Penunjuk** di bawah panel konfigurasi adalah instrumen pengukuran untuk Sandbox:

* Gerakkan penunjuk ke atas imej dan baca nilai sumber bagi setiap saluran, serta nilai indeks dalam barisnya sendiri
* Hidupkan butang **INDEX** di atas histogram untuk melihat pengagihan nilai indeks dalam bingkai, dengan dua ambang klip anda digambarkan sebagai garisan putus-putus jingga dan nilai kursor sebagai garisan putih — ini adalah cara terpantas untuk memilih tetingkap yang sebenarnya mengandungi data anda
* Hidupkan **CURSOR** untuk melihat garisan penanda pada nilai di bawah penuding
* Zum melebihi 60× (kurang jika saiz blok GSD ditetapkan) untuk menyerlahkan piksel individu yang dipaparkan dengan nilai terapung

Rutin praktikal:

1. Catatkan nilai di atas vegetasi sihat, vegetasi tertekan, tanah terbukak dan air
2. Perhatikan di mana kluster-kluster itu terletak pada histogram indeks
3. Tetapkan min/maks untuk membingkai kluster yang anda ambil berat
4. Pilih mod pemangkasan — _Latar Belakang Asli_ mengekalkan pemandangan di sekelilingnya dapat dilihat

***

## Mengeksport dari Sandbox

Semua yang di atas adalah pratonton langsung sehingga anda menyimpannya. Butang **Eksport/Simpan Imej(e)** di bahagian atas bar sisi akan membuka satu tetingkap yang tergelincir di atas bar sisi (bukannya menutupi imej, supaya anda masih boleh melihat apa yang anda pertimbangkan).

<figure><img src="../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>### Pilihan

| Pilihan                          | Kesan                                                                                                                                                |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Terapkan pada imej semasa**      | Menyimpan imej yang dipaparkan tepat seperti mana, dengan tetapan ini                                                                                                |
| **Terapan pada semua imej projek** | Mengjalankan semula konfigurasi yang sama pada setiap imej dalam projek. Imej yang tiada jalur yang diperlukan oleh indeks ini akan dilangkau, dan tidak dianggap sebagai kegagalan |
| **Bar gradien Indeks/LUT**      | Juga menulis imej legenda berasingan bagi setiap eksport, dengan julat nilai dilabelkan                                                                     |
| **Histogram Indeks**             | Juga menulis imej histogram berasingan bagi setiap eksport, memaparkan data min/maks dan ambang klip                                               |

Jika **saiz blok GSD** tab imej melebihi 1, panel akan memberitahu anda sebelum anda mengesahkan: eksport akan menyimpan apa yang anda lihat, termasuk purata blok. Tetapkan kawalan GSD kembali kepada 1 terlebih dahulu jika anda mahukan resolusi penuh.

### Ke mana fail disimpan

Setiap klik pada **Eksport**akan memperuntukkan satu**folder baru yang tidak pernah digunakan semula**:

```
<project folder>/Sandbox_Exports/<IndexName>_<Index|LUT>_<NNN>/
```

Contohnya: `Sandbox_Exports/NDVI_LUT_001/`, kemudian `Sandbox_Exports/NDVI_LUT_002/` untuk larian seterusnya. Penomboran ini diperoleh dengan mengimbas apa yang sudah ada pada cakera, jadi ia kekal walaupun anda memulakan semula atau memadamkan folder secara manual. Tiada apa-apa yang akan ditimpa — tujuan utama Sandbox adalah untuk membandingkan satu percubaan dengan yang sebelumnya.

Di dalam folder, bagi setiap imej:

| Fail                                                   | Kandungan                                                   |
| ------------------------------------------------------ | ---------------------------------------------------------- |
| `<source name>_<IndexName>_<Index\|LUT>.png`           | Imej yang dirender, piksel demi piksel apa yang dipaparkan oleh pemapar |
| `<source name>_<IndexName>_<Index\|LUT>_legend.png`    | Fail sampingan bar gradien, jika diminta                     |
| `<source name>_<IndexName>_<Index\|LUT>_histogram.png` | Fail sampingan histogram indeks, jika diminta                  |

Kedua-dua sidecar sentiasa ditulis pada **resolusi penuh**, walaupun imej utama di-block-average: saiz blok adalah resolusi paparan, dan kedua-dua sidecar membaca nilai indeks sebenar setiap piksel. Mereka juga mencetak lebih banyak maklumat berbanding versi pada skrin — kedua-duanya mencatat tetingkap regangan _dan_ nilai min/maks sebenar data, jadi legenda yang disimpan masih boleh dibaca beberapa bulan kemudian tanpa membuka projek.

### Kemajuan dan keputusan

Eksport keseluruhan projek mengambil masa beberapa minit, jadi jalannya proses melaporkan kemajuan melalui saluran langsung dan bukannya menunggu:

* Bar kemajuan memaparkan `current / total` dan fail yang sedang ditulis
* Apabila selesai, panel melaporkan berapa banyak imej telah dieksport, berapa banyak yang dilangkau, dan laluan folder output
* Imej yang dilangkau disenaraikan dengan sebabnya (sehingga lima ditunjukkan, kemudian satu baris &quot;+N lagi&quot;). Sebab biasa ialah lapisan yang tidak mempunyai saluran yang diperlukan oleh indeks ini
* Jika **tiada** imej dalam projek yang boleh menggunakan indeks tersebut, proses itu akan melaporkan kegagalan dan bukannya meninggalkan anda dengan folder kosong

Hanya satu eksport sandbox boleh dijalankan pada satu-satu masa. Permintaan untuk memulakan yang kedua sementara yang pertama masih dijalankan akan ditolak dengan mesej yang jelas, dan bukannya membenarkan dua proses bersaing untuk fail projek yang sama.

### Grid mengambil alih larian

Setiap larian yang selesai akan muncul sebagai butang tersendiri dalam bar alat [grid imej](image-grid.md), dilabelkan `<IndexName> <Index|LUT> <NNN>`. Begitulah cara anda membandingkan larian: eksport dua kali dengan gradien atau ambang yang berbeza, kemudian bertukar antara dua butang pada grid.

***

## Formula indeks tersuai (Chloros

+)

{% hint style="info" %}
**Tempat menciptanya**: di bar sisi Sandbox, atau dalam**Tetapan Projek** sebelum pemprosesan. Kedua-duanya menulis ke senarai peringkat projek yang sama.
{% endhint %}

1. Buka pengira formula tersuai daripada menu lungsur formula indeks (memerlukan log masuk dengan langgananChloros

+ yang layak)
2. Tulis formula menggunakan **simbol jalur** `x`, `y`, `z`, `a`, `b`, `c` — bukan nama kumpulan
3. Pengendali yang tersedia: `+`, `-`, `*`, `/`, `^`, dan `()` untuk pengelompokan
4. Fungsi yang tersedia: `sqrt()`, `log()`, `ln()`, `abs()`, `sign()`, `log1p()`, `log2()`
5. Namakan dan simpannya — ia akan muncul di bahagian bawah menu lungsur formula dan anda mengikat slotnya dengan menyeret bulatan saluran, sama seperti pratetap terbina dalam

```

Modified NDVI with an offset:   (y-x)/(y+x+0.5)
Simple ratio:                   y/x
Three-band difference:          (y-x)/(y+x-z)
Squared ratio:                  (y/x)^2
```

{% hint style="warning" %}
**Formula tersuai hanya untuk GUI.** PilihanCLI

/SDK

`--indices` mengembangkan 22 nama pratetap terbina dalam dan secara senyap melangkau apa sahaja yang lain, termasuk formula tersuai anda. Untuk memproses formula tersuai secara pukal, konfigurasikan ia dalam Tetapan Projek dan jalankan pemprosesan, atau gunakan eksport &quot;Terapkan pada semua imej projek&quot; di Sandbox.
{% endhint %}

***

## Penyelesaian Masalah

### &quot;Lapisan ini tidak mempunyai saluran yang diperlukan oleh indeks ini&quot;

Formula ini membaca kedudukan saluran yang tidak dimiliki oleh lapisan semasa — contohnya indeks tiga-slot pada fail satu atau dua saluran. Tukar kepada lapisan berbilang jalur (pantulan atau debayered), atau pilih indeks yang sesuai dengan penapis kamera anda.

### &quot;Tidak dapat mencapai backend pemprosesan imej&quot;

Backend tidak memberi maklum balas. Semak tab Log; jika backend sedang dihidupkan semula, Sandbox akan pulih dengan sendirinya setelah ia kembali.

### Imej tidak berubah apabila saya menyeret bulatan

Formula belum lengkap lagi. Formula yang tidak lengkap akan dianggap sebagai keadaan seretan pertengahan biasa — tiada apa-apa yang dipaparkan dan tiada apa-apa yang dilaporkan sebagai ralat. Isi setiap slot yang digunakan oleh formula.

### Keseluruhan imej berwarna satu warna

Tetingkap klip anda mungkin berada jauh di luar data. Tekan **AUTO**untuk menyelaraskannya pada peratusan ke-2/98, atau hidupkan histogram**INDEX** untuk melihat di mana data sebenarnya terletak.

### Warna yang dieksport tidak sepadan dengan apa yang saya lihat

Ia sepatutnya sepadan — laluan eksport adalah cerminan sengaja pratonton langsung, termasuk alfa mod pemotongan, dan purata blok diterapkan _selepas_ pewarnaan dengan tepat seperti yang dilakukan oleh pemapar. Jika ia berbeza, semak bahawa saiz blok GSD tidak berubah antara melihat dan mengeksport.

***

## Langkah Seterusnya

* [**Lapisan Imej**](image-layers.md) — lapisan mana untuk menjalankan indeks, dan apa maksud nilainya
* [**Membuka Imej Penuh Skrin**](opening-an-image-full-screen.md) — bacaan kursor, histogram dan kawalan GSD secara terperinci
* [**Formula Indeks Multispektral**](../project-settings/multispectral-index-formulas.md) — setiap pratetap, pada setiap permukaan
* [**Tetapan Projek**](../project-settings/project-settings.md) — membakar tetapan yang anda temui ke dalam pelaksanaan pemprosesan
