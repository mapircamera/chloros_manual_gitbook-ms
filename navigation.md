# GUI : Navigasi

Apabila anda melancarkan Chloros buat pertama kali, ia akan memulakan backend pemprosesannya. Setelah backend bersedia, ikon menu utama di kiri atas akan terdedah <img src=".gitbook/assets/image (1) (1) (1) (1).png" alt="" data-size="line"> dan tab Kamera dan Penderia Cahaya akan dibuka dalam bar sisi kiri (ia akan kelihatan malap sehingga ketika itu).

<figure><img src=".gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Dari kiri ke kanan, tajuk utama mengandungi:

### <img src=".gitbook/assets/image (1) (1) (1) (1).png" alt="" data-size="line"> Menu Utama

<figure><img src=".gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>Dari menu utama anda boleh:

* **Projek Baharu**— mencipta projek baharu. Jika anda mempunyai templat projek yang disimpan, satu menu lungsur**Pilih Templat** akan muncul supaya projek baharu bermula daripada tetapan templat tersebut.
* **Buka Projek**— membuka projek sedia ada. Senarai ini termasuk butang**Buka Folder Projek** yang membuka folder projek dalam pengurus fail anda.
* **Duplikasi Projek** — salin projek yang sedang dibuka dengan nama baru (nama bebas seperti "MyProject (2)" dicadangkan) dan buka salinannya. _(tampak selepas projek dibuka)_
* **Tambah Fail** — tambah fail imej individu ke dalam projek semasa _(tampak selepas projek dibuka)_
* **Tambah Folder** — tambah satu atau lebih folder imej ke dalam projek semasa _(tampak selepas projek dibuka)_
* **Mulakan Pemprosesan / Hentikan Pemprosesan** — mulakan atau hentikan saluran pemprosesan imej _(diaktifkan selepas fail ditambah)_
* **Sambung ke Kamera** — lompat ke [Tab Kamera](lattice/) untuk menyambungkan kamera atau susunan LATTICE. Berfungsi tanpa projek dibuka.
* **Sambung ke Penderia Cahaya** — lompat ke [tab Penderia Cahaya](daq/) untuk menyambungkan penderia cahaya DAQ. Berfungsi tanpa projek dibuka.

{% hint style="info" %}
**Windows sahaja**: Antaramuka Pengguna Grafis Desktop Chloros tersedia pada Windows. Pengguna Linux harus melihat dokumentasi [CLI](CLI.md) dan [Python SDK](api-python-sdk.md) untuk pemprosesan tanpa papan pemuka.
{% endhint %}

### Butang Main/Mula <img src=".gitbook/assets/image (2) (1) (1).png" alt="" data-size="line">



Apabila diaktifkan, butang mula pemprosesan memulakan saluran pemprosesan imej.

### Bar Kemajuan <img src=".gitbook/assets/image (4).png" alt="" data-size="line">

<img src=".gitbook/assets/image (5).png" alt="" data-size="line">



Dalam mod Chloros percuma, yang memproses semua fail secara bersiri, bar kemajuan akan menunjukkan 2 peringkat: Pengesanan Sasaran dan Pemprosesan.

Dalam mod berlesen Chloros+ berbayar, yang memproses semua fail serentak, bar kemajuan menunjukkan 4 peringkat: Mengesan, Menganalisis, Kalibrasi, Mengeksport. Jika anda mengarahkan penunjuk tetikus ke atas bar kemajuan Chloros+, ia akan membuka panel bar kemajuan 4 peringkat yang diperluas supaya anda boleh memantau kemajuan. Mengklik bar kemajuan atas akan membekukan panel lungsur, dan mengklik sekali lagi akan mencairkannya.

<figure><img src=".gitbook/assets/plus_prog.JPG" alt=""><figcaption></figcaption></figure>

## Menu Tepi

Menu bar sisi kiri mengandungi pelbagai ikon untuk berinteraksi, dalam susunan ini dari atas ke bawah:

#### <img src=".gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> [Seting Projek](project-settings/project-settings.md)

Tab Seting Projek membolehkan anda melaraskan tetapan global projek dan tetapan pemprosesan projek. Laraskan tetapan ini sebelum memulakan pemprosesan fail anda.

#### Pelayar Fail <img src=".gitbook/assets/icon_file-browser.JPG" alt="" data-size="line">

Tambah fail/folder dan keluarkan fail daripada projek. Fail dwi salinan diabaikan. Centang kotak lajur sasaran untuk mana-mana imej sasaran, dan pemprosesan hanya akan mencari imej yang dicentang untuk sasaran, sekali gus mempercepatkan masa pemprosesan anda dengan ketara. Gunakan suis Imej/Metadata untuk bertukar antara melihat grid imej kecil imej yang dipilih dan jadual metadata terperinci.

#### <img src=".gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> [Penyemak Imej](image-viewer-gui/opening-an-image-full-screen.md)

Apabila imej diklik dalam pemapar imej utama, ia akan dibuka pada skrin penuh dalam tab Pemapar Imej.

#### <img src=".gitbook/assets/image (3) (1).png" alt="" data-size="line"> [Pemapar Peta](image-viewer-gui/map-markers.md)

Lihat imej anda pada peta 2D interaktif berdasarkan koordinat GPS mereka. Menyokong penyedia jubin Google Maps dan ESRI, memilih secara automatik perkhidmatan terbaik untuk lokasi anda. Layangkan tetikus ke atas penanda untuk melihat pratonton imej miniatur.

#### <img src=".gitbook/assets/image (17).png" alt="" data-size="line"> [Kamera](lattice/)

Sambung dan kawal kamera LATTICE secara langsung — satu pada satu masa atau sebagai susunan multi-kamera yang diselaraskan. Tab ini memaparkan jubin pratonton langsung dengan lapisan dan histogram, tetapan bagi setiap kamera dan setiap susunan, serta Tetapan Rakaman yang memilih kamera dan jenis eksport yang dihasilkan oleh "Capture All". Tersedia apabila backend sudah siap; lihat [seksyen LATTICE](lattice/) untuk panduan lengkap.

#### <img src=".gitbook/assets/image (23).png" alt="" data-size="line"> [Penderia Cahaya](daq/)

Sambungkan penderia cahaya DAQ — DAQ-U (USB), DAQ-M (Bluetooth), dan DAQ-E (Ethernet) — dan lihat carta spektrum kalibrasi langsung mereka dalam W/m²/nm. Dari sini anda boleh merakam fail `.daq` ke dalam projek terbuka, menamakan semula penderia, memilih profil pembetulan cap, dan mengemas kini firmware DAQ-E. Tersedia setelah backend siap; lihat [seksyen DAQ](daq/) untuk panduan lengkap.

#### Log Perbaikan Ralat <img src=".gitbook/assets/icon_log.JPG" alt="" data-size="line">

Semak log untuk cetakan debug apabila masalah berlaku. Salin/muat turun log dan hantar kepada [Sokongan MAPIR](https://www.mapir.camera/community/contact) untuk mendapatkan bantuan.

Baharu dalam 1.3.0: ikon pepijat di bahagian atas tab log (**Laporkan pepijat**) menghantar laporan kepada MAPIR dengan keterangan anda, sehingga 10 imej, log Chloros dan butiran sistem. Anda mesti log masuk untuk menghantarnya. Lihat [Melaporkan pepijat daripada tab log](processing-images-gui/monitoring-the-processing.md#reporting-a-bug-from-the-log-tab).

#### <img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line"> [Log Masuk Pengguna](chloros+-login.md)

Bar sisi log masuk pengguna membolehkan anda log masuk ke akaun Chloros+ anda untuk membuka ciri lanjutan. Anda juga boleh melihat versi aplikasi semasa, serta melaraskan bahasa teks yang dipaparkan dalam GUI Chloros dan CLI.
