# Rakaman &amp; Format .daq

Fail `.daq` ialah format rakaman penderia cahaya daripadaChloros

: satu **pangkalan data SQLite** yang mengandungi bingkai spektral yang telah dilaras daripada satu penderia DAQ. Rakam satu daripadanya semasa sesi tangkapan dan saluran pemantulan boleh kemudian membahagikan setiap imej dengan iradiasi ke bawah yang diukur pada saat yang tepat itu.

## Kandungan .daq

| Sifat | Nilai |
| --- | --- |
| Kontena | Pangkalan data SQLite, satu fail bagi setiap penderia bagi setiap rakaman |
| Nama fail | Mengandungi **ID penderia**dan**cap masa**, contohnya `daq_data_daq-e-def330_2026_04_13_18h30m00.daq` |
| Spektrum setiap bingkai | 135 titik, 340–1010 nm pada selang 5 nm, ditambah tristimulasi CIE XYZ |
| Unit | Iradiasi spektral yang dikalibrasi, **W/m²/nm** (bungkusan kalibrasi kilang + profil cap diterapkan) |
| Metadata bercop | ID Sensor (kunci untuk mendapatkan penentukuran kilang unit tersebut) dan profil topi yang digunakan — lihat [Profil Topi &amp; Julat Dikalibrasi](caps-and-range.md) |

Format adalah sama di merata DAQ-U, DAQ-M, dan DAQ-E, jadi pemprosesan susulan tidak mengambil berat pengangkut mana yang merakamnya.

Perekodan yang dikalibrasi memerlukan bundel kalibrasi kilang penderia. Untuk DAQ-U dan DAQ-M, backend memuat turun bundel daripada awanMAPIR

berdasarkan ID penderia (perekodan akan ditolak jika gagal); unit DAQ-E dikecualikan kerana mereka menyimpan kalibrasi mereka pada peranti.

## Perekodan daripada GUI

Perekodan dalam GUI memerlukan **projek dibuka** (butang Rakam akan dilumpuhkan jika tidak):

* **Rakam Semua / Hentikan Semua** — di bahagian atas bar sisi Penderia Cahaya; memulakan atau menghentikan perekodan `.daq` pada setiap penderia yang disambungkan sekaligus.
* **Rakam / Hentikan Rakaman** — bagi setiap sensor, dalam modal tetapan gear. Penunjuk &quot;REC&quot; merah akan dipaparkan dalam baris maklumat langsung sensor semasa rakaman.

Fail ditulis ke `<project>/light_sensor/`, dan apabila rakaman dihentikan — sama ada melalui Hentikan, Hentikan Semua, atau menyahpasang sensor rakaman — `.daq` yang siap **secara automatik ditambah ke projek terbuka**. Ia muncul dalam senarai fail projek tanpa langkah penambahan manual, sudah sedia untuk pemprosesan pantulan.

<!-- SCREENSHOT-NEEDED: Light Sensors tab with one DAQ sensor connected and recording: sidebar showing the red "Stop All" state of the Record All button, the sensor row, and the settings modal open with the red "REC" indicator visible in the live info rows. -->

<!-- SCREENSHOT-NEEDED: File Browser / project file list immediately after stopping a DAQ recording, showing the .daq file auto-added to the open project alongside imagery. -->



## Rakaman daripadaCLI



CLI

merakam melalui kolam sensor backend (backend mesti sedang berjalan — arahan-arahan ini adalah klien nipisHTTP

):

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
| `--output` / `-o` | `~/Documents/DAQ Live View/` pada sistem fail **backend**, bukan padaCLI

|

Perbezaan direktori keluaran penting apabilaCLI

mensasarkan backend pada mesin lain: fail itu berada di lokasi di mana backend dijalankan.

## Rakaman daripadaPython

`DAQSensorSession` (dipulangkan oleh `chloros_sdk.connect_daq_sensor()`) mendedahkan rakaman berkongsi yang sama: `record_start(output_dir=None, device_name=None)` memulangkan laluan fail, `record_stop()` memulangkan `{path, rows}`. Rujuk [RujukanSDK

](../reference/sdk-reference.md) untukAPI

sesi penuh. Kelas perkakasan langsungSDK

(pemasangan desktop sahaja) menulis rakaman ke `~/Documents/DAQ/` secara lalai; untuk binaan yang dikeluarkan, laluan berkongsi di atas adalah laluan yang disokong.

## Menggunakan .daq semasa pemprosesan

Untuk menghasilkan pantulan daripada imej,Chloros

memerlukan sinaran menuruni yang sepadan dengan setiap pendedahan:

* **Simpan `.daq` bersama imej tersebut.**Pada masa pemprosesan, saluran paip menyelesaikan**radiasi menuruni yang sepadan cap masa** secara automatik daripada `.daq` yang dirakam (apa-apa model DAQ) — atau daripada `.csv` asli DAQ-M — yang ditemui bersama imej. Rakaman GUI memenuhi ini secara automatik, kerana ia ditambah ke dalam projek sebaik sahaja ia dihentikan.
* **Kalibrasi dimuat mengikut permintaan.** Jika bundel kalibrasi kilang bagi setiap kamera atau DAQ belum disimpan secara tempatan,Chloros

akan memuatkannya secara automatik daripada awanMAPIR

pada penggunaan pertama (perlu internet sekali; disimpan di bawah `~/.chloros/`).
* **Sambutan langsung menulis fail sampingan sendiri.** Bagi mana-mana bingkai pantulan yang dirakam secara langsung, bacaan DAQ yang sebenarnya digunakan disimpan sebagai fail sampingan `.daq` di sebelah imej, supaya sambutan tersebut boleh diproses semula kemudian tanpa rakaman asal.

## Memperoleh semula iradiasi

Pemprosesan projek juga mengeksport setiap rakaman penderia cahaya yang disimpannya, ke dalam
folder `Light Sensor/` di sebelah produk imej. Ini **tidak** memerlukan imej: penderia cahaya yang diterbangkan bersendirian adalah tangkapan lengkap, dan folder yang mengandungi hanya fail `.daq` adalah input yang sah. Proses tersebut melaporkan berapa banyak produk penderia cahaya yang ditulisnya.

| Produk | Apa itu |
| --- | --- |
| `<name>_calibrated.daq` | Satu arkib yang boleh diproses semula dalam skema yang sama seperti rakaman langsung, kini menyatakan bundel penentukuran yang menjana ia. Mengimportnya semula **tidak** akan menentukurnya buat kali kedua. |
| `<name>_calibrated.csv` | Radiasi spektral dalam W/m²/nm pada grid panjang gelombang sensor itu sendiri, satu baris bagi setiap bacaan, ditambah lajur fotometrik: kuasa total, lux fotopik dan skotopik, PPFD dengan pembahagian biru/hijau/merah, dan panjang gelombang puncak. |

DAQ-U atau DAQ-M yang bundel kalibrasinya tidak dapat diambil — anda tidak dalam talian, atau
penderia itu tiada kalibrasi dalam fail — akan **dilangkau dengan sebab**, tidak pernah ditulis
sebagai fail &quot;telah dikalibrasi&quot; yang mengandungi kiraan mentah. Sambungkan ke internet dan jalankan semula. DAQ-E menyimpan penentukuran sendiri, jadi ia hanya memerlukannya apabila unit tidak disambungkan dan tiada apa-apa yang disimpan secara tempatan.

### DAQ-A: kiraan mentah, dan mengapa itulah jawapan yang betul

**DAQ-A** wujud sebelum sistem bundel kalibrasi mengikut siri dan tiada bundel untuk diambil. Itu bukan satu kelalaian: DAQ-A dikalibrasi di lapangan terhadap sasaran pantulan, dan kalibrasi berasaskan sasaran hanya memerlukan tindak balas *relatif* penderia — yang memang tepat apa yang dikira sebagai kiraan mentah.Chloros

mengkalibrasi dengannya hari ini.

Jadi, rakaman DAQ-A dieksport, tetapi di bawah nama yang berbeza:

```
<project>/
└── Light Sensor/
    ├── <name>_raw.daq
    └── <name>_raw.csv
```

`_raw`, bukan `_calibrated` — nama fail yang berbeza dan bukannya penanda di dalam fail, kerana tuntutan itu perlu kekal walaupun fail tersebut dihantar melalui e-mel sebagai nama kosong. Header `.csv` menyatakan `raw spectral sensor counts (NOT irradiance)` dan memberi amaran bahawa nilai-nilai tersebut boleh dibandingkan **dalam** fail itu dan bukan merentas sensor. Lajur yang hanya bermakna untuk iradiasi sebenar — kuasa total, lux, PPFD — dibiarkan kosong daripada dikira daripada kiraan.

Rakaman DAQ-A-SD yang lebih lama (skema v1.01 / v1.02) hanya merekodkan masa penulisan fail, bukan cap masa bagi setiap bacaan.Chloros

tidak akan memadankan imej dengan catatan tersebut — memadankan satu bingkai dengan masa penulisan adalah salah tanpa pernah kelihatan salah — tetapi eksport membacanya dengan baik danCSV

menyatakan jam mana yang digunakan.

Untuk kisah pantulan penuh — sensor tunggal dengan kamera, dan sensor berganda persekitaran/objek — lihat [Aliran Kerja Pantulan](reflectance.md).
