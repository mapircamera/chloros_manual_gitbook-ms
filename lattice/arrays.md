# Susunan Pelbagai Kamera

Susunan LATTICE ialah dua atau lebih kamera LATTICE yang disambungkan sebagai satu unit bersepadu yang diselaraskan. Satu kamera adalah **master**: ia memancarkan denyut picu GPIO perkakasan pada talian penyegerakan bersama (lalai**Line2**), jadi setiap ahli terdedah pada saat yang sama. Chloros menambah penyegerakan masa PTP, pratonton langsung (jubin setiap kamera atau komposit berbilang jalur yang diselaraskan), dan tangkapan bersepadu — setiap pusingan tangkapan menghasilkan satu**kumpulan bingkai** di mana semua kamera berkongsi cap masa dan ID bingkai yang sama (dilaporkan sebagai `fid:N` pada keluaran tangkapan).

Susunan adalah cara kamera mono (M3M) menghasilkan indeks vegetasi — satu kamera menyumbang satu jalur, dan susunan menyelaraskan mereka menjadi timbunan berbilang jalur. Lihat [Kamera Mono &amp; Indeks Vegetasi](mono-indices.md).

Terdapat tiga cara yang setara untuk menyambungkan satu array, dan kesemuanya menjalankan aliran &quot;smart-prep&quot; yang sama:

| Antara muka | Titik kemasukan |
| --- | --- |
| GUI | Tab Kamera → **Sambungkan Array** (butang biru) |
| CLI | `chloros-cli lattice array-connect --serials SN1,SN2,…` (siri pertama = induk) |
| Python SDK | `connect_array(serials=[…])` → `ArraySession` (siri pertama = induk) |

Smart-prep menjalankan, mengikut urutan: prob kebolehan rangkaian (ping ICMP DF + prob GVSP), pemilihan lapisan penyelarasan, pengecilan automatik saiz bingkai untuk muat pada talian, pengaktifan PTP, pemilihan automatik format piksel bagi setiap kamera, penaburan pendedahan automatik daripada keadaan tersimpan setiap kamera, dan konfigurasi pencetus GPIO pada Line2.

{% hint style="info" %}
Kamera mesti boleh dihubungi pada pautan sebelum mana-mana perkara ini berfungsi — lihat [Menyambungkan Kamera](connecting.md) untuk penemuan, penentuan alamat, dan muat turun penentukuran sambungan pertama. Untuk rig berbilang kamera, tetapan receive-ring kad rangkaian hos (NIC) adalah sama penting dengan kelajuan pautan; jadual lengkap simptom→penyelesaian terdapat dalam [Rujukan CLI § Penyediaan &amp; Penalaan NIC Hos](../reference/cli-reference.md#host-nic-setup--tuning-lattice-arrays).
{% endhint %}

## Dialog Sambungkan Susunan

Tab Kamera → **Sambungkan Susunan**membuka wizard tiga langkah:**Pilih → Mod Paparan → Tetapan**.

### Langkah 1 — Pilih master dan slave



<!-- SCREENSHOT-NEEDED: Array Connect dialog, Select scene, with 3-4 LATTICE cameras discovered. Table showing Camera / Serial / IP / Master radio / Slave checkbox columns, with the green "GPIO master detected — selections pre-populated" probe banner visible above the table. -->Dialog ini mengimbas rangkaian sebaik sahaja ia dibuka (&quot;Mengimbas rangkaian...&quot;), kemudian mengesan pendawaian pencetus GPIO (&quot;Mengesani pendawaian GPIO...&quot;). Anda memerlukan sekurang-kurangnya **2 kamera** untuk membina satu array.

Pemeriksa pendawaian akan mengisi pemilihan peranan secara automatik apabila boleh, dan melaporkan salah satu daripada tiga sepanduk:

| Sepanduk | Maksud |
| --- | --- |
| &quot;Pemegang GPIO dikesan — pemilihan diisi secara automatik&quot; (hijau) | Pengesan telah menemui topologi pencetus; kotak semak radio induk dan hamba sudah diisi. |
| &quot;Tiada induk dikesan - semak kabel GPIO&quot; (jingga) | Tiada kamera melihat denyut pencetus; semak pendawaian penyelarasan. Anda masih boleh memilih peranan secara manual. |
| &quot;Tiada Kabel Sinkronisasi: {serial}&quot; (oren) | Kamera yang disenaraikan tidak mempunyai kabel sinkronisasi yang disambungkan. |

Jadual kamera mempunyai lajur **Kamera / Siri / IP / Utama (radio) / Hamba (kotak semak)**:

* Pilih tepat **satu master**dan**satu atau lebih slave**. Mengklik radio master semasa sekali lagi akan membersihkannya.
* Kamera yang ditandakan **&quot;Tiada Kabel Sinkronisasi&quot;** tidak boleh dipilih sebagai slave — seorang slave tanpa pendawaian pencetus akan menunggu pada talian sinkronisasi selama-lamanya dan memberikan siaran yang mati. Sambungkan kamera tersebut sebagai kamera berdiri sendiri sebaliknya.
* Kamera yang sudah disambungkan sebagai berdiri sendiri *tidak* dilumpuhkan: penyambungan ke dalam array membebaskan sesi berdiri sendiri dan membuka semula kamera dalam array tersebut.

**Seterusnya: Mod Paparan →**diaktifkan setelah satu master dan sekurang-kurangnya satu slave dipilih.**Rescan** akan menjalankan semula penemuan dan pengesanan wayar.

{% hint style="warning" %}
**Cancel** dilumpuhkan semasa imbasan atau pengesanan sedang dijalankan — membatalkan di tengah-tengah pengesanan boleh menyebabkan kamera terhenti SDK pada firmware kamera LATTICE. Tunggu sehingga pemutar selesai.
{% endhint %}

### Langkah 2 — Mod Paparan



<!-- SCREENSHOT-NEEDED: Array Connect dialog, Display Mode scene, showing the two selectable cards ("Separate Cameras" and "Combined Cameras") with Combined selected/highlighted as the default. -->| Mod | Apa yang anda dapat |
| --- | --- |
| **Kamera Berasingan** | Satu jubin langsung bagi setiap kamera, semuanya dicetuskan serentak supaya bingkai kekal selari. Setiap kamera mengekalkan warna dan tetapan sendiri. |
| **Kamera Digabungkan** *(laluan lalai)* | Satu jubin tunggal yang memaparkan komposit jalur pelbagai yang diselaraskan NDVI/indeks. Kamera berkongsi warna susunan. |

Mod paparan hanya mengubah persembahan pratonton langsung — tingkah laku tangkapan adalah sama dalam kedua-duanya.

### Langkah 3 — Tetapan Susunan dan hasil

<!-- SCREENSHOT-NEEDED: Array Connect dialog, Settings scene, healthy state: left column with ROI / Binning / Pin resolution / Trigger Rate controls, right "Projected Outcome" column showing green "Simultaneous capture" tier, an fps range, the NIC line, the "Sim-emit burst" line, and the "Wire budget" line with a checkmark. -->yang dijangka

Apabila memasuki babak ini Chloros meminta **cadangan**dan secara automatik menerapkan gabungan ROI + binning yang sesuai dengan cincin penerima NIC anda (ia lebih memilih binning berbanding pemotongan ROI, kerana binning mengekalkan keseluruhan medan pandangan). Setiap perubahan yang anda buat akan menjalankan semula analisis secara langsung dan mengemas kini panel**Keputusan Terproyeksikan** di sebelah kanan.

Lajur kiri — tetapan:

| Kawalan | Pilihan | Lalai | Nota |
| --- | --- | --- | --- |
| **ROI (Medan Pandangan)** | Penuh (2048×1536) / Separuh (1024×768) / Suku (512×384) | Penuh | Potongan sensor: Potongan separuh/suku ke kawasan yang lebih kecil pada pitch piksel asal. |
| **Binning** | 1× / 2× (jumlah 2×2) / 4× (jumlah 4×4) | 1× | Binning perkakasan: 2×2 = FoV penuh pada suku kos wayar; 4×4 = FoV penuh pada 1/16. Tersembunyi jika kamera tidak menyokong binning. |
| **Imej sisi wayar** (baca keluar) | — | — | Lebar×tinggi selepas binning yang sebenarnya dihantar pada wayar, dipotong kepada gandaan 16 (minimum 64). |
| **Resolusi pin**| kotak semak | mati | Chloros biasanya mengaktifkan binning secara automatik semasa sambungan apabila kadar yang dijangka jatuh di bawah**

1.5 fps**. Pinning mengekalkan saiz bingkai yang anda pilih dan menerima kadar yang lebih rendah — dan menukar konfigurasi yang melebihi had menjadi penolakan sambungan secara keras dan bukannya penurunan automatik. |
| **Kadar Picu** | 0.5–60 fps, langkah 0.1 | kosong = auto | Kadar picu peluru master. Biarkan kosong untuk membolehkan Chloros mengeluarkannya. |
| **Bajet Wayar**| 20–2000 MB/s, langkah 10 | kosong = auto | Berapa banyak yang hos sebenarnya boleh serap, dalam MB/s —**nombor tunggal yang menjadi asas bagi keseluruhan peruntukan tatasusunan.**Dikesan secara automatik daripada penyesuai rangkaian. Turunkan jika susunan melaporkan bingkai rosak: nilai yang dikesan melebihi keupayaan penyesuai USB dan suis kongsi. Mengubahnya akan menjalankan semula unjuran secara langsung. |

Lajur kanan — **Keputusan Unjuran**:

* **Tingkatan Sinkronisasi** — &quot;Rakaman serentak&quot; (hijau), &quot;Rakaman serentak (pancaran selang-seli FTD)&quot; (hijau), &quot;Rakaman selang-seli (lencongan 100 ms)&quot; (jingga), atau &quot;Konfigurasi terlalu besar&quot; (merah).
* **projeksi fps** — dipaparkan sebagai julat (&quot;lemah → terang&quot;), kerana kadar susunan selari dihadkan oleh pendedahan kamera yang paling perlahan.
* **baris NIC** — kelajuan pautan dan throughput berterusan (&quot;NIC {mbps} Mbps · berterusan {N} MB/s&quot;).
* **Semakan letupan Sim-emit** — sama ada NIC hos boleh menerima penyerapan cincin satu letupan serentak daripada semua kamera (&quot;Letupan Sim-emit: X MB · cincin NIC boleh guna: Y MB ✓/✗&quot;).
* **Semakan bajet wayar** — permintaan agregat keadaan-seimbang berbanding had wayar selamat-langgaran (&quot;Bajet wayar: {permintaan} MB/s permintaan oleh {n} kamera · had {had} MB/s ✓/✗ terlebih langganan&quot;).
* **&quot;Max kamera pada wayar ini: {n} — ditetapkan oleh had minimum jalur lebar bagi setiap kamera, jadi penyusutan tidak akan meningkatkannya.&quot;** — dipaparkan apabila anda hampir (atau melebihi) had maksimum bilangan kamera.
* **&quot;RAIH BOLEH TERJATUH pada tetapan ini.&quot;** — amaran merah dengan sebab backend, serta senarai penghalang dan cadangan pembaikan berwarna biru (&quot;Untuk memuatkan tatasusunan ini pada rangkaian&quot; / &quot;Untuk membuka kunci tangkapan serentak&quot;).**Terapkan &amp; Sambung** dikunci sehingga terdapat unjuran, dan labelnya memberitahu anda sebab ia menolak:

| Label butang | Maksud | Apa yang sebenarnya membantu |
| --- | --- | --- |
| &quot;Menganalisis...&quot; | Analisis masih dijalankan. | Tunggu. |
| **&quot;Terlalu banyak kamera untuk rangkaian ini&quot;**| Susunan melebihi had rangkaian (semakan agregat gagal). | Kurangkan bilangan kamera, gunakan bingkai jumbo dari hujung ke hujung, atau gunakan NIC yang lebih pantas.**ROI yang lebih kecil TIDAK akan membantu** — lihat di bawah. |
| **&quot;Kurangkan ROI untuk aktifkan&quot;** | Rangka akan terbuang pada tetapan ini (semakan burst/ring gagal). | Kurangkan ROI, naikkan binning, atau baiki cincin penerimaan NIC. |



<!-- SCREENSHOT-NEEDED: Array Connect dialog, Settings scene, over-subscribed state: red "Wire budget ... over-subscribed" line, the "Max cameras on this wire" hint, and the Apply button reading "Too many cameras for this network". Reproduce by configuring more cameras than the 1 GbE ceiling (e.g. 7+ cams at 1500 MTU) or with CHLOROS-simulated models via `lattice analyze-array`. -->Semasa menyambung, panel muat turun penentukuran hijau mungkin akan muncul dengan bar kemajuan bagi setiap siri: pada kali pertama kamera disambungkan pada mesin, Chloros akan menarik pakej penentukuran kilang bersaiz ~3.8 MB daripada kamera melalui GigE (kira-kira 70 saat bagi setiap kamera). Kamera yang disimpan dalam cache tidak akan memaparkan panel ini. Lihat [Menyambungkan Kamera](connecting.md).

## Lebar jalur: berapa banyak kamera yang boleh muat

Kebolehan satu susunan untuk memikul beban adalah sifat wayar itu sendiri, bukan Chloros, jadi angka perancangan terdapat dalam manual perkakasan: **[Perancangan Lebar Jalur Susunan](https://mapir.gitbook.io/lattice-camera/setup/array-bandwidth-planning)**.

Apa yang dilakukan oleh Chloros dengan maklumat tersebut: dialog sambungan menjalankan pemeriksaan rangkaian, menganggar kadar bingkai yang boleh dicapai, dan memilih tahap yang sesuai. Jika susunan melebihi had wayar, ia akan menolak sambungan daripada terus menjatuhkan paket secara senyap — lihat panel hasil anggaran yang diterangkan di atas.

## Apabila bingkai hilang

Sebuah kamera boleh tiada daripada kumpulan yang diterbitkan atas dua sebab yang berbeza sama sekali,
dan ia memerlukan penyelesaian yang bertentangan. Chloros mengiranya secara berasingan dan bukannya melaporkan satu
angka &quot;tidak lengkap&quot; yang tidak menyatakan kedua-duanya:

| Apa yang berlaku | Apa maksudnya | Di mana hendak dilihat |
| --- | --- | --- |
| **Rusak**— bingkai tiba dan mempunyai struktur yang rosak | Kehilangan paket GVSP di laluan rangkaian |**Bajet wayar**, cincin penerima NIC, bingkai jumbo, suis |
| **Tidak pernah tiba**— tiada bingkai pun sampai | Kamera tidak mencetuskan, atau tiada apa-apa yang keluar daripadanya |**Kabel penyegerakan M8**, garisan penyegerakan, sama ada setiap ahli telah bersedia |

Pemisahan dinilai semula setiap 10 saat sementara susunan sedang mengalir. Di atas 5% ia
direkodkan dengan kedua-dua nombor dinyatakan, dan setiap tampanan rosak dilaporkan buat pertama kali ia
berlaku bagi setiap kamera, kemudian dikumpulkan semula sekali setiap minit supaya sesi panjang kekal boleh dibaca.

**Sisipan rosak dengan sifar yang tidak pernah tiba bermakna pencetus dan sela kabel adalah sempurna**dan setiap sisipan yang hilang berada di laluan rangkaian. Penyelesaiannya ialah menurunkan**Bajet Wayar** dan menyambung semula.

{% hint style="warning" %}
**Mengurangkan kadar pencetus tidak membantu dengan bingkai rosak.** Penjadualan paket kamera ditulis sekali, semasa sambungan. Mengurangkan kadar pencetus mengubah kekerapan letusan berlaku, bukan kelajuan letusan itu sendiri ke atas wayar. Pada rig 4-kamera yang diukur, a
Penurunan kadar pencetus 5× tidak mengubah apa-apa, manakala menurunkan bajet talian daripada 240 kepada 200 MB/s menjadikan rig yang sama berkurang daripada 10.4% bingkai rosak kepada sifar.
{% endhint %}

Susunan yang sedang berjalan tidak dapat merancang semula dirinya — nyahpasang dan pasang semula supaya pemilih masa sambung dapat berfungsi berdasarkan bajet baru.

### Penyesuai rangkaian USB dihadkan pada 200 MB/s

Penyesuai Ethernet USB mengiklankan kadar pautan *Ethernet*nya, tetapi apa yang sebenarnya
boleh dijanjikannya terhad oleh bas USB dan pemandunya. Dongle USB 10GbE pernah diiktiraf mempunyai throughput kira-kira 1000 MB/s — satu angka yang tidak pernah diukur oleh sesiapa pun — dan menetapkan kelajuan empat kamera mengikut ruang lebihan maya itu telah merosakkan 6–18% bingkai sementara susunan itu masih melaporkan kadar bingkai sasaran yang sihat. Penyambung yang disambungkan melalui USB kini dihadkan pada **200 MB/s**. Had ini adalah mutlak dan bukannya peratusan, kerana hadnya adalah pada bus: penyambung USB 1 GbE memperoleh kira-kira 80 MB/s dan tidak terjejas.

Jika hos anda benar-benar lebih pantas daripada had tersebut, naikkan **Wire Budget** untuk mencerminkannya.

## Penyelarasan masa PTP

*Penyelarasan* bingkai datang daripada pencetus perkakasan; **PTP** (IEEE 1588 PTPv2) menyediakan *cap masa* yang setara di setiap peranti. Ia diaktifkan secara lalai semasa menyambungkan array:

* Backend hos **Chloros** menjalankan grandmaster PTP. Kamera LATTICE dan penderia cahaya DAQ-E menjadi slave kepadanya dalam domain 0, jadi cap masa imej dan spektra DAQ mendarat pada satu jam (~1 ms).
* `--no-ptp` (CLI) menyahaktifkannya untuk kerja bangku — cap masa merentas kamera kemudian **tidak** boleh dibandingkan.
* Periksa kesihatan penyelarasan dengan CLI:

```bash
chloros-cli time-sync status     # grandmaster state, clock identity
chloros-cli time-sync peers      # slaves seen (cameras + DAQ-E sensors)
chloros-cli time-sync cameras    # per-camera PtpStatus / PtpOffsetFromMaster / PtpMeanPathDelay
```

Tab Kamera itu sendiri tidak mempunyai penunjuk PTP; permukaan penyegerakan bagi setiap kamera memaparkan **Peranan**(Master/Slave) yang hanya boleh dibaca,**Garisan Penyegerakan**, dan lapisan Keupayaan susunan. Status PTP DAQ-E dipaparkan dalam butiran sensor pada tab Penderia Cahaya.

## Paparan

<!-- SCREENSHOT-NEEDED: Cameras tab with a connected combined array: sidebar showing the ARRAY row (color badge, array name, "DAQ · on" pill) with indented member camera rows, and the main area showing the combined index composite tile with the LUT-colored NDVI render, top-left array name pill, and top-right fps readout. -->susunan secara langsung

Kawasan aliran utama menawarkan dua susun atur (tukar di bar atas): **paparan grid**(setiap jubin adalah sel; seret untuk menyusun semula apabila gembok grid tidak dikunci) dan**paparan senarai**(susunan penuh lebar di atas, satu kamera aktif di bawah). Pemacu gelangsar**Zoom Suapan** menetapkan saiz jubin; pada lebar sel di bawah 200 px, lapisan nama/fps akan tersembunyi secara automatik.**Mod berasingan** memaparkan satu jubin bagi setiap kamera. Setiap jubin memaparkan lapisan:

* nama kamera (atas kiri),
* **bacaan fps** (atas-kanan) — ini adalah *kadar tangkapan sebenar* kamera yang dilaporkan oleh backend, bukan kadar tinjauan pratonton ( pratonton langsung dihadkan pada 30 fps tanpa mengira kadar tangkapan),
* titik status — hijau (siaran) / jingga (memuat) / merah (ralat),
* pemutar bingkai lapuk **(stale-frame spinner)** apabila tiada bingkai baru tiba selama 2 s — normal untuk ~5 s selepas sebarang sambungan/putus sambung sementara backend menyimbangkan semula bajet jalur rentas kamera.**Mod gabungan**memaparkan satu jubin komposit: backend menyingkirkan bayaran, menapis, menyelaras, menyingkirkan hingar, menukar kepada sinaran per-band (ditambah pantulan DLS apabila penderia cahaya diikat), menilai ungkapan indeks tatasusunan, terapan LUT, dan menstrimkan hasil sebagai MJPEG. Sehingga bingkai sejajar pertama dipaparkan, jubin menerangkan statusnya: &quot;Menyiapkan susunan…&quot;, &quot;Kalibrasi penjajaran…&quot;, &quot;Menunggu bingkai pertama…&quot;, atau — jika bajet percubaan semula penjajaran automatik (~30 saat) telah habis — &quot;Penjajaran diperlukan&quot; dengan butang**Kalibrasi penjajaran**.

Fakta berguna mod gabungan:

* Komposit didaftarkan pada bingkai kamera **utama**. Penjejakan AE-ROI dan pengukuran titik pada komposit adalah tepat untuk kamera utama dan anggaran untuk kamera hamba; gunakan**Paparan Berpisah** (tetapan susunan → &quot;Tunjukkan kamera ahli&quot;) untuk jubin piksel tepat bagi setiap kamera tanpa membuka sambungan kamera tambahan.
* **Lapisan Paparan**(tetapan susunan; lalai dimatikan) membolehkan anda memilih lapisan latar hadapan dan latar belakang — mana-mana kamera ahli atau**Indeks**. Dengan latar hadapan = Indeks, piksel di luar LUT Min/Max akan memaparkan lapisan latar belakang.
* **Resolusi Render* (lalai 720p) menetapkan ketinggian siaran langsung *dan* saiz eksport komposit yang disimpan. Imej setiap kamera sentiasa dieksport pada resolusi penuh.
* Penyelarasan dikira bagi setiap sesi dan tidak pernah disimpan — lihat bahagian penyelarasan pada tetingkap tetapan susunan untuk sisa RMS dan butang Kalibrasi Semula.

## Pengambilan: pemantauan vs analisis

Permukaan pengambilan susunan dibahagikan dengan jelas kepada **darjah pemantauan**(rekod apa yang anda lihat) dan**darjah analisis** (rekod data mentah, kalibrasi kemudian):

| Aliran kerja | Gred | Apa yang disimpannya | GUI | CLI |
| --- | --- | --- | --- | --- |
| **Rakaman**(gambar pegun) | Analisis | Satu kumpulan bingkai diselaraskan bagi setiap pusingan; fail bagi setiap kamera pada setiap tahap eksport yang dipilih (mentah/debayered/radiance/refleksi/pra-tonton/indeks) + fail sampingan `.daq` | butang**Rakam Semua** + Tetapan Rakaman | `lattice array-capture` |
| **Rakam video indeks** | Pemantauan | Komposit indeks gabungan secara langsung seperti yang dipaparkan — 8-bit, resolusi pratonton, LUT terbina dalam; memerlukan aliran langsung dibuka | ● Rakam video indeks (susunan gabungan) | `lattice array-record` |
| **Letupan mentah → bina video**| Analisis | Bingkai sensor mentah pada kadar tangkapan penuh + manifest + `.daq`, kemudian rekonstruksi luar talian menjadi video radiasi / pantulan / indeks yang dikalibrasi, diselaraskan waktunya dengan bacaan DAQ | ⦿ Rakam letupan mentah →**Bina video** | `lattice array-burst` → `lattice array-build-video` |

Peraturan am: jika piksel akan digunakan untuk *pengukuran*, gunakan tangkapan atau burst (darjah analisis); jika anda hanya perlu *menonton atau menunjukkan* apa yang dilihat oleh susunan, rakam video indeks (darjah pemantauan).

### Tetapan Tangkapan (GUI)



<!-- SCREENSHOT-NEEDED: Capture Settings pane (gear next to Capture All) with a connected array: capture-mode buttons (Single/Continuous/Interval), the bulk export-type toggle row, the Fastest Capture toggle, and the per-array group card showing the Aligned checkbox and the "Record index video" / "Record raw burst" buttons. -->Gerezan di sebelah **Rakam Semua** membuka tetingkap Tetapan Rakaman (memerlukan projek yang terbuka — rakaman disimpan ke dalamnya):

* **Mod Rakaman**:**Tunggal**(satu pusingan) /**Berterusan**(berturut-turut; dibataskan oleh bilangan tangkapan, lalai 1, atau tempoh, lalai 10 s) /**Interval** (timelapse: N tangkapan setiap selang X untuk jumlah Y, lalai 1 setiap 5 s untuk 1 minit).
* **Jenis Eksport bagi setiap kamera**: Raw, Debayered, Radiance, Reflectance, Pratonton, Indeks — semua yang terpakai diaktifkan secara lalai. Radiance/Reflectance disembunyikan bagi kamera penapis**RGB**;**Reflectance hanya akan muncul apabila kamera mempunyai penderia cahaya DAQ** (tersendiri atau diwarisi daripada susunan); Indeks memerlukan ungkapan indeks yang dikonfigurasikan.
* **Disesuaikan**(per susunan, lalai**hidup**): memutar eksport ahli mengikut profil penjajaran susunan supaya eksport diselaraskan piksel. Mentah sentiasa kekal tidak dipusing tetapi membawa transformasi dalam metadata.
* **Perekodan Terpantas* (togol): hanya mentah + bacaan DAQ yang ditetapkan + komposit indeks gabungan percuma, melangkau matematik penentukuran pada masa perekodan untuk kadar maksimum — bina semula radiasi/pantulan/indeks kemudian daripada `.daq` yang disimpan.
* Pilihan kekal bersama projek. Kamera yang disembunyikan atau ditangguhkan akan dilangkau.

Setara CLI (titik akhir backend yang sama, semantik yang sama):

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

Pemampatan  untuk tangkapan ialah `deflate` (tanpa kehilangan, lalai) atau `none` — jadual bendera penuh, susun atur folder tangkapan, dan peraturan pemprosesan semula terdapat dalam [Rujukan CLI](../reference/cli-reference.md#capture-modes-recorders--offline-reprocess).

## Memadankan penderia cahaya DAQ

Prviu yang diperbetulkan berdasarkan pantulan dan pencahayaan memerlukan data cahaya ke bawah daripada penderia DAQ (disambungkan di tab **Penderia Cahaya**):

* Bar sisi **baris array**memaparkan pil**&quot;DAQ · on/off&quot;** — *hidup* apabila penderia cahaya peringkat array ditetapkan **atau** mana-mana kamera ahli mempunyai penderia tersendiri; petua alatkannya menyenaraikan dengan tepat penderia mana yang menyalurkan ke kamera mana.
* Tetapkan di seluruh tatasusunan dalam tetapan tatasusunan → **Penyedar Cahaya Sekitar**→ menu lungsur**Penyedar Cahaya**. Pilihan ini kekal dengan projek, disebarkan ke setiap kamera ahli, dan kamera individu masih boleh menimbalinya dengan penyedar mereka sendiri.
* Baris status di bawahnya melaporkan keadaan langsung: **Padam**→ &quot;Menunggu spektrum pertama…&quot; →**&quot;Aktif — semua kamera dalam susunan telah diperbetulkan pencahayaannya&quot;** → atau, jika tiada spektrum baharu diterima dalam 3 saat terakhir, notis lama — bacaan terakhir terus digunakan (bacaan tidak pernah tamat tempoh pada laluan tangkapan).

Apabila sensor telah ditetapkan: jenis eksport Reflektan menjadi tersedia, pratonton langsung diperbetulkan mengikut pencahayaan, pendedahan automatik ramalan boleh menggunakan spektrum, dan setiap tangkapan reflektansi menulis bacaan DAQ yang sebenarnya digunakan sebagai **fail sampingan `.daq`** di sebelah imej supaya tangkapan boleh diproses semula kemudian.

## `array-connect` Pilihan CLI

| Bendera | Lalai | Keterangan |
| --- | --- | --- |
| `--serials SN1,SN2,…` | mengesan secara automatik semua kamera LATTICE (perlu ≥2) | **Siri pertama adalah MASTER.** |
| `--line {Line0,Line2,Line3}` | `Line2` | GPIO sync line. |
| `--target-fps F` | auto | Kadar picuan pencetus Master. |
| `--binning {1,2,4}` | auto | Penyusunan perkakasan. |
| `--force-tier {sim-capture-sim-emit, sim-capture-ftd-stagger, slip-emit-and-capture}` | auto | Pengambilalihan pakar bagi pemilih lapisan penyelarasan. |
| `--wire-ceiling-mbps MB_PER_S` | dikesan secara automatik | Belanjawan wayar hos dalam MB/s — bentuk **Belanjawan Wayar**yang**CLI**. Kurangkannya jika susunan melaporkan bingkai yang rosak. Disimpan bersama projek, jadi penyambungan semula kemudian akan memulihkannya. |
| `--no-recommend` | off | Langkau langkah analisis rangkaian. |
| `--no-ptp` | off | Nyahdayakan PTP (cap masa merentas kamera maka tidak dapat dibandingkan). |

`lattice array-list`, `array-status`, dan `array-disconnect` mengurus sesi berterusan. Rujukan penuh subperintah, termasuk penjajaran (`align-calibrate` / `align-apply`) dan perisian rangkaian, terdapat dalam Rujukan [CLI § chloros-cli lattice](../reference/cli-reference.md#chloros-cli-lattice); setara SDK (`connect_array`, `ArraySession`, `attach_array`, `analyze_array_network`) terdapat dalam [Rujukan SDK](../reference/sdk-reference.md). Daripada Python bajet wayar ialah `connect_array(..., wire_ceiling_mbps=120)`, dan pembahagian korup langsung/tidak pernah tiba adalah pada [`/api/camera/array/<id>/capability`](../reference/sdk-reference.md#array-health--which-subsystem-is-losing-frames).
