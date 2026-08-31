# Panduan NVIDIA Jetson  

Chloros

pada NVIDIA Jetson membolehkan pemprosesan imej multispektral di hujung — di lapangan, pada UAV, dan di pemasangan jauh.Chloros

1.2.0 mengesan model Jetson anda semasa permulaan dan mengoptimumkan strategi pemprosesannya untuk perkakasan yang dikesan. **Tiada pelarasan manual diperlukan.**

***

## Model Jetson yang Disokong

| Model                | RAM            | Strategi Pemprosesan                                     | Penggunaan Disyorkan                                          |
| -------------------- | -------------- | ------------------------------------------------------- | -------------------------------------------------------- |
| **Jetson AGX Orin**  | 32-64GB dikongsi | `GPU_PARALLEL` (2 pekerja)                              | Prestasi maksimum, set data besar                      |
| **Jetson Orin NX**   | 8-16GB dikongsi  | `GPU_PARALLEL` (2 pekerja, 16GB) / `GPU_SINGLE` (8GB)   | Cadangan utama untuk penerbangan dan penyebaran di lapangan |
| **Jetson Orin Nano** | 8GB dikongsi     | `GPU_SINGLE` (1 pekerja, bersiri)                     | Komputasi tepi peringkat permulaan                        |

{% hint style="info" %}
Pakej arm64Linux

memerlukan **JetPack 6**, yang tersedia pada keluarga Jetson Orin. Model yang lebih lama (Nano, TX2, Xavier NX) tidak dapat menjalankan JetPack 6 dan tidak disokong oleh pakej semasa.
{% endhint %}

***

## Keperluan

* **JetPack 6.x** (yang terkini disyorkan)
* **NVIDIA CUDA** (termasuk dalam JetPack)
* **PelanChloros

+ berbayar** — Tahap Copper atau ke atas (diperlukan untuk semua aksesCLI

/SDK

; dikuatkuasakan di pihak pelayan)

## Pemasangan

```bash
# Install the JetPack 6 .deb package
sudo dpkg -i chloros_1.2.0_arm64_jp6.deb
sudo apt-get install -f

# Verify installation
chloros-cli --version    # prints "Chloros CLI 1.2.0"

# Install Python SDK (optional) — the bundled wheel always matches this build
pip install --user /usr/lib/chloros/sdk/chloros_sdk-*.whl

# Run system diagnostics
chloros-cli selftest
```

Untuk butiran pemasanganLinux

umum, lokasi fail, dan penyelesaian masalah, lihat [PemasanganLinux

](linux-installation.md).

{% hint style="info" %}
**Letakkan direktori ekstraksi pada storan pantas.** Biner yang telah disusun akan membuka diri ke dalam direktori sementara pada setiap pelancaran — sangat perlahan daripada kad SD.Chloros

menggunakan `/mnt/ssd/tmp` secara automatik apabila ia wujud; jika tidak, tetapkan `TMPDIR` kepada laluan pada NVMe anda (`export TMPDIR=/mnt/nvme/tmp`).
{% endhint %}

***

## Adaptasi Komputasi Dinamik pada Jetson

### Cara Kerjanya

Semasa permulaan,Chloros

membuat profil sistem anda:

1. **Mendedeteksi model Jetson** melalui `/proc/device-tree/model`
2. **Membaca memori GPU/CPU kongsi yang tersedia** (Jetson menggunakan memori bersatu)
3. **Memilih strategi pemprosesan** (`GPU_PARALLEL`, `GPU_SINGLE`, atau `CPU_PARALLEL`)
4. **Menetapkan bilangan pekerja, jenis saluran paip, dan peruntukan memori** secara automatik

Keputusan ditentukan oleh **jumlah RAM kongsi**, bukan oleh nama model:

* **Kurang daripada 12GB jumlah RAM**(semua Jetson 8GB): `GPU_SINGLE` dengan**1 pekerja — pemprosesan bersiri yang disengajakan**. Memori terlalu terhad untuk pekerja serentak, jadi imej diproses satu pada satu masa. Pada Jetsons dengan**8GB atau kurang**, Thread 3 melangkau sepenuhnya kolam pekerja dan menjalankan kerjanya bagi setiap imej secara dalam proses.
* **12GB atau lebih** (Orin NX 16GB, AGX Orin): memori bersatu layak untuk `GPU_PARALLEL`, tetapi bilangan pekerja dihadkan kepada 2 pada Jetson — GPU, RAM proses pekerja, dan konteks CUDA per-pekerja semuanya menggunakan kolam kongsi yang sama, jadi lebih ramai pekerja berisiko mengalami kegagalan kehabisan memori.

