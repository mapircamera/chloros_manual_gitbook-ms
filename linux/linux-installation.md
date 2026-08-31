# Pemasangan Linux

Chloros diedarkan untuk Linux sebagai pakej `.deb` yang memasang CLI dan pelayan backend. Python SDK adalah pakej pip berasingan (juga disertakan dalam `.deb` sebagai wheel yang sepadan versinya).

Nama fail pakej memaparkan versi dan seni bina: `chloros_1.2.0_amd64.deb` untuk x86_64, dan `chloros_1.2.0_arm64_jp6.deb` untuk binaan JetPack 6 Jetson. Gantikan fail yang sebenarnya anda muat turun dalam arahan di bawah.

***

## Linux amd64 (x86_64)

### Keperluan Sistem

| Keperluan | Minimum | Disyorkan |
| --- | --- | --- |
| **Pengedaran** | Ubuntu 22.04 LTS+ / Debian 12+ | Ubuntu 24.04 LTS |
| **Pemproses** | x86_64 (Intel/AMD) | Intel Core i7 atau lebih baik |
| **Memori (RAM)** | 8GB | 16GB atau lebih |
| **Kad grafik** | Tiada (pemprosesan CPU) | GPU NVIDIA dengan VRAM 4GB+ (12GB+ membuka kunci `GPU_PARALLEL`, 7GB+ memastikan Texture Aware dimatikan daripada laluan imej tunggal) |
| **Simpanan** | Ruang kosong 2GB | SSD dengan ruang kosong 10GB+ |
| **Python** | Python 3.7+ (untuk SDK) | Python 3.10+ |

> **Ubuntu 20.04 dan Debian 11 tidak disokong.** Senarai kebergantungan `.deb` adalah
> diambil daripada apa yang sebenarnya dipautkan oleh backend Chloros, dan itu termasuk
> `libc6 (>= 2.34)`. Focal dan bullseye kedua-duanya disertakan dengan glibc 2.31, jadi `apt` menolak pemasangan terus daripada membiarkannya gagal kemudian semasa runtime.

### Pemasangan

```bash
sudo dpkg -i chloros_1.2.0_amd64.deb
sudo apt-get install -f    # pulls the declared dependencies (libibverbs1, libcap2-bin)
```

{% hint style="info" %}
`dpkg -i` tidak menyelesaikan kebergantungan. Jika ia melaporkan pakej yang hilang, `sudo apt-get install -f` (atau `sudo apt --fix-broken install`) akan menyempurnakan pemasangan — ini adalah laluan biasa, bukan ralat.
{% endhint %}

Semak pemasangan:

```bash
chloros-cli --version    # prints "Chloros CLI 1.2.0"
```



<!-- SCREENSHOT-NEEDED: Terminal on Ubuntu 22.04 immediately after `sudo dpkg -i chloros_1.2.0_amd64.deb`, showing the full postinst output: the "Chloros installed successfully!" banner, the Usage lines, the "Python SDK:" block naming the bundled wheel path under /usr/lib/chloros/sdk/, any "GPU Acceleration:" detection line, and the closing "Systemd Service (optional): sudo systemctl enable --now chloros-backend.service" hint -->***

## Linux arm64 (NVIDIA Jetson)

### Keperluan Sistem

| Keperluan | Minimum | Disyorkan |
| --- | --- | --- |
| **Platform** | NVIDIA Jetson dengan JetPack 6 | Jetson Orin NX 16GB atau AGX Orin |
| **JetPack** | JetPack 6.x | JetPack 6 terkini |
| **Memori (RAM)** | 8GB (dikongsi GPU/CPU) | 16GB+ dikongsi (12GB+ adalah ambang untuk pekerja GPU selari) |
| **Simpanan** | Ruang kosong 2GB | SSD NVMe dengan ruang kosong 10GB+ |
| **Python** | Python 3.7+ (untuk SDK) | Python 3.10+ |

### Pemasangan

```bash
sudo dpkg -i chloros_1.2.0_arm64_jp6.deb
sudo apt-get install -f
chloros-cli --version
```

Susun atur yang sama seperti `.deb` amd64, dengan binaan CUDA yang disesuaikan untuk Jetson Orin / Orin NX / Orin Nano. Untuk maklumat mengenai memori Jetson, termal, dan kelakuan penyebaran lapangan, lihat [Panduan NVIDIA Jetson](nvidia-jetson-guide.md).

