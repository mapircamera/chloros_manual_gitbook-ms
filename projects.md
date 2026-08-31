# GUI : Projek

Chlorosmembolehkan anda mencipta projek yang boleh dibuka semula pada masa hadapan. Projek adalah satu folder biasa (di dalam Folder Projek anda) yang mengandungi:

* `project.json` — tetapan projek, senarai fail, dan keutamaan paparan
* `cameras.json` — kamera dan susunan yang disambungkan semasa projek dibuka, dengan tetapan mereka
* `sensors.json` — penderia cahaya DAQ yang disambungkan semasa projek dibuka, serta pengikatan kamera↔penderia
* tangkapan anda, rakaman `.daq`, dan folder keluaran terproses

Tiada format fail projek proprietari — folder dan fail JSON-nya adalah projek tersebut, yang juga memudahkan projek disalin, diarkibkan, dan dijalankan daripada [CLI](CLI.md) atau [Python SDK](api-python-sdk.md).

## Projek Baru

<figure><img src=".gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>Pilih &quot;Projek Baru&quot; daripada menu utama dan masukkan nama unik untuk projek anda.

Jika anda telah menyimpan sebarang templat projek, menu lungsur **Pilih Templat** akan muncul di bawah medan nama — memilih salah satu daripadanya akan memulakan projek baru menggunakan tetapan templat tersebut. Skrip disimpan daripada [Tetapan Projek](project-settings/project-settings.md): masukkan nama dalam medan &quot;Nama Skrip Projek Simpanan&quot; dan klik ikon simpan.

## Buka Projek

<figure><img src=".gitbook/assets/v120-open-project.jpg" alt=""><figcaption><p>Projek Terbuka menyenaraikan setiap projek dalam folder projek anda, dengan <strong>Buka Folder Projek</strong> di bahagian bawah</p></figcaption></figure>

Pilih &quot;Open Project&quot; untuk melihat senarai projek sedia ada dalam Folder Projek. Jika tiada projek wujud, menu sisi sekunder tidak akan dibuka. Anda boleh melihat beberapa projek GUI yang dicipta (t1, t2, t3) tersenarai dalam foto di atas. Projek DATE\_TIME telah dibuat oleh CLI menggunakan skema penamaan projek lalai. Mengklik mana-mana nama projek akan membukanya.

Mengklik butang &quot;Open Project Folder&quot; akan membuka pengurus fail komputer anda di laluan projek tersebut. Anda boleh menyesuaikan laluan projek dalam [Project Settings](project-settings/project-settings.md).

Jika mana-mana fail imej sumber projek telah dipindahkan atau dipadamkan sejak kali terakhir ia dibuka, Chloros akan memaparkan dialog yang menyenaraikan fail yang hilang dengan tepat, bukannya membuka grid kosong.

## Mendua Projek

Tersedia setelah projek dibuka. Pilih &quot;Mendua Projek&quot; untuk menyalin projek semasa ke bawah nama baru —  mencadangkan nama bebas seterusnya (contohnya &quot;MyProject (2)&quot;) — dan salinan itu dibuka serta-merta.

## Tambah Fail

Selepas projek dibuka, pilih &quot;Tambah Fail&quot; daripada menu utama untuk menambah fail imej secara individu ke dalam projek semasa. Ini mencerminkan fungsi tambah pada pelayar fail tetapi boleh diakses terus daripada menu utama untuk kemudahan.

## Tambah Folder

Selepas projek dibuka, pilih &quot;Tambah Folder&quot; daripada menu utama untuk menambah folder imej ke dalam projek semasa. Anda boleh memilih berbilang folder dalam satu langkah. Fail dwi salinan diabaikan.

## Mulakan / Hentikan Pemprosesan

Selepas fail ditambah ke dalam projek, &quot;Mulakan Pemprosesan&quot; akan tersedia dalam menu utama. Ini adalah tindakan yang sama seperti mengklik butang Main/Mulakan di bar tajuk atas. Semasa pemprosesan, item menu akan bertukar kepada &quot;Hentikan Pemprosesan&quot; untuk membolehkan anda menghentikan aliran kerja.

## Sambung ke Kamera / Sambung ke Penderia Cahaya

Bahagian bawah menu utama mempunyai dua pintasan perkakasan, tersedia sama ada dengan atau tanpa projek dibuka:

* **Sambung ke Kamera** — membuka [Tab Kamera](lattice/) untuk menyambungkan kamera atau susunan LATTICE.
* **Sambung ke Penderia Cahaya** — membuka [tab Penderia Cahaya](daq/) untuk menyambungkan penderia cahaya DAQ.

Menyambungkan perkakasan semasa projek dibuka akan menyimpannya ke dalam projek (lihat di bawah). Tanpa projek, sambungan hanya untuk sesi.

{% hint style="info" %}
Item menu Tambah Fail, Tambah Folder, dan Mulakan/Hentikan Pemprosesan hanya akan kelihatan atau diaktifkan apabila sesuatu projek dibuka dan fail telah ditambah. Ia menyediakan akses pantas kepada tindakan yang juga boleh diakses melalui bar sisi dan butang tajuk Pelayar Fail.
{% endhint %}

## Projek mengingati perkakasan anda

Baharu dalam 1.2.0: projek menyimpan perkakasan yang anda sambungkan selagi ia terbuka. Kamera dan susunan (dengan tetapan setiap kamera, nama, warna, dan susun atur grid) diambil gambarnya ke dalam `cameras.json`, dan penderia cahaya (dengan nama, warna, dan pengikat kamera) ke dalam `sensors.json` — secara automatik, semasa anda bekerja.

Apabila anda **membuka semula** projek, Chloros tidak serta-merta menyentuh sebarang perkakasan. Setiap bahagian menyambung semula buat pertama kali apabila anda melawat tab yang memilikinya:

* Membuka tab **Kamera** menyambung semula kamera dan susunan yang disimpan dan menerapkan semula tetapan mereka yang disimpan.
* Membuka tab **Light Sensors** menyambung semula sensor DAQ yang disimpan.

Dengan cara ini, membuka projek semata-mata untuk menelusuri atau mengeksport imej tidak akan mengaktifkan kamera untuk penstriman. Jika peranti yang disimpan tidak dapat ditemui apabila tabnya dibuka, sebuah dialog akan memberitahu anda peranti mana yang tidak tersedia supaya anda boleh menyambung semula atau menghapusnya.

## Rakaman DAQ dan fail .daq dalam projek

* Rakaman `.daq` yang dibuat semasa projek dibuka (daripada tab Penderia Cahaya atau semasa tangkapan) **ditambah secara automatik ke dalam projek**.
* Fail `.daq` yang diimport, dan semua rakaman projek, disenaraikan dalam bahagian **DAQ Light Sensor** di [Project Settings](project-settings/project-settings.md), masing-masing dengan profil pembetulan capnya.
* Semasa pemprosesan, fail `.daq` projek membekalkan pencahayaan ke bawah untuk produk reflektansi — lihat [Format Imej Keluaran](output-image-formats.md).

## Mengendalikan projek yang disimpan tanpa GUI

Projek yang disimpan boleh dikendalikan tanpa GUI:

* **CLI**: `chloros-cli project open / connect / capture / sensor / align / run` beroperasi pada laluan folder projek — lihat [Rujukan CLI](reference/cli-reference.md).
* **SDK**: `chloros_sdk.open_project(path)` memulangkan penangan projek; `connect_all()` mengaktifkan setiap kamera dan penderia yang disimpan dalam talian dengan tetapan yang disimpannya — lihat [Rujukan SDK](reference/sdk-reference.md).
