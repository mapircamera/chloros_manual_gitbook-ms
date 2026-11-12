# Pemasangan Linux

Chloros diedarkan untuk Linux sebagai pakej `.deb` yang memasang CLI, pelayan belakang dan Python SDK (untuk sistem `python3`, ditambah wheel yang sepadan versinya untuk virtualenvs).

Nama fail pakej memaparkan versi dan seni bina: `chloros_1.3.0_amd64.deb` untuk x86_64, dan `chloros_1.3.0_arm64_jp6.deb` untuk arm64 (NVIDIA Jetson, JetPack 6). Gantikan fail yang sebenarnya anda muat turun dalam arahan di bawah.

{% hint style="info" %}
**Baharu dalam 1.3.0: program-program dihantar tanpa dibongkar.** Backend dan CLI dipasang sebagai folder, `/usr/lib/chloros/backend/` dan `/usr/lib/chloros/cli/`, daripada fail self-extracting tunggal. Mereka tidak lagi membuka pemampatan ke dalam folder sementara setiap kali pelancaran, jadi mereka memulakan dengan lebih pantas (pada x86_64 backend dahulu mengambil kira-kira 100 saat untuk membuka pemampatan pada setiap permulaan) dan tidak lagi memerlukan beberapa GB ruang kosong dalam `/tmp`. `.deb` amd64 juga kira-kira separuh saiznya sebelum ini.
{% endhint %}

***

## Linux amd64 (x86_64)

### Keperluan Sistem

| Keperluan | Minimum | Disyorkan |
| --- | --- | --- |
| **Pengedaran** | Ubuntu 22.04 LTS+ / Debian 12+ | Ubuntu 24.04 LTS |
| **Pemproses** | x86_64 (Intel/AMD) | Intel Core i7 atau lebih baik |
| **Memori (RAM)** | 8GB | 16GB atau lebih |
| **Kad grafik** | Tiada (pemprosesan CPU) | NVIDIA GPU dengan VRAM 4GB+ (12GB+ membuka kunci `GPU_PARALLEL`, 7GB+ mengekalkan Texture Aware dimatikan pada laluan imej tunggal) |
| **Penyimpanan** | Ruang kosong 2GB | SSD dengan ruang kosong 10GB+ |
| **Python** | Python 3.7+ (untuk SDK) | Python 3.10+ |

> **Ubuntu 20.04 dan Debian 11 tidak disokong.** Senarai kebergantungan `.deb` adalah
> diambil daripada apa yang sebenarnya dipautkan oleh backend Chloros, dan ia termasuk a
> `libc6` yang lebih baru daripada glibc 2.31 yang dibekalkan oleh focal dan bullseye, jadi `apt` menolak pemasangan terus daripada membiarkannya gagal kemudian semasa runtime.

### Pemasangan

```bash
sudo apt install ./chloros_1.3.0_amd64.deb
```

{% hint style="info" %}
**Gunakan `apt install ./…`, bukan `dpkg -i`.** `apt` menyelesaikan kebergantungan pakej dan juga memasang apa yang disyorkan: keperluan Python SDK, `python3-numpy` dan `python3-requests`. `dpkg -i` tidak memasang kedua-duanya. CLI dan backend adalah berdiri sendiri dan berjalan tanpa mereka, tetapi `import chloros_sdk` memerlukannya; keluaran pemasangan akan menamakan mana-mana yang hilang. Simpan `./`, supaya `apt` memasang fail tersebut daripada mencari pakej dengan nama itu. Pakej itu juga mengisytiharkan `libgl1`, yang dimuatkan oleh OpenCV terbungkus dan mungkin tiada pada imej pelayan minimum.
{% endhint %}

Semak pemasangan:

```bash
chloros-cli --version    # prints "Chloros CLI 1.3.0"
```

<!-- SCREENSHOT-NEEDED: Terminal on Ubuntu 22.04 immediately after `sudo apt install ./chloros_1.3.0_amd64.deb`, showing the full postinst output: the "Chloros installed successfully!" banner, the Usage lines, the "Python SDK:" block ("chloros_sdk 1.3.0 for python3" and the "In a virtualenv: pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl" line), any "GPU Acceleration:" detection line, and the closing "Systemd Service (optional): sudo systemctl enable --now chloros-backend@<user>.service" hint -->

