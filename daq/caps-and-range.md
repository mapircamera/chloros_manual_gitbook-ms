# Profil Topi &amp; Julat Terkalibrasi

> Penutup itu sendiri — penutup mana dihantar dengan sensor mana, bagaimana ia dipasang, dan tingkah laku optiknya — didokumenkan dalam **[manual pengguna DAQ](https://mapir.gitbook.io/daq)**. Halaman ini menerangkan cara *memberitahu* penutup yang dipasang kepadaChloros

, yang menjadikan pembetulan itu tepat.

Setiap penentukuran radiometrik kilang bagi penderia cahaya DAQ menerangkan penderia *tanpa penutup*. Penutup fizikal yang dipasang di atas penyebar mengubah cahaya yang dikumpul oleh penderia, jadiChloros

menerapkan **profil pembetulan penutup** yang diukur di kilang di atas pakej penentukuran. Menyatakan topi yang betul adalah sebahagian daripada mendapatkan data yang dikalibrasi — halaman ini menerangkan topi yang wujud bagi setiap model, cara menyatakannya, dan apakah julat spektral terkalibrasi sebenar bagi penderia tersebut.

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
* **`none` pada DAQ-E BUKAN no-op.** Diffuser DAQ-E yang terbenam dan dilapisi kaca mempunyai pembetulan geometri sebenar tersendiri, jadi &quot;tiada penutup&quot; itu sendiri adalah profil yang diukur pada model ini.
* DAQ-E tanpa penutup **tidak boleh mengukur cahaya matahari langsung pada apa jua ketinggian** — penutup Sunshine adalah konfigurasi lapangan. Jangan merancang kerja luar menggunakan DAQ-E tanpa penutup.

Dalam tetapan setiap penderia GUI (ikon gear dalam tab Penderia Cahaya), menu lungsur **Cap** juga menawarkan &quot;Tiada (penderia kosong)&quot; pada DAQ-U dan DAQ-M — pada kedua-dua model itu, &quot;kosong&quot; hanya bermaksud tiada pembetulan topi digunakan, mengikut nota di atas. Pilihnya hanya apabila penutupnya telah tanggal secara fizikal.

## Menyatakan penutup — dan mengapa ia penting

**`cap_id` yang dinyatakan mesti sepadan dengan penutup yang terpasang secara fizikal pada penderia.** Sensor mahupun perisian tidak dapat mengesan penutup yang dipasang. Penyataan ini mempengaruhi dua perkara:

1. **Pembetulan secara langsung** yang digunakan pada setiap spektrum.
2. **Cap penutup yang ditulis ke dalam setiap rakaman `.daq`**, yang dipercayai oleh pemprosesan pantulan seterusnya.

Penutup Sunshine meredam cahaya kira-kira **12× mengikut reka bentuk**, jadi merekod dengan penutup yang salah yang diisytiharkan akan menyebabkan spektra terskala salah pada faktor tersebut. Nyatakan perubahan penutup dengan segera.

### Menetapkan penutup

GUI: tab Penderia Cahaya → ikon gear pada baris penderia → menu lungsur **Cap**. Lalai untuk setiap model ialah `sunshine_cosine` (semua penderia DAQ dihantar dengan pembetul kosinus dipasang), dan pilihan ini kekal dengan projek.



CLI<!-- SCREENSHOT-NEEDED: DAQ tab per-sensor settings modal (gear icon) scrolled to the Cap dropdown, open to show the per-model choices with "Sunshine (cosine corrector)" selected. Use a connected DAQ-E so the Hostname/Firmware/PTP rows are also visible above it. -->

(backend mesti sedang berjalan):

```bash
# Declare at connect time
chloros-cli daq pool-connect --eth-host daq-e-def330.local --cap-id sunshine_cosine

# Swap at runtime (after physically changing the cap)
chloros-cli daq pool-set-cap --sensor-id daq-e-def330 --cap-id fov_45
```

CLI

menerima keseluruhan senarai `cap_id` secara sintaksis (`{none, fov_15, fov_30, fov_45, fov_60, fov_90, sunshine_cosine}`); setiap profil disahkan terhadap model sensor semasa sambungan, jadi ID topi yang tidak tersedia (contohnya ID E-only pada DAQ-U) akan gagal dengan ralat jelas dan bukannya membetulkan secara salah. Lalai backend apabila tiada apa-apa yang dihantar ialah `sunshine_cosine`.

Python

SDK

Nota: `cap_id` **bukan** tombolSDK

— `connect_daq_sensor()` / `DAQSensorSession` tidak mendedahkan sebarang parameter cap. Pilih cap melalui arahanCLI

di atas atau menu lungsur GUI; lihat [RujukanSDK

](../reference/sdk-reference.md).

Lanjutan: profil dihantar bersama pemasanganChloros

di `daq/cap_profiles/<u|m|e>/<cap_id>.json` dan boleh diimbangi semula bagi setiap pengguna di `~/.chloros/daq_cap_profiles/<u|m|e>/<cap_id>.json`.

Selain daripada penutup, penderia yang tidak pernah dikalibrasi semula secara automatik menerima penambahbaikan gelap-pengeseran kecil yang diperoleh daripada armada — tiada tindakan pengguna terlibat.

## Prestasi penutup cahaya matahari (konfigurasi luar)

Nombor yang boleh anda gunakan untuk membina prosedur:

| Harta | Nilai |
| --- | --- |
| Lapangan pandangan | Hemisfera 180° |
| Ralat tindak balas kosinus | ≤ ±4 % sehingga 60° sudut insiden; ≤ ±4.5 % sehingga 70° |
| Had matahari rendah | Tidak disyorkan di bawah ~15° ketinggian matahari |
| Pelemahan | ~12× (mengikut reka bentuk) |
| Ulangan pemasangan semula penutup | ≈ 1.5 % |
| Iradiasi kuantitatif | Purata bacaan **≥ 15 saat** (ciri instrumen, bukan kecacatan) |

Untuk mana-mana nombor iradiasi kuantitatif — termasuk rujukan pantulan — gunakan purata bacaan sekurang-kurangnya 15 saat dan bukannya satu bingkai.

## Julat spektral yang dikalibrasi

| Sifat | Nilai |
| --- | --- |
| Pengambilan sampel spektral | 340–1010 nm pada selang 5 nm (135 titik) |
| Julat kalibrasi radiometrik | **~374–974 nm** (dipaksakan dalam perisian) |

Penderia melaporkan keseluruhan grid 340–1010 nm, tetapi penguatan radiometrik yang boleh dijejaki NIST merangkumi ~374–974 nm.Chloros

**menolak pembahagian reflektansi mutlak** untuk mana-mana jalur kamera yang kurang daripada separuh berat spektralnya berada dalam julat tersebut, melaporkan sebab langganan `dls-uncalibrated-band-<nm>` dan bukannya menghasilkan produk yang tidak dikalibrasi. Antara SKU kamera yang dihantar, hanya penapis F988 berada di luar julat ini; ia menggunakan aliran kerja panel pantulan sebaliknya — lihat [Aliran Kerja Pantulan](reflectance.md).

Untuk model sensor, pengangkut, dan ID sensor, lihat [Rangkuman DAQ](README.md). Untuk maklumat tentang bagaimana cap topi digunakan semasa pemprosesan, lihat [Perekodan &amp; Format .daq](recording.md).
