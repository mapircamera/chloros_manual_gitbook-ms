---
metaLinks: {}
---

# Memulakan

<div data-full-width="false"><figure><img src=".gitbook/assets/chloros_logo_transparent.png" alt=""><figcaption></figcaption></figure></div>

Chloros ialah aplikasi perisian daripada [MAPIR](https://www.mapir.camera) untuk memproses imej multispektral, mengawal perkakasan MAPIR secara langsung, dan merekod data sensor. Chloros 1.3.0 menyokong keseluruhan keluarga produk MAPIR:

* **Kamera Survey3** — memproses tangkapan RAW+JPG menjadi peta pantulan dan indeks vegetasi yang dikalibrasi. Lihat [Kamera Disokong](supported-cameras.md).
* **Kamera LATTICE** — sambungkan modul kamera multispektral GigE secara langsung, secara bersendirian atau sebagai susunan multi-kamera yang diselaraskan: pratonton, rakam, dan proses menjadi produk radiasi dan pantulan yang dikalibrasi. Lihat [seksyen LATTICE](lattice/README.md).
* **Penderia cahaya DAQ** — penderia spektral DAQ-U (USB), DAQ-M (Bluetooth), dan DAQ-E serta DAQ-E-S (Ethernet): spektra kalibrasi secara langsung, rekod `.daq`, dan pencahayaan ke bawah untuk pemprosesan pantulan. Lihat [seksyen DAQ](daq/README.md).

{% hint style="success" %}
**Apa yang Baru dalam Chloros 1.3.0**: Pembantu AI boleh mengendalikan Chloros melalui `chloros-mcp`, dan CLI, SDK dan `chloros-mcp` menawarkan ciri yang sama. Produk indeks LATTICE telah dipertingkatkan, susunan penuhnya lebih pantas dan berfungsi pada mana-mana rangkaian, dan backend terus berjalan antara arahan. Lihat senarai Perkara Baharu di bawah, dan [Muat Turun](download.md) untuk pemasang.
{% endhint %}

{% hint style="info" %}
**Menggunakan Chloros dengan pembantu AI?** Manual ini dibina untuk tujuan itu. Arahkan pembantu anda ke:

* `https://mapir.gitbook.io/chloros/llms.txt` — indeks boleh dibaca mesin bagi setiap halaman.
* Mana-mana halaman sebagai Markdown mentah — sambungkan `.md` kepada URLnya (contohnya `https://mapir.gitbook.io/chloros/reference/cli-reference.md`).
* Rujukan [CLI](reference/cli-reference.md) dan Rujukan [SDK](reference/sdk-reference.md) — halaman rujukan nilai tepat yang lengkap, ditulis untuk penggunaan LLM.

Contoh arahan: *"Bacalah https://mapir.gitbook.io/chloros/reference/cli-reference.md, kemudian tulis skrip yang log masuk dan memproses folder ~/flights/flight_001 menjadi GeoTIFF reflektansi + NDVI."*

Atau jalankan Chloros secara langsung: `chloros-mcp`, sebuah pelayan MCP yang dipasang dengan SDK, berfungsi dengan Claude Desktop, Claude Code, Cursor dan klien MCP lain.

Panduan penuh: [Menggunakan Chloros dengan Pembantu AI](ai-assistants.md).
{% endhint %}

***

## Apa yang Baru dalam Chloros 1.3.0

* **Penolong AI boleh mengendalikan Chloros.** `chloros-mcp`, sebuah pelayan MCP yang dipasang dengan Python SDK, membolehkan Claude Desktop, Claude Code, Cursor dan klien MCP lain memproses imej dan menjalankan kamera LATTICE, tatasusunan dan penderia cahaya DAQ. Lihat [Menggunakan Chloros dengan Penolong AI](ai-assistants.md).
* **CLI, SDK dan `chloros-mcp` menawarkan ciri yang sama.** Hanya log masuk, penyediaan DAQ-E dan pelayan DAQ langsung kekal dengan CLI, untuk keselamatan. Beberapa skrip 1.2.0 memerlukan perubahan: lihat **Perubahan dalam 1.3.0** di bahagian atas [Rujukan CLI](reference/cli-reference.md) dan [Rujukan SDK](reference/sdk-reference.md).
* **Produk indeks LATTICE yang dipertingkatkan.** NDVI dan produk indeks lain yang dieksport daripada tangkapan LATTICE dipertingkatkan, dalam setiap mod pemprosesan.
* **Pilihan pemasang Windows.**Di bawah**Tetapan Sistem**, pemasang menawarkan**Aktifkan laluan panjang Windows**dan**Kecualikan backend Chloros daripada imbasan Microsoft Defender**, kedua-duanya dipilih secara lalai. Yang pertama membenarkan folder projek berada lebih dalam daripada 260 aksara; yang kedua menghentikan pengimbasan masa nyata daripada memperlahankan tangkapan kamera (kira-kira 30% daripada kadar tangkapan pada kadar tinggi). Pemuat kini juga menambah folder CLI ke `PATH` hanya sekali, dan membuang entri duplikat yang ditinggalkan oleh pemuat sebelum ini. Lihat [Muat Turun](download.md).
* **Folder projek yang terlalu dalam ditolak pada peringkat awal.** Apabila laluan panjang Windows dilumpuhkan (tetapan lalai Windows), folder projek yang terlalu dalam melebihi had laluan 260 aksara akan ditolak sebelum sebarang proses dijalankan, bersama maklumat tentang folder projek terpanjang yang sesuai dan cara untuk mengaktifkan laluan panjang.** Sebelum ini, pelaksanaan seperti itu tidak menghasilkan sebarang produk. Dengan laluan panjang diaktifkan, produk dalam folder yang sangat dalam kini mengekalkan metadata mereka (tag GPS dan XMP).
* **Pemampatan topeng.** Apabila pencampuran semula jalur produk sinaran atau pantulan LATTICE menghasilkan nilai jalur negatif, nilai tersebut dipotong kepada sifar, dan satu topeng piksel demi piksel dalam folder `clamp_masks/` di sebelah folder produk merekodkan piksel dan jalur yang terjejas. Produk menamakan maskernya dalam tag `Chloros:UnmixClampMask`. Paparan langsung kini memotong dengan tepat seperti yang dilakukan oleh eksport. Lihat [Format Imej Keluaran](output-image-formats.md).
* **Produk menerangkan dirinya sendiri.** Produk indeks membawa tag pemprosesan mereka sendiri (`Chloros:ProcessingLevel=index`, ungkapan indeks, dan sama ada ia dikira daripada sinaran atau pantulan) bukannya tag fail mentah sumber. ProdukSurvey3
membawa tag pemprosesan Chloros dalam setiap mod pemprosesan. Fail LATTICE mentah dan debayered kekal dalam orientasi asal sensor dan merekodkan orientasi mereka dalam XMP; Pemapar Imej memusingkannya hanya untuk paparan.
* ** Pakej Linux dihantar tanpa dibongkar.** Backend dan CLI dipasang sebagai folder di bawah `/usr/lib/chloros/`, jadi ia tidak lagi membongkar sendiri pada setiap pelancaran. Pada x86_64, backend tidak lagi mengambil masa kira-kira 100 saat untuk memulakan, dan `.deb` kini bersaiz kira-kira separuh daripada saiznya sebelum ini. ExifTool yang disertakan kini berjalan pada pemasangan Ubuntu yang bersih. `.deb` kini juga memasang Python SDK untuk sistem `python3` (pasang ia dengan `sudo apt install ./chloros_1.3.0_<arch>.deb`), meningkatkan buffer penerimaan soket kernel yang diperlukan oleh aliran kamera, dan menambah perkhidmatan backend yang berjalan sebagai pengguna anda. Pakej Jetson, `chloros_1.3.0_arm64_jp6.deb`, dibina untuk JetPack 6 dan diproses pada GPU Jetson dengan binaan CUDA PyTorch JetPack. Pada Linux, setiap imej yang dieksport kini sampai ke cakera sekali sahaja (ditulis dan ditandakan dalam memori, kemudian disalin ke tempatnya): pada Jetson, di mana cakera menentukan kelajuan, pelaksanaan LATTICE 288-imej mengambil masa 14 minit bukannya 24. Lihat [Pemasangan Linux](linux/linux-installation.md).
* **Kamera LATTICE OCN membaca dengan lebih tepat di bawah cahaya matahari.** Radiasi, pantulan dan indeks mendapat pembetulan armada selepas pencampuran semula jalur, jadi sasaran kelabu dibaca hampir neutral NDVI di bawah cahaya matahari. Kamera RGN kekal tidak berubah.
* **Laporan pepijat dalam aplikasi.** Ikon pepijat di bahagian atas tab Log Ralat menghantar kepada MAPIR keterangan, sehingga 10 imej, log dan butiran sistem. Lihat [GUI : Navigasi](navigation.md).
* **Susunan LATTICE.**Susunan mengekalkan keseluruhan sensor secara lalai, dan penghantaran 12-bit padat (`--packed`, lalai `auto`) meningkatkan kadar bingkai keseluruhan sensor kira-kira sepertiga; susunan berfungsi pada mana-mana rangkaian pada kadar bingkai yang dibenarkannya (pautan perlahan menurunkan kadar bingkai dan bukannya menolak susunan atau mengecilkan bingkainya); susunan yang kameranya melalui kitaran kuasa akan pulih dengan sendirinya, dengan PTP dihidupkan semula; pendedahan automatik tidak lagi menurunkan kadar itu dalam cahaya malap (ia mengisi tempoh, kemudian meningkatkan penguatan); Rakaman berterusan dapat mengekalkan kelajuan susunan pantas; kawalan**Highlights** untuk kamera RGB; dan `ArraySession` dalam SDK kini disokong oleh `grab()`, `frame_stream()` dan `continuous()`. Lihat [seksyen LATTICE](lattice/README.md).
* **Pendedahan automatik yang lebih stabil.** Pendedahan mengekalkan tahap pada cahaya yang sekata dan menstabilkan dalam beberapa saat selepas perubahan, sorotan kecil tidak lagi menggelapkan adegan, pendedahan manual mengekalkan penguatan pada Auto, dan kamera yang berhenti bertindak balas di bawah cahaya matahari yang terang dikekalkan pada tahap cahaya tersebut, dibaca oleh penderia cahaya atau, jika tiada disambungkan, oleh bingkai kamera itu sendiri. Lihat [Tetapan Kamera](lattice/camera-settings.md).
* **Penderia cahaya.** **Sokongan DAQ-E-S:** penyebar tetapnya tidak memerlukan pembetulan topi, dan setiap bacaan membawa kecenderungan, putaran dan kemiringan unit tersebut. Rakaman menyatakan sama ada penutupnya dipilih atau diandaikan, dan pemprosesan memberi amaran sekiranya ia diandaikan: DAQ-U, DAQ-M dan DAQ-E dihantar tanpa penutup, jadi nyatakan penutup yang dipasang. Rakaman `.daq` secara langsung akan terus wujud walaupun berlaku pemadaman kuasa (simpan fail `.daq-journal` bersama `.daq`), dan DAQ-E yang berhenti penghantaran akan disambungkan semula dengan jurang ditandakan dalam rakaman. Projek yang hanya menyimpan rakaman penderia cahaya menyenaraikannya dan memprosesnya melalui aplikasi dan CLI, dan rakaman yang dibuat dengan `chloros_scripts` disenaraikan sebagai DAQ yang membuatnya, dengan cap yang boleh anda tetapkan. Setiap tangkapan menyimpan bacaan DAQ yang digunakan. Lihat [seksyen DAQ](daq/README.md).
* **Pemprosesan.**Rangka LATTICE mula diproses semasa pengesanan sasaran. Rujukan sasaran dan DAQ ditentukan bagi setiap jalur. Projek yang dipindahkan akan menyambung semula failnya. Eksport tangkapan 8-bit adalah 8-bit, dengan format**TIFF (8-bit)** yang baharu. Sebuah pelaksanaan Survey3 tanpa sasaran penentukuran akan direkodkan sebagai produk sandarannya (`Vignette_Corrected_Images` atau `Sensor_Response_Images`), bukan sebagai imej pantulan. Tag DLS bagi setiap jalur dalam imej pantulan sentiasa menggunakan data bingkai itu sendiri. Texture Aware ditolak untuk tangkapan LATTICE, yang sentiasa menggunakan debayer standard.
* **CLI & SDK.** Laluan output relatif kini disimpan dalam folder di mana anda menjalankan arahan atau skrip (sebelum ini, backend terpasang akan cuba menyelesaikannya dalam folder pemasangan sendiri dan gagal). Backend terus berjalan antara arahan dan skrip dan menghentikan sendiri selepas 30 minit tidak digunakan; `chloros-cli backend status` dan `backend stop` mengurusnya. Perintah capture menamakan setiap fail yang tidak dapat ditulis dan keluar dengan kod 1, dan capture berulang akan berhenti apabila pemacu penuh. Pelaksanaan `process` yang berjaya melaporkan `Image products written: N (plus M clamp masks)`. Pada Windows, `ChlorosLocal` SDK / `process_folder` mencari backend yang dipasang (dan menggunakan salah satu yang sudah berjalan), dan `read_image_audit_tags` berfungsi daripada `pip install` biasa.

***

## Apa yang Baru dalam Chloros 1.2.0

* **Kawalan kamera langsung — tab Kamera baharu.** Sambungkan kamera LATTICE satu persatu atau sebagai susunan multi-kamera bersepadu (sinkronisasi masa PTP, tangkapan dipicu perkakasan), dengan lapisan pratonton langsung, histogram setiap jalur, pendedahan automatik pintar, pengira indeks langsung, dan kemas kini firmware kamera dalam aplikasi.
* **Penderia cahaya — tab Penderia Cahaya baharu.** Sambungkan penderia DAQ-U (USB), DAQ-M (Bluetooth), dan DAQ-E (Ethernet); lihat spektra kalibrasi langsung (W/m²/nm), rakam fail `.daq` ke dalam projek anda, pilih profil pembetulan cap, dan kemas kini firmware DAQ-E melalui rangkaian.
* **Mod tangkapan dan perakam.** Tangkapan Satu / Berterusan / Selang serta mod Tangkapan Terpantas hanya mentah; pilihan setiap projek untuk kamera dan jenis eksport yang dihasilkan oleh "Tangkap Semua"; perakam tatasusunan untuk video indeks gred pemantauan dan letupan mentah gred analisis dengan binaan video luar talian.
* **Saluran pemprosesan LATTICE.** Import folder tangkapan LATTICE dan keluarkan setiap bingkai mentah ke dalam produk debayered, pratonton, radiasi float32 (W/m²/sr/nm), dan pantulan dengan suis per-produk. Refleksans boleh diperoleh daripada sasaran penentukuran dalam bingkai atau pancaran ke bawah DAQ; penjajaran tatasusunan digunakan pada eksport; penentukuran kilang yang hilang dimuat turun secara automatik mengikut siri kamera.
* **Projek mengingati perkakasan.** Kamera dan penderia cahaya yang disambungkan disimpan bersama projek (`cameras.json` / `sensors.json`) dan disambungkan semula dengan tetapan yang disimpan apabila anda membuka semula projek. Lihat [GUI : Projects](projects.md).
* **Kemas kini pemapar imej.** Baca keluar piksel/indeks kursor dengan penskalaan pantulan yang betul bagi setiap fail, histogram lapisan, gelangsar binning GSD, mod grid Per Trigger / Per Kamera, paparan produk LATTICE, dan eksport sandbox Indeks/LUT ke cakera.
* **CLI & SDK, diperluas dengan ketara.** Keluarga arahan `lattice`, `daq pool-*`, `project`, dan `time-sync` baharu; pilihan `process` baru (`--input-level`, suis per-produk, `--reflectance-source`, bendera penjajaran tatasusunan); pegangan sambungan pintar SDK (`connect_camera` / `connect_array` / `connect_daq_sensor`) yang memulakan backend secara automatik; Automasi `open_project()`; roda SDK dibundel bersama pemasang dan diterbitkan ke PyPI sebagai `chloros-sdk`.
* **Semantik kegagalan yang jujur.** Jalankan `chloros-cli process` yang meminta produk tetapi tidak menulis sebarang produk kini gagal dengan jelas dan keluar dengan nilai bukan sifar; pelaksanaan yang berjaya melaporkan berapa banyak produk imej yang telah ditulis.
* **Susun atur keluaran baharu.** Produk disimpan dalam folder `<project>/<camera>/<format>/<Product>_Images/` dan mengekalkan nama fail sumber — folder, bukan Suffiks nama fail, mengenal pasti produk. Lihat [Format Imej Keluaran](output-image-formats.md).
* **Lebih banyak input, pelan, dan bahasa.** Sokongan input `.dng`; semua 38 bahasa antaramuka telah lengkap; had perkakasan mengikut pelan dengan penggunaan percuma (tanpa log masuk) sehingga 4 kamera dan 2 penderia cahaya.
* **Kebolehpercayaan.** Pemprosesan Berhenti dengan lancar dengan ringkasan pelaksanaan yang tepat, projek berbilang kamera mengeksport setiap kamera, dan kemas kini pemasang tidak lagi log anda keluar.***

Chloros tersedia dalam 3 antaramuka aplikasi:

## Chloros: Aplikasi GUI Desktop

Tetingkap berasingan berdiri sendiri dengan semua ciri, termasuk tab Kamera dan Penderia Cahaya secara langsung. _Windows sahaja._

## [Chloros CLI: Antara muka baris perintah](CLI.md)

Pemprosesan pukal baris perintah serta perintah langsung `lattice`, `daq pool-*`, `project`, dan `time-sync`. Sesuai untuk automasi, pengekodan skrip, dan operasi tanpa kepala. Tersedia pada **Windows, Linux amd64, dan Linux arm64 (NVIDIA Jetson)**. _CLI
memerlukan pelan berbayar Chloros+ untuk diakses._

## [Chloros API: Python SDK](api-python-sdk.md)

Antara muka Python berprogram untuk automasi dan aliran kerja tersuai: pemprosesan saluran paip penuh, sesi kamera/susunan secara langsung, sesi sensor DAQ, dan automasi projek yang disimpan. Dipasang dengan pakej desktop/CLI dan juga diterbitkan sebagai `pip install chloros-sdk`. _API memerlukan pelan berbayar Chloros+ untuk diakses._

***

## Platform yang Disokong

| Platform | GUI | CLI | Python SDK |
| --- | --- | --- | --- |
| **Windows 10/11 (x64)** | Ya | Ya | Ya |
| **Linux amd64 (x86_64)** | Tidak | Ya | Ya |
| **Linux arm64 (NVIDIA Jetson)** | Tidak | Ya | Ya |

Untuk arahan pemasangan Linux, lihat bahagian [Linux & Edge Computing](linux/linux-overview.md).

***

## Mulakan dalam Tiga Langkah

1. **Pasang** — muat turun dan jalankan pemasang untuk platform anda. Lihat [Muat Turun](download.md).
2. **Log masuk (pilihan untuk GUI)** — GUI memproses imej secara percuma tanpa akaun. Log masuk [Chloros+](chloros+-login.md) membuka pemprosesan selari, pecutan GPU, had peranti yang lebih tinggi, dan akses CLI/SDK.
3. **Buat projek pertama anda** — buka Chloros, buat [Projek Baru](projects.md), [tambah imej anda](processing-images-gui/adding-files-to-a-project.md), dan [mulakan pemprosesan](processing-images-gui/starting-the-processing.md). Untuk mengendalikan perkakasan secara langsung sebaliknya, buka tab Kamera atau Penderia Cahaya — lihat [GUI : Navigasi](navigation.md).

***

## Chloros+

Walaupun Chloros percuma untuk digunakan bagi kebanyakan tugasan, anda mungkin mendapati anda mahukan lebih. Di situlah lesen berbayar untuk Chloros+ boleh memberi manfaat kepada anda. Dengan lesen Chloros+ anda boleh membuka ciri baharu seperti:

* **Pemprosesan Berbenang Pelbagai**: mempercepat pemprosesan imej dengan ketara untuk projek yang lebih besar dengan memproses imej secara serentak melalui saluran paip.
* **Pecutan GPU (CUDA)**: manfaatkan pilihan memori GPU yang lebih tinggi hari ini untuk mempercepat lagi saluran paip pemprosesan imej. Kami mengesyorkan 4GB atau lebih VRAM untuk hasil terbaik.
* **Chloros+**[**CLI**](CLI.md) **Akses**: jalankan Chloros+ dari baris perintah untuk mengautomasikan dan menyepadukan ke dalam perisian anda sendiri. Tersedia pada mana-mana peringkat berbayar; dikuatkuasakan di pihak pelayan.
* **Chloros+**[**API**](api-python-sdk.md) **Akses:** jalankan Chloros+ daripada Python untuk kawalan berprogram, membolehkan integrasi lancar dengan saluran penyelidikan anda, aliran kerja analisis data, dan aplikasi tersuai. Tersedia pada mana-mana peringkat berbayar; dikuatkuasakan di pihak pelayan.
* **Had Perkakasan Lebih Tinggi**: sambungkan lebih banyak kamera dan penderia cahaya sekaligus. Tanpa log masuk, GUI menyambungkan sehingga 4 kamera dan 2 penderia cahaya DAQ; pelan berbayar meningkatkan kedua-dua had:

| Pelan | Kamera | Penderia cahaya DAQ |
| --- | --- | --- |
| Iron (percuma, tanpa log masuk) | 4 | 2 |
| Copper / Bronze | 6 | 3 |
| Silver | 10 | 6 |
| Gold | 20 | 12 |

* **Penggunaan Pelbagai Peranti**: setiap lesen Chloros+ membenarkan 2+ peranti didaftarkan. Gunakan akaun MAPIR Cloud anda untuk mengurus peranti yang didaftarkan. Tambah sokongan untuk lebih banyak peranti dengan menaik taraf lesen Chloros+ anda.
* **Kaedah Debayer Canggih yang Peka kepada Tekstur:** debayer berkualiti tinggi yang peka kepada sempadan digabungkan dengan model penyahbisuan AI/ML yang membuang hampir semua bunyi debayering.
* **Formula Indeks Multispektral Tersuai:** masukkan indeks multispektral tersuai dalam pengira raster Chloros, sama ada untuk pemprosesan dan sandpit tontonan imej.
* **Linux & Komputasi Hujung:** jalankan Chloros pada platform Linux x86_64 dan ARM64 termasuk NVIDIA Jetson untuk pemprosesan di lapangan dan di hujung. Lihat [Rangkuman Linux](linux/linux-overview.md).

<p align="center"><a href="https://cloud.mapir.camera/pricing" class="button primary" data-icon="envira">Chloros+ Harga & Pendaftaran</a></p>

<figure><img src=".gitbook/assets/plus_prog.JPG" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_grid_zoom.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_grid_mode.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_grid_meta.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_map_markers.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/cli.JPG" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: cli.JPG shows the 1.1.0 CLI banner. Re-shoot a terminal running `chloros-cli --version` + `chloros-cli status` on the 1.3.0 build so the banner prints "Chloros CLI 1.3.0". -->
