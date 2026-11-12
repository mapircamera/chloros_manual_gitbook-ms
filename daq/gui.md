# Tab DAQ dalam Chloros

Tab DAQ — dilabelkan **Penderia Cahaya** di bar sisi Chloros — adalah permukaan kawalan langsung untuk [penderia cahaya DAQ-U, DAQ-M, dan DAQ-E](README.md): sambungkan penderia melalui mana-mana pengangkutan, saksikan spektra yang dikalibrasi secara masa nyata, kira pantulan langsung daripada pasangan penderia, dan rakam fail `.daq` terus ke dalam projek anda.

Tab ini akan tersedia setelah backend Chloros selesai dimulakan. Carta pada tab ini disalurkan oleh perkhidmatan DAQ Chloros melalui sambungan langsung yang akan menyambung semula secara automatik (jeda 2–10 saat) jika terputus; selagi perkhidmatan tidak dapat diakses, baris Status penderia akan memaparkan **Tiada Pelayan**.

Susun atur adalah **bar sisi penderia**(satu baris bagi setiap penderia yang disambungkan) dan**ruang carta** (satu jubin carta bagi setiap penderia atau kumpulan).

<!-- SCREENSHOT-NEEDED: full DAQ (Light Sensors) tab in list view with one DAQ-E connected — sensor sidebar on the left (Connect Sensor + Record All buttons, one sensor row), spectrum chart with rainbow fill in the main area, live data table below the chart -->

***

## Menyambungkan sensor

Klik **Sambungkan Sensor** di bahagian atas bar sisi. Tetingkap dialog sambungan akan dibuka di kawasan utama (atau sebagai lapisan apabila menambah sensor lain — butang Batal akan muncul dalam kes itu).

| Kawalan | Perilaku |
| --- | --- |
| **Jenis Peranti** | `DAQ-U (USB)` (lalai), `DAQ-M (Bluetooth)`, atau `DAQ-E (Ethernet)`. Menukar akan memulakan semula pengimbasan untuk pengangkutan yang baru dipilih. |
| **Port / Peranti BLE / Nama Hos / IP** | Menyenaraikan peranti yang ditemui sebagai `device - description`; entri pertama yang diiktiraf sebagai sensor akan dipilih secara automatik. Semasa imbasan ia memaparkan `Scanning...` (USB), `Scanning (N)...` dengan kira balik 8 saat (BLE), atau `Discovering ethernet sensors (N)...` dengan kira balik 5 saat (Ethernet). Keputusan kosong memaparkan `No ports` / `No BLE devices` / `No ethernet sensors found`. |
| **↻ Segarkan** | Mengimbas semula pengangkutan terpilih serta-merta (dilumpuhkan semasa imbasan BLE/Ethernet). |
| **Sambungkan Sensor** | Diaktifkan setelah peranti dipilih; dinamakan semula kepada `Connecting...` semasa sambungan dibuat. |

Penemuan hanya dijalankan **semasa dialog sambungan dipaparkan di skrin**, dan diulang setiap 15 saat untuk pengangkutan yang dipilih sahaja — hanya membuka tab tidak akan mengimbas. Sekiranya gagal, dialog akan memaparkan: *"Sambungan gagal. Cuba cabut dan pasang semula sensor, kemudian klik Sambung sekali lagi."*

Bar sisi akan terbuka secara automatik apabila sensor pertama anda disambungkan.

{% hint style="info" %}
**DAQ-E tidak muncul?**DAQ-E tidak mempunyai LED status — semak penunjuk PoE/pautan pada port suis atau penyuntik yang ia dicucuk, dan benarkan beberapa saat selepas dihidupkan untuk ia memulakan. Mesin Chloros mesti berada di domain siaran yang sama (mDNS tidak merentasi penghala). Peraturan firewall**Chloros Backend (UDP-In)** pemuat turun membenarkan mDNS (UDP 5353), data DAQ-E (UDP 5002) dan PTP (UDP 319/320); firewall pihak ketiga memerlukan port yang sama dibenarkan. Dua unit DAQ-E pada satu LAN dikesan secara berasingan, masing-masing di bawah nama hos `daq-e-<id>.local` tersendiri.
{% endhint %}

<figure><img src="../.gitbook/assets/v120-daq-device-type.png" alt=""><figcaption>Jenis Peranti menawarkan DAQ-U (USB), DAQ-M (Bluetooth) dan DAQ-E (Ethernet)</figcaption></figure>

***

## Bar sisi penderia

