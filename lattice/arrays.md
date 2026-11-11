# Susunan Pelbagai Kamera

Susunan LATTICE ialah dua atau lebih kamera LATTICE yang disambungkan sebagai satu unit bersepadu. Satu kamera adalah **utama**: ia menghantar denyut pencetus GPIO perkakasan pada talian penyelarasan bersama (lalai**Line2**), supaya setiap kameranya menangkap pada saat yang sama. Chloros menambah penyelarasan masa PTP, pratonton langsung (jubin bagi setiap kamera atau satu komposit berbilang jalur yang diselaraskan), dan tangkapan bersepadu — setiap pusingan tangkapan menghasilkan satu **kumpulan bingkai** di mana semua kamera berkongsi cap masa dan ID bingkai yang sama (dilaporkan sebagai `fid:N` pada keluaran tangkapan).

Susunan adalah cara kamera mono (M3M) menghasilkan indeks vegetasi — satu kamera menyumbang satu jalur, dan susunan menyelaraskan mereka menjadi timbunan berbilang jalur. Lihat [Kamera Mono & Indeks Vegetasi](mono-indices.md).

Terdapat tiga cara yang setara untuk menyambungkan satu array, dan kesemuanya menjalankan aliran "smart-prep" yang sama:

| Antara muka | Titik kemasukan |
| --- | --- |
| GUI | Tab Kamera → **Sambungkan Array** (butang biru) |
| CLI | `chloros-cli lattice array-connect --serials SN1,SN2,…` (siri pertama = induk) |
| Python SDK | `connect_array(serials=[…])` → `ArraySession` (siri pertama = induk) |

