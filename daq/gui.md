# Tab DAQ dalam Chloros

Tab DAQ — dilabelkan **Penderia Cahaya** dalam bar sisi Chloros — adalah permukaan kawalan langsung untuk [penderia cahaya DAQ-U, DAQ-M, dan DAQ-E](README.md): menyambungkan penderia melalui mana-mana pengangkutan, melihat spektra yang dikalibrasi secara masa nyata, mengira pantulan langsung daripada sepasang penderia, dan merakam fail `.daq` terus ke dalam projek anda.

Tab ini akan tersedia setelah backend Chloros selesai dimulakan. Carta pada tab ini disalurkan oleh perkhidmatan DAQ Chloros melalui sambungan langsung yang akan menyambung semula secara automatik (jeda 2–10 saat) jika terganggu; selagi perkhidmatan tidak dapat diakses, baris Status penderia akan memaparkan **Tiada Pelayan**.

Susun atur adalah **bar sisi sensor**(satu baris bagi setiap sensor yang disambungkan) dan**ruang carta** (satu jubin carta bagi setiap sensor atau kumpulan).

<!-- SCREENSHOT-NEEDED: full DAQ (Light Sensors) tab in list view with one DAQ-E connected — sensor sidebar on the left (Connect Sensor + Record All buttons, one sensor row), spectrum chart with rainbow fill in the main area, live data table below the chart -->

***

## Menyambungkan sensor

Klik **Sambungkan Sensor** di bahagian atas bar sisi. Tetingkap dialog sambungan akan dibuka di kawasan utama (atau sebagai lapisan apabila menambah sensor lain — butang Batal akan muncul dalam kes itu).

| Kawalan | Perilaku |
| --- | --- |
| **Jenis Peranti** | `DAQ-U (USB)` (lalai), `DAQ-M (Bluetooth)`, atau `DAQ-E (Ethernet)`. Menukar akan memulakan semula pengimbasan untuk pengangkutan yang baru dipilih. |
| **Port / Peranti BLE / Nama Hos / IP** | Menyenaraikan peranti yang ditemui sebagai `device - description`; entri pertama yang diiktiraf sebagai penderia akan dipilih secara automatik. Semasa imbasan ia memaparkan `Scanning...` (USB), `Scanning (N)...` dengan kira balik 8 saat (BLE), atau `Discovering ethernet sensors (N)...` dengan kira balik 5 saat (Ethernet). Keputusan kosong membacakan `No ports` / `No BLE devices` / `No ethernet sensors found`. |
| **↻ Segarkan** | Mengimbas semula pengangkutan yang dipilih serta-merta (dimatikan semasa imbasan BLE/Ethernet). |
| **Sambung** | Diaktifkan setelah peranti dipilih; dinamakan semula kepada `Connecting...` semasa sambungan dibuat. |

Penemuan hanya dijalankan **semasa dialog sambungan dipaparkan di skrin**, dan diulang setiap 15 saat untuk pengangkutan yang dipilih sahaja — hanya membuka tab tidak akan mengimbas. Sekiranya gagal, dialog akan memaparkan: *&quot;Sambungan gagal. Cuba cabut dan pasang semula sensor, kemudian klik Sambung sekali lagi.&quot;*

Bar sisi akan terbuka secara automatik apabila sensor pertama anda disambungkan.

{% hint style="info" %}
**DAQ-E tidak muncul?** DAQ-E tidak mempunyai LED status — semak penunjuk PoE/pautan pada port suis atau penyuntik yang ia dicucuk, dan benarkan beberapa saat selepas dihidupkan untuk ia memulakan. Mesin Chloros mesti berada di domain siaran yang sama (mDNS tidak merentasi penghala). Pada Windows, terima permintaan firewall Defender pada kali pertama Chloros mengikat soket multicastnya (mDNS UDP 5353, data DAQ-E UDP 5002, PTP UDP 319/320). Dua unit DAQ-E pada satu LAN dikesan secara berasingan, masing-masing di bawah nama hos `daq-e-<id>.local` sendiri.
{% endhint %}

<figure><img src="../.gitbook/assets/v120-daq-device-type.png" alt=""><figcaption>Jenis Peranti menawarkan DAQ-U (USB), DAQ-M (Bluetooth) dan DAQ-E (Ethernet)</figcaption></figure>

***

## Bar sisi penderia

