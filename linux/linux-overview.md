# Gambaran Keseluruhan Linux

Chloros 1.3.0 menyediakan sokongan Linux asli untuk **CLI**dan**Python SDK** — pemprosesan imej multispektral tanpa kepala, serta kawalan kamera LATTICE secara langsung dan penderia cahaya DAQ — pada stesen kerja Linux, pelayan, dan peranti tepi NVIDIA Jetson.

{% hint style="info" %}
**Tiada GUI desktop pada Linux.**GUI Desktop Chloros hanya untuk Windows. Pengguna Linux berinteraksi dengan Chloros melalui [CLI](../CLI.md) dan [Python SDK](../api-python-sdk.md). `.deb` menambah entri**ChlorosCLI** ke dalam menu aplikasi anda — ia hanya membuka emulator terminal yang menjalankan `chloros-cli`.
{% endhint %}

***

## Matriks Sokongan Platform

| Ciri | Windows (GUI) | Windows (CLI/SDK) | Linux amd64 (CLI/SDK) | Linux arm64 / Jetson (CLI/SDK) |
| --- | --- | --- | --- | --- |
| **GUI Desktop** | Ya | Tidak terpakai | Tidak | Tidak |
| **CLI** (`chloros-cli`) | Ya | Ya | Ya | Ya |
| **Python SDK** (`chloros-sdk`) | Ya | Ya | Ya | Ya |
| **Saluran pemprosesan imej** | Ya | Ya | Ya | Ya |
| **Kawalan kamera LATTICE (langsung)** | Ya (Tab Kamera) | Ya (`chloros-cli lattice`, SDK) | Ya | Ya |
| **Penderia cahaya DAQ (langsung)** | Ya (Tab Penderia Cahaya) | Ya (`chloros-cli daq pool-*`, SDK) | Ya | Ya |
| **Penyelarasan masa PTP (hos adalah grandmaster)** | Ya | Ya (`chloros-cli time-sync`) | Ya | Ya |
| **Pecutan GPU (CUDA)** | Ya | Ya | Ya | Ya (JetPack 6) |
| **Debayer sedar tekstur** (perlukan GPU CUDA) | Ya (Chloros+) | Ya (Chloros+) | Ya (Chloros+) | Ya (Chloros+) |
| **Penyesuaian Komputasi Dinamik** | Ya | Ya | Ya | Ya |
| **Backend sebagai perkhidmatan sistem** (`chloros-backend@<user>.service`) | Tidak | Tidak | Ya (pilih untuk sertai) | Ya (pilih untuk sertai) |
| **Pemutakhiran di tempat** (`chloros-cli update`) | Tidak (jalankan pemasang) | Tidak (jalankan pemasang) | Ya | Ya |***

## Seni Bina yang Disokong

| Seni bina | Keterangan | Pakej |
| --- | --- | --- |
| **amd64 (x86_64)** | Pemproses desktop/pelayan standard (Intel, AMD) | `chloros_<version>_amd64.deb` |
| **arm64 (aarch64)** | Pemproses ARM — keluarga NVIDIA Jetson Orin (JetPack 6) | `chloros_<version>_arm64_jp6.deb` |

## Distribusi Linux yang Disokong

* **Ubuntu 22.04 LTS atau lebih baru** (amd64)
* **Debian 12 atau lebih baru** (amd64)
* **NVIDIA JetPack 6** (arm64 — platform Jetson Orin)***

## Apa yang Diperolehi Pengguna Linux

