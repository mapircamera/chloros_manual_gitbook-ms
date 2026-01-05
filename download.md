---
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/download
---

# Muat turun

Muat turun versi terkini Chloros untuk bermula dengan pemprosesan imej berbilang spektrum.

### Keperluan Sistem

| Keperluan | Minimum | Disyorkan |
| -------------------- | ------------------------------- | ------------------------------- |
| **Sistem Pengendalian** | Windows 10 (64-bit) | Windows 11 (64-bit) |
| **Pemproses** | Intel Core i5 atau setara | Intel Core i7 atau lebih baik |
| **Memori (RAM)** | 8GB | 16GB atau lebih |
| **Kad Grafik** | DirectX 11 serasi | GPU NVIDIA dengan 4GB+ VRAM |
| **Storan** | 6GB ruang kosong | SSD dengan 10GB+ ruang kosong |
| **Paparan** | 1920x1080 | 2560x1440 atau lebih tinggi |
| **Internet** | Diperlukan untuk pengaktifan lesen | Diperlukan untuk pengaktifan lesen |

{% gaya petunjuk="info" %}
**Pecutan GPU**: Pengguna Chloros+ dengan GPU NVIDIA (4GB+ VRAM) boleh menggunakan pecutan CUDA untuk pemprosesan yang jauh lebih pantas. Pengguna Chloros+ juga mendapat pemprosesan berbilang benang untuk kelajuan maksimum.
Petua {% %}

***

## Muat turun Chloros

### <a href="https://drive.google.com/file/d/1HjwrUY4M7HGxDbMybO7iPe_6JoHnUGr4/view?usp=drive_link" class="button primary">Muat turun Chloros Di Sini</a>

### Keluaran Stabil Terkini

**Chloros Installer untuk Windows*** **Versi**: 1.0.4
* **Tarikh Tayangan**: 5 Januari 2026
* **Saiz Fail (Muat Turun)**: 1.8GB
* **Saiz Fail (Dipasang)**: 5.7GB
* **Jenis Fail**: .exe (Pemasang Windows)

#### **Langkah Pemasangan:**

1. Muat turun fail `CHLOROS INSTALLER - CURRENT VERSION.exe`
2. Dwiklik pemasang untuk memulakan pemasangan
3. Ikut gesaan wizard pemasangan
4. Pilih direktori pemasangan (lalai: `C:\Program Files\[USER]\Chloros\`)
5. Selesaikan pemasangan dan lancarkan Chloros, Chloros (Pelayar) atau Chloros CLI
6. Log masuk dengan akaun [MAPIR Cloud Chloros+ anda](https://cloud.mapir.camera/pricing) (atau teruskan dengan versi percuma)

{% gaya petunjuk="berjaya" %}
Pemasang secara automatik menambah `chloros-cli` pada PATH sistem anda untuk akses baris arahan.
Petua {% %}

***

## Sumber Tambahan

### Python SDK

Untuk pemaju dan aliran kerja automasi, pasang Chloros Python SDK:

```bash
pip install chloros-sdk
```

**Dokumentasi**: [API: Python SDK](api-python-sdk.md)**Keperluan**: Desktop Chloros mesti dipasang, log masuk lesen Chloros+ diperlukan***

## Apa yang Termasuk

Pemasangan Chloros termasuk:

* ✅ **Chloros** - Antara muka grafik berciri penuh
* ✅ **Chloros (Pelayar)** - Antara muka berasaskan web untuk sistem berspesifikasi rendah
* ✅ **Chloros CLI** - Antara muka baris perintah (memerlukan lesen Chloros+)
* ✅ **Chloros SDK** - Python API (memerlukan lesen Chloros+)
* ✅ **Profil Kamera** - Templat kamera MAPIR prakonfigurasi***

## Naik taraf kepada Chloros+

Buka kunci ciri lanjutan dengan langganan Chloros+:

* 🚀 **Pemprosesan Berbilang Benang** - Proses imej secara selari
* ⚡ **Pecutan GPU (CUDA)** - Manfaatkan kuasa GPU NVIDIA
* 💻 **CLI Access** - Automatik dengan alatan baris arahan
* 🍅 **Python SDK** - Akses API Terprogram
* 📱 **Berbilang Peranti** - Gunakan pada 2-10+ peranti (bergantung kepada pelan)
* 🧮 **Formula Tersuai** - Buat indeks berbilang spektrum tersuai

<p align="center"><a href="https://cloud.mapir.camera/pricing" class="button primary">Lihat Pelan Chloros+ &#x26; Harga</a></p>

***

## Bantuan Pemasangan

### Menyelesaikan masalah

**Pemasangan gagal dengan mesej ralat:**

* Pastikan anda mempunyai hak pentadbir
* Lumpuhkan sementara perisian antivirus
* Pastikan anda memenuhi keperluan sistem minimum

**Permohonan tidak akan bermula:**

* Cuba versi Chloros (Pelayar).
* Sahkan Windows 10/11 (64-bit) dipasang
* Kemas kini pemacu grafik
* Semak Windows Event Viewer untuk butiran ralat
* Hubungi sokongan dengan log ralat

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

<summary>Versi 1.0.4</summary>

#### **Tarikh Tayangan**: 5 Januari 2026**Ciri Baharu*** **Togol Imej/Metadata**: Togol ditambahkan dalam Pelayar Fail untuk melihat metadata imej yang dipilih dalam jadual dan bukannya grid imej
* **Peluncur Zum Grid Imej**: Peluncur UI baharu untuk melaraskan saiz lakaran kenit (juga menyokong CTRL + roda tetikus)
* **Butang Eksport Grid Imej**: Butang di baris atas untuk menukar imej kecil daripada JPG kepada eksport yang diproses (Sasaran, Reflectance, Indeks, LUT)
* **Tab Peta**: Peta 2D interaktif baharu yang menunjukkan imej penanda lokasi GPS
  * Menyokong Peta Google dan jubin peta ESRI (auto-memilih perkhidmatan jubin terbaik berdasarkan ketersediaan tahap zum)
  * Pratonton lakaran kecil tuding tetikus pada penanda peta

**Pembetulan Pepijat*** Sokongan yang dipertingkatkan untuk memasang Chloros pada komputer bukan bahasa Inggeris

</detail>

<butiran>

<summary>Versi 1.0.3</summary>

#### **Tarikh Tayangan**: 20 Disember 2025**Ciri Baharu*** Pelancaran Awal

**Peningkatan*** Pelancaran Awal

**Pembetulan Pepijat*** Pelancaran Awal

**Isu Diketahui*** Pelancaran Awal

</detail>

***

## Perjanjian Lesen**Perisian Milik** - Hak Cipta (c) 2025 MAPIR Inc.

Penggunaan, pengedaran atau pengubahsuaian yang tidak dibenarkan adalah dilarang.

**Versi Percuma**: Tersedia untuk kegunaan peribadi dan komersial dengan pengehadan ciri**Chloros+**: Lesen berasaskan langganan untuk ciri lanjutan dan penggunaan komersial