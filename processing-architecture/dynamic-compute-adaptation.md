# Adaptasi Komputasi Dinamik

Chloros 1.3.0 menggunakan pengesanan perkakasan dan pemilihan strategi pemprosesan automatik. Enjin pemprosesan menyesuaikan diri dengan perkakasan anda — daripada Jetson Orin Nano hingga stesen kerja berbilang GPU — tanpa sebarang konfigurasi manual.

***

## Cara Ia Berfungsi

Apabila Chloros memulakan, ia memproses profil sistem anda:

1. **Mengesan sistem pengendalian** — Windows atau Linux
2. **Mengecam teras CPU dan jumlah RAM**

3.**Mengesan kehadiran GPU** — kebolehan NVIDIA CUDA, VRAM, model
4. **Mengesan model Jetson** (jika berkenaan) — melalui `/proc/device-tree/model`
5. **Menyemak sensor haba** (Jetson) — untuk pemprosesan yang peka terhadap suhu
6. **Memilih strategi pengkomputeran** — berdasarkan semua perkakasan yang dikesan
7. **Mengkonfigurasi bilangan pekerja, jenis saluran, dan peruntukan memori** secara automatik

Profil yang dikesan disimpan dalam cache untuk sesi dalam memori dan pada cakera, jadi pelaksanaan kemudian akan bermula dengan lebih pantas:

| Platform | Profil yang disimpan |
| --- | --- |
| **Linux / Jetson** | `~/.config/chloros/system_config.json` (mengiktiraf `XDG_CONFIG_HOME`) |
| **Windows** | `%LOCALAPPDATA%\Chloros\config\system_config.json` |

Padamkan fail tersebut untuk memaksa pengesanan semula — berguna selepas menambah GPU atau lebih banyak RAM. Chloros juga mengesan semula secara automatik apabila cache ditulis oleh versi lama yang tidak serasi.

***

## Strategi Pengkomputeran

Chloros memilih salah satu daripada tiga strategi pengkomputeran berdasarkan perkakasan anda:

| Strategi | Pilihan Apabila | Pekerja | Pelaksana | Saluran |
| --- | --- | --- | --- | --- |
| **`GPU_PARALLEL`**| CUDA GPU melaporkan**12GB+ VRAM**(pada memori bersatu Jetson, juga memerlukan jumlah RAM kongsi 12GB+) | `min(4, ⌊(VRAM − 0.5GB) × 0.9 ÷ 3.5GB⌋)`, minimum 2 —**hadnya 2 pada Jetson** | Proses pekerja (konteks CUDA berasingan) | `fused_gpu` |
| **`GPU_SINGLE`**| CUDA GPU dengan**2-12GB VRAM**| 3 (tindihan I/O; akses GPU disusunkemudi oleh semafor).**1 (bersiri) pada Jetson dengan RAM kurang daripada 12GB** | Proses pekerja (konteks CUDA berasingan); bersiri dalam proses pada Jetson RAM rendah | `fused_gpu` / `tiled_gpu` |
| **`CPU_PARALLEL`** | Tiada GPU CUDA, atau VRAM kurang daripada 2GB | `max(2, physical cores − 1)` | `ThreadPoolExecutor` | `cpu_fallback` |

Contoh kerja formula pekerja `GPU_PARALLEL`: 12GB VRAM → 2 pekerja, 16GB → 3 pekerja, 20GB+ → 4 pekerja. Setiap pekerja diperuntukkan 3.5GB, keperluan yang diukur bagi tugas Texture Aware, dalam 90% VRAM yang tinggal selepas rizab 0.5GB.

{% hint style="info" %}
**Strategi GPU memerlukan PyTorch yang boleh menggunakan CUDA.** Mesin yang PyTorch-nya tiada peranti CUDA yang boleh digunakan akan mendapat `CPU_PARALLEL`, tidak kira apa pun GPUnya. Pakej Jetson 1.3.0 menggunakan binaan CUDA PyTorch JetPack 6, jadi Jetson layak setelah pustaka CUDA dan cuDNN JetPack dipasang. Jetson dengan RAM kurang daripada 12GB sentiasa memproses satu imej pada satu masa, dalam proses (dicatat sebagai `GPU_SINGLE`).
{% endhint %}

