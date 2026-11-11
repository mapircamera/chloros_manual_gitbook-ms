# Profil Topi &amp; Julat Terkalibrasi

> Penutup itu sendiri — penutup mana dihantar dengan penderia mana, bagaimana ia dipasang, dan tingkah laku optiknya — didokumenkan dalam **[manual pengguna DAQ](https://mapir.gitbook.io/daq)**. Halaman ini menerangkan cara *memberitahu* penutup yang dipasang kepada Chloros
, yang menjadikan pembetulan itu tepat.

Setiap penentukuran radiometrik kilang bagi penderia cahaya DAQ menerangkan penderia *tanpa penutup*. Penutup fizikal yang dipasang di atas penyebar mengubah cahaya yang dikumpul oleh penderia, jadi Chloros
menerapkan **profil pembetulan penutup** yang diukur di kilang di atas pakej penentukuran. Menyatakan topi yang betul adalah sebahagian daripada mendapatkan data yang dikalibrasi — halaman ini menerangkan topi yang wujud bagi setiap model, cara untuk menyatakannya, dan apakah julat spektral terkalibrasi sebenar bagi penderia itu.

## Ketersediaan penutup mengikut model

| Profil penutup (`cap_id`) | Penutup fizikal | DAQ-U | DAQ-M | DAQ-E |
| --- | --- | --- | --- | --- |
| `sunshine_cosine` | Penutup pembetul kosinus cahaya matahari (**lalai pada setiap model**) | Ya | Ya | Ya |
| `fov_15` / `fov_45` / `fov_90` | Kon pengekang FOV (15° / 45° / 90°) | Ya | — | Ya |
| `fov_30` / `fov_60` | Kon pembatas FOV (30° / 60°) | Ya | — | — |
| `none` | Tiada penutup dipasang | — | — | Ya |

Nota khusus model:

* **DAQ-M mempunyai satu profil penutup: `sunshine_cosine`.** Bare-plus-Sunshine-cap adalah definisi produknya, dan DAQ-M kosong tidak memerlukan profil geometri.
* **DAQ-U kosong adalah benar-benar kosong** — ia langsung tidak memerlukan profil geometri, itulah sebabnya tiada profil `none` wujud untuknya.
* **`none` pada DAQ-E TIDAK merupakan no-op.** Diffuser DAQ-E yang terbenam dan dilapisi kaca mempunyai pembetulan geometri sebenar tersendiri, jadi &quot;tanpa penutup&quot; itu sendiri merupakan profil yang diukur pada model ini.
* DAQ-E tanpa penutup **tidak dapat mengukur cahaya matahari langsung pada apa jua ketinggian** — penutup Sunshine adalah konfigurasi lapangan. Jangan merancang kerja luar menggunakan DAQ-E tanpa penutup.

Dalam tetapan setiap penderia GUI (ikon gear dalam tab Penderia Cahaya), menu lungsur **Cap** juga menawarkan &quot;Tiada (penderia kosong)&quot; pada DAQ-U dan DAQ-M — pada kedua-dua model tersebut, &quot;kosong&quot; hanya bermaksud tiada pembetulan topi digunakan, seperti yang dinyatakan dalam nota di atas. Pilihnya hanya apabila penutupnya telah tanggal secara fizikal.

## Menyatakan penutup — dan mengapa ia penting

**`cap_id` yang dinyatakan mesti sepadan dengan penutup yang terpasang secara fizikal pada penderia.** Sensor mahupun perisian tidak dapat mengesan penutup yang dipasang. Penyataan ini mempengaruhi dua perkara:

1. **Pembetulan secara langsung** yang digunakan pada setiap spektrum.
2. **Cap penutup yang ditulis ke dalam setiap rakaman `.daq`**, yang dipercayai oleh pemprosesan pantulan seterusnya.

Penutup Sunshine memudarkan kira-kira **12× mengikut reka bentuk**, jadi merakam dengan penutup yang dinyatakan salah akan menykala semula spektra dengan faktor yang sama. Nyatakan perubahan penutup dengan segera.

### Menetapkan penutup

GUI: Tab Light Sensors → ikon gear pada baris penderia → menu lungsur **Cap**. Lalai untuk setiap model ialah `sunshine_cosine`, satu andaian (DAQ-U, DAQ-M dan DAQ-E dihantar tanpa cap, dan rakaman menganggap cap telah ditetapkan sehingga anda memilih satu), dan pilihan ini kekal dengan projek.



CLI<!-- SCREENSHOT-NEEDED: DAQ tab per-sensor settings modal (gear icon) scrolled to the Cap dropdown, open to show the per-model choices with "Sunshine (cosine corrector)" selected. Use a connected DAQ-E so the Hostname/Firmware/PTP rows are also visible above it. -->
(backend mesti sedang berjalan):

```bash
# Declare at connect time
chloros-cli daq pool-connect --eth-host daq-e-def330.local --cap-id sunshine_cosine

# Swap at runtime (after physically changing the cap)
chloros-cli daq pool-set-cap --sensor-id daq-e-def330 --cap-id fov_45
```

CLI
menerima keseluruhan senarai `cap_id` secara sintaksis (`{none, fov_15, fov_30, fov_45, fov_60, fov_90, sunshine_cosine}`); setiap profil disahkan terhadap model sensor semasa sambungan, jadi ID topi yang tidak tersedia (contohnya ID E-only pada DAQ-U) akan gagal dengan ralat jelas dan bukannya diperbetulkan secara salah. Lalai backend apabila tiada apa-apa yang dihantar ialah `sunshine_cosine`.

PythonSDK
: namakan cap pada masa sambungan dengan `connect_daq_sensor(cap_id="sunshine_cosine")`, atau tukarnya semasa runtime dengan `DAQSensorSession.set_cap("fov_45")`; lihat [Rujukan SDK
](../reference/sdk-reference.md#daq-sensor-sessions).

Lanjutan: profil dihantar dalam pemasangan `daq/cap_profiles/<u|m|e>/<cap_id>.json` dan boleh diimbangi oleh pengguna di `~/.chloros/daq_cap_profiles/<u|m|e>/<cap_id>.json`.

Selain daripada penapis, sensor yang tidak pernah dikalibrasi semula secara automatik menerima penambahbaikan dark-offset kecil yang diperoleh daripada armada — tiada sebarang tindakan pengguna diperlukan.

## DAQ-E-S: penyebar tetap dan kecenderungan

**DAQ-E-S** adalah sebuah DAQ-E dengan dua perbezaan yang penting bagi Chloros
. Segala perkara lain (penemuan, penyambungan, PTP, rakaman, tetapan peranti) berfungsi dengan tepat seperti untuk DAQ-E.

* **Penghamburnya adalah kekal, jadi ia tidak memerlukan pembetulan topi.** Penghambur telah dipasang pada unit semasa pengesahan kilangannya diukur, jadi pengesahan itu sudah membaca iradiasi sinaran ke bawah yang sebenar. Chloros
tidak menerapkan sebarang profil topi padanya tidak kira apa yang diminta: mana-mana topi yang dinamakan untuknya akan diabaikan dengan mesej, rakaman-rakamannya menyatakan topi itu berasal daripada model, dan amaran topi-andaian tidak akan muncul untuknya. Dalam Tetapan Projek → Penderia Cahaya DAQ, barisnya tidak menunjukkan sebarang penutup, dan penimbalan penutup di seluruh projek tidak menyentuhnya. Jangan terapkan profil penutup pada datanya secara manual: pembetulan Sinar Matahari di atasnya akan membaca kira-kira 11× lebih tinggi.
* **Ia dilengkapi penderia pecut 3-paksi.** Tindak balas kosinus penyebar hanya sah selagi ia rata, jadi sensor yang condong melaporkan iradiasi yang salah tanpa sebarang simptom lain. Setiap bacaan DAQ-E-S oleh itu membawa kemiringan, putaran dan condongannya, dan `.daq` yang dirakam daripadanya menyimpannya di sebelah setiap spektrum (`imu_tilt_deg`, `imu_roll_deg`, `imu_pitch_deg`, …), jadi anda boleh membezakan awan daripada dudukan yang terhentak.

Untuk setiap medan sikap dan bagaimana ralat dalam aliran sikap dilaporkan, lihat [DAQ-E-S dalam Rujukan CLI
](../reference/cli-reference.md#daq-e-s--fixed-diffuser-and-tilt-sensor).

## Prestasi topi cahaya matahari (konfigurasi luar)

Nilai yang boleh dijadikan asas prosedur:

| Sifat | Nilai |
| --- | --- |
| Sudut pandangan | Hemisfera 180° |
| Ralat tindak balas kosinus | ≤ ±4 % sehingga 60° sudut insiden; ≤ ±4.5 % sehingga 70° |
| Had matahari rendah | Tidak disyorkan di bawah ~15° ketinggian matahari |
| Pelemahan | ~12× (mengikut reka bentuk) |
| Ulang-ulik pemasangan semula penutup | ≈ 1.5 % |
| Iradiasi kuantitatif | Purata bacaan **≥ 15 saat** (ciri instrumen, bukan kecacatan) |

Untuk mana-mana nombor iradiasi kuantitatif — termasuk rujukan pantulan — gunakan purata bacaan sekurang-kurangnya 15 saat dan bukannya satu bingkai.

## Julat spektral yang dikalibrasi

| Sifat | Nilai |
| --- | --- |
| Pengambilan sampel spektral | 340–1010 nm pada selang 5 nm (135 titik) |
| Julat kalibrasi radiometrik | **~374–974 nm** (dipaksakan dalam perisian) |

Penderia melaporkan keseluruhan grid 340–1010 nm, tetapi penguatan radiometrik yang boleh dijejaki NIST merangkumi ~374–974 nm. Chloros
**menolak pembahagian pantulan mutlak** untuk mana-mana jalur kamera yang kurang daripada separuh berat spektralnya berada dalam julat tersebut, melaporkan sebab langganan `dls-uncalibrated-band-<nm>` dan bukannya menghasilkan produk yang tidak dikalibrasi. Antara SKU kamera yang dihantar, hanya penapis F988 jatuh di luar julat ini; ia sebaliknya menggunakan aliran kerja panel pantulan — lihat [Aliran Kerja Pantulan](reflectance.md).

Untuk model sensor, pengangkut, dan ID sensor, lihat [Rangkuman DAQ](README.md). Untuk maklumat tentang bagaimana cap topi digunakan semasa pemprosesan, lihat [Perekodan &amp; Format .daq](recording.md).