Setiap penderia yang disambungkan akan mendapat satu baris (ditambah satu baris bagi setiap kumpulan Persekitaran+Objek). Baris boleh disusun semula dengan diseret, dan susunannya juga akan menyusun semula jubin carta. Klik satu baris untuk menjadikan penderia/kumpulan itu sebagai carta aktif dalam paparan senarai.

| Elemen | Makna |
| --- | --- |
| Border kiri berwarna | Warna graf penderia. |
| Lencana Pengangkutan | `DAQ-U` / `DAQ-M` / `DAQ-E`, atau lencana hijau `REF` untuk kumpulan pantulan Ambient+Object. |
| Nama peranti | Secara lalai menggunakan siri penderia (identiti stabil untuk penentukuran, nama fail `.daq`, dan padanan import); nama tersuai kekal bagi setiap projek. |
| Pil **Dikalibrasi** (hijau) | Ditunjukkan apabila bundel kalibrasi kilang sensor dimuat, iaitu spektra adalah benar W/m²/nm. |
| Pil **Kemas Kini Tersedia** (kuning jingga, hanya DAQ-E) | Firmware yang sedang berjalan lebih lama daripada imej yang disertakan dengan binaanChloros

ini. Semasa kemas kini ia memaparkan kemajuan secara langsung (`Flashing… N%`, `Restarting sensor…`, kemudian `Updated X → Y` atau `Failed`). |
| Mata | Menyala atau memadamkan keterlihatan sensor ini pada carta. |
| Gear | Membuka modal tetapan bagi setiap sensor (di bawah). |
| ✕ (merah) | Memutuskan sambungan sensor, atau membuang kumpulan Ambien+Objek. |

Di atas baris terdapat dua butang:

* **Sambungkan Sensor** — membuka dialog sambungan (ditandakan semula sebagai `Connecting...` semasa sibuk).
* **Rakam Semua / Hentikan Semua**— memulakan atau menghentikan rakaman `.daq` pada**setiap**sensor yang disambungkan. Membutuhkah sekurang-kurangnya satu sensor**dan satu projek terbuka** (petua alat: &quot;Buka projek untuk merakam&quot;); ia bertukar merah selagi mana-mana rakaman sedang dijalankan.

Keadaan kosong memaparkan &quot;Tiada sensor yang disambungkan&quot;.

<!-- SCREENSHOT-NEEDED: sensor sidebar with three rows — a DAQ-E showing both the green Calibrated pill and the amber Update Available pill, a DAQ-U row, and a green REF group row — plus the Connect Sensor and Record All buttons -->

***

## Tetapan bagi setiap sensor (modal gear)

Buka dengan ikon gear pada baris sensor. Kandungan mengikut susunan:

* **Baris maklumat** — Jenis Peranti (DAQ-U/M/E), Sambungan (`Serial (USB)` / `Bluetooth` / `Ethernet`), Port (port COM, alamat BLE, atau hos), dan Siri.
* **Laporan Kalibrasi: Muat Turun** — memuat turun sijil kalibrasi unit ini yang boleh dijejaki NIST (PDF) dan membukanya dalam pemapar PDF anda. Tersedia setelah nombor siri diketahui; sijil akan disimpan dalam cache pada sambungan pertama.
* **Nama Peranti** — klik pensel untuk menamakan semula; kekal bagi setiap projek.
* **Warna Garis Graf** — sampel warna; kekal bagi setiap projek.
* **Masa Integrasi (ms)**— gelangsar + nombor,**1–500 ms**, lalai**32 ms**. Dilumpuhkan semasa AE dihidupkan.
* **Purata Bingkai**— gelangsar + nombor,**1–50 bingkai**, lalai**20**.
* **AE: ON/OFF**— suis pendedahan automatik;**laluan ON secara lalai** semasa menyambung. Matikan untuk menetapkan masa integrasi secara manual.
* **Hentikan Penstriman / Mulakan Penstriman** — menjeda atau menyambung semula penstriman langsung.
* **Rakam / Hentikan Rakaman** — rakaman `.daq` bagi setiap sensor (perlukan projek yang terbuka).
* **Cap** — profil pembetulan cap (seksyen seterusnya).
* **Baris maklumat langsung** — Masa Integrasi (ms), FPS, Sampel, Rakaman (merah `REC` atau `Off`), dan Status (`Streaming` / `Paused` / `SATURATED` / `No Server`).

### DAQ-E sahaja: baris rangkaian, firmware, dan PTP

