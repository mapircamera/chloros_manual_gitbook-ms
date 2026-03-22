# Panduan NVIDIA Jetson

Chloros pada NVIDIA Jetson membolehkan pemprosesan imej berbilang spektrum di tepi — di lapangan, pada UAV dan dalam pemasangan jauh. Chloros secara automatik mengesan model Jetson anda dan mengoptimumkan strategi pemprosesannya untuk perkakasan anda.

***

## Model Jetson yang Disokong

| Model | RAM | Strategi Pemprosesan | Penggunaan Disyorkan |
| -------------------- | -------------- | ---------------------------------------------------- | -------------------------------------------------------- |
| **Jetson AGX Orin** | 32-64GB dikongsi | `GPU_PARALLEL` (4 pekerja) | Prestasi maksimum, set data besar |
| **Jetson Orin NX** | 8-16GB dikongsi | `GPU_PARALLEL` (3 pekerja, 16GB) / `GPU_SINGLE` (8GB) | Pengesyoran utama untuk penggunaan udara dan lapangan |
| **Jetson Orin Nano** | 8GB dikongsi | `GPU_SINGLE` (1 pekerja) | Pengiraan tepi peringkat permulaan |
| **Jetson Nano** | 4-8GB dikongsi | `GPU_SINGLE` (1 pekerja) | Tahap kemasukan, kekangan ingatan |

{% hint style="info" %}
**Model Jetson warisan** (TX2, TX1, Xavier NX) mungkin tidak disokong. Prestasi akan berbeza-beza berdasarkan memori GPU yang tersedia dan keupayaan CUDA.
{% endhint %}

***

## Keperluan

* **JetPack 6.x** (terbaru disyorkan)
* **NVIDIA CUDA** (disertakan dengan JetPack)
* **lesen Chloros+** (diperlukan untuk akses CLI/SDK)

## Pemasangan

```bash
# Install the JetPack 6 .deb package
sudo dpkg -i chloros-arm64-jp6.deb

# Verify installation
chloros-cli --version

# Install Python SDK (optional)
pip install chloros-sdk

# Run system diagnostics
chloros-cli selftest
```

Untuk butiran pemasangan Linux umum, lihat [Linux Installation](linux-installation.md).

***

## Penyesuaian Pengiraan Dinamik pada Jetson

Chloros secara automatik mengesan model Jetson anda dan memilih strategi pemprosesan yang optimum. **Tiada penalaan manual diperlukan.**

### Cara Ia Berfungsi

Pada permulaan, Chloros memprofilkan sistem anda:

1. **Mengesan model Jetson** melalui `/proc/device-tree/model`
2. **Membaca GPU/memori kongsi yang tersedia**

3.**Memilih strategi pemprosesan** (`GPU_PARALLEL`, `GPU_SINGLE` atau `CPU_PARALLEL`)
4. **Menetapkan kiraan pekerja, jenis saluran paip dan peruntukan memori** secara automatik

### Gelagat Setiap Model

| Model Jetson | Strategi | Pekerja | Talian Paip | Concurrency |
| --------------------------- | -------------- | ------- | ------------------------------ | ----------- |
| **Jetson Nano 8GB** | `GPU_SINGLE` | 1 | `tiled_gpu` (jimat ingatan) | Bersiri |
| **Jetson Orin Nano 8GB** | `GPU_SINGLE` | 1 | `tiled_gpu` | Bersiri |
| **Jetson Orin NX 8GB** | `GPU_SINGLE` | 2 | `tiled_gpu` | Bersiri |
| **Jetson Orin NX 16GB** | `GPU_PARALLEL` | 3 | `fused_gpu` (laluan GPU penuh) | Serentak |
| **Jetson AGX Orin 32-64GB** | `GPU_PARALLEL` | 4 | `fused_gpu` | Serentak |

