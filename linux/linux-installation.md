# Pemasangan Linux

Chloros diedarkan untuk Linux sebagai pakej `.deb` yang memasang CLI dan bahagian belakang. Python SDK dipasang secara berasingan melalui pip.

***

## Linux amd64 (x86_64)

### Keperluan Sistem

| Keperluan | Minimum | Disyorkan |
| --- | --- | --- |
| **Pengagihan** | Ubuntu 20.04+ / Debian 11+ | Ubuntu 22.04+ |
| **Pemproses** | x86_64 (Intel/AMD) | Intel Core i7 atau lebih baik |
| **Memori (RAM)** | 8GB | 16GB atau lebih |
| **Kad Grafik** | Tiada (pemprosesan CPU) | GPU NVIDIA dengan 4GB+ VRAM |
| **Storan** | 2GB ruang kosong | SSD dengan 10GB+ ruang kosong |
| **Python** | Python 3.7+ (untuk SDK) | Python 3.10+ |

### Pemasangan

Muat turun pakej `.deb` dan pasang:

```bash
sudo dpkg -i chloros-amd64.deb
```

Sahkan pemasangan:

```bash
chloros-cli --version
```

***

## Linux arm64 (NVIDIA Jetson)

### Keperluan Sistem

| Keperluan | Minimum | Disyorkan |
| --- | --- | --- |
| **Platform** | NVIDIA Jetson dengan JetPack 6 | Jetson Orin NX 16GB atau AGX Orin |
| **JetPack** | JetPack 6.x | JetPack 6 terkini |
| **Memori (RAM)** | 8GB (GPU/CPU dikongsi) | 16GB+ dikongsi |
| **Storan** | 2GB ruang kosong | NVMe SSD dengan 10GB+ percuma |
| **Python** | Python 3.7+ (untuk SDK) | Python 3.10+ |

### Pemasangan

Muat turun pakej JetPack 6 `.deb` dan pasang:

```bash
sudo dpkg -i chloros-arm64-jp6.deb
```

Sahkan pemasangan:

```bash
chloros-cli --version
```

Untuk persediaan Jetson terperinci termasuk pengurusan haba dan penggunaan medan, lihat [Panduan NVIDIA Jetson](nvidia-jetson-guide.md).

***

## Pemasangan Python SDK (Semua Linux)

Python SDK dipasang secara berasingan melalui pip dan berfungsi pada amd64 dan arm64:

```bash
pip install chloros-sdk
```

Untuk menyertakan sokongan penstriman kemajuan pilihan:

```bash
pip install chloros-sdk[progress]
```

Sahkan SDK:

```bash
python -c "import chloros_sdk; print(chloros_sdk.__version__)"
```

{% hint style="info" %}
Pakej `.deb` memasang Chloros CLI dan bahagian belakang. Python SDK ialah pakej pip berasingan yang berkomunikasi dengan bahagian belakang melalui HTTP API tempatan.
{% endhint %}

***

## Direktori Konfigurasi

Chloros pada Linux mengikut [Spesifikasi Direktori Pangkalan XDG](https://specifications.freedesktop.org/basedir-spec/basedir-spec-latest.html):

| Tujuan | Linux Laluan | Windows Setara |
| --- | --- | --- |
| **Tatarajah** | `~/.config/chloros/` | `%APPDATA%\Chloros\` |
| **Data / Projek** | `~/.local/share/chloros/` | `%LOCALAPPDATA%\Chloros\` |
| **Cache / Bukti kelayakan** | `~/.cache/chloros/` | `%APPDATA%\Chloros\cache\` |

## Lokasi Boleh Laksana Bahagian Belakang

Pakej `.deb` memasang bahagian belakang ke lokasi standard. CLI dan SDK auto-mengesan laluan belakang:

| Kaedah Pemasangan | Laluan Bahagian Belakang |
| --- | --- |
| Pakej `.deb` | `/usr/lib/chloros/chloros-backend` |
| Manual / tersuai | `/opt/mapir/chloros/backend/chloros-backend` |

Anda boleh mengatasi laluan hujung belakang dengan bendera `--backend-exe` CLI atau parameter pembina `backend_exe` SDK.

***

## Persediaan Kali Pertama

### 1. Aktifkan Lesen Anda

Lesen Chloros+ diperlukan untuk akses CLI dan SDK:

```bash
chloros-cli login your@email.com 'your-password'
```

### 2. Semak Status Lesen Anda

```bash
chloros-cli status
```

### 3. Proseskan Set Data Pertama Anda

```bash
chloros-cli process ~/datasets/flight001
```

### 4. Jalankan Diagnostik Sistem

Sahkan bahawa sistem anda dikonfigurasikan dengan betul:

```bash
chloros-cli selftest
```

Ini menjalankan 7 semakan diagnostik termasuk versi, permulaan bahagian belakang, sambungan API dan ketersediaan CUDA/GPU.

***

## Contoh Skrip Bash

### Proses Berbilang Set Data

```bash
#!/bin/bash
for dataset in ~/datasets/2026/*/; do
    echo "Processing $(basename "$dataset")..."
    chloros-cli process "$dataset" --format tiff-32
    echo "Done: $(basename "$dataset")"
done
```

### Proses dengan Tetapan Tersuai

```bash
#!/bin/bash
chloros-cli process ~/datasets/field_a \
    --output ~/output/field_a \
    --format tiff-32 \
    --indices NDVI NDRE GNDVI \
    --debayer texture-aware \
    --no-vignette
```

### Pemprosesan Automatik dengan Cron

Tambahkan pada crontab anda (`crontab -e`) untuk memproses set data baharu secara automatik:

```cron
# Process any new datasets at 2 AM daily
0 2 * ** /usr/bin/chloros-cli process /data/incoming --output /data/processed >> /var/log/chloros.log 2>&1
```

### Contoh Python SDK

```python
from chloros_sdk import process_folder

# One-line processing
result = process_folder(
    "/home/user/datasets/flight001",
    indices=["NDVI", "NDRE"],
    export_format="TIFF (32-bit, Percent)"
)
```

***

## Menyelesaikan masalah

### CLI Tidak Ditemui Selepas Pemasangan

Jika `chloros-cli` tidak ditemui selepas memasang pakej `.deb`:

```bash
# Check if the binary exists
which chloros-cli
ls -la /usr/bin/chloros-cli

# If not in PATH, check the installation
dpkg -L chloros-amd64  # or chloros-arm64-jp6

# Reload your shell
source ~/.bashrc
```

### Kebenaran Ditolak

```bash
# Ensure the binary is executable
sudo chmod +x /usr/bin/chloros-cli
sudo chmod +x /usr/lib/chloros/chloros-backend
```

### Bahagian Belakang Gagal Dimulakan

```bash
# Check if port 5000 is already in use
lsof -i :5000

# Kill any existing process on port 5000
kill $(lsof -t -i :5000)

# Try starting with a different port
chloros-cli --port 5001 process ~/datasets/flight001
```

### CUDA Tidak Dikesan

```bash
# Check NVIDIA driver installation
nvidia-smi

# Check CUDA availability
nvcc --version

# On Jetson, check JetPack version
cat /etc/nv_tegra_release
```

### Perpustakaan Kongsi Hilang

```bash
# Install common dependencies
sudo apt-get update
sudo apt-get install -f

# Check for missing libraries
ldd /usr/lib/chloros/chloros-backend | grep "not found"
```

***

## Mengemas kini Chloros pada Linux

Gunakan arahan kemas kini terbina dalam untuk menyemak dan memasang kemas kini:

```bash
# Check for updates without installing
chloros-cli update --check

# Check for and install updates
chloros-cli update
```

***

## Langkah Seterusnya

* [Panduan NVIDIA Jetson](nvidia-jetson-guide.md) — Pengoptimuman dan penggunaan khusus Jetson
* [CLI : Baris Perintah](../CLI.md) — Rujukan arahan penuh CLI
* [API : Python SDK](../api-python-sdk.md) — Rujukan penuh SDK
* [Penyesuaian Pengiraan Dinamik](../processing-architecture/dynamic-compute-adaptation.md) — Cara Chloros menyesuaikan diri dengan perkakasan anda