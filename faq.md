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

Ya. Chloros 1.3.0 menyokong modul kamera LATTICE M3C dan M3M secara menyeluruh: **kawalan langsung**— cari, sambung, pratonton, dan rakam daripada tab Kamera GUI, `chloros-cli lattice`, atau PythonSDK, termasuk susunan multi-kamera diselaraskan dengan penyegerakan masa PTP — dan**pemprosesan radiometrik penuh** ke atas rakaman (mentah → debayered → sinaran → pantulan → indeks). Lihat [Kamera Disokong](supported-cameras.md) dan [panduan LATTICE](lattice/README.md).

</details>

<details>

<summary>Bolehkah saya mengkalibrasi imej saya untuk pantulan tanpa sasaran kalibrasi?</summary>

**Survey3:** Tidak. Tanpa imej sasaran penentukuran yang diambil pada masa yang sama dengan imej bukan sasaran diambil, anda tidak akan dapat mengaitkan nilai piksel imej kepada peratus pantulan yang diketahui. Jika anda juga tidak menyertakan log daripada penderia cahaya MAPIR, spektrum cahaya persekitaran tidak akan diukur, dan keputusan pantulan tidak akan tepat.

**LATTICE:** Ya. Pantulan boleh dirujuk kepada iradiasi ke bawah yang diukur oleh penderia cahaya DAQ dan bukannya panel (ρ = π·L/E). Apabila sasaran dalam bingkai yang lulus QA hadir, ia secara lalai menjadi rujukan mutlak (`--reflectance-source auto`), melainkan ia lulus dengan penanda, di mana rantaian sasaran dan DAQ bersaing berdasarkan skor yang diukur. Satu pengecualian: "F988 reflektansi dikalibrasi menggunakan panel reflektansi dalam adegan: jalur itu terletak di luar julat kalibrasi penderia cahaya DAQ, jadi Chloros menggunakan tangkapan panel terkini anda dan menyimpannya di antara penjejakan panel." Lihat [Sasaran Penentukuran](calibration-targets.md).

</details>

<details>

<summary>Adakah saya memerlukan penderia cahaya DAQ?</summary>

Bukan untuk radiasi: Eksport radiasi LATTICE datang daripada kalibrasi radiometrik kilang setiap kamera dan tidak memerlukan sensor DAQ mahupun sasaran. Untuk **pantulan**anda memerlukan rujukan bagi cahaya persekitaran — sama ada pengukuran cahaya menuruni sensor DAQ atau sasaran kalibrasi dalam bingkai. Penderia DAQ membolehkan anda menghasilkan pantulan yang dikalibrasi**tanpa meletakkan sebarang panel dalam adegan**. Fail `.daq` yang direkodkan dipadankan dengan imej anda secara automatik berdasarkan cop masa. Lihat [Sasaran Kalibrasi](calibration-targets.md) dan [Rujukan CLI](reference/cli-reference.md).

</details>

<details>

<summary>Bolehkah saya menggunakan Chloros dengan pembantu AI (Claude, ChatGPT, dan lain-lain)?</summary>

Ya — manual ini dan [CLI]/SDK dibina untuk tujuan ini:

* Indeks penuh manual disajikan di `https://mapir.gitbook.io/chloros/llms.txt` supaya pembantu AI dapat menemui setiap halaman.
* Markdown mentah setiap halaman boleh didapati di URL kecil dengan menambah `.md` (contohnya `https://mapir.gitbook.io/chloros/reference/cli-reference.md`).
* [Rujukan [CLI]](reference/cli-reference.md) dan [Rujukan [SDK]](reference/sdk-reference.md) ditulis untuk kegunaan LLM: penanda tepat, lalai, semantik penamatan, dan arahan yang boleh disalin-tampal.

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
* **folder produk** — `Reflectance_Calibrated_Images/`, `Debayered_Images/`, `Preview_Images/`, `Radiance_Images/` (sentiasa di bawah `tiff32`), `<INDEX>_Index_Images/`
* **`clamp_masks/`** — multispektral LATTICE sahaja: topeng setiap piksel di samping folder produk, lihat di bawah**Fail yang dieksport mengekalkan nama fail sumber — folder mengenal pasti produk, bukan sambungan nama fail.**Dengan CLI, folder projek akan dibuat di bawah folder projek lalai anda (`Documents/MAPIR/Chloros Projects` melainkan anda menukarnya) melainkan anda menggunakan `-o`; `-o` relatif diambil berbanding folder di mana anda menjalankan arahan. Perhatikan bahawa pelaksanaan `chloros-cli process` yang meminta produk tetapi tidak menulis sebarang produk akan mencetak `Processing finished but wrote no image products.` dan**keluar dengan nilai bukan sifar**, supaya skrip dapat mengesaninya. Lihat [Format Imej Keluaran](output-image-formats.md) dan [Rujukan CLI](reference/cli-reference.md).

