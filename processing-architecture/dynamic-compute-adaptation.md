# Penyesuaian Pengiraan Dinamik

Chloros 1.1.0 memperkenalkan pengesanan perkakasan pintar dan pemilihan strategi pemprosesan automatik. Enjin pemprosesan menyesuaikan diri dengan perkakasan anda — daripada Jetson Nano kepada stesen kerja berbilang GPU — tanpa sebarang konfigurasi manual.

***

## Cara Ia Berfungsi

Apabila Chloros bermula, ia secara automatik memprofilkan sistem anda:

1. **Mengesan sistem pengendalian** — Windows atau Linux
2. **Mengenal pasti teras CPU dan jumlah RAM**

3.**Mengesan kehadiran GPU** — Keupayaan NVIDIA CUDA, VRAM, model
4. **Mengenal pasti model Jetson** (jika berkenaan) — melalui `/proc/device-tree/model`
5. **Menyemak penderia terma** (Jetson) — untuk pemprosesan menyedari suhu
6. **Memilih strategi pengiraan optimum** — berdasarkan semua perkakasan yang dikesan
7. **Mengkonfigurasikan kiraan pekerja, jenis saluran paip dan peruntukan memori** secara automatik

Hasilnya dicache supaya larian berikutnya bermula lebih cepat. Jika perkakasan berubah (cth., GPU ditambahkan), Chloros memfailkan semula pada pelancaran seterusnya.

***

## Strategi Pengiraan

Chloros memilih salah satu daripada tiga strategi pengiraan berdasarkan perkakasan anda:

| Strategi | GPU Diperlukan | Pekerja | Talian Paip | Terbaik Untuk |
| --- | --- | --- | --- | --- |
| **`GPU_PARALLEL`** | Ya (12GB+ VRAM atau 16GB+ dikongsi) | 3-4 | `fused_gpu` | GPU Desktop dengan 12GB+, Jetson Orin NX 16GB, AGX Orin |
| **`GPU_SINGLE`** | Ya (< 12GB VRAM) | 1-3 | `tiled_gpu` | GPU peringkat permulaan, Jetson Nano, Orin Nano |
| **`CPU_PARALLEL`** | Tidak | teras - 1 | `cpu_fallback` | Sistem tanpa GPU NVIDIA |

### Jenis Saluran Paip

* **`fused_gpu`** — Laluan pemprosesan GPU penuh. Semua operasi debayer, pembetulan dan indeks dijalankan pada GPU dalam satu pas bersatu. Daya pengeluaran tertinggi tetapi memerlukan lebih banyak VRAM.
* **`tiled_gpu`** — Laluan GPU yang cekap memori. Memproses imej dalam jubin untuk dimuatkan dalam memori GPU yang terhad. Daya pengeluaran yang lebih rendah tetapi berfungsi pada peranti yang dikekang memori.
* **`cpu_fallback`** — pemprosesan CPU sahaja menggunakan selari berbilang benang. Digunakan apabila tiada GPU NVIDIA tersedia.***

## Gelagat Khusus Platform

| Platform | Strategi | Pekerja | Talian Paip | Nota |
| --- | --- | --- | --- | --- |
| **Jetson Nano 8GB** | `GPU_SINGLE` | 1 | `tiled_gpu` (bersiri) | Mod cekap memori, memproses satu imej pada satu masa |
| **Jetson Orin NX 16GB** | `GPU_PARALLEL` | 3 | `fused_gpu` (serentak) | Peranti kelebihan yang disyorkan — pemprosesan GPU selari sebenar |
| **Jetson AGX Orin 64GB** | `GPU_PARALLEL` | 4 | `fused_gpu` (serentak) | Prestasi kelebihan maksimum |
| **Desktop dengan 8GB GPU** | `GPU_SINGLE` | 3 | `tiled_gpu` | Prestasi desktop yang baik dengan jubin cekap memori |
| **Desktop dengan 12GB+ GPU** | `GPU_PARALLEL` | 3-4 | `fused_gpu` | Prestasi desktop optimum |
| **Sistem CPU sahaja** | `CPU_PARALLEL` | teras - 1 | `cpu_fallback` | Tiada GPU diperlukan, menggunakan ThreadPool |

{% hint style="info" %}
**Memori bersatu Jetson**: Peranti Jetson berkongsi memori GPU dan CPU. Jetson Orin NX 16GB melaporkan ~15.3GB VRAM, tetapi ini adalah RAM fizikal yang sama yang digunakan oleh proses OS dan CPU. Chloros mengambil kira ini apabila menetapkan ambang peruntukan memori.
{% endhint %}

***

## Peruntukan Memori GPU Dinamik

