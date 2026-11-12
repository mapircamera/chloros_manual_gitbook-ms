# Saluran Pemprosesan

Chloros 1.3.0 menggunakan saluran pemprosesan 4-benang yang berfungsi seperti barisan pemasangan berperingkat. Setiap benang mengendalikan fasa aliran kerja yang berbeza, jadi beberapa imej boleh berada dalam proses pada peringkat yang berbeza serentak.

<figure><img src="../.gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>

***

## Seni Bina Saluran

```

Images In → [Thread 1: Detection] → [Thread 2: Calibration] → [Thread 3: Processing] → [Thread 4: Export] → Files Out
```

Setiap imej mengalir melalui keempat-empat thread mengikut urutan. Dengan pemprosesan berbilang-thread Chloros+, beberapa imej menempati thread yang berbeza serentak — sementara Thread 3 memproses satu imej, Thread 1 boleh mengesan imej seterusnya, Thread 2 mengkalibrasi imej lain, dan Thread 4 menulis imej yang siap ke cakera.

Kemajuan dilaporkan bagi setiap thread dan disalurkan melalui Server-Sent Events (bahagian belakang menerbitkannya di `/api/events`). Dalam paparan kemajuan secara langsung di CLI, empat peringkat dilabelkan sebagai **Mengesahkan, Menganalisis, Memproses, Mengeksport**.

***

## Butiran Benang

### Benang 1: Pengesanan

**Tujuan**: Memuat imej dan mengesan sasaran penentukuran.

* Membaca fail imej dari cakera — pasangan Survey3 `.raw`+`.jpg`, rekod LATTICE `.tif`/`.tiff`, dan `.dng`
* Mengasingkan metadata EXIF (GPS, model kamera, cap masa, pendedahan)
* Mengesan sasaran penentukuran: geometri sasaran bertanda ArUco untuk rakaman LATTICE, dan penderia panel klasik untuk foto sasaran penentukuran Survey3
* Keluaran: data imej + metadata + keputusan pengesanan sasaran

Secara utama, benang ini bergantung pada I/O dan CPU.

### Benang 2: Penentukuran

**Tujuan**: Mengira parameter penentukuran daripada sasaran yang dikesan.

* Mengira koefisien penentukuran pantulan daripada imej sasaran
* Mengira parameter pembetulan vignet
* Menentukan lengkung penentukuran bagi setiap jalur
* Keluaran: parameter penentukuran untuk setiap imej

Benang pengiraan yang terikat pada CPU. Pada larian Survey3 atau larian campuran, Benang 3 menunggu pada ia apabila penentukuran pantulan diaktifkan, supaya pekali-pekalinya sedia sebelum mana-mana imej diproses. Pada larian LATTICE sahaja, Benang 3 berjalan semasa pengesanan: setiap bingkai bermula sebaik sahaja jangkar sasarannya menjadi muktamad. Satu bingkai mengambil sasaran terdekat dari segi masa di kedua-dua belah, jadi ia menunggu sehingga setiap bingkai kameranya yang lebih dekat dari segi masa berbanding sasaran terdekat semasa telah diimbas. Hasilnya adalah sama, cuma lebih awal.

### Benang 3: Pemprosesan (GPU)

**Tujuan**: Mengaplikasikan pembetulan dan mengira indeks vegetasi.**Ini adalah benang yang paling intensif dari segi pengkomputeran.*** **Debayering**: menukar data Bayer RAW kepada imej berbilang saluran
  * Standard (Pantas, Kualiti Sederhana) — lalai, `--debayer standard`
  * Sedar Tekstur (Lambat, Kualiti Tertinggi) — hanya untuk Chloros+, `--debayer texture-aware`, menggunakan model penyahbisin UI/ML
  * Rakaman LATTICE mono (M3M) adalah jalur tunggal: langkah demosaik dan imbangan putih akan dilangkau untuknya (dengan mesej log satu baris), manakala sebarang imej M3C/Bayer dalam larian yang sama masih akan menerima langkah-langkah tersebut
