# Rakaman & Format .daq

Fail `.daq` ialah format rakaman penderia cahaya Chloros: sebuah **pangkalan data SQLite** yang mengandungi bingkai spektral yang telah dilaras daripada satu penderia DAQ. Rakam satu daripadanya semasa sesi tangkapan dan saluran pemantulan boleh kemudian membahagikan setiap imej dengan iradiasi ke bawah yang diukur pada saat yang tepat itu.

## Kandungan .daq

| Sifat | Nilai |
| --- | --- |
| Konten | Pangkalan data SQLite, satu fail bagi setiap penderia bagi setiap rakaman |
| Nama fail | Termasuk **ID penderia**dan**cap masa**, contohnya `daq_data_daq-e-def330_2026_04_13_18h30m00.daq` |
| Spektrum setiap bingkai | 135 titik, 340–1010 nm pada selang 5 nm, ditambah tristimulasi CIE XYZ |
| Unit | Iradiasi spektral yang dikalibrasi, **W/m²/nm**(bungkusan kalibrasi kilang + profil cap diterapkan) |
| Metadata bercop | ID Sensor (kunci untuk mendapatkan kalibrasi kilang unit tersebut) dan profil topi yang digunakan — lihat [Profil Topi & Julat Dikalis](caps-and-range.md) |

Format adalah sama di seluruh DAQ-U, DAQ-M, DAQ-E dan DAQ-E-S, jadi pemprosesan susulan tidak kisah pengangkut mana yang merakamnya. Rakaman DAQ-E-S juga menyimpan cerun, gelongsor dan haluan unit di sebelah setiap spektrum.

Rakaman akan terus selamat walaupun berlaku pemadaman kuasa (1.3.0). Rakaman langsung ditulis dengan jurnal dan penyegerakan cakera penuh pada setiap komit, jadi pemadaman kuasa atau proses yang dihentikan hanya akan menyebabkan kehilangan kira-kira 3 saat terakhir, bukan keseluruhan fail. Rakaman yang terhenti semasa penulisan boleh meninggalkan fail `<name>.daq-journal` di sebelah `.daq`:**simpan dan salinkan kedua-duanya bersama.** Chloros memulihkan jurnal pada kali pertama ia membaca fail tersebut. Cakera yang sibuk menangguhkan fail, bukan bacaan sensor.

**DAQ-E yang berhenti penghantaran akan disambung semula (1.3.0).** Apabila aliran DAQ-E menjadi senyap (kuasa suisnya dimulakan semula, pautan terputus), backend menyambungkannya semula dengan tetapan yang sama, dan rakaman padanya diteruskan dalam `.daq` yang sama dengan jurang ditandakan dalam fail.

Perekodan berkalibrasi memerlukan bundel kalibrasi kilang sensor. Untuk DAQ-U dan DAQ-M, backend memuat turun bundel daripada awan MAPIR mengikut ID sensor (perekodan akan ditolak jika ia tidak dapat); unit DAQ-E dikecualikan kerana mereka menyimpan kalibrasi mereka pada peranti.

## Perekodan daripada GUI

Perekodan dalam GUI memerlukan **projek dibuka** (butang Rakam akan dilumpuhkan jika tidak):

* **Rakam Semua / Hentikan Semua** — di bahagian atas bar sisi Penderia Cahaya; memulakan atau menghentikan perekodan `.daq` pada setiap penderia yang disambungkan sekaligus.
* **Rakam / Hentikan Rakaman** — bagi setiap sensor, dalam modal tetapan gear. Penunjuk "REC" merah akan dipaparkan dalam baris maklumat langsung sensor semasa rakaman.

Fail ditulis ke `<project>/light_sensor/`, dan apabila rakaman dihentikan — sama ada melalui Hentikan, Hentikan Semua, atau menyahpasang sensor rakaman — `.daq` yang siap **secara automatik ditambah ke projek terbuka**. Ia akan muncul dalam senarai fail projek tanpa langkah penambahan manual, sudah sedia untuk pemprosesan pantulan.

<!-- SCREENSHOT-NEEDED: Light Sensors tab with one DAQ sensor connected and recording: sidebar showing the red "Stop All" state of the Record All button, the sensor row, and the settings modal open with the red "REC" indicator visible in the live info rows. -->

<!-- SCREENSHOT-NEEDED: File Browser / project file list immediately after stopping a DAQ recording, showing the .daq file auto-added to the open project alongside imagery. -->



## Rakaman daripada CLI



CLI merakam melalui kolam sensor backend (backend mesti sedang berjalan — arahan-arahan ini adalah klien HTTP ringan):

```bash
# Connect the sensor into the backend pool
chloros-cli daq pool-connect --eth-host daq-e-def330.local

# Record for 150 seconds, with a human-friendly device label
chloros-cli daq pool-record --sensor-id daq-e-def330 --duration 150 \
    -o ./out --device-name "rooftop-A"

# Or run open-ended and stop explicitly
chloros-cli daq pool-record --sensor-id daq-e-def330            # --duration defaults to 0 = run until --stop
chloros-cli daq pool-record --sensor-id daq-e-def330 --stop
```

Dapatkan nilai `--sensor-id` daripada `chloros-cli daq pool-list`. Dua tetapan lalai yang perlu diketahui:

| Pilihan | Lalai |
| --- | --- |
| `--duration` | `0` — rekod sehingga `pool-record --stop` |
| `--output` / `-o` | `~/Documents/DAQ Live View/` pada sistem fail **backend**, bukan pada CLI |

Perbezaan direktori output menjadi penting apabila CLI mensasarkan backend pada mesin lain: fail itu akan berada di lokasi di mana backend dijalankan. Dengan backend pada komputer yang sama, `-o` relatif (seperti `./out` di atas) diselesaikan terhadap folder di mana anda menjalankan perintah.

## Rakaman daripada Python

`DAQSensorSession` (dipulangkan oleh `chloros_sdk.connect_daq_sensor()`) mendedahkan rakaman berkongsi yang sama: `record_start(output_dir=None, device_name=None)` mengembalikan laluan fail, `record_stop()` mengembalikan `{path, rows}`. Rujuk [Rujukan [SDK]](../reference/sdk-reference.md) untuk API sesi penuh. Kelas perkakasan terus SDK (pemasangan desktop sahaja) menulis rakaman ke `~/Documents/DAQ/` secara lalai; untuk binaan yang dikeluarkan, laluan berkumpulan di atas adalah laluan yang disokong.

## Menggunakan .daq semasa pemprosesan

Untuk menghasilkan pantulan daripada imej, Chloros memerlukan sinaran menuruni yang sepadan dengan setiap pendedahan:

* **Simpan `.daq` bersama imej.**Pada masa pemprosesan, saluran pemprosesan menyelesaikan**radiasi menuruni yang sepadan cap masa** secara automatik daripada rakaman `.daq` yang direkod (apa-apa model DAQ) — atau daripada `.csv` asli DAQ-M — yang ditemui bersama imej. Rakaman GUI memenuhi ini secara automatik, kerana ia ditambah ke dalam projek sebaik sahaja ia dihentikan.
* **Penentukuran dimuat apabila diperlukan.** Jika bundel penentukuran kilang bagi setiap kamera atau DAQ belum di-cache secara tempatan, Chloros akan memuat turunnya secara automatik daripada awan MAPIR pada penggunaan pertama kali (perlu internet sekali sahaja; di-cache di bawah `~/.chloros/`).
* **Rakaman langsung menulis fail sidecar mereka sendiri.** Untuk mana-mana bingkai pantulan yang dirakam secara langsung, bacaan DAQ yang sebenarnya digunakan disimpan sebagai fail `.daq` dalam folder `daq/` rakaman tersebut, supaya rakaman itu boleh diproses semula kemudian tanpa rakaman asal.
* **Rakaman daripada perakam `chloros_scripts` (1.3.0).**Satu `.daq` yang ditulis oleh perakam awam [`chloros_scripts`](https://github.com/mapircamera/chloros_scripts) disenaraikan sebagai model DAQ yang membuatnya, dan penutupnya boleh ditetapkan dalam**Penyetelan Projek → DAQ Penderia Cahaya** seperti rakaman lain. Rakaman itu menulis kiraan penderia mentah; Chloros memuat turun penentukuran kilang penderia mengikut nombor siri dan menerapkannya.
* **Siapa yang membacanya (1.3.0).**Di sebelah setiap tangkapan `.daq`, `.daq.cameras.json` menamakan kamera(s) yang bingkai-bingkainya diterangkan oleh bacaan itu, dan pemprosesan memadankannya**hanya dengan imej daripada kamera-kamera tersebut**. Sebuah kamera berdiri sendiri yang tiada penderia cahaya sendiri oleh itu tidak lagi meminjam bacaan kamera lain — ikat penderia padanya, atau rakam satu. Rakaman yang dibuat dengan DAQ itu sendiri (GUI Record, `daq pool-record`), dan mana-mana `.daq` tanpa fail tersebut (termasuk rakaman lama), masih menyokong setiap kamera.

## Memperoleh semula iradiasi

Pemprosesan projek juga mengeksport setiap rakaman penderia cahaya yang dimilikinya, ke dalam
folder `Light Sensor/` di sebelah produk imej. Ini **tidak** memerlukan imej: penderia cahaya yang dirakam bersendirian adalah tangkapan lengkap, dan folder yang hanya mengandungi fail `.daq` adalah input yang sah. Sebuah projek yang hanya mengandungi rakaman penderia cahaya menyenaraikannya dan memprosesnya sama ada dari aplikasi mahupun CLI, dan senarai tersebut kekal selepas pelaksanaan. Pelaksanaan itu melaporkan berapa banyak produk penderia cahaya yang ditulisnya.

| Produk | Apa itu |
| --- | --- |
| `<name>_calibrated.daq` | Satu arkib yang boleh diproses semula dalam skema yang sama seperti rakaman langsung, kini menyatakan bundel penentukuran yang menjana ia. Mengimportnya semula **tidak** menentukurnya buat kali kedua. |
| `<name>_calibrated.csv` | Iradiasi spektral dalam W/m²/nm pada grid panjang gelombang sensor itu sendiri, satu baris bagi setiap bacaan, ditambah lajur fotometrik: kuasa total, lux fotopik dan skotopik, PPFD dengan pembahagian biru/hijau/merah, dan panjang gelombang puncak. |

DAQ-U atau DAQ-M yang bundel penentukurannya tidak dapat diambil — anda tidak dalam talian, atau
penderia itu tiada penentukuran dalam fail — akan **dilangkau dengan sebab**, tidak pernah ditulis
sebagai fail "telah ditalar" yang mengandungi kiraan mentah. Sambungkan ke internet dan jalankan semula. DAQ-E menyimpan penentukuran sendiri, jadi ia hanya memerlukannya apabila unit tidak disambungkan dan tiada apa-apa yang disimpan secara tempatan.

### DAQ-A: kiraan mentah, dan mengapa itulah jawapan yang betul

**DAQ-A** wujud sebelum sistem bundel penentukuran mengikut siri dan tiada bundel untuk diambil. Itu bukan satu kecuaian: DAQ-A dikalibrasi di lapangan terhadap sasaran pantulan, dan kalibrasi berasaskan sasaran hanya memerlukan tindak balas *relatif* penderia — yang memang tepat apa yang terdapat dalam kiraan mentahnya. Chloros mengkalibrasi dengannya hari ini.

Jadi, rakaman DAQ-A dieksport, tetapi di bawah nama yang berbeza:

```
<project>/
└── Light Sensor/
    ├── <name>_raw.daq
    └── <name>_raw.csv
```

`_raw`, bukan `_calibrated` — nama fail yang berbeza dan bukannya penanda di dalam fail, kerana tuntutan itu perlu kekal walaupun fail tersebut dihantar melalui e-mel sebagai nama kosong. Header `.csv` menyatakan `raw spectral sensor counts (NOT irradiance)` dan memberi amaran bahawa nilai-nilai tersebut boleh dibandingkan **dalam** fail itu dan bukan merentas penderia. Lajur yang hanya bermakna bagi iradiasi sebenar — kuasa total, lux, PPFD — dibiarkan kosong daripada dikira daripada kiraan.

Rakaman DAQ-A-SD yang lebih lama (skema v1.01 / v1.02) hanya merekodkan masa penulisan fail, bukan cap masa bagi setiap bacaan. Chloros tidak akan memadankan imej dengan yang tersebut — memadankan satu bingkai dengan masa penulisan akan menjadi salah tanpa pernah kelihatan salah — tetapi eksport membacanya dengan baik dan CSV menyatakan jam mana ia digunakan.

Untuk cerita pantulan penuh — sensor tunggal dengan kamera, dan sensor berganda persekitaran/objek — lihat [Aliran Kerja Pantulan](reflectance.md).