Smart-prep menjalankan: prob kebolehan rangkaian (ping ICMP DF + prob GVSP), pemilihan lapisan penyelarasan, unjuran kadar bingkai pada saiz bingkai yang diminta, keputusan penghantaran 12-bit terbungkus (lihat [Pengangkutan 12-bit Terpakat](#packed-12-bit-transport)), pengaktifan PTP, pemilihan format piksel automatik bagi setiap kamera, penaburan pendedahan automatik daripada keadaan simpanan setiap kamera, dan konfigurasi pencetus GPIO pada Line2.

**Sensor penuh secara lalai (baru dalam 1.3.0).** Connect mengekalkan saiz bingkai yang anda minta — keseluruhan sensor 2048×1536, tiada penyusutan dan tiada pemotongan, melainkan anda memilih sebaliknya — dan membiarkan kadar bingkai jatuh mengikut apa yang disediakan oleh hos dan rangkaian. Ia tidak pernah melakukan binning atau pemangkasan secara automatik (1.2.0 meningkatkan binning secara automatik di bawah 1.5 fps). Binning atau ROI yang lebih kecil terpulang kepada anda: dalam dialog sambungan, pilih salah satu dan Keputusan Dijangka akan memaparkan kadar bingkai yang diberikannya; `array-connect` mencetak pilihan yang lebih pantas dengan kadar yang dijangkakan.

{% hint style="info" %}
Kamera mesti boleh dihubungi pada pautan sebelum semua ini berfungsi — lihat [Menghubungkan Kamera](connecting.md) untuk penemuan, penentuan alamat, dan muat turun penentukuran sambungan pertama. Untuk rig berbilang kamera, tetapan cincin penerimaan NIC hos sama penting dengan kelajuan pautan; jadual lengkap simptom→penyelesaian terdapat dalam [Rujukan CLI § Penyediaan & Penalaan NIC Hos](../reference/cli-reference.md#host-nic-setup--tuning-lattice-arrays).
{% endhint %}

## Dialog Sambungan Susunan

Tab Kamera → **Sambung Susunan**membuka wizard tiga langkah:**Pilih → Mod Paparan → Tetapan**.

### Langkah 1 — Pilih host dan peranti bawahan

<!-- SCREENSHOT-NEEDED: Array Connect dialog, Select scene, with 3-4 LATTICE cameras discovered (an FRGB among them). Table showing Camera / Serial / IP / Master radio / Slave checkbox columns in camera order (M3C first, FRGB at the top), with the FRGB pre-selected as master and the green "Sync cable found on N cameras. Any of them can be master; the RGB camera (else the first) is pre-selected." probe banner visible above the table. -->



Dialog akan mengimbas rangkaian sebaik sahaja ia dibuka ("Mengimbas rangkaian..."), kemudian menguji kabel penyegerakan ("Menguji kabel penyegerakan...") untuk mencari kamera yang berada pada talian penyegerakan M8. Anda memerlukan sekurang-kurangnya **2 kamera** untuk membina satu susunan.

Kabel penyegerakan M8 bersambung di satu persimpangan, jadi **mana-mana kamera yang disambungkan boleh menjadi master**. Pemeriksa mengisi peranan secara automatik — kamera RGB (`-FRGB`) sebagai induk apabila ia disambungkan dengan kabel, jika tidak, kamera pertama yang disambungkan dengan kabel, dan kamera lain yang disambungkan dengan kabel sebagai anak — dan melaporkan salah satu daripada tiga panji:

| Panji | Maksud |
| --- | --- |
| "Kabel penyegerakan ditemui pada {n} kamera. Mana-mana daripadanya boleh menjadi tuan rumah; kamera RGB (atau sebaliknya kamera pertama) telah dipilih secara automatik." (hijau) | Pengesan telah menemui kamera-kamera pada talian penyegerakan; kotak semak radio tuan rumah dan hamba sudah diisi. |
| "Tiada tuan rumah dikesan - semak kabel GPIO" (oren) | Tiada kamera mengesan denyut picu; semak kabel penyelarasan. Peranan masih diisi pra-isi (kamera RGB, jika tidak, kamera pertama dalam senarai, sebagai tuan rumah) dan anda boleh menukarnya. |
| "Tiada Kabel Sinkronisasi: {serials}" (oren) | Kamera yang disenaraikan tidak mempunyai kabel sinkronisasi yang disambungkan. |

Jadual kamera mempunyai lajur **Kamera / Siri / IP / Utama (radio) / Hamba (kotak semak)**. Baris disenaraikan mengikut**susunan kamera** — kamera M3C (Bayer) sebelum M3M (mono); dalam kalangan M3C, FRGB, FRGN, FOCN, kemudian FNGB; M3M mengikut panjang gelombang penapis, yang terendah dahulu (F450 sebelum F808). Senarai kamera di bar sisi dan susun atur lalai grid menggunakan susunan yang sama.

* Pilih tepat **satu master**dan**satu atau lebih slave**. Mengklik radio master semasa sekali lagi akan membatalkannya. Memilih master yang lain hanya memerlukan satu klik: master baru akan meninggalkan para slave dan master sebelumnya menjadi slave (kecuali jika ia tiada kabel penyegerakan).
* Kamera yang ditandakan **"Tiada Kabel Sinkronisasi"** tidak akan pernah boleh dipilih sebagai slave — slave tanpa wayar picu akan menunggu pada talian sinkronisasi selama-lamanya dan memberikan siaran gelap. Sambungkan kamera tersebut sebagai kamera berdiri sendiri sebaliknya.
* Kamera yang sudah disambungkan secara berdiri sendiri *tidak* dilumpuhkan: sambungan array melepaskan sesi berdiri sendiri dan membuka semula kamera dalam array.

**Seterusnya: Mod Paparan →**diaktifkan setelah satu master dan sekurang-kurangnya satu slave dipilih.**Imbas Semula** menjalankan semula penemuan dan pengesanan wayaran.

{% hint style="warning" %}
**Batal** dilumpuhkan semasa imbasan atau pengesanan sedang dijalankan — membatalkan di tengah-tengah pengesanan boleh menyebabkan kemalangan SDK pada firmware kamera LATTICE. Tunggu sehingga pemutar selesai.
{% endhint %}

### Langkah 2 — Mod Paparan



<!-- SCREENSHOT-NEEDED: Array Connect dialog, Display Mode scene, showing the two selectable cards ("Separate Cameras" and "Combined Cameras") with Combined selected/highlighted as the default. -->

| Mod | Apa yang anda dapat |
| --- | --- |
| **Kamera Berasingan** | Satu jubin langsung bagi setiap kamera, semuanya dicetuskan serentak supaya bingkai kekal selari. Setiap kamera mengekalkan warna dan tetapan sendiri. |
| **Kamera Digabungkan** *(lalai)* | Satu tatal yang memaparkan komposit jalur pelbagai NDVI/indeks yang diselaraskan. Kamera berkongsi warna susunan. |

Mod paparan hanya menukar persembahan pratonton langsung — tingkah laku tangkapan adalah sama dalam kedua-duanya.

### Langkah 3 — Tetapan Susunan dan hasil

<!-- SCREENSHOT-NEEDED: Array Connect dialog, Settings scene, healthy state: left column with ROI / Binning / Trigger Rate / Wire Budget controls (1.3.0 has no "Pin resolution" checkbox), right "Projected Outcome" column showing green "Simultaneous capture" tier, an fps range, the NIC line, the "Sim-emit burst" line, and the "Wire budget" line with a checkmark. -->

yang dijangka

Apabila memasuki babak ini, Chloros meminta **saranan**daripada backend dan menerapkannya:**sensor penuh — tiada binning, tiada pemotongan ROI — bila-bila masa mana-mana kadar bingkai boleh menyokongnya**, pada kadar apa pun yang disediakan oleh hos dan talian. Hanya apabila tiada kadar boleh menyampaikan sensor penuh, saranan itu akan mengecilkan bingkai, dan kemudian ia lebih memilih binning berbanding pemotongan ROI, kerana binning mengekalkan keseluruhan medan pandangan. Anda menukar resolusi untuk kadar bingkai dari situ. Setiap perubahan yang anda buat akan menjalankan semula analisis secara langsung dan mengemas kini panel**Keputusan Dijangka** di sebelah kanan.

Lajur kiri — tetapan:

| Kawalan | Pilihan | Lalai | Nota |
| --- | --- | --- | --- |
| **ROI (Medan Pandangan)** | Penuh (2048×1536) / Separuh (1024×768) / Suku (512×384) | Penuh | Potongan sensor: Memotong separuh/suku ke kawasan yang lebih kecil pada pitch piksel asal. |
| **Penggabungan piksel** | 1× / 2× (jumlah 2×2) / 4× (jumlah 4×4) | 1× | Penggabungan perkakasan: 2×2 = FoV penuh pada suku kos penghantaran; 4×4 = FoV penuh pada 1/16. Tersembunyi jika kamera tidak menyokong binning. |
| **Imej sisi wayar** (pembacaan) | — | — | Lebar×tinggi selepas binning yang sebenarnya dihantar pada wayar, dipotong kepada gandaan 16 (minimum 64). |
| **Kadar Picu** | 0.5–60 fps, langkah 0.1 | kosong = auto | Kadar picu tuil master. Biarkan kosong untuk membolehkan Chloros mengitarnya. |
| **Bajet Wayar**| 20–2000 MB/s, langkah 10 | kosong = auto | Berapa banyak yang hos sebenarnya boleh serap, dalam MB/s —**nombor tunggal yang menjadi asas keseluruhan peruntukan susunan.** Dikesan secara automatik daripada penyesuai rangkaian. Kurangkan jika susunan melaporkan bingkai rosak: nilai yang dikesan terlalu tinggi untuk penyesuai USB dan suis kongsi. Mengubahnya akan menjalankan semula unjuran secara langsung. Medan ini dibuka dengan bajet yang digunakan untuk menyambungkan susunan terakhir (disimpan bersama projek), jadi bajet yang telah anda betulkan sekali akan dikekalkan. |

Petak semak **Resolusi Pin** dari 1.2.0 telah dihapuskan: sambungan sentiasa mengekalkan saiz bingkai yang anda pilih dan menurunkan kadar bingkai sebaliknya.

Lajur kanan — **Keputusan Anggaran**:

* **Tahap Sinkronisasi** — "Rakaman serentak" (hijau), "Rakaman serentak (pancaran bertindan FTD)" (hijau), "Rakaman bertindan (luncur 100 ms)" (kuning), atau "Konfigurasi terlalu besar" (merah). Chloros memilih lapisan: tangkapan serentak setiap kali wayar boleh dikongsi pada kadar yang membolehkan kamera diselaraskan (kadar bingkai menurun apabila bahagian setiap kamera mengecil), dan tangkapan bertindan hanya apabila ia tidak dapat dilakukan — jadi satu susunan akan bersambung pada mana-mana rangkaian, pada kadar bingkai yang dibenarkan oleh rangkaian tersebut.
* **projeksi fps** — kadar bingkai yang dijangka array akan jalankan. Dengan tangkapan serentak ia satu nombor: kadar diutamakan, dan pendedahan automatik mengisi tempoh yang diberikan, kemudian meningkatkan penguatan, jadi suasana malap tidak menurunkannya (lihat [Bagaimana pendedahan automatik menggunakan pendedahan dan penguatan](camera-settings.md)). Dengan tangkapan berselang, ia dipaparkan sebagai julat ("lemah → terang"), kerana pendedahan setiap kamera memanjangkan selang dalam suasana malap.
* **Garis NIC** — kelajuan pautan dan throughput berterusan ("NIC {mbps} Mbps · berterusan {N} MB/s").
* **Semakan letupan Sim-emit** — sama ada NIC hos boleh menerima penyerapan cincin satu letupan serentak daripada semua kamera ("Letupan Sim-emit: X MB · Cincin NIC boleh guna: Y MB ✓/✗").
* **Semakan bajet wayar** — permintaan agregat keadaan-seimbang berbanding had wayar selamat-langgaran ("Bajet wayar: {permintaan} MB/s yang diminta oleh {n} kamera · had {had} MB/s ✓/✗ melebihi langganan").
* **"Max kamera pada wayar ini: {n} — ditetapkan oleh had minimum jalur lebar bagi setiap kamera, jadi penyusutan tidak akan meningkatkannya."** — dipaparkan apabila anda hampir (atau melebihi) had maksimum bilangan kamera.
* **"RAGAKAN AKAN TERJATUH pada tetapan ini."**"** — amaran merah dengan sebab backend, serta senarai penghalang dan cadangan pembaikan berwarna biru ("Untuk memuatkan tatasusunan ini pada rangkaian" / "Untuk membuka kunci tangkapan serentak").**Terpakai & Sambung** dikunci sehingga terdapat unjuran, dan labelnya memberitahu anda mengapa ia menolak:

| Label butang | Maksud | Apa yang sebenarnya membantu |
| --- | --- | --- |
| "Menganalisis..." | Analisis masih dijalankan. | Tunggu. |
| **"Terlalu banyak kamera untuk rangkaian ini"**| Rangkaian tidak dapat memuatkan susunan itu walaupun satu kamera pada satu masa (semakan agregat gagal): jauh lebih banyak kamera daripada yang dapat diprosesnya, atau kamera lain yang menstrim padanya. | Kurangkan bilangan kamera, gunakan bingkai jumbo dari hujung ke hujung, atau gunakan NIC yang lebih pantas.**ROI yang lebih kecil TIDAK akan membantu** — lihat di bawah. |
| **"Kurangkan ROI untuk aktifkan"** | Rangka akan terbuang pada tetapan ini (semakan burst/ring gagal). | Kurangkan ROI, naikkan binning, atau baiki cincin penerimaan NIC. |

<!-- SCREENSHOT-NEEDED: Array Connect dialog, Settings scene, over-subscribed state: red "Wire budget ... over-subscribed" line, the "Max cameras on this wire" hint, and the Apply button reading "Too many cameras for this network". Reproduce by configuring more cameras than the 1 GbE ceiling (e.g. 7+ cams at 1500 MTU) or with CHLOROS-simulated models via `lattice analyze-array`. -->



Semasa menyambung, panel muat turun penentukuran hijau mungkin muncul dengan bar kemajuan bagi setiap siri: pada kali pertama kamera disambungkan pada mesin, Chloros memuat turun pakej penentukuran kilang bersaiz ~3.8 MB daripada kamera melalui GigE (kira-kira 70 saat bagi setiap kamera). Kamera yang disimpan dalam cache tidak pernah memaparkan panel ini; dengan lebih daripada enam kamera, barisnya akan beralih. Lihat [Menyambungkan Kamera](connecting.md).

### Penghantaran 12-bit Terpake

Format 12-bit (`BayerRG12`, `Mono12`) biasanya dihantar sebagai perkataan 16-bit. **Penghantaran Terpadat**menghantar 12 bit yang sama dalam 1.5 bait setiap piksel dan membuka padatnya pada hos, jadi setiap bingkai mengambil masa 25% lebih sedikit untuk dihantar — pada sensor penuh, kira-kira sepertiga lebih banyak bingkai setiap saat.**Data yang disimpan adalah sama.** Array connect memutuskan secara automatik (`auto`): ia hanya memampatkan apabila ia membantu dan hos mempunyai CPU yang tersedia untuk membuka semula. GUI tidak mempunyai suis untuknya; dari CLI gunakan `array-connect --packed {auto,on,off}` ( lalai `auto`). Format 8-bit dan 16-bit tidak pernah dipadatkan.

## Lebar jalur: berapa banyak kamera boleh muat

Keupayaan satu array untuk memindahkan data adalah sifat talian, bukan Chloros, jadi angka perancangan terdapat dalam manual perkakasan: **[Perancangan Lebar Jalur Array](https://mapir.gitbook.io/lattice-camera/setup/array-bandwidth-planning)**.

Apa yang dilakukan oleh Chloros dengannya: dialog sambungan menjalankan tinjauan rangkaian, meramalkan kadar bingkai yang boleh dicapai, dan memilih tahap yang sesuai. Satu susunan menyambung pada mana-mana rangkaian: pada pautan perlahan, setiap kamera disesuaikan mengikut bahagiannya pada talian dan kadar bingkai menurun, bukannya susunan itu ditolak atau bingkainya dikurangkan saiz. Hanya satu susunan yang tidak dapat ditampung oleh talian walaupun satu kamera pada satu-satu masa akan ditolak (**Terlalu banyak kamera untuk rangkaian ini**) daripada menjatuhkan paket secara senyap — lihat panel hasil ramalan yang diterangkan di atas.

## Apabila bingkai hilang

Sebuah kamera boleh hilang daripada kumpulan yang diterbitkan atas dua sebab yang berbeza sama sekali,
dan ia memerlukan penyelesaian yang bertentangan. Chloros mengiranya secara berasingan daripada melaporkan satu
angka "tidak lengkap" yang tidak menyatakan kedua-duanya:

| Apa yang berlaku | Apa maksudnya | Di mana hendak diperiksa |
| --- | --- | --- |
| **Rusak**— bingkai tiba dan mempunyai struktur yang buruk | Kehilangan paket GVSP di laluan rangkaian |**Belanjawan wayar**, cincin penerima NIC, bingkai jumbo, suis |
| **Tidak pernah tiba**— tiada bingkai tiba langsung | Kamera tidak mencetuskan, atau tiada apa-apa yang keluar daripadanya |**Kabel penyegerakan M8**, talian penyegerakan, sama ada setiap ahli telah diaktifkan |

Pemisahan itu dinilai semula setiap 10 saat semasa susunan sedang mengalir. Jika melebihi 5%, ia akan dicatat dengan kedua-dua nombor disebut, dan setiap buffer rosak dilaporkan buat pertama kali ia berlaku bagi setiap kamera, kemudian dikumpulkan semula sekali setiap minit supaya sesi yang panjang kekal boleh dibaca.

**Sifar bingkai rosak dengan tiada bingkai yang tidak sampai bermakna pencetus dan penyegerakan kabel adalah sempurna**dan setiap bingkai yang hilang berada di laluan rangkaian. Penyelesaiannya ialah menurunkan**Bajet Wayar** dan menyambung semula.

{% hint style="warning" %}
**Mengurangkan kadar pencetus tidak membantu dengan bingkai rosak.** Penjadualan paket kamera ditulis sekali, semasa sambungan. Mengurangkan kadar pencetus mengubah kekerapan letusan berlaku, bukan kelajuan letusan itu sendiri ke wayar. Pada rig 4 kamera yang diukur, penurunan kadar pencetus 5× tidak mengubah apa-apa, manakala menurunkan bajet wayar dari 240 kepada
200 MB/s menjadikan rig yang sama berkurang daripada 10.4 % bingkai rosak kepada sifar.
{% endhint %}

Susunan yang sedang berjalan tidak dapat merancang semula dirinya — nyahpasang dan pasang semula supaya pemilih masa sambungan dapat berfungsi berdasarkan bajet baru.

### Penyesuai rangkaian USB dihadkan

Penyesuai Ethernet USB mengiklankan kadar pautan *Ethernet*nya, tetapi apa yang sebenarnya dapat dijaga terhad oleh bas USB dan pemandunya. Dongle USB 10GbE dahulu diiktiraf mempunyai throughput kira-kira 1000 MB/s — satu angka yang tidak pernah diukur oleh sesiapa pun — dan menetapkan kelajuan empat kamera mengikut ruang lebihan maya itu merosakkan 6–18% bingkai sementara susunan itu masih melaporkan kadar bingkai sasaran yang sihat. Penyesuai yang disambungkan melalui USB kini dihadkan pada
**200 MB/s** pada Windows dan NVIDIA Jetson, dan **400 MB/s** pada Linux x86_64. Had ini adalah mutlak dan bukannya peratusan, kerana hadnya adalah bus: penyesuai USB 1 GbE memperoleh kira-kira 80 MB/s dan tidak terjejas.

Jika hos anda benar-benar lebih pantas daripada had tersebut, naikkan **Wire Budget** untuk menyatakan demikian.

## Penyelarasan masa PTP

*Penyelarasan* bingkai datang daripada pencetus perkakasan; **PTP** (IEEE 1588 PTPv2) menyediakan *cap masa* yang setara pada setiap peranti. Ia diaktifkan secara lalai semasa sambungan array:

* Backend hos **Chloros**menjalankan PTP grandmaster**. Kamera LATTICE dan penderia cahaya DAQ-E menjadi hamba kepadanya dalam domain 0, jadi cap masa imej dan spektra DAQ mendarat pada satu jam (~1 ms).
* `--no-ptp` (CLI) menyahaktifkannya untuk kerja di makmal — cap masa merentas kamera kemudian **tidak** dapat dibandingkan.
* Periksa kesihatan penyelarasan dengan CLI:

```bash
chloros-cli time-sync status     # grandmaster state, clock identity, health (OK, or why its packets are not reaching the network)
chloros-cli time-sync peers      # slaves seen (cameras + DAQ-E sensors)
chloros-cli time-sync cameras    # per-camera PtpStatus / PtpOffsetFromMaster / PtpMeanPathDelay
```

Tab Kamera sendiri tidak mempunyai penunjuk PTP; permukaan penyelarasan bagi setiap kamera memaparkan **Peranan**(Hanya/Ikut) yang hanya boleh dibaca,**Garisan Penyelarasan**, dan lapisan Keupayaan tatanan. Status PTP DAQ-E dipaparkan dalam butiran sensor di tab Penderia Cahaya.

## Paparan

<!-- SCREENSHOT-NEEDED: Cameras tab with a connected combined array: sidebar showing the ARRAY row (color badge, array name, "DAQ · on" pill) with indented member camera rows in camera order (M3C before M3M, monos by wavelength), and the main area showing the combined index composite tile with the LUT-colored NDVI render, top-left array name pill, and top-right fps readout. -->

susunan secara langsung Kawasan aliran utama menawarkan dua susun atur (tukar di bar atas): **paparan grid**(setiap jubin adalah sel; seret untuk menyusun semula apabila gembok grid dibuka kunci) dan**papar senarai**(baris lebar penuh: satu**susunan gabungan**adalah satu baris — jubin kompositnya — manakala susunan**asing**memberikan**setiap ahli barisnya sendiri**, dengan siaran langsungnya sendiri dan jadual Sasaran Kalibrasi tersendiri; di bawah susunan, satu kamera berdiri sendiri — yang aktif). Pemacu gelangsar**Feed Zoom** menetapkan saiz jubin; di bawah lebar sel 200 px, lapisan nama/fps akan tersembunyi secara automatik. Kamera ahli muncul di bar sisi dan dalam susun atur lalai grid mengikut susunan kamera (M3C sebelum M3M, FRGB dahulu, monos mengikut panjang gelombang); membuka kunci grid dan menyeret masih mengutamakan susunan grid.

<!-- SCREENSHOT-NEEDED: list view of a connected SEPARATE array (1.3.0): one full-width row per member camera, each with its live feed and its own Calibration Target table below it. -->



**Mod Berasingan** memaparkan satu jubin bagi setiap kamera. Setiap jubin memaparkan lapisan:

* nama kamera (atas-kiri),
* **pembaca fps** (atas-kanan) — ini adalah *kadar tangkapan sebenar* kamera yang dilaporkan oleh backend, bukan kadar tinjauan pratonton (pratonton langsung dihadkan pada 30 fps tanpa mengira kadar tangkapan),
* titik status — hijau (siaran) / jingga (memuat) / merah (ralat),
* **pemutar bingkai lapuk** apabila tiada bingkai baru tiba selama 2 s — normal untuk ~5 s selepas sebarang sambungan/putus sambung sementara backend mengimbangi semula bajet jalur rentas kamera.**Mod gabungan**memaparkan satu jubin komposit: backend menyahbayang, menapis, menyelaras, menyahbisukan, menukar kepada sinaran per-band (ditambah pantulan DLS apabila penderia cahaya diikat), menilai ungkapan indeks tatasusunan, terapkan LUT, dan menstrimkan hasil sebagai MJPEG. Sehingga bingkai yang diselaraskan pertama dipaparkan, jubin menerangkan keadaannya: "Menyediakan tatasusunan…", "Kalibrasi penjajaran…", "Menunggu bingkai pertama…", atau — jika percubaan semula penjajaran automatik jubin (kira-kira satu minit) tamat — "Penjajaran diperlukan" dengan butang**Kalibrasi penjajaran**.

Bacaan fps jubin susunan juga melaporkan keadaan aliran:**"Menerapkan tetapan…"**semasa perubahan tetapan (format, binning, ROI) memulakan semula kamera,**"Terhenti"**apabila susunan masih aktif tetapi telah berhenti menghantar kumpulan bingkai, dan**""Berhenti"**apabila ia berhenti menstrim — sambungkan semula array dalam kes itu. Apabila pemisahan spektral perlu mengehadkan jalur yang dipaparkan dalam komposit pada sebahagian besar pikselnya (25% atau lebih), jubin akan memaparkan**"Data jalur keyakinan rendah · N%"**: nilai indeks yang dibina pada jalur itu disyaki (biasanya satu jalur menerima terlalu sedikit cahaya).

Fakta berguna mod gabungan:

* Komposit diselaraskan dengan bingkai kamera **utama**. Penjejakan AE-ROI dan pengukuran titik pada komposit adalah tepat untuk kamera utama dan anggaran untuk kamera hamba; gunakan**Paparan Berpisah** (tetapan susunan → "Tunjukkan kamera ahli") untuk jubin tepat mengikut piksel bagi setiap kamera tanpa membuka sambungan kamera tambahan.
* **Lapisan Paparan* (tetapan susunan; lalai dimatikan) membolehkan anda memilih lapisan latar hadapan dan latar belakang — mana-mana kamera ahli atau **Indeks**. Apabila lapisan latar hadapan = Indeks, piksel di luar LUT Min/Max akan memaparkan lapisan latar belakang.
* **Resolusi Render** (lalai 720p) menetapkan ketinggian siaran langsung *dan* saiz eksport komposit yang disimpan. Imej bagi setiap kamera sentiasa dieksport pada resolusi penuh.
* Penyelarasan dikira bagi setiap sesi dan tidak pernah disimpan. Chloros menyelaras **setiap susunan secara automatik selepas sambungan, dalam kedua-dua mod paparan**, setelah pendedahan automatik ahli-ahli stabil (ia terus mencuba sehingga 5 minit). Bahagian**Penjajaran**pada tetingkap tetapan susunan memaparkan sisihan kuasa dua berpurata (RMS) dan butang**Kalibrasi semula penjajaran** — juga untuk susunan berasingan yang tiada jubin komposit.

## Pengambilan: pemantauan lwn. analisis

Permukaan tangkapan susunan dibahagikan dengan jelas kepada **darjah pemantauan**(rekod apa yang anda lihat) dan**darjah analisis** (rekod data mentah, kalibrasi kemudian):

| Aliran kerja | Darjah | Apa yang disimpannya | GUI | CLI |
| --- | --- | --- | --- | --- |
| **Rakaman**(gambar pegun) | Analisis | Satu kumpulan bingkai diselaraskan bagi setiap pusingan; fail bagi setiap kamera pada setiap tahap eksport yang dipilih (mentah/debayered/radiance/reflectance/pra-lihat/indeks) + fail sampingan `.daq` |**butang **Rakam Semua** + Tetapan Rakaman | `lattice array-capture` |
| **Rakam video indeks** | Pemantauan | Komposit indeks gabungan secara langsung seperti yang dipaparkan — 8-bit, resolusi pratonton, LUT terbina dalam. Dari CLI tanpa GUI dibuka, backend memaparkan NDVI (atau `--expr`) susunan itu setelah susunan tersebut sejajar; rakaman yang tidak menulis sebarang bingkai akan gagal | ● Rakam video indeks (susunan gabungan) | `lattice array-record` |
| **Letupan mentah → bina video**| Analisis | Bingkai sensor mentah pada kadar tangkapan penuh + manifes + `.daq`, kemudian rekonstruksi luar talian menjadi video sinaran / pantulan / indeks yang dikalibrasi, diselaraskan waktunya dengan bacaan DAQ | ⦿ Rakam letupan mentah →**Bina video** | `lattice array-burst` → `lattice array-build-video` |

Peraturan am: jika piksel akan digunakan untuk *ukuran*, gunakan tangkapan atau burst (darjah analisis); jika anda hanya perlu *menonton atau menunjukkan* apa yang dilihat oleh susunan, rakam video indeks (darjah pemantauan).

### Tetapan Rakaman (GUI)

<!-- SCREENSHOT-NEEDED: Capture Settings pane (gear next to Capture All) with a connected array: capture-mode buttons (Single/Continuous/Interval), the bulk export-type toggle row, the Fastest Capture toggle, and the per-array group card showing the Aligned checkbox and the "Record index video" / "Record raw burst" buttons. -->



Gigi roda di sebelah **Rakam Semua** membuka tetingkap Tetapan Rakaman (memerlukan projek yang terbuka — rakaman disimpan ke dalamnya):

* **Mod tangkapan**:**Tunggal**(satu kali imbas) /**Berterusan**(berturut-turut; dihadkan oleh bilangan tangkapan, lalai 1, atau tempoh, lalai 10 saat) /**Selang** (timelapse: N tangkapan setiap X selang untuk Y jumlah, lalai 1 setiap 5 saat untuk 1 minit).
* **Jenis eksport setiap kamera**: Raw, Debayered, Radiance, Reflectance, Pratonton, Indeks — semua yang terpakai diaktifkan secara lalai. Radiance/Reflectance disembunyikan untuk kamera penapisRGB; **Reflectance hanya muncul apabila kamera mempunyai penderia cahaya DAQ** (deria sendiri atau diwarisi daripada susunan); Index memerlukan ungkapan indeks yang dikonfigurasikan.
* **Disesuaikan**(per susunan, lalai**hidup**): memutar eksport ahli mengikut profil penjajaran susunan supaya eksport diselaraskan pada piksel. Raw sentiasa kekal tidak diubah bentuk tetapi membawa transformasi dalam metadata. Fail ahli susunan disimpan dalam orientasi asli setiap kamera (sebarang tetapan Cermin/Pusingan direkodkan dalam fail, bukan diterapkan).
* **Tangkapan Terpantas** (togol): hanya mentah + bacaan DAQ yang ditetapkan + komposit indeks gabungan percuma, melangkau matematik penentukuran semasa tangkapan untuk kadar maksimum — bina semula radiasi/refleksi/indeks kemudian daripada `.daq` yang disimpan.
* Pilihan kekal bersama projek. Kamera yang disembunyikan atau ditangguhkan akan dilangkau.
* **Berterusan** pada susunan dijalankan di dalam backend Chloros (baru dalam 1.3.0): apabila setiap kamera dalam senarai tergolong dalam satu susunan, backend akan menjalankan gelung tangkapan itu sendiri, menulis satu kumpulan sambil ia memilih kumpulan seterusnya, tanpa permintaan bagi setiap tangkapan. Penyimpanan penuh memaksa hentian ("Berhenti: storan penuh. N disimpan, F gagal"). Lihat [Seting & Mod Tangkap](capture.md#capture-modes) untuk nota Microsoft Defender mengenai kadar tangkapan yang tinggi.

CLI setara (titik akhir backend yang sama, semantik yang sama):

```bash
# One synced group, every applicable export level per camera (the default)
chloros-cli lattice array-capture -o output/

# Interval timelapse: one reflectance pass every 10 s for 5 minutes
chloros-cli lattice array-capture --interval 10 --duration 300 --processing reflectance -o timelapse/

# Fastest grab for a moving rig — raw + .daq now, calibrate later
chloros-cli lattice array-capture --fastest -o flightline/

# 30-second monitoring clip of the combined index view, plus a GIF
chloros-cli lattice array-record --duration 30 --fps 10 --gif -o monitoring/

# 5-second analysis-grade raw burst, then build the combined index video
chloros-cli lattice array-burst --duration 5 --build --products combined:index --fps 10 -o capture/
```

Pemampatan TIFF untuk tangkapan ialah `deflate` (tanpa kehilangan, lalai) atau `none` — jadual bendera penuh, susun atur folder tangkapan, dan peraturan pemprosesan semula terdapat dalam [Rujukan CLI](../reference/cli-reference.md#capture-modes-recorders--offline-reprocess).

## Memadankan penderia cahaya DAQ

Prviu yang diperbetulkan berdasarkan pantulan dan pencahayaan memerlukan data cahaya ke bawah daripada penderia DAQ (dihubungkan di tab **Penderia Cahaya**):

* **Baris array**di bar sisi memaparkan pil**"DAQ · on/off"** — *hidup* apabila penderia cahaya peringkat array ditetapkan **atau** mana-mana kamera ahli mempunyai penderia sendiri; petunjuk alatkannya menyenaraikan dengan tepat penderia mana yang menyalurkan ke kamera mana.
* Tetapkan di seluruh susunan dalam tetapan susunan → menu lungsur **Penderia Cahaya Sekitar**→**Penderia Cahaya**. Pilihan ini kekal dengan projek, disebarkan ke setiap kamera ahli, dan kamera individu masih boleh menimbalinya dengan penderia mereka sendiri.
* Bar status di bawahnya melaporkan keadaan langsung: **Padam**→ "Menunggu spektrum pertama…" →**"Aktif — semua kamera dalam susunan telah diperbetulkan pencahayaannya"** → atau, jika tiada spektrum baharu diterima dalam 3 saat terakhir, notis lapuk — bacaan terakhir terus digunakan (bacaan tidak pernah tamat tempoh pada laluan tangkapan).

Apabila sensor telah ditetapkan: jenis eksport Reflektan menjadi tersedia, pratonton langsung diperbetulkan mengikut pencahayaan, pendedahan automatik ramalan boleh menggunakan spektrum, dan setiap tangkapan reflektansi menulis bacaan DAQ yang sebenarnya digunakan sebagai fail **`.daq`** dalam folder `daq/` supaya tangkapan boleh diproses semula kemudian. Di sampingnya, `.daq.cameras.json` menamakan kamera-kamera yang bingkai mereka diterangkan oleh bacaan tersebut; apabila diproses semula, Chloros memadankan bacaan itu hanya dengan imej daripada kamera-kamera tersebut.

## `array-connect` Pilihan CLI



| Bendera | Lalai | Keterangan |
| --- | --- | --- |
| `--serials SN1,SN2,…` | kenal pasti secara automatik semua kamera LATTICE (perlu ≥2) | **Siri pertama ialah MASTER.** |
| `--line {Line0,Line2,Line3}` | `Line2` | GPIO talian selari. |
| `--target-fps F` | auto | Kadar tembakan pencetus Utama. |
| `--binning {1,2,4}` | auto | Penyusunan perkakasan. Auto ialah keseluruhan sensor (tiada penyusunan) setiap kali mana-mana kadar bingkai membawanya; connect tidak pernah menyusun hanya untuk mengekalkan kadar. |
| `--packed {auto,on,off}` | `auto` | [Pengangkutan 12-bit terbungkus](#packed-12-bit-transport): `auto` hanya memadatkan apabila ia membantu dan hos mampu menyahpadatkan. |
| `--force-tier {sim-capture-sim-emit, sim-capture-ftd-stagger, slip-emit-and-capture}` | auto | Pengecualian pakar bagi pemilih lapisan penyegerakan. |
| `--wire-ceiling-mbps MB_PER_S` | dikesan secara automatik | Belanjawan wayar hos dalam MB/s — bentuk **CLI**bagi medan **Belanjawan Wayar**. Kurangkan jika susunan melaporkan bingkai yang rosak. Disimpan bersama projek, jadi penyambungan semula kemudian akan memulihkannya. |
| `--no-recommend` | off | Langkau langkah analisis rangkaian. |
| `--no-ptp` | off | Nyahdayakan PTP (cap masa merentas kamera maka tidak dapat dibandingkan). |

`lattice array-list`, `array-status`, dan `array-disconnect` menguruskan sesi berterusan. Rujukan penuh subperintah, termasuk penjajaran (`align-calibrate` / `align-apply`) dan alat rangkaian, terdapat dalam [Rujukan [CLI] § chloros-cli lattice](../reference/cli-reference.md#chloros-cli-lattice); setara SDK (`connect_array`, `ArraySession`, `attach_array`, `analyze_array_network`) terdapat dalam [Rujukan [SDK]](../reference/sdk-reference.md). Daripada Python, bajet wayar ialah `connect_array(..., wire_ceiling_mbps=120)`, dan pembahagian rosak langsung/tidak pernah tiba terdapat pada [`/api/camera/array/<id>/capability`](../reference/sdk-reference.md#array-health--which-subsystem-is-losing-frames).