* **Pembetulan Vignette**: menerapkan pembetulan vignette lensa pada seluruh imej
* **Kalibrasi Reflektan**: menerapkan koefisien kalibrasi untuk menukarkan kepada nilai reflektansi
* **Pengiraan Indeks**: mengira indeks vegetasi (NDVI, NDRE, GNDVI, …)
* Keluaran: data imej yang diproses siap untuk dieksport

Thread ini paling banyak mendapat manfaat daripada pecutan GPU, dan ia adalah thread yang dilaras oleh [Dynamic Compute Adaptation](dynamic-compute-adaptation.md).

### Benang 4: Eksport

**Tujuan**: Menulis imej yang diproses ke cakera.

* Menulis fail keluaran dalam format yang dipilih — `TIFF (16-bit)`, `TIFF (8-bit)`, `TIFF (32-bit, Percent)`, `PNG (8-bit)`, `JPG (8-bit)`
* Menanam metadata dalam fail output (GPS, cap masa, parameter pemprosesan) — termasuk tag pemprosesan `Chloros:*`, yang mulai versi 1.3.0 ditulis dalam setiap mod pemprosesan, untuk Survey3 serta LATTICE
* Mengatur keluaran di bawah folder projek sebagai `<camera>/<format>/<Product>_Images/` — contohnya `LATT-M3M-L41-F550/tiff16/Reflectance_Calibrated_Images/`. **Fail yang dieksport mengekalkan nama fail sumber; folder itu mengenal pasti produk.**
* Untuk tangkapan LATTICE, satu bingkai sumber boleh berkembang menjadi beberapa produk (Debayered, Pratonton, Radiasi, Reflektan, Indeks), masing-masing dalam folder produknya sendiri. Produk Indeks dikira daripada produk reflektan yang dihasilkan oleh larian (jika tiada, daripada produk lain yang dihasilkan oleh larian, seperti radiasi). Produk Radiance dan pantulan daripada kamera multispektral M3C juga boleh mendapat masker pengekang piksel demi piksel dalam `<format>/clamp_masks/` (lihat [Format Imej Keluaran](../output-image-formats.md#clamp-masks))
* Keluaran: fail akhir pada cakera

Utamanya satu benang yang terikat pada I/O — storan SSD memperbaikinya dengan ketara.

***

## Di Sebalik Tabir: Pelaksana

Dalam Benang 3, kerja bagi setiap imej diparaelakkan seperti berikut:

* **Strategi GPU**(`GPU_SINGLE`, `GPU_PARALLEL`) menggunakan kolam**proses pekerja**, masing-masing merupakan proses OS berasingan dengan konteks CUDA tersendiri. Chloros memulakan setiap pekerja dengan melancarkan program backend yang dipasangnya sekali lagi — bukan melalui `multiprocessing` Python, kerana permulaan pekerja tidak berfungsi di dalamnya aplikasi terbungkus
* **`CPU_PARALLEL`** menggunakan kolam utas (Python's `ThreadPoolExecutor`) — NumPy dan OpenCV melepaskan GIL, jadi utas sudah mencukupi
* Peranti Jetson dengan 8GB atau kurang RAM kongsi terus melangkau eksekutor sepenuhnya dan memproses secara berperingkat dalam proses yang sama
* Texture Aware pada GPU dengan VRAM kurang daripada 7GB juga berjalan secara berperingkat — model penapis hingar tidak dapat memuat lebih daripada sekali

Chloros tidak menggunakan sebarang rangka kerja teragih pihak ketiga (seperti Ray). Lihat [Dynamic Compute Adaptation](dynamic-compute-adaptation.md) untuk maklumat tentang bagaimana strategi dan bilangan pekerja dipilih.

***

## Pemprosesan Bersiri vs. Berpipa

### Mod Percuma (Bersiri)

Dalam versi percuma Chloros, imej diproses **satu pada satu masa**, secara bersiri melalui semua empat peringkat:

```

Image 1: [Detect] → [Calibrate] → [Process] → [Export]
                                                         Image 2: [Detect] → [Calibrate] → [Process] → [Export]
```

GUI memaparkan bar kemajuan yang disederhanakan dalam mod percuma; fasa bersiri dilaporkan sebagai **Pengesanan Sasaran**dan kemudian**Pemprosesan**.

### Mod Chloros+ (Berpam-pam)

Dengan lesen Chloros+, keempat-empat thread beroperasi **serentak** pada imej yang berbeza:

```

Thread 1: [Image 1] [Image 2] [Image 3] [Image 4] ...
Thread 2:           [Image 1] [Image 2] [Image 3] ...
Thread 3:                     [Image 1] [Image 2] ...
Thread 4:                               [Image 1] ...
```

Bar kemajuan GUI memaparkan empat peringkat; tunjukkan tetikus di atasnya untuk melihat kemajuan bagi setiap benang. Dalam CLI, empat peringkat yang sama disiarkan secara langsung sebagai **Mengesani, Menganalisis, Memproses, Mengeksport**.

{% hint style="info" %}
**Satu label, dua nama.** CLI memanggil peringkat 3 sebagai _Processing_. Suapan kemajuan mod premium backend — yang dipaparkan oleh bar kemajuan GUI — menamakan peringkat yang sama sebagai _Calibrating_. Kedua-duanya adalah benang yang sama melakukan kerja yang sama (Benang 3: debayer, pembetulan, indeks).
{% endhint %}

{% hint style="success" %}
**Pemprosesan berjalur dengan**Chloros+** boleh 3-5 kali lebih pantas daripada pemprosesan bersiri, bergantung pada perkakasan dan saiz set data anda. Peningkatan kelajuan adalah paling ketara pada sistem dengan GPU dan SSD yang pantas.
{% endhint %}

***

## Kemajuan Eksport Reben 4

Reben eksport mempunyai penjejakan kemajuan tersendiri, yang boleh anda semak secara berasingan:**CLI:**

```bash
chloros-cli export-status
```

**SDK:**

```python
status = chloros.get_status()
print(f"Export: {status['export']['percent']}% - Phase: {status['export']['phase']}")
```

Pemprosesan selesai apabila Thread 4 mencapai 100%.

{% hint style="info" %}
**Pelaksanaan yang tidak menulis sebarang imej adalah kegagalan.**Sekiranya berjaya, `chloros-cli process` melaporkan berapa banyak produk imej yang ditulis (`Image products written: N`). Jika produk telah diminta dan**tiada satu pun**yang ditulis — hanya `project.json` dan `calibration_data.json` — CLI mencetak `Processing finished but wrote no image products.` dan**keluar dengan nilai tidak sifar**, menamakan folder projek dan punca biasa (folder input tidak diiktiraf sebagai tangkapan — semak susun atur dan `--input-level` — atau setiap produk yang diminta tidak terpakai untuk kamera tersebut). Skrip boleh bergantung pada kod keluar.
{% endhint %}

***

## Hubungan dengan Penyesuaian Komputasi Dinamik

[Penyesuaian Komputasi Dinamik](dynamic-compute-adaptation.md) terutamanya menjejaskan **Thread 3 (Pemprosesan)**:

* **`GPU_PARALLEL`**: Thread 3 menjalankan beberapa imej melalui GPU secara serentak menggunakan saluran `fused_gpu`
* **`GPU_SINGLE`**: Thread 3 menyusun akses GPU secara bersiri dengan semafor sementara proses pekerja melakukan tumpang tindih I/O, menggunakan `fused_gpu` atau saluran `tiled_gpu` yang cekap dari segi memori
* **`CPU_PARALLEL`**: Thread 3 menggunakan pemprosesan berasaskan CPU dengan keparalelan berbilang-benang

Peruntukan memori GPU Thread 3 juga meningkat apabila Thread 1 dan 2 selesai — lihat [Peruntukan Memori GPU Dinamik](dynamic-compute-adaptation.md#dynamic-gpu-memory-allocation).

***

## Langkah Seterusnya

* [Penyesuaian Komputasi Dinamik](dynamic-compute-adaptation.md) — Bagaimana Chloros memilih strategi optimum untuk perkakasan anda
* [Panduan NVIDIA Jetson](../linux/nvidia-jetson-guide.md) — Tingkah laku saluran yang khusus untuk platform pada Jetson
* [Pemantauan Pemprosesan](../processing-images-gui/monitoring-the-processing.md) — Pemantauan kemajuan GUI
* [Rujukan CLI](../reference/cli-reference.md) — `process`, `export-status`, kod keluar, dan susun atur keluaran
