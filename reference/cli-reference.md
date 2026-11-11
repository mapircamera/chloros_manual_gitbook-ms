# Rujukan Chloros CLI Chloros

**Versi:**

1.3.0**Dijana:**2026-07-29 19:19 ·**Disemak:** 2026-11-06**Kumpulan Sasaran:** Disesuaikan untuk penggunaan LLM; mudah dibaca manusia.**Skop:** Setiap subperintah `chloros-cli` yang dihadapi pengguna, beserta pilihan dan contoh yang boleh disalin dan ditampal.

Dokumen ini adalah rujukan lengkap untuk alat baris perintah `chloros-cli` yang disertakan dengan MAPIR Chloros. Ia sengaja disusun secara menyeluruh supaya LLM (atau manusia) dapat menyusun sebarang aliran kerja yang disokong daripada senarai di bawah tanpa memeriksa kod sumber.

Jika anda hanya memerlukan intipati, lompat ke:
- [Permulaan Cepat Lima Minit](#five-minute-quickstart)
- [Aliran Kerja Sambungan Pertama Kamera LATTICE](#lattice-camera-first-connect-workflow)
- [Aliran Kerja Sambungan Pertama Penderia DAQ](#daq-sensor-first-connect-workflow)
- [Smart-AE / Smart-Capture](#smart-ae--smart-capture)
- [Mod Rakaman, Perakam & Proses Semula Luar Talian](#capture-modes-recorders--offline-reprocess)

> **Mengendalikan Chloros daripada pembantu AI?** Claude Desktop, Claude Code, Cursor dan klien MCP lain boleh menjalankan pemprosesan, kamera dan tatasusunan LATTICE, serta penderia cahaya DAQ melalui `chloros-mcp`, pelayan MCP yang dipasang bersama Python SDK (dan dengan .deb Linux, dalam `/usr/bin`): `claude mcp add chloros -- chloros-mcp`. Tetapan, pilihan dan alat terdapat dalam rujukan SDK [Pelayan MCP (Penolong AI)](sdk-reference.md#mcp-server-ai-assistants). Pembantu dengan shell juga boleh menjalankan setiap arahan di halaman ini secara langsung.

> **Diperubah dalam 1.3.0** (yang akan dikesan oleh skrip atau aliran kerja 1.2.0 yang sedia ada).
>
> **Perubahan yang mungkin perlu diuruskan oleh skrip 1.2.0:**
>
> - **`--preset` terpakai.** `lattice capture`, `continuous`, `calibrate`, `multi-capture`, `multi-stream`, `align-calibrate`, `align-apply` dan `align-stream` mengeluarkannya dalam versi 1.2.0 dan dijalankan pada tetapan kamera sendiri. Ia kini menetapkan format pratetap, pendedahan, penguatan dan kadar bingkai: `--preset high_speed` merakam 8-bit pada 5 ms, 6 dB, 60 fps. `lattice cam-connect --preset` juga mengekalkan format pratet (1.2.0 menetapkan kamera kepada 12-bit selepas menerapkan pratet). Lihat [Pilihan Umum](#common-options-shared-by-most-subcommands).
> - **Format piksel Bayer pada kamera M3M (mono) ialah kedalamannya.** Preset menamakan format Bayer, yang tidak dapat diambil oleh sensor mono: dalam 1.2.0 kamera menolak format tersebut dan mengekalkan format yang ada, jadi `--preset high_speed` tidak 8-bit di sana. Kini ia adalah format Mono dengan kedalaman yang sama: `high_speed` dan `--pixel-format BayerRG8` menghasilkan `Mono8`, `high_quality`, `triggered` dan `BayerRG12` menghasilkan `Mono12`. Dalam susunan campuran M3C + M3M, setiap kamera mengambil format yang dikongsi dalam keluarga masing-masing, dan `network-analysis` / `analyze-array --pixel-format` memaparkan setiap kamera pada kedalaman tersebut. Begitu juga sebaliknya: M3C yang diminta untuk format Mono mengambil format Bayer pada kedalaman tersebut, jadi satu susunan yang masternya adalah M3M tidak lagi membuka semula kameranya yang M3C pada format Mono. Lihat [Kamera Jalur Tunggal Mono (M3M)](#mono-m3m-single-band-cameras).
> - **`lattice array-capture` eksport diselaraskan secara lalai, seperti aplikasi.** Setiap ahli `debayered`, `display`, `radiance`, `reflectance` dan fail indeks dibengkokkan ke dalam bingkai kamera utama dan dipotong mengikut tumpang tindih ahli-ahli (lebih kecil daripada saiz sensor akibat offset kamera, `XMP-mapir:AlignmentJSON` `"applied": true`), jadi tangkapan CLI adalah fail yang sama seperti tangkapan aplikasi Chloros untuk adegan yang sama; `raw` kekal asli. Sebelum ini, tangkapan tanpa `--aligned` menyimpan setiap fail pada saiz asli, tanpa dibongkar. `--no-aligned` mengekalkan itu. Tetapan lalai yang sama terpakai kepada `--smart`, `--continuous` dan `--interval`, dan kepada SDK dan `chloros-mcp`. Lihat [mod-mod `array-capture`](#array-capture-modes).
> - **`lattice array-capture --compression` secara lalai adalah `none`.** Fail ditulis tanpa pemampatan (kira-kira 6.3 MB setiap bingkai resolusi penuh) untuk kadar berterusan terpantas; 1.2.0 menulis `deflate` (kira-kira 4.1 MB). Gunakan `--compression deflate` untuk fail yang lebih kecil. Kedua-duanya tidak kehilangan kualiti.
> - **PNG dan JPEG hanya menyokong produk `display` / `preview` dan indeks.** Penangkapan `--format png` atau `jpg` dengan tahap yang tidak dapat ditampung oleh fail 8-bit akan ditolak sebelum apa-apa ditangkap (HTTP 400, keluar `1`), dan begitu juga `--processing all` lalai dengan `--format png`/`jpg`. Lihat [Tahap Eksport Tangkapan](#capture-export-levels-the-all-default).
> - **`lattice dls` memerlukan `--mac`.**Tiada lagi MAC lalai (ia adalah alamat satu unit); `daq pool-discover --only ble` menemui alamat sensor.**`lattice dls --camera SERIAL`** adalah baharu.
> - **Lebih banyak kegagalan keluar `1`.** `process` apabila eksport sesuatu larian tidak dapat disahkan atau ia kehilangan imej, walaupun produk lain telah ditulis; arahan tangkapan dengan mana-mana fail yang tidak dapat ditulis (fail-fail lain masih disimpan); `lattice continuous` dan `multi-stream` apabila pemacu penuh; `array-burst` / `array-record` apabila perekod melaporkan ralat; `array-build-video` apabila produk yang diminta dilangkau atau tidak dibina seperti yang diminta; `project burst` / `stream` apabila tangkapan gagal separuh jalan; `project sensor log` apabila rakaman tidak mempunyai sebarang bacaan; `lattice exposure --set`, `gain --set` dan `configure --set` dengan nilai yang ditolak oleh kamera. `lattice network-analysis --json` keluar seperti yang dilakukan oleh ringkasan (`1` untuk `needs_force_slip`). Sebaliknya: `daq pool-disconnect` bagi sensor yang tidak lagi berada dalam kumpulan keluar `0`. Lihat [Kod Keluar](#exit-codes).
> - **`lattice array-connect` yang perlahan tidak pernah gagal apabila susunan disambungkan.** Ia menunggu sehingga 600 s (180 s dalam 1.2.0; `project connect` 90 s), mencetak langkah backend semasa ia berjalan, dan sebarang sambungan yang belum selesai menjelang masa itu akan dibatalkan oleh backend — setiap kamera dipulihkan seperti keadaan asal — jadi `Error:` (keluar `1`) bermaksud tiada susunan yang disambungkan. Pada 1.2.0, sambungan sebaik sahaja kamera dihidupkan boleh melebihi tempoh menunggu dan menyambungkan susunan selepas arahan gagal. Ctrl+C kini membatalkan sambungan. Lihat [Persistent Connections](#persistent-connections-smart-prep-gui-equivalent-flow).
> - **`lattice array-connect` mengekalkan keseluruhan sensor.** Tanpa `--width` / `--height` / `--binning`, setiap kamera ditetapkan kepada sensor penuh dan kadar bingkai jatuh kepada apa yang dihantar oleh komputer dan rangkaian ini; keluaran sambungan menyenaraikan pilihan yang lebih pantas (penyusutan atau ROI yang lebih kecil) dengan kadar yang dijangkakan. Versi 1.2.0 mengecilkan atau menyusun bingkai untuk mengekalkan kadar. Lihat [Pilihan `array-connect`](#array-connect-options).
> - **Rangkaian perlahan tidak lagi menolak susunan.** Setiap kamera disesuaikan dengan bahagian jalur lebar masing-masing dan kadar bingkai menurun mengikutnya. Di mana kamera tidak dapat disesuaikan dengan begitu perlahan, susunan akan berjalan dalam pengeluaran bersiri (`slip-emit-and-capture`): setiap bingkai tiba, tetapi kamera tidak terdedah serentak. `--force-tier sim-capture-sim-emit` bertegas pada pendedahan serentak. Lihat [Array fps & burst model](#array-fps--burst-model).
> - **`lattice network-analysis` / `analyze-array` menentukan apa yang connect terapkan**: tanpa `--pixel-format`, format yang ditetapkan oleh connect (Mono12 / BayerRG12, sebelum ini BayerRG8), dan keputusan [12-bit terbungkus](#packed-12-bit-transport---packed). Oleh itu, arahan yang sama boleh menyatakan kadar bingkai yang berbeza berbanding dalam 1.2.0.
> - **Laluan relatif berasaskan lokasi anda menjalankan arahan.** `-o` dan laluan lain yang dibuka oleh backend (`--input`, `--burst-dir`, `--profile`, `--save`, `--mask`), `output` langkah resipi, dan `lattice index` lalai bagi `lattice` diselesaikan terhadap folder di mana anda menjalankan `--input`. Dalam 1.2.0 backend menyelesaikannya dalam folder pemasangan sendiri, yang pada binaan terpasang gagal dengan "Akses ditolak". Lihat [Konvensyen](#conventions).
> - **`lattice index` dijalankan pada backend dalam setiap bentuk**, seperti arahan `lattice` yang lain, jadi mulakan backend terlebih dahulu. Indeks luar talian (`--input`) dikira oleh backend, yang membaca TIFF dan menulis produk di sebelahnya atau di `-o`, dan `--list-presets` / `--list-gradients` mencetak jadual backend. Pada Windows, ini kini berfungsi: dahulu, Windows CLI menjawab setiap bentuk `lattice index`, termasuk `--live`, dengan `Error: lattice_sdk not available`. Pada Linux, indeks luar talian dahulu dijalankan di dalam CLI tanpa backend dan tanpa log masuk; kini ia memerlukan kedua-duanya, seperti setiap arahan CLI yang lain. Lihat [Index / Vegetation Maths](#index--vegetation-maths).
> - **Fail baru di sebelah produk.** Setiap produk sinaran, pantulan dan indeks di mana pencampuran semula jalur mengunci piksel akan mendapat topeng dalam folder `clamp_masks/` di sebelah folder produk, dan tangkapan cetakan mencetak baris `clamp mask:`. Lihat [Topeng clamp Unmix](#unmix-clamp-mask-which-pixels-a-band-was-clamped-on).
> - **Orientasi kamera.** Rakaman kamera tunggal menyimpan `raw` dan `debayered` seperti dibaca oleh sensor dan merekodkan pembalikan/putaran kamera dalam XMP; `process` menerapkannya pada produk yang dihasilkan. Fail ahli tatasusunan kekal asli melainkan anda menyerahkan `--orient` baru (`array-capture`, `array-burst`, `project capture` / `burst` / `stream`). Lihat [Orientasi kamera](#what-a-captures-folder-looks-like).
> - Nama tahap eksport yang bukan satu ditolak.** `lattice capture --processing` dan setiap senarai `--levels` mengambil `raw`, `debayered`, `radiance`, `reflectance` atau `display` (atau nama lain seperti `preview`; `--processing` juga `all`). Sebarang nama lain ditolak sebelum kamera disentuh (HTTP 400 `UNKNOWN_LEVEL`, keluar `1`); 1.2.0 menyimpan `raw` untuknya dan keluar `0`.
> - **`daq pool-set-cap` memerlukan `--cap-id`.** Tanpa ia, arahan akan keluar `2`; 1.2.0 telah membuang pembetulan penutup, meninggalkan sensor kosong. `--cap-id none` meminta sensor kosong.
> - **`lattice resolution --binning-mode` telah dihapuskan.** Binning sentiasa Purata; penanda itu masih diterima, mencetak amaran dan tidak melakukan apa-apa. Lihat [Penalaan Sensor](#sensor-tuning).
> - **`process --debayer texture-aware` ditolak untuk tangkapan LATTICE.** Untuk folder yang mengandungi tangkapan LATTICE, `process` keluar daripada `1` selepas pengimportan, menyenaraikan berapa banyak yang ada, sebelum memproses apa-apa. Sambungan LATTICE sentiasa mendapat demosaik piawai, jadi versi 1.2.0 menerima pilihan itu dan mengabaikannya; aplikasi Chloros juga tidak menawarkannya untuk sambungan tersebut. Lihat [`chloros-cli process`](#chloros-cli-process).
> - **Kamera dikembalikan seperti keadaan asal — melainkan `--keep-settings`.** `lattice array-disconnect`, setiap arahan sementara (`multi-capture` tanpa tatasusunan, `multi-stream`, `multi-test`, `align-calibrate`, `align-apply`, `align-stream`, `index --live`) dan `lattice continuous` memulihkan setiap kamera seperti sedia kala: pemasangan wayar picunya dan garisan penyegerakan, PTP-nya, dan setiap tetapan kamera yang diubah oleh sesi (pendedahan dan penguatan, sasaran AE, had dan peredaman, imbangan putih, paras hitam, format piksel, binning dan ROI, pembalikan, kadar bingkai). `--keep-settings` sebaliknya menyimpan setiap kameratetapan ke dalam memori kamera itu sendiri, supaya ia hidup dengan tetapan tersebut; `--clear-settings` menetapkan semula memori itu kepada tetapan kilang. Dalam kedua-dua kes, kamera-kamera itu dibiarkan berjalan bebas. Satu projek menetapkan kameranya daripada tetapan simpanan sendiri apabila ia menyambungkannya. Lihat [Memori kamera](#camera-memory----keep-settings----clear-settings).
> - **Setiap mod menyimpan projek dengan cara yang sama.** Sebarang perubahan pada kamera, tatasusunan atau penderia cahaya yang disambungkan di bawah projek — daripada aplikasi, CLI, SDK atau pelayan MCP — disimpan ke dalam `cameras.json` / `sensors.json`, dan membuka projek dalam mana-mana mod menetapkan peranti daripadanya. Sebelum ini, hanya aplikasi menyimpan projek, dan simpanan seterusnya akan menimpa apa-apa yang lain. Lihat [Satu rekod projek, setiap mod](#one-project-record-every-mode).
> - **Fail yang dibuat daripada beberapa kamera menamakan setiap satu. Satu susunan berbilang jalur (`align-apply`, `align-stream`, `index --live --save-multiband`), indeks langsung yang dibuat daripada jalur beberapa kamera, dan imej `composite/` tangkapan susunan membawa siri, model dan pek penentukuran setiap kamera sumber sebagai senarai (`XMP-mapir:SourceCameraSerials`, `SourceCameraModels`, `SourceCalBundleSha256s`, …), dan EXIF `SerialNumber` menyenaraikan nombor siri, dipisahkan dengan koma. Mereka dahulu tidak membawa sebarang kamera langsung, atau hanya siri kamera pertama. Lihat [Fail yang dibuat daripada beberapa kamera](#files-made-from-several-cameras).
> - **Hanya fail `raw` membawa tag penentukuran kamera.** Set yang serasi dengan MicaSense (`BlackLevel`, `RadiometricCalibration`, `Band{R,G,B}RadiometricCalibration` bagi setiap saluran, set `MicaSenseVignetting*` dan muatan `CalibrationJSON` menerangkan nilai mentah penderia, jadi ia hanya terdapat dalam fail tangkapan `raw`. Setiap fail lain — `debayered`, `display`, `radiance`, `reflectance` dan fail indeks, bingkai `lattice continuous` dan `multi-stream`, dan setiap produk `process` — membawa identiti kamera (`XMP-mapir:CameraSerial`, `CameraModel`, `FilterType`, `LensModel`, `CalBundleSha256`, ...) dan, pada tangkapan, rekod bingkai itu sendiri (`XMP-mapir:FrameJSON`: pendedahan, penguatan, suhu, format piksel, masa tangkapan). Dalam 1.2.0 mereka turut menyertakan set mentah, yang digunakan oleh satu alat (Pix4D, Metashape) digunakan pada piksel yang bukan nilai mentah. `process` mencari penentukuran fail sedemikian melalui nombor siri dan `CalBundleSha256`.
>
> **Perubahan lain:**
>
> - **Kadar bingkai automatik bagi satu susunan menetapkan pada apa yang disalurkan oleh susunan tersebut.** Kadar yang kehilangan lebih daripada 1% pencetusannya akan diturunkan, dinilai berdasarkan beberapa sesi pandangan panjang dan bukannya satu, dan kadar asal yang ditinggalkan akan dicuba semula dari semasa ke semasa. Kadar yang anda tetapkan sendiri tidak akan diubah.
> - **Pendedahan automatik lebih stabil.** Ia mengekalkan pendedahan pada cahaya yang konsisten, menstabilkan dalam beberapa saat selepas perubahan, mengabaikan sorotan kecil, dan mengembalikan kamera yang berhenti bertindak balas di bawah cahaya matahari terik. Lihat [Bagaimana pendedahan automatik berfungsi](#how-auto-exposure-behaves).
> - **`process` hanya mengira imej dan rakaman anda.** Memulakan semula pada folder yang mengandungi `camera_cal/` projek tidak lagi mengira fail penentukuran tersebut ("Ditemui 96 imej" pada set 90 imej), dan projek yang telah mengambilnya sebelum ini akan membuangnya.
> - **`lattice multi-test` meneruskan daripada keadaan kuasa-hidup kamera.** Ia menghidupkan PTP kamera untuk pelaksanaan, menunggu jamnya terkunci dan stabil sebelum pencetus pertamanya — setiap kamera terkunci selama 120 s, kira-kira 2 minit dari kuasa-hidup (`--ptp-settle SECONDS` menetapkan ia), kerana jam yang baru terkunci akan tersasar sehingga ~200 us selama kira-kira 2 minit — dan ia menetapkan semula PTP setiap kamera mengikut keadaan asal. Kamera yang masih terkunci dari ujian sebelumnya tidak akan ditunggu lagi. Dulu ia akan mematikan PTP pada rangkaian satu suis di mana tiada DAQ-E yang menjawab, dan membaca jam bebas setiap kamera sebagai jam PTP yang tidak akan terkunci (`PTP is not locked`, setiap larian). Lihat [Multi-Kamera (Sesi Sementara)](#multi-camera-transient-sessions).
> - **Rakaman `.daq` menyatakan sama ada penutupnya dipilih atau diandaikan.** DAQ-U, DAQ-M dan DAQ-E dihantar tanpa penutup, dan Chloros tidak dapat melihat sama ada penutup dipasang, jadi sensor yang disambungkan tanpa penutup yang dinamakan mendapat `sunshine_cosine` secara lalai, satu *andaian*. Setiap rakaman Chloros (skema 1.30) kini menyimpan `cap_id_source` di samping `cap_id`: `operator` apabila anda menamakan penutup (termasuk `none`), `auto_default` apabila ia adalah lalai. Memproses rakaman yang penutupnya diandaikan, dengan penutup DAQ projek dibiarkan pada Auto, mencetak `[daq-dls] WARNING` yang menamakan fail: DAQ-U kosong di bawah pembetulan cahaya matahari membaca kira-kira 30x tinggi. **Namakan penutup yang anda pasang** apabila anda menyambung, atau tetapkan penutup DAQ projek, dan amaran itu akan hilang.
> - **Tiada lagi tetingkap konsol pip pada Windows.** Pada PC yang dipasang Python, backend dan setiap pekerja imejnya menjalankan `pip` sekali semasa permulaan, dan setiap satunya membuka tetingkap konsol. Mereka tidak lagi berbuat demikian.
> - **Rakaman `.daq` langsung bertahan walaupun berlaku pemadaman kuasa.**Rakaman ditulis dengan jurnal dan penyegerakan cakera penuh pada setiap komit, di belakang sensor, jadi cakera yang perlahan menangguhkan fail dan tidak pernah menangguhkan bacaan. Rakaman yang dihentikan di tengah-tengah commit (pemadaman kuasa, kill) boleh meninggalkan `<name>.daq-journal` di sisinya:**salin kedua-duanya bersama.**Chloros akan memulihkan jurnal pada kali pertama ia membaca fail, jadi rakaman dibuka dengan semua yang telah dikomit sebelum pemberhentian (kira-kira 3 saat terakhir hilang); dalam 1.2.0 pemadaman kuasa pada masa yang salah boleh menyebabkan keseluruhan fail tidak dapat dibaca ("imej cakera pangkalan data rosak").
> - **Perekodan pada cakera yang sibuk tidak lagi menyebabkan sensor terhenti.** `daq pool-record` menyusun giliran bacaan dan menulisnya pada utas tersendiri; bacaan yang tidak dapat ditulis akan dikira (`daq pool-list` memaparkan `N write error(s)` dengan ralat terakhir) bukannya membekukan sensor (satu penulisan mengambil masa 8.4 s di sebelah empat kamera yang menyimpan fail TIFF).
> - **`lattice multi-capture` tanpa array tidak lagi kehilangan bingkai yang disimpannya apabila pencetus tamat masa.** Pemicu kamera yang tamat masa (`SC_ERR_TIMEOUT -1011`, dilihat dengan empat kamera pada satu suis 1 GbE) menamatkan pelaksanaan pada kumpulan bingkai tersebut: kumpulan yang telah disimpan akan disenaraikan dengan `Saved:`, diikuti oleh ralat, keluar `1`. Dalam 1.2.0 ia adalah ralat HTTP 500 tanpa sebarang senarai, walaupun fail-fail tersebut ada di cakera.
> - **Kamera yang dicetuskan oleh perisian tidak lagi melepaskan setiap pencetus kedua.** Kamera dalam mod pencetus perisian kini menyusun pencetus yang tiba semasa bacaan keluarnya (atau Chloros menunggu sehingga ia siap), seperti yang telah dilakukan oleh kamera yang diselaraskan melalui kabel.
> - **`daq pool-discover` menamakan DAQ-E-S `DAQ-E-S`** dalam lajur `MODEL` (dan `--json`'s `model`). Ia mengatakan `DAQ-E` untuk setiap sensor Ethernet; satu yang tidak menjawab semakan identiti dalam masa kira-kira 2 saat masih disenaraikan, sebagai `DAQ-E`.
> - **Produk indeks LATTICE yang dipertingkatkan** (NDVI dan indeks lain) daripada `process`, dalam setiap mod pemprosesan.
> - **Kamera OCN:**penambahbaikan sinaran, pantulan dan produk indeks dalam cahaya matahari.** Pembetulan armada digunakan selepas pencampuran semula jalur; kamera RGN kekal tidak berubah, dan nilai OCN berbeza daripada versi 1.2.0. Ia juga terpakai pada tangkapan OCN yang dibuat dengan versi terdahulu, apabila ia diproses pada komputer yang menyimpan kalibrasi yang digunakan semasa tangkapan diambil. (Chloros mengekalkan penentukuran kamera setelah kamera disambungkan).
> - **`lattice continuous` merekodkan bingkai mentah.** Pelaksanaan `tiff` menulis de-mosaic 3-rangka berbilang-pesawat, yang `process` tidak dapat memulakannya. Rangka-rangkanya kini adalah fail `raw` yang ditulis oleh tangkapan larian: satu pesawat mozek pada nilai asal sensor, tidak dimampatkan, dengan pek kalibrasi. Skrip yang membaca tiga satah daripada fail-fail ini hanya membaca satu.
>
> - **Setiap tangkapan menyimpan bacaan DAQ yang digunakan, seperti yang dilakukan oleh GUI.** `lattice capture`, `lattice continuous` (satu `.daq` setiap bingkai), `array-capture` dan `multi-capture` dengan array bersambung (dan SDK) menulis `.daq` dan `.match.json` setiap kali DAQ diikat atau dikumpulkan, tangkapan hanya mentah disertakan; dalam 1.2.0 menyimpan tangkapan mentah atau debayered hanya dengan `--force-daq`. `--no-daq` mematikannya, `--daq-tolerance-ms MS` menetapkan toleransi padanan untuk tangkapan tersebut, dan  baru000290 mengikat kamera atau susunan kepada DAQ daripada CLI. `.daq` capture yang disimpan dipadankan dalam `process` hanya dengan kamera yang dinamaikannya. Sebuah tangkapan kini menunggu bacaan seterusnya oleh DAQ supaya ia dapat menggabungkan bacaan di kedua-dua belah pendedahan. Lihat [The DAQ reading a capture saves](#the-daq-reading-a-capture-saves) dan [A DAQ Reading Far From the Exposure](#a-daq-reading-far-from-the-exposure).
> - **`process --indices NAME`** menambah indeks ke dalam projek bagi jalur setiap kamera, termasuk projek baharu. Dalam versi 1.2.0, nama yang belum disenaraikan oleh projek diabaikan dengan `Unknown index name(s) ignored`.
> - **`process` berhampiran array langsung:**ia tidak lagi memulakan semula backend yang memegang kamera atau DAQ, dan menunggu sehingga 15 s untuk run yang sedang berakhir.**Ctrl+C** menghentikan run dalam backend, bukan hanya dalam CLI. Lihat [Nota `process`](#notes).
> - **Folder projek yang terlalu dalam untuk had laluan 260 aksara Windows** akan ditolak terus dengan folder terpanjang yang muat, bukannya menjalankan proses yang tidak menulis apa-apa. Pemuat turun Windows kini menawarkan "Aktifkan laluan panjang Windows" dan pengecualian Microsoft Defender untuk backend, kedua-duanya dipilih secara lalai. Lihat [Windows (.exe)](#windows-exe).
> - **`lattice array-capture --continuous`** menjalankan tangkapan berterusan backend sendiri: satu baris kemajuan setiap 2 saat, satu ringkasan bukannya senarai bagi setiap fail, dan kod keluar bukan sifar pada mana-mana fail yang tidak ditulis atau pemacu yang penuh. Lihat [mod `array-capture`](#array-capture-modes).
> - **`array-capture` pertama selepas `array-connect`** menunggu sehingga 20 saat untuk kumpulan bingkai pertama susunan tersebut, bukannya gagal dengan ralat HTTP 503 "Susunan masih memanas".
> - **Pendedahan susunan di bawah ROI:** ROI kecil tidak lagi mengehadkan pendedahan ahli susunan pada tempoh bebas larian penderia (4.9 ms pada 768 × 304). Lihat [Pendedahan pada ahli susunan](#exposure-on-an-array-member).
> - **Pendedahan automatik tidak lagi menurunkan kadar bingkai automatik sesebuah susunan.** Susunan yang diselaraskan melalui kabel beroperasi pada kadar yang dibenarkan oleh rangkaian dan bacaan kamera; pendedahan automatik mengisi tempoh yang diberikan oleh kadar tersebut, kemudian meningkatkan penguatan. Pendedahan manual yang lebih lama daripada tempoh (`lattice exposure --set`, `configure --set`) memperlahankan pencetus untuk menampaknya, bukannya ditolak, dan kadar akan kembali apabila ahli kembali ke auto. Lihat [Pendedahan pada ahli tatasusunan](#exposure-on-an-array-member).
> - **Tatasusunan menyampaikan kumpulan bingkai tidak lengkap yang lebih sedikit**, dan lebih sedikit ralat tangkapan 503 ("Tiada kumpulan bingkai diselaraskan baharu"): input pencetus setiap kamera hamba kini telah dipenapisan.
> - **Penghantaran 12-bit berpadat** (`--packed`, lalai `auto`) meningkatkan kadar bingkai matriks sensor penuh kira-kira sepertiga. `array-connect --packet-size` kini digunakan (1.2.0 menerimanya dan mengabaikannya). Lihat [Pengangkutan 12-bit berpadat](#packed-12-bit-transport---packed).
> - **Susunan yang kameranya memulihkan kitaran kuasa dengan sendirinya.** Selepas kitaran kuasa suis, susunan `array-connect` re-membuka kamera apabila ia menjawab dan menyalurkan semula dengan PTP dihidupkan semula; dalam 1.2.0 ia berhenti sepenuhnya dan memerlukan `array-connect` yang baharu. `array-status` melaporkan `ptp_enabled: False` (dan `ptp_off:`) manakala kamera membaca PTP off, di mana ia dahulu melaporkan tetapan yang array itu disambungkan. Lihat [Persistent Connections](#persistent-connections-smart-prep-gui-equivalent-flow).
> - **`lattice array-record` merekod tanpa GUI dibuka.** Apabila pandangan gabungan GUI ditutup, backend memaparkan pandangan itu sendiri: susunanNDVI, atau indeks yang anda hantar dengan `--expr` baru. Rakaman yang tidak menulis sebarang bingkai kini gagal (`no-frames: …`, keluar `1`). Sebelum ini, ia hanya merakam selagi paparan gabungan GUI dibuka, dan rakaman yang tidak menulis apa-apa masih keluar `0`. Lihat [`array-record`](#array-record--combined-index-videogif-monitoring-grade).
> - **Kamera yang sudah dipegang oleh Chloros tidak dibuka buat kali kedua.** Perintah yang akan membukanya sendiri sebaliknya menjawab HTTP 409 `CAMERA_HELD`, menyenaraikan apa yang memegangnya dan perintah yang melepaskannya. Ini termasuk kamera yang sedang digunakan oleh arahan lain sekarang (sebuah `multi-test` atau `multi-capture` yang membuka kameranya, sebuah `lattice capture`): arahan kedua akan ditolak sehingga arahan pertama selesai. Membukanya semula boleh menghentikan aliran pemegang atau menjatuhkan backend. Lihat [Sambungan Berterusan](#persistent-connections-smart-prep-gui-equivalent-flow).
> - **Perintah berbilang kamera sementara menyampaikan setiap kamera atau gagal.** `lattice multi-capture` tanpa sebarang array disambungkan, `multi-stream`, `multi-test`, `align-calibrate`, `align-apply`, `align-stream` dan `index --live` keluar daripada `1` dengan menamakan kamera yang tidak menghantar sebarang bingkai. Aliran melangkau kumpulan bingkai yang tiada kamera dan berhenti selepas tiga berturut-turut; `multi-test` mencetak satu baris bagi setiap pencetus, `(no frame)` di mana kamera terlepasnya; `align-stream` juga keluar `1` pada ralat kerja. Setiap larian kemudian memulihkan setiap kamera seperti asalnya: pendawaian pencetus dan baris penyegerakan, PTP (`PtpEnable`) dan setiap tetapan kamera yang diubah oleh larian — pendedahan dan penguatan, sasaran AE, had dan peredaman, imbangan putih, paras hitam, format piksel, binning dan ROI, pembalikan, kadar bingkai — melainkan `--keep-settings` / `--clear-settings` (lihat [Memori kamera](#camera-memory----keep-settings----clear-settings)). Sebelum ini, arahan-arahan ini menyimpan mana-mana bingkai yang diterima dan keluar daripada `0` (`align-stream` mencetak ralat kerja sebagai `Note:`), dan meninggalkan setiap kamera dalam `TriggerMode` `On`. Mereka juga menetapkan pendedahan setiap kamera daripada pemandangan sebelum menyimpan bingkai (maksimum beberapa saat), pada sasaran, satu tatasusunan bersambung memulakan setiap kamera pada: 40 multispektral, 60 RGB (`--brightness N` menetapkan), dan menyimpannya untuk keseluruhan sesi; seperti juga satu `lattice capture` bagi kamera Chloros tidak memegang. Sebelum ini, bingkai pertama setiap kamera diambil pada had pendedahan kamera itu sendiri, kira-kira 100 ms, tanpa mengira keadaan cahaya, dan sering dipotong. Lihat [Multi-Kamera (Sesi Sementara)](#multi-camera-transient-sessions).
> - **Arahan multi-kamera sementara berkongsi rangkaian seperti yang dilakukan oleh `array-connect`, dan hanya membuka `--serials`.** Setiap kamera diberikan kelajuan mengikut bahagiannya pada pautan hos, jadi empat kamera berjalan pada pautan 1 Gbps; sebelum ini, keempat-empatnya meminta 125 MB/s antara mereka, semuanya daripada 1 GbE, dan setiap bingkai kehilangan paket. Susunan yang tidak dapat ditampung oleh rangkaian akan ditolak sebelum sebarang aliran kamera dimulakan. `--serials` kini memilih kamera, dengan kamera pertama mencetuskan picu; sebelum ini, setiap arahan membuka semua kamera dalam rangkaian tanpa mengira apa yang dikatakan oleh `--serials`. Kegagalan yang merupakan kehilangan paket GVSP kini menandakannya sebaliknya daripada menunjukkan kabel penyegerakan M8. Lihat [Multi-Kamera (Sesi Sementara)](#multi-camera-transient-sessions).
> - **`lattice array-disconnect` membiarkan kamera berjalan bebas, seperti yang ditemui.** `TriggerMode` `Off`, dan segala-galanya seperti yang ditemui oleh sambungan — tetapan pencetus dan talian penyegerakan (`TriggerSource`, `TriggerActivation`, `TriggerOverlap`, `TriggerDelay`, `LineMode` / `LineSource`), PTP, dan tetapan kamera yang diubah oleh sesi — jadi satu-perintah kamera pada ahli terdahulu mendapatkan bingkai sekaligus, dan tuan rumah tidak lagi kekal pada `TriggerSource` `Software`. `--keep-settings` menyimpan tetapan sesi ke dalam memori setiap kamera, `--clear-settings` menetapkan semula memori itu (lihat [Memori kamera](#camera-memory----keep-settings----clear-settings)). `lattice continuous` juga menetapkan kamera yang dikesan dalam mod bersedia untuk berjalan bebas bagi larian itu (dan mengembalikannya selepas itu), dan larian tanpa sebarang bingkai keluar daripada `1`.
> - **`lattice configure --get` / `lattice exposure`** mencetak `note:` dengan kamerasebenar pendedahan atau penguatan semasa mod automatik berjalan. `lattice exposure --set` / `configure --set` yang ditolak menyatakan julat yang diterima oleh kamera dan mengembalikan mod automatik, bukannya mematikan pendedahan automatik. Lihat [Penalaan Sensor](#sensor-tuning).
> - **Kawalan kamera baharu:**`lattice highlights` (menarikkan semula permukaan putih cerah dalam paparan RGB) dan `lattice exposure --brightness-auto`.**`lattice multi-detect`** mencari kamera berwayar dalam satu pusingan probe dan menetapkan lalai tuan rumah kepada kamera RGB.
> - **Arahan DAQ baru:** `daq pool-bind`; `pool-enable-ptp`; dan untuk tetapan DAQ-E sendiri `pool-info`, `pool-set-network`, `pool-set-auto-stream`, `pool-reboot` dan `pool-update-firmware`. Lihat [Penyetelan Peranti DAQ-E](#daq-e-device-settings).
> - **DAQ-E yang berhenti menghantar akan disambung semula oleh backend.** Apabila aliran DAQ-E yang dikongsi menjadi senyap (kitaran kuasa suis yang menyalirkannya, pautan kamera yang terputus), backend menyambungkannya semula dengan tetapan asal, mencuba semula pada selang masa yang semakin panjang selagi ia tiada. Rakaman padanya diteruskan dalam `.daq` yang sama dengan jurang ditandakan dalam fail, dan `pool-record --stop` menyenaraikan setiap jurang. Dalam 1.2.0 sensor itu menjadi senyap sehingga `pool-disconnect` + `pool-connect`, yang juga menamatkan rakaman. Lihat [A DAQ-E That Stops Delivering](#a-daq-e-that-stops-delivering).
> - **`time-sync status`** melaporkan sama ada paket grandmaster PTP sampai ke rangkaian (`health`), dengan `WARNING:` pada stderr apabila ia tidak sampai. Lihat [`time-sync`](#chloros-cli-time-sync).
> - **Linux .deb:** ia meningkatkan penimbal penerimaan soket kernel yang diperlukan oleh aliran kamera (pada lalai kernel, menyebabkan kehilangan rangkaian dan kadar bingkai), menambah perkhidmatan backend yang berjalan sebagai pengguna anda, dan `lattice network` memeriksa penimbal tersebut. CLI tidak pernah menghentikan backend yang dijalankan oleh perkhidmatan, dan peningkatan akan memulakan semula perkhidmatan backend yang diaktifkan. Lihat [Menjalankan backend sebagai perkhidmatan](#running-the-backend-as-a-service-headless) dan [Host Linux — buffer penerimaan soket](#linux-hosts--socket-receive-buffers).
> - **`lattice array-capture` memberitahu apabila ia tidak dapat menyelaras:**setiap sebab tangkapan disimpan tidak diselaraskan (tiada profil penyelarasan, ahli pada resolusi berbeza, satu tatasusunan tidak diselaraskan secara kabel, sesebuah bingkai yang profil tidak dapat dipetakan) dicetak sebagai baris `Alignment:`.**`chloros-cli daq help`** adalah `daq --help` (ia keluar daripada `2`), dan pada Linux, bantuan menyenaraikan arahan `daq` perkakasan terus yang dibina termasuk.
> - **Jetson:** pakejnya ialah `chloros_1.3.0_arm64_jp6.deb`, untuk JetPack 6, dan pemprosesan GPU-nya dijalankan pada Orin dengan CUDA PyTorch JetPack. Lihat [Linux arm64 — Jetson](#linux-arm64--jetson-jetpack-6).

---

## Konvensyen

- Semua arahan bermula dengan `chloros-cli`. Pada Windows, binari adalah `chloros-cli.exe`; pada Linux/Pada Jetson ia `chloros-cli`.
- Argumen pilihan ditunjukkan sebagai `--flag`. Argumen posisi yang diperlukan ditunjukkan tanpa kurungan.
- Apabila nilai lalai diberikan, mengabaikan penanda akan menggunakan nilai tersebut.
- CLI adalah klien HTTP ringan melalui backend Chloros (pelayan pada `127.0.0.1:5000` yang juga digunakan oleh aplikasi desktop). **Arahan yang memulakannya:**
  - Arahan teras — `process`, `login`, `logout`, `status`, `export-status`, `time-sync` dan `selftest` — menggunakan backend yang sedang berjalan, atau memulakannya apabila tiada yang berjalan dan menghentikannya semula apabila selesai.
  - **Perintah `lattice`, `project` dan `daq` tidak pernah memulakannya.** Mereka memerlukan backend yang sudah berjalan: backend aplikasi desktop Windows, backend perkhidmatan Linux [backend service](#running-the-backend-as-a-service-headless), atau satu yang anda mulakan sendiri (lihat [Mulakan Backend](#start-the-backend)). Tanpa satu, mereka mencetak `Error: Chloros backend not reachable at http://127.0.0.1:5000`, dengan arahan yang memulakannya, dan keluar `1`.
- `CHLOROS_BACKEND_URL=<url>` menunjuk kepada **`lattice`**,**`project`**, dan**`daq pool-*`** keluarga arahan di backend lain (`lattice network-analysis` menggunakan `--backend-url` sebaliknya). Arahan teras sengaja mem-pin `http://127.0.0.1:<port>` dan mengabaikannya (literal IPv4 mengelakkan penalti ~2 s-per-permintaan `localhost`→`::1` oleh Windows). Lihat [Pembolehubah Persekitaran](#environment-variables).
- **Laluan relatif diselesaikan berdasarkan folder di mana anda menjalankan `chloros-cli`.** Backend ialah proses yang membuka folder keluaran dan input, jadi sebelum menghantar laluan, CLI menjadikannya mutlak: `-o` / `--output` (termasuk lalaiannya, `output`), `--input`, `--burst-dir`, `--profile`, `--save` dan `--mask`, a [recipe](#recipe-dsl) langkah `output` (default `output`), dan folder lalai `output` bagi `lattice index --live`. `-o captures/` oleh itu menulis ke `<current folder>/captures/`, dan laluan yang dicetak adalah mutlak. `~` diperluas. Ke backend jauh (`CHLOROS_BACKEND_URL` menamakan mesin lain) laluan dihantar seperti yang ditaip dan diselesaikan pada mesin tersebut.
- Log masuk akaun Chloros+ diperlukan untuk semua SDK/CLI panggilan (jalankan `chloros-cli login` sekali bagi setiap mesin; disimpan dalam cache di `~/.chloros/`).
- Contoh menggunakan laluan Linux; pada Windows gantikan `/home/user/...` dengan `C:/Users/.../...`. Satu DAQ-Uport siri adalah gaya `COM3` pada Windows dan `/dev/ttyUSB0` / `/dev/ttyACM0` pada Linux.
- Sinopsis menulis bendera yang saling eksklusif sebagai `{--a | --b}` dan pilihan dalam `[...]`.

---

## Sinopsis Peringkat Atas

```
chloros-cli [global options] COMMAND [command options]
```

### Pilihan Global

| Bendera | Deskripsi |
| --- | --- |
| `--backend-exe PATH` | Menimpa fail boleh laku belakang yang dikesan secara automatik. |
| `--port N` | Pelabuhan belakang HTTP (lalai: `5000`). Digunakan hanya oleh arahan teras (`process`, `login`, `logout`, `status`, `export-status`, `time-sync`, `selftest`); `lattice`, `project` dan `daq` menggunakan `CHLOROS_BACKEND_URL` (default `http://127.0.0.1:5000`). |
| `-v, --verbose` | Aktifkan keluaran terperinci. |
| `--restart` | Memaksa memulakan semula backend (membunuh mana-mana `backend_server.py` yang sedang berjalan). Backend yang dijalankan oleh perkhidmatan systemd (Linux .deb) tidak pernah dihentikan: arahan mencetak `sudo systemctl restart <unit>` dan keluar `1`. |
| `--version` | Cetak versi (`Chloros CLI 1.3.0`). |
| `--help` | Tunjukkan bantuan aras atas. |

### Indeks Perintah

| Perintah | Tujuan |
| --- | --- |
| [`process`](#chloros-cli-process) | Memproses satu folder perekodan Survey3 atau LATTICE secara menyeluruh. |
| [`login`](#chloros-cli-login) | Sahkan mesin ini dengan akaun Chloros+. |
| [`logout`](#chloros-cli-logout) | Padam maklumat log masuk yang disimpan. |
| [`status`](#chloros-cli-status) | Tunjukkan status lesen / pengesahan semasa. |
| [`export-status`](#chloros-cli-export-status) | Kemajuan eksport Thread-4 secara langsung semasa pelaksanaan `process`. |
| [`language`](#chloros-cli-language) | Tetapkan atau senaraikan bahasa paparan CLI (38 disokong). |
| [`set-project-folder`](#project-folder-commands) / [`get-project-folder`](#project-folder-commands) / [`reset-project-folder`](#project-folder-commands) | Folder projek lalai (dikongsi dengan GUI). |
| [`update`](#chloros-cli-update) | Semak dan pasang kemas kini Chloros (Linux/Jetson). |
| [`selftest`](#chloros-cli-selftest) | Diagnostik sistem + ujian asap. |
| [`time-sync`](#chloros-cli-time-sync) | Status / kawalan grandmaster PTP. |
| [`lattice`](#chloros-cli-lattice) | Kawalan & tangkapan kamera LATTICE (45+ subperintah). |
| [`daq`](#chloros-cli-daq) | Kawalan penderia spektral DAQ (DAQ-U / DAQ-M / DAQ-E). |
| [`project`](#chloros-cli-project) | Buka dan jalankan projek Chloros yang disimpan (kamera + DAQ). |

---

## Pemasangan

`chloros-cli` disertakan dalam pemasang desktop Chloros di setiap platform yang disokong — tiada muat turun CLI berasingan. Memasang pakej platform menambah `chloros-cli` ke dalam `PATH` anda bersama aplikasi desktop dan binari backend yang diendalikannya.

Muat turun terkini: [`https://mapir.gitbook.io/chloros/download`](https://mapir.gitbook.io/chloros/download)

> Pemuat juga disertakan skrip pelancar mudah (`Chloros_CLI.bat` / `Chloros_CLI.ps1`, `Launch_CLI.*`, `chloros-cli.sh`) yang membuka shell CLI yang sedia digunakan.

### Windows (.exe)

1. Muat turun pemasang Windows daripada halaman muat turun.
2. Jalankan `Chloros Installer 1.3.0.exe` dan ikut penolong pemasangan. Laluan pemasangan lalai ialah `C:\Program Files\Chloros\` (CLI diletakkan di `C:\Program Files\Chloros\cli\`, yang ditambah ke dalam PATH oleh pemasang).
3. Buka terminal baru (`cmd.exe`, PowerShell, atau Windows Terminal) supaya `PATH` yang dikemas kini dikesan.

```powershell
chloros-cli --version
```

Pemuat turun secara automatik menambah `chloros-cli.exe` ke dalam laluan `PATH` sistem anda dan membundel pemacu kamera LATTICE. Backend adalah `C:\Program Files\Chloros\backend\chloros-backend.exe` (folder `backend` dalam folder pemasangan anda), yang tidak terdapat pada `PATH`.

**Tetapan sistem.** Halaman *Pilih Tugas Tambahan* pemasang mempunyai dua pilihan di bawah **Tetapan sistem**, kedua-duanya**disemak secara lalai**:

| Pilihan (sebagaimana yang tertera dalam pemasang) | Apa yang dilakukannya | Opt-out pemasangan senyap |
| --- | --- | --- |
| **Aktifkan laluan panjang Windows** (membenarkan folder projek berada lebih dalam daripada 260 aksara; disyorkan) | MenetapkWindows `LongPathsEnabled` pada skala mesin kepada 1. Windows dihantar dengan ia dimatikan, dan kemudian tiada laluan boleh melebihi 260 aksara; produk Chloros terletak sehingga kira-kira 130 aksara di bawah folder projek, jadi folder projek yang lebih dalam ditolak (lihat [Troubleshooting](#troubleshooting-pointers)). Berkuat kuasa untuk program yang dimulakan selepas pemasangan. Kekal diaktifkan semasa menyahpasang, kerana program lain mungkin bergantung padanya. | `/MERGETASKS="!longpaths"` |
| **Mengecualikan backend Chloros daripada imbasan Microsoft Defender**(perekodan kamera lebih pantas; disyorkan) | Menambah pengecualian**proses** Defender untuk `chloros-backend.exe` dalam folder pemasangan, supaya perlindungan masa nyata tidak mengimbas setiap fail yang ditulis oleh backend, di mana sahaja ia menulisnya. Tiada program lain dikecualikan; pengecualian akan dialihkan semasa menyahpasang. Sekiranya Defender tiada atau dikunci oleh dasar, langkah ini akan gagal dengan senyap dan pemasangan akan diteruskan. | `/MERGETASKS="!defenderexclusion"` |**Mengapa pilihan Defender penting:** pada kadar tangkapan yang tinggi, pengimbasan masa nyata pada fail tangkapan menelan kos kira-kira 30% daripada kadar tangkapan (diukur pada tangkapan berterusan 9 kamera: 77.7 tangkapan/s dikecualikan, 52.7 diimbas). `lattice array-capture --continuous` mencetak nota apabila ia mengukur Defender menggunakan satu teras CPU atau lebih pada tangkapan; jalankan semula pemasang dengan pilihan itu dicentang.

Pemasangan senyap (`/SILENT` atau `/VERYSILENT`) menerapkan kedua-duanya melainkan dinyah pilih, contohnya `"Chloros Installer 1.3.0.exe" /VERYSILENT /MERGETASKS="!defenderexclusion"`.

**Tembok api Windows.** Pemuat turun sentiasa menambah dua peraturan masuk untuk `chloros-backend.exe` pada setiap profil rangkaian, dan penyahpasang akan membuangnya: `Chloros Backend (UDP-In)`, untuk aliran kamera, PTP, mDNS dan aliran data DAQ-E, dan `Chloros Backend (TCP-In)`, terhad kepada subnet tempatan, untuk kemas kini firmware DAQ-E, di mana sensor menyambung semula ke komputer ini untuk mengumpul imej (lihat [Penyetelan Peranti DAQ-E](#daq-e-device-settings)). API HTTP backend kekal terikat pada `127.0.0.1`.

### Linux amd64 (.deb)

Untuk stesen kerja x86_64 berasaskan Ubuntu 22.04 LTS atau lebih baru / Debian.

> **Ubuntu 20.04 tidak disokong.** Senarai kebergantungan pakej ini diperoleh daripada
> apa yang sebenarnya dipautkan oleh backend, dan itu termasuk `libc6 (>= 2.35)`;
> focal menghantar glibc 2.31. `apt` menolak pemasangan daripada membiarkannya gagal semasa
> runtime. Senarai itu juga menyenaraikan `libgl1`, yang dimuatkan oleh OpenCV terbungkus dan mungkin tiada pada imej pelayan minimal; `apt install` menyediakannya.

```bash
sudo apt install ./chloros_1.3.0_amd64.deb
chloros-cli --version
```

Pemasangan .deb:
- `chloros-cli` ke `/usr/bin/chloros-cli`, dan `chloros-backend` (memulakan backend) ke `/usr/bin/chloros-backend`
- Backend yang disusun dan CLI, dibuka, dalam `/usr/lib/chloros/backend/` dan `/usr/lib/chloros/cli/`
- Pemacu kamera LATTICE
- Model penapis hingar, pakej penentukuran, dan konfigurasi saluran kemas kini
- SDK Python untuk sistem `python3` (`import chloros_sdk`). `apt install` juga membawa keperluan-keperluan, `python3-numpy` dan `python3-requests` (disyorkan, bukan wajib: program-program di atas bersifat berdiri sendiri; `dpkg -i` melangkau mereka, dan keluaran pemasangan menyenaraikan apa yang hilang). Dalam virtualenv: `pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl`
- `/etc/sysctl.d/60-chloros-gige.conf`, yang meningkatkan penimbal penerimaan soket kernel untuk kamera meningkatkan kepada 32 MB, dan menerapkannya serta-merta (nilai yang sudah ditetapkan lebih tinggi dibiarkan seperti sedia kala). Mengalih keluar pakej akan memadam fail tersebut. Lihat [Linux hosts — buffer penerimaan soket](#linux-hosts--socket-receive-buffers).
- Dua perkhidmatan systemd pilihan untuk backend, kedua-duanya tidak diaktifkan (seksyen seterusnya)

#### Menjalankan backend sebagai perkhidmatan (tanpa kepala)

Tanpa perkhidmatan, backend (dan bersama-sama dengannya grandmaster PTP) hanya berjalan selagi sesuatu telah memulakannya: `chloros-backend` dalam terminal, skrip SDK, atau arahan teras `chloros-cli` untuk tempoh jalannya. Untuk backend yang sentiasa aktif pada mesin tanpa papan pemuka, aktifkan perkhidmatan **sebagai pengguna anda**:

```bash
sudo systemctl enable --now chloros-backend@$USER.service    # $USER expands to your user name
systemctl status chloros-backend@$USER.service
```

`chloros-backend@<user>.service` menjalankan backend sebagai pengguna tersebut. Rakaman yang ditulisnya adalah milik mereka, log terperincinya berada dalam `~/.cache/chloros/logs` mereka, dan ia menggunakan `chloros-cli login` mereka (`~/.chloros`), jadi log masuk sekali sebagai pengguna itu terlebih dahulu. PTP tidak memerlukan root. Seperti setiap backend yang dipasang, ia hanya mendengar pada `127.0.0.1:5000`.

`chloros-backend.service` menjalankan backend sebagai **root**. Ia masih dipasang supaya mesin yang diaktifkannya terus berjalan, tetapi segala yang ditulis oleh backend root milik root, jadi pengguna tidak dapat memadam tangkapan mereka sendiri. Untuk beralih ke pengguna anda:

```bash
sudo systemctl disable --now chloros-backend.service
sudo systemctl enable --now chloros-backend@$USER.service
```

Log terperinci perkhidmatan root berada di `/var/log/chloros`, boleh dibaca oleh ahli kumpulan `adm` (perkhidmatan menetapkan `CHLOROS_LOG_DIR`; lihat [Pembolehubah Persekitaran](#environment-variables)). Memasang pakej mencetak arahan `enable` dengan nama pengguna anda diisi, dan suis juga apabila perkhidmatan root diaktifkan.

**Aktifkan satu unit backend.** Hanya satu backend berjalan pada satu masa. Unit kedua yang diaktifkan (`chloros-backend.service` di samping `chloros-backend@<user>.service`, atau dua unit `@` pengguna) menolak untuk bermula dan kekal `failed` dengan status keluar 98; systemd tidak memulakannya semula. `systemctl status` memberitahu sebabnya: `Another instance is already running. Exiting.`, kemudian apa yang memegang backend (port 5000, atau fail kunci dan pengguna yang dimilikinya), kemudian `systemd will not restart <unit> (exit status 98)`. Selepas but semula, mana-mana unit mungkin yang akan berjalan. Untuk menukar, nyahdayakan unit yang tidak anda gunakan, seperti di atas.**CLI tidak pernah menghentikan backend perkhidmatan.** `chloros-cli` menggunakannya seperti sedia ada, sama seperti GUI dan SDK: `process` berjalan padanya, memuatkan projeknya sendiri menggantikan projek terakhir. Di mana CLI sebaliknya akan memulakan semula backend (`--restart`, atau `process` yang pengimportannya tidak menemui apa-apa), ia menamakan unit itu, mencetak `sudo systemctl restart <unit>` dan keluar `1`, kerana backend yang dihentikan dengan cara itu akan kekal terhenti. `GET /api/status` menamakan unit sebagai `service` (`null` untuk backend yang tiada perkhidmatan dijalankan).**Kemas kini dan penyingkiran.** Mengupgred pakej menghentikan setiap perkhidmatan backend, kemudian memulakan semula setiap yang diaktifkan, unit `@<user>` terlebih dahulu (satu yang dimulakan secara manual tetapi tidak pernah diaktifkan kekal berhenti, seperti selepas but semula). Apabila lebih daripada satu diaktifkan, hanya satu yang akan berjalan dan yang lain kekal gagal seperti di atas; peningkatan mencetak `Chloros: N backend units are enabled: …` dan `Keep one enabled: sudo systemctl disable --now <unit>`. Mengeluarkannya menghentikan dan menyahaktifkannya. Membersihkannya juga memadam `/var/log/chloros`.

### Linux arm64 — Jetson (JetPack 6)

```bash
sudo apt install ./chloros_1.3.0_arm64_jp6.deb
chloros-cli --version
```

Susun atur yang sama seperti .deb amd64, dengan penimbal penerimaan dan [perkhidmatan backend](#running-the-backend-as-a-service-headless) disertakan, untuk Jetson Orin / Orin NX / Orin Nano pada **JetPack 6** (L4T R36). PyTorch-nya ialah binaan CUDA JetPack 6 untuk Orin, jadi pemprosesan GPU, termasuk debayer yang menyokong tekstur, dijalankan pada GPU Jetson. Pakej ini tidak menyertakan CUDA itu sendiri: ia menggunakan pustaka CUDA dan cuDNN yang dipasang oleh JetPack 6 (`sudo apt install nvidia-jetpack` pada imej yang tiada pustaka tersebut). Apabila tiada GPU CUDA boleh digunakan, pemprosesan dijalankan pada CPU dan pemprosesan bertekstur menggunakan debayer standard (lihat [`process`](#chloros-cli-process)).

### Autentikasi Sekali Seperangkat

Setiap platform memerlukan log masuk Chloros+ sekali sahaja sebelum panggilan SDK/CLI berfungsi:

```bash
chloros-cli login user@example.com 'YourPassword'
```

Butiran pengesahan disimpan dalam cache di `~/.chloros/user_session.json`.

### Mulakan Backend

Perintah `lattice`, `project` dan `daq` berkomunikasi dengan backend yang sudah berjalan; ia tidak pernah memulakannya (lihat [Perjanjian](#conventions)). Pastikan satu daripadanya berjalan semasa anda menggunakannya:

| Platform | Cara |
| --- | --- |
| Windows | Buka aplikasi desktop Chloros (backendnya juga menyediakan CLI), atau jalankan `"C:\Program Files\Chloros\backend\chloros-backend.exe"` dalam terminalnya sendiri (folder `backend` dalam folder pemasangan anda) dan biarkan ia terbuka. |
| Linux / Jetson | Jalankan `chloros-backend` dalam terminalnya sendiri, atau pastikan satu sentiasa berjalan dengan [perkhidmatan backend](#running-the-backend-as-a-service-headless). |

Hanya satu backend berjalan pada satu masa; setiap klien (GUI, CLI, skrip SDK) berkongsi ia. Perintah teras (`process`, `login`, `status`, …) juga menggunakannya, dan memulakannya apabila tiada yang sedang berjalan.

Satu backend terus berjalan antara arahan, jadi arahan seterusnya tidak memulakan semula backend tersebut. Ia berhenti apabila anda log keluar (`chloros-cli logout`, melainkan aplikasi Chloros dibuka atau larian atau binaan luar talian sedang dijalankan), apabila anda menjalankan `chloros-cli backend stop`, atau secara automatik selepas 30 minit tanpa sebarang penggunaan: tiada arahan, skrip atau aplikasi, tiada larian atau binaan luar talian, tiada kamera atau penderia cahaya yang disambungkan. `backend stop` menolak semasa larian atau binaan luar talian sedang dijalankan, menamakan ia; `--force` menghentikannya sekarang dan larian atau binaan itu hilang (satu burst dikekalkan, dan `array-build-video` membinanya semula). Set `CHLOROS_BACKEND_IDLE_MIN` sebelum backend mula untuk menukar 30 minit (`0`: tidak pernah); [perkhidmatan backend](#running-the-backend-as-a-service-headless) tidak akan berhenti sendiri. `chloros-cli backend status` menunjukkan sama ada ada yang sedang berjalan, apa yang menggunakannya dan sebarang binaan luar talian yang sedang berjalan. `process` membuka projeknya dalam backend yang sedang berjalan, melainkan aplikasi Chloros dibuka dengan projek yang ia dibuka: maka ia akan menolak, dan anda perlu memproses dari aplikasi atau menutup projek itu terlebih dahulu. Projek yang ditinggalkan terbuka oleh `process` sebelum ini atau skrip SDK hanya digantikan.

### Semak Pasang

```bash
chloros-cli --version           # prints "Chloros CLI 1.3.0"
chloros-cli selftest            # 7-step diagnostic (backend, GPU, models, CUDA)
chloros-cli status              # shows license tier + logged-in user
```

`selftest` hanya akan keluar apabila kesemua tujuh semakan lulus. Semakan terakhir memerlukan GPU CUDA (GPU NVIDIA, atau Jetson Orin) dan model penapis hingar, jadi pada komputer yang tiada GPU tersebut ia akan menyenaraikan `Failed: CUDA + Denoiser` dan keluar daripada `1`; semua kecuali debayer sedar tekstur yang hanya untuk GPU masih berfungsi di sana.

> **Langganan Chloros+ diperlukan.**CLI memerlukan pelan Chloros+ yang aktif.**Copper**ialah peringkat kemasukan Chloros+ — setiap peringkat berbayar Chloros+ mempunyai akses CLI/SDK; hanya peringkat percuma**Iron** tidak mempunyai. (Peta ID-pelan: `0`=Iron/percuma, `1`=Copper, `2`=Bronze, `3`=Silver, `4`=Gold.) Tingkatkan di [`https://cloud.mapir.camera/pricing`](https://cloud.mapir.camera/pricing).
>
> Had ini dikuatkuasakan oleh backend, bukan hanya oleh CLI: permintaan yang di-flag oleh SDK/CLI tanpa pelan berbayar akan ditolak dengan `403 PLAN_UPGRADE_REQUIRED`, sama ada ia datang dari `chloros-cli`, Python SDK, atau klien HTTP yang dibangunkan sendiri. Sebaliknya, pemanggil yang telah log keluar akan menerima `401 AUTH_REQUIRED`. Akses berfungsi secara luar talian untuk tempoh moratorium pelan (30 hari bagi pelan bulanan, sehingga tamat tempoh bagi pelan tahunan) dan berhenti apabila tempoh itu tamat; `chloros-cli status` terus berfungsi supaya sebabnya dapat dilihat (ia adalah satu-satunya laluan SDK/CLI yang dikecualikan daripada pintu peringkat — `GET /api/license-status`).

---

## Panduan Cepat Lima Minit

```bash
# 1. Sign in once on this machine
chloros-cli login user@example.com 'YourPassword'

# 2. Survey3 / LATTICE folder → finished radiance + NDVI in one call
chloros-cli process "/home/user/captures/flight_001" \
  --vignette --reflectance --indices NDVI GNDVI OSAVI

# 3. The lattice / daq commands need a running backend: on Windows open the
#    Chloros desktop app; on Linux start one (or see "Start the Backend")
chloros-backend &

# 4. Take a single LATTICE photo with the first camera found
chloros-cli lattice capture -o output/

# 5. Connect a 4-cam LATTICE array with the GUI's smart-prep flow
chloros-cli lattice array-connect \
  --serials 213800234,214000533,214701288,214701292

# 6. Read a spectrum from a connected DAQ-U (Windows: --port COM3)
chloros-cli daq pool-connect --port /dev/ttyUSB0
chloros-cli daq pool-latest --sensor-id CB-7C-A8-2E-5F   # id from 'daq pool-list'
```

`process` (langkah 2) memulakan backend untuk jalannya sendiri apabila tiada yang sedang berjalan dan menghentikannya apabila selesai, jadi mulakan satu untuk langkah 4 dan seterusnya. Lihat [Mulakan Backend](#start-the-backend).

---

## `chloros-cli process`

Proses satu folder imej melalui keseluruhan paip Chloros (pengesanan sasaran → penentukuran → vignette → pantulan → eksport indeks).

### Sinopsis

```
chloros-cli process INPUT [OPTIONS]
```

### Argumen Posisi

| Argumen | Deskripsi |
| --- | --- |
| `INPUT` | Laluan ke folder input yang mengandungi fail `.raw + .jpg` (Survey3), `.tif/.tiff` (LATTICE), atau `.dng`. |

### Pilihan Umum

| Penanda | Lalai | Keterangan |
| --- | --- | --- |
| `-o, --output PATH` | satu folder baru yang dinamakan oleh `-n` dalam folder projek lalai | Folder projek. Projek dinamakan sempena folder itu, dan folder yang sudah mengandungi `project.json` akan mendapat `_1` / `_2` saudara seiring daripada digantikan. Folder projek lalai ialah yang dipilih oleh `set-project-folder` (atau GUI), jika tidak `Documents/MAPIR/Chloros Projects`. |
| `-n, --project-name NAME` | auto (cap masa) | Nama projek, digunakan hanya **tanpa** `-o` (dengan `-o` projek mengambil nama folder). |
| `--debayer {standard,texture-aware}` | `standard` | `texture-aware` menggunakan debayer neural Chloros+ untuk imej Survey3; lebih perlahan tetapi kualiti lebih tinggi. Ia hanya untuk Survey3: Tangkapan LATTICE sentiasa menggunakan demosaik piawai, jadi untuk folder yang mengandungi tangkapan LATTICE, `process` menolak `texture-aware` selepas import, dengan ralat yang menyatakan berapa banyak daripadanya, dan keluar `1` sebelum memproses apa-apa (aplikasi Chloros juga tidak menawarkan Texture Aware untuknya). Ia memerlukan GPU CUDA (GPU NVIDIA pada Windows / Linux x64, GPU Orin pada Jetson): tanpa satu, larian menggunakan `standard`, dan backend mencatat satu baris `[DEBAYER] … needs a CUDA GPU …` yang menyatakan sebabnya (ditunjukkan dengan `--verbose`). |
| `--vignette / --no-vignette` | `--vignette` | Pembetulan vignet. |
| `--reflectance / --no-reflectance` | `--reflectance` | Kalibrasi reflektansi (menggunakan sasaran panel jika ditemui, kalibrasi NIST per-siri untuk LATTICE). Untuk LATTICE multispectral ini juga berfungsi sebagai **produk** reflektansi alih — lihat [Perintah Alih Eksport Per-Produk](#per-product-export-toggles-lattice-multispectral). |
| `--ppk` | off | Terapkan pembetulan PPK GNSS daripada fail sidecar. |
| `--exposure-pin-1 CAMERA_MODEL` | none | Model kamera yang disambungkan ke pin pendedahan 1, untuk penyelarasan masa PPK. |
| `--exposure-pin-2 CAMERA_MODEL` | none | Model kamera yang disambungkan ke pin pendedahan 2, untuk penyelarasan masa PPK. |
| `--recal-interval SECONDS` | `0` | Jarak selang kalibrasi semula minimum, dalam saat (*Minimum recalibration interval* projek). |
| `--timezone-offset HOURS` | `0` | Offset zon waktu penderia cahaya daripada UTC, dalam jam (*Offset zon waktu penderia cahaya* projek). |
| `--format FORMAT` | `TIFF (16-bit)` | Salah satu daripada `TIFF (16-bit)`, `TIFF (32-bit, Percent)`, `TIFF (8-bit)`, `PNG (8-bit)`, `JPG (8-bit)`. Chloros tidak pernah meningkatkan kedalaman bit imej: pada semua imej 8-bit, format yang lebih mendalam ditulis sebagai `TIFF (8-bit)` dan run memberitahu demikian sekali sahaja. Dalam projek yang menggabungkan tangkapan 8-bit dan 12/16-bit, setiap tangkapan dieksport pada kedalaman sendiri atau lebih rendah. Radiance sentiasa 32-bit titik apung. |
| `--indices NAME [NAME ...]` | tiada | Indeks vegetasi mengikut nama pratetap (`NDVI`, `GNDVI`, `OSAVI`, `SAVI`, `EVI`, `GCI`, …), tidak sensitif huruf besar-kecil. Setiap satu ditambah ke dalam projek untuk jalur kamera masing-masing, dengan projek baru disertakan. Nama yang bukan pratetap, atau yang jalur kamera tidak dapat menjana (`NDRE` memerlukan RE dan NIR, yang tiada satu pun kamera menyediakan), dinamakan dalam amaran dan tidak mendapat imej indeks. |
|
`--input-level {auto,raw,debayered,processed}` | `auto` | Memaksa titik kemasukan saluran paip untuk LATTICE TIFFs (Survey3.raw tidak terjejas). Juga pintu pelarian yang membolehkan tangkapan tanpa **raw** diproses sama sekali — lihat [Rupa folder tangkapan](#what-a-captures-folder-looks-like). |
| `--debayered / --no-debayered` | on | Keluarkan produk debayered linear (`Debayered_Images`). Lihat [Per-Product Export Toggles](#per-product-export-toggles-lattice-multispectral). |
| `--preview / --no-preview` | dihidupkan | Keluarkan pratonton paparan (`Preview_Images`): RGB = imbangan putih (DAQ-illuminant apabila tersedia, jika tidak dunia-kelabu) + gamma; multispec = regangan warna palsu. |
| `--radiance / --no-radiance` | on | Keluarkan radiasi float32 (`Radiance_Images`, W/m²/sr/nm). |
| `--reflectance-source {daq,target,auto}` | `auto` | Rujukan untuk produk pantulan LATTICE: `auto` = sasaran dalam bingkai yang lulus QA adalah rujukan mutlak (sasaran dengan bendera QA bersaing dengan DAQ pada skor yang diukur), sandaran DAQ-downwelling (ρ = π·L/E); `target` = ketat (tiada penggantian DAQ); `daq` = DAQ-otoritatif. Lihat [Perintah Eksport Per-Produk](#per-product-export-toggles-lattice-multispectral). |
| `--target-reflectance-dir DIR` | tiada | Direktori imbasan pantulan sasaran **diukur** per-unit (`<serial>.csv`); berpaling kepada spektra T3/T4P nominal sekiranya gagal. |
| `--array-alignment / --no-array-alignment` | on | Susunan LATTICE: terapkan penjajaran modul-ke-modul yang dicap dalam setiap XMP `Chloros:Alignment*` pada setiap produk yang diproses (debayered / pratonton / radiasi / pantulan / indeks). Tiada tindakan untuk imej tanpa tag. |
| `--array-alignment-crop / --no-array-alignment-crop` | crop | Menyunting eksport yang diselaraskan ke kawasan tumpang tindih biasa tatasusunan supaya semua modul berkongsi jejak yang sama; `--no-…` mengekalkan kanvas penderia penuh (isi hitam di luar sumber). |
| `--array-alignment-interp {bilinear,nearest,cubic}` | `bilinear` | Pengsampelan semula untuk pemutar penyelarasan. `nearest` mengekalkan DN sumber yang tepat (tiada pencampuran nilai radiometrik antara piksel). |

### Pilihan Pengesanan Sasaran

| Bendera | Deskripsi |
| --- | --- |
| `--min-target-size PIXELS` | Saiz minimum sasaran penentukuran dalam piksel untuk pengesan. |
| `--target-clustering 0-100` | Peratusan pengelompokan sasaran (lalai `60`). |
| `--target / --targets` | Mencari sasaran penentukuran hanya dalam subfolder `target` atau `targets` bagi input (lebih pantas). |

### Contoh

```bash
# Simplest: defaults are good for most surveys
chloros-cli process "/home/user/images/survey_001"

# Multi-index with explicit format
chloros-cli process "/home/user/images/survey_001" \
  --vignette \
  --reflectance \
  --format "TIFF (32-bit, Percent)" \
  --indices NDVI GNDVI OSAVI

# Texture-aware debayer for highest quality (Chloros+ only)
chloros-cli process "/home/user/images/survey_001" \
  --debayer texture-aware \
  --indices NDVI

# Process LATTICE captures explicitly (auto-detects from EXIF normally)
chloros-cli process "/home/user/captures/lattice_flight" \
  --input-level processed

# LATTICE multispectral → float32 radiance only (no DAQ downwelling needed)
chloros-cli process "/home/user/captures/lattice_flight" \
  --no-debayered --no-preview --no-reflectance

# LATTICE reflectance anchored to an in-frame target (strict, no DAQ fallback),
# with per-unit measured target scans looked up by serial
chloros-cli process "/home/user/captures/lattice_flight" \
  --reflectance-source target --target-reflectance-dir "/home/user/target_scans"

# LATTICE array capture: keep native geometry (ignore stamped alignment)
chloros-cli process "/home/user/captures/array_flight" \
  --no-array-alignment

# Aligned, uncropped, value-preserving resampling
chloros-cli process "/home/user/captures/array_flight" \
  --no-array-alignment-crop --array-alignment-interp nearest

# Save to a project folder of your choice (the project is named after it)
chloros-cli process "C:/input" -o "C:/projects/Field_A_2026-05-26"

# Name the project and let it go to the default project folder
chloros-cli process "C:/input" -n "Field_A_2026-05-26"
```

### Suis Eksport Per-Produk (LATTICE multispektral)

Pemprosesan LATTICE berkembang ke **setiap produk yang terpakai dalam satu langkah**. Empat suis per-jenis — `--debayered`, `--preview`, `--radiance`, `--reflectance` — semuanya**DIHIDUPKAN secara lalai**; gunakan borang `--no-<type>` untuk mematikan satu. kamera induk RGB hanya mengeluarkan data debayered + pratonton (tiada radiasi/refleksi bagi setiap jalur), jadi `--radiance`/`--reflectance` adalah no-op untuk mereka. Togol diabaikan untuk Survey3 `.raw` (yang mengikuti laluan pantulan/sasaran piawai). *(X lamapenanda  telah **dibuang** dan digantikan dengan suis ini; tiada lagi tahap `sensor-response`.)*

| Produk | Keluaran | DAQ downwelling diperlukan? |
| --- | --- | --- |
| `--debayered` | Demosaik linear (`Debayered_Images`): fail yang sama yang disimpan oleh tangkapan pada tahap `debayered` — offset gelap pek kalibrasi (DSNU) daripada data mentah, kemudian demosaik sedar tepi OpenCV; tiada LUT lineariti, medan rata atau pencampuran semula. | Tidak. |
| `--preview` | Pratonton paparan (`Preview_Images`): RGB = WB + gamma; multispec = regangan warna palsu. | Tidak. |
| `--radiance` | float32 W/m²/sr/nm daripada rantaian radiometrik penuh (`Radiance_Images`). | Tidak. |
| `--reflectance` | uint16 pantulan ρ (`32768` = 1.0), Sedia untuk Pix4D. | **Ya**, melainkan sasaran dalam bingkai yang lulus QA mengankernya (lihat di bawah). |

`--reflectance-source` memilih rujukan pantulan:**`auto`**(lalai) menjadikan sasaran dalam bingkai yang lulus QA sebagai**rujukan mutlak**— rantaian garisan empirikal yang dipatangkan pada sasaran disemak silang pada panel yang ditinggalkan dan pemenang yang diukur diterapkan, satu jalur demi satu: jalur yang DAQ tidak dapat kalibrasi, atau yang kecocokan cerun DAQ-nya gagal, mengambil garisan sasaran itu sendiri (`ReflectanceElBandMethods` menentukan yang mana) — sasaran yang lulus QA dengan bendera (panel terbuang, jalur tidak mengikut susunan) juga bersaing dengan pembahagi DAQ pada skor yang diukur itu, dan bingkai yang kalah kepadanya dicop `ReflectanceChain=daq` — kembali kepada pembahagi DAQ downwelling (ρ = π·L/E) apabila tiada sasaran hadir atau QA gagal;**`target`**adalah ketat (tiada penggantian DAQ);**`daq`**memilih untuk menggunakan tingkah laku berkuasa DAQ. Geometri sasaran (ArUco / ROI tetap / jalur) diambil daripada konfigurasi sasaran projek; `--target-reflectance-dir DIR` menyimpan**diukur* per unit* imbasan (`<serial>.csv`) dicari berdasarkan nombor siri/QR unit sasaran, dengan spektra T3/T4P nominal sebagai pilihan alternatif.

Laluan pantulan DAQ menyelesaikan **sinar menuruni yang sepadan cap masa**secara automatik daripada**`.daq`**yang dirakam (DAQ-U/M/E) atau `.csv` asli DAQ-M yang ditemui bersama imej. Jika bundel penentukuran per-kamera atau DAQ tidak disimpan secara tempatan, saluran paip**memuat turun secara automatik dari AWS** pada penggunaan pertama (perlu internet sekali; disimpan dalam `~/.chloros/`).**Siapa pembacaan ini.**Pembacaan yang disimpan oleh `.daq` adalah pembacaan kamera (atau kamera-kamera) yang diterangkan — dinamakan dalam fail sidecar `<file>.daq.cameras.json` di sebelahnya (`lattice capture`, `multi-capture`, `array-capture` dan GUI sama; burst mentah menamakan bacaan setiap ahli dalam manifesnya) — dan `process` memadankannya**hanya dengan imej daripada kamera-kamera tersebut**. Kamera berdiri sendiri tanpa bacaan sendiri oleh itu tidak lagi meminjam DAQ kamera lain; ikat DAQ padanya, atau rakam satu. Rakaman DAQ itu sendiri (`daq pool-record`, atau rakaman yang dimulakan dalam GUI), atau mana-mana `.daq` tanpa fail sidecar itu (termasuk tangkapan lama), masih berkhidmat untuk setiap kamera.

#### Membaca piksel pantulan (Pix4D / Metashape / skrip anda sendiri)

Pantulan disimpan sebagai DN integer, dan **DN yang bermaksud ρ = 1.0 bergantung pada kamera sumber**:

| Sumber | ρ = 1.0 adalah | Cara untuk mengetahui |
| --- | --- | --- |
| LATTICE (M3C / M3M) | `32768` (ruang lebih sehingga ρ 2.0) | XMP `Chloros:PixelScale=32768` dicop pada fail. |
| Survey3 | `65535` (dipotong pada ρ 1.0) | Tiada tag XMP `Chloros:*` — ketiadaan itu *ialah* isyaratnya. |

**Baca `Chloros:PixelScale` dan bahagikannya dengannya** daripada menganggap ia sebagai pemalar. Tag ini ditakrifkan dalam domain uint16, jadi ia kekal `32768` merentasi format keluaran yang menimbang semula — `TIFF (16-bit)`, `TIFF (8-bit)`, `PNG (8-bit)`, `JPG (8-bit)` dan `TIFF (32-bit, Percent)` semuanya menerangkan sendiri (normalkan jenis data yang disimpan kembali ke uint16 terlebih dahulu: ×257 daripada 8-bit, ×65535 daripada float).

> **Fail daripada binaan lama mungkin tidak mempunyai skala.**Satu TIFF reflektansi 8-bit diskalakan semula dan ditandakan `Chloros:PixelScale` seperti yang lain. Binaan sebelum 1.3.0 memotongnya kepada 0..255 sebaliknya, jadi setiap nilai melebihi ρ≈0.008 menjadi rata kepada 255, dan mereka mengabaikan kedua-dua tuple `Chloros:PixelScale` dan `MicaSense:RadiometricCalibration`.**Jika tag itu tiada pada fail reflektansi LATTICE, jangan andaikan wujud skala — proses semula ia** daripada membahagikan piksel yang tidak pernah boleh dibahagikan.

#### Membaca piksel indeks (NDVI dan indeks vegetasi lain)

**Raster indeks integer adalah regangan kontras bagi setiap imej, bukan peta −1…1.** `TIFF (16-bit)`, `TIFF (8-bit)`, Produk indeks `PNG (8-bit)` dan `JPG (8-bit)` — termasuk TIFF 8-bit yang dirakam — menyimpan

```

DN = (value − IndexMin) / (IndexMax − IndexMin) × full_scale
```

di mana `IndexMin` / `IndexMax` adalah nilai indeks terhingga terendah dan tertinggi bagi bingkai tersebut. DN 0 bukan −1, dan skala penuh bukan +1.

Chloros menandakan kedua-dua hujung supaya pemetaan kekal boleh dibalikkan:

| Tag | Maksud |
| --- | --- |
| `Chloros:IndexScaling` | `linear_minmax` — konvensyen di bawah |
| `Chloros:IndexMin` | nilai indeks yang disimpan sebagai DN 0 |
| `Chloros:IndexMax` | nilai indeks yang disimpan sebagai skala penuh |

Baca dan terbalikkan, menormalkan DN yang disimpan kepada 0…1 dengan terlebih dahulu menggunakan had maksimum dtype yang disimpan supaya satu ungkapan merangkumi setiap format integer:

```python
import json, subprocess
import numpy as np, tifffile

tags = json.loads(subprocess.run(
    ["exiftool", "-j", "-XMP:all", path],
    capture_output=True, text=True).stdout)[0]

# float(): exiftool hands back a negative real as a quoted string, so
# IndexMin arrives as str on most real NDVI data while IndexMax is a number.
lo, hi = float(tags["IndexMin"]), float(tags["IndexMax"])

raw = tifffile.imread(path)                    # uint16 for TIFF (16-bit)
ndvi = lo + raw.astype(np.float64) / np.iinfo(raw.dtype).max * (hi - lo)
```

Membaca tidak memerlukan `-config pix4d.config` — exiftool membaca sifat XMP ini tanpa bantuan. Konfig hanya diperlukan untuk *menulis*nya.

> **`IndexMin == IndexMax`** bermaksud setiap piksel pada bingkai itu menyimpan nilai indeks yang sama. Raster diisi pada skala pertengahan dan tiada apa-apa untuk dibahagikan — ambil titik hujung.

> **Tiada tag `Chloros:Index*` bermakna tiada pemetaan yang dinyatakan.** Eksport LUT ialah render berwarna yang pikselnya berwarna gradasi yang dikaitkan dengan tingkap ambang LUT anda, dan bingkai tanpa piksel indeks terhingga tiada julat untuk dinyatakan. Jangan anggap ada pemetaan dalam mana-mana kes — peraturan yang sama seperti apabila `PixelScale` hilang.

Kerana regangan adalah bagi setiap imej, dua bingkai daripada satu penerbangan tidak boleh dibandingkan sebagai DN**— setiap satu membawa titik hujungnya sendiri, jadi NDVI yang sama mendarat pada DN yang berbeza dalam imej yang berbeza. Untuk kerja kuantitatif merentasi penerbangan (statistik zonal, ambang tetap, siri masa), eksport**`TIFF (32-bit, Percent)`**: raster indeks ditulis di sana sebagai nilai float32**nilai indeks itu sendiri**, tanpa penskalaan semula dan tanpa tag yang diperlukan. Walaupun nama format ini, produk indeks bukan ×100 — penskalaan itu hanya terpakai pada reflektansi jenis data integer.

#### Topeng pengekangan pencampuran semula (piksel mana sesuatu jalur dikekang)

Pencampuran semula spektral 3×3 boleh menjadikan sesuatu jalur negatif — di mana cahaya hampir tidak mengandungi jalur tersebut, atau di mana pemandangan berada di luar apa yang diterangkan oleh matriks pencampuran semula kamera. Nilai itu bukan nilai sebenar, jadi eksport menetapkan nilainya kepada 0 — dan paparan langsung memaparkan nilai 0 yang sama. Sesebuah jalur boleh dikekang pada beberapa piksel atau pada hampir kesemuanya; topeng merekodkan kedua-duanya. `Chloros:Band{R,G,B}UnmixClampFraction` menyatakan berapa banyak bingkai yang terjejas; topeng pengekang menyatakan **piksel mana**:

- **`clamp_masks/<product folder>/<product name>_clampmask.tif`**— dalam folder `clamp_masks/`**di sebelah** folder produk, bukan di dalamnya (`radiance/x_radiance.tif` → `clamp_masks/radiance/x_radiance_clampmask.tif`; ekspor `process` → `tiff16/clamp_masks/Reflectance_Calibrated_Images/x_clampmask.tif`) — untuk setiap radiasi, produk pantulan dan indeks di mana sekurang-kurangnya satu piksel dikekang: merakam dan mengeksport `process` sama (dan TIFF per-bingkai `array-build-video --save-tiffs`). `uint8` jalur tunggal, pada grid piksel produk itu sendiri (termasuk orientasi, ROI, binning dan penjajaran tatasusunan). Setiap topeng menamakan kamera produknya (`Make` / `Model` / siri), masa tangkapan dan tag lensa, dan menyenaraikan jalur yang diwakili oleh bit-bitnya (`XMP-Camera:BandName`, bit 0 diutamakan); `ImageDescription` dan `Software` menandakannya sebagai topeng klamp, dan ia tidak mempunyai penentukuran.
- **Bit-bit**: bit 0 = jalur dalam saluran R produk (`Chloros:BandRName`), bit 1 = G (`BandGName`), bit 2 = B (`BandBName`). Bit yang ditetapkan bermaksud nilai piksel dalam jalur tersebut telah diikat kepada 0 — anggap ia, dan sebarang indeks yang dibina daripadanya, sebagai rendah keyakinan.
- **`Chloros:UnmixClampMask`** dicop pada setiap produk yang menjalankan unmix: laluan topeng berbanding folder produk itu sendiri (`../clamp_masks/<product folder>/<product name>_clampmask.tif`, sentiasa dipisahkan dengan `/` — gabungkannya dengan folder imej untuk membuka topeng), atau `none` apabila tiada piksel dikekang. Tiada tag bermakna produk tidak pernah menjalankan unmix (kamera RGB atau mono, tahap raw/debayered).
- `lattice capture` / `array-capture` mencetak laluan topeng di bawah fail yang disimpan (`clamp mask: …`), dan senarai keputusan tangkapan menyenaraikannya sebagai `clamp_mask` bingkai tersebut.
- `lattice index --live --save-multiband` menulis satu topeng bagi setiap kamera sumber untuk susunan berbilang jalur — lihat [`lattice index` — topeng susunan berbilang jalur](#lattice-index--multiband-stack-masks). Video tiada topeng: `array-build-video` melaporkan pecahan clamp setiap bingkai sebaliknya (lihat [`array-build-video`](#array-build-video--offline-reprocess-a-saved-burst)).

Kerana tiada folder produk mengandungi topeng, pada mana-mana kedalaman, folder produk (`radiance/`, `reflectance/`, folder `*_Images/` eksport `process`) boleh diberikan kepada Pix4D, Metashape atau perisian fotogrametri lain seperti mana adanya — termasuk subfolder — dan ia hanya menerima imej. Serahkan folder produk itu, bukan folder di atasnya (yang juga mengandungi `clamp_masks/` dan setiap jenis eksport lain). Import Chloros sendiri melangkau topeng. Membaca topeng: `(mask & 1) != 0` ialah "saluran R-channel diklamp di sini", `& 2` G, `& 4` B.

#### EXIF dipindahkan ke dalam eksport

`process` menyalin **blok GPS dan ExifIFD tangkapan sumber** ke setiap produk, jadi eksport membawa `FocalLength`, `FNumber`, `ExposureTime`, `ISO`, `DateTimeOriginal` dan `CameraSerialNumber` bersama georeferensing.**`FocalLength` bukan pilihan untuk fotogrametri.** Pix4D menyelesaikan jarak sampel tanah daripada panjang fokus ditambah ketinggian; tanpa tag, ia kembali kepada skala yang sangat salah. Pada satu penerbangan kebun oren 49-jepretan, ketiadaan tag menjadikan tapak 411 m × 160 m menjadi satu rekonstruksi
47.8 km × 13 km — sebuah ortho 455 MP yang kebanyakannya nodata, yang kemudian dibaca sebagai masalah penilehan dan masalah BigTIFF sebelum sesiapa pun menyemak GSD. Jika ortho anda terhasil pada skala yang tidak munasabah, jalankan `exiftool -FocalLength` ke atas produk yang dieksport terlebih dahulu.

Salinan itu sengaja **tidak** `-all:all`: IFD0'tag struktur memecahkan output LATTICE apabila disalin, dan `ExifImageWidth` / `ExifImageHeight` dikecualikan kerana ia menerangkan tangkapan *sumber* — eksport yang pernah diubah saiznya sebaliknya akan membawa dimensi
bertentangan dengan rasternya sendiri. XMP ditulis terus dan bukannya disalin, kerana ExifTool
membuang tag XMP yang sama-panggilan apabila blok XMP disalin (yang akan menyebabkan kehilangan tag kalibrasi
MAPIR).

### Di manakah keluaran disimpan

Produk ditulis **di bawah folder projek, dikumpulkan mengikut kamera dan kemudian mengikut format fail**:

```
<project>/
└── LATT-M3M-L41-F550/                  # one folder per camera model+lens+filter
    ├── tiff16/
    │   ├── Reflectance_Calibrated_Images/
    │   ├── Debayered_Images/
    │   ├── Preview_Images/
    │   └── <INDEX>_Index_Images/        # e.g. NDVI_Index_Images
    └── tiff32/
        └── Radiance_Images/             # float32 radiance always lands here
```

Folder kamera adalah `LATT-<sensor>-<lens>-F<filter>` untuk LATTICE (menyesuaikan dengan EXIF tangkapan
`Model`) dan `<model>_<filter>` untuk Survey3 — dua kamera yang berkongsi sensor dan penapis tetapi berbeza dalam lensa mengekalkan pokok berasingan, kerana vignette, medan pandangan dan distorsi berbeza. Format
folder mengikuti `--format`: `tiff16`, `tiff8`, `png8`, `jpg8`, atau `tiff32` untuk `TIFF (32-bit, Percent)`.

> **Setiap produk yang dieksport mengekalkan nama fail SUMBER.**Eksport radiasi `capture_…_raw.tif` masih dipanggil `capture_…_raw.tif` — ia hanya terletak di `tiff32/Radiance_Images/`.**Folder mengenal pasti produk, bukan nama fail**, jadi globbing
untuk `*radiance*.tif` tidak menemui apa-apa; padankan pada direktori sebaliknya.

### Rakaman penderia cahaya — `.daq` yang telah dikalibrasi + `.csv`

`process` juga mengendalikan rakaman `.daq` dalam folder input anda, dan ia **tidak**
perlu sebarang imej untuk melakukannya: DAQ-U / DAQ-M / DAQ-E yang diterbangkan bersendirian adalah tangkapan lengkap, dan satu folder yang hanya mengandungi fail `.daq` adalah input yang sah.

Sebuah DAQ boleh dirakam **tanpa**penentukuran — itulah yang [`chloros_scripts`] awam lakukan secara lalai: ia menulis kiraan sensor mentah dan menandakan fail tersebut supaya ia boleh diproses kemudian.(https://github.com/mapircamera/chloros_scripts) perekod (`record_daq.py`) melakukannya secara lalai: mereka menulis kiraan sensor mentah dan menandakan fail supaya Chloros mengambil kalibrasi kilang sensor itu**melalui siri** (cache tempatan dahulu, kemudian Cloud MAPIR) dan menerapkannya. `process` menulis semula keputusan:

```
<project>/
└── Light Sensor/
    ├── <name>_calibrated.daq        # reprocessable archive, declares its bundle
    └── <name>_calibrated.csv        # W/m^2/nm per reading + photometric columns
```

`.csv` membawa satu baris bagi setiap bacaan: cop masa UTC, masa integrasi, kuasa total,
lux fotopik/skotopik, PPFD (dan pembahagian biru/hijau/merah), panjang gelombang puncak, kemudian
spektrum penuh pada grid panjang gelombang sensor itu sendiri. `.daq` mengimport semula tanpa dikalibrasi buat kali kedua.

`<name>` ialah nama fail rakaman itu sendiri. Dua rakaman dalam folder berbeza boleh berkongsi satu bacaan per-penangkapan (`<capture>/daq/daq_data_<sensor id>_<ts>.daq`) yang disimpan pada milisaat yang sama oleh dua kamera — dan setiap satu masih mendapat keluaran masing-masing: yang pertama mengekalkan
`<name>`, yang seterusnya menambah folder yang membezakannya **selepas** nama (`<name>__<capture>_daq_calibrated.daq`), jadi tarikh dan masa rakaman masih diutamakan.

Apabila berjaya, pelaksanaan melaporkan `Light-sensor products written: N (calibrated .daq + .csv)`.
Bahagian dalam kurungan menggambarkan apa yang sebenarnya ditulis, jadi ia berbunyi
`(RAW COUNTS — this sensor has no calibration bundle)` untuk sensor tanpa bundel dan `(N calibrated, M raw counts)` untuk folder yang memegang kedua-duanya. Kejadian utama backend sendiri `[DAQ-EXPORT]` dan `[RUN-SUMMARY]` mendapatkan kata-kata mereka dengan cara yang sama — tiada satu pun daripada ketiga-tiganya boleh menganggap eksport mentah itu telah dikalibrasi.

Rakaman DAQ-U / DAQ-M / DAQ-E yang bundel penentukurannya tidak dapat diambil — anda tidak dalam talian, atau sensor itu tiada penentukuran dalam fail — adalah **dilangkau dengan sebab** pada baris
`[DAQ-EXPORT]`, tidak pernah ditulis sebagai fail "dikalibrasi" yang memegang kiraan mentah.
Sambungkan ke internet dan jalankan semula. Sebabnya adalah sebab sebenar yang ditetapkan oleh pembaca untuk fail tersebut (skema tidak dapat dibaca, tiada bundel, ralat penulisan), dan ringkasan larian menyenaraikan sebab **berbeza** — dua puluh fail yang dilangkau kerana satu sebab dikira sebagai satu sebab, bukan dua puluh ulangan sebab yang sama. CLImenamakan setiap rakaman yang dilangkau di akhir pelaksanaan, beserta sebabnya:

```
⚠ Light-sensor recording not exported: <file>.daq: no calibration bundle available for …
```

Satu folder rakaman yang mengeksport **tiada satu pun** daripadanya gagal: lihat [Satu larian yang tidak menulis sebarang imej gagal](#a-run-that-writes-no-images-fails).

Sebuah rakaman yang namanya berakhir dengan `_calibrated` atau `_raw` (`field_raw.daq`) **tidak
diimport**: itulah nama yang diberikan oleh `process` kepada produknya sendiri, dan produk yang dibaca semula sebagai rakaman akan dikira dua kali. CLI menyatakan demikian dan menamakan fail itu —
`Not imported as a light-sensor recording: field_raw.daq … Rename the file to import it.`
— bukannya melaporkan folder kosong. Ubah nama rakaman seperti itu sebelum memprosesnya.

#### Eksport rakaman DAQ-A sebagai kiraan mentah

**DAQ-A**keluarga ini wujud sebelum sistem bundel per-siri dan tiada bundel kalibrasi untuk diambil — sebaliknya ia dikalibrasi di lapangan menggunakan sasaran pantulan, itulah sebabnya ia tidak pernah memerlukannya. Menolak rakaman-rakaman tersebut menyebabkan mereka tiada cara langsung untuk mendapatkan nombor mereka, jadi mereka mengeksport di bawah**nama yang berbeza**:

```
<project>/
└── Light Sensor/
    ├── <name>_raw.daq        # NOT _calibrated
    └── <name>_raw.csv        # raw spectral sensor counts, NOT irradiance
```

Nama fail yang berbeza dan bukannya penanda di dalam fail, kerana maklumat tersebut perlu kekal
walaupun dihantar melalui e-mel sebagai nama kosong. Header `.csv` menyatakan `raw spectral sensor counts (NOT irradiance)` dan memberi amaran bahawa nilai-nilai tersebut boleh dibandingkan **dalam**fail — yang memang itulah kegunaannya dalam penentukuran berasaskan sasaran — dan bukan merentas penderia. Baris fotometrik bergantung kuasa (kuasa total, lux fotopik dan skotopik, PPFD) ditulis**NULL** dan bukannya diintegrasikan daripada kiraan, dan ringkasan larian menyatakan `RAW COUNTS` jadi "dieksport" dalam log tidak boleh dibaca sebagai iradiasi.

Rakaman lama **v1.01 / v1.02** (DAQ-A-SD menulis ini) tidak mempunyai epoch bagi setiap bacaan, hanya masa penulisan fail. Pemadanan imej↔aliran ke bawah masih menolaknya — memadankan bingkai dengan masa penulisan akan salah secara tersembunyi — tetapi pengeksport membacanya, dan
CSV mencetak `clock=daq_created_on` jadi produk menyatakan jam mana ia digunakan.

### Nota

- `process` secara automatik mengesan sama ada folder anda adalah Survey3, LATTICE, atau campuran.
- Aliran kemajuan melalui Server-Sent Events; CLI memaparkan kemajuan secara langsung bagi setiap thread (Mengesan, Menganalisis, Memproses, Mengeksport).
- Untuk Linux/Jetson, CLI memeriksa swap dan mungkin memberi amaran sebelum memproses folder besar. Debayer yang sedar tekstur juga secara automatik menerapkan had kekerapan GPU pada Jetson berkuasa rendah (Nano, Orin Nano).
- Apabila berjaya, pelaksanaan melaporkan berapa banyak produk imej yang ditulis (`Image products written: N`, dengan `(plus M clamp masks)` apabila unmix mengeklamp mana-mana piksel).
- **`process` berkongsi backend yang sedang berjalan dengan selamat.** Ia memulakan semula backend yang sudah berjalan secara automatik hanya apabila backend tersebut tidak aktif dengan projek sebelumnya dimuat dan tidak memegang sebarang kamera, susunan atau DAQ. Sebuah backend yang memegang mana-mana daripadanya (GUI yang menstrim satu array, skrip SDK, rakaman DAQ) digunakan seperti sedia ada, tidak pernah dihidupkan semula, jadi `process` boleh dijalankan bersebelahan dengan array langsung. Jika backend melaporkan terdapat larian yang sedang dijalankan, `process` menunggu sehingga 15 saat untuk ia selesai (jalanan yang baru sahaja selesai mengambil masa kira-kira satu saat untuk dibersihkan), kemudian menolak dengan `Backend is mid-run (processing). Wait for it to finish, or pass --restart to force a fresh backend (this kills the in-flight run).`. Penanda global `--restart` masih memaksa backend baharu, dan menamatkan sebarang sesi kamera, array atau DAQ padanya. Sebarang backend yang dijalankan oleh perkhidmatan systemd sistem Linux tidak pernah dihidupkan semula: `process` menggunakannya, dan `--restart` mencetak `sudo systemctl restart <unit>` sebaliknya (lihat [Menjalankan backend sebagai perkhidmatan](#running-the-backend-as-a-service-headless)). Begitu juga dengan backend aplikasi Chloros sendiri: apabila aplikasi dibuka, `process` menggunakannya, memuatkan projeknya di tempat projek aplikasi (backend yang dimulakan di tempatnya akan mengunci aplikasi terbuka itu).
- **Sebuah larian yang sedang dijalankan mengekalkan projeknya.** Semasa pelaksanaan pemprosesan sedang dijalankan — dimulakan oleh aplikasi, `process`, skrip SDK atau `chloros-mcp` — backend menolak untuk mencipta, membuka, mengimport ke dalam, menambah atau mengeluarkan fail, memindahkan, memadam atau menukar tetapan pemprosesan sesebuah projek, dengan HTTP 409 `PROCESSING_IN_PROGRESS` dan `A processing run is in progress on project '<name>', so ... now is refused`; `process` yang mendapatkannya keluar `1`. Tetapan paparan (saiz miniatur) masih disimpan. Mod pemprosesan dan tetapan pantulan sasaran projek ditolak dengan cara yang sama (jalanan mengekalkan mod yang digunakan pada permulaan). Sebuah tangkapan yang disimpan ke dalam projek semasa pelaksanaan (daripada aplikasi, CLI, SDK atau `chloros-mcp`) disimpan seperti biasa, tetapi hanya akan menyertai senarai fail projek apabila pelaksanaan tamat; balasannya menyatakan demikian (`project_add`), begitu juga rakaman penderia cahaya yang dihentikan dalam projek. Dahulu, klien kedua boleh menukar projek terbuka di tengah-tengah pelaksanaan, dan pelaksanaan yang sedang berjalan dieksport ke projek lain.
- **Ctrl+C menghentikan pelaksanaan di backend, bukan hanya di CLI.** CLI meminta backend berhenti memproses, mencetak `Waiting for the backend to stop (Ctrl+C again to stop waiting)...`, dan menunggu sehingga 120 s untuk `Processing stopped.`, jadi `chloros-cli process … && next` tidak memulakan `next` pada backend yang masih sibuk dengan larian. Ctrl+C kedua menamatkan menunggu. `process` kemudian keluar `130`, seperti mana-mana arahan yang terganggu.

#### Sebuah larian yang tidak menulis sebarang imej gagal

Jika anda meminta produk dan larian menulis **tiada** — hanya `project.json` dan
`calibration_data.json` — `process` menganggapnya sebagai kegagalan: ia mencetak
`Processing finished but wrote no image products.` dan **keluar dengan nilai bukan sifar**, supaya skrip boleh mengesaninya. Mesej itu menamakan folder projek dan punca biasa:

- folder input tidak diiktiraf sebagai tangkapan (semak susun atur dan `--input-level`), atau
- setiap produk yang diminta diabaikan kerana tidak terpakai untuk kamera tersebut (contohnya meminta
  radiasi/refleksi daripada kamera hanya RGB).

Jalankan semula dengan `--verbose` dan semak log backend untuk baris `[LATTICE-EXPORT]` / `[EXPORT-CHECK]`,
yang menerangkan peninggalan bagi setiap kamera yang sebaliknya tidak sampai ke output CLI.

Pelaksanaan khusus hanya untuk metadata — semua suis produk dimatikan dan tiada `--indices` — masih dianggap **berjaya**, kerana output imej kosong adalah hasil yang betul dalam kes itu.

#### Pelaksanaan yang eksportnya tidak dapat disahkan akan gagal

Selepas pelaksanaan, `process` menyemak tag fail yang dieksport menggunakan ExifTool. Jika ExifTool tidak dapat dijalankan, ia mencetak `Pengesahan Eksport GAGAL: ExifTool tidak dapat dijalankan, jadi tag bagi N daripada M fail yang dieksport
tidak diperiksa (lihat baris [EXIFTOOL] dalam log backend)` and `Produk ditulis dan kekal di tempat; jalankan semula untuk mengesahkannya.`, does not print `Pemprosesan selesai!`, dan **keluar dengan nilai bukan sifar**. Produk disimpan.

#### Yang merakam satu larian mengambil

 Rakaman yang selesai sebelum `process` dijalankan disertakan. Rakaman yang disimpan semasa larian tidak; `process` memberi amaran berapa banyak yang terlepas, dan menjalankannya semula akan menyertakan mereka.

#### Satu larian yang kehilangan imej adalah gagal

Jika imej gagal semasa pelaksanaan, `process` mencetak `N image(s) failed and were not exported` dengan punca yang paling biasa, tidak mencetak `Processing complete!`, dan **keluar dengan nilai bukan sifar**. Produk-produk lain ditulis dan kekal di tempatnya, dan log belakang mempunyai setiap kegagalan.

Begitu juga dengan **jalanan hanya-penderia-cahaya**: Definisi folder rakaman `.daq` ialah tiada imej untuk dieksport, dan pelaksanaan dinilai berdasarkan `.daq` / `.csv` yang dikalibrasi dan ditulisnya sebagai ganti.
Jika ia tidak menulis **apa-apa** — setiap rakaman telah dilangkau — ia mencetak
`Processing finished but wrote no light-sensor products.`, menamakan setiap rakaman dengan sebab ia dilangkau, dan **keluar dengan nilai bukan sifar**.

---

## `chloros-cli login`

Authentikasi mesin ini dengan akaun awan Chloros+. Butiran log masuk disimpan dalam cache dengan selamat di `~/.chloros/user_session.json`.

```
chloros-cli login EMAIL PASSWORD
```

### Contoh

```bash
chloros-cli login user@example.com 'YourPassword'

# Passwords containing $ should use SINGLE quotes
chloros-cli login user@example.com 'my$ecret$pass'
```

> **PowerShell `$$` mangling is auto-corrected.** In double quotes PowerShell expands `$$` (mengeluarkannya daripada, atau menggandakan bahagian-bahagian, kata laluan). Pada 401, CLI secara automatik mencuba semula dengan `$$` semula-yang disambungkan, kemudian dengan separuh kata laluan yang dialih-duplikat; jika percubaan semula berjaya ia akan log masuk anda dan mencetak sintaks petik tunggal yang betul untuk digunakan pada kali akan datang.

> **Penggunaan tanpa papan kekal/ber skrip: tiada sesi yang disimpan bermakna akan ada prompt log masuk.** Perintah teras (`process`, `status`, `export-status`, `time-sync`, …) yang dijalankan tanpa sesi cache akan meminta `Email:` / `Password:` pada stdin sebelum meneruskan. Dengan stdin ditutup (cron, unit systemd) ia gagal serta-merta dengan `login cancelled`; dengan stdin sebagai terminal terbuka atau paip ia menunggu input. Jalankan `chloros-cli login EMAIL PASSWORD` sekali setiap mesin sebelum menjadualkan kerja tanpa kepala.

---

## `chloros-cli logout`

Membersihkan sesi yang disimpan dalam cache dan memaksa log masuk semula pada panggilan seterusnya.

Log keluar semasa larian pemprosesan sedang dijalankan tidak menghentikannya: larian itu akan selesai dalam mod pemprosesan yang ia mulakan, dan larian seterusnya akan menggunakan pemprosesan standard. `logout` menyatakan demikian (`The processing run in progress finishes in <mode> mode; the next run uses standard`), dan begitu juga aplikasi apabila anda menandatangani di sana.

```bash
chloros-cli logout
```

---

## `chloros-cli status`

Tunjukkan peringkat lesen semasa (Iron/Copper/Bronze/Silver/Gold), pengguna yang disahkan, dan bilangan pengikatan peranti.

```bash
chloros-cli status
```

---

## `chloros-cli export-status`

Tanya kemajuan eksport Thread-4 secara langsung. Selamat dipanggil **semasa** pelaksanaan `process` dari shell lain.

```bash
chloros-cli export-status
```

---

## `chloros-cli language`

Tetapkan bahasa paparan CLI (38 bahasa disokong, termasuk CJK, RTL, dan Indic). Secara lancar beralih kepada Bahasa Inggeris pada konsol lama yang tidak dapat memaparkan skrip tersebut.

```
chloros-cli language [LANG_CODE] [--list]
```

### Contoh

```bash
# List all available languages
chloros-cli language --list

# Switch to Spanish
chloros-cli language es

# Show the currently-active language
chloros-cli language
```

---

## Arahan Folder Projek

Ini menguruskan lokasi folder projek lalai (dikongsi dengan GUI).

```bash
chloros-cli set-project-folder "/home/user/Chloros Projects"
chloros-cli get-project-folder
chloros-cli reset-project-folder
```

`get-project-folder` mencetak folder ke mana projek baharu dihantar. Jika folder yang dikonfigurasikan tidak wujud lagi (ia telah dipadam, dinamakan semula, atau berada pada pemacu yang tidak disambungkan), CLI akan kembali ke folder lalai, seperti yang dilakukan oleh GUI: folder lalai dicetak ke stdout, amaran yang menamakan folder yang hilang dihantar ke stderr, dan kod keluarannya ialah `0`.

---

## `chloros-cli update`

Hanya untuk Linux / Jetson. Semak `version_url` daripada `/etc/chloros/update.conf` dan, apabila terdapat versi yang lebih baru, memuat turun `.deb` yang sepadan dan memasangnya dengan `sudo dpkg -i` serta-merta (tiada paparan permintaan pengesahan; `sudo` mungkin meminta kata laluan anda). Pada Jetson, hanya sistem JetPack 6 yang ditawarkan kemas kini. Pada Windows, arahan tidak melakukan apa-apa dan melaporkan `up to date`: kemas kini dengan pemasang baru.

```bash
chloros-cli update            # check + install
chloros-cli update --check    # check only
```

Pada Linux/Jetson, CLI juga menjalankan **semakan kemas kini automatik pada setiap permulaan** (tidak menyekat, tidak pernah menangguhkan arahan): ia membaca `/etc/chloros/update.conf`, menyimpan hasil dalam cache selama 1 jam dalam `~/.chloros/update_cache.json`, dan mencetak `Update available: vX.Y.Z / Run: chloros-cli update` apabila terdapat versi yang lebih baru. Diabaikan secara senyap bagi sebarang ralat dan pada Windows; `CHLOROS_DISABLE_UPDATE_CHECK=1` mematikannya. Disebabkan cache selama 1 jam, sebarang keluaran yang diterbitkan dalam tempoh tersebut masih akan dibaca sebagai terkini.

---

## `chloros-cli selftest`

Melakukan ujian asap 7 langkah: versi, ketersediaan port, permulaan backend, `/api/test`, `/api/system-info` (GPU/CUDA/PyTorch), kewujudan model denoiser, kesediaan CUDA+denoiser. Ia menggunakan backend yang sedang berjalan, atau memulakannya untuk ujian dan menghentikannya selepas itu. Ia mencetak `N/7 checks passed` dan pemeriksaan yang gagal, dan keluar `0` hanya apabila kesemuanya tujuh lulus; semakan CUDA+denoiser gagal pada komputer tanpa GPU CUDA (lihat [Verify the Install](#verify-the-install)).

```bash
chloros-cli selftest
```

---

## `chloros-cli time-sync`

Status dan kawalan grandmaster PTP. Host Chloros menjalankan grandmaster PTP; kamera LATTICE dan unit DAQ-E menjadi hamba kepadanya untuk cap masa merentas peranti (kira-kira 1 ms merentas peranti). PTP menyelaras **cap masa**: kamera dalam satu susunan mengambil gambar serentak kerana pencetus perkakasan M8 mengaktifkannya (dalam masa kurang daripada 100 µs antara modul), bukan kerana PTP. `chloros-cli time-sync` tanpa arahan anak menjalankan `status`.

| Arahan anak | Deskripsi |
| --- | --- |
| `status` | Tunjukkan status grandmaster, keutamaan BMCA, identiti jam, dan sama ada paketnya sampai ke rangkaian: `health` adalah `OK` (`STOPPED` apabila grandmaster tidak berjalan), atau `SENDS_FAILING` / `DEGRADED` / `NO_SOCKETS` / `PEERS_SILENT` dengan `health_detail`, dan kemudian satu baris `WARNING:` dicetak ke stderr (stdout kekal seJSON). `sync_sent` / `announce_sent` hanya mengira mesej yang terhantar; `*_send_errors` dan `last_send_error` mengira mesej yang tidak terhantar. Selepas pautan kamera terputus dan kembali, grandmaster membina semula soketnya sendiri (`rebinds`, `last_rebind`). |
| `peers` | Senarai hamba yang dilihat melalui Delay_Req (kamera + penderia DAQ-E). |
| `cameras` | Kesihatan PTP bagi setiap kamera (`PtpStatus`, `PtpOffsetFromMaster`, `PtpMeanPathDelay`). |
| `restart` | Mulakan semula proses grandmaster. |
| `set-priority --priority1 N --priority2 N` | Ungkit keutamaan BMCA. |

Grandmaster mengumumkan pada NIC kamera (sebarang penyesuai dengan alamat `169.254.x.x` termasuk). Jika tiada yang dipasang, ia akan kembali kepada penyesuai laluan lalai hanya apabila ia bersambung melalui kabel, tidak pernah melalui Wi-Fi atau penyesuai maya (Tailscale, WSL), kerana tiada kamera atau DAQ-E terletak di belakangnya. Apabila tiada apa-apa untuk diumumkan, ia berjalan tanpa soket (`health` `NO_SOCKETS`), dan kamera atau DAQ seterusnya yang disambungkan membinanya semula pada NIC yang sudah aktif pada masa itu; `time-sync restart` melakukannya sekaligus.

Grandmaster berjalan dalam prosesnya sendiri, di sebelah backend (`own_process` / `process_pid` dalam `time-sync status`), dengan keutamaan tinggi di Windows (`process_priority`), jadi backend yang sibuk (jalanan pemprosesan, eksport, beberapa arahan sekaligus) tidak dapat menangguhkan masa yang dicetaknya; sebelum ini, satu penangguhan seperti itu boleh menggerakkan jam setiap kamera sekaligus. Proses itu berakhir dengan backend; jika ia tidak dapat dimulakan, grandmaster akan berjalan di dalam backend seperti sebelum ini (`own_process` false, dan log backend menunjukkan demikian).

### Contoh

```bash
chloros-cli time-sync status
chloros-cli time-sync peers
chloros-cli time-sync cameras
chloros-cli time-sync restart
chloros-cli time-sync set-priority --priority1 1 --priority2 1
```

---

## `chloros-cli lattice`

Kawalan kamera LATTICE. Setiap subperintah dialihkan melalui backend Chloros; backend ini memiliki kolam kamera jadi panggilan CLI seterusnya menggunakan semula pegangan terbuka yang sama.

### Pilihan Umum (dikongsi oleh kebanyakan subperintah)

| Bendera | Deskripsi |
| --- | --- |
| `-d, --device N` | Indeks kamera (lalai: 0). |
| `-s, --serial SN` | Siri khusus; memebat `--device`. |
| `--serials SN1,SN2,…` | Nombor siri yang dipisahkan dengan koma untuk operasi berbilang kamera. [perintah berbilang kamera sementara](#multi-camera-transient-sessions) hanya membuka kamera-kamera ini, dengan kamera pertama berfungsi sebagai pencetus. |
| `--all` | Beroperasi pada setiap kamera yang ditemui. |
| `--exposure US` | Masa pendedahan dalam mikrosaat. |
| `--gain DB` | Keuntungan dalam dB. |
| `--pixel-format FMT` | contohnya `BayerRG8`, `BayerRG12`; `Mono8`, `Mono12` pada M3M. Nama menetapkan kedalaman bitnya dalam keluarga kamera itu sendiri: pada M3M, nama Bayer menetapkan format Mono kedalamannya (`BayerRG8` menetapkan `Mono8`), pada M3C nama Mono menetapkan nama Bayer (`Mono12` menetapkan `BayerRG12`). |
| `--width N` / `--height N` | Dimensi imej. |
| `--preset {default,high_quality,high_speed,triggered}` | Terapkan pratetapan tetapan: format piksel, pendedahan, penguatan dan kadar bingkai (`high_speed`: `BayerRG8`, 5 ms, 6 dB, 60 fps; `high_quality`: `BayerRG12`, 20 ms, 0 dB; `lattice presets` menyenaraikannya). Pada M3M formatnya adalah Mono dengan kedalaman yang sama: `high_speed` merakam `Mono8`, `high_quality` dan `triggered` `Mono12`. Satu bendera yang diberikan bersamanya (`--exposure`, `--gain`, `--pixel-format`) mengatasi tetapan lalai, dan satu tetapan yang tidak dinyatakan oleh tetapan lalai mengekalkan tetapan asal kamera. Semua percuma-run kecuali `triggered`, yang mengaktifkan kamera untuk edge perkakasan pada Baris 2 — jika tiada apa-apa yang memacu baris tersebut, ia akan menunggu selama-lamanya daripada mengambil imej. |
| `-o, --output DIR` | Direktori keluaran (lalai: `output`). |
| `--packet-size {auto,jumbo,standard,N}` | Saiz paket GVSP. `auto` menjalankan prob ICMP+GVSP; `jumbo` = 9000; `standard` = 1500. |

### Aliran Kerja Sambungan Pertama Kamera LATTICE

```bash
# 0. A backend must be running: the Chloros desktop app (Windows),
#    `chloros-backend`, or the Linux service (see "Start the Backend")

# 1. Discover cameras on the network
chloros-cli lattice info

# 2. Single-cam smoke test: capture one frame.
#    By default this saves EVERY export type applicable to the cam
#    (raw, debayered, radiance, reflectance, preview). Pass e.g.
#    `--processing debayered` to save just one.
chloros-cli lattice capture -o output/

# 3. Connect a synchronized array (RECOMMENDED ENTRY POINT for arrays).
#    This is the same "smart-prep" flow the Chloros GUI uses:
#      - Network capability probe (ICMP DF ping + GVSP probe)
#      - Tier auto-pick (sim-emit / ftd-stagger / slip)
#      - Full sensor (no binning, no ROI); the frame rate falls to
#        what the host and wire deliver, and the faster options are
#        printed
#      - PTP enabled by default
#      - Per-cam pixel format auto-pick
#      - AE seeding from the cam's saved state
#      - GPIO trigger config on Line2
chloros-cli lattice array-connect \
  --serials 213800234,214000533,214701288,214701292

# 4. Capture one synced frame group from the live array.
#    Defaults to --processing all (one file per export type per cam);
#    pass a single level to narrow it, e.g. --processing reflectance.
chloros-cli lattice array-capture --processing reflectance -o output/

# 5. Live-preview one cam in your browser
chloros-cli lattice viewer --serial 213800234

# 6. Tear down when done
chloros-cli lattice array-disconnect
```

### Rujukan Subperintah

#### Penemuan & Maklumat

| Subperintah | Tujuan |
| --- | --- |
| `lattice info` | Senaraikan kamera yang disambungkan (pembekal, model, siri, IP, MAC). |
| `lattice probe [--pixel-format FMT] [--json] [--no-discover]` | Analisis sistem hos untuk konfigurasi kamera yang optimum. `--no-discover` melangkau penemuan kamera (lebih pantas, analisis hanya NIC). |
| `lattice network [--fix] [--estimate] [--cameras N] [--pixel-format FMT]` | Semak/betulkan tetapan NIC (pada Linux, juga penimbal penerimaan soket kernel — lihat [host Linux](#linux-hosts--socket-receive-buffers)); anggaran jalur lebar/FPS. |
| `lattice network-analysis --master SN [--slaves SN1,SN2,…] [--width N] [--height N] [--pixel-format FMT] [--binning {1,2,4}] [--force-tier TIER] [--packed {auto,on,off}] [--backend-url URL] [--json]` | Rangka belakang rangkaian skema stabil-keupayaan + cadangan susunan (mengembalikan `status` ∈ `ok` / `auto_shrunk` / `auto_capped_fps` / `needs_force_slip` / `error`). `--width` / `--height` secara lalai kepada sensor penuh, `2048` × `1536`. Ia keluar `0` untuk `ok`, `auto_capped_fps` dan `auto_shrunk`, dan `1` untuk `needs_force_slip` dan `error`, dengan atau tanpa `--json`. Ia sampai ke backend di `--backend-url` (default `http://127.0.0.1:5000`), bukan `CHLOROS_BACKEND_URL`. `auto_capped_fps` mengekalkan resolusi yang diminta tetapi mengehadkan fps sasaran — baca `recommended.recommended_target_fps` dan pass ia sebagai sasaran connect; anggap ia sebagai kejayaan, bukan ralat. `auto_shrunk` hanya muncul apabila bingkai yang diminta tidak dapat dihantar pada mana-mana kadar bingkai; connect tidak pernah mengecilkan bingkai secara sendiri. |
| `lattice analyze-array [--models M1,M2,…] [--binning {1,2,4}] [--n-active N] [--width N] [--height N] [--pixel-format FMT] [--force-tier TIER] [--packed {auto,on,off}] [--json]` | Analisis "apa yang berlaku jika" tanpa membuka kamera (`--width` / `--height` lalai `2048` × `1536`). Kedua-duanya, ini dan `network-analysis`, memaparkan apa yang akan disiarkan oleh connect: tanpa `--pixel-format`, format per-kamera yang digunakan oleh connect (Mono12 / BayerRG12), dan dengan `--packed auto` (lalai) [terpakat 12-bit](#transport-12-bit-terpakat---terpakat) keputusan connect akan buat pada komputer ini — jadi fps yang dinyatakan adalah kadar yang array jalankan. Kedua-duanya mencetak keputusan pembungkusan. **`--n-active` adalah jumlah keseluruhan kamera pada talian, bukan hanya bagi array ini**— naikkan apabila kamera berdiri sendiri mengalir serentak, atau bajet talian dikira berdasarkan permintaan yang mengira kurang bilangannya (lalai: `len(--models)`). Sentiasa mencetak baris agregat `Wire budget:` (MB/s yang diminta berbanding had selamat-langgaran) dan `Max cameras:`, dan menandakan `** OVER-SUBSCRIBED**` apabila susunan melebihi langganan wayar — lihat [Array fps & model burst](#array-fps--burst-model). |
| `lattice gpu` | Tunjukkan status GPU. |
| `lattice firmware [--update] [--force] [-y\|--yes]` | Semak atau kemas kini firmware kamera. Pilihan `.fwa` tempatan dipin: fail dalam `firmware/<MODEL_PREFIX>/` yang sepadan dengan `MIN_FIRMWARE_VERSION` binaan diflash apabila wujud (versi tertinggi sahaja sebagai pilihan terakhir), jadi imej vendor yang lebih baru disimpan pada cakera tidak aktif sehingga pin itu diaktifkan — secara sengaja, keluaran yang lebih baru sampai ke unit melalui manifest AWS yang disahkan, yang lebih digalakkan apabila ia baharu. |
| `lattice presets [--apply NAME]` | Senaraikan atau terapkan pratetkamera. `--apply` menetapkan apa yang dinamakan oleh pratet (format pikselnya, pendedahan, penguatan, kadar bingkai, pencetus) dan mencetak tetapan tersebut apabila kamera membacanya selepas: pada M3M, format Mono bagi kedalaman pratetap; kadar bingkai yang ia jalankan, yang boleh lebih rendah daripada yang diminta oleh pratetap (`high_speed` meminta 60 fps; kamera penuh bingkai pada pautan 1 Gbps berjalan sekitar 35). Setiap tetapan lain yang diubah dipulihkan seperti asal. `--apply default` menetapkan semula kamera kepada tetapan kilang asal: tetapan kilang dengan pendedahan automatik bebas pada Line2, had throughput pautan dan pembetulan kecacatan dimatikan, serta imbangan putih kamera RGB yang dikalibrasi dikekalkan (bersatu pada mana-mana yang lain). Tiada apa-apa yang disimpan ke memori kamera, jadi pada kuasa-hidup seterusnya ia bermula dari memorinya sendiri seperti sebelum ini. Sebagai `--preset` atau `default` untuk tangkapan atau sambungan, tiada tetapan pratetap. |
| `lattice status` | Status kamera langsung. |

#### Rakaman

| Subperintah | Tujuan |
| --- | --- |
| `lattice capture [--format tiff\|tif\|png\|jpg\|jpeg] [--jpeg-quality N] [--processing LEVEL] [--levels L1,L2,…] [--no-daq] [--daq-tolerance-ms MS]` | Satu bingkai. Menyimpan setiap jenis eksport secara lalai (`--processing all`); lihat [Tahap Eksport Rakaman](#capture-export-levels-the-all-default). `--processing` mengambil `all`, `raw`, `debayered`, `radiance`, `reflectance` atau `display` (`preview` diterima untuk `display`); mana-mana nama lain ditolak sebelum kamera disentuh (keluar `1`). `--levels` menyimpan subset eksplisit (mengatasi `--processing`). Chloros tidak memegang kamera dibuka untuk rakaman, dan pendedahannya ditetapkan daripada babak terlebih dahulu (beberapa saat paling lama), seperti yang dilakukan oleh [perintah sementara](#multi-camera-transient-sessions); Apabila rakaman berakhir, tidak kira bagaimana ia berakhir, kamera itu dipulihkan seperti asal (format, pendedahan, saluran warna, setiap tetapan yang diubah oleh rakaman — senarai di bawah [Multi-Kamera (Sesi Sementara)](#multi-camera-transient-sessions)). [Pembacaan DAQ untuk tangkapan yang digunakan disimpan](#the-daq-reading-a-capture-saves) sebagai fail sampingan `.daq`, satu tangkapan mentah sahaja disertakan; `--no-daq` / `--daq-tolerance-ms` melarasnya ([DAQ menyimpan bacaan tangkapan](#the-daq-reading-a-capture-saves)). `--force-daq` masih diterima. `--jpeg-quality` = kualiti JPEG 1–100 (lalai 95). **Fail yang disimpan ke dalam projek terbuka akan disertakan.** Seperti dalam aplikasi, tangkapan yang failnya disimpan dalam folder projek yang dibuka diChloros akan menambahkannya ke senarai fail projek tersebut; fail yang disimpan di tempat lain dibiarkan begitu sahaja. Perintah mencetak satu baris yang menyatakan demikian (`Project: ...`), atau, semasa pelaksanaan pemprosesan sedang dijalankan, fail-fail itu ditangguhkan dan disertakan apabila pelaksanaan selesai. Begitu juga dengan `multi-capture`, `array-capture` (`--smart` juga) dan `project capture`. |
| `lattice continuous [--format tiff\|tif\|png\|jpg\|jpeg] [--jpeg-quality N] [--queue-depth N] [--no-daq] [--daq-tolerance-ms MS]` | Aliran ke cakera sehingga Ctrl+C. Satu larian `tiff`kerangka-kerangka `raw` adalah fail yang ditulis oleh tangkapan susunan kamera itu: mozek sensor, satu satah pada nilai tersendiri, tidak dimampatkan, dengan pek kalibrasi (satu bingkai 12-bit 2048 x 1536 ialah 6.0 MiB ditambah paket, apa pun adegan), jadi `process` bermula daripadanya. `png` dan `jpg` menyimpan 8 bit, jadi rentetan tersebut adalah bingkai yang telah menjalani demosaic untuk tontonan, yang mana `process` tidak bermula daripadanya. Dengan DAQ terikat (`daq pool-bind`) atau dikumpulkan, setiap bingkai mendapat `.daq` dan `.match.json` tersendiri dalam folder `daq/` bagi larian itu, seperti `lattice capture` menyimpan satu; `--no-daq` mematikan itu, dan butang henti mencetak berapa banyak. Setiap bingkai ditulis sekali, dengan metadata lengkapnya; Apabila menekan Ctrl+C, bingkai yang telah dirakam akan ditulis sebelum kamera dilepaskan. Ia menghentikan sendiri apabila cakera penuh, mencetak `Failed:` / `Disk full:` dan keluar `1`; mana-mana bingkai yang tidak dapat ditulis juga keluar `1`. Kamera yang dibiarkan dalam keadaan sedia untuk pencetus perkakasan (`TriggerMode` `On`) ditetapkan untuk berjalan bebas bagi sesi tersebut. Apabila sesi berakhir, kamera dikembalikan seperti asal — tetapan pencetusnya dan setiap tetapan yang diubah oleh sesi (pendedahan, penguatan, format, …) — kecuali `--keep-settings`, yang menyimpan tetapan larian ke dalam memori kamera sendiri (berlari bebas), atau `--clear-settings`, yang menetapkan semula memori itu kepada tetapan kilang. Sebuah larian yang tidak merakam sebarang bingkai adalah kegagalan: jika tiada bingkai selama 10 saat, ia menghentikan sendiri, mencetak `No frames:` dengan sebab dan keluar `1`. Rangka yang penulis tidak dapat ikuti dikira sebagai terbuang, dan pemberhentian mencetak `Dropped:` dengan sebabnya (kadar bingkai kamera, penulis dan barisan); kadar bingkai yang lebih rendah atau format piksel 8-bit mengekalkan semuanya. Perintah memeriksa dengan backend setiap 2 saat; jika ia mati tanpa menghentikan rakaman (`kill -9`, kehabisan memori), backend menghentikannya 30 saat selepas pemeriksaan terakhir dan mengembalikan kamera (`stop_reason` `client_gone`, dan satu baris dalam log backend). `multi-stream` dan `align-stream` melakukan perkara yang sama. Permulaan yang kameranya mengambil masa lebih lama untuk dibuka berbanding masa tunggu arahan (60 s; 180 s untuk `multi-stream` / `align-stream`) — kamera masih sedang boot — dibatalkan oleh backend: kamera itu dipulangkan dan ditutup dan tiada rakaman yang terus berjalan (HTTP 504 `CLIENT_DEADLINE_PASSED`). |
| `lattice viewer [--brightness N] [--ae-damping F] [--frame-rate FPS]` | Pratonton MJPEG langsung berasaskan pelayar: menyambungkan kamera, mencetak URL aliran dan membukanya dalam pelayar anda. `--ae-damping` menetapkan peredam pendedahan automatik (0.4–100). |

##### Siaran langsung (MJPEG; tiada RTSP)

Chloros menyajikan video langsung sebagai **MJPEG melalui HTTP**(`multipart/x-mixed-replace`, sempadan `frame`) daripada backend.**RTSP tidak disokong.** Buka URL dalam pelayar, atau baca dengan mana-mana klien MJPEG (`cv2.VideoCapture(url)` OpenCV, ffmpeg, VLC). Kamera mesti disambungkan di backend terlebih dahulu (`lattice cam-connect`, `lattice array-connect`, `lattice viewer`, atau GUI).

| URL (`http://127.0.0.1:5000` + …) | Aliran |
| --- | --- |
| `/api/camera/stream/<serial>` | Pratonton paparan, sehingga kira-kira 30 fps. Untuk ahli tatasusunan, `?max_h=` (lalai `720`) dan `?jpeg_q=` (lalai `80`) menetapkan ketinggian dan kualiti JPEG. Ini ialah URL yang dibuka oleh `lattice viewer`. |
| `/api/camera/<serial>/stream-annotated` | Pratonton yang sama dengan lapisan GUI dilukis di dalamnya: `zebra=1`, `crosshair=1`, `grid=3x3` (atau `9x9`), `histogram=1`, `peaking=1`, `spot=1`; tanpa sebarang pertanyaan, tetapan overlay yang disimpan oleh projek untuk kamera tersebut. |
| `/api/camera/<serial>/stream-reflectance` | pantulan 8-bit bagi setiap jalur: ρ = piksel ÷ bahagian tersebuts `X-Chloros-Pixel-Scale` header (255 melainkan `refl_max` diangkat). `fps` (default `10`), `jpeg_q` (lalai `95`), `max_h` (lalai `0` = asli), `band=<name>`, `align=1`. JPEG, jadi lossy. Ditolak dengan HTTP 409 apabila kamera tiada reflectance (kamera RGB, tiada pek penentukuran, tiada bacaan DAQ). |

- **Menandingan bingkai merentas kamera.**Setiap bahagian yang dihantar oleh**ahli tatasusunan**, pada ketiga-tiga aliran, membawa `X-Chloros-Group-Id` (penjumlah kumpulan diselaraskan tatasusunan), penanda aras `X-Chloros-Frame-Id` dan `X-Chloros-Timestamp-Ns`. Bahagian daripada kamera yang berbeza dengan `X-Chloros-Group-Id` yang sama berasal daripada pencetus yang sama. Kamera berdiri sendiri tidak mempunyai kumpulan dan tidak menghantar id kumpulan. Pelayar mengabaikan pengepala bagi setiap bahagian; klien yang memproses aliran berbilang bahagian itu sendiri membacanya.
- **Ditangguhkan semasa paparan tatasusunan dibuka.** `/api/camera/stream/<serial>` bagi ahli tatasusunan menghantar bingkai "paused" statik semasa aliran langsung gabungan tatasusunan dibuka (contohnya, paparan tatasusunan GUI), dan disambung semula apabila ia ditutup.

#### Penyelarasan Sensor

| Subperintah | Tujuan |
| --- | --- |
| `lattice configure [{--get N1 N2… \| --set N=V N=V… \| --dump}] [--json]` | Baca/tulis mana-mana nod GenICam. `--set ExposureTime=…` (`Gain=…`) tanpa `ExposureAuto` (`GainAuto`) terlebih dahulu memadam suis mod automatik, seperti yang dilakukan oleh `lattice exposure --set`; nilai yang ditolak oleh kamera mencetak `Failed to set <node>: …` pada stderr dan keluar `1`. `--get` dan pembetulan kumpulan lalai mencetak `note:` di bawah `ExposureTime` / `Gain` semasa mod auto itu berjalan — lihat [Membaca pendedahan dan penguatan di bawah mod auto](#reading-exposure-and-gain-under-auto-mode). |
| `lattice exposure [{--auto \| --auto-once \| --off \| --set US}] [--brightness 0-255] [--brightness-auto] [--damping 0.4-100] [--upper-limit US]` | Pendedahan & AE. Pendedahan `--set` atau sasaran `--brightness` dikekalkan sebagai milik anda (kawalan pendedahan automatik Chloros membiarkannya begitu sahaja); penguatan kekal seperti sedia kala, jadi dalam mod automatik ia terus menyesuaikan diri ke arah sasaran sehingga anda menetapkan `lattice gain`. `--auto` memulangkan pendedahan, `--brightness-auto` menyerahkan semula sasaran kepada sasaran automatik Chloros. Tanpa sebarang bendera ia membaca nod pendedahan, dengan nilai `note:` yang sama seperti `configure --get`. Nilai `--set` yang ditolak oleh kamera mencetak `Failed to set ExposureTime: ExposureTime=<value> refused (<camera error>); the camera accepts <min>..<max> now (ExposureAuto put back to <mode>)` pada stderr — mod automatik yang ditulis dimatikan dipulihkan, bukannya kekal Matik — mencetak tiada baris `Exposure: … us (auto OFF)`, dan keluar `1`. |
| `lattice gain [{--auto \| --off \| --set DB}]` | Penguatan & penguatan automatik. Nilai `--set` yang kamera enggan mencetak `Failed to set Gain: …` pada stderr, tiada baris `Gain: … dB (auto OFF)`, dan keluar `1`. |
| `lattice resolution [--set WxH] [--offset X,Y] [--binning N]` | ROI Sensor & binning. Binning sentiasa **Purata** — lihat di bawah. |
| `lattice format [--set FMT] [--list]` | Format piksel. |
| `lattice trigger [--mode On\|Off] [--source SRC] [--delay-us US] [--activation EDGE] [--list-sources] [--software]` | Pencetus perkakasan/perisian. |
| `lattice white-balance [--auto] [--off] [--red R] [--blue B]` (tiada penanda aras = WB satu-tembakan) | Operasi WB. RGB/kamera Bayer sahaja; tiada kesan (dilangkau) pada M3M mono. One-shot mengekalkan imbangan putih yang diselesaikannya (`BalanceWhiteAuto` dan penguatan); setiap tetapan lain yang diubah akan dikembalikan seperti asal, dan semua ini berlaku apabila one-shot tidak dapat dijalankan. |
| `lattice color-profile [--set raw\|linear\|natural\|enhanced\|custom_temp] [--cct 2000-10000] [--get]` | Saluran warna paparan RGB. `natural` (lalai) ialah kemasan langsung murah; `enhanced` menambah defringe + vibrance + kontras tempatan CLAHE untuk penampilan hub-parity penuh pada kos kemasan per-bingkai kira-kira 2×, jadi kadar bingkai **langsung** yang lebih rendah — rakaman yang disimpan sentiasa mendapat kemasan penuh dalam apa jua keadaan. Hanya untuk kamera RGB/Bayer; dipingirkan pada mono M3M. |
| `lattice color [--saturation 0-200] [--contrast 0-200] [--reset] [--get]` | Saturasi/kontras pratonton langsung (kamera penapis RGB); `100` adalah neutral, `--reset` menetapkan kedua-duanya kepada 100. Rakaman yang disimpan tidak terjejas. Dilangkau pada mono M3M. |
| `lattice highlights [{--set -100..0 \| --reset \| --get}]` (tiada bendera = baca) | Sorotan Paparan (kamera penapis RGB): menarik piksel paling cerah dan paling putih ke bawah — contohnya permukaan putih cerah yang dipaparkan sebagai tompok putih rata. `0` = matikan, `-100` = paling kuat. Pratonton langsung dan tangkapan peringkat paparan mengikutinya; sinaran/pantulan tidak pernah, dan ia tidak dapat memulihkan data sensor yang terpotong (kurangkan pendedahan untuk itu). Dilangkau pada mono M3M. |
| `lattice filter [--list]` | Senaraikan model penapis yang diketahui oleh Chloros (`RGN-IMX265`, `OCN`, `NGB`, …). Penapis kamera dibaca daripada rentetan modelnya dan tidak boleh ditetapkan: `--set NAME` hanya melihat model, kemudian keluar `1` dengan `setting the filter model is not supported`. |
| `lattice power [--sleep]` | Menyiasat nod kuasa/termal; bertukar ke mod tidak aktif berkuasa rendah. `--sleep` membiarkan kamera dalam keadaan tidur (had throughput pautan dihidupkan (`DeviceLinkThroughputLimitMode` `On`) pada aras lantai, mod penjimatan kuasa dihidupkan jika kamera menyokongnya) dengan setiap tetapan lain, ia dipulihkan seperti asal; tidur yang gagal dipulihkan sepenuhnya. Pembangkitan mengembalikan pautan seperti keadaan semasa tidur ditemui (had, modnya, dan kelewatan paket `GevSCPD`, yang dikekalkan pada 0 oleh kamera selagi had dihidupkan; rekod disimpan pada komputer ini, sehingga kebangkitan selepas backend dihidupkan semula menemuinya juga), atau, untuk kamera yang komputer ini tidak letakkan dalam tidur tetapi kelihatan sedang tidur (hadnya dihidupkan pada paras lantai), dengan had dimatikan dan `GevSCPD` dibiarkan pada 0 pada 80 (tetapan kilang); had yang ditetapkan di atas paras bawah dibiarkan seperti sediakala. Penanda aras dan pembangkitan semula memulihkan apa yang ditulis oleh pembukaan mereka. |

> **Pengagregatan sentiasa Purata.** `lattice resolution --binning N` menetapkan pengagregatan Purata dan tidak menawarkan suis mod. Pengagregatan Jumlah (Sum binning) mengalikan setiap DN dengan N², menelan kos kira-kira dua stop ruang aman sorotan bagi setiap langkah pengagregatan; Chloros membahagikan semula faktor itu sebelum rantaian radiometrik, tetapi piksel yang dipacu oleh Jumlah ke had siling sensor akan dipotong dan tidak dapat dipulihkan. Ia tidak membeli apa yang Average tidak mampu — satu indeks tidak berubah-ubah mengikut skala dan skalar radiasi dikalibrasi pada skala Average. Jika sebuah kamera tiba dalam tetapan Sum (dibiarkan begitu oleh alat lain, atau oleh tetapan kilang), Chloros mengesan perkara ini daripada bacaan balik nod dan memberi pampasan; menjalankan `lattice resolution --binning N` mengembalikannya ke Purata. Versi 1.2.0
> Penanda `--binning-mode` masih diterima jadi skrip lama boleh dijalankan, tetapi ia hanya mencetak amaran:
> binning kekal pada Purata.

> **Tukar binning, format piksel dan pencetus pada kamera tidak memberi kesan.** Pada kamera,
> backend mengekalkan open dan streaming (`cam-connect`, GUI), `lattice resolution --binning`
> tidak memberi kesan (ia mencetak binning yang masih berkuat kuasa, `Binning: 1x1`), dan
> `lattice format --set` dan `lattice trigger --mode` ditolak oleh kamera
> (`SC_ERR_ERROR -1001`). Bebaskan dahulu (`lattice cam-disconnect -s <SN>`), atau tetapkan
> mereka semasa sambungan (`cam-connect --pixel-format …`, `array-connect --binning …`).

##### Membaca pendedahan dan penguatan di bawah mod automatik

Semasa `ExposureAuto` (`GainAuto`) kamera sedang berjalan, `ExposureTime` (`Gain`) simpul menyimpan **nilai terakhir yang ditulis**, bukan apa yang kamera lakukan sekarang. Untuk kamera yang backend-nya dibuka (`cam-connect`, `array-connect`, GUI), `lattice configure --get`, paparan lalai `lattice configure` dan `lattice exposure` (tiada bendera) oleh itu mencetak nilai nod dan, di bawahnya, satu nota dengan bingkai terkini kamera:

```

ExposureTime = 2000.448
  note: ExposureAuto is Continuous: ExposureTime holds the last value written (2000.448); the camera's newest frame was at 4900 us.
```

Baca nilai bingkai daripada nota; nilai nod hanyalah tulisan terakhir. Melalui HTTP, respons `POST /api/camera/<serial>/read-nodes` yang sama mengekalkan `nodes` tidak berubah dan menambah `frame_values` (`{"ExposureTime": <us>, "Gain": <dB>}`, bingkai terbaru) dan `notes` (ayat di atas). Kedua-duanya wujud hanya apabila mod automatik bukan `Off` dan sebuah bingkai diketahui; kamera yang dibuka hanya untuk bacaan tidak mempunyai bingkai dan tidak menerima kedua-duanya. `lattice gain` (tanpa bendera) mencetak nod sahaja — gunakan `lattice configure --get Gain` untuk nota.

##### Pendedahan pada ahli tatasusunan

Semasa tatasusunan memancarkan, setiap ahli berjalan dengan **`AcquisitionFrameRateEnable` pada**, pada kadar bingkai sedikit di atas kadar pencetus susunan: periodenya ialah periodenya pencetus tolak 15 % (sekurang-kurangnya 3 ms), atau kadar maksimum kamera itu sendiri apabila pencetus sudah hampir dengannya. `configure --get` pada ahli oleh itu membaca `AcquisitionFrameRateEnable` sebagai `True` dan `AcquisitionFrameRate` pada tetapan itu; pengaktifan akan dimatikan semula apabila susunan berhenti.**Jangan tulis kedua-dua nod ini pada ahli tatasusunan** — tatasusunan mengekalkan mereka selari dengan pencetusannya dan menulisnya semula.

Penyetelan itu juga adalah had pendedahan ahli: kira-kira 85 ms pada pencetus 10 fps (tempoh 100 ms), pada **sebarang ROI atau binning**Sebelum 1.3.0, satu ROI kecil mengehadkan pendedahan ahli pada tempoh bebas-jalur sensor sebaliknya (4.9 ms pada 768 × 304), tidak kira betapa lambatnya susunan mencetuskan.**`ExposureTime` manual yang lebih lama daripada had akan memperlahankan pencetus untuk memadankannya** apabila ia ditulis dengan `lattice exposure --set` atau `configure --set` (atau dari GUI). Susunan itu pertama sekali membuka had ahli tersebut untuk nilai itu dan membiarkan kadar yang lebih perlahan berjalan beberapa kitaran pencetus, jadi penulisan itu boleh mengambil masa beberapa saat lebih lama; pencetus kemudiannya akan mencetus tidak lebih pantas daripada yang dibenarkan oleh pendedahan. Pada susunan kadar automatik, kadar yang dijalaninya sebelum ini akan kembali sebaik sahaja setiap ahli yang pendedahan manualnya memperlahankan kadar itu kembali ke pendedahan automatik. Nilai yang tidak dapat diambil oleh kamera itu sendiri tetap ditolak, dengan julat yang diterima dinyatakan dalam ralat, dan pendedahan automatik ahli itu dipulihkan jika ia dimatikan hanya untuk penulisan itu. Pendedahan resipi `apply` tidak melambatkan pencetus: ia dikekang pada had atas. Chloros tidak pernah memendekkan pendedahan manual untuk menyesuaikan pencetus yang lebih pantas; kadar yang dipaku (`array-connect --target-fps F`) yang lebih pantas daripada yang dibenarkan oleh pendedahan akan meninggalkan tempoh had yang cukup lama untuk ahli tersebut, dan ahli tersebut tidak akan mengambil pencetus lebih pantas daripada yang dibenarkan oleh pendedahannya.

#### Kalibrasi & Sensor

| Subperintah | Tujuan |
| --- | --- |
| `lattice calibrate [--filter NAME] [--attempts N] [--save PATH]` | Kalibrasi daripada sasaran pantulan. Sehingga `--attempts` bingkai (lalai `30`) dicari untuknya; kerangka yang tidak lengkap (kehilangan pekeliling GVSP) menelan satu percubaan, bukan keseluruhan pelaksanaan. Ia gagal dengan `no target detected in N attempts`, atau `no complete frame in N attempts: <last error>` apabila tiada bingkai tiba dalam keadaan utuh. |
| `lattice dls {--connect \| --spectrum \| --irradiance} --mac MAC [--filter NAME] [--camera SERIAL] [--json]` | Spektrum dan magnitud band DAQ-M, dibaca melalui Bluetooth (BLE) melalui backend. `--mac` adalah MAC BLE sensor itu sendiri dan diperlukan dengan mana-mana tiga tindakan: tiada lalai, kerana setiap unit mempunyai yang tersendiri (`daq pool-discover --only ble` menyenaraikannya); tanpa ia, arahan akan keluar `1`. Tanpa sebarang tindakan, ia mencetak bantuan. `--camera SERIAL` mengintegrasikan magnitudo jalur yang dicetak melalui tindak balas spektral yang diukur oleh kamera itu sendiri, bukannya lengkung kumpulan model penapis — kamera RGB, dengan pek penentukuran kamera dimuat (disambungkan) atau disimpan dalam cache pada komputer ini. Tajuk menunjukkan lengkung yang digunakan: `Band magnitudes (camera <SERIAL>):`, atau `Band magnitudes (model <filter>):` apabila kamera tiada (bukan kamera RGB, atau tiada pek dimuat atau disimpan) dan lengkung armada telah digunakan sebaliknya. Melalui HTTP, `POST /api/dls/cli-read` mengambil `camera_serial` dan memulangkan `band_magnitudes_source`. |
| `lattice vignette --input DIR --output DIR [--lens-model KEY]` | Terapkan pembetulan vignet pada imej sedia ada, setiap satu dengan padang rata lensa masing-masing (daripada Model EXIF-nya). `--lens-model` diterapkan pada imej yang Modelnya tidak menamakan sebarang kamera; imej yang Modelnya bercanggah diabaikan. |

#### Multi-Kamera (Sesi Sementara)

Perintah sementara membuka kamera untuk satu sesi dan menutupnya apabila ia selesai: `multi-capture` tanpa sebarang susunan kekal disambungkan, `multi-stream` dan `multi-test` di sini, dan `align-calibrate`, `align-apply`, `align-stream` dan `index --live` di bawah. Peraturan-peraturan ini terpakai untuk kesemuanya:

- **`--serials SN1,SN2,…` memilih kamera.** Hanya kamera-kamera itu dibuka, dan yang pertama mencetuskan pemicu (untuk `align-calibrate`, `--reference` apabila diberikan). Satu siri yang tidak berada dalam rangkaian akan menyebabkan pelaksanaan gagal sebelum mana-mana kamera dibuka. Tanpa `--serials`, setiap kamera dalam rangkaian akan dibuka. Kamera Chloros yang sudah memegang (GUI, `cam-connect`, `array-connect`) menolak pelaksanaan sama ada `--serials` menamakannya atau tidak: ia berkongsi rangkaian yang run disusun untuknya. (`index --live` tanpa `--serials` membuka kamera yang diaktifkan dalam profilnya sebaliknya: lihat [Index / Vegetation Maths](#index--vegetation-maths).)
- **Kamera-kamera berkongsi rangkaian dengan cara `array-connect` berkongsi.** Setiap kamera diselaraskan mengikut bahagiannya daripada apa yang dibawa oleh pautan hos (kelewatan antara paket, bukan had throughput kamera, yang had bawahnya 31.25 MB/s sudah melebihi oleh empat kamera pada 1 GbE), jadi empat kamera berjalan pada pautan 1 Gbps. Apabila semua kamera tidak dapat menghantar serentak pada kadar paling perlahan yang mereka capai, mereka diaktifkan satu persatu, masing-masing dengan pautan ke dirinya sendiri, dan arahan mencetak `Serial emit: the wire cannot carry these cameras at once, so each is triggered <ms> ms after the one before; their frames are not exposed together`. Apabila walaupun satu kamera pada satu-satu masa tidak muat, pelaksanaan ditolak sebelum sebarang kamera mula menstrim: `array over-subscribes the wire: <n> cameras demand <x> MB/s but the collision-safe ceiling is <y> MB/s. …`, HTTP 409, exit `1`.
- **Setiap kamera menghantar, atau pelaksanaan gagal.** Kamera yang tidak menghantar sebarang bingkai adalah ralat: perintah mencetak `Error: … no frame from <n> of <total> cameras: SN:<serial> (<why>), …`, di mana `<why>` adalah ralat ambil kamera itu, kemudian apa yang ditunjukkan oleh punca, dan keluar `1`. Satu bingkai yang tiba dengan kehilangan pekelangan GVSP (`IncompleteBufferError: GVSP packet loss …`) bermakna rangkaian tidak dapat memikul jumlah lebar jalur kamera (`Every missing frame arrived with GVSP packet loss: …`): periksa kelajuan pautan hos dan pautan naik suis (`lattice network`), gunakan bingkai jumbo, atau buka lebih sedikit kamera dengan `--serials`. Kamera yang tidak menghantar apa-apa biasanya tidak menerima pencetus induk: periksa kabel penyelarasan M8-nya, kemudian jalankan `lattice multi-detect`. Apa yang dilakukan oleh setiap arahan terhadap kumpulan bingkai yang kekurangan kamera dinyatakan dalam barisnya.
- **Setiap kamera dipulihkan seperti asal.** Apabila larian selesai, setiap kamera akan kembali ke keadaan asal apabila larian dibuka: `TriggerMode`, `TriggerSource`, `TriggerActivation`, `TriggerOverlap`, `TriggerDelay`, dan `LineMode` / `LineSource` pada `Line2` dan `Line3` (dan penapisan deglitch input pencetus yang diberikan oleh barisan hamba, `LineFilterSelector` / `LineFilterWidth`); `PtpEnable` (dan set `PtpSlaveOnly` / `PtpDomainNumber` bersamanya); `ExposureAuto` / `GainAuto` (dan `ExposureTime` / `Gain` di mana ia berada pada nilai manual); `TargetBrightness`, `ExposureAutoLimitAuto` / `ExposureAutoUpperLimit` / `ExposureAutoLowerLimit`, `GainAutoUpperLimit`, `ExposureAutoDamping`; `BalanceWhiteAuto` (dan setiap `BalanceRatio` di mana imbangan putih adalah manual); `BlackLevelAuto` / `BlackLevel`; `PixelFormat`, binning, `Width` / `Height` / `OffsetX` / `OffsetY`; `GammaEnable` (dan `Gamma` di mana gamma dihidupkan), `LUTEnable`, `ColorTransformationEnable`; `ReverseX` / `ReverseY`; `UserSetSelector`; saiz paket (`GevSCPSPacketSize` / `DeviceStreamChannelPacketSize`); pacing, `DeviceLinkThroughputLimitMode` (dan `DeviceLinkThroughputLimit` di mana mod adalah `On`), inter-kelewatan paket `GevSCPD` dan permulaan stagger `GevSCFTD` yang diberikan oleh setiap kamera; data ketulan yang dihidupkan oleh setiap aliran (`ChunkModeActive`, setiap `ChunkEnable` di bawah `ChunkSelector`); `AcquisitionFrameRateEnable` (dan `AcquisitionFrameRate` di mana enable dihidupkan — apabila ia dimatikan kadar adalah hanya baca dan membaca pada kadar maksimum yang dibenarkan oleh pacing, jadi ia bukan satu tetapan). Hanya apa yang berbeza ditulis, kamera berhenti dahulu, kadar pacing dan kadar bingkai sebelum pendedahan yang memerlukan tempoh bingkai mereka (kamera mengehadkan `ExposureTime` kepada satu tempoh bingkai). Dengan `--keep-settings`, pendawaian pencetus dan talian penyegerakan disambungkan semula, kamera berjalan bebas, dan baki tetapan disimpan ke dalam memori kamera sendiri seperti yang ditetapkan semasa pelaksanaan; `--clear-settings` memasukkan tetapan kilang ke situ.
- **Pendedahan ditetapkan berdasarkan adegan sebelum bingkai pertama disimpan.** Proses ini mengambil kumpulan bingkai yang diselaraskan dan membuangnya sambil menetapkan pendedahan dan penguatan setiap kamera daripada bingkai-bingkai tersebut. Matlamatnya ialah mendapatkan purata kecerahan pada sasaran kamera tanpa lebih daripada 1% bingkai dipotong; bagi adegan dengan sorotan terang, proses akan berhenti sebelum mencapai sasaran. Ini biasanya mengambil 2-6 kumpulan bingkai, dan paling lama 5 s.
  - Setiap kamera mensasarkan target yang dimulakan oleh tatasusunan bersambung pada filternya: TargetBrightness **40**pada kamera multispektral (RGN, OCN, NGB) dan**60** pada kamera RGB. Ini adalah separuh sasaran pendedahan yang diberikan oleh 80 (tiga perempat pada RGB), jadi panel reflektansi cerah kurang berkemungkinan dipotong. `--brightness N` (0-255) menetapkan sasaran setiap kamera sebaliknya (`multi-test` tidak mempunyai `--brightness`: pendedahan automatiknya dimatikan). Log backend menamakan sasaran setiap kamera: `[transient-ae] AE target per camera (filter default): SN:<serial> <target>, …`.
  - Eksposur didahulukan, sehingga had auto-eksposur kamera itu sendiri. Penguatan hanya meliputi baki yang tinggal.
  - Eksposur dan penguatan yang diperoleh dikekalkan untuk keseluruhan pelaksanaan, jadi setiap bingkai `multi-stream` diambil pada eksposur yang sama. Rakaman yang mesti mengikuti perubahan cahaya sesuai diletakkan pada tatasusunan bersambung (`array-connect`), yang pengecaman automatiknya terus menyesuaikan diri.
  - `--exposure` dan `--gain` dikekalkan tepat seperti yang diberikan. Dengan kedua-duanya diberikan, tiada bingkai yang dibuang.
  - Apabila larian tamat, pendedahan automatik setiap kamera dihidupkan semula.
  - Log backend melaporkan keputusan: `[array-ae] exposure solved in <s> s (<n> warm-up groups): SN:<serial> <ms> ms <dB> dB mean <m>/<target>, …`, atau `exposure NOT solved after …` dengan sebab dan `(not settled)` pada setiap kamera yang tidak terjejas. Bingkai-bingkai masih diambil dalam kes itu.
  - `CHLOROS_TRANSIENT_AE_SETTLE_S` menukar had 5 s; `0` mematikan ini (lihat [Pembolehubah Persekitaran](#environment-variables)).

**Semasa Chloros memegang mana-mana kamera** (GUI, `cam-connect`, atau tatasusunan yang disambungkan), `multi-stream`, `multi-test`, `multi-detect`, `align-apply`, `align-stream` dan `index --live` enggan dijalankan, kerana ia membuka setiap kamera di rangkaian dan pembukaan kedua daripada kamera yang dipegang boleh menyebabkan backend terhenti. (`index --live` menolak apabila kamera yang ingin dibuka dipegang: sama ada `--serials` miliknya atau kamera dalam profilnya, dan mana-mana kamera untuk profil yang tidak menamakan apa-apa.) Backend menjawab HTTP 409 `CAMERA_HELD`, menyenaraikan setiap kamera yang dipegang dan arahan pelepasan (`chloros-cli lattice cam-disconnect --all` / `chloros-cli lattice array-disconnect --all`), dan arahan itu keluar `1`. Apabila satu susunan disambungkan, `array-capture --continuous` merekodkannya di tempat `multi-stream`, dan `array-capture` (disesuaikan secara lalai) menyimpan satu set yang diselaraskan di tempat `align-apply`; `multi-capture` dan `align-calibrate` menggunakan susunan bersambung yang memegang kamera mereka bukannya membukanya.

| Subperintah | Tujuan |
| --- | --- |
| `lattice multi-info [--serials SN1,SN2,…]` | Senaraikan semua kamera dengan peranan penyelarasan; dengan `--serials`, hanya yang pertama sebagai `MASTER`. |
| `lattice multi-capture [--serials SN1,SN2,…] [--format FMT] [--jpeg-quality N] [--processing LEVEL] [--brightness N] [--no-daq] [--daq-tolerance-ms MS]` | Satu bingkai diselaraskan daripada setiap kamera. Menyimpan **semua jenis eksport secara lalai**apabila satu susunan kekal disambungkan, dan bacaan DAQ yang digunakan ([Bacaan DAQ untuk tangkapan menyimpan](#the-daq-reading-a-capture-saves)); fallback sementara tanpa array hanya**mendebayer sahaja** (jalankan `array-connect` terlebih dahulu untuk yang lain). `--brightness` hanya terpakai pada fallback: array yang disambungkan mengekalkan pengesanan automatiknya sendiri. Fallback tidak menyimpan sebarang `.daq` (`--no-daq` / `--daq-tolerance-ms` tidak memberi kesan di sana), dan `multi-capture` tidak mengambil `--force-daq`. Dalam fallback, kamera yang tidak menghasilkan sebarang bingkai akan menyebabkan kegagalan tangkapan (`Error: multi-capture failed: no frame from …`, keluar `1`) dan tiada fail bagi kumpulan bingkai tersebut disimpan. |
| `lattice multi-stream [--serials SN1,SN2,…] [--fps F] [--count N] [--format FMT] [--jpeg-quality N] [--brightness N]` | Menyegerakkan bingkai (sementara), `--fps` lalai `3`, sehingga Ctrl+C atau `--count` kumpulan. Kumpulan bingkai yang tiada disampaikan oleh kamera tidak ditulis, dan tiga berturut-turut menamatkan aliran. Jika mana-mana kumpulan dilangkau, pelaksanaan berakhir dengan mencetak `Incomplete: <n> frame group(s) not written, a camera delivered nothing (SN:<serial> x<count>, …)`, ditambah `Error: the stream stopped: …` yang menamakan kamera apabila ia menamatkan aliran, dan keluar `1`. Menghentikan sendiri apabila pemacu penuh, mencetak `Failed:` / `Disk full:` dan keluar `1`; mana-mana fail yang tidak dapat ditulis juga akan keluar `1`. |
| `lattice multi-test [--serials SN1,SN2,…] [--count N] [--ptp-settle SECONDS]` | ujian masa selari GPIO (sementara), `--count` pencetus lalai `10`. Mencetak bagaimana rangkaian dikongsi (`Wire: each camera paced to <x> MB/s (<n> on a <y> MB/s ceiling, <link> Mbps link)`, dan baris `Serial emit:` apabila kamera dicetuskan satu demi satu, yang kemudiannya memasukkan selang cop masa), satu baris `Sync group: master SN:<serial> -> SN:<serial>, …` bagi setiap kumpulan penyegerakan (`(no slaves: not synced)` untuk kamera tanpa kabel penyegerakan, yang dicetuskan secara berasingan), kemudian satu baris bagi setiap pencetus: `[  0] SN:<serial>=<timestamp> \| …`, dengan `SN:<serial>=(no frame)` bagi kamera yang tidak menghantar apa-apa untuk pencetus tersebut. Kemudian `Offsets from master SN:<serial> (<PTP / device (PTP off)> clock, <n> trigger(s)):` dan satu baris `SN:<serial>: mean <±x> us, jitter <y> us` bagi setiap kamera, setiap kamera berbanding tuan rumah dalam satu jam: pencetus yang capnya mencampurkan jam peranti kamera dan PTP (PTP dimatikan atau masih berkunci) diabaikan, dengan `Note:` mengiranya. Sebarang bingkai yang terlepas menyebabkan ujian gagal (`Error: multi-test failed: <k> of <n> frame groups incomplete (SN:<serial> missed <k>); …`, keluar `1`); tiga pencetus tidak lengkap berturut-turut menamatkannya lebih awal. Ia juga gagal (keluar `1`) apabila kurang daripada dua pencetus mempunyai setiap kamera pada satu jam (`… the clocks cannot be compared`), atau apabila jam tidak pernah sepadan: jam PTP lebih daripada 1 ms (ditambah sebarang selang peringkat bersiri) daripada tuan rumah (`… PTP is not locked`), atau jam peranti yang selisihnya berubah lebih daripada itu antara pencetus. Ia memulakan grandmaster PTP jika ia tidak berjalan, menghidupkan PTP setiap kamera (seperti yang dilakukan oleh `array-connect`, tidak kira rupa rangkaian), dan sebelum pencetus pertama menunggu jam PTP setiap kamera terkunci pada grandmaster dan stabil: `PtpStatus` Slave pada satu grandmaster, dalam lingkungan 100 us daripadanya dan stabil selama 3 s, dan setiap kamera terkunci sedemikian, tanpa henti, selama 120 s — kira-kira 2 minit dari kuasa-on. Jam yang baru terkunci akan tersasar sehingga ~200 us selama kira-kira 2 minit selepas ia membaca Slave, dan `PtpOffsetFromMaster` pada kamera sendiri tidak menampilkannya; 120 s membawa kamera-kamera itu ke dalam julat ~10 us daripada jam yang telah stabil. Satu kamera masih terkunci sejak ujian awal yang membiarkan PTP dihidupkan, seperti yang dilihat oleh backend ini, mengira dari kunci itu, jadi ujian sejurus selepas satu lagi tidak menunggu semula; kamera yang dihidupkan oleh ujian, atau yang ditemui dengan PTP dihidupkan yang backend ini belum pernah lihat berkunci (dihidupkan di tempat lain, atau sebelum mula semula backend), menunggu 120 s, dan dalam satu susunan kedua-dua kamera yang baru sahaja berkunci masih menunggu giliran mereka. Kamera yang keluar daripada Slave, melepasi 100 us atau mengikuti grandmaster lain untuk 3 bacaan berturut-turut (1.5 s) semasa menunggu akan memulakan semula pengiraannya; satu atau dua bacaan sedemikian direkodkan (`[array-sync] PTP SN:<serial> off lock at <s> s (<n> of 3): <status> <±x> us gm <id> -- locked <s> s, counting on`) dan kiraan diteruskan, dan id grandmaster yang tidak dapat dibaca dianggap sebagai tidak diketahui, bukan sebagai perubahan. Setiap kamera memulakan semula kiraan serentak (dalam masa satu saat) adalah pergerakan masa grandmaster, bukan kamera: log backend menunjukkan `[array-sync] PTP grandmaster step: all <n> cameras restarted at <s> s …`, `ptp_lock` menyenaraikannya dalam `grandmaster_steps`, dan satu penantian yang kemudian tamat bermula kesalahannya dengan `every camera's count restarted together at <s> s -- the PTP grandmaster's time moved under them …`. Had itu tidak dilanjutkan untuknya. `--ptp-settle SECONDS` menetapkan masa penyelesaian untuk pelaksanaan (0–600; `0` untuk tiada menunggu penyelesaian). Tempoh menunggu dihadkan kepada masa penyelesaian ditambah 90 s (210 s secara lalai, sekurang-kurangnya 90 s), dan arahan menunggu backend selama 240 s ditambah masa penyelesaian (360 s secara lalai). Ia mencetak `PTP: locked after <s> s (each camera locked <settle> s, already locked: SN:<serial>, …; from the grandmaster: SN:<serial> <±x> us, …)`, dan log backend mempunyai satu baris setiap 5 s dengan status dan offset setiap kamera, berapa lama ia telah terkunci dan berapa lama yang tinggal: `[array-sync] PTP at <s> s: SN:<serial> Slave <±x> us for <s> s (<s> s left), … -- <s> s left, the wait gives up at <s> s`. Setiap kamera dihidupkan dengan PTP dimatikan; daripada situ jam akan terkunci dalam kira-kira 10 s. Selepas itu setiap kamera dipulihkan seperti keadaan asal apabila ujian menemuinya — PTP, pendedahan automatik dan penguatan yang dibiarkan 'Off' oleh ujian untuk jalannya, dan selebihnya (kecuali `--keep-settings`) — jadi kamera yang ditinggalkan dengan PTP dihidupkan (`lattice configure -s <serial> --set PtpEnable=True`, atau larian dengan `--keep-settings`) kekal terkunci, dan setelah backend ini melihat ia terkunci untuk tempoh penetapan, ujian kemudian tidak menungguinya. Jika jam tidak menetap dalam masa menunggu, ia gagal sebelum dicetuskan (`PTP: NOT locked …`, keluar `1`) dan memberitahu apa yang perlu dilakukan: kamera yang tidak pernah terkunci (`PtpStatus Listening`) bermakna grandmaster tidak sampai kepadanya (`time-sync status`); manakala kamera yang sentiasa terputus-putus dinamakan mengikut berapa lama ia telah terkunci (`… had not been locked for the 120 s settle time … (SN:<serial> locked <s> s, <s> s to go)`); jam masih konvergen: semak `time-sync status`, dan untuk memastikan ia terkunci sebelum ujian, hidupkan PTP dan biarkan ia hidup (`lattice configure -s <serial> --set PtpEnable=True` untuk setiap kamera, atau jalankan ujian dengan `--keep-settings`; ujian ini akan memulihkan apa yang ditemuinya, jadi ia kekal hidup), kemudian jalankan ujian. |
| `lattice multi-detect [--line LINE] [--json]` | Cari kamera pada talian selari M8, dan master lalai: kamera RGB (model berakhir dengan `-FRGB`) jika ia bersambung kabel, jika tidak, kamera bersambung kabel pertama mengikut nombor siri. Kabel M8 bertemu di satu sambungan, jadi **mana-mana kamera yang disambungkan boleh menjadi tuan rumah** — `MASTER` yang dicetak adalah lalai, bukan fakta pendawaian. Setiap kamera secara bergilir memacu talian dan kamera lain diperiksa untuk denyutannya; kamera yang telah ditemui dalam kumpulan berwayar tidak akan diuji semula, jadi satu susunan yang sepenuhnya berkabel mengambil satu pusingan. Tanpa `--line` ia menyiasat `Line2`, kemudian `Line3`. Mencetak `MASTER: SN:…`, satu `SLAVE: SN:…` bagi setiap kamera berwayar lain dan `(probed on <line>)`; `--json` menambah hasil penuh (`master`, `slaves`, `no_cable` — kamera yang denyutan tidak sampai — `line`, dan `results` bagi setiap kamera). Apabila ia selesai, setiap kamera memulihkan tetapan pencetus dan talian penyegerakan yang dimilikinya (nod yang sama seperti di atas). |

#### Penyelarasan

| Subperintah | Tujuan |
| --- | --- |
| `lattice align-calibrate [--serials SN1,SN2,…] [--method orb\|akaze\|phase\|checkerboard\|manual] [--model translation\|rigid\|affine\|homography] [--frames N] [--checkerboard RxC] [--points PATH] [--reference SN] [--save PATH] [--preview] [--vignette] [--prefilter none\|gradient\|clahe\|blur\|hist_match] [--rms-threshold-px N] [--brightness N]` — tombol detektor/pemadanan tambahan `[--max-features N] [--ratio-threshold F] [--matcher bf\|flann] [--knn-k N]`, pusingan RANSAC `[--ransac-threshold-px F] [--ransac-iters N] [--ransac-confidence F]`, gabungan berbilang bingkai `[--averaging mean\|median\|inlier_weighted]`, kekangan geometri `[--lock-rotation] [--lock-scale] [--lock-axis x\|y]`, sekatan ruang `[--roi X0,Y0,X1,Y1] [--mask PATH]`, dan per-slave mengatasi `[--per-cam-override SN:KEY=VALUE]` (boleh diulang) | Mengira profil penjajaran daripada kamera langsung. Lalai: `--method orb`, `--model rigid`, `--frames 3`, `--save alignment_profile.json` (dalam folder di mana anda menjalankannya), `--max-features 2000`, `--ratio-threshold 0.75`, `--matcher bf`, `--knn-k 2`, `--ransac-threshold-px 3.0`, `--ransac-iters 2000`, `--ransac-confidence 0.99`, `--averaging mean`. (`project align calibrate` mempunyai tetapan lalai tersendiri; lihat [jadualnya](#project-align-calibrate-options).) `--prefilter` secara lalai menggunakan `gradient` (peta tepi; sepadan dengan GUI/penyelarasan susunan — tepi kekal merentasi jalur spektral). `--matcher flann` memberi pulangan di atas ~5000 ciri; `--averaging median` tahan terhadap satu tangkapan buruk, `inlier_weighted` memberi berat mengikut bilangan padanan; `--lock-scale` memaparkan ke putaran terdekat (tanpa skala), `--lock-axis` menetapkan sifar pada satu komponen terjemahan; `--mask` terpakai pada setiap kamera (gunakan `--per-cam-override` untuk tetapan bagi setiap kamera, contohnya `--per-cam-override 214701292:method=phase`). `--rms-threshold-px` menolak untuk menyimpan kalibrasi yang RMS reprojeksi melebihi gerbang. `--method manual` mengambil titik-titiknya dari `--points PATH`: obyek JSON `{"<serial>": [[x, y], ...], ...}` yang memberikan koordinat piksel setiap kamera bagi ciri yang sama, termasuk kamera rujukan (sekurang-kurangnya 2 bagi setiap kamera); setiap `--frames` diselesaikan daripada titik-titik tersebut. Apabila satu susunan bersambung memegang kamera-kamera, kalibrasi menggunakan bingkai langsung susunan tersebut, dan profil juga menjadi penjajaran susunan itu untuk tangkapan-tangkapan. Jika tidak, ia membuka [susunan sementara](#multi-camera-transient-sessions): kamera yang tidak menghantar sebarang bingkai dalam mana-mana `--frames` akan gagal penentukuran (keluar `1`, menamakannya), dan tiada profil disimpan. |
| `lattice align-apply --profile PATH [--serials SN1,SN2,…] [--format tiff\|png] [--bit-depth 8\|12\|16] [--bands NAMES] [--order NAMES] [--gpu\|--no-gpu] [--no-crop] [--per-camera] [--per-band] [--vignette] [--interpolation nearest\|linear\|cubic\|lanczos] [--border-mode constant\|replicate\|reflect\|wrap] [--border-value N] [--brightness N]` | Tangkap satu bingkai berbilang jalur yang diselaraskan. `--bit-depth` secara lalai memadankan kamera; `--no-crop` mengekalkan bingkai penuh (pad dengan hitam); `--interpolation` (lalai `linear`) dan `--border-mode`/`--border-value` (default `constant`/0) mengawal warapan CPU — laluan GPU tetap bilinear. Membuka [susunan sementara](#multi-camera-transient-sessions): kamera yang tidak menghantar sebarang bingkai akan menyebabkan rakaman gagal (keluar `1`, menamakannya), dan tiada apa-apa yang ditulis. |
| `lattice align-stream --profile PATH [--serials SN1,SN2,…] [--fps F] [--count N] [--bit-depth 8\|12\|16] [--bands NAMES] [--order NAMES] [--gpu\|--no-gpu] [--no-crop] [--per-band] [--vignette] [--interpolation nearest\|linear\|cubic\|lanczos] [--border-mode MODE] [--border-value N] [--brightness N]` | Mengalirkan bingkai jalur berganda yang selari (knob warp yang sama seperti `align-apply`), `--fps` lalai `3`. Membuka [transient array](#multi-camera-transient-sessions): kumpulan bingkai yang kamera tidak menyampaikan apa-apa tidak disimpan, dan tiga berturut-turut menamatkan aliran. Jika mana-mana kumpulan dilangkau, pelaksanaan akan berakhir dengan mencetak `Incomplete: <n> frame group(s) not saved, a camera delivered nothing (SN:<serial> x<count>, …)` dan `Error: …`, dan keluar `1`. Sebarang aliran yang berakhir pada ralat lain juga mencetak `Error: …` dan keluar `1`. |
| `lattice align-info --profile PATH [--json]` | Paparkan butiran profil. |
| `lattice align-reorder --profile PATH [--order NAMES] [--enable SERIALS] [--disable SERIALS]` | Ubah susunan lapisan. |

#### Indeks / Matematik Vegetasi

```bash
# Offline: compute NDVI from an aligned multi-band TIFF (an RGN camera's bands)
chloros-cli lattice index --input aligned.tif --preset NDVI \
  --channel red=Red_660_RGN --channel nir=NIR_850_RGN \
  --output ndvi.tif --colorize --gradient RdYlGn

# Live: open the cameras, capture one aligned set with a profile made by
# `lattice align-calibrate`, and compute the index from it
chloros-cli lattice index --live --profile align.json --preset NDVI \
  --channel red=Red_660_RGN --channel nir=NIR_850_RGN \
  --save-multiband -o output/
```

**`--channel` memetakan simbol pratetap kepada jalur** (NDVI: `red`, `nir`), satu `--channel SYMBOL=BAND` setiap satu.

- **`--live`: hanya untuk simbol yang susunan tidak dapat jawab.Seperti dengan `--input`, simbol pratetap yang anda tinggalkan mengambil satu jalur daripada susunan (jalur kamera yang diaktifkan dalam profil) yang namanya bermula dengannya. Ini diperiksa sebelum mana-mana kamera dibuka: simbol yang tiada jalur, atau beberapa jalur, menjawabnya akan ditolak kemudian, menamakan mereka, keluar `1`. `--formula` tersuai memerlukan `--channel` untuk setiap simbol.
- **Produk `--live` menyatakan milik siapa mereka.** `index.tif` dan `index_LUT.png` membawa kamera tumpukan (Jenama, Model, siri), masa tangkapan (pendedahan kamera rujukan, UTC) dan lensa, dan kedua-duanya menamakan indeks itu sebagai jalur tunggal mereka (`XMP-Camera:BandName` `NDVI`; imej berwPNGmenamakan indeks itu, bukan saluran warnanya), seperti yang dilakukan oleh produk `--input`. Sebaliknya, satu indeks daripada jalur beberapa kamera menamakan setiap satunya, dan `Model`-nya ialah `LATTICE` — lihat [Fail yang dibuat daripada beberapa kamera](#files-made-from-several-cameras).
- **`--input`: hanya untuk simbol yang fail tidak dapat jawab.** Simbol yang anda tinggalkan mengambil satu jalur fail yang namanya bermula dengannya: `red` mengambil `Red_660` atau `Red_660_RGN`, `nir` mengambil `NIR_850`. Nama-nama itu datang daripada fail itu sendiri: senarai jalur milik tumpukan itu sendiri, atau, pada tangkapan sinaran / pantulan Chloros atau produk `process`, tag `Chloros:Band{R,G,B}Name` / `XMP-Camera:BandName` miliknya. Jadi `lattice index --input <reflectance capture> --preset NDVI` tidak memerlukan `--channel`.
- **Dua jalur yang menjawab kepada satu simbol tidak diagak di antara** (susunan 9 kamera mempunyai dua jalur NIR): namakan itu. Simbol yang tiada sebarang jawapan ditolak dengan `channel_map missing entries`, keluar `1`.**Nama jalur dalam susunan** membawa penapis kamera: jalur kamera M3C ialah `<band>_<filter>` — `NIR_850_RGN`, `Green_550_RGN`, `Red_660_RGN` pada OCN; `NIR_808_OCN`, `Cyan_490_OCN`, `Orange_615_OCN` pada OCN; `Blue_475_RGB`, `Green_550_RGB`, `Red_625_RGB` pada RGB; `Blue_475_NGB`, `Green_550_NGB`, `NIR_850_NGB` pada OCN; `Blue_475_RGB`, `Green_550_RGB`, `Red_625_RGB` pada RGB; `Blue_475_NGB` jalur tunggal ialah `Mono_<filter>` (`Mono_660`, `Mono_850`). XPgelombang tunggal ialah `Mono_<filter>` (`Mono_660`, `Mono_850`). `lattice align-info --profile PATH` menyenaraikan gelombang dalam satu profil.

Set penuh bendera: `--input PATH | --live --profile PATH`, `--preset NAME` (NDVI/NDRE/EVI/SAVI/GNDVI/…), `--formula EXPR`, `--channel SYM=BAND` (boleh diulang), `--capture-level raw|debayered|radiance|reflectance|unknown` (menimpa tahap tangkapan yang direkodkan dalam sumber TIFF; lalai: dibaca daripada metadata TIFF, jika gagal menggunakan `Chloros:ProcessingLevel` dalam fail Chloros, maka pengambilan pantulan melaporkan `reflectance`), `--output PATH` / `-o` (luar talian: satu laluan; dengan sambungan imej, fail dinamakan berdasarkan batang imej itu — `-o ndvi_FRGN.png` menulis `ndvi_FRGN.tif` dan `ndvi_FRGN_LUT.png` — dan tanpa satu daripadanya ia menjadi awalan, `-o ndvi` -> `ndvi.tif`, `ndvi_LUT.png`; lalai di sebelah input, seperti `<input name>_<preset>`; dengan `--live` dan tiada `-o`, satu folder `output/` di bawah folder di mana anda menjalankan arahan), `--output-format all|raw|tif|colorized|lut|png`, `--gradient NAME|JSON`, `--vmin/--vmax/--percentile LO,HI`, `--bg-mode clip|transparent|indexColor|backgroundColor`, `--colorize`, `--list-presets`, `--list-gradients`. Dengan `--live` tombol bengkok penjajaran juga terpakai: `--save-multiband`, `--gpu/--no-gpu`, `--no-crop`, `--bit-depth 8|12|16`, `--vignette`, `--interpolation nearest|linear|cubic|lanczos`, `--border-mode constant|replicate|reflect|wrap`, `--border-value N`, `--brightness N` (sasaran pendedahan automatik kamera, lihat peraturan transient), `--serials SN1,SN2,…`. `--live` membuka [susunan transient](#multi-camera-transient-sessions): kamera yang tidak menyampaikan sebarang bingkai akan menyebabkan kegagalan larian (keluar `1`, menamakannya), dan tiada susun atur atau indeks yang ditulis. Ia membuka `--serials` (yang pertama mencetuskan pemicu), jika tidak, kamera dalam profil yang kemasukannya `layer_order` diaktifkan, rujukannya dahulu — rujukan itu walaupun jalurnya dilumpuhkan, kerana yang lain diselaraskan kepada dan ia mencetuskan pemicu. Kamera yang jalurnya dilumpuhkan tidak pernah dikalibrasi, jadi kamera RGB yang dilumpuhkan dalam profil tidak lagi gagal pada indeks reflektansi (`no-absolute-radiance-scale`). Profil yang tidak menamakan sebarang kamera akan membuka setiap kamera di rangkaian, seperti sebelum ini.

Setiap borang berjalan pada backend, jadi ia mesti sedang berjalan: Mulakan ia daripada GUI Chloros, atau dengan arahan yang dicetak oleh CLI apabila ia tidak dapat mencapainya. Secara luar talian, backend membaca `--input` dan menulis float TIFF dan PNG berwarna itu sendiri, jadi `--input` atau XPR relatifOTX001604 menyelesaikan rujukan terhadap folder tempat anda menjalankan arahan (lihat [Konvensyen](#conventions)). Kedua-dua produk luar talian membawa rekod kamera input, seperti eksport indeks `process`: Jenama / Model / siri, GPS, tag lensa, masa tangkapan (milik input itu sendiri; apabila EXIF-nya menyatakan tiada, saat pendedahan yang direkodkannya atau cop nama failnya, dalam UTC) dan identiti penentukuran MAPIR — bukan penentukuran raw-DN, yang tidak terpakai pada indeks — ditambah `Chloros:ProcessingLevel=index` dan ungkapan indeks; nama TIFF terapung menamakan satu jalur, preset. `--list-presets` dan `--list-gradients` mencetak jadual pratetap dan gradien backend. Formula yang ditolak oleh backend, atau jalur `--channel` yang tiada dalam TIFF, akan keluar daripada `1` dengan sebab daripada backend.

> Simbol `--channel` adalah sensitif huruf besar-kecil.** Sisi simbol mesti sepadan dengan nama saluran pratetap dengan tepat (pratetap menggunakan huruf kecil, contohnya NDVI = `red`,`nir` — semak `--list-presets`), dan sisi jalur mesti sepadan dengan nama jalur dalam susunan yang selari (atau menjadi indeks jalur berasaskan 0 dalam mod luar talian). `--channel red=Red_660_RGN --channel nir=NIR_850_RGN` berfungsi; `--channel RED=660` gagal dengan `channel_map missing entries` ralat.

#### `lattice index` — topeng susunan berbilang jalur

Pada `--capture-level radiance` atau `reflectance` setiap pencampuran semula 3×3 kamera boleh mengeklampkan satu jalur kepada 0 (lihat [Topeng clamp unmix](#unmix-clamp-mask-which-pixels-a-band-was-clamped-on)). Satu susunan berbilang jalur mengandungi jalur daripada beberapa kamera, dan setiap kamera mengapit jalurnya sendiri, jadi `--live --save-multiband` menulis **satu topeng bagi setiap kamera sumber** di sebelah susunan:

```
output/
├── index_multiband.tif
└── clamp_masks/
    ├── index_multiband_SN<serial A>-<filter A>_clampmask.tif
    └── index_multiband_SN<serial B>-<filter B>_clampmask.tif
```

- Setiap topeng adalah `uint8` jalur tunggal pada grid piksel **tumpukan** (melalui pemutar dan pemangkasan penjajaran yang sama), dengan bit kamera itu sendiri: bit 0 = jalur saluran R-nya, bit 1 = G, bit 2 = B. Sebarang jalur tumpukan yang diambil daripada saluran *c* sesebuah kamera akan dikekang di mana bit *c* kamera tersebut (R = 0, G = 1, B = 2) ditetapkan.
- Nama asal usul terbenam bagi setiap jalur menamakan topeng kamera tersebut (`unmix_clamp_mask`, berbanding dengan folder susunan, atau `none`) dan bit untuk dibaca (`unmix_clamp_mask_bit`).
- Kamera yang unmix-nya tidak mengunci apa-apa tidak menerima fail; kamera mono atau RGB tidak menjalankan unmix dan juga tidak menerima apa-apa. CLI mencetak setiap topeng di bawah susunan (`clamp mask SN…: …`); respons menyenaraikannya sebagai `clamp_masks`.
- Indeks TIFF / PNG bagi larian yang sama dikira daripada susunan yang sama, pada grid yang sama, jadi topeng-topeng itu juga menerangkan tentangnya. `--capture-level raw` tidak menjalankan unmix dan tidak menulis sebarang topeng.
- Susun lapis dan indeks disimpan terus dalam `--output`, jadi topeng disimpan dalam `clamp_masks/` milik `--output` — tidak pernah di atas folder yang anda namakan. Folder itu mencampurkan jenis produk (stack, index, render berwarna): pilih failnya, jangan serah keseluruhan folder kepada perisian fotogrametri.

#### Sambungan Kekal (Aliran Smart-Prep, Setara GUI)

Arahan-arahan ini mengekalkan kamera terbuka dalam kolam backend merentasi invokasi CLI.

| Subperintah | Tujuan |
| --- | --- |
| `lattice cam-connect [--serial SN]` | Menambah satu kamera ke dalam kolam (satu kamera, tiada tatasusunan). [pilihan biasa](#common-options-shared-by-most-subcommands) `--exposure`, `--gain`, `--preset`, `--pixel-format`, `--width` / `--height` dan `--packet-size` (`standard` = 1500, `jumbo` = 9000, atau bait; `auto` probe) digunakan semasa sambungan (sebuah `--exposure` atau `--gain` yang mematikan mod automatik). Ia akan kembali apabila aliran kamera telah bermula (menunggu kira-kira 15 saat untuk itu, kemudian 10 saat untuk buffer pertamanya yang lengkap) dan mencetak `stream: streaming`, atau `stream: no_frames -- connected, no frames yet: …` apabila aliran tidak menyampaikan apa-apa — untuk kamera pada paket jumbo, dengan nasihat untuk menyambung semula dengan `--packet-size standard`. Sementara satu kamera disambungkan (sambungan pertama memuat turun penentukuran, kira-kira 70 s), arahan lain yang memerlukan kamera menunggu sehingga maksimum 5 s, kemudian gagal dengan HTTP 503 `CAMERA_POOL_BUSY` yang menamakan kamera yang sedang disambungkan; jalankan semula selepas seketika. `lattice status` dan senarai kamera tidak pernah menunggu: kamera yang sedang disambungkan akan dipaparkan sebagai sedang menyambung, dengan fazenya. |
| `lattice cam-disconnect [--serial SN] [--all] [--keep-settings \| --clear-settings]` | Bebaskan. Kamera dipulihkan seperti ketika ditemui semasa sambungan (penyambungan pemicu, PTP, tetapan); `--keep-settings` menyimpan tetapannya ke dalam memori sendiri sebaliknya, `--clear-settings` menetapkan semula memori itu kepada tetapan kilang (lihat [Memori kamera](#camera-memory----keep-settings----clear-settings)). Kamera yang merupakan ahli tatasusunan akan membebaskan keseluruhan tatasusunan. |
| `lattice cam-list` | Senaraikan kamera dalam kolam. |
| **`lattice array-connect`**|**Sambungkan tatasusunan berterusan yang diselaraskan (titik permulaan yang disyorkan).** Mengjalankan aliran penyediaan pintar GUI sepenuhnya. Kira-kira satu minit untuk 9 kamera; segera selepas kamera dihidupkan ia boleh mengambil beberapa masa. Semasa ia menunggu ia mencetak setiap langkah yang dicapai oleh backend, sebagai `    <s> s: <step>` (contohnya `    41 s: open SN:214000535 (4 of 9)`), dan log backend mempunyai satu baris masa bagi setiap langkah (`[array-connect <id>] <step>: <s> s …`) dan satu baris terakhir dengan langkah-langkah paling perlahan. Ia menunggu sehingga 600 saat. Sebarang sambungan yang belum dilakukan menjelang masa itu akan dibatalkan oleh backend: setiap kamera yang dibuka akan dipulihkan seperti asal dan ditutup, perintah mencetak `Error:` dengan langkah yang telah dicapai dan keluar `1`, dan tiada rangkaian disambungkan (HTTP 504 `CLIENT_DEADLINE_PASSED`). Ctrl+C membatalkan sambungan dengan cara yang sama (`Interrupted: …`); jika array tersebut sudah bersambung, baris itu menyatakan demikian dan menamakan `array-disconnect` yang membebaskannya. Perintah yang dibunuh semasa ia menunggu (`kill -9`) membiarkan sambungan itu dibatalkan apabila backend tidak mendengar daripadanya selama 30 saat. Susunan yang sedang aktif tetapi belum mempunyai kumpulan bingkai bersepadu masih dilaporkan bersambung, dengan streamnya sebagai `no_frames`. |
| `lattice array-disconnect [--array-id ID] [--all] [--keep-settings \| --clear-settings]` | Melepaskan satu tatasusunan. Setiap kamera dibiarkan berjalan bebas (`TriggerMode` `Off`), dengan segala-galanya yang lain seperti yang ditemui semasa sambungan — tetapan pencetus dan talian penyelarasan (master's `TriggerSource` kembali kepada asal, talian sinchronisnya kembali menjadi input), PTP-nya, dan tetapan kamera yang diubah oleh sesi — supaya arahan satu kamera pada ahli berfungsi serta-merta. `--keep-settings` menyimpan setiap kameratetapan setiap kamera seperti yang ditinggalkan oleh sesi ke dalam memori kamera sendiri; `--clear-settings` menetapkan semula memori itu kepada tetapan kilang. Lihat [Memori kamera](#camera-memory----keep-settings----clear-settings). |
| `lattice array-list` | Senaraikan susunan yang disambungkan. |
| `lattice array-status [--array-id ID]` | fps langsung, PTP, ralat terakhir. `ptp_enabled` adalah `True` hanya apabila setiap ahli membaca PTP pada; `ptp_off:` menamakan seorang ahli yang tidak melakukannya. `state:`baris OTX membaca `restarting (applying settings)` semasa but semula tetapan, `recovering (…)` semasa seorang ahli yang terputus kuasa dibuka semula (lihat nota di bawah), `stalled (…)` semasa gelung grab berjalan tetapi tidak menerbitkan kumpulan bingkai, dan `stopped (…)` apabila gelung grab telah menyerah (sambungkan semula susunan). |
| `lattice array-capture [--processing LEVEL\|all] [--levels L1,L2,…] [--aligned\|--no-aligned] [--orient] [--index\|--no-index] [--no-daq] [--daq-tolerance-ms MS] [--smart] [--fastest] [--compression deflate\|none] [--continuous\|--interval S] [--count N] [--duration S]` | Satu tangkapan diselaraskan daripada susunan langsung — Satu / Berterusan / Jeda / Yang terpantas. **Lalai kepada `all`**(satu fail bagi setiap jenis eksport yang terpakai bagi setiap kamera),**disesuaikan** seperti tangkapan aplikasi (`--no-aligned` untuk fail asli). Kamera yang dilangkau (contohnya RGB dikecualikan daripada radiasi/refleksi) dilaporkan dengan `Skipped: SN:<serial> (<reason>)`; bacaan DAQ yang digunakan untuk tangkapan disimpan bersebelahan, tangkapan mentah sahaja disertakan, dan dilaporkan dengan `DAQ: <path>` ([The DAQ reading a capture saves](#the-daq-reading-a-capture-saves)). Lihat [Mod Rakaman, Perakam & Pemprosesan Semula Luar Talian](#capture-modes-recorders--offline-reprocess). |
| `lattice array-record [--fps F] [--duration S] [--gif] [--gif-only] [--expr EXPR]` | Rakam paparan indeks gabungan secara langsung ke video/GIF (darjah pemantauan; tanpa sebarang paparan GUI dibuka, backend memaparkan NDVI susunan, atau `--expr`). |
| `lattice array-burst [--duration S] [--max-frames N] [--orient] [--build] [--products …]` | Letusan mentah Bayer berkelajuan tinggi (darjah analisis; diproses semula secara luar talian). Seperti `array-record` dan `array-capture --continuous`, ia mendaftar dengan backend pada setiap tinjauan: dibunuh tanpa dihentikan (`kill -9`), backend menghentikannya 30 saat selepas pemeriksaan terakhir (`stop_reason` `client_gone`; manifes burst dimuktamadkan dan pembinaan indeks gabungan dimulakan, seperti yang berlaku apabila pemberhentian) dan array kekal bersambung. |
| `lattice array-build-video --burst-dir DIR [--products …] [--fps F] [--save-tiffs] [--gif]` | Memproses semula burst mentah yang disimpan menjadi video(s) yang dikalibrasi. |

> **Kamera yang kehilangan kuasa atau pautannya akan kembali secara automatik.** Apabila sesuatu ahli berhenti menjawab (suis kitar semula kuasa memulakan semula setiap kamera yang dikuasakannya), susunan itu menunggu kameranya, membukanya semula apabila ia menjawab semula, dan memulangkan apa yang ditetapkan oleh `array-connect`: PTP, talian pencetus dan penyegerakan, kelajuan rangkaian, format piksel dan saiz bingkai, serta pendedahan automatik. Ia kemudian menyalurkan semula tanpa `array-connect` baru. Sementara itu `array-status` memaparkan `state: recovering (...)` dan `last_error` memberitahu kamera mana yang ia tunggu; selepas itu ia mencetak `re-opened:`. Kamera yang di-restart akan kembali dengan PTP dimatikan, jadi sehingga ia dibuka semula `ptp_enabled` adalah `False` dan `ptp_off:` menamakannya.

> **Kamera yang dipegang tidak akan dibuka buat kali kedua.** Pembukaan kedua menghentikan aliran pemegang dan menulis semula tetapan, dan boleh menyebabkan backend terhenti. Jadi pada kamera yang dipegang oleh pool (GUI atau `cam-connect`), ahli tatasusunan, atau kamera yang dipegang oleh kerja latar belakang (`lattice continuous` yang sedang berjalan, `multi-stream` atau `align-stream`, miliknya sendiri atau pelanggan lain), `lattice continuous`, `lattice capture`, `cam-connect`, `power --sleep`, `calibrate`, a one-shot `white-balance` dan `presets --apply` menolak dengan HTTP 409 `CAMERA_HELD`, menamakan pemegang dan arahan pelepasan (`chloros-cli lattice cam-disconnect -s <SN>`, `chloros-cli lattice array-disconnect --array-id <ID>`, atau untuk sesuatu kerja, ID-nya dan siapa yang memulakannya), dan keluar `1`. Demikian juga kamera yang sedang digunakan oleh arahan lain sekarang (`multi-test` atau `multi-capture` yang membuka kameranya, satu-shot lain), untuk setiap arahan di sini: ralat mengatakan untuk menunggu arahan itu selesai. `lattice power` tanpa `--sleep` membaca melalui pemegang pemegang itu sendiri di mana ia wujud (kolam, tatasusunan, kamera terbuka sesuatu tugas), dan ditolak untuk kamera yang sedang digunakan oleh arahan lain. Setiap balasan `CAMERA_HELD` membawa kod dalam kedua-dua `code` dan `error_code`. Untuk arahan yang membuka setiap kamera, lihat [Multi-Kamera (Sesi Sementara)](#multi-kamera-sesi-sekejap).

##### Pilihan `array-connect`

| Bendera | Lalai | Keterangan |
| --- | --- | --- |
| `--serials SN1,SN2,…` | auto-discover semua kamera LATTICE (perlu ≥2) | Siri pertama adalah MASTER. Apabila diabaikan, penemuan akan menapis kepada model LATTICE (M3C / M3M) dan menyambungkan kesemuanya. |
| `--line {Line0,Line2,Line3}` | `Line2` | GPIO sync line. |
| `--target-fps F` | auto | Kadar picu pemicu Utama. |
| `--force-tier {sim-capture-sim-emit, sim-capture-ftd-stagger, slip-emit-and-capture}` | auto | Ungguli pemilih peringkat. |
| `--wire-ceiling-mbps MB_PER_S` | dikesan secara automatik | **Belanjawan wayar berterusan hos, dalam MB/s — nilai yang menjadi asas untuk keseluruhan peruntukan tatasusunan.** Kurangkannya apabila susunan melaporkan bingkai GVSP yang rosak: nilai auto diperoleh daripada kadar pautan yang diiklankan oleh NIC, yang melebih-lebihkan penyesuai USB, laluan PCIe nipis dan fabrik kongsi yang sibuk. Disimpan dalam blok tangkapan susunan projek, jadi membuka semula / CLI / menyambung semula SDK memulihkannya. Lihat [Kesihatan Array](#array-health--which-subsystem-is-losing-frames). |
| `--binning {1,2,4}` | tiada (penuh sensor) | Pembingungan perkakasan (sentiasa Purata). |
| `--width N` / `--height N` / `--pixel-format FMT` | penuh sensor; format per-kamera | Saiz bingkai atau format piksel eksplisit untuk setiap kamera, bukannya pilihan format penuh sensor dan per-kamera (Mono12 / BayerRG12). |
| `--no-recommend` | off | Langkau langkah analisis rangkaian (setiap kamera kemudian mengekalkan saiz semasa tanpa ditetapkan kepada sensor penuh). |
| `--no-ptp` | off | Nyahdayakan PTP (cap masa merentas kamera kemudian **tidak** boleh dibandingkan). |
| `--packed {auto,on,off}` | `auto` | Alirkan bingkai 12-bit yang dipadatkan danungkai pada komputer ini — lebih banyak bingkai sesaat pada sensor penuh, data yang sama. `auto` hanya memadatkan apabila ia membantu dan komputer ini mampu melakukannya; `off` tidak pernah memampatkan. Lihat [Penghantaran 12-bit Terpakat](#packed-12-bit-transport---packed). |
| `--packet-size {auto,jumbo,standard,N}` | `auto` | saiz paket GVSP untuk setiap kamera dalam susunan. `auto` membenarkan setiap kamera menyiasat rangkaian; `jumbo` = 9000, `standard` = 1500, atau saiz dalam bait menetapkannya. Sebelum 1.3.0 `array-connect` menerima penanda ini dan mengabaikannya. |

> **Sensor penuh secara lalai.** Tanpa `--width` / `--height` / `--binning` /
> `--pixel-format`, `array-connect` menetapkan setiap kamera ke sensor penuh — tiada
> binning, tiada pemotongan ROI — dan membiarkan kadar bingkai jatuh mengikut apa yang dihantar oleh komputer dan rangkaian, bukannya mengecilkan bingkai untuk mengekalkan kadar. Baris analisis rangkaian menunjukkan kadar yang dijangka, dan baris seterusnya menyenaraikan pilihan yang lebih pantas (binning atau ROI yang lebih kecil) dengan kadar yang dijangka masing-masing; pass
> `--binning` atau `--width`/`--height` untuk memilih satu. Connect tidak pernah membin atau memotong
> secara automatik.

##### Penghantaran 12-bit Terpek (`--packed`)

Pada sensor penuh, kadar bingkai tatasusunan ditentukan oleh kelajuan setiap kamera menghantar bingkai-bingainya. Format 12-bit (`BayerRG12`, `Mono12`) biasanya dihantar sebagai perkataan 16-bit; penghantaran **terpadat** menghantar 12 bit yang sama dalam 1.5 bait bagi setiap piksel dan
dan membongkarnya pada komputer ini, jadi setiap bingkai mengambil masa 25% lebih sedikit untuk dihantar — kira-kira **33% lebih banyak bingkai sesaat pada sensor penuh**(diukur: 10.7 → 14.0 fps pada susunan 9 kamera).**Data yang disimpan adalah sama persis**: nilai yang sama, fail 16-bit yang sama, metadata yang sama.

- `auto` (laluan lalai) hanya memadatkan apabila ia membantu **dan** komputer ini mampu
  membongkar. Semasa sambungan, backend mengukur kelajuan membuka pek sendiri, bilangan CPU, beban CPU semasa dan bajet rangkaian, jadi desktop dengan teras lebihan akan mengepek pada kelajuan penuh sensor, manakala komputer yang sibuk atau berkuasa rendah akan menstrim tanpa membuka pek. ROI kecil dan bingkai yang disatukan mendapat
  sedikit.
- `off` tidak pernah mengepak — gunakan ia untuk menolak pengepakkan semasa mendiagnosis, atau pada
  komputer yang perlu menggunakan semua CPU-nya untuk kerja lain.
- `on` sentiasa memampatkan format 12-bit.

Keluaran connect memaparkan keputusan dan sebabnya (`packed 12-bit: on (auto: dijangka 11.0 -> 14.4 fps, …)`); `/api/camera/array/laporan status
`packed_transport` dan `packed_decision`. Untuk menetapkan lalai bagi seluruh mesin
daripada menghantar penanda setiap kali, tetapkan `CHLOROS_ARRAY_PACKED12=off` (atau `on`) dalam persekitaran backend — `--packed on|off` yang eksplisit masih diutamakan.
Format 8-bit dan 16-bit tidak pernah dipadatkan.

### Satu rekod projek, setiap mod

Satu folder projek mengandungi tetapan setiap peranti: `cameras.json` — setiap kamera (`cameras[].settings`, untuk satu kamera dan untuk setiap ahli tatasusunan) dan tetapan merentas tatasusunan (`arrays[].capture`: format, ROI, binning, kadar pencetus, bajet wayar) — dan `sensors.json`, setiap penderia cahaya (masa integrasi, purata bingkai, cap). Aplikasi, CLI, SDK dan pelayan MCP semua membaca dan menulis fail yang sama, dengan cara yang sama:

- **Peranti yang disambungkan di bawah projek disimpan ke dalamnya dan merekod setiap perubahan ke dalamnya.** "Di bawah projek" bermaksud dengan projek dibuka di backend (aplikasi), atau — daripada SDK — melalui `ChlorosProject`, yang menamakan folder tersendiri. Kamera, array atau penderia cahaya yang belum disimpan dalam projek akan ditambah ke dalam failnya apabila ia disambungkan (`lattice cam-connect`, `array-connect`, `daq pool-connect`, aplikasi, SDK, `chloros-mcp`), dengan tetapan masing-masing. Perubahan daripada mana-mana mod (`lattice exposure`, `gain`, `configure --set`, `resolution`, satu susunantetapan dan kadar pencetus, penutup penderia cahaya, kawalan aplikasi) disimpan ke dalam fail projek itu beberapa ketika kemudian. Pendedahan, penguatan atau sasaran AE yang ditetapkan pada kamera disimpan sebagai 'held', jadi membukanya semula akan menetapkan semula ia.
- **Membuka projek menetapkan peranti daripadanya**, dalam setiap mod: kamera, susunan atau penderia cahaya yang disambungkan tanpa tetapan sendiri akan mendapat tetapan yang disimpan oleh projek (`lattice cam-connect` / `array-connect` apabila projek dibuka, `chloros-cli project connect`, `open_project().connect_all()`, buka semula aplikasi). Susunan yang disambungkan di bawah id baru mengemas kini susunan yang disimpan oleh projek dengan kamera yang sama; susunan dengan kamera lain ditambah di sebelahnya.
- **Simpanan aplikasi sendiri tidak pernah menimpa tetapan langsung peranti** dengan salinan lama miliknya, dan mengekalkan keutamaan bagi setiap ahli yang ditetapkan oleh pemanggil CLI atau SDK.
- **Format piksel yang anda tetapkan adalah format yang disimpan oleh projek.** Format yang ditetapkan dalam Tetapan Susunan, oleh `--pixel-format` atau oleh `connect_array(pixel_format=)`, termasuk 8-bit, disimpan dan disambung semula dengan tepat. Jika tiada ditetapkan, setiap kamera akan bersambung pada lalai 12-bitnya (`BayerRG12`, `Mono12` pada sensor mono). Nama format menunjukkan kedalaman bitnya; keluarga adalah milik sensor, jadi kamera berwarna tidak akan ditetapkan kepada format Mono atau kamera mono kepada format Bayer, dan baris `[PixelFormat <serial>] ... written` akan memberitahu demikian apabila format telah dipetakan. Satu 8- Eksport tangkapan 8-bit adalah 8-bit, termasuk pantulan: diskala semula dan ditandakan dengan `PixelScale`.
- Peranti yang disambungkan tanpa projek tidak merekod apa-apa.
- **Rakaman mengikuti projek yang dibuat selepasnya; membuka meninggalkannya dengan projeknya.**Apabila sebuah projek dibuat — sama ada oleh SDK `create_project()`, `chloros-mcp`'s `create_project`, atau**Projek Baru**aplikasi — atau disalin (fungsi**Duplikat**aplikasi, yang membuka salinan) sementara rakaman masih menulis ke dalam projek yang terbuka sebelum ini, rakaman itu akan terus berjalan, dan apa yang ditulisnya akhirnya akan berada dalam projek baru, di tempat yang sama di dalamnya (di bawah). Membuka projek sedia ada — `chloros-cli process`, `project devices --reconnect`, `ChlorosProject.process()`,**Buka Projek** aplikasi — membiarkan rakaman masih berjalan dengan projek yang dirakamnya: ia selesai di situ, dan fail-failnya menyertai senarai fail projek itu (aplikasi menghentikan setiap rakaman sebelum ia membuka projek).
  - `array-record` dan `array-burst`: video itu, atau folder burst dengan setiap bingkai, dipindahkan ke sana sepenuhnya apabila rakaman berhenti (`--duration`, Ctrl+C). `Burst dir` yang dicetaknya ialah yang baru, dan `--build` serta `array-build-video` berfungsi daripadanya. Pergerakan yang ditolak oleh pemacu meninggalkan rakaman di tempat ia ditulis; pemulanganlog penghujung mencatatkan sebabnya, begitu juga respons HTTP hentian (`relocation_error`). Bingkai mentah burst yang dihentikan dan `.daq` disertakan dalam senarai fail projek yang menjadi tujuannya; video (`array-record`, video yang dibina daripada burst) tidak ditambah ke dalam senarai fail projek.
  - `array-capture --continuous`: setiap tangkapan selepas perubahan disimpan ke dalam projek baru; yang disimpan sebelum itu kekal di tempatnya.
  - `daq pool-record` dan `project sensor log` (dan rakaman sensor cahaya aplikasi itu sendiri): `.daq` dipindahkan ke dalam projek barufolder `light_sensor` apabila rakaman berhenti, dan menyertai senarai fail projek itu.

  Rakaman ke dalam folder di luar projek yang terbuka (sebuah `-o` di tempat lain) tetap di situ, dan `lattice continuous` serta `multi-stream` mengekalkan folder yang telah diberikan kepada mereka.
- **Dalam aplikasi, rakaman selepas projek baru dibuat akan dimasukkan ke dalamnya.** Membuat atau menduplikasi projek mengekalkan setiap rakaman yang sedang dijalankan dan penyuapan, dan setiap rakaman yang diambil selepas itu (Rakam Semua, Mulakan Rakaman) disimpan ke dalam projek baru dan dimasukkan ke dalam senarai failnya. Membuka projek sedia ada dalam aplikasi akan menghentikan kesemuanya terlebih dahulu.
- **Projek tidak dipindahkan semasa ia merakam.**Memindahkan projek yang terbuka ke direktori kerja lain (**Tetapan → Direktori Kerja** aplikasi; tiada arahan CLI yang memindahkan projek, (klien HTTP yang menghantar ke `/api/move-project` melakukannya) ditolak dengan HTTP 409 `PROJECT_RECORDING` semasa rakaman tatasusunan atau burst, tangkapan berterusan tatasusunan, kerja `lattice continuous` / `multi-stream` atau rakaman penderia cahaya menulis ke dalamnya: `Project '<name>' is recording (<what>), so moving the project now is refused. Stop the recording, then try again.` Aplikasi itu menolak dengan cara yang sama, sebelum ia meminta folder, sementara Capture All, Start Capturing atau rakaman sensor cahaya miliknya sedang berjalan.

Ini berasingan daripada apa yang ditetapkan pada kamera apabila disambung semula (lihat [Memori kamera](#camera-memory----keep-settings----clear-settings)): setiap projek menetapkan kameranya sendiri apabila ia dibuka.

### Memori kamera — `--keep-settings` / `--clear-settings`

Apabila satu array disambungkan, kameranya mengambil tetapan array tersebut, dan anda kemudian boleh mengubahnya. Apabila Chloros melepaskan kamera — `array-disconnect`, penghujung arahan sementara (`multi-capture` tanpa sebarang susunan, `multi-stream`, `multi-test`, `align-calibrate`, `align-apply`, `align-stream`, `index --live`), perhentian `lattice continuous`, penghujung `lattice capture` pada kamera yang tidak dipegangnya — ia memulangkan setiap satu seperti sedia kala sebelum sambungan: pemasangan pendikit dan talian penyegerakan, tetapan PTP dan kamera (senarai penuh terdapat di bawah [Multi-Kamera (Sesi Sementara)](#multi-camera-transient-sessions)).

Untuk menggunakan kamera di luar projek, dua penanda pada mana-mana arahan tersebut menulis **memori kamera itu sendiri** — apa yang dihidupkannya, dalam mana-mana perisian:

- **`--keep-settings`** menyimpan tetapan setiap kamera seperti yang ditinggalkan sesi ke dalam memorinya (UserSet2, dijadikan set but). Kamera-kamera itu masih dibiarkan berjalan bebas (`TriggerMode` `Off`), dengan pendawaian talian penyegerakan seperti sedia kala. Kamera yang tiada UserSet2 menyimpan ke dalam UserSet1; komputer ini mengingatnya, jadi susunan Chloros akan pulih sendiri — yang menetapkan semula UserSet1 yang telah menyimpang daripada garis dasar Chloros — membiarkannya begitu sahaja.
- **`--clear-settings`** menetapkan semula memori setiap kamera kepada tetapan kilang: garis dasar Chloros, yang dipanggang oleh penyembuhan kendiri arainya — tetapan lalai kilang dengan `TriggerMode` `Off`, `TriggerSource` `Line2`, pendedahan automatik, imbangan putih kamera RGB yang dikalibrasi dikekalkan — dalam UserSet1, dijadikan set but.
- Kedua-duanya tidak boleh digabungkan. Setiap kamera mendapat satu baris: `SN:…: settings saved to the camera's memory (UserSet2); it powers up with them.` atau `SN:…: camera memory reset to the factory settings.`; balasan `camera_memory` mempunyai nilai yang sama bagi setiap kamera.

Aplikasi ini tidak menawarkan penanda aras ini.

**Setiap jalan keluar memulangkan kamera ke keadaan asal.**Proses pemulihan yang sama seperti asal dijalankan pada setiap laluan keluar, bukan hanya pada arahan di atas: `cam-disconnect` (dan**X**kamera aplikasi);**X** aplikasi pada satu tatasusunan atau pada baris mana-mana ahli tatasusunan (tatasusunan tidak boleh dijalankan tanpa ahli, jadi keseluruhan tatasusunan dibebaskan); menutup aplikasi (ia memberi backend sehingga 20 saat untuk mengembalikan setiap kamera, susunan dan menjalankan `lattice continuous` kembali sebelum ia menghentikannya; satu arahan yang masih berjalan kemudian — arahan sementara yang membuka kameranya atau menunggu jam PTP mereka, `lattice capture` sekali sahaja, `power`, `white-balance` atau `presets --apply` — berhenti pada titik selamat seterusnya, mengembalikan kameranya terlebih dahulu, selama sehingga 8 s, dan menjawab HTTP 503 `SHUTTING_DOWN`; satu susunan connect yang masih berjalan — `lattice array-connect`, connect aplikasi — berhenti sebelum kamera seterusnya dan menutup semula kamera yang telah dibuka, selama sehingga 10 saat, dan menjawab HTTP 503 `SHUTTING_DOWN`; dan satu yang tiba semasa penutupan tidak membuka apa-apa: HTTP 503 `SHUTTING_DOWN`); dan arahan CLI yang dibunuh atau terminalnya ditutup (SIGTERM / SIGHUP menghentikan kerjanya — dan memulangkan kameranya — sebelum ia keluar `143` / `129`, seperti Ctrl+C). `lattice continuous`, `multi-stream` atau `align-stream` yang dibunuh terus (SIGKILL), yang tidak dapat menghentikan apa-apa, kerjanya dihentikan dan kameranya dikembalikan oleh backend 30 s selepas pemeriksaan terakhirnya. Backend yang terhenti atau dibunuh secara paksa tidak dapat memulihkan apa-apa; kitaran kuasa mengembalikan setiap kamera ke memorinya, dan penyambungan Chloros seterusnya menyambung semula kamera yang dijumpainya dalam keadaan bersedia.**Projek menetapkan kameranya sendiri.** Apabila satu susunan disambungkan dengan projek yang sedang dibuka, setiap ahlinya akan mendapat tetapan yang disimpan oleh projek untuknya — satu pendedahan, penguatan atau sasaran AE yang disimpan padanya (disimpan sebagai tetapan anda, jadi pendedahan automatik tidak akan mengubahnya) dan mod imbangan putihnya (pembetulan Chloros; imbangan putih kamera sendiri kekal dimatikan) — apa sahaja yang tersimpan dalam memori kamera.

### Smart-AE / Smart-Capture

Susunan LATTICE menjalankan AE berterusan di latar belakang sebaik sahaja ia disambungkan, tetapi babak yang baru diarahkan mengambil masa sedikit untuk mencapai persetujuan. `array-capture --smart` ialah **kemudahan berbalut**: ia menunggu AE menstabilkan pada setiap kamera dalam susunan, kemudian mencetuskan tangkapan. Gunakan apabila anda menukar pemandangan di tengah-tengah sesi.

```bash
# Connect once, then take settled captures whenever you re-point the rig
chloros-cli lattice array-connect --serials SN1,SN2,SN3,SN4
chloros-cli lattice array-capture --smart --processing reflectance -o pose_a/
# (move the rig)
chloros-cli lattice array-capture --smart --processing reflectance -o pose_b/
```

Dasar penstabilan adalah konservatif secara lalai: had masa tamat 5 saat, tetingkap kestabilan 1.5 saat, toleransi penyebaran pendedahan ±5 %. Laras melalui SDK (`ArrayHandle.capture_smart(settle_timeout_s=…, stability_window_s=…, exposure_tolerance_pct=…)`) jika anda memerlukan tingkah laku yang berbeza daripada automasi.

#### Cara pendedahan automatik berfungsi

- **Pendedahan dahulu, penguatan terakhir.** Dengan penguatan pada Auto, penguatan kekal pada 0 dB sehingga pendedahan mencapai nilai terpanjang yang dibenarkan.
- **Pendedahan manual mengekalkan penguatan pada Auto** sehingga anda menukar penguatan atau menetapkannya kepada manual. Apabila kembali ke Auto, had penguatan adalah seperti sebelum ini.
- **Sorotan kecil tidak menggelapkan adegan.** Refleksi cermin sehingga 1% daripada bingkai diabaikan, dan pada panel sasaran penentukuran yang diketahui, sehingga 0.5% daripada panel.
- **Had pendedahan DAQ hanya terpakai semasa sasaran penentukuran kelihatan.** Ia mengelakkan panel putih daripada dipotong; apabila tiada panel kelihatan, kamera akan menyesuaikan pendedahan mengikut adegan.
- **Cahaya yang konsisten kekal stabil, dan cahaya yang berubah akan menstabil dalam beberapa saat.** Pada cahaya yang konsisten, pendedahan dan hadnya kekal tidak berubah, dan selepas perubahan cahaya yang besar, pemandangan akan menstabil dalam beberapa saat. Rakaman klip tidak lagi mengehadkan pendedahan secara kekal, dan perubahan ROI akan mencari semula pendedahan.
- **Sasaran kecerahan kekal dalam julat.** Ia tidak meningkat selagi kamera berada pada had pendedahan dan penguatannya tidak dapat meningkat, jadi cahaya yang kembali tidak akan menyebabkan silau pada bingkai.
- **Kamera yang pengecaman automatiknya berhenti bertindak balas akan dikekalkan pada tahap untuk cahaya yang ada.** Di bawah cahaya matahari yang terang, kamera boleh jatuh ke pengecaman terpendeknya dan kekal di situ dengan bingkai yang gelap, tetapan semuanya menunjukkan normal. Chloros memerhati bingkai, mengekalkan kamera pada tahap yang disimpannya untuk cahaya tersebut, dan melepaskannya apabila cahaya berubah. Sensor cahaya yang gelap atau ditutup tidak mengganggu perkara ini.

### Tahap Eksport Rakaman (default `all`)

Mulai keluaran ini, `lattice capture`, `lattice multi-capture`, dan `lattice array-capture` secara lalai menggunakan `--processing all`** — satu fail yang disimpan bagi setiap jenis eksport yang terpakai pada setiap kamera, sepadan dengan tingkah laku "Tangkap Semua" GUI. Tahap-tahapnya adalah:

| Tahap | Output | Terpakai kepada |
| --- | --- | --- |
| `raw` | Bayer saluran tunggal (kamera monokrom: jalur tunggal) terus dari sensor. | Semua kamera. |
| `debayered` | Warna 3-saluran, disimpan dalam susunan saluran **RGB**(satu TIFF dengan tafsiran fotometrik RGB; PNG/JPEG juga dibuka sebagai RGB): offset gelap pek penentukuran (DSNU) diambil daripada imej mentah, kemudian tepi OpenCV-demosaik sedar — tiada LUT lineariti, medan rata atau pencampuran semula (kamera mono: skala kelabu 1-saluran). Rangka debayered dalam memori yang dikembalikan oleh SDK Python (`grab()`, `frame_stream()`) adalah**BGR**, susunan OpenCV; hanya fail yang disimpan adalah RGB. | Semua kamera. |
| `radiance` | float32 W/m²/sr/nm melalui rantaian radiometrik penuh. | Multispektral (M3C/M3M) sahaja — **dilangkau untuk kamera penapis RGB. |
| `reflectance` | uint16 ρ (`32768` = 1.0), sedia untuk Pix4D. | Multispektral sahaja, dan **hanya apabila DAQ diikat + kamera telah dikalibrasi**; jika tidak, diabaikan. |
| `display` | Rantaian pratonton GUI penuh (CCM + WB + gamma mengikut profil kamera). Disimpan dalam folder `preview/`. `lattice capture` juga menerima nama `preview` untuknya; `array-capture` / `multi-capture` hanya mengambil `display`. | Semua kamera. |

Serahkan satu aras untuk menyimpan hanya aras itu (`--processing debayered`). Apabila anda meminta `all`, aras yang tidak terpakai pada kamera tertentu akan dilangkau (dan dilaporkan), bukan dikesan sebagai ralat — kamera yang tidak bersambung atau belum dikalibrasi masih menerima `raw` / `debayered` / `preview`.

#### Bacaan DAQ yang menyimpan tangkapan

Setiap kali DAQ diikat pada kamera atau tatasusunan, atau dikumpulkan, bacaan downwelling yang digunakan untuk tangkapan ditulis ke dalam **`.daq`** fail dalam subfolder `daq/` dalam folder captures, dengan `.match.json` (sejauh mana bacaan itu daripada pendedahan) dan `.cameras.json`-nya (yang mana bacaannya), dan dilaporkan pada baris `DAQ:`. Keadaan ini terpakai untuk setiap tahap, termasuk tangkapan mentah atau tangkapan yang telah menjalani debayering, jadi mana-mana tangkapan boleh diproses semula menjadi radiasi, pantulan atau indeks kemudian. Ia adalah fail yang sama, dengan nama dan fail sidecar yang sama, sama ada tangkapan itu datang daripada GUI, CLI atau SDK. Tanpa sebarang bacaan DAQ langsung, tiada `.daq`, dan `multi-capture` tanpa tatasusunan yang disambungkan (backup sementara) tidak menyimpan apa-apa.

| Penanda | Kesan |
| --- | --- |
| `--no-daq` | Jangan simpan bacaan pada tangkapan yang tahapnya tidak menggunakannya (`raw`, `debayered`). Tahap `radiance`, `reflectance` atau `display` masih menulisnya, kerana pengimportan semula memerlukan bacaan yang digunakan untuk menghasilkan produk tersebut. |
| `--daq-tolerance-ms MS` | Sejauh mana bacaan boleh berbeza daripada pendedahan dan masih dikira dalam toleransi (lalai 500). Hanya keputusan yang berubah (`.match.json`'s `in_tolerance` dan `tolerance_ms`, `DlsInTolerance` bagi reflektansi), bukan bacaan yang digunakan. Lihat [Satu Bacaan DAQ Jauh Dari Pendedahan](#a-daq-reading-far-from-the-exposure). |
| `--force-daq` | Cara lama untuk meminta `.daq` pada tangkapan hanya mentah. Ia kini lalai, dan `lattice capture` dan `array-capture` masih menerima penanda itu (`multi-capture` tidak). `--no-daq` mengatasi ia. |

DAQ mana: yang diikat pada kamera atau tatasusunan (pilihan penderia cahaya dalam GUI, ikatan yang disimpan dalam projek, atau `chloros-cli daq pool-bind`), jika tidak, yang paling baru yang dimiliki oleh backend. Letusan mentah (`array-burst`) mengekalkan `.daq` sendiri bagi setiap bacaan berasingan di bawah folder burst seperti sebelum ini.

**PNG dan JPEG menyimpan 8 bit setiap saluran, jadi mereka hanya mengambil `display` / `preview` dan produk indeks sahaja.** `--format png` atau `jpg` dengan tahap yang tidak dapat disokong akan ditolak sebelum apa-apa dirakam, menamakan tahap tersebut dan TIFF sebagai format yang menyokongnya:

- `radiance` (float32) dan `reflectance` (uint16, `32768` = 1.0) — sentiasa, jadi `--processing all` (lalai) dengan `--format png`/`jpg` juga ditolak: gunakan `--format tiff`, atau `--levels display`.
- `raw` dan `debayered` daripada kamera yang menyiarkan lebih daripada 8 bit (kerana bingkai 12-bit M3C / Mono12 M3M) — CLI tidak dapat melihat kedalaman bit kamera, jadi backend menolak permintaan ini (HTTP 400). Yang tunggal-frame `lattice capture --processing debayered` mengambil bingkai 8-bit untuk PNG/JPEG dan tidak ditolak, begitu juga `multi-capture` tanpa array berterusan (debayer 8-bit).

Setiap laluan tangkapan menjawab dengan cara yang sama (`error_code` `FORMAT_CANNOT_HOLD_LEVEL`): `lattice capture`, `multi-capture`, `array-capture`, `project capture` / `burst`, dan laluan HTTPdi belakangnya.

### Rupa folder tangkapan

Setiap jenis eksport diletakkan dalam **subfolder tersendiri**di bawah `-o`, jadi tangkapan berbilang-tingkat tidak pernah mencampurkan jenis. Skit ini menunjukkan**rekod tangkapan `all` lalai bagi satu kamera oleh `lattice capture`** — beberapa peringkat bagi setiap kamera, jadi setiap fail mempunyai sambungan `_<level>`:

```
output/
├── raw/           capture_<ts>_SN<serial>-<filter>_raw.tiff
├── debayered/     capture_<ts>_SN<serial>-<filter>_debayered.tiff
├── radiance/      capture_<ts>_SN<serial>-<filter>_radiance.tiff
├── reflectance/   capture_<ts>_SN<serial>-<filter>_reflectance.tiff
├── preview/       capture_<ts>_SN<serial>-<filter>_display.tiff
├── index/         per-camera vegetation-index (LUT) render, when --index is on
├── composite/     array foreground/background live-view composite, when produced
├── clamp_masks/   radiance/…_radiance_clampmask.tif, reflectance/…, index/… — the unmix clamp
│                  masks, one subfolder per product folder, only when a band clamped
└── daq/           daq_data_<sensor id>_<ts>.daq: the downwelling reading matched to the capture
                   (+ .daq.cameras.json: whose)
```

Bagaimana nama fail berbeza:

- **Medan kamera — siri, kemudian penapis:** setiap fail tangkapan dinamakan menurut kamera yang mengambilnya sebagai `SN<serial>-<filter>`, di mana `<filter>` ialah bahagian penapis model kamera: `SN261902142-F450` untuk `M3M-L87-F450`, `SN213800234-FRGN` untuk `M3C-L87-FRGN`, `SN214701292-FRGB` untuk kamera RGB. Penapis menyambungkan nombor siri dengan `-`, bukan `_`, jadi nama mempunyai medan yang dipisahkan oleh `_` yang sama seperti sebelum ini (awalan, tarikh, masa, milisaat, kamera, tahap). Kamera yang modelnya tidak dapat dibaca (DeviceUserID yang dipadam) dinamakan `SN<serial>` sahaja. Rakaman daripada keluaran terdahulu tidak mempunyai penapis dalam nama; Chloros membaca kedua-duanya.
- **Awalan:** `lattice capture` menamakan fail `capture_…`; `array-capture`, `multi-capture` dengan susunan yang disambungkan, dan `project capture` / `burst` / `stream` bagi nama tatasusunan menamakannya `sync_<ts>_SN<serial>-<filter>…`.
- **Aksara akhir aras hanya dengan beberapa aras.**Kamera yang menyimpan**beberapa**tahap dalam satu tangkapan (`all`, atau `--levels a,b`) menambah `_<level>`. Kamera yang menyimpan**satu** tahap mengekalkan nama asal, tanpa akhiran — `--processing reflectance` menulis `reflectance/sync_<ts>_SN<serial>-<filter>.tiff` — supaya skrip yang ditulis untuk tangkapan satu-tahap terus berfungsi. Peringkat masih diberikan oleh folder.
- **Topeng Clamp mengikuti nama produk:** `<product name>_clampmask.tif`. Rakaman pantulan satu aras menulis `clamp_masks/reflectance/sync_<ts>_SN<serial>-<filter>_clampmask.tif`; rakaman `all` menulis `clamp_masks/reflectance/sync_<ts>_SN<serial>-<filter>_reflectance_clampmask.tif`.
- **Panjangan** mengikuti `--format`: `tiff` lalai menghasilkan `.tiff`, `--format tif` menghasilkan `.tif` (topeng clamp sentiasa `.tif`).
- **Satu pengecualian:** `lattice capture --processing debayered` (atau `--levels debayered`) mengambil satu fast single- laluan bingkai dan menulis `capture_<ts>_SN<serial>-<filter>.tiff` terus ke dalam `-o` — tiada subfolder.
- **Aliran dan letupan** membawa medan yang sama: `lattice continuous` menulis `frame_<seq>_SN<serial>-<filter>.tiff`, `lattice multi-stream` `stream_<seq>_SN<serial>-<filter>_<frame id>.tiff`, dan burst mentah `<burst>_f<frame>_SN<serial>-<filter>_raw.tiff`.

`<ts>` ialah cap masa tangkapan dan `<serial>` ialah siri kamera, jadi satu kumpulan diselaraskan berkongsi cap masa merentas kamera. **Nota satu ketidaksymetri:** tahap `display` disimpan dalam folder
yang dinamakan `preview/` manakala fail berbilang-tingkat mengekalkan `_display` dalam nama — folder dan suku kata berbeza untuk aras itu sahaja. Aras yang tidak diketahui akan menggunakan folder dengan nama mereka sendiri, dan jika subfolder tidak dapat dibuat, fail itu akan ditulis ke akar keluaran dan bukannya hilang.

**Pemprosesan semula folder rakaman:**arah `chloros-cli process` ke pada**akar captures** (`output/`). `process` biasanya hanya mengimport folder yang anda namakan, tetapi apabila folder itu tidak mengandungi apa-apa
imej dan mempunyai subfolder, ia akan menuruni secara automatik — jadi subfolder aras dan subfolder `daq/` semua diambil sekaligus. Setiap aras tangkapan diimport sebagai satu imej dengan aras lain tersedia sebagai mod, bukannya satu imej bagi setiap aras.

Menamakan **subfolder aras** secara langsung (contohnya `output/raw/`) juga berfungsi. Melakukannya meninggalkan `daq/`
di belakang, jadi salin atau tunjukkan bacaan DAQ di sebelahnya apabila anda re-memperoleh produk radiometrik daripada `raw/` — jika tidak, padanan cap masa tidak mempunyai apa-apa untuk dirujuk.

**Pemprosesan sentiasa bermula daripada `raw`.** Dalam setiap tangkapan, bingkai mentah adalah sumber saluran;
`debayered`, `radiance`, `reflectance` dan `preview` disediakan sebagai mod tontonan tetapi tidak pernah dimasukkan semula melalui saluran paip. Memproses semula produk terbitan akan menerapkan semula vignette, CCM dan matematik radiasi yang sudah terbina dalam pikselnya, jadi Chloros menolaknya daripada memproses dua kali. Dua akibat yang perlu diketahui:

- Render `index/` dan `composite/` **tidak pernah** diproses. Keluaran ini, bukan rakaman — render LUT NDVI tidak mempunyai tafsiran sinaran yang bermakna.
- Folder rakaman yang dieksport **tanpa** `raw` (contohnya `array-capture --processing reflectance`) mempunyai
  tiada sumber saluran yang sah. Rakaman-rakaman itu diimport dan dipaparkan dengan normal, tetapi `process` melangkau
  mereka dan memberitahu demikian:

  ```
  [IMPORT-LEVEL] Skipping 4 already-processed file(s) with no raw source: capture_…_reflectance.tif
  [IMPORT-LEVEL] Processing starts from raw. Re-capture with --processing raw, or force an entry
                 point with --input-level.
  ```

  Jika anda benar-benar perlu menghantar produk terbitan — sesi hab yang dirakam dengan
  `demosaic` pada, atau folder warisan — `--input-level {raw,debayered,processed}` memaksa titik kemasukan
  dan menimpa arahan langkau. Penanda itu adalah pintu kecemasan yang disengajakan; `auto` (lalai)
  tidak pernah memproses tangkapan yang tiada raw.

**Tag penentukuran: hanya pada `raw`.** Fail `raw` bagi tangkapan membawa pek kalibrasi kamera: muatan `CalibrationJSON` dan tag yang serasi dengan MicaSense (`BlackLevel`, `RadiometricCalibration`, `Band{R,G,B}RadiometricCalibration`, `MicaSenseVignetting*`), yang menerangkan nilai mentah sensor. Setiap fail aras lain (`debayered`, `display`, `radiance`, `reflectance`, render `index/`) dan setiap `lattice continuous` / kerangka `multi-stream` membawa identiti kamera (`XMP-mapir:CameraSerial`, `CameraModel`, `FilterType`, `LensModel`, `CalBundleSha256`, ...) dan rekod bingkai itu sendiri, `XMP-mapir:FrameJSON` (pendedahan, penguatan, suhu, format piksel, masa tangkapan, perlawanan DAQ), kerana setiap produk `process` membawa identiti tersebut. `process` mencari penentukuran fail sedemikian melalui nombor siri dan `CalBundleSha256` dalam cache penentukuran Chloros, memuat turun pakej itu sekali jika ini komputer tidak memilikinya.

#### Fail yang dibuat daripada beberapa kamera

Sebuah fail yang dibuat daripada jalur lebih daripada satu kamera — satu susunan berbilang jalur (`align-apply`, `align-stream`, `index --live --save-multiband`), indeks langsung yang dibuat daripada beberapa kamera (`index.tif`, `index_LUT.png`), imej `composite/` tangkapan tatasusunan — menamakan setiap kamera yang menyumbang kepadanya:

- **Senarai bagi setiap kamera, mengikut susunan jalur** (gabungan: lapisan latar belakang, kemudian latar hadapan; lapisan indeks, kamera-kamera yang menjadi sumber jalur-jalurnya): `XMP-mapir:SourceCameraSerials`, `SourceCameraModels`, `SourceSensorProfiles`, `SourceProductLines`, `SourceFilterTypes`, `SourceLensModels`, `SourceCalDateUTCs`, `SourceCalBundleSha256s` (paket penentukuran yang dilalui oleh jalur setiap kamera, jika tidak — satu susunan `align-apply` / `align-stream`, yang jalurnya tidak melalui mana-mana — pek kamera Chloros telah disimpan, yang dinamakan oleh fail tangkapan kamera itu), `SourceLightboxCertIds`, `SourceHalogenLampCertIds`, `SourcePackTiers` — senarai selari, item *n* bagi setiap satu menerangkan kamera yang sama; `-` di mana kamera tiada nilai (tiada pek penentukuran, lensa tidak diketahui). Setiap kamera adalah **satu** item: apabila peknya mempunyai beberapa nilai untuk satu medan — sijil lampu halogen pek — ia digabungkan dengan `;` (`F-1479-1;F-1480-1;F-1481-1`), tidak pernah dengan tanda koma, supaya senarai dibaca sebagai satu item bagi setiap kamera tidak kira bagaimana ia dicetak.
- **EXIF `SerialNumber`** mengandungi siri yang sama, disambungkan dengan koma (`214000535,214000551`).
- Ia tidak membawa sebarang identiti kamera tunggal (`XMP-mapir:CameraSerial`, `CalBundleSha256`, …): yang menamakan satu kamera dan peknya, dan `process` akan menyelesaikan penentukuran fail daripada mereka. Satu indeks yang dikira daripada jalur beberapa kamera (`index.tif`, `index_LUT.png`) mempunyai EXIF `Model` `LATTICE` (kerangka), bukan model `LATT-…` satu kamera.

Sebuah fail jalur **satu** kamera — bagi setiap jalur TIFF (`--per-band`), satu bingkai `align-apply --per-camera`, satu susunan atau indeks langsung bagi satu kamera — membawa tag identiti kamera tersebut sendiri, seperti mana-mana fail tangkapan lain. Susunan `align-apply`, fail setiap jalur dan setiap kamera, dan setiap susunan `align-stream` serta fail setiap jalurnya, membawa masa tangkapan kumpulan mereka (pendedahan kamera rujukan, UTC), dan bingkai `--per-camera` kamera mono adalah satu satah, pada `--bit-depth` seperti susunan.**Orientasi kamera (cermin / putar).** Tetapan orientasi kamera (GUI's Mirror H /
Cermin V / putaran) membentuk apa yang disimpan oleh tangkapan:

- **`raw` dan `debayered` disimpan tepat seperti dibaca oleh sensor**, apa pun tetapan. A
  Mozaik Bayer yang dipusingkan atau dibalikkan akan menjadi corak penapis warna yang berbeza, dan peta kalibrasi (pengeseran gelap, medan rata) berada pada grid sensor itu sendiri.
- **`preview`/`display`, `radiance`, `reflectance` dan render `index/` diorientasikan** — dipusingkan sekali, selepas semua matematik penentukuran, supaya ia dibuka mengikut cara kamera diframing. Mereka
  Fail-fail `clamp_masks/…_clampmask.tif` berada pada grid yang sama.
- **Setiap fail tangkapan merekodkan tetapan** dalam XMP: `XMP-Camera:ChlorosFlipHorizontal` /
  `ChlorosFlipVertical` (`true`/`false`), `ChlorosRotation` (0 / 90 / 180 / 270, mengikut arah jam, selepas
 flips) dan `ChlorosOrientationApplied` — `false` pada imej mentah dan debayered (piksel asli),
  `true` pada produk yang diorientasikan. `ChlorosProcessing` menyenaraikan operasi yang diterapkan pada piksel fail tersebut (`none` apabila asli). Ini termasuk setiap bingkai yang dialirkan oleh `lattice continuous` (tetapan kamera itu sendiri, tidak diterapkan) dan setiap bingkai `lattice multi-stream` (identiti: bingkai anggotanya asli, seperti pada tangkapan tatasusunan). `lattice align-stream` / `align-apply`
  stacks merekodkan tetapan kamera induk, tidak diterapkan: jalur mereka dibengkokkan ke dalam bingkai induk tetapi tidak dipusingkan.
- **`process` menerapkan orientasi yang dirakam** apabila ia memproses imej mentah: offset gelap, medan rata, demosaik dan pencampuran semula dalam koordinat sensor terlebih dahulu, diikuti oleh pemutar selari susunan,
  orientasi terakhir — supaya produk pratonton, sinaran, pantulan dan indeks keluar tepat seperti yang
  dalam tangkapan langsung, dan merekod `ChlorosOrientationApplied=true`. Cadangan lalai tanpa penentukuran
  produk (`Sensor_Response_Images`, `Vignette_Corrected_Images`) disusun mengikut orientasi yang sama, selepas
  medan rata. Produk debayered kekal dalam format asal.
- **Pemapar imej Chloros memaparkan fail mentah atau debayered yang dipusingkan mengikut rekodnya** (pameran sahaja — fail tidak diubah), jadi ia kelihatan seperti produknya; thumbnail, paparan bersaiz penuh, bacaan penuding dan kotak pasir Indeks/LUT semuanya menggunakan pusingan yang sama. Fail tanpa rekod, dan produk (yang sudah diorientasikan), dipaparkan seperti yang disimpan. Pengeksportan sandbox merekodkan orientasi yang dibawa oleh pikselnya (`ChlorosOrientationApplied=true`; identiti untuk lapisan tanpa rekod).
- **Tangkapan tanpa rekod diproses sebagai asli** (tingkah laku tidak berubah). Sebelum 1.3.0, tangkapan satu kamera dengan tetapan flip atau putaran akan menukar fail mentah dan debayerednya sendiri; Chloros tidak dapat membezakan tangkapan sedemikian yang sudah-menukar fail raw daripada satu yang asli dan tidak membuat andaian, jadi ia memprosesnya seperti yang disimpan. Peta kalibrasi berada pada grid sensor itu sendiri, jadi untuk tangkapan seperti ini gunakan satu yang diambil dengan 1.3.0 apabila kalibrasinya penting.
- **Susunan tidak memusingkan mana-mana fail ahli secara lalai** (pemutar balikan penjajaran dan XMP `Alignment*` berada dalam bingkai kamera sendiri yang belum dipusingkan) dan merekodkan identiti. `array-capture --orient` memusingkan produk setiap ahli, selepas pemutarbelakan penyelarasan: mengikut orientasi **utama** apabila ia diputarbelakankan ke dalam keranganya (supaya ahli-ahli kekal selari), jika tidak, mengikut orientasi kamera mereka sendiri. Setiap fail merekod orientasi bingkai di mana pikselnya berada — satu mentah, milik kamera sendiri — dan satu mentah yang membawa transformasi penjajaran juga merekod `ChlorosAlignedFlipHorizontal` /
  `ChlorosAlignedFlipVertical` / `ChlorosAlignedRotation`: milik induk, untuk produk yang diwarp
  ke dalam bingkai induk. `process` memusingkan setiap produk mengikut yang tergolong pada pikselnya:
  yang diselaraskan apabila ia memutarbentuk imej mentah (`--array-alignment`, lalai — yang diselaraskan secara langsung
  produk tangkapan), milik kamera sendiri apabila ia tidak (`--no-array-alignment` — tangkapan langsung yang tidak diselaraskan). Pilihan yang sama wujud untuk `array-burst`, `project capture` / `burst` / `stream`, resipi, dan SDK.

### Rakaman Terlepas dalam Susunan Penapis Campuran

Apabila anda mencampurkan kamera RGB dan multispektral dalam satu susunan, `array-capture --processing radiance` (atau `reflectance`) menyimpan bingkai multispektral dan **melangkau** kamera RGB — sinaran per-Bayer tidak bermakna untuk penderia jalur lebar. CLI mencetak setiap fail yang disimpan (dengan tahap eksportnya), setiap `.daq` yang ditulis, dan setiap kali langkau secara eksplisit, jadi bilangan fail tidak mengejutkan:

```
  Saved: …/output/reflectance/sync_…_SN213800234-FRGN.tiff [reflectance] (SN:213800234, fid:1)
  Saved: …/output/reflectance/sync_…_SN214000533-FOCN.tiff [reflectance] (SN:214000533, fid:1)
  Saved: …/output/reflectance/sync_…_SN214701288-FNGB.tiff [reflectance] (SN:214701288, fid:1)
  DAQ:   …/output/daq/daq_data_daq-e-54b5e0_….daq
  Skipped: SN:214701292 (reflectance-not-applicable-to-rgb-cam filter=RGB)

  3 synchronized frames captured. (1 skipped)
```

Token sebab-melangkau mengikuti corak `<level>-not-applicable-to-rgb-cam`. Kamera yang pek penentukuran tidak dimuat akan melangkau `radiance` / `reflectance` dengan `<level>-needs-calibration (connect the camera so its cal pack loads)`, dan dengan tiada bacaan DAQ pantulan yang boleh digunakan, pantulan melangkau dengan `reflectance-skipped-no-dls`. Pantulan juga boleh melangkau dengan `reflectance-skipped-no-fresh-dls` / `reflectance-skipped-bound-daq-unavailable (…)`, dan dengan `dls-uncalibrated-band-<nm>` apabila jalur itu kebanyakannya terletak di luar julat kalibrasi radiometrik penderia cahaya DAQ (~374–974 nm) — antara SKU penghantaran hanya F988, yang aliran kerjanya disokong ialah panel pantulan.

Gunakan `--processing debayered` (atau `display`) untuk memasukkan setiap kamera tanpa mengira jenis penapis, atau `all` lalai untuk mendapatkan setiap tahap yang terpakai bagi setiap kamera dalam satu sesi.

### Bacaan DAQ Jauh Dari Pendedahan

Setiap `.daq` yang disimpan oleh tangkapan ialah bacaan DAQ yang dipadankan dengan pendedahan tersebut, dan `.match.json` di sebelahnya merekodkan sejauh mana jarak antara kedua-duanya (`gap_ms`) dan sama ada ia berada dalam toleransi (`in_tolerance`, 500 ms melainkan set tangkapan menetapkan `--daq-tolerance-ms`). Bacaan di luar julat itu masih digunakan, jadi tiada bingkai yang hilang, dan ia dilabelkan: fail XMP reflektansi membawa `DlsInTolerance=False` dan `DlsMatchGapS`. `lattice capture`, `multi-capture` dan `array-capture` juga mencetaknya di bawah `.daq` yang berkaitan, dengan DAQ aliran DAQ apabila seseorang menerangkannya:

**Bagaimana bacaan dipadankan dengan pendedahan.** Satu tangkapan menunggu bacaan pertama DAQ selepas pendedahan, supaya ia boleh mencampurkan bacaan di kedua-dua belahnya (`method` `interpolated`). DAQ-E menghantar bacaan setiap 0.4 s pada waktu siang dan setiap 1–2 s di dalam bangunan, jadi masa menunggu dijumlahkan kepada kira-kira satu setengah daripada selang tersebut pada arahan, bukan pada pendedahan. DAQ yang telah berhenti menghantar tidak akan ditunggu. DAQ-E pembacaan adalah purata cahaya sepanjang selang masa sejak pembacaan sebelumnya, jadi ia diletakkan di tengah-tengah selang itu: `gap_ms` diukur dari pendedahan ke titik tengah itu, dan `bracket_stamps_ns` memberikan cap masa bagi pembacaan itu sendiri. `array-capture --continuous` menunggu tanpa melambatkan pengambilan: fail mentahnya ditulis serta-merta dan `.daq` apabila bacaan tiba. Dengan `--interval`, setiap pusingan menunggu seperti satu tangkapan tunggal.

```
  DAQ:   …/output/daq/daq_data_daq-e-54b774_….daq
         DAQ reading 114.9 s from the exposure (tolerance 500 ms): reflectance uses it, labelled DlsInTolerance=False
         DAQ stream fault: no frame accepted from daq-e-54b774.local in 25 s, after 1932 good frame(s). …
```

Apabila tiada fail pantulan ditulis daripada bacaan itu, baris pertama menamatkan `saved anyway; its .match.json says in_tolerance: false` sebaliknya.

Perekodan masih keluar `0`: setiap fail telah ditulis dan menyatakan kandungannya. Satu DAQ yang aliran streamnya terhenti terus membekalkan bacaan terakhirnya, jadi periksa ia dengan `chloros-cli daq pool-list` sebelum bergantung pada pantulan perekodan tersebut. Backend menyambung semula DAQ-E yang dikumpulkan yang telah berhenti menghantar (lihat [A DAQ-E That Stops Delivering](#a-daq-e-that-stops-delivering)). Bacaan yang berada dalam toleransi hanya mencetak baris `DAQ:`, melainkan ia adalah bacaan yang disimpan (di bawah).

**Satu bacaan yang disimpan selepas DAQ-nya hilang.** Apabila tiada DAQ dapat membekalkan bacaan untuk pendedahan (DAQ yang diikat tidak tersedia, atau tiada DAQ berkumpulan yang memilikinya, contohnya selepas `daq pool-disconnect`), tangkapan menggunakan semula bacaan terakhir yang dimiliki oleh Chloros, dan `.match.json` menerangkan bacaan itu terhadap pendedahan ini: `method` `retained`, `gap_ms` daripada pendedahan ini ke sampel di mana bacaan diambil, `in_tolerance` daripada jurang itu, dan `retained_from_epoch_ns`, pendedahan yang pertama kali dipadankan dengan bacaan tersebut. Oleh itu, tangkapan yang diambil lama selepas DAQ tidak lagi aktif mencetak baris di atas dan di bawah satu lagi, yang dicetak walaupun bacaan masih dalam toleransi:

```
         This is the DAQ's last reading: the DAQ is no longer reporting, so each capture reuses it until a new one arrives
```

`.daq` masih menamakan DAQ yang mengambil bacaan, dengan penentukuran dan cap di bawahnya bacaan itu diambil, jadi import semula tahu DAQ, penentukuran dan cap mana yang menghasilkannya.

### Fail yang Gagal Ditulis

Kamera yang failnya tidak dapat ditulis — cakera penuh, ralat kebenaran — tidak menghalang fail kamera lain daripada disimpan. Setiap arahan tangkapan (`lattice capture`, `multi-capture`, `array-capture`; `project capture`, `burst`, `stream`; dan `capture`, `burst` dan `stream` bagi resipi mencetak setiap fail yang gagal ke stderr, kemudian **keluar `1`** walaupun yang lain disimpan:

```
  Failed: SN:214701292 [reflectance] OSError: [Errno 28] No space left on device
  Disk full: the drive the backend captures to has no space left. Free space on it (or capture to another drive) before capturing again.
```

Setiap fail yang dituntut oleh tangkapan dilindungi, satu-satunya-frame debayered `lattice capture --levels debayered` termasuk. `.daq` yang dibaca, di mana fail sampingan asal usul `.match.json` tidak dapat ditulis, disenaraikan sebagai `[match]` (`.daq` itu sendiri telah disimpan).

Pada pemacu yang penuh, tangkapan berulang (`array-capture --continuous` / `--interval`, `project burst` / `stream`, resipi `burst` / `stream` (`.daq` itu sendiri telah disimpan).  Pada pemacu yang penuh, tangkapan berulang (`array-capture --continuous` / X) **berhenti pada pusingan itu** bukannya tidak menulis apa-apa ke cakera yang sama untuk baki pelaksanaan. Sebarang kegagalan lain dilaporkan pada setiap pusingan ia berlaku, dan pelaksanaan diteruskan sehingga ke penghujung, kemudian keluar `1`. `array-capture --continuous` (perekodan berterusan backend) tidak menyenaraikan sebarang fail, termasuk yang gagal: ia mengiranya pada baris kemajuan dan ringkasan (`N failed`, `N file(s) failed to write.`) dan keluar `1` — lihat [Mod Berterusan](#continuous-mode---teruskan).

Perakam — `array-burst` dan `array-record` — melaporkan kegagalan mereka sendiri. `array-burst` menghentikan dirinya sendiri apabila pemacu penuh, atau sejurus sebelum ia berlaku. Kedua-duanya terus beroperasi walaupun terdapat kegagalan penulisan lain. Apabila salah satunya melaporkan ralat, ia dicetak selepas pengekod, dan arahan **keluar `1`**:

```
  Recorder error: disk-full: OSError: [Errno 28] No space left on device
  Disk full: the drive the backend captures to has no space left. Free space on it (or capture to another drive) before capturing again.
```

`Disk low:` sebagai ganti `Disk full:` bermakna burst berhenti sebelum mengisi pemandu. Rangka yang dirakam sebelum pemberhentian akan disimpan. `array-burst --build` melangkau binaan apabila ruang habis, kerana video akan disimpan ke pemandu yang sama. Ruang kosong dahulu, kemudian jalankan `array-build-video --burst-dir …`. Selepas sebarang ralat lain, binaan masih dijalankan.

`array-record` yang tidak menulis sebarang bingkai juga merupakan rakaman yang gagal (`Recorder error: no-frames: the combined view published no frame to record …`, keluar `1`) — lihat [`array-record`](#array-record--combined-index-videogif-monitoring-grade).

---

## Mod Rakaman, Perakam & Pemprosesan Semula Luar Talian

Kesemuanya beroperasi pada **susunan berterusan** (jalankan `array-connect` terlebih dahulu). Ia mencerminkan panel tangkapan GUI.

### Mod `array-capture`

`array-capture` adalah satu perintah tunggal dengan empat mod rana serta satu set penukar eksport:

| Mod | Bendera | Tingkah laku |
| --- | --- | --- |
| **Tunggal** *(laluan)* | (tiada) | Satu kumpulan tangkapan diselaraskan, kemudian keluar. |
| ****Berterusan** | `--continuous` | Rakaman berturut-turut sehingga `Ctrl+C`, `--count N` rakaman, `--duration S`, atau sehingga pemacu penuh. Menggunakan rakaman berterusan terbina dalam backend (mod Berterusan GUI) — lihat [mod Berterusan](#continuous-mode---continuous) di bawah. |
| **Interval** | `--interval S` | Satu pusingan setiap `S` saat (diukur dari permulaan setiap pusingan), had yang sama. |
| **Terpantas** | `--fastest` | Mentah-sahaja + bacaan DAQ yang ditetapkan + komposit indeks gabungan; melangkau matematik sinaran/pantulan/paparan supaya bingkai tiba dengan pantas. Menyiratkan `--processing raw --force-daq`. Proses semula `.daq` yang disimpan ke dalam produk yang dikalibrasi kemudian. |

Togol eksport (gabungkan dengan mana-mana mod; kesemuanya berkongsi hujung GUI/SDK):

| Penanda | Kesan |
| --- | --- |
| `--array-id ID` | Susunan untuk rakam (abaikan apabila hanya satu disambungkan). |
| `-o, --output DIR` | Folder output (lalai `output`, di bawah folder di mana anda menjalankan arahan). |
| `--format {tiff,tif,png,jpg,jpeg}` | Format fail (lalai `tiff`). PNG hanya mengambil `display` dan indeks render; lihat [Capture Export Levels](#capture-export-levels-the-all-default). |
| `--processing LEVEL` | Tahap eksport tunggal (`raw`, `debayered`, `radiance`, `reflectance`, `display`), atau `all` (default). |
| `--levels L1,L2,…` | Subset eksplisit jenis eksport (contohnya `raw,radiance,reflectance`); **menimpa `--processing`**. |
| `--aligned` / `--no-aligned` | **Lalai: diselaraskan, seperti tangkapan aplikasi Chloros ke atas tatasusunan yang sama** (tetapan Tangkapan Diselaraskan bagi setiap tatasusunan, diaktifkan melainkan anda menamatkannya). Eksport bukan mentah setiap ahli (`debayered`, `display`, `radiance`, `reflectance`, render indeks) dibelokkan ke dalam bingkai kamera utama dan dipotong mengikut tumpang tindih bersama ahli-ahli, jadi fail-fail itu didaftar piksel dan keluar dengan saiz, potongan dan tag `Alignment*` yang dibuat oleh aplikasi — lebih kecil daripada sensor (atau ROI) oleh offset ahli-ahli; di bawah ROI transformasi itu dipindahkan semula ke atas tetingkap. `XMP-mapir:AlignmentJSON` mengatakan `"applied": true` dan memberikan `crop_roi`. Raw kekal tidak diwarp pada saiz asal tetapi membawa transformasi dalam metadata (`"applied": false`). `--no-aligned` menyimpan setiap fail pada saiz asal, tidak diwarp. `--aligned` adalah pilihan lalai yang diperincikan. Sama ada warpan diterapkan atau tidak, setiap fail ahli merekod transformasi `Alignment*` kamera masing-masing sebaik sahaja susunan itu mempunyai profil penjajaran — kecuali kamera yang profilnya tidak sejajar (dibiarkan kosong, atau dipaksa menjadi identiti oleh penyelesaian yang gagal), dan kamera rujukan apabila tiada kamera lain yang sejajar: kedua-duanya tidak mempunyai tag `Alignment*`. Sekiranya tiada profil pada susunan, tangkapan disimpan tidak sejajar, dan CLI mencetak sebab pada XPRbaris OTX002231, seperti yang dilakukannya untuk setiap kes lain di mana warp tidak dapat diterapkan (ahli pada resolusi berbeza, satu susunan yang tidak diselaraskan secara kabel, satu bingkai yang profilnya tidak dapat dipetakan). Penyelarasan automatik susunan itu membuat profil setelah pendedaannya stabil; `align-calibrate` membuatnya atas permintaan. |
| `--orient` | Putar **produk** setiap ahli — `display`, `radiance`, `reflectance`, render `index/` dan `composite/` — oleh orientasi kamera, selepas warp penjajaran: produk yang dibengkokkan ke dalam bingkai induk (lalai) oleh *orientasi induk, supaya setiap anggota kekal selari; satu yang tidak diselaraskan (`--no-aligned`, atau tangkapan yang tidak dapat dikenakan distorsi) mengikut orientasi kamera masing-masing. **Lalai: dimatikan — setiap fail anggota kekal asli**, seperti tangkapan tatasusunan sentiasa. `raw` dan `debayered` kekal dalam bingkai asal dalam kedua-dua keadaan. Setiap fail merekod orientasi yang dibawa oleh produknya (lihat [Orientasi kamera](#what-a-captures-folder-looks-like)); XMP `Alignment*` kekal sebagai transformasi bingkai asli, diterapkan sebelum orientasi, jadi `process` (distorsi, kemudian orientasi) menghasilkan semula produk secara langsung. |
| `--index` / `--no-index` | Simpan / langkau lapisan indeks vegetasi bagi setiap kamera di mana ia telah dikonfigurasikan. Lalai: paparkan. |
| `--no-daq` | Langkau fail sidecar `.daq` apabila tiada aras terpilih menggunakan DAQ (perekodan mentah sahaja). Secara lalai bacaan disimpan, supaya bingkai boleh diproses semula menjadi pantulan/indeks secara luar talian. `--force-daq` masih diterima (lalai). |
| `--daq-tolerance-ms MS` | Toleransi padanan DAQ untuk tangkapan ini (lalai 500 ms). |
| `--smart` | Menunggu AE menstabilkan pada semua kamera sebelum mencetuskan (lihat [Smart-AE / Smart-Capture](#smart-ae--smart-capture)). |
| `--compression {deflate,none}` | Pemampatan piksel TIFF. `none` (lalai sejak 1.3.0; 1.2.0 menulis `deflate`) = tidak dimampatkan — piksel sensor tepat seperti dibaca, ~6.3 MB setiap bingkai resolusi penuh, dan kadar berterusan terpantas; `deflate` = zlib L1 tanpa kehilangan + peramal mendatar, ~4.1 MB setiap bingkai, lebih perlahan. Kedua-duanya tanpa kehilangan dan dibaca secara identik semasa import. |

> **TIFF satu-tulis + model kadar berterusan.**Setiap fail ditulis**sekali**: piksel, paket XMP, IFD0 Make/Model/NomborSiriKamera dan tag EXIF `ExposureTime` / `SerialNumber` dalam satu kali larian, tanpa apa-apa yang menulisnya semula selepas itu. `--continuous` merakam kumpulan bersambung yang diselaraskan — setiap rakaman mengambil kumpulan selepas yang sebelumnya, termasuk satu yang diterbitkan semasa rakaman itu masih menyimpan — jadi ia berjalan pada kadar tatasusunan. DEFLATE memegang GIL Python, jadi penulisan mampat**tidak**dijalankan secara selari merentasi benang penulis setiap kamera; perekodan resolusi penuh berterusan pada kadar sensor memerlukan penulisan tanpa pemampatan (laluan lalai)**dan** cakera kelas NVMe (~600 MB/s berterusan untuk susunan 9 kamera pada ~10 fps). Suis yang sama didedahkan sebagai `compression` pada `POST /api/camera/array/capture`.

```bash
# Interval timelapse: one reflectance pass every 10 s for 5 minutes
chloros-cli lattice array-capture --interval 10 --duration 300 \
  --processing reflectance -o timelapse/

# Fastest grab for a moving rig — raw + .daq now, calibrate later
chloros-cli lattice array-capture --fastest -o flightline/

# Co-registered multi-band export (aligned is the default; drop the index overlay)
chloros-cli lattice array-capture --processing reflectance --no-index -o out/

# Each member at its native size, unwarped
chloros-cli lattice array-capture --processing reflectance --no-aligned -o native/

# Continuous raw capture for 60 s, as fast as the array publishes
chloros-cli lattice array-capture --continuous --duration 60 --fastest -o flightline/
```

#### Mod berterusan (`--continuous`)

`array-capture --continuous` (tanpa `--interval` atau `--smart`) menyerahkan pelaksanaan kepada **perekodan berterusan backend sendiri** — sesi yang dijalankan oleh mod Berterusan GUI. Backend menyimpan setiap kumpulan diselaraskan sendiri, dengan dua tangkapan sedang dijalankan dan tiada permintaan daripada CLI di antara kumpulan, jadi ia dapat mengekalkan kelajuan pada susunan pantas di mana permintaan bagi setiap tangkapan akan ketinggalan (diukur 73–77 tangkapan/s pada susunan 9 kamera pada ROI 512-piksel). Setiap togol eksport di atas terpakai kepada setiap tangkapan.

- **Batas:** `--count N` berhenti selepas N tangkapan (satu tangkapan = satu kumpulan diselaraskan: fail setiap ahli); `--duration S` selepas S saat. Tanpa salah satu daripadanya, ia akan berjalan sehingga `Ctrl+C`, cakera penuh, ralat, atau susunan itu hilang. `Ctrl+C` menghentikan sesi backend, kemudian mencetak ringkasan.
- **Kemajuan** setiap 2 s, dengan `, N failed` dan `, N groups skipped` disertakan apabila tidak sifar (kumpulan yang dilangkau ialah satu yang diterbitkan oleh susunan bahawa tiada tangkapan diambil):

  ```
     12.0s  880 captures (73.3/s), 7920 files
  ```

- **Tiada penyenaraian bagi setiap fail.** Jalannya berakhir dengan satu baris ringkasan — tangkapan, kadar, fail dan folder keluaran — diikuti dengan `N file(s) failed to write.` / `N group(s) skipped.` apabila tidak sifar:

  ```
  Done — 880 captures (73.3/s), 7920 files in /home/user/flightline.
  ```

- **Berhenti** apabila cakera penuh dengan `Continuous capture stopped: the drive is full.`, dan pada ralat atau apabila tatasusunan hilang dengan `Continuous capture stopped: <reason>.` (kedua-duanya pada stderr).
- **Status keluar:** `0` apabila ia menyimpan rakaman dan setiap fail berjaya ditulis, termasuk `Ctrl+C`; `1` pada mana-mana fail yang gagal ditulis, cakera penuh, ralat, susunan hilang, atau tiada tangkapan langsung.
- **Microsoft Defender:** apabila Defender menggunakan satu teras CPU atau lebih untuk mengimbas fail yang ditulis oleh tangkapan, larian mencetak sekali, pada stderr: `Note: Microsoft Defender is using N.N CPU cores scanning the capture files, CPU the capture needs. Re-run the Chloros installer with "Exclude the Chloros backend from Microsoft Defender scanning" checked.` Lihat pemasang [Tetapan sistem](#windows-exe).
- Satu tangkapan berterusan bagi setiap tatasusunan pada satu-satu masa; tangkapan kedua ditolak dengan `a continuous capture is already running for this array`. Melalui HTTP sesi adalah `POST /api/camera/array/continuous/start` (badan `/api/camera/array/capture` ditambah `max_captures` / `duration_s`), `GET /api/camera/array/continuous/status?array_id=ID` dan `POST /api/camera/array/continuous/stop`.

**Interval**(`--interval S`, dengan atau tanpa `--continuous`) dan**`--continuous --smart`** kekalkan gelung per-permintaan: satu permintaan tangkapan bagi setiap pass — pada setiap saat yang dijadualkan untuk Interval, selepas AE stabil untuk `--smart` — dengan fail setiap pass disenaraikan seperti dalam mod Single, satu baris `Done — N capture pass(es).` di akhir, dan `--count N` mengira pass.

### `array-record` — video/GIF indeks gabungan (darjah pemantauan)

Merakam **pandangan indeks gabungan langsung** tatasusunan ke dalam `.avi` (dan secara pilihan ke dalam `.gif`). Apabila pandangan gabungan GUI dibuka, ia merakam pandangan tersebut seperti yang dipaparkan. Jika tiada tetingkap dibuka, backend memaparkan pandangan itu sendiri: indeks yang anda hantar dengan `--expr`, atau secara lalai NDVI matriks, setelah matriks diselaraskan. NDVI lalai menggunakan jalur merah dan NIR terdekat pada 660 nm dan 850 nm, seperti yang dilakukan oleh paparan langsung. Paparan GUI yang dibuka semasa rakaman akan dirakam selagi ia kekal terbuka. Ia memeriksa kemajuan setiap 2 saat dan berhenti pada `--duration`, `Ctrl+C`, atau apabila perakam menamatkan sendiri.

Satu rakaman yang tidak menulis sebarang bingkai akan gagal: perakam melaporkan `no-frames: …` (susunan mungkin masih dalam proses penjajaran atau menetapkan pendedahan) dan arahan **keluar `1`**.

```bash
# 30-second combined-index clip at 10 fps, plus a GIF (no GUI needed)
chloros-cli lattice array-record --duration 30 --fps 10 --gif -o monitoring/

# Record a chosen index instead of the default NDVI
chloros-cli lattice array-record --duration 30 \
  --expr "(NIR_850_RGN-Red_660_RGN)/(NIR_850_RGN+Red_660_RGN)" -o monitoring/
```

| Bendera | Lalai | Keterangan |
| --- | --- | --- |
| `--array-id ID` | hanya tatasusunan | Tatasusunan sasaran (abaikan jika hanya satu yang disambungkan). |
| `-o, --output DIR` | `output` | Direktori keluaran. Laluan relatif adalah di bawah folder anda jalankan arahan daripada. |
| `--fps F` | `10` | Kadar bingkai rakaman. |
| `--duration S` | sehingga Ctrl+C | Berhenti automatik selepas `S` saat. |
| `--gif` | off | Juga tulis GIF animasi. |
| `--gif-only` | off | Hanya tulis GIF (tiada `.avi`). |
| `--expr EXPR` | NDVI larik | Ekspresi indeks untuk direkodkan apabila tiada paparan GUI dibuka, dalam nama jalur larik (contohnya `'(NIR_850_RGN-Red_660_RGN)/(NIR_850_RGN+Red_660_RGN)'`; lihat [nama jalur](#index--vegetation-maths)). Nama warna pendek (`NIR`, `Red`) hanya berfungsi apabila satu jalur dalam tatasusunan mempunyai nilai tersebut. Paparan GUI yang terbuka akan direkodkan sebagai dipaparkan sebaliknya. |

### `array-burst` — burst Bayer mentah berkelajuan tinggi (darjah analisis)

Membaca buffer kumpulan diselaraskan gelung tangkapan secara langsung — **tidak memerlukan rantaian kalibrasi, exiftool, atau paparan langsung** — jadi ia berjalan pada kadar tangkapan penuh kamera. Menulis bingkai mentah + manifest bagi setiap bingkai + satu `.daq` bagi setiap bacaan DLS yang berbeza di bawah `<output>/bursts/<base>/`. Proses semula secara luar talian (perintah seterusnya), atau hantar `--build` untuk melakukannya serta-merta apabila berhenti.

```bash
# 5-second raw burst, then build the combined index video in one shot
chloros-cli lattice array-burst --duration 5 --build \
  --products combined:index --fps 10 -o capture/
```

| Bendera | Lalai | Keterangan |
| --- | --- | --- |
| `--array-id ID` | hanya tatasusunan | Tatasusunan sasaran. |
| `-o, --output DIR` | `output` | Direktori output (burst mendarat di `<DIR>/bursts/<base>/`). |
| `--duration S` | sehingga Ctrl+C | Henti automatik selepas `S` saat. |
| `--max-frames N` | tanpa had | Auto-henti selepas `N` bingkai mentah. |
| `--orient` | off | Produk yang dihasilkan oleh binaan daripada ledakan ini dipusingkan oleh orientasi kamera — produk bagi setiap kamera oleh kamera mereka sendiri, indeks gabungan oleh master — seperti yang dilakukan oleh `array-capture --orient`. Rangka mentah kekal asli; rekod `manifest.json` burst ini merekod orientasi setiap ahli dan pilihan. |
| `--build` | off | Selepas berhenti, proses semula burst dengan segera (sama seperti `array-build-video`). |
| `--products …` | `combined:index` | Dengan `--build`: video mana yang hendak dibina (lihat di bawah). |
| `--fps F` | `10` | Dengan `--build`: keluarkan fps video. |
| `--save-tiffs` | off | Dengan `--build`: juga simpan TIFF yang dikalibrasi per-frame (masing-masing dengan [topeng clamp unmix](#unmix-clamp-mask-which-pixels-a-band-was-clamped-on) apabila piksel dikekang; hasil binaan menyenaraikannya sebagai `clamp_masks`). |
| `--gif` | off | Dengan `--build`: juga tulis GIF animasi. |

### `array-build-video` — proses semula luar talian burst yang disimpan

Menyesuaikan masa setiap bingkai mentah dengan bacaan `.daq` yang disimpan terdekat dan memprosesnya melalui **rantaian radiasi / pantulan / indeks yang sama seperti saluran import**, menjana satu atau lebih video.

`--products` adalah senarai koma item `kind:level`, di mana `kind` ∈ `per_cam` | `combined` dan `level` ∈ `radiance` | `reflectance` | `index`. `level` tanpa `kind:` secara lalai adalah `per_cam`. Lalai adalah `combined:index`.

```bash
# Per-cam reflectance video for every member + one combined NDVI video
chloros-cli lattice array-build-video \
  --burst-dir "capture/bursts/2026-06-24_141500" \
  --products per_cam:reflectance,combined:index \
  --fps 10 --save-tiffs
```

| Bendera | Lalai | Keterangan |
| --- | --- | --- |
| `--burst-dir DIR` | (diperlukan) | Laluan ke folder burst (`…/bursts/<base>/`). |
| `--products …` | `combined:index` | `kind:level` senarai, seperti di atas. |
| `--fps F` | `10` | Keluaran fps video. |
| `--save-tiffs` | off | Juga simpan TIFF yang dikalibrasi setiap bingkai bersama video(s). |
| `--gif` | off | Juga tulis GIF animasi. |

**Video tidak mempunyai topeng clamp** (satu aliran imej, 8-bit). Sebaliknya, hasil binaan membawa `clamp_fractions`: bagi setiap produk video, bahagian piksel setiap bingkai yang dikekang oleh unmix dalam jalur saluran R / G / B yang dipegangnya — `Chloros:Band{R,G,B}UnmixClampFraction` produk sinaran/pantulan bingkai itu akan dicap (untuk indeks gabungan, bagi setiap kamera). Jalannya mencetak bingkai terburuk bagi setiap produk pada satu baris `Clamp:`. `--save-tiffs` menambah topeng piksel bagi TIFFs, dalam `clamp_masks/<level>/` di sebelah folder `<level>/` TIFFs.**Pembinaan yang terhenti tidak meninggalkan sebarang video yang rosak.Setiap video ditulis sebagai `<name>.partial.avi` dan hanya mengambil nama akhir apabila ia selesai, jadi video lama dengan nama itu kekal sehingga penggantinya lengkap. Jalankan semula binaan untuk menggantikan `.partial.avi` yang tertinggal.

Proses ini mencetak setiap video yang ditulisnya, kemudian sebarang produk `Skipped:` (kamera/paras yang diminta — atau indeks gabungan — yang tidak menghasilkan video, beserta sebabnya — contohnya tiada bundel kalibrasi, tiada bacaan DLS untuk pantulan, atau tiada pasangan jalur merah/NIR untuk indeks gabungan) dan mana-mana `Note:`. Kamera RGB yang meminta sinaran atau pantulan akan mendapat video paparan sebagai ganti, disimbangkan warna putih apabila bundel kalibrasi kamera tersedia; kamera yang dirender tanpa ia dinamakan dalam `Note:`. Ia **keluar dengan nilai tidak sifar** apabila produk yang diminta diabaikan atau tidak dibina seperti yang diminta (ungkapan indeks yang dikonfigurasikan tidak dinilai secara luar talian); video yang dibina masih ditulis dan disenaraikan.

> **Pilih perakam yang sesuai.** `array-record` adalah *darjah pemantauan* — ia merakam komposit langsung yang dipaparkan: pandangan gabungan GUI, atau jika tiada GUI dibuka, pandangan belakang sendiri Pemandangan NDVI (atau `--expr`). `array-burst` → `array-build-video` adalah *darjah analisis* — ia menyimpan data mentah sensor pada kadar penuh dan membina semula video radiasi/refleksi/indeks yang dikalibrasi kemudian, tanpa memerlukan paparan langsung.

### Kamera Jalur Tunggal Mono (M3M)

Barisan **M3M**ialah saudara mono kepada Bayer**M3C**: satu penapis interferens jalur sempit bagi setiap kamera (`M3M-<lens>-F<wavelength>`, contohnya `M3M-L87-F685`), oleh itu sensor menghasilkan**satu jalur skala kelabu**tanpa mozek Bayer. Tiada apa yang perlu didemosai, tiada gangguan silang antara saluran untuk dipisahkan, dan tiada imbangan putih untuk ditetapkan — keseluruhan saluran warna paparan**RGB** tidak terpakai.

Apa maksudnya pada CLI:

- **`lattice white-balance`, `lattice color-profile`, `lattice color`**mengesan kamera mono dan**melangkau dengan mesej satu baris** bukannya menghantar tetapan yang tidak bermakna. Mereka masih berjalan dengan normal terhadap kamera M3C RGB/Bayer dalam sesi yang sama.
- **Format piksel Bayer menunjukkan kedalamannya.** Format piksel Bayer menunjukkan kedalamannya. 388 memberikan `Mono8`; `BayerRG12`, `high_quality`, `triggered`, `Mono12`, `BayerRG12p`, `Mono12p`, `lattice format`, `--pixel-format Mono12`, `BayerRG12`, `lattice format --set`, `lattice calibrate`, `process --reflectance`, `array-capture --processing radiance`, `index --live`, `--serials`, `cam-connect`, `array-connect`, `CAMERA_HELD`, `1`, `chloros-cli lattice cam-disconnect --all`, `chloros-cli lattice array-disconnect --all`, `--channel`, `red`, `nir`, `--list-presets`, `--channel red=0 --channel nir=1`, `M3M`, `M3C`, `is_mono`, XP388 menghasilkan `Mono8`; `BayerRG12`, `high_quality` dan `triggered` menghasilkan `Mono12`; nama yang dipadatkan kekal dipadatkan (`BayerRG12p` menghasilkan `Mono12p`). Fail-fail dan `lattice format` menamakan format Mono. Susunan yang mencampurkan kamera M3C dan M3M mengambil satu format cara yang sama: Bayer pada setiap M3C, kedalaman Mono pada setiap M3M. Nama adalah kedalaman bitnya: perkara yang sama terpakai secara sebaliknya, jadi M3C tidak pernah diletakkan pada format Mono (`--pixel-format Mono12` adalah `BayerRG12` pada M3C, mana-mana kamera yang menjadi master bagi susunan itu), dan `lattice format --set` memetakan dengan cara yang sama dan mencetak format yang ditetapkan.
- **`lattice calibrate` / `process --reflectance` / `array-capture --processing radiance`** masih berfungsi — radiasi dan pantulan adalah peta radiometrik *per-band* dan ditakrifkan dengan sempurna untuk satu band. Rangka mono membawa matriks tindak balas sensor **identiti** (tiada pencampuran semula 3×3), jadi pesawat melalui matematik penentukuran tanpa terjejas.
- **Satu kamera mono tidak boleh menghasilkan indeks vegetasi.**NDVI/NDRE/dan lain-lain memerlukan sekurang-kurangnya dua jalur (contohnya Red + NIR). Untuk mendapatkan indeks daripada perkakasan mono, tujukan**beberapa** kamera M3M pada panjang gelombang yang berbeza, selaraskan mereka ke dalam satu susunan berbilang-jalur, dan buat indeks daripadanya:

```bash
# Red (660) + NIR (850) mono pair -> aligned 2-band stack -> NDVI
chloros-cli lattice index --live --profile align.json \
  --preset NDVI --channel red=Mono_660 --channel nir=Mono_850 \
  --save-multiband -o output/
```

`index --live` membuka kamera itu sendiri (sesuatu [sesi sementara](#multi-camera-transient-sessions)), jadi jangan sambungkan apa-apa dahulu: `--serials`, jika tidak, kamera yang diaktifkan dalam profil (lihat [Index / Vegetation Maths](#index--vegetation-maths)). Sementara Chloros memegang mana-mana kamera (GUI, `cam-connect`, `array-connect`) ia ditolak dengan HTTP 409 `CAMERA_HELD` dan keluar `1`; lepaskan mereka dengan `chloros-cli lattice cam-disconnect --all` / `chloros-cli lattice array-disconnect --all`.

simbol `--channel` mesti sepadan dengan nama saluran pratetap **tepat** (sensitif huruf besar/kecil; NDVI ditulis dengan huruf kecil `red`,`nir` — lihat `--list-presets`), dan nama sisi jalur menamakan jalur dalam susunan yang selari (mod luar talian juga menerima indeks jalur berasaskan 0, contohnya `--channel red=0 --channel nir=1`).

Diskriminator di seluruh susunan ialah token `M3M` dalam rentetan model (ia tidak pernah muncul dalam rentetan `M3C`), yang dipaparkan kepada GUI/SDK sebagai `is_mono`.

---

## Penyediaan & Penalaan NIC Host (susunan LATTICE)

Kamera LATTICE menstrim GVSP melalui penyesuai Ethernet host, jadi untuk susunan berbilang kamera, **pemacu**penyesuai tersebut dan**saiz cincin penerimaan** sama pentingnya dengan kadar pautan. Tetapan yang salah akan muncul sebagai `FRAMES WILL DROP` / pintu `Reduce ROI to enable` dalam panel Tetapan Susunan (dan dalam `lattice network-analysis` / `analyze_array_network()` SDK), walaupun kamera itu sendiri sihat.

### Penyesuai USB 10GbE — Realtek RTL8157 ("Pengawal Keluarga USB 10GbE Realtek")

Tetapan di bawah adalah sifat pemacu Windows. Pada Linux, penyesuai USB juga mendapat had (400 MB/s pada x86_64, 200 MB/s pada NVIDIA Jetson) (lihat [Array fps & burst model](#array-fps--burst-model)), dan buffer penerimaan soket kernel penting untuk setiap penyesuai — lihat [Linux hosts](#linux-hosts--socket-receive-buffers).

| Item | Nilai yang diperlukan | Mengapa ia penting |
| --- | --- | --- |
| **Versi pemacu**|**≥ v10.67 (Jan 2026)**, INF `rtump64x64sta.inf` | Pemacu warisan**2016**(v10.65, `rtump64x64.inf`) mengendalikan penutupan kuasa dan bugcheck dengan**`DRIVER_POWER_STATE_FAILURE` (BSOD `0x9F`)**semasa penutupan/mula semula/tidur. Peralihan terhenti (~5 minit tamat masa), pengguna mematikan kuasa secara paksa, dan pemadaman tidak lengkap berulang kali**merosakkan repositori WMI**(PowerShell/alat mula gagal dengan `Invalid class`) dan**menyekat susunan USB** pada but seterusnya (NIC tidak akan diaktifkan; pemacu USB berhenti menyenaraikan). Kemas kini dari realtek.com (atau pembekal dongle) sebelum bergantung pada permulaan semula yang bersih. |
| **Butang Penerimaan**— kata kunci `ReceiveBufferLen` |**256**(maksimum pemacu) | Cincin RX NIC. Lalai pemacu**32**hanya meninggalkan ~0.26 MB cincin yang boleh digunakan — terlalu kecil untuk letupan multi-kamera — jadi panel susunan melaporkan `Sim-emit burst … exceeds NIC RX ring usable capacity 0.26 MB` dan menyekat sambungan. Pada**256**lingkaran itu besar (**~13.5 MB diukur pada hos 10GbE makmal**), memberikan saluran RX ruang yang mencukupi untuk letupan GVSP berbilang-kamera. (Sama ada konfigurasi tertentu benar-benar *berjaya disambungkan* ditentukan oleh dua semakan — **drain-aware**semakan kebolehditerima dan semakan**kelebihan langganan agregat** — bukan perbandingan mentah burst-vs-ring; lihat [Array fps & burst model](#array-fps--burst-model).) |
| **URB Penerimaan**— kata kunci `PendingReceives` |**64** (maks) | Blok permintaan USB dalam penerbangan; dinaikkan bersama Penimbal Penerimaan untuk penyerapan lonjakan. |
| **Jumbo Frame** — kata kunci `*JumboPacket` | **9014** | Diperlukan untuk paket GVSP 9000-bait (6× kurang paket/bingkai berbanding 1500). |

> ⚠️ **Kemas kini pemacu NIC akan menetapkan semula sifat lanjutan ini kepada lalai.**Selepas mengemas kini atau menggantikan pemacu penyesuai,**terapkan semula** `ReceiveBufferLen=256` dan `PendingReceives=64`, jika tidak panel susunan akan menetapkan semula walaupun "tiada apa yang berubah pada perkakasan." Ini adalah punca nombor 1 mengapa rig yang sebelum ini berfungsi tiba-tiba enggan menyambung.

Terapkan dari PowerShell **tingkat tinggi** (gantikan dengan nama penyesuai anda, contohnya `"Ethernet 5"`):

```powershell
Set-NetAdapterAdvancedProperty -Name "Ethernet 5" -RegistryKeyword ReceiveBufferLen -RegistryValue 256
Set-NetAdapterAdvancedProperty -Name "Ethernet 5" -RegistryKeyword PendingReceives  -RegistryValue 64
Get-NetAdapterAdvancedProperty  -Name "Ethernet 5" -RegistryKeyword ReceiveBufferLen,PendingReceives   # verify
```

> **`lattice network --fix` merangkumi penyesuai USB 10GbE.** Ia kini mengesan jenis penyesuai dan melaras kata kunci receive-ring yang betul: `*ReceiveBuffers`→2048 untuk PCIe NICs (Intel I219, dll.), atau `ReceiveBufferLen`→256 + `PendingReceives`→64 untuk pengawal **USB** 10GbE Realtek (yang tidak menyokong `*ReceiveBuffers`) Sasaran dikekang kepada had maksimum yang dilaporkan oleh setiap pemacu (`NumericParameterMaxValue`), jadi ia tidak akan menulis nilai di luar julat.  Jalankan dari terminal **tingkat tinggi**; seperti mana-mana penyetelan berasaskan pendaftaran, perubahan akan berkuat kuasa selepas pemacu rangkaian dihidupkan semula atau komputer di-restart. Perintah manual `Set-NetAdapterAdvancedProperty` di atas kekal sebagai alternatif yang baik — ia digunakan secara langsung (mengikat semula pemacu rangkaian) tanpa perlu dihidupkan semula.

Host Linux — bufer penerimaan soket

Pada Linux, termasuk NVIDIA Jetson, bufer penerimaan soket kernel sama pentingnya dengan cincin penerimaan NIC. Pada tetapan lalai kernel (`net.core.rmem_max` / `net.core.rmem_default` = 212,992 bait), ledakan paket GVSP oleh kamera melimpahi soket aliran sebelum Chloros membacanya. Pakej-pakej itu terbuang dalam kernel manakala NIC itu sendiri melaporkan tiada kehilangan, dan satu susunan kehilangan kumpulan bingkai serta menurunkan kadar bingkainya. Diukur pada susunan 4 kamera di belakang penyesuai USB 10 GbE, menaikkan kedua-duanya kepada 32 MB meningkatkan kadar susunan daripada 4.96 kepada 7.52 fps selepas sambungan, dan tangkapan mentah berterusan daripada 4.7 kepada 7.6 tangkapan/s, tanpa sebarang paket terbuang dalam kernel.

- **Fail .deb menetapkan mereka.** Memasang pakej menulis `/etc/sysctl.d/60-chloros-gige.conf` (kedua-duanya di `33554432`) dan menerapkannya serta-merta. Nilai yang sudah ditetapkan lebih tinggi dibiarkan seperti sedia ada.
- **`lattice network` memeriksanya.** Apabila mana-mana satu adalah lebih rendah, ia menyenaraikan `socket receive buffers too small for GigE Vision (…)` di bawah *Issues found*, dan log backend memaparkan amaran yang sama apabila kamera atau susunan pertama disambungkan.
- **Untuk menaikkannya secara manual** (sehingga but semula seterusnya; fail pakej ini menerapkannya pada setiap but):

  ```bash
  sudo sysctl -w net.core.rmem_max=33554432 net.core.rmem_default=33554432
  ```

  `lattice network --fix` juga menaikkan dan mengekalkan mereka, tetapi hanya apabila backend dijalankan sebagai root. Backend yang dijalankan sebagai pengguna anda (yang dimulakan dengan `chloros-backend`, atau [`chloros-backend@<user>`](#running-the-backend-as-a-service-headless)) tidak dapat mengubah tetapan kernel, dan mencetak baris `sudo sysctl` sebaliknya.

**Penalaan penyesuai automatik hanya menyentuh penyesuai yang disambungkan.** Pada sambungan kamera pertama, backend yang berjalan sebagai root akan menala penyesuai berwayar yang cincin penerimaannya kecil. Ia kini hanya melaras penyesuai yang mempunyai pautan, jadi port terbina dalam yang tercabut mengekalkan MTU-nya, dan ia tidak mengubah penimbal penerimaan. `lattice network` tidak lagi menyenaraikan penyesuai yang tercabutMTU sebagai isu. `lattice network`, `--fix`, `lattice probe` dan penalaan penyesuai hanya mempertimbangkan port Ethernet berwayar, bukan CAN bus Jetson (`can0`) atau pautan mod peranti USB (`usb0`, `usb1`) ia menawarkan PC hos; penyesuai Ethernet USB untuk kamera masih dikira.

### Asas rangkaian (semua pautan LATTICE)

- **Penalaminan:** link-local `169.254.0.0/16` (GigE Vision LLA). Host mengambil `169.254.x.x/16` statik; kamera + DAQ-E menetapkan sendiri dalam julat yang sama. DHCP/gateway tidak diperlukan.
- **Saiz paket:**utamakan jumbo (9000), tetapi biarkan auto-probe mencarinya — ia melihat melebihi had ICMP 1500-byte kamera melalui probe GVSP, jadi ia akan menggunakan jumbo di mana sahaja talian benar-benar menyokongnya. Setiap kamera diukur sekali setiap sesi backend, dan sekali lagi apabila pautannya berubah (penyesuai lain, kelajuan pautan lain, atau pautan yang terputus, seperti semasa menukar suis); sambungan semula akan menggunakan semula keputusan tersebut. Selepas perubahan di belakang suis yang masih aktif, mulakan semula backend untuk mengukur semula. Sambungan pertama kadangkala boleh mengambil beberapa minit untuk pemeriksaan ini, dan log akan menunjukkan demikian apabila ia berlaku. Gunakan pin dengan `CHLOROS_GVSP_PACKET_SIZE_FORCE=9000` hanya apabila anda lebih mengetahui daripada probe, dan utamakan per-perintah berbanding yang kekal: pin akan melangkau probe, jadi jika laluan sebenarnya tidak dapat memikul 9000,**setiap** tangkapan akan tamat masa dengan `SC_ERR_TIMEOUT -1011` (lihat [Pembolehubah Persekitaran](#environment-variables)).
- **Gelung RX berskala dengan `ReceiveBufferLen`:**pada `32` lalai, cincin yang boleh digunakan adalah ~0.26 MB (terlalu kecil untuk sebarang burst multi-kamera); pada `256` maksimum ia besar (~13.5 MB diukur pada hos 10GbE makmal), memberikan ruang yang mencukupi. Sama ada konfigurasi akan bersambung kemudian ditentukan oleh pemeriksaan kemasukan yang sedar aliran keluar**dan** pemeriksaan agregat keterlebihan langganan di bawah — bukan perbandingan mentah burst-vs-ring.

### Model fps & burst Susunan

Cara membaca panel Tetapan Susunan (dan `lattice analyze-array` / `analyze_array_network` dalam SDK):

- **Burst dijumlahkan bagi setiap kamera mengikut format piksel sebenar kamera tersebut.**Kamera Mono**M3M**menyalurkan**Mono12 (2 B/px)**, atau dengan `--pixel-format` format Mono kedalaman ia (`BayerRG8` adalah `Mono8`, 1 B/px); kamera Bayer**M3C**menyalurkan 8- atau 12-bit (firmware M3C secara senyap mengeluarkan BayerRG12 walaupun BayerRG8 diminta). Jadi, satu bingkai penuh 4-kamera adalah**~12.6 MB jika semuanya 8-bit tetapi ~25 MB dengan tiga kamera mono 12-bit**. Projeksi menentukan format setiap kamera daripada modelnya (cache identiti), jadi burst sepadan dengan apa yang sebenarnya dipindahkan — bukan andaian satu saiz BayerRG8.
- **Penyambung Ethernet USB dihadkan pada 200 MB/s tanpa mengira label namanya.** Jadual kecekapan yang menukarkan kadar pautan kepada angka berterusan diperoleh daripada PCIe; NIC USB mengiklankan kadar pautan *Ethernet*nya tetapi dihadkan oleh bas USB dan pemandunya. Dongle USB 10GbE pernah mencatat kira-kira 1063 MB/s "terpelihara" — satu angka yang tidak pernah diuji — dan kelajuan yang terhasil merosakkan 6–18% bingkai sambil masih melaporkan fps sasaran yang sihat. NIC yang disambungkan melalui USB kini dihadkan secara mutlak: **200 MB/s** pada Windows dan pada NVIDIA Jetson, **400 MB/s** pada Linux x86_64 (pada Linux, penyesuai pada port USB 2.0 dibiudgetkan **30 MB/s**, dan rekod keupayaan menyatakan untuk mencolokkannya ke port USB 3) (hadnya adalah bus USB dan pemacu hos, jadi ia tidak meningkat selari dengan papan nama; penyesuai USB 1 GbE memperoleh ~80 MB/s dan tidak terjejas). Pada Linux, penyesuai dikenal pasti melalui sysfs (berpaling kepada `ethtool -i`'s `bus-info`). Setiap angka diukur: pada Windows, rangkaian 4 kamera yang sama merosakkan 6–10% bingkai melebihi 200 MB/s; pada Linux x86_64, penyesuai yang sama berfungsi dengan lancar pada setiap had sehingga 480 MB/s (273 MB/s dihantar, had kamera itu sendiri). Pada NVIDIA Jetson, penyesuai yang sama hanya berfungsi dengan lancar pada 200 MB/s dan kehilangan bingkai secara berselang di atasnya. `wire_ceiling_source` dalam rekod keupayaan menyatakan demikian secara bertulis, dan `nic_is_usb` menandakannya. Gantikan sama ada dengan `--wire-ceiling-mbps`.
- **Admittance sedar tentang saliran, bukan keseluruhan-ledakan-berbanding-ring.** Satu burst serentak hanya perlu muat dengan *backlog sementara* = `max(0, Σ per-cam arrival − host drain) × emit_window`, bukan keseluruhan burst. Pada fabrik hos pantas / kamera perlahan (satu **PCIe**Host 10G + 4× cam 1 GbE: ketibaan ≈ 320 MB/s, saliran ≈ 1063 MB/s) hos mengosongkan lebih pantas daripada kamera mengisi, tunggakan ≈ 0, jadi sim-emit resolusi penuh**diterima**walaupun burst 25 MB melebihi cincin 13.5 MB. Letakkan empat kamera yang sama di belakang penyesuai**USB** 10GbE pada Windows dan kadar saliran ialah 200 MB/s, bukan 1063 — ketibaan melebihi kadar saliran, dan kehilangan dipaparkan sebagai bingkai rosak, bukannya kadar bingkai yang lebih rendah. Pada hos 1 GbE, ambang DLThr 31.25 MB/s kamera menyebabkan ketibaan melepasi aliran keluar → ia dengan betul **menyekat** (untuk kelas sekatan *ini*, kurangkan ROI atau gunakan binning ≥ 2). Admittance adalah salah satu daripada **dua** pintu sambungan — satu lagi ialah pemeriksaan agregat keterlebihan langganan di bawah.
- **fps yang dijangka adalah kitaran kamera yang paling perlahan.**Dalam emit serentak setiap kamera menghantar bingkai pada masa yang sama, jadi susunan beredar pada masa yang lebih lama antara bacaan sensor dan masa emit ahli yang paling perlahan (byte bingkai ÷ kadar emitnya: pautan**akses** kamera, 1 GbE ≈ 80 MB/s, atau bahagiannya daripada had jalur hos apabila ia lebih kecil). Setiap pendedahan bertindih dengan bacaan bingkai sebelumnya, jadi pendedahan tidak memasuki kitaran: pendedahan automatik mengisi tempoh yang diberikan oleh kadar, kemudian meningkatkan penguatan, dan suasana malap tidak lagi menurunkan kadar (dalam 1.2.0 ia melakukannya). Hanya pendedahan manual yang lebih lama daripada tempoh itu memperlahankan pencetus (lihat [Pendedahan pada ahli tatasusunan](#exposure-on-an-array-member)). Menambah kamera tidak melambatkan susunan sehingga perkongsian wayar mereka mengecil. Dalam emit bersiri (`slip-emit-and-capture`), masa emit kamera dijumlahkan sebaliknya, dan pendedahan setiap ahli diambil kira dalam penyelarasan. Diukur pada sensor penuh, 12-bit, pada rangkaian 9 kamera dengan hos PCIe 10 GbE: 10.7 fps, atau 14.0 fps dengan [penghantaran berpadat](#packed-12-bit-transport---packed).
- **Selepas tangkapan berterusan, unjuran menunjukkan kadar yang dicapai.** Tangkapan selama 30 saat atau lebih pada kadar automatik merekodkan kadar yang stabil pada saiz tatasusunan tersebut di komputer ini; percubaan singkat pengawal kadar pada kadar yang lebih pantas diabaikan. Mulai saat itu, baris `array-connect` dan panel Tetapan Susunan memetik kadar itu apabila ia di bawah kadar model, dengan apa yang mengehadkannya: `clock-bound` (susunan berjalan pada kadar yang dicapainya, gelung grab menunggu jam pencetusnya), `loop-bound` (gelung grab tiada masa tinggal), `capture-bound` (penangkapan ketinggalan dan melangkau kumpulan) atau `measured` (tiada yang menyatakan yang mana). Bentuk model itu berada dalam `wire_fps_max`. Susunan itu masih meningkat kepada apa yang dibawa oleh wayar.
- **Susunan berfungsi pada mana-mana rangkaian — kadar bingkai adalah hadnya.**Setiap kamera mendapat bahagian sama rata daripada**had jalur selamat-langgaran**(`sustained × sim_emit_factor`), dipacu dengan GevSCPD, dan kesemuanya masih mengekspos pada pencetus yang sama. Pada wayar yang perlahan, bahagiannya kecil dan kadar bingkai menurun bersamanya: 8 kamera 12-bit beresolusi penuh pada 1 GbE tanpa bingkai jumbo mendapat 52 / 8 = 6.5 MB/s setiap satu, kira-kira**1 fps**(kira-kira 1.3 fps apabila dipadatkan), di mana hos 10G menjalankannya pada 11–14. Jika kamera tidak dapat diatur pada kelajuan begitu perlahan — sambungkan bacaan julat GevSCPD setiap kamera untuk mengetahui — susunan akan beralih kepada emit bersiri (`slip-emit-and-capture`): kamera-kamera bergilir-gilir menggunakan talian pada kadar pautan penuh, dicetuskan satu demi satu, jadi setiap bingkai masih tiba tetapi ahli-ahli**tidak terdedah serentak**. `analyze-array` dan keluaran connect menamakan mod; pin `--force-tier sim-capture-sim-emit` untuk menolak fallback. Saiz bingkai tidak mempengaruhi perkongsian ini, jadi**binning dan ROI yang lebih kecil meningkatkan kadar bingkai tetapi tidak mengubah sama ada satu array muat**. Hanya wayar yang tidak dapat memuatkan susunan itu secara bersiri pun adalah**terlanggan lebih**— contohnya lebih banyak kamera daripada satu wayar boleh kendalikan, atau banyak kamera lain menstrim padanya — dan di situ simptomnya ialah kehilangan pekeliling GVSP, bukan kadar yang lebih rendah: `analyze-array` menetapkan nilai fps yang boleh dicapai kepada sifar dan mencetak `** OVER-SUBSCRIBED**`, sambungan diteruskan dengan amaran kuat, dan sambungan HTTP dengan `pin_resolution: true` (mod ketat) **menolak** sebaliknya. Penyelesaiannya ialah mengurangkan bilangan kamera, menggunakan bingkai jumbo dari hujung ke hujung, atau menggunakan NIC yang lebih pantas. `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED=1` menurunkan penolakan itu kepada amaran kuat untuk kerja makmal — lihat [Pembolehubah Persekitaran](#environment-variables).

### Kesihatan tatasusunan — subsistem mana yang kehilangan bingkai

`GET /api/camera/array/<array_id>/capability` pada tatasusunan yang bersambung membawa blok `health` langsung, yang dinilai semula pada tetingkap **10 saat** berterusan. Ia memisahkan kehilangan bingkai kepada dua punca yang memerlukan pembetulan bertentangan, bukannya melaporkan satu kadar "tidak lengkap" yang tidak menyatakan kedua-duanya:

| Medan | Apa maksudnya | Subsistem mana |
| --- | --- | --- |
| `gvsp_corrupt_rate_pct` (per serial) | Bingkai **telah tiba dan bermasalah secara struktur**— Kehilangan pekeliling GVSP. |**Rangkaian**: bajet wayar, pacing, cincin penerima NIC, MTU |
| `never_arrived_rate_pct` (per serial) | Bingkai **tidak pernah sampai langsung**— kamera tidak mencetuskan, atau tiada apa-apa yang keluar daripadanya. |**Pemicu / selari**: kabel M8, `--line`, `TriggerMode` |
| `worst_gvsp_corrupt_pct` / `worst_never_arrived_pct` | Kadar terburuk bagi setiap kamera. | — |
| `per_cam_rate_pct` | Kadar tidak lengkap gabungan bagi setiap kamera (kedua-dua punca digabungkan). | — |
| `stable_for_seconds` | Berapa lama setiap kamera kekal di bawah 0.01 %. | — |

Melebihi 5%, backend merekodkan baris `[array-health <id>] WARN` yang menamakan perpecahan — pada pelanggaran pertama, pada perubahan jalur keterukan, sekali setiap minit selagi ia berterusan, dan sekali apabila ia reda. Separuh yang rosak menulis `[gvsp-corrupt <SN>]` ke dalam fail log backend pada
rekod pertama bagi setiap kamera dan sebab, kemudian pengagregatan setiap 60 saat; panel log aplikasi menunjukkan
baris `[array-health <id>]` sebaliknya. Setiap penilaian masih dimasukkan ke dalam fail log belakang; pengira meneruskan pada setiap tampanan tanpa mengira apa yang dicetak.

Rekod yang sama melaporkan nombor yang menjadi asas keseluruhan peruntukan:

| Medan | Apa maksudnya |
| --- | --- |
| `wire_ceiling_mbps` | Belanjawan jalur hos yang berterusan, MB/s. |
| `wire_ceiling_source` | Daripada mana nombor itu datang, dalam kata-kata — contohnya `USB-capped 200 MB/s (was theoretical 1062; PnPDeviceID=USB\VID_0BDA&PID_815A)` (pada Linux x86_64, `USB-capped 400 MB/s (…; sysfs: bus=usb (usb2/2-1/2-1:1.0))`) atau `user override 120 MB/s (auto said 200)`. |
| `wire_ceiling_is_user_set` | `true` apabila `--wire-ceiling-mbps` (atau medan **Wire Budget** GUI) menetapkannya. |
| `nic_is_usb` | `true` untuk penyesuai Ethernet USB — lihat had USB di atas. |

**Membacanya:** `gvsp_corrupt_rate_pct` bukan sifar dengan `never_arrived_rate_pct` pada 0
bermakna pencetus dan selari kabel adalah sempurna dan 100% kehilangan adalah pada laluan rangkaian — turunkan `--wire-ceiling-mbps` dan sambung semula. Corak sebaliknya menunjukkan pada kabel selari atau talian pencetus sebaliknya.

> **`--target-fps` bukan pemicu untuk bingkai yang rosak.** Pacing GevSCPD ditulis sekali semasa sambungan, jadi menurunkan kadar pencetus mengubah kitaran tugas dan bukannya kadar letupan siaran serentak. Pemotongan permintaan 5× yang diukur tidak memberikan sebarang penambahbaikan;
> menurunkan had wayar dari 240 ke 200 MB/s menjadikan rig yang sama berkurang daripada 10.4 % rosak kepada 0.00 %.

> **Susunan yang sedang berjalan tidak dapat merancang semula dirinya pada firmware LATTICE (M3C / M3M).** Ia
> tidak dapat membetulkannya sendiri; putuskan sambungan dan sambung semula supaya pemilih masa-sambungan boleh
> merancang semula dengan had baru.

### Gejala → penyelesaian

| Gejala (Tetapan Susunan / sambungan / `analyze_array_network`) | Punca | Penyelesaian |
| --- | --- | --- |
| `FRAMES WILL DROP … exceeds NIC RX ring usable capacity 0.26 MB`, `Reduce ROI to enable` | `ReceiveBufferLen` ditetapkan semula kepada 32 (biasanya selepas kemas kini pemacu) | Tetapkan `ReceiveBufferLen`→256, `PendingReceives`→64; buka semula panel (mulakan semula backend jika ia menyimpan saiz cincin lama dalam cache) |
| But semula/tutup tergantung; kemudian ralat WMI `Invalid class`, NIC tidak dapat diaktifkan, pemacu USB hilang | Pemandu Realtek USB 10GbE lama 2016 → BSOD `0x9F` → pemadaman paksa | Kemas kini pemandu penyesuai kepada ≥ v10.67 (2026), kemudian terapkan semula tetapan receive-ring di atas |
| Susunan menyambung pada sensor penuh tetapi kadar bingkai rendah | Auto mengekalkan sensor penuh dan membiarkan kadar jatuh mengikut apa yang boleh dibawa oleh talian | Pilih salah satu pilihan yang lebih pantas yang disenaraikan oleh output sambungan (`--binning 2` / `4`, atau XPROT yang lebih kecilX002529/`--height`), aktifkan bingkai jumbo, atau naik taraf pautan |
| Susunan melaporkan fps sasaran yang sihat tetapi hanya menyampaikan sebahagian daripadanya; `health.gvsp_corrupt_rate_pct` bukan sifar, `never_arrived_rate_pct` 0 | Belanjawan wayar hos yang dianggarkan melebihi apa yang sebenarnya dapat disokong (biasanya pada penyesuai Ethernet USB, laluan PCIe nipis, atau fabrik kongsi) | Sambung semula dengan `--wire-ceiling-mbps` yang lebih rendah dan semak semula blok kesihatan. **Bukan** `--target-fps` — penalaan GevSCPD ditetapkan semasa sambungan |
| Linux: satu susunan kehilangan kumpulan bingkai dan menurunkan kadar bingkainya sementara NIC melaporkan tiada kehilangan; `lattice network` menyenaraikan `socket receive buffers too small` | Buffer penerimaan soket kernel berada pada tetapan lalai, jadi paket kamera dibuang dalam kernel | Tingkatkan `net.core.rmem_max` / `rmem_default` kepada 32 MB — lihat [hos Linux](#linux-hosts--socket-receive-buffers). Fail .deb melakukan ini semasa pemasangan |
| Kamera hilang daripada kumpulan yang diterbitkan; `health.never_arrived_rate_pct` bukan sifar, `gvsp_corrupt_rate_pct` 0 | Laluan pencetus/sinkronisasi — kamera tidak mencetus, bukan masalah rangkaian | Periksa kabel sinkronisasi M8 dan `--line`; pastikan setiap ahli berada dalam keadaan bersedia (`TriggerMode=On`) |
| `**OVER-SUBSCRIBED**` / `Wire budget` melebihi dalam `analyze-array`, atau penolakan sambungan dalam mod ketat (`pin_resolution`; `array over-subscribes the wire`) | Kabel tidak dapat membawa rangkaian pada apa jua kadar yang dicapai oleh kamera, sama ada serentak atau bergilir — jauh lebih banyak kamera daripada yang boleh dikongsi oleh kabel, atau kamera lain yang sedang mengalir padanya | Kurangkan bilangan kamera, gunakan bingkai jumbo dari hujung ke hujung, atau gunakan NIC yang lebih pantas. **ROI/binning TIDAK akan membantu** (pembahagian tidak bergantung pada saiz bingkai). `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED=1` diutamakan di makmal (menerima kehilangan paket) |
| Susunan disambungkan dalam `slip-emit-and-capture` (bersiri) pada rangkaian perlahan | Kamera tidak dapat diperlahankan cukup untuk berkongsi wayar serentak, jadi mereka bergilir-gilir; bingkai tidak terdedah pada masa yang sama | Bingkai jumbo atau NIC yang lebih pantas memulihkan tangkapan serentak; `--force-tier sim-capture-sim-emit` menegaskan perkara itu (dan kehilangan paket sebaliknya) |

---

## `chloros-cli daq`

Arahan penderia spektral. Perintah `pool-*` adalah klien HTTP ringan yang mengendalikan sensor melalui kolam kekal backend, jadi ia memerlukan backend yang sedang berjalan (lihat [Mulakan Backend](#start-the-backend)). Backend memiliki transport, jadi skrip CLI dan SDK berkongsi satu pemegang langsung daripada berebut port siri. `pool-discover` adalah satu-satunya cara untuk mengetahui MAC BLE DAQ-M.

> **`chloros-cli daq --help`** (atau `-h`, atau `daq help`) menyenaraikan subperintah `pool-*` (pada Linux, bersama-sama dengan perintah perkakasan terus yang disertakan dalam binaan), dan `chloros-cli daq <subcommand> --help` (atau `daq help <subcommand>`) memberikan pilihan bagi setiap satu. Mereka merangkumi sambung, stream, rakam fail `.daq` yang telah dikalibrasi, tukar profil cap dan pautkan DAQ kepada kamera, dan untuk DAQ-E, tetapan rangkaiannya, PTP, permulaan semula dan kemas kini firmware. DAQ juga boleh dikendalikan daripada Python dengan `chloros_sdk.connect_daq_sensor()`, yang menggunakan laluan berkongsi yang sama.
>
> **Gunakan arahan `pool-*` pada setiap platform.** Mereka berkelakuan sama pada Windows, Linux dan Jetson. Pada Windows, `daq discover` dan `daq list` adalah sinonim bagi `pool-discover`, dan mana-mana arahan `daq` yang bukan `pool-*` keluar daripada `1` dengan `DAQ direct-hardware commands require the full daq package`. Pada Windows,`daq discover` dan `daq list` adalah sinonim bagi `pool-discover`, dan mana-mana arahan `daq` yang bukan `pool-*`, `daq discover`, `daq list` dan `pool-*` yang lain, `daq`, `daq discover` dan `--only`. Pada Windows,`daq discover` dan `daq list` adalah sinonim bagi `pool-discover`, dan mana-mana arahan `daq` yang bukan `pool-*`, `daq discover`, `daq list` dan  `daq discover`, `daq list` dan arahan `daq` bukan `pool-*` yang lain adalah arahan langsung alat DAQperintah perkakasan: mereka membuka sensor dalam proses CLI sendiri, di luar kolam backend (jadi bukan pada sensor yang dipegang oleh backend, GUI atau skrip), dan `daq discover` di sana mengambil `--only` dan `--timeout` tetapi bukan `--json`. Rujukan ini hanya mendokumenkan arahan `pool-*` sahaja.

### Aliran Kerja Sambungan Pertama Sensor DAQ

```bash
# 1. Scan every transport, showing the address to connect with.
#    This is how you find a DAQ-M's BLE MAC — unlike a DAQ-E hostname or a
#    DAQ-U serial port, a MAC isn't printed on the device or listed by the OS.
chloros-cli daq pool-discover
chloros-cli daq pool-discover --only ble           # BLE only
chloros-cli daq pool-discover --json               # machine-readable

# 2. Open a persistent pool session (handle stays alive across CLI calls)
chloros-cli daq pool-connect                                   # smart-detect
chloros-cli daq pool-connect --port /dev/ttyUSB0               # DAQ-U on a serial port (Windows: COM3)
chloros-cli daq pool-connect --mac AA:BB:CC:DD:EE:FF           # DAQ-M by BLE MAC
chloros-cli daq pool-connect --eth-host daq-e-xxx.local        # DAQ-E by hostname

# 3. List what's in the pool, including the sensor_id you'll use next
#    (DAQ-U ids look like 'CB-7C-A8-2E-5F'; DAQ-E ids like 'daq-e-def330')
chloros-cli daq pool-list

# 4. Read the latest spectrum frame
chloros-cli daq pool-latest --sensor-id CB-7C-A8-2E-5F

#    Bind it to a camera, so that camera's captures use and save its readings
chloros-cli daq pool-bind --camera 213800234 CB-7C-A8-2E-5F

# 5. Record a calibrated .daq file for 60s
chloros-cli daq pool-record --sensor-id CB-7C-A8-2E-5F --duration 60 \
  -o ~/Documents/spectra --device-name "field-A"

# 6. Release
chloros-cli daq pool-disconnect --sensor-id CB-7C-A8-2E-5F
```

### Rujukan `pool-*`

| Subperintah | Tujuan |
| --- | --- |
| `daq pool-connect` (smart-detect) | Membuka sensor dalam kolam backend. |
| `daq pool-connect --port PORT` (`-p`) | DAQ-U pada port siri tertentu. |
| `daq pool-connect --ble` | DAQ-M melalui BLE, MAC diimbas secara automatik. |
| `daq pool-connect --mac MAC` (`-m`) | DAQ-M melalui BLE pada MAC yang diketahui (menyiratkan `--ble`). |
| `daq pool-connect --eth-host HOST` | DAQ-E melalui Ethernet pada nama hos atau IP yang diketahui (menunjukkan `--eth`). |
| `daq pool-connect --eth` | DAQ-E melalui Ethernet, hos dikesan secara automatik (mDNS + sokongan balik ARP; berfungsi daripada cache ARP kosong pada Windows dan Linux). |
| `daq pool-connect --integration-time MS (-t) --frame-avg N (-f) --no-ae` | Laras tetingkap integrasi / keadaan AE. |
| `daq pool-connect --no-stream` | Sambung tetapi jangan mula penstriman lagi (sambung dengan `pool-stream --start`). |
| `daq pool-connect --cap-id {none, fov_15, fov_30, fov_45, fov_60, fov_90, sunshine_cosine}` | Profil pembetulan kapasitansi. Lalai di backend ialah `sunshine_cosine`; `none` adalah sensor kosong. [DAQ-E-S](#daq-e-s--fixed-diffuser-and-tilt-sensor) mengabaikannya (penyebar tetap). **Penderia dihantar tanpa penutup, jadi namakan penutup yang anda pasang:** lalai direkodkan sebagai diandaikan (`cap_id_source=auto_default`) dan pemprosesan memberi amaran mengenainya, kerana DAQ-U kosong di bawah pembetulan cahaya matahari membaca kira-kira 30x lebih tinggi. |
| `daq pool-discover [--only usb,ble,eth] [--timeout SEC] [--json]` | Imbas setiap pengangkutan untuk sensor yang boleh anda sambungkan, tanpa menyambung. **Begitulah cara anda mencari MAC BLE DAQ-M.** `--timeout` adalah bagi setiap pengangkut (lalai 5 s, 1-20). Penderia yang sudah dibuka dalam kolam tidak disenaraikan — DAQ-M yang disambungkan berhenti mengiklankan — jadi gunakan `pool-list` untuknya. `usb` hanya menyenaraikan peranti siri USB: port siri komputer sendiri (`COM1`, `/dev/ttyS*` pada Linux) tidak boleh menjadi sensor dan tidak disertakan. |
| `daq pool-list` | Tunjukkan setiap sensor dalam kolam backend. |
| `daq pool-bind (--camera SERIAL \| --array ARRAY_ID) {SENSOR \| --clear}` | Ikatan kamera atau tatasusunan kepada DAQ, seperti pemilihan sensor cahaya dalam GUI: capture-nya menggunakan, memadankan dan menyimpan bacaan DAQ tersebut. `SENSOR` ialah ID sensor daripada `pool-list` atau alamat di mana DAQ itu diakses. `--clear` (tiada `SENSOR`) membatalkan pengikatan, dan tangkapan beralih semula kepada DAQ yang paling baharu. Pengikatan kekal sehingga backend dihidupkan semula, atau sehingga kamera, susunan atau DAQ itu diputuskan sambungan: pemutusan sambungan (di sini, daripada SDK atau dalam aplikasi) membatalkan semua pengikatan perkakasan yang diputuskan, tanpa mengira siapa yang menetapkannya, termasuk pengikatan ahli-ahli susunan dengan susunan itu. Kamera mesti disambungkan, dalam projek yang terbuka, atau dibuka pada komputer ini sebelum ini, dan susunan disambungkan atau dalam projek yang terbuka; mana-mana siri atau id lain ditolak mengikut nama dan tiada apa-apa yang diikat. |
| `daq pool-disconnect {--sensor-id ID \| --all}` | Pelepasan — sesiapa yang membuka sensor, jadi ia juga membebaskan sensor yang ditinggalkan oleh skrip yang rosak dalam kolam (sebarang rakaman padanya akan dihentikan terlebih dahulu). Sensor yang tidak berada dalam kolam tiada apa-apa untuk dilepaskan: ia mencetak `ID: already released (not in the pool)` dan keluar `0`; pelepasan yang ditolak atau tidak disahkan oleh backend keluar `1`. SDK: `DAQSensorSession.force_disconnect()`. |
| `daq pool-latest --sensor-id ID [--recent N] [--json]` | N bingkai spektrum terkini (lalai 1). |
| `daq pool-stream --sensor-id ID [--start \| --stop]` | Sambung / rehatkan penstriman. |
| `daq pool-record --sensor-id ID [--duration SEC] [-o DIR] [--device-name NAME] [--confirm-after S] [--heartbeat S]` / `--stop` | Mulakan / hentikan rakaman .daq. `-o` secara lalai menggunakan `~/Documents/DAQ Live View` pada komputer backend (jalur relatif berada di bawah folder tempat anda menjalankan arahan); `--duration` (`-d`, lalai `0` = sehingga `--stop`). `--confirm-after S` memberi amaran apabila tiada bacaan tiba S saat selepas permulaan (lalai 20; 0 untuk mematikan); dengan `--duration`, `--heartbeat S` mencetak kiraan bacaan setiap S saat dan memberi amaran apabila ia berhenti meningkat (lalai 30; 0 untuk mematikan). Rakaman pada DAQ-E yang menjadi senyap diteruskan dalam fail yang sama setelah backend menyambung semula, dan `--stop` menyenaraikan jurang ([A DAQ-E That Stops Delivering](#a-daq-e-that-stops-delivering)). Ctrl+C menamatkan rakaman `--duration` lebih awal dan melaporkannya; Tanpa `--duration` rakaman terus berjalan selepas Ctrl+C (keluar `130`) sehingga `--stop`. |
| `daq pool-set-cap --sensor-id ID --cap-id CAP` | Menukar profil pembetulan topi semasa runtime. `--cap-id` diperlukan; `--cap-id none` membuang pembetulan topi (sensor kosong). DAQ-E-S mengekalkan `as_recorded` apa sahaja yang ditetapkan. |
| `daq pool-enable-ptp --sensor-id ID` | Hidupkan semula PTP untuk DAQ-E yang PTP-nya dimatikan. Tetapan disimpan pada unit, sehingga unit yang dilumpuhkan kekal dilumpuhkan — cap waktunya tidak diselaraskan dengan PTP — sehingga ia dijalankan. `pool-list` menandakan unit seperti itu (`!! PTP is switched OFF`) dan mencetak arahan ini untuknya. Chloros tidak pernah menghidupkan semula unit secara sendiri, kerana matikan boleh disengajakan. |
| `daq pool-info --sensor-id ID [--json]` | Tetapan DAQ-E sendiri: firmware-nya (dan sama ada keluaran Chloros ini membawa versi yang lebih baru), alamatnya dan bagaimana ia memperolehinya (DHCP / pautan-lokal / statik), tetapan rangkaian yang disimpan sehingga sensor menjalankannya (satu disimpan termasuk `--no-reboot`), penstriman semasa kuasa-hidup, dan PTP. Pada sensor yang penstrimannya tidak sampai ke komputer ini, ia membaca sensor pada sambungan baharu dan memberitahu demikian. |
| `daq pool-set-network --sensor-id ID {--auto \| --static IP [--mask M] [--gateway G]} [--no-reboot] [--force] [--timeout S]` | Tetapkan DAQ-E kepada DHCP (`--auto`) atau alamat statik (`--mask` lalai `255.255.255.0`). Sensor itu dihidupkan semula untuk menerapkannya dan disambungkan semula dengan tetapan yang sama, menunggu sehingga `--timeout` saat (lalai 45) untuknya; `--no-reboot` hanya menyimpannya. Lihat [Penyetelan Peranti DAQ-E](#daq-e-device-settings). |
| `daq pool-set-auto-stream --sensor-id ID {--on \| --off}` | Sama ada DAQ-E memulakan penstriman sebaik kuasa dihidupkan tanpa perisian disambungkan (disimpan pada sensor). Chloros memulakan penstriman itu sendiri dalam apa jua keadaan. |
| `daq pool-reboot --sensor-id ID [--timeout S]` | Memulakan semula DAQ-E dan menyambungkannya semula dengan tetapan sebelumnya (menunggu sehingga `--timeout`, lalai 45 saat). |
| `daq pool-update-firmware --sensor-id ID [--timeout S]` | Flash firmware DAQ-E yang disertakan dengan keluaran Chloros ini ke dalam sensor yang menjalankan versi lama, kemudian sambungkan semula (kira-kira satu minit; jangan matikan kuasanya; `--timeout` lalai 45 saat untuk ia kembali). Sensor yang sudah terkini dibiarkan begitu sahaja. |

### DAQ-E Yang Berhenti Menghantar Data

Sebuah DAQ-E dalam kolam yang tidak menghantar sebarang spektrum selama 20 saat akan disambungkan semula oleh backend. Mematikan dan menghidupkan semula suis yang membekalkan kuasa kepada sensor melakukan perkara ini: sensor akan dihidup semula dengannya dan tidak akan menyalurkan data lagi sehingga diarahkan berbuat demikian (kecuali `pool-set-auto-stream --on`).

- **Ia memeriksa dahulu bahawa ia adalah sensor yang sama.** Ia bertanya pada alamat yang dimiliki oleh sensor sebelum ini, tanpa mengubah apa-apa, untuk mendapatkan nama, MAC dan nombor siri sensor. Ia juga bertanya di bawah nama `daq-e-<id>.local` sensor, tetapi hanya mempercayai jawapan itu apabila ia datang daripada penyesuai rangkaian (MAC) sensor itu sendiri. Tiada apa-apa yang lain yang menjawab akan disambung semula atau ditulis.
- **Ia menyambung semula dengan tetapan sesi:** masa integrasi, purata bingkai, pendedahan automatik dan profil cap seperti sedia kala, bukan lalai kuasa-hidup sensor. Sensor yang mempunyai PTP dihidupkan dan kembali dengan ia dimatikan akan dihidupkan semula. Kemudian ia memulakan semula aliran, dan penerima komputer untuknya.
- **Ia mencuba semula pada selang masa yang semakin panjang:** serta-merta, kemudian selepas 5, 10, 20 dan 40 saat, kemudian setiap 60 saat selagi sensor tiada. Setiap percubaan hanya bertanya kepada sensor siapa dirinya. Sensor yang menjawab tetapi masih tidak menghantar apa-apa selepas tiga kali penyambungan semula akan dibiarkan dan dilaporkan (`auto-reconnect GAVE UP`). `pool-stream --start` mencuba lagi.
- **`pool-list` menunjukkan kedudukannya.** Semasa sensor senyap, baris `STREAM FAULT` memberitahu apa yang sedang dilakukan oleh penyambungan semula. Baris dibersihkan dengan spektrum pertama, dan sensor kemudian membaca `reconnected automatically N time(s)`.
- **Rakaman diteruskan dalam `.daq` yang sama. Fail menandakan setiap jurang dengan dua baris: `event_type` 5 pada spektrum terakhir sebelum jurang dan 6 pada spektrum pertama selepasnya, masing-masing hanya dengan cap masa. `pool-record --duration` mencetak `!! STREAM LOST …` semasa sensor tiada dan `stream resumed after …` apabila ia kembali. `pool-record --stop` menyenaraikan setiap jurang dan memberi amaran, seperti yang dilakukannya untuk fail kosong (rekodan yang tidak mengandungi sebarang bacaan keluar `1`).
- **Satu rekodan mengekalkan satu penentukuran.** Jika sensor kembali dengan penentukuran yang berbeza semasa rekodan dijalankan, baki rekodan mengekalkan penentukuran asal, dan amaran henti akan menyatakan demikian. `pool-disconnect` + `pool-connect` mengambil yang baru.
- **Arahan anda diutamakan.** Sensor yang anda `pool-disconnect`, atau pauze dengan `pool-stream --stop`, tidak akan disambung semula, walaupun proses penyambungan semula sedang dijalankan.
- **`pool-info` pada sensor senyap** membaca sensor pada sambungan baharu dan menambah `!! stream NOT reaching this computer`. Sensor yang tidak menjawab dilaporkan serta-merta. `pool-list` tidak lagi memaparkan keadaan PTP untuk sensor yang telah berhenti menjawab.
- DAQ-U dan DAQ-M tidak dilindungi. DAQ-M menyambung semula secara automatik melalui Bluetooth, dan DAQ-U yang dicabut daripada USB memerlukan `pool-connect` semula.

### DAQ-E-S — penyebar tetap dan sensor condong

**DAQ-E-S** ialah DAQ-E dengan dua perbezaan yang penting bagi perisian. Segala perkara lain — penemuan, penyambungan, PTP, rakaman, tetapan peranti di bawah — berfungsi dengan tepat seperti untuk DAQ-E.**Penghamburnya bersifat kekal, jadi ia tidak memerlukan pembetulan penutup.** Penghambar gaya Sunshine terbina dalam dan telah dipasang pada unit semasa pengesahan kilang diukur, jadi pengesahan itu sudah membaca iradiasi pancaran ke bawah yang sebenar. Chloros menetapkan penutupnya kepada `as_recorded` (tiada profil) apa sahaja yang diminta: topi yang dinamakan untuknya (`sunshine_cosine`, `fov_*`, `none`) diabaikan dengan mesej, dan rakaman-rakamannya menyimpan `cap_id_source=model`, jadi amaran had yang diandaikan tidak pernah muncul untuknya. Jangan juga terapkan had pada datanya secara manual: `sunshine_cosine` di atas akan membaca kira-kira 11x lebih tinggi.

**Ia dilengkapi akselerometer 3-paksi.** Tindak balas kosinus penyebar cahaya matahari hanya sah selagi ia rata, jadi sensor yang condong melaporkan radiasi yang salah — dan pantulan mewarisi ralat tersebut — tanpa sebarang simptom lain. Setiap bacaan DAQ-E-S oleh itu membawa sikapnya:

| Medan | Maksud |
| --- | --- |
| `tilt_deg`, `roll_deg`, `pitch_deg` | Sudut dalam darjah. `None` apabila firmware menandakan sudut tidak ditakrifkan (kejatuhan bebas, paksi mati). |
| `x_mg`, `y_mg`, `z_mg`, `mag_mg` | Pecutan dalam mili-g. `mag_mg` jauh daripada ~1000 bermakna unit sedang bergerak dan sudut tidak bermakna untuk sampel tersebut. |
| `cal_applied` | Tetapkan: paksi dan sudut berada dalam bingkai diffuser (penentukuran pemasangan unit telah diterapkan). Nyah: bingkai papan mentah, yang boleh menunjukkan sehingga ~2.3° kecenderungan yang tidak sebenar. |
| `sample_age_ms` | Umur sampel akselerometer apabila spektrum dihantar. |
| `temp_raw` | Tren suhu die tanpa kalibrasi — hanya perbezaan, bukan bacaan termometer. |

`daq pool-latest --json` mengembalikannya di bawah kunci `imu` setiap bingkai (`null` pada DAQ-U, DAQ-M atau DAQ-E biasa). Satu `.daq` yang direkodkan daripada DAQ-E-S menyimpan nilai yang sama dalam lajur `imu_*`-nya (`imu_tilt_deg`, `imu_roll_deg`, `imu_pitch_deg`, `imu_mag_mg`, `imu_cal_applied`, …) di sebelah setiap spektrum, supaya pembaca dapat membezakan awan daripada tripod yang terjatuh. Pada setiap model lain, lajur-lajur itu kosong.

Aliran sikap dilaporkan secara berasingan: `daq pool-list` mencetak `!! ATTITUDE STREAM FAULT (spectra unaffected)` semasa ia terhenti, dan berapa banyak ralat yang dipulihkannya selepas itu. Ralat di situ tidak pernah menandakan jurang dalam rakaman spektral. `daq pool-discover` menyenaraikan unit sebagai `DAQ-E-S` (sebagai `DAQ-E` jika ia tidak menjawab semakan identiti dalam masa kira-kira 2 saat).

### Tetapan Peranti DAQ-E

```bash
chloros-cli daq pool-info --sensor-id daq-e-def330

# A fixed address on the network this computer is on (192.168.1.x)
chloros-cli daq pool-set-network --sensor-id daq-e-def330 \
  --static 192.168.1.50 --gateway 192.168.1.1

# Back to the factory setting: DHCP, falling back to a 169.254.x.x
# link-local address when no DHCP server answers
chloros-cli daq pool-set-network --sensor-id daq-e-def330 --auto

chloros-cli daq pool-update-firmware --sensor-id daq-e-def330
```

- **`--static` tanpa `--gateway` tidak menyimpan sebarang gateway** (sebarang gateway yang ditetapkan sebelum ini akan diabaikan), yang betul untuk rangkaian kamera tanpa penghala.
- **Alamat statik mesti berada di rangkaian komputer ini.** Chloros menolak subnet yang tidak memasukkan alamat komputer ini di rangkaian sensor: selepas but semula, tiada apa-apa yang boleh mencapai sensor untuk menukarnya kembali. Luluskan `--force` hanya apabila komputer ini akan dialamatkan semula, atau dirut, ke subnet baru.
- **Butiran semula ditolak semasa penderia sedang merakam.** Hentikan rakaman terlebih dahulu (`pool-record --stop`). Rakaman yang dimulakan seketika sebelum but semula akan ditutup olehnya, dan `pool-set-network`, `pool-reboot` dan `pool-update-firmware` diakhiri dengan baris `Warning:` yang menamakan fail rakaman tersebut (juga apabila sensor kemudian tidak kembali).
- **Selepas but semula, Chloros hanya menyambung semula sensor itu sendiri.** Ia menunggu di lokasi sensor -- dan selepas `--auto` juga di bawah namanya `daq-e-<id>.local` -- untuk jawapan dengan nama sensor, nama hos dan model, daripada penyesuai rangkaian (MAC) yang dimilikinya, dan menyambung semula di alamat yang menjawab. Jawapan di bawah nama itu hanya diterima dengan MAC tersebut, jadi daripada komputer yang mencapai sensor melalui penghala, atau daripada WSL2 dalam rangkaian NAT lalai, sensor yang dipindahkan DHCP ke alamat baru tidak disambung semula: perintah itu akan memberitahu demikian dan mencetak perintah `pool-connect --eth-host <its address> …` yang akan mengembalikannya dengan tetapan penutup dan pendedahan yang asal.
- **`pool-update-firmware` menolak sensor yang versi firmware-nya tidak dapat dibaca.** Sensor yang mengambil masa lebih lama daripada tempoh menunggu untuk kembali selepas kilat tidak dianggap gagal: sebaik sahaja ia menjawab, ia disambungkan semula dengan tetapan firmware baru diisi, dan arahan memberi amaran bahawa ia perlahan.
- **Kemas kini firmware memerlukan sambungan TCP masuk.** Sensor akan menyambung semula ke komputer ini untuk mengumpul imej. Chloros mendengar pada penyesuai yang menuju ke sensor (pada setiap penyesuai hanya apabila ia tidak dapat membezakan) dan hanya menyediakan alamat sensor itu sendiri; mana-mana sambungan lain ditutup tanpa dilayan. Pada Windows, peraturan firewall `Chloros Backend (TCP-In)` pemasang membenarkannya masuk (lihat [Windows (.exe)](#windows-exe)); firewall yang menyekatnya akan menyebabkan kemas kini gagal dengan `… device never connected back on TCP <port> …`.
- **Di bawah WSL2, gunakan rangkaian cerminan** (`networkingMode=mirrored` dalam `%UserProfile%\.wslconfig`). Dalam mod NAT lalai, alamat komputer ini di rangkaian sensor adalah milik WSL sendiri, jadi setiap alamat statik ditolak (kecuali `--force`), siaran langsung tidak sampai ke WSL, dan kemas kini firmware tidak dapat diselesaikan, kerana sensor menyambung semula ke komputer untuk menerima imej.
- **ID sensor dan nama hos tidak berubah.** Apa pun alamatnya, DAQ-E masih `daq-e-<id>` dan `daq-e-<id>.local`, jadi `--eth-host daq-e-<id>.local` terus menemuinya.
- **GUI mempunyai tetapan yang sama**: tab Penderia Cahaya, tetapan penderia (gigi gear), Rangkaian. GUI mengekalkan sambungan sendiri ke penderia, jadi ubah penderia yang dibuka oleh GUI daripada GUI itu: butaan semula daripada CLI menamatkan sesi GUI dengannya.
- Setara SDK ialah `DAQSensorSession.device_info()`, `set_network()`, `set_auto_stream()`, `reboot()` dan `update_firmware()`.

---

## `chloros-cli project`

Buka, sambungkan ke, dan kendalikan projek Chloros yang disimpan (sebuah folder dengan `cameras.json` + `sensors.json` + `project.json`). Segala-galanya dialihkan melalui backend supaya GUI dan CLI menghasilkan keadaan perkakasan yang sama.

### Rujukan Subperintah

| Subperintah | Tujuan |
| --- | --- |
| `project open PATH [--relink FOLDER]` | Cetak manif peranti projek (kamera, susunan, penderia), baca daripada failnya, dan semak fail sumbernya (lihat [Projek yang dipindahkan](#a-moved-project---relink)). Ia tidak membuka projek di backend: projek yang dibuka di sana, dan peranti serta rakaman-rakamannya, kekal seperti sedia ada. Keluar `1` apabila tiada fail sumber projek dapat ditemui. |
| `project devices PATH [--reconnect]` | Senaraikan atau jalankan semula penemuan. |
| `project connect PATH [--cameras-only] [--sensors-only] [--packed {auto,on,off}]` | Sambungkan setiap kamera / susunan / sensor yang disimpan. Setiap susunan disambungkan seperti yang dilakukan oleh `lattice array-connect`: sehingga 600 saat, langkah-langkahnya dicetak ke stderr (`[array <id>]    <s> s: <step>`; stdout adalah laporan JSON), dan sebarang rangkaian yang tidak disambungkan dalam tempoh itu akan dibatalkan — kameranya dikembalikan seperti asal — dan dilaporkan di bawah `errors`, tidak pernah dibiarkan bersambung. `--packed` menetapkan pengangkutan 12-bit terbungkus bagi setiap rangkaian (default `auto`; projek tidak menyimpan sebarang tetapan per-array; lihat [Penghantaran 12-bit Terpek](#packed-12-bit-transport---packed))Setiap penderia cahaya dibuka dalam kolam DAQ backend pada alamat simpanannya dengan masa integrasi, purata bingkai, pendedahan automatik dan penutup yang disimpan: sensor tanpa cap yang dipilih mendapat `sunshine_cosine`, satu cap yang disimpan (`none`) kekal tidak terpasang, dan DAQ-E-S tidak pernah mengambil cap. Panel DAQ GUI bukan sebahagian daripada kolam: tutup sensor di sana terlebih dahulu. |
| `project capture PATH NAME [-o DIR] [--format FMT] [--exposure US] [--gain DB] [--prefix P] [--orient]` | Pengambilan tunggal daripada kamera atau tatasusunan bernama (`--prefix` lalai `capture`). `--orient` (susunan): susunan menyimpan data mentah, yang kekal dalam format asal — ini merekodkan bahawa produk `process` yang dibuat daripadanya hendaklah diorientasikan, seperti `array-capture --orient`. Lalai: asal. `project burst` / `stream` dan `capture` / `burst` / `stream` (`orient: true`) mengambil pilihan yang sama. |
| `project burst PATH NAME [-n N] [-i S] [-o DIR] [--format FMT] [--exposure US] [--gain DB] [--prefix P] [--orient]` | ledakan N-bingkai daripada kamera atau susunan bernama (`-n/--count` lalai 5; `-i/--interval` saat antara bingkai, lalai 0). Siri burst array menyingkirkan duplikat kumpulan yang diselaraskan berulang (pengawasan ketinggalan) supaya susunan separa kitaran tidak dapat mengembalikan N salinan satu bingkai; mencetak keputusan bagi setiap iterasi. Rakaman yang gagal di tengah proses akan menghentikan burst dan keluar daripada `1`, dengan memberitahu berapa banyak yang telah disimpan sebelum itu. |
| `project stream PATH NAME [-n N] [--fps F] [-o DIR] [--format FMT] [--exposure US] [--gain DB] [--orient]` | Rakaman ke cakera dalam gelung: `--fps F` merakam satu bingkai kedua (lalai 3), satu bingkai setiap tangkapan daripada kamera, satu kumpulan bingkai diselaraskan daripada susunan (dengan pengawasan ketinggalan yang sama seperti `project burst`), sehingga `-n N` menangkap (default `0` = sehingga Ctrl+C, yang mencetak berapa banyak yang telah disimpan dan keluar `0`, atau `1` apabila tiada yang disimpan atau fail gagal; Ctrl+C sebelum `-n N` menangkap akan menamatkan `130`). Penangkapan yang gagal separuh jalan menghentikan aliran dan keluar dari `1`, memberitahu berapa banyak bingkai telah disimpan sebelum itu. `--poll-interval` telah ditinggalkan dan diabaikan: ia masih diterima supaya skrip lama boleh dijalankan, dengan nota pada stderr. |
| `project sensor read PATH NAME [--json]` | Bingkai spektrum terkini. |
| `project sensor log PATH NAME [--seconds SEC] [-o DIR] [--device-name NAME]` | Rakam .daq untuk `--seconds` (lalai 10). Rakaman yang tidak mengandungi sebarang bacaan dilaporkan pada stderr dan `1` keluar. |
| `project run PATH RECIPE.yaml` | Jalankan resipi tangkapan YAML/JSON. `--dry-run` mengesahkan tanpa dijalankan. |
| `project align calibrate PATH NAME [--method M] [--model M] [--frames N] [--reference SN] [--max-features N] [--ratio-threshold F] [--ransac-threshold-px F] [--min-matches N] [--max-reproj-err-px F] [--checkerboard RxC] [--name PROFILE]` | Mengira penjajaran untuk satu tatasusunan — lihat [jadual bendera di bawah](#project-align-calibrate-options). |
| `project align status PATH NAME [--json]` | Cetak profil penjajaran semasa. |
| `project align clear PATH NAME` | Buang profil yang disimpan. |
| `project align tweak PATH NAME --serial SN [--dx N] [--dy N] [--rotation-deg N] [--scale N]` | Gesa penukaran satu hamba (default `0`, `0`, `0`, `1.0`: tiada perubahan). |
| `project align export PATH NAME --to FILE` | Simpan profil ke dalam JSON. |
| `project align import PATH NAME --from FILE [--no-validate]` | Muat profil yang disimpan. |

#### Pilihan `project align calibrate`

| Penanda | Lalai | Keterangan |
| --- | --- | --- |
| `--method {feature_orb, feature_akaze, phase_correlation, checkerboard, manual}` | `feature_orb` | Kaedah penjajaran. **Ejaan ini berbeza daripada `lattice align-calibrate`**, yang menggunakan bentuk ringkas `orb` / `akaze` / `phase`; kedua-dua arahan ini tidak boleh ditukar pada bendera ini. |
| `--model {translation, rigid, affine, homography}` | `affine` | Menukar model untuk menyesuaikan. |
| `--frames N` | `1` | Snek bingkai diselaraskan ke purata. |
| `--reference SN` | tuan | Rujuk siri kamera; setiap ahli lain diwarp ke atasnya. |
| `--max-features N` | `5000` | Had bilangan ciri ORB. |
| `--ratio-threshold F` | `0.75` | Lowe'ujian nisbah. |
| `--ransac-threshold-px F` | `3.0` | Ambang inlier RANSAC. |
| `--min-matches N` | `15` | **Pagar kualiti** — tolak penyelesaian di bawah bilangan padanan inlier sebanyak ini. |
| `--max-reproj-err-px F` | `4.0` | **Pagar kualiti** — tolak penyelesaian yang melebihi ralat reprojeksi RMS ini. |
| `--checkerboard RxC` | — | Geometri papan untuk `--method checkerboard`, contohnya `9x6`. |
| `--name PROFILE` | kosong | Nama profil disematkan dalam JSON yang disimpan. **Bukan nama array** — itu adalah `NAME` berposisi. |

Dua pintu kualiti adalah sebab mengapa kalibrasi boleh berjaya menyelesaikan dan masih menolak untuk menyimpan: profil yang gagal pada salah satu daripadanya akan secara senyap menyalahdaftar setiap tangkapan kemudian, jadi ia ditolak daripada disimpan.

### Projek yang dipindahkan (`--relink`)

`project.json` bagi projek menamakan setiap fail sumber -- setiap imej dan rakaman penderia cahaya -- mengikut laluan penuhnya. Pindahkan folder projek, atau keseluruhan folder projek ke pemacu lain, dan laluan-laluan tersebut masih menamakan tempat lama.

Membuka projek membaiki apa yang boleh dilakukan sendiri: fail yang berada di dalam folder projek akan dicari dalam folder di mana projek itu berada sekarang, dan setiap laluan yang ditemui di sana akan dialihkan semula dan disimpan (`project.json` pertama kali disandarkan sebagai
`project.json.before-relink-<date>-<time>`). Ini berlaku pada setiap kali dibuka -- aplikasi,
`chloros-cli project open`, `chloros-cli process` ke dalam projek sedia ada, SDK dan `chloros-mcp`. Laluan hanya akan dialihkan semula kepada fail yang wujud di situ.

Fail yang berada di luar folder projek -- sesi hub, import Tambah Folder -- tidak dapat ditemui dengan cara itu. Namakan folder tempat mereka berada sekarang:

```bash
chloros-cli project open "N:/Chloros Projects/Field_A" --relink "E:/backup/Hub Projects"
```

`--relink` mengambil folder ke mana fail-fail itu dipindahkan, atau mana-mana folder di atasnya: setiap folder fail yang hilang dicari di bawahnya berdasarkan nama folder belakangnya
(`Hub Projects/<session>/raw/<serial>`, kemudian `<session>/raw/<serial>`, ...), dan setiap
fail-fail folder diambil dari tempat yang menemui paling banyak daripadanya. `project open` mencetak apa yang telah diarahkan semula, berapa banyak fail sumber yang ditemui, dan folder-folder tempat baki fail berada. Beberapa masih hilang: amaran, keluar `0` -- projek dibuka dengan yang lain. Tiada ditemui: ralat menamakan folder-folder tersebut dan `--relink`, keluar `1`; backend juga
menolak untuk membuka projek sedemikian untuk `process` atau `project devices --reconnect` (HTTP 409 `PROJECT_SOURCES_MISSING`).

### Contoh

```bash
# Open a project and see what it knows about
chloros-cli project open "/home/user/Chloros Projects/Field_A"

# Its hub captures moved to another drive: point it at them
chloros-cli project open "/home/user/Chloros Projects/Field_A" --relink "/media/backup/Hub Projects"

# Connect everything saved in the project
chloros-cli project connect "/home/user/Chloros Projects/Field_A"

# Capture from a named camera (defined in cameras.json)
chloros-cli project capture "/home/user/Chloros Projects/Field_A" FrontLeft \
  -o output/ --format tiff

# Capture from a named array
chloros-cli project capture "/home/user/Chloros Projects/Field_A" main_rig \
  -o output/ --format tiff

# Capture with overrides
chloros-cli project capture "/home/user/Chloros Projects/Field_A" main_rig \
  --exposure 5000

# Read a spectrum
chloros-cli project sensor read "/home/user/Chloros Projects/Field_A" Sky --json

# Record a DAQ log
chloros-cli project sensor log "/home/user/Chloros Projects/Field_A" Sky \
  --seconds 120 -o ~/Documents/spectra/

# Align an array (live)
chloros-cli project align calibrate "/home/user/Chloros Projects/Field_A" main_rig
chloros-cli project align status "/home/user/Chloros Projects/Field_A" main_rig

# Run a recipe
chloros-cli project run "/home/user/Chloros Projects/Field_A" recipe.yaml
```

### DSL Resipi

`project run RECIPE.yaml` menerima fail YAML atau JSON yang menerangkan urutan tindakan:

```yaml
# recipe.yaml
overrides:
  cameras:
    FrontLeft:
      exposure_us: 5000
      target_brightness: 80

stop_on_error: true
actions:
  - apply:
      name: FrontLeft
      settings:
        auto_exposure: "Off"
        gain_db: 6.0
        auto_gain: "Off"
  - wait: 2s
  - capture:
      name: FrontLeft
      output: pose_a/
      format: tiff
  - stream:
      name: main_rig
      count: 60
      fps: 5
      output: stream/
  - burst:
      name: main_rig
      count: 10
      interval: 0.5
      output: burst_a/
      format: tiff
  - sensor:
      name: Sky
      action: read
```

Tindakan yang disokong: `apply`, `wait`, `capture`, `stream`, `burst`, `sensor`. `apply` mengambil kunci `cameras.json` `settings` — `exposure_us`, `auto_exposure`, `gain_db`, `auto_gain`, `target_brightness`, … (nama-nama lama `exposure_time`, `exposure_auto`, `gain`, `gain_auto` diterima sebagai nama lain). Pendedahan, penguatan atau sasaran yang ditetapkan sedemikian akan dikekalkan sebagai tetapan anda: kawalan pendedahan automatik Chloros (termasuk pendedahan yang diukur oleh DAQ) tidak akan mengubahnya. Pendedahan manual mengekalkan penguatan seperti sedia kala: dalam mod auto, kamera terus menyesuaikan penguatan (melalui seluruh julatnya) ke arah kecerahan sasaran sehingga anda menetapkan `gain_db` atau `auto_gain: "Off"`. `auto_exposure: "Continuous"` kemudian menyerahkan semula pendedahan kepada automasi, dan `target_brightness_auto: true` menyerahkan semula sasaran yang dipegang kepada sasaran automatik Chloros. Tindakan `burst` mengambil `name` (diperlukan), `count` (lalai 5), `interval` (saat, lalai 0), `output`, `format`, dan `settings` (bentuk tetapan per-kamera yang sama seperti `apply`); letupan tatasusunan menggunakan pengawas kumpulan disegerakkan semula yang sama seperti `project burst`.

A `capture`, Langkah `burst` atau `stream` secara lalai menjadi `output`. `output` relatif — termasuk lalai — diselesaikan terhadap folder di mana anda menjalankan `chloros-cli project run`, bukan folder fail resipi atau folder projek (dalam 1.2.0 ia berada di folder pemasangan backend).

Jalankan ia:

```bash
chloros-cli project run "/path/to/project" recipe.yaml

# Dry-run to validate without firing hardware
chloros-cli project run "/path/to/project" recipe.yaml --dry-run
```

**Setiap pelaksanaan diperiksa terlebih dahulu, terhadap projek.** Setiap tindakan mesti tepat salah satu daripada kata kerja di atas sebagai kuncinya — `- capture: {name: FrontLeft}`, bukan `- action: capture` atau `- type: capture` — dengan `name` yang dimiliki oleh `cameras.json` / `sensors.json` projek, sebuah kamera atau susunan untuk `apply` / `capture` / `burst` / `stream` dan sensor cahaya untuk `sensor`; `stream` memerlukan `count` > 0 dan `wait` memerlukan tempoh. Resipi dengan sebarang ralat seperti itu tidak menjalankan sebarang tindakan (`[recipe] error: …`, `[recipe] not run: …`, keluar `1`), walaupun dengan `stop_on_error: false`. Kunci yang tidak digunakan oleh langkah adalah `[recipe] warning:`, begitu juga setiap tatasusunan atau penderia cahaya yang dikendalikan oleh resipi yang tidak disambungkan sekarang. Resipi tidak pernah menyambungkan peranti: jalankan `chloros-cli project connect PATH` terlebih dahulu. `--dry-run` mencetak resipi, kemudian ralat dan amaran yang sama dan `[recipe] valid: …` / `NOT valid: …`, tidak menjalankan apa-apa, dan keluar `1` apabila resipi mempunyai ralat.

---

## Pembolehubah Alam Sekitar

| Pembolehubah | Kesan |
| --- | --- |
| `CHLOROS_BACKEND_URL` | Menimpa URL belakang (lalai `http://127.0.0.1:5000`) — **hanya diiktiraf oleh keluarga arahan `lattice`, `project`, dan `daq pool-*`** (`lattice network-analysis` menggunakan `--backend-url` sebaliknya). Perintah teras (`process`, `login`, `logout`, `status`, `export-status`, `time-sync`, `selftest`) menetapkan `http://127.0.0.1:<port>` dan mengabaikan pembolehubah ini (literal IPv4 memintas penalti ~2 s-per-permintaan Windows `localhost`→`::1`), jadi ia sentiasa menyasarkan mesin tempatan. |
| `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED` | Tetapkan dalam persekitaran backend. `1` menurunkan penolakan terlebih-langganan — satu susunan yang disambungkan dengan `pin_resolution`, atau [perintah multi-kamera sementara](#multi-camera-transient-sessions) (`error_code` `ARRAY_OVERSUBSCRIBED`) — kepada amaran kuat, menerima kehilangan pekeliling GVSP. Hanya untuk ujian — lihat [Model fps & burst Array](#array-fps--burst-model). |
| `CHLOROS_ARRAY_PACKED12` | Tetapkan dalam persekitaran backend: `on` atau `off` menetapkan lalai [pengangkutan 12-bit terbungkus](#packed-12-bit-transport---packed) untuk setiap array (default `auto`). `--packed` yang eksplisit masih diutamakan. |
| `CHLOROS_DISABLE_UPDATE_CHECK` | `1` (atau `true` / `yes`) mematikan [semakan kemas kini automatik](#chloros-cli-update) pada Linux CLI semasa permulaan. |
| `CHLOROS_CLI_MODE` | Ditetapkan oleh CLI itu sendiri; memberitahu backend untuk mengaktifkan pemprosesan selari. |
| `CHLOROS_GVSP_PROBE_FALLBACK` | `0` melangkau probe sandaran GVSP (Hanya keputusan ICMP). **Ini mematikan jumbo, ia tidak sekadar menenangkan log** — kamera hanya menjawab ping DF sehingga 1500 pada setiap laluan, jadi prob ini satu-satunya yang boleh mengesan jumbo. Menjimatkan ~1 s setiap kamera setiap sambungan; menelan kos ~1.45× had jalur jika rangkaian *boleh* membawa jumbo. SDK memberi amaran apabila anda menetapkannya. |
| `CHLOROS_GVSP_SESSION_REUSE` | Tetapkan dalam persekitaran backend: `0` mengukur saiz paket pada setiap sambungan dan bukannya sekali bagi setiap kamera bagi setiap sesi backend. |
| `CHLOROS_GVSP_PACKET_SIZE_FORCE` | Menetapkan saiz paket GVSP kepada N bait; melangkau pengesanan sepenuhnya. Utamakan per-perintah (`CHLOROS_GVSP_PACKET_SIZE_FORCE=9000 chloros-cli …`) berbanding menetapkannya secara kekal: saiz yang dipasang berhenti menyesuaikan diri dengan rangkaian di hadapannya, dan memasang 9000 pada laluan yang tidak dapat membawa paket jumbo menyebabkan **setiap** tangkapan tamat masa dengan `SC_ERR_TIMEOUT -1011`. |
| `CHLOROS_TRANSIENT_AE_SETTLE_S` | Tetapkan dalam persekitaran backend: bilangan saat maksimum untuk [perintah multi-kamera sementara](#multi-camera-transient-sessions), atau `lattice capture` pada kamera yang tiada apa-apa yang memegangnya, menghabiskan masa menetapkan pendedahan setiap kamera daripada adegan sebelum ia menyimpan bingkai (default `5`). `0` mematikannya: pengesanan automatik setiap kamera kemudian dijalankan dari bingkai pertama, bermula pada had pendedahan masing-masing. |
| `CHLOROS_LOG_DIR` | Tetapkan dalam persekitaran backend: folder ke mana log terperincinya dihantar. Default `%LOCALAPPDATA%\Chloros\logs` pada Windows, `~/.cache/chloros/logs` pada Linux (`$XDG_CACHE_HOME/chloros/logs` apabila ia ditetapkan). Perkhidmatan root .deb menetapkan `/var/log/chloros`, yang boleh dibaca oleh kumpulan `adm` — lihat [Menjalankan backend sebagai perkhidmatan](#running-the-backend-as-a-service-headless). |
| `TMPDIR` (Linux) | Tempat fail sementara diletakkan. CLI secara automatik menggunakan `/mnt/ssd/tmp` jika wujud. |

---

## Kod Keluar

| Kod | Maksud |
| --- | --- |
| `0` | Berjaya. |
| `1` | Kegagalan umum (kebanyakan ralat subperintah). Perintah tangkapan juga akan menamatkan `1` apabila mana-mana fail yang diminta untuk ditulis gagal ditulis, walaupun fail-fail lain berjaya disimpan, dan `array-burst` / `array-record` keluar `1` apabila perakam melaporkan ralat — lihat [Fail yang Gagal Ditulis](#files-that-fail-to-write). `process` juga keluar `1` apabila eksport sesuatu larian tidak dapat disahkan, atau apabila ia kehilangan imej, walaupun produk lain telah ditulis. |
| `2` | Ralat hujah (penanda yang tidak diketahui atau hilang; juga `time-sync set-priority` tanpa keutamaan). |
| `130` | Terhenti oleh Ctrl+C: mana-mana arahan yang dipotong — `lattice`, `project`, `daq` dan `process` sama (`process` selepas backend menghentikan larian, lihat [Nota `process`](#notes)). Perintah yang pemberhentian normalnya ialah Ctrl+C — `lattice array-capture --continuous` / `--interval`, `lattice viewer`, `lattice continuous`, `multi-stream`, `align-stream`, `array-record`, `array-burst`, dan `project stream` dengan `-n 0` — keluar berdasarkan apa yang dilakukannya sebaliknya: `0` apabila semuanya telah ditulis, `1` apabila apa-apa gagal. |
| `143` / `129` | Dibunuh oleh SIGTERM / SIGHUP (128 + isyarat), selepas pembersihan arahan dijalankan: kerja yang sedang berjalan dihentikan dan kameranya dikembalikan, larian pemprosesan dihentikan di backend. `lattice continuous` atau rakaman lain yang pemberhentian biasa adalah Ctrl+C turut keluar dari `143`, bukan `0`. |

`lattice network-analysis` keluar dari `1` untuk keputusan `needs_force_slip` dan `error`, dan `selftest` keluar daripada `1` melainkan semua tujuh pemeriksaan lulus.

---

## Petua Penyelesaian Masalah

- **"Log masuk diperlukan"** → Jalankan `chloros-cli login EMAIL PASSWORD` sekali pada mesin ini.
- **"Folder projek terlalu dalam untuk komputer ini"** → Laluan panjang Windows telah dilumpuhkan pada mesin ini (tetapan lalai Windows), jadi tiada laluan boleh melebihi 260 aksara, dan produk diletakkan sehingga kira-kira 130 aksara di bawah folder projek. Tiada apa-apa yang diproses. Mesej itu memberikan folder projek terpanjang yang sesuai: gunakan yang lebih pendek (`-o`, atau `chloros-cli set-project-folder`), atau aktifkan laluan panjang Windows dan mulakan semula Chloros. Pemasang Windows menawarkan ini sebagai "Benarkan laluan panjang Windows", yang dicentang secara lalai; jika tidak, gunakan Dasar Kumpulan: *Konfigurasi Komputer > Templat Pentadbiran > Sistem > Sistem Fail > Benarkan laluan panjang Win32*.
- **"backend tidak dapat dicapai"** → Mulakan aplikasi desktop Chloros (Windows), atau mulakan backend sendiri: `"C:\Program Files\Chloros\backend\chloros-backend.exe"` pada Windows (folder `backend` dalam folder pemasangan anda; ia tidak ada pada `PATH`), `chloros-backend` pada Linux. Atau semak `CHLOROS_BACKEND_URL` jika jauh.
- **perintah `lattice` gagal dengan "pemacu kamera LATTICE tidak ditemui"** → Backend tidak dapat memuatkan runtime kamera LATTICE yang dipasang dengannya. Setiap arahan `lattice` dijalankan pada backend; CLI itu sendiri tidak mempunyai pemacu kamera. Pasang semula Chloros: pemasang Windows dan .deb memasang runtime di samping backend.
- **Array connect / Tetapan Array memaparkan "FRAMES WILL DROP" atau "Reduce ROI to enable"** → Cincin penerimaan NIC hos terlalu kecil (biasanya ditetapkan semula kepada 32 selepas kemas kini pemacu NIC). Lihat [Host NIC Setup & Tuning](#host-nic-setup--tuning-lattice-arrays) — tetapkan `ReceiveBufferLen=256`, `PendingReceives=64`.
- **Mesin tergantung semasa but semula/tutup, kemudian WMI `Invalid class` / NIC tidak dapat diaktifkan / pemacu USB hilang** → Pemacu penyesuai USB 10GbE lapuk menyebabkan `DRIVER_POWER_STATE_FAILURE` (BSOD `0x9F`). Kemas kini pemacu penyesuai — lihat [Host NIC Setup & Tuning](#host-nic-setup--tuning-lattice-arrays).
- **Linux: rakaman milik root, jadi anda tidak boleh memadamnya, dan log backend tidak berada dalam `~/.cache` anda** → Backend berjalan sebagai root melalui `chloros-backend.service`. Tukar ke `chloros-backend@<user>.service`, yang menjalankannya sebagai anda — lihat [Menjalankan backend sebagai perkhidmatan](#running-the-backend-as-a-service-headless). Sehingga ketika itu, log perkhidmatan root berada di `/var/log/chloros` (boleh dibaca oleh kumpulan `adm`).
- **Linux: unit backend ialah `failed` dengan "Instance lain sudah berjalan" (status keluar 98)** → Backend lain sudah memegang port: hanya satu berjalan pada satu masa. Jika dua unit backend diaktifkan, simpan satu: `sudo systemctl disable --now <unit>` untuk yang tidak anda gunakan — lihat [Menjalankan backend sebagai perkhidmatan](#running-the-backend-as-a-service-headless). Jika hanya unit ini diaktifkan, backend yang dimulakan di luar systemd (oleh `chloros-backend`, `chloros-cli` atau skrip SDK) memegangnya: hentikan backend itu, kemudian `sudo systemctl start <unit>`.
- **Amaran pertukaran Jetson** → Tambah swap berasaskan fail; CLI mencetak arahan `fallocate` / `swapon` yang tepat.
- **"backend tidak dapat dicapai" daripada arahan `lattice`, `project` atau `daq`** → tiada backend yang sedang berjalan: arahan-arahan tersebut tidak pernah memulakannya. Jalankan `chloros-cli backend status`, dan lihat [Mulakan Backend](#mulakan-backend).
- **"`daq net ...` tidak tersedia di Chloros"** (Linux dan Jetson) → Sesetengah tetapan DAQ-E sendiri dikhaskan untuk perkhidmatan MAPIR. Tetapan rangkaian, permulaan semula dan kemas kini firmware adalah `pool-set-network`, `pool-reboot` dan `pool-update-firmware`; hubungi sokongan MAPIR untuk yang lain.
- **"Perintah DAQ terus ke perkakasan memerlukan pakej daq penuh"** (Windows) → Gunakan `pool-*`, yang mengendalikan sensor yang sama melalui backend, atau `chloros_sdk.connect_daq_sensor()` daripada Python.

---

## Lihat Juga

- [Rujukan Python SDK](sdk-reference.md) — setara berprogram bagi setiap arahan CLI.
- [Penderia Cahaya DAQ](https://mapir.gitbook.io/chloros/daq-light-sensors) dalam manual Chloros — penderia, topi dan julat kalibrasi mereka, rakaman, rangkaian DAQ-E.
- [Kamera LATTICE](https://mapir.gitbook.io/chloros/lattice-cameras/lattice) dalam manual Chloros — penyambungan, tetapan kamera, mod tangkapan, tatasusunan.
- Dokumen dalam talian: `https://mapir.gitbook.io/chloros/cli`
