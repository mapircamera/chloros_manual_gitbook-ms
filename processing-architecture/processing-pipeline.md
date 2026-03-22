# Memproses Saluran Paip

Chloros 1.1.0 menggunakan saluran paip pemprosesan 4-benang yang beroperasi sebagai barisan pemasangan berperingkat. Setiap utas mengendalikan fasa aliran kerja pemprosesan yang berbeza, membenarkan berbilang imej diproses serentak pada peringkat yang berbeza.

***

## Senibina Talian Paip

```

Images In → [Thread 1: Detection] → [Thread 2: Calibration] → [Thread 3: Processing] → [Thread 4: Export] → Files Out
```

Setiap imej mengalir melalui keempat-empat utas mengikut urutan. Dengan pemprosesan berbilang benang Chloros+, berbilang imej boleh berada dalam utas yang berbeza serentak — manakala Thread 3 memproses satu imej, Thread 1 boleh mengesan imej seterusnya, Thread 2 boleh menentukur yang lain, dan Thread 4 boleh menulis imej yang diproses sebelum ini ke cakera.

***

## Butiran Benang

### Benang 1: Pengesanan

**Tujuan**: Muatkan imej dan mengesan sasaran penentukuran.

* Membaca fail imej dari cakera (RAW, JPG)
* Mengekstrak metadata EXIF ​​(GPS, model kamera, cap masa, pendedahan)
* Mengesan sasaran penentukuran ArUco dalam imej sasaran yang ditanda
* Output: data imej + metadata + hasil pengesanan sasaran

Ini terutamanya I/O dan benang terikat CPU.

### Benang 2: Penentukuran

**Tujuan**: Kira parameter penentukuran daripada sasaran yang dikesan.

* Mengira pekali penentukuran pantulan daripada imej sasaran
* Mengira parameter pembetulan vignet
* Menentukan lengkung penentukuran setiap jalur
* Output: parameter penentukuran untuk setiap imej

Ini ialah benang pengiraan terikat CPU.

### Benang 3: Pemprosesan (GPU)

**Tujuan**: Gunakan pembetulan dan kira indeks tumbuh-tumbuhan.**Ini ialah urutan yang paling intensif pengiraan.*** **Debayering**: Menukar data corak RAW Bayer kepada imej berbilang saluran
  * Standard (Pantas, Kualiti Sederhana) — lalai
  * Sedar Tekstur (Lambat, Kualiti Tertinggi) — Chloros+ sahaja, menggunakan AI/ML denoising
* **Pembetulan vignet**: Menggunakan pembetulan vignet kanta merentas imej
* **Penentukuran pantulan**: Menggunakan pekali penentukuran untuk menukar kepada nilai pemantulan
* **Pengiraan indeks**: Mengira indeks tumbuh-tumbuhan (NDVI, NDRE, GNDVI, dsb.)
* Output: data imej yang diproses sedia untuk dieksport

Benang ini mendapat manfaat paling banyak daripada pecutan GPU. Sistem [Dynamic Compute Adaptation](dynamic-compute-adaptation.md) mengoptimumkan gelagat urutan ini terutamanya.

### Benang 4: Eksport

**Tujuan**: Tulis imej yang diproses ke cakera.

* Menulis fail output dalam format yang dipilih (TIFF 16-bit, TIFF 32-bit %, PNG, JPG)
* Benamkan metadata EXIF ​​dalam fail output (GPS, cap masa, parameter pemprosesan)
* Mengatur output ke dalam subfolder model kamera
* Output: fail akhir pada cakera

Ini terutamanya utas terikat I/O. Storan SSD meningkatkan prestasi Thread 4 dengan ketara.

***

## Pemprosesan Berurutan lwn. Berpaip

### Mod Percuma (Berurutan)

Dalam versi percuma Chloros, imej diproses **satu demi satu**, secara berurutan melalui keempat-empat peringkat:

```

Image 1: [Detect] → [Calibrate] → [Process] → [Export]
                                                         Image 2: [Detect] → [Calibrate] → [Process] → [Export]
```

Bar kemajuan GUI menunjukkan 2 peringkat: Pengesanan Sasaran dan Pemprosesan.

### Mod Chloros+ (Bersambung paip)

Dengan lesen Chloros+, keempat-empat utas beroperasi **secara serentak** pada imej yang berbeza:

```

Thread 1: [Image 1] [Image 2] [Image 3] [Image 4] ...
Thread 2:           [Image 1] [Image 2] [Image 3] ...
Thread 3:                     [Image 1] [Image 2] ...
Thread 4:                               [Image 1] ...
```

Bar kemajuan GUI menunjukkan 4 peringkat: Mengesan, Menganalisis, Menentukur, Mengeksport. Tuding pada bar kemajuan untuk melihat kemajuan setiap utas.

{% hint style="success" %}
**Pemprosesan berpaip dengan Chloros+** boleh 3-5x lebih pantas daripada pemprosesan berjujukan, bergantung pada perkakasan dan saiz set data anda. Kelajuan paling hebat pada sistem dengan GPU dan SSD yang pantas.
{% endhint %}

***

## Kemajuan Eksport Thread 4

Dalam Chloros 1.1.0, utas eksport (Thread 4) mempunyai penjejakan kemajuan khususnya sendiri. Anda boleh memantau kemajuan eksport secara berasingan:**CLI:**
```bash
chloros-cli export-status
```

**SDK:**
```python
status = chloros.get_status()
print(f"Export: {status['export']['percent']}% - Phase: {status['export']['phase']}")
```

Pemprosesan selesai apabila Thread 4 mencapai 100%.

***

## Hubungan dengan Penyesuaian Pengiraan Dinamik

Sistem [Penyesuaian Pengiraan Dinamik](dynamic-compute-adaptation.md) terutamanya mempengaruhi **Benang 3 (Pemprosesan)**:

* Strategi **`GPU_PARALLEL`**: Thread 3 menjalankan berbilang imej melalui GPU secara serentak menggunakan saluran paip `fused_gpu`
* Strategi **`GPU_SINGLE`**: Thread 3 memproses satu imej pada satu masa menggunakan saluran paip `tiled_gpu` yang cekap memori
* Strategi **`CPU_PARALLEL`**: Thread 3 menggunakan pemprosesan berasaskan CPU dengan selari berbilang benang

Peruntukan memori GPU Thread 3 juga berubah secara dinamik apabila Thread 1 dan 2 selesai — lihat [Peruntukan Memori GPU Dinamik](dynamic-compute-adaptation.md#dynamic-gpu-memory-allocation).

***

## Langkah Seterusnya

* [Penyesuaian Pengiraan Dinamik](dynamic-compute-adaptation.md) — Cara Chloros memilih strategi optimum untuk perkakasan anda
* [Panduan NVIDIA Jetson](../linux/nvidia-jetson-guide.md) — Gelagat saluran paip khusus platform pada Jetson
* [Memantau Pemprosesan](../processing-images-gui/monitoring-the-processing.md) — Pemantauan kemajuan GUI