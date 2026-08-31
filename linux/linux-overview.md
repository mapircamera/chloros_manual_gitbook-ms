# Gambaran Umum Linux

Chloros 1.2.0 menyediakan sokongan asli Linux untuk **CLI**dan**Python SDK** — pemprosesan imej multispektral tanpa kepala, serta kawalan kamera LATTICE secara langsung dan penderia cahaya DAQ — pada stesen kerja, pelayan, dan peranti tepi NVIDIA Jetson.

{% hint style="info" %}
Tiada GUI desktop pada Linux. GUI Desktop Chloros hanya untuk Windows. Pengguna Linux berinteraksi dengan Chloros melalui [CLI](../CLI.md) dan [Python SDK](../api-python-sdk.md). `.deb` menambah entri **Chloros CLI** ke dalam menu aplikasi anda — ia hanya membuka emulator terminal yang menjalankan `chloros-cli`.
{% endhint %}

***

## Matriks Sokongan Platform

| Ciri | Windows (GUI) | Windows (CLI / SDK) | Linux amd64 (CLI / SDK) | Linux arm64 / Jetson (CLI / SDK) |
| --- | --- | --- | --- | --- |
| **GUI Desktop** | Ya | Tidak berkenaan | Tidak | Tidak |
| **CLI** (`chloros-cli`) | Ya | Ya | Ya | Ya |
| **Python SDK** (`chloros-sdk`) | Ya | Ya | Ya | Ya |
| **Saluran pemprosesan imej** | Ya | Ya | Ya | Ya |
| **Kawalan kamera LATTICE (langsung)** | Ya (Tab Kamera) | Ya (`chloros-cli lattice`, SDK) | Ya | Ya |
| **Penderia cahaya DAQ (langsung)** | Ya (Tab Penderia Cahaya) | Ya (`chloros-cli daq pool-*`, SDK) | Ya | Ya |
| **Penyelarasan masa PTP (host adalah grandmaster)** | Ya | Ya (`chloros-cli time-sync`) | Ya | Ya |
| **Pecutan GPU (CUDA)** | Ya | Ya | Ya | Ya (JetPack 6) |
| **Debayer Sedar Tekstur** | Ya (Chloros+) | Ya (Chloros+) | Ya (Chloros+) | Ya (Chloros+) |
| **Penyesuaian Komputasi Dinamik** | Ya | Ya | Ya | Ya |
| **Backend sebagai perkhidmatan sistem** (`chloros-backend.service`) | Tidak | Tidak | Ya (pilih untuk sertai) | Ya (pilih untuk sertai) |
| **Pemutakhiran di tempat** (`chloros-cli update`) | Tidak (jalankan pemasang) | Tidak (jalankan pemasang) | Ya | Ya |***

## Seni Bina yang Disokong

| Seni bina | Keterangan | Pakej |
| --- | --- | --- |
| **amd64 (x86_64)** | Pemproses desktop/pelayan standard (Intel, AMD) | `chloros_<version>_amd64.deb` |
| **arm64 (aarch64)** | Pemproses ARM — keluarga NVIDIA Jetson Orin | `chloros_<version>_arm64_jp6.deb` (bina JetPack 6) |

## Pengedaran Linux yang Disokong

* **Ubuntu 22.04 LTS atau yang lebih baru** (amd64)
* **Debian 12 atau yang lebih baru** (amd64)
* **NVIDIA JetPack 6** (arm64 — platform Jetson Orin)***

## Apa yang Diperolehi Pengguna 