Setiap penderia yang disambungkan akan mendapat satu baris (ditambah satu baris bagi setiap kumpulan Persekitaran+Objek). Baris boleh diseret untuk disusun semula, dan susunannya juga akan menyusun semula jubin carta. Klik satu baris untuk menjadikan penderia/kumpulan itu sebagai carta aktif dalam paparan senarai.

| Elemen | Makna |
| --- | --- |
| Border kiri berwarna | Warna graf penderia. |
| Lencana Pengangkutan | `DAQ-U` / `DAQ-M` / `DAQ-E`, atau lencana hijau `REF` untuk kumpulan pantulan Ambient+Object. |
| Nama peranti | Secara lalai menggunakan siri penderia (identiti stabil untuk penentukuran, nama fail `.daq`, dan padanan import); nama tersuai kekal bagi setiap projek. |
| pil **Dikalibrasi** (hijau) | Ditunjukkan apabila bundel kalibrasi kilang sensor dimuat, iaitu spektra adalah benar W/m²/nm. |
| **Kemas Kini Tersedia** pil (kuning, DAQ-E sahaja) | Firmware yang sedang berjalan lebih lama daripada imej yang disertakan dengan binaan Chloros ini. Semasa kemas kini ia memaparkan kemajuan secara langsung (`Flashing… N%`, `Restarting sensor…`, kemudian `Updated X → Y` atau `Failed`). |
| Mata | Menghidupkan/mematikan keterlihatan sensor ini pada carta. |
| Gear | Membuka modal tetapan bagi setiap sensor (di bawah). |
| ✕ (merah) | Memutuskan sambungan sensor, atau membuang kumpulan Ambient+Object. |

Di atas baris terdapat dua butang:

* **Sambungkan Sensor** — membuka dialog sambungan (ditukar label kepada `Connecting...` semasa sibuk).
* **Rakam Semua / Hentikan Semua**— memulakan atau menghentikan rakaman `.daq` pada**setiap**sensor yang disambungkan. Perlukan sekurang-kurangnya satu sensor**dan satu projek terbuka** (petua alat: "Buka projek untuk merakam"); ia bertukar merah selagi mana-mana rakaman sedang dijalankan.

Keadaan kosong memaparkan "Tiada sensor yang disambungkan".

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
* **AE: ON/OFF**— suis pendedahan automatik;**laluan ON secara lalai** semasa sambungan. Matikan untuk menetapkan masa integrasi secara manual.
* **Hentikan Penstriman / Mulakan Penstriman** — rehat atau sambung penstriman langsung.
* **Rakam / Hentikan Rakaman** — rakaman `.daq` bagi setiap sensor (perlukan projek yang terbuka).
* **Cap** — profil pembetulan cap (seksyen seterusnya).
* **Baris maklumat langsung** — Masa Integrasi (ms), FPS, Sampel, Rakaman (merah `REC` atau `Off`), dan Status (`Streaming` / `Paused` / `SATURATED` / `No Server`).

### DAQ-E sahaja: baris rangkaian, firmware, dan PTP

* **Nama hos / IP** — alamat semasa unit.
* **Firmware** — versi firmware semasa, serta sel tindakan:<version\>

butang</version\>

**Kemas Kini ke \<version\>**akan muncul apabila binaan **Chloros** ini membungkus imej firmware DAQ-E yang lebih baru. Kemas kini diflask melalui rangkaian dalam masa kira-kira 30 saat; sensor akan dihidupkan semula dan disambungkan semula secara automatik, dan pemindahan yang terganggu tidak menjejaskan firmware semasa. Aliran kemajuan disiarkan secara langsung (`Flashing… N%` → `Restarting sensor…` → `Updated X → Y`), dan sel membacakan `Up to date` apabila semasa.
* **PTP Sync** — status PTP langsung (berpindah kembali ke `unknown`). Firmware DAQ-E v1.2.0+ menyertai IEEE 1588 PTPv2 sebagai jam hamba sahaja; backend hos Chloros adalah grandmaster PTP, dan setiap kamera DAQ-E dan LATTICE di LAN menjadi hamba kepadanya dalam domain 0, mengekalkan cap masa dalam lingkungan kira-kira 1 ms.
* **Rangkaian**— alamat yang digunakan oleh unit dan bagaimana ia diperoleh (DHCP, pautan-tempatan atau statik), dan tetapan yang disimpan yang terpakai selepas but semula.**Ubah…**membuka borang:**Automatik (DHCP)**atau**IP Tetap**dengan alamat IP, topeng subnet dan gateway pilihan.**Terpakai dan Mulakan Semula** menyimpannya, memulakan semula penderia dan menyambung semula. Alamat statik di luar rangkaian komputer ini ditolak, kerana Chloros tidak dapat mencapai penderia selepas but semula; satu kotak untuk membenarkannya walaupun begitu akan muncul, untuk komputer yang akan dialamatkan semula atau dialihkan ke rangkaian tersebut. **Streaming semasa kuasa-hidup**menetapkan sama ada penderia memulakan penstriman sebaik sahaja dihidupkan, sebelum sebarang perisian disambungkan.**Mulakan semula sensor** memulakan semula dan menyambung semula dengan tetapan semasa. Permulaan semula ditolak semasa sensor sedang merekod. Baharu dalam 1.3.0.