* **Nama hos / IP** — alamat semasa unit.
* **Firmware** — versi firmware semasa, serta sel tindakan:<version\>

butang</version\>

**Kemas Kini ke \<version\>** akan muncul apabila binaanChloros

ini membungkus imej firmware DAQ-E yang lebih baru. Kemas kini diflask melalui rangkaian dalam kira-kira 30 saat; penderia akan dihidupkan semula dan disambung semula secara automatik, dan pemindahan yang terganggu tidak menjejaskan firmware semasa. Aliran kemajuan disiarkan secara langsung (`Flashing… N%` → `Restarting sensor…` → `Updated X → Y`), dan sel membaca `Up to date` apabila semasa.
* **PTP Sync** — status PTP langsung (berpindah kembali ke `unknown`). Firmware DAQ-E v1.2.0+ mengambil bahagian dalam IEEE 1588 PTPv2 sebagai jam hamba sahaja; backend hosChloros

adalah grandmaster PTP, dan setiap kamera DAQ-E dan LATTICE di LAN menjadi hamba kepadanya dalam domain 0, mengekalkan cap masa dalam lingkungan kira-kira 1 ms.

Untuk kumpulan Ambient+Object, modal gear hanya memaparkan sensor sumber kumpulan, Nama Peranti, dan Warna Garis Graf.



<!-- SCREENSHOT-NEEDED: per-sensor settings modal for a DAQ-E — info rows, Calibration Report Download, Hostname/IP + Firmware row with an "Update to <ver>" button, PTP Sync row, Integration Time / Frame Average sliders, AE ON toggle, and the Cap dropdown all visible (scrolled composite acceptable) -->

### Pilihan Cap

Tetingkap lungsur **Cap** memberitahuChloros

cap fizikal mana yang dipasang di atas penyebar penderia, dan menerapkan profil pembetulan yang diukur di kilang bagi cap tersebut ke atas setiap spektrum. Pilihan bergantung pada model:

| Model | Pilihan Cap |
| --- | --- |
| DAQ-U | Tiada (sensor tanpa pelindung), FOV 15°, FOV 30°, FOV 45°, FOV 60°, FOV 90°, Sunshine (pembetul kosinus) |
| DAQ-M | Tiada (penderia terdedah), Sunshine (pembetul kosin) |
| DAQ-E | Tiada (penderia terdedah), FOV 15°, FOV 45°, FOV 90°, Sunshine (pembetul kosin) |

**Laras lalai bagi setiap model ialah Sunshine (pembetul kosinus)** —MAPIR

menghantar setiap DAQ dengan penutup Sunshine dipasang, dan ia adalah konfigurasi luar standard: pandangan hemisfera 180° dengan ralat kosinus ≤ ±4 % sehingga 60° dan ≤ ±4.5 % sehingga 70° (tidak disyorkan di bawah ketinggian matahari ~15°), meredam secara reka bentuk (~12×). Pilihan anda kekal dalam projek.

{% hint style="warning" %}
**Pilihan topi mesti sepadan dengan topi fizikal.**Sama ada penderia mahupun perisian tidak dapat mengesan topi yang dipasang. Pilihan ini mempengaruhi pembetulan secara langsung dan cap yang ditulis dalam setiap fail `.daq` — dengan peredaman ~12× topi Sunshine, pertukaran topi yang tidak dinyatakan akan membetulkan spektra secara salah dengan anggaran faktor tersebut. (Mengalihkan dan memasang semula penutup yang sama mengulangi sehingga kira-kira 1.5 %.) Pilih**Tiada (sensor terdedah)** hanya apabila penutup fizikalnya telah dikeluarkan; pada DAQ-E, &quot;Tiada&quot; masih menerapkan profil geometri kilang untuk penyebar kaca terbenamnya — ia bukan tiada operasi — dan DAQ-E tanpa penutup adalah konfigurasi bangku, bukan konfigurasi lapangan yang disokong.
{% endhint %}

{% hint style="info" %}
Meningkatkan daripada manual terdahulu: penukar &quot;Sunshine Diffuser Installed&quot; di pihak pelayar daripada 1.1.0 telah dihapuskan. Pengendalian topi kini adalah profil Topi per-sensor ini, yang diterapkan di pihak pelayan.
{% endhint %}

***

## Kawasan carta

