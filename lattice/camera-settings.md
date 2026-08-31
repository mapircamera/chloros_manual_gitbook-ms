# Tetapan Kamera

Tab **Kamera** adalah permukaan kawalan langsungChloros

untuk kamera LATTICE: satu kawasan siaran utama yang memaparkan setiap kamera yang disambungkan sebagai jubin langsung, dan sebuah bar sisi yang meluncur antara tiga halaman — **senarai kamera**, sebuah**panel tetapan**(per-kamera, susunan, atau tetapan tangkapan — satu pada satu masa), dan**Kalkulator Indeks**. Halaman ini mendokumenkan setiap kawalan dalam senarai kamera, panel tetapan per-kamera, dan panel tetapan susunan. Mod tangkapan, pemilihan jenis eksport, dan aliran Capture All terdapat pada halaman berkaitan [Capture Settings &amp; Modes](capture.md).

Tab Kamera akan muncul di bar sisi setelah backendChloros

sedia. Semua kawalan di bawah berkomunikasi dengan backend tempatan melalui `127.0.0.1:5000`; perubahan akan digunakan pada kamera langsung serta-merta melainkan dinyatakan sebaliknya.

## Jenis kamera yang digunakan di halaman ini

Kawalan akan memaparkan atau menyembunyikan dirinya berdasarkan jenis kamera yang dipilih. Manual ini menggunakan istilah-istilah ini di merata tempat:

| Istilah | Maksud | Menapis saluran |
| --- | --- | --- |
| **KameraRGB** | LATTICE M3C dengan penapis FRGB (model mengandungi `-FRGB`) |Red

/Green

/Blue

|
| **Bayer multispektral** | LATTICE M3C dengan FRGN, FOCN, atau FNGB | FRGN:Red

/Green

/NIR

· FOCN:Orange

/Cyan

/NIR

· FNGB:NIR

/Green

/Blue

|
| **Mono (M3M)** | LATTICE M3M — satu penapis jalur sempit, satu jalur yang dikalibrasi | Jalur tunggal |
| **Ahli susunan** | Kamera yang disambungkan sebagai sebahagian daripada susunan bersepadu (paparan gabungan atau berasingan) | Mengikut penapisnya |

KameraRGB

mendapat pemprosesan fotometrik (imbangan putih, profil warna, gamma); kamera multispektral dan mono mendapat rantaian radiometrik dan melangkau kawalan fotometrik. Ahli susunan menyerahkan tetapan peringkat aliran (format piksel, resolusi, binning, pencetus, kadar bingkai) ke susunan — baris-baris tersebut menjadi hanya-baca dalam panel per-kamera dan sebaliknya dipindahkan ke panel tetapan susunan.

## Kawasan suapan utama



<!-- SCREENSHOT-NEEDED: Cameras tab with 2+ cameras connected in grid view — live tiles visible with name and fps overlays, sidebar camera list open on the right. -->

Apabila tiada kamera disambungkan, kawasan suapan memaparkan paparan **&quot;Sambungkan kamera untuk mula&quot;**dengan dua butang:**Sambungkan Kamera**(hijau, membuka dialog sambungan satu kamera) dan**Sambung Susunan** (biru, membuka dialog sambungan susunan). Dialog sambungan itu sendiri didokumenkan dalam [Menyambungkan Kamera](connecting.md); konsep susunan (penyegerakan, peringkat, jalur lebar) dalam [Susunan Pelbagai Kamera](arrays.md). Apabila anda membuka projek yang disimpan yang mengandungi kamera, skrin pembukaannya sebaliknya akan memaparkan pemutar dengan &quot;Membuka semula N kamera yang disimpan…&quot; manakalaChloros

memulihkan aliran daripada sesi terakhir.

<!-- SCREENSHOT-NEEDED: Cameras tab empty state — the "Connect a camera to get started" splash with the green Connect Camera and blue Connect Array buttons. -->

### Bar atas

| Kawalan | Fungsinya |
| --- | --- |
| **Togol mod paparan**| Menukar antara**paparan grid**(semua jubin sebagai sel) dan**paparan senarai** (susunan penuh lebar di atas, SATU kamera aktif di bawah). Petua alat: &quot;Tukar ke paparan grid&quot; / &quot;Tukar ke paparan senarai&quot;. |
| **Kunci grid**(gembok) | Lalai**dikunci** — jubin dibekukan di tempat. Buka kuncinya untuk menyusun semula jubin ke dalam mana-mana slot dengan menyeret (jarak dikekalkan). Grid akan dikunci semula secara automatik setiap kali kamera baru disambungkan. Petua alat: &quot;Buka kunci grid (aktifkan seret jubin)&quot; / &quot;Kunci grid (bekukan jubin di tempat)&quot;. |
| Pemalam **Feed Zoom** | Saiz jubin, dari 60 px sehingga ke lebar bekas penuh. Sel mengekalkan nisbah aspek 4:3. Pada lebar sel di bawah 200 px, lapisan nama dan fps akan tersembunyi untuk memastikan jubin kelihatan kemas. |

### Jubin Suapan

Setiap kamera memaparkan jubin langsung gabungan; sebuah kamera juga boleh memaparkan tiga jubin **pecahan per-saluran** skala kelabu (lihat [Pecahan Saluran](#overlay-dipaparkan-di-atas-siaran-langsung)), dan susunan memaparkan jubin gabungan. Jubin aktif mempunyai cincin pilihan dalam warna kamera (atau susunan).

Apabila meletakkan penunjuk ke atas jubin, butang tutup **X** akan muncul:

* Menutup jubin **komposit** sementara split saluran ia hanya menyembunyikan komposit tersebut.
* Menutup **jubin terakhir yang kelihatan bagi kamera berdiri sendiri** memutuskan sambungan kamera tersebut.
* Jubin pecahan ahli susunan gabungan tidak pernah memutuskan sambungan kamera — ia hanya menyembunyikannya.

Apabila grid tidak dikunci, seret mana-mana jubin ke mana-mana slot; susun atur akan disimpan bersama projek.

## Bar sisi — senarai

<!-- SCREENSHOT-NEEDED: sidebar camera list pane showing a standalone camera row and an ARRAY group with indented member rows, the DAQ on/off pill visible on the array row, plus the Connect Camera / Connect Array / Capture All buttons at the top. -->

kamera Halaman bar sisi pertama menyenaraikan setiap kamera dan susunan yang disambungkan:

* **Sambungkan Kamera**(hijau) /**Sambungkan Susunan** (biru, memaparkan &quot;Mengesani...&quot; semasa mengimbas). Kedua-duanya dilumpuhkan semasa dialog sambungan dibuka.
* **Rakam Semua** (merah) — merakam setiap kamera yang disenaraikan dengan jenis eksport yang dipilih dalam Tetapan Rakaman. Memerlukan projek yang terbuka. Didokumenkan sepenuhnya dalam [Tetapan &amp; Mod Rakaman](capture.md).
* **Gear Tetapan Rakaman** (di sebelah Rakam Semua) — membuka [pane Tetapan Rakaman](capture.md#the-capture-settings-pane). Dilumpuhkan tanpa projek atau semasa merakam.

### Baris kamera

Setiap baris kamera memaparkan sempadan berkod warna (warna tersuai kamera), label &quot;CAM&quot; — dengan **M**biru (utama) atau huruf peranan**S** hijau (hamba) untuk ahli tatasusunan — dan nama paparan. Nama lalai ialah `LATTICE-MODEL (serial)`; tukar namanya dari tetingkap tetapan setiap kamera. Butang baris:

| Butang | Kesan |
| --- | --- |
| **Mata**| Tukar paparan. Kamera yang disembunyikan akan keluar dari grid dan**dikecualikan daripada Rakam Semua**. |
| **Gear** | Buka tetingkap tetapan bagi setiap kamera (seksyen seterusnya). |
| **Jeda / Main**| Bekukan pratonton langsung**hanya pada sisi paparan** — rakaman di belakang terus berjalan. Kamera yang ditangguhkan tidak dapat merakam. |
| **X** | Putuskan sambungan. UI dikemas kini serta-merta (optimistik); pemutusan sambungan di belakang boleh mengambil masa 10–30 saat untuk diselesaikan. |

### Baris tatasusunan

Satu baris tatasusunan memaparkan lencana &quot;ARRAY&quot; dalam warna tatasusunan, nama tatasusunan (boleh dinamakan semula dalam tetapan tatasusunan), dan pil **DAQ · on/off**—**hidup** apabila Pengesan Cahaya di peringkat tatasusunan ditetapkan *atau* mana-mana ahli mempunyai penderia bagi setiap kamera; petunjuk alatnya menyenaraikan dengan tepat penderia mana yang menyalurkan kepada apa. Kamera ahli disenaraikan di bawah dengan indentasi dalam baris mereka sendiri. Butang baris array: **mata**(menyembunyikan/menunjukkan SEMUA ahli sekaligus),**gear**(panel tetapan array),**X**(memutuskan sambungan keseluruhan array).

Status penderia cahaya (DLS) yang digunakan dalam baris susunan dan tetingkap tetapan susunan mempunyai empat keadaan:**padam**,**menunggu**(tiada spektrum lagi),**aktif**(spektrum tiba dalam 3 saat terakhir), dan**lapuk** — tiada spektrum baru dalam 3 saat, tetapi bacaan terakhir *masih digunakan* (bacaan DAQ tidak pernah tamat tempoh di laluan tangkapan).

Anda boleh menyeret kamera berdiri sendiri dan keseluruhan kumpulan array melepasi satu sama lain di bar sisi untuk menyusun semula senarai; ahli array tidak boleh diseret secara berasingan.

## Panel tetapan bagi setiap kamera

Buka dengan **ikon gear** pada baris kamera. Panel akan meluncur di atas senarai kamera.

<!-- SCREENSHOT-NEEDED: per-camera settings pane, top portion — header with color swatch, camera name, rename pencil and close X; live histogram with the orange dashed AE-target line and green mean-luma line; the RGB per-band toggle button visible top-right of the histogram. -->



**Header**:**petak warna**kamera (klik untuk membuka pemilih warna terbina dalam — menetapkan warna sempadan bar sisi dan cincin pilihan jubin),**nama**dengan butang pensel**Namakan Semula**(menyimpan nama kosong akan kembali ke lalai `MODEL (serial)`), dan**×** untuk tutup.

### Histogram langsung

Di bahagian atas tetingkap terdapat histogram luma langsung yang dikira daripada pratonton **JPEG** pada kira-kira 8 Hz. Puratanya diberi pemberat Bayer — (R+2G+B)/4 — untuk menepati metrik AE kamera itu sendiri.

* **garis putus-putusOrange**= sasaran AE. **Seret secara mendatar untuk menyasarkan semula** — satu arahan dihantar apabila dilepaskan, dan menyeret akan menukar mod sasaran AE kepada Manual.
* **garis pepenuhGreen** = luma min sebenar (apa yang AE berikan pada masa ini).
* **butangRGB** (atas-kanan): menyah/menghidupkan histogram superimposisi setiap jalur yang berwarna mengikut penapis kamera (contohnya pada FRGN: kelabuNIR

, hijau, merah). Pada kamera mono (M3M), butang akan tertera &quot;MONO&quot; dan dilumpuhkan — mono sentiasa memaparkan histogram luma jalur tunggal.
* Label paksi-X mengikut kedalaman bit sensor format piksel semasa: 0..255, 0..1023, 0..4095, atau 0..65535.

### Baris

<!-- SCREENSHOT-NEEDED: per-camera settings info rows — Model, Radiometric Calibration "Active" badge with the tier/sha/date caption, Calibration Report Download button, Serial, Firmware row showing the "Up to date" state, IP, Temperature readout, Calibration Target checkbox, Light Sensor dropdown. -->

maklumat kamera

| Baris | Kelakuan |
| --- | --- |
| **Model** | Baca sahaja (contohnya `LATT-M3C-L87-FRGN`). |
| **Kalibrasi Radiometrik**| Lencana**&quot;Aktif&quot;**dengan kapsyen yang memaparkan peringkat kalibrasi, hash, tarikh kalibrasi, dan senarai jalur, dimuat daripada pek kalibrasi kamera (lihat [Kalibrasi Radiometrik Kilang](https://mapir.gitbook.io/lattice-camera/calibration/factory-radiometric-calibration)).**Tersembunyi untuk kameraRGB

** — ia mempunyai penentukuran keseimbangan putih fotometrik, bukan radiasi setiap jalur. |
| **Laporan Penentukuran**| Butang**Muat Turun** — membuka PDF sijil penentukuran NIST setiap siri kamera dalam pemapar OS anda. Jika sijil belum disimpan dalam cache,Chloros

akan memaparkan petunjuk sebaliknya. |
| **Siri** | Hanya baca. |
| **Firmware**| Menunjukkan versi semasa, kemudian menentukan versi yang tersedia untuk model ini (disimpan dalam cache mengikut model — satu susunan N-kamera menyemak pelayan sekali sahaja). Keadaannya: &quot;Semak…&quot; → butang**&quot;Kemas kini ke X&quot;**→ &quot;Sedang mem-flash…&quot; → &quot;Dinaik taraf A → B&quot; / &quot;Gagal: …&quot; / &quot;Dilangkau: …&quot; / hijau**&quot;Terbaru&quot;**. Petua alat butang kemas kini: &quot;Seting kilang + flash + atur semula UserSet1. ~2–3 minit; jangan putuskan sambungan.&quot; |
| **IP** | Hanya baca. |
| **Suhu** | Hanya baca, dikemas kini setiap 3 saat. Bertukar kepada warna jingga pada ≥65 °C dan merah dengan ⚠ pada ≥75 °C. |
| Kotak semak **Sasaran Kalibrasi** | Mengaktifkan pengesanan sasaran pantulan ArUco dengan jadual pengesahanNDVI

bagi setiap panel di bawah siaran langsung (penglihatan senarai). Hanya untuk sesi — sentiasa dibuka semasa sesi sahaja. |
| **Senarai lungsur**Penderia Cahaya** | Mengikat penderia cahaya DAQ (DAQ-E/M/U, daripada senarai tab Penderia Cahaya) kepada kamera ini untuk pembetulan pencahayaan cahaya ke bawah (DLS) dan pendedahan automatik ramalan. &quot;Tiada&quot; membersihkan ikatan. Jika tiada penderia disambungkan, senarai lungsur akan memaparkan &quot;(tiada penderia disambungkan — buka tab DAQ)&quot;. Pengikatan disimpan bersama projek. |

### Pendedahan &amp; Penguatan

<!-- SCREENSHOT-NEEDED: per-camera Exposure & Gain section — Exposure (us) and Gain (dB) rows with Auto/Manual toggles, AE Target Brightness, AE Smoothing slider, AE Region of Interest row with the Aim button, and (on an array camera) AE Tune Speed and Highlight Protection rows. -->

Semua input berangka di sini menggunakan pemutar tahan untuk mempercepatkan: ketuk = ±1, tahan &gt;1.5 saat = ±10, tahan &gt;3 saat = ±100. Nilai dihantar ke kamera apabila anda melepaskan.

| Kawalan | Julat / pilihan | Lalai | Terpakai kepada | Fungsinya |
| --- | --- | --- | --- | --- |
| **Pendedahan (us)**| Min/maks langsung kamera | Auto | Semua | Masa pendedahan dalam mikrodetik, dengan suis**Auto/Manual**. Auto = pendedahan automatik berterusan di pihak kamera. |
| **Penguatan (dB)**| Ambang minimum/maksimum kamera secara langsung (contohnya sehingga 48 dB) | Manual (padam) | Semua | Penguatan analog/digital dengan suis**Otomatik/Manual** sendiri. |
| **Kecerahan Sasaran AE**| 0–255 | 80, mod**Auto**| Semua (boleh disunting apabila AE atau penguatan automatik diaktifkan) | Kecerahan yang disasarkan oleh AE. Dalam**Auto**(laluan lalai), pengawal latar belakang berasaskan histogram memilih sasaran itu sendiri, mengekalkan pendedahan pada 60–75% daripada maksimum penderia. Meny taip nilai atau menyeret garisan jingga histogram menukarnya kepada**Tangan**. |
| **Pelicinan AE** | 0.5–40, langkah 0.1 | 8.0 | Semua | Penyerapan hentakan AE. Petua alat: &quot;Lebih rendah = AE bertindak balas lebih pantas (boleh berdenyut pada fps tinggi). Lebih tinggi = lebih lancar / lebih perlahan.&quot; Nilai yang jauh di bawah lalai boleh menyebabkan AE berdenyut dan menstabilkan penstriman pada kadar bingkai tinggi; 8.0 adalah lalai yang stabil. |
| ****Wilayah Minat AE**| Kotak semak Aktif + butang**Aim**| Padam | Semua | Apabila diaktifkan, AE hanya mengukur wilayah garis putus-putus hijau dan bukannya keseluruhan bingkai.**Aim** mengaktifkan klik-untuk-letak pada siaran langsung: satu klik memusatkan wilayah pada 30 % bingkai; Seret-klik untuk melukis segi empat sama tersuai (minimum 5 % × 5 %). Sasaran akan mematikan dirinya sendiri selepas satu penempatan. Kawasan itu dipetakan semula ke koordinat asal kamera di bawah sebarang putaran/cerminan yang telah anda tetapkan, dan disimpan bersama projek. |
| **Kelajuan Larasan AE** | 0.1–5, langkah 0.1 | 1.0 | Ahli susunan sahaja | Betapa pantas sasaran AE automatik menjejaki perubahan kecerahan pemandangan; 1.0× menyemak semula setiap 2.5 saat. |
| **Pelindung Sorotan** | Ketat (1 %) / Normal (5 %) / Longgar (15 %) | Ketat | Kamera yang mendedahkan tetapan | Berapa banyak bingkai yang boleh dipotong kepada putih sebelum AE menggelapkan imej. |

{% hint style="info" %}
**Keperluan cahaya untuk kamera multispektral Bayer (RGN

/OCN

/NGB

):** adegan mesti mempunyai cahaya yang mencukupi dalam ketiga-tiga saluran atau penentukuran tidak akan berfungsi dengan betul — satu pendedahan sensor tunggal merangkumi ketiga-tiga spektrum. Gunakan penderia cahaya DAQ untuk mengukur cahaya anda, atau beralih sepenuhnya ke mono (M3M) supaya setiap jalur mendapat pendedahan tersendiri. Jika tangkapan melanggar perkara ini,Chloros

akan mengesan dan memberi amaran (pemberitahuan unmix-clamp).
{% endhint %}

### Format Piksel &amp; Resolusi

<!-- SCREENSHOT-NEEDED: per-camera Pixel Format & Resolution section on a STANDALONE camera — Pixel Format, Resolution, and Binning dropdowns plus the Current WxH readout. A second capture on an array member showing the read-only "Set in array settings" state would also be useful. -->

**Ahli susunan** memaparkan baris &quot;Semasa&quot; (format + LxT) dan &quot;Pengumpulan&quot; yang hanya boleh dibaca dengan nota &quot;Ditetapkan dalam tetapan susunan&quot; — memulakan semula aliran pada satu ahli akan memutuskan penyelarasan, jadi ini diuruskan dalam [panel tetapan susunan](#array-settings-pane).**Kamera berdiri sendiri** mendapat:

| Kawalan | Pilihan | Fungsinya |
| --- | --- | --- |
| **Format Pixel** | BayerRG8 / BayerRG10 / BayerRG12 / BayerRG16 / Mono8 | Format piksel sensor (kedalaman bit). |
| **Resolusi** | Penuh / Separuh / Suku | Berkaitan dengan binning semasa: Penuh = 2048/N × 1536/N untuk binning N×N. |
| **Penggabungan** | 1x1 (tiada) / 2x2 / 4x4 | Penggabungan perkakasan N×N — nilai yang lebih besar mengurangkan resolusi tetapi meningkatkan SNR dan kadar bingkai. Menukar tetapan ini akan memulakan semula aliran dan menetapkan semula sebarang ROI kepada medan pandangan penuh yang baru. |
| **Semasa** | baca sahaja | Saiz sebenar WxH dan offset (x, y) yang sedang digunakan. |

### Pratonton Langsung

Semua dalam bahagian ini hanya untuk **pihak paparan**— ia mengubah apa yang anda lihat dalam siaran langsung, manakala rakaman yang disimpan kekal linear dan tidak diubah — dengan satu pengecualian:**Vignette** bersifat radiometrik dan juga mempengaruhi eksport (dijelaskan di bawah).

<!-- SCREENSHOT-NEEDED: per-camera Live Preview section on an RGB (FRGB) camera — Render resolution, White Balance mode, Gamma, Denoise, Sharpness, Vignette, Color Profile dropdown open showing Raw/Linear/Natural/Enhanced/Custom Temperature, Saturation, Contrast, Mirror H/V and Rotation. -->

<!-- SCREENSHOT-NEEDED: per-camera Live Preview section on a Bayer multispectral (e.g. FRGN) camera — showing the Index row with its gear button (and the absence of the RGB-only White Balance / Gamma / Color Profile / Saturation / Contrast rows). -->



| Kawalan | Julat / pilihan | Lalai | Terpakai kepada | Fungsinya |
| --- | --- | --- | --- | --- |
| **Resolusi render** | 360p (tercepat) / 480p / 720p / 1080p / Resolusi sensor asli (terlambat) | 720p | Semua | Ketinggian di mana backend menjalankan rantaian pratonton radiometrik. Lebih rendah membeli kadar bingkai tanpa menukar bidang pandangan. |
| **Indeks**| Petak semak + gear | Padam | Bayer multispektral sahaja,**bukan** ahli susunan gabungan | Pratonton indeks vegetasi secara langsung. Gear membuka [Pengira Indeks] yang dikongsi(#index-calculator-pane) dimuat awal dengan jalur semula jadi penapis kamera (contohnya `Red_660_RGN`, `Green_550_RGN`, `NIR_850_RGN`). Ekspresi tersuai ditambah LUT (hidup/mati, tahap lalai 3, min lalai 0.2, maksimum lalai 1) dikira pada setiap bingkai pratonton. Ahli susunan gabungan menyembunyikan baris ini — susunan itu memiliki satu indeks kongsi. |
| **Imbangan Putih** | Padam / Sekali / Berterusan + butang rakam semula | Berterusan | HanyaRGB

| Imbangan putih secara langsung. Butang segar semula merakam semula imbangan putih daripada spektrum DLS semasa (dimatikan apabila mod ialah Padam). |
| **Gamma** | Hidup / Mati | Hidup | HanyaRGB

| Menunjukkan gamma (γ = 2.2 LUT) pada pratonton langsung. Rakaman yang disimpan kekal linear. |
| **Penurunan Kebisingan** | Petak semak + kekuatan 0–100 | Padam / 50 | Semua (per-kamera, walaupun dalam susunan) | Penapis bilateral pada pratonton langsung. Lebih tinggi = lebih lancar tetapi butiran lebih lembut. |
| **Ketajaman** | Kotak semak + kekuatan 0–100 | Matikan / 30 | Semua | Topeng ketajaman pada pratonton langsung, digunakan terakhir. Boleh memperhebatkan hingar. Hanya untuk pratonton. |
| **Vignette**| Kotak semak + kekuatan 0–100 | Matikan / 0 | Semua | Penyingkiran vignette residu secara manual (mencerahkan sudut), dilapis di atas anggaran Smart Vignette susunan.**Radiometrik — menjejaskan paparan langsung DAN eksport**, tidak seperti Denoise/Ketajaman. |
| **Profil Warna** | Mentah / Linear / Semula Jadi / Ditingkatkan / Suhu Tersuai | Semula Jadi | HanyaRGB

| Lihat di bawah. |
| **Suhu Warna** | 2000–10000 K, langkah 100 | 5500 K | Hanya profil Suhu Tersuai | Menetapkan imbangan putih pada suhu warna berkaitan tetap (input DLS diabaikan). Nilai Kelvin terakhir yang dipilih akan diingati semasa menukar profil. |
| **Saturasi** | 0–200 (100 = neutral) | 100 |RGB

sahaja | Saturasi HSV pada pratonton langsung. |
| **Kontras** | 0–200 (100 = neutral) | 100 | Hanya untukRGB

| Kontras linear sekitar kelabu pertengahan pada pratonton langsung. |
| **Cermin H / Cermin V** | Kotak semak | Padam | Semua | Membalikkan pratonton secara mendatar / menegak. |
| **Pusingan**| 0° / 90° / 180° / 270° | 0° | Semua | Memusing pratonton. Orientasi digunakan pada akhir rantaian pratonton belakang —**rakaman yang disimpan kekal dalam orientasi asal kamera**, dan pandangan komposit tatasusunan mengabaikannya. |**Semantik profil warna** (kameraRGB

):

* **Raw** — memintas sepenuhnya rantaian pemprosesan.
* **Linear** — isyarat gelap + medan rata + imbangan putih; tiada matriks warna, tiada gamma.
* **Natural** *(laluan)* — linear ditambah matriks pembetulan warna yang diukur dan lengkung nada yang menyesuaikan diri dengan pemandangan.
* **Enhanced** — Natural ditambah kecerahan warna dan kontras tempatan CLAHE. Kos tambahan hanya terpakai pada pratonton langsung — rakaman yang disimpan sentiasa mendapat hasil akhir penuh tanpa mengira profil.
* **Suhu Tersuai** — Natural dengan imbangan putih dipaku pada nilai Kelvin pilihan anda.

{% hint style="warning" %}
Untuk Natural, Untuk Enhanced dan Custom Temperature, panel menunjukkan nota nada: bingkai dipercerahkan mengikut babak masing-masing, jadi imej *paparan* yang disimpan tidak boleh dibandingkan bingkai ke bingkai. **Keluarkan radiasi atau pantulan untuk pengukuran.**
{% endhint %}

### Lapisan paparan (ditaruh di atas siaran langsung)

Ini hanya untuk frontend — ditaruh di atas video, tidak pernah menyentuh aliran atau rakaman.

<!-- SCREENSHOT-NEEDED: a live feed tile with overlays active — zebra stripes on clipped sky, 3x3 grid, focus peaking in the default orange, and the on-feed histogram strip; the overlays section of the settings pane visible alongside. -->

| Lapisan | Kawalan | Lalai | Fungsinya |
| --- | --- | --- | --- |
| **Zebra** | Petak semak + ambang 200–255 | Padam / 250 | Garis mendiagnal ungu muda pada piksel yang dipotong. |
| ****Crosshair** | Kotak semak | Padam | Penanda tengah bingkai. |
| **Grid** | Padam / 3 × 3 / 9 × 9 | Padam | Grid komposisi. |
| **Histogram** | Kotak semak + lebar 0.10–0.90 bingkai | Padam / 0.25 | Jalur histogram pada suapan. |
| **Puncak Fokus** | Kotak semak + ambang 20–200 + sampel warna | Off / 80 / `#ff5722` | Sorotan tepi Sobel untuk penumpuan. |
| **Pecahan Saluran** | Butang &quot;Tunjukkan Pecahan (Red

/Green

/NIR

)&quot; / &quot;Sembunyikan Pecahan&quot; | Tersembunyi | Menambah tiga jubin skala kelabu bebas bagi setiap saluran di sebelah komposit (label butang mengikut saluran penapis kamera). Setiap jubin pecahan boleh diseret dan berkongsi warna sempadan kamera. Tidak tersedia pada kamera mono. Disimpan bersama projek. |

### Pengukur Titik

* Petak semak **Klik untuk Sampel**: klik siaran langsung untuk mengambil sampel satu piksel (retikel palang silang menandakannya), atau seret klik untuk memilih kawasan bagi purata piksel.**Kosongkan**memadam sampel dan retikel. Tidak boleh digunakan bersama dengan mod**Aim** AE-ROI.
* **Show**dropdown:**Raw (bit depth)**— nombor digital asli pada kedalaman bit sensor (contohnya 12-bit → 0..4095) — atau**Paparan (8-bit)** (lalai). Apabila indeks langsung diaktifkan, Paparan akan memaparkan nilai indeks yang dikira (contohnyaNDVI

) sebaliknya.
* Panel bacaan menyenaraikan koordinat piksel, saiz bingkai, format piksel, kedalaman bit, dan jadual saluran (Saluran / Nilai / %) dengan label jalur dan panjang gelombang; pasangan hijau Bayer dipuratakan; sampel kawasan menunjukkan &quot;N px avg&quot;.

Status meter titik hanya untuk sesi sahaja.

<!-- SCREENSHOT-NEEDED: Spot Meter in use — reticle placed on the live feed, readout panel showing the per-channel value table with band wavelength labels. -->

### Pendedahan Automatik Ramalan (dikendalikan oleh DLS)

Bahagian ini hanya akan muncul apabila **sekurang-kurangnya satu penderia cahaya DAQ disambungkan** — penyelesai memerlukan spektrum ke bawah secara langsung untuk menggerakkannya.

<!-- SCREENSHOT-NEEDED: Predictive Auto-Exposure (DLS-driven) section with a DAQ connected — Enable checkbox, Smoothing (α) slider at 0.30, and the "Recalibrate ρ" button. -->



| Kawalan | Julat | Lalai | Fungsinya |
| --- | --- | --- | --- |
| **Aktifkan** | Petak semak | Hidup (kamera berdiri sendiri) | Penyelesai bentuk tertutup menggunakan spektrum DLS ditambah kamera pek skalar kalibrasi kamera untuk menghasilkan jalur paling terang hampir tepu sambil memastikan jalur paling gelap kekal di atas ambang SNR — satu penulisan pendedahan tunggal bagi setiap penyelesaian, tanpa gelung penstabilan. Direka untuk timelapse berkuasa solar di mana setiap rakaman mesti didedahkan dengan betul. Backend secara senyap bertukar kepada AE reaktif apabila bacaan DLS sudah lapuk/tiada atau pek kalibrasi tidak dimuatkan. |
| **Pelicinan (α)** | 0.05–1.0, langkah 0.05 | 0.3 | Pelicinan penyelesaian ramalan berturut-turut (lebih rendah = lebih lancar). |
| **Reflektan pemandangan**| Butang**Kalibrasi Semula ρ** | — | Menganggarkan semula faktor pantulan adegan yang digunakan oleh penyelesai. |

{% hint style="info" %}
**Sambungan tatasusunan mematikan AE ramalan secara lalai** — untuk tatasusunan, AE pintarChloros

serta pengendalian pencahayaan automatik di pihak kamera mengendalikan pencahayaan (dengan perlindungan jenuh) dan anggaran reflektansi satu adegan oleh AE ramalan tidak selamat merentasi adegan campuran. Anda boleh mengaktifkannya semula bagi setiap kamera di sini jika anda secara khusus mahukan pendedahan radiometrik yang dipacu oleh DLS.
{% endhint %}

**Bumbung pendedahan dikawal oleh DAQ dan AE berpaut pada insiden.** Tanpa mengira kotak semak di atas, apabila penderia cahaya DAQ ditugaskan kepada kameraRGB

,Chloros

mengira — daripada sinaran menuruni mutlak yang diukur — pendedahan×penguatan maksimum di mana permukaan pantulan 100% kekal di bawah pemangkasan, dan menerapkannya sebagai **bumbung**pada pendedahan automatik. Semasa bumbung itu aktif, kamera berada dalam keadaan**terpasang pada insiden**: ia berjalan dalam gelung terbuka pada pendedahan yang diukur oleh meter insiden dengan penguatan pada 0 dB — pendedahan mengikut cahaya yang diukur, bukan kandungan pemandangan. Oleh kerana had ini hanya boleh memendekkan pendedahan, ia tidak boleh menyebabkan pemotongan sendiri. Had ini akan terhenti secara automatik — dan AE pemandangan biasa disambung semula — setiap kali bacaan DAQ tiada, lapuk (&gt;30 saat), atau gelap, atau jika ≥15% bingkai terpotong pada pendedahan yang ditetapkan (bermakna sensor dan kamera melihat pencahayaan yang berbeza). Tiada suis GUI; ini adalah tingkah laku standard setiap kali kamera eRGB

mempunyai DAQ yang terikat.

### Pengambilan &amp; Pemicu

Ahli-ahli<!-- SCREENSHOT-NEEDED: Acquisition & Trigger section on a standalone camera — Trigger Mode, Trigger Source, and the Frame Rate row in Auto mode showing live fps; ideally a second capture on an array member showing the read-only Role/Sync Line/Peers rows. -->

array juga memaparkan baris **Peranan**(Hanya Baca) (Master dalam biru / Slave dalam hijau),**Talian Sinkronisasi**, dan**Rakan Sebaya**.

| Kawalan | Pilihan | Lalai | Nota |
| --- | --- | --- | --- |
| **Mod Picu** | Padam / Hidup | Hidup | Dilumpuhkan untuk ahli tatasusunan (tatasusunan menguruskan pemicuan). |
| **Sumber Picu** | Perisian / Line0 (M8) / Line1 / Line2 | Line0 | Disembunyikan semasa Mod Picu dimatikan; dilumpuhkan untuk ahli tatasusunan. Line0 ialah input pencetus luaran M8 yang diasingkan optikal. |
| **Kadar Bingkai**| Auto / Manual + nilai | Auto |**Auto**: had kadar bingkai kamera dimatikan — pendedahan menentukan fps, dan kotak memaparkan kadar sebenar secara langsung.**Manual**: anda mengehadkan fps dengan gelangsar (1 hingga ke had maksimum yang terhad oleh jalur lebar), bermula daripada kadar sebenar semasa. Ahli tatasusunan melihat &quot;Bacaan sahaja N fps (langsung)&quot; dengan &quot;Atur dalam tetapan tatasusunan&quot;. |

### Rangkaian / Pengangkutan

| Baris | Tingkah laku |
| --- | --- |
| **Saiz Pakej**| 1500 (Standard) / 9000 (Jumbo) — lalai**Jumbo**. |
| **Kadar Pemindahan** | Had kadar pemindahan pautan yang hanya boleh dibaca dalam MB/s. Backend menyamakan semula ini merentas semua kamera yang disambungkan pada setiap sambungan/putus sambung. |
| **Pengendalian Buffer** | Mod pengendalian buffer yang hanya boleh dibaca. |

### Rakaman

Panel ini diakhiri dengan butang **&quot;Buka Tetapan Rakaman…&quot;** yang akan membawa ke [Panel Tetapan Rakaman](capture.md#the-capture-settings-pane) (dimatikan sehingga projek dibuka — &quot;Buat atau buka projek untuk menyimpan rakaman&quot;). Jika kamera disembunyikan atau dihentikan, petunjuk akan mengingatkan anda untuk memaparkan semula/menyesuaikan semula sebelum merakam.

## Panel tetapan ARRAY

Buka dengan **gear**pada baris ARRAY. Header: nama array dengan pensel menamakan semula, dan**×** untuk tutup. Bahagian di bawah yang ditandakan *hanya gabungan* hanya muncul untuk array yang disambungkan dalam mod paparan gabungan.

<!-- SCREENSHOT-NEEDED: array settings pane, top portion — array name header, Sync section (Master/Slaves/Sync Line), and Ambient Light Sensor section with the Light Sensor dropdown and the green "Active — all cameras in the array are illumination-corrected" status line. -->



### Sinkronisasi

Baris **Master**,**Slaves**, dan**Sync Line** hanya untuk bacaan.

### Penderia Cahaya Sekeliling

Ditunjukkan untuk kedua-dua susunan gabungan dan berasingan:

* Kotak semak **Sasaran Kalibrasi** — &quot;Mengesan sasaran ArUcoMAPIR

dan mengesahkan LUT reflektansi panel berbandingNDVI

&quot;; memacu lapisan sasaran dan jadual pengesahan jubin gabungan.
* Senarai lungsur **Penderia Cahaya** — mengikat satu DAQ kepada keseluruhan susunan. Pilihan ini kekal serta-merta, merebak ke setiap senarai lungsur Penderia Cahaya pada setiap kamera ahli (anda masih boleh menimpa mengikut kamera), dan mula menghantar spektra ke susunan.
* Baris **Status** langsung: Mati · &quot;Menunggu spektrum pertama…&quot; · &quot;Aktif — semua kamera dalam susunan telah diperbetulkan pencahayaannya&quot; · &quot;Tiada spektrum baharu dalam 3 saat terakhir — masih menggunakan bacaan terakhir (tiada tamat masa lapuk)…&quot;.
* Nota dalam panel: &quot;Pembetulan radiometrik merentasi susunan. Tetapan bagi setiap kamera akan diutamakan.&quot;

### Rakaman — tetapan penderia seragam *(digabungkan sahaja)*

Tetapan ini terpakai secara seragam kepada setiap ahli (perubahan bagi setiap ahli akan mengganggu penyelarasan). Pindaan disediakan secara berperingkat dan digunakan bersama-sama.

<!-- SCREENSHOT-NEEDED: array settings Capture section — Pixel Format, Binning, Resolution preset, the ROI crop W/H/X/Y fields with the "max WxH" hint and Reset button, Trigger Rate row in Auto showing the derived fps, and the Apply/Cancel buttons; ideally with the live orange crop-preview box visible on the array tile. -->

| Kawalan | Pilihan / julat | Fungsinya |
| --- | --- | --- |
| **Format Pixel** | BayerRG8 / BayerRG10 / BayerRG12 / BayerRG16 / Mono8 | Format sensor seragam untuk semua ahli. |
| **Pembingkaian** | 1x1 / 2x2 / 4x4 | Pembingkaian perkakasan — mengekalkan keseluruhan bidang pandangan sambil meningkatkan SNR dan kadar bingkai. Menukar tetapan ini akan menetapkan semula medan ROI kepada keseluruhan bidang pandangan penuh yang baru. |
| **Preset Resolusi** | Penuh / Separuh / Suku | Berkaitan dengan binning; mengisi medan ROI dengan potongan berpusat. |
| **Potongan ROI (px)**| Medan nombor W / H / X / Y | Potongan sensor. Lebar/tinggi diselaraskan kepada gandaan 16 (minimum 64); offset diselaraskan kepada gandaan 4. Petunjuk &quot;max WxH&quot; memaparkan had maksimum dan**Reset** mengembalikan ke medan pandangan penuh. Semasa menyunting, satu kotak pratonton potongan berwarna jingga akan dilukis secara langsung pada jubin susunan (termasuk skema sensor penuh apabila memotong ke luar). |
| **Kadar Picu**| Togol Auto / Manual + fps 0.5–10, langkah 0.5 |**Auto**(laluan lalai): backend mengira kadar picu daripada resolusi dan jalur lebar — input dinyahdayakan dan memaparkan nilai yang dikira.**Manual**: menetapkan nilai anda pada Butang Terapkan. |

Nota dalam panel: &quot;Perubahan format/rezolusi memulakan semula semua kamera seketika. Kadar pencetus terpakai secara langsung.&quot; Butang **Terpakai / Batal** terletak di bahagian bawah panel.

### Penyelarasan (penjajaran bersama) *(gabungan sahaja)*

<!-- SCREENSHOT-NEEDED: array settings Alignment section after a successful calibration — green "RMS x.xx px" residual pill, "✓ All cameras aligned (N)" summary, the per-camera table with px error / match count / NCC columns, the Recalibrate alignment button and the "Auto-expose cameras for alignment" checkbox. -->



* **Baki** pil: &quot;RMS x.xx px&quot; — hijau di bawah 1 px, jingga di bawah 3 px, merah jika tidak, atau jika mana-mana kamera gagal; &quot;tiada profil&quot; sebelum penyelesaian pertama.
* Baris ringkasan: &quot;✓ Semua kamera diselaraskan (N)&quot; / &quot;⚠ p/N kamera diselaraskan — <serial (filter)="">gagal&quot; / &quot;Potongan aktif — Kalibrasi semula untuk menyelaraskan (menggunakan sensor penuh)&quot; / &quot;Menunggu pendedahan reda…&quot;.
* Jadual bagi setiap kamera: kamera (4 digit terakhir siri + penapis), ralat reprojeksi dalam px dengan bilangan padanan (&quot;ref&quot; untuk kamera induk), dan skor korelasi silang ternormalkan yang bertindih berbanding ambang lulus 0.35.
* Butang **Kalibrasi semula penjajaran** (tertulis &quot;Kalibrasi penjajaran&quot; sebelum profil pertama) — menjalankan semula pendaftaran bersama pada bingkai baharu.
* **&quot;Ekspos kamera secara automatik untuk penjajaran&quot;** kotak semak (diperiksa secara lalai) — mencerahkan sementara kamera yang gelap atau rata (pendedahan dahulu, kemudian penguatan) supaya ia mempunyai tekstur untuk dipadankan, kemudian memulihkan AE.

Preview gabungan menyelaras secara automatik apabila dibuka; kalibrasi semula jika fokus atau kedalaman pemandangan berubah. Penyelarasan adalah **secara sesi sahaja mengikut reka bentuk** — ia tidak pernah disimpan ke dalam profil, kerana ia bergantung pada jarak pemandangan pada masa itu. Rakaman masih boleh dieksport dengan pendaftaran piksel (lihat [Eksport Terleraras](capture.md#per-array-controls)).

### Vignet Pintar

* Kotak semak **Benarkan pembetulan**— menerapkan anggaran vignet bagi setiap kamera pada rantaian radiometrik (siaran langsung**dan** eksport).
* **Kalibrasi daripada pandangan semasa**— tujukan susunan ke arah sasaran seragam (panel rata, dinding, atau langit) terlebih dahulu; setiap kamera diratakan secara berasingan dan laporan status menunjukkan &quot;n/N kamera · −x.x %&quot; peningkatan kerataan.**Kosongkan** membuang anggaran tersebut.
* Laras setiap kamera dengan gelangsar **Vignette** khusus kamera dalam [Praperintian Langsung](#live-preview).

### Praperintian Langsung *(gabungan sahaja)** **Indeks**: aktifkan kotak semak + gear — membuka [Kalkulator Indeks](#index-calculator-pane) yang dikongsi dengan jalur yang diambil daripada**semua** kamera ahli. Satu baris pratonton ekspresi di bawahnya memaparkan ekspresi semasa (&quot;Tiada set ekspresi — buka pengira untuk membuat satu&quot;), dikemas kini setiap saat.
* Senarai turun **Kualiti Rendering**(preset yang sama seperti setiap kamera, lalai 720p): ketinggian aliran tontonan langsung**dan** saiz eksport komposit yang disimpan. Nota dalam panel: &quot;Pratinjau + saiz komposit yang disimpan. Imej setiap kamera sentiasa dieksport pada resolusi penuh.&quot;

### Paparan Lapisan *(gabungan sahaja)** Kotak semak **Aktifkan** (laluan lalai — kamera induk dipaparkan terus; aktif = komposit berlapis).
* **Latar Hadapan**/**Latar Belakang**dropdown: setiap kamera anggota (mengikut nama) atau**Indeks**. Apabila Latar Hadapan diatur kepada Indeks, piksel di luar LUT Min/Max akan memaparkan lapisan Latar Belakang.

### Paparan Terpisah *(gabungan sahaja)*

**&quot;Tunjukkan kamera ahli&quot;**— butang**Belah / Sembunyikan kamera ahli** yang menambah siaran langsung setiap ahli sebagai jubin grid berasingan di sebelah komposit. Jubin tersebut membaca penimbal bingkai yang sedia ada pada susunan (tanpa sambungan kamera tambahan). Hanya paparan grid; disimpan bagi setiap susunan bersama projek.

### Keupayaan

Panel baca sahaja yang dikemas kini setiap 5 saat:

* **Label lapisan**: &quot;Rakaman serentak&quot; (hijau) · &quot;Rakaman serentak (pancaran selang-seli FTD)&quot; (hijau) · &quot;Rakaman selang-seli (penggelinciran 100 ms)&quot; (kuning jingga) · &quot;Konfigurasi terlalu besar&quot; (merah).
* **Kesihatan bingkai**: &quot;x.xx % belum selesai&quot; — hijau di bawah 1 %, jingga di bawah 5 %, merah pada 5 % atau lebih.
* **Baris pautan**: &quot;NIC {mbps} Mbps - kekal {MB/s} MB/s&quot;.

Ini adalah bajet jalur lebar masa nyata susunan. Untuk model fps dan rangkaian yang mendasari — dan apa yang perlu diubah apabila peringkat bertukar menjadi jingga atau merah — lihat [Multi-Camera Arrays](arrays.md) dan [Rujukan CLI](../reference/cli-reference.md).



<!-- SCREENSHOT-NEEDED: array settings Capabilities panel showing a green "Simultaneous capture" tier, the frame-health percentage, and the NIC/sustained-throughput line. -->## Panel Pengira Indeks

Halaman sidebar ketiga, dikongsi oleh gear Indeks bagi setiap kamera dan gear Indeks bagi susunan gabungan (satu pada satu masa — tajuknya akan berbunyi &quot;Kalkulator Indeks — <camera name="">&quot; atau &quot;Kalkulator Indeks — <array name="">&quot;). Ia menerima senarai jalur (jalur semula jadi penapis kamera, atau semua jalur merentasi ahli-ahli susunan), ekspresi semasa, dan konfigurasi LUT (hidup/mati, tahap — lalai 3, min — lalai 0.2, maks — lalai 1), serta histogram indeks secara langsung. **Terpakai** menetapkan ekspresi; perubahan LUT terpakai secara langsung pada pratonton.

<!-- SCREENSHOT-NEEDED: Index Calculator pane open for a combined array — band buttons for all member cameras, an NDVI-style expression in the editor, LUT controls, and the live index histogram. -->

## Tetapan per-kamera berbanding tetapan yang diuruskan oleh tatasusunan

Rujukan pantas untuk apa yang terletak di mana apabila kamera adalah ahli tatasusunan:

| Yang diuruskan oleh tatasusunan (baca sahaja dalam panel kamera) | Masih per-kamera di dalam tatasusunan |
| --- | --- |
| Format piksel, resolusi, binning | Pendedahan automatik (pendedahan, penguatan, sasaran, pelicinan, ROI) |
| Mod/sumber pencetus, kadar bingkai | Pengurangan hingar, ketajaman, vignet |
| | Orientasi (cermin/putar), lapisan paparan, meter tumpuan |
| | Indeks (susunan paparan berasingan), pengikatan penderia cahaya |

Perilaku merentas lain:

* **Paparan gabungan vs berasingan** dipilih semasa menyambungkan susunan: gabungan = satu jubin komposit yang diselaraskan (sumber ahli hanya melalui Paparan Berpisah); berasingan = setiap ahli memaparkan jubin terselarasnya sendiri. Kamera tidak pernah memaparkan kedua-dua sumber berdiri sendiri dan jubin tatasusunan.
* **Sambung semula secara automatik**: membuka projek yang disimpan akan memulihkan kameranya dan susunannya serta menerapkan semula setiap tetapan yang disimpan ke backend sebelum aliran bermula semula.
* **Pintu Pengambilan**: kamera yang disembunyikan atau ditangguhkan dikecualikan daripada Rakaman Semua; satu susunan hanya dihalang sepenuhnya apabila SEMUA ahlinya disembunyikan/ditangguhkan. Lihat [Tetapan &amp; Mod Rakaman](capture.md).

## Bagaimana tetapan kekal

Keadaan tab kamera disimpan **bersama projek**, bukan dalam pelayar:

* Setiap perubahan reaktif mengambil snapshot kamera dan susunan ke dalam `cameras.json` projek (dibuang pantulan 500 ms). Ini merangkumi nama dan warna kamera, tetapan pendedahan/penguatan/AE, format piksel/rezolusi/pembinanan, kadar pencetus, tetapan pratonton (rezolusi render, pengurangan hingar, ketajaman, vignet, profil warna, kepenuhan/kontras), orientasi, lapisan, pembahagian saluran, konfigurasi indeks, tetapan AE-peramalan, ROI AE, nama array, mod paparan, tetapan tangkapan array (termasuk kedudukan potongan ROI), dan blok grid (zoom suapan, mod paparan, kunci grid, susunan jubin manual, kamera tersembunyi, jubin tertutup, kamera aktif).
* Pengikatan sensor cahaya disimpan dalam `sensors.json` projek.
* Membuka semula projek menyambung semula perkakasan dan menerapkannya semula.
* **Tiada projek dibuka = hanya sesi sahaja**: tanpa projek, tiada apa-apa yang kekal selepas menutup Chloros.
* Hanya sesi tanpa mengira projek: status seketika, sampel meter tumpuan, kotak semak Sasaran Kalibrasi bagi setiap kamera (sentiasa dibuka), dan profil penjajaran susunan (dikira semula bagi setiap sesi mengikut reka bentuk).
* Satu pengecualian: pilihan eksport **Tetapan Rakaman** dan mod rakaman kekal bagi setiap projek dalam storan aplikasi tempatan dan bukannya `cameras.json` — lihat [Tetapan &amp; Mod Rakaman](capture.md).</array></camera></serial>
