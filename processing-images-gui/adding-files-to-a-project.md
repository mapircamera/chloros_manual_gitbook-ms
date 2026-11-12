# Menambah Fail ke dalam Projek

Setelah anda mencipta atau membuka projek dalam Chloros, langkah seterusnya ialah menambah imej multispektral anda untuk memulakan pemprosesan. Tab Pelayar Fail<img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line"> memudahkan anda mengimport imej dan menguruskan set data anda.

## Mengakses Pelayar Fail

1. Buka atau cipta projek dalam Chloros
2. Klik ikon **File Browser** <img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line"> di bar sisi kiri
3. Panel File Browser akan memaparkan senarai fail projek anda

{% hint style="info" %}
**Jenis Fail yang Disokong**:

* **Survey3W / Survey3N**: Pasangan RAW+JPG dan imej JPG (RAW+JPG disyorkan)
* **LATTICE**: tangkapan `.tif` / `.tiff` — ditulis oleh kawalan kamera Chloros atau oleh hab LATTICE
* **Data penderia cahaya**: Rakaman `.daq` (DAQ-U/M/E) dan log DAQ-M `.csv` downwelling — diimport bersama imej untuk menggerakkan kalibrasi pantulan
{% endhint %}

***

## Menambah Imej ke Projek Anda

Terdapat dua cara utama untuk menambah imej ke projek anda:

### Kaedah 1: Tambah Fail

Gunakan pilihan ini untuk mengimport fail imej individu atau pilihan kecil fail.

1. Klik butang **"Add Files"** <img src="../.gitbook/assets/image (3).png" alt="" data-size="line"> di bahagian atas panel File Browser
2. Navigasi ke folder yang mengandungi imej anda
3. Pilih satu atau lebih fail imej (tekan dan tahan **Ctrl** untuk memilih beberapa fail)
4. Klik **"Open"** untuk mengimport fail yang dipilih

### Kaedah 2: Tambah Folder

Gunakan pilihan ini untuk mengimport semua imej dari satu folder sekaligus. Anda boleh memilih **beberapa folder** dalam satu tetingkap dialog.

1. Klik butang **"Add Folder"** <img src="../.gitbook/assets/image (1) (1).png" alt="" data-size="line"> di bahagian atas panel Pelayar Fail
2. Navigasi ke dan pilih folder (atau folder-folder) yang mengandungi imej sesi tangkapan anda
3. Klik **"Select Folder"** untuk mengimport semua imej yang disokong

{% hint style="info" %}
**Fail yang gagal dimuatkan akan dilaporkan.** Jika sesuatu folder mengandungi fail yang diiktiraf oleh Chloros tetapi tidak dapat dimuatkan, amaran akan memaklumkan anda — imej tidak akan hilang secara senyap daripada grid.
{% endhint %}

***

## Mengimport Folder Rakaman LATTICE

Rakaman LATTICE disimpan dengan **satu subfolder bagi setiap tahap eksport** — contohnya `raw/`, `debayered/`, `radiance/`, `reflectance/`, `preview/` — dengan fail downwelling `.daq` yang sepadan di dalam root:

```
output/
├── raw/           capture_<timestamp>_SN<serial>_raw.tif
├── debayered/     capture_<timestamp>_SN<serial>_debayered.tif
├── preview/       capture_<timestamp>_SN<serial>_display.tif
└── *.daq          the downwelling reading matched to the capture
```

**Tandakan Tambah Folder pada akar tangkapan** (`output/` di atas). Apabila folder yang dipilih tidak mengandungi sebarang imej tetapi mempunyai subfolder, Chloros akan menelusuri subfolder tersebut secara automatik — subfolder pada setiap aras dan folder akar `.daq` semua diambil sekaligus.

**Cara import tangkapan:**

* Setiap tangkapan diimport sebagai **satu imej tunggal**, dikumpulkan mengikut tangkapan (bukan satu kemasukan bagi setiap tahap). Tahap-tahap lain bagi tangkapan yang sama akan muncul sebagai mod paparan bagi imej tersebut.
* **Pemprosesan sentiasa bermula daripada bingkai mentah.** Tahap-tahap lain boleh dilihat, tetapi hanya `raw` yang akan dimasukkan ke dalam saluran pemprosesan — memproses semula produk yang telah diproses akan menyebabkan pembetulan digunakan dua kali, jadi Chloros menolaknya. Eksport yang diimport semula tidak akan pernah mengambil slot mentah sesuatu tangkapan.
* Folder tangkapan yang disimpan **tanpa** import mentah akan dipaparkan seperti biasa, tetapi pemprosesan akan melangkauinya dan akan memberitahu demikian dalam log. (Penanda aras CLI `--input-level` boleh memaksa titik kemasukan untuk kes ini — lihat [Rujukan CLI](../reference/cli-reference.md#what-a-captures-folder-looks-like).)
* Mulai versi 1.3.0, folder captures juga boleh mengandungi folder `clamp_masks/` (topeng piksel demi piksel untuk tahap sinaran dan pantulan — lihat [Format Imej Keluaran](../output-image-formats.md#clamp-masks)). Topeng bukan imej pemandangan, dan Chloros akan melangkau mereka semasa import di mana sahaja ia berada.

**Sesi LATTICE hub** diimport dengan cara yang sama: tudingkan Tambah Folder ke pada folder sesi yang disalin daripada hab (ia mengandungi `raw/` dan `previews/`), bersama sebarang log DAQ-M `.csv` downwelling. Jika penentukuran kamera atau DAQ belum disimpan dalam cache pada mesin anda, Chloros akan mengambilnya secara automatik melalui nombor siri semasa pengimportan (perlu internet sekali sahaja).

***

## Memahami Jadual Pelayar Fail

Setelah imej diimport, ia akan muncul dalam jadual dengan lajur berikut:

### Nama Fail

* Nama fail asal daripada kamera
* Menjaga konvensyen penamaan kamera (contohnya, IMG\_0001.RAW atau capture\_20260816\_101500\_SN213800234\_raw.tif)

### Cap Masa

* Tarikh dan masa imej dirakam
* Diekstrak daripada metadata EXIF imej
* Digunakan untuk padanan penderia cahaya, penyelarasan PPK dan penjadualan sasaran penentukuran

### Model Kamera

* Pengesanan automatik konfigurasi kamera dan penapis
* Contoh Survey3: Survey3W\_RGN, Survey3N\_OCN, Survey3W\_RGB
* Contoh LATTICE: LATT-M3M-L41-F550, LATT-M3C-L87-FRGN
* Digunakan untuk menerapkan profil pemprosesan yang betul

### Baris Sasaran (Petak Semak)

* Semak petak ini untuk imej yang mengandungi sasaran penentukuran
* Apabila sekurang-kurangnya satu imej disemak, **hanya imej yang disemak akan diimbas** untuk sasaran
* Lihat [Memilih Imej Sasaran](choosing-target-images.md) untuk maklumat lanjut

### Melihat Metadata Imej

Mengklik butang togol di penjuru atas kanan di atas jadual akan memaparkan metadata imej yang dipilih di kawasan grid imej.

<figure><img src="../.gitbook/assets/chloros_grid_meta.gif" alt=""><figcaption></figcaption></figure>

***

## Fail Penderia Cahaya dalam Projek Anda

* Fail `.daq` dan `.csv` muncul dalam senarai Pelayar Fail tetapi bukan imej yang boleh diklik — ia membekalkan iradiasi ke bawah untuk penentukuran pantulan.
* Setiap fail `.daq`/`.csv` yang diimport disenaraikan dalam **Project Settings → DAQ Light Sensor**, di mana anda boleh menyemak pembetulan topi penyebar yang digunakan untuk setiap fail. Lihat [Mengeset Tetapan Projek](adjusting-project-settings.md).
* Rakaman yang anda buat dalam tab **Penderia Cahaya** akan ditambah ke dalam projek terbuka secara automatik — tiada import manual diperlukan.

***

## Mengurus Fail dalam Projek Anda

### Mengalihkan Fail

Untuk mengalihkan imej yang tidak diingini daripada projek anda:

1. Pilih satu atau lebih imej dalam jadual Pelayar Fail
2. Klik butang **"Remove Selected"** <img src="../.gitbook/assets/image (2) (1).png" alt="" data-size="line">
3. Sahkan pemindahan (fail tidak dipadamkan daripada cakera, hanya dialihkan daripada projek)

### Mengatur Susun dan Menapis

* **Atur mengikut lajur**: Klik mana-mana tajuk lajur untuk mengatur imej
* **Aturan cap masa**: Berguna untuk menyusun urutan tangkapan mengikut kronologi
* **Penapis model kamera**: Kumpulkan imej mengikut jenis kamera jika menggunakan berbilang kamera***

## Pratonton Imej

### Melihat Imej Penuh

Klik mana-mana miniatur imej dalam Pelayar Fail untuk memaparkannya di kawasan pratonton utama:

1. Imej akan muncul di panel pratonton tengah
2. Gunakan kawalan zum untuk memeriksa butiran imej
3. Navigasi antara imej menggunakan kekunci anak panah

### Navigasi Pantas

* **Imej Sebelumnya**: Klik anak panah kiri atau tekan kekunci ←
* **Imej Seterusnya**: Klik anak panah kanan atau tekan kekunci →
* **Zom Masuk/Keluar**: Gunakan roda tetikus atau butang zom
* **Alih**: Klik dan seret pada imej apabila dizoom masuk***

## Pengendalian Fail Duplikat

Chloros secara automatik mengesan dan mengabaikan fail duplikat:

* Fail dengan nama fail yang sama akan dilangkau
* Mencegah pemprosesan berganda secara tidak sengaja
* Pesan amaran akan dipaparkan apabila fail duplikat dikesan

{% hint style="warning" %}
**Penting**: Jangan menamakan semula atau mengubah suai fail imej asal anda sebelum mengimport. Chloros bergantung pada nama fail asal dan metadata untuk pemprosesan yang betul.
{% endhint %}

***

## Set Data Kamera Campuran

Jika projek anda mengandungi imej daripada berbilang kamera MAPIR:

1. Chloros secara automatik mengesan setiap model kamera — Survey3, LATTICE, atau campuran
2. Setiap jenis kamera diproses dengan profil penentukuran yang sesuai
3. Pelayar Fail memaparkan model kamera dalam lajur Model Kamera
4. Setiap kamera mendapat pokok folder keluaran sendiri apabila diproses

**Contoh senario**: susun atur dwi-kamera Survey3W RGN + Survey3N OCN, atau susunan LATTICE dengan RGB induk dan beberapa modul jalur sempit***

## Amalan Terbaik

### Atur Sebelum Masuk

* Simpan imej sasaran penentukuran dalam folder yang sama dengan imej tinjauan
* Simpan fail penderia cahaya `.daq` / `.csv` bagi setiap sesi tangkapan bersama imej sesi tersebut
* Kekalkan struktur folder asal daripada kamera/kad SD/hab anda
* Jangan campurkan set data daripada sesi yang berbeza dalam satu projek

### Penamaan Fail

* Pelihara nama fail kamera asal (IMG\_0001.RAW, capture\_..., dan sebagainya)
* Jangan tukar nama fail sebelum import
* Nama asal mengandungi metadata penting

### Imej Sasaran Kalibrasi

* Sentiasa sertakan 1-2 imej sasaran kalibrasi bagi setiap sesi (Survey3; untuk LATTICE, rakaman DAQ boleh digunakan sebagai ganti — lihat [Memilih Imej Sasaran](choosing-target-images.md))
* Rakam sasaran sebelum dan selepas sesi rakaman
* Letakkan sasaran dalam keadaan pencahayaan yang sama seperti kawasan rakaman
* Tanda imej sasaran menggunakan kotak semak Sasaran

***

## Isu dan Penyelesaian Lazim

### Imej Tidak Muncul Selepas Import

**Punca yang mungkin:**

* Format fail tidak disokong (lihat senarai jenis yang disokong di bahagian atas halaman ini)
* Imej berasal daripada kamera bukan MAPIR (lihat [Kamera yang Disokong](../supported-cameras.md))
* Kerosakan fail atau pemindahan tidak lengkap daripada kad SD

**Penyelesaian**: Semak keserasian format fail dan model kamera, dan periksa amaran pemuatan fail untuk mengetahui fail yang tepat yang gagal dimuat.

### Model Kamera Tidak Dikesan

**Punca yang mungkin:**

* Metadata EXIF telah diubah suai
* Imej disunting dalam perisian luaran
* Pemindahan fail tidak lengkap

**Penyelesaian**: Import semula fail asal yang tidak diubah suai daripada kamera/kad SD

### Cap Masa Hilang

**Punca yang mungkin:**

* Jam kamera tidak diset dengan betul
* Data EXIF dipadam oleh perisian luaran

**Penyelesaian**: Semak tetapan masa kamera adalah betul semasa mengambil gambar

### Laporan Projek Dibuka Semula Menunjukkan Fail Hilang

Jika fail sumber dipindahkan atau dipadam sejak projek terakhir dibuka, Chloros akan memberitahu anda **fail** **mana**yang hilang dan bukannya membuka pada grid kosong. Jika folder projek itu sendiri dipindahkan, Chloros akan menuding semula laluan sumbernya ke folder di mana `project.json` berada sekarang, dan menyimpan sandaran (`project.json.before-relink-<date>-<time>`). Apabila ia tidak menemuinya, tetingkap**Fail sumber tidak ditemui**menawarkan**BROWSE** ke lokasi baru, kemudian Projek Baharu atau Padam Projek. Daripada CLI, gunakan `chloros-cli project open PATH --relink FOLDER`; daripada SDK, `open_project(path, relink=...)`.

***

## Langkah Seterusnya

Setelah fail anda diimport:

1. **Semak senarai fail** - Pastikan semua imej dimuat dengan betul
2. **Semak model kamera** - Sahkan pengesanan kamera yang betul
3. **Tandakan imej sasaran** - Lihat [Memilih Imej Sasaran](choosing-target-images.md)
4. **Laraskan tetapan** - Konfigurasikan pilihan pemprosesan dalam [Tetapan Projek](adjusting-project-settings.md)
5. **Mulakan pemprosesan** - Lihat [Memulakan Pemprosesan](starting-the-processing.md)

Untuk maklumat terperinci tentang konfigurasi projek, lihat [Mengatur Seting Projek](adjusting-project-settings.md).
