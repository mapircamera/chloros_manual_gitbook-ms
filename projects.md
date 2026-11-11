# GUI : Projek

Chloros membolehkan anda mencipta projek yang boleh dibuka semula pada masa hadapan. Projek adalah satu folder biasa (di dalam Folder Projek anda) yang mengandungi:

* `project.json` — tetapan projek, senarai fail, dan keutamaan paparan
* `cameras.json` — kamera dan susunan yang disambungkan semasa projek dibuka, beserta tetapan mereka
* `sensors.json` — penderia cahaya DAQ yang disambungkan semasa projek dibuka, serta pautan kamera↔penderia
* tangkapan anda, rakaman `.daq`, dan folder keluaran terproses

Tiada format fail projek proprietari — folder dan fail JSONnya adalah projek itu sendiri, yang juga memudahkan projek disalin, diarkibkan, dan dijalankan daripada [CLI](CLI.md) atau [Python SDK](api-python-sdk.md).

Kecuali anda menukarnya dalam [Tetapan Projek](project-settings/project-settings.md), Folder Projek ialah `Documents\MAPIR\Chloros Projects` dalam folder Dokumen anda (`~/Documents/MAPIR/Chloros Projects` pada Linux).

{% hint style="warning" %}
**Windows: jaga agar folder projek ringkas melainkan laluan panjang diaktifkan.**Windows disertakan dengan laluan panjang yang dilumpuhkan, dan tiada laluan fail boleh lebih panjang daripada 260 aksara. Produk yang diproses disimpan sehingga kira-kira 130 aksara di bawah folder projek, jadi mulai versi 1.3.0, Chloros menolak folder projek yang terlalu dalam — apabila anda menciptanya atau membukanya, atau apabila anda memulakan pemprosesan — bukannya berjalan dan tidak menulis apa-apa. Mesej itu memberikan folder projek terpanjang yang muat. Gunakan Folder Projek yang lebih pendek, atau aktifkan laluan panjang Windows (pilihan**Activate Windows Long Paths** pada pemasang, yang dipilih secara lalai) dan mulakan semula Chloros. Lihat [Memulakan Pemprosesan](processing-images-gui/starting-the-processing.md#the-project-folder-is-too-deep-for-this-computer).
{% endhint %}

## Projek Baru

<figure><img src=".gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>Pilih &quot;Projek Baru&quot; daripada menu utama dan masukkan nama unik untuk projek anda.

Jika anda telah menyimpan sebarang templat projek, menu lungsur **Pilih Templat** akan muncul di bawah medan nama — memilih salah satu daripadanya akan memulakan projek baru daripada tetapan templat tersebut. Skrip templat disimpan daripada [Tetapan Projek](project-settings/project-settings.md): masukkan nama dalam medan &quot;Nama Simpan Skrip Templat Projek&quot; dan klik ikon simpan.

## Buka Projek

<figure><img src=".gitbook/assets/v120-open-project.jpg" alt=""><figcaption><p>Projek Terbuka menyenaraikan setiap projek dalam folder projek anda, dengan <strong>Buka Folder Projek</strong> di bahagian bawah</p></figcaption></figure>

Pilih &quot;Buka Projek&quot; untuk melihat senarai projek sedia ada dalam Folder Projek. Jika tiada projek wujud, menu sisi sekunder tidak akan dibuka. Anda boleh melihat beberapa projek yang dicipta oleh GUI (t1, t2, t3) tersenarai dalam foto di atas. Projek DATE\_TIME telah dibuat oleh CLI menggunakan skema penamaan projek lalai. Mengklik mana-mana nama projek akan membukanya.

Mengklik butang &quot;Open Project Folder&quot; akan membuka pengurus fail komputer anda pada laluan projek tersebut. Anda boleh menyesuaikan laluan projek dalam [Tetapan Projek](project-settings/project-settings.md).

Jika mana-mana fail imej sumber projek telah dipindahkan atau dipadamkan sejak kali terakhir ia dibuka, Chloros akan memaparkan dialog yang menyenaraikan dengan tepat fail yang hilang, bukannya membuka grid kosong.

## Mendua Projek

Tersedia setelah projek dibuka. Pilih &quot;Mendua Projek&quot; untuk menyalin projek semasa dengan nama baru — Chloros mencadangkan nama kosong seterusnya (contohnya &quot;MyProject (2)&quot;) — dan salinan itu dibuka serta-merta.

## Tambah Fail

Selepas projek dibuka, pilih &quot;Tambah Fail&quot; daripada menu utama untuk menambah fail imej secara individu ke dalam projek semasa. Ini mencerminkan fungsi tambah pada pelayar fail tetapi boleh diakses terus daripada menu utama untuk kemudahan.

## Tambah Folder

Selepas projek dibuka, pilih &quot;Tambah Folder&quot; daripada menu utama untuk menambah folder imej ke dalam projek semasa. Anda boleh memilih berbilang folder dalam satu langkah. Fail dwi salinan akan diabaikan.

## Mulakan / Hentikan Pemprosesan

Selepas fail ditambah ke dalam projek, &quot;Mulakan Pemprosesan&quot; akan tersedia dalam menu utama. Ini adalah tindakan yang sama seperti mengklik butang Main/Mulakan di bar tajuk atas. Semasa pemprosesan, item menu akan bertukar kepada &quot;Hentikan Pemprosesan&quot; untuk membolehkan anda menghentikan aliran kerja.

## Sambung ke Kamera / Sambung ke Penderia Cahaya

Di bahagian bawah menu utama terdapat dua pintasan perkakasan, tersedia sama ada dengan atau tanpa projek dibuka:

* **Sambung ke Kamera** — membuka [Tab Kamera](lattice/) untuk menyambungkan kamera atau susunan LATTICE.
* **Sambungkan ke Penderia Cahaya** — membuka [tab Penderia Cahaya](daq/) untuk menyambungkan penderia cahaya DAQ.

Menyambungkan perkakasan semasa projek dibuka akan menyimpannya ke dalam projek (lihat di bawah). Tanpa projek, sambungan hanya untuk sesi.

{% hint style="info" %}
Item menu Tambah Fail, Tambah Folder, dan Mulakan/Hentikan Pemprosesan hanya akan kelihatan atau diaktifkan apabila sesuatu projek dibuka dan fail telah ditambah. Ia menyediakan akses pantas kepada tindakan yang juga boleh diakses melalui bar sisi dan butang tajuk Pelayar Fail.
{% endhint %}

## Projek mengingati perkakasan anda

Baharu dalam 1.2.0: projek menyimpan perkakasan yang anda sambungkan selagi ia terbuka. Kamera dan susunan (dengan tetapan setiap kamera, nama, warna, dan susun atur grid) diambil gambarnya ke dalam `cameras.json`, dan penderia cahaya (dengan nama, warna, dan pengikat kamera) ke dalam `sensors.json` — secara automatik, semasa anda bekerja.

Apabila anda **membuka semula** projek, Chloros tidak serta-merta menyentuh sebarang perkakasan. Setiap bahagian menyambung semula buat pertama kali apabila anda melawat tab yang memilikinya:

* Membuka tab **Kamera** menyambung semula kamera dan susunan yang disimpan dan menerapkan semula tetapan mereka yang disimpan.
* Membuka tab **Light Sensors**menyambung semula sensor DAQ yang disimpan dan menerapkan semula tetapan yang disimpannya, termasuk cap. Sensor yang ditetapkan kepada**Tiada (sensor kosong)** dalam menu Cap-nya kekal kosong. Projek yang disimpan oleh versi Chloros yang lebih awal merekodkan sensor kosong sebagai tiada pilihan, jadi ia akan kembali dengan pembetulan kosinus sinaran matahari; tetapkan semula kepada Tiada dan simpan projek tersebut.

Dengan cara ini, membuka projek semata-mata untuk menelusuri atau mengeksport imej tidak akan mengaktifkan kamera untuk penstriman. Jika peranti yang disimpan tidak dapat ditemui apabila tabnya dibuka, sebuah dialog akan memberitahu anda peranti mana yang tidak tersedia supaya anda boleh menyambung semula atau mengeluarkannya.

## Rakaman DAQ dan fail .daq dalam projek

* Rakaman `.daq` yang dibuat semasa projek dibuka (daripada tab Penderia Cahaya atau semasa pengambilan) **secara automatik ditambah ke dalam projek**.
* Fail `.daq` yang diimport, dan semua rakaman projek, disenaraikan dalam bahagian **DAQ Light Sensor** di [Project Settings](project-settings/project-settings.md), masing-masing dengan profil pembetulan capnya.
* Semasa pemprosesan, fail `.daq` projek membekalkan pencahayaan ke bawah untuk produk reflektansi — lihat [Format Imej Keluaran](output-image-formats.md).

## Mengendalikan projek yang disimpan tanpa paparan

Projek yang disimpan boleh dikendalikan tanpa GUI:

* **CLI**: `chloros-cli project open / connect / capture / sensor / align / run` beroperasi pada laluan folder projek — lihat [Rujukan CLI](reference/cli-reference.md).
* **SDK**: `chloros_sdk.open_project(path)` memulangkan pemegang projek; `connect_all()` memuatkan setiap kamera, tatasusunan dan penderia cahaya yang disimpan dalam talian dengan tetapan yang disimpannya (penderia cahaya melalui backend Chloros yang sedang berjalan) — lihat [Rujukan SDK](reference/sdk-reference.md).