Bar atas melekat memaparkan **togol pandangan senarai ⇄ grid**dan gelangsar**Zoom Carta** (saiz jubin 200–2000 px). Paparan akan bertukar secara automatik kepada grid apabila terdapat lebih daripada satu kumpulan carta, dan kembali kepada senarai apabila terdapat satu atau kurang. Mod paparan dan saiz carta dikekalkan bagi setiap projek.**Carta spektrum** untuk setiap penderia menunjukkan:

* **Paksis X** — Panjang gelombang (nm). Grid penderia ialah 340–1010 nm pada selang 5 nm (135 titik), diinterpolasi kepada 1 nm untuk dipaparkan.
* **Paksis Y** — Kuasa (W/m²), dengan awalan SI automatik (m/µ/n) yang dipilih daripada puncak. Spektra adalah iradiasi spektral yang dikalibrasi secara radiometrik (W/m²/nm) pada ketiga-tiga pengangkut.
* Isian spektral pelangi di bawah satu jejak; berbilang penderia pada satu carta akan bertindih sebagai garisan berwarna dengan isian yang pudar.
* **Hover**— kursor menegak dengan panjang gelombang dan nilai bagi setiap penderia;**seret** untuk zum (butang zum keluar akan muncul semasa zum).
* Butang **+** (penglihatan grid sahaja) untuk menambah sensor ke carta ini atau mencipta kumpulan (di bawah).
* Nama peranti dipusatkan di atas, dan pemutar sehingga bingkai pertama tiba.

**Kekenyangan** tidak ditandakan pada carta itu sendiri: sensor yang terlalu kenyang akan memaparkan teks status merah `SATURATED` dan baris merah `Saturated: Yes` dalam jadual data langsung. Kurangkan masa integrasi atau aktifkan semula AE untuk membersihkannya.



<!-- SCREENSHOT-NEEDED: grid view with at least two chart tiles visible, the Chart Zoom slider and list/grid toggle in the top bar, and the "+" add-sensor button visible on one tile -->

***

## Jadual data langsung (papar pandangan senarai)

Di bawah carta dalam pandangan senarai, dikemas kini setiap 500 ms:

* **Semua model**: Sampel warna Cahaya (sRGB daripada CIE XYZ), Terpuasi (Ya/Tidak), CIE 1931 X/Y/Z, Kromatikiti x/y, CIE u′/v′, CCT (K), CRI (Ra), Panjang Gelombang Dominan (nm), Panjang Gelombang Puncak (nm), Ketulenan Eksitasi, Duv, CIE L*/a*/b*, dan Munsell H/V/C.
* **Penderia yang telah dikalibrasi sahaja**(mana-mana DAQ-U / DAQ-M / DAQ-E setelah bundel kalibrasi kilangnya dimuatkan — lencana hijau**Dikalis** pada baris penderia adalah petandanya): Kuasa Total (W/m²), Luks Fotopik (lx), Luks Skotopik (lx), Perbandingan S/P, PPFD serta PPFDRed

/Green

/Blue

(µmol/m²/s), dan iradiasi opik — S-cone, Melanopik, Rhodopik, M-cone, L-cone (semua W/m²).

<!-- SCREENSHOT-NEEDED: list view live data table for a DAQ-E showing both the colorimetric rows and the power-calibrated rows (Total Power, Photopic/Scotopic Lux, PPFD, opic irradiances) -->

***

## Kumpulan pantulan (Persekitaran + Objek)

Dua penderia yang bersambung boleh digabungkan ke dalam paparan pantulan langsung — tiada kamera terlibat:

1. Dalam paparan grid, klik **+**pada jubin carta dan pilih**Gabungkan Persekitaran + Objek**.
2. Pilih satu penderia **Sumber Cahaya Sekitar**dan satu penderia**Pemindai Objek**(dua penderia berasingan), kemudian**Buat**.

Chloros

mengira R(λ) = object(λ) / ambient(λ) bagi setiap panjang gelombang daripada dua aliran langsung (0 di mana ambient ≤ 0). Label kumpulan mengikut kelas penentukuran penderia:

* Kedua-dua penderia ditentukur (bungkusan dimuat) → **&quot;Reflekans Nampaknya&quot;**.
* Mana-mana sensor tidak dikalibrasi → **&quot;Reflektan Relatif&quot;**.

Kumpulan ini muncul sebagai baris `REF` hijau di bar sisi dan carta tersendiri (isi pelangi, nilai terapung sehingga 4 perpuluhan, seret-zoom).