* **Chloros CLI** — antara muka baris perintah penuh untuk pemprosesan kelompok, automasi, dan penulisan skrip
* **Chloros Python SDK** — antara muka Python berprogram untuk saluran penyelidikan dan alat tersuai (boleh dipasang dari PyPI, dan juga disertakan dalam `.deb` sebagai roda yang sepadan versinya)
* **Kawalan kamera LATTICE** — cari, sambung, konfigurasi, dan rakam daripada kamera LATTICE serta susunan multi-kamera bersepadu melalui `chloros-cli lattice` dan SDK; `.deb` membundarkan runtime Arena SDK yang diperlukan oleh kamera-kamera tersebut
* **Kawalan penderia cahaya DAQ** — sambungkan penderia DAQ-U/M/E, siarkan spektra yang telah dikalibrasi, dan rakam fail `.daq` melalui `chloros-cli daq pool-*` dan SDK
* **Penyelarasan masa PTP** — backend Chloros menjalankan grandmaster PTP yang menjadi rujukan untuk kamera LATTICE dan penderia DAQ-E; inspeksikan dengan `chloros-cli time-sync`, dan jalankannya secara tanpa pengawasan dengan unit systemd `chloros-backend.service` (lihat [Pemasangan Linux](linux-installation.md#always-on-ptp-for-headless-hosts))
* **Otomatisasi projek** — jalankan projek yang disimpan secara tanpa papan pemuka dengan `chloros-cli project` dan `open_project` daripada SDK
* **Pecutan GPU** — pemprosesan dipercepatkan CUDA pada GPU NVIDIA (desktop dan Jetson)
* **Penyesuaian Komputasi Dinamik** — pengesanan perkakasan automatik dan pemilihan strategi pemprosesan, dengan pilihan `CHLOROS_STRATEGY` untuk diubah suai sebagai jalan keluar pakar
* **Semua ciri pemprosesan** — pipeline yang sama seperti Windows: penentukuran, pembetulan vignette, indeks vegetasi, dan setiap format eksport
* **Ciri Chloros+** — pemprosesan berbilang benang (berpipa), debayer Sedar Tekstur, dan indeks tersuai, dengan pelan Chloros+ berbayar

## Apa yang Pengguna Linux Tidak Dapatkan

* **GUI Desktop** — tiada antara muka grafik; semua interaksi adalah melalui CLI atau Python SDK
* **Pemeriksa Imej** — tiada pemeriksa imej interaktif, paparan grid, atau penanda peta
* **Pengurusan projek visual** — projek dibuat dan dikendalikan melalui arahan CLI dan panggilan SDK (perkakasan itu sendiri — kamera, sensor, tangkapan — kekal boleh dikawal sepenuhnya dari terminal)***

## Keperluan Lesen

Akses CLI dan SDK memerlukan tahap berbayar Chloros+ — Copper atau lebih tinggi (Copper, Bronze, Silver, Gold). Tahap percuma **Iron** tidak mempunyai akses CLI / SDK. Sekatan ini dikuatkuasakan oleh backend, bukan hanya oleh CLI:

| Situasi | Tindak balas Backend |
| --- | --- |
| Tidak log masuk | `401` dengan `error_code: AUTH_REQUIRED` |
| Log masuk pada lapisan Iron percuma | `403` dengan `error_code: PLAN_UPGRADE_REQUIRED` |

`chloros-cli status` berfungsi pada mana-mana peringkat — ia adalah satu-satunya laluan yang dikecualikan daripada pintu — jadi sebab penolakan sentiasa dapat dilihat.

***

## Memulakan di Linux

1. **Pasang Chloros** — lihat [Pemasangan Linux](linux-installation.md) untuk pemasangan `.deb`
2. **Verifikasi** — `chloros-cli --version` mencetak `Chloros CLI 1.2.0`; `chloros-cli selftest` menjalankan diagnostik 7 langkah
3. **Pasang PythonSDK** (pilihan) — `pip install chloros-sdk`
4. **Log masuk** — `chloros-cli login your@email.com 'your-password'` (sekali bagi setiap mesin, dan semula selepas setiap peningkatan pakej)
5. **Proses set data pertama anda** — `chloros-cli process ~/datasets/flight001`

Untuk NVIDIA Jetson, lihat [Panduan NVIDIA Jetson](nvidia-jetson-guide.md) khusus untuk tetapan platform, tingkah laku terma, dan penyebaran lapangan.

***

## Langkah Seterusnya

* [Pemasangan Linux](linux-installation.md) — pemasangan terperinci, lokasi fail, dan penyelesaian masalah untuk amd64 dan arm64
* [Panduan NVIDIA Jetson](nvidia-jetson-guide.md) — penyediaan khusus Jetson, tingkah laku memori dan terma, penyebaran lapangan
* [CLI : Command Line](../CLI.md) — panduan CLI
* [API : Python SDK](../api-python-sdk.md) — panduan SDK
* [Rujukan CLI](../reference/cli-reference.md) dan [Rujukan SDK](../reference/sdk-reference.md) — senarai lengkap arahan/API untuk 1.2.0
* [Penyesuaian Komputasi Dinamik](../processing-architecture/dynamic-compute-adaptation.md) — bagaimana Chloros menyesuaikan diri dengan perkakasan anda

{% hint style="info" %}
**Membaca manual ini secara berperisian.** Setiap halaman juga disajikan sebagai Markdown mentah di URL tersendiri serta `.md` (contohnya `https://mapir.gitbook.io/chloros/linux/linux-installation.md`), dan indeks keseluruhan manual diterbitkan di [`https://mapir.gitbook.io/chloros/llms.txt`](https://mapir.gitbook.io/chloros/llms.txt).
{% endhint %}