Anda boleh menimpa pilihan automatik dengan pembolehubah persekitaran `CHLOROS_STRATEGY` — lihat [Dynamic Compute Adaptation](../processing-architecture/dynamic-compute-adaptation.md#manual-strategy-override).

### Kelakuan Per-Model

| Model Jetson                | Strategi       | Pekerja | Pelaksanaan                                      |
| --------------------------- | -------------- | ------- | ---------------------------------------------- |
| **Jetson Orin Nano 8GB**    | `GPU_SINGLE`   | 1       | Gelung bersiri dalam proses (`tiled_gpu` di bawah tekanan memori) |
| **Jetson Orin NX 8GB**      | `GPU_SINGLE`   | 1       | Gelung bersiri dalam proses                     |
| **Jetson Orin NX 16GB**     | `GPU_PARALLEL` | 2       | Proses pekerja serentak, laluan `fused_gpu` |
| **Jetson AGX Orin 32-64GB** | `GPU_PARALLEL` | 2       | Proses pekerja serentak, laluan `fused_gpu` |

Perbezaan utama antara platform ialah **memori**. Jetson 8GB perlu memproses imej satu persatu menggunakan pendekatan berpetak yang cekap memori apabila tekanan tinggi, manakala Orin 16GB+ boleh menjalankan 2 imej melalui GPU serentak menggunakan paip bersepadu berprestasi tinggi.

### Belanjawan GPU Per-Model

Setiap model Jetson juga mempunyai profil perkakasan yang mengehadkan berapa banyak pemprosesan daripada kolam kongsi yang boleh dituntut, dan menentukur saiz kelompok:

| Model | Had belanjawan GPU | Pengganda saiz kelompok | Ditempah untuk sistem/paparan |
| --- | --- | --- | --- |
| **Jetson Orin Nano** | 70% | ×0.8 | 2.0 GB |
| **Jetson Orin NX** | 75% | ×1.0 | 3.0 GB |
| **Jetson AGX Orin** | 80% | ×1.5 | 4.0 GB |

RAM yang dikesan menyesuaikan profil: Jetson yang melaporkan **16GB atau lebih** akan meningkatkan pengganda batch-nya sebanyak ×1.2. Saiz batch asas sebelum pengganda ialah 8 imej.

Untuk rujukan penyesuaian komputasi lengkap, lihat [Dynamic Compute Adaptation](../processing-architecture/dynamic-compute-adaptation.md).

***

## Had Frekuensi GPU untuk Texture Aware pada Nano dan Orin Nano

Debayer Texture Aware menjalankan inferens rangkaian neural GPU, yang boleh mencetuskan **amaran arus berlebihan**pada model Jetson berkuasa rendah (kelas 10-15W) pada kelajuan jam GPU penuh. Sebelum pemprosesan Texture Aware pada**Jetson Nano atau Orin Nano**,Chloros

memeriksa frekuensi maksimum GPU dan mengehadkannya kepada **510 MHz** (510000000) jika ia pada masa ini lebih tinggi:

* JikaCLI

dapat menulis nod sysfs frekuensi GPU, had itu **diterapkan secara automatik** dan pengesahan akan dicetak.
* Jika tidak (perlu root),CLI

mencetak arahan `sudo` yang tepat untuk menerapkan had secara manual, menunggu seketika supaya anda boleh membacanya, kemudian meneruskan — pemprosesan masih berjalan tetapi mungkin akan memaparkan amaran arus berlebihan.

Untuk menerapkan had sendiri sebelum pemprosesan:

```bash
echo 510000000 | sudo tee /sys/devices/platform/bus@0/17000000.gpu/devfreq/17000000.gpu/max_freq
```

Model berkuasa tinggi (Orin NX 25W, AGX Orin 60W) berjalan pada kelajuan GPU penuh; tiada had yang dikenakan. Standard debayer tidak pernah mencetuskan had pada mana-mana model.

{% hint style="info" %}
**Texture Aware pada Jetson sentiasa menjalankan satu imej pada satu masa.** Setiap pekerja memerlukan konteks CUDA sendiri (~1GB) serta salinan model penapis hingar (denoiser) sendiri, yang memori bersatu tidak mampu sediakan — jadi pada Jetson, laluan Texture Aware diikat pada satu pekerja dengan capaian GPU diserialisasikan. Jangka Texture Aware akan menjadi lebih perlahan berbanding Standard pada mana-mana Jetson.
{% endhint %}

***

## Pengurusan Terma

Peranti Jetson mempunyai had terma yang terhad, terutamanya dalam penyebaran tertutup atau di udara.Chloros

memantau suhu SoC dan secara automatik mengehadkan saiz kumpulan:

| Suhu         | Tindakan                                            |
| ------------------- | ------------------------------------------------- |
| **&lt; 70°C**          | Operasi biasa — kelajuan pemprosesan penuh          |
| **70°C** (Amaran) | Saiz batch dikurangkan secara berperingkat (100% → 50% antara 70°C dan 80°C) |
| **80°C** (Kritikal) | Pengebatan agresif (50% → 0% antara 80°C dan 90°C) |
| **90°C** (Penutupan) | Hentikan pemprosesan GPU sepenuhnya — perlu disejukkan |

{% hint style="warning" %}
**Pastikan pengudaraan dan penyejukan yang mencukupi** untuk pemprosesan berterusan, terutamanya dalam petak lapangan tertutup atau sistem terbang. Penghadan terma akan mengurangkan throughput pemprosesan untuk melindungi perkakasan.
{% endhint %}

***

## Pengurusan Memori

Peranti Jetson menggunakan **memori bersatu** — GPU dan CPU berkongsi RAM fizikal yang sama. VRAM yang dilaporkan (contohnya ~15.3GB pada Orin NX 16GB) bukan memori GPU khusus; ia adalah RAM yang sama yang digunakan oleh sistem pengendalian dan setiap proses lain.

### Amaran Swap dan Cadangan

Sebelum memproses pada Jetson, CLI mengira imej RAW dalam folder input anda (`.tif`, `.tiff`, `.raw`, `.dng` — pratonton JPG tidak dikira), menganggarkan memori puncak yang diperlukan oleh larian, dan **memberi amaran sebelum memulakan** jika RAM + swap mungkin tidak mencukupi. Amaran itu bertajuk `LOW MEMORY WARNING - Jetson Detected`, mencetak bilangan imej anda, RAM, swap semasa, dan anggaran puncak, kemudian memberikan `fallocate` / perintah `chmod` / `mkswap` / `swapon` bersaiz untuk projek anda (tidak pernah lebih kecil daripada 8GB). Ia berehat beberapa saat supaya mesej tidak terlepas dalam tatal balik, kemudian pemprosesan diteruskan.**Anggaran memori yang digunakan oleh amaran:**

| Mod Debayer | Asas | Setiap imej |
| --- | --- | --- |
| Standard | ~1.5 GB | ~10 MB |
| Sedar Teksur | ~2.5 GB (model + masa larian Python) | ~15 MB |

Amaran ini akan muncul apabila anggaran puncak melebihi RAM + swap tolak margin keselamatan 1GB, dan ia hanya mengira swap **berdasarkan fail** — tetapan yang hanya menggunakan zram masih akan dikesan.

Untuk menambah swap secara manual (contoh: 8GB):

```bash
# Check current memory and swap
free -h

# Create a swap file
sudo fallocate -l 8G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# Make persistent across reboots
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```



<!-- SCREENSHOT-NEEDED: Terminal on a Jetson Orin (SSH session) showing the full "LOW MEMORY WARNING - Jetson Detected" block printed by `chloros-cli process` on a large folder: the image count and debayer mode line, RAM / current swap / estimated peak figures, and the fallocate/chmod/mkswap/swapon command block it recommends -->

### Pengendalian OOM (Habis Memori)

Semasa pemprosesan,Chloros

memantau memori GPU dan merosot dengan lancar bukannya terhenti:

1. Apabila penggunaan memori GPU melebihi **85%**, saiz kumpulan dikurangkan secara pencegahan
2. Jika peristiwa kehabisan memori masih berlaku, saiz kumpulan **dibahagikan dua**, dan dibahagikan dua lagi pada setiap OOM berturut-turut; setiap kumpulan berjaya seterusnya akan mengurangkan penalti itu satu langkah
3. Di bawah tekanan berterusan, saluran paip akan kembali dari `fused_gpu` kepada laluan `tiled_gpu` yang cekap memori, dan kepada pemprosesan CPU sebagai pilihan terakhir

***

## Penyebaran di Lapangan

### Pertimbangan Kuasa

| Model Jetson     | Penggunaan Kuasa Tipikal | Nota                   |
| ---------------- | ------------------ | ----------------------- |
| Jetson Orin Nano | 7-15W              | Palam barel DC          |
| Jetson Orin NX   | 10-25W             | Palam barel DC          |
| Jetson AGX Orin  | 15-60W             | USB-C PD atau palam barel |

Rancang bajet kuasa anda untuk pemprosesan berterusan — penggunaan kuasa puncak berlaku semasa Thread 3 (Pemprosesan) yang intensif GPU.

### Cadangan Penyimpanan

* **SSD NVMe** sangat dicadangkan untuk penyebaran arm64
* Kad SD terlalu perlahan untuk pemprosesan — gunakan hanya sebagai media but
* Rancang ruang simpanan 2-3 kali ganda saiz data imej mentah anda untuk output yang diproses

### Operasi Tanpa Skrin melaluiSSH



Chloros

CLI

adalah ideal untuk penyebaran Jetson tanpa skrin:

```bash
# SSH into the Jetson
ssh user@jetson-hostname

# Process a dataset
chloros-cli process /data/datasets/flight001 --format "TIFF (32-bit, Percent)"

# Monitor export progress
chloros-cli export-status
```

### Backend Sentiasa-Aktif untuk Penyelarasan Masa LATTICE / DAQ-E

Jika Jetson anda mengawal kamera LATTICE atau penderia cahaya DAQ-E secara tanpa kepala, aktifkan perkhidmatan sistemd backend supaya PTP grandmaster berjalan secara berterusan (unit ini dipasang tetapi tidak diaktifkan secara lalai):

```bash
sudo systemctl enable --now chloros-backend.service
chloros-cli time-sync status
```

Lihat [PemasanganLinux

](linux-installation.md#always-on-ptp-for-headless-hosts) untuk butiran, termasuk bagaimana pakej ini menjadikan port PTP 319/320 boleh diikat tanpa kebenaran root.

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

`chloros-cli process` keluar dengan nilai bukan sifar apabila satu larian yang meminta produk tidak menulis sebarang imej, jadi status kegagalan systemd bermakna untuk pemantauan.

Pasangkan dengan pemasa systemd untuk pemprosesan terjadual:

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

## Aliran Kerja Contoh

### Pemprosesan Jetson Asas

```bash
#!/bin/bash
# Process a drone flight dataset on Jetson
chloros-cli process /data/flights/flight_042 \
    --output /data/processed/flight_042 \
    --format "TIFF (32-bit, Percent)" \
    --indices NDVI NDRE GNDVI
```

###PythonSDK

pada Jetson

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

### Pemprosesan Batch Pelbagai Penerbangan

```bash
#!/bin/bash
# Process all flight datasets in a directory
for flight in /data/flights/*/; do
    name=$(basename "$flight")
    echo "Processing $name..."
    chloros-cli process "$flight" \
        --output "/data/processed/$name" \
        --format "TIFF (32-bit, Percent)" \
        --indices NDVI NDRE
    echo "Completed $name"
done
```

***

## Sistem Jetson yang Disyorkan untuk Penggunaan di Lapangan

Untuk penyebaran di lapangan dan udara, pertimbangkan pilihan papan pembawa Jetson Orin NX 16GB ini:

* **Udara/drone**: Sistem dengan penarafan getaran (MIL-STD), ringan (di bawah 300g), penyejukan pasif
* **Medan lasak**: Sarung kalis air IP67/IP69K dengan sambungan kamera PoE GigE
* **Minimum/belanjawan**: Kit pembangun dengan sarung tambahan

Hubungi [SokonganMAPIR

](https://www.mapir.camera/community/contact) untuk cadangan perkakasan khusus bagi senario penyebaran anda.

***

## Langkah Seterusnya

* [PemasanganLinux

](linux-installation.md) — Butiran pemasanganLinux

umum
* [Penyesuaian Komputasi Dinamik](../processing-architecture/dynamic-compute-adaptation.md) — Rujukan strategi komputasi penuh
* [Saluran Pemprosesan](../processing-architecture/processing-pipeline.md) — Memahami saluran 4-benang
* [CLI

: Baris Perintah](../CLI.md) — PanduanCLI


* [API

:Python

SDK

](../api-python-sdk.md) — PanduanSDK


* [RujukanCLI

](../reference/cli-reference.md) dan [RujukanSDK

](../reference/sdk-reference.md) — Senarai lengkap arahan/API

untuk 1.2.0
