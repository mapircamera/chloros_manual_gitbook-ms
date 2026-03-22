---
description: Frequently Asked Questions
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/faq
---

# Soalan Lazim

<butiran>

<summary>Bolehkah saya memproses imej daripada kamera yang bukan jenama MAPIR dengan Chloros?</summary>

Tidak, Chloros hanya menyokong pemprosesan imej kamera MAPIR. Sila lihat senarai [model kamera yang disokong](supported-cameras.md) untuk mendapatkan maklumat lanjut. Kami menawarkan pemprosesan kamera lain pada MAPIR Cloud, lihat senarai penuh [di sini](https://mapir.gitbook.io/mapir-cloud/supported-cameras).

</detail>

<butiran>

<summary>Bolehkah saya menentukur imej saya untuk pemantulan tanpa sasaran penentukuran?</summary>

Tidak. Tanpa imej sasaran penentukuran yang ditangkap sekitar apabila imej bukan sasaran ditangkap, anda tidak akan dapat mengaitkan nilai piksel imej dengan peratus pemantulan yang diketahui. Jika anda juga tidak memasukkan log daripada penderia cahaya MAPIR maka spektrum cahaya ambien tidak akan diukur dan hasil pemantulan tidak akan tepat.

</detail>

<butiran>

<summary>Bolehkah saya mengedit imej saya sebelum memproses dalam Chloros?</summary>

Tidak. Chloros menganggap data input belum diubah suai. Jangan tukar nama fail.

</detail>

<butiran>

<summary>Bolehkah saya menetapkan kamera MAPIR Survey3 saya kepada pendedahan automatik dan memproses imej dalam Chloros?</summary>

Tidak. Survey3 set data imej mesti mempunyai pendedahan tetap/terkunci, jadi tiada kelajuan pengatup automatik atau ISO automatik. Semua imej model kamera yang sama mesti mempunyai kelajuan pengatup dan ISO (pendedahan) yang sama.

</detail>

<butiran>

<summary>Bolehkah Chloros memproses atau menganalisis imej orthomosaic?</summary>

Tidak. Hanya imej kamera MAPIR individu disokong, bukan imej bercantum seperti peta orthomosaic.

</detail>

<butiran>

<summary>Bagaimanakah saya boleh mempercepatkan langkah pengesanan sasaran Chloros?</summary>

Dalam jadual penyemak imbas fail, pra-memilih imej sasaran dalam lajur kanan akan memberitahu Chloros untuk hanya melihat dalam imej tersebut untuk sasaran penentukuran, dengan sangat mempercepatkan pemprosesan.

</detail>

<butiran>

<summary>Jika saya akan memuat naik imej saya ke <a href="https://www.mapir.camera/collections/software/products/mapir-cloud-subscription">MAPIR Cloud</a> patutkah saya memproses dalam Chloros sebelum memuat naik?</summary>

Jika anda bercadang untuk memuat naik ke platform pemprosesan dalam talian kami [MAPIR Cloud](https://www.mapir.camera/collections/software/products/mapir-cloud-subscription) jangan edit imej sebelum memuat naik. Cloud akan melakukan semua pemprosesan yang sama dan banyak lagi.

</detail>

<butiran>

<summary>Adakah MAPIR akan menyokong ciri X? Saya sangat berharap MAPIR ditawarkan X.</summary>

Kami sentiasa berminat untuk menerima maklum balas mengenai produk kami. Jika anda menemui masalah dengan produk kami, atau mempunyai cadangan tentang cara kami boleh menambah baik produk kami, sila [HUBUNGI KAMI](https://www.mapir.camera/community/contact) untuk berkongsi pendapat anda. Kebanyakan R\&D kami dipandu dengan mendengar keperluan terbesar pelanggan kami.

</detail>

<butiran>

<summary>Adakah Chloros tersedia untuk Linux?</summary>

Ya! Chloros 1.1.0 menyokong Linux amd64 (x86\_64) dan arm64 (NVIDIA Jetson JetPack 6) melalui pakej `.deb`. CLI dan Python SDK disokong sepenuhnya pada Linux. Tiada GUI untuk Linux — semua interaksi adalah melalui [CLI](CLI.md) atau [Python SDK](api-python-sdk.md). Lihat [Linux Overview](linux/linux-overview.md) untuk butiran.

</detail>

<butiran>

<summary>Bolehkah saya menjalankan Chloros pada NVIDIA Jetson?</summary>

Ya! Chloros 1.1.0 menyokong platform NVIDIA Jetson termasuk Jetson Nano, Orin Nano, Orin NX dan AGX Orin yang menjalankan JetPack 6. Chloros secara automatik mengesan model Jetson anda dan mengoptimumkan strategi pemprosesannya. Lihat [Panduan NVIDIA Jetson](linux/nvidia-jetson-guide.md) untuk arahan persediaan dan penggunaan.

</detail>

<butiran>

<summary>Adakah Chloros mengoptimumkan secara automatik untuk perkakasan saya?</summary>

Ya! Chloros 1.1.0 termasuk [Penyesuaian Pengiraan Dinamik](processing-architecture/dynamic-compute-adaptation.md) yang mengesan secara automatik penderia terma CPU, GPU, RAM dan (pada Jetson) anda. Ia kemudian memilih strategi pemprosesan yang optimum — daripada `GPU_PARALLEL` pada sistem memori tinggi kepada `GPU_SINGLE` pada peranti terhalang kepada `CPU_PARALLEL` pada sistem tanpa GPU NVIDIA. Tiada konfigurasi manual diperlukan.

</detail>

<butiran>

<summary>Apakah saluran paip pemprosesan 4-benang?</summary>

Chloros 1.1.0 menggunakan seni bina saluran paip 4-benang untuk pengguna Chloros+: Thread 1 (Pengesanan) memuatkan imej dan mengesan sasaran penentukuran, Thread 2 (Calibration) mengira penentukuran pantulan, Benang 3 (Pemprosesan) melakukan penentukuran dan pecutan GPU4 (Eksport) menulis fail output. Berbilang imej boleh berada dalam urutan yang berbeza serentak untuk daya pemprosesan maksimum. Lihat [Processing Pipeline](processing-architecture/processing-pipeline.md) untuk butiran.

</detail>

<butiran>

<summary>Bagaimana cara saya menjalankan diagnostik pada pemasangan Chloros saya?</summary>

Gunakan arahan `selftest` untuk menjalankan 7 diagnostik sistem termasuk semakan versi, ketersediaan port, permulaan bahagian belakang, sambungan API, maklumat sistem, model denoiser dan ketersediaan CUDA:

```bash
chloros-cli selftest
```

Ini amat berguna pada sistem Linux/Jetson untuk mengesahkan persediaan GPU dan CUDA.

</detail>