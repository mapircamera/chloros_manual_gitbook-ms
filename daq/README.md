# Penderia Cahaya DAQ

> **Mencari perkakasan?**Penderia itu sendiri — model, pemasangan, penutup, port, kuasa dan aplikasi SCANNER — didokumenkan dalam**[manual pengguna DAQ](https://mapir.gitbook.io/daq)**. Bab ini merangkumi penggunaan mereka daripada Chloros. Penderia cahaya**DAQ**

MAPIRmengukur cahaya persekitaran sebagai spektra yang dikalibrasi secara radiometrik. Dalam Chloros mereka menjalankan dua peranan:

* **Peranti spektral berdiri sendiri** — carta spektrum langsung, data kolorimetrik, dan rakaman `.daq`, semuanya daripada [Light Sensors tab](gui.md), [CLI](cli-quick-start.md), atau PythonSDK.
* **Sumber sinaran ke bawah untuk pantulan** — semasa pemprosesan, Chloros menginterpolasi bacaan `.daq` anda kepada setiap cap masa pendedahan tangkapan dan menggunakan cahaya sinaran bawah yang diukur untuk menukarkan radiasi kamera kepada pantulan (`--reflectance-source daq`), tanpa memerlukan panel dalam adegan untuk jalur yang dikalibrasi.



<!-- SCREENSHOT-NEEDED: product photo of the DAQ-U, DAQ-M, and DAQ-E units side by side, each with its Sunshine cosine-corrector cap fitted (request from hardware team — no repo asset exists) -->***

## Tiga model, satu format data

| Model | Pengangkutan | Penemuan |
| --- | --- | --- |
| **DAQ-U** | USB (siri) | imbasan port siri |
| **DAQ-M** | Tenaga Rendah Bluetooth | imbasan BLE mengikut nama |
| **DAQ-E** | Ethernet (IPv4, berkuasa PoE) | mDNS `_daq-e._tcp` (nama hos `daq-e-<id>.local`) |

Ketiganya menggunakan protokol wayar yang sama dan menyampaikan data yang identik:

* Satu spektrum **135-titik dari 340 hingga 1010 nm pada selang 5 nm**, ditambah nilai tristimulus CIE XYZ, dalam setiap bingkai.
* **Radiasi spektral yang dikalibrasi secara radiometrik dalam W/m²/nm** — pek kalibrasi kilang bagi setiap unit (ditambah profil pembetulan penutup aktifnya) digunakan sebelum data sampai kepada anda.
* Format rakaman **`.daq`** yang sama (fail SQLite). Pemprosesan susulan adalah identik tanpa mengira pengangkutan mana yang menghasilkan fail tersebut.

Susunan penghantaran (USB serial, BLE, mDNS/zeroconf) dibundel dalam backend Chloros — tiada apa-apa yang perlu dipasang untuk berkomunikasi dengan mana-mana tiga model sama ada dari GUI atau arahan `pool-*` pada CLI.

***

## Julat kalibrasi: dilaporkan 340–1010 nm, ~374–974 nm dikalibrasi

Penderia melaporkan keseluruhan grid 340–1010 nm, tetapi keuntungan radiometrik yang boleh dijejaki NIST merangkumi kira-kira **374–974 nm**. Chloros menolak pembahagian pantulan mutlak untuk mana-mana jalur kamera dengan kurang daripada separuh berat spektralnya berada dalam julat yang dikalibrasi itu; jalur yang dilangkau dilaporkan dengan sebab pelompatan `dls-uncalibrated-band-<nm>`.

Antara SKU penapis LATTICE yang dihantar, hanya **F988** terjejas:

Reflektan F988 dikalibrasi menggunakan panel reflektansi di dalam adegan: jalur itu terletak di luar julat kalibrasi penderia cahaya DAQ, jadi Chloros menggunakan tangkapan panel terkini anda dan menyimpannya di antara penglihatan panel.

Jika tangkapan F988 diproses dengan hanya data DAQ tersedia, Chloros menolak reflektansi berasaskan DAQ untuk jalur tersebut dengan sebab lompatan `dls-uncalibrated-band-988` — [aliran kerja panel reflektansi](../calibration-targets.md) adalah laluan yang disokong untuk F988.

***

## ID Sensor

Setiap DAQ melaporkan ID sensor yang stabil. Bentuknya berbeza mengikut model:

| Model | Bentuk ID | Contoh |
| --- | --- | --- |
| DAQ-U | 5-oktet bersambung dengan tanda hubung | `CB-7C-A8-2E-5F` |
| DAQ-M | 5-oktet bersambung dengan tanda hubung | `CB-74-02-30-6B` |
| DAQ-E | `daq-e-<6 hex digits>` | `daq-e-def330` |

ID penderia ialah:

* dicop ke dalam setiap fail `.daq` yang dirakamnya,
* kunci yang digunakan oleh Chloros untuk mendapatkan bundel penentukuran kilang unit tersebut,
* nilai yang anda hantar kepada `--sensor-id` dalam arahan CLI `pool-*`, dan
* untuk DAQ-E, juga nama hos mDNS-nya (`daq-e-def330.local`) — nilai yang diterima oleh `--eth-host`.

***

## Kalibrasi kilang dan awan

Setiap unit DAQ dikalibrasi secara individu di kilang menggunakan rantaian radiometrik yang boleh dijejaki ke NIST, dan Chloros memuatkan bundel kalibrasi setiap unit yang dikaitkan dengan ID penderia masing-masing. Laporan penentukuran bagi setiap unit (PDF) boleh dimuat turun daripada tetapan penderia dalam [tab Penderia Cahaya](gui.md).

{% hint style="warning" %}
**DAQ-U dan DAQ-M memerlukan akses awan untuk penentukuran.**Tiada satu pun model ini menyimpan apa-apa di dalam peranti: pek penentukuran kilang mereka disimpan di dalam awan MAPIR dan diambil mengikut ID sensor (kemudian disimpan sementara secara tempatan). Chloros memerlukan sambungan internet untuk menyampaikan data W/m²/nm yang telah ditentukur daripada DAQ-U atau DAQ-M.**DAQ-E adalah pengecualian** — ia menyimpan penentukuran pada peranti itu sendiri.
{% endhint %}


<!-- PRE-PUBLISH-CHECK: LAUNCH item 3 (DAQ-M end-to-end connect smoke) was still unverified as of 2026-08-16 — re-confirm the DAQ-M cloud-calibration flow on the release build before publishing this page. -->***

## Tempat rakaman disimpan

| Permukaan | Destinasi `.daq` lalai |
| --- | --- |
| GUI — tab Penderia Cahaya | `<project folder>/light_sensor/` (rakaman yang selesai ditambah ke dalam projek secara automatik) |
| CLI — `daq pool-record` | `~/Documents/DAQ Live View/` pada mesin yang menjalankan backend |

Setiap nama fail `.daq` merangkumi ID sensor dan cap masa.

***

## Dalam bab ini

* [**Tetingkap DAQ dalam Chloros**](gui.md) — panduan GUI lengkap: menyambungkan setiap model, tetapan bagi setiap sensor, carta spektrum, data kolorimetrik secara langsung, pantulan dua-sensor, dan rakaman.
* [**Permulaan Cepat CLI (pool-\*)**](cli-quick-start.md) — memandu sensor DAQ daripada `chloros-cli daq pool-*`, laluan baris perintah yang disokong.
* [**Profil Cap &amp; Julat Dikalibrasi**](caps-and-range.md) — cap yang wujud bagi setiap model, cara mengisytiharkannya, dan julat spektral yang dikalibrasi secara terperinci.
* [**Perekodan &amp; Format .daq**](recording.md) — format SQLite `.daq` dan aliran kerja perekodan.
* [**Rangkaian DAQ-E &amp; Penyelarasan Masa**](ethernet-ptp.md) — mod pengangkutan DAQ-E dan penyelarasan masa PTP.
* [**Aliran Kerja Reflektan**](reflectance.md) — menggunakan data DAQ downwelling untuk menghasilkan reflektan.
* Untuk dokumentasi peringkat bendera yang lengkap, lihat [Rujukan CLI](../reference/cli-reference.md) (seksyen `chloros-cli daq`) dan [Rujukan SDK](../reference/sdk-reference.md) (`chloros_sdk.connect_daq_sensor()`), kedua-duanya ditulis untuk digunakan terus oleh pembantu AI.