{% hint style="success" %}
**Jetson Orin NX 16GB** ialah tempat menarik untuk penggunaan kelebihan — ia menerima strategi `GPU_PARALLEL` dengan 3 pekerja serentak, memberikan pemprosesan GPU selari sebenar dalam faktor bentuk yang padat.
{% endhint %}

Perbezaan utama antara platform ialah **memori**. Jetson Nano dengan 8GB memori dikongsi mesti memproses imej satu demi satu menggunakan pendekatan jubin yang cekap memori, manakala Orin NX dengan 16GB boleh menjalankan 3 imej melalui GPU secara serentak menggunakan saluran paip bercantum yang lebih tinggi.

Untuk rujukan penyesuaian pengiraan yang lengkap, lihat [Penyesuaian Pengiraan Dinamik](../processing-architecture/dynamic-compute-adaptation.md).

***

## Pengurusan Terma

Peranti Jetson mempunyai ruang kepala haba yang terhad, terutamanya dalam penggunaan tertutup atau bawaan udara. Chloros termasuk pemantauan terma automatik dan pendikit:

| Suhu | Tindakan |
| ------------------- | ------------------------------------------------- |
| ***70°C** | Operasi biasa — kelajuan pemprosesan penuh |
| **70°C** (Amaran) | Kurangkan saiz kelompok secara automatik |
| **80°C** (Kritis) | Pendikitan agresif — serentak bawah |
| **90°C** (Tutup) | Hentikan pemprosesan GPU sepenuhnya — menyejukkan badan diperlukan |

{% hint style="warning" %}
**Pastikan pengudaraan dan penenggelaman haba yang mencukupi** untuk pemprosesan yang berterusan, terutamanya dalam kepungan medan tertutup atau sistem bawaan udara. Pendikitan haba akan mengurangkan daya pemprosesan untuk melindungi perkakasan.
{% endhint %}

***

## Pengurusan Memori

Peranti Jetson menggunakan **memori bersatu** — GPU dan CPU berkongsi RAM fizikal yang sama. Ini bermakna VRAM yang dilaporkan (cth., 15.3GB pada Orin NX 16GB) bukan memori GPU khusus; ia dikongsi dengan sistem pengendalian dan proses lain.

### Syor Tukar

Untuk set data yang besar atau pemprosesan debayer Texture Aware, Chloros mungkin mengesyorkan membuat ruang swap:

```bash
# Check current memory and swap
free -h

# Create a swap file (example: 8GB)
sudo fallocate -l 8G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# Make persistent across reboots
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

**Anggaran memori setiap imej:**

* Debayer standard: \~10 MB setiap imej
* Texture Aware debayer: \~15 MB setiap imej

Chloros mengira memori yang diperlukan secara automatik berdasarkan saiz set data anda dan memberi amaran kepada anda jika pertukaran disyorkan.

### OOM (Kehabisan Ingatan) Saling Balik

Jika keadaan kehabisan ingatan dikesan semasa pemprosesan:

1. Chloros secara automatik mengurangkan kiraan pekerja GPU
2. Jatuh kembali daripada saluran paip `fused_gpu` kepada `tiled_gpu` (lebih cekap memori)
3. Meneruskan pemprosesan pada daya pengeluaran yang dikurangkan daripada ranap

***

## Penerapan Medan

### Pertimbangan Kuasa

| Model Jetson | Cabutan Kuasa Biasa | Nota |
| ---------------- | ------------------- | ------------------------ |
| Jetson Nano | 5-10W | USB-C atau bicu tong |
| Jetson Orin Nano | 7-15W | Bicu tong DC |
| Jetson Orin NX | 10-25W | Bicu tong DC |
| Jetson AGX Orin | 15-60W | USB-C PD atau bicu tong |

Rancang belanjawan kuasa anda untuk pemprosesan yang berterusan — cabutan kuasa puncak berlaku semasa Benang 3 (Pemprosesan) intensif GPU.

### Cadangan Storan

* **NVMe SSD** amat disyorkan untuk penggunaan arm64
* Kad SD terlalu lambat untuk diproses — gunakan sebagai media but sahaja
* Rancang untuk 2-3x saiz data imej mentah anda untuk output yang diproses

### Operasi Tanpa Kepala melalui SSH

Chloros CLI sesuai untuk penggunaan Jetson tanpa kepala:

```bash
# SSH into the Jetson
ssh user@jetson-hostname