Untuk kumpulan Ambient+Object, modal gear hanya memaparkan sensor sumber kumpulan, Nama Peranti, dan Warna Garis Graf.

<!-- SCREENSHOT-NEEDED: per-sensor settings modal for a DAQ-E — info rows, Calibration Report Download, Hostname/IP + Firmware row with an "Update to <ver>" button, PTP Sync row, Integration Time / Frame Average sliders, AE ON toggle, and the Cap dropdown all visible (scrolled composite acceptable) -->



### Pilihan Penutup

Tetingkap lungsur **Penutup** memberitahu Chloros penutup fizikal mana yang dipasang di atas penyebar penderia, dan menerapkan profil pembetulan yang diukur di kilang bagi penutup itu ke atas setiap spektrum. Pilihan bergantung pada model:

| Model | Pilihan Penutup |
| --- | --- |
| DAQ-U | Tiada (sensor tanpa pelindung), FOV 15°, FOV 30°, FOV 45°, FOV 60°, FOV 90°, Sunshine (pembetul kosinus) |
| DAQ-M | Tiada (penderia terdedah), Sunshine (pembetul kosin) |
| DAQ-E | Tiada (penderia terdedah), FOV 15°, FOV 45°, FOV 90°, Sunshine (pembetul kosin) |

**Laras lalai bagi setiap model ialah Sunshine (pembetul kosinus)** — MAPIR menghantar setiap DAQ dengan penutup Sunshine dipasang, dan ia adalah konfigurasi luar standard: pemandangan hemisfera 180° dengan ralat kosinus ≤ ±4 % sehingga 60° dan ≤ ±4.5 % sehingga 70° (tidak disyorkan di bawah kira-kira 15° ketinggian solar), meredam mengikut reka bentuk (~12×). Pilihan anda kekal dalam projek.

{% hint style="warning" %}
Pilihan penutup mesti sepadan dengan penutup fizikal.**Sama ada penderia mahupun perisian tidak dapat mengesan penutup yang dipasang. Pilihan ini mempengaruhi pembetulan secara langsung dan cap yang ditulis ke dalam setiap fail `.daq` — dengan perlemahan ~12× penutup Sunshine, Pertukaran penutup yang tidak dinyatakan menyebabkan spektra diselaraskan dengan salah pada anggaran faktor tersebut. (Mengalihkan dan memasang semula penutup yang sama mengulangi kesilapan sehingga kira-kira 1.5 %.) Pilih**Tiada (penderia kosong)** hanya apabila penutup telah dialihkan secara fizikal; pada DAQ-E, "None" masih menerapkan profil geometri kilang untuk penyebar kaca yang terbenam — ia bukan tiada operasi — dan DAQ-E tanpa penutup adalah konfigurasi bangku, bukan konfigurasi lapangan yang disokong.
{% endhint %}

{% hint style="info" %}
Peningkatan daripada manual terdahulu: suis "Sunshine Diffuser Terpasang" di pihak pelayar dari 1.1.0 telah dihapuskan. Pengendalian topi kini adalah profil Topi per-sensor ini, yang diterapkan di pihak pelayan.
{% endhint %}

***

## Kawasan carta

Bar atas melekat memaparkan **togol pandangan senarai ⇄ grid**dan gelangsar**Zoom Carta** (saiz jubin 200–2000 px). Pandangan akan bertukar secara automatik kepada grid apabila terdapat lebih daripada satu kumpulan carta, dan kembali kepada senarai apabila terdapat satu atau kurang. Mod paparan dan saiz carta kekal bagi setiap projek.**Carta spektrum** untuk setiap penderia memaparkan:

