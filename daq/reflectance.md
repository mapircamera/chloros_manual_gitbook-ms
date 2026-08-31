# Aliran Kerja Reflektan

Penderia cahaya DAQ menukarkan imej radiometrik kepada reflektan. Terdapat dua aliran kerja yang berbeza:

1. **Sensor tunggal** — satu DAQ mengukur iradiasi ke bawah sementara kamera merakam, danChloros

membahagikan radiasi kamera dengan rujukan tersebut.
2. **Dua-sensor** — dua sensor DAQ, satu memantau langit dan satu lagi memantau objek, menghasilkan lengkung pantulan spektral secara langsung tanpa melibatkan kamera.

## Satu sensor + kamera (rujukan sinaran ke bawah)

DAQ berfungsi sebagai sensor cahaya sinaran ke bawah (DLS): kamera mengukur radiasi menaik **L**(W/m²/sr/nm), DAQ mengukur iradiasi menurun**E** (W/m²/nm), danChloros

mengira pantulan bagi setiap jalur sebagai:

> ρ = π · L / E

Bacaan DAQ sentiasa **dipasangkan cap masa dengan pendedahan** — ini adalah sebab DAQ dan kamera berkongsi jam yang dikawal oleh PTP (lihat [DAQ-E Networking &amp; Time Sync](ethernet-ptp.md)). Pasang topi cosine sinar matahari untuk kerja di luar dan nyatakannya dengan betul; penyataan topi secara langsung menskala E (lihat [Profil Topi &amp; Julat Dikalis](caps-and-range.md)). Untuk kerja kuantitatif, ingat ciri instrumen: iradiasi kuantitatif datang daripada purata bacaan sekurang-kurangnya 15 saat.

### Rakaman langsung

Ikatan DAQ kepada kamera dalam tab Kamera: setiap panel tetapan kamera mempunyai menu lungsur **Penderia Cahaya** yang menyenaraikan setiap DAQ yang disambungkan (DAQ-U/M/E) daripada tab Penderia Cahaya; untuk tatasusunan bersepadu, pemilihan Penderia Cahaya merentasi tatasusunan akan disebarkan ke setiap ahlinya (kamera individu masih boleh menimpa). Setelah diikat, spektra penderia memberi makan ke slot DLS kamera dan eksport reflektansi dibahagikan dengan bacaan yang sepadan.



<!-- SCREENSHOT-NEEDED: Cameras tab per-camera settings panel showing the "Light Sensor" dropdown open, with a connected DAQ sensor listed and selected. -->

Dua tingkah laku yang perlu diketahui:

* **Tiada DAQ yang diikat → reflektansi ditolak, bukan diada-adakan.**Chloros

menolak produk reflektansi dan merekodkan sebab terlepas daripada sekadar memulangkan produk yang kurang baik tanpa diberitahu.
* **Bacaan yang digunakan dikekalkan.** Untuk setiap bingkai pantulan, bacaan DAQ yang sebenarnya digunakan ditulis sebagai fail sampingan `.daq` di sebelah imej, supaya rakaman dapat diproses semula kemudian ([Perekodan &amp; Format .daq](recording.md)).

### Memproses imej yang dirakam

Untuk pemprosesan pasca-penerbangan, rakam `.daq` semasa sesi dan simpannya bersama imej — saluran pemprosesan akan menyelesaikan imej yang sepadan dengan cop masa secara automatik, mengambil sebarang kalibrasi kilang yang hilang daripada awanMAPIR

pada penggunaan pertama. Rakaman GUI ditambah ke projek terbuka secara automatik apabila ia berhenti.

Rujukan pantulan boleh dipilih semasa pemprosesan — `--reflectance-source` pada `chloros-cli process`, atau tetapan sumber pantulan dalam Tetapan Projek GUI:

| Nilai | Perilaku |
| --- | --- |
| `auto` (lalai) | Sasaran penentukuran dalam bingkai yang lulus QA adalah rujukan mutlak; DAQ downwelling (ρ = π·L/E) adalah pilihan sekunder |
| `daq` | Berkuasa DAQ |
| `target` | Sasaran ketat dalam bingkai; tiada penggantian DAQ |

Lihat [Sasaran Kalibrasi](../calibration-targets.md) untuk aliran kerja sasaran dan [bab LATTICE](../lattice/README.md) serta [RujukanCLI

](../reference/cli-reference.md) untuk keseluruhan aliran pemprosesan. Apabila membaca piksel pantulan yang dieksport, gunakan skala yang dicop (LATTICE: 32768 = ρ 1.0, XMP `Chloros:PixelScale`;Survey3

: 65535) — lihat [Format Imej Keluaran](../output-image-formats.md).

### Jalur di luar julat kalibrasi DAQ

Julat kalibrasi radiometrik DAQ ialah ~374–974 nm.Chloros

