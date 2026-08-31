---
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/download
---

# Muat Turun

Muat turun versi terkiniChloros

untuk memulakan pemprosesan imej multispektral.

### Keperluan Sistem

####Windows



| Keperluan          | Minimum                                              | Disyorkan                                          |
| -------------------- | ---------------------------------------------------- | ---------------------------------------------------- |
| **Sistem Pengendalian** |Windows

10 (64-bit)                                  |Windows

11 (64-bit)                                  |
| **Pemproses**        | Intel Core i5 atau setaraf                          | Intel Core i7 atau lebih baik                              |
| **Memori (RAM)**     | 8GB                                                  | 16GB atau lebih                                         |
| **Kad Grafik**      | Serasi DirectX 11                                | NVIDIA GPU dengan 4GB+ VRAM                            |
| **Simpanan**          | 6GB ruang kosong                                       | SSD dengan 10GB+ ruang kosong                            |
| **Paparan**          | 1920x1080                                            | 2560x1440 atau lebih tinggi                                  |
| **Internet**         | Diperlukan untuk pengaktifan lesen \[pilihan]Chloros

+ | Diperlukan untuk pengaktifan lesen \[pilihan]Chloros

+ |

####Linux

amd64 (x86\_64)

| Keperluan       | Minimum                    | Disyorkan               |
| ----------------- | -------------------------- | ------------------------- |
| **Pengedaran**  | Ubuntu 22.04 LTS+ / Debian 12+ | Ubuntu 24.04 LTS      |
| **Pemproses**     | x86\_64 (Intel/AMD)        | Intel Core i7 atau lebih baik  |
| **Memori (RAM)**  | 8GB                        | 16GB atau lebih              |
| **Kad Grafik**    | Tiada (pemprosesan CPU)      | Kadar NVIDIA dengan VRAM 4GB+ |
| **Penyimpanan**       | Ruang kosong 2GB             | SSD dengan ruang kosong 10GB+      |
| **Python**        |Python

3.7+ (untukSDK

)      |Python

3.10+              |

####Linux

arm64 (NVIDIA Jetson)

| Keperluan      | Minimum                      | Disyorkan                     |
| ---------------- | ---------------------------- | ------------------------------- |
| **Platform**     | NVIDIA Jetson dengan JetPack 6 | Jetson Orin NX 16GB atau AGX Orin |
| **Memori (RAM)** | 8GB (dikongsi GPU/CPU)         | 16GB+ dikongsi                    |
| **Penyimpanan**   | 2GB ruang kosong               | SSD NVMe dengan 10GB+ ruang kosong |
| **Python**       |Python

3.7+ (untukSDK

)        |Python

3.10+                    |

{% hint style="info" %}
**Pecutan GPU**: PenggunaChloros

+ dengan GPU NVIDIA boleh menggunakan pecutan CUDA untuk pemprosesan yang jauh lebih pantas. Ini berfungsi pada kedua-duaWindows

(GPU desktop) danLinux

(GPU desktop dan NVIDIA Jetson). PenggunaChloros

+ juga mendapat pemprosesan berbilang benang untuk kelajuan maksimum.
{% endhint %}

***

## Muat turunChloros

### Versi Terkini Stabil: Versi 1.2.0

<!-- NOLAN: replace installer links + release date for 1.2.0 — the three download buttons below still point at the 1.1.0 Google Drive files, and the release date needs to be added to the heading above. -->



### <a href="https://drive.google.com/uc?export=download&#x26;id=1HjwrUY4M7HGxDbMybO7iPe_6JoHnUGr4" class="button primary">Muat turun Chloros untuk Windows (.exe)</a>



### <a href="https://drive.google.com/uc?export=download&#x26;id=1dB8-ke3wxNXpw_e1qJ4BhwBpCoNd4kLS" class="button primary">Muat turun Chloros untuk Linux amd64 (.deb)</a>



### <a href="https://drive.google.com/uc?export=download&#x26;id=1d1OwdcYA4Rf4jkuPi2IBeWT2772_HnyO" class="button primary">Muat turun Chloros untuk Linux arm64 / Jetson (.deb)</a>



#### PemasangWindows

(GUI +CLI

+ Backend)

* **Jenis Fail**: .exe (PemasangWindows

)

**Langkah-langkah Pemasangan:**