***

## Linux arm64 (NVIDIA Jetson)

### Keperluan Sistem

| Keperluan | Minimum | Disyorkan |
| --- | --- | --- |
| **Platform** | NVIDIA Jetson dengan JetPack 6 | Jetson Orin NX 16GB atau AGX Orin |
| **JetPack** | JetPack 6.x | JetPack 6 terkini |
| **Memori (RAM)** | 8GB (dikongsi GPU/CPU) | 16GB+ dikongsi (12GB+ adalah ambang untuk pemprosesan selari) |
| **Simpanan** | Ruang kosong 2GB | SSD NVMe dengan ruang kosong 10GB+ |
| **Python** | Python 3.7+ (untuk SDK) | Python 3.10+ |

### Pemasangan

```bash
sudo apt install ./chloros_1.3.0_arm64_jp6.deb
chloros-cli --version
```

Susun atur yang sama seperti `.deb` amd64 (Python SDK, termasuk buffer penerimaan dan perkhidmatan backend), untuk Jetson Orin / Orin NX / Orin Nano pada **JetPack 6** (L4T R36). PyTorch-nya ialah binaan CUDA JetPack 6 untuk Orin, jadi pemprosesan GPU, termasuk debayer Texture Aware, dijalankan pada GPU Jetson. Pakej ini tidak menyertakan CUDA itu sendiri: ia menggunakan pustaka CUDA dan cuDNN yang dipasang oleh JetPack 6 (`sudo apt install nvidia-jetpack` pada imej yang tiada pustaka tersebut). Apabila tiada GPU CUDA boleh digunakan, pemprosesan dijalankan pada CPU dan `--debayer texture-aware` menggunakan debayer Standard (backend mencatat satu baris `[DEBAYER] … needs a CUDA GPU …` yang menerangkan sebabnya). Untuk maklumat mengenai memori Jetson, terma, dan kelakuan penyebaran lapangan, lihat [Panduan NVIDIA Jetson](nvidia-jetson-guide.md).

***

## Pemasangan PythonSDK (Semua Linux)**`.deb` memasang SDK untuk anda.** Kedua-dua pek (amd64 dan Jetson) memasangnya untuk sistem `python3`, jadi `import chloros_sdk` berfungsi serta-merta selepas pemasangan, tanpa pip dan tanpa rangkaian. Pakej-pakennnya (`chloros_sdk`, `lattice_sdk`, `lattice_cam`, `arena_api` dan pemuat projek) dimasukkan ke dalam `/usr/lib/python3/dist-packages`, yang dimiliki oleh pakej: peningkatan akan menggantikannya, dan menyingkirkan pakej akan menyingkirkannya.  Versi sentiasa sepadan dengan CLI dan backend yang anda pasang. 

 Keperluan SDK itu sendiri, `python3-numpy` dan `python3-requests`, adalah **disyorkan, bukan diwajibkan** oleh pakej: `sudo apt install ./chloros_<version>_<arch>.deb` memasangnya, `dpkg -i` tidak. Keluaran pemasangan melaporkan versi SDK yang diimport oleh `python3`, sebarang keperluan yang hilang, dan mana-mana `chloros_sdk` di tempat lain pada laluan `python3` yang menindih salinan pakej tersebut (seperti yang dipasang lebih awal dengan `pip install --user`).**Dalam virtualenv**, yang tidak melihat pakej sistem, pasang wheel yang disertakan ke dalamnya (secara sepenuhnya luar talian):

```bash
pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl
```

