---
metaLinks: {}
---

# Memulakan

<div data-full-width="false"><figure><img src=".gitbook/assets/chloros_logo_transparent.png" alt=""><figcaption></figcaption></figure></div>

Chloros

adalah aplikasi perisian daripada [MAPIR

](https://www.mapir.camera) untuk memproses imej multispektral, mengawal perkakasanMAPIR

secara langsung, dan merekod data sensor.Chloros

1.2.0 menyokong keseluruhan keluarga produkMAPIR

:

* ** KameraSurvey3** — memproses tangkapan RAW+JPG menjadi peta pantulan dan indeks vegetasi yang dikalibrasi. Lihat [Kamera Disokong](supported-cameras.md).
* **kamera LATTICE** — sambungkan modul kamera multispektral GigE secara langsung, secara bersendirian atau sebagai susunan multi-kamera yang diselaraskan: pratonton, rakam, dan proses menjadi produk radiasi dan pantulan yang dikalibrasi. Lihat [seksyen LATTICE](lattice/README.md).
* **Penderia cahaya DAQ** — penderia spektral DAQ-U (USB), DAQ-M (Bluetooth), dan DAQ-E (Ethernet): spektra kalibrasi secara langsung, rakaman `.daq`, dan pencahayaan ke bawah untuk pemprosesan pantulan. Lihat [seksyen DAQ](daq/README.md).

{% hint style="success" %}
**Apa Yang Baharu dalamChloros

1.2.0**: kawalan kamera LATTICE dan susunan secara langsung, integrasi penderia cahaya DAQ, mod tangkapan dan perakam, saluran pemprosesan radiometrik LATTICE sepenuhnya, automasi projek daripadaCLI

/SDK

, dan banyak lagi. Lihat senarai Perkara Baharu di bawah, dan [Muat Turun](download.md) untuk log perubahan.
{% endhint %}

{% hint style="info" %}
**MenggunakanChloros

dengan pembantu AI?** Manual ini dibina untuknya. Arahkan pembantu anda ke:

* `https://mapir.gitbook.io/chloros/llms.txt` — indeks yang boleh dibaca mesin bagi setiap halaman.
* Mana-mana halaman sebagai Markdown mentah — sambungkan `.md` kepadaURL

-nya (contohnya `https://mapir.gitbook.io/chloros/reference/cli-reference.md`).
* Rujukan [CLI

](reference/cli-reference.md) dan Rujukan [SDK

](reference/sdk-reference.md) — halaman rujukan nilai tepat yang lengkap, ditulis untuk penggunaan LLM.

Contoh arahan: *&quot;Bacalah https://mapir.gitbook.io/chloros/reference/cli-reference.md, kemudian tulis skrip yang log masuk dan memproses folder ~/flights/flight_001 menjadi GeoTIFF reflektansi +NDVI

.&quot;*

Panduan penuh: [MenggunakanChloros

dengan Pembantu AI](ai-assistants.md).
{% endhint %}

***

## Apa yang Baru dalamChloros

1.2.0

* **Kawalan kamera secara langsung — tab Kamera baharu.** Sambungkan kamera LATTICE satu persatu atau sebagai susunan berbilang kamera yang diselaraskan (penyelarasan masa PTP, tangkapan dipicu perkakasan), dengan lapisan pratonton langsung, histogram setiap jalur, pendedahan automatik pintar, pengira indeks langsung, dan kemas kini firmware kamera dalam aplikasi.
* **Penderia cahaya — tab Penderia Cahaya baharu.** Sambungkan penderia DAQ-U (USB), DAQ-M (Bluetooth), dan DAQ-E (Ethernet); lihat spektra kalibrasi langsung (W/m²/nm), rakam fail `.daq` ke dalam projek anda, pilih profil pembetulan cap, dan kemas kini firmware DAQ-E melalui rangkaian.
* **Mod tangkapan dan perekod.** Tangkapan Tunggal / Berterusan / Selang serta mod Tangkapan Terpantas hanya mentah; pilihan setiap projek untuk kamera dan jenis eksport yang dihasilkan oleh &quot;Tangkap Semua&quot;; perekod tatasusunan untuk video indeks gred pemantauan dan letupan mentah gred analisis dengan binaan video luar talian.
* **Saluran pemprosesan LATTICE.** Import folder tangkapan LATTICE dan kembangkan setiap bingkai mentah kepada produk debayered, pratonton, radiasi float32 (W/m²/sr/nm), dan pantulan dengan suis per-produk. Refleksan boleh diperoleh daripada sasaran penentukuran dalam bingkai atau pancaran ke bawah DAQ; penjajaran tatasusunan digunakan pada eksport; penentukuran kilang yang hilang dimuat turun secara automatik mengikut siri kamera.
* **Projek mengingati perkakasan.** Kamera dan penderia cahaya yang disambungkan disimpan bersama projek (`cameras.json` / `sensors.json`) dan disambungkan semula dengan tetapan yang disimpan apabila anda membuka semula projek. Lihat [GUI : Projects](projects.md).
* **Kemas kini pemapar imej.** Baca keluar piksel/indeks kursor dengan penskalaan pantulan yang betul bagi setiap fail, histogram lapisan, gelangsar binning GSD, mod grid Per Trigger / Per Kamera, paparan produk LATTICE, dan eksport sandbox Indeks/LUT ke cakera.
* **CLI

&amp;SDK

, diperluas dengan ketara.** Keluarga arahan `lattice`, `daq pool-*`, `project`, dan `time-sync` baharu; pilihan `process` baru (`--input-level`, suis per-produk, `--reflectance-source`, bendera penjajaran tatasusunan);SDK

penangan smart-connect (`connect_camera` / `connect_array` / `connect_daq_sensor`) yang memulakan backend secara automatik; Automasi `open_project()`; rodaSDK

disertakan bersama pemasang dan diterbitkan ke PyPI sebagai `chloros-sdk`.
* **Semantik kegagalan yang jujur.** Jalankan `chloros-cli process` yang meminta produk tetapi tidak menulis sebarang kini gagal dengan jelas dan keluar dengan nilai tidak sifar; pelaksanaan yang berjaya melaporkan berapa banyak produk imej yang mereka tulis.
* **Susun atur keluaran baharu.** Produk disimpan dalam folder `<project>/<camera>/<format>/<Product>_Images/` dan mengekalkan nama fail sumber — folder itu, bukan sambungan nama fail, mengenal pasti produk. Lihat [Format Imej Keluaran](output-image-formats.md).
* **Lebih banyak input, pelan, dan bahasa.** Sokongan input `.dng`; semua 38 bahasa antaramuka telah lengkap; had perkakasan mengikut pelan dengan penggunaan percuma (tanpa log masuk) sehingga 4 kamera dan 2 penderia cahaya.
* **Kebolehpercayaan.** Hentikan Pemprosesan dengan kemas dan menyediakan ringkasan pelaksanaan yang tepat, projek berbilang kamera mengeksport setiap kamera, dan kemas kini pemasang tidak lagi log anda keluar.***

Chloros

tersedia dalam 3 antaramuka aplikasi:

##Chloros

: Aplikasi GUI Desktop

Tetingkap berasingan berdiri sendiri dengan semua ciri, termasuk tab Kamera dan Penderia Cahaya secara langsung. _Hanya untuk Windows._

## [Chloros

CLI

: Antara muka baris perintah](CLI.md)