# Process a dataset
chloros-cli process /data/datasets/flight001 --format tiff-32

# Monitor export progress
chloros-cli export-status
```

### Pemprosesan Automatik dengan systemd

Buat perkhidmatan systemd untuk pemprosesan automatik:

```ini
# /etc/systemd/system/chloros-process.service
[Unit]
Description=Chloros Automated Processing
After=network.target

[Service]
Type=oneshot
User=chloros
ExecStart=/usr/bin/chloros-cli process /data/incoming --output /data/processed
StandardOutput=append:/var/log/chloros-process.log
StandardError=append:/var/log/chloros-process.log

[Install]
WantedBy=multi-user.target
```

Pasangkan dengan pemasa systemd untuk pemprosesan berjadual:

```ini
# /etc/systemd/system/chloros-process.timer
[Unit]
Description=Run Chloros Processing Every Hour

[Timer]
OnCalendar=hourly
Persistent=true

[Install]
WantedBy=timers.target
```

```bash
sudo systemctl enable chloros-process.timer
sudo systemctl start chloros-process.timer
```

***

## Contoh Aliran Kerja

### Pemprosesan Jetson Asas

```bash
#!/bin/bash
# Process a drone flight dataset on Jetson
chloros-cli process /data/flights/flight_042 \
    --output /data/processed/flight_042 \
    --format tiff-32 \
    --indices NDVI NDRE GNDVI
```

### Python SDK pada Jetson

```python
from chloros_sdk import ChlorosLocal

with ChlorosLocal() as chloros:
    chloros.create_project("field_survey_042")
    chloros.import_images("/data/flights/flight_042")
    chloros.configure(
        indices=["NDVI", "NDRE", "GNDVI"],
        export_format="TIFF (32-bit, Percent)",
        reflectance_calibration=True
    )
    chloros.process(mode="parallel")

print("Processing complete!")
```

### Memproses Berkelompok Penerbangan Berbilang

```bash
#!/bin/bash
# Process all flight datasets in a directory
for flight in /data/flights/*/; do
    name=$(basename "$flight")
    echo "Processing $name..."
    chloros-cli process "$flight" \
        --output "/data/processed/$name" \
        --format tiff-32 \
        --indices NDVI NDRE
    echo "Completed $name"
done
```

***

## Sistem Jetson Disyorkan untuk Penggunaan Medan

Untuk penempatan lapangan dan udara, pertimbangkan pilihan papan pembawa Jetson Orin NX 16GB ini:

* **Airborne/drone**: Sistem dengan penarafan getaran (MIL-STD), ringan (di bawah 300g), penyejukan pasif
* **Medan lasak**: Penutup kalis air IP67/IP69K dengan sambungan kamera PoE GigE
* **Minimum/belanjawan**: Kit pembangun dengan lampiran tambahan

Hubungi [MAPIR Support](https://www.mapir.camera/community/contact) untuk mendapatkan pengesyoran perkakasan khusus untuk senario penggunaan anda.

***

## Langkah Seterusnya

* [Pemasangan Linux](linux-installation.md) — Butiran pemasangan Linux umum
* [Penyesuaian Pengiraan Dinamik](../processing-architecture/dynamic-compute-adaptation.md) — Rujukan strategi pengiraan penuh
* [Memproses Paip](../processing-architecture/processing-pipeline.md) — Memahami saluran paip 4-benang
* [CLI : Command Line](../CLI.md) — Rujukan penuh CLI
* [API : Python SDK](../api-python-sdk.md) — Rujukan penuh SDK