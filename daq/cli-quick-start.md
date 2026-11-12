# CLI Permulaan Pantas (pool-*)

Pemacu `chloros-cli` yang disertakan mengendalikan sensor DAQ melalui keluarga arahan **`daq pool-*`** — klien HTTP nipis yang mengendalikan sensor melalui kolam sensor berterusan backend Chloros. Backend memiliki pengangkutan, jadi GUI, CLI dan skrip SDK semuanya berkongsi satu pemegang langsung (live handle) dan bukannya berebut port. Segala yang diperlukan pelanggan boleh diakses melalui `pool-*`: sambung, streaming, rakam fail `.daq` yang telah dikalibrasi, dan tukar profil cap.

`pool-*` adalah permukaan DAQ yang disokong dalam binaan yang dikeluarkan. `chloros-cli daq --help` menyenaraikan subperintah `pool-*`, dan `daq discover` / `daq list` menjalankan imbasan yang sama seperti `pool-discover`. Apabila subperintah DAQ perkakasan terus tidak tersedia, ia akan keluar dengan ralat eksplisit yang menyatakan pakej yang hilang dan mengarahkan anda semula ke `pool-*` — tiada apa yang gagal secara senyap. (`pip install chloros-sdk` juga tidak menyediakan arahan perkakasan terus.)

***

## Prasyarat

* **Backend Chloros mesti sedang berjalan** — arahan `pool-*` adalah klien HTTP, bukan pemacu perkakasan. Pada Windows, mulakan aplikasi desktop Chloros (ia memulakan backend). Pada Linux/Jetson tanpa papan kekal, aktifkan perkhidmatan sebagai pengguna anda: `sudo systemctl enable --now chloros-backend@$USER.service` (lihat [Pemasangan Linux](../linux/linux-installation.md#always-on-ptp-for-headless-hosts)).
* **Log masuk**Chloros+ (tingkat berbayar)**: jalankan `chloros-cli login` terlebih dahulu. Penguatkuasaan adalah di pihak pelayan — tanpa log masuk, arahan gagal dengan `401 AUTH_REQUIRED`; pada peringkat percuma (Iron), ia gagal dengan `403 PLAN_UPGRADE_REQUIRED`.
* Perintah-perintah menyasarkan `http://127.0.0.1:5000` secara lalai; keluarga `daq pool-*` menghormati pembolehubah persekitaran `CHLOROS_BACKEND_URL` jika backend anda berjalan di tempat lain.

***

## Sesi lima minit

```bash
# 1. Connect a sensor into the backend pool (pick the line matching your model)
chloros-cli daq pool-connect                                  # smart-detect any DAQ
chloros-cli daq pool-connect --port COM3                      # DAQ-U on a specific COM port
chloros-cli daq pool-connect --mac AA:BB:CC:DD:EE:FF          # DAQ-M by BLE MAC
chloros-cli daq pool-connect --eth-host daq-e-def330.local    # DAQ-E by hostname (reliable)
#    (a DAQ-M's BLE MAC is not printed anywhere: find it with `chloros-cli daq pool-discover --only ble`)

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
| `--frame-avg N` / `-f N` | Rata-rata bingkai bagi setiap spektrum yang dilaporkan. |
| `--no-ae` | Nyahdayakan pendedahan automatik (AE diaktifkan secara lalai). |
| `--no-stream` | Sambung tanpa memulakan aliran (lanjutan kemudian dengan `pool-stream --start`). |
| `--cap-id CAP` | Profil pembetulan cap; lalai backend ialah `sunshine_cosine`. Lihat [`pool-set-cap`](#pool-set-cap-declare-the-fitted-cap). |

Menyambungkan DAQ-E yang sudah ada dalam kolam di bawah alamat lain — nama hos `daq-e-<id>.local` atau IPnya — akan mengembalikan entri kolam sedia ada dan bukannya membuka unit tersebut buat kali kedua.

{% hint style="warning" %}
**Perlu diingat tentang penemuan automatik `--eth`.** Pada hos berbilang sambungan (lebih daripada satu antara muka rangkaian aktif), `pool-connect --eth` *pertama* selepas but boleh menjadi kosong walaupun sensor sihat — carian imbasan mungkin terlepas antara muka sensor sementara cache ARP belum diisi. Jika `--eth` tidak menemui apa-apa, cuba semula, atau langkau penemuan sepenuhnya dengan `--eth-host <ip-or-hostname>`, yang merupakan laluan yang boleh dipercayai pada mesin berbilang sambungan. Nama hos DAQ-E ialah `daq-e-<id>.local` (contohnya `daq-e-def330.local`); IP tulennya juga boleh digunakan.
{% endhint %}

## `pool-discover` — imbas tanpa menyambung

```bash
chloros-cli daq pool-discover                  # USB, BLE and Ethernet
chloros-cli daq pool-discover --only ble       # BLE only: this is how you find a DAQ-M's MAC
chloros-cli daq pool-discover --json           # machine-readable
```

Mengimbas sensor yang boleh anda sambungkan dan memaparkan alamat untuk disambungkan, tanpa membuat sambungan. `--only` mengambil subset `usb,ble,eth` yang dipisahkan dengan koma; `--timeout SEC` menetapkan masa imbasan bagi setiap pengangkut ( lalai 5 s, dihadkan kepada 1–20 s). Penderia yang sudah dibuka dalam kolam tidak disenaraikan (DAQ-M yang disambungkan berhenti mengiklankan) — gunakan `pool-list` untuk itu. Pada binaan yang dihantar, `chloros-cli daq discover` dan `daq list` menjalankan imbasan yang sama ini.

## `pool-list` — lihat apa yang disambungkan

Menunjukkan setiap sensor dalam kolam backend, termasuk `sensor_id` yang diperlukan oleh setiap arahan lain:

| Model | Bentuk `sensor_id` | Contoh |
| --- | --- | --- |
| DAQ-U / DAQ-M | 5-octet hyphenated | `CB-7C-A8-2E-5F` |
| DAQ-E | `daq-e-<6 hex digits>` | `daq-e-def330` |

DAQ-E yang PTP-nya telah dimatikan (tetapan disimpan pada unit) akan ditandakan dalam `pool-list` dengan `!! PTP is switched OFF on this unit (saved in its flash): its timestamps are not PTP-synced.` dan arahan untuk menghidupkannya semula — lihat [`pool-enable-ptp`](#pool-enable-ptp-switch-a-daq-es-ptp-back-on).

## `pool-latest` — baca bingkai spektrum

```bash
chloros-cli daq pool-latest --sensor-id daq-e-def330 --recent 10 --json
```

Mengembalikan bingkai terkini, atau bingkai `--recent N` terkini; `--json` mengeluarkan keluaran boleh dibaca oleh mesin untuk pengskripan. Rangka disukat secara radiometrik sebagai irradiasi spektral (W/m²/nm) pada grid 135 titik, 340–1010 nm, dengan profil topi sensor telah digunakan. Untuk nombor iradiasi kuantitatif, puratakan sekurang-kurangnya 15 saat bingkai — ini adalah ciri instrumen, bukan kecacatan.

## `pool-stream` — hentikan seketika atau sambung penstriman

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
| `--output DIR` / `-o DIR` | `~/Documents/DAQ Live View/` | Direktori keluaran. Dengan backend pada komputer ini, laluan relatif diselesaikan berdasarkan folder di mana anda menjalankan arahan; dengan backend jauh, laluan diselesaikan **pada mesin yang menjalankan backend**. |
| `--device-name NAME` | — | Label yang disimpan bersama rakaman. |
| `--stop` | — | Hentikan rakaman yang sedang dijalankan. |
| `--confirm-after SEC` | `20` | Selepas memulakan, tunggu sehingga panjang ini untuk baris pertama ditulis, dan beri amaran dengan kuat (`!! WARNING: no rows written after …s. This recording is producing an EMPTY file.`) jika tiada yang sampai. `0` menyahdayakan pemeriksaan. |
| `--heartbeat SEC` | `30` | Dengan `--duration`, cetak kiraan baris setiap SEC saat dan beri amaran jika ia berhenti meningkat. `0` melumpuhkan ia. |

{% hint style="info" %}
Perekodan berlaku di backend, jadi fail `.daq` disimpan di sistem fail **mesin backend** — secara lalai di `~/Documents/DAQ Live View/` di sana, bukan semestinya di tempat anda menjalankan CLI. Nama fail merangkumi ID penderia dan cap masa.
{% endhint %}

## `pool-set-cap` — nyatakan penutup yang dipasang

```bash
chloros-cli daq pool-set-cap --sensor-id daq-e-def330 --cap-id sunshine_cosine
```

ID topi memilih profil pembetulan yang diukur di kilang yang digunakan pada setiap spektrum, dan ia **mesti sepadan dengan topi yang dipasang secara fizikal pada sensor** — sama ada sensor mahupun perisian tidak dapat mengesan topi itu sendiri, dan pilihan tersebut dicop ke dalam setiap fail `.daq`. Larasannya di mana-mana ialah `sunshine_cosine` (setiap DAQ dihantar dengan topi pembetul kosinus Sunshine dipasang, peredaman ~12× mengikut reka bentuk — pertukaran topi yang tidak dinyatakan akan membetul spektra secara salah dengan anggaran faktor tersebut).

| `--cap-id` | Tersedia pada |
| --- | --- |
| `sunshine_cosine` (lalai) | DAQ-U, DAQ-M, DAQ-E |
| `fov_15`, `fov_45`, `fov_90` | DAQ-U, DAQ-E |
| `fov_30`, `fov_60` | DAQ-U sahaja |
| `none` | DAQ-E sahaja — lihat nota |

ID penutup di luar set sensor akan ditolak semasa sambungan dengan ralat jelas. `none` (DAQ-E) bermaksud penutupnya telah tanggal secara fizikal — ia masih menerapkan profil geometri kilang untuk penyebar kaca yang terbenam pada DAQ-E, jadi ia bukan no-op, dan DAQ-E tanpa penutup adalah konfigurasi bangku, bukan konfigurasi lapangan yang disokong. (DAQ-U kosong benar-benar kosong dan tidak memerlukan sebarang profil pembetulan; DAQ-M digunakan dengan penutup Sunshine-nya.)

## `pool-disconnect` — lepaskan sensor

```bash
chloros-cli daq pool-disconnect --sensor-id daq-e-def330   # one sensor
chloros-cli daq pool-disconnect --all                      # everything in the pool
```

`pool-disconnect` membebaskan sensor yang dibuka oleh sesiapa sahaja, jadi ia juga membebaskan sensor yang ditinggalkan dalam kolam oleh skrip yang rosak (perekodan ke atasnya akan dihabiskan terlebih dahulu). Sensor yang tidak berada dalam kolam tiada apa-apa untuk dilepaskan: arahan mencetak `<sensor-id>: already released (not in the pool)` dan keluar `0`. Pelepasan yang ditolak atau tidak disahkan oleh backend akan keluar `1`.

## `pool-enable-ptp` — menghidupkan semula PTP DAQ-E

```bash
chloros-cli daq pool-enable-ptp --sensor-id daq-e-def330
```

DAQ-E menyimpan tetapan PTP hidup/mati dalam memorinya sendiri, jadi unit yang PTP-nya dimatikan kekal mati — dan cap waktunya tidak diselaraskan dengan PTP — sehingga ia dihidupkan semula. `pool-list` menandakan unit sedemikian dan mencetak arahan ini untuknya. Chloros tidak pernah menghidupkan semula unit itu sendiri, kerana matikan boleh disengajakan. Baharu dalam 1.3.0.

## Tetapan peranti DAQ-E

```bash
chloros-cli daq pool-info --sensor-id daq-e-def330
chloros-cli daq pool-set-network --sensor-id daq-e-def330 \
  --static 192.168.1.50 --gateway 192.168.1.1
chloros-cli daq pool-set-network --sensor-id daq-e-def330 --auto
chloros-cli daq pool-set-auto-stream --sensor-id daq-e-def330 --on
chloros-cli daq pool-reboot --sensor-id daq-e-def330
chloros-cli daq pool-update-firmware --sensor-id daq-e-def330
```

`pool-info` memaparkan firmware sensor (dan sama ada keluaran Chloros ini membawa versi yang lebih baru), alamatnya dan bagaimana ia diperoleh, tetapan rangkaian yang disimpan selagi ia berbeza daripada yang sedang digunakan, penstriman semasa kuasa dihidupkan dan PTP. `pool-set-network` memulakan semula penderia untuk menerapkan perubahan dan menyambung semula dengan tetapan yang ada; `--no-reboot` hanya menyimpannya. Alamat statik mesti berada dalam rangkaian komputer ini, jika tidak Chloros akan menolaknya; `--force` menafikan itu, hanya untuk komputer yang akan dialamatkan semula atau dirut ke alamat tersebut. Permulaan semula ditolak semasa penderia sedang merekod. `pool-update-firmware` ialah **Update to \<version\>**</version\> GUI <version\>untuk baris perintah, dan cara untuk mengemas kini DAQ-E pada Linux, yang tiada GUI. Baharu dalam 1.3.0.

***

## Ringkasan arahan

| Arahan | Tujuan |
| --- | --- |
| `daq pool-connect [--port P \| --ble \| --mac M \| --eth \| --eth-host H] [-t MS] [-f N] [--no-ae] [--no-stream] [--cap-id CAP]` | Buka sensor dalam kolam backend. |
| `daq pool-discover [--only usb,ble,eth] [--timeout SEC] [--json]` | Imbas untuk sensor tanpa menyambung (menemui MAC BLE DAQ-M). |
| `daq pool-list` | Tunjukkan setiap sensor dalam kolam dengan `sensor_id` mereka. |
| `daq pool-latest --sensor-id ID [--recent N] [--json]` | N bingkai spektrum yang dikalibrasi terkini. |
| `daq pool-stream --sensor-id ID [--start \| --stop]` | Sambung / jeda penstriman. |
| `daq pool-record --sensor-id ID [-d SEC] [-o DIR] [--device-name NAME] [--confirm-after SEC] [--heartbeat SEC] [--stop]` | Mulakan / hentikan rakaman `.daq` (di pihak backend). |
| `daq pool-set-cap --sensor-id ID --cap-id CAP` | Tukar profil pembetulan kapasiti semasa runtime. |
| `daq pool-disconnect --sensor-id ID [--all]` | Lepaskan satu penderia, atau kesemuanya. |
| `daq pool-enable-ptp --sensor-id ID` | Hidupkan semula PTP untuk DAQ-E yang PTP-nya dimatikan. |
| `daq pool-info --sensor-id ID [--json]` | Firmware, alamat, tetapan rangkaian yang disimpan, penstriman semasa kuasa dihidupkan dan PTP bagi DAQ-E. |
| `daq pool-set-network --sensor-id ID (--auto \| --static IP [--mask M] [--gateway G]) [--no-reboot] [--force]` | DHCP atau alamat statik; memulakan semula dan menyambung semula sensor. |
| `daq pool-set-auto-stream --sensor-id ID (--on \| --off)` | Menyalurkan data semasa kuasa-hidup. |
| `daq pool-reboot --sensor-id ID` | Mulakan semula DAQ-E dan sambungkannya semula. |
| `daq pool-update-firmware --sensor-id ID` | Flash firmware DAQ-E keluaran ini ke dalam sensor yang lebih lama. |

***

## Penyelesaian masalah sambungan DAQ-E pertama

1. DAQ-E tidak mempunyai LED status — sahkan kuasa melalui penunjuk PoE/pautan pada port suis atau penyuntik, dan biarkan beberapa saat selepas dihidupkan supaya ia boleh but dan menyertai rangkaian.
2. Mesin backend mesti berada di **domain siaran yang sama** seperti sensor — mDNS tidak menembusi penghala.
3. Firewall mesti membenarkan mDNS (UDP 5353), data DAQ-E (UDP 5002) dan PTP (UDP 319/320) masuk. Pemasang Windows menambah peraturan untuk ini, dan `.deb` pada Linux membuka port tersebut dalam `ufw` / `firewalld` yang aktif semasa pemasangan; firewall pihak ketiga, atau yang diaktifkan kemudian, perlu membenarkannya.
4. Masih tiada apa-apa daripada `--eth`? Gunakan `--eth-host` dengan nama hos (`daq-e-<id>.local`) atau IP unit, yang akan melangkau penemuan.

***
{% hint style="info" %}
**Petua untuk pembantu AI.** Setiap halaman manual ini disajikan sebagai Markdown mentah — sambungkan `.md` kepada slug URL huruf kecil pada URL (halaman ini: `https://mapir.gitbook.io/chloros/daq/cli-quick-start.md`); indeks yang boleh dibaca mesin ialah `https://mapir.gitbook.io/chloros/llms.txt`. Untuk dokumentasi lengkap pada peringkat bendera bagi `chloros-cli daq` dan setiap keluarga arahan lain, muat turun [Rujukan CLI](../reference/cli-reference.md) (`https://mapir.gitbook.io/chloros/reference/cli-reference.md`); jalur Python ialah `chloros_sdk.connect_daq_sensor()` dalam [Rujukan SDK](../reference/sdk-reference.md).
{% endhint %}
</version\>