**Dari PyPI** — hanya untuk mesin yang tiada pakej Chloros dipasang, yang mana SDKnya memacu backend pada mesin lain (lihat [Mod Backend Jauh](../reference/sdk-reference.md#remote-backend-mode-pip-only-host-via-tunnel)):

```bash
pip install chloros-sdk
```

Tambahan pilihan, untuk pemasangan pip (virtualenv atau PyPI):

| Tambahan | Arahan | Menambah |
| --- | --- | --- |
| `progress` | `pip install chloros-sdk[progress]` | `sseclient-py` untuk penstriman kemajuan secara langsung |
| `camera` | `pip install chloros-sdk[camera]` | `bleak` untuk pengangkutan BLE (DAQ-M) |

Semak SDK:

```bash
python3 -c "import chloros_sdk; print(chloros_sdk.__version__)"
```

{% hint style="info" %}
PythonSDK berkomunikasi dengan backend yang dipasang oleh `.deb` melalui HTTPAPI tempatan (`http://127.0.0.1:5000`). `ChlorosLocal` dan pembantu sambungan memulakannya secara automatik apabila diperlukan; `connect_all()` bagi projek yang disimpan tidak pernah melakukannya, jadi pada mesin tanpa papan pemuka, jalankan backend sebagai [perkhidmatan](#always-on-ptp-for-headless-hosts). Sentiasa gunakan alamat IPv4 literal dan bukannya `localhost` — `localhost` boleh menafsir kepada `::1` dan mengambil masa kira-kira dua saat setiap permintaan.
{% endhint %}

***

## Penyediaan Kali Pertama

### 1. Log Masuk

Akses CLI dan SDK memerlukan tahap berbayar Chloros+ (**Tembaga** atau ke atas), yang dikuatkuasakan di pihak pelayan: pemanggil yang log keluar akan mendapat `401 AUTH_REQUIRED`, dan pemanggil tahap percuma (Besi) akan mendapat `403 PLAN_UPGRADE_REQUIRED`.

```bash
chloros-cli login your@email.com 'your-password'
```

Butiran pengesahan disimpan dalam cache di `~/.chloros/user_session.json`.

{% hint style="info" %}
**Kenaikan taraf mengekalkan log masuk anda; penyingkiran membersihkannya.** Memasang `.deb` yang lebih baru di atas pemasangan sedia ada (termasuk melalui `chloros-cli update`) mengekalkan `~/.chloros/user_session.json` dan cache lesen, jadi kerja CLI/SDK tanpa kepala terus berfungsi. Mengalih keluar pakej (`sudo apt remove chloros`) akan memadam cache log masuk dan lesen yang disimpan untuk setiap pengguna pada mesin, jadi pemasangan baharu memerlukan `chloros-cli login` semula.
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
| 1 | **Versi** | CLI melaporkan versinya (`v1.3.0`). |
| 2 | **Port tersedia** | Port 5000 kosong, *atau* telah dijawab oleh backend Chloros yang sihat (yang dikira sebagai lulus). |
| 3 | **Permulaan backend** | Binari backend dilancarkan. |
| 4 | **Ujian API (`/api/test`)** | Backend menjawab `status: ok`. |
| 5 | **Maklumat sistem** | Mencetak `GPU: <name>, CUDA: <bool>, PyTorch: <version>` daripada `/api/system-info`. |
| 6 | **Model Denoiser** | Menjumpai model `*.pth.enc` (pada Linux: `/usr/lib/chloros/models`). |
| 7 | **CUDA + Denoiser**| Texture Aware sebenarnya boleh digunakan — memerlukan CUDA**dan** sekurang-kurangnya satu fail model. |

Proses ini berakhir dengan `N/7 checks passed`, menyenaraikan sebarang kegagalan mengikut nama.

### 4. Proses Set Data Pertama Anda

```bash
chloros-cli process ~/datasets/flight001
```

***

## Fail dan Direktori

### Bagi setiap pengguna,

Chloros menyimpan kelayakan dan konfigurasi CLI dalam satu direktori rentas platform, **`~/.chloros/`** (pada Windows, `%USERPROFILE%\.chloros\`). Dua cache khusus Linuxmengikuti konvensyen XDG sebaliknya — ini mematuhi `XDG_CONFIG_HOME` / `XDG_CACHE_HOME` apabila ditetapkan.

| Laluan | Tujuan |
| --- | --- |
| `~/.chloros/user_session.json` | Cache sesi log masuk yang ditulis oleh `chloros-cli login` (dikekalkan semasa naik taraf pakej; dibersihkan apabila pakej dikeluarkan) |
| `~/.chloros/working_directory.txt` | Keutamaan folder projek lalai (`chloros-cli set-project-folder` / `get-project-folder` / `reset-project-folder`) |
| `~/.chloros/cli_language.json` | Keutamaan bahasa CLI (`chloros-cli language <code>`) |
| `~/.chloros/user.json` | Tetapan bahasa yang dikongsi dengan GUI Windows — satu `language` di sini keutamaan berbanding `cli_language.json` |
| `~/.chloros/update_cache.json` | Penyimpanan sementara selama satu jam untuk semakan kemas kini permulaan Linux/Jetson |
| `~/.chloros/backend.log` | Log backend apabila backend dilancarkan oleh CLI |
| `~/.chloros/camera_cal/<serial>/<bundle_sha>/` | Pakej penentukuran LATTICE tersimpan bagi setiap kamera, diuruskan melalui hash siri dan bundel |
| `~/.chloros/daq_cap_profiles/<u\|m\|e>/<cap_id>.json` | Ganti pengguna pilihan untuk profil pembetulan topi DAQ |
| `~/.config/chloros/system_config.json` | Profil perkakasan yang disimpan dari Dynamic Compute Adaptation — padamkan ia untuk memaksa pengesanan perkakasan semula |
| `~/.cache/chloros/logs/backend_<YYYYMMDD_HHMMSS>.log` | Log pelayan backend, satu fail bagi setiap pelancaran |
| `~/Documents/MAPIR/Chloros Projects/` | Folder projek lalai apabila tiada pengganti ditetapkan |

### Seluruh Sistem

| Laluan | Tujuan |
| --- | --- |
| `/usr/bin/chloros-cli` | Skrip pembalut — menetapkan `LD_LIBRARY_PATH` untuk pustaka asli yang disertakan (dan `TMPDIR` ke `/mnt/ssd/tmp` apabila folder itu wujud), kemudian menjalankan binari sebenar |
| `/usr/bin/chloros-backend` | Skrip pembalut — sama untuk backend, yang ditandainya sebagai binaan pengeluaran yang dipasang |
| `/usr/lib/chloros/cli/`, `/usr/lib/chloros/backend/` | CLI dan backend yang disusun, dipasang tanpa dibongkar (baru dalam 1.3.0); program-program tersebut ialah `cli/chloros-cli` dan `backend/chloros-backend` |
| `/usr/lib/chloros/chloros-cli`, `/usr/lib/chloros/chloros-backend` | Skrip pelancar dua baris yang memulakan program sebenar di atas — disimpan di laluan ini kerana SDK, CLI dan skrip lama mencarinya di sini |
| `/usr/lib/chloros/arena_runtime/` | Persekitaran runtime SDK Arena yang diperlukan oleh kamera LATTICE |
| `/usr/lib/chloros/models/*.pth.enc` | Model penapis hingar yang disulitkan digunakan oleh debayer Berpengetahuan Teksur |
| `/usr/lib/python3/dist-packages/chloros_sdk/` (dan `lattice_sdk`, `lattice_cam`, `arena_api`, pemuat projek) | SDK Python, dipasang untuk sistem `python3` dan dimiliki oleh pakej |
| `/usr/lib/chloros/sdk/chloros_sdk-*.whl` | roda SDK Python yang sepadan dengan binaan tepat ini, untuk virtualenv |
| `/usr/lib/chloros/exiftool` | Pelancar ExifTool terbungkus (dihubungkan secara simbolik ke `/usr/local/bin/exiftool` hanya jika tiada exiftool sistem wujud). Mulai versi 1.3.0, skrip ExifTool disertakan dengan perpustakaan Perl tersendiri dalam `/usr/lib/chloros/exiftool-dist/`, jadi ia berjalan tanpa pakej sistem `libimage-exiftool-perl` |
| `/etc/chloros/update.conf` | Konfigurasi kemas kini-saluran dibaca oleh `chloros-cli update` |
| `/etc/sysctl.d/60-chloros-ptp.conf` | Menetapkan `net.ipv4.ip_unprivileged_port_start = 319` supaya backend boleh mengikat port PTP tanpa root |
| `/etc/sysctl.d/60-chloros-gige.conf` | Meningkatkan buffer penerimaan soket kernel (`net.core.rmem_max`, `net.core.rmem_default`) kepada 32 MB untuk aliran kamera LATTICE, dan menerapkannya semasa pemasangan (nilai yang telah ditetapkan lebih tinggi dibiarkan seperti sedia ada). Pada tetapan lalai kernel, paket kamera dibuang dalam kernel dan susunan kehilangan bingkai dan kadar bingkai; `chloros-cli lattice network` melaporkan buffer apabila ia rendah. Lihat [Linux hosts — buffer penerimaan soket](../reference/cli-reference.md#linux-hosts--socket-receive-buffers) |
| `/etc/ld.so.conf.d/Arena_SDK.conf` | Menunjukkan pemuat dinamik ke `/usr/lib/chloros/arena_runtime` |
| `/lib/udev/rules.d/70-chloros-daq.rules` | Memberi pengguna yang log masuk akses ke jambatan bersiri USB DAQ-U (CP2102N, `10c4:ea60`) |
| `/lib/systemd/system/chloros-backend@.service` | Perkhidmatan backend sentiasa aktif pilihan yang menjalankan backend sebagai pengguna yang anda namakan (dipasang, **tidak diaktifkan**) — untuk diaktifkan, lihat [Always-On PTP](#always-on-ptp-for-headless-hosts) |
| `/lib/systemd/system/chloros-backend.service` | Perkhidmatan yang sama yang menjalankan backend sebagai **root**(dipasang,**tidak diaktifkan**), dikekalkan supaya mesin yang diaktifkannya terus berjalan |
| `/var/log/chloros/` | Log terperinci perkhidmatan root, boleh dibaca oleh kumpulan `adm` (dihapuskan apabila pakej dipurg) |
| `/usr/share/applications/chloros-cli.desktop` | entri menu aplikasi "Chloros CLI" yang membuka terminal |

**Tembok api (baru dalam 1.3.0).** Jika `ufw` atau `firewalld` aktif semasa pemasangan, pakej ini membuka port UDP `5002` (data DAQ-E), `5353` (penemuan mDNS), `319` dan `320` (PTP), dan membuang peraturan tersebut apabila pakej itu dialih keluar. Jika firewall dipasang tetapi tidak aktif, ia tidak menambah apa-apa dan mencetak port yang perlu dibenarkan sekiranya anda mengaktifkannya kemudian. Ia tidak membuka sebarang port TCP: kemas kini firmware DAQ-E, di mana sensor menyambung semula ke komputer ini melalui TCP untuk mengumpul imej, gagal dengan `… device never connected back on TCP <port> …` manakala firewall aktif menyekat sambungan tersebut.

## Lokasi Eksekutabel Backend

CLI dan SDK mengesan backend secara automatik:

| Komponen | Laluan |
| --- | --- |
| CLI | `/usr/bin/chloros-cli` |
| Backend | `/usr/lib/chloros/chloros-backend` (pelancar untuk `/usr/lib/chloros/backend/chloros-backend`) |

Gantikan laluan backend dengan penanda `--backend-exe` CLI atau parameter pembina `backend_exe` SDK, dan port dengan `--port` (lalai `5000`).

{% hint style="info" %}
`CHLOROS_BACKEND_URL` menunjuk ke **`lattice`**,**`project`**, dan keluarga arahan**`daq pool-*`** di backend jauh. Perintah teras (`process`, `login`, `logout`, `status`, `export-status`, `time-sync`, `selftest`) sengaja mengabaikannya dan sentiasa mensasarkan `http://127.0.0.1:<port>`.
{% endhint %}

***

## Kamera LATTICE dan Penderia Cahaya DAQ pada Linux

Keluarga arahan live-hardware semuanya berfungsi pada Linux (amd64 dan Jetson):

* **`chloros-cli lattice`** — menemui, menyambung, mengkonfigurasi, dan menangkap imej daripada kamera LATTICE dan tatasusunan bersepadu masa. `.deb` membungkus runtime Arena SDK yang diperlukan dan mendaftarkannya dengan pemuat dinamik.
* **`chloros-cli daq pool-*`** — menyambungkan penderia cahaya DAQ-U/M/E melalui kolam backend, menstrim spektra yang telah dikalibrasi, merekodkan fail `.daq` dan menukar profil topi, dan untuk DAQ-E menukar tetapan rangkaiannya, memulakannya semula dan mengemas kini firmware-nya. `chloros-cli daq discover` mengimbas penderia yang boleh anda sambungkan, dan `chloros-cli daq --help` menyenaraikan subperintah `pool-*`.
* **`chloros-cli project`** — jalankan projek yang disimpan (kamera, penderia, dan tetapan pemprosesannya) secara tanpa paparan.
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
**`--eth-host` melangkau penemuan.** `pool-connect --eth` mencari DAQ-E melalui mDNS, dengan sokongan fallback ARP yang berfungsi daripada cache ARP yang kosong; dengan memberikan IP atau nama hos sensor menggunakan `--eth-host`, ia akan menyambung terus kepadanya.
{% endhint %}

Kebenaran siri DAQ-U diuruskan oleh peraturan udev yang dipasang (`uaccess` + kumpulan `dialout`). Jika sensor yang sudah disambungkan masih tidak dapat diakses, muatkan semula peraturan atau sambungkan semula:

```bash
sudo udevadm control --reload-rules
sudo udevadm trigger --subsystem-match=tty
```

Lihat [rujukan [CLI](../CLI.md)] untuk set arahan penuh.

### PTP Sentiasa-Hidup untuk Hos Tanpa Skrin

Pakej ini memasang dua perkhidmatan systemd untuk backend, kedua-duanya tidak diaktifkan. Pada Jetson tanpa kepala atau pelayan yang perlu mengekalkan penyelarasan masa PTP berjalan secara berterusan untuk sensor DAQ-E dan kamera LATTICE, atau di mana skrip SDK menyambungkan projek yang disimpan (`connect_all()` tidak pernah memulakan backend), aktifkan perkhidmatan **sebagai pengguna anda**:

```bash
sudo systemctl enable --now chloros-backend@$USER.service    # $USER expands to your user name
systemctl status chloros-backend@$USER.service
```

`chloros-backend@<user>.service` menjalankan backend sebagai pengguna tersebut: tangkapan yang ditulisnya adalah milik mereka, log terperincinya berada dalam `~/.cache/chloros/logs` mereka, dan ia menggunakan `chloros-cli login` mereka (`~/.chloros`), jadi log masuk sekali sebagai pengguna itu terlebih dahulu. Memasang pakej mencetak arahan ini dengan nama pengguna anda diisi.

Tanpa perkhidmatan, backend (dan bersama dengannya grandmaster PTP) hanya berjalan selagi sesuatu telah memulakannya — arahan `chloros-cli` atau skrip SDK.

Unit ini mengikat backend kepada `127.0.0.1:5000` (tetapan persekitaran `CHLOROS_HOST` / `CHLOROS_PORT` di dalam unit; diimbangi dengan `sudo systemctl edit chloros-backend@.service`) dan memulakannya semula jika gagal selepas 5 saat.

**Perkhidmatan root.**`chloros-backend.service` menjalankan backend sebagai**root**. Ia masih dipasang supaya mesin yang diaktifkannya terus berjalan, tetapi segala yang ditulis oleh backend root milik root, jadi pengguna tidak dapat memadam tangkapan mereka sendiri; lognya berada di `/var/log/chloros`, boleh dibaca oleh kumpulan `adm`. Untuk bertukar kepada pengguna anda:

```bash
sudo systemctl disable --now chloros-backend.service
sudo systemctl enable --now chloros-backend@$USER.service
```

**Backend perkhidmatan dikongsi, tidak pernah dihentikan oleh klien.**Skrip `chloros-cli` dan SDK menggunakannya seperti mana ia ditemui: `process` dijalankan padanya, memuatkan projeknya sendiri menggantikan projek terakhir. Di mana CLI sebaliknya akan memulakan semula backend (`--restart`, atau `process` yang importannya tidak menemui apa-apa), ia menamakan unit itu, mencetak `sudo systemctl restart <unit>` dan keluar `1`, kerana backend yang dihentikan dengan cara itu akan kekal terhenti. Mulakan semula sendiri dengan `sudo systemctl restart chloros-backend@$USER.service`. Backend perkhidmatan tidak pernah menghentikan dirinya sendiri semasa tidak aktif, dan `chloros-cli backend stop` menolaknya, menamakan arahan `systemctl stop`.**Bagaimana PTP mendapatkan portnya.** PTP menggunakan UDP 319/320, kedua-duanya di bawah ambang port istimewa biasa 1024. Pakej `postinst` menulis `/etc/sysctl.d/60-chloros-ptp.conf` dengan `net.ipv4.ip_unprivileged_port_start = 319`, yang membolehkan backend mengikatnya semasa dijalankan sebagai pengguna anda. Mulai versi 1.3.0, pakej ini tidak menetapkan sebarang keupayaan fail (`setcap`) pada backend, dan `libcap2-bin` tidak lagi menjadi kebergantungan: pada backend yang tidak dikemas, keupayaan fail akan menghalangnya daripada bermula, jadi jangan menambahkannya sendiri.

***

## Contoh Skrip Bash

{% hint style="info" %}
Kod keluar mesra skrip. `chloros-cli process` keluar `0` apabila berjaya dan **bukan sifar apabila gagal — termasuk larian yang meminta produk imej tetapi tidak menulis sebarang** (ia mencetak `Processing finished but wrote no image products.` dan menamakan folder projek serta punca-punca biasa). Jalnan yang berjaya melaporkan berapa banyak produk imej telah ditulis (`Image products written: N`, serta `(plus M clamp masks)` apabila topeng ditulis)Kod keluar: `0` berjaya, `1` gagal, `2` ralat hujah, `130` terganggu.
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
    --indices NDVI GNDVI OSAVI \
    --debayer texture-aware \
    --no-vignette
```

Nilai `--format` yang sah adalah tepat empat, dan ia mengandungi ruang — sentiasa petiknya:

| Nilai `--format` | Folder keluaran |
| --- | --- |
| `TIFF (16-bit)` *(laluan lalai)* | `tiff16` |
| `TIFF (8-bit)` | `tiff8` |
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
    indices=["NDVI", "OSAVI"],
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
sudo chmod +x /usr/lib/chloros/chloros-backend /usr/lib/chloros/backend/chloros-backend
```

### Backend Tidak Akan Berfungsi Selepas Menambah Kebolehan Fail

Jangan jalankan `setcap` pada backend. Mulai versi 1.3.0, pakej ini tidak menetapkan sebarang keupayaan fail, dan port 319/320 PTP berasal daripada tetapan `sysctl` yang diterangkan di bawah [Bagaimana PTP mendapatkan portnya](#always-on-ptp-for-headless-hosts). Keupayaan fail meletakkan backend yang tidak dikemas dalam mod pelaksanaan selamat di mana ia tidak dapat mencari perpustakaan sendiri, jadi ia tidak akan bermula. Jika anda menambah satu, keluarkannya:

```bash
sudo setcap -r /usr/lib/chloros/backend/chloros-backend
```

### PTP Tidak Akan Dimulakan / Tidak Dapat Mengikat Pelabuhan 319

Pastikan lantai pelabuhan tanpa keistimewaan telah diturunkan, dan terapkan semula untuk but semasa jika ia belum:

```bash
sysctl net.ipv4.ip_unprivileged_port_start     # expect 319
sudo sysctl -w net.ipv4.ip_unprivileged_port_start=319
```

Kemudian semak grandmaster:

```bash
chloros-cli time-sync status
chloros-cli time-sync peers
```

### Perintah `lattice` Gagal Dengan "pemacu kamera LATTICE tidak ditemui"

Backend tidak dapat memuatkan runtime kamera LATTICE yang dipasang dengannya. Setiap arahan `lattice` dijalankan pada backend; CLI itu sendiri tidak mempunyai pemacu kamera. Pasang semula Chloros (`sudo apt install --reinstall ./chloros_1.3.0_<arch>.deb`): `.deb` memasang runtime di sebelah backend, dalam `/usr/lib/chloros/arena_runtime`, dan mendaftarkannya dengan pemuat dinamik. Untuk memeriksa pendaftaran tersebut:

```bash
cat /etc/ld.so.conf.d/Arena_SDK.conf     # expect /usr/lib/chloros/arena_runtime
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

Log backend untuk pelancaran yang gagal terdapat di `~/.cache/chloros/logs/`. Jika port 5000 dimiliki oleh perkhidmatan backend (`systemctl status chloros-backend@$USER.service`), mulakan semula perkhidmatan tersebut dan bukannya menghentikan prosesnya.

### CUDA Tidak Dikesan

```bash
# Check NVIDIA driver installation (x86_64)
nvidia-smi

# Check CUDA availability
nvcc --version

# On Jetson, check the L4T release (JetPack 6 is R36) and that JetPack's CUDA libraries are installed
cat /etc/nv_tegra_release
sudo apt install nvidia-jetpack
```

`chloros-cli selftest` melaporkan perkara yang sama dalam satu baris: `GPU: <name>, CUDA: <bool>, PyTorch: <version>`.

### Pustaka Berkongsi Hilang

```bash
sudo apt-get update
sudo apt-get install -f

# Check for missing libraries (the program itself, not the launcher script)
ldd /usr/lib/chloros/backend/chloros-backend | grep "not found"
```

### Permulaan Perlahan pada Sistem Kad SD

Mulai versi 1.3.0, backend dan CLI dipasang tanpa dibongkar, jadi ia tidak lagi mengekstrak sendiri ke dalam direktori sementara pada setiap permulaan. Fail sementara disalurkan ke `/mnt/ssd/tmp` secara automatik apabila folder tersebut wujud; jika tidak, anda boleh menentukannya ke sistem fail yang pantas:

```bash
export TMPDIR=/mnt/nvme/tmp
```

***

## Mengemas kini Chloros pada Linux

Perintah `update` hanya untuk Linux/Jetson. Ia menyemak versi yang diterbitkan dalam saluran kemas kini yang dikonfigurasikan di `/etc/chloros/update.conf` dan menawarkan untuk memuat turun dan memasang `.deb` yang sepadan:

```bash
# Check for updates without installing
chloros-cli update --check

# Check for and install updates
chloros-cli update
```

Pada Linux/Jetson, CLI juga menjalankan semakan kemas kini tanpa halangan pada setiap permulaan (hasil disimpan dalam cache selama satu jam di `~/.chloros/update_cache.json`) dan mencetak `Update available: vX.Y.Z` apabila terdapat versi yang lebih baru. Tetapan anda, projek dan log masuk akan kekal selepas kemas kini.

Menaik taraf pakej menghentikan setiap perkhidmatan backend semasa pemasangan, jadi skrip yang sedang berjalan akan kehilangan kamera dan penderia, dan kemudian memulakan semula setiap satu yang diaktifkan (satu yang dimulakan secara manual tetapi tidak pernah diaktifkan akan kekal berhenti, seperti selepas but semula).

## Nyahpasang

```bash
sudo apt remove chloros
```

Pemupusan menghentikan dan menyahdayakan setiap perkhidmatan backend, memulihkan had lantai port tanpa keistimewaan lalai (1024), membuang fail tetapan buffer-penerimaan (nilai yang sedang berjalan kekal sehingga but semula), pautan simbol exiftool terbungkus, konfig loader Arena, peraturan firewall yang ditambah oleh pakej dan Python SDK yang dipasangnya, dan memadam butiran log masuk yang disimpan dalam cache. Projek dan fail data `~/.chloros/` anda tidak akan diubah. `sudo apt purge chloros` juga memadam `/var/log/chloros`.

***

## Langkah Seterusnya

* [Panduan NVIDIA Jetson](nvidia-jetson-guide.md) — Pengoptimuman dan penyebaran khusus Jetson
* [CLI : Command Line](../CLI.md) — panduan CLI
* [API : Python SDK](../api-python-sdk.md) — panduan SDK
* [Rujukan CLI](../reference/cli-reference.md) dan [Rujukan SDK](../reference/sdk-reference.md) — senarai lengkap arahan/API untuk 1.3.0
* [Penyesuaian Komputasi Dinamik](../processing-architecture/dynamic-compute-adaptation.md) — bagaimana Chloros menyesuaikan diri dengan perkakasan anda