***

## Pemasangan Python SDK (Semua Linux)

SDK adalah klien Python HTTP tulen untuk backend, jadi pakej yang sama berfungsi pada amd64 dan arm64. Dua sumber:**Dari PyPI** — keluaran stabil yang diterbitkan:

```bash
pip install chloros-sdk
```

**Daripada wheel yang disertakan** — dijamin sepadan dengan backend CLI yang baru anda pasang (gunakan ini apabila `.deb` anda lebih baru daripada PyPI):

```bash
pip install --user /usr/lib/chloros/sdk/chloros_sdk-*.whl
```

{% hint style="warning" %}
**Pendistribusian PEP 668** (Ubuntu 23.10+, Debian 12+) menolak pemasangan pip di seluruh sistem. Gunakan `pip install --user …`, persekitaran maya, atau `sudo pip install --break-system-packages …`. Pemuat pemasang pakej tidak pernah memasang secara automatik SDK ke dalam sistem anda Python — pilihan itu diserahkan kepada anda.
{% endhint %}

Tambahan pilihan:

| Ekstra | Perintah | Menambah |
| --- | --- | --- |
| `progress` | `pip install chloros-sdk[progress]` | `sseclient-py` untuk penstriman kemajuan secara langsung |
| `camera` | `pip install chloros-sdk[camera]` | `bleak` untuk pengangkutan BLE (DAQ-M) |

Verifikasi SDK:

```bash
python -c "import chloros_sdk; print(chloros_sdk.__version__)"
```

{% hint style="info" %}
`.deb` memasang Chloros CLI dan backend. Python SDK berkomunikasi dengan backend tersebut melalui local HTTP API (`http://127.0.0.1:5000`) dan memulakannya secara automatik apabila diperlukan. Sentiasa gunakan alamat IPv4 literal dan bukannya `localhost` — `localhost` boleh diselesaikan ke `::1` dan mengambil masa kira-kira dua saat setiap permintaan.
{% endhint %}

***

## Penyediaan Kali Pertama

### 1. Log Masuk

Akses ke CLI dan SDK memerlukan pelan berbayar Chloros+ (**Tembaga** atau lebih tinggi), yang dikuatkuasakan di pihak pelayan: pemanggil yang log keluar akan mendapat `401 AUTH_REQUIRED`, dan pemanggil pelan percuma (Besi) akan mendapat `403 PLAN_UPGRADE_REQUIRED`.

```bash
chloros-cli login your@email.com 'your-password'
```

Butiran pengesahan disimpan dalam cache di `~/.chloros/user_session.json`.

{% hint style="warning" %}
**Anda mesti log masuk semula selepas setiap pemasangan atau peningkatan.** Skrip `prerm` pakej ini sengaja membersihkan `~/.chloros/user_session.json` dan lesen yang disimpan untuk setiap pengguna pada mesin, jadi binaan baru sentiasa mengesahkan semula lesen dan bukannya mempercayai cache yang sudah lapuk.
{% endhint %}

### 2. Semak Status Lesen Anda

```bash
chloros-cli status
```

`chloros-cli status` berfungsi pada mana-mana peringkat (termasuk percuma), jadi anda sentiasa boleh melihat mengapa akses tersedia atau tidak tersedia.

### 3. Jalankan Diagnostik Sistem

```bash
chloros-cli selftest
```

Tujuh pemeriksaan dijalankan secara berurutan, dan arahan akan keluar dengan nilai bukan sifar jika mana-mana daripadanya gagal:

| # | Pemeriksaan | Apa yang dibuktikan |
| --- | --- | --- |
| 1 | **Versi** | CLI melaporkan versinya (`v1.2.0`). |
| 2 | **Ports tersedia** | Port 5000 kosong, *atau* sudah dijawab oleh backend Chloros yang sihat (yang dikira sebagai lulus). |
| 3 | **Permulaan backend** | Binari backend dilancarkan. |
| 4 | **Ujian API (`/api/test`)** | Backend menjawab `status: ok`. |
| 5 | **Maklumat sistem** | Mencetak `GPU: <name>, CUDA: <bool>, PyTorch: <version>` daripada `/api/system-info`. |
| 6 | **Model Denoiser** | Menjumpai model `*.pth.enc` (di Linux: `/usr/lib/chloros/models`). |
| 7 | **CUDA + Denoiser**| Texture Aware sebenarnya boleh digunakan — memerlukan CUDA**dan** sekurang-kurangnya satu fail model. |

Proses ini berakhir dengan `N/7 checks passed`, menyenaraikan sebarang kegagalan mengikut nama.

### 4. Proses Set Data Pertama Anda

```bash
chloros-cli process ~/datasets/flight001
```

***

## Fail dan Direktori


Chlorosbagi setiap pengguna menyimpan kelayakan dan konfigurasi CLI dalam satu direktori rentas platform, **`~/.chloros/`** (pada Windows, `%USERPROFILE%\.chloros\`). Dua cache khusus Linux mengikuti konvensyen XDG sebaliknya — ini mematuhi `XDG_CONFIG_HOME` / `XDG_CACHE_HOME` apabila ditetapkan.

| Laluan | Tujuan |
| --- | --- |
| `~/.chloros/user_session.json` | Cache sesi log masuk yang ditulis oleh `chloros-cli login` (dibersihkan pada setiap pemasangan/peningkatan pakej) |
| `~/.chloros/working_directory.txt` | Pengganti folder projek lalai (`chloros-cli set-project-folder` / `get-project-folder` / `reset-project-folder`) |
| `~/.chloros/cli_language.json` | Keutamaan bahasa CLI (`chloros-cli language <code>`) |
| `~/.chloros/user.json` | Tetapan bahasa dikongsi dengan GUI Windows — satu `language` di sini diutamakan berbanding `cli_language.json` |
| `~/.chloros/update_cache.json` | Penyimpanan semula satu jam untuk semakan kemas kini permulaan Linux/Jetson |
| `~/.chloros/backend.log` | Log backend apabila backend dilancarkan oleh CLI |
| `~/.chloros/camera_cal/<serial>/<bundle_sha>/` | Pakej penentukuran LATTICE tersimpan bagi setiap kamera, diuruskan mengikut hash siri dan bundel |
| `~/.chloros/daq_cap_profiles/<u\|m\|e>/<cap_id>.json` | Ganti nilai pengguna pilihan untuk profil pembetulan topi DAQ |
| `~/.config/chloros/system_config.json` | Profil perkakasan yang disimpan dari Adaptasi Komputasi Dinamik — padamkan untuk memaksa pengesanan perkakasan baharu |
| `~/.cache/chloros/logs/backend_<YYYYMMDD_HHMMSS>.log` | Log pelayan belakang, satu fail bagi setiap pelancaran |
| `~/Chloros Projects/` | Folder projek lalai apabila tiada pengganti ditetapkan |

### Seluruh Sistem

| Laluan | Tujuan |
| --- | --- |
| `/usr/bin/chloros-cli` | Skrip pembalut — menetapkan `LD_LIBRARY_PATH` untuk pustaka asli terbundel, kemudian menjalankan binari sebenar |
| `/usr/bin/chloros-backend` | Skrip pembalut — sama, ditambah `CHLOROS_PRODUCTION=1` supaya pintuauth backend tidak dapat menyahdayakan dirinya secara senyap |
| `/usr/lib/chloros/chloros-cli`, `/usr/lib/chloros/chloros-backend` | Biner yang disusun |
| `/usr/lib/chloros/arena_runtime/` | Persekitaran runtime Arena SDK yang diperlukan oleh kamera LATTICE |
| `/usr/lib/chloros/models/*.pth.enc` | Model penapis hingar yang disulitkan digunakan oleh debayer yang peka tekstur |
| `/usr/lib/chloros/sdk/chloros_sdk-*.whl` | Python SDK roda yang sepadan dengan binaan tepat ini |
| `/usr/lib/chloros/exiftool` | exiftool terbungkus (dihubungkan secara simbolik ke `/usr/local/bin/exiftool` hanya jika tiada exiftool sistem wujud) |
| `/etc/chloros/update.conf` | Konfigurasi saluran kemas kini dibaca oleh `chloros-cli update` |
| `/etc/sysctl.d/60-chloros-ptp.conf` | Menetapkan `net.ipv4.ip_unprivileged_port_start = 319` supaya backend boleh mengikat port PTP tanpa hak root |
| `/etc/ld.so.conf.d/Arena_SDK.conf` | Menunjuk pemuat dinamik ke `/usr/lib/chloros/arena_runtime` |
| `/lib/udev/rules.d/70-chloros-daq.rules` | Memberi pengguna yang log masuk akses ke jambatan bersiri USB DAQ-U (CP2102N, `10c4:ea60`) |
| `/lib/systemd/system/chloros-backend.service` | Perkhidmatan backend sentiasa aktif (dipasang, **tidak diaktifkan**) |
| `/usr/share/applications/chloros-cli.desktop` | entri menu aplikasi &quot;Chloros CLI&quot; yang membuka terminal |

## Lokasi Eksekutabel Backend

CLI dan SDK mengesan backend secara automatik:

| Komponen | Laluan |
| --- | --- |
| CLI | `/usr/bin/chloros-cli` |
| Backend | `/usr/lib/chloros/chloros-backend` |

Tetapkan semula laluan backend dengan penanda aras `--backend-exe` CLI atau parameter pembina `backend_exe` SDK, dan port dengan `--port` (default `5000`).

{% hint style="info" %}
`CHLOROS_BACKEND_URL` menunjuk kepada **`lattice`**,**`project`**, dan keluarga arahan**`daq pool-*`** di backend jauh. Arahan teras (`process`, `login`, `logout`, `status`, `export-status`, `time-sync`, `selftest`) sengaja mengabaikannya dan sentiasa mensasarkan `http://127.0.0.1:<port>`.
{% endhint %}

***

## Kamera LATTICE dan Penderia Cahaya DAQ pada Linux

Keluarga arahan live-hardware semuanya berfungsi pada Linux (amd64 dan Jetson):

* **`chloros-cli lattice`** — menemui, menyambung, mengkonfigurasi, dan menangkap daripada kamera LATTICE dan tatasusunan bersepadu masa. `.deb` membungkus runtime Arena SDK yang diperlukan dan mendaftarkannya dengan pemuat dinamik.
* **`chloros-cli daq pool-*`** — menyambungkan penderia cahaya DAQ-U/M/E melalui kolam backend, mengalirkan spektra yang telah dikalibrasi, dan merekodkan fail `.daq`. CLI yang disusun hanya menyertakan keluarga `pool-*`: `pool-connect`, `pool-disconnect`, `pool-list`, `pool-latest`, `pool-stream`, `pool-record`, `pool-set-cap`.
* **`chloros-cli project`** — jalankan projek yang disimpan (kamera, sensor, dan tetapan pemprosesannya) tanpa antaramuka pengguna.
* **`chloros-cli time-sync`** — memeriksa grandmaster PTP yang dijalankan oleh backend Chloros untuk kamera LATTICE dan sensor DAQ-E.

```bash
# DAQ-E at a known address — the reliable path on multi-homed hosts
chloros-cli daq pool-connect --eth-host 192.168.2.50

# DAQ-U over USB serial
chloros-cli daq pool-connect --port /dev/ttyUSB0

# What is connected, then the latest calibrated spectrum as JSON
chloros-cli daq pool-list
chloros-cli daq pool-latest --sensor-id daq-e-a1b2c3 --json
```

`--sensor-id` diperlukan oleh `pool-latest`, `pool-stream`, `pool-record`, dan `pool-set-cap`; `pool-list` memaparkan ID yang kini terdapat dalam kolam.

{% hint style="info" %}
**Utamakan `--eth-host` untuk sambungan DAQ-E pertama pada mesin berbilang sambungan.** Penemuan automatik menelusuri mDNS dan mungkin terlepas antara muka sensor daripada cache ARP yang kosong, jadi `pool-connect --eth` pertama selepas but mungkin gagal walaupun sensor dalam keadaan sempurna sihat. Memberi IP atau nama hos sensor akan melangkau penemuan sepenuhnya.
{% endhint %}

**Kebenaran siri DAQ-U** diuruskan oleh peraturan udev yang dipasang (`uaccess` + kumpulan `dialout`). Jika sesuatu sensor yang sudah disambungkan masih tidak dapat diakses, muatkan semula peraturan atau pasangkannya semula:

```bash
sudo udevadm control --reload-rules
sudo udevadm trigger --subsystem-match=tty
```

Rujuk [rujukan CLI](../CLI.md) untuk set arahan penuh.

### PTP Sentiasa-Hidup untuk Hos Tanpa Skrin

Pada pemasangan pertama, unit systemd `chloros-backend.service` dijana tetapi **tidak diaktifkan**. Pada Jetson tanpa papan kekal atau pelayan yang perlu mengekalkan penyelarasan masa PTP berjalan secara berterusan untuk sensor DAQ-E dan kamera LATTICE, aktifkannya:

```bash
sudo systemctl enable --now chloros-backend.service
sudo systemctl status chloros-backend.service
```

Tanpa ia, PTP hanya berjalan selagi backend Chloros sedang berjalan — iaitu, semasa sesi CLI / SDK aktif.

Unit ini mengikat backend kepada `127.0.0.1:5000` (tetapan persekitaran `CHLOROS_HOST` / `CHLOROS_PORT` di dalam unit; tindas dengan `sudo systemctl edit chloros-backend.service`) dan memulakannya semula jika gagal selepas 5 saat.

**Bagaimana PTP mendapatkan portnya.** PTP menggunakan UDP 319/320, kedua-duanya di bawah ambang port berkeistimewaan biasa 1024. Pakej `postinst` menulis `/etc/sysctl.d/60-chloros-ptp.conf` dengan `net.ipv4.ip_unprivileged_port_start = 319`, yang membolehkan backend mengikatnya semasa dijalankan sebagai pengguna anda. Ia juga menerapkan `setcap cap_net_bind_service,cap_net_raw=+ep` pada binari backend sebagai langkah berjaga-jaga — itulah sebabnya `libcap2-bin` adalah kebergantungan yang diisytiharkan oleh pakej ini.***

## Contoh Skrip Bash

{% hint style="info" %}
Kod keluar mesra skrip. `chloros-cli process` keluar `0` apabila berjaya dan **bukan sifar apabila gagal — termasuk larian yang meminta produk imej tetapi tidak menulis sebarang produk** (ia mencetak `Processing finished but wrote no image products.` dan nama folder projek serta punca biasa). Jalankan yang berjaya melaporkan berapa banyak produk imej telah ditulis (`Image products written: N`). Kod keluar: `0` berjaya, `1` gagal, `2` ralat hujah, `130` terganggu.
{% endhint %}

### Proses Pelbagai Set Data

```bash
#!/bin/bash
for dataset in ~/datasets/2026/*/; do
    echo "Processing $(basename "$dataset")..."
    if chloros-cli process "$dataset" --format "TIFF (32-bit, Percent)"; then
        echo "Done: $(basename "$dataset")"
    else
        echo "FAILED: $(basename "$dataset")" >&2
    fi
done
```

### Proses dengan Tetapan Tersuai

```bash
#!/bin/bash
chloros-cli process ~/datasets/field_a \
    --output ~/output/field_a \
    --format "TIFF (32-bit, Percent)" \
    --indices NDVI NDRE GNDVI \
    --debayer texture-aware \
    --no-vignette
```

Nilai `--format` yang sah adalah tepat empat, dan ia mengandungi ruang — sentiasa petik ia:

| Nilai `--format` | Folder keluaran |
| --- | --- |
| `TIFF (16-bit)` *(laluan lalai)* | `tiff16` |
| `TIFF (32-bit, Percent)` | `tiff32` |
| `PNG (8-bit)` | `png8` |
| `JPG (8-bit)` | `jpg8` |

`--debayer` menerima `standard` (lalai) atau `texture-aware` (Chloros+).

### Pemprosesan Automatik dengan Cron

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

## Penyelesaian Masalah

### CLI Tidak Ditemui Selepas Pemasangan

```bash
# Check if the binary exists
which chloros-cli
ls -la /usr/bin/chloros-cli

# List everything the package installed
dpkg -L chloros

# Reload your shell
source ~/.bashrc
```

### Kebenaran Ditolak

```bash
sudo chmod +x /usr/bin/chloros-cli
sudo chmod +x /usr/lib/chloros/chloros-backend
```

### &quot;setcap gagal&quot; Semasa Pemasangan

`.deb` menerapkan `cap_net_bind_service` kepada `/usr/lib/chloros/chloros-backend` supaya ia boleh mengikat port PTP 319/320 tanpa hak root. Jika `libcap2-bin` hilang semasa pemasangan, panggilan itu akan diabaikan. Pasang dan pasang semula pakej tersebut:

```bash
sudo apt install libcap2-bin
sudo apt reinstall chloros
```

### PTP Tidak Akan Berfungsi / Tidak Dapat Mengikat Port 319

Pastikan had port tanpa keistimewaan telah diturunkan, dan terapkan semula untuk but semasa jika ia tidak:

```bash
sysctl net.ipv4.ip_unprivileged_port_start     # expect 319
sudo sysctl -w net.ipv4.ip_unprivileged_port_start=319
```

Kemudian semak grandmaster:

```bash
chloros-cli time-sync status
chloros-cli time-sync peers
```

### &quot;Pemandu kamera LATTICE tidak ditemui&quot;

Persekitaran masa larian Arena SDK tidak diselesaikan. Sahkan konfigurasi pemuat yang ditulis oleh pakej itu wujud dan dikemas kini:

```bash
cat /etc/ld.so.conf.d/Arena_SDK.conf     # expect /usr/lib/chloros/arena_runtime
sudo ldconfig
ls /usr/lib/chloros/arena_runtime | head
```

### Backend Gagal Dimulakan

```bash
# Check if port 5000 is already in use
lsof -i :5000

# Kill any existing process on port 5000
kill $(lsof -t -i :5000)

# Try starting with a different port
chloros-cli --port 5001 process ~/datasets/flight001
```

Log backend untuk pelancaran yang gagal terdapat dalam `~/.cache/chloros/logs/`.

### CUDA Tidak Dikesan

```bash
# Check NVIDIA driver installation
nvidia-smi

# Check CUDA availability
nvcc --version

# On Jetson, check JetPack version
cat /etc/nv_tegra_release
```

`chloros-cli selftest` melaporkan perkara yang sama dalam satu baris: `GPU: <name>, CUDA: <bool>, PyTorch: <version>`.

### Pustaka Berkongsi Hilang

```bash
sudo apt-get update
sudo apt-get install -f

# Check for missing libraries
ldd /usr/lib/chloros/chloros-backend | grep "not found"
```

### Permulaan Perlahan pada Sistem Kad SD

Biner yang disusun mengekstrak dirinya ke dalam direktori sementara pada setiap pelancaran. Jika `/mnt/ssd/tmp` wujud, Chloros menggunakannya secara automatik; jika tidak, tetapkan `TMPDIR` ke sistem fail yang pantas:

```bash
export TMPDIR=/mnt/nvme/tmp
```

***

## Mengemas kini Chloros pada Linux

Perintah `update` hanya untuk Linux/Jetson. Ia memeriksa versi yang diterbitkan dalam saluran kemas kini yang dikonfigurasikan di `/etc/chloros/update.conf` dan menawarkan untuk memuat turun dan memasang `.deb` yang sepadan:

```bash
# Check for updates without installing
chloros-cli update --check

# Check for and install updates
chloros-cli update
```

Pada Linux/Jetson, CLI juga menjalankan semakan kemas kini tanpa halangan pada setiap permulaan (hasil disimpan dalam cache selama satu jam di `~/.chloros/update_cache.json`) dan mencetak `Update available: vX.Y.Z` apabila terdapat versi yang lebih baru. Tetapan dan projek anda akan kekal selepas kemas kini; anda perlu log masuk semula selepas itu.

## Nyahpasang

```bash
sudo apt remove chloros
```

Penyingkiran menghentikan `chloros-backend.service`, memulihkan lantai port tanpa keistimewaan lalai (1024), membuang symlink exiftool terbundel dan konfigurasi pemuat Arena, serta memadam maklumat log masuk yang disimpan dalam cache. Projek dan fail data `~/.chloros/` anda akan dibiarkan seperti sediakala.

***

## Langkah Seterusnya

* [Panduan NVIDIA Jetson](nvidia-jetson-guide.md) — Pengoptimuman dan penyebaran khusus Jetson
* [CLI: Baris Perintah](../CLI.md) — panduan CLI
* [API: Python SDK](../api-python-sdk.md) — panduan SDK
* [Rujukan CLI](../reference/cli-reference.md) dan [Rujukan SDK](../reference/sdk-reference.md) — senarai lengkap arahan/API untuk 1.2.0
* [Penyesuaian Komputasi Dinamik](../processing-architecture/dynamic-compute-adaptation.md) — bagaimana Chloros menyesuaikan diri dengan perkakasan anda