1. Muat turun fail .exe di atas
2. Klik dua kali pada pemasang untuk memulakan pemasangan
3. Ikuti arahan wizard pemasangan
4. Pilih direktori pemasangan (lalai: `C:\Program Files\MAPIR\Chloros\`)
5. Lengkapkan pemasangan dan lancarkanChloros

atauChloros

CLI

6. Log masuk dengan akaun [MAPIR

CloudChloros

+ anda](https://cloud.mapir.camera/pricing) (atau teruskan dengan versi percuma)

{% hint style="success" %}
Pemasang secara automatik menambah `chloros-cli` ke dalam PATH sistem anda untuk capaian baris perintah.
{% endhint %}

####Linux

amd64 (Pakej .deb —CLI

+ Backend)

* **Jenis Fail**: .deb (pekali Debian/Ubuntu)
* **Senibina**: x86_64 (amd64)

```bash
sudo dpkg -i chloros-amd64.deb
chloros-cli --version  # Verify installation
```

####Linux

arm64 — NVIDIA Jetson (.deb Package —CLI

+ Backend)

* **Jenis Fail**: .deb (JetPack 6)
* **Arkitek**: aarch64 (arm64)

```bash
sudo dpkg -i chloros-arm64-jp6.deb
chloros-cli --version  # Verify installation
```

Lihat [PemasanganLinux

](linux/linux-installation.md) untuk arahan persediaan terperinci dan [Panduan NVIDIA Jetson](linux/nvidia-jetson-guide.md) untuk panduan khusus Jetson.

####Python

SDK

(Semua Platform)

Setiap pemasang disertakan dengan roda `chloros_sdk` yang sepadan, jadi versiSDK

sentiasa sepadan dengan GUI/CLI

/backend yang dipasang. PadaWindows

, pemasang akan memasangnya ke dalam sistem anda secara automatik; padaLinux

, `.deb` meletakkan roda pada `/usr/lib/chloros/sdk/` dan mencetak arahan pemasangan:

```bash
pip install --user /usr/lib/chloros/sdk/chloros_sdk-*.whl
```

Untuk hos pip sahaja (tiada pakejChloros

dipasang),SDK

juga terdapat di PyPI:

```bash
pip install chloros-sdk
```

Rujuk [API

:Python

SDK

](api-python-sdk.md) dan [RujukanSDK

](reference/sdk-reference.md) untuk dokumentasi.

{% hint style="info" %}
**PenggunaLinux**: Pakej `.deb` memasangCLI

dan backend. Tiada GUI untukLinux

— semua interaksi adalah melaluiCLI

atauSDK

.
{% endhint %}

***

## Sumber Tambahan

###Python

SDK



Untuk pembangun dan aliran kerja automasi, pasangChloros

Python

SDK

:

```bash
pip install chloros-sdk
```

**Dokumentasi**: [API

:Python

SDK

](api-python-sdk.md)

**Prasyarat**:Chloros

mesti dipasang (pemasangWindows

atau pakejLinux

`.deb`), log masuk lesenChloros

+ diperlukan

***

## Apa yang Termasuk

### PemasangWindows



* ✅ **GUIChloros** - Antaramuka grafik berperolehan penuh
* ✅ **Antaramuka Baris PerintahChloros

CLI

** - Antaramuka baris perintah (perlukan lesenChloros

+)
* ✅ **BackendChloros** - Enjin pemprosesan
* ✅ **Profil Kamera** - Templat kameraMAPIR

yang telah dikonfigurasikan

### Pakej .debLinux



* ✅ **Antaramuka Baris PerintahChloros

CLI

** - Antaramuka baris perintah (perlukan lesenChloros

+)
* ✅ **BackendChloros** - Enjin pemprosesan
* ✅ **Profil Kamera** - Templat kameraMAPIR

yang telah dikonfigurasikan
* ❌ Tiada GUI —Linux

adalah tanpa kepala, hanyaCLI

/SDK



###Python

SDK

(pip, semua platform)

* ✅ **Chloros

SDK

** -Python

API

(perlukan lesenChloros

+)

***

## Tingkatkan keChloros

+

Buka ciri lanjutan dengan langgananChloros

+:

* 🚀 **Pemprosesan Berbenang Pelbagai** - Proses imej secara selari
* ⚡ **Pecutan GPU (CUDA)** - Manfaatkan kuasa GPU NVIDIA
* 💻 **AksesCLI** - Automasi dengan alat baris perintah
* 🐍 **Python

SDK

** - AksesAPI

secara berperaturan
* 📱 **Pelbagai Peranti** - Gunakan pada 2-10+ peranti (bergantung pada pelan)
* **🐻 Kaedah Debayer Cerdas Lanjutan** - debayer bermutu tinggi yang peka pada sempadan digabungkan dengan model penyahbisuan AI/ML yang membuang hampir semua bunyi debayering.
* 🧮 **Formula Tersuai** - Buat indeks multispektral tersuai

<p align="center"><a href="https://cloud.mapir.camera/pricing" class="button primary">Lihat Pelan &amp; Harga Chloros+</a></p>***

## Bantuan Pemasangan

### Penyelesaian Masalah

**Pemasangan gagal dengan mesej ralat:**

* Pastikan anda mempunyai hak pentadbir
* Lumpuhkan perisian antivirus buat sementara
* Semak sama ada anda memenuhi keperluan sistem minimum

**Aplikasi tidak dapat dibuka (Windows

):**

* Semak sama adaWindows

10/11 (64-bit) telah dipasang
* Kemas kini pemacu grafik
* Semak Event ViewerWindows

untuk butiran ralat
* Hubungi sokongan dengan log ralat

**CLI

tidak dapat dimulakan (Linux

):**

* Semak sama ada pakej `.deb` dipasang dengan betul: `dpkg -l | grep chloros`
* Semak kebenaran: `sudo chmod +x /usr/bin/chloros-cli`
* Jalankan diagnostik: `chloros-cli selftest`
* Semak perpustakaan yang hilang: `ldd /usr/lib/chloros/chloros-backend | grep "not found"`

**Masalah pengaktifan lesen:**

* Pastikan sambungan internet aktif
* Semak kelayakan di [https://cloud.mapir.camera](https://cloud.mapir.camera)
* Semak firewall tidak menyekatChloros

* Lihat [Chloros

+ Login](chloros+-login.md) untuk arahan terperinci

### Mendapatkan Sokongan

Perlukan bantuan dengan pemasangan atau penyediaan?

* 📧 **E-mel**: info@mapir.camera
* 🌐 **Laman web**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* 📚 **Dokumentasi**: [Getting Started](./)
* ❓ **Soalan Lazim**: [Frequently Asked Questions](faq.md)***

## Kemas Kini Perisian

Chloros

menyemak kemas kini, memaklumkan apabila versi baru tersedia, dan memautkan ke halaman muat turun ini — anda mengemas kini dengan menjalankan pemasang baru yang disahkan. Tetapan dan projek anda kekal selepas kemas kini. PadaLinux

dan Jetson, `chloros-cli update` menyemak versi yang lebih baru dan menawarkan untuk memuat turun dan memasang `.deb` yang sepadan (perintah ini hanya untukLinux

).

***

## Log Perubahan**Versi 1.2.0 (Terbaru)**— lihat**Apa yang Baru dalamChloros

1.2.0** di halaman [Getting Started](./) untuk senarai penuh ciri.

<details>

<summary>Versi 1.0.5</summary>

**Tarikh Keluaran: 10 Februari 2026**

**Ciri Baharu*** **Kaedah Debayer Peka Tekstur \[ Hanya 9@deepl.internal] -** Texture Aware menggunakan debayer peka tepi berkualiti tinggi yang digabungkan dengan model penyahbisuan AI/ML yang membuang hampir semua bunyi debayering.
* **Sokongan untuk Sasaran Kalibrasi T4P*** **Pemprosesan GPUChloros

+ yang lebih pantas, pengurusan memori yang lebih baik**

**Pembetulan pepijat*** Antaramuka hadapan (GUI) baharu sepenuhnya, kini sepatutnya berfungsi pada semua komputerWindows

.

</details>

<details>

<summary>Versi 1.0.4</summary>

**Tarikh Keluaran: 5 Januari 2026**

**Ciri Baharu*** **Togol Imej/Metadata**: Ditambah togol dalam Pelayar Fail untuk melihat metadata imej terpilih dalam jadual dan bukannya grid imej
* **Gelangsar Zum Grid Imej**: Gelangsar UI baharu untuk melaras saiz miniatur (juga menyokong CTRL + roda tetikus)
* **Butang Eksport Grid Imej**: Butang di baris atas untuk menukar pratonton kecil daripada JPG kepada eksport yang diproses (Targets, Reflectance, Index, LUT)
* **Tab Peta**: Peta 2D interaktif baru yang memaparkan penanda lokasi GPS imej
  * Menyokong Google Maps dan jubin peta ESRI (memilih secara automatik perkhidmatan jubin terbaik berdasarkan ketersediaan tahap zum)
  * Pratonton imbasan kecil pada penanda peta apabila tetikus diletakkan di atasnya

**Pembetulan pepijat*** Sokongan dipertingkatkan untuk memasang Chloros pada komputer berbahasa bukan Inggeris

</details>

<details>

<summary>Versi 1.0.3</summary>

**Tarikh Keluaran: 20 Disember 2025**

**Ciri Baharu*** Pelancaran Awal

**Peningkatan*** Pelancaran Awal

**Pembetulan pepijat*** Pelancaran Awal

**Masalah Yang Dikenali*** Pelancaran Awal

</details>

***

## Perjanjian Lesen**Perisian Milik** - Hak Cipta (c) 2026 MAPIR Inc.

Penggunaan, pengedaran, atau pengubahsuaian tanpa kebenaran adalah dilarang.

**Versi Percuma**: Tersedia untuk kegunaan peribadi dan komersial dengan had ciri**Chloros+**: Lesen berasaskan langganan untuk ciri lanjutan dan penyebaran komersial