</details>

<details>

<summary>Apakah folder clamp_masks dan fail *_clampmask.tif?</summary>

Baharu dalam 1.3.0. Produk radiasi dan pantulan kamera multispektral LATTICE M3C menjalankan pencampuran semula jalur 3×3, dan di mana ia menghasilkan nilai negatif dalam sesuatu jalur, Chloros memotong nilai tersebut kepada sifar. Sebuah produk dengan sekurang-kurangnya satu piksel yang dipotong mendapat topeng `uint8` jalur tunggal yang menandakan piksel dan jalur yang telah dipotong (bit 0 = saluran R produk, bit 1 = G, bit 2 = B). Topeng disimpan dalam folder `clamp_masks/` **di sebelah** folder produk — contohnya `tiff16/clamp_masks/Reflectance_Calibrated_Images/<name>_clampmask.tif` — dan tag XMP `Chloros:UnmixClampMask` produk menamakan maskernya (atau mengatakan `none`).

Mereka bukan imej pemandangan: import Chloros sendiri melangkau mereka, dan kerana mereka tidak pernah berada dalam folder produk, anda boleh menyerahkan folder produk kepada Pix4D atau Metashape seperti sedia ada. Lihat [Clamp masks](output-image-formats.md#clamp-masks).

</details>

<details>

<summary>Mengapa Chloros mengatakan "Folder projek terlalu dalam untuk komputer ini"?</summary>

Ini hanya untuk Windows. Windows disertakan dengan laluan panjang dilumpuhkan, dan tiada laluan fail boleh melebihi 260 aksara. Produk Chloros terletak sehingga kira-kira 130 aksara di bawah folder projek, jadi Chloros menolak folder projek yang lebih dalam sebelum pemprosesan — daripada berjalan dan tidak menulis apa-apa, seperti yang boleh berlaku sebelum 1.3.0. Mesej itu memberikan folder projek terpanjang yang sesuai.

Betulkan dengan salah satu cara berikut:

* Gunakan folder projek yang lebih pendek (**Direktori Kerja** dalam Tetapan Projek, atau `-o` / `chloros-cli set-project-folder` untuk CLI).
* Aktifkan laluan panjang Windows dan mulakan semula Chloros. Pilihan **Aktifkan laluan panjang Windows** pada pemasang Chloros (diperiksa secara lalai) melakukan perkara ini; jalankan semula pemasang jika anda membatalkan pilihan tersebut.

Lihat [Memulakan Pemprosesan](processing-images-gui/starting-the-processing.md#the-project-folder-is-too-deep-for-this-computer).

</details>

<details>

<summary>Bolehkah saya menyunting imej saya sebelum diproses dalam Chloros?</summary>

Tidak. Chloros menganggap data input tidak diubah suai. Jangan ubah nama fail.

</details>

<details>

<summary>Bolehkah saya menetapkan kamera MAPIR Survey3 saya kepada pendedahan automatik dan memproses imej dalam Chloros?</summary>

Tidak. Set data imejSurvey3 mesti mempunyai pendedahan yang tetap/dikunci, jadi tiada kelajuan rana automatik atau ISO automatik. Semua imej bagi model kamera yang sama mesti mempunyai kelajuan rana dan ISO (pendedahan) yang identik.

Kamera LATTICE tidak mempunyai sekatan ini: Chloros mengawal pendedahan mereka secara langsung (Smart AE), dan setiap tangkapan merekodkan pendedahan dan penguatan yang sebenarnya digunakan, yang diambil kira oleh saluran pemprosesan radiometrik.

</details>

<details>

<summary>Bolehkah Chloros memproses atau menganalisis imej ortomosaik?</summary>

Tidak. Hanya imej kamera individu MAPIR disokong, bukan imej yang disambung seperti peta ortomosaik.

</details>

<details>

<summary>Bagaimana saya boleh mempercepatkan langkah pengesanan sasaran dalam Chloros?</summary>

Dalam jadual pelayar fail, memilih imej sasaran terlebih dahulu di lajur kanan akan memberitahu Chloros untuk mencari hanya dalam imej tersebut bagi sasaran penentukuran, sekali gus mempercepatkan pemprosesan.

</details>

<details>

<summary>Jika saya akan memuat naik imej saya ke <a href="https://www.mapir.camera/collections/software/products/mapir-cloud-subscription">MAPIR Cloud,</a> adakah saya perlu memprosesnya dalam Chloros sebelum memuat naik?</summary>

Jika anda merancang untuk memuat naik ke platform pemprosesan dalam talian kami [MAPIR Cloud](https://www.mapir.camera/collections/software/products/mapir-cloud-subscription), jangan sunting imej sebelum memuat naik. Cloud akan menjalankan semua pemprosesan yang sama dan lebih banyak lagi.

</details>

<details>

<summary>Adakah MAPIR akan menyokong ciri X? Saya benar-benar berharap MAPIR menawarkan X.</summary>

Kami sentiasa berminat menerima maklum balas mengenai produk kami. Jika anda menemui sebarang masalah dengan produk kami, atau mempunyai cadangan tentang bagaimana kami boleh meningkatkan produk kami, sila [HUBUNGI KAMI](https://www.mapir.camera/community/contact) untuk berkongsi pandangan anda. Kebanyakan R&D kami dipandu oleh mendengar keperluan terbesar pelanggan kami.

</details>

<details>

<summary>Adakah Chloros tersedia untuk Linux?</summary>

Ya! Chloros 1.3.0 menyokong Linux amd64 (x86_64) dan arm64 (NVIDIA Jetson JetPack 6) melalui pek `.deb`. CLI dan Python SDK disokong sepenuhnya pada Linux, termasuk kawalan kamera LATTICE secara langsung dan sensor DAQ. Tiada GUI untuk Linux — semua interaksi adalah melalui [CLI](CLI.md) atau [Python SDK](api-python-sdk.md). Lihat [Rangkuman [Linux]](linux/linux-overview.md) untuk butiran.

</details>

<details>

<summary>Bolehkah saya menjalankan Chloros pada NVIDIA Jetson?</summary>

Ya! Chloros menyokong keluarga NVIDIA Jetson **Orin** — Orin Nano, Orin NX dan AGX Orin — yang menjalankan JetPack 6. Model lama (Jetson Nano, TX2, Xavier NX) tidak dapat menjalankan JetPack 6 dan tidak disokong oleh pakej semasa. Chloros mengesan model Jetson anda secara automatik dan mengoptimumkan strategi pemprosesannya. Pakej Jetson menggunakan binaan CUDA PyTorch JetPack 6, jadi pemprosesan dijalankan pada GPU Jetson. Lihat [Panduan NVIDIA Jetson](linux/nvidia-jetson-guide.md) untuk arahan penyediaan dan penyebaran.

</details>

<details>

<summary>Adakah Chloros secara automatik mengoptimumkan untuk perkakasan saya?</summary>

Ya! Chloros merangkumi [Dynamic Compute Adaptation](processing-architecture/dynamic-compute-adaptation.md) yang mengesan secara automatik CPU, GPU, RAM anda, dan (pada Jetson) sensor haba. Ia kemudian memilih strategi pemprosesan yang optimum — daripada `GPU_PARALLEL` pada GPU berkapasiti memori tinggi kepada `GPU_SINGLE` pada yang lebih kecil kepada `CPU_PARALLEL` pada sistem tanpa GPU CUDA yang boleh digunakan oleh PyTorch. Tiada konfigurasi manual diperlukan.

</details>

<details>

<summary>Apakah saluran pemprosesan 4-benang?</summary>

Chloros menggunakan seni bina jalur paip 4-benang untuk pengguna Chloros+: Thread 1 (Pengesanan) memuat imej dan mengesan sasaran penentukuran, Thread 2 (Penentukuran) mengira penentukuran pantulan, Thread 3 (Pemprosesan) melakukan debayering dipercepatkan GPU dan pengiraan indeks, dan Thread 4 (Eksport) menulis fail keluaran. Beberapa imej boleh berada dalam benang berbeza serentak untuk throughput maksimum. Lihat [Saluran Pemprosesan](processing-architecture/processing-pipeline.md) untuk butiran.

</details>

<details>

<summary>Bagaimana saya menjalankan diagnostik pada pemasangan Chloros saya?</summary>

Gunakan arahan `selftest` untuk menjalankan ujian asap 7 langkah: versi, ketersediaan port, permulaan backend, sambungan API (`/api/test`), maklumat sistem (`/api/system-info` — GPU/CUDA/PyTorch), kehadiran model denoiser, dan kesiapsiagaan CUDA + denoiser:

```bash
chloros-cli selftest
```

Ini amat berguna pada sistem Linux untuk mengesahkan persediaan GPU dan CUDA. Pada Jetson, `CUDA: False` biasanya bermaksud pustaka CUDA dan cuDNN JetPack 6 hilang: pasang mereka dengan `sudo apt install nvidia-jetpack`.

</details>
