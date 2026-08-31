# Saluran Pemprosesan

Chloros1.2.0 menggunakan saluran pemprosesan 4-benang yang berfungsi seperti barisan pemasangan berperingkat. Setiap benang mengendalikan fasa aliran kerja yang berbeza, jadi beberapa imej boleh berada dalam proses pada peringkat yang berbeza serentak.

<figure><img src="../.gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>

***

## Seni Bina Saluran

```

Images In → [Thread 1: Detection] → [Thread 2: Calibration] → [Thread 3: Processing] → [Thread 4: Export] → Files Out
```

Setiap imej mengalir melalui keempat-empat thread mengikut urutan. Dengan pemprosesan berbilang-thread Chloros+, beberapa imej menempati thread yang berbeza serentak — manakala Thread 3 memproses satu imej, Thread 1 boleh mengesan imej seterusnya, Thread 2 mengkalibrasi satu lagi, dan Thread 4 menulis imej yang siap ke cakera.

Kemajuan dilaporkan bagi setiap thread dan disalurkan melalui Server-Sent Events (backend menerbitkannya di `/api/events`). Dalam paparan kemajuan langsung CLI, empat peringkat dilabelkan sebagai **Mengesahkan, Menganalisis, Memproses, Mengeksport**.***

## Butiran Benang

### Benang 1: Pengesanan

**Tujuan**: Memuat imej dan mengesan sasaran penentukuran.

* Membaca fail imej dari cakera — pasangan Survey3 `.raw`+`.jpg`, perekodan LATTICE `.tif`/`.tiff`, dan `.dng`
* Mengasingkan metadata EXIF (GPS, model kamera, cap masa, pendedahan)
* Mengesan sasaran penentukuran: geometri sasaran bertanda ArUco untuk rakaman LATTICE, dan penderia panel klasik untuk foto sasaran penentukuran Survey3
* Keluaran: data imej + metadata + keputusan pengesanan sasaran

Secara utama, benang ini terikat pada I/O dan CPU.

### Benang 2: Kalibrasi

**Tujuan**: Mengira parameter kalibrasi daripada sasaran yang dikesan.

* Mengira pekali penentukuran pantulan daripada imej sasaran
* Mengira parameter pembetulan vignette
* Menentukan lengkung penentukuran bagi setiap jalur
* Keluaran: parameter penentukuran untuk setiap imej

Benang pengiraan yang terikat pada CPU. Benang 3 menungguinya apabila penentukuran pantulan diaktifkan, supaya pekalinya sedia sebelum mana-mana imej diproses.

### Benang 3: Pemprosesan (GPU)

**Tujuan**: Mengaplikasikan pembetulan dan mengira indeks vegetasi.**Benang ini adalah yang paling intensif dari segi pengkomputeran.*** **Debayering**: menukar data Bayer RAW kepada imej berbilang saluran
  * Standard (Pantas, Kualiti Sederhana) — lalai, `--debayer standard`
  * Sedar Tekstur (Lambat, Kualiti Tertinggi) — hanya untukChloros+, `--debayer texture-aware`, menggunakan model penyahbisin UI/ML
  * Rakaman LATTICE mono (M3M) adalah jalur tunggal: langkah demosaik dan imbangan putih akan dilangkau untuknya (dengan mesej log satu baris), manakala sebarang imej M3C/Bayer dalam larian yang sama masih akan menerima langkah-langkah tersebut
* **Pembetulan vignet**: menerapkan pembetulan vignet lensa pada seluruh imej
* **Kalibrasi reflektansi**: menerapkan koefisien kalibrasi untuk menukarkan kepada nilai reflektansi
* **Pengiraan indeks**: mengira indeks vegetasi (NDVI, NDRE, GNDVI, …)
* Keluaran: data imej yang diproses siap untuk dieksport

Benang ini paling banyak mendapat manfaat daripada pecutan GPU, dan ia adalah benang yang dilaras oleh [Dynamic Compute Adaptation](dynamic-compute-adaptation.md).

### Benang 4: Eksport

**Tujuan**: Menulis imej yang diproses ke cakera.

* Menulis fail output dalam format yang dipilih — `TIFF (16-bit)`, `TIFF (32-bit, Percent)`, `PNG (8-bit)`, `JPG (8-bit)`
* Menanam metadata dalam fail output (GPS, cop masa, parameter pemprosesan)
* Mengatur output di bawah folder projek sebagai `<camera>/<format>/<Product>_Images/` — contohnya `LATT-M3M-L41-F550/tiff16/Reflectance_Calibrated_Images/`. **Fail yang dieksport mengekalkan nama fail sumber; folder mengenal pasti produk.**
* Untuk tangkapan LATTICE, satu bingkai sumber boleh menghasilkan beberapa produk (Debayered, Pratonton, Radiance, Reflectance, Indeks), setiap satu dalam folder produknya sendiri
* Keluaran: fail akhir pada cakera

Benang ini terutamanya terikat pada I/O — storan SSD memperbaikinya dengan ketara.

***

## Di Sebalik Tabir: Pelaksana

Dalam Benang 3, kerja bagi setiap imej diparaelakkan dengan `concurrent.futures` piawai Python:

