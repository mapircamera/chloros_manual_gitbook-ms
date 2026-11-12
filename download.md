---
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/download
---

# Muat Turun

Muat turun versi terkini Chloros untuk memulakan pemprosesan imej multispektral.

### Keperluan Sistem

#### Windows


| Keperluan          | Minimum                                              | Disyorkan                                          |
| -------------------- | ---------------------------------------------------- | ---------------------------------------------------- |
| **Sistem Pengendalian** | Windows 10 (64-bit)                                  | Windows 11 (64-bit)                                  |
| **Pemproses**        | Intel Core i5 atau setaraf                          | Intel Core i7 atau lebih baik                              |
| **Memori (RAM)**     | 8GB                                                  | 16GB atau lebih                                         |
| **Kad Grafik**    | Serasi DirectX 11                                | GPU NVIDIA dengan VRAM 4GB+                            |
| **Penyimpanan**      | Ruang kosong 6GB                                      | SSD dengan ruang kosong 10GB+                            |
| **Paparan**          | 1920x1080                                            | 2560x1440 atau lebih tinggi                                  |
| **Internet**         | Diperlukan untuk pengaktifan lesen \[pilihan] Chloros+ | Diperlukan untuk pengaktifan lesen \[pilihan] Chloros+ |

#### Linux amd64 (x86_64)

| Keperluan       | Minimum                    | Disyorkan               |
| ----------------- | -------------------------- | ------------------------- |
| **Distribusi**  | Ubuntu 22.04 LTS+ / Debian 12+ | Ubuntu 24.04 LTS      |
| **Pemproses**     | x86\_64 (Intel/AMD)        | Intel Core i7 atau lebih baik   |
| **Memori (RAM)**  | 8GB                        | 16GB atau lebih              |
| **Kad Grafik**    | Tiada (pemprosesan CPU)      | Kadar NVIDIA dengan VRAM 4GB+ |
| **Simpanan**       | Ruang kosong 2GB             | SSD dengan ruang kosong 10GB+      |
| **Python**        | Python 3.7+ (untuk SDK)      | Python 3.10+              |

#### Linux arm64 (NVIDIA Jetson)

| Keperluan      | Minimum                      | Disyorkan                     |
| ---------------- | ---------------------------- | ------------------------------- |
| **Platform**     | NVIDIA Jetson dengan JetPack 6 | Jetson Orin NX 16GB atau AGX Orin |
| **Memori (RAM)** | 8GB (dikongsi GPU/CPU)         | 16GB+ dikongsi                    |
| **Penyimpanan**      | Ruang kosong 2GB               | SSD NVMe dengan ruang kosong 10GB+        |
| **Python**       | Python 3.7+ (untuk SDK)        | Python 3.10+                    |

{% hint style="info" %}
**Pecutan GPU**: Pengguna Chloros+ dengan GPU NVIDIA boleh menggunakan pecutan CUDA untuk pemprosesan yang jauh lebih pantas. Ini berfungsi dengan GPU desktop NVIDIA pada Windows dan Linux amd64, dan pada NVIDIA Jetson dengan JetPack 6. Pengguna Chloros+ juga mendapat pemprosesan berbilang benang untuk kelajuan maksimum.
{% endhint %}

***

## Muat Turun Chloros


### Versi Tetap Terbaru: Versi 1.3.0 (11 November 2026)

### <a href="https://drive.google.com/uc?export=download&#x26;id=1HjwrUY4M7HGxDbMybO7iPe_6JoHnUGr4" class="button primary">Muat turun Chloros untuk Windows (.exe)</a>


### <a href="https://drive.google.com/uc?export=download&#x26;id=1dB8-ke3wxNXpw_e1qJ4BhwBpCoNd4kLS" class="button primary">Muat turun Chloros untuk Linux amd64 (.deb)</a>


### <a href="https://drive.google.com/uc?export=download&#x26;id=1d1OwdcYA4Rf4jkuPi2IBeWT2772_HnyO" class="button primary">Muat turun Chloros untuk Linux arm64 / Jetson (.deb)</a>


#### Pemasang Windows (GUI + CLI + Backend)

* **Jenis Fail**: .exe (Pemasang Windows)

**Langkah Pemasangan:**

1. Muat turun fail .exe di atas
2. Klik dua kali pemasang untuk memulakan pemasangan
3. Ikuti arahan wizard pemasangan
4. Pilih direktori pemasangan (lalai: `C:\Program Files\Chloros\`)
5. Pada halaman **Pilih Tugas Tambahan**, biarkan kedua-dua pilihan**Tetapan Sistem** dicentang (disyorkan — lihat di bawah)
6. Siapkan pemasangan dan lancarkan Chloros atau Chloros CLI
7. Log masuk dengan akaun [MAPIR Cloud Chloros+](https://cloud.mapir.camera/pricing) (atau teruskan dengan versi percuma)

**Pilihan tetapan sistem** (kedua-duanya dipilih secara lalai):

| Pilihan, seperti yang tertulis dalam pemasang | Fungsinya | Opt-out pemasangan senyap |
| --- | --- | --- |
| **Benarkan laluan panjang Windows** (membolehkan folder projek berada lebih dalam daripada 260 aksara; disyorkan) | MenetapkWindows's machine-wide `LongPathsEnabled` setting kepada 1. Windows datang dengan tetapan ini dimatikan, dan kemudian tiada laluan fail boleh lebih panjang daripada 260 aksara; produk Chloros meletakkan sehingga kira-kira 130 aksara di bawah folder projek, jadi dengan laluan panjang dimatikan, folder projek yang lebih dalam akan ditolak. Berkuat kuasa untuk program yang dimulakan selepas pemasangan. Ia kekal diaktifkan selepas menyahpasang, kerana program lain mungkin bergantung padanya. | `/MERGETASKS="!longpaths"` |
| **Kecualikan backend Chloros daripada imbasan Microsoft Defender** (perekodan kamera lebih pantas; disyorkan) | Menambah pengecualian proses Microsoft Defender untuk `chloros-backend.exe` dalam folder pemasangan, supaya perlindungan masa nyata tidak mengimbas setiap fail yang ditulis oleh backend, di mana sahaja ia menulisnya. Pada kadar tangkapan yang tinggi, imbasan menelan kos kira-kira 30% daripada kadar tangkapan (77.7 berbanding 52.7 tangkapan sesaat dalam tangkapan berterusan 9 kamera). Tiada program lain dikecualikan, dan pengecualian itu akan dialihkan semasa menyahpasang. Sekiranya Defender tiada atau dikunci oleh dasar, langkah ini akan gagal secara senyap dan pemasangan akan diteruskan. | `/MERGETASKS="!defenderexclusion"` |

Pemasangan senyap (`/SILENT` atau `/VERYSILENT`) menggunakan kedua-dua pilihan melainkan anda menolak dengan nilai `/MERGETASKS` yang ditunjukkan.

{% hint style="success" %}
Pemasang menambah folder CLI (`C:\Program Files\Chloros\cli\`) ke sistem `PATH` anda sekali sahaja — memasang semula atau menaik taraf tidak lagi menambah entri duplikat, dan membuang duplikat yang ditambah oleh pemasang terdahulu. Buka terminal baru selepas pemasangan untuk menggunakan `chloros-cli`. Pemasang juga menambah dua peraturan Windows Firewall untuk backend, pada setiap profil rangkaian: **Backend Chloros (UDP-Masuk)**, untuk aliran kamera, PTP, mDNS dan aliran data DAQ-E, dan**Backend Chloros (TCP-Masuk)**, terhad kepada subnet tempatan, untuk kemas kini firmware DAQ-E, di mana sensor menyambung semula ke komputer ini untuk mengumpul imej. Kedua-duanya akan dialih keluar semasa menyahpasang.
{% endhint %}

#### Linux amd64 (.deb Package — CLI + Backend)

* **Jenis Fail**: .deb (pakej Debian/Ubuntu)
* **Arkitek**: x86_64 (amd64)

```bash
sudo apt install ./chloros_1.3.0_amd64.deb
chloros-cli --version     # Verify installation: "Chloros CLI 1.3.0"
```

#### Linux arm64 — NVIDIA Jetson (.deb Package — CLI + Backend)

* **Jenis Fail**: .deb (JetPack 6)
* **Senibina**: aarch64 (arm64)

```bash
sudo apt install ./chloros_1.3.0_arm64_jp6.deb
chloros-cli --version     # Verify installation: "Chloros CLI 1.3.0"
```

Gunakan nama fail yang sebenarnya anda muat turun, dan biarkan `./` supaya `apt` memasang fail itu dan bukannya mencari pakej mengikut nama. Pasang dengan `apt install`, bukan `dpkg -i`: apt juga memasang keperluan Python SDK, yang disyorkan oleh pakej tersebut dan bukannya diwajibkan, dan `dpkg -i` melangkau mereka. Sejak versi 1.3.0, backend dan CLI dipasang secara tidak dikemas di bawah `/usr/lib/chloros/`, jadi ia akan bermula tanpa membuka kemasan terlebih dahulu.

Pakej Jetson menggunakan binaan CUDA PyTorch JetPack 6, jadi pemprosesan, termasuk debayer Texture Aware, dijalankan pada GPU Jetson. Ia menggunakan pustaka CUDA dan cuDNN yang dipasang oleh JetPack 6 (`sudo apt install nvidia-jetpack` pada imej yang tiada pustaka tersebut).

Lihat [Pemasangan [Linux](linux/linux-installation.md) untuk arahan penyediaan terperinci dan [Panduan NVIDIA Jetson](linux/nvidia-jetson-guide.md) untuk panduan khusus Jetson.

#### Python SDK (Semua Platform)

Setiap pemasang menyertakan roda `chloros_sdk` yang sepadan, jadi versi SDK sentiasa sepadan dengan GUI/CLI/backend yang dipasang. Pada Windows, pemasang akan memasangnya ke dalam Python sistem anda secara automatik. Pada Linux, `.deb` memasang SDK untuk sistem `python3` itu sendiri (ke dalam `/usr/lib/python3/dist-packages`), supaya `import chloros_sdk` berfungsi sebaik sahaja dipasang, tanpa pip dan tanpa rangkaian. Hanya virtualenv, yang tidak melihat pakej sistem, memerlukan wheel terbungkus dipasang ke dalamnya:

```bash
pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl
```

Untuk hos yang hanya menggunakan pip (tiada pakej Chloros dipasang; SDK kemudian memacu backend pada mesin lain), SDK juga terdapat di PyPI:

```bash
pip install chloros-sdk
```

Rujuk [API: Python SDK](api-python-sdk.md) dan [Rujukan SDK](reference/sdk-reference.md) untuk dokumentasi.

{% hint style="info" %}
**Pengguna**Linux **: Pakej `.deb` memasang CLI, backend dan Python SDK. Tiada GUI untuk Linux — semua interaksi adalah melalui CLI atau SDK.
{% endhint %}

***

## Sumber Tambahan

### Python SDK


Untuk pembangun dan aliran kerja automasi, Chloros Python SDK disertakan dengan setiap pemasang: pemasang Windows memasangnya ke dalam Python anda, dan pemasang Linux `.deb` memasangnya untuk sistem `python3`. Pada mesin yang tiada pakej Chloros (yang mengendalikan backend pada mesin lain), pasang ia dari PyPI:

```bash
pip install chloros-sdk
```

**Dokumentasi**: [API: Python SDK](api-python-sdk.md)

**Prasyarat**: backend Chloros untuk dikendalikan (pemasang Windows atau pakej `.deb` untuk Linux memasangnya), dan log masuk Chloros+ pada pelan berbayar

***

## Apa yang Disertakan

### Pemasang Windows


* ✅ **GUI Chloros** - Antaramuka grafik berperincian penuh
* ✅ **CLI CLI** - Antaramuka baris perintah (perlukan lesen Chloros+)
* ✅ **Backend Chloros** - Enjin pemprosesan
* ✅ **Python SDK** - dipasang ke dalam Python anda apabila ia wujud (perlukan lesen Chloros+ )
* ✅ **Profil Kamera** - templat kamera MAPIR yang telah dikonfigurasikan

### Pakej .deb Linux


* ✅ **Chloros CLI** - Antaramuka baris perintah (perlukan lesen Chloros+ )
* ✅ **Backend Chloros** - Enjin pemprosesan
* ✅ **Python SDK** - dipasang untuk sistem `python3` (perlukan lesen Chloros+ )
* ✅ **Profil Kamera** - Templat kamera MAPIR yang telah dikonfigurasikan
* ❌ Tiada GUI — Linux hanya CLI/SDK tanpa kepala

### Python SDK SDK (PyPI, untuk mesin tanpa pakej Chloros)

* ✅ **Chloros SDK SDK** - Python API (perlukan lesen Chloros+ Chloros)

***

## Tingkatkan ke Chloros+

Buka ciri lanjutan dengan langganan Chloros+:

* 🚀 **Pemprosesan Berbenang Pelbagai** - Proses imej secara selari
* ⚡ **Pecutan GPU (CUDA)** - Manfaatkan kuasa GPU NVIDIA
* 💻 **Akses CLI** - Automasi dengan alat baris perintah
* 🐍 **Python SDK** - Akses API secara berprogram
* 📱 **Pelbagai Peranti** - Gunakan pada 2-10+ peranti (bergantung pada pelan)
* **🐻 Kaedah Debayer Canggih Berpengetahuan Teksatur** - debayer berpengetahuan sempadan berkualiti tinggi yang digabungkan dengan model penyahbisin AI/ML yang membuang hampir semua bunyi debayering.
* 🧮 **Formula Tersuai** - Buat indeks multispektral tersuai

<p align="center"><a href="https://cloud.mapir.camera/pricing" class="button primary">Lihat Pelan & Harga Chloros+</a></p>

***

## Bantuan Pemasangan

### Penyelesaian Masalah

**Pemasangan gagal dengan mesej ralat:**

* Pastikan anda mempunyai hak pentadbir
* Lumpuhkan perisian antivirus buat sementara
* Semak sama ada anda memenuhi keperluan sistem minimum

**Aplikasi tidak dapat dimulakan (Windows):**

* Semak sama ada Windows 10/11 (64-bit) telah dipasang
* Kemas kini pemacu grafik
* Semak Pemerhati Acara Windows untuk butiran ralat
* Hubungi sokongan dengan log ralat

**CLI tidak dapat dimulakan (Linux):**

* Semak sama ada pakej `.deb` dipasang dengan betul: `dpkg -l | grep chloros`
* Semak kebenaran: `sudo chmod +x /usr/bin/chloros-cli`
* Jalankan diagnostik: `chloros-cli selftest`
* Semak perpustakaan yang hilang: `ldd /usr/lib/chloros/backend/chloros-backend | grep "not found"` (sejak 1.3.0, `/usr/lib/chloros/chloros-backend` adalah skrip pelancar kecil; program itu sendiri berada di `/usr/lib/chloros/backend/`)

**Masalah pengaktifan lesen:**

* Pastikan sambungan internet aktif
* Semak kelayakan di [https://cloud.mapir.camera](https://cloud.mapir.camera)
* Semak jika firewall menyekat Chloros

* Lihat [Chloros+ Login](chloros+-login.md) untuk arahan terperinci

### Mendapatkan Sokongan

Perlukan bantuan dengan pemasangan atau penyediaan?

* 📧 **E-mel**: info@mapir.camera
* 🌐 **Laman web**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* 📚 **Dokumentasi**: [Getting Started](./)
* ❓ **Soalan Lazim**: [Frequently Asked Questions](faq.md)

***

## Kemas Kini Perisian

Chloros memeriksa kemas kini, memaklumkan apabila versi baru tersedia, dan memautkan ke halaman muat turun ini — anda mengemas kini dengan menjalankan pemasang baru yang disahkan. Tetapan anda, projek dan log masuk anda kekal selepas kemas kini. Pada Linux dan Jetson, `chloros-cli update` menyemak versi yang lebih baru dan menawarkan untuk memuat turun dan memasang `.deb` yang sepadan (perintah ini hanya untuk Linux).

***

## Log Perubahan**Versi 1.3.0 (Terbaru, 11 November 2026)**— lihat**Apa Yang Baharu dalam Chloros

1.3.0** di halaman [Getting Started](./) untuk senarai penuh.**Versi 1.2.0**— lihat**Apa Yang Baru dalam Chloros 1.2.0** di halaman [Getting Started](./).

<details>

<summary>Versi 1.0.5</summary>

**Tarikh Keluaran: 10 Februari 2026**

**Ciri Baharu**

* **Kaedah Debayer Sedar Tekstur \[Hanya Chloros+] -** Texture Aware menggunakan debayer sedar sempadan berkualiti tinggi yang digabungkan dengan model penyahbisuan AI/ML yang membuang hampir semua bunyi debayering.
* **Sokongan untuk Sasaran Kalibrasi T4P**
* **Pemprosesan GPU Chloros+ yang lebih pantas, pengurusan memori yang lebih baik**

**Pembetulan pepijat**

* Antaramuka depan (GUI) baharu sepenuhnya, kini sepatutnya berfungsi pada semua komputer Windows.

</details>

<details>

<summary>Versi 1.0.4</summary>

**Tarikh Keluaran: 5 Januari 2026**

**Ciri Baharu**

* **Togol Imej/Metadata**: Ditambah togol dalam Pelayar Fail untuk melihat metadata imej terpilih dalam jadual dan bukannya grid imej
* **Gelangsar Zum Grid Imej**: Gelangsar UI baharu untuk melaras saiz miniatur (juga menyokong CTRL + roda tetikus)
* **Butang Eksport Grid Imej**: Butang di baris atas untuk menukar pratonton kecil daripada JPG kepada eksport yang diproses (Sasaran, Refleksan, Indeks, LUT)
* **Tab Peta**: Peta 2D interaktif baru yang memaparkan penanda lokasi GPS imej
  * Menyokong Google Maps dan jubin peta ESRI (memilih secara automatik perkhidmatan jubin terbaik berdasarkan ketersediaan tahap zum)
  * Pratonton imbasan kecil pada penanda peta apabila tetikus diletakkan di atasnya

**Pembetulan pepijat**

* Sokongan yang dipertingkatkan untuk memasang Chloros pada komputer berbahasa bukan Inggeris

</details>

<details>

<summary>Versi 1.0.3</summary>

**Tarikh Keluaran: 20 Disember 2025**

**Ciri-ciri Baharu**

* Pelancaran Awal

**Peningkatan**

* Pelancaran Awal

**Pembetulan pepijat**

* Pelancaran Awal

**Masalah Yang Dikenali**

* Pelancaran Awal

</details>

***

## Perjanjian Lesen**Perisian Milik** - Hak Cipta (c) 2026 MAPIR Inc.

Penggunaan, pengedaran, atau pengubahsuaian tanpa kebenaran adalah dilarang.

**Versi Percuma**: Tersedia untuk kegunaan peribadi dan komersial dengan had ciri**Chloros+**: Lesen berasaskan langganan untuk ciri lanjutan dan penyebaran komersial