menolak reflektansi berasaskan DAQ untuk mana-mana jalur kamera dengan kurang daripada separuh berat spektralnya berada dalam julat tersebut, melaporkan sebab lompatan `dls-uncalibrated-band-<nm>`. Antara SKU yang dihantar, ini hanya menjejaskan F988: pantulan F988 dikalibrasi menggunakan panel pantulan dalam adegan: jalur itu terletak di luar julat kalibrasi penderia cahaya DAQ, jadiChloros

menggunakan tangkapan panel terkini anda dan menyimpannya di antara penjejakan panel. Jika kamera F988 dijalankan hanya dengan DAQ,Chloros

menolak reflektansi berasaskan DAQ untuk jalur tersebut dengan sebab lompatan `dls-uncalibrated-band-988` — aliran kerja panel adalah laluan yang disokong.

## Sensor dwi (cahaya sekeliling + objek)

Dua penderia DAQ — mana-mana pasangan, merentas mana-mana pengangkut — memberikan spektrum pantulan langsung tanpa kamera: satu penderia menghadap langit (**Sumber Cahaya Sekitar**), satu lagi menghadap subjek (**Pemindai Objek**), danChloros

mengira bagi setiap panjang gelombang:

> R(λ) = object(λ) / ambient(λ)

(sifar apabila cahaya sekeliling ≤ 0).

### Dalam GUI

Dengan kedua-dua penderia disambungkan dalam tab Penderia Cahaya, buka lapisan tambah-penderia (butang &quot;+&quot; pada jubin carta dalam paparan grid) dan pilih **Gabungkan Cahaya Sekeliling + Objek**. Pilih dua penderia dalam menu lungsur Sumber Cahaya Sekitar dan Pengimbas Objek dan klik Cipta. Kumpulan itu akan muncul sebagai carta tersendiri dan sebagai baris bar sisi dengan lencana**REF** hijau.



<!-- SCREENSHOT-NEEDED: The add-sensor overlay's "Combine Ambient + Object" panel with two connected DAQ sensors selected in the Ambient Light Source and Object Scanner dropdowns, Create button enabled. -->

<!-- SCREENSHOT-NEEDED: A live Apparent Reflectance chart from an Ambient+Object DAQ pair in list view, with the vegetation-index table visible below the chart (NDVI etc. showing live values). -->

Di bawah carta pantulan (pemandangan senarai), satu **jadual indeks vegetasi** langsung mengira indeks daripada lengkung menggunakan pusat jalur pada biru 450 / hijau 550 / merah 670 /NIR

800 nm. Indeks berasaskan nisbah yang membatalkan skala mutlak (NDVI

,GNDVI

, ENDVI,WDRVI

,GRVI

,CVI

,GCI

, MSR) sentiasa dipaparkan; indeks yang memerlukan pantulan mutlak (EVI

,SAVI

,OSAVI

, GSAVI, GOSAVI, MSAVI2, RDVI, TDVI,LAI

, NLI, MNLI, FCI,GEMI

) hanya muncul apabila kedua-dua penderia dikalibrasi kuasa.

### Nilai Kasar vs. Nilai Relatif — peraturan pelabelan

Chloros

melabel keluaran dua penderia berdasarkan apa yang sebenarnya boleh didakwa oleh pasangan penderia:

| Pasangan penderia | Label |
| --- | --- |
| Kedua-dua penderia dikalibrasi — pek kalibrasi kilang dimuatkan | **Nilai Pantulan Kasar** |
| Mana-mana sensor tidak dikalibrasi | **Refleksan Relatif** |

Ketiga-tiga model ini bersifat radiometrik: sebaik sahaja bundel kalibrasi kilang sesuatu sensor dimuatkan, spektranya adalah mutlak W/m²/nm, jadi nisbah dua sensor yang dikalibrasi menghasilkan refleksan mutlak yang kelihatan — bukan pengangkutan yang menentukannya. Sensor yang masih menyalurkan kiraan mentah (tiada bundel boleh dicapai) akan menurunkan keputusan kepada lengkung relatif (bentuk spektral masih sah). Kedua-dua sensor harus mempunyai had yang diisytiharkan dengan betul ([Cap Profiles &amp; Calibrated Range](caps-and-range.md)).

### DaripadaPython



Tiada panggilan khusus untuk dua penderia dalam permukaanSDK

yang digabungkan: buka dua sesi dengan `chloros_sdk.connect_daq_sensor()` dan hitungkan nisbah spektra `latest()` mereka sendiri, menggunakan konvensyen pelabelan yang sama. (Alat rakaman dwi-penderia juga wujud pada permukaan perkakasan langsung dalamanMAPIR

, disenaraikan dalam [RujukanCLI

](../reference/cli-reference.md) untuk kelengkapan — ia bukan sebahagian daripadaCLI

yang diedarkan; aliran kerja GUI di atas adalah laluan langsung yang disokong.)