Strategi GPU menjalankan kolam proses pekerja, setiap satu adalah proses OS berasingan dengan konteks CUDA tersendiri, yang dimulakan oleh Chloros dengan melancarkan semula program backend yang dipasangnya (bukan melalui `multiprocessing` milik Python, kerana permulaan pekerjaannya tidak berfungsi di dalam aplikasi terbungkus). Strategi CPU menggunakan kolam benang (Python's `ThreadPoolExecutor`). Chloros tidak menggunakan sebarang rangka kerja teragih pihak ketiga (seperti Ray).

### Jenis Saluran

* **`fused_gpu`** — Laluan pemprosesan GPU penuh. Operasi Debayer, pembetulan, dan indeks dijalankan pada GPU dalam satu pas gabungan. Kelajuan pemprosesan tertinggi, memerlukan VRAM paling banyak.
* **`tiled_gpu`** — Laluan GPU cekap memori. Memproses imej dalam jubin untuk muat dalam memori GPU yang terhad. Kelajuan pemprosesan lebih rendah tetapi berfungsi pada peranti yang terhad memorinya.
* **`cpu_fallback`** — Pemprosesan hanya CPU menggunakan keparalelan berbilang benang. Digunakan apabila tiada GPU NVIDIA tersedia, dan sebagai jalan pintas pilihan terakhir apabila kedua-dua laluan GPU gagal.

Rantaian sandaran masa larian sentiasa adalah `fused_gpu` → `tiled_gpu` → `cpu_fallback`.

***

## Penggantian Strategi Manual

Aturkan pembolehubah persekitaran `CHLOROS_STRATEGY` untuk memaksa strategi tertentu — jalan keluar pakar apabila pengesanan automatik memilih sesuatu yang tidak sesuai untuk situasi anda (contohnya, mengekalkan GPU kosong untuk kerja lain):

```bash
# Valid values: CPU_PARALLEL, GPU_SINGLE, GPU_PARALLEL
CHLOROS_STRATEGY=CPU_PARALLEL chloros-cli process ~/datasets/flight001
```

Pembolehubah dipadankan tanpa mengira huruf besar/kecil; apa sahaja yang bukan salah satu daripada tiga nama tersebut diabaikan dan pengesanan automatik diteruskan seperti biasa. Di bawah keutamaan, Chloros masih memilih bilangan pekerja untuk anda:

| Keutamaan | Bilangan pekerja yang digunakan |
| --- | --- |
| `CPU_PARALLEL` | `max(2, physical cores − 1)` |
| `GPU_SINGLE` | 3 |
| `GPU_PARALLEL` | `min(4, physical cores)` |

Penggabungan `GPU_SINGLE` atau `GPU_PARALLEL` pada mesin yang tiada peranti CUDA boleh guna akan ditolak dengan baris `[STRATEGY]` dalam log, dan larian menggunakan `CPU_PARALLEL`.

Utamakan menetapkan ia setiap arahan daripada secara kekal, supaya larian biasa terus menyesuaikan secara automatik.

***

## Kelakuan Khusus Platform

| Platform | Strategi | Pekerja | Saluran | Nota |
| --- | --- | --- | --- | --- |
| **Jetson Orin Nano 8GB** | `GPU_SINGLE` | 1 | `tiled_gpu` (bersiri) | Mod cekap memori, satu imej pada satu masa |
| **Jetson Orin NX 8GB** | `GPU_SINGLE` | 1 | `tiled_gpu` (bersiri) | RAM kongsi di bawah 12GB memaksa pemprosesan bersiri |
| **Jetson Orin NX 16GB** | `GPU_PARALLEL` | 2 | `fused_gpu` (serentak) | Peranti pinggir yang disyorkan — Jetson terhad kepada 2 pekerja |
| **Jetson AGX Orin 32-64GB** | `GPU_PARALLEL` | 2 | `fused_gpu` (serentak) | Prestasi tepi maksimum (juga terhad pada Jetson kepada 2 pekerja) |
| **Desktop dengan GPU 8GB** | `GPU_SINGLE` | 3 | `fused_gpu` / `tiled_gpu` | 3 pekerja bertindih I/O manakala semafor menyirialisasikan akses GPU |
| **Desktop dengan GPU 12GB+** | `GPU_PARALLEL` | 2-4 | `fused_gpu` (serentak) | Prestasi desktop optimum: 12GB → 2 pekerja, 16GB → 3, 20GB+ → 4 |
| **Sistem CPU sahaja** | `CPU_PARALLEL` | teras fizikal − 1 (min 2) | `cpu_fallback` | Tiada GPU diperlukan, menggunakan kolam benang |

{% hint style="info" %}
**Memori bersatu Jetson**: Peranti Jetson berkongsi memori GPU dan CPU. Jetson Orin NX 16GB melaporkan ~15.3GB VRAM, tetapi itu adalah RAM fizikal yang sama digunakan oleh proses OS dan CPU. Itulah sebabnya Jetson 16GB+ layak untuk `GPU_PARALLEL` seperti GPU desktop 12GB+, namun dihadkan kepada 2 pekerja — GPU, proses pekerja, dan konteks CUDA setiap pekerja semuanya menggunakan kolam kongsi yang sama.
{% endhint %}

### Belanjawan GPU mengikut VRAM (GPU berasingan)

Pada hos x86_64 dengan GPU NVIDIA berasingan, VRAM yang dikesan juga menetapkan berapa banyak pemprosesan kad boleh tuntut dan sejauh mana saiz kumpulan boleh membesar:

| VRAM dikesan | Had bajet GPU | Pelbagai saiz kelompok |
| --- | --- | --- |
| **8GB+** | 90% | ×2.0 |
| **6-8GB** | 85% | ×1.75 |
| **

3.5-6GB** | 80% | ×1.5 |
| **2-3.5GB** | 75% | ×1.25 |
| **Kurang daripada 2GB** | 70% | ×1.0 |

GPU berasingan hanya memperuntukkan 0.5GB untuk sistem, kerana ia tidak berkongsi RAM sistem. Profil Jetson memperuntukkan jauh lebih banyak dan hadnya lebih rendah — lihat [Panduan NVIDIA Jetson](../linux/nvidia-jetson-guide.md#per-model-gpu-budget).

***

## Peruntukan Memori GPU Dinamik

Chloros menggunakan [saluran pemprosesan 4-benang](processing-pipeline.md):

* **Benang 1** (Pengesanan) — Pemuatan imej, penganalisisan EXIF, pengesanan sasaran
* **Benang 2** (Kalibrasi) — Pengiraan penentukuran pantulan
* **Thread 3** (Pemprosesan) — Debayer GPU, pembetulan vignet, pengiraan indeks
* **Thread 4** (Eksport) — Penulisan fail, penyisipan metadata

Thread 1, 2, dan 4 menggunakan GPU ringan; Thread 3 adalah yang paling membebani. Apabila thread dalam saluran sebelumnya selesai, bajet GPU mereka **diagihkan semula kepada thread aktif yang tinggal**, jadi Thread 3 mendapat memori yang semakin banyak apabila proses berjalan.

### Tahap Pengagihan

| Tahap | Thread Aktif | Pengagihan Memori GPU |
| --- | --- | --- |
| **Awal** | 1, 2, 3, 4 | Dibahagikan kepada semua thread, kebanyakannya kepada Thread 3 |
| **Awal-Pertengahan** | 2, 3, 4 | Bahagian Thread 1 diagihkan semula |
| **Pertengahan-Akhir** | 3, 4 | Bahagian Thread 1+2 diberikan kepada 3+4 |
| **Akhir** | 3 atau 4 | Thread terakhir yang aktif mendapat peruntukan maksimumnya |

Dua peraturan mengawal angka-angka ini:

* Sebarang thread yang **satu-satunya** aktif akan diberikan peruntukan maksimum profilnya.
* Apabila lebih daripada satu tugas GPU *berat* aktif, peruntukan asas setiap tugas berat akan dibahagikan di antara mereka (tidak pernah di bawah minimum yang dikonfigurasikan).

Nilai yang sebenarnya digunakan semasa runtime ialah yang **lebih rendah** antara peruntukan profil platform dan cadangan masa nyata daripada pemantau memori GPU, jadi kad yang sibuk sentiasa diutamakan berbanding profil yang optimistik.***

## Pemprosesan Sedar Tekstur

Debayer Texture Aware (hanya **Chloros+** — `--debayer texture-aware`) menjalankan model penapisan hingar AI/ML yang memerlukan kira-kira 1.75GB VRAM dalam FP16 bagi setiap salinan, jadi ia menggunakan memori GPU yang jauh lebih banyak berbanding kaedah Standard:

* Sistem dengan **kurang daripada 7GB VRAM**memproses Texture Aware dalam**gelung serentak, satu imej pada satu masa** — beberapa salinan model tidak muat, dan kolam pekerja hanya akan menambah perebutan
* Sistem dengan **VRAM 7GB+** boleh memproses Texture Aware secara serentak, walaupun dengan bilangan pekerja yang dikurangkan berbanding Standard
* Pada **Jetson**, Texture Aware sentiasa dipin pada satu pekerja sahaja
* Dengan **tiada GPU CUDA yang boleh digunakan**, pelaksanaan menggunakan debayer Standard, dan backend mencatat satu baris `[DEBAYER] … needs a CUDA GPU …` yang menerangkan sebabnya
* Pada Jetson berkuasa rendah (Nano, Orin Nano), CLI akan mengenakan had frekuensi GPU apabila anda meminta Texture Aware — lihat [Panduan NVIDIA Jetson](../linux/nvidia-jetson-guide.md#gpu-frequency-cap-for-texture-aware-on-nano-and-orin-nano)

***

## Pengurusan Terma (Jetson)

Peranti Jetson mempunyai sekatan terma, terutamanya dalam penyebaran tertutup atau di udara. Chloros memantau penderia suhu terbina dalam Jetson dan menyesuaikan saiz kumpulan secara automatik:

| Suhu | Tindak balas |
| --- | --- |
| **< 70°C** | Operasi biasa — kelajuan penuh |
| **70°C** (Amaran) | Saiz batch dikurangkan secara berperingkat (100% → 50% antara 70°C dan 80°C) |
| **80°C** (Kritikal) | Penghadan agresif (50% → 0% antara 80°C dan 90°C) |
| **90°C** (Penutupan) | Hentikan pemprosesan GPU sepenuhnya |

Pada sistem desktop dengan penyejukan yang mencukupi, pelambatan terma jarang dicetuskan.

***

## Pengendalian Tekanan Memori

Chloros memantau memori GPU secara berterusan semasa pemprosesan dan bertindak balas pada tiga peringkat.

**Saiz kumpulan.** Satu kumpulan bermula pada 8 imej kali pendaraban platform daripada jadual di atas. Chloros kemudian memeriksa VRAM kosong, menempatkan 20% daripadanya untuk keperluan PyTorch sendiri, dan menganggap kira-kira 100MB memori GPU bagi setiap imej 12MP — kumpulan itu ialah yang lebih kecil, sama ada had yang diperoleh daripada memori atau asas platform. Ia tidak pernah turun di bawah 1.

**Pengurangan pencegahan.**Di atas**85% penggunaan VRAM**, saiz kumpulan dikurangkan sebelum apa-apa gagal.**Pemotongan peruntukan per-benang.** Apabila penggunaan langsung meningkat, bajet GPU setiap benang dikurangkan: ×0.75 melebihi penggunaan 80%, ×0.5 melebihi 90%. Julat pemantau ialah 70% (konservatif), 85% (had operasi biasa), dan 95% (risiko OOM).**Penangguhan dan pemulihan OOM.** Jika peristiwa kehabisan memori (out-of-memory) berlaku juga:

* saiz kumpulan **dibahagikan kepada separuh**, dan dibahagikan kepada separuh lagi pada setiap OOM berturut-turut — setiap kumpulan berjaya berikutnya mengurangkan penalti itu satu langkah
* peruntukan benang aktif dipotong kepada 70% daripada nilai semasa mereka dan pengagih beralih kepada strategi konservatifnya, dan kembali melonggarkan selepas beberapa peruntukan berjaya berturut-turut
* di bawah tekanan teruk, saluran kembali dari `fused_gpu` ke `tiled_gpu`, dan ke `cpu_fallback` sebagai pilihan terakhir

**RAM Host (Jetson).** Sebelum pemprosesan, CLI menganggarkan memori hos puncak daripada kiraan imej dan mod debayer anda dan memberi amaran jika RAM ditambah swap disokong fail mungkin tidak mencukupi, mencetak arahan tepat untuk menambah swap — lihat [Panduan NVIDIA Jetson](../linux/nvidia-jetson-guide.md#swap-warning-and-recommendations).

***

## Pemantauan Penyesuaian Komputasi

### Diagnostik Sistem

`chloros-cli selftest` adalah cara terpantas untuk mengesahkan apa yang dilihat oleh lapisan komputasi:

```bash
chloros-cli selftest
```

7 pemeriksaan beliau merangkumi versi, ketersediaan port, permulaan backend, `/api/test`, maklumat sistem, kewujudan model denoiser, dan kesiapsiagaan CUDA + denoiser. Pemeriksaan 5 mencetak baris perkakasan secara langsung:

```
      GPU: NVIDIA RTX A4000, CUDA: True, PyTorch: 2.7.0
```

Semakan 7 mencetak `CUDA: <bool>, Denoiser: <bool>` — kedua-duanya mesti benar supaya Texture Aware boleh digunakan sama sekali.

### Log Backend

Strategi dan bilangan pekerja dipilih di dalam backend pada permulaan setiap pelaksanaan — tiada papan pengumuman CLI yang mengumumkannya. Apabila sesuatu berkelakuan tidak dijangka (jalur GPU beralih ke pilihan kedua, OOM, penyingkir hingar yang tidak dapat dimuat), log backend untuk sesi tersebut adalah tempat ia muncul:

| Platform | Lokasi log |
| --- | --- |
| **Linux / Jetson** | `~/.cache/chloros/logs/backend_<YYYYMMDD_HHMMSS>.log` (satu fail bagi setiap pelancaran) |
| **Linux, backend yang dimulakan oleh CLI** | juga `~/.chloros/backend.log` |
| **Windows** | `%LOCALAPPDATA%\Chloros\logs\` |

### Kemajuan Langsung

Semasa pelaksanaan, CLI memaparkan kemajuan secara langsung bagi setiap thread (Mengesan, Menganalisis, Memproses, Mengeksport) yang disiarkan melalui Server-Sent Events — petunjuk praktikal sama ada Thread 3 adalah botol leher. Lihat [Processing Pipeline](processing-pipeline.md).

***

## Langkah Seterusnya

* [Saluran Pemprosesan](processing-pipeline.md) — Memahami seni bina saluran 4-benang
* [Panduan NVIDIA Jetson](../linux/nvidia-jetson-guide.md) — Penyebaran dan pengoptimuman khusus Jetson
* [CLI: Command Line](../CLI.md) — Panduan CLI

* [Rujukan CLI](../reference/cli-reference.md) — Senarai arahan menyeluruh untuk 1.3.0
