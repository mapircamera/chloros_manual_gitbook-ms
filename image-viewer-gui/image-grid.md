# Grid Imej

Selepas mengimport imej ke dalam projek, anda akan melihatnya disusun dalam grid di kawasan utama. Grid ini adalah tempat anda memilih **versi mana setiap imej yang sedang anda lihat** — butang di atasnya menukar setiap imej kecil sekaligus antara fail sumber dan setiap produk yang diproses.

## Saiz Miniatur

Gunakan gelangsar zum di penjuru kanan atas untuk melaras saiz miniatur imej. Gelangsar ini bermula dari **64 px hingga 1200 px**.

* **Ctrl + roda tetikus** juga mengubah skala miniatur.
* **Ctrl + `+`**/**Ctrl + `=`**dan**Ctrl + `−`** menukar saiz sebanyak 4 px setiap tekan. Lintasan papan kekunci berhenti pada 64 px di hujung kecil dan, di hujung besar, pada saiz apa pun yang muat tepat dua pratonton setiap baris dalam tetingkap semasa.
* Saiz yang anda tetapkan disimpan bersama projek (`UI → Grid thumbnail size` dalam `project.json`, lalai `160`), jadi membuka semula projek akan memulihkannya.

<figure><img src="../.gitbook/assets/chloros_grid_zoom.gif" alt=""><figcaption></figcaption></figure>*Resolusi* lakaran kecil adalah tetapan berasingan daripada *saiz* lakaran kecil: lihat **Paparan → Resolusi Lakaran Kecil Imej** dalam [Tetapan Projek](../project-settings/project-settings.md) (lalai 512 px pada sisi terpanjang). Saiz ialah betapa besar jubin itu dilukis; resolusi ialah berapa banyak butiran dimuatkan untuk mengisinya.***

## Bar alat grid

Barisan butang di atas grid mempunyai sehingga tiga kumpulan, dari kiri ke kanan:

1. **Per Trigger / Per Kamera** — mod pengelompokan. Muncul hanya untuk projek yang mengandungi rakaman LATTICE.
2. **Butang penapis kamera** — satu bagi setiap kamera LATTICE. Muncul hanya dalam mod Per Kamera.
3. **Butang mod Eksport/Paparan** — produk yang mana setiap imbasan kecil dipaparkan.

Apabila tetingkap terlalu sempit untuk kesemuanya, kumpulan akan mengecut dari kanan ke kiri menjadi menu lungsur apabila disasar: butang eksport/paparan akan terlipat dahulu, diikuti oleh butang kamera. Kumpulan yang disembunyikan meninggalkan satu butang dengan label pilihan yang sedang aktif, dan apabila disasar, ia akan menurunkankan keseluruhan set. **Per Trigger / Per Camera tidak pernah disembunyikan.**<!-- SCREENSHOT-NEEDED: Image grid toolbar of a LATTICE array project at full width, showing all three button groups inline: Per Trigger / Per Camera, three camera filter buttons labelled "LATT-M3M (serial)", and the export/view buttons including TIFF, RAW (Original), RAW (Radiance), RAW (Reflectance). -->***

## Butang Tonton Eksport

Butang-butang ini menukar pratonton grid antara jenis imej. **Sebuah butang akan muncul sebaik sahaja produk yang dinamakannya wujud** — yang bagi fail sumber bermaksud serta-merta semasa pengimportan, bukan selepas pemprosesan.Chloros

mengimbas semula produk projek semasa sesuatu larian sedang dijalankan, jadi butang-butang akan muncul semasa pemprosesan apabila setiap produk mula disimpan ke cakera.

### Butang asas

Butang eksport paling kiri dilabelkan untuk **apa yang sebenarnya anda import**:

| Apa yang anda import | Label butang |
| --- | --- |
|Survey3

RAW+JPG | `JPG` |
| LATTICE merakam dengan pratonton paparan di sebelah bingkai mentah | `PNG` atau `TIFF`, mengikut pratonton yang ada |
| LATTICE merakam di mana fail asas **ialah** bingkai raw | *tiada butang* — `RAW (Original)` sudah memaparkan fail tersebut |

Dalam projek campuran, label akan mengikut sambungan yang digunakan oleh kebanyakan imej.

### Butang produk

| Butang | Menunjukkan | Bila ia muncul |
| --- | --- | --- |
| **Sasaran** | Imej dengan sasaran penentukuran yang dikesan | Selepas pelaksanaan yang mengesan sasaran |
| **Refleksan** | Imej reflektan yang dikalibrasi | Hanya untuk projekSurvey3

— projek LATTICE menggunakan `RAW (Reflectance)` sebaliknya, jadi grid tidak pernah memaparkan dua butang reflektan |
| **Imbangan Putih** | Produk imbangan putih (kameraRGB

) | Selepas pemprosesan |
| **Betulkan Vignette** | Gantian tanpa penentukuran yang betulkan vignette | Selepas pelaksanaan di mana penentukuran reflektansi tidak dapat diterapkan dan *Betulkan Vignette* diaktifkan |
| **Tindak Balas Sensor** | Tindak balas tidak dikalibrasi tindak balas sensor | Sama, tetapi dengan *Pembetulan Vignette* dimatikan |
| **`RAW (<INDEX> Index)`** | Satu butang bagi setiap indeks yang dikira | Selepas pelaksanaan dengan indeks yang dikonfigurasikan |
| **`<INDEX> LUT`** | Satu butang bagi setiap indeks pemetaan warna | Selepas pelaksanaan dengan LUT dikonfigurasikan |
| **`<Index> <Index\|LUT> <NNN>`** | Satu butang bagi setiap larian eksport [Index/LUT Sandbox](index-lut-sandbox.md) | Saat eksport sandbox selesai |

### Butang aras LATTICE

Projek yang mengandungi tangkapan LATTICE menambah butang-butang ini, dilabel dengan nama aras dan bukannya nama produk:

| Butang | Aras |
| --- | --- |
| **RAW (Asal)** | Rangka mentah sumber, seperti yang diimport |
| **RAW (Radiasi)** | Radiasi spektral Float32, W/m²/sr/nm |
| **RAW (Refleksan)** | refleksan uint16, 32768 = ρ 1.0 |

`RAW (Original)` tersedia sebaik sahaja diimport — ia tidak memerlukan pemprosesan. Apabila import LATTICE langsung tiada butang asas (setiap fail asas tangkapan adalah bingkai mentahnya), grid akan bergerak sendiri ke butang tahap pertama yang tersedia supaya sorotan bar alat sepadan dengan apa yang anda lihat.

Keluaran dua tahapChloros

tidak mempunyai butang grid mereka sendiri:

* **Debayered** — paparan `RAW (Original)` sudah memaparkan imej selepas debayering, jadi butang kedua pada imej yang kelihatan sama akan menjadi gangguan. Produk `RAW (Debayered)` masih ditulis ke cakera dan masih boleh dipilih daripada menu lungsur lapisan skrin penuh.
* **Prviu** — pada kamera multispektral, prviu didaftarkan sebagai lapisan `White Balanced`, yang mempunyai butang. Pada kamera multispektral ia didaftarkan sebagai `RAW (Preview)` dan boleh diakses daripada menu lungsur lapisan skrin penuh.

{% hint style="info" %}
Butang-butang aras ini hanya dipaparkan untuk projek yang benar-benar mengandungi bingkai LATTICE. ProjekSurvey3

mendaftarkan beberapa nama lapisan dalaman yang sama, dan butang-butang tersebut ditapis untuk mereka supaya gridSurvey3

mengekalkan set `JPG / Targets / Reflectance` yang biasa.
{% endhint %}

Mengklik lakaran kecil grid akan membuka [Pemandang Imej](opening-an-image-full-screen.md) skrin penuh pada **produk yang sama yang dipaparkan oleh grid itu** — jika grid ditetapkan kepada `Targets`, lakaran kecil akan membuka imej sasaran yang dieksport.

<figure><img src="../.gitbook/assets/chloros_grid_mode.gif" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: This GIF predates the LATTICE level buttons and the toolbar group separators. Reshoot on a LATTICE project cycling base -> RAW (Original) -> RAW (Radiance) -> RAW (Reflectance) -> an index button, so the new button set and the level names are visible. -->


***

## Mengkumpulan projek LATTICE: Per Picu vs Per Kamera

Susunan tangkapan menghasilkan beberapa imej pada saat yang sama daripada modul kamera yang berbeza. Pengelompokan menentukan cara grid menyusunnya. Kedua-dua mod memaparkan bar tajuk boleh dikurangkan lebar penuh; **setiap kumpulan bermula dalam keadaan terdedah**, danChloros
mengingati kumpulan yang anda tutup. Keadaan samarut dijejaki secara berasingan bagi setiap mod, jadi menutup satu kumpulan dalam Per Kamera tidak akan menutup apa-apa dalam Per Pencetus.

### Per Kamera (laluan lalai)

Satu kumpulan bagi setiap modul kamera. Header memaparkan model dan siri kamera (`LATT-M3M — <serial>`) dan bilangan foto. Jubin dalam satu kumpulan disusun mengikut urutan masa berdasarkan acara tangkapan.

Dalam mod ini, bar alat juga mendapat satu butang penapis kamera bagi setiap kamera, dilabelkan `MODEL (SERIAL)`. Semua kamera bermula dalam keadaan terpilih; mengklik butang akan membatalkan pilihan kamera tersebut dan mengeluarkan kumpulan kamera itu daripada grid. Ini adalah cara terpantas untuk menyemak satu kumpulan kamera merentasi keseluruhan penerbangan.

### Bagi Setiap Pencetus

Satu kumpulan bagi setiap peristiwa tangkapan — set bingkai yang dirakam oleh semua modul pada pencetus yang sama. Header memaparkan masa tangkapan, bilangan kamera yang menyumbang, dan lencana bagi setiap model kamera dalam kumpulan. Jubin dalam satu kumpulan disusun mengikut nombor siri kamera, jadi jalur yang sama terletak dalam lajur yang sama untuk setiap pencetus.

Imej<!-- SCREENSHOT-NEEDED: Image grid in Per Trigger mode for a 3-camera LATTICE array, showing two consecutive trigger groups with their header bars (chevron, capture timestamp, "3 cameras", and the three model badges) and one group collapsed to show the closed state. -->
bukan LATTICE dalam projek campuran tidak dikumpulkan — ia dipaparkan sebagai jubin biasa selepas kumpulan-kumpulan tersebut.

***

## Thumbnail grid mengikut saiz blok GSD

Jika anda telah menetapkan saiz blok **GSD (px)** dalam bar sisi tab imej, pratonton grid akan dipaparkan pada resolusi darat yang sama — bukan hanya pada paparan skrin penuh. Saiz blok 8 bermaksud setiap piksel yang dipaparkan adalah purata blok piksel sumber bersaiz 8 × 8, di mana-mana dalam aplikasi yang memaparkan imej tersebut.

Kerana satu jubin pada asalnya hanya selebar beberapa ratus piksel, saiz blok kasar berhenti memberikan perbezaan yang ketara pada grid jauh sebelum ia berlaku dalam paparan skrin penuh: bingkai 4000 px yang dilukis ke dalam jubin 160 px sudah berada pada kira-kira 25 piksel sumber bagi setiap piksel yang dipaparkan. Lihat [Membuka Imej Penuh Skrin](opening-an-image-full-screen.md#gsd-block-size) untuk kawalan itu sendiri.

***

## Halaman berkaitan

* [**Membuka Imej Penuh Skrin**](opening-an-image-full-screen.md) — pemapar skrin penuh, nilai kursor dan histogram
* [****Lapisan Imej**](image-layers.md) — menu lungsur lapisan di dalam pemapar skrin penuh
* [**Sandbox Indeks/LUT**](index-lut-sandbox.md) — membina dan mengeksport visualisasi indeks
* [**Tetapan Projek**](../project-settings/project-settings.md) — suis eksport yang menentukan produk mana yang wujud sama sekali