* **strategi GPU**(`GPU_SINGLE`, `GPU_PARALLEL`) menggunakan `ProcessPoolExecutor` dengan**spawn** kaedah start — setiap pekerja adalah proses berasingan dengan konteks CUDA tersendiri (`fork` akan mewarisi keadaan CUDA ibu bapa yang telah diinisialisasi dan merosakkannya)
* **`CPU_PARALLEL`** menggunakan `ThreadPoolExecutor` — NumPy dan OpenCV melepaskan GIL, jadi benang sudah mencukupi
* Peranti Jetson dengan 8GB atau kurang RAM kongsi sepenuhnya melangkau eksekutor dan memproses secara berperingkat dalam proses yang sama
* Texture Aware pada GPU dengan VRAM kurang daripada 7GB juga berjalan secara berperingkat — model penapis hingar tidak dapat memuat lebih daripada sekali

Chlorostidak menggunakan sebarang rangka kerja teragih pihak ketiga (seperti Ray). Lihat [Dynamic Compute Adaptation](dynamic-compute-adaptation.md) untuk maklumat tentang bagaimana strategi dan bilangan pekerja dipilih.

***

## Pemprosesan Bersiri vs. Berpipa

### Mod Percuma (Bersiri)

Dalam versi percuma Chloros, imej diproses **satu pada satu masa**, secara bersiri melalui semua empat peringkat:

```

Image 1: [Detect] → [Calibrate] → [Process] → [Export]
                                                         Image 2: [Detect] → [Calibrate] → [Process] → [Export]
```

GUI memaparkan bar kemajuan yang disederhanakan dalam mod percuma; fasa bersiri dilaporkan sebagai **Pengesanan Sasaran**dan kemudian**Pemprosesan**.

### Mod Chloros+ (Saluran)

Dengan lesen Chloros+, keempat-empat thread beroperasi **serentak** pada imej yang berbeza:

```

Thread 1: [Image 1] [Image 2] [Image 3] [Image 4] ...
Thread 2:           [Image 1] [Image 2] [Image 3] ...
Thread 3:                     [Image 1] [Image 2] ...
Thread 4:                               [Image 1] ...
```

Bar kemajuan GUI memaparkan empat peringkat; tunjukkan penunjuk tetikus di atasnya untuk melihat kemajuan bagi setiap benang. Dalam CLI, empat peringkat yang sama disiarkan secara langsung sebagai **Mengesani, Menganalisis, Memproses, Mengeksport**.

{% hint style="info" %}
**Satu label, dua nama.** CLI memanggil peringkat 3 sebagai _Processing_. Suapan kemajuan mod premium backend — yang dipaparkan oleh bar kemajuan GUI — menamakan peringkat yang sama sebagai _Calibrating_. Kedua-duanya adalah benang yang sama melakukan kerja yang sama (Benang 3: debayer, pembetulan, indeks).
{% endhint %}

{% hint style="success" %}
**Pemprosesan berpaip dengan Chloros+** boleh 3-5 kali lebih pantas daripada pemprosesan bersiri, bergantung pada perkakasan dan saiz set data anda. Peningkatan kelajuan adalah paling ketara pada sistem dengan GPU dan SSD yang pantas.
{% endhint %}

***

## Kemajuan Pengeksportan Benang 4

Benang pengeksportan mempunyai penjejakan kemajuan tersendiri, yang boleh anda semak secara berasingan:**CLI:**

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
**Pelaksanaan yang tidak menulis sebarang imej adalah satu kegagalan.**Sekiranya berjaya, `chloros-cli process` melaporkan berapa banyak produk imej yang telah ditulis (`Image products written: N`). Jika produk diminta dan**tiada satu pun**yang ditulis — hanya `project.json` dan `calibration_data.json` — CLI mencetak `Processing finished but wrote no image products.` dan**keluar dengan nilai bukan sifar**, menamakan folder projek dan punca biasa (folder input tidak diiktiraf sebagai tangkapan — semak susun atur dan `--input-level` — atau setiap produk yang diminta tidak terpakai untuk kamera tersebut). Skrip boleh bergantung pada kod keluar.
{% endhint %}

***

## Perkaitan dengan Penyesuaian Komputasi Dinamik

[Penyesuaian Komputasi Dinamik](dynamic-compute-adaptation.md) terutamanya menjejaskan **Thread 3 (Pemprosesan)**:

* **`GPU_PARALLEL`**: Thread 3 menjalankan beberapa imej melalui GPU serentak menggunakan saluran `fused_gpu`
* **`GPU_SINGLE`**: Thread 3 menyirialisasikan akses GPU dengan semafor sementara proses pekerja melakukan tumpang tindih I/O, menggunakan `fused_gpu` atau saluran `tiled_gpu` yang cekap dari segi memori
* **`CPU_PARALLEL`**: Thread 3 menggunakan pemprosesan berasaskan CPU dengan keparalelan berbilang-benang

Peruntukan memori GPU Benang 3 juga meningkat apabila Benang 1 dan 2 selesai — lihat [Peruntukan Memori GPU Dinamik](dynamic-compute-adaptation.md#dynamic-gpu-memory-allocation).

***

## Langkah Seterusnya

* [Penyesuaian Komputasi Dinamik](dynamic-compute-adaptation.md) — Bagaimana Chloros memilih strategi optimum untuk perkakasan anda
* [Panduan NVIDIA Jetson](../linux/nvidia-jetson-guide.md) — Tingkah laku saluran yang khusus untuk platform Jetson
* [Pemantauan Pemprosesan](../processing-images-gui/monitoring-the-processing.md) — Pemantauan kemajuan GUI
* [Rujukan CLI](../reference/cli-reference.md) — `process`, `export-status`, kod keluar, dan susun atur keluaran