* **Chloros CLI** — antara muka baris perintah penuh untuk pemprosesan kelompok, automasi, dan pengekodan skrip
* **Chloros Python SDK SDK** — antara muka Python berprogramatik untuk saluran penyelidikan dan alat tersuai, dipasang oleh `.deb` untuk sistem `python3` (roda padanan versi untuk virtualenvs terdapat dalam `/usr/lib/chloros/sdk/`; PyPI adalah untuk mesin tanpa pakej Chloros)
* **Kawalan kamera LATTICE** — cari, sambung, konfigurasi, dan rakam daripada kamera LATTICE dan susunan multi-kamera bersepadu melalui `chloros-cli lattice` dan SDK; `.deb` membundel runtime Arena SDK yang diperlukan oleh kamera
* **Kawalan penderia cahaya DAQ** — sambungkan penderia DAQ-U/M/E, siarkan spektra yang telah dikalibrasi, dan rakam fail `.daq` melalui `chloros-cli daq pool-*` dan SDK
* **Penyelarasan masa PTP** — backend Chloros menjalankan grandmaster PTP yang menjadi rujukan untuk kamera LATTICE dan penderia DAQ-E; inspeksikan dengan `chloros-cli time-sync`, dan jalankannya secara tanpa kepala dengan unit systemd `chloros-backend@<user>.service` (lihat [Pemasangan Linux](linux-installation.md#always-on-ptp-for-headless-hosts))
* **Otomasi projek** — jalankan projek yang disimpan secara tanpa papan pemuka dengan `chloros-cli project` dan `open_project` daripada SDK
* **Pecutan GPU** — pemprosesan dipercepatkan CUDA pada GPU NVIDIA (GPU desktop pada amd64, dan GPU Jetson dengan JetPack 6)
* **Penyesuaian Komputasi Dinamik** — pengesanan perkakasan automatik dan pemilihan strategi pemprosesan, dengan `CHLOROS_STRATEGY` sebagai jalan keluar pakar
* **Semua ciri pemprosesan** — saluran yang sama seperti Windows: penentukuran, pembetulan vignette, indeks vegetasi, dan setiap format eksport
* **Ciri-ciri Chloros+** — pemprosesan berbilang benang (berpipa), debayer Sedar Tekstur (pada GPU CUDA), dan indeks tersuai, dengan pelan berbayar Chloros+

## Apa yang Pengguna Linux Tidak Dapatkan

* **GUI Desktop** — tiada antara muka grafik; semua interaksi adalah melalui CLI atau Python SDK
* **Pemandang Imej** — tiada pemandang imej interaktif, paparan grid, atau penanda peta
* **Pengurusan projek visual** — projek dibuat dan dikendalikan melalui arahan CLI dan panggilan SDK (perkakasan itu sendiri — kamera, penderia, tangkapan — kekal boleh dikawal sepenuhnya dari terminal)***

## Keperluan Lesen

Akses CLI dan SDK memerlukan pelan **Chloros+ berbayar — Copper atau ke atas**(Copper, Bronze, Silver, Gold). Pelan**Iron** percuma tidak mempunyai akses CLI/SDK. Had ini dikuatkuasakan oleh backend, bukan hanya oleh CLI:

| Situasi | Tindak balas backend |
| --- | --- |
| Tidak log masuk | `401` dengan `error_code: AUTH_REQUIRED` |
| Log masuk pada peringkat Iron percuma | `403` dengan `error_code: PLAN_UPGRADE_REQUIRED` |

`chloros-cli status` berfungsi pada mana-mana peringkat — ia adalah satu-satunya laluan yang dikecualikan daripada pintu — jadi sebab penolakan sentiasa dapat dilihat.

***

## Memulakan pada Linux

1. **Pasang Chloros** — lihat [Pemasangan Linux](linux-installation.md) untuk pemasangan `.deb`
2. **Verifikasi** — `chloros-cli --version` mencetak `Chloros CLI 1.3.0`; `chloros-cli selftest` menjalankan diagnostik 7-langkah
3. **Semak Python SDK** — `.deb` memasangnya: `python3 -c "import chloros_sdk; print(chloros_sdk.__version__)"` (dalam virtualenv, `pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl`)
4. **Log masuk** — `chloros-cli login your@email.com 'your-password'` (sekali bagi setiap mesin; peningkatan pakej mengekalkan log masuk, menyahpasang pakej akan membersihkannya)
5. **Proses set data pertama anda** — `chloros-cli process ~/datasets/flight001`

Untuk NVIDIA Jetson, lihat [Panduan NVIDIA Jetson](nvidia-jetson-guide.md) khusus untuk persediaan platform tertentu, tingkah laku termal, dan penyebaran di lapangan.

***

## Langkah Seterusnya

* [Pemasangan Linux](linux-installation.md) — pemasangan terperinci, lokasi fail, dan penyelesaian masalah untuk amd64 dan arm64
* [Panduan NVIDIA Jetson](nvidia-jetson-guide.md) — penyediaan khusus Jetson, tingkah laku memori dan terma, penyebaran lapangan
* [CLI : Baris Perintah](../CLI.md) — panduan CLI
* [API : Python SDK](../api-python-sdk.md) — panduan SDK
* [Rujukan CLI](../reference/cli-reference.md) dan [Rujukan SDK](../reference/sdk-reference.md) — senarai lengkap arahan/API untuk 1.3.0
* [Penyesuaian Komputasi Dinamik](../processing-architecture/dynamic-compute-adaptation.md) — bagaimana Chloros menyesuaikan diri dengan perkakasan anda

{% hint style="info" %}
**Membaca manual ini secara berprogram.** Setiap halaman juga disajikan sebagai Markdown mentah di URLnya sendiri serta `.md` (contohnya `https://mapir.gitbook.io/chloros/linux/linux-installation.md`), dan indeks keseluruhan manual diterbitkan di [`https://mapir.gitbook.io/chloros/llms.txt`](https://mapir.gitbook.io/chloros/llms.txt).
{% endhint %}