Chloros menggunakan [talian paip pemprosesan 4-benang](processing-pipeline.md):

* **Benang 1** (Pengesanan) — Pemuatan imej, penghuraian EXIF, pengesanan sasaran
* **Benang 2** (Penentukuran) — Pengiraan penentukuran pantulan
* **Benang 3** (Pemprosesan) — Pendebay GPU, pembetulan vignet, pengiraan indeks
* **Benang 4** (Eksport) — Penulisan fail, pembenaman metadata

Apabila utas saluran paip terdahulu menyelesaikan kerjanya (cth., semua imej telah dikesan), peruntukan memori GPU mereka dikeluarkan dan **diagihkan semula ke rangkaian aktif yang tinggal**. Ini bermakna Thread 3 (peringkat intensif GPU) mendapat lebih banyak memori secara beransur-ansur apabila saluran paip semakin maju, meningkatkan daya pemprosesan untuk kerja yang paling intensif pengiraan.

### Peringkat Peruntukan

| Pentas | Benang Aktif | Pengagihan Memori GPU |
| --- | --- | --- |
| **Awal** | 1, 2, 3, 4 | Pisahkan semua urutan |
| **Pertengahan Awal** | 2, 3, 4 | Memori benang 1 diagihkan semula |
| **Pertengahan Lewat** | 3, 4 | Memori benang 1+2 pergi ke 3+4 |
| **Lewat** | 3 atau 4 | Memori maksimum untuk baki benang |***

## Pemprosesan Sedar Tekstur

Kaedah debayer Texture Aware (Chloros+ sahaja) menggunakan lebih banyak memori GPU dengan ketara berbanding kaedah Standard disebabkan oleh model denoising AI/ML:

* Sistem dengan **< 7GB VRAM** dipaksa ke dalam gelung pemprosesan segerak untuk mod Texture Aware (satu imej pada satu masa)
* Sistem dengan **7GB+ VRAM** boleh memproses Texture Aware secara serentak, walaupun pada bilangan pekerja yang berkurangan berbanding Standard***

## Pengurusan Terma (Jetson)

Peranti Jetson mempunyai kekangan haba, terutamanya dalam penggunaan tertutup atau bawaan udara. Chloros memantau suhu GPU dan CPU dan melaraskan pemprosesan secara automatik:

| Suhu | Maklum balas |
| --- | --- |
| ***70°C** | Operasi biasa — kelajuan penuh |
| **70°C** (Amaran) | Kurangkan saiz kelompok |
| **80°C** (Kritis) | Pendikitan agresif — serentak yang lebih rendah dan kiraan pekerja |
| **90°C** (Tutup) | Hentikan pemprosesan GPU sepenuhnya |

Pemantauan suhu menggunakan `tegrastats` pada platform Jetson. Pada sistem desktop dengan penyejukan yang mencukupi, pendikitan haba jarang dicetuskan.

***

## Pengendalian Tekanan Memori

Chloros memantau tekanan memori sistem semasa pemprosesan:

* **Ambang ingatan**: 85% penggunaan mencetuskan tingkah laku konservatif
* **Pengurangan OOM**: Jika peristiwa di luar ingatan berlaku, peruntukan dikurangkan sebanyak 25% (0.75x gandaan)
* **Sambungan saluran paip**: Di bawah tekanan memori yang teruk, saluran paip jatuh semula daripada `fused_gpu` kepada `tiled_gpu` secara automatik
* **Cadangan pertukaran**: Pada Jetson, Chloros memberi amaran kepada anda jika ruang swap tidak mencukupi untuk saiz set data anda***

## Memantau Penyesuaian Kiraan

### CLI Status Output

Apabila pemprosesan bermula, CLI memaparkan profil perkakasan yang dikesan:

```

Chloros CLI 1.1.0
Platform: Linux aarch64 (Jetson Orin NX 16GB)
Strategy: GPU_PARALLEL | Workers: 3 | Pipeline: fused_gpu
CUDA: Available | GPU Memory: 15.3 GB (shared)
```

### Diagnostik Sistem

Jalankan `chloros-cli selftest` untuk melihat profil perkakasan penuh dan mengesahkan keupayaan pengiraan:

```bash
chloros-cli selftest
```

Ini menyemak ketersediaan CUDA, memori GPU, model denoiser dan kesambungan hujung belakang.

***

## Langkah Seterusnya

* [Memproses Paip](processing-pipeline.md) — Memahami seni bina saluran paip 4-benang
* [Panduan NVIDIA Jetson](../linux/nvidia-jetson-guide.md) — Pengerahan dan pengoptimuman khusus Jetson
* [CLI : Command Line](../CLI.md) — Rujukan penuh CLI