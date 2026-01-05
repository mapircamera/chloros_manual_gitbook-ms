# Memilih Imej Sasaran

Menandai imej yang mengandungi sasaran penentukuran ialah langkah penting yang mempercepatkan saluran pemprosesan Chloros dengan ketara. Dengan pra-memilih imej sasaran, anda menghapuskan keperluan untuk Chloros untuk mengimbas setiap imej dalam set data anda untuk sasaran penentukuran.

## Mengapa Menandai Imej Sasaran?

### Kelajuan Pemprosesan

Tanpa menandakan imej sasaran, Chloros mesti:

* Imbas setiap imej dalam projek anda
* Jalankan algoritma pengesanan sasaran pada setiap imej
* Semak ratusan atau ribuan imej tanpa perlu

**Hasil**: Pemprosesan boleh mengambil masa yang lebih lama, terutamanya untuk set data yang besar.

### Dengan Imej Sasaran Bertanda

Apabila anda menyemak lajur Sasaran untuk imej tertentu:

* Chloros hanya mengimbas imej yang diperiksa untuk mencari sasaran
* Pengesanan sasaran selesai dengan lebih cepat
* Keseluruhan masa pemprosesan sangat dikurangkan

{% gaya petunjuk="berjaya" %}
**Peningkatan Kelajuan**: Menandai 2-3 imej sasaran dalam set data 500 imej boleh mengurangkan masa pengesanan sasaran daripada 30+ minit kepada kurang daripada 1 minit.
Petua {% %}

***

## Cara Menandai Imej Sasaran

### Langkah 1: Kenal pasti Imej Sasaran Anda

Lihat imej anda yang diimport dalam Pelayar Fail dan kenal pasti imej yang mengandungi sasaran penentukuran.

**Senario biasa:*** **Sasaran pra-tangkap**: Ditangkap sebelum memulakan sesi
* **Sasaran selepas tangkapan**: Ditangkap selepas melengkapkan sesi
* **Sasaran dalam medan**: Sasaran diletakkan dalam kawasan tangkapan
* **Berbilang sasaran**: 2-3 imej sasaran setiap sesi (disyorkan)

### Langkah 2: Semak Lajur Sasaran

Untuk setiap imej yang mengandungi sasaran penentukuran:

1. Cari imej dalam jadual Pelayar Fail
2. Cari lajur **Sasaran** (lajur paling kanan)
3. Klik kotak pilihan dalam lajur Sasaran untuk imej tersebut
4. Ulang untuk semua imej yang mengandungi sasaran

### Langkah 3: Sahkan Pilihan Anda

Sebelum memproses, semak semula:

* [ ] Semua imej dengan sasaran penentukuran disemak
* [ ] Tiada imej bukan sasaran disemak secara tidak sengaja
* [ ] Sasaran boleh dilihat dengan jelas dalam imej bertanda

***

## Amalan Terbaik untuk Imej Sasaran

### Garis Panduan Tangkap Sasaran

**Masa:**

* Tangkap imej sasaran serta-merta sebelum dan sepanjang sesi tangkapan anda
* Dalam keadaan pencahayaan yang sama seperti penderia cahaya DAQ anda
* Sebaik-baiknya tangkap imej sasaran sekerap mungkin untuk hasil terbaik. Jika tidak, data sensor cahaya akan digunakan untuk melaraskan penentukuran dari semasa ke semasa.

**Kedudukan Kamera:**

* Pegang kamera di atas sasaran supaya berada di tengah dan mengisi sekitar 40-60% daripada pusat imej.
* Pastikan kamera selari/nadir dengan permukaan sasaran

**Pencahayaan:**

* Pencahayaan ambien yang sama seperti penderia cahaya DAQ anda
* Elakkan bayang-bayang pada permukaan sasaran
* Jangan sekat sumber cahaya anda dengan badan, kenderaan atau tumbuh-tumbuhan anda
* Keadaan mendung memberikan hasil yang paling konsisten

**Keadaan Sasaran:**

* Pastikan panel sasaran bersih dan kering
* Kesemua 4 panel hendaklah kelihatan jelas dan tidak terhalang
* Sasarkan serenjang/nadir ke sumber cahaya jika boleh

### Berapa Banyak Imej Sasaran?

**Minimum:**1 imej sasaran setiap sesi.**Disyorkan:** 3-5 imej sasaran setiap sesi.**Jadual amalan terbaik:**

* 3-5 imej ditangkap sejurus selepas penderia cahaya merakam
* Putar kamera antara tangkapan untuk hasil terbaik
* Pilihan: pertengahan sesi secara berkala jika keadaan pencahayaan sentiasa berubah

***

## Bekerja dengan Berbilang Kamera

### Persediaan Dwi-Kamera

Jika menggunakan dua kamera MAPIR serentak (cth., Survey3W RGN + Survey3N OCN):

1. Tangkap imej sasaran dengan **kedua-dua kamera** pada masa yang sama
2. Gunakan **sasaran fizikal yang sama** untuk kedua-dua kamera
3. Tandai imej sasaran untuk **kedua-dua jenis kamera** dalam Pelayar Fail
4. Chloros akan menggunakan sasaran yang sesuai untuk setiap penentukuran kamera

### Lajur Model Kamera

Lajur **Model Kamera** membantu mengenal pasti imej yang datang dari kamera mana:

* Survey3W\_RGN
* Survey3N\_OCN
* Survey3W\_RGB
* dll.

Gunakan lajur ini untuk mengesahkan anda telah menandakan sasaran untuk setiap jenis kamera dalam projek anda.

***

## Tetapan Pengesanan Sasaran

### Melaraskan Sensitiviti Pengesanan

Jika Chloros tidak mengesan sasaran anda dengan betul, laraskan tetapan ini dalam [Tetapan Projek](adjusting-project-settings.md):**Kawasan sampel penentukuran minimum:*** **Lalai**: 25 piksel
* **Tingkatkan** jika mendapat pengesanan palsu pada artifak kecil
* **Kurang** jika sasaran tidak dikesan**Pengkelompokan sasaran minimum:*** **Lalai**: 60
* **Tingkatkan** jika sasaran dipecahkan kepada berbilang pengesanan
* **Kurangkan** jika sasaran dengan variasi warna tidak dikesan sepenuhnya***

## Isu Imej Sasaran Biasa

### Masalah: Tiada Sasaran Dikesan

**Punca yang mungkin:**

* Imej sasaran tidak ditanda dalam Pelayar Fail
* Sasarkan terlalu kecil dalam bingkai (< 30% daripada imej)
* Pencahayaan yang kurang baik (bayang-bayang, silau)
* Tetapan pengesanan sasaran terlalu ketat

**Penyelesaian:**

1. Sahkan lajur Sasaran disemak untuk imej yang betul
2. Semak kualiti imej sasaran dalam pratonton
3. Tangkap semula sasaran jika kualiti kurang baik
4. Laraskan tetapan pengesanan sasaran jika perlu

### Masalah: Pengesanan Sasaran Palsu

**Punca yang mungkin:**

* Bangunan putih, kenderaan atau penutup tanah disalah anggap sebagai sasaran
* Tompok terang dalam tumbuh-tumbuhan
* Kepekaan pengesanan terlalu rendah

**Penyelesaian:**

1. Tandakan hanya imej sasaran sebenar untuk mengehadkan skop pengesanan
2. Tingkatkan kawasan sampel penentukuran minimum
3. Meningkatkan nilai pengelompokan sasaran minimum
4. Pastikan imej sasaran hanya menunjukkan sasaran (kekacauan latar belakang minimum)

***

## Senarai Semak Pengesahan

Sebelum memulakan pemprosesan, sahkan pemilihan imej sasaran anda:

* [ ] Sekurang-kurangnya 1 imej sasaran ditanda setiap sesi
* [ ] Kotak pilihan lajur sasaran ditandakan untuk semua imej sasaran
* [ ] Imej sasaran ditangkap dalam tempoh masa yang sama seperti tinjauan
* [ ] Sasaran kelihatan jelas dalam pratonton apabila diklik
* [ ] Kesemua 4 panel penentukuran kelihatan dalam setiap imej sasaran
* [ ] Tiada bayang-bayang atau halangan pada sasaran
* [ ] Untuk dwi-kamera: Sasaran ditanda untuk kedua-dua jenis kamera

***

## Pemprosesan Tanpa Sasaran

### Pemprosesan Tanpa Sasaran Penentukuran

Walaupun tidak disyorkan untuk kerja saintifik, anda boleh memproses tanpa sasaran:

1. Biarkan semua kotak semak lajur Sasaran tidak ditandakan
2. **Lumpuhkan** "Penentukuran pantulan" dalam Tetapan Projek
3. Pembetulan vignet masih akan digunakan
4. Output tidak akan ditentukur untuk pemantulan mutlak

{% gaya petunjuk="amaran" %}
**Tidak Disyorkan**: Tanpa penentukuran pantulan, nilai piksel mewakili kecerahan relatif sahaja, bukan ukuran pantulan saintifik. Gunakan sasaran penentukuran untuk hasil yang tepat dan boleh berulang.
Petua {% %}

***

## Langkah Seterusnya

Sebaik sahaja anda menandakan imej sasaran anda:

1. **Semak tetapan anda** - Lihat [Melaraskan Tetapan Projek](adjusting-project-settings.md)
2. **Mulakan pemprosesan** - Lihat [Memulakan Pemprosesan](starting-the-processing.md)
3. **Pantau kemajuan** - Lihat [Memantau Pemprosesan](monitoring-the-processing.md)

Untuk mendapatkan maklumat lanjut tentang sasaran penentukuran sendiri, lihat [Sasaran Penentukuran](../calibration-targets.md).