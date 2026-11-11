# Aliran Kerja Reflektan

Penderia cahaya DAQ menukarkan imej radiometrik kepada reflektan. Terdapat dua aliran kerja yang berbeza:

1. **Sensor tunggal** — satu DAQ mengukur iradiasi menuruni sementara kamera merakam, dan Chloros
membahagikan sinaran kamera dengan rujukan tersebut.
2. **Dua-sensor** — dua sensor DAQ, satu memantau langit dan satu lagi memantau objek, menghasilkan lengkung pantulan spektral secara langsung tanpa melibatkan kamera.

## Satu sensor + kamera (rujukan sinaran ke bawah)

DAQ berfungsi sebagai sensor cahaya sinaran ke bawah (DLS): kamera mengukur radiasi menaik **L**(W/m²/sr/nm), DAQ mengukur radiasi menuruni**E** (W/m²/nm), dan Chloros
mengira pantulan bagi setiap jalur sebagai:

> ρ = π · L / E

Bacaan DAQ sentiasa **dipasangkan cap masa dengan pendedahan** — ini adalah sebab DAQ dan kamera berkongsi jam yang dikawal oleh PTP (lihat [DAQ-E Networking &amp; Time Sync](ethernet-ptp.md)). Pasang topi cosine cosinus Sunshine untuk kerja di luar dan nyatakannya dengan betul; penyataan topi secara langsung menskala E (lihat [Profil Topi &amp; Julat Dikalis](caps-and-range.md)). Untuk kerja kuantitatif, ingat ciri instrumen: iradiasi kuantitatif datang daripada purata bacaan sekurang-kurangnya 15 saat.

### Rakaman langsung

Ikatan DAQ kepada kamera dalam tab Kamera: setiap panel tetapan kamera mempunyai menu lungsur **Penderia Cahaya** yang menyenaraikan setiap DAQ yang disambungkan (DAQ-U/M/E) daripada tab Penderia Cahaya; untuk susunan bersepadu, pemilihan Penderia Cahaya merentasi susunan akan disebarkan ke setiap ahlinya (kamera individu masih boleh menimpa). Setelah diikat, spektra penderia membekalkan slot DLS kamera dan eksport pantulan dibahagikan dengan bacaan yang sepadan. Dalam satu susunan, satu ahli dengan penderia sendiri menggunakan bacaan sendiri — untuk jalur gelombang dalam komposit langsung, eksportnya dan pendedahan automatik — dan ahli-ahli lain menggunakan bacaan susunan tersebut.

<!-- SCREENSHOT-NEEDED: Cameras tab per-camera settings panel showing the "Light Sensor" dropdown open, with a connected DAQ sensor listed and selected. -->


Dua tingkah laku yang perlu diketahui:

* **Tiada DAQ yang diikat → reflektansi ditolak, bukan diada-adakan.** Chloros
menolak produk reflektansi dan merekodkan sebab terlepas daripada sekadar memulangkan produk yang kurang baik tanpa diberitahu.
* **Bacaan yang digunakan dikekalkan.** Untuk setiap bingkai pantulan, bacaan DAQ yang sebenarnya digunakan ditulis sebagai fail `.daq` dalam folder `daq/` rakaman, supaya rakaman itu boleh diproses semula kemudian ([Perekodan &amp; Format .daq](recording.md)). Satu `.daq.cameras.json` di sebelahnya menamakan kamera-kamera yang diterangkan, dan pemprosesan semula hanya memadankannya dengan imej kamera-kamera tersebut.

### Pemprosesan imej yang dirakam

Untuk pemprosesan pasca-penerbangan, rakam `.daq` semasa sesi dan simpan ia bersama imej — saluran paip menyelesaikan imej yang sepadan dengan cap masa ke bawah secara automatik, memuat turun sebarang kalibrasi kilang yang hilang daripada awan MAPIR
pada penggunaan pertama. Rakaman GUI ditambah ke projek terbuka secara automatik apabila ia berhenti.

Rujukan pantulan boleh dipilih semasa pemprosesan — `--reflectance-source` pada `chloros-cli process`, atau tetapan sumber pantulan dalam Tetapan Projek GUI:

| Nilai | Kelakuan |
| --- | --- |
| `auto` (lalai) | Sasaran penentukuran dalam bingkai yang lulus QA adalah rujukan mutlak; DAQ downwelling (ρ = π·L/E) adalah pilihan sekunder. Sasaran yang lulus QA dengan bendera (panel terjatuh, atau jalur tidak mengikut susunan) tidak secara automatik diutamakan: rantaian sasaran, sasaran+DAQ dan hanya-DAQ dicuba mengikut susunan skor yang diukur pada panel yang disisihkan. |
| `daq` | Berkuasa DAQ |
| `target` | Sasaran ketat dalam bingkai; tiada penggantian DAQ |

Lihat [Sasaran Kalibrasi](../calibration-targets.md) untuk aliran kerja sasaran dan [Bab LATTICE](../lattice/README.md) serta [Rujukan CLI
](../reference/cli-reference.md) untuk keseluruhan saluran pemprosesan. Apabila membaca piksel pantulan yang dieksport, gunakan skala yang dicop (LATTICE: 32768 = ρ 1.0, XMP `Chloros:PixelScale`; Survey3
: 65535) — lihat [Format Imej Keluaran](../output-image-formats.md).

