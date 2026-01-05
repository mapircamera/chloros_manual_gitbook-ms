# Kotak Pasir Indeks/LUT

Kotak Pasir Indeks/LUT ialah ruang kerja interaktif dalam Pemapar Imej Chloros yang membolehkan anda mencuba pengiraan indeks berbilang spektrum dan visualisasi warna dalam masa nyata. Alat berkuasa ini membantu anda menguji indeks yang berbeza, memperhalusi julat nilai dan membuat visualisasi sedia penerbitan tanpa memproses semula keseluruhan set data anda.

## Apakah itu Kotak Pasir Indeks/LUT?

### Tujuan

Kotak Pasir menyediakan:

* **Pengiraan indeks masa nyata** - Gunakan sebarang indeks tumbuh-tumbuhan serta-merta
* **Pelarasan LUT interaktif** - Perhaluskan kecerunan dan julat warna
* **Pengoptimuman aliran kerja** - Tentukan tetapan terbaik sebelum pemprosesan kelompok

### Kotak pasir lwn. Pemprosesan Projek

**Kotak Pasir Indeks/LUT (Interaktif):**

* Imej tunggal pada satu masa
* Maklum balas segera
* Eksperimen dan berulang
* Tiada perubahan kekal pada fail
* Sesuai untuk meneroka dan menguji

**Pemprosesan Projek (Batch):**

* Keseluruhan set data sekali gus
* Tetapan pra-konfigurasi
* Fail keluaran kekal
* Intensif masa
* Terbaik apabila tetapan dimuktamadkan

{% gaya petunjuk="berjaya" %}
**Aliran Kerja Terbaik**: Gunakan Kotak Pasir untuk mencuba dan mencari tetapan indeks dan LUT yang optimum, kemudian gunakan tetapan tersebut semasa Pemprosesan Projek untuk keseluruhan set data anda.
Petua {% %}

***

## Bekerja dengan Kotak Pasir Indeks/LUT

### Memahami Indeks Pra-Kira

Dalam Chloros, indeks boleh digunakan semasa pemprosesan projek. Untuk menentukan tetapan indeks dan LUT yang anda mahu gunakan untuk eksport, adalah paling mudah untuk menggunakan kotak pasir pemapar imej.

Kotak pasir membolehkan anda:

* **Gunakan indeks dan kecerunan warna (LUT) baharu** untuk menggambarkan data
* **Laraskan tetapan visualisasi** secara interaktif
* **Lihat** imej indeks yang telah dikira
* **Periksa** nilai piksel pada semua peringkat zum

### Membuka Kotak Pasir

Kotak Pasir Indeks/LUT diakses dalam **Pemapar Imej** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> tab bar sisi:

1. Klik imej dalam grid imej penyemak imbas fail, ia dibuka dalam tab **Pemapar Imej** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> tab
2. Klik **the Image Viewer** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> tab untuk membuka bar sisi pop-keluar kiri jika ia belum dibuka

### Memilih Imej untuk Menggunakan Indeks/LUT

Untuk bekerja dengan indeks dalam Pemapar Imej <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> kotak pasir:

1. **Buka imej** dari grid imej utama dengan mengklik padanya
2. Tab **Image Viewer** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> kemudiannya akan dibuka
3. Klik menu lungsur **Lapisan** (atas kanan pemapar)
4. Pilih lapisan daripada menu lungsur:
   * RAW (Pantulan)

### Menggunakan Indeks pada Imej

Sebaik sahaja imej adalah skrin penuh dan **Pemapar Imej** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> bar sisi tab dibuka:

1. Tandakan kotak Indeks di bahagian atas bar sisi
2. Pilih penapis kamera anda daripada menu lungsur kiri
3. Pilih formula indeks yang dikehendaki daripada menu lungsur kanan
4. Seret bulatan warna saluran penapis ke lokasi dalam formula indeks di bawah
5. Setelah formula sah, imej akan mengemas kini dan menunjukkan nilai indeks
6. Gerakkan kursor tetikus anda untuk melihat nilai di lokasi kursor
7. Zum masuk untuk melihat piksel individu dan nilai yang berkaitan dengannya

Setiap indeks mempunyai julat nilai dan makna tertentu:

#### Contoh NDVI

```

Formula: (NIR - Red) / (NIR + Red)

For Survey3W RGN camera:
NIR = 850nm band
Red = 661nm band

Result range: -1.0 to +1.0
Typical vegetation: 0.4 to 0.9
Stressed vegetation: 0.2 to 0.4
Bare soil: 0.0 to 0.2
Water: -0.1 to 0.1
```

Untuk dokumentasi formula indeks yang lengkap, lihat [Formula Indeks Berbilang Spektrum](../project-settings/multispectral-index-formulas.md).

***

## Bekerja dengan LUT (Jadual Carian)

### Apakah itu LUT?

**Jadual Carian Atas (LUT)** memetakan nilai indeks berangka kepada warna untuk visualisasi:

* **Input**: Nilai piksel indeks (cth., NDVI 0.65)
* **Output**: Warna RGB (cth., hijau terang)
* **Tujuan**: Jadikan corak lebih mudah dilihat dan ditafsir**Skala kelabu lwn. Warna LUT:**

* Skala kelabu: Saintifik dan neutral, menunjukkan data mentah
* Warna LUT: Intuitif dan berkesan, menyerlahkan corak dan perbezaan

{% gaya pembayang="berjaya" %}
**Kuasa Visualisasi**: Menggunakan LUT warna pada imej indeks skala kelabu menjadikannya secara dramatik lebih mudah untuk mengenal pasti corak, anomali dan kawasan yang diminati sepintas lalu.
Petua {% %}

### Menggunakan LUT pada Imej Indeks

Sebaik sahaja anda mempunyai imej indeks yang ditunjukkan

1. Klik butang <img src="../.gitbook/assets/image (1) (1).png" alt="" data-size="line"> "+Tambah LUT"
2. Pilih kecerunan warna
3. Laraskan titik akhir min/maks keratan
4. Laraskan Mod Keratan
5. Tandai kotak Indeks dalam **Pemapar Imej** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> bar sisi tab untuk menggunakan LUT

### Memilih Kecerunan Warna

**Memilih kecerunan:**

1. Dalam panel LUT, cari**bar kecerunan berwarna**

2. Tuding tetikus anda di atasnya untuk melihat pratetap kecerunan yang tersedia
3. Pilih kecerunan yang dikehendaki
4. Imej **kemas kini serta-merta** dengan warna baharu apabila kotak Indeks ditandakan

{% gaya petunjuk="berjaya" %}
**Amalan Terbaik**: Untuk indeks tumbuh-tumbuhan seperti NDVI, kecerunan Red-Yellow-Green adalah paling intuitif kerana ia sejajar dengan persatuan warna semula jadi (hijau=sihat, kuning=sederhana, merah=tekanan).
Petua {% %}

### Melaraskan Kelas Warna

**Kawalan Kelas**menentukan bilangan langkah warna diskret yang muncul dalam kecerunan anda:**Pilihan kiraan kelas:*** **2-5 kelas**: Kategori yang sangat luas, zon yang berbeza
* **6-10 kelas**: Seimbang, bagus untuk pengelasan
* **11-20 kelas**: Kecerunan licin, penampilan berterusan
* **20+ kelas**: Hampir berterusan, kelancaran maksimum**Cara melaraskan:**

1. Dalam panel LUT, cari**petak swatch warna di bawah bar kecerunan**

2. Laraskan bilangan kelas dengan menambah dengan butang +
3. Keluarkan bilangan kelas dengan mengklik dua kali pada swatch warna
4. Kemas kini kecerunan **dalam masa nyata** pada imej**Kesan pada visualisasi:*** **Kurang kelas** (3-5): Mencipta zon berbeza, klasifikasi dipermudahkan, lebih mudah untuk membezakan kategori
* **Kelas sederhana** (6-10): Pendekatan seimbang, bagus untuk kebanyakan aplikasi
* **Lebih banyak kelas** (15-20): Peralihan lancar, variasi terperinci, penampilan fotografi**Bila hendak digunakan:*** **Beberapa kelas (3-5)**: Slaid pembentangan, peta klasifikasi, laporan ringkas
* **Kelas sederhana (6-10)**: Analisis umum, perincian seimbang, laporan standard
* **Banyak kelas (15-20)**: Analisis saintifik, pemeriksaan terperinci, output kualiti penerbitan

### Julat Nilai Penalaan Halus

**kawalan julat nilai**menentukan nilai indeks yang memetakan warna dalam kecerunan anda:**Kawalan julat dalam panel LUT:*** **Nilai minimum**: Sempadan bawah skala warna
* **Nilai maksimum**: Sempadan atas skala warna
* **Nilai perantaraan**: Diedarkan secara automatik antara min dan maks (berdasarkan kiraan kelas)

#### Melaraskan Nilai Min/Maks

**Untuk melaraskan julat nilai:**

1. Dalam panel LUT, cari medan input**Min Value**dan**Max Value**

2. Klik medan**Nilai Min**

3. Taip nilai minimum yang dikehendaki (cth., `0.2`)
4. Tekan **Enter** atau klik di luar medan
5. Ulang untuk medan **Nilai Maks** (cth., `0.9`)
6. Visualisasi **kemas kini serta-merta**{% gaya pembayang="info" %}**Penskalaan Auto**: Apabila anda mula-mula menggunakan LUT, Chloros secara automatik menetapkan min/maks kepada julat data sebenar dalam imej. Anda kemudiannya boleh mengecilkan julat ini untuk memfokuskan pada julat nilai minat tertentu.
Petua {% %}

**Contoh pelarasan julat NDVI:*** **Julat penuh**: `-1.0` hingga `1.0` (tunjukkan semua nilai yang mungkin)
* **Tertumpu pada tumbuh-tumbuhan**: `0.2` hingga `0.9` (tidak termasuk tanah kosong dan air)
* **Tumbuhan yang sihat sahaja**: `0.5` hingga `0.9` (serlahkan hanya tumbuhan yang cergas)
* **Pengesanan tekanan**: `0.2` hingga `0.5` (menekankan kawasan masalah)
* **Julat tersuai**: Laraskan berdasarkan nilai piksel yang anda perhatikan**Mengapa melaraskan julat?*** **Tingkatkan kontras** dalam kawasan minat anda
* **Kecualikan nilai yang tidak berkaitan** (cth., badan air, tanah kosong)
* **Standarkan visualisasi** merentas berbilang imej atau tarikh
* **Tekankan perbezaan halus** dalam julat nilai yang sempit

### Menggunting Nilai Luar Julat

Apabila nilai piksel berada di luar julat min/maks yang ditentukan, anda boleh mengawal cara ia dipaparkan menggunakan **mod keratan**.

#### **Pilihan mod keratan yang tersedia:**

#### 1. Minimum dan Maksimum

* Piksel **di bawah minimum**→ paparan menggunakan**warna pertama** dalam kecerunan (cth., merah)
* Piksel **di atas maksimum**→ paparan menggunakan**warna terakhir** dalam kecerunan (cth., hijau)
* **Kes penggunaan**: Tekankan keterlaluan, tunjukkan julat data penuh dengan warna tepu pada had
* **Contoh**: Nilai NDVI di bawah 0.2 semuanya kelihatan merah, nilai di atas 0.9 semuanya kelihatan hijau

#### 2. Latar Belakang Lutsinar

* Piksel **di luar julat**menjadi**lutsinar sepenuhnya*** Hanya piksel **dalam julat** menunjukkan kecerunan warna
* **Kes penggunaan**: Tindanan GIS, mengasingkan julat nilai tertentu, menyerlahkan kawasan yang menarik sahaja
* **Contoh**: Tunjukkan hanya NDVI 0.4-0.7 dalam warna, semua yang lain lutsinar

{% gaya petunjuk="amaran" %}
**Had Ketelusan**: Piksel lutsinar akan muncul sebagai warna latar belakang dalam pemapar. Apabila dieksport semasa pemprosesan, ketelusan dikekalkan dalam format PNG tetapi bukan dalam JPG.
Petua {% %}

#### 3. Latar Belakang Indeks

* Piksel **julat luar**dipaparkan dalam**skala kelabu** (menunjukkan nilai indeks mentah)
* Piksel **dalam julat**menunjukkan**kecerunan warna*** **Kes penggunaan**: Serlahkan halus, kekalkan konteks sambil menekankan bidang yang diminati
* **Contoh**: Tumbuhan bertekanan yang diserlahkan warna (NDVI 0.3-0.5) sambil menunjukkan kawasan yang sihat dalam warna kelabu

#### 4. Latar Belakang Asal

* Piksel **julat luar**memaparkan**imej berbilang spektrum asal*** Piksel **dalam julat**menunjukkan**kecerunan warna*** **Kes penggunaan**: Paling intuitif - menggabungkan konteks imej semula jadi dengan tindanan warna analitik
* **Contoh**: Lihat rupa medan/pangkas sebenar dengan kawasan tegasan berkod warna bertindih

### Memilih Mod Keratan yang Betul

| Mod Keratan | Terbaik Untuk | Gaya Visualisasi |
| -------------------------- | ------------------------------------------ | ---------------------------- |
| **Minimum dan Maksimum** | Paparan data penuh, analisis saintifik | Semua piksel berwarna |
| **Latar Belakang Lutsinar** | Tindanan GIS, mengasingkan julat tertentu | Warna pada julat, kosong melebihi |
| **Latar Belakang Indeks** | Penekanan halus, mengekalkan konteks data | Warna pada julat, kelabu melebihi |
| **Latar Belakang Asal** | Laporan, pembentangan, analisis intuitif | Warna pada julat, foto melebihi |

### Mencipta Warna LUT Tersuai

Untuk kawalan penuh ke atas visualisasi anda, anda boleh mencipta **kecerunan warna tersuai** dengan mengedit hentian warna individu.**Untuk mencipta kecerunan tersuai:**

1. Dalam panel LUT, cari**bar pratonton kecerunan**

2. Cari**petak swatch warna** di bawah kecerunan
3. **Klik hentian warna** untuk memilihnya
4. **pemilih warna** dibuka
5. Pilih warna baharu menggunakan:
   * **Roda warna**: Pemilihan warna visual
   * **Peluncur RGB/HSV**: Kawalan warna yang tepat
   * **Entri kod Hex**: Spesifikasi warna yang tepat (cth., `#FF0000` untuk merah)
6. Klik pada pemilih warna **untuk menggunakan warna baharu**

7. Kecerunan**kemas kini serta-merta** pada imej**Menambah atau mengalih keluar warna berhenti:*** **Tambah hentian**: Klik ikon + untuk menambah swatch baharu di penghujung
* **Alih keluar hentian**: Klik dua kali pada segi empat sama warna untuk mengeluarkan swatch**Strategi penyesuaian:*** **Kecerunan terbalik**: Terbalikkan susunan warna untuk membalikkan maksud (cth., hijau=rendah, merah=tinggi)
* **Warna jenama**: Padankan palet warna organisasi anda untuk laporan
* **Mesra rabun warna**: Gunakan gabungan oren-biru atau ungu-kuning
* **Pengoptimuman cetakan**: Pilih warna yang berfungsi dalam kedua-dua percetakan warna dan skala kelabu
* **Berbilang ambang**: Gunakan warna yang berbeza pada ambang nilai tertentu untuk pengelasan

{% gaya petunjuk="info" %}
**Menyimpan Kecerunan Tersuai**: Kecerunan tersuai boleh disimpan dan digunakan semula. Klik ikon simpan dalam panel LUT untuk mengekalkan skema warna tersuai anda untuk kegunaan masa hadapan.
Petua {% %}

***

## Aliran Kerja Interaktif

### Kemas Kini Masa Nyata

Semua pelarasan LUT dalam kotak pasir mengemas kini imej **segera dan interaktif**:

* **Tukar lapisan** → Imej berubah serta-merta
* **Pilih kecerunan** → Kemas kini warna serta-merta
* **Laraskan julat nilai** → Kontras perubahan dalam masa nyata
* **Tukar kelas** → Kemas kini kelancaran kecerunan serta-merta
* **Ubah suai keratan** → Paparan latar belakang berubah serta-merta
* **Edit warna** → Kecerunan tersuai digunakan serta-merta**Tiada butang "Guna" diperlukan** - semua perubahan adalah secara langsung dan interaktif!

{% gaya petunjuk="berjaya" %}
**Maklum Balas Langsung**: Maklum balas visual segera membolehkan anda bereksperimen dengan pantas dengan tetapan berbeza sehingga anda menemui visualisasi optimum untuk keperluan analisis anda.
Petua {% %}

### Aliran Kerja Penapisan Berulang

**Aliran kerja pengoptimuman LUT biasa:**

1.**Pilih lapisan indeks** (cth., RAW (Pantulan))
2. **Gunakan indeks** - Pilih penapis kamera dan formula indeks, seret bulatan berwarna ke lokasi yang sesuai dalam formula indeks
3. **Gunakan kecerunan LUT** - Mulakan dengan pratetap Red-Yellow-Green
4. **Periksa nilai piksel** - Gerakkan kursor ke sekeliling, julat nilai nota
5. **Laraskan min/maks** - Sempit untuk memfokus pada tumbuh-tumbuhan (cth., 0.2 hingga 0.9)
6. **Pilih keratan** - Cuba "Latar Belakang Asal" untuk konteks
7. **Perhalusi warna** - Sesuaikan kecerunan jika diperlukan untuk penekanan khusus
8. **Tamatkan tetapan**- Tetapan dokumen dan salin ke Tetapan Projek untuk pemprosesan eksport

### Pemeriksaan Nilai Piksel

Memahami nilai piksel sebenar adalah penting untuk menetapkan julat LUT yang berkesan:**Cara memeriksa nilai:**

1. Nilai piksel menunjukkan apabila imej mempunyai sama ada Indeks, atau kedua-dua Indeks dan LUT**kotak ditandai**.
2. **Alihkan kursor anda** ke atas kawasan imej yang berbeza
3. **Perhatikan nilai piksel** yang dipaparkan dalam legenda semasa anda menuding
4. Zum masuk untuk melihat piksel individu yang diserlahkan dengan nilai terapung
5. **Ambil nota** julat nilai untuk ciri yang berbeza:
   * **Tumbuhan yang sihat**: cth., NDVI 0.55-0.85
   * **Tumbuhan tertekan**: cth., NDVI 0.30-0.50
   * **Tanah kosong**: cth., NDVI 0.05-0.25
   * **Air** (jika ada): cth., NDVI -0.05 hingga 0.10**Menggunakan nilai piksel untuk menetapkan julat LUT:**Selepas memeriksa nilai piksel, laraskan LUT min/maks anda dengan sewajarnya:**Contoh senario:*** **Pemerhatian**: Nilai tanah = 0.05-0.25, Tertekan = 0.25-0.50, Sihat = 0.50-0.85
* **Matlamat**: Visualisasikan kesihatan tumbuhan sahaja (tidak termasuk tanah)
* **Tetapan LUT**: Min = `0.25`, Maks = `0.85`
* **Keratan**: "Latar Belakang Asal" untuk melihat tanah dalam warna semula jadi
* **Hasil**: Kecerunan warna hanya digunakan pada tumbuh-tumbuhan, tanah ditunjukkan sebagai imej asal

{% gaya pembayang="info" %}
**Julat Dinamik**: Tanaman, musim dan peringkat pertumbuhan yang berbeza akan mempunyai julat nilai yang berbeza. Sentiasa periksa nilai piksel dalam set data khusus anda sebelum menetapkan julat LUT.
Petua {% %}

***

## Indeks Tersuai (Chloros+)

### Mencipta Formula Indeks Tersuai

{% gaya petunjuk="info" %}
**Tempat Buat**: Indeks tersuai boleh dikonfigurasikan dalam**Tetapan Projek** sebelum diproses, serta dalam bar sisi kotak pasir Pemapar Imej.
Petua {% %}

**Untuk membuat indeks tersuai:**

1.**Buka Tetapan Projek** (sebelum diproses) atau bar sisi kotak pasir Pemapar Imej
2. Navigasi ke **Indeks formula dropdown**

3. Cari pilihan**"Tersuai"** (mesti dilog masuk dengan lesen Chloros+)
4. **Tentukan formula anda** menggunakan pembolehubah jalur:
   * Nama jalur: `NIR`, `Red`, `Green`, `Blue`, `RedEdge`, dsb.
   * Operator: `+`, `-`, `*`, `/`, `^` (eksponen)
   * Fungsi: `sqrt()`, `abs()`, dsb. (jika disokong)
   * Tanda kurung: `()` untuk susunan operasi
5. **Namakan indeks anda** (cth., "MyIndex" atau "CustomNDVI")
6. **Simpan konfigurasi**

**Contoh formula tersuai:**

```

Modified NDVI with offset:
(NIR - Red) / (NIR + Red + 0.5)

Simple ratio:
NIR / Red

Complex multi-band:
(NIR - Red) / (NIR + Red - Blue)

Exponential index:
(NIR / Red) ^ 2
```

{% gaya petunjuk="amaran" %}
**Pengesahan Formula**: Pastikan formula anda menggunakan jalur yang tersedia dalam kamera anda. Contohnya, RedEdge hanya tersedia pada kamera dengan penapis RedEdge.
Petua {% %}

***

## Langkah Seterusnya

Kini setelah anda memahami Kotak Pasir Indeks/LUT:

* **Gunakan pada pemprosesan**: Gunakan tetapan yang ditemui dalam [Tetapan Projek](../project-settings/project-settings.md)
* **Proses kelompok**: Gunakan indeks yang dioptimumkan pada set data penuh
* **Ketahui lebih lanjut**: Baca [Formula Indeks Berbilang Spektrum](../project-settings/multispectral-index-formulas.md)

Dokumentasi berkaitan:

* [**Lapisan Imej**](image-layers.md) - Pengurusan lapisan dan visualisasi
* [**Membuka Skrin Penuh Imej**](opening-an-image-full-screen.md) - Asas Pemapar Imej
* [**Memproses Imej (GUI)**](../processing-images-gui/adding-files-to-a-project.md) - Aliran kerja pemprosesan penuh