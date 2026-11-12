# Tetapan Kamera

Tab **Kamera** ialah permukaan kawalan langsung Chloros untuk kamera LATTICE: satu kawasan siaran utama yang memaparkan setiap kamera yang disambungkan sebagai jubin langsung, dan sebuah bar sisi yang meluncur antara tiga halaman — **senarai kamera**, sebuah**panel tetapan**(per-kamera, susunan, atau tetapan tangkapan — satu pada satu masa), dan**Pengira Indeks**. Halaman ini mendokumenkan setiap kawalan dalam senarai kamera, panel tetapan per-kamera, dan panel tetapan susunan. Mod tangkapan, pilihan jenis eksport, dan aliran Rakam Semua berada di halaman rakan kongsi [Tetapan & Mod Rakaman](capture.md).

Tab Kamera akan muncul di bar sisi sebaik sahaja backend Chloros sedia. Semua kawalan di bawah berkomunikasi dengan backend tempatan melalui `127.0.0.1:5000`; perubahan akan digunakan pada kamera secara langsung serta-merta melainkan dinyatakan sebaliknya.

## Jenis kamera yang digunakan pada halaman ini

Kawalan akan memaparkan atau menyembunyikan diri berdasarkan jenis kamera yang dipilih. Manual ini menggunakan istilah-istilah ini di merata tempat:

| Istilah | Maksud | Menapis saluran |
| --- | --- | --- |
| **Kamera RGB** | LATTICE M3C dengan penapis FRGB (model mengandungi `-FRGB`) | Red / Green / Blue |
| **Bayer multispektral** | LATTICE M3C dengan FRGN, FOCN, atau FNGB | FRGN: Red / Green / NIR · FOCN: Orange / Cyan / NIR · FNGB: NIR / Green / Blue |
| **Mono (M3M)** | LATTICE M3M — satu penapis jalur sempit, satu jalur yang dikalibrasi | Satu jalur |
| **Ahli susunan** | Kamera yang disambungkan sebagai sebahagian daripada susunan selari (paparan gabungan atau berasingan) | Mengikut penapisnya |

Kamera RGB mendapat pemprosesan fotometrik (imbangan putih, profil warna, gamma); kamera multispektral dan mono mendapat rantaian radiometrik dan melangkau kawalan fotometrik. Ahli susunan mengendalikan tetapan peringkat aliran (format piksel, resolusi, binning, pencetus, kadar bingkai) ke dalam susunan — baris-baris tersebut menjadi hanya-baca dalam panel setiap kamera dan sebaliknya dipindahkan ke panel tetapan susunan.

## Kawasan suapan utama



<!-- SCREENSHOT-NEEDED: Cameras tab with 2+ cameras connected in grid view — live tiles visible with name and fps overlays, sidebar camera list open on the right. -->

Apabila tiada kamera disambungkan, kawasan suapan akan memaparkan paparan **"Sambungkan kamera untuk mula"**dengan dua butang:**Sambungkan Kamera**(hijau, membuka dialog sambungan satu kamera) dan**Sambungkan Susunan** (biru, membuka dialog sambungan susunan). Dialog sambungan itu sendiri didokumenkan dalam [Menyambungkan Kamera](connecting.md); konsep susunan (penyegerakan, peringkat, jalur lebar) dalam [Susunan Pelbagai Kamera](arrays.md). Apabila anda membuka projek yang disimpan yang mempunyai kamera di dalamnya, paparan permulaan sebaliknya akan memaparkan pemutar dengan "Membuka semula N kamera yang disimpan…" sementara Chloros memulihkan aliran daripada sesi terakhir.

<!-- SCREENSHOT-NEEDED: Cameras tab empty state — the "Connect a camera to get started" splash with the green Connect Camera and blue Connect Array buttons. -->

### Bar atas

| Kawalan | Fungsinya |
| --- | --- |
| **Togol mod paparan**| Menukar antara**paparan grid**(semua jubin sebagai sel) dan**paparan senarai**(baris lebar penuh: setiap susunan gabungan sebagai satu baris komposit, setiap ahli susunan**berasingan** sebagai barisnya sendiri dengan aliran dan jadual Sasaran Kalibrasi tersendiri, kemudian SATU kamera berdiri sendiri — yang aktif — di bawah). Petua alat: "Tukar ke paparan grid" / "Tukar ke paparan senarai". |
| **Kunci grid**(gembok) | Secara lalai**dikunci** — jubin dibekukan di tempat. Buka kuncinya untuk menyusun semula jubin ke dalam mana-mana slot dengan menyeret (jarak dikekalkan). Grid secara automatik dikunci semula setiap kali kamera baru disambungkan. Petua alat: "Buka kunci grid (aktifkan seret jubin)" / "Kunci grid (bekukan jubin di tempat)". |
| Pemateri **Zoom Suapan** | Saiz jubin, dari 60 px sehingga ke lebar bekas penuh. Sel mengekalkan nisbah 4:3. Pada lebar sel di bawah 200 px, overlay nama dan fps akan tersembunyi untuk memastikan jubin kelihatan kemas. |

### Jubin Suapan