Menu **+**juga menawarkan**Tambah Sensor Baharu** dengan tiga penempatan: *Gabungkan Sensor Baharu* (sertai carta ini), *Pindahkan Sensor Terdapat Ke Sini*, atau *Lihat Sensor Baharu* (carta tersendiri).



<!-- SCREENSHOT-NEEDED: the "+" add-sensor overlay open on a chart tile showing the menu (Add New Sensor / Combine Ambient + Object / Cancel), and the Ambient + Object sub-dialog with its two sensor selects -->

### Jadual indeks vegetasi

Dalam paparan senarai, jadual indeks vegetasi terletak di bawah carta kumpulan pantulan, dikira daripada pantulan langsung di pusat jalur **biru 450 / hijau 550 / merah 670 /NIR

800 nm** (nilai sehingga 4 perpuluhan, `---` apabila tidak dapat dikira; selerakkan tetikus pada nama indeks untuk nama penuh):

* **Sentiasa dipaparkan** (tidak berubah skala, mana-mana gabungan penderia):NDVI

,GNDVI

, ENDVI,WDRVI

,GRVI

,CVI

,GCI

, MSR.
* **Hanya apabila kedua-dua penderia telah ditala kuasa** (kedua-dua bundel dimuatkan):EVI

,SAVI

,OSAVI

, GSAVI, GOSAVI, MSAVI2, RDVI, TDVI,LAI

, NLI, MNLI, FCI,GEMI

.

<!-- SCREENSHOT-NEEDED: an Ambient+Object reflectance group in list view — reflectance chart labeled "Apparent Reflectance" with the vegetation index table below it showing live NDVI etc. -->

***

## Merakam fail `.daq`

* Rakaman memerlukan **projek dibuka** — jika tidak, kedua-dua Rekod Semua (bar sisi) dan butang Rekod bagi setiap sensor akan dilumpuhkan.
* Fail ditulis ke **`<project folder>/light_sensor/`**; nama fail mengandungi ID sensor dan cop masa, dan nama peranti disimpan bersama rakaman.
* Apabila rakaman dihentikan (Henti, Henti Semua, atau disambung putus di tengah-tengah rakaman), `.daq` yang siap **ditambah ke projek terbuka secara automatik** — ia muncul dalam senarai fail projek tanpa perlu ditambah secara manual, bersedia untuk digunakan sebagai data downwelling bagi [pemprosesan reflektansi](README.md).
* Penunjuk `REC` merah akan dipaparkan dalam baris langsung modal tetapan semasa merakam.

Untuk nombor iradiasi kuantitatif, ambil purata sekurang-kurangnya 15 saat data — ini adalah ciri instrumen, bukan kecacatan.

<!-- SCREENSHOT-NEEDED: recording in progress — sidebar Stop All button in its red state and the settings modal live rows showing Recording: REC -->



***

## Susun atur berbilang penderia dan ketahanan projek

* Gabungkan beberapa penderia dalam satu carta (paksi kongsi), kekalkan carta berasingan (susun atur grid automatik), alihkan penderia antara carta, seret untuk menyusun semula baris/jubin, dan sembunyikan penderia individu dengan suis mata.
* Bagi setiap projek, tetapanChloros

akan disimpan: nama peranti, warna graf, saiz carta, mod paparan, dan tetapan setiap penderia (masa integrasi, purata bingkai, keadaan AE, pilihan cap).
* **Membuka semula projek akan menyambung semula penderia secara automatik** mengikut alamat — port COM untuk DAQ-U, peranti BLE untuk DAQ-M, nama hos mDNS untuk DAQ-E (diresolves walaupun IP unit berubah) — dan menerapkan semula profil cap yang disimpan, purata bingkai, status AE, dan masa integrasi manual setiap sensor.***

## Penggandingan Kamera (DLS)

Tiada apa yang perlu dipadankan. Tidak seperti aliran kerja DLS dron yang mengikat penderia cahaya kepada kamera pada peringkat awal,Chloros

memadankan data DAQ dengan imej pada peringkat kemudian: pada masa import/pemprosesan, bacaan `.daq` diinterpolasi kepada cap masa pendedahan setiap tangkapan. Rakam dengan mana-mana penderia yang disambungkan (`.daq` dimasukkan ke dalam projek secara automatik), dan pemprosesan reflektansi mencari bacaan yang tepat mengikut masa — lihat [DAQ Light Sensors](README.md) untuk maklumat tentang bagaimana data yang turun ke bawah digunakan.</version\>