* **Paksis X** — Panjang gelombang (nm). Grid penderia ialah 340–1010 nm pada selang 5 nm (135 titik), diinterpolasi kepada 1 nm untuk dipaparkan.
* **Paksis Y**— untuk penderia yang dikalibrasi (pil hijau**Dikalisbrasi**),**Kuasa (W/m²/nm)**— iradiasi spektral yang dikalibrasi secara radiometrik pada ketiga-tiga pengangkut — dengan awalan SI automatik (m/µ/n) yang dipilih daripada puncak. Sensor yang bundel kalibrasinya tidak dimuat akan memplot bacaan mentahnya sebagai**Intensiti (bilangan)**, tanpa skala. Apabila satu carta menindih kedua-dua jenis, kuasa berada pada paksi kiri dan kiraan pada paksi kanan.
* Isian spektral pelangi di bawah satu jejak tunggal; pelbagai penderia pada satu carta ditindih sebagai garisan berwarna dengan isian pudar.
* **Hover**— kursor menegak dengan panjang gelombang dan nilai bagi setiap penderia (W/m²/nm untuk penderia yang dikalibrasi, kiraan keseluruhan jika tidak);**seret** untuk zum (butang zum keluar akan muncul semasa zum).
* Butang **+** (penglihatan grid sahaja) untuk menambah penderia ke carta ini atau mencipta kumpulan (di bawah).
* Nama peranti dipaparkan di tengah di bahagian atas, dan pemutar sehingga bingkai pertama tiba.

**Kepenuhan** tidak ditandakan pada carta itu sendiri: sensor yang terpenuh akan memaparkan teks status merah `SATURATED` dan baris merah `Saturated: Yes` dalam jadual data langsung. Kurangkan masa integrasi atau aktifkan semula AE untuk membersihkannya.

<!-- SCREENSHOT-NEEDED: grid view with at least two chart tiles visible, the Chart Zoom slider and list/grid toggle in the top bar, and the "+" add-sensor button visible on one tile -->



***

## Jadual data langsung (papar pandangan senarai)

Di bawah carta dalam paparan senarai, dikemas kini setiap 500 ms:

* **Semua model**: Sampel warna Cahaya (sRGB daripada CIE XYZ), Terjenuh (Ya/Tidak), CIE 1931 X/Y/Z, Kromatikiti x/y, CIE u′/v′, CCT (K), CRI (Ra), Panjang Gelombang Dominan (nm), Panjang Gelombang Puncak (nm), Ketulenan Eksitasi, Duv, CIE L*/a*/b*, dan Munsell H/V/C.
* **Penderia yang telah dikalibrasi sahaja**(mana-mana DAQ-U / DAQ-M / DAQ-E setelah bundel kalibrasi kilangnya dimuatkan — lencana hijau**Dikalisasi** pada baris penderia adalah petandanya): Kuasa Total (W/m²), Luks Fotopik (lx), Luks Skotopik (lx), Perbandingan S/P, PPFD serta PPFD Red /Green /Blue (µmol/m²/s), dan iradiasi opik — S-kone, Melanopik, Rhodopik, M-kone, L-kone (semua W/m²).

<!-- SCREENSHOT-NEEDED: list view live data table for a DAQ-E showing both the colorimetric rows and the power-calibrated rows (Total Power, Photopic/Scotopic Lux, PPFD, opic irradiances) -->

***

## Kumpulan pantulan (Cahaya Sekeliling + Objek)

Dua penderia yang bersambung boleh digabungkan menjadi paparan pantulan langsung — tanpa melibatkan kamera:

1. Dalam paparan grid, klik **+**pada jubin carta dan pilih**Gabungkan Cahaya Sekeliling + Objek**.
2. Pilih satu penderia **Sumber Cahaya Sekitar**dan satu penderia**Pemindai Objek**(dua penderia berasingan), kemudian**Buat**.

Chloros mengira R(λ) = object(λ) / ambient(λ) bagi setiap panjang gelombang daripada dua aliran langsung (0 di mana ambient ≤ 0). Label kumpulan mengikut kelas penentukuran penderia:

* Kedua-dua penderia ditentukur (bungkusan dimuat) → **"Pantulan Nampaknya"**.
* Mana-mana sensor tidak dikalibrasi → **"Reflektan Relatif"**.

Kumpulan ini akan muncul sebagai baris hijau `REF` dalam bar sisi dan carta tersendiri (isi pelangi, nilai terapung sehingga 4 perpuluhan, seret untuk zum).

Menu **+**juga menawarkan**Tambah Sensor Baharu** dengan tiga pilihan penempatan: *Gabungkan Sensor Baharu* (sertai carta ini), *Pindahkan Sensor Terdapat Ke Sini*, atau *Lihat Sensor Baharu* (carta tersendiri).

<!-- SCREENSHOT-NEEDED: the "+" add-sensor overlay open on a chart tile showing the menu (Add New Sensor / Combine Ambient + Object / Cancel), and the Ambient + Object sub-dialog with its two sensor selects -->



### Jadual indeks vegetasi

Dalam paparan senarai, jadual indeks vegetasi terletak di bawah carta kumpulan pantulan, dikira daripada pantulan langsung di pusat jalur **biru 450 / hijau 550 / merah 670 / NIR 800 nm** (nilai sehingga 4 perpuluhan, `---` apabila tidak dapat dikira; selerakkan tetikus pada nama indeks untuk nama penuh):

* **Sentiasa dipaparkan** (tidak berubah skala, mana-mana gabungan penderia): NDVI, GNDVI, ENDVI, WDRVI, GRVI, CVI, GCI, MSR.
* **Hanya apabila kedua-dua penderia telah ditala kuasa** (kedua-dua bundel dimuat): EVI, SAVI, OSAVI, GSAVI, GOSAVI, MSAVI2, RDVI, TDVI, LAI, NLI, MNLI, FCI, GEMI.

<!-- SCREENSHOT-NEEDED: an Ambient+Object reflectance group in list view — reflectance chart labeled "Apparent Reflectance" with the vegetation index table below it showing live NDVI etc. -->

***

## Merakam fail `.daq`

* Rakaman memerlukan **projek terbuka** — jika tidak, butang Rakam Semua (bar sisi) dan butang Rakam bagi setiap penderia akan dilumpuhkan.
* Fail ditulis ke **`<project folder>/light_sensor/`**; nama fail mengandungi ID sensor dan cap masa, dan nama peranti disimpan bersama rakaman.
* Apabila rakaman dihentikan (Henti, Henti Semua, atau terputus di tengah rakaman), `.daq` yang siap **ditambah ke dalam projek terbuka secara automatik** — ia muncul dalam senarai fail projek tanpa perlu ditambah secara manual, sedia untuk digunakan sebagai data pancaran ke bawah bagi [pemprosesan pantulan](README.md).
* Penunjuk `REC` merah akan dipaparkan dalam baris langsung modal tetapan semasa rakaman.

Untuk nombor iradiasi kuantitatif, puratakan sekurang-kurangnya 15 saat data — ini adalah ciri instrumen, bukan kecacatan.

<!-- SCREENSHOT-NEEDED: recording in progress — sidebar Stop All button in its red state and the settings modal live rows showing Recording: REC -->



***

## Susun atur berbilang penderia dan ketahanan projek

* Gabungkan beberapa penderia dalam satu carta (paksi kongsi), kekalkan carta berasingan (susun atur grid automatik), alihkan penderia antara carta, seret untuk menyusun semula baris/jubin, dan sembunyikan penderia individu dengan suis mata.
* Bagi setiap projek, Chloros menyimpan: nama peranti, warna graf, saiz carta, mod paparan, dan tetapan setiap penderia (masa integrasi, purata bingkai, keadaan AE, pilihan cap).
* **Membuka semula projek menyambung semula penderia-penderianya secara automatik** mengikut alamat — port COM untuk DAQ-U, peranti BLE untuk DAQ-M, nama hos mDNS untuk DAQ-E (diresolves walaupun IP unit berubah) — dan menerapkan semula profil cap yang disimpan, purata bingkai, status AE, dan masa integrasi manual setiap sensor.

***

## Penyandingan kamera (DLS)

Tiada apa yang perlu dipasangkan. Tidak seperti aliran kerja DLS dron yang mengikat penderia cahaya kepada kamera pada peringkat awal, Chloros memadankan data DAQ dengan imej pada peringkat kemudian: pada masa import/pemprosesan, bacaan `.daq` diinterpolasi kepada cap masa pendedahan setiap tangkapan. Rakam dengan mana-mana penderia yang disambungkan (`.daq` dimasukkan ke dalam projek secara automatik), dan pemprosesan reflektansi mencari bacaan yang tepat mengikut masa — lihat [DAQ Light Sensors](README.md) untuk maklumat tentang bagaimana data yang turun ke bawah digunakan. Untuk tangkapan LATTICE **langsung**, pilih sensor dalam menu lungsur**Penderia Cahaya** pada tab Kamera sebaliknya; Setiap tangkapan kemudian menyimpan bacaan yang digunakannya, yang dipadankan hanya dengan kamera bagi tangkapan tersebut (lihat [Aliran Kerja Reflektan](reflectance.md)).</version\>