Setiap kamera menghasilkan jubin langsung komposit; sebuah kamera juga boleh memaparkan tiga jubin **pecahan per-saluran** skala kelabu (lihat [Pecahan Saluran](#overlay-paparan-yang-ditaruh-di-atas-siaran-langsung)), dan susunan memaparkan jubin gabungan. Jubin aktif mempunyai cincin pilihan dalam warna kamera (atau susunan).

Apabila meletakkan penunjuk ke atas jubin, butang tutup **X** akan muncul:

* Menutup jubin **komposit** sementara split saluran ia masih tersimpan hanya menyembunyikan komposit tersebut.
* Menutup **jubin terakhir yang kelihatan bagi kamera berdiri sendiri** memutuskan sambungan kamera tersebut.
* **Jubin split ahli susunan gabungan tidak pernah memutuskan sambungan** kamera — ia hanya menyembunyikannya.

Apabila grid tidak dikunci, seret mana-mana jubin ke mana-mana slot; susun atur disimpan bersama projek.

## Bar sisi — senarai

<!-- SCREENSHOT-NEEDED: sidebar camera list pane showing a standalone camera row and an ARRAY group with indented member rows, the DAQ on/off pill visible on the array row, plus the Connect Camera / Connect Array / Capture All buttons at the top. -->

kamera Halaman bar sisi pertama menyenaraikan setiap kamera dan susunan yang disambungkan:

* **Sambungkan Kamera**(hijau) /**Sambungkan Susunan** (biru, memaparkan "Mengesan..." semasa mengimbas). Kedua-duanya dilumpuhkan semasa dialog sambungan dibuka.
* **Rakam Semua** (merah) — merakam setiap kamera yang disenaraikan dengan jenis eksport yang dipilih dalam Tetapan Rakaman. Memerlukan projek yang dibuka. Didokumenkan sepenuhnya dalam [Tetapan & Mod Rakaman](capture.md).
* **Gear Tetapan Rakaman** (di sebelah Rakam Semua) — membuka [pane Tetapan Rakaman](capture.md#the-capture-settings-pane). Dilumpuhkan tanpa projek atau semasa mengambil imej.

### Baris kamera

Setiap baris kamera memaparkan sempadan berkod warna (warna tersuai kamera), label "CAM" — dengan **M**biru (utama) atau huruf**S** hijau (hamba) bagi peranan ahli susunan — dan nama paparan. Nama lalai ialah `LATTICE-MODEL (serial)`; tukar namanya dari tetingkap tetapan bagi setiap kamera. Butang baris:

| Butang | Kesan |
| --- | --- |
| **Mata**| Tukar keterlihatan. Kamera yang disembunyikan akan keluar dari grid dan**dikecualikan daripada Rakam Semua**. |
| **Gear** | Buka panel tetapan bagi setiap kamera (seksyen seterusnya). |
| **Pause / Play**| Bekukan pratonton langsung**di pihak paparan sahaja** — rakaman di belakang terus berjalan. Kamera yang dipause tidak boleh merakam. |
| **X** | Putuskan sambungan. UI dikemas kini serta-merta (optimistik); pemutusan sambungan oleh backend sendiri boleh mengambil masa 10–30 saat untuk diselesaikan. |

### Baris tatasusunan

Satu baris tatasusunan memaparkan lencana "ARRAY" dalam warna tatasusunan, nama tatasusunan (boleh dinamakan semula dalam tetapan tatasusunan), dan pil **DAQ · hidup/mati**—**hidup** apabila Penderia Cahaya pada peringkat tatasusunan ditetapkan *atau* mana-mana ahli mempunyai penderia bagi setiap kamera; Petua alatnya menyenaraikan dengan tepat sensor mana yang menyalurkan kepada apa. Kamera ahli disenaraikan di bawah dengan indentasi, masing-masing mempunyai baris tersendiri. Butang baris tatasusunan: **mata**(menyembunyikan/menunjukkan SEMUA ahli sekaligus),**gear**(panel tetapan tatasusunan),**X**(memutuskan sambungan keseluruhan tatasusunan).

Status penderia cahaya (DLS) yang digunakan dalam baris tatasusunan dan panel tetapan tatasusunan mempunyai empat keadaan:**padam**,**menunggu**(tiada spektrum lagi),**aktif**(spektrum tiba dalam 3 saat terakhir), dan**lapuk** — tiada spektrum baru dalam 3 saat, tetapi bacaan terakhir *masih digunakan* (bacaan DAQ tidak pernah tamat tempoh pada laluan tangkapan).

Sehingga anda menyusunnya semula, senarai memaparkan susunan dahulu, kemudian kamera berdiri sendiri, dengan kamera (dan ahli setiap susunan) dalam **susunan kamera**: M3C (Bayer) sebelum M3M (mono); dalam kalangan M3C, FRGB, FRGN, FOCN, kemudian FNGB; M3M mengikut panjang gelombang penapis, yang terendah di hadapan. Susun atur lalai grid mengikuti susunan yang sama. Anda boleh menyeret kamera berdiri sendiri dan kumpulan tatasusunan keseluruhan melepasi satu sama lain di bar sisi untuk menyusun semula senarai; ahli tatasusunan tidak boleh diseret secara berasingan.

## Panel tetapan bagi setiap kamera

Buka dengan **gear** pada baris kamera. Panel ini akan tergelincir ke atas senarai kamera.

<!-- SCREENSHOT-NEEDED: per-camera settings pane, top portion — header with color swatch, camera name, rename pencil and close X; live histogram with the orange dashed AE-target line and green mean-luma line; the RGB per-band toggle button visible top-right of the histogram. -->



**Header**:**petak warna**kamera (klik untuk membuka pemilih warna terbina dalam — menetapkan warna sempadan bar sisi dan cincin pilihan jubin),**nama**dengan butang pensel**Namakan Semula**(menyimpan nama kosong akan kembali kepada lalai `MODEL (serial)`), dan**×** untuk tutup.

### Histogram langsung

Di bahagian atas tetingkap terdapat histogram luma langsung yang dikira daripada pratonton **JPEG** pada kira-kira 8 Hz. Puratanya diberi pemberat Bayer — (R+2G+B)/4 — untuk menepati metrik AE kamera itu sendiri.

* **Garis putus-putus Jingga** = sasaran AE.**Seret secara mendatar untuk menyasarkan semula** — satu arahan dihantar apabila dilepaskan, dan menyeret akan menukar mod sasaran AE kepada Manual.
* **Garis pepenuh Hijau** = luma min sebenar (apa yang AE berikan pada masa ini).
* **Butang**RGB **(atas-kanan): menukar histogram superimposisi setiap jalur yang diwarnakan oleh penapis kamera (contohnya pada FRGN: kelabu**NIR **, hijau, merah). Pada kamera mono (M3M), butang ini bertulis "MONO" dan dilumpuhkan — mono sentiasa memaparkan histogram luma jalur tunggal.
* Label paksi-X mengikut kedalaman bit sensor format piksel semasa: 0..255, 0..1023, 0..4095, atau 0..65535.

### Baris

<!-- SCREENSHOT-NEEDED: per-camera settings info rows — Model, Radiometric Calibration "Active" badge with the tier/sha/date caption, Calibration Report Download button, Serial, Firmware row showing the "Up to date" state, IP, Temperature readout, Calibration Target checkbox, Light Sensor dropdown. -->

maklumat kamera

| Baris | Perilaku |
| --- | --- |
| **Model** | Baca sahaja (contohnya `LATT-M3C-L87-FRGN`). |
| **Kalibrasi Radiometrik** | Lencana "Aktif" Green dengan kapsyen yang memaparkan peringkat kalibrasi, hash, tarikh kalibrasi, dan senarai jalur, dimuat daripada pek kalibrasi kamera (lihat [Kalibrasi Radiometrik Kilang](https://mapir.gitbook.io/lattice-camera/calibration/factory-radiometric-calibration)). **Disembunyikan untuk kamera RGB** — ia mempunyai kalibrasi keseimbangan putih fotometrik, bukan radiasi per-band. |
| **Laporan Kalibrasi**| Butang**Muat Turun** — membuka PDF sijil kalibrasi NIST per-siri kamera dalam pemapar OS anda. Jika sijil belum disimpan dalam cache, Chloros akan memaparkan petunjuk sebaliknya. |
| **Siri** | Baca sahaja. |
| **Firmware**| Menunjukkan versi semasa, kemudian menentukan versi yang tersedia untuk model ini (disimpan dalam cache mengikut model — satu susunan N-kamera menyemak pelayan sekali sahaja). Menunjukkan: "Semak…" → butang**"Kemas kini ke X"**→ "Sedang mem-flash…" → "Dikemas kini dari A ke B" / "Gagal: …" / "Dilangkau: …" / hijau**"Terbaru"**. Petua butang kemas kini: "Seting kilang + flash + atur semula UserSet1. ~2–3 minit; jangan putuskan sambungan." |
| **IP** | Baca sahaja. |
| **Suhu** | Hanya baca, dikemas kini setiap 3 saat. Bertukar oren pada ≥65 °C dan merah dengan ⚠ pada ≥75 °C. |
| **Sasaran Kalibrasi** kotak semak | Mengaktifkan pengesanan sasaran pantulan ArUco dengan jadual pengesahan NDVI bagi setiap panel di bawah aliran langsung (pemandangan senarai) — lihat [Jadual Sasaran Kalibrasi](#the-calibration-target-jadual). Hanya untuk sesi — sentiasa dibuka dalam keadaan dimatikan. |
| Senarai turun **Penderia Cahaya** | Mengikat penderia cahaya DAQ (DAQ-E/M/U, daripada senarai tab Penderia Cahaya) kepada kamera ini untuk pembetulan pencahayaan cahaya ke bawah (DLS) dan pendedahan automatik ramalan. ""Tiada" akan memadamkan pengikatan. Jika tiada penderia disambungkan, menu lungsur akan memaparkan "(tiada penderia disambungkan — buka tab DAQ)". Pengikatan disimpan bersama projek. |

#### Jadual Sasaran Kalibrasi

Jadual ini adalah **baca sah sahaja** ("sah sahaja — tidak mengubah kal"). Headernya memaparkan siri kamera, penapis, nombor sasaran dan saiz penanda dalam piksel, dan ia mempunyai satu baris bagi setiap panel:

* **Panel**, kemudian satu lajur bagi setiap jalur dengan nilai panel — kamera Bayer memaparkan tiga jalurnya mengikut nama dan panjang gelombang (contohnya FRGN: Red 660 nm / Green 550 nm / NIR 850 nm); sebuah kamera **mono (M3M) memaparkan satu jalur tunggalnya** (contohnya `F750`) dan tiada lajur NDVI.
* **Sat** — perkadaran piksel panel yang tepu (kuning jingga dari 1 %, merah dari 5 %).
* **NDVI Meas**,**NDVI Theo** (NDVI rujukan reflektansi panel) dan **Δ**— kamera multispektral Bayer sahaja. Tanda kecil selepas "NDVI Meas" menyatakan apa yang NDVI yang diukur dikira daripadanya:**ρ**(reflektansi — bacaan penderia cahaya tersedia),**L**(radiasi — tiada bacaan penderia cahaya) atau**DN** (nilai mentah kamera). Hanya NDVI**ρ** boleh dibandingkan dengan NDVI Theo: bagi L dan DN, lajur Δ diwarnakan kelabu dan satu nota menyatakan demikian ("Tiada bacaan penderia cahaya: NDVI adalah daripada radiasi, jadi Δ bukan pemeriksaan kalibrasi" / "NDVI adalah daripada nilai mentah kamera, jadi Δ bukan pemeriksaan kalibrasi"). Dengan ρ, Δ berwarna hijau di bawah 0.02, jingga di bawah 0.05, merah jika tidak.

<!-- SCREENSHOT-NEEDED: a 1.3.0 Calibration Target table under a live feed in list view — a Bayer camera's table showing the band columns, Sat, and "NDVI Meas ρ" / NDVI Theo / Δ; ideally next to a mono camera's single-band table with no NDVI columns. -->

### Pendedahan & Penguatan

<!-- SCREENSHOT-NEEDED: per-camera Exposure & Gain section — Exposure (us) and Gain (dB) rows with Auto/Manual toggles, AE Target Brightness, AE Smoothing slider, AE Region of Interest row with the Aim button, and (on an array camera) AE Tune Speed and Highlight Protection rows. -->

Semua input berangka di sini menggunakan pemutar tahan untuk mempercepat: ketuk = ±1, tahan >1.5 saat = ±10, tahan >3 saat = ±100. Nilai dihantar ke kamera apabila anda melepaskan.

| Kawalan | Julat / pilihan | Lalai | Terpakai kepada | Fungsinya |
| --- | --- | --- | --- | --- |
| **Pendedahan (us)**| Min/maksimum langsung kamera | Auto | Semua | Masa pendedahan dalam mikrosaat, dengan**Auto/Manual**togol. Auto = pendedahan automatik berterusan di pihak kamera. Menetapkan pendedahan manual mengekalkan**Gain** pada Auto jika ia begitu, membebaskan penggunaan seluruh julat gain kamera, sehingga anda menyunting gain atau menetapkannya kepada Manual. |
| **Penguatan (dB)**| Had minimum/maksimum secara langsung kamera (contohnya sehingga 48 dB) | Manual (padam) | Semua | Penguatan analog/digital dengan suis**Auto/Manual** sendiri. |
| **Kecerahan Sasaran AE**| 0–255 | 80, mod**Auto**| Semua (boleh disunting apabila AE atau penguatan automatik diaktifkan) | Kecerahan yang disasarkan oleh AE. Dalam**Auto**(lalai) pengawal belakang berasaskan histogram memilih sasaran itu sendiri, mengekalkan pendedahan pada 60–75% maksimum sensor. Meny taip nilai atau menyeret garisan jingga histogram menukarnya kepada**Manual**. |
| **Pelicinan AE** | 0.5–40, langkah 0.1 | 8.0 | Semua | Penampan AE. Petua alat: "Lebih rendah = AE bertindak balas lebih pantas (boleh berdenyut pada fps tinggi). Lebih tinggi = lebih lancar / lebih perlahan." Nilai yang jauh di bawah lalai boleh menyebabkan AE berdenyut dan menstabilkan penstriman pada kadar bingkai tinggi; 8.0 adalah lalai yang stabil. |
| **Kawasan Minat AE**| Petak semak Aktifkan + butang**Aim**| Padam | Semua | Apabila diaktifkan, AE hanya mengukur kawasan garis putus-putus hijau dan bukannya keseluruhan bingkai.**Aim** mengaktifkan klik-untuk-letakkan pada siaran langsung: satu klik memusatkan kawasan pada 30% bingkai; seret-klik mengimbas segi empat sama tersuai (minimum 5% × 5%). Sasaran akan dilucutkan selepas satu penempatan. Kawasan itu dipetakan semula ke koordinat asal kamera di bawah sebarang putaran/cerminan yang telah anda tetapkan, dan disimpan bersama projek. |
| **Kelajuan Larasan AE** | 0.1–5, langkah 0.1 | 1.0 | Ahli susunan sahaja | Betapa pantas sasaran AE automatik menjejaki perubahan kecerahan pemandangan; 1.0× menyemak semula setiap 2.5 saat. |
| **Perlindungan Sorotan** | Ketat (1 %) / Normal (5 %) / Longgar (15 %) | Ketat | Kamera yang mendedahkan tetapan | Berapa banyak bingkai yang boleh dipotong kepada putih sebelum AE menggelapkan imej. |

{% hint style="info" %}
**Keperluan cahaya untuk kamera multispektral Bayer (RGN / OCN / NGB):** pemandangan mesti mempunyai cahaya yang mencukupi dalam ketiga-tiga saluran atau penentukuran tidak akan berfungsi dengan betul — satu pendedahan sensor tunggal merangkumi ketiga-tiga spektrum. Gunakan penderia cahaya DAQ untuk mengukur cahaya anda, atau beralih sepenuhnya ke mono (M3M) supaya setiap jalur mendapat pendedahan tersendiri. Jika tangkapan melanggar perkara ini, Chloros akan mengesan dan memberi amaran kepada anda (pemberitahuan unmix-clamp). Secara langsung, jubin pembahagi saluran memaparkan **"Data jalur keyakinan rendah · N%"** apabila unmix mengeklampkan jalur tersebut pada 25% atau lebih pikselnya.
{% endhint %}

{% hint style="info" %}
**Bagaimana pendedahan automatik menggunakan pendedahan dan penguatan (1.3.0).** Dengan Gain pada Auto, Chloros mengekalkan gain pada 0 dB sehingga pendedahan mencapai nilai terpanjang yang dibenarkan (had atas AE, tempoh pencetus array, atau siling pendedahan DAQ); barulah gain meningkat. Dalam satu array, kadar pencetus didahulukan: pendedahan automatik mengisi tempoh yang diberikan oleh kadar tersebut, kemudian meningkatkan penguatan, jadi pemandangan yang semakin gelap tidak lagi menurunkan kadar pencetus Auto (dalam 1.2.0 ia berlaku). Eksposur **manual**pada anggota array yang lebih lama daripada tempoh pencetus akan memperlahankan pencetus array untuk menyesuaikannya; kadar Auto akan kembali sebaik sahaja setiap ahli yang melambatkannya kembali ke pendedahan auto. Dengan**Pendedahan**dan**Kecerahan Sasaran AE** kedua-duanya pada Auto dan penderia cahaya DAQ memberikan bacaan, pendedahan setiap kamera dikekalkan sebaik sahaja pendedahan auto stabil, selagi cahaya yang diukur kekal dalam lingkungan kira-kira ±25 %; perubahan cahaya yang lebih besar dan berterusan, butiran tetapan dimulakan semula (contohnya perubahan ROI) atau perubahan pendedahan anda sendiri akan mengaktifkannya semula.
{% endhint %}

{% hint style="info" %}
**Bagaimana pendedahan automatik berfungsi (1.3.0).**

* Pendedahan manual mengekalkan penguatan pada Auto sehingga anda menukar penguatan.
* Sorotan kecil tidak menggelapkan adegan: pantulan cermin sehingga 1% daripada bingkai diabaikan, dan pada panel sasaran penentukuran yang diketahui, sehingga 0.5% daripada panel.
* Had pendedahan DAQ hanya terpakai semasa sasaran penentukuran kelihatan; jika tiada panel dilihat, kamera akan mengambil gambar adegan tersebut.
* Eksposur kekal stabil pada cahaya yang konsisten dan menstabilkan dalam beberapa saat selepas perubahan besar. Klip tidak lagi mengehadkan eksposur secara kekal, dan perubahan ROI mencari eksposur semula.
* Sasaran kecerahan tidak naik melebihi julat kamerajangkauan, jadi cahaya yang kembali tidak akan mencerahkan bingkai secara berlebihan.
* Kamera yang pengecasan automatiknya berhenti bertindak balas di bawah cahaya matahari terik akan dikekalkan pada tahap cahaya sekelilingnya, yang dibaca oleh penderia cahaya atau, jika tiada disambungkan, oleh bingkai kamera itu sendiri, dan dilepaskan apabila cahaya berubah. Sensor cahaya yang gelap atau tertutup tidak mengganggu ini.
{% endhint %}

### Format Pixel & Resolusi

<!-- SCREENSHOT-NEEDED: per-camera Pixel Format & Resolution section on a STANDALONE camera — Pixel Format, Resolution, and Binning dropdowns plus the Current WxH readout. A second capture on an array member showing the read-only "Set in array settings" state would also be useful. -->

**Ahli-ahli array** memaparkan baris "Semasa" (format + LxT) dan "Pengumpulan" yang hanya boleh dibaca dengan nota "Ditetapkan dalam tetapan array" — memulakan semula aliran pada satu ahli akan memutuskan penyelarasan, jadi ini diuruskan dalam [panel tetapan array](#array-settings-pane).**Kamera berdiri sendiri** mendapat:

| Kawalan | Pilihan | Fungsinya |
| --- | --- | --- |
| **Format Pixel** | 8-bit / 10-bit / 12-bit / 16-bit | Kedalaman bit sensor; keluarga format adalah milik sensor. Lalai ialah 12-bit, dan projek menyimpan apa yang anda tetapkan, termasuk 8-bit, dan disambung semula pada tetapan tersebut. |
| **Resolusi** | Penuh / Separuh / Suku | Berkaitan dengan binning semasa: Penuh = 2048/N × 1536/N untuk binning N×N. |
| **Pembingkaian** | 1x1 (tiada) / 2x2 / 4x4 | Pembingkaian perkakasan N×N — nilai yang lebih besar mengurangkan resolusi tetapi meningkatkan SNR dan kadar bingkai. Menukar tetapan ini akan memulakan semula aliran dan menetapkan semula sebarang ROI kepada medan pandangan penuh yang baru. |
| **Semasa** | baca sahaja | Saiz sebenar WxH dan offset (x, y) yang sedang digunakan. |

### Pratonton Langsung

Semua dalam bahagian ini hanya untuk **pihak paparan**— ia mengubah apa yang anda lihat dalam siaran langsung, manakala rakaman yang disimpan kekal linear dan tidak diubah — dengan satu pengecualian:**Vignette** bersifat radiometrik dan juga menjejaskan eksport (disebut di bawah).

<!-- SCREENSHOT-NEEDED: per-camera Live Preview section on an RGB (FRGB) camera — Render resolution, White Balance mode, Gamma, Denoise, Sharpness, Vignette, Color Profile dropdown open showing Raw/Linear/Natural/Enhanced/Custom Temperature, Saturation, Contrast, Mirror H/V and Rotation. -->

<!-- SCREENSHOT-NEEDED: per-camera Live Preview section on a Bayer multispectral (e.g. FRGN) camera — showing the Index row with its gear button (and the absence of the RGB-only White Balance / Gamma / Color Profile / Saturation / Contrast rows). -->



| Kawalan | Julat / pilihan | Lalai | Terpakai pada | Fungsinya |
| --- | --- | --- | --- | --- |
| **Resolusi render** | 360p (tercepat) / 480p / 720p / 1080p / Resolusi sensor asli (terlambat) | 720p | Semua | Ketinggian di mana backend menjalankan rantaian pratonton radiometrik. Lebih rendah mengurangkan kadar bingkai tanpa menukar bidang pandangan. |
| **Indeks**| Petak semak Aktifkan + gear | Padam | Multispektral Bayer sahaja,**bukan** ahli susunan gabungan | Pratonton indeks vegetasi secara langsung. Gear tersebut membuka [Kalkulator Indeks](#index-calculator-pane) yang dikongsi, dimuatkan awal dengan jalur semula jadi penapis kamera (contohnya `Red_660_RGN`, `Green_550_RGN`, `NIR_850_RGN`). Ekspresi tersuai ditambah LUT (hidup/mati, tahap lalai 7-stop, min lalai 0.2, maks lalai 1) dikira pada setiap bingkai pratonton. Ahli susunan gabungan menyembunyikan baris ini — susunan itu mempunyai satu indeks kongsi. |
| **Imbangan Putih** | Padam / Sekali / Berterusan + butang rakam semula | Berterusan | Hanya RGB | Imbangan putih secara langsung. Butang Refresh merakam semula imbangan putih daripada spektrum DLS semasa (dimatikan apabila mod adalah Off). |
| **Gamma** | Hidup / Mati | Hidup | Hanya RGB | Paparan gamma (γ = 2.2 LUT) pada pratonton langsung. Gambar yang disimpan kekal linear. |
| **Penurunan Kebisingan** | Kotak semak + kekuatan 0–100 | Padam / 50 | Semua (per kamera, walaupun dalam susunan) | Penapis dua hala pada pratonton langsung. Lebih tinggi = lebih lancar tetapi butiran lebih lembut. |
| **Ketajaman** | Petak semak + kekuatan 0–100 | Padam / 30 | Semua | Topeng kabur pada pratonton langsung, digunakan terakhir. Boleh menguatkan hingar. Hanya untuk pratonton. |
| **Vignette**| Kotak semak + kekuatan 0–100 | Matikan / 0 | Semua | Penyingkiran vignette residu secara manual (memerahkan sudut), dilapis di atas anggaran Smart Vignette array.**Radiometrik — menjejaskan paparan langsung DAN eksport**, tidak seperti Denoise/Ketajaman. |
| **Profil Warna** | Mentah / Linear / Semula Jadi / Ditingkatkan / Suhu Tersuai | Semula Jadi | Hanya RGB | Lihat di bawah. |
| **Suhu Warna** | 2000–10000 K, langkah 100 | 5500 K | RGB sahaja, profil Suhu Tersuai sahaja | Menetapkan imbangan putih pada suhu warna berkaitan tetap (pautan DLS diabaikan). Nilai Kelvin terakhir yang dipilih akan diingati semasa menukar profil. |
| **Sorotan** | −100 hingga 0, langkah 1 | 0 (dimatikan) | Hanya RGB | Baharu dalam 1.3.0. Menggelapkan kawasan paling terang dan paling putih dalam gambar (0 = tidak berubah, −100 = paling kuat). Terpakai pada pratonton langsung **dan pada tangkapan peringkat paparan (Pratonton)***, yang merekod tetapan (`Chloros:DisplayHighlights`); eksport radiasi dan pantulan tidak pernah terjejas. Ia tidak dapat memulihkan data sensor yang terpotong — kurangkan pendedahan untuk itu. Overlay zebra mengikuti imej yang menggelap. Dilumpuhkan dengan profil warna Raw ("Tidak diterapkan dengan profil warna Raw."). |
| **Saturasi** | 0–200 (100 = neutral) | 100 | Hanya RGB | Saturasi HSV pada pratonton langsung. |
| **Kontras** | 0–200 (100 = neutral) | 100 | Hanya RGB | Kontras linear sekitar kelabu pertengahan pada pratonton langsung. |
| **Cermin H / Cermin V** | Petak semak | Padam | Semua | Membalikkan pratonton secara mendatar / menegak. |
| **Pusingan**| 0° / 90° / 180° / 270° | 0° | Semua | Memusingkan pratonton. Orientasi digunakan pada penghujung rantaian pratonton backend.**Sambungan yang disimpan (1.3.0):**produk Preview, Radiance, Reflectance dan Index kamera berdiri sendiri dipusingkan oleh Cermin/Pusingan, manakala**Raw dan Debayered kekal dalam orientasi asal kamera**; setiap fail tangkapan merekodkan tetapan dalam XMPnya, jadi pemprosesan raw kemudian akan menerapkannya dengan cara yang sama.**Fail ahli tatasusunan kekal asal** (tetapan direkodkan, bukan diterapkan), dan pandangan komposit tatasusunan mengabaikannya. |

**Semantik profil warna** (kamera RGB):

* **Raw** — memintas sepenuhnya rantaian pemprosesan.
* **Linear** — isyarat gelap + medan rata + imbangan putih; tiada matriks warna, tiada gamma.
* **Natural** *(lalai)* — linear ditambah matriks pembetulan warna yang diukur dan lengkung nada yang menyesuaikan diri dengan pemandangan.
* **Enhanced** — Natural ditambah kecerahan warna dan kontras tempatan CLAHE. Kos tambahan hanya terpakai pada pratonton langsung — rakaman yang disimpan sentiasa mendapat kemasan penuh tanpa mengira profil.
* **Suhu Tersuai** — Natural dengan imbangan putih dipaku pada nilai Kelvin pilihan anda.

{% hint style="warning" %}
Untuk Natural, Enhanced, dan Custom Temperature, panel memaparkan nota nada: bingkai dipercerahkan mengikut babak mereka sendiri, jadi imej *paparan* yang disimpan tidak boleh dibandingkan bingkai ke bingkai. **Eksport radiasi atau pantulan untuk pengukuran.**
{% endhint %}

### Lapisan paparan (ditaruh di atas siaran langsung)

Ini hanya untuk frontend — dilukis di atas video, tidak pernah menyentuh aliran atau rakaman.

<!-- SCREENSHOT-NEEDED: a live feed tile with overlays active — zebra stripes on clipped sky, 3x3 grid, focus peaking in the default orange, and the on-feed histogram strip; the overlays section of the settings pane visible alongside. -->

| Lapisan | Kawalan | Lalai | Fungsinya |
| --- | --- | --- | --- |
| **Zebra** | Kotak semak + ambang 200–255 | Padam / 250 | Garis mendiagnal magenta pada piksel yang dipotong. |
| **Salib** | Kotak semak | Padam | Penanda tengah bingkai. |
| **Grid** | Off / 3 × 3 / 9 × 9 | Off | Grid komposisi. |
| **Histogram** | Kotak semak + lebar 0.10–0.90 bingkai | Off / 0.25 | Jalur histogram pada siaran. |
| **Focus Peak** | Kotak semak + ambang 20–200 + sampel warna | Padam / 80 / `#ff5722` | Sorotan tepi Sobel untuk menumpukan imej. |
| **Pemisahan Saluran** | "Tunjukkan Pemisahan (Red / Green / NIR)" / butang "Sembunyikan Pecahan" | Tersembunyi | Menambah tiga jubin skala kelabu bebas bagi setiap saluran di sebelah komposit (label butang mengikut saluran penapis kamera). Setiap jubin pecahan boleh diseret dan berkongsi warna sempadan kamera. Tidak tersedia pada kamera mono. Disimpan bersama projek. |

### Pengukur Titik

* Kotak semak **Klik untuk Sampel**: klik siaran langsung untuk mengambil sampel satu piksel (retikel panah akan menandanya), atau seret klik untuk memilih kawasan bagi purata piksel.**Kosongkan**memadam sampel dan retikel. Tidak boleh digunakan bersama mod**Aim** AE-ROI.
**Senarai lungsur **Tunjukkan**:**Bahan mentah (kedalaman bit)**— nombor digital asli pada kedalaman bit penderia (contohnya 12-bit → 0..4095) — atau**Paparan (8-bit)** (lalai). Apabila indeks langsung diaktifkan, Paparan akan memaparkan nilai indeks yang dikira (contohnyaNDVI) sebaliknya.
* Panel bacaan menyenaraikan koordinat piksel, saiz bingkai, format piksel, kedalaman bit, dan jadual saluran (Saluran / Nilai / %) dengan label jalur dan panjang gelombang; pasangan hijau Bayer dipuratakan; sampel kawasan menunjukkan "N px avg".

Keadaan meter tumpuan adalah untuk sesi sahaja.

<!-- SCREENSHOT-NEEDED: Spot Meter in use — reticle placed on the live feed, readout panel showing the per-channel value table with band wavelength labels. -->

### Pendedahan Automatik Peramalan (dikawal oleh DLS)

Bahagian ini hanya akan muncul apabila **sekurang-kurangnya satu penderia cahaya DAQ disambungkan** — penyelesai memerlukan spektrum sinaran ke bawah secara langsung untuk menggerakkannya.

<!-- SCREENSHOT-NEEDED: Predictive Auto-Exposure (DLS-driven) section with a DAQ connected — Enable checkbox, Smoothing (α) slider at 0.30, and the "Recalibrate ρ" button. -->



| Kawalan | Julat | Lalai | Fungsinya |
| --- | --- | --- | --- |
| **Aktifkan** | Petak semak | Hidup (kamera berdiri sendiri) | Penyelesai bentuk tertutup menggunakan spektrum DLS ditambah skalar pek kalibrasi kamera untuk meletakkan jalur paling cerah hampir tepu sambil mengekalkan jalur paling gelap di atas paras lantai SNR — satu penulisan pendedahan tunggal bagi setiap penyelesaian, tiada gelung penetapan. Direka untuk timelapse berkuasa solar di mana setiap rakaman mesti didedahkan dengan betul. Backend secara senyap bertukar kepada AE reaktif apabila bacaan DLS lapuk/tiada atau pek kalibrasi tidak dimuatkan. |
| **Pelicinan (α)** | 0.05–1.0, langkah 0.05 | 0.3 | Pelicinan penyelesaian ramalan berturut-turut (lebih rendah = lebih licin). |
| **Refleksan adegan**| Butang**Kalibrasi semula ρ** | — | Menganggarkan semula faktor reflektansi adegan yang digunakan oleh penyelesai. |

{% hint style="info" %}
**Sambungan tatasusunan mematikan AE ramalan secara lalai** — untuk tatasusunan, AE pintar Chloros dan pendedahan automatik di pihak kamera mengendalikan pendedahan (dengan perlindungan jenuh) dan satu adegan AE ramalan-anggaran reflektansi tidak selamat merentasi adegan campuran. Anda boleh mengaktifkannya semula bagi setiap kamera di sini jika anda secara khusus mahukan pendedahan radiometrik yang dikendalikan oleh DLS.
{% endhint %}

**Had pendedahan yang dikendalikan oleh DAQ dan AE berpaut pada insiden.** Bebas daripada kotak semak di atas, apabila penderia cahaya DAQ ditugaskan kepada kamera RGB, Chloros mengira — daripada sinaran menuruni mutlak yang diukur — pendedahan×penguatan maksimum di mana permukaan 100 %-reflektan kekal di bawah pemotongan, dan menerapkannya sebagai **bumbung* pada pendedahan automatik. Sejak versi 1.3.0, bumbung ini hanya terpakai semasa sasaran penentukuran berada dalam pandangan kamera tersebut (dilihat dalam bingkai dalam tempoh 10 saat terakhir); Apabila tiada sasaran dalam pandangan, auto-dedah biasa kamera, dengan perlindungan sorotan tersendiri, akan dijalankan sebaliknya, supaya bahagian lain dalam adegan tidak menjadi lebih gelap daripada yang diperlukan. Semasa had aktif, kamera **terpasak pada insiden**: ia dijalankan secara gelung terbuka pada pendedahan yang diukur oleh meter insiden dengan penguatan pada 0 dB — pendedahan mengikut cahaya yang diukur, bukan kandungan pemandangan. Kerana had ini hanya boleh memendekkan pendedahan, ia sendiri tidak boleh menyebabkan pemotongan (clipping). Sejak 1.3.0, had ini dikira menggunakan pemalar pendedahan kamera sendiri daripada pek penentukuran (nilai purata armada sahaja apabila pek tidak mempunyai satu). Had ini akan terhenti secara automatik — dan AE biasa adegan akan disambung semula — setiap kali tiada sasaran penentukuran dalam pandangan, bacaan DAQ hilang, lapuk (>30 s), atau gelap, atau jika ≥15 % bingkai terpotong pada pendedahan yang ditetapkan (bermakna sensor dan kamera melihat pencahayaan yang berbeza). Tiada suis GUI; ini adalah tingkah laku standard setiap kali kamera RGB mempunyai DAQ yang terikat.

### Pengambilankon & Picu

Ahli-ahli<!-- SCREENSHOT-NEEDED: Acquisition & Trigger section on a standalone camera — Trigger Mode, Trigger Source, and the Frame Rate row in Auto mode showing live fps; ideally a second capture on an array member showing the read-only Role/Sync Line/Peers rows. -->

array juga memaparkan baris **Peranan**(Hanya Baca: Utama dalam biru / Hamba dalam hijau),**Garisan Sinkronisasi**, dan**Rakan Sebaya**.

| Kawalan | Pilihan | Lalai | Nota |
| --- | --- | --- | --- |
| **Mod Picu** | Padam / Hidup | Hidup | Dilumpuhkan untuk ahli tatasusunan (tatasusunan menguruskan pemicuan). |
| **Sumber Picu** | Perisian / Line0 (M8) / Line1 / Line2 | Line0 | Disembunyikan semasa Mod Picu dimatikan; dilumpuhkan untuk ahli tatasusunan. Line0 ialah input picu luaran M8 yang diasingkan optikal. |
| **Kadar Bingkai**| Auto / Manual + nilai | Auto |**Auto**: had kadar bingkai kamera dimatikan — pendedahan menentukan fps, dan kotak menunjukkan kadar sebenar secara langsung.**Manual**: anda mengehadkan fps dengan gelangsar (1 hingga ke had maksimum yang terhad oleh jalur lebar), berasaskan kadar sebenar semasa. Ahli susunan melihat "N fps (langsung)" secara baca sahaja dengan "Atur dalam tetapan susunan". |

### Rangkaian / Pengangkutan

| Baris | Kelakuan |
| --- | --- |
| **Saiz Pakej**| 1500 (Standard) / 9000 (Jumbo) — lalai**Jumbo**. |
| **Kadar Aliran** | Had kadar aliran pautan yang hanya boleh dibaca dalam MB/s. Backend menyimbangkan semula ini merentasi semua kamera yang disambungkan pada setiap sambungan/putus sambung. |
| **Pengendalian Buffer** | Mod pengendalian buffer yang hanya boleh dibaca. |

### Rakaman

Panel ini diakhiri dengan butang **"Buka Tetapan Rakaman…"** yang akan membawa ke [Panel Tetapan Rakaman](capture.md#the-capture-settings-pane) (dimatikan sehingga projek dibuka — "Buat atau buka projek untuk menyimpan rakaman"). Jika kamera disembunyikan atau dihentikan, petunjuk akan mengingatkan anda untuk memaparkan semula/menyesuaikan semula sebelum merakam.

## Panel tetapan susunan

Buka dengan **gear**pada baris ARRAY. Header: nama array dengan pensel untuk menamakan semula, dan**×** untuk tutup. Bahagian di bawah yang ditandakan *hanya gabungan* hanya muncul untuk array yang disambungkan dalam mod paparan gabungan.

<!-- SCREENSHOT-NEEDED: array settings pane, top portion — array name header, Sync section (Master/Slaves/Sync Line), and Ambient Light Sensor section with the Light Sensor dropdown and the green "Active — all cameras in the array are illumination-corrected" status line. -->



### Sinkronkan

Baris **Master**,**Slaves**, dan**Sync Line** hanya untuk bacaan.

### Penderia Cahaya Sekeliling

Ditunjukkan untuk kedua-dua susunan gabungan dan berasingan:

* Senarai lungsur **Penderia Cahaya** — mengikat satu DAQ kepada keseluruhan susunan. Pilihan kekal serta-merta, menyebar ke menu lungsur Light Sensor pada setiap kamera ahli (anda masih boleh menimpa mengikut kamera), dan mula menghantar spektra ke susunan.
* Baris **Status** Langsung: Mati · "Menunggu spektra pertama…" · "Aktif — semua kamera dalam susunan telah diperbetulkan pencahayaannya" · "Tiada spektrum baharu dalam 3 saat terakhir — masih menggunakan bacaan terakhir (tiada tamat masa lama)…".
* Nota dalam panel: "Pembetulan radiometrik merangkumi keseluruhan susunan. Tetapan bagi setiap kamera akan diutamakan."

### Sasaran Kalibrasi

Kumpulan tersendiri sejurus selepas Penderia Cahaya Sekitar, untuk susunan gabungan dan berasingan:

* Kotak semak **Aktifkan** — "Mengesan sasaran ArUco MAPIR dan mengesahkan NDVI berbanding LUT pantulan panel"; menggerakkan jadual lapisan sasaran dan pengesahan jubin gabungan. Pada susunan **asing** ia mengaktifkan [Jadual Sasaran Kalibrasi] tersendiri bagi setiap ahlinya(#the-calibration-target-table) (satu bagi setiap baris kamera dalam paparan senarai).

### Rakaman — tetapan penderia seragam

Ditunjukkan untuk kedua-dua susunan gabungan dan berasingan (tajuk kumpulan dibaca "Rakaman (seragam merentas kamera)"). Tetapan ini digunakan secara seragam untuk setiap ahli (perubahan bagi setiap ahli akan mengganggu penyelarasan). Suntingan disediakan secara berperingkat dan digunakan serentak dengan **Terapkan**— kecuali suis**ROI Hidup / ROI Padam**, yang digunakan serta-merta.

<!-- SCREENSHOT-NEEDED: array settings Capture section (1.3.0) — Pixel Format, Binning, Resolution preset, the ROI crop row with the "ROI on" toggle, the "Select" button, the "max WxH" hint and Reset button, the W/H/X/Y fields, the full-sensor ROI map below them with the orange crop box and its "W×H @ X,Y" label above the box, Trigger Rate row in Auto showing the derived fps, and the Apply/Cancel buttons. -->

| Kawalan | Pilihan / julat | Fungsinya |
| --- | --- | --- |
| **Format Pixel** | 8-bit / 10-bit / 12-bit / 16-bit | Kedalaman bit seragam untuk semua ahli; setiap kamera menggunakan format kedalaman tersebut untuk sensornya sendiri. |
| **Pembingunan** | 1x1 / 2x2 / 4x4 | Penggabungan perkakasan — mengekalkan keseluruhan bidang pandangan sambil meningkatkan SNR dan kadar bingkai. Menukar tetapan ini akan menetapkan semula medan ROI kepada medan pandangan penuh yang baru. |
| **Tetapan pratetap resolusi** | Penuh / Separuh / Suku | Berkaitan dengan binning; mengisi medan ROI dengan potongan berpusat. |
| **Potongan ROI (px)**| butang togol**ROI Hidup / ROI Padam**, butang**Pilih**, medan L / T / X / Y, peta sensor penuh | Potongan sensor — lihat [Penyunting ROI](#the-roi-editor). Lebar/tinggi diselaraskan kepada gandaan 16 (minimum 64); offset diselaraskan kepada gandaan 4. Petunjuk "max WxH" memaparkan had maksimum dan**Reset** memulangkan medan kepada seluruh medan pandangan. Semasa menyunting, kotak potong oren menjejaki setiap perubahan medan pada peta dan pada jubin tatasusunan. |
| **Kadar Picu**| Togol Auto / Manual + fps 0.5–10, langkah 0.5 |**Auto**(laluan lalai): backend mengira kadar pencetus daripada resolusi dan jalur lebar — input dilumpuhkan dan memaparkan nilai yang dikira.**Manual**: menetapkan nilai anda pada Terapkan. Kadar yang ditetapkan yang tidak dapat disokong oleh saiz bingkai baru akan dikekalkan pada kadar terpantas yang dibenarkan oleh saiz tersebut, dan akan kembali apabila saiz bingkai membenarkannya semula. |

Nota dalam panel: "Perubahan format/rezolusi memulakan semula semua kamera seketika. Kadar pencetus digunakan secara langsung." Butang **Terpakai / Batal** terletak di bahagian bawah panel.

#### Penyunting ROI

Baharu dalam 1.3.0. Baris pemangkasan ROI dan peta di bawahnya berfungsi bersama:

* **ROI Hidup / ROI Padam**— menghidupkan atau mematikan pemangkasan sensor**segera**(kamera akan dihidupkan semula seketika; jubin susunan akan memaparkan "Mengekodkan tetapan…").**Padam**membaca keseluruhan sensor dan**mengingat potongan anda**— medan tersebut terus memaparkannya;**Hidup** menerapkannya semula. Menghidupkan ROI tanpa menetapkan potongan akan menerapkan potongan separuh saiz yang berpusat ("Tiada potongan ditetapkan, jadi potongan separuh saiz yang berpusat telah diterapkan. Laras menggunakan medan.").
* **Pilih**— melakar kotak potongan pada peta: seret merentasinya, atau klik dua sudut bertentangan (Esc membatalkan). Petak empat segi itu akan muncul dalam medan Lebar/Tinggi/Panjang/Lebar Latitud/Panjang Latitud, disesuaikan dengan langkah di atas; tiada apa-apa yang diterapkan sehingga anda menekan**Terapkan**, jadi anda boleh melarasnya dengan teliti terlebih dahulu menggunakan anak panah medan. Satu segi empat sama bagi setiap kali menekan Pilih.
* **Peta**menunjukkan kawasan potongan pada keseluruhan sensor, jadi anda boleh meluaskannya semula: "Sensor penuh, langsung" sementara kamera membaca keseluruhan sensor, atau "Snit penuh bingkai dari {masa}. Kamera hanya membaca potongan." semasa ia dipotong. Jika tatasusunan hanya pernah disalurkan dalam bentuk potongan, peta akan membaca "Tiada paparan bingkai penuh lagi: tatasusunan ini hanya pernah disalurkan dalam bentuk potongan." dengan butang**Tunjukkan bingkai penuh**, yang mematikan ROI. Label `W×H @ X,Y` bagi potongan itu terletak di luar kotak (di atasnya, atau di bawah apabila kotak menyentuh bahagian atas), pada peta dan pada jubin langsung.
* **Terpakai**menetapkan medan: pemotongan menghidupkan ROI; bingkai penuh (selepas**Reset**) menamatkannya dengan pemotongan sebelumnya masih diingati; ROI yang dimatikan dan medannya masih menunjukkan pemotongan yang diingati kekal dimatikan apabila anda menerapkan perubahan lain (format, binning, kadar).
* **Pada susunan yang diselaraskan, tetingkap potongan setiap ahli diletakkan oleh penyelarasan**, jadi setiap kamera membingkai bahagian yang sama dalam pemandangan. Susunan yang dipotong sebelum ia pernah diselaraskan akan diselaraskan pada sensor penuh terlebih dahulu (potongan diangkat, penyelarasan diselesaikan, dan potongan dipulihkan dengan tetingkap bagi setiap kamera).
* Selepas perubahan crop, pendedahan automatik akan menyamakan semula untuk bingkai baru. Di bawah crop, pendedahan ahli masih boleh berjalan sehingga tempoh pencetus.
* Status ROI hidup/mati dan potongan yang diingati disimpan bersama projek.

### Penjajaran (penjajaran bersama)

Ditunjukkan untuk **kedua-dua susunan gabungan dan berasingan** (1.2.0 hanya menunjukkannya untuk susunan gabungan); tajuk kumpulan tertulis "Penjajaran".

<!-- SCREENSHOT-NEEDED: array settings Alignment section after a successful calibration — green "RMS x.xx px" residual pill, "✓ All cameras aligned (N)" summary, the per-camera table with px error / match count / NCC columns, the Recalibrate alignment button and the "Auto-expose cameras for alignment" checkbox. -->



* pil **Residu**: "RMS x.xx px" — hijau jika di bawah 1 px, jingga jika di bawah 3 px, merah jika tidak atau jika mana-mana kamera gagal; "tiada profil" sebelum penyelesaian pertama.
* Baris ringkasan: "✓ Semua kamera diselaraskan (N)" / "⚠ p/N kamera diselaraskan — <serial (filter)="">gagal" / "Potongan aktif — Kalibrasi semula untuk menyelaraskan (menggunakan sensor penuh)" / "Menunggu pendedahan stabil…" / "Memohon tetapan…" (kamera sedang dihidupkan semula) / "Susunan berhenti menghantar bingkai — sambungkan semula susunan".
* Jadual bagi setiap kamera: kamera (4 digit terakhir siri + penapis), ralat reprojeksi dalam px dengan bilangan padanan ("ref" untuk kamera induk), dan skor korelasi silang normalisasi tumpang tindih berbanding ambang lulus 0.35.
* **Kalibrasi semula penjajaran** butang (tertulis "Kalibrasi penjajaran" sebelum profil pertama) — menjalankan semula pendaftaran bersama pada bingkai baharu.
* Kotak semak **"Pendedahan automatik kamera untuk penjajaran"** (diperiksa secara lalai) — mencerahkan sementara kamera yang gelap atau rata (pendedahan dahulu, kemudian penguatan) supaya ia mempunyai tekstur untuk dipadankan, kemudian memulihkan AE.

Chloros menyelaras setiap susunan secara automatik selepas disambungkan, dalam kedua-dua mod paparan, sebaik sahaja pendedahan automatik ahli-ahli stabil (ia terus mencuba sehingga 5 minit); pratonton gabungan juga menyelaras semasa dibuka. Susunan berasingan tiada jubin komposit, jadi bahagian ini memeriksa status penjajaran dan menjalankan Kalibrasi Semula dengan sendirinya. Kalibrasi Semula jika fokus atau kedalaman pemandangan berubah. Penjajaran adalah **hanya untuk sesi mengikut reka bentuk** — ia tidak pernah disimpan ke dalam profil, kerana ia bergantung pada jarak pemandangan pada masa itu. Rakaman masih boleh dieksport berdaftar piksel (lihat [Eksport Teralignment](capture.md#per-array-controls)).

### Vignet Pintar

* Kotak semak **Dayakan pembetulan**— menerapkan anggaran vignet bagi setiap kamera ke dalam rantaian radiometrik (siaran langsung**dan** eksport).
* **Kalibrasi daripada paparan semasa**— tujukan susunan ke sasaran seragam (panel rata, dinding, atau langit) terlebih dahulu; setiap kamera diratakan secara individu dan status melaporkan "n/N kamera · −x.x %" peningkatan keteratan.**Kosongkan** membuang anggaran.
* Status: "Dikalibrasi", "Tidak dikalibrasi", atau "Dikalibrasi sebahagiannya — jalankan semula" apabila anggaran bagi beberapa ahli sahaja masih terpakai pada bingkai semasa mereka (contohnya selepas Kalibrasi Semula memindahkan tetingkap potongan seorang ahli) — jalankan Kalibrasi semula.
* Laraskan dengan tepat bagi setiap kamera menggunakan gelangsar **Vignette** khusus kamera dalam [Praperiksaan Langsung](#live-preview).

### Pratonton Langsung

* Kotak semak **Histogram**+ gelangsar lebar (0.10–0.90 bingkai) — jalur histogram pada aliran untuk setiap jubin ahli sekaligus (termasuk jubin saluran terpisah), bagi setiap kamera [Tindanan Histogram](#display-overlays-drawn-over-the-live-feed) ditetapkan untuk keseluruhan susunan. Ia akan dipaparkan sebagai diaktifkan apabila setiap ahli sudah memilikinya. Pada susunan**berasingan**, ini adalah satu-satunya baris dalam kumpulan.
* *Gabungan sahaja:* **Indeks**: kotak semak + gear — membuka [Kalkulator Indeks](#index-calculator-pane) yang dikongsi dengan jalur yang dilukis daripada**semua** kamera ahli. Satu baris pratonton ekspresi di bawahnya memaparkan ekspresi semasa ("Tiada ekspresi ditetapkan — buka kalkulator untuk mencipta satu"), dikemas kini setiap saat.
* *Hanya gabungan:* menu lungsur **Resolusi Rendering**(preset yang sama seperti setiap kamera, lalai 720p): ketinggian aliran tontonan langsung**dan** saiz eksport komposit yang disimpan. Nota dalam panel: "Praperintian + saiz komposit yang disimpan. Imej setiap kamera sentiasa dieksport pada resolusi penuh."

### Lapisan Paparan *(gabungan sahaja)** Kotak semak **Aktifkan** (laluan lalai — kamera induk dipaparkan terus; aktif = komposit berlapis).
* **Senarai lungsur**Latar Hadapan**/**Latar Belakang**: setiap kamera ahli (mengikut nama) atau**Indeks**. Apabila Latar Hadapan diatur kepada Indeks, piksel di luar LUT Min/Max akan memaparkan lapisan Latar Belakang.

### Paparan Berpisah *(gabungan sahaja)*

**"Tunjukkan kamera ahli"**— butang**Belah / Sembunyikan kamera ahli** yang menambah siaran langsung setiap ahli sebagai jubin grid berasingan di sebelah komposit. Jubin tersebut membaca penimbal bingkai tatasusunan yang sedia ada (tiada sambungan kamera tambahan). Papan pemuka grid sahaja; disimpan bagi setiap tatasusunan bersama projek.

### Keupayaan

Panel baca-sahaja yang dikemas kini setiap 5 saat:

* **Label lapisan**: "Rakaman serentak" (hijau) · "Rakaman serentak (pancaran selang-seli FTD)" (hijau) · "Rakaman selang-seli (luncur 100 ms)" (kuning jingga) · "Konfigurasi terlalu besar" (merah).
* **Kesihatan bingkai**: "x.xx % belum selesai" — hijau di bawah 1 %, jingga di bawah 5 %, merah pada 5 % atau lebih.
* **Baris pautan**: "NIC {mbps} Mbps - kekal {MB/s} MB/s".

Ini adalah bajet jalur lebar masa nyata bagi susunan. Untuk model fps dan rangkaian yang mendasari — dan apa yang perlu diubah apabila tahap menjadi jingga atau merah — lihat [Susunan Kamera Pelbagai](arrays.md) dan [Rujukan CLI](../reference/cli-reference.md).

<!-- SCREENSHOT-NEEDED: array settings Capabilities panel showing a green "Simultaneous capture" tier, the frame-health percentage, and the NIC/sustained-throughput line. -->

## Panel Pengira Indeks

Halaman sidebar ketiga, dikongsi oleh gear Indeks bagi setiap kamera dan gear Indeks bagi susunan gabungan (satu pada satu masa — tajuknya ialah "Pengira Indeks — <camera name="">" atau "Pengira Indeks — <array name="">"). Ia menerima senarai jalur (penapis kamera- jalur semula jadi, atau semua jalur merentasi ahli-ahli susunan), ungkapan semasa, dan konfigurasi LUT (hidup/mati, tahap — lalai 7-henti, min — lalai 0.2, maks — lalai 1), ditambah histogram indeks secara langsung. **Terapkan** menetapkan ungkapan tersebut; Perubahan LUT diterapkan secara langsung pada pratonton.

<!-- SCREENSHOT-NEEDED: Index Calculator pane open for a combined array — band buttons for all member cameras, an NDVI-style expression in the editor, LUT controls, and the live index histogram. -->

## Tetapan per-kamera vs tetapan yang diuruskan oleh tatasusunan

Rujukan pantas untuk apa yang wujud di mana apabila kamera adalah ahli tatasusunan:

| Yang diuruskan oleh tatasusunan (baca sahaja dalam panel kamera) | Masih per-kamera di dalam tatasusunan |
| --- | --- |
| Format piksel, resolusi, binning | Pendedahan automatik (pendedahan, penguatan, sasaran, pelicinan, ROI) |
| Mod/sumber pencetus, kadar bingkai | Pengurangan hingar, ketajaman, vignette |
| | Orientasi (cermin/pusingan), lapisan paparan (tetapan larian → Pratonton Langsung menetapkan histogram pada setiap ahli sekaligus), meter tumpuan |
| | Indeks (larian paparan berasingan), pengikatan penderia cahaya |

Perilaku merentas lain:

* **Paparan gabungan vs berasingan** dipilih semasa menyambungkan susunan: gabungan = satu jubin komposit yang diselaraskan (sumber ahli hanya melalui Paparan Berpisah); berasingan = setiap ahli memaparkan jubin tersendiri yang diselaraskan. Kamera tidak pernah memaparkan kedua-dua sumber berdiri sendiri dan jubin susunan.
* **Sambung semula automatik**: membuka projek yang disimpan akan memulihkan kameranya dan susunannya serta menerapkan semula setiap tetapan yang disimpan ke backend sebelum penstriman disambung semula.
* **Pintu tangkapan**: kamera yang tersembunyi atau terhenti dikecualikan daripada Tangkapan Semua; satu susunan hanya dihalang sepenuhnya apabila SEMUA ahlinya tersembunyi/terhenti. Lihat [Tetapan & Mod Rakaman](capture.md).

## Bagaimana tetapan kekal

Keadaan tab kamera disimpan **bersama projek**, bukan dalam pelayar:

* Setiap perubahan reaktif mengambil snapshot kamera dan array ke dalam `cameras.json` projek (dibounce selama 500 ms). Ini merangkumi nama dan warna kamera, tetapan pendedahan/penguatan/AE, format piksel/rezolusi/binning, kadar pencetus, tetapan pratonton (rezolusi render, penyingkiran hingar, ketajaman, vignette, profil warna, Sorotan, ketepuan/kontras), orientasi, lapisan superimposisi, pembahagian saluran, konfigurasi indeks, tetapan AE ramalan, ROI AE, nama tatasusunan, mod paparan, tetapan tangkapan tatasusunan (termasuk potongan ROI, sama ada ROI dihidupkan, dan potongan yang diingati semasa ia dimatikan), Belanjawan Wayar tatasusunan, dan blok grid (zoom suapan, mod paparan, kunci grid, susunan jubin manual, kamera tersembunyi, jubin tertutup, kamera aktif).
* Pengikatan sensor cahaya disimpan dalam `sensors.json` projek tersebut.
* Membuka semula projek menyambung semula perkakasan dan menerapkannya semula sepenuhnya.
* **Tiada projek dibuka = hanya sesi sahaja**: tanpa projek, tiada apa-apa yang kekal selepas menutup Chloros.
* Hanya sesi tanpa mengira projek: keadaan rehat, sampel meter tumpuan, kotak semak Sasaran Kalibrasi bagi setiap kamera (sentiasa dibuka), dan profil penjajaran matriks (direka semula setiap sesi).
* Satu pengecualian: pilihan eksport **Tetapan Rakaman** dan mod rakaman kekal bagi setiap projek dalam storan aplikasi tempatan dan bukannya `cameras.json` — lihat [Tetapan & Mod Rakaman](capture.md).</array></camera></serial>