Pemprosesan pukal baris perintah serta perintah `lattice`, `daq pool-*`, `project`, dan `time-sync` secara langsung. Sesuai untuk automasi, pengekodan skrip, dan operasi tanpa kepala. Tersedia pada **Windows

,Linux

amd64, danLinux

arm64 (NVIDIA Jetson)**. _CLI memerlukan pelan berbayarChloros

+ untuk diakses._

## [Chloros

API

:Python

SDK

](api-python-sdk.md)

Antara mukaPython

berprogramatik untuk automasi dan aliran kerja tersuai: pemprosesan saluran penuh, sesi kamera/susunan secara langsung, sesi sensor DAQ, dan automasi projek yang disimpan. Dipasang dengan pakej desktop/CLI

dan juga diterbitkan sebagai `pip install chloros-sdk`. _API memerlukan tahap berbayarChloros

+ untuk diakses._

***

## Platform yang Disokong

| Platform | GUI |CLI

|Python

SDK

|
| --- | --- | --- | --- |
| **Windows

10/11 (x64)** | Ya | Ya | Ya |
| **Linux

amd64 (x86_64)** | Tidak | Ya | Ya |
| **Linux

arm64 (NVIDIA Jetson)** | Tidak | Ya | Ya |

Untuk arahan pemasanganLinux

, lihat bahagian [Linux

&amp; Edge Computing](linux/linux-overview.md).

***

## Mulakan dalam Tiga Langkah