### Jalur di luar julat kalibrasi DAQ

Julat kalibrasi radiometrik DAQ adalah ~374–974 nm. Chloros
menolak reflektansi berasaskan DAQ untuk mana-mana jalur kamera yang kurang daripada separuh berat spektralnya berada dalam julat tersebut, melaporkan sebab lompatan `dls-uncalibrated-band-<nm>`. Antara SKU yang sedang dihantar, ini hanya menjejaskan F988: pantulan F988 dikalibrasi menggunakan panel pantulan dalam adegan: jalur itu terletak di luar julat kalibrasi penderia cahaya DAQ, jadi Chloros
menggunakan tangkapan panel terkini anda dan menyimpannya di antara sesi penalaan panel. Jika kamera F988 dijalankan hanya dengan DAQ, Chloros
menolak pantulan berasaskan DAQ untuk jalur tersebut dengan sebab lompatan `dls-uncalibrated-band-988` — aliran kerja panel adalah laluan yang disokong.

## Sensor dwi (cahaya persekitaran + objek)

Dua penderia DAQ — mana-mana pasangan, merentasi mana-mana pengangkut — memberikan spektrum pantulan secara langsung tanpa kamera: satu penderia menghadap ke langit (**Sumber Cahaya Sekitar**), satu lagi menghadap ke objek (**Pemindai Objek**), dan Chloros
mengira bagi setiap panjang gelombang:

> R(λ) = object(λ) / ambient(λ)

(sifar di mana ambient ≤ 0).

### Dalam GUI

Apabila kedua-dua penderia disambungkan dalam tab Penderia Cahaya, buka lapisan tambah-penderia (butang &quot;+&quot; pada jubin carta dalam paparan grid) dan pilih **Gabungkan Cahaya Sekitar + Objek**. Pilih kedua-dua sensor dalam menu lungsur Sumber Cahaya Sekitar dan Pengimbas Objek dan klik Cipta. Kumpulan itu akan muncul sebagai carta tersendiri dan sebagai baris bar sisi dengan lencana hijau**REF**.

<!-- SCREENSHOT-NEEDED: The add-sensor overlay's "Combine Ambient + Object" panel with two connected DAQ sensors selected in the Ambient Light Source and Object Scanner dropdowns, Create button enabled. -->

<!-- SCREENSHOT-NEEDED: A live Apparent Reflectance chart from an Ambient+Object DAQ pair in list view, with the vegetation-index table visible below the chart (NDVI etc. showing live values). -->


Di bawah carta reflektansi (pemandangan senarai), satu **jadual indeks vegetasi langsung** mengira indeks daripada lengkung menggunakan pusat jalur pada biru 450 / hijau 550 / merah 670 / NIR
800 nm. Indeks berasaskan nisbah yang membatalkan skala mutlak (NDVI
, GNDVI
, ENDVI, WDRVI
, GRVI
, CVI
, GCI
, MSR) sentiasa dipaparkan; indeks yang memerlukan pantulan mutlak (EVI
, SAVI
, OSAVI
, GSAVI, GOSAVI, MSAVI2, RDVI, TDVI, LAI
, NLI, MNLI, FCI, GEMI
) hanya muncul apabila kedua-dua sensor adalah model yang dikalibrasi kuasa.

### Nampak berbanding Relatif — peraturan pelabelan

Chloros
melabel keluaran dua penderia berdasarkan apa yang sebenarnya boleh didakwa oleh pasangan penderia:

| Pasangan penderia | Label |
| --- | --- |
| Kedua-dua penderia dikalibrasi — bundel kilang dimuatkan | **Pantulan Nampak** |
| Mana-mana sensor tidak dikalibrasi | **Refleksan Relatif** |

Ketiga-tiga model ini bersifat radiometrik: sebaik sahaja bundel kalibrasi kilang sesuatu sensor dimuatkan, spektranya adalah mutlak W/m²/nm, jadi nisbah dua sensor yang dikalibrasi menghasilkan refleksan mutlak yang kelihatan — bukan pengangkutan yang menentukannya. Sensor yang masih menghantar kiraan mentah (tiada bundel dapat dicapai) akan menurunkan keputusan kepada lengkung relatif (bentuk spektral masih sah). Kedua-dua sensor harus mempunyai had yang diisytiharkan dengan betul ([Profil Had &amp; Julat Dikalibrasi](caps-and-range.md)).

### Daripada Python


Tiada panggilan khusus untuk dua penderia dalam permukaan SDK
yang digabungkan: buka dua sesi dengan `chloros_sdk.connect_daq_sensor()` dan hitungkan nisbah spektra `latest()` mereka sendiri, menggunakan konvensyen pelabelan yang sama. (Alat rakaman dua penderia juga wujud pada permukaan langsung perkakasan dalaman MAPIR
, disenaraikan dalam [Rujukan CLI
](../reference/cli-reference.md) untuk melengkapkan maklumat — ia bukan sebahagian daripada CLI
yang diedarkan; aliran kerja GUI di atas adalah laluan langsung yang disokong.)
