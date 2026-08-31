# Memilih Imej Sasaran

Menandakan imej yang mengandungi sasaran penentukuran memberitahu Chloros dengan tepat di mana hendak mencarinya. Apabila sekurang-kurangnya satu imej ditandakan dalam lajur Sasaran, Chloros akan mengimbas **hanya imej yang ditandakan** — jadi menandakan sasaran adalah cara untuk mempercepat pemprosesan dan mengelakkan imej tinjauan daripada disalah anggap sebagai sasaran.

<figure><img src="../.gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>

## Mengapa Menandakan Imej Sasaran?

### Menandakan Mengawal Imbasan

Apabila anda menandakan lajur Sasaran untuk imej tertentu:

* Chloros mengimbas hanya imej yang ditandakan untuk mencari sasaran
* Pengesanan sasaran selesai dengan lebih pantas
* Imej tinjauan tidak akan menghasilkan pengesanan sasaran palsu

Jika **tiada** imej dipilih, Chloros akan kembali mengimbas setiap imej dalam projek:

* Algoritma pengesanan sasaran dijalankan pada setiap imej
* Ratusan atau ribuan imej diperiksa secara tidak perlu
* Pemprosesan mengambil masa yang lebih lama, terutamanya untuk set data besar

{% hint style="success" %}
**Peningkatan Kelajuan**: Menandakan 2-3 imej sasaran dalam set data 500 imej boleh mengurangkan masa pengesanan sasaran daripada 30+ minit kepada kurang daripada 1 minit.
{% endhint %}

***

## Cara Menandakan Imej Sasaran

### Langkah 1: Kenal pasti Imej Sasaran Anda

Semak imej yang telah anda import dalam Pelayar Fail dan kenal pasti imej mana yang mengandungi sasaran penentukuran.

**Senario biasa:*** **Sasaran pra-rakaman**: Dirakam sebelum memulakan sesi
* **Sasaran pasca-perekaman**: Dirakam selepas menamatkan sesi
* **Sasaran di lapangan**: Sasaran yang diletakkan di dalam kawasan perekaman
* **Beberapa sasaran**: 2-3 imej sasaran setiap sesi (disyorkan)

### Langkah 2: Semak Ruang Target <img src="../.gitbook/assets/image (33).png" alt="" data-size="original">

Untuk setiap imej yang mengandungi target penentukuran:

1. Lokasikan imej dalam jadual Pelayar Fail
2. Cari lajur **Target** (lajur paling kanan)
3. Klik kotak semak dalam lajur Target untuk imej tersebut
4. Ulang untuk semua imej yang mengandungi sasaran

### Langkah 3: Semak Pilihan Anda

Sebelum memproses, semak semula:

* [ ] Semua imej dengan sasaran penentukuran telah disemak
* [ ] Tiada imej bukan sasaran disemak secara tidak sengaja
* [ ] Sasaran jelas kelihatan dalam imej yang disemak

***

## LATTICE: Sasaran Adalah Pilihan Apabila DAQ Merakam

Untuk kamera multispektral LATTICE, sasaran penentukuran dalam bingkai adalah **salah satu daripada dua** rujukan pantulan yang mungkin:

* **Sasaran dalam bingkai**: apabila imej sasaran yang ditandakan melepasi pintu kualiti (QA) Chloros, sasaran itu menjadi**rujukan pantulan mutlak** untuk imej di sekelilingnya.
* **DAQ downwelling**: apabila tiada sasaran (atau QA gagal), Chloros mengira pantulan daripada sinaran ke bawah penderia cahaya DAQ sebaliknya (ρ = π·L/E). Jika rakaman `.daq` atau DAQ-M `.csv` merangkumi tangkapan anda, anda akan mendapat pantulan kalibrasi**tanpa sebarang imej sasaran langsung**.

Tingkah laku automatik ini adalah lalai. Dalam CLI / SDK ia sepadan dengan `--reflectance-source auto`; anda juga boleh memaksa `target` (ketat — tiada penggantian DAQ) atau `daq` (otoritatif-DAQ). Rujuk [Rujukan CLI](../reference/cli-reference.md#per-product-export-toggles-lattice-multispectral).

Geometri sasaran LATTICE: selain pengesanan panel klasik yang digunakan untuk Survey3, pemprosesan LATTICE menyokong sasaran berpenandaan ArUco, sasaran ROI tetap, dan sasaran jalur, yang dikonfigurasikan mengikut projek. Imbasan pantulan sasaran **diukur** bagi setiap unit boleh dibekalkan mengikut nombor siri (CLI: `--target-reflectance-dir`, satu `<serial>.csv` bagi setiap unit sasaran), dengan spektra T3/T4P nominal sebagai pilihan alternatif.

{% hint style="info" %}
**Modul F988**: Reflektan F988 dikalibrasi menggunakan panel reflektansi di dalam adegan: jalur itu terletak di luar julat kalibrasi penderia cahaya DAQ, jadi Chloros menggunakan tangkapan panel terkini anda dan menyimpannya di antara penjejakan panel. Jika modul F988 diproses hanya dengan DAQ, Chloros menolak pantulan berasaskan DAQ untuk jalur tersebut (sebab langkau `dls-uncalibrated-band-988`) — aliran kerja panel adalah laluan yang disokong.
{% endhint %}

***

## Amalan Terbaik untuk Imej Sasaran

### Garis Panduan Pengambilan Sasaran

**Penyelarasan Masa:**

* Ambil imej sasaran serta-merta sebelum dan sepanjang sesi pengambilan anda
* Dalam keadaan pencahayaan yang sama seperti penderia cahaya DAQ anda
* Secara ideal, rakam imej sasaran sekerap mungkin untuk mendapatkan keputusan terbaik. Jika tidak, data penderia cahaya akan digunakan untuk melaras kalibrasi dari semasa ke semasa.

**Posisi Kamera:**

* Pegang kamera di atas sasaran supaya ia terletak di tengah dan memenuhi sekitar 40-60% bahagian tengah imej.
* Pastikan kamera selari/tegak lurus dengan permukaan sasaran

**Pencahayaan:**

* Pencahayaan sekeliling yang sama seperti penderia cahaya DAQ anda
* Elakkan bayang-bayang pada permukaan sasaran
* Jangan halang sumber cahaya anda dengan badan, kenderaan atau tumbuhan
* Keadaan berawan memberikan keputusan yang paling konsisten

**Keadaan Sasaran:**

* Pastikan panel sasaran bersih dan kering
* Semua panel sasaran anda (contohnya, ke-4 pada T4) hendaklah jelas kelihatan dan tidak terhalang
* Sasaran tegak lurus/nadir kepada sumber cahaya jika boleh

### Berapa Banyak Imej Sasaran?

**Minimum:**1 imej sasaran setiap sesi.**Disyorkan:** 3-5 imej sasaran setiap sesi.**Jadual amalan terbaik:**

* 3-5 imej dirakam sejurus selepas penderia cahaya mula merekod
* Gilirkan kamera antara setiap rakaman untuk hasil terbaik
* Pilihan: secara berkala di tengah-tengah sesi jika keadaan pencahayaan sentiasa berubah

***

## Bekerja dengan Pelbagai Kamera

### Susun Atur Dua Kamera

Jika menggunakan dua kamera MAPIR serentak (contohnya, Survey3W RGN + Survey3N OCN):

1. Rakam imej sasaran dengan **kedua-dua kamera** pada masa yang sama
2. Gunakan **sasaran fizikal yang sama** untuk kedua-dua kamera
3. Tandakan imej sasaran untuk **kedua-dua jenis kamera** dalam Pelayar Fail
4. Chloros akan menggunakan sasaran yang sesuai untuk kalibrasi setiap kamera

### Ruang Model Kamera

Ruang **Model Kamera** membantu mengenal pasti imej yang datang daripada kamera mana:

* Survey3W\_RGN
* Survey3N\_OCN
* LATT-M3M-L41-F550
* LATT-M3C-L87-FRGN
* dan lain-lain.

Gunakan lajur ini untuk mengesahkan anda telah menandakan sasaran untuk setiap jenis kamera dalam projek anda.

***

## Tetapan Pengesanan Sasaran

### Mengatur Sensitiviti Pengesanan

Jika Chloros tidak mengesan sasaran anda dengan betul, atur tetapan ini dalam [Tetapan Projek](adjusting-project-settings.md):**Keluasan sampel pensijilan minimum (px):*** **Lalai**: 25 piksel
* **Tingkatkan** jika mendapat pengesanan palsu pada artifak kecil
* **Kurangkan** jika sasaran tidak dikesan**Pengklusteran Sasaran Minimum (0-100):*** **Lalai**: 60
* **Tingkatkan** jika sasaran dipecahkan kepada beberapa pengesanan
* **Kurangkan** jika sasaran dengan variasi warna tidak dikesan sepenuhnya

{% hint style="info" %}
**Petua CLI**: `chloros-cli process` menerima tetapan yang sama (`--min-target-size`, `--target-clustering`), dan penanda bendera `--target`/`--targets` menandakan keseluruhan folder input sebagai target-panel-sahaja. Rujuk [Rujukan CLI](../reference/cli-reference.md).
{% endhint %}

***

## Isu Imej Sasaran Biasa

### Masalah: Tiada Sasaran Dikesan

**Punca yang mungkin:**

* Imej sasaran tidak ditandakan dalam Pelayar Fail
* Sasaran terlalu kecil dalam bingkai (&lt; 30% imej)
* Pencahayaan yang buruk (bayang-bayang, silau)
* Tetapan pengesanan sasaran terlalu ketat

**Penyelesaian:**

1. Semak sama ada lajur Sasaran dicentang untuk imej yang betul
2. Semak kualiti imej sasaran dalam pratonton
3. Rakam semula imej sasaran jika kualitinya rendah
4. Laras tetapan pengesanan sasaran jika perlu

### Masalah: Pengesanan Sasaran Palsu

**Punca yang mungkin:**

* Bangunan putih, kenderaan, atau penutup tanah disangka sebagai sasaran
* Tompokan cerah pada tumbuhan
* Sensitiviti pengesanan terlalu rendah

**Penyelesaian:**

1. Tandakan hanya imej sasaran sebenar — imej yang ditandakan sahaja akan diimbas
2. Tingkatkan kawasan sampel penentukuran minimum
3. Tingkatkan nilai pengelompokan sasaran minimum
4. Pastikan imej sasaran hanya menunjukkan sasaran (gangguan latar belakang minimum)

***

## Senarai Semak Pengesahan

Sebelum memulakan pemprosesan, sahkan pemilihan imej sasaran anda:

* [ ] Sekurang-kurangnya 1 imej sasaran ditandakan bagi setiap sesi (atau, untuk LATTICE, rakaman `.daq`/`.csv` yang merangkumi sesi tersebut)
* [ ] Kotak semak lajur sasaran dicentang untuk semua imej sasaran
* [ ] Imej sasaran dirakam dalam jangka masa yang sama seperti tinjauan
* [ ] Sasaran jelas kelihatan dalam pratonton apabila klik
* [ ] Semua panel penentukuran kelihatan dalam setiap imej sasaran
* [ ] Tiada bayang-bayang atau halangan pada sasaran
* [ ] Untuk kamera dwi: Sasaran ditandakan untuk kedua-dua jenis kamera

***

## Pemprosesan Tanpa Sasaran

### LATTICE: Dengan Rakaman DAQ

Jika penderia cahaya DAQ merakam sinaran penyinaran ke bawah semasa tangkapan LATTICE anda, tiada sasaran diperlukan:

1. Impor fail `.daq` (atau DAQ-M `.csv`) dengan imej
2. Biarkan lajur Sasaran tidak dicentang
3. Reflektans dikira daripada rujukan sinaran ke bawah DAQ secara automatik
4. Radiasi tidak pernah memerlukan sasaran atau DAQ — ia datang hanya daripada penentukuran radiometrik kilang kamera

### Pemprosesan Tanpa Sebarang Rujukan

Anda juga boleh memproses tanpa sasaran dan tanpa DAQ:

1. Biarkan semua kotak semak lajur Sasaran tidak dicentang
2. **Lumpuhkan** &quot;Penentukuran pantulan / imbangan putih&quot; dalam Tetapan Projek — pengesanan sasaran kemudiannya akan diabaikan sepenuhnya
3. Pembetulan vignet akan tetap diterapkan
4. Keluaran tidak akan ditentukur untuk pantulan mutlak (LATTICE multispectral masih mengeksport produk debayered, pratonton dan sinaran)

{% hint style="warning" %}
**Tidak Disyorkan untuk kerja saintifik Survey3**: Tanpa penentukuran pantulan, nilai piksel Survey3 hanya mewakili kecerahan relatif, bukan ukuran pantulan saintifik. Gunakan sasaran penentukuran (atau, untuk LATTICE, penderia cahaya DAQ) untuk keputusan yang tepat dan boleh diulang.
{% endhint %}

***

## Langkah Seterusnya

Setelah anda menandakan imej sasaran anda:

1. **Semak tetapan anda** - Lihat [Mengesetkan Tetapan Projek](adjusting-project-settings.md)
2. **Mulakan pemprosesan** - Lihat [Memulakan Pemprosesan](starting-the-processing.md)
3. **Pantau kemajuan** - Lihat [Memantau Pemprosesan](monitoring-the-processing.md)

Untuk maklumat lanjut mengenai sasaran penentukuran itu sendiri, lihat [Sasaran Penentukuran](../calibration-targets.md).
