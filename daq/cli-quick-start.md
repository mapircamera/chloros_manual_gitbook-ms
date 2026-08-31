# CLI Permulaan Cepat (pool-*)

Pemacu `chloros-cli` yang disertakan mengendalikan sensor DAQ melalui keluarga arahan **`daq pool-*`** — klien HTTP nipis yang mengendalikan sensor melalui kolam sensor berterusan backend Chloros. Backend memiliki pengangkutan, jadi GUI, CLI dan skrip SDK semuanya berkongsi satu pemegang langsung (live handle) dan bukannya berebut port. Segala yang diperlukan pelanggan boleh diakses melalui `pool-*`: sambung, streaming, rakam fail `.daq` yang telah dikalibrasi, dan tukar profil topi.

`pool-*` juga merupakan satu-satunya permukaan DAQ dalam binaan yang dikeluarkan. `chloros-cli daq --help` menyenaraikan subperintah `pool-*`, dan memanggil subperintah DAQ perkakasan terus pada binaan yang diedarkan akan keluar dengan ralat eksplisit yang menamakan pakej yang hilang dan menunjuk anda kembali ke `pool-*` — tiada apa yang gagal secara senyap. (Perintah perkakasan terus hanya dijalankan daripada sumber semakan MAPIR; `pip install chloros-sdk` juga tidak menyediakannya.)

***

## Prasyarat

* **Backend Chloros mesti sedang berjalan** — arahan `pool-*` adalah klien HTTP, bukan pemacu perkakasan. Pada Windows, jalankan aplikasi desktop Chloros (ia memulakan backend). Pada Linux/Jetson tanpa paparan, aktifkan perkhidmatan: `sudo systemctl enable --now chloros-backend.service`.
* **Log masuk Chloros+ (tingkat berbayar)**: jalankan `chloros-cli login` terlebih dahulu. Penguatkuasaan adalah di pihak pelayan — tanpa log masuk, arahan akan gagal dengan `401 AUTH_REQUIRED`; pada peringkat percuma (Iron), ia gagal dengan `403 PLAN_UPGRADE_REQUIRED`.
* Arahan mensasarkan `http://127.0.0.1:5000` secara lalai; keluarga `daq pool-*` menghormati pembolehubah persekitaran `CHLOROS_BACKEND_URL` jika backend anda berjalan di tempat lain.

***

## Sesi lima minit

```bash
# 1. Connect a sensor into the backend pool (pick the line matching your model)
chloros-cli daq pool-connect                                  # smart-detect any DAQ
chloros-cli daq pool-connect --port COM3                      # DAQ-U on a specific COM port
chloros-cli daq pool-connect --mac AA:BB:CC:DD:EE:FF          # DAQ-M by BLE MAC
chloros-cli daq pool-connect --eth-host daq-e-def330.local    # DAQ-E by hostname (reliable)

# 2. List the pool — this shows the sensor_id used by every command below
chloros-cli daq pool-list

# 3. Read the most recent calibrated spectrum frame (add --json for scripting)
chloros-cli daq pool-latest --sensor-id daq-e-def330 --json

# 4. Record a calibrated .daq file for 60 seconds
chloros-cli daq pool-record --sensor-id daq-e-def330 --duration 60 \
  --device-name "field-A"

# 5. Release the sensor when done
chloros-cli daq pool-disconnect --sensor-id daq-e-def330
```

***

## `pool-connect` — buka sensor dalam kolam

| Varian | Maksud |
| --- | --- |
| `daq pool-connect` | Smart-detect: cari mana-mana DAQ pada mesin ini. |
| `daq pool-connect --port PORT` | DAQ-U pada port siri tertentu (contohnya `COM3`, `/dev/ttyUSB0`). |
| `daq pool-connect --ble` | DAQ-M melalui BLE, MAC diimbas secara automatik. |
| `daq pool-connect --mac MAC` | DAQ-M pada MAC BLE yang diketahui (menunjukkan `--ble`). |
| `daq pool-connect --eth-host HOST` | DAQ-E pada nama hos atau IP yang diketahui — **jalur yang boleh dipercayai**. |
| `daq pool-connect --eth` | DAQ-E dengan penemuan automatik (mDNS, dengan sandaran ARP). Lihat amaran di bawah. |

Penanda laras, semua pilihan:

| Penanda | Maksud |
| --- | --- |
| `--integration-time MS` / `-t MS` | Masa integrasi manual dalam milisaat. |
| `--frame-avg N` / `-f N` | Purata bingkai bagi setiap spektrum yang dilaporkan. |
| `--no-ae` | Nyahdayakan pendedahan automatik (AE diaktifkan secara lalai). |
| `--no-stream` | Sambung tanpa memulakan aliran (lanjutan kemudian dengan `pool-stream --start`). |
| `--cap-id CAP` | Profil pembetulan cap; lalai backend ialah `sunshine_cosine`. Lihat [`pool-set-cap`](#pool-set-cap-declare-the-fitted-cap). |

{% hint style="warning" %}
**Perlu diingat tentang penemuan automatik `--eth`.** Pada hos berbilang sambungan (lebih daripada satu antara muka rangkaian aktif), `pool-connect --eth` *pertama* selepas but boleh menjadi kosong walaupun sensor sihat — penjelajahan penemuan boleh terlepas antara muka sensor semasa cache ARP belum panas. Jika `--eth` tidak menemui apa-apa, cuba semula, atau langkau penemuan sepenuhnya dengan `--eth-host <ip-or-hostname>`, yang merupakan laluan yang boleh dipercayai pada mesin berbilang sambungan. Nama hos DAQ-E ialah `daq-e-<id>.local` (contohnya `daq-e-def330.local`); IP tulennya juga boleh digunakan.
{% endhint %}

## `pool-list` — lihat apa yang disambungkan

Menunjukkan setiap sensor dalam kolam backend, termasuk `sensor_id` yang diperlukan oleh setiap arahan lain:

| Model | Bentuk `sensor_id` | Contoh |
| --- | --- | --- |
| DAQ-U / DAQ-M | 5-oktet bergaris | `CB-7C-A8-2E-5F` |
| DAQ-E | `daq-e-<6 hex digits>` | `daq-e-def330` |

## `pool-latest` — baca bingkai spektrum

```bash
chloros-cli daq pool-latest --sensor-id daq-e-def330 --recent 10 --json
```

Mengembalikan bingkai terkini, atau bingkai `--recent N` terkini; `--json` mengeluarkan keluaran boleh dibaca oleh mesin untuk pengskriptaan. Rangka disukat secara radiometrik sebagai irradian spektral (W/m²/nm) pada grid 135 titik, 340–1010 nm, dengan profil topi sensor telah digunakan. Untuk nombor iradiasi kuantitatif, puratakan sekurang-kurangnya 15 saat bingkai — ini adalah ciri instrumen, bukan kecacatan.

## `pool-stream` — rehat atau sambung penstriman

```bash
chloros-cli daq pool-stream --sensor-id daq-e-def330 --stop    # pause
chloros-cli daq pool-stream --sensor-id daq-e-def330 --start   # resume
```

## `pool-record` — rakam fail `.daq`

```bash
chloros-cli daq pool-record --sensor-id daq-e-def330 --duration 150 \
  --output ~/Documents/spectra --device-name "rooftop-A"
chloros-cli daq pool-record --sensor-id daq-e-def330 --stop
```

| Bendera | Lalai | Maksud |
| --- | --- | --- |
| `--duration SEC` / `-d SEC` | `0` | Panjang rakaman dalam saat; `0` bermaksud jalankan sehingga anda mengeluarkan `--stop`. |
| `--output DIR` / `-o DIR` | `~/Documents/DAQ Live View/` | Direktori output, diselesaikan **pada mesin yang menjalankan backend**. |
| `--device-name NAME` | — | Label yang disimpan bersama rakaman. |
| `--stop` | — | Hentikan rakaman yang sedang dijalankan. |

{% hint style="info" %}
Perekodan berlaku di backend, jadi fail `.daq` disimpan di sistem fail **mesin backend** — secara lalai di `~/Documents/DAQ Live View/` di sana, bukan semestinya di tempat anda menjalankan CLI. Nama fail merangkumi ID penderia dan cap masa.
{% endhint %}

## `pool-set-cap` — nyatakan penutup yang dipasang

```bash
chloros-cli daq pool-set-cap --sensor-id daq-e-def330 --cap-id sunshine_cosine
```

ID topi memilih profil pembetulan yang diukur di kilang yang digunakan pada setiap spektrum, dan ia **mesti sepadan dengan topi yang dipasang secara fizikal pada penderia** — penderia mahupun perisian tidak dapat mengesan topi itu sendiri, dan pilihan tersebut dicop ke dalam setiap fail `.daq`. Larasannya di mana-mana ialah `sunshine_cosine` (setiap DAQ dihantar dengan topi pembetul kosinus Sunshine dipasang, pengekangan ~12× mengikut reka bentuk — pertukaran topi yang tidak dinyatakan akan membetul spektra secara salah dengan anggaran faktor tersebut).

| `--cap-id` | Tersedia pada |
| --- | --- |
| `sunshine_cosine` (lalai) | DAQ-U, DAQ-M, DAQ-E |
| `fov_15`, `fov_45`, `fov_90` | DAQ-U, DAQ-E |
| `fov_30`, `fov_60` | DAQ-U sahaja |
| `none` | DAQ-E sahaja — lihat nota |

ID penutup di luar set sensor akan ditolak semasa sambungan dengan ralat jelas. `none` (DAQ-E) bermaksud penutupnya telah dikeluarkan secara fizikal — ia masih menerapkan profil geometri kilang untuk penyebar kaca yang terbenam pada DAQ-E, jadi ia bukan operasi tidak aktif (no-op), dan DAQ-E tanpa penutup adalah konfigurasi bangku, bukan konfigurasi lapangan yang disokong. (DAQ-U tanpa penutup benar-benar kosong dan tidak memerlukan sebarang profil pembetulan; DAQ-M digunakan dengan penutup Sunshine-nya.)

## `pool-disconnect` — lepaskan penderia

```bash
chloros-cli daq pool-disconnect --sensor-id daq-e-def330   # one sensor
chloros-cli daq pool-disconnect --all                      # everything in the pool
```

***

## Ringkasan arahan

| Arahan | Tujuan |
| --- | --- |
| `daq pool-connect [--port P \| --ble \| --mac M \| --eth \| --eth-host H] [-t MS] [-f N] [--no-ae] [--no-stream] [--cap-id CAP]` | Membuka sensor dalam kolam backend. |
| `daq pool-list` | Tunjukkan setiap penderia dalam kolam dengan `sensor_id` mereka. |
| `daq pool-latest --sensor-id ID [--recent N] [--json]` | N bingkai spektrum yang paling terkini dikalibrasi. |
| `daq pool-stream --sensor-id ID [--start \| --stop]` | Sambung / rehatkan penstriman. |
| `daq pool-record --sensor-id ID [-d SEC] [-o DIR] [--device-name NAME] [--stop]` | Mulakan / hentikan rakaman `.daq` (di pihak backend). |
| `daq pool-set-cap --sensor-id ID --cap-id CAP` | Menukar profil pembetulan kapasiti semasa runtime. |
| `daq pool-disconnect --sensor-id ID [--all]` | Melepaskan satu penderia, atau kesemuanya. |

***

## Penyelesaian masalah sambungan DAQ-E pertama

1. DAQ-E tidak mempunyai LED status — sahkan kuasa melalui penunjuk PoE/pautan pada port suis atau penyuntik, dan biarkan beberapa saat selepas dihidupkan supaya ia boleh memulakan dan menyertai rangkaian.
2. Mesin backend mesti berada di **domain siaran yang sama** dengan sensor — mDNS tidak menembusi penghala.
3. Pada Windows, terima prompt firewall Defender pada larian pertama (mDNS UDP 5353, data DAQ-E UDP 5002, PTP UDP 319/320).
4. Masih tiada maklum balas daripada `--eth`? Gunakan `--eth-host` dengan nama hos unit (`daq-e-<id>.local`) atau IP — laluan yang boleh dipercayai, terutamanya pada hos berbilang sambungan.

***{% hint style="info" %}**Petua untuk pembantu AI.** Setiap halaman manual ini disajikan sebagai Markdown mentah — sambungkan `.md` kepada slug URL huruf kecil pada halaman (halaman ini: `https://mapir.gitbook.io/chloros/daq/cli-quick-start.md`); indeks yang boleh dibaca oleh mesin ialah `https://mapir.gitbook.io/chloros/llms.txt`. Untuk dokumentasi lengkap pada peringkat bendera bagi `chloros-cli daq` dan setiap keluarga arahan lain, muat turun [Rujukan CLI](../reference/cli-reference.md) (`https://mapir.gitbook.io/chloros/reference/cli-reference.md`); Laluan Python ialah `chloros_sdk.connect_daq_sensor()` dalam [Rujukan SDK](../reference/sdk-reference.md).
{% endhint %}