1. **Pasang** — muat turun dan jalankan pemasang untuk platform anda. Lihat [Muat Turun](download.md).
2. **Log masuk (pilihan untuk GUI)** — GUI memproses imej secara percuma tanpa akaun. [Log masukChloros

+](chloros+-login.md) membuka pemprosesan selari, pecutan GPU, had peranti yang lebih tinggi, dan aksesCLI

/SDK

.
3. **Buat projek pertama anda** — bukaChloros

, buat [Projek Baru](projects.md), [tambah imej anda](processing-images-gui/adding-files-to-a-project.md), dan [mulakan pemprosesan](processing-images-gui/starting-the-processing.md). Untuk mengendalikan perkakasan secara langsung sebaliknya, buka tab Kamera atau Penderia Cahaya — lihat [GUI : Navigasi](navigation.md).

***

##Chloros

+

WalaupunChloros

percuma untuk digunakan bagi kebanyakan tugas, anda mungkin mendapati anda memerlukan lebih banyak lagi. Di sinilah lesen berbayar untukChloros

+ boleh memberi manfaat kepada anda. Dengan lesenChloros

+ anda boleh membuka ciri baharu seperti:

* **Pemprosesan Berbenang Pelbagai**: mempercepat pemprosesan imej dengan ketara untuk projek yang lebih besar dengan memproses imej secara serentak melalui saluran pemprosesan.
* **Pecutan GPU (CUDA)**: manfaatkan pilihan memori GPU yang lebih tinggi hari ini untuk mempercepatkan lagi saluran pemprosesan imej. Kami mengesyorkan 4GB atau lebih VRAM untuk hasil terbaik.
* **AksesChloros

+**[**CLI**](CLI.md): jalankan + dari baris perintah untuk mengautomasikan dan menyepadukan ke dalam perisian anda sendiri. Tersedia pada mana-mana pelan berbayar; dikuatkuasakan di pihak pelayan.
* **Chloros

+**[**API**](api-python-sdk.md) **Akses:** jalankanChloros

+ dariPython

untuk kawalan berprogram, membolehkan integrasi lancar dengan saluran penyelidikan anda, aliran kerja analisis data, dan aplikasi tersuai. Tersedia pada mana-mana pelan berbayar; dikuatkuasakan di pihak pelayan.
* **Had Perkakasan Lebih Tinggi**: sambungkan lebih banyak kamera dan penderia cahaya sekaligus. Tanpa log masuk, GUI menyambungkan sehingga 4 kamera dan 2 penderia cahaya DAQ; pelan berbayar meningkatkan kedua-dua had:

| Pelan | Kamera | Penderia cahaya DAQ |
| --- | --- | --- |
| Iron (percuma, tanpa log masuk) | 4 | 2 |
| Copper / Bronze | 6 | 3 |
| Silver | 10 | 6 |
| Gold | 20 | 12 |

* **Penggunaan Pelbagai Peranti**: setiap lesenChloros

+ membenarkan 2+ peranti didaftarkan. Gunakan akaun AMAPIR

Cloud anda untuk mengurus peranti yang didaftarkan. Tambah sokongan untuk lebih banyak peranti dengan menaik taraf lesenChloros

+ anda.
* **Kaedah Debayer Canggih yang Peka kepada Tekstur:** debayer berkualiti tinggi yang peka kepada sempadan digabungkan dengan model penyahbisikan AI/ML yang membuang hampir semua bunyi debayering.
* **Formula Indeks Multispektral Tersuai:** masukkan indeks multispektral tersuai dalam pengira rasterChloros

, sama ada untuk pemprosesan atau sandpit tontonan imej.
* **Linux

&amp; Edge Computing:** jalankanChloros

pada platformLinux

x86\_64 dan ARM64 termasuk NVIDIA Jetson untuk pemprosesan di lapangan dan di hujung. Lihat [TinjauanLinux

](linux/linux-overview.md).

<p align="center"><a href="https://cloud.mapir.camera/pricing" class="button primary" data-icon="envira">Chloros+ Penentuan Harga &amp; Pendaftaran</a></p>

<figure><img src=".gitbook/assets/plus_prog.JPG" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_grid_zoom.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_grid_mode.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_grid_meta.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_map_markers.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/cli.JPG" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: cli.JPG shows the 1.1.0 CLI banner. Re-shoot a terminal running `chloros-cli --version` + `chloros-cli status` on the 1.2.0 build so the banner prints "Chloros CLI 1.2.0". -->
