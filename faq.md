---
description: Frequently Asked Questions
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/faq
---

# Soalan Lazim

<details>

<summary>Bolehkah saya memproses imej daripada kamera yang bukan jenama MAPIR dengan Chloros?</summary>

Tidak, Chloros hanya menyokong pemprosesan imej kamera MAPIR — keluarga Survey3 dan LATTICE. Sila lihat senarai [model kamera yang disokong](supported-cameras.md) untuk maklumat lanjut. Kami juga menawarkan pemprosesan kamera lain di MAPIR Cloud, lihat senarai penuh [di sini](https://mapir.gitbook.io/mapir-cloud/supported-cameras).

</details>

<details>

<summary>Adakah Chloros menyokong kamera LATTICE?</summary>

Ya. Chloros 1.2.0 menyokong modul kamera LATTICE M3C dan M3M secara menyeluruh: **kawalan langsung**— cari, sambung, pratonton, dan rakam daripada tab Kamera GUI, `chloros-cli lattice`, atau PythonSDK, termasuk susunan multi-kamera diselaraskan dengan penyelarasan masa PTP — dan**pemprosesan radiametrik penuh**ke atas rakaman (mentah → debayered → sinaran → pantulan → indeks). Lihat [Kamera Disokong](supported-cameras.md) dan [panduan LATTICE](lattice/README.md).

</details>

<details>

<summary>Bolehkah saya mengkalibrasi imej saya untuk pantulan tanpa sasaran kalibrasi?</summary>Survey3

:** Tidak. Tanpa imej sasaran penentukuran yang diambil pada masa yang sama dengan imej bukan sasaran diambil, anda tidak akan dapat mengaitkan nilai piksel imej dengan peratus pantulan yang diketahui. Jika anda juga tidak menyertakan log daripada penderia cahayaMAPIR

, spektrum cahaya persekitaran tidak akan diukur, dan keputusan pantulan tidak akan tepat.

**LATTICE:** Ya. Pantulan boleh dirujuk kepada iradiasi ke bawah yang diukur oleh penderia cahaya DAQ dan bukannya panel (ρ = π·L/E). Apabila sasaran dalam bingkai yang lulus QA *ada*, ia secara lalai menjadi rujukan mutlak (`--reflectance-source auto`). Satu pengecualian: &quot;F988 reflektansi dikalibrasi menggunakan panel reflektansi dalam adegan: jalur itu terletak di luar julat kalibrasi penderia cahaya DAQ, jadiChloros

menerapkan tangkapan panel terkini anda dan menyimpannya di antara penjejakan panel.&quot; Lihat [Sasaran Kalibrasi](calibration-targets.md).

</details>

<details>

<summary>Adakah saya memerlukan penderia cahaya DAQ?</summary>

Bukan untuk radiasi: Eksport radiasi LATTICE datang daripada kalibrasi radiometrik kilang setiap kamera dan tidak memerlukan sensor DAQ mahupun sasaran. Untuk **pantulan**anda memerlukan rujukan bagi cahaya persekitaran — sama ada pengukuran cahaya ke bawah oleh sensor DAQ atau sasaran kalibrasi dalam bingkai. Sensor DAQ membolehkan anda menghasilkan pantulan yang dikalibrasi**tanpa meletakkan sebarang panel dalam adegan**. Fail `.daq` yang direkodkan dipadankan dengan imej anda secara automatik berdasarkan cop masa. Lihat [Sasaran Kalibrasi](calibration-targets.md) dan [Rujukan Kalibrasi Refleksi](reference/cli-reference.md).

</details>

<details>

<summary>Bolehkah saya menggunakan Chloros dengan pembantu AI (Claude, ChatGPT, dan lain-lain)?</summary>

Ya — manual ini dan CLI / SDK dibina untuk tujuan ini:

* Indeks penuh manual disajikan di `https://mapir.gitbook.io/chloros/llms.txt` supaya pembantu AI dapat menemui setiap halaman.
* Markdown mentah setiap halaman boleh didapati di URL dengan nama halaman kecil huruf kecil diikuti dengan `.md` (contohnya `https://mapir.gitbook.io/chloros/reference/cli-reference.md`).
* [Rujukan CLI](reference/cli-reference.md) dan [Rujukan SDK](reference/sdk-reference.md) ditulis untuk kegunaan LLM: penanda aras tepat, lalai, semantik keluar, dan arahan yang boleh disalin dan ditampal.

Lihat [AI Assistants](ai-assistants.md) untuk cara menudingkan pembantu anda ke Chloros.

</details>

<details>

<summary>Ke mana fail keluaran yang diproses saya pergi?</summary>

Produk ditulis di bawah folder projek, dikumpulkan mengikut kamera dan kemudian mengikut format fail:

```
<project>/<camera-folder>/<format-folder>/<Product>_Images/
```

* **folder-kamera** — `LATT-<sensor>-<lens>-F<filter>` untuk LATTICE, `<model>_<filter>` (contohnya `Survey3N_RGN`) untuk Survey3
* **format-folder** — `tiff16`, `tiff8`, `png8`, `jpg8`, atau `tiff32`
* **folder produk** — `Reflectance_Calibrated_Images/`, `Debayered_Images/`, `Preview_Images/`, `Radiance_Images/` (sentiasa di bawah `tiff32`), `<INDEX>_Index_Images/`**Fail yang dieksport mengekalkan nama fail sumber — folder mengenal pasti produk, bukan sambungan nama fail.**Dengan CLI, folder projek akan dibuat di sebelah folder input melainkan anda menggunakan `-o`. Perhatikan bahawa larian `chloros-cli process` yang meminta produk tetapi tidak menulis sebarang produk akan mencetak `Processing finished but wrote no image products.` dan**keluar dengan nilai bukan sifar**, supaya skrip dapat mengesaninya. Lihat [Format Imej Output](output-image-formats.md) dan [Rujukan CLI](reference/cli-reference.md).

</details>

<details>

<summary>Bolehkah saya menyunting imej saya sebelum memproses dalam Chloros?</summary>

Tidak. Chloros menganggap data input tidak diubah suai. Jangan ubah nama fail.

</details>

<details>

<summary>Bolehkah saya menetapkan kamera MAPIR Survey3 saya kepada pendedahan automatik dan memproses imej dalam Chloros?</summary>

Tidak. Set data imej Survey3 mesti mempunyai pendedahan yang tetap/dikunci, jadi tiada kelajuan rana automatik atau ISO automatik. Semua imej bagi model kamera yang sama mesti mempunyai kelajuan rana dan ISO (pendedahan) yang identik.

Kamera LATTICE tidak mempunyai sekatan ini: Chloros mengawal pendedahan secara langsung (Smart AE), dan setiap tangkapan merekodkan pendedahan dan penguatan yang sebenarnya digunakan, yang diambil kira oleh saluran pemprosesan radiometrik.

</details>

<details>

<summary>Bolehkah Chloros memproses atau menganalisis imej ortomosaik?</summary>

Tidak. Hanya imej kamera MAPIR individu disokong, bukan imej yang disambung seperti peta ortomosaik.

</details>

<details>

<summary>Bagaimana saya boleh mempercepatkan langkah pengesanan sasaran dalam Chloros?</summary>

Dalam jadual pelayar fail, memilih imej sasaran terlebih dahulu di lajur kanan akan memberitahu Chloros untuk mencari hanya dalam imej tersebut bagi sasaran penentukuran, sekali gus mempercepat pemprosesan.

</details>

<details>

<summary>Jika saya akan memuat naik imej saya ke <a href="https://www.mapir.camera/collections/software/products/mapir-cloud-subscription">MAPIR Cloud,</a> adakah saya perlu memprosesnya dalam Chloros sebelum memuat naik?</summary>

Jika anda merancang untuk memuat naik ke platform pemprosesan dalam talian kami [MAPIR

Cloud](https://www.mapir.camera/collections/software/products/mapir-cloud-subscription), jangan sunting imej sebelum memuat naik. Cloud akan menjalankan semua pemprosesan yang sama dan lebih lagi.

</details>

<details>

<summary>Adakah MAPIR akan menyokong ciri X suatu hari nanti? Saya benar-benar berharap MAPIR menawarkan X.</summary>

Kami sentiasa berminat untuk menerima maklum balas mengenai produk kami. Jika anda menemui sebarang masalah dengan produk kami, atau mempunyai cadangan tentang bagaimana kami boleh meningkatkan produk kami, sila [HUBUNGI KAMI](https://www.mapir.camera/community/contact) untuk berkongsi pandangan anda. Kebanyakan R&amp;D kami dipandu oleh mendengar keperluan terbesar pelanggan kami.

</details>

<details>

<summary>Adakah Chloros tersedia untuk Linux?</summary>

Ya! Chloros 1.2.0 menyokong Linux amd64 (x86_64) dan arm64 (NVIDIA Jetson JetPack 6) melalui pek `.deb`. CLI dan PythonSDK disokong sepenuhnya pada Linux, termasuk kawalan kamera LATTICE secara langsung dan sensor DAQ. Tiada GUI untuk Linux — semua interaksi adalah melalui [CLI](CLI.md) atau [Python SDK](api-python-sdk.md). Lihat [Linux Overview](linux/linux-overview.md) untuk butiran.

</details>

<details>

<summary>Bolehkah saya menjalankan Chloros pada NVIDIA Jetson?</summary>

Ya! Chloros menyokong platform NVIDIA Jetson termasuk Jetson Nano, Orin Nano, Orin NX, dan AGX Orin yang menjalankan JetPack 6. Chloros secara automatik mengesan model Jetson anda dan mengoptimumkan strategi pemprosesannya. Rujuk [Panduan NVIDIA Jetson](linux/nvidia-jetson-guide.md) untuk arahan penyediaan dan penyebaran.

</details>

<details>

<summary>Adakah Chloros secara automatik mengoptimumkan untuk perkakasan saya?</summary>

Ya! Chloros merangkumi [Dynamic Compute Adaptation](processing-architecture/dynamic-compute-adaptation.md) yang mengesan secara automatik CPU, GPU, RAM, dan (pada Jetson) sensor haba anda. Ia kemudian memilih strategi pemprosesan yang optimum — daripada `GPU_PARALLEL` pada sistem berkapasiti memori tinggi kepada `GPU_SINGLE` pada peranti terhad kepada `CPU_PARALLEL` pada sistem tanpa GPU NVIDIA. Tiada konfigurasi manual diperlukan.

</details>

<details>

<summary>Apakah saluran pemprosesan 4-benang?</summary>

Chloros

menggunakan seni bina saluran paip 4-benang untuk pengguna eChloros

+: Thread 1 (Pengesanan) memuat imej dan mengesan sasaran penentukuran, Thread 2 (Penentukuran) mengira penentukuran pantulan, Thread 3 (Pemprosesan) menjalankan debayering dipercepatkan GPU dan pengiraan indeks, dan Thread 4 (Eksport) menulis fail keluaran. Beberapa imej boleh berada dalam benang berbeza serentak untuk throughput maksimum. Lihat [Processing Pipeline](processing-architecture/processing-pipeline.md) untuk butiran.

</details>

<details>

<summary>Bagaimana saya menjalankan diagnostik pada pemasangan Chloros saya?</summary>

Gunakan perintah `selftest` untuk menjalankan ujian asap 7 langkah: versi, ketersediaan port, permulaan backend, sambungan API (`/api/test`), maklumat sistem (`/api/system-info` — GPU/CUDA/PyTorch), kehadiran model denoiser, dan kesiapsiagaan CUDA + denoiser:

```bash
chloros-cli selftest
```

Ini amat berguna pada sistem Linux/Jetson untuk mengesahkan persediaan GPU dan CUDA.

</details>
