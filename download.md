---
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/download
---

# Muat turun

Muat turun versi terkini Chloros untuk bermula dengan pemprosesan imej berbilang spektrum.

### Keperluan Sistem

#### Windows

| Keperluan | Minimum | Disyorkan |
| -------------------- | ---------------------------------------------------- | ---------------------------------------------------- |
| **Sistem Pengendalian** | Windows 10 (64-bit) | Windows 11 (64-bit) |
| **Pemproses** | Intel Core i5 atau setara | Intel Core i7 atau lebih baik |
| **Memori (RAM)** | 8GB | 16GB atau lebih |
| **Kad Grafik** | DirectX 11 serasi | GPU NVIDIA dengan 4GB+ VRAM |
| **Storan** | 6GB ruang kosong | SSD dengan 10GB+ ruang kosong |
| **Paparan** | 1920x1080 | 2560x1440 atau lebih tinggi |
| **Internet** | Diperlukan untuk pengaktifan lesen \[pilihan] Chloros+ | Diperlukan untuk pengaktifan lesen \[pilihan] Chloros+ |

#### Linux amd64 (x86\_64)

| Keperluan | Minimum | Disyorkan |
| ----------------- | -------------------------- | -------------------------- |
| **Pengagihan** | Ubuntu 20.04+ / Debian 11+ | Ubuntu 22.04+ |
| **Pemproses** | x86\_64 (Intel/AMD) | Intel Core i7 atau lebih baik |
| **Memori (RAM)** | 8GB | 16GB atau lebih |
| **Kad Grafik** | Tiada (pemprosesan CPU) | GPU NVIDIA dengan 4GB+ VRAM |
| **Storan** | 2GB ruang kosong | SSD dengan 10GB+ percuma |
| **Python** | Python 3.7+ (untuk SDK) | Python 3.10+ |

#### Linux arm64 (NVIDIA Jetson)

| Keperluan | Minimum | Disyorkan |
| ---------------- | ---------------------------- | ------------------------------- |
| **Platform** | NVIDIA Jetson dengan JetPack 6 | Jetson Orin NX 16GB atau AGX Orin |
| **Memori (RAM)** | 8GB (GPU/CPU dikongsi) | 16GB+ dikongsi |
| **Storan** | 2GB ruang kosong | NVMe SSD dengan 10GB+ percuma |
| **Python** | Python 3.7+ (untuk SDK) | Python 3.10+ |

{% hint style="info" %}
**Pecutan GPU**: Pengguna Chloros+ dengan GPU NVIDIA boleh menggunakan pecutan CUDA untuk pemprosesan yang jauh lebih pantas. Ini berfungsi pada kedua-dua Windows (GPU desktop) dan Linux (GPU desktop dan NVIDIA Jetson). Pengguna Chloros+ juga mendapat pemprosesan berbilang benang untuk kelajuan maksimum.
{% endhint %}

***

## Muat turun Chloros

### Keluaran Stabil Terkini (23 Mac 2026): Versi 1.1.0

### <a href="https://drive.google.com/uc?export=download&#x26;id=1HjwrUY4M7HGxDbMybO7iPe_6JoHnUGr4" class="button primary">Muat turun Chloros untuk Windows (.exe)</a>

### <a href="https://drive.google.com/uc?export=download&#x26;id=1dB8-ke3wxNXpw_e1qJ4BhwBpCoNd4kLS" class="button primary">Muat turun Chloros untuk Linux amd64 (.deb)</a>

### <a href="https://drive.google.com/uc?export=download&#x26;id=1d1OwdcYA4Rf4jkuPi2IBeWT2772_HnyO" class="button primary">Muat turun Chloros untuk Linux arm64 / Jetson (.deb)</a>

#### Pemasang Windows (GUI + CLI + Bahagian Belakang)

* **Jenis Fail**: .exe (Pemasang Windows)**Langkah Pemasangan:**

1. Muat turun fail .exe di atas
2. Dwiklik pemasang untuk memulakan pemasangan
3. Ikut gesaan wizard pemasangan
4. Pilih direktori pemasangan (lalai: `C:\Program Files\[USER]\Chloros\`)
5. Selesaikan pemasangan dan lancarkan Chloros atau Chloros CLI
6. Log masuk dengan akaun [MAPIR Cloud Chloros+ anda](https://cloud.mapir.camera/pricing) (atau teruskan dengan versi percuma)

{% hint style="success" %}
Pemasang secara automatik menambah `chloros-cli` pada PATH sistem anda untuk akses baris arahan.
{% endhint %}

#### Linux amd64 (.Pakej deb — CLI + Bahagian Belakang)

* **Jenis Fail**: .deb (pakej Debian/Ubuntu)
* **Seni Bina**: x86\_64 (amd64)

```bash
sudo dpkg -i chloros-amd64.deb
chloros-cli --version  # Verify installation
```

#### Linux arm64 — NVIDIA Jetson (Pakej .deb — CLI + Bahagian Belakang)

* **Jenis Fail**: .deb (JetPack 6)
* **Seni Bina**: aarch64 (arm64)

```bash
sudo dpkg -i chloros-arm64-jp6.deb
chloros-cli --version  # Verify installation
```

Lihat [Linux Installation](linux/linux-installation.md) untuk arahan persediaan terperinci dan [NVIDIA Jetson Guide](linux/nvidia-jetson-guide.md) untuk panduan khusus Jetson.

#### Python SDK (Semua Platform)

```bash
pip install chloros-sdk
```

Lihat [API : Python SDK](api-python-sdk.md) untuk dokumentasi.

{% hint style="info" %}
**Pengguna Linux**: Pakej `.deb` memasang CLI dan bahagian belakang. Python SDK dipasang secara berasingan melalui pip. Tiada GUI untuk Linux — semua interaksi adalah melalui CLI atau SDK.
{% endhint %}

***

## Sumber Tambahan

### Python SDK

Untuk pemaju dan aliran kerja automasi, pasang Chloros Python SDK:

```bash
pip install chloros-sdk
```

**Dokumentasi**: [API: Python SDK](api-python-sdk.md)**Keperluan**: Chloros mesti dipasang (pemasang Windows atau Linux `.deb` pakej), Chloros+ log masuk lesen diperlukan***

## Apa yang Termasuk

### Pemasang Windows

* ✅ **Chloros GUI** - Antara muka grafik berciri penuh
* ✅ **Chloros CLI** - Antara muka baris perintah (memerlukan lesen Chloros+)
* ✅ **Chloros Backend** - Enjin pemprosesan
* ✅ **Profil Kamera** - Templat kamera MAPIR prakonfigurasi

### Pakej Linux .deb

* ✅ **Chloros CLI** - Antara muka baris perintah (memerlukan lesen Chloros+)
* ✅ **Chloros Backend** - Enjin pemprosesan
* ✅ **Profil Kamera** - Templat kamera MAPIR prakonfigurasi
* ❌ Tiada GUI — Linux tanpa kepala CLI/SDK sahaja

### Python SDK (pip, semua platform)

* ✅ **Chloros SDK** - Python API (memerlukan lesen Chloros+)***

## Naik taraf kepada Chloros+

Buka kunci ciri lanjutan dengan langganan Chloros+:

* 🚀 **Pemprosesan Berbilang Benang** - Proses imej secara selari
* ⚡ **Pecutan GPU (CUDA)** - Manfaatkan kuasa GPU NVIDIA
* 💻 **CLI Access** - Automatik dengan alatan baris arahan
* 🍅 **Python SDK** - Akses API Terprogram
* 📱 **Berbilang Peranti** - Gunakan pada 2-10+ peranti (bergantung kepada pelan)
* **🐻 Kaedah Debayer Sedar Tekstur Lanjutan** - debayer sedar tepi berkualiti tinggi digabungkan dengan model denoising AI/ML yang menghilangkan hampir semua bunyi debayering.
* 🧮 **Formula Tersuai** - Buat indeks berbilang spektrum tersuai

<p align="center"><a href="https://cloud.mapir.camera/pricing" class="button primary">Lihat Pelan Chloros+ &#x26; Harga</a></p>

***

## Bantuan Pemasangan

### Menyelesaikan masalah

**Pemasangan gagal dengan mesej ralat:**

* Pastikan anda mempunyai hak pentadbir
* Lumpuhkan sementara perisian antivirus
* Pastikan anda memenuhi keperluan sistem minimum

**Permohonan tidak akan bermula (Windows):**

* Sahkan Windows 10/11 (64-bit) dipasang
* Kemas kini pemacu grafik
* Semak Windows Event Viewer untuk butiran ralat
* Hubungi sokongan dengan log ralat

**CLI tidak akan bermula (Linux):**

* Sahkan pakej `.deb` dipasang dengan betul: `dpkg -l | grep chloros`
* Semak kebenaran: `sudo chmod +x /usr/bin/chloros-cli`
* Jalankan diagnostik: `chloros-cli selftest`
* Semak perpustakaan yang hilang: `ldd /usr/lib/chloros/chloros-backend | grep "not found"`

**Isu pengaktifan lesen:**

* Pastikan sambungan internet aktif
* Sahkan kelayakan di [https://cloud.mapir.camera](https://cloud.mapir.camera)
* Semak firewall tidak menyekat Chloros
* Lihat [Chloros+ Log Masuk](chloros+-login.md) untuk arahan terperinci

### Mendapat Sokongan

Perlukan bantuan dengan pemasangan atau persediaan?

* 📧 **E-mel**: info@mapir.camera
* 🌐 **Tapak web**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* 📚 **Dokumentasi**: [Bermula](./)
* ❓ **Soalan Lazim**: [Soalan Lazim](faq.md)***

## Log Tukar

<butiran>

<summary>Versi 1.1.0 (Terbaru)</summary>

**Tarikh Tayangan: Mac 2026**

**Ciri Baharu*** **Sokongan Linux** — CLI dan SDK asli untuk Linux amd64 (x86\_64) dan arm64 (NVIDIA Jetson JetPack 6). Pasang melalui pakej `.deb`.
* **Sokongan NVIDIA Jetson** — Pemprosesan dioptimumkan untuk peranti tepi Jetson Nano, Orin Nano, Orin NX dan AGX Orin.
* **Penyesuaian Pengiraan Dinamik** — Pengesanan perkakasan automatik dan pengoptimuman strategi pemprosesan. Chloros menyesuaikan diri dengan perkakasan anda daripada Jetson Nano kepada stesen kerja berbilang GPU.
* **Saluran Paip Pemprosesan 4-Benang** — Pengesanan Serentak, Penentukuran, Pemprosesan dan Eksport benang dengan peruntukan memori GPU dinamik.
* **Perintah CLI baharu** — `selftest` (diagnostik sistem) dan `update` (pengurusan kemas kini Linux).
* **Bendera Proses CLI baharu** — `--debayer` (standard/sedar tekstur), `--indices` (nyatakan indeks), `--target` (cari subfolder sasaran terlebih dahulu untuk pengesanan lebih pantas).
* **Item Menu GUI Baharu** — Tambah Fail, Tambah Folder dan Mula/Hentikan Pemprosesan kini boleh diakses daripada menu lungsur utama.**Peningkatan**

* Pengesanan auto bahagian belakang merentas platform (laluan Windows dan Linux)
* SDK `get_status()` dipertingkat dengan penjejakan kemajuan setiap utas
* Pengecualian SDK baharu: `ChlorosConfigurationError`, `ChlorosAuthenticationError`
* Pengurusan terma dan pendikit penyesuaian untuk NVIDIA Jetson
* Pengurusan memori automatik dengan sandaran OOM kepada pemprosesan GPU berjubin

</detail>

<butiran>

<summary>Versi 1.0.5</summary>

**Tarikh Tayangan: 10 Februari 2026**

**Ciri Baharu*** **Kaedah Texture Aware Debayer \[Chloros+ Only] -** Texture Aware menggunakan debayer sedar tepi berkualiti tinggi digabungkan dengan model denoising AI/ML yang menghilangkan hampir semua hingar debayering.
* **Sokongan untuk Sasaran Penentukuran T4P*** **Pemprosesan GPU Chloros+ yang lebih pantas, pengurusan memori yang lebih baik**

**Pembetulan Pepijat*** Bahagian hadapan (GUI) baharu sepenuhnya, seharusnya berfungsi pada semua komputer Windows sekarang.

</detail>

<butiran>

<summary>Versi 1.0.4</summary>

**Tarikh Tayangan: 5 Januari 2026**

**Ciri Baharu*** **Togol Imej/Metadata**: Togol ditambahkan dalam Pelayar Fail untuk melihat metadata imej yang dipilih dalam jadual dan bukannya grid imej
* **Peluncur Zum Grid Imej**: Peluncur UI baharu untuk melaraskan saiz lakaran kenit (juga menyokong CTRL + roda tetikus)
* **Butang Eksport Grid Imej**: Butang di baris atas untuk menukar imej kecil daripada JPG kepada eksport yang diproses (Sasaran, Reflectance, Indeks, LUT)
* **Tab Peta**: Peta 2D interaktif baharu yang menunjukkan imej penanda lokasi GPS
  * Menyokong Peta Google dan jubin peta ESRI (auto-memilih perkhidmatan jubin terbaik berdasarkan ketersediaan tahap zum)
  * Pratonton lakaran kecil tuding tetikus pada penanda peta

**Pembetulan Pepijat*** Sokongan yang dipertingkatkan untuk memasang Chloros pada komputer bukan bahasa Inggeris

</detail>

<butiran>

<summary>Versi 1.0.3</summary>

**Tarikh Tayangan: 20 Disember 2025**

**Ciri Baharu*** Pelancaran Awal

**Peningkatan*** Pelancaran Awal

**Pembetulan Pepijat*** Pelancaran Awal

**Isu Diketahui*** Pelancaran Awal

</detail>

***

## Perjanjian Lesen**Perisian Milik** - Hak Cipta (c) 2026 MAPIR Inc.

Penggunaan, pengedaran atau pengubahsuaian yang tidak dibenarkan adalah dilarang.

**Versi Percuma**: Tersedia untuk kegunaan peribadi dan komersial dengan pengehadan ciri**Chloros+**: Lesen berasaskan langganan untuk ciri lanjutan dan penggunaan komersial