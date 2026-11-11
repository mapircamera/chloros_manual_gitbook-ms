# Rujukan **Chloros Python**

**Versi:**

1.3.0**Dijana:**2026-07-29 19:19 ·**Disemak:** 2026-11-06**Pakej:** `chloros-sdk` (PyPI)**Kumpulan Sasaran:** Dioptimumkan untuk penggunaan LLM; boleh dibaca manusia.**Skop:** Setiap kelas, fungsi, dan pembantu awam yang didedahkan oleh `import chloros_sdk`, dengan contoh yang boleh disalin-tampal merangkumi pemprosesan imej, kawalan kamera tunggal, tatasusunan bersepadu, penderia DAQ, dan automasi projek, serta pelayan `chloros-mcp` untuk pembantu AI.

Jika anda hanya memerlukan perkara utama, lompat ke:
- [Pemasangan &amp; Mula Cepat](#installation)
- [Smart-Connect untuk Susunan LATTICE](#smart-connect-for-lattice-cameras)
- [Sesi Penderia DAQ](#daq-sensor-sessions)
- [Utiliti Kamera](#camera-utilities)
- [Susunan Transien, Profil Penyelarasan &amp; Indeks](#transient-arrays-alignment-profiles--indices)
- [Folder Projek &amp; Resipi](#project-folder--recipes)
- [Automasi Projek](#project-automation--chlorosproject)
- [Smart-AE / Smart-Capture](#smart-ae--smart-capture)
- [Rangka Langsung dalam Aliran Kerja Anda Sendiri](#live-frames-in-your-own-pipeline)
- [Pelayan MCP (Penolong AI)](#mcp-server-ai-assistants)

> **Diperubah dalam 1.3.0** (yang akan dikesan oleh skrip 1.2.0 sedia ada).
>
> **Perubahan yang mungkin perlu diuruskan oleh skrip 1.2.0:**
>
> - **Kecelakaan yang tidak disahkan oleh backend kini diangkat.** `CameraSession.disconnect()`, `ArraySession.disconnect()` / `force_disconnect()` pada satu susunan yang dibuka oleh sesi, `DAQSensorSession.disconnect()` / `force_disconnect()` pada sensor yang dibuka, dan `ChlorosProject.disconnect_all()` mencetuskan `ChlorosConnectError` apabila backend menolak pelepasan atau tidak mengesahkannya tepat pada masanya, kerana peranti mungkin masih dipegang. Pada 1.2.0 ia dikembalikan secara senyap. Keluarnya blok `with` masih tidak pernah memicu.
> - **Peranti yang dipinjam tidak akan pernah dipadamkan.** `with attach_array(...)` dan `disconnect()` yang eksplisit ke atasnya membiarkan array terus berjalan (dalam 1.2.0, meninggalkan blok itu memadamkannya, walaupun GUI memilikinya); panggil `force_disconnect()` apabila anda berhasrat untuk menamatkannya. Begitu juga, `connect_daq_sensor()` yang mendapati sensor sudah berada dalam kolam, termasuk smart-detect bendera-sifar, membiarkannya terbuka pada `disconnect()` dan apabila keluar daripada blok `with`-nya.
> - **PNG dan JPEG hanya mengambil produk `display` dan indeks.** `capture()` dengan `format="png"` / `"jpg"` (`ext=".png"` / `".jpg"`) dan tahap `radiance` atau `reflectance`, atau `processing="all"`, membangkitkan `ValueError` sebelum sebarang permintaan; `raw` / `debayered` 12-bit ditolak oleh backend. Lihat [PNG / JPEG](#png--jpeg-take-the-display-and-index-products-only).
> - **`connect_daq_sensor(sensor_id=...)` membuka sensor itu atau menaikkan** `ChlorosConnectError` yang menyenaraikan apa yang ditemuinya; ia tidak pernah beralih kepada sensor lain. Lihat [Pengangkutan Terpasang](#pinned-transport).
> - **`frame_stream()` membangkitkan ralat bukannya menjadi senyap:** `FrameFetchError` pada penolakan, tick seterusnya akan mengulang (bacaan pantulan tanpa sebarang DAQ disertakan), selepas 60 saat tanpa bingkai, dan selepas 60 saat tanpa pencetus baru. Lihat [Satu pencetus bagi setiap bacaan](#one-trigger-per-read--group_id).
> - **Tamat masa baca atau sambungan terputus mencetuskan `ChlorosConnectError`** daripada setiap panggilan smart-connect dan `analyze_array_network()`, di mana 1.2.0 mencetuskan timeout asas atau ralat sambungan.
> - **`connect_array()` menunggu 600 s secara lalai (`timeout`, 180 s dalam 1.2.0), dan sambungan yang gagal akan dibatalkan.**** Panggilan menghantar `timeout` ke backend, yang memulihkan setiap kamera seperti ketika ia ditemui dan tidak menyambung apa-apa apabila sambungan tidak dilakukan tepat pada masanya; panggilan itu membangkitkan `ChlorosConnectError` (HTTP 504 `CLIENT_DEADLINE_PASSED`). Dalam 1.2.0, sambungan yang dibuat sejurus selepas kamera dihidupkan boleh bertahan lebih lama daripada panggilan dan menyambungkan susunan itu selepas ia telah diangkat. `connect_all()` menunggu setiap susunan yang disimpan dengan cara yang sama (120 saat dalam 1.2.0). Lihat [`connect_array()` Signature](#connect_array-signature).
> - **`connect_array()` mengekalkan keseluruhan sensor.** Tanpa `width` / `height` / `binning` setiap kamera ditetapkan kepada sensor penuh dan kadar bingkai menurun kepada apa yang dihantar oleh hos dan rangkaian; 1.2.0 memampatkan atau menggabungkan bingkai untuk mengekalkan kadar. Lihat [Synchronized Array](#synchronized-array--arraysession-smart-prep).
> - **Format piksel Bayer pada kamera M3M (mono) adalah kedalamannya.** M3M hanya menawarkan format Mono, dan tetapan awal menamakan format Bayer: dalam 1.2.0 kamera menolak format itu dan mengekalkan formatnya sendiri manakala `settings.pixel_format` menamakan format Bayer. `LatticeCamera(preset=...)`, `apply_preset()`, `set_format()`, a `CameraSettings(pixel_format=...)`, `connect_camera(preset=...)` dan satu susunan&#x27;format yang dikongsi kini menetapkan format Mono pada kedalaman yang sama pada M3M: `"BayerRG8"` dan `high_speed` memberikan `Mono8`; `"BayerRG12"`, `high_quality` dan `triggered` menghasilkan `Mono12`; `cam.settings.pixel_format` menamakannya. Begitu juga sebaliknya: M3C tidak pernah dimasukkan ke dalam format Mono, jadi `"Mono12"` -- daripada mana-mana daripadanya, `connect_array(pixel_format=...)`, `set_settings(pixel_format=...)` atau format tersimpan satu siri -- memberikan `BayerRG12` pada M3C, mana-mana kamera yang menjadi tuan rumah siri itu. `CameraSettings` yang anda hantar mengekalkan namanya, jadi objek yang sama masih meminta kedalaman itu daripada setiap kamera dalam keluarganya sendiri. `connect_camera(preset=...)` mengekalkan format pratetap (1.2.0 menetapkan kamera kepada 12-bit selepas menerapkan pratetap), dan kamera yang dibuka pada pratetap tidak lagi menulis apa yang diambilnya ke dalam `PRESETS`. Sebarang format lain yang ditolak oleh kamera (salah satu daripadanya tiada dalam senarai `PixelFormat`, seperti `RGB8` pada M3M atau `BayerRG16` pada firmware tanpa sokongan) juga tidak dinamakan sebagai dirinya sendiri: selepas `LatticeCamera` connect, `apply_settings()` atau `set_format()`, `cam.settings.pixel_format` dan `packed_transport` menamakan format yang disalurkan oleh kamera, dan satu baris cetakan menamakan format yang ditolak.
> - **`analyze_array_network()` memproyeksikan apa yang connect terapkan:** `pixel_format=None` (dahulunya `"BayerRG8"`) bermaksud format-format yang ditetapkan oleh connect (Mono12 / BayerRG12), dan `packed="auto"` keputusan pengangkutan terbungkus, jadi fps yang dipetiknya boleh berbeza daripada 1.2.0&#x27;s. `pixel_format` yang anda lalui menghasilkan M3M pada kedalaman itu. Lihat [Pembantu Analisis Rangkaian](#network-analysis-helper).
> - **`CameraSession.capture()` mengembalikan `CaptureResult`** (masih satu senarai frame dicts) dengan `.skipped`, `.failed`, `.daq_files` dan `.daq_matches`. Satu `ArraySession.capture()` hasil juga membawa `.alignment` (sama ada pemutar selaian telah diterapkan, dan mengapa tidak) dan `.oriented`.
> - **Penangkapan tatasusunan diselaraskan secara lalai, seperti pada aplikasi.** `ArraySession.capture()`, `continuous()`, `capture_repeated()` dan `ArrayHandle.capture()` / `capture_smart()` memutarbelitkan `debayered`, `display` setiap ahli, `radiance`, `reflectance` dan fail indeks ke dalam bingkai kamera utama dan memotongnya mengikut tumpang tindih ahli-ahli (lebih kecil daripada sensor mengikut offset kamera, `XMP-mapir:AlignmentJSON` `"applied": true`), jadi ia adalah fail yang sama seperti tangkapan aplikasi Chloros untuk adegan yang sama; `raw` kekal pada saiz asal. Sebelum ini, tangkapan tanpa `aligned=True` menyimpan setiap fail pada saiz asal, tanpa diubah bentuk. `aligned=False` mengekalkan itu. `chloros-mcp`&#x27;`capture_array` dan `start_capture_run` menggunakan tetapan lalai yang sama. Lihat [Kaedah `ArraySession`](#arraysession-methods).
> - **`connect_all()` menamakan backend yang menolak SDK.** Backend yang sedang berjalan yang telah log keluar atau berada pada pelan di bawah Copper dilaporkan di bawah `errors` dengan sebabnya sendiri (HTTP 401 / 403), bukan sebagai &quot;tiada backend Chloros sedang berjalan&quot;, dan setiap panggilan `connect_all()` memeriksa backend semula, jadi memanggilnya selepas log masuk berfungsi pada objek projek yang sama.
> - **`output_dir` relatif berfungsi pada backend tempatan.** Laluan relatif dijadikan mutlak terhadap direktori kerja skrip anda sebelum dihantar. Dalam 1.2.0 backend menyelesaikannya dalam folder pemasangan sendiri, yang gagal dengan &quot;Akses ditolak&quot;.
> - **`numpy` kini merupakan keperluan** bagi SDK (panggilan bingkai langsung mengembalikan susunan numpy).
> - **Nama tahap eksport yang bukan salah satu daripadanya akan ditolak.** `processing=` dan `levels=` mengambil `raw`, `debayered`, `radiance`, `reflectance` atau `display` (atau nama lain seperti `preview`; `processing=` juga `"all"`). Sebarang nama lain ditolak sebelum kamera disentuh (HTTP 400 `UNKNOWN_LEVEL`: `capture()` menimbulkan `ChlorosConnectError`, `grab()` / `frame_stream()` `FrameFetchError`); 1.2.0 disimpan atau dikembalikan `raw` untuknya.
> - **Texture Aware ditolak untuk tangkapan LATTICE.** Pada projek yang mengandungi tangkapan LATTICE, `configure(debayer="Texture Aware (Slow, Highest Quality)")` mencetuskan `ChlorosConfigurationError`, dan begitu juga `process()` selepas `configure()` yang meminta untuknya sebelum import. Nilai Texture Aware yang disimpan oleh projek sebelum ini ditetapkan kepada Standard, dengan `UserWarning`. Rakaman LATTICE sentiasa mendapat demosaik standard, jadi versi 1.2.0 menerima tetapan itu dan mengabaikannya; aplikasi Chloros juga tidak menawarkannya untuk rakaman tersebut. Lihat [Nilai Disokong](#supported-values).
> - **Kamera dikembalikan seperti asal — melainkan `keep_settings=True`.** `ArraySession.disconnect()` / `force_disconnect()`, penghujung setiap fungsi sementara (`capture_cameras_transient()`, `start_multi_camera_stream()`, `test_array_sync()`, `calibrate_alignment_profile()`, `capture_aligned_multiband()`, `start_aligned_stream()`, `capture_live_index()`) dan pemberhentian `start_camera_continuous()` mengembalikan setiap kamera seperti sedia kala: pendawaian pencetus dan talian penyegerakan, PTP, dan setiap tetapan kamera yang diubah oleh sesi (pendedahan dan penguatan, sasaran AE, had dan peredaman, imbangan putih, paras hitam, format piksel, binning dan ROI, pembalikan, kadar bingkai). Sebaliknya, `keep_settings=True` menyimpan tetapan setiap kamera ke dalam memori kamera itu sendiri, supaya ia hidup dengan tetapan tersebut; `clear_settings=True` menetapkan semula memori itu kepada tetapan kilang. Dalam kedua-dua kes, kamera dibiarkan berjalan bebas. Satu projek menetapkan kameranya daripada tetapan simpanannya sendiri apabila ia menyambungkannya. Lihat [Memori kamera](#camera-memory--keep_settings--clear_settings).
> - **Setiap mod menyimpan projek dengan cara yang sama.** Sebarang perubahan pada kamera, tatasusunan atau penderia cahaya yang disambungkan di bawah projek — daripada SDK, aplikasi, CLI atau pelayan MCP — disimpan ke dalam `cameras.json` / projek tersebut `sensors.json`, dan membuka projek dalam mana-mana mod menetapkan peranti daripadanya; satu `ChlorosProject` menamakan folder sendiri ke backend. Sebelum ini, hanya aplikasi menyimpan projek. Lihat [Satu rekod projek, setiap mod](#one-project-record-every-mode).
> - **Fail yang dibuat daripada beberapa kamera menamakan setiap satu.** Satu susunan multiband, satu indeks langsung yang dibuat daripada jalur beberapa kamera dan imej `composite/` tangkapan tatasusunan membawa siri, model dan pek penentukuran setiap kamera sumber sebagai senarai (`XMP-mapir:SourceCameraSerials`, `SourceCameraModels`, `SourceCalBundleSha256s`, …), dan EXIF `SerialNumber` menyenaraikan nombor siri, yang disambungkan dengan koma. Dahulu ia tidak mengandungi sebarang kamera, atau hanya nombor siri kamera pertama. Lihat [Fail yang dibuat daripada beberapa kamera](#files-made-from-several-cameras).
> - **Hanya fail `raw` mengandungi tag penentukuran kamera.** Set yang serasi dengan MicaSense (`BlackLevel`, `RadiometricCalibration`, perset `Band{R,G,B}RadiometricCalibration` berbilang saluran (`BlackLevel`, `RadiometricCalibration`, fail tangkapan `CalibrationJSON` sahaja. Setiap fail lain — set `MicaSenseVignetting*` dan muatan `raw` menerangkan nilai mentah penderia, jadi ia disertakan dalam `debayered`fail tangkapan  sahaja. Setiap fail lain — tangkapans `debayered`, `display`, `radiance`, `reflectance` dan fail indeks, rakaman berterusan dan berbilang kamera- bingkai aliran, dan setiap produk `process()` — membawa identiti kamera (`XMP-mapir:CameraSerial`, `CameraModel`, `FilterType`, `LensModel`, `CalBundleSha256`, ...) dan, pada tangkapan, rekod bingkai itu sendiri (`XMP-mapir:FrameJSON`: pendedahan, penguatan, suhu, format piksel, masa tangkapan). Dalam 1.2.0, ia turut menyertakan set mentah, yang mana alat yang menerapkannya (Pix4D, Metashape) akan diterapkan pada piksel yang bukan nilai mentah. Pemprosesan mencari penentukuran fail sedemikian melalui nombor siri dan `CalBundleSha256`.
>
> **Perubahan lain:**
>
> - **Kadar bingkai automatik bagi satu susunan akan stabil berdasarkan apa yang disalurkan oleh susunan tersebut.** Kadar yang kehilangan lebih daripada 1% pencetusnya akan diturunkan, dinilai berdasarkan beberapa sesi pandangan panjang dan bukannya satu, dan kadar asal akan dicuba semula sekali-sekala. Kadar yang anda tetapkan sendiri tidak akan diubah.
> - **Pendedahan automatik lebih stabil.** Ia mengekalkan pada cahaya yang konsisten, menstabilkan dalam beberapa saat selepas perubahan, mengabaikan sorotan kecil, dan mengembalikan kamera yang berhenti bertindak balas di bawah cahaya matahari yang terang. Lihat [Bagaimana Pendedahan Automatik Berfungsi](#how-auto-exposure-behaves).
> - **`import_images(recursive=True)` hanya mengira imej dan rakaman anda.** Folder yang mengandungi `camera_cal/` projek tidak lagi mengira fail penentukuran tersebut sebagai imej dan rakaman, dan projek yang telah mengambilnya sebelum ini akan membuangnya.
> - **`configure(radiometric_output=...)` terpakai.** Ia kini menetapkan produk `Export radiance` / `Export reflectance` projek; sebelum ini, tetapan produk setiap projek sendiri akan mengatasi tetapan ini, jadi `radiance`, `sensor-response` dan `all` semua mengeksport radiasi dan pantulan.
> - **`test_array_sync()` meneruskan daripada keadaan kuasa-hidup kamera.** Ia menghidupkan PTP kamera semasa pelaksanaan, menunggu jamnya terkunci dan stabil sebelum pencetus pertamanya (`ptp_lock`) — setiap kamera terkunci selama 120 saat, kira-kira 2 minit selepas dihidupkan (`ptp_settle_s=` menetapkan ia), kerana jam yang baru terkunci akan dimatikan sehingga ~200 us selama kira-kira 2 minit — dan menetapkan semula PTP setiap kamera mengikut keadaan asal. Kamera yang masih terkunci dari ujian sebelumnya tidak ditunggu lagi. Ia dahulu akan mematikan PTP pada rangkaian satu suis di mana tiada DAQ-E menjawab, dan membaca jam bebas setiap kamera sebagai jam PTP yang tidak akan terkunci (&quot;PTP tidak terkunci&quot;, setiap kali larian).
> - **Rakaman `.daq` menyatakan sama ada penutupnya dipilih atau diandaikan.** DAQ-U, DAQ-M dan DAQ-E dihantar tanpa penutup, dan Chloros tidak dapat melihat sama ada penutup dipasang, jadi sensor yang disambungkan tanpa penutup yang dinamakan menerima `sunshine_cosine` secara lalai, satu *andaian*. Setiap rakaman Chloros (skema 1.30) kini menyimpan `cap_id_source` di samping `cap_id`: `operator` apabila anda menamakan cap (termasuk `none`), `auto_default` apabila ia menjadi lalai. Memproses rakaman yang kapasitornya diandaikan, dengan kapasitor DAQ projek dibiarkan pada Auto, mencetak `[daq-dls] WARNING` yang menamakan fail: sebuah DAQ-U kosong di bawah pembetulan cahaya matahari menunjukkan bacaan kira-kira 30x tinggi. **Namakan penutup yang anda pasang** apabila anda menyambung (`connect_daq_sensor(cap_id=...)` atau `DAQSensorSession.set_cap()`, `chloros-cli daq pool-connect --cap-id` / `daq pool-set-cap`, atau daripada menu lungsur cap GUI), atau tetapkan cap DAQ projek, dan amaran itu akan hilang.
> - **Tiada lagi tetingkap konsol pip pada Windows.**Pada PC yang dipasang Python, backend dan setiap pekerja imejnya menjalankan `pip` sekali semasa permulaan, dan masing-masing membuka tetingkap konsol. Mereka tidak lagi berbuat demikian.
> - Rakaman `.daq` secara langsung akan terus berjalan walaupun berlaku pemadaman kuasa. Setiap rakaman ditulis dengan jurnal dan disinkronkan sepenuhnya pada cakera pada setiap komit, di belakang sensor, jadi cakera yang perlahan hanya akan menangguhkan fail dan tidak pernah menangguhkan bacaan. Rakaman yang terhenti di tengah-tengah commit (seperti pemadaman kuasa atau arahan kill) boleh meninggalkan fail `<name>.daq-journal` di sebelahnya:**salin kedua-duanya bersama.**Chloros akan menggulung semula jurnal pada kali pertama ia membaca fail, jadi rakaman dibuka dengan semua yang telah dikomit sebelum pemberhentian (kira-kira 3 saat terakhir hilang); dalam 1.2.0 pemadaman kuasa pada masa yang salah boleh menyebabkan keseluruhan fail tidak dapat dibaca (&quot;imej cakera pangkalan data rosak&quot;).
> - **`DAQSensorSession.record_start()` pada cakera yang sibuk tidak lagi menahan sensor**: bacaan-bacaan dibariskan dan ditulis pada utas tersendiri, dan bacaan yang tidak dapat ditulis akan dikira dalam `status()`&#x27;s `record_errors` (dengan `record_last_error`).
> - **Direct `LatticeCamera`:**satu bingkai tanpa pendedahan/penguatan ketulan kini melaporkan**sebenar** pendedahan dan penguatan kamera (baca balik sebelum ini mengambil nilai yang disimpan dalam cache yang boleh kekal beku sepanjang kitaran pendedahan automatik), dan `apply_settings(trigger_source='LineN')` / `set_trigger_source('LineN')` menerapkan penapisan deglitch input 5 µs yang sama seperti yang diperoleh oleh hamba kabel Chloros (tanpa penapisan, hamba akan mencetuskan bingkai tanpa penghantaran pencetus).
> - **Kamera yang dicetuskan oleh perisian tidak lagi terlepas setiap pencetus kedua.** Kamera dalam mod pencetus perisian kini menyusun pencetus yang tiba semasa bacaan keluarnya (atau Chloros menunggu sehingga ia siap), seperti yang telah dilakukan oleh kamera diselaraskan-kabel.
> - **`discover_daq_sensors()` menamakan DAQ-E-S `DAQ-E-S`.** `model` adalah `DAQ-E` untuk setiap penderia Ethernet; satu yang tidak menjawab semakan identiti dalam masa kira-kira 2 saat masih disenaraikan sebagai `DAQ-E`.
> - **Baharu: `chloros-mcp`, pelayan MCP untuk pembantu AI.** Claude Desktop, Claude Code, Cursor dan klien MCP lain boleh memproses imej (menubuhkan projek, mengimport tangkapan, memilih tetapan, memproses, melaporkan apa yang dieksport oleh larian) dan menjalankan kamera LATTICE, tatasusunan dan penderia cahaya DAQ: sambungkan, pratonton, tetapkan pendedahan dan warna, rakam, rekod. Alat-alatnya merangkumi apa yang dilakukan oleh SDK ini. Ia dipasang bersama SDK, dan .deb Linux meletakkannya dalam `/usr/bin`. Lihat [Pelayan MCP (Penolong AI)](#mcp-server-ai-assistants).
> - **Baharu: utiliti kamera**, arahan `chloros-cli lattice` sebagai panggilan SDK: profil warna, ketepuan / kontras, sorotan, imbangan putih (sekali ambil, otomatik, nisbah tetap), pratetap, model penapis, status dan kemas kini firmware, kuasa (probe, tidur, sedar), penentukuran sasaran, penyesuai rangkaian (semak, anggaran, baiki), pembetulan vignet pada fail, bacaan DLS, dan probe hos. Lihat [Utiliti Kamera](#camera-utilities).
> - **Baharu: baki CLI sebagai panggilan SDK**, jadi CLI, SDK dan `chloros-mcp` menawarkan ciri yang sama (CLI mengekalkan hanya log masuk, penyediaan DAQ-E dan pelayan DAQ langsung, untuk keselamatan): kamera dibuka untuk satu arahan (`start_camera_continuous()`, `start_multi_camera_stream()`, `capture_cameras_transient()`, `test_array_sync()`, `detect_array_master()`, `analyze_array_models()`), fail profil penjajaran (`calibrate_alignment_profile()`, `capture_aligned_multiband()`, `start_aligned_stream()`, `alignment_profile_info()`, `reorder_alignment_profile()`), indeks (`index_catalog()`, `compute_index()`, `capture_live_index()`), grandmaster PTP (`time_sync_status()` dan rakan-rakannya), folder projek (`project_folder()`, `set_project_folder()`, `reset_project_folder()`) dan resipi (`run_project_recipe()`, pelari CLI sendiri). Untuk sensor cahaya: `connect_daq_sensor(cap_id=...)`, `DAQSensorSession.set_cap()`, `daq_bindings()` / `set_daq_bindings()`, `daq_band_presets()` dan `light_sensor_reflectance()` (pantulan dan indeks Sekitar + Objek bagi dua penderia); untuk kamera: `CameraSession.status()` dan `capture_repeated()`; untuk satu susunan: `ArraySession.capability()` dan `set_member_settings()` (pendedahan, penguatan, ... satu ahli). Lihat [Transient Arrays, Alignment Profiles &amp; Indices](#transient-arrays-alignment-profiles--indices), [Project Folder &amp; Recipes](#project-folder--recipes) dan [DAQ Sensor Sessions](#daq-sensor-sessions).
> - **Produk indeks LATTICE yang dipertingkatkan** (NDVI dan indeks lain) daripada `process()`, dalam setiap mod pemprosesan.
> - **Kamera LATTICE OCN: produk sinaran, pantulan dan indeks yang dipertingkatkan dalam cahaya matahari.** Pembetulan armada digunakan selepas pencampuran semula jalur — secara langsung, dalam tangkapan dan dalam `process()`; kamera RGN kekal tidak berubah. Lihat [Keluaran Radiometrik](#radiometric-output-lattice-multispectral-pipeline).
> - **Setiap tangkapan menyimpan bacaan DAQ yang digunakan, seperti yang dilakukan oleh GUI.** `CameraSession.capture()` dan `ArraySession.capture()` / `continuous()` menulis `.daq` dan `.match.json` setiap kali DAQ diikat atau dikumpulkan, tangkapan hanya mentah disertakan; dalam 1.2.0 tangkapan mentah atau debayered disimpan hanya dengan `force_daq=True`. `daq=False` mematikannya dan `daq_tolerance_ms=` menetapkan toleransi padanan; kedua-dua keputusan menyatakan apa yang disimpan dalam `.daq_files` dan `.daq_matches`. Rakaman kini menunggu bacaan seterusnya oleh DAQ supaya ia dapat menggabungkan bacaan di kedua-dua belah pendedahan. Lihat [Bacaan DAQ yang disimpan oleh rakaman](#the-daq-reading-a-capture-saves--daq_files-and-daq_matches).
> - **`ArraySession` mempunyai bingkai langsung dan tangkapan berterusan di pihak pelayan.** `grab()` dan `frame_stream()` memulangkan bingkai numpy daripada satu pencetus (baca yang sama seperti `ArrayHandle.grab()` / `frame_stream()` dalam API projek), dan `continuous()` menyimpan setiap kumpulan diselaraskan di backend dan memulangkan a [`ContinuousHandle`](#continuoushandle). Lihat [Live Frames in Your Own Pipeline](#live-frames-in-your-own-pipeline).
> - **Susunan `grab()` dipin ke satu pencetus** (`ArrayHandle.grab()`, dan `ArraySession.grab()` baru). Dalam 1.2.0, 12 daripada 20 empat-kamera `ArrayHandle.grab()` membaca straddled dua trigger. Setiap bingkai kini membawa `group_id`, dan satu array `frame_stream()` menghasilkan setiap trigger sekali. Lihat [Satu pencetus bagi setiap bacaan](#one-trigger-per-read--group_id).
> - **Satu band dan bingkai selari:** `grab()` / `frame_stream()` mengambil `band=`, `align=`, `bit_depth=` dan `refl_max=` pada pemegang kamera dan pemegang array. Lihat [Mengeksport satu jalur](#exporting-a-single-band--band).
> - **Kaedah dan argumen `ArraySession` baru:** `set_target_fps()`, `force_disconnect()`, `capture(after_seq=..., orient=...)` (dengan `CaptureResult.group_seq` dan `.failed`), `continuous()`, `connect_array(packed=...)`; `capture_repeated()` mengikat kumpulan berturut-turut dan berhenti apabila pemacu penuh.
> - **`ArraySession.record()` merekod tanpa GUI dibuka.** Apabila pandangan gabungan GUI ditutup, backend memaparkan pandangan itu sendiri: susunanNDVI, atau indeks yang anda hantar sebagai `expr=`. `RecorderHandle.stop()` mencetuskan `ChlorosConnectError` untuk rakaman yang gagal, iaitu rakaman yang tidak menulis sebarang bingkai, dan mengekalkan keputusan henti pada `.result` (meninggalkan blok `with` tidak mencetuskan: semak `rec.result`). Lihat [`RecorderHandle`](#recorderhandle).
> - **Kaedah `DAQSensorSession` baru:** `enable_ptp()`, `force_disconnect()`, dan untuk tetapan DAQ-E sendiri `device_info()`, `set_network()`, `set_auto_stream()`, `reboot()` dan `update_firmware()`. `record_stop()` mengembalikan `warning` dan `gaps`. Pemulihan semula yang menutup rakaman menamakannya, dalam balasannya atau, apabila pemulihan semula gagal, pada `ChlorosConnectError` (`recording_closed`; lihat [DAQ-E Tetapan Peranti](#daq-e-device-settings)). Lihat [Kaedah `DAQSensorSession`](#daqsensorsession-methods).
> - **DAQ-E yang dikongsi yang berhenti penghantaran akan disambungkan semula oleh backend**, dengan tetapan yang sama seperti sebelum ini, dan rakaman padanya akan diteruskan dalam `.daq` yang sama dengan jurang ditandakan dalam fail; `record_stop()` memulangkan jurang sebagai `gaps`. Dalam 1.2.0 sensor itu menjadi senyap sehingga ia diputuskan sambungannya dan disambungkan semula, yang menamatkan rakaman. Lihat rujukan CLI [A DAQ-E That Stops Delivering](cli-reference.md#a-daq-e-that-stops-delivering).
> - **`open_project()` berfungsi daripada SDK yang dipasang.** Roda (pemasang dan PyPI) kini membawa pemuat projek yang diperlukan; sebelum ini, `open_project()` memanggil `ImportError` menamakan `project_cameras` / `project_sensors` dalam mana-mana SDK yang dipasang melalui pip. `connect_all()` menyambungkan kamera yang disimpan, susunan dan penderia cahaya melalui backend yang sedang berjalan (penderia cahaya melalui kolam DAQ-nya), dan peranti yang disimpan yang tiada cara untuk dicarinya disenaraikan di bawah `errors` dengan sebab, bukannya diabaikan tanpa sepatah kata. Susunan yang disimpan yang sudah aktif di backend (GUI, atau `chloros-cli project connect`) disambungkan dan terus dijalankan, sedangkan sebelum ini ia dilaporkan sebagai ralat (&quot;Kamera <sn>sudah </sn>berada <sn>dalam array <id>&quot;). Lihat [Kaedah `ChlorosProject`](#chlorosproject-methods).
> - **`ChlorosLocal()`, `process_folder()` dan `process_lattice_capture()` mencari Chloros pada Windows.** Mereka melihat di mana pemasang meletakkan backend (`<install folder>\backend\`, `C:\Program Files\Chloros\backend\` secara lalai, folder tersuai daripada rekod installer itu sendiri), dan menyambung ke backend yang sudah berjalan tanpa mencarinya sama sekali. Dalam 1.2.0 mereka memaparkan ralat &quot;Chloros backend not found&quot; pada mesin yang dipasang melainkan anda menggunakan `backend_exe=`.
> - **Sebuah backend yang mati semasa permulaan dilaporkan serta-merta.** Apabila `ChlorosLocal()` atau panggilan smart-connect memulakan backend yang keluar sebelum ia menjawab, panggilan itu diangkat dalam beberapa saat, `ChlorosBackendError` daripada `ChlorosLocal` dan `ChlorosConnectError` daripada smartpanggilan -connect, dengan kod keluar, arahan yang dijalaninya dan baris terakhir yang dicetak oleh backend. 1.2.0 menunggu tamat masa mula sebanyak 60 saat dan tidak memberitahu sebab. Lihat [Backend Auto-Start](#backend-auto-start).
> - **Backend terus berjalan antara skrip.** Meninggalkan blok `with ChlorosLocal(...)`, pulangan `process_folder()` / `process_lattice_capture()` dan `chloros-mcp` keluar tidak lagi menghentikan backend yang dimulakan oleh </id></sn>SDK<sn><id>; skrip seterusnya menggunakannya semula. Ia berhenti pada `logout()` (kecuali jika aplikasi Chloros dibuka atau sesuatu larian sedang dijalankan), pada `shutdown_backend()`, atau secara automatik selepas 30 minit jika tiada apa-apa yang menggunakannya (`CHLOROS_BACKEND_IDLE_MIN` dalam persekitaran backend mengubah perkara itu; `0`: tidak pernah).
> - **Sebuah backend yang dimulakan oleh SDK diminta untuk dimatikan, bukan dibunuh.**`shutdown_backend()` membenarkan backend melepaskan kamera dan penderia cahayanya serta menamatkan sebarang rakaman; ia akan ditamatkan hanya jika ia masih berjalan 10 saat kemudian. Versi 1.2.0 menamatkannya serta-merta. (pada Windows, pembunuhan paksa), yang boleh menyebabkan sensor cahaya DAQ yang disambungkan kepada backend tersebut menjadi tidak responsif. Backend yang ditemui SDK sedang berjalan akan dibiarkan sahaja.
> - **Pada Linux, auto-start tidak pernah menjalankan `backend_server.py` dari folder semasa.** Tanpa backend yang dipasang, skrip yang dimulakan dalam folder yang mengandungi satu backend menjalankannya; kini hanya backend yang dipasang akan dimulakan (atau, untuk `ChlorosLocal`, yang dinamakan oleh `backend_exe=`).
> - **`configure(indices=...)`**menambah setiap preset ke projek untuk jalur kamera masing-masing, satu projek baru disertakan, dan memberi amaran (`UserWarning`) untuk nama yang tidak dapat ditambah.****`import_images()`** melaporkan rakaman `.daq` sebagai `scan_count` dan tidak lagi memberi amaran tentang folder tangkapan LATTICE.
> - **`read_image_audit_tags()` berfungsi melalui pemasangan pip.** Ia membaca metadata imej itu sendiri; dalam 1.2.0 ia mengembalikan `{}` tanpa komponen dalaman pemasangan desktop pada laluan Python.
> - **`capture()` sejurus selepas `connect_array()`** menunggu sehingga 20 saat untuk kumpulan bingkai pertama susunan tersebut, bukannya gagal dengan ralat HTTP 503 &quot;Susunan masih memanas&quot;.
> - **`status()` melaporkan kelewatan.** Medan `stalled` baru adalah True semasa gelung grab berjalan tetapi tidak menerbitkan apa-apa, dan `actual_fps` kemudiannya adalah 0. Dalam 1.2.0 `actual_fps` mengekalkan nilai terakhirnya semasa sebuat pengehentian, jadi satu array yang terhenti memaparkan `actual_fps > 0` manakala tangkapan gagal dengan 503 &quot;Tiada kumpulan bingkai yang disegerakkan baharu&quot;.
> - **Kurang set bingkai tidak lengkap dan ralat tangkapan 503:** input pencetus setiap kamera hamba kini telah dipenap.
> - **Pengangkutan 12-bit berpadat** (`connect_array(packed="auto")`) meningkatkan kadar bingkai keseluruhan susunan sensor kira-kira sepertiga. Lihat [Pengangkutan 12-bit Berpadat](#packed-12-bit-transport-packed).
> - **Pendedahan Array di bawah ROI:** ROI kecil tidak lagi mengehadkan pendedahan ahli kepada tempoh operasi bebas sensor (4.9 ms pada 768 × 304). Lihat [Pendedahan pada Ahli Array](#exposure-on-array-members).
> - **Pendedahan automatik tidak lagi menurunkan kadar bingkai automatik sesebuah susunan.** Susunan yang diselaraskan dengan kabel beroperasi pada kadar yang dibenarkan oleh rangkaian dan bacaan kamera; pendedahan automatik mengisi tempoh yang diberikan oleh kadar tersebut, kemudian meningkatkan penguatan. Pendedahan manual yang lebih lama daripada tempoh tersebut akan memperlahankan pencetus untuk menyesuaikannya, bukannya ditolak. Lihat [Pendedahan pada Ahli Susunan](#exposure-on-array-members).
> - **`read_nodes()`**menambah `frame_values` dan `notes` semasa pendedahan automatik dijalankan (lihat [`read_nodes()` di bawah pendedahan automatik](#read_nodes-under-auto-exposure--frame_values-and-notes)). Apabila kamera menolak pendedahan atau penguatan,**`set_settings()`** menyatakan julat yang diterima dan memulihkan semula mod automatik yang telah dilumpuhkan untuk nilai tersebut; `set_settings(target_brightness_auto=True)` adalah baharu.
> - **`orient=`** pada `ArraySession.capture()` / `capture_fastest()` / `continuous()` / `burst()` dan `ArrayHandle.capture()` / `capture_smart()` memusingkan produk mengikut orientasi kamera. Lihat [Kaedah `ArraySession`](#arraysession-methods).
> - **`DLS()` memerlukan `mac_address` pada sensor itu sendiri.** Tiada lagi MAC lalai (ia adalah alamat satu unit); `DLS()` tanpa satu daripadanya menimbulkan `DLSError`. Lihat [DAQ-M band magnitudes](#daq-m-band-magnitudes--dlsband_magnitudes).
> - **Pemasang Windows** menawarkan &quot;Aktifkan laluan panjang Windows&quot; dan &quot;Kecualikan backend Chloros daripada imbasan Microsoft Defender&quot;, kedua-duanya dipilih secara lalai. Lihat [Langkah 1](#step-1--install-the-chloros-platform-package).
> - **Pemasang .deb Linux** memasang Python SDK untuk sistem `python3` (sebelumnya, ia hanya meletakkan roda pada disk), meningkatkan buffer penerimaan soket kernel yang diperlukan oleh aliran kamera, dan menambah perkhidmatan backend yang berjalan sebagai pengguna anda. Pakej Jetson ialah `chloros_1.3.0_arm64_jp6.deb`, untuk JetPack 6, dengan CUDA PyTorch JetPack. Lihat [Langkah 1](#step-1--install-the-chloros-platform-package) dan [Langkah 2](#linux-deb).

---

## Seni Bina dalam 60 Saat

SDK adalah lapisan nipis Python di atas backend Chloros (pelayan Flask yang sama digunakan oleh GUI desktop dan CLI). Untuk automasi, anda mengimport `chloros_sdk` dan memanggil kaedah aras tinggi; di sebalik tabir, setiap panggilan menjadi permintaan HTTP ke backend tempatan pada port 5000 — `http://127.0.0.1:5000/api/...` (dengan sengaja bukan `localhost`, yang diselesaikan kepada `::1` terlebih dahulu pada Windows dan memakan masa ~2 saat setiap permintaan terhadap backend IPv4 sahaja). Backend memiliki kolam perkakasan — kamera, sensor DAQ, profil penjajaran, penimbal bingkai — jadi skripSDK boleh wujud bersama GUI tanpa berebut port bersiri atau jalur lebar NIC.

Terdapat tiga permukaan yang akan anda gunakan:

1. **`ChlorosLocal` + fungsi bebas** (`process_folder`, `process_lattice_capture`) — Saluran pemprosesan imej. Jalankan keseluruhan folder melalui penentukuran / debayer / eksport indeks daripada satu panggilan Python.
2. **Pemegang Smart-connect** (`connect_camera`, `connect_array`, `connect_daq_sensor`) — Buka sesi backend berterusan untuk perkakasan langsung. Aliran &quot;smart-prep&quot; yang sama seperti GUI: pengimbasan rangkaian, pemilihan lapisan automatik, PTP, penaburan AE, konfigurasi pencetus GPIO.
3. **`ChlorosProject` / `open_project`** — Memuatkan projek yang disimpan (folder dengan `cameras.json` + `sensors.json` + `project.json`), menyambungkan kameranya, arays dan penderia cahaya sekaligus, dan mengendalikan tangkapan dengan pemegang bernama.

Permukaan 1 dan 2 **memulakan secara automatik backend tempatan** jika tiada yang sedang mendengar (binari terbungkus yang sama yang dilancarkan GUI/CLI) — jadi skrip ringkas berfungsi dari shell baru tanpa anda memulakan backend terlebih dahulu: `ChlorosLocal` (dan `process_folder()` / `process_lattice_capture()`), `connect_camera()`, `connect_array()`, `connect_daq_sensor()`, `discover_lattice_cameras()` dan `discover_daq_sensors()`. `list_cameras()`, `list_arrays()`, `list_daq_sensors()`, `attach_array()` dan `analyze_array_network()` tidak pernah memulakannya: mereka memerlukan backend yang sedang berjalan. Masukkan `auto_start_backend=False` untuk tidak menyertainya (contohnya apabila menunjuk ke backend jauh, yang tidak pernah di-spawn). Lihat [Backend Auto-Start](#backend-auto-start)Surface 3 berkelakuan berbeza: `open_project()` tidak mengambil sebarang parameter `auto_start_backend`, dan `connect_all()` tidak pernah memulakan backend — ia menyemak `http://127.0.0.1:5000` sekali dan, jika tiada maklum balas, kembali kepada kawalan peranti `lattice_sdk` secara langsung (tanpa backend). Ia memerlukan runtime Arena SDK dan OpenCV (`cv2`) boleh diimport dalam Python anda, yang roda SDK tidak memasang; tanpa mereka, setiap kamera dan tatasusunan yang disimpan dilaporkan sebagai ralat, beserta sebabnya. Penderia cahaya projek tidak mempunyai laluan bebas backend: tanpa backend berjalan, setiap penderia yang disimpan dilaporkan sebagai ralat, beserta sebabnya (lihat [`SensorHandle`](#sensorhandle)). Hanya `proj.process()` dan `stream(..., overlays=True)` secara malas membina `ChlorosLocal()` (yang memulakan secara automatik).

Ketiganya adalah auth-gated: jalankan `chloros-cli login` sekali pada mesin, atau log masuk melalui GUI desktop. Apa yang dipanggil tanpa sesi sah menimbulkan bergantung pada permukaan (lihat [Pengecualian](#exceptions)): `ChlorosAuthenticationError` daripada `ChlorosLocal` (dan `process_folder()` / `process_lattice_capture()`), `ChlorosConnectError` daripada smartpanggilan -connect dan sesi mereka, `status: "error"` dengan `error_code` daripada `analyze_array_network()`, dan satu entri di bawah `errors` dalam XPRlaporan OTX000531.

Prasyarat:
- Python 3.7+ (seperti yang dinyatakan oleh pakej; dibangunkan/diuji pada 3.10)
- Desktop Chloros dipasang secara tempatan (binari backend disertakan dalam pemasang)
- Log masuk Chloros+ aktif pada **mana-mana peringkat berbayar Chloros+ (Copper atau ke atas)**; peringkat percuma**Iron**tidak mempunyai akses SDK/CLI. Ini dikuatkuasakan**pelayan-sisi**: setiap permintaan yang ditandakan dengan SDK/CLI mesti membawa kedua-dua sesi langsung dan pelan berbayar. Pelan di bawah Copper mendapat `403` dengan `error_code: PLAN_UPGRADE_REQUIRED` (`ChlorosLicenseError` daripada `ChlorosLocal`, `ChlorosConnectError` daripada panggilan smart-connect). Pemanggil yang log keluar mendapat `401` / `AUTH_REQUIRED` sebaliknya (`ChlorosAuthenticationError` daripada `ChlorosLocal`, `ChlorosConnectError` daripada panggilan smart-connect) — kedua-duanya berbeza kerana menjalankan semula `chloros-cli login` membetulkan `401` dan tidak dapat membetulkan `403`, yang memerlukan peningkatan pelan.
- Penggunaan luar talian disokong dalam tempoh kurnia pelan: peringkat dibaca daripada cache pengesahan pelayan (5 minit) atau cache lesen bertandatangan terikat mesin (30 hari untuk pelan bulanan, sehingga tamat langganan untuk pelan tahunan). Apabila tempoh kemaafan itu tamat, pelan akan bertukar kepada percuma dan akses SDK/CLI akan terhenti sehingga mesin dapat mencapai pelayan sekali. `chloros-cli status` (`GET /api/license-status`) kekal boleh diakses pada peringkat percuma supaya sebabnya dapat dilihat.
- Windows 10/11 64-bit, **Ubuntu 22.04 LTS atau lebih baru**, atau Jetson (JetPack 6). Ubuntu 20.04**tidak** disokong: kebergantungan `.deb` diperoleh daripada apa yang dipautkan oleh backend, termasuk `libc6 (>= 2.35)`, dan focal dihantar dengan glibc 2.31.

---

## Pemasangan

Python SDK adalah lapisan nipis Python di atas backend Chloros. Kecuali anda menjalankan backend pada mesin lain (lihat [Mod Backend Jauh](#remote-backend-mode-pip-only-host-via-tunnel)), anda perlu memasang pakej desktop **Chloros secara tempatan** (pemasang Windows atau `.deb` untuk Linux), dan ia termasuk penderia cahaya DAQ. Pakej ini menyediakan binari backend, runtime Arena SDK untuk kamera LATTICE, dan bundel penentukuran.

Muat turun terkini: [`https://mapir.gitbook.io/chloros/download`](https://mapir.gitbook.io/chloros/download)

### Langkah 1 — Pasang pakej platform Chloros

#### Windows (.exe)

1. Muat turun `Chloros Installer 1.3.0.exe` daripada halaman muat turun.
2. Jalankan pemasang dan ikut penolong. Laluan pemasangan lalai ialah `C:\Program Files\Chloros\`.
3. Lancarkan Chloros sekurang-kurangnya sekali dan log masuk dengan akaun Chloros+ anda.

Pemasang membenarkan backend (`chloros-backend.exe`, tiada apa-apa lagi) melalui Windows Firewall pada setiap profil rangkaian: UDP masuk, untuk aliran kamera, PTP, mDNS dan aliran DAQ-E, dan TCP masuk hanya dari subnet tempatan, untuk sambungan yang dibuat oleh DAQ-E kembali ke komputer semasa kemas kini firmware ([`update_firmware()`](#daq-e-device-settings)). Pemangkin akan membuang kedua-dua peraturan tersebut. API HTTP backend kekal pada `127.0.0.1` dalam apa jua keadaan.

Kumpulan **Seting Sistem** wizard mempunyai dua pilihan, kedua-duanya dipilih secara lalai:

| Pilihan | Apa fungsinya | Tidak menyertakan pemasangan senyap |
| --- | --- | --- |
| **Benarkan laluan panjang Windows** | Membolehkan folder projek berada lebih dalam daripada had laluan 260 aksara Windows. Dengan laluan panjang dilumpuhkan (lanjutan lalai Windows), folder projek yang terlalu dalam untuk produknya akan ditolak sebelum apa-apa diproses — lihat [Troubleshooting](#troubleshooting-pointers). | `/MERGETASKS="!longpaths"` |
| **Kecualikan backend Chloros daripada imbasan Microsoft Defender** | Menghentikan imbasan sebenar Defender-imbasan masa nyata pada setiap fail yang ditulis oleh tangkapan kamera. Pada kadar tangkapan yang tinggi, imbasan ini menelan kos kira-kira 30% daripada kadar: diukur pada ROI 512 pada rangkaian 9 kamera, tangkapan mentah berterusan mencapai 52.7 tangkapan/s dengan imbasan dan 77.7 tangkapan/s apabila backend dikecualikan. | `/MERGETASKS="!defenderexclusion"` |

Satu tangkapan berterusan melaporkan beban Defender sebagai `defender_cores` (lihat [`ContinuousHandle`](#continuoushandle)).

#### Linux amd64 (.deb)

```bash
sudo apt install ./chloros_1.3.0_amd64.deb
chloros-cli --version
chloros-cli login user@example.com 'YourPassword'
```

Pasang dengan `apt install ./…`, bukan `dpkg -i`: apt juga memasang keperluan Python SDK, yang disyorkan oleh pakej ini dan bukannya diwajibkan (lihat [Langkah 2](#linux-deb)).

#### Linux arm64 — Jetson (JetPack 6)

```bash
sudo apt install ./chloros_1.3.0_arm64_jp6.deb
chloros-cli --version
chloros-cli login user@example.com 'YourPassword'
```

Untuk Jetson Orin / Orin NX / Orin Nano pada JetPack 6 (L4T R36). PyTorch-nya adalah binaan CUDA JetPack 6, jadi pemprosesan GPU dijalankan pada GPU Orin; ia menggunakan perpustakaan CUDA dan cuDNN yang dipasang oleh JetPack 6 (`sudo apt install nvidia-jetpack` pada imej yang tidak memilikinya).

#### Linux: penimbal penerimaan dan perkhidmatan belakang

Kedua-dua .debs meningkatkan penimbal penerimaan soket kernel yang diperlukan oleh aliran kamera LATTICE kepada 32 MB (`/etc/sysctl.d/60-chloros-gige.conf`). Pada lalai kernel, paket kamera terbuang dalam kernel dan satu susunan kehilangan bingkai dan kadar bingkai; `chloros-cli lattice network` melaporkan buffer apabila ia rendah. Lihat rujukan CLI [hos Linux — buffer penerimaan soket](cli-reference.md#linux-hosts--socket-receive-buffers).

Pada mesin tanpa papan pemuka, jalankan backend sebagai perkhidmatan **dengan akaun pengguna anda**, supaya skrip dapat menemui satu yang sedang berjalan (`connect_all()` tidak pernah memulakan backend):

```bash
sudo systemctl enable --now chloros-backend@$USER.service    # $USER expands to your user name
```

Backend itu kemudian berjalan sebagai anda: tangkapan yang ditulisnya adalah milik anda, dan ia menggunakan `chloros-cli login` anda. `chloros-backend.service` root masih dipasang untuk mesin yang mengaktifkannya, tetapi setiap fail yang ditulis oleh backend root adalah milik root. Rujuk rujukan CLI [Menjalankan backend sebagai perkhidmatan](cli-reference.md#running-the-backend-as-a-service-headless).

Backend yang dijalankan oleh perkhidmatan dikongsi, tidak pernah dihentikan oleh klien: skrip SDK dan `chloros-cli` menggunakannya seperti mana ia menemuinya (`ChlorosLocal.shutdown_backend()` hanya menghentikan backend yang dimulakan oleh `ChlorosLocal` sendiri), dan `GET /api/status` menamakan unit itu sebagai `service`. Mulakan semula dengan `sudo systemctl restart chloros-backend@$USER.service`. Menyemak naik pakej menghentikan perkhidmatan backend semasa ia dipasang, supaya skrip yang sedang berjalan kehilangan kamera dan sensornya, dan kemudian memulakan semula setiap yang diaktifkan; menyingkirkan pakej menghentikannya dan menyahaktifkan yang diaktifkan. Hanya satu backend berjalan pada satu masa, jadi aktifkan satu unit: unit kedua enggan bermula dan kekal `failed` (status keluar 98, tidak dihidupkan semula), dan `systemctl status` memberitahu sebabnya. Untuk menukar unit, nyahdayakan unit yang tidak anda gunakan.</id></sn>

### Langkah 2 — Pasang Python SDK

**Pemuat turun Chloros menyertakan roda SDK yang sepadan.** Setiap pemuat turun Windows dan .deb Linux meletakkan `chloros_sdk-X.Y.Z-py3-none-any.whl` pada cakera yang sepadan tepat dengan versi GUI / CLI / backend. Anda tidak perlu melayari PyPI untuk kekal selari.

#### Windows

Pemuat turun akan menjalankan `pip install` secara automatik terhadap roda yang disertakan menggunakan Python sistem anda (pelancar `py.exe` diutamakan, beralih kepada `python -m pip` jika perlu). Tiada tindakan diperlukan — `import chloros_sdk` berfungsi dalam persekitaran Python anda selepas pemasangan berjaya. Jika tiada Python pada sistem, pemasang akan secara senyap melangkau langkah ini dan GUI + CLI terus berfungsi.

#### Linux (.deb)

Kedua-dua .deb (x86_64 dan Jetson) memasang SDK untuk sistem `python3`, jadi `import chloros_sdk` berfungsi selepas pemasangan, tanpa pip dan tanpa rangkaian. Pakej-pakej (`chloros_sdk`, `lattice_sdk`, `lattice_cam`, `arena_api` dan pemuat projek) diletakkan ke dalam `/usr/lib/python3/dist-packages`, dan arahan `chloros-mcp` daripada SDK (pelayan [MCP](#mcp-server-ai-assistants)) ke dalam `/usr/bin`, semuanya dimiliki oleh pakej: peningkatan akan menggantikannya dan menyingkirkan pakej akan menyingkirkannya.

Keperluan SDK itu sendiri, `python3-numpy` dan `python3-requests`, adalah **disyorkan, bukan diwajibkan** (GUI, CLI dan backend adalah lengkap sendiri): `sudo apt install ./chloros_<version>_amd64.deb` (`…_arm64_jp6.deb` pada Jetson) memasangnya, `dpkg -i` tidak. Keluaran pemasangan melaporkan versi SDK yang diimport oleh `python3`, sebarang keperluan yang hilang, dan `chloros_sdk` di tempat lain pada laluan `python3` yang menaungi salinan pakej itu (seperti yang dipasang lebih awal dengan `pip install --user`).

Dalam virtualenv, yang tidak melihat pakej sistem, pasang wheel yang disertakan ke dalamnya:

```bash
pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl
```

Untuk penyebaran Jetson yang terpisah (air-gapped), ini sepenuhnya luar talian — SDK dan wheel sudah ada di cakera.

#### PyPI Awam

Untuk hos hanya-pip (tiada pakej desktop Chloros dipasang; SDK kemudian memacu backend pada mesin lain, lihat [Mod Backend Jauh](#remote-backend-mode-pip-only-host-via-tunnel)):

```bash
pip install chloros-sdk
```

PyPI mengandungi keluaran stabil terkini. Pastikan SDK pada versi yang sama dengan backend Chloros yang diendalikannya (lihat [Pengevonan &amp; Keserasian](#versioning--compatibility)). Pembantu DAQ-M `DLS` juga memerlukan `bleak`: `pip install "chloros-sdk[camera]"` (atau `pip install bleak`).

#### Verifikasi

```python
import chloros_sdk
print(chloros_sdk.__version__)
print("CAMERA_AVAILABLE =", chloros_sdk.CAMERA_AVAILABLE)
print("PROJECT_AVAILABLE =", chloros_sdk.PROJECT_AVAILABLE)
```

> **Langganan Chloros+ diperlukan.** Semua panggilan SDK memerlukan log masuk Chloros+ yang aktif. Jalankan `chloros-cli login user@example.com 'YourPassword'` sekali setiap mesin; kelayakan disimpan dalam cache oleh `~/.chloros/`.

### Adakah Saya Perlukan Pakej Desktop?

Pakej pip sahaja **tidak** mencukupi untuk kebanyakan aliran kerja. Berikut adalah apa yang diperlukan oleh setiap permukaan SDK:

| Permukaan SDK | Perlukan Pakej Desktop? | Kenapa |
| --- | --- | --- |
| `ChlorosLocal`, `process_folder`, `process_lattice_capture` | **Ya**(untuk memulakannya)**/ Tidak** (backend yang sudah boleh diakses) | Gunakan backend yang sedang berjalan (aplikasi desktop, atau satu yang dimulakan oleh skrip lain); jika tidak, mulakan secara automatik yang dipasang: `/usr/lib/chloros/chloros-backend` (Linux) atau `<install folder>\backend\chloros-backend.exe` (Windows; `C:\Program Files\Chloros\backend\` secara lalai, folder pemasangan tersuai dibaca daripada rekod pemasang). Masukkan `backend_exe=` untuk menamakannya sendiri. |
| `chloros-mcp` ([pelayan MCP](#mcp-server-ai-assistants)) | **Ya**(untuk memulakan satu)**/ Tidak** (backend sudah boleh diakses) | Dibina pada `ChlorosLocal`, jadi sama seperti baris di atas. |
| `read_image_audit_tags` | **Tidak** | Membaca metadata imej yang diproses sendiri (perpustakaan piawai Python sahaja). |
| `connect_camera`, `connect_array`, `connect_daq_sensor`, `analyze_array_network`, `list_*`, `discover_*` | **Ya**(lokal)**/ Tidak**(jauh) | Klien HTTP tulen di belakang backend. Backend tempatan → pakej desktop diperlukan. Backend jauh → `backend_url=`**melalui terowong** (lihat Mod Backend Jauh — backend yang disertakan hanya mengikat gelung balik). |
| `ChlorosProject` / `open_project` | **Ya** | Menggerakkan projek yang disimpan melalui backend. |
| Kelas LATTICE langsung (`LatticeCamera`, `CameraPool`, `Calibration`, `DLS`, …) | **Ya** | Perlu runtime Arena SDK, yang disertakan dalam pakej desktop, dan OpenCV (`cv2`) dalam Python anda, yang tidak dipasang sama ada oleh pakej desktop mahupun SDK (`pip install opencv-python`). `CAMERA_AVAILABLE` adalah `False` apabila diimport sebaliknya. `DLS` juga memerlukan `bleak`. |

### Mod Backend Jauh (hos hanya-pip, melalui terowong)

> **Backend yang dipasang tidak dapat dihubungi melalui LAN.** Ia mendengar pada
> loopback sahaja (`127.0.0.1` / `::1`), jadi `backend_url="http://<lan-ip>:5000"`
> **tidak dapat mencapai Chloros yang dipasang**. Untuk mengendalikan backend pada mesin lain, alihkan port loopbacknya sendiri dan tujukan SDK ke terowong:

```bash
# on the pip-only host: forward local 5000 to the Chloros machine's loopback
ssh -N -L 5000:127.0.0.1:5000 user@chloros-host
```

```python
import chloros_sdk

BACKEND = "http://127.0.0.1:5000"     # the tunnel endpoint
serials = ["213800234", "214000533"]   # master first

# auto_start_backend=False: the tunnel URL looks local, so without it a tunnel
# that is down would start this machine's own backend, if one is installed.
arr = chloros_sdk.connect_array(serials, backend_url=BACKEND,
                                auto_start_backend=False)
daq = chloros_sdk.connect_daq_sensor(eth_host="daq-e-1.local",
                                     backend_url=BACKEND,
                                     auto_start_backend=False)

# The backend opens every path on ITS machine: pass absolute paths valid there.
# (A relative path would be made absolute against this machine's working folder.)
arr.capture("/home/chloros/captures/run1", processing="raw")
```

Host tanpa kepala / CI / robotik boleh mengekalkan satu mesin dengan pemasangan desktop penuh sebagai &quot;Chloros server&quot; dan `pip install chloros-sdk` di semua tempat lain — tetapi pengangkutan di antara kedua-duanya ialah terowong yang diatur oleh pengguna di atas, bukan URL LAN terus.

> **`ChlorosLocal` pada hos yang hanya mempunyai pip.** `ChlorosLocal(backend_url=BACKEND)` menyambung ke backend yang boleh diakses tanpa pemasangan desktop: ia mencari binari backend tempatan hanya apabila ia perlu MEMULAKAN satu (tiada yang menjawab dan `auto_start_backend=True`). Sehingga 1.2.0 ia mencari binari terlebih dahulu dan memaparkan `ChlorosBackendError` (&quot;Chloros backend tidak ditemui…&quot;) pada hos pip-only walaupun dengan backend yang boleh dicapai. Folder yang anda lalui (`process_folder`, `import_images`) diperiksa pada mesin ini dan dibaca oleh backend, jadi dengan backend jauh mereka mesti menamakan folder yang sama pada kedua-duanya, seperti pemacu kongsi.

### Aliran Kerja Hanya DAQ (hos hanya pip)

`pip install chloros-sdk` sahaja tidak boleh mengendalikan sensor DAQ: sensor DAQ sentiasa dikendalikan melalui backend Chloros dengan `connect_daq_sensor()`:

- **Sensor pada mesin ini:pasang pakej desktop Chloros di sini. `connect_daq_sensor()` memulakan backendnya jika tiada yang sedang berjalan.
- **Sensor pada mesin lain:** mesin tersebut memerlukan pakej desktop; capaian ke backendnya melalui terowong dalam [Mod Backend Jauh](#remote-backend-mode-pip-only-host-via-tunnel). Backend itu membuka sensor pada mesinnya sendiri, sehingga DAQ-U disambungkan ke mesin itu, DAQ-M berada dalam julat Bluetooth-nya, dan DAQ-E berada di rangkaian yang boleh diaksesnya.

```python
import chloros_sdk

# http://127.0.0.1:5000 is the local desktop install's backend, or the tunnel.
with chloros_sdk.connect_daq_sensor() as daq:   # smart-detect USB / BLE / ETH
    print(daq.model, daq.transport, daq.address)
    for frame in daq.latest(n=5):
        print(frame["spectrum"][:10])
```

Bagaimanapun, backend menguatkuasakan pelan Chloros+: log masuk pada mesin yang menjalankan backend (`chloros-cli login`, atau aplikasi desktop) dengan mana-mana peringkat berbayar (Copper atau lebih tinggi). Pelayan belakang yang log keluar dan pelan di bawah Copper kedua-duanya akan membangkitkan `ChlorosConnectError` yang membawa sebab pelayan belakang (`AUTH_REQUIRED` / `PLAN_UPGRADE_REQUIRED`).

---

## Permulaan pantas

```python
import chloros_sdk

# === Image processing ===
results = chloros_sdk.process_folder(
    "C:/DroneImages/Flight001",
    indices=["NDVI", "GNDVI", "OSAVI"],
)

# === Live LATTICE single-cam ===
with chloros_sdk.connect_camera("213800234") as cam:
    cam.set_settings(exposure_time=10000, gain=0.0)
    cam.capture("output/")

# === Live LATTICE synchronized array (GUI smart-prep flow) ===
with chloros_sdk.connect_array(
        ["213800234", "214000533", "214701288", "214701292"]) as arr:
    arr.capture("output/", processing="reflectance")

# === Live DAQ spectral sensor ===
with chloros_sdk.connect_daq_sensor() as daq:    # smart-detect USB / BLE / ETH
    for frame in daq.latest(n=5):
        print(frame["spectrum"][:10])

# === Drive a saved project end-to-end ===
proj = chloros_sdk.open_project("/path/to/project")
proj.connect_all()
proj.arrays["main_rig"].capture("output/", processing="reflectance")
proj.disconnect_all()
```

---

## Indeks API Tahap Atas

```python
import chloros_sdk

# === Image processing (full pipeline) ===
chloros_sdk.ChlorosLocal                          # class
chloros_sdk.process_folder(...)                   # one-shot helper
chloros_sdk.process_lattice_capture(...)          # LATTICE-friendly defaults
chloros_sdk.read_image_audit_tags(path)           # post-run audit

# === Live cameras (persistent backend pool) ===
chloros_sdk.connect_camera(serial, ...)           # → CameraSession
chloros_sdk.connect_array(serials, ...)           # → ArraySession (smart-prep)
chloros_sdk.attach_array(serials_or_id, ...)      # → ArraySession (attach without re-connecting)
chloros_sdk.list_cameras()
chloros_sdk.list_arrays()
chloros_sdk.discover_lattice_cameras()
chloros_sdk.analyze_array_network(...)            # network capability + recommendation
chloros_sdk.CaptureResult                         # list subclass returned by CameraSession.capture / ArraySession.capture
chloros_sdk.RecorderHandle                        # handle for an array record()/burst() job
chloros_sdk.ContinuousHandle                      # handle for an array continuous() capture
chloros_sdk.GrabResult                            # dict returned by grab() / yielded by frame_stream()
chloros_sdk.FrameFetchError                       # raised by grab()/frame_stream() on a refusal

# === Live DAQ sensors (persistent backend pool) ===
chloros_sdk.connect_daq_sensor(...)               # → DAQSensorSession
chloros_sdk.discover_daq_sensors()                # scan USB/BLE/ETH (finds a DAQ-M MAC)
chloros_sdk.list_daq_sensors()
chloros_sdk.daq_bindings() / set_daq_bindings(cameras=, arrays=)   # which DAQ feeds which camera
chloros_sdk.daq_band_presets(filter_code=None)    # band sets for Ambient + Object reflectance
chloros_sdk.light_sensor_reflectance(ambient_id, object_id, ...)   # two DAQs -> reflectance + indices
chloros_sdk.time_sync_status() / time_sync_peers() / time_sync_cameras()   # PTP grandmaster
chloros_sdk.restart_time_sync() / set_time_sync_priority(priority1=, priority2=)

# === Camera utilities (the CLI's lattice commands; by serial) ===
chloros_sdk.camera_color_profile(serial) / set_camera_color_profile(serial, ...)
chloros_sdk.camera_color_adjust(serial) / set_camera_color_adjust(serial, ...)
chloros_sdk.camera_highlights(serial) / set_camera_highlights(serial, value)
chloros_sdk.camera_white_balance_once(serial) / set_camera_white_balance(serial, ...)
chloros_sdk.camera_presets() / apply_camera_preset(serial, preset)
chloros_sdk.camera_filter_models() / camera_filter_model_info(name)
chloros_sdk.camera_firmware_status() / update_camera_firmware(serial)
chloros_sdk.camera_power_probe(serial) / sleep_camera(serial) / wake_camera(serial)
chloros_sdk.calibrate_camera_with_target(serial, ...)
chloros_sdk.network_adapters(...) / fix_network_settings()
chloros_sdk.apply_vignette_correction(input_dir, output_dir, ...)
chloros_sdk.read_dls(mac, ...) / probe_system(...) / system_info()

# === Transient cameras, alignment profiles, indices (cameras opened for one command) ===
chloros_sdk.start_camera_continuous(serial, ..., daq=None, daq_tolerance_ms=None)  # → CliJob; a DAQ bound to the camera records one .daq per frame (daq=False: none)
chloros_sdk.start_multi_camera_stream(serials, ...)   # → CliJob
chloros_sdk.capture_cameras_transient(serials, ...)
chloros_sdk.test_array_sync(serials, count, ptp_settle_s=None) / detect_array_master(line) / analyze_array_models(models)
chloros_sdk.calibrate_alignment_profile(serials, save_path, ...)
chloros_sdk.capture_aligned_multiband(profile_path, ...) / start_aligned_stream(profile_path, ...)  # → dict / CliJob
chloros_sdk.alignment_profile_info(path) / reorder_alignment_profile(path, ...)
chloros_sdk.index_catalog() / compute_index(input_path, ...) / capture_live_index(profile_path, ...)
chloros_sdk.CliJob                                # stats() / stop() / wait()

# === Project lifecycle ===
chloros_sdk.open_project(path, relink=None)       # → ChlorosProject
chloros_sdk.project_folder() / set_project_folder(path) / reset_project_folder()
chloros_sdk.run_project_recipe(recipe, project_path)  # `chloros-cli project run`
chloros_sdk.ChlorosProject                        # class
chloros_sdk.AlignmentSpec                         # dataclass
chloros_sdk.ArrayHandle, CameraHandle, SensorHandle

# === Direct-hardware (no-backend) classes (from lattice_sdk) ===
chloros_sdk.LatticeCamera, CameraSettings, PRESETS, CameraPool
chloros_sdk.Calibration, CalibrationCoefficients, FilterModel, list_filters
chloros_sdk.DLS, NetworkDiagnostics               # DLS: DAQ-M over Bluetooth; needs bleak

# === Exceptions ===
chloros_sdk.ChlorosError                          # base
chloros_sdk.ChlorosBackendError
chloros_sdk.ChlorosLicenseError
chloros_sdk.ChlorosConnectionError
chloros_sdk.ChlorosProcessingError
chloros_sdk.ChlorosProjectFilesMissingError      # a ChlorosProcessingError
chloros_sdk.ChlorosAuthenticationError
chloros_sdk.ChlorosConfigurationError
chloros_sdk.ChlorosConnectError                   # raised by smart-connect surface
chloros_sdk.LatticeError, CameraNotFoundError, ...  # from lattice_sdk

# === Availability flags ===
chloros_sdk.CAMERA_AVAILABLE     # True iff lattice_sdk imported cleanly
chloros_sdk.PROJECT_AVAILABLE    # True when chloros_sdk.project imported; open_project()
                                 # still raises ImportError without the project loaders
```

---

## Pemprosesan Imej — `ChlorosLocal`

Kelas utama pipeline. Memulakan backend apabila ia dibina (jika tiada yang menjawab dan `auto_start_backend=True`), mencipta / mengkonfigurasi projek, memantau kemajuan, mengembalikan ringkasan pasca-jalankan.

### Pembina

```python
ChlorosLocal(
    api_url="http://127.0.0.1:5000",   # backend URL (also: backend_url=)
    auto_start_backend=True,            # spawn backend if not running
    backend_exe=None,                   # override backend binary path
    timeout=30,                         # request timeout seconds
    backend_startup_timeout=60,         # backend boot timeout
    processing_timeout=14400,           # hard cap on process() (4 h)
    processing_stuck_timeout=1800,      # no-progress threshold (30 min)
)
```

### Kaedah

| Kaedah | Deskripsi |
| --- | --- |
| `create_project(project_name, camera=None)` | Buat projek baru (secara pilihan dengan templat kamera seperti `"Survey3N_RGN"`). |
| `open_project(project_path, relink=None)` | Membuka projek yang disimpan (foldernya, atau namanya dalam folder projek) dan menjadikannya projek yang terbuka. Laluan projek yang dipindahkan akan diarahkan semula ke folder tempat ia berada sekarang; `relink` menamakan folder di mana fail-failnya yang lain yang hilang berada sekarang. Mengembalikan jawapan backend dengan `source_files`; menghasilkan `ChlorosProjectFilesMissingError` apabila tiada yang dapat ditemui. Lihat **Projek yang dipindahkan** di bawah. |
| `import_images(folder_path, recursive=False)` | Import imej RAW/TIF/JPG/DNG **dan rakaman sensor cahaya `.daq`**. Mengembalikan `count` (imej) dan `scan_count` (rakaman). Memberi amaran hanya jika folder tidak mengandungi kedua-duanya. |
| `export_light_sensor(daq=True, csv=True)` | Menulis `.daq` + `.csv` untuk setiap rakaman penderia cahaya dalam projek, ke dalam `<project>/Light Sensor/`. Lihat [Light-Sensor Recordings](#light-sensor-recordings--calibrated-daq--csv). |
| `configure(debayer=..., vignette_correction=..., reflectance_calibration=..., indices=[...], export_format=..., ppk=..., daq_log_path=..., input_level=..., radiometric_output=..., array_alignment=..., array_alignment_crop=..., array_alignment_interpolation=..., custom_settings=None)` | Tetapkan tombol pemprosesan. `indices` adalah nama pratetap (`NDVI`, `GNDVI`, `OSAVI`, …), masing-masing ditambah ke dalam projek untuk jalur kamera masing-masing; nama yang bukan pratetap, atau yang jalur tersebut tidak dapat dihasilkan (`NDRE` memerlukan RE dan NIR, yang tiada satu kamera pun sediakan), mendapat `UserWarning` yang menamakannya dan tiada imej indeks. `daq_log_path` menamakan rakaman penderia cahaya (fail `.daq` atau `.csv`, atau folder yang mengandungi fail-fail tersebut) yang setiap bingkai dibandingkan dengannya, LATTICE serta Survey3, bukannya rakaman yang diimport bersama imej; jika laluan itu tiada atau tidak mengandungi sebarang rakaman, dan rakaman yang diimport akan digunakan. |
| `process(mode="parallel", wait=True, progress_callback=None, poll_interval=2.0)` | Jalankan saluran paip. Mengembalikan `{"status": "complete", "async": False}`, ditambah kunci `summary` apabila backend menyediakan satu — lihat [Ringkasan Selepas Jalankan &amp; Petunjuk](#post-run-summary--hints) — `export_check_failure` apabila eksport larian tidak dapat disahkan, dan `image_failure` apabila ia kehilangan imej. `wait=False` memulangkan `{"status": "started", "async": True}` sekaligus. |
| `get_config()` / `get_status()` / `status()` | Periksa status backend. |
| `logout()` | Log keluar: memadam maklumat log masuk yang disimpan dalam cache dan membebaskan slot peranti mesin ini (`device_deregistration` dalam hasil). Jalannya pemprosesan yang sedang dijalankan tidak dihentikan: ia akan selesai dalam mod yang ia mulakan (`run_in_progress: true` dan `run_mode` dalam hasil), dan larian seterusnya menggunakan pemprosesan standard. |
| `shutdown_backend()` | Hentikan backend jika instans ini memulakannya; backend yang dijumpai sedang berjalan dibiarkan sahaja. Meninggalkan blok `with` atau instans yang sedang dikumpul sampah tidak menghentikannya: backend terus berjalan untuk skrip seterusnya dan menghentikan dirinya sendiri selepas 30 minit jika tiada apa-apa yang menggunakannya. Backend diminta untuk dimatikan, jadi ia melepaskan kamera dan penderia cahayanya serta menamatkan sebarang rakaman terlebih dahulu; ia dihentikan hanya jika ia masih berjalan 10 saat kemudian. |
| `discover_cameras()` | Menemui kamera LATTICE **melalui backend instans ini** (`/api/camera/discover`). Mengembalikan senarai dict (`serial`, `model`, `ip`, …) — bentuk yang sama seperti yang dilihat oleh GUI/CLI. Senarai kosong jika tiada ditemui atau backend tidak dapat dihubungi. |
| `camera_capture(output_dir, format="tiff", **settings)` | Menangkap satu bingkai**melalui backend**(dimulakan secara automatik oleh penangan ini) supaya ia mendapat penyediaan yang sama seperti GUI/CLI (lalai 12-bit, penggunaan semula kolam, metadata kalibrasi terbina dalam). Selesaikan sasaran dengan `serial=` atau `device_index=`; lalui `exposure`/`gain`/`pixel_format`/`preset` sebagai `**settings`. Mengembalikan dict metadata warisan (`filepath`, `width`, `height`, `pixel_format`, `exposure_time`, `gain`, `timestamp`). |
| `camera_stream(serial, *, fps=10.0, overlay=None, decode=True, connect_timeout=10.0, read_timeout=15.0)` | Menghasilkan bingkai pratonton yang disusun lapisan daripada kumpulan kamera — klien MJPEG nipis melalui laluan `/api/camera/<serial>/stream-annotated` pada backend (garis zebra / grid / sasaran silang / histogram / peaking / titik yang dilukis di sisi pelayan). `decode=True` menghasilkan susunan BGR dan memerlukan OpenCV (`cv2`), yang tidak dipasang oleh SDK; `False` menghasilkan bait JPEG mentah. Juga boleh diakses per-projek sebagai `ChlorosProject.stream(overlays=True)`. |

**Sebuah larian yang sedang dijalankan mengekalkan projeknya.** Semasa pelaksanaan pemprosesan sedang dijalankan — sama ada skrip ini, aplikasi, `chloros-cli process` atau `chloros-mcp` — backend enggan mencipta, membuka, mengimport ke dalam atau mengubah tetapan pemprosesan projek (HTTP 409 `PROCESSING_IN_PROGRESS`): `create_project()` dan `import_images()` menimbulkan `ChlorosProcessingError`, dan `configure()` mencetuskan `ChlorosConfigurationError`, dan `process()` dengan mod lain selain yang sedang berjalan mencetuskan `ChlorosProcessingError` (sesuatu larian mengekalkan mod yang digunakan ketika permulaannya), masing-masing dengan mesej backend. Rakaman yang disimpan ke dalam projek kemudian menyertainya apabila larian berakhir (balasan `project_add` menyatakan demikian). Tunggu sehingga larian selesai (`get_status()`), kemudian sambung.**Projek yang dipindahkan.** `project.json` projek menamakan setiap fail sumber (setiap imej dan rakaman penderia cahaya) mengikut laluan penuhnya, jadi memindahkan folder projek -- atau keseluruhan folder projek ke pemacu lain -- menyebabkan mereka terus menamakan tempat lama. Setiap kali dibuka, ia membetulkan apa yang boleh dilakukan sendiri: fail yang sebelum ini berada dalam folder projek dicari dalam folder projek yang baru, dan setiap laluan yang ditemui di sana diubah semula dan disimpan (`project.json` pertama kali disandarkan sebagai `project.json.before-relink-<date>-<time>`). Sesebuah laluan hanya akan dialihkan semula kepada fail yang wujud di situ. Fail yang berada di luar folder projek -- sesi hub, import Tambah Folder -- hanya ditemui dengan `relink=<the folder they are in now>` (atau mana-mana folder di atasnya). `open_project()` memulangkan `source_files`: `total`, `found`, `missing`, `missing_roots` (folder-folder di mana fail yang hilang berada), `missing_outside_project` dan `relink` (apa yang diarahkan semula, dari mana ke mana). Projek yang mempunyai beberapa fail hilang dibuka bersama fail-fail yang tinggal; projek yang tiada fail ditemui langsung tidak dibuka, dan `open_project()` mencetuskan `ChlorosProjectFilesMissingError` (`.missing_roots`, `.audit`; HTTP 409 `PROJECT_SOURCES_MISSING`). `project open --relink` dan `chloros-mcp` oleh CLIs `open_project` (`relink_folder`) melakukan perkara yang sama.

Gunakan sebagai pengurus konteks untuk pembersihan terjamin:

```python
with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("FieldA_2026-05-26", camera="Survey3N_RGN")
    cl.import_images("C:/DroneImages/Flight001")
    cl.configure(
        vignette_correction=True,
        reflectance_calibration=True,
        indices=["NDVI", "GNDVI", "OSAVI"],
        export_format="TIFF (16-bit)",
    )
    results = cl.process(mode="parallel", wait=True)
print(results["summary"])
```

### Rakaman Penderia Cahaya — kalibrasi `.daq` + `.csv`

DAQ-U / DAQ-M / DAQ-E boleh dirakam **tanpa** bundel penentukuran. Itulah yang dilakukan oleh [`chloros_scripts`](https://github.com/mapircamera/chloros_scripts) awam
perekod (`record_daq.py`) melakukan secara lalai: mereka menulis kiraan sensor mentah dan menandakan fail supaya Chloros memuat turun penentukuran kilang sensor itu **mengikut siri** — cache tempatan
pertama, kemudian Awan MAPIR — dan menerapkannya semasa pengimportan.

Chloros menulis semula keputusan itu sebagai dua produk bagi setiap rakaman, di bawah
`<project>/Light Sensor/`:

| Produk | Apa itu |
| --- | --- |
| `<name>_calibrated.daq` | Arkib yang boleh diproses semula — skema yang sama seperti rakaman langsung, kini menyatakan bundel yang menjana ia. Mengimportnya semula **tidak** akan mengkalibratnya buat kali kedua. |
| `<name>_calibrated.csv` | Iradiasi spektral dalam W/m²/nm pada grid panjang gelombang tersendiri sensor, satu baris bagi setiap bacaan, ditambah lajur fotometrik (kuasa total, lux fotopik/skotopik, PPFD dan pembahagian biru/hijau/merah, panjang gelombang puncak). |
| `<name>_raw.daq` / `<name>_raw.csv` | **Hanya sensor tanpa bundle (DAQ-A).** Bilangan spektral mentah sensor — *bukan* iradiasi. Lihat di bawah. |

`<name>` ialah nama fail rakaman itu sendiri. Dua rakaman dalam folder berbeza boleh berkongsi
satu — bacaan setiap tangkapan (`<capture>/daq/daq_data_<sensor id>_<ts>.daq`) disimpan dalam milisaat yang sama oleh dua kamera — dan masing-masing masih mendapat produknya sendiri: yang pertama mengekalkan
`<name>`, yang seterusnya menambah folder yang membezakannya **selepas** nama
(`<name>__<capture>_daq_calibrated.daq`), jadi tarikh dan masa rakaman masih diutamakan.

`process()` melakukan eksport ini sebagai salah satu peringkatnya. Ia **tidak** memerlukan imej: penderia cahaya yang diterbangkan bersendirian adalah aliran kerja kelas pertama, dan projek seperti itu mempunyai sifar imej secara binaannya.

Rakaman DAQ-A dieksport sebagai kiraan mentah. Keluarga DAQ-A wujud sebelum sistem bundel per-siri dan tiada bundel untuk diambil — ia dikalibrasi di lapangan terhadap sasaran pantulan sebaliknya, itulah sebabnya ia tidak pernah memerlukannya. Rakaman-rakaman tersebut dieksport di bawah stem `_raw` dan bukannya `_calibrated`: nama fail yang berbeza dan bukannya penanda di dalam fail, kerana tuntutan itu perlu kekal walaupun dihantar melalui e-mel sebagai nama kosong. The
Kepala `.csv` menyatakan `raw spectral sensor counts (NOT irradiance)` dan memberi amaran bahawa nilai-nilai itu boleh dibandingkan **dalam** fail tersebut — tepat seperti yang digunakan oleh penentukuran berasaskan sasaran
mereka untuk — dan bukan merentas sensor. Lajur fotometrik bergantung kuasa (kuasa total, lux fotopik/skotopik, PPFD) dikembalikan **NULL** dan bukannya diintegrasikan daripada kiraan.

DAQ-U / DAQ-M / DAQ-E yang bundelnya tidak dapat dimuat turun akan tetap **dilangkau**, bukan ditulis secara mentah: dalam kes ini, bundel tersebut wujud dan &quot;sambung semula dan proses semula&quot; adalah nasihat yang benar.

Rakaman warisan **v1.01 / v1.02** (yang ditulis oleh DAQ-A-SD) tidak mempunyai epoch bagi setiap bacaan, hanya masa penulisan fail. Pencocok imej↔downwelling masih menolaknya — memadankan a
kerana memadankan bingkai dengan masa penulisan akan salah secara tidak kelihatan — tetapi pengeksport membacanya, dan CSV mencetak `clock=daq_created_on` supaya produk menyatakan jam mana yang digunakannya.

```python
import chloros_sdk

with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("DAQ-U_2026-08-26")
    cl.import_images("C:/Flights/raw_daq")     # .daq only — no camera involved
    result = cl.export_light_sensor()          # or just cl.process()

for rec in result["exported"]:
    print(rec["csv"])
for rec in result["skipped"]:
    print("skipped", rec["source"], "--", rec["reason"])
```

Sebuah rakaman yang bundel kalibrasinya tidak dapat diambil (luar talian, atau penderia tanpa
kalibrasi dalam fail) dilaporkan di bawah `skipped` **dengan sebabnya**. Ia tidak pernah ditulis sebagai fail &quot;dikalibrasi&quot; yang mengandungi kiraan mentah — sambungkan ke internet dan jalankan semula, dan eksport akan selesai.

### Panggilan Balikan Kemajuan

```python
def show_progress(percent, message):
    print(f"[{percent:3d}%] {message}")

with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("FieldA")
    cl.import_images("C:/DroneImages/Flight001")
    cl.configure(indices=["NDVI"])
    cl.process(progress_callback=show_progress, poll_interval=1.0)
```

### Ringkasan Selepas Jalankan &amp; Petua

Setelah selesai, `process()` memuatkan `GET /api/processing-summary` dan melampirkan badan sebagai `result["summary"]`. Pemuatan ini adalah usaha terbaik dan tidak pernah menghalang pemulangan yang berjaya — jika ringkasan tidak tersedia, `process()` kembali ke bentuk `{"status": "complete", "async": False}` biasa. Setiap entri dalam `summary["hints"]` — ayat penuh dengan cadangan pembetulan, contohnya mengapa sesuatu larian menghasilkan sifir keluaran — juga dihantar semula sebagai `UserWarning` Python, jadi larian sifir-keluaran mendiagnosis sendiri walaupun anda tidak pernah memeriksa dict:

```python
result = cl.process()
for hint in result.get("summary", {}).get("hints", []):
    print("HINT:", hint)
# hints also arrive on the warnings channel:
#   python -W always::UserWarning your_script.py
```

`summary["totals"]` ialah separuh yang boleh dibaca oleh mesin:

| Kunci | Apa yang dikira |
| --- | --- |
| `models` | Kumpulan kamera dalam larian. |
| `images_in_groups` | Imej sumber merentasi kumpulan-kumpulan tersebut. |
| `targets_found` | Sasaran pantulan yang dikesan. |
| `images_calibrated` | Imej yang run tulis produk reflektansi untuknya. |
| `images_failed` | Imej yang gagal dan tidak dieksport. |
| `exported_files` | **Fail produk imej jalanan menulis**: setiap produk (debayered, pratonton, radiasi, pantulan, indeks, render sasaran), dalam setiap format. |
| `reflectance_files` | Produk pantulan di antaranya (`Reflectance_Calibrated_Images`), sama ada sasaran atau pancaran sensor cahaya (DAQ) mengkalibrasikannya. Sebarang larian tanpa sasaran yang masih menulis keluaran pantulan akan memberitahu demikian dalam petunjuk; hanya larian yang tidak menulis sebarang keluaran pantulan akan diberitahu bahawa keluaran pantulannya hilang. |
| `clamp_masks_written` | [topeng clamp unmix](#unmix-clamp-mask--which-pixels-a-band-was-clamped-on) yang ditulis di sebelahnya — rakan, bukan produk, jadi tidak dalam `exported_files` (CLI mengiranya secara berasingan dengan cara yang sama). |
| `captures_left_out` | Bingkai mentah dalam folder `captures` projek yang tidak diambil oleh larian ini. Sebuah larian menunggu semasa permulaan untuk apa yang dirakam sebelum ia, jadi ini adalah 0 melainkan tangkapan telah disimpan semasa larian atau menunggu telah tamat. `summary["captures_left_out_note"]` menyatakan apa yang perlu dilakukan tentangnya. |
| `daq_recordings_exported` / `daq_recordings_skipped` | Rakaman sensor cahaya, dikira secara berasingan dengan sengaja — ia berasal dari peringkat yang berbeza dan wujud untuk larian tanpa sebarang imej, jadi menggabungkannya akan membuat larian hanya-DAQ kelihatan seolah-olah ia mengeksport imej. |

Di sampingnya: `summary["output_dirs"]` (setiap direktori yang ditulis),
`summary["light_sensor_export"]`, `summary["stopped"]` (benar apabila pengguna memotong
jalannya, supaya kiraan separa tidak terbaca sebagai larian yang selesai tetapi menghasilkan kurang daripada yang dijangkakan), dan `summary["groups"]` (pecahan mengikut kumpulan).

`exported_files` direkodkan oleh saluran paip **semasa ia menulis**, bukan diimbas daripada objek imej projek kemudian. Strategi selari dan GPU membina objek imej mereka sendiri (dalam subproses pekerja untuk laluan GPU), jadi imbasan lama yang dilaporkan
`0 file(s) written` untuk setiap larian sedemikian dan kemudian mengeluarkan petunjuk sifar-eksport — pada larian di mana semuanya berjaya. Jika anda menulis skrip berdasarkan nombor ini, larian selari yang sihat kini melaporkan bilangan tidak sifar.

Laporan sensor cahaya melangkau melaporkan sebab sebenar yang ditetapkan oleh pembaca untuk setiap fail — skema yang tidak dapat dibaca, bundel yang hilang, ralat penulisan — **dihapuskan duplikatnya**, jadi dua puluh fail yang dilangkau kerana satu sebab dilaporkan sebagai satu sebab dan bukannya dua puluh ulangan sebab itu.

> **`process()` tidak diangkat apabila suatu pelaksanaan tidak menghasilkan sebarang imej.** Ini adalah salah satu daripada dua tempat di mana SDK dan CLI sengaja berbeza: `chloros-cli process` menganggap &quot;produk telah diminta, tiada yang ditulis&quot; sebagai kegagalan dan keluar dengan nilai bukan sifar, manakala SDK kembali seperti biasa dan melaporkan
> keadaan melalui `summary` / petunjuk. Jika saluran paip anda sepatutnya berhenti pada larian kosong, periksa sendiri — semak `summary` (atau kira fail di bawah folder projek) daripada bergantung pada ketiadaan pengecualian. Punca biasa adalah folder input yang tidak diiktiraf sebagai tangkapan dan produk yang dilangkau kerana tidak terpakai untuk kamera yang ada (contohnya sinaran daripada kamera hanya RGB).
>
> `process()` tidak menimbulkan ralat apabila eksport sesuatu larian tidak dapat disahkan juga: ia memberi amaran dan menetapkan
> `result["export_check_failure"]`. Produk-produk tersebut telah ditulis. Untuk menghentikan proses pada ketika itu, semak kunci tersebut. Jalnan yang kehilangan imej adalah sama: ia memberi amaran dan menetapkan `result["image_failure"]`, yang menyebabkan `chloros-cli process` keluar dengan nilai bukan sifar.

### Fungsi Kemudahan

```python
# One-call process: project + import + configure + process
results = chloros_sdk.process_folder(
    folder_path="C:/DroneImages/Flight001",
    project_name="FieldA_2026-05-26",
    camera="Survey3N_RGN",
    indices=["NDVI", "GNDVI", "OSAVI"],
    vignette_correction=True,
    reflectance_calibration=True,
    export_format="TIFF (16-bit)",
    mode="parallel",
    debayer="High Quality (Faster)",      # or "Texture Aware (Slow, Highest Quality)"
    ppk=False,
    recursive=False,
    processing_timeout=14400,
)

# LATTICE-friendly defaults (no panel-target detection, standard debayer)
results = chloros_sdk.process_lattice_capture(
    folder_path="C:/Captures/2026-05-13_Field",
    indices=["NDVI"],
)

# process_folder() / process_lattice_capture() also take input_level=,
# array_alignment=, array_alignment_crop=, array_alignment_interpolation=,
# progress_callback=, poll_interval=, timeout=, processing_stuck_timeout=,
# backend_exe= and api_url= (NOT backend_url=). indices defaults to ["NDVI"].

# Audit which calibration sources were applied to a processed image
tags = chloros_sdk.read_image_audit_tags("output/Reflectance_Calibrated/x.tif")
print(tags["CalibrationSource"])   # 'per_serial' / 'legacy_lookup' / 'none'
print(tags["VignetteSource"])      # 'per_serial' / 'legacy_polynomial' / 'none'
```

Setiap proses berjalan di dalam `with ChlorosLocal(...)` sendiri; backend yang perlu dihidupkan terus berjalan apabila ia kembali, untuk panggilan seterusnya, dan menghentikan dirinya sendiri selepas 30 minit jika tiada apa-apa yang menggunakannya. Tiada satu pun mengambil `auto_start_backend`, `radiometric_output` atau `daq_log_path`; gunakan `ChlorosLocal` dan `configure()` untuk yang tersebut. `process_lattice_capture()` tidak menggunakan `camera`, `debayer` atau `ppk`; ia melangkau pengesanan panel dan menggunakan debayer piawai.

### Nilai Disokong

```python
# export_format
"TIFF (16-bit)"           # default, recommended
"TIFF (32-bit, Percent)"  # reflectance percentage as float32
"TIFF (8-bit)"            # all-8-bit images are written as this; never raised
"PNG (8-bit)"
"JPG (8-bit)"

# debayer
"High Quality (Faster)"               # standard, default
"Texture Aware (Slow, Highest Quality)"  # neural debayer for Survey3 imagery, Chloros+ only.
                                         # Survey3 only: on a project holding LATTICE captures
                                         # (which always use the standard demosaic) configure()
                                         # or process() raises ChlorosConfigurationError; a
                                         # Texture Aware value the project saved earlier is set
                                         # to Standard with a UserWarning, as the app does. Needs a
                                         # CUDA GPU the backend's PyTorch can use (an NVIDIA
                                         # GPU, or a Jetson Orin's). Without one, process()
                                         # uses the standard debayer, and the backend prints
                                         # one "[DEBAYER] ... needs a CUDA GPU" line saying why.
"Standard (Fast, Medium Quality)"      # alias used internally for LATTICE

# input_level (LATTICE only; Survey3 .raw ignores)
"auto"        # default — infers from each file's XMP ProcessingLevel tag
"raw"         # force-treat as raw Bayer
"debayered"   # force-treat as Chloros's saved debayered (3-channel) product
"processed"   # force-treat as already-calibrated radiance

# array_alignment / array_alignment_crop (LATTICE arrays; None = keep saved setting)
True          # backend default — apply the module-to-module transform stamped
              # in each capture's Chloros:Alignment* XMP to every product
False         # export in native sensor geometry / skip the common-overlap crop

# array_alignment_interpolation (alignment warp resampling)
"bilinear"    # backend default
"nearest"     # preserves exact source DNs (no inter-pixel value mixing)
"cubic"
```

#### Keluaran Radiometrik (saluran multispektral LATTICE)

Tahap eksport multispektral LATTICE (M3C/M3M) saluran `process` — `reflectance` (lalai), `radiance`, `sensor-response`, atau `all` (setiap mod terpakai bagi setiap imej) — menetapkan **&quot;Eksport radiasi&quot;**dan**&quot;Eksport pantulan&quot;produk: `radiance` mengeksport sinaran sahaja, `reflectance` pantulan sahaja, `sensor-response` tiada kedua-duanya (hanya produk debayered dan pratonton), `all` kedua-duanya. Produk debayered dan pratonton bukan tahap yang boleh diubah. `configure()` mempunyai kata kunci khusus untuknya:

```python
with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("Field_A")
    cl.import_images("C:/Captures/lattice_flight")
    cl.configure(
        radiometric_output="radiance",   # reflectance (default) / radiance / sensor-response / all
        export_format="TIFF (32-bit, Percent)",
    )
    cl.process()
```

Lubang pelarian lanjutan — menulis produk melalui `custom_settings` — masih berfungsi, tetapi ingat ia menggantikan keseluruhan blok tetapan (lihat amaran di bawah). Tulislah kunci `"Export <product>"`: `"Export radiance"` projek itu sendiri / Peraturan `"Export reflectance"` mengatasi kunci `"Radiometric output"` yang lebih lama, jadi kunci itu sahaja tidak mengubah apa-apa.

```python
cl.configure(custom_settings={
    "Project Settings": {
        "Processing": {"Export radiance": True, "Export reflectance": False},
        "Export": {"Calibrated image format": "TIFF (32-bit, Percent)"},
    }
})
```

`reflectance` (lalai) membahagikan sinaran kamera dengan **sinar ke bawah DAQ yang sepadan mengikut cop masa**, diselesaikan secara automatik daripada `.daq` (DAQ-U/M/E) yang dirakam**atau `.csv` asli DAQ-M**yang ditemui bersama imej; sebarang bundel kalibrasi per-kamera atau DAQ yang hilang secara tempatan akan**diperolehi secara automatik daripada AWS** pada penggunaan pertama. CLI mendedahkan ini seperti per-produk jenis togol pada `chloros-cli process`: `--radiance`/`--no-radiance`, `--reflectance`/`--no-reflectance`, `--debayered`, `--preview`.**Siapa yang membacanya.** Bacaan yang disimpan oleh `.daq` adalah bacaan kamera (atau kamera-kamera) yang diterangkannya — dinamakan dalam fail sidecar `<file>.daq.cameras.json` di sisinya (rekod tunggal, diselaraskan dan susunan; burst mentah menamakan bacaan setiap ahli dalam manifesnya) — dan hanya dipadankan dengan imej daripada kamera-kamera tersebut. Kamera berdiri sendiri yang tiada bacaan sendiri oleh itu tidak lagi meminjam DAQ kamera lain; ikat DAQ padanya, atau rakam satu. Rakaman DAQ itu sendiri (`chloros-cli daq pool-record`, atau rakaman yang dimulakan dalam GUI), atau mana-mana `.daq` tanpa fail sampingan itu (termasuk tangkapan lama), masih berfungsi untuk setiap kamera.**Kamera OCN dalam cahaya matahari.** Setiap pek penentukuran OCN dibina di bawah cahaya jalur sempit, satu LED bagi setiap jalur, manakala cahaya matahari mencampurkan jalur-jalur tersebut. Chloros 1.3.0 menerapkan pembetulan asas matahari secara keseluruhan selepas pemisahan jalur, yang meningkatkan produk sinaran, pantulan dan indeks OCN dalam cahaya matahari. Ia berjalan di mana sahaja unmix dijalankan — `process()`, captures, `grab()` / `frame_stream()` dan live view — untuk pek OCN yang menyokongnya; kamera RGN kekal tidak berubah. Imej OCN yang dirakam dengan versi sebelumnya turut mendapatkannya apabila diproses pada komputer yang menyimpan penentukuran yang digunakan semasa ia dirakam (Chloros menyimpan penentukuran kamera setelah kamera disambungkan); jika tidak, ia diproses seperti sebelum ini. Oleh itu, nilai sinaran, pantulan dan indeks OCN berbeza daripada versi 1.2.0.

> `custom_settings` **menggantikan** keseluruhan blok tetapan yang dikira (ia memintas kata kunci dan pengesahan lain `configure()` secara sengaja). Apabila anda menggunakannya, sertakan setiap kunci `Project Settings` yang anda perlukan, seperti dalam contoh di atas.

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

Baca dan terbalikkan, menormalkan DN yang disimpan kepada 0…1 terlebih dahulu mengikut had maksimum dtype yang disimpan supaya satu ungkapan merangkumi setiap format integer:

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

> **`IndexMin == IndexMax`** bermaksud setiap piksel pada bingkai itu menyimpan nilai indeks yang sama. Raster diisi pada pertengahan skala dan tiada apa-apa untuk dibahagikan — ambil titik akhir.

> **Tiada tag `Chloros:Index*` bermaksud tiada pemetaan yang dinyatakan.** Eksport LUT adalah render berwarna yang pikselnya berwarna gradasi yang dipautkan kepada tetingkap ambang LUT anda, dan satu bingkai tanpa piksel indeks terhingga tidak mempunyai julat untuk dinyatakan. Jangan andaikan terdapat pemetaan dalam mana-mana kes — peraturan yang sama seperti `PixelScale` yang hilang.**Kerana regangan adalah bagi setiap imej, dua bingkai daripada satu penerbangan tidak boleh dibandingkan sebagai DN**— masing-masing membawa titik hujungnya sendiri, jadi NDVI yang sama mendarat pada DN yang berbeza dalam imej yang berbeza. Untuk kerja kuantitatif merentasi penerbangan (statistik zonal, ambang tetap, siri masa), eksport**`TIFF (32-bit, Percent)`**: raster indeks ditulis di sana sebagai nilai float32**nilai indeks itu sendiri**, tanpa penskalaan semula dan tanpa tag yang diperlukan. Walaupun nama format ini, produk indeks bukan ×100 — penskalaan itu hanya terpakai pada reflektansi jenis integer-dtype.

#### Topeng pengekang pencampuran — piksel mana satu jalur telah dikekang

Pencampuran spektral 3×3 boleh menjadikan jalur negatif — di mana cahaya hampir tidak mengandungi jalur tersebut, atau di mana pemandangan berada di luar apa yang diterangkan oleh matriks pencampuran kamera. Nilai itu bukan nilai sebenar, jadi eksport menetapkan nilainya kepada 0 — dan paparan langsung menunjukkan 0 yang sama. Sesebuah jalur boleh dikekang pada beberapa piksel atau pada hampir kesemuanya; topeng merekodkan kedua-duanya. `Chloros:Band{R,G,B}UnmixClampFraction` mengatakan berapa banyak bingkai yang terjejas; topeng kekangan mengatakan **piksel mana**:

- **`clamp_masks/<product folder>/<product name>_clampmask.tif`**— dalam folder `clamp_masks/`**di sebelah** folder produk, tidak pernah di dalamnya (`radiance/x_radiance.tif` → `clamp_masks/radiance/x_radiance_clampmask.tif`) — untuk setiap produk radiasi, pantulan dan indeks di mana sekurang-kurangnya satu piksel diklamp. `uint8` jalur tunggal, pada grid piksel produk itu sendiri (orientasi, ROI, binning dan warp penjajaran susunan termasuk). Setiap topeng menamakan kamera produknya (`Make` / `Model` / siri), masa tangkapan dan tag lensa, dan menyenaraikan jalur yang diwakili oleh bitnya (`XMP-Camera:BandName`, bit 0 dahulu); `ImageDescription` dan `Software` menandakannya sebagai topeng clamp, dan ia tidak mempunyai kalibrasi.
- **Bit**: bit 0 = jalur dalam saluran R produk (`Chloros:BandRName`), bit 1 = G (`BandGName`), bit 2 = B (`BandBName`). Bit yang ditetapkan bermaksud nilai piksel dalam jalur tersebut telah dipaku pada 0 — anggap ia, dan sebarang indeks yang dibina daripadanya, sebagai rendah keyakinan.
- **`Chloros:UnmixClampMask`** dicop pada setiap produk yang menjalankan unmix: laluan topeng berbanding folder produk itu sendiri (`../clamp_masks/<product folder>/<product name>_clampmask.tif`, sentiasa `/`-dipisahkan — gabungkannya dengan folder imej untuk membuka topeng), atau `none` apabila tiada piksel dikekang. Tiada tag bermaksud produk itu tidak pernah menjalankan unmix (kamera RGB atau mono, tahap raw/debayered).
- Keputusan tangkapan membawanya sebagai **`clamp_mask`** bingkai (jalur topeng, atau `None` apabila tiada piksel dikekang) — dalam keputusan `CameraSession.capture()`, `ArraySession.capture()`&#x27;s `CaptureResult` bingkai dan respons tangkapan HTTP. Pembinaan luar talian menyenaraikan topengnya sebagai `clamp_masks`.
- Susun lapisan berbilang jalur indeks langsung (`/api/camera/cli-array-index` dengan `save_multiband`) mendapat satu topeng bagi setiap kamera sumber, dalam `clamp_masks/` milik folder keluaran itu sendiri — lihat rujukan CLI`lattice index`. Video tidak mempunyai topeng: hasil binaan luar talian melaporkan sebaliknya pecahan clamp bagi setiap bingkai.

Kerana tiada folder produk mengandungi topeng, pada mana-mana kedalaman, folder produk boleh diberikan kepada perisian fotogrametri seperti sedia ada — termasuk subfolder — dan ia hanya mengambil imej. Serahkan folder produk itu kepadanya, bukan folder di atasnya. Import Chloros sendiri melangkau topeng.

```python
import os, numpy as np, tifffile

mask = tifffile.imread(
    "clamp_masks/radiance/capture_<ts>_SN<serial>-<filter>_radiance_clampmask.tif")
# or, from the image: os.path.join(os.path.dirname(image_path), tag), with
# tag = the image's Chloros:UnmixClampMask
r_clamped = (mask & 1) != 0      # the band Chloros:BandRName names
g_clamped = (mask & 2) != 0
b_clamped = (mask & 4) != 0
```

---

## Smart-Connect untuk Kamera LATTICE

Sesi backend berterusan untuk perkakasan langsung. Titik hujung yang sama digunakan oleh GUI, jadi kelakuannya adalah sama di antara SDK / CLI / GUI.

### Kamera Tunggal — `CameraSession`

```python
import chloros_sdk

# Open by serial; reuses existing pool entry if one exists
with chloros_sdk.connect_camera("213800234") as cam:
    # cam is a CameraSession; supports context manager + manual disconnect
    cam.set_settings(
        exposure_time=10000,    # microseconds
        gain=0.0,               # dB
        pixel_format="BayerRG12",
        target_brightness=80,
        ae_damping=8.0,
    )
    cam.capture("output/", ext=".tiff")
```

Pendedahan, penguatan dan tetapan sasaran di sini dikekalkan sebagai tetapan anda: kawalan pendedahan automatik Chloros (termasuk pendedahan yang diukur oleh DAQ) tidak mengubahnya. `cam.set_settings(exposure_auto="Continuous")` (atau `gain_auto=`) memulangkan pendedahan (penguatan), dan `cam.set_settings(target_brightness_auto=True)` menyerahkan target semula kepada target automatik Chloros.

#### Tandatangan `connect_camera()`

```python
connect_camera(
    serial,
    *,
    preset=None,                       # "default" | "high_quality" | "high_speed" | "triggered"
    settings=None,                     # dict overlaid on the preset
    backend_url="http://127.0.0.1:5000",  # deliberately not 'localhost' (::1-first on Windows ≈ 2 s/request)
    timeout=60.0,
    auto_start_backend=True,           # spawn a local backend if none is running
) -> CameraSession
```

- **Ia kembali dengan aliran telah dimulakan.** Backend menunggu, terhad (kira-kira 15 saat untuk aliran bermula, kemudian 10 saat untuk buffer pertamanya yang lengkap — bukan untuk bingkai pratonton pertama, yang muncul kira-kira 1.5 saat kemudian), jadi `status()` sejurus selepas ia mengatakan `streaming`. `connect_stream` sesi adalah blok `stream` balasan: `state` `streaming`, `no_frames` (aliran bermula dan tidak menghantar apa-apa — `note` menyatakan demikian dan, untuk kamera pada paket jumbo, menyambung semula dengan saiz paket 1500 bait), `starting` atau `start_failed`, dengan `waited_s`. `None` untuk kamera yang sudah disambungkan. Pratonton (`get_camera_preview`, `/api/camera/jpeg/<serial>`) meminta sebaik sahaja selepas menunggu, terhad (4 s), untuk bingkai pratonton pertama kamera bukannya menjawab &quot;Tiada bingkai tersedia lagi&quot;; kamera yang aliran streamnya tidak menghantar apa-apa (`no_frames`, atau diaktifkan untuk pencetus perkakasan) masih menjawab serta-merta.
- **Saiz paket adalah terpulang kepada anda.** `settings={"gev_packet_size": 1500, "gev_packet_size_auto": False}` membuka kamera pada paket bersaiz 1500 bait, tidak diterokai (`chloros-cli lattice cam-connect --packet-size standard` menghantar saiz yang sama).
- **Satu sambungan pada satu masa mengekalkan kolam kamera**: pembukaan, penjejakan saiz paket, muat turun penentukuran pertama (kira-kira 70 saat untuk kamera baharu pada komputer ini), permulaan aliran. Permintaan yang memerlukan kamera pada masa itu (`read_nodes`, `set_settings`, capture, sambungan kedua) menunggu sehingga 5 saat, kemudian gagal dengan HTTP 503 `CAMERA_POOL_BUSY`, menamakan kamera yang disambungkan (`ChlorosConnectError`); tanya sekali lagi dalam sekejap. Senarai tidak pernah menunggu: `list_cameras()` (`GET /api/camera/pool`), `list_arrays()` dan `CameraSession.status()` menjawab serta-merta. Kamera yang sedang disambungkan disenaraikan di bawah `connecting` dalam jawapan kolam (`serial`, `phase` — `opening the camera`, `rebalancing the pool bandwidth`, `loading its calibration`, `starting its stream`, `waiting for its stream` — dan `elapsed_s`), dan `status()` miliknya mengatakan `connecting: True` dengan `phase` miliknya; kamera-kamera lain dibaca secara langsung apabila kolam bebas, jika tidak, ia menjawab daripada bacaan terakhir mereka (`live: False`).

#### Kaedah `CameraSession`

| Kaedah | Deskripsi |
| --- | --- |
| `status(timeout=15.0)` | Keadaan siaran langsung kamera, seperti yang dibaca oleh `chloros-cli lattice status`: `streaming`, `stream_state` (`starting`, `no_frames`, `streaming`, `start_failed` atau `stopped`), `device` (model, siri, firmware, suhu, `is_mono`) dan tetapan semasa. |
| `read_nodes(names, enum_names=(), timeout=30.0)` | Baca nod GenICam; memulangkan `{nodes, errors, enums, device, was_in_pool, in_array_id}`, ditambah `frame_values` dan `notes` semasa pendedahan automatik dijalankan (lihat di bawah). |
| `set_settings(**kwargs)` | Menulis nod mengikut nama mesra (`exposure_time`, `gain`, `pixel_format`, `width`, `height`, `target_brightness`, `target_brightness_auto`, `ae_damping`, `ae_upper_limit`, `trigger_mode`, `trigger_source`, …). `exposure_time` (`gain`) adalah pendedahan manual (penguatan): ditulis tanpa `exposure_auto` (`gain_auto`), iaitu mod auto dimatikan terlebih dahulu, supaya nilai itu tidak ditolak oleh mod auto yang sedang berjalan. Keputusan `applied` menyimpan apa yang diambil oleh kamera; penulisan yang ditolak oleh kamera disimpan dalam `errors` dan tidak dikekalkan. Ralat itu menyatakan julat yang kini diterima oleh kamera (maksimum `ExposureTime` mengikut kadar bingkai kamera), dan mod auto yang dimatikan hanya untuk nilai yang ditolak akan dihidupkan semula; `restored` menyenaraikannya (contohnya `{"ExposureAuto": "Continuous"}`). |
| `capture(output_dir="output", ext=".tiff", jpeg_quality=95, processing=None, levels=None, force_daq=None, daq=None, daq_tolerance_ms=None, settings=None, timeout=None)` | Tangkap satu **tunggal** bingkai. Mengembalikan `CaptureResult`: senarai dict metadata bingkai (satu bagi setiap tahap yang disimpan), dengan `.skipped`, `.failed`, `.daq_files` dan `.daq_matches`. (Perekodan berterusan/ramai bingkai telah dialih keluar — panggil `capture()` dalam gelung jika anda memerlukan siri.) |
| `capture_repeated(output_dir="output", *, count=None, duration_s=None, interval_s=0.0, on_capture=None, **capture_kwargs)` | Rakaman berturut-turut (`interval_s=0`) atau rakaman selang masa daripada kamera ini, untuk tangkapan `count` dan/atau `duration_s` (sekurang-kurangnya satu, supaya ia berhenti), seperti yang dilakukan oleh `ArraySession.capture_repeated()` untuk satu tatasusunan. Mengembalikan senarai `CaptureResult`s; ia berhenti selepas tangkapan yang melaporkan pemacu penuh. `on_capture(i, result)` dijalankan selepas setiap satu; kata kunci lain dihantar ke `capture()`. |
| `disconnect(keep_settings=False, clear_settings=False)` | Pembebasan daripada kolam, kamera dikembalikan seperti keadaan semasa disambung (pemasangan pencetus, PTP, tetapan); `keep_settings=True` menyimpan tetapan ke dalam memori sendiri, `clear_settings=True` menetapkan semula memori itu (lihat [Memori kamera](#camera-memory--keep_settings--clear_settings)); mengembalikan `camera_memory` apabila mana-mana satu diminta. Kamera yang merupakan ahli tatasusunan akan membebaskan keseluruhan tatasusunan. Tiada kesan jika kita disambungkan ke sesi yang sudah terbuka. Mengembalikan nilai apabila backend mengesahkan pelepasan, atau apabila tiada apa-apa untuk dilepaskan (kamera sudah tiada, atau tiada backend yang sedang berjalan); pelepasan yang tamat masa atau yang ditolak oleh backend akan membangkitkan `ChlorosConnectError`, kerana kamera mungkin masih dipegang dan sambungan seterusnya akan bertembung dengannya. Keluar daripada blok `with` tidak pernah membangkitkan. |

Atribut: `serial`, `device` (rekod penemuan kamera) dan `backend_url`.

Kawalan eksport `capture()` (model yang sama seperti susunan + GUI):

- `processing` / `levels` — `processing="all"` menyimpan setiap jenis eksport yang terpakai; `levels=["raw","radiance"]` menyimpan hanya yang tersebut (mengatasi `processing`). Abaikan kedua-duanya untuk lalai backend.
- `daq` — bacaan DAQ untuk tangkapan yang digunakan disimpan sebagai fail sampingan `.daq` secara lalai, dengan tangkapan mentah sahaja disertakan, supaya imej tersebut boleh diproses semula menjadi pantulan/indeks kemudian. `daq=False` melangkauinya apabila tiada tahap menggunakannya. `force_daq=True` ialah ejaan lama bagi lalai. Tiada DAQ, tiada `.daq`.
- `daq_tolerance_ms` — sejauh mana bacaan boleh dibuat daripada pendedahan dan masih berada dalam toleransi (lalai 500). Hanya keputusan yang berubah.

#### `read_nodes()` di bawah pendedahan automatik — `frame_values` dan `notes`

Semasa `ExposureAuto` (`GainAuto`) berjalan, `ExposureTime` (`Gain`) nod membaca semula **nilai terakhir yang ditulis**, bukan apa yang sedang dilakukan oleh kamera. `nodes` masih mengekalkan nilai nod tersebut. Apabila anda meminta `ExposureTime` atau `Gain` pada kamera yang disambungkan yang mod automatiknya bukan `Off`, balasan menambah:

- `frame_values` — nilai yang dibawa oleh bingkai terbaru kamera, di bawah nama nod (`ExposureTime` dalam µs, `Gain` dalam dB);
- `notes` — satu ayat bagi setiap nod sedemikian yang menyatakan kedua-dua nilai.

```python
r = cam.read_nodes(["ExposureTime", "Gain"])
print(r["nodes"]["ExposureTime"])                  # last value written, e.g. 2000.448
print(r.get("frame_values", {}).get("ExposureTime"))  # what the newest frame used, e.g. 19600.0
for name, note in r.get("notes", {}).items():
    print(note)
# ExposureAuto is Continuous: ExposureTime holds the last value written (2000.448);
# the camera's newest frame was at 19600 us.
```

Kedua-dua kunci tiada apabila mod automatik adalah `Off` (nod itu kemudiannya adalah kameranilai sebenar) dan untuk kamera yang tidak disambungkan ke backend. Untuk tatasusunan, `ArraySession.status()["member_ae"]` memberikan nilai bingkai setiap ahli sekaligus.

#### Had pendedahan DAQ-metered (HTTP)

Dengan penderia cahaya DAQ yang disambungkan, Chloros mengehadkan pendedahan × penguatan kamera daripada cahaya siang yang diukur supaya panel rujukan putih tidak terpotong. Had ini hanya terpakai selagi sasaran penentukuran berada dalam pandangan kamera tersebut (dilihat dalam bingkai dalam masa 10 saat terakhir); jika tiada sasaran dalam pandangan, pendedahan automatik biasa kamera, dengan perlindungan sorotan tersendiri, akan dijalankan sebaliknya, supaya bahagian lain dalam adegan tidak menjadi lebih gelap daripada yang diperlukan. Tiada pembalut SDK; baca atau tukarnya bagi setiap kamera dengan `GET` / `POST /api/camera/<serial>/daq-exposure-cap` (badan POST `{"enabled": false}` akan mematikannya bagi kamera tersebut). Balasan mengandungi `enabled`, `cap_us` (bumbung langsung, `None` semasa tidak aktif, termasuk apabila tiada sasaran penentukuran dalam pandangan), `k_exp_g` (konstanta yang digunakan untuk mengira siling) dan:

| Medan | Maksud |
| --- | --- |
| `k_exp_g_source` | Konstanta yang digunakan: `camera` — milik kamera ini sendiri, dikira daripada titik lab dalam pek kalibrasinya (kamera RGB); `fleet` — pemalar armada, digunakan apabila pek tiada titik makmal; `override` — tetapan keutamaan medan yang ditetapkan pada backend. |
| `k_exp_g_fleet` | Konstanta armada, untuk perbandingan. |

### Susunan Selari — `ArraySession` (Smart-Prep)

`connect_array` adalah **titik permulaan yang disyorkan** untuk susunan berbilang kamera. Ia menjalankan aliran penyediaan pintar GUI penuh di sebalik tabir:

1. **Analisis rangkaian**(`/api/camera/array/recommend`) — menetapkan**sensor penuh** (tiada binning, tiada pemotongan ROI) dan membiarkan kadar bingkai jatuh mengikut apa yang dihantar oleh hos dan rangkaian ini, dengan anggaran format piksel dan pembungkusan yang akan digunakan oleh sambungan. Bingkai tidak akan diperkecil untuk mengekalkan kadar: gunakan `width` / `height` atau `binning=2` / `4` untuk menukar resolusi demi kadar bingkai (titik akhirs `alternatives` menyenaraikan pilihan-pilihan tersebut dengan fps yang dijangka).
2. **Pemilihan automatik lapisan** — `sim-capture-sim-emit` (setiap kamera mengambil gambar pada pencetus yang sama, menghantar pada kadar yang diselaraskan untuk berkongsi talian, kadar bingkai menurun apabila perkongsian mengecil) pada mana-mana rangkaian yang kamera boleh diselaraskan kadarnya untuk berkongsi; `slip-emit-and-capture` (kamera bergilir-gilir menggunakan talian, tidak terdedah serentak) hanya apabila mereka tidak dapat melakukannya.
3. **PTP diaktifkan**secara lalai — cap masa merentas kamera mendarat pada satu jam bersamaan kepada**~1 ms**, [PTP grandmaster](#ptp-grandmaster--time_sync_status) di backend. Pendedahan serentak datang daripada pencetus perkakasan M8 (**&lt; 100 µs** antara modul), bukan daripada PTP: PTP menyelaras *cap masa*, bukan pendedahan.
4. **Pemilihan format piksel automatik bagi setiap kamera** — mono → `Mono12`, warna → `BayerRG12`.
5. **Penyemaian AE** — mengambil gambaran keadaan AE semasa setiap kamera supaya sambungan tidak menetapkan semula pendedahan di tengah-tengah proses.
6. **Konfigurasian pencetus GPIO** — `connect_array` mengaktifkan setiap kamera (`TriggerMode=On`, `TriggerSource=Line2`) supaya tuan rumahpulsa menggerakkan peranti hamba melalui kabel M8. Ini adalah langkah untuk susunan sahaja: satu kamera yang dibuka dengan `LatticeCamera` akan berjalan bebas sebaliknya. Melepaskan susunan (`disconnect()` / `force_disconnect()`, `chloros-cli lattice array-disconnect`) menetapkan semula setiap kamera ke mod bebas (`TriggerMode=Off`), dengan segala tetapan lain seperti yang ditemui semasa penyambungan: tetapan pencetus dan talian penyegerakan (`TriggerSource`, `TriggerActivation`, `TriggerOverlap`, `TriggerDelay`, `LineMode` / `LineSource`; talian sinchronisasi induk kembali ke input), PTP, dan tetapan kamera yang diubah oleh sesi — melainkan `keep_settings=True` / `clear_settings=True` (lihat [Memori kamera](#camera-memory--keep_settings--clear_settings)).

```python
import chloros_sdk

# First serial is the MASTER (fires the trigger pulse); rest are slaves.
with chloros_sdk.connect_array(
        ["213800234", "214000533", "214701288", "214701292"]) as arr:
    print(arr.array_id, arr.sync_mode, arr.ptp_enabled)
    arr.capture("output/", processing="reflectance")
```

Atribut: `array_id`, `master_serial`, `slave_serials`, `ptp_enabled`, `sync_mode`, `packed_transport` dan `packed_reason` (empat terakhir ialah `None` pada pemegang daripada `attach_array`), `connect_stream` dan `backend_url`. `connect_stream` ialah blok `stream` bagi respons sambungan, bentuk sambungan tunggal ditambah `members` (`{serial: {state, why?}}`): `streaming` sebaik sahaja kumpulan pertama yang diselaraskan dalam tatasusunan tiba (backend menungguinya, terhad, kira-kira 10 saat), atau `no_frames` dengan kegagalan ambil terakhir setiap ahli sebagai `why` dan, untuk paket jumbo, fallback 1500-byte dalam `note`. `None` pada pemegang `attach_array`.

#### Tandatangan `connect_array()`

```python
connect_array(
    serials,                              # list[str]; serials[0] = master
    *,
    line="Line2",                         # GPIO sync line: Line0 | Line2 | Line3
    target_fps=None,                      # pin the master trigger rate (auto if None); see set_target_fps
    force_tier=None,                      # override tier picker; see below
    wire_ceiling_mbps=None,               # host sustained wire budget, MB/s (auto if None)
    width=None,                           # explicit frame size; skips network analysis
    height=None,
    pixel_format=None,
    binning=None,
    recommend=True,                       # set False to skip the recommend step
    ptp_enable=True,                      # set False to disable PTP
    packed="auto",                        # packed 12-bit transport: "auto" | "on" | "off"
    backend_url="http://127.0.0.1:5000",  # same IPv6-avoidance default as connect_camera
    timeout=600.0,                        # s; also the backend's deadline (see below)
    auto_start_backend=True,              # spawn a local backend if none is running
) -> ArraySession
```

`timeout` ialah berapa lama panggilan menunggu sambungan, dan ia dihantar ke
backend sebagai tarikh akhir. Sambungan yang tidak dilakukan menjelang masa itu dibatalkan: setiap kamera yang dibuka dikembalikan seperti asalnya dan ditutup, tiada rangkaian disambungkan, dan panggilan membangkitkan `ChlorosConnectError` (HTTP 504 `CLIENT_DEADLINE_PASSED`, menamakan langkah yang telah dicapai oleh sambungan). Jadi, panggilan yang diangkat tidak akan meninggalkan sebarang array
disambungkan di belakangnya. Satu susunan 9 kamera disambungkan dalam kira-kira satu minit; sejurus selepas kamera dihidupkan ia boleh mengambil masa beberapa minit. Satu susunan yang sudah aktif tetapi belum mempunyai kumpulan bingkai selari akan dikembalikan, dengan `connect_stream["state"]`
`no_frames`. Log backend mempunyai satu baris masa bagi setiap langkah penyambungan (`[array-connect <id>] <step>: <s> s …`) dan satu baris terakhir yang menamakan yang paling perlahan.

Nilai `force_tier`:
- `"sim-capture-sim-emit"` — serentak sebenar (semua kamera mencetus pada tepi jam yang sama).
- `"sim-capture-ftd-stagger"` — selang masa domain masa yang fleksibel (kamera menghantar pada masa yang sedikit berbeza supaya paket disusun bersiri di atas wayar).
- `"slip-emit-and-capture"` — tangkapan bersiri bagi setiap kamera (tiada penyelarasan masa). Dipilih secara automatik hanya apabila kamera-kamera tidak dapat diperlahankan dengan cukup untuk berkongsi talian pada masa yang sama.

`wire_ceiling_mbps` mengimbangi **belanjawan talian berterusan hos** dalam MB/s — satu-satunya
nombor yang menjadi asas peruntukan keseluruhan tatasusunan. Biarkan ia pada `None` untuk menggunakan nilai yang dikesan secara automatik. Turunkan nilainya apabila susunan storan melaporkan bingkai rosak GVSP: nilai automatik diperoleh daripada kadar pautan yang diiklankan oleh NIC, yang melebih-lebihkan penyesuai USB, laluan PCIe yang sempit dan fabrik kongsi yang sibuk — dan anggaran berlebihan ini muncul sebagai bingkai rosak dan bukannya sebagai pautan yang perlahan secara nyata. Nilai ini disimpan dalam blok tangkapan array projek, jadi pembukaan semula atau `connect_array` kemudian akan memulihkannya seperti mana-mana tetapan array lain.
Lihat [Kesihatan Array](#array-health--which-subsystem-is-losing-frames).

#### Penghantaran 12-bit Terpampat (`packed=`)

Pada sensor penuh, kadar bingkai satu susunan ditentukan oleh kelajuan setiap kamera menghantar bingkainya. Format 12-bit (`BayerRG12`, `Mono12`) biasanya dihantar sebagai perkataan 16-bit; penghantaran terkepak menghantar 12 bit yang sama dalam 1.5 bait bagi setiap piksel dan membuka pekannya pada hos, jadi setiap bingkai mengambil masa 25% lebih singkat untuk dihantar — kira-kira33% lebih banyak bingkai sesaat pada sensor penuh** (diukur: 10.7 → 14.0 fps pada
susunan 9 kamera). **Data yang disimpan adalah identik**: nilai yang sama, fail 16-bit
yang sama, metadata yang sama.

- `packed="auto"` (lalai) hanya memampatkan apabila ia membantu **dan** hos mampu
  membongkar: semasa sambungan, backend mengukur kelajuan pembongkaran sendiri,
  jumlah CPU, beban semasa dan bajet rangkaian. Desktop dengan teras lebihan memampatkan pada sensor penuh; hos yang sibuk atau berkuasa rendah (komputer papan tunggal, pengawal tertanam) menstrim tanpa dibongkar. ROI kecil dan bingkai yang dikumpulkan (binned) kurang mendapat manfaat.
- `packed="off"` (atau `False`) tidak pernah memampatkan — untuk mengecualikannya semasa mendiagnosis, atau pada hos yang perlu menggunakan CPU-nya untuk kerja lain.
- `packed="on"` (atau `True`) sentiasa memampatkan format 12-bit.
- Sebarang nilai lain akan mencetuskan `ValueError`. Format 8-bit dan 16-bit tidak pernah
  dipadatkan.

Projek `analyze_array_network(..., packed="auto")` mempunyai keputusan yang sama, jadi fps-nya ialah kadar yang akan digunakan oleh susunan (array) (tindak balas membawa `packed_transport` dan `packed_reason`); `pixel_format` lalainya adalah apa yang digunakan oleh connect (Mono12 / BayerRG12) dan bukannya 8-bit.

Keputusan dan sebabnya terdapat pada sesi:

```python
arr = chloros_sdk.connect_array(serials)          # packed="auto"
print(arr.packed_transport, arr.packed_reason)
# True  auto: projected 11.0 -> 14.4 fps, unpack 0.16 of 1.50 budgeted cores

arr = chloros_sdk.connect_array(serials, packed="off")
```

`ChlorosProject.connect_all(packed=...)` menerapkan keutamaan yang sama kepada setiap
array dalam satu projek (default `None`, yang bermaksud `"auto"`: satu projek tidak menyimpan
sebarang tetapan terbungkus bagi setiap array).
Untuk tetapan lalai bagi seluruh mesin, tetapkan `CHLOROS_ARRAY_PACKED12=off` (atau `on`) dalam persekitaran backend; `packed="on"` / `"off"` yang eksplisit masih diutamakan.
`/api/camera/array/status` melaporkan `packed_transport` dan `packed_decision`.

#### Kadar Bingkai yang Dijangkakan

Kadar bingkai susunan yang diukur (disegerakkan perkakasan, 12-bit; sensor penuh = 2048 × 1536). Gunakan nilai-nilai ini untuk mengesahkan kebolehpercayaan `status()["actual_fps"]`; `analyze_array_network()` menganggar kadar untuk hos dan rangkaian anda sendiri.

| Tetapan | Kadar bingkai tatasusunan |
| --- | --- |
| Penuh sensor, PCIe 10 GbE NIC hos, penghantaran padat (`packed="auto"` pada desktop) | ~14 fps |
| Penuh sensor, PCIe 10 GbE NIC hos, tidak dibungkus (`packed="off"`) | ~10.5 fps |
| Penuh sensor, 8 kamera pada penyesuai USB 10 GbE | ~3.8 fps |
| Penuh sensor, pautan hos 1 GbE | 1.2–1.7 fps |
| Binning 2 × 2 (`binning=2`), NIC hos 10 GbE | ~30–33 fps |

- Pada pautan PCIe 10 GbE, **kamera** adalah had pada penggunaan penuh sensor, bukan rangkaian: 4 dan 8 kamera beroperasi pada kadar yang sama.
- **`target_fps` adalah kadar pencetus, dan susunan tidak pernah beroperasi lebih pantas.** `target_fps=1` menjalankan susunan pada 1 fps; `None` (lalai) menjalankannya pada kadar tertinggi yang disokong oleh hos dan rangkaian. Pin di atas kadar itu akan diturunkan ke kadar ini (lihat [`set_target_fps`](#interval-capture-for-drone-surveys)).
- **`link_speed_mbps` of `4294` dalam `status()` bukan kadar pautan.** Ini bermakna Windows tidak dapat membaca kelajuan penyesuai (pemacu melaporkan nilai 32-bit terbesar), yang biasa bagi penyesuai Ethernet USB. Kadar boleh guna penyesuai USB pada Windows dihadkan kepada 200 MB/s tidak kira apa pun (lihat [Kesihatan Array](#array-health--which-subsystem-is-losing-frames)); untuk penyetelan, lihat seksyen &quot;penyesuai USB 10GbE&quot; dalam rujukan CLI di bawah [Host NIC Setup &amp; Tuning](cli-reference.md#host-nic-setup--tuning-lattice-arrays).
- **Had USB juga terpakai pada Linux: 400 MB/s pada x86_64, 200 MB/s pada NVIDIA Jetson.** Penyesuai dikenal pasti daripada sysfs (jika tidak, rujuk `ethtool -i`), jadi penyesuai USB 5 GbE atau 10 GbE dihadkan pada had ini, bukannya pada kadar papan nama 500 / ~1060 MB/s. Angka x86_64 diukur pada Linux: penyesuai yang sama yang merosakkan bingkai melebihi 200 MB/s pada Windows menjalankan rangkaian 4 kamera dengan lancar pada setiap had sehingga 480 MB/s. Pada NVIDIA Jetson, penyesuai yang sama hanya berfungsi dengan lancar pada 200 MB/s dan kehilangan bingkai secara berselang melebihi kelajuan itu. Jika hos anda dapat menyerap lebih banyak tanpa bingkai `gvsp_corrupt`, tingkatkannya dengan `wire_ceiling_mbps`. Pada Linux, pemacu pada port USB 2.0 dibiarkan 30 MB/s; pasangkannya ke port USB 3.

#### Penglangganan Berlebihan (bawah minimum bagi setiap kamera)

Susunan berfungsi pada mana-mana rangkaian; kadar bingkai adalah hadnya. Pembahagian pacing Sim-emit memperuntukkan bahagian yang sama rata kepada setiap kamera daripada bajet jalur selamat-langgaran dan kesemuanya masih mengekspos pada pencetus yang sama, jadi pada jalur yang perlahan bahagian itu — dan kadar bingkai — adalah kecil: 8 penuh-kamera 12-bit pada 1 GbE tanpa bingkai jumbo mendapat 6.5 MB/s setiap satu, kira-kira **1 fps**(kira-kira 1.3 fps apabila dipadatkan). Bahagian itu ditetapkan pada kadar terendah yang boleh diatur untuk kamera (`per_cam_floor_bps`: baca julat GevSCPD setiap kamera semasa sambungan, dan tidak pernah di bawah 1 MB/s). Jika talian memerlukan mereka lebih perlahan daripada itu, sambungan akan kembali kepada penghantaran**siri**(`slip-emit-and-capture`): kamera bergilir pada kadar pautan penuh, jadi setiap bingkai sampai tetapi ahli-ahli**tidak terdedah serentak**— lapisan respons dan pemblokir (`SERIAL: ...`) menyatakan demikian; hantar `force_tier="sim-capture-sim-emit"` untuk menolak pengunduran. Saiz bingkai tidak diambil kira dalam perkongsian, jadi**penyusunan dan ROI meningkatkan kadar bingkai tetapi tidak mengubah sama ada satu susunan muat**. Hanya talian yang tidak dapat memindahkan susunan pada mana-mana kadar, sama ada serentak atau bergilir,**melebihi langganan** — mod kegagalan ialah kehilangan pek GVSP, bukan kadar bingkai yang lebih rendah. Penyelesaian: mengurangkan bilangan kamera, bingkai jumbo dari hujung ke hujung, atau NIC yang lebih pantas.

- Respons `analyze_array_network()` dan `/api/camera/array/connect` membawa `oversubscribed`, `aggregate_demand_bps`, `collision_safe_ceiling_bps`, `max_cams_collision_safe`, dan `per_cam_floor_bps`. Apabila `oversubscribed` benar, projeksi **menyatakan sifar medan fps** (`achievable_fps_max` / `fps_bright` / `fps_dark`) daripada melaporkan kadar perlahan tetapi berfungsi yang mengelirukan.
- `POST /api/camera/array/connect` menerima param badan `pin_resolution` (**HTTP sahaja — bukan kwarg SDK**; `connect_array` tidak mendedahkannya). Ia adalah mod ketat: sambungan yang terlebih langganan dengan `pin_resolution` ditetapkan**ditolak secara keras** dengan ralat yang menyenaraikan setiap penawarannya. Tanpa ia, sambungan diteruskan dengan amaran bahawa susunan akan kehilangan paket. Walau apa pun, sambungan mengekalkan saiz bingkai yang diminta — ia tidak pernah menyaring atau memotongnya secara automatik.
- Untuk ujian sahaja: tetapkan `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED=1` dalam persekitaran backend untuk menurunkannya kepada amaran kuat — anda terus bersambung dan menerima kehilangan paket.

#### Kesihatan Susunan — subsistem mana yang kehilangan bingkai

`ArraySession.capability()` (`GET /api/camera/array/<array_id>/capability`) membawa blok `health` langsung pada
susunan yang bersambung, dinilai semula secara berterusan **10 saat** tingkap. Ia memisahkan kehilangan bingkai kepada dua punca yang memerlukan pembetulan bertentangan, bukannya satu kadar &quot;tidak lengkap&quot; yang tidak menyatakan kedua-duanya:

| Medan | Apa maksudnya | Subsistem mana |
| --- | --- | --- |
| `gvsp_corrupt_rate_pct` (per serial) | Bingkai **telah tiba dan rosak secara struktur**— kehilangan pek GVSP. |**Rangkaian**: bajet wayar, pacing, cincin penerima NIC, MTU |
| `never_arrived_rate_pct` (per serial) | Bingkai **tidak pernah sampai langsung**— kamera tidak mencetuskan, atau tiada apa-apa yang keluar daripadanya. |**Pemicu / penyegerakan**: Kabel M8, `line=`, `TriggerMode` |
| `worst_gvsp_corrupt_pct` / `worst_never_arrived_pct` | Kadar kamera terburuk untuk setiap satu. | — |
| `per_cam_rate_pct` | Kadar tidak lengkap gabungan bagi setiap kamera (kedua-dua punca digabungkan). | — |
| `stable_for_seconds` | Berapa lama setiap kamera kekal di bawah 0.01 %. | — |

Selain `health`, rekod yang sama melaporkan bilangan yang menjadi rujukan bagi keseluruhan peruntukan:

| Medan | Apa maksudnya |
| --- | --- |
| `wire_ceiling_mbps` | Belanjawan wayar terpelihara hos yang berkuat kuasa, MB/s. |
| `wire_ceiling_source` | Daripada mana angka itu datang, dalam kata-kata — contohnya `USB-capped 200 MB/s (was theoretical 1062; …)` atau `user override 120 MB/s (auto said 200)`. |
| `wire_ceiling_is_user_set` | `true` apabila `wire_ceiling_mbps=` menetapkannya. |
| `nic_is_usb` | `true` untuk penyesuai Ethernet USB. |

```python
import chloros_sdk

serials = ["213800234", "214000533"]           # master first
arr = chloros_sdk.attach_array(serials)
h = arr.capability()

health = h.get("health", {})
print("wire ceiling:", h["wire_ceiling_mbps"], "MB/s", h["wire_ceiling_source"])
print("corrupt (network) :", health.get("worst_gvsp_corrupt_pct"), "%")
print("absent  (trigger) :", health.get("worst_never_arrived_pct"), "%")

if (health.get("worst_gvsp_corrupt_pct") or 0) > 1.0:
    # Network path. Reconnect with a lower budget -- NOT a lower target_fps.
    # force_disconnect(), not disconnect(): this handle came from attach_array,
    # and disconnect() never tears down an array the session only attached to.
    # It returns only once the backend confirms the release; a teardown that
    # timed out or was refused raises ChlorosConnectError instead, so the
    # reconnect below never races a half-released array.
    arr.force_disconnect()
    arr = chloros_sdk.connect_array(serials, wire_ceiling_mbps=120)
```

**Membacanya:** `gvsp_corrupt_rate_pct` bukan sifar dengan `never_arrived_rate_pct` pada 0 bermaksud pencetus dan penyegerakan kabel adalah sempurna dan 100% kehilangan berlaku di laluan rangkaian — lebih rendah
`wire_ceiling_mbps` dan sambung semula. Corak songsang menunjukkan pada kabel penyelarasan atau
garis pencetus sebaliknya.

> **`target_fps` bukan pemicu untuk bingkai yang rosak.** Penalaan GevSCPD ditulis sekali semasa
> sambungan, jadi menurunkan kadar pencetus mengubah kitaran tugas dan bukannya
> kadar letupan pelepasan serentak. Pemotongan permintaan 5× yang diukur tidak memberikan sebarang penambahbaikan, manakala
> menurunkan had kabel daripada 240 kepada 200 MB/s menjadikan rig yang sama berkurang daripada 10.4 % bingkai rosak kepada
> 0.00 %.

> **Susunan yang sedang berjalan tidak dapat merancang semula dirinya pada firmware LATTICE (M3C / M3M).** Ia tidak boleh membetulkannya sendiri; putuskan sambungan dan sambung semula supaya pemilih masa sambungan boleh merancang semula berdasarkan had baru.**Penyambung USB Ethernet dihadkan pada 200 MB/s** oleh probe tanpa mengira
nameplate: jadual kecekapan yang menukarkan kadar pautan kepada angka berterusan adalah berpunca daripada PCIe, dan kad NIC USB mengiklankan kadar pautan Ethernetnya sambil dibatasi oleh bas USB dan pemandunya. Had ini adalah mutlak, bukan pecahan — penyesuai USB 1 GbE menghasilkan ~80 MB/s dan tidak terjejas. Ia terpakai pada Windows dan Linux (termasuk Jetson); lihat [Kadar Bingkai Dijangka](#expected-frame-rates) untuk tempat ia diukur.

#### Kaedah `ArraySession`

| Kaedah | Deskripsi |
| --- | --- |
| `status(timeout=10.0)` | Keadaan langsung tatasusunan yang sedang berjalan: `target_fps` / `target_fps_auto` (kadar pencetus dan sama ada pengawal kadar memilikinya), `target_fps_clamped_reason` (kenapa kadar yang dipinjamkan dikekalkan lebih rendah, jika tidak `None`), `actual_fps` (diukur; 0 sebaik sahaja gelung capai melepaskan atau terhenti), `recommended_fps`, `streaming` (False sebaik sahaja gelung grab berputus — semak ini, bukan hanya `last_error`; kekal Benar melalui but semula tetapan, yang ditandakan oleh `restarting`), `stalled` (Benar semasa gelung capai berjalan tetapi belum menerbitkan kumpulan bingkai selama kira-kira sepuluh tempoh bingkai, 2–30 s; `last_error` hanya menyatakan demikian selepas 60 saat), `last_frame_age_s`, `last_error`, `serials`, `ptp_enabled` (`True` hanya apabila tatasusunan disambungkan dengan PTP **dan setiap ahli terakhir membaca `PtpEnable` pada; bukan sama seperti terkunci, yang diukur oleh `ptp_epoch`), `ptp_configured` (apa yang diminta oleh sambungan), `ptp_off_members` / `ptp_unread_members` (ahli yang terakhir membaca `PtpEnable` mati / tidak menjawab), `member_recovery` (`None`, atau ahli yang kehilangan kuasa atau pautannya: `state` `waiting` selagi ia tidak menjawab, `reopening`, kemudian `recovered`, dengan `members` dan `why` — susunan itu membukanya semula apabila ia menjawab sekali lagi dan memberikannya semula PTP masa-sambungan, pencetus dan pendawaian garisan selari, penalaan dan tetapan kamera, kemudian mengalir semula tanpa `connect_array`) baharu, dan `ptp_epoch` (diukur), `sync_mode` / `sync_tier`, `packed_transport` / `packed_decision`, `per_cam_emit_mbps`, `link_speed_mbps` (`4294` bermaksud Windows tidak dapat membaca kelajuan penyesuai — lihat [Kadar Rangka Yang Dijangkakan](#expected-frame-rates)), `trigger_pipeline` / `pipeline_tune`, `radiometric_chain` (sama ada rantaian radiasi setiap jalur boleh digunakan, dan ahli mana yang memecahkannya), dan `band_clamp` — manakala paparan langsung memaparkan susunan: bahagian piksel yang bercahaya yang diikat oleh unmix kepada 0, bagi setiap ahli (`members`: `{serial: [band0, band1, band2]}` dalam susunan jalur R, G, B kamera) dan bagi setiap jalur komposit menunjukkan (`composite`: `{band name: share}`), dengan `warn`, bahagian yang ditandakan oleh GUI sebagai &quot;Data jalur keyakinan rendah&quot; (diukur kira-kira sekali setiap saat; `None` apabila tiada paparan langsung dijalankan), dan `member_ae` — bagi setiap ahli, pendedahan (`exposure_us`) dan penguatan (`gain_db`) bingkai terakhir yang diterbitkan, daripada bingkai itu sendiri (`read_nodes` of `ExposureTime` / `Gain` memulangkan nilai terakhir yang ditulis semasa pendedahan automatik berjalan, bukan apa yang sedang dilakukannya). |
| `set_member_settings(serial, **kwargs)` | Menulis tetapan satu kamera ahli, seperti yang diambil oleh `CameraSession.set_settings()` (pendedahan, penguatan, kecerahan sasaran, ...); ahli kekal dalam tatasusunan. Menghasilkan `ChlorosConnectError` untuk siri yang bukan ahli. Lihat [Pendedahan pada Ahli Tatasusunan](#exposure-on-array-members). |
| `capability(timeout=10.0)` | Apa yang telah ditetapkan oleh smart-prep (tingkat tangkapan, saiz bingkai, `pixel_format`), blok `health` langsung dan bajet wayar hos. Lihat [Kesihatan Array](#array-health--which-subsystem-is-losing-frames). |
| `set_target_fps(fps, timeout=10.0)` | Ubah kadar pencetus semasa array berjalan, tanpa menyambung semula. Nombor pin itu (pengawal kadar membiarkannya); `None` memulangkannya ke auto. `{requested, target_fps, target_fps_auto, clamped}` memulangkan — `clamped` adalah True apabila kadar diturunkan kepada apa yang hos dan rangkaian boleh tanggung. Lihat [Interval Capture for Drone Surveys](#interval-capture-for-drone-surveys). |
| `grab(processing="debayered", with_metadata=False, apply_calibration=True, apply_white_balance=True, apply_index=False, index_expression=None, bit_depth=None, band=None, align=False, refl_max=None)` | Satu bingkai daripada setiap kamera, **semuanya daripada satu pencetus**, sebagai susunan numpy (tiada fail ditulis). Mengembalikan `GrabResult` (atau `dict`): `{serial: array}`, atau `{serial: (array, metadata)}` dengan `with_metadata=True`. `.group_id` adalah pencetus yang dikongsi oleh semua bingkai; `.errors` memetakan setiap kamera yang terlepas kepada sebabnya (kamera yang tidak dapat dilayan pada pencetus tersebut diabaikan, tidak pernah digantikan oleh yang lain). `processing` ialah `raw` / `debayered` / `radiance` / `reflectance` / `display`. Dibaca sama seperti [`ArrayHandle.grab()`](#one-trigger-per-read--group_id) dalam API projek — satu pelaksanaan bersama — jadi setiap kata kunci di sana (`band`, `align`, `bit_depth`, `refl_max`) berfungsi di sini. Perlu `numpy`. |
| `frame_stream(*, processing="debayered", fps=5.0, duration=None, count=None, apply_calibration=True, apply_white_balance=True, apply_index=False, index_expression=None, with_metadata=False, bit_depth=None, band=None, align=False, refl_max=None)` | Iterator yang menghasilkan satu hasil `grab()` bagi setiap tick (hanya hujah kata kunci), dipacu oleh `fps`, dibatasi oleh `duration` (saat) dan/atau `count` (`None` = tidak terhad). **Set yang dihasilkan adalah pencetus baru:** satu tick yang trigger terbarunya adalah set yang telah dihasilkan tunggu trigger seterusnya bukannya mengulanginya, jadi aliran berjalan pada kadar yang lebih perlahan antara `fps` dan kadar bingkai rangkaian itu sendiri. Menghasilkan `FrameFetchError` bukannya menjadi senyap: sekali apabila setiap kamera menolak atas sebab yang tick seterusnya akan diulang, selepas 60 saat tick tanpa bingkai, dan selepas 60 saat tanpa pencetus baru (&quot;tiada pencetus baru selama 60 saat&quot;). Pelaksanaan sama seperti `ArrayHandle.frame_stream()`. Lihat [Live Frames in Your Own Pipeline](#live-frames-in-your-own-pipeline). |
| `continuous(output_dir="output", *, format="tiff", processing="debayered", levels=None, aligned=None, render_index=None, force_daq=None, orient=None, max_captures=None, duration_s=None, daq=None, daq_tolerance_ms=None, timeout=30.0)` | Mulakan tangkapan berterusan backend sendiri (Mod Berterusan GUI) → [`ContinuousHandle`](#continuoushandle). Menyimpan **setiap** kumpulan diselaraskan yang diterbitkan oleh susunan, seperti `capture()` akan menyimpannya (kawalan eksport yang sama), sehingga dihentikan atau sehingga `max_captures` / `duration_s` (`None` = sehingga dihentikan). Menghasilkan `ChlorosConnectError` jika satu sudah berjalan untuk susunan ini. |
| `capture(output_dir="output", format="tiff", processing="debayered", levels=None, aligned=None, render_index=None, force_daq=None, orient=None, smart=False, after_seq=None, daq=None, daq_tolerance_ms=None, timeout=300.0)` | Satu kumpulan tangkapan diselaraskan. Mengembalikan `CaptureResult` (senarai frame dicts + `.skipped` + `.failed` + `.group_seq` + `.daq_files` + `.daq_matches` + `.alignment` + `.oriented`). Serahkan hasil sebelumnyas `group_seq` sebagai `after_seq` untuk mendapatkan kumpulan diselaraskan seterusnya selepas ia, bukannya menunggu yang baru. Kawal selia eksport di bawah. |
| `capture(..., smart=True)` | **Smart capture** — menunggu AE stabil merentasi semua kamera, kemudian mencetuskan. |
| `capture_fastest(output_dir="output", force_daq=True, render_index=True, orient=None, timeout=120.0)` | Rakaman terpantas: hanya mentah + bacaan DAQ yang ditetapkan (+ indeks gabungan percuma). Meniru butang &quot;Fastest Capture&quot; GUI. |
| `capture_repeated(output_dir="output", count=None, duration_s=None, interval_s=0.0, on_capture=None, **capture_kwargs)` | Tunggal / Berterusan / Selang dalam satu gelung terhad. Mengembalikan `list[CaptureResult]`.**Perlukan `count` dan/atau `duration_s`** supaya ia menamatkan (SDK tidak mempunyai Ctrl+C). Juga menamatkan awal, selepas pass, apabila satu pass melaporkan pemacu penuh (satu entri `.failed` dengan `disk_full`); pass itu adalah hasil terakhir. |
| `record(output_dir="output", fps=10.0, duration_s=None, video=True, gif=False, expr=None, timeout=30.0)` | Mulakan merakam pandangan indeks gabungan langsung ke video/GIF → `RecorderHandle`. Video itu adalah Motion-JPEG `.avi` (tiada ffmpeg diperlukan). Apabila tiada paparan GUI dibuka, backend memaparkannya: `expr` (ungkapan indeks dalam nama jalur susunan, contohnya `"(NIR_850_RGN-Red_660_RGN)/(NIR_850_RGN+Red_660_RGN)"`), atau NDVI susunan secara lalai. Satu perekod komposit bagi setiap susunan. |
| `burst(output_dir="output", duration_s=None, max_frames=None, index_config=None, serial_index_config=None, orient=None, timeout=30.0)` | Mulakan burst raw-Bayer fps-tinggi → `RecorderHandle`. Proses semula secara luar talian dengan `build_video()`. `orient=True`: produk-produk yang dihasilkan oleh `build_video()` dipusingkan mengikut orientasi kamera (produk bagi setiap kamera oleh orientasi kamera masing-masing, manakala indeks gabungan oleh orientasi kamera induk); bingkai mentah kekal dalam bentuk asal dan rekod manifest burst mencatatkan orientasi setiap ahli. |
| `build_video(burst_dir, products=None, fps=10.0, video=True, gif=False, save_tiffs=False, wait=True, poll_s=2.0, timeout=1800.0)` | Memproses semula secara luar talian burst mentah yang disimpan menjadi video(s) yang dikalibrasi. Blok sehingga selesai (`wait=True`) dan memulangkan hasil binaan: `{success, outputs, errors, skipped, substitutions, notes, degraded, combined}` — lihat [hasil binaan](#the-build_video-result) di bawah. |
| `build_video_status(job_id, timeout=15.0)` | Poll kerja binaan luar talian: `{running, result, error, burst_dir}`. |
| `disconnect(keep_settings=False, clear_settings=False)` | Lepas keseluruhan susunan — **jika sesi ini membukanya** (`connect_array`): ia kemudian menjadi `force_disconnect()`, dan membangkitkan `ChlorosConnectError` apabila pelepasan tidak disahkan (keluar blok `with` tidak pernah diaktifkan). Pada handle daripada `attach_array` ia membiarkan array itu berjalan dan mencetak satu baris ke stderr yang menyatakan demikian: array itu mungkin milik GUI atau skrip lain. Setiap kamera dibiarkan berjalan bebas dan sebaliknya seperti yang ditemui oleh connect; `keep_settings=True` menyimpan tetapan ke dalam memori kamera itu sendiri, `clear_settings=True` menetapkan semula memori itu kepada tetapan kilang (lihat [Memori kamera](#camera-memory--keep_settings--clear_settings)). |
| `force_disconnect(timeout=60.0, keep_settings=False, clear_settings=False)` | Melepaskan keseluruhan array tanpa mengira siapa yang membukanya. Gunakan pada handle yang dilampirkan apabila anda bermaksud menamatkan array (contohnya untuk menyambung semula dengan tetapan berbeza). Mengembalikan nilai apabila backend mengesahkan pelepasan, atau apabila tiada apa-apa untuk dilepaskan (susunan itu sudah tiada, atau tiada backend yang sedang berjalan); teardown yang tamat masa atau yang ditolak oleh backend akan mencetuskan `ChlorosConnectError`. Dengan mana-mana bendera, ia mengembalikan apa yang telah disimpan dalam memori setiap kamera (`camera_memory`), jika tidak `None`. |

Kawalan eksport `capture()` (titik akhir yang sama digunakan oleh GUI/CLI):

- `processing` / `levels` — `processing="all"` (atau `levels=["raw","radiance",…]`) menyimpan setiap jenis eksport yang terpakai bagi setiap kamera; satu nilai `processing` hanya menyimpan tahap tersebut sahaja.
- `aligned` — **aktif secara lalai (`None`), kerana tangkapan aplikasi Chloros ke atas susunan yang sama adalah**(tetapan Tangkap**Diselar** bagi setiap susunan). Eksport bukan mentah setiap ahli diwarapkan ke dalam bingkai kamera induk oleh [profil penyelarasan] susunan itu(#array-alignment) dan dipotong mengikut tumpang tindih bersama ahli-ahli, jadi fail-fail itu didaftar piksel dan keluar dengan saiz, potongan dan tag `Alignment*` yang ditetapkan oleh aplikasi (di bawah ROI transformasi itu disasarkan semula pada tetingkap); `XMP-mapir:AlignmentJSON` mengatakan `"applied": true` dengan `crop_roi`. Raw kekal tidak diubah bentuk pada saiz asal tetapi membawa transformasi dalam metadata. `aligned=False` menyimpan setiap fail pada saiz asal, tidak diubah bentuk; `aligned=True` adalah lalai yang dijelaskan. `continuous()`, `capture(smart=True)`, `capture_repeated()` dan `ArrayHandle.capture()`/`capture_smart()` pada peringkat projek menggunakan tetapan lalai yang sama. Sama ada warp diterapkan atau tidak, setiap fail ahli merekod transformasi `Alignment*` kamera mereka sebaik sahaja tatasusunan mempunyai profil penjajaran — kecuali kamera yang profil tidak menjajarkan (dilepaskan, atau dipaksa menjadi identiti oleh penyelesaian yang gagal), dan kamera rujukan apabila tiada kamera lain diselaraskan: kesemuanya tidak mempunyai tag `Alignment*`. Tanpa sebarang profil pada susunan, tangkapan disimpan tidak selari, dan hasilnya menunjukkan demikian: `result.alignment` adalah `{"requested": True, "applied": False, "warnings": [...]}`, satu amaran bagi setiap sebab pemutarbelitan tidak dapat diterapkan (tiada profil, ahli pada resolusi berbeza, susunan yang tidak boleh-selaras, satu bingkai yang profil tidak dapat dipetakan padanya). `applied` adalah `True` hanya apabila setiap eksport yang meminta warp mendapatkannya. Penjajaran automatik susunan menghasilkan profil sebaik sahaja pendedahannya stabil; `ArrayHandle.calibrate_alignment()` / `align-calibrate` pada CLI menghasilkan profil atas permintaan. `result.oriented` menunjukkan sama ada `orient=True` telah memproses produk.
- `render_index=False` — langkau lapisan indeks vegetasi bagi setiap kamera; lalai memaparkannya di lokasi yang dikonfigurasikan.
- `daq` / `daq_tolerance_ms` — bacaan DAQ disimpan sebagai fail sampingan `.daq` secara lalai, walaupun tiada aras terpilih yang memerlukannya; `daq=False` akan melangkauinya pada masa itu, dan `daq_tolerance_ms` menetapkan toleransi padanan (lalai 500). `force_daq=True` adalah ejaan lama bagi nilai lalai. Lihat [The DAQ reading a capture saves](#the-daq-reading-a-capture-saves--daq_files-and-daq_matches).
- `orient=True` — ubah suai setiap ahli **produk**(`display`, `radiance`, `reflectance`, indeks per-kamera dan komposit) mengikut orientasi kamera, selepas pemutaralan penjajaran: mengikut orientasi**master**apabila ia diwarp ke dalam kerangkanya (default `aligned` — ahli-ahli kekal terdaftar bersama), jika tidak, mengikut kamera mereka sendiri.**Lalai: setiap fail ahli kekal asli**, kerana tangkapan susunan sentiasa mempunyai; `raw` dan `debayered` kekal asli dalam apa jua keadaan. Setiap fail merekod orientasi bingkai tempat pikselnya berada (`XMP-Camera:ChlorosRotation` / `ChlorosFlip*` / `ChlorosOrientationApplied` — satu raw, kamera mereka sendiri), dan XMP `Alignment*` kekal transformasi bingkai asli, diterapkan sebelum orientasi; sebuah raw yang membawa transformasi itu juga merekod `ChlorosAligned*` (milik master), orientasi produk yang dibengkokkan ke dalam bingkai master. Pemprosesan memusingkan setiap produk mengikut yang tergolong pada pikselnya: yang selari apabila ia membengkokkan raw (Penjajaran Array dihidupkan), milik kamera sendiri apabila ia tidak. `capture_repeated()` dan `capture_fastest()` meneruskannya; `ArrayHandle.capture()` / `capture_smart()` di peringkat projek turut mengambilnya.

Pemampatan TIFF (tolok hanya HTTP):** `ArraySession.capture()` tidak menghantar sebarang kunci `compression`, jadi tetapan lalai backend terpakai — `POST /api/camera/array/capture` membaca parameter badan `compression`, `"none"` secara lalai: tanpa pemampatan, piksel sensor tepat seperti dibaca (~6.3 MB setiap bingkai penuh resolusi). `"deflate"` (zlib L1 tanpa kehilangan + peramal mendatar, ~4.1 MB/satu bingkai) menulis fail yang lebih kecil dengan lebih perlahan — kedua-duanya tanpa kehilangan dan dibaca secara identik semasa import. SDK tidak menyediakan sebarang kwarg untuknya; jalan keluar ialah `chloros-cli lattice array-capture --compression deflate` atau raw HTTP. DEFLATE memegang GIL Python, jadi penulisan mampat tidak dapat dijalankan secara selari merentasi thread penulis bagi setiap kamera — rakaman resolusi penuh berterusan pada kadar sensor memerlukan tetapan lalai tanpa pemampatan. Butiran: [Rujukan CLI → array-capture](cli-reference.md).**Kumpulan berturut-turut (HTTP):** titik akhir memulangkan `group_seq`; gelung yang menghantarnya semula sebagai `after_seq` pada permintaan seterusnya akan mendapat kumpulan diselaraskan seterusnya — termasuk satu yang diterbitkan semasa tangkapan sebelumnya sedang disimpan — bukannya menunggu kumpulan yang diterbitkan selepas permintaan, dan `skipped_groups` melaporkan mana-mana yang terlepas oleh gelung. `capture_repeated`, `--continuous` CLI dan mod Berterusan GUI semuanya melakukan ini; selang masa kekal segar pada setiap masa yang dijadualkan.**Keutamaan eksport bagi setiap ahli (HTTP sahaja):**titik akhir yang sama juga menerima `exclude_serials` (senarai — buang ahli daripada set yang disimpan; tatasusunan masih mencetuskan sebagai satu kumpulan diselaraskan dan ahli yang dikecualikan dikembalikan dalam `excluded`), `serial_levels` (`{serial: [level tokens]}` keutamaan peringkat kamera), dan `serial_index` (`{serial: bool}` keutamaan lapisan indeks per-kamera). Ini adalah parameter badan GUI-parity dan**bukan kwargs SDK buat masa ini**; ahli yang tiada dalam peta akan kembali kepada `levels` / `render_index` merentasi keseluruhan tatasusunan.

##### Menyemak Kamera yang Dilangkau — `CaptureResult.skipped`

`ArraySession.capture()` mengembalikan `CaptureResult`, yang merupakan subkelas `list`: ulangi ia, indekskan ia, `len()` ia — setiap corak sedia ada terus berfungsi. Kod baru boleh memeriksa atribut `.skipped` untuk melihat kamera mana yang dikecualikan dan mengapa. Kes paling biasa ialah kamera RGB dalam susunan penapis campuran apabila anda meminta `processing="radiance"` atau `"reflectance"` — sinaran per-Bayer tidak bermakna untuk sensor jalur lebar, jadi backend melangkau kamera-kamera tersebut daripada menghasilkan data yang tidak bermakna.

```python
with chloros_sdk.connect_array(serials) as arr:
    result = arr.capture("output/", processing="reflectance")

    # Back-compat: iterate as a plain list
    for frame in result:
        print(frame["filepath"], frame["serial"])

    # New: see why N-1 cams were saved
    for skip in result.skipped:
        print(f"skipped SN:{skip['serial']} reason={skip['reason']}")
        # e.g. {'serial': '214701292', 'level': 'reflectance',
        #       'reason': 'reflectance-not-applicable-to-rgb-cam',
        #       'filter': 'RGB'}
```

Token sebab mengikuti corak `<level>-not-applicable-to-rgb-cam` (satu entri bagi setiap tahap yang dilangkau, masing-masing membawa `level`). Kamera yang pek kalibrasinya tidak dimuat akan melangkau `radiance` / `reflectance` dengan `<level>-needs-calibration (connect the camera so its cal pack loads)`, dan dengan tiada bacaan DAQ yang boleh digunakan, pantulan melangkau dengan `reflectance-skipped-no-dls`. Pelompatan khusus pantulan yang lain ialah `reflectance-skipped-no-fresh-dls` (tiada bacaan sinaran ke bawah baharu tersedia), `reflectance-skipped-bound-daq-unavailable (…)` (DAQ terikat tidak dapat dicapai), dan `dls-uncalibrated-band-<nm>` — jalur ini kebanyakannya terletak di luar julat kalibrasi radiometrik penderia cahaya DAQ (~374–974 nm), jadi pembahagian reflektansi berasaskan DAQ mutlak ditolak dan bingkai itu dengan kuat diturunkan ke respons sensor. Antara SKU yang dihantar, hanya F988 mencetuskan ia; aliran kerja yang disokong oleh kamera itu ialah panel reflektansi.

##### Fail yang Tidak Dapat Ditulis — `CaptureResult.failed`

Sebuah tangkapan yang hanya dapat menulis sebahagian failnya masih akan mengembalikan: senarai memaparkan setiap fail yang **telah** ditulis, dan `.failed` menyenaraikan baki fail, satu dict bagi setiap fail dengan `serial` (`None` untuk komposit gabungan dan fail sampingan `.daq` bagi tatasusunan), `level` (`raw`, `radiance`, …, `index`, `composite`, `daq`, atau `match` — provenance `.match.json` bagi `.daq`, apabila `.daq` ditulis tetapi fail sampingan tidak) dan `error`. Pada pemacu penuh, entri itu juga membawa `disk_full: True`; setiap tangkapan kemudian pada pemacu itu akan gagal dengan cara yang sama, itulah sebabnya `capture_repeated()` berhenti di situ. Tiada pengecualian diangkat untuk tangkapan separa, dan fail dalam `.failed` tidak ditinggalkan separuh tertulis pada cakera. `.failed` kosong pada tangkapan yang bersih.

```python
result = arr.capture("output/", processing="all")
for f in result.failed:
    print(f"SN:{f['serial']} {f['level']} not written: {f['error']}")
if any(f.get("disk_full") for f in result.failed):
    raise SystemExit("drive full -- free space and capture again")
```

##### Bacaan DAQ yang disimpan oleh tangkapan — `.daq_files` dan `.daq_matches`

Setiap kali DAQ diikat pada kamera atau susunan, atau dikumpulkan, setiap tangkapan menyimpan bacaan downwelling yang digunakannya: sebuah `.daq` dalam subfolder `daq/` folder tangkapan, dengan `.match.json` dan `.cameras.json` (yang mana bacaan itu). Perkara ini juga terpakai untuk tangkapan mentah atau tangkapan de-bayered, dan ia adalah fail yang sama sama ada tangkapan itu berasal daripada GUI, CLI atau SDK. `daq=False` melangkauinya untuk tangkapan yang tahapnya tidak menggunakan DAQ (tahap `radiance`, `reflectance` atau `display` masih menulisnya); `daq_tolerance_ms` memindahkan garisan dalam-toleransi untuk itu perekodan dan bukannya pembacaan yang digunakan. DAQ mana: yang diikat pada kamera atau tatasusunan (pilihan penderia cahaya GUI, pengikatan projek, atau `chloros-cli daq pool-bind`), jika tidak, yang paling baru daripada backend. CLI menerangkan kawalan `--no-daq` dan `--daq-tolerance-ms` ([CLI Rujukan](cli-reference.md#the-daq-reading-a-capture-saves)).

```python
r = cam.capture("out", levels=["raw"])      # a CameraSession; ArraySession results match
for m in r.daq_matches:                      # one per .daq in r.daq_files
    print(m["daq_file"], m["gap_ms"], m["in_tolerance"])
```

Sebuah tangkapan menyimpan bacaan penderia cahaya yang digunakannya sebagai `.daq` dengan `<file>.daq.match.json` di sisinya, yang menyatakan bagaimana bacaan itu dikaitkan dengan pendedahan: `method`, `gap_ms` (daripada pendedahan ke bacaan DAQ terdekat) dan `in_tolerance` (dalam masa 500 ms, atau `daq_tolerance_ms` pada tangkapan itu) Fail XMP reflektansi membawa keputusan yang sama seperti `Chloros:DlsMatchMethod`, `Chloros:DlsMatchGapS` dan `Chloros:DlsInTolerance`.

Sebuah tangkapan menunggu bacaan pertama DAQ selepas pendedahan, supaya ia dapat menggabungkan bacaan di kedua-dua belahnya (`method` `interpolated`). DAQ-E menghantar bacaan setiap 0.4 s pada waktu siang dan setiap 1-2 s di dalam bangunan, jadi `capture()` akan kembali lewat sehingga kira-kira satu setengah daripada selang tersebut; pendedahan itu sendiri tidak ditangguhkan. DAQ yang telah berhenti menghantar tidak ditunggu. Bacaan DAQ-E adalah purata cahaya sepanjang selang masa sejak bacaan sebelumnya, jadi ia diletakkan di tengah-tengah selang masa itu: `gap_ms` diukur sehingga ke tengah itu, dan `.match.json`&#x27; `bracket_stamps_ns` memberikan cap masa bagi bacaan itu sendiri. `ArraySession.continuous()` menunggu tanpa melambatkan pengambilan. `capture_repeated()`&#x27;s back-pindahan balik tidak menunggu, jadi ia mengekalkan kadarnya, dan dipadankan dengan bacaan terdekat sebelum pendedahan.

Bacaan tidak pernah tamat tempoh, jadi tiada bingkai hilang kerana bacaan lewat — sebaliknya ia dilabelkan:

- **Aliran DAQ terhenti** — bacaan terbarunya digunakan, dan `gap_ms` menyatakan betapa tuanya ia (`in_tolerance: false` melebihi 500 ms).
- **Tiada DAQ untuk dibaca**— terputus sambungan (`disconnect()`, `chloros-cli daq pool-disconnect`), atau DAQ yang diikat tidak dapat dihubungi — bacaan terakhir kamera digunakan semula dan diterangkan pada**ini** pendedahan: `method` `retained`, `gap_ms` daripada pendedahan ini, `in_tolerance` dikira semula, dan `retained_from_epoch_ns` menamakan pendedahan yang pertama kali dipadankan dengannya. Kamera yang tidak pernah mengambil bacaan masih tidak mendapat pantulan, dan hasilnya menerangkan sebabnya (`reflectance-skipped-no-fresh-dls` / `reflectance-skipped-bound-daq-unavailable (…)`, atau penolakan tangkapan).

Fail yang disimpan oleh tangkapan di dalam folder projek yang dibuka di Chloros akan disertakan dalam senarai fail projek itu, seperti tangkapan dalam aplikasi (fail yang disimpan di tempat lain dibiarkan begitu sahaja): `CameraSession.capture()`, `ArraySession.capture()` (`smart=True` juga) dan `capture_cameras_transient()`. Keputusannya dinyatakan dalam `.project_add` (`capture_cameras_transient()`: `project_add` pada kamus) -- `held`, `files` dan `message` pada backend; `held` ialah `True` semasa pelaksanaan pemprosesan sedang dijalankan, dan fail-fail itu digabungkan apabila ia selesai. `None` apabila tiada fail mendarat dalam projek.

`CameraSession.capture()` dan `ArraySession.capture()` memulangkan mereka sebagai `.daq_files` dan `.daq_matches` bagi keputusan, sebagai tindak balas tangkapan HTTP (`/api/camera/<serial>/cli-capture`, `/api/camera/capture`, `/api/camera/capture-all-synced`, `/api/camera/array/capture`) membawa `daq_files` dan `daq_matches`, satu bagi setiap `.daq`: `daq_file`, `cameras`, `method`, `gap_ms`, `in_tolerance`, `tolerance_ms`, dan `stream_fault` — ralat aliran DAQ sendiri, hanya dicari apabila bacaan melebihi toleransi. Sebelum bergantung pada pantulan daripada tangkapan, periksa `in_tolerance` dalam `.match.json` atau XMP, atau DAQ [`status()`](#daqsensorsession-methods).

Peringkat `processing`:

| Peringkat | Output |
| --- | --- |
| `"raw"` | Bayer saluran tunggal (kamera mono: jalur tunggal) terus dari sensor. |
| `"debayered"` *(lalai SDK)* | 3-saluran warna: offset gelap pek kalibrasi (DSNU) diambil daripada data mentah, kemudian demosaik sedar tepi OpenCV — tiada LUT lineariti, padang rata atau unmix (kamera mono: skala kelabu 1-saluran). TIFF yang disimpan adalah dalam susunan saluran **RGB**(RGB fotometrik, jadi mana-mana pemapar imej akan memaparkan warna sebenar); susunan dalam memori `grab()` / `frame_stream()` yang dikembalikan adalah dalam susunan**BGR** OpenCV susunan — membalikkan saluran (`frame[..., ::-1]`) untuk membandingkan satu dengan fail yang disimpan. `chloros-cli process` menulis produk yang sama seperti `Debayered_Images`. |
| `"radiance"` | float32 W/m²/sr/nm melalui seluruh rantaian radiometrik. Multispektral sahaja — kamera RGB diabaikan. |
| `"reflectance"` | uint16 0..32768 (sedia untuk Pix4D); memerlukan padanan DAQ langsung untuk rujukan mutlak. Multispektral sahaja. |
| `"display"` | Rantaian penuh yang sepadan dengan pratonton GUI (CCM + WB + gamma mengikut profil kamera). |
| `"all"` | **Satu fail bagi setiap tahap yang terpakai** untuk setiap kamera (sepadan dengan GUI &quot;Capture All&quot; / lalai CLI). `CaptureResult` yang dikembalikan kemudiannya mengandungi satu frame dict bagi setiap `(cam, level)`, dengan tahap dalam setiap dict; tingkat yang tidak terpakai muncul dalam `.skipped`. Bacaan DAQ yang digunakan untuk tangkapan disimpan sebagai fail sampingan `.daq`. |

> **Nota — lalai berbeza daripada CLI.** `ArraySession.capture()` lalai kepada `processing="debayered"`; arahan `chloros-cli lattice array-capture` lalai kepada `processing="all"`. Serahkan `processing="all"` secara eksplisit daripada SDK untuk mencerminkan simpanan berbilang aras CLI/GUI.

##### Nama fail tangkapan tatasusunan

Setiap tahap disimpan dalam subfolder tersendiri `output_dir` (`raw/`, `debayered/`, `radiance/`, `reflectance/`, `preview/` untuk `display`, `index/`, `composite/`). Setiap fail menamakan kameranya sebagai `SN<serial>-<filter>`: siri, kemudian bahagian penapis model kamera — `SN214000533-FOCN` untuk `M3C-L87-FOCN`, `SN261902142-F450` untuk `M3M-L87-F450` — digabungkan dengan `-`, jadi nama mempunyai medan yang dipisahkan oleh `_` yang sama seperti sebelum ini. Kamera yang modelnya tidak dapat dibaca ialah `SN<serial>` sahaja, dan tangkapan daripada keluaran terdahulu tidak mempunyai penapis dalam nama. Nama fail bergantung pada berapa banyak peringkat yang diminta oleh ahli:

| Ahli meminta | Nama fail | Contoh (`reflectance`, lalai `format="tiff"`) |
| --- | --- | --- |
| **Satu tahap** (`processing="reflectance"`, atau `levels` satu item) | `sync_<timestamp>_SN<serial>-<filter>.<format>` — tiada awalan aras, seperti dalam keluaran terdahulu, supaya skrip sedia ada terus berfungsi | `reflectance/sync_<ts>_SN214000533-FOCN.tiff` |
| **Beberapa tahap** (`processing="all"`, atau `levels` dengan lebih daripada satu item) | `sync_<timestamp>_SN<serial>-<filter>_<level>.<format>` | `reflectance/sync_<ts>_SN214000533-FOCN_reflectance.tiff` |

[clamp mask](#unmix-clamp-mask--which-pixels-a-band-was-clamped-on) mengikuti nama produk, `clamp_masks/<product folder>/<product name>_clampmask.tif`: satuperekodan pantulan tahap - menulis `clamp_masks/reflectance/sync_<ts>_SN<serial>-<filter>_clampmask.tif`, manakala `processing="all"` menulis `clamp_masks/reflectance/sync_<ts>_SN<serial>-<filter>_reflectance_clampmask.tif`. Peraturan suku kata ditentukan bagi setiap ahli daripada aras yang diminta, jadi jika ada aras yang dilangkau (contohnya pantulan pada kamera RGB), ia tidak mengubahnya. Baca laluan daripada bingkai yang dikembalikan daripada membinanya. Rakaman satu kamera (`CameraSession.capture()`) sentiasa menambah tahap: `capture_<ts>_SN<serial>-<filter>_<level>.tiff`.

PNG dan JPEG hanya menyokong produk paparan dan indeks sahaja

PNG atau JPEG menyimpan 8 bit bagi setiap saluran. Permintaan untuk tahap yang tidak disokong akan ditolak sebelum sebarang tangkapan dibuat, dengan mesej yang menyatakan tahap tersebut dan TIFF sebagai format yang menyokongnya:

- **`radiance`**(float32) dan**`reflectance`**(uint16, `32768` = 1.0) — dan seterusnya `processing="all"`, yang merangkumi mereka. SDK memeriksa ini pada argumen: `capture()` menimbulkan**`ValueError`** tanpa permintaan (`CameraSession`, `ArraySession`, `CameraHandle`, `ArrayHandle`, `capture_smart`).
- **`raw` / `debayered` daripada streaming kamera melebihi 8 bit** (12-bit M3C, Mono12 M3M) — backend menolak ini, kerana mengetahui kedalaman bit setiap kamera: HTTP 400 dengan `error_code` `FORMAT_CANNOT_HOLD_LEVEL`, diangkat sebagai `ChlorosConnectError` (`CameraSession` / `ArraySession`) atau `RuntimeError` (`CameraHandle` / `ArrayHandle`). Rakaman satu aras `debayered` oleh satu kamera mengambil bingkai 8-bit untuk PNG/JPEG dan tidak ditolak.

Simpan data radiometrik sebagai `format="tiff"` (`ext=".tiff"`); gunakan PNG/JPEG untuk `display` dan render indeks.

#### Cara Kerja Pendedahan Automatik

- **Pendedahan dahulu, penguatan kemudian.** Dengan penguatan pada Auto, penguatan kekal pada 0 dB sehingga pendedahan mencapai nilai terpanjang yang dibenarkan.
- **Pendedahan manual mengekalkan gain pada Auto** sehingga anda menukar gain atau menetapkannya kepada manual. Apabila kembali pada Auto, had gain adalah seperti sebelum ini.
- **Sorotan kecil tidak menggelapkan adegan.** Refleksi kilat sehingga 1 % daripada bingkai diabaikan, dan pada panel sasaran penentukuran yang diketahui, sehingga 0.5 % daripada panel.
- **Had pendedahan DAQ hanya terpakai semasa sasaran penentukuran kelihatan.** Ia mengelakkan panel putih daripada terpotong; jika tiada panel dilihat, kamera akan mengambil gambar mengikut pendedahan untuk pemandangan.
- **Cahaya yang konsisten kekal stabil, dan cahaya yang berubah akan menstabil dalam beberapa saat.** Pada cahaya yang konsisten, pendedahan dan hadnya kekal tidak berubah, dan selepas perubahan cahaya yang besar, pemandangan akan menstabil dalam beberapa saat. Clip tidak lagi mengehadkan pendedahan secara kekal, dan perubahan ROI mencari semula pendedahan tersebut.
- **Sasaran kecerahan kekal dalam julat.** Ia tidak meningkat selagi kamera berada pada had pendedahannya dan penguatannya tidak dapat meningkat, jadi cahaya yang kembali tidak akan mencerahkan bingkai secara berlebihan.
- **Kamera yang pengecaman automatiknya berhenti bertindak balas akan dikekalkan pada tahap untuk cahaya persekitarannya.** Di bawah cahaya matahari yang terang, kamera boleh jatuh ke pengecaman terpendeknya dan kekal di situ dengan bingkai gelap, manakala semua tetangannya menunjukkan normal. Chloros memerhati bingkai, mengekalkan kamera itu pada tahap yang disimpannya untuk cahaya tersebut, dan melepaskannya apabila cahaya berubah. Penderia cahaya gelap atau yang ditutup tidak mengganggu perkara ini.

#### Pendedahan pada Ahli Susunan

Kamera LATTICE menetapkan `ExposureTime` pada satu tempoh kadar bingkainya. Semasa satu array sedang mengalir, Chloros menjalankan setiap ahli dengan `AcquisitionFrameRateEnable` dihidupkan, pada kadar sedikit di atas kadar pencetus, jadi had pendedahan ahli terletak sedikit di bawah tempoh pencetus, **pada mana-mana ROI**:

- Tempoh bingkai sesuatu ahli ialah tempoh pencetus tolak kelonggaran sebanyak 15% daripada tempoh tersebut (sekurang-kurangnya 3 ms, paling banyak separuh daripada tempoh), dan had pendedahanannya ialah tempoh bingkai tersebut tolak kira-kira 0.1 ms. Contoh: pencetus 10 fps (100 ms) memberikan kadar ahli kira-kira 11.8 fps dan had pendedahan kira-kira 85 ms.
- **manual yang lebih lama daripada had tersebut akan memperlahankan pencetus untuk memuatkannya.** Chloros pertama sekali membuka had ahli tersebut untuk nilai itu dan membiarkan kadar yang lebih perlahan berjalan beberapa tempoh pencetus (penulisan boleh mengambil masa beberapa saat lagi), kemudian menulisnya, dan pencetus tidak akan mencetuskan lebih pantas daripada yang dibenarkan oleh pendedahan. Pada susunan kadar automatik, kadar yang dijalaninya sebelum ini akan kembali sebaik sahaja setiap ahli yang kadar manualnya melambatkan pendedahan itu beralih semula ke pendedahan automatik. Nilai yang tidak dapat diambil oleh kamera itu sendiri masih ditolak, dengan julat yang diterima kini dalam ralat, dan pendedahan automatik ahli itu dipulihkan jika ia dimatikan hanya untuk penulisan itu. Sebelum 1.3.0, ROI kecil mengehadkan setiap ahli pada tempoh bebas sensor itu sendiri, apa pun kadar pencetus: 4.9 ms pada 768 × 304, jadi tiada ahli boleh diserlahkan.
- **Chloros** tidak pernah memendekkan pendedahan manual untuk menyesuaikan pencetus yang lebih pantas. Kadar auto kekal dalam had yang dibenarkan oleh pendedahan manual; kadar pin yang lebih pantas daripada yang dibenarkan oleh pendedahan akan menetapkan had masa yang cukup lama untuk ahli tersebut, dan ia hanya menerima pencetus pada kadar yang tidak melebihi had pendedahannya. Kurangkan pendedahan atau pin apabila setiap ahli perlu menghasilkan setiap pencetus.
- **Bacaan balik:** membaca nod ahli menunjukkan `AcquisitionFrameRateEnable` Benar dan `AcquisitionFrameRate` pada kadar ahli; pengaktifan akan dimatikan semula apabila susunan berhenti.
- **Susunan memiliki kedua-dua nod tersebut. Jangan tulis `AcquisitionFrameRateEnable` atau `AcquisitionFrameRate` pada ahli susunan** — tetapkan susunan kadar dengan `target_fps` / `set_target_fps()` sebaliknya.

Tulis pendedahan ahli dengan `arr.set_member_settings(serial, exposure_time=20000)` (kata kunci `CameraSession.set_settings()`; `chloros-cli lattice exposure --serial` melakukan perkara yang sama). Ahli kekal dalam tatasusunan. Jangan buka ahli tatasusunan dengan `connect_camera()`: connect menolak kamera yang sudah berada dalam tatasusunan, dan `CameraSession` yang dikembalikannya akan mengeluarkan kamera itu daripada tatasusunannya apabila ia terputus. `ArraySession.status()["member_ae"]` melaporkan setiap ahlipendedahan dan penguatan daripada bingkai terbarunya.

### Satu rekod projek, setiap mod

Satu folder projek menyimpan tetapan setiap peranti: `cameras.json` — setiap kamera (`cameras[].settings`, untuk satu kamera dan untuk setiap ahli susunan) dan setiap susunan secara keseluruhan (`arrays[].capture`: format, ROI, binning, kadar pencetus, bajet wayar) — dan `sensors.json`, setiap penderia cahaya (masa integrasi, purata bingkai, cap). Aplikasi, CLI, SDK dan pelayan MCP semuanya membaca dan menulis fail yang sama, dengan cara yang sama:

- **Peranti yang disambungkan di bawah projek disimpan ke dalam projek tersebut dan merekod setiap perubahan ke dalamnya.** &quot;Di bawah projek&quot; bermaksud dengan projek dibuka di backend (aplikasi), atau — melalui `ChlorosProject` (`open_project()`), yang menamakan folder sendiri ke backend (`project_path`). Kamera, susunan atau penderia cahaya yang belum disimpan oleh projek akan ditambah ke dalam failnya apabila ia disambungkan (`connect_camera()`, `connect_array()`, sambungan penderia cahaya, CLI, aplikasi, `chloros-mcp`), dengan tetapan masing-masing. Peralihan daripada mana-mana mod (`set_settings()`, `ArraySession.set_member_settings()`, `set_target_fps()`, tetapan susunan, `set_cap()`, CLI, kawalan aplikasi) disimpan ke dalam fail projek itu beberapa ketika kemudian. Pendedahan, penguatan atau sasaran AE yang ditetapkan pada kamera disimpan sebagai dipegang, jadi membuka semula memegangnya sekali lagi.
- **Membuka projek menetapkan peranti daripadanya**, dalam setiap mod: kamera, susunan atau penderia cahaya yang disambungkan tanpa tetapan tersendiri akan mendapat projekyang disimpan (`lattice cam-connect` / `array-connect` apabila projek dibuka, `chloros-cli project connect`, `open_project().connect_all()`, buka semula aplikasi). Susunan yang disambungkan di bawah id baru mengemas kini susunan yang disimpan oleh projek dengan kamera yang sama; susunan dengan kamera lain ditambah di sebelahnya.
- **Simpanan aplikasi sendiri tidak pernah menimpa tetapan langsung peranti** dengan salinan lama miliknya, dan mengekalkan keutamaan bagi setiap ahli yang disimpan oleh pemanggil CLI atau SDK.
- **Format piksel yang anda tetapkan adalah format yang disimpan oleh projek.** Format yang ditetapkan dalam Tetapan Susunan, oleh `--pixel-format` atau oleh `connect_array(pixel_format=)`, termasuk 8-bit, disimpan dan disambung semula dengan tepat. Jika tiada ditetapkan, setiap kamera akan bersambung pada lalai 12-bitnya (`BayerRG12`, `Mono12` pada sensor mono). Nama format menunjukkan kedalaman bitnya; keluarga adalah milik sensor, jadi kamera berwarna tidak pernah ditetapkan ke format Mono atau kamera mono ke format Bayer, dan baris `[PixelFormat <serial>] ... written` menyatakan demikian apabila format telah dipetakan. Eksport tangkapan 8-bit adalah 8-bit, termasuk pantulan: diskala semula dan ditandakan dengan `PixelScale`.
- Peranti yang disambungkan tanpa projek merekod tidak ada apa-apa.
- **Sebuah rakaman mengikuti projek yang dibuat selepasnya; sebuah rakaman terbuka membiarkannya dengan projeknya.**Apabila sebuah projek dibuat — `create_project()`, `chloros-mcp`&#x27;s `create_project`, atau**Projek Baru**aplikasi — atau disalin (fungsi**Duplikat**aplikasi, yang membuka salinan itu) sementara rakaman masih menulis ke dalam projek yang dibuka sebelum ini, rakaman itu akan terus berjalan, dan apa yang ditulisnya akhirnya akan berada dalam projek baru, di tempat yang sama di dalamnya (di bawah). Membuka projek sedia ada — `ChlorosProject.process()` (yang membuka projeknya di backend), `chloros-cli process`,**Buka Projek** aplikasi — membiarkan rakaman masih berjalan dengan projek yang dirakamnya: ia selesai di situ, dan fail-failnya menyertai senarai fail projek itu (aplikasi menghentikan setiap rakaman sebelum ia membuka projek).
  - `ArraySession.record()` dan `ArraySession.burst()`: video itu, atau folder burst dengan setiap bingkai, dipindahkan ke sana sepenuhnya apabila rakaman dihentikan. Keputusan pemberhentian melaporkan laluan baru (`out_dir`, `video_path`), dan binaan burst berfungsi daripadanya. Pergerakan yang ditolak oleh pemacu meninggalkan rakaman di tempat ia ditulis, dan hasilnya melaporkan demikian (`relocation_error`). Bingkai mentah burst yang dihentikan dan `.daq` disertakan dalam senarai fail projek tempat ia berakhir; video tidak ditambah ke dalam senarai fail projek.
  - `ArraySession.continuous()`: setiap rakaman selepas perubahan disimpan ke dalam projek baru; yang disimpan sebelum itu kekal di tempatnya.
  - `DAQSensorSession.record_start()` (dan rakaman sensor cahaya aplikasi itu sendiri): `.daq` bergerak ke folder `light_sensor` projek baru apabila rakaman berhenti; `record_stop()` mengembalikan laluan baru, dan fail itu disertakan dalam senarai fail projek tersebut.

  Perekodan ke dalam folder di luar projek yang dibuka kekal di situ, dan `start_camera_continuous()` / tugas berbilang aliran mengekalkan folder yang telah diberikan kepada mereka.
- **Dalam aplikasi, tangkapan selepas projek baru dimasukkan ke dalamnya.** Mencipta atau menggandakan projek mengekalkan setiap sesi tangkapan dan rakaman yang sedang berjalan, dan setiap tangkapan yang diambil selepas itu (Tangkap Semua, Mulakan Tangkapan) disimpan ke dalam projek baru dan disertakan dalam senarai failnya. Membuka projek sedia ada dalam aplikasi akan menghentikan kesemuanya terlebih dahulu.
- **Projek tidak dipindahkan semasa ia merakam.**Memindahkan projek terbuka ke direktori kerja lain (**Tetapan → Direktori Kerja**aplikasi; tiada panggilan SDK yang memindahkan projek, permintaan untuk memindahkan projek terbuka ke direktori kerja lain (**Tetapan → Direktori Kerja** aplikasi; tiada panggilan SDK memindahkan projek,   Ini berasingan daripada apa yang berlaku pada kamera apabila disambung semula (lihat [Memori kamera](#camera-memory--keep_settings--clear_sett)):  permintaan oleh klien HTTP yang menghantar ke `/api/move-project` ditolak dengan HTTP 409 `PROJECT_RECORDING` sementara rakaman tatasusunan atau burst, tangkapan berterusan tatasusunan, kerja kamera berterusan / pelbagai-aliran atau rakaman penderia cahaya menulis ke dalamnya: `Project '<name>' is recording (<what>), so moving the project now is refused. Stop the recording, then try again.`

Ini berasingan daripada tetapan kamera yang dikekalkan semasa penyambungan diputuskan (lihat [Memori kamera](#camera-memory--keep_settings--clear_settings)): sesebuah projek menetapkan kameranya sendiri apabila ia dibuka.

### Memori kamera — `keep_settings` / `clear_settings`

Apabila satu susunan disambungkan, kameranya akan mengambil tetapan susunan tersebut, dan anda kemudian boleh mengubahnya. Apabila Chloros melepaskan kamera — `ArraySession.disconnect()` / `force_disconnect()`, penghujung fungsi sementara, pemberhentian `start_camera_continuous()` — ia memulihkan setiap satu seperti asal sebelum disambungkan: pendawaian pencetus dan talian penyegerakannya, tetapan PTP dan kamera — `ExposureAuto` / `GainAuto` (dan satu manual `ExposureTime` / `Gain`), `TargetBrightness` dan had serta peredaman AE, imbangan putih, paras hitam, `PixelFormat`, binning dan ROI, saluran warna (`GammaEnable` / `Gamma`, `LUTEnable`, `ColorTransformationEnable`), `ReverseX` / `ReverseY`, `UserSetSelector`, PTP&#x27;s `PtpSlaveOnly` / `PtpDomainNumber`, saiz paket (`GevSCPSPacketSize` / `DeviceStreamChannelPacketSize`), pelarasan kelajuan (`DeviceLinkThroughputLimitMode` / `DeviceLinkThroughputLimit`, `GevSCPD`, `GevSCFTD`), ketulan data (`ChunkModeActive`, setiap `ChunkEnable` di bawah `ChunkSelector`), penapisan deglitch input pencetus memberikan talian selari hamba (`LineFilterSelector` / `LineFilterWidth`) dan kadar bingkai pemerolehan (`AcquisitionFrameRate` hanya apabila enable-nya dihidupkan: apabila ia dimatikan, kadar terbaca adalah seberapa banyak yang dibenarkan oleh pacing, bukan satu tetapan). Hanya apa yang berbeza ditulis, kamera berhenti dahulu, pacing dan kadar bingkai sebelum pendedahan yang memerlukan tempoh bingkai mereka.

Untuk menggunakan kamera di luar projek, dua hujah menulis **memori kamera sendiri** — apa yang dihidupkan dengannya, dalam mana-mana perisian (pada `disconnect()` / `force_disconnect()`, setiap fungsi sementara, `start_camera_continuous()` dan `ChlorosProject.disconnect_all()`; MCP: hujah yang sama seperti `disconnect_device` dan alat sementara; CLI: `--keep-settings` / `--clear-settings`):

- **`keep_settings=True`** menyimpan tetapan setiap kamera seperti ketika sesi ditinggalkan ke dalam memorinya (UserSet2, dijadikan set but). Kamera-kamera itu masih dibiarkan berjalan bebas (`TriggerMode` `Off`), pendawaian talian penyegerakan seperti yang ditemui. Kamera yang tiada UserSet2 menyimpan ke dalam UserSet1; komputer ini mengingatnya, jadi pemulihan sendiri susunan Chloros — yang menetapkan semula UserSet1 yang telah menyimpang daripada asas Chloros — membiarkannya begitu sahaja.
- **`clear_settings=True`** menetapkan semula memori setiap kamera kepada tetapan kilang: asas Chloros, iaitu yang dipanggang oleh pemulihan sendiri larikannya — tetapan lalai kilang dengan `TriggerMode` `Off`, `TriggerSource` `Line2`, pendedahan automatik, imbangan putih yang dikalibrasi kamera RGB disimpan — dalam UserSet1, dijadikan set but.
- Bukan kedua-duanya (`ValueError`; backend menjawab 400). Balasan membawa `camera_memory`, bagi setiap kamera `{"saved_to": "UserSet2" | "UserSet1" | None, "errors": [...]}` atau `{"cleared": bool, "errors": [...]}` — `force_disconnect()` memulangkannya; untuk sesuatu tugas ia disertakan dengan `stop()`.

Aplikasi ini tidak menawarkan perkara-perkara ini.

**Setiap jalan keluar memulangkan kamera.**Pemulangan seperti asal dijalankan pada setiap laluan keluar: `CameraSession.disconnect()` (kamera aplikasi**X**),**X** pada satu tatasusunan atau mana-mana ahli tatasusunan (keseluruhan tatasusunan dilepaskan), menutup aplikasi (backend mendapat sehingga 20 saat untuk memulihkan setiap kamera, tatasusunan dan rakaman berterusan yang sedang berjalan; fungsi sementara atau panggilan sekali jalan yang masih berjalan akan berhenti di titik selamat seterusnya, memulihkan kameranya terlebih dahulu, selama sehingga 8 s, dan menjawab HTTP 503 `SHUTTING_DOWN`; `connect_array()` yang masih berjalan berhenti sebelum kamera seterusnya dan mengembalikan kamera yang telah dibuka, selama sehingga 10 s, dan memulakan `ChlorosConnectError` (HTTP 503 `SHUTTING_DOWN`); dan satu yang tiba semasa penutupan tidak membuka apa-apa: HTTP 503 `SHUTTING_DOWN`), dan arahan `chloros-cli` yang dibatalkan atau terminalnya ditutup (kerjanya dihentikan dan kameranya dikembalikan terlebih dahulu). Backend yang rosak atau dibatalkan secara paksa tidak dapat mengembalikan apa-apa; kitaran kuasa mengembalikan setiap kamera ke memorinya.**Projek menetapkan kameranya sendiri.** Apabila satu susunan disambungkan melalui projek — projek terbuka aplikasi, atau `ChlorosProject`, yang menamakan dirinya kepada backend (`project_path`) — setiap ahli mendapat tetapan yang disimpan oleh projek untuknya: pendedahan, penguatan atau sasaran AE yang disimpan padanya (disimpan sebagai milik anda, jadi pendedahan automatik tidak akan mengubahnya) dan mod imbangan putihnya (pembetulan Chloros; penyaman putih kamera sendiri tetap dimatikan), apa sahaja yang disimpan dalam memori kamera.

### Mod Rakaman &amp; Perakam

Permukaan tatasusunan mencerminkan panel rakaman GUI: mod rana Tunggal / Berterusan / Selang / Terpantas, serta dua perakam (video komposit langsung dan letupan mentah → pemprosesan semula luar talian).

```python
import time, chloros_sdk

with chloros_sdk.connect_array(serials) as arr:
    # Single (default) — one synced group
    arr.capture("out/", processing="reflectance")

    # Fastest — raw + .daq + combined index now, calibrate later
    arr.capture_fastest("flightline/")

    # Interval — one reflectance pass every 2 s, 5 passes (bounded so it ends)
    arr.capture_repeated("timelapse/", count=5, interval_s=2.0,
                         processing="reflectance",
                         on_capture=lambda i, r: print(f"pass {i}: {len(r)} frames"))

    # Continuous — every synced group for 60 s, saved by the backend itself
    with arr.continuous("run1/", processing="raw", duration_s=60) as run:
        run.wait()
    print(run.result["captures"], run.result["captures_per_s"],
          run.result["stop_reason"])

    # Combined-index video/GIF recorder: the GUI's combined view, or with no
    # GUI open the array's NDVI (pass expr="..." for another index)
    with arr.record("monitoring/", fps=10, gif=True) as rec:
        time.sleep(30)
    if rec.result and rec.result.get("error"):   # leaving the block does not raise
        print("recording failed:", rec.result["error"])
    else:
        print(rec.result["video_path"])

    # Raw-Bayer burst → offline reprocess into calibrated video(s)
    with arr.burst("capture/", duration_s=5) as b:
        pass
    out = arr.build_video(b.result["out_dir"], products=[
        {"kind": "per_cam", "level": "reflectance"},
        {"kind": "combined", "level": "index"}])
    print(out["outputs"])
```

- **`capture_repeated`**ialah gelung Berterusan/Selang (Continuous/Interval) SDK. Oleh kerana tiada `Ctrl+C` untuk menghentikannya daripada skrip, anda**mesti** memanggil `count` dan/atau `duration_s` (ia berhenti apabila salah satu daripadanya dicapai). `interval_s` diukur dari permulaan setiap pusingan (menyesuaikan GUI). Pass berterusan (`interval_s=0`) menggerakkan rantaian `group_seq` → `after_seq`, jadi ia adalah kumpulan bersinkronisasi berturut-turut dalam susunan kadar. kwargs baki dihantar terus ke `capture()`.
- **`continuous`**adalah tangkapan berterusan backend sendiri — Mod Berterusan GUI.**Gunakan ia daripada `capture_repeated()` apabila anda mahu setiap pencetus disimpan pada cakera pada kadar bingkai yang tinggi:** `capture_repeated()` membuat satu permintaan HTTP bagi setiap kumpulan daripada skrip anda, manakala `continuous()` mengekalkan dua tangkapan dalam perjalanan di dalam backend tanpa perjalanan balik antara kumpulan. Diukur pada ROI 512 pada rangkaian 9 kamera: 73–77 tangkapan/s, berbanding rangkaian yang menyiarkan kira-kira 78. Gunakan `capture_repeated()` untuk tangkapan selang (`interval_s`) dan untuk pas yang anda periksa satu per satu. Pada Windows, kecualikan backend daripada Microsoft Defender untuk kadar tinggi (lihat [Langkah 1](#step-1--install-the-chloros-platform-package)).
- **`record`** adalah *darjah pemantauan*: Ia merekod komposit indeks gabungan yang dipaparkan secara langsung. Dengan pandangan gabungan GUI dibuka, ia merakam pandangan tersebut seperti yang dipaparkan. Jika tiada yang dibuka, backend memaparkan pandangan itu sendiri: `expr` jika anda menghantar satu, jika tidak, NDVI matriks (gelombang merah dan NIR terdekat dengan 660 nm dan 850 nm, seperti yang dipilih oleh paparan langsung), setelah susunan diselaraskan. Namakan jalur dalam `expr` seperti yang dinamakan oleh susunan: `<band>_<filter>` untuk kamera M3C (`NIR_850_RGN`, `Red_660_RGN`, `Orange_615_OCN`), `Mono_<filter>` untuk M3M (`Mono_850`); nama warna pendek (`NIR`, `Red`) berfungsi hanya apabila satu jalur dalam susunan memilikinya. Tinjauan GUI yang dibuka semasa rakaman akan dirakam selagi ia kekal terbuka. Rakaman yang tidak menulis sebarang bingkai akan gagal dengan `error` `"no-frames: …"` (susunan mungkin masih menyelaras atau menetapkan pendedahannya), dan `stop()` diangkat (lihat [`RecorderHandle`](#recorderhandle)). Satu perekod komposit bagi setiap tatasusunan (diangkat jika satu sudah berjalan).
- **`burst` → `build_video`** adalah *darjah analisis*: `burst` menulis bingkai mentah + manifest bagi setiap bingkai + satu `.daq` bagi setiap bacaan DLS berbeza di bawah `<output>/bursts/<base>/` pada kadar penuh gelung grab (tiada rantaian, tiada exiftool, tiada tontonan langsung). `build_video` memadankan masa setiap bingkai dengan `.daq` terdekat dan menjalankan semula paip import radiance/rantaian pantulan/indeks. `products` ialah senarai `{"kind": "per_cam"|"combined", "level": "radiance"|"reflectance"|"index"}` (lalai: indeks gabungan). `burst().stop()` juga secara automatik memulakan gabungan terbaik-pembinaan indeks, dikembalikan sebagai `build_job` dalam hasil henti.

##### Hasil `build_video`

| Kunci | Maksud |
| --- | --- |
| `success` | `True` hanya apabila setiap produk yang diminta dibina seperti yang diminta: sekurang-kurangnya satu output, tiada apa-apa dalam `skipped`, dan bukan `degraded`. |
| `outputs` | Laluan video (dan GIF) yang ditulis. |
| `errors` | Masalah, sebagai teks. |
| `skipped` | Satu `{"serial", "level", "reason"}` bagi setiap produk yang diminta yang menghasilkan **tiada** video — kamera (tiada bundel kalibrasi untuk kamera itu, tiada bacaan DLS untuk pantulan, rantaian pemprosesan gagal) atau indeks gabungan, yang membawa `"kind": "combined"` dan `serial` `None` (tiada pasangan jalur merah/NIR di antara kamera, tiada kumpulan dengan radiasi untuk kedua-dua jalur). Mana-mana entri menghasilkan `success` `False`; video lain masih ditulis dan disenaraikan. |
| `substitutions` | Kamera dipaparkan pada tahap berbeza daripada yang diminta, dan sebabnya — contohnya, kamera RGB yang meminta sinaran/pantulan akan mendapat video paparan, dengan imbangan putih apabila bundel penentukuran kamera tersedia. Bukan kegagalan. |
| `notes` | Teks maklumat, bukan masalah — contohnya kamera yang tiada ekspresi indeks bagi setiap kamera tidak mempunyai video indeks, atau video paparan kamera RGB tidak diimbangi putih (tiada bundel penentukuran, atau bundel tanpa imbangan putih kilang), menamakan kamera tersebut. Bukan kegagalan. |
| `degraded` | `True` apabila produk telah ditulis tetapi bukan yang diminta: indeks gabungan luar talian adalah perbezaan normalisasi merah/NIR terbina dalam, jadi ungkapan indeks yang dikonfigurasikan tidak dinilai. Menyebabkan `success` `False`. |
| `combined` | Ringkasan indeks gabungan: gelombang yang digunakan, julat LUT, bingkai yang dibina. |
| `clamp_masks` | Laluan [topeng clamp unmix](#unmix-clamp-mask--which-pixels-a-band-was-clamped-on) bagi per-frame TIFF yang dikalibrasi (`save_tiffs=True`), dalam `clamp_masks/<level>/` di sebelah folder `<level>/` bagi TIFF, satu bagi setiap TIFF di mana piksel dikekang. Satu video tidak mengandungi sebarang. |
| `clamp_fractions` | Apa yang dikatakan oleh video sebagai ganti topeng: satu `{"kind", "serial", "level", "frames"}` bagi setiap produk video (`kind` `"combined"` untuk kamera anggota indeks gabungan), `frames` satu senarai `[frame, R, G, B]` — bahagian piksel bingkai itu yang tidak dicampur dan dikekang dalam jalur yang dipegang oleh saluran R / G / B-nya (`Chloros:Band{R,G,B}UnmixClampFraction`). |

#### `RecorderHandle`

Dipulangkan oleh `ArraySession.record()` dan `ArraySession.burst()`. Gunakan ia sebagai pengurus konteks untuk berhenti secara automatik apabila keluar daripada skop, atau kendalikannya secara manual.

| Ahli | Deskripsi |
| --- | --- |
| `job_id` | ID kerja backend (str). |
| `kind` | `"composite"` (daripada `record`) atau `"raw"` (daripada `burst`). |
| `start_stats` | Dikt yang dikembalikan oleh panggilan `start`. |
| `result` | `None` semasa berjalan; dict hasil henti akhir apabila dihentikan. |
| `stats(timeout=10.0)` | Statistik kerja langsung (kerangka yang ditulis, fps sebenar, masa berlalu). |
| `stop(timeout=60.0)` | Hentikan perakam; memulangkan dan menyimpan hasil akhir (`.result` kekal `None` jika permintaan henti itu sendiri gagal). Untuk rakaman `record()` yang gagal — satu yang tidak menulis sebarang bingkai yang disertakan (`error` `"no-frames: …"`) — ia menyimpan hasil pada `.result`, kemudian membangkitkan `ChlorosConnectError`. Idempotent (panggilan kedua mengembalikan hasil yang disimpan, tanpa membangkitkan). Meninggalkan blok `with` menghentikan perekod tanpa membangkitkan, jadi periksa `rec.result.get("error")` selepas itu. |

```python
rec = arr.burst("capture/")
# ... drive manually ...
print(rec.stats()["frames"])
result = rec.stop()
print(result["out_dir"], result.get("build_job"))
```

#### `ContinuousHandle`

Dipulangkan oleh `ArraySession.continuous()`. Guna ia sebagai pengurus konteks — blok `with` menghentikan tangkapan jika ia masih berjalan — atau kendalikannya dengan `status()` / `wait()` / `stop()`. Satu tangkapan berterusan dijalankan bagi setiap susunan pada satu-satu masa.

| Ahli | Deskripsi |
| --- | --- |
| `start_status` | Status dict yang dikembalikan oleh panggilan permulaan. |
| `result` | `None` semasa berjalan; status dict akhir setelah tangkapan selesai (ditetapkan oleh `stop()`, atau oleh `status()` / `wait()` yang melihatnya berakhir). |
| `status(timeout=10.0)` | Status dict langsung (medan di bawah). |
| `wait(timeout=None, poll_s=0.5)` | Blok sehingga tangkapan berakhir dengan sendirinya — `max_captures` atau `duration_s` dicapai, tatasusunan hilang, pemacu penuh, ralat — dan kembalikan status akhir. Dengan `timeout`, kembalikan status terkini selepas beberapa saat tersebut walaupun ia masih berjalan: periksa `running`. |
| `stop(timeout=30.0)` | Hentikan tangkapan dan kembalikan status akhirnya. Idempoten (panggilan kedua akan mengembalikan hasil yang disimpan). |

Medan status:

| Medan | Maksud |
| --- | --- |
| `running` | Benar selagi rakaman berjalan. |
| `captures` | Kumpulan diselaraskan disimpan. |
| `files` | Fail ditulis. |
| `failed_files` | Fail yang tidak dapat ditulis (seperti dalam [`CaptureResult.failed`](#files-that-could-not-be-written--captureresultfailed)). |
| `failed_captures` | Percubaan tangkapan yang gagal sepenuhnya, termasuk yang dicuba semula oleh tangkapan (HTTP 503 semasa susunan tiada kumpulan baharu tepat pada masanya). |
| `skipped_groups` | Mengelompokkan susunan yang diterbitkan yang tidak disimpan (perekaman ketinggalan). |
| `captures_per_s` | Kadar simpanan purata sejak permulaan. |
| `elapsed_s` / `started_at` | Saat operasi setakat ini; masa mula (saat epoch Unix). |
| `last_group_seq` | Kumpulan diselaraskan terkini yang disimpan. |
| `last_error` | Ralat terkini, atau `None`. |
| `stop_reason` | Setelah selesai: `count` (`max_captures` dicapai), `duration` (`duration_s` dicapai), `stopped` (`stop()` dipanggil), `array_gone` (susunan terputus sambungan), `error` (20 kegagalan berturut-turut, atau permintaan yang ditolak oleh backend), `disk_full` (fail yang tiada ruang pada pemacu). `None` semasa berjalan. |
| `max_captures` / `duration_s` | Batasan yang digunakan semasa permulaan (`0` = tiada). |
| `defender_cores` | Teras CPU yang digunakan oleh Microsoft Defender dalam tetingkap 10 saat terakhir; `None` sehingga satu diukur, dan sentiasa `None` pada hos tanpa Defender. |
| `defender_warned` | Benar apabila rakaman telah mencatat bahawa Defender memperlahankannya (pada 1 teras atau lebih). Kecualikan backend daripada imbasan Defender — lihat [Langkah 1](#step-1--install-the-chloros-platform-package). |

```python
run = arr.continuous("captures/run1", processing="raw", max_captures=5000)
try:
    while True:
        st = run.wait(timeout=5)              # returns every 5 s while running
        print(f"{st['captures']} captures, {st['captures_per_s']:.1f}/s, "
              f"{st['skipped_groups']} skipped")
        if not st["running"]:
            break
finally:
    final = run.stop()                        # no-op once it has ended
if final["failed_files"] or final["stop_reason"] in ("disk_full", "error"):
    raise SystemExit(f"continuous capture: {final['stop_reason']}: "
                     f"{final['last_error']}")
```

### Rangka Langsung dalam Paip Anda Sendiri

Untuk membekalkan bingkai yang diselaraskan ke dalam kod anda sendiri — pembelajaran mesin, pemapar tersuai, perekod anda sendiri — gunakan `ArraySession.frame_stream()`. Setiap tick menghasilkan `GrabResult`: `{serial: numpy array}` untuk setiap kamera, **semuanya daripada satu pencetus**, dengan id pencetus tersebut pada `.group_id`. Ia adalah pelaksanaan yang sama seperti `ArrayHandle.frame_stream()` dalam API projek, jadi segala-galanya di bawah [Satu pencetus bagi setiap bacaan](#one-trigger-per-read--group_id), [`band`](#exporting-a-single-band--band), [`align`](#penjajaran-aliran--penjajaran) dan [`bit_depth`](#kedalaman-bit--menukar-ketepatan-untuk-kelebaran- jalur) terpakai kepada kedua-duanya.

```python
import chloros_sdk
from chloros_sdk import FrameFetchError

serials = ["213800234", "214000533", "214701288", "214701292"]   # master first

with chloros_sdk.connect_array(serials) as arr:
    try:
        for frames in arr.frame_stream(processing="raw", fps=10, duration=300,
                                       with_metadata=True):
            if not frames:              # no camera answered this tick
                continue
            if frames.errors:           # cameras missing from this trigger
                print("left out:", {s: str(e) for s, e in frames.errors.items()})
            for serial, (img, hdr) in frames.items():
                # img: numpy array (raw: one Bayer or mono plane)
                # hdr: group_id, frame_id, timestamp_ns, exposure_us, gain_db, ...
                my_pipeline(frames.group_id, serial, img, hdr["timestamp_ns"])
    except FrameFetchError as exc:      # the stream cannot continue
        print("stream ended:", exc.status, exc)
```

- **Setiap set yang dihasilkan adalah pencetus baru.** `frames.group_id` tidak pernah berulang dalam aliran; apabila anda meminta `fps` lebih kerap daripada kadar larian tatasusunan, aliran itu menunggu pencetus seterusnya, jadi ia berjalan pada kadar yang lebih perlahan daripada kedua-duanya.
- **Kamera yang hilang dinamakan, bukan diisi semula.** Kamera yang tidak dapat dilayan pada pencetus itu akan tiada dalam dict dan disenaraikan dalam `frames.errors`.
- **Ia berakhir dengan bunyi kuat.** `FrameFetchError` pada penolakan akan berulang pada tick seterusnya, selepas 60 saat tanpa bingkai, atau selepas 60 saat tanpa pencetus baru.**Apa yang disampaikannya.**`frame_stream()` memindahkan keseluruhan bingkai numpy melalui sambungan HTTP tempatan dan mencapai kelajuan hampir**170 MB/s**. Ini sudah memadai untuk pemprosesan secara langsung bingkai mentah, atau susunan binned atau ROI, tetapi tidak untuk setiap pencetus pada resolusi penuh — untuk mengekalkan**setiap** pencetus, simpan ke cakera dengan [`continuous()`](#continuoushandle). Diukur pada susunan empat kamera (FOCN, FRGN, FRGB, F450) dengan penghantaran berpadat; setiap tick adalah satu set satu pencetus lengkap:

| | Sensor penuh (susunan 14.2–14.4 fps) | Penggabungan 2 × 2 (susunan ~33 fps) |
| --- | --- | --- |
| `frame_stream(processing="raw")` | 6.7 set/s | 22.2 set/s |
| `frame_stream(processing="debayered")` | 2.3 set/s | 9.3 set/s |
| `frame_stream(processing="display")` | 0.8 set/s | 3.0 set/s |
| MJPEG per-kamera, 4 aliran pada 720p | 13.5 fps setiap satu; setiap bingkai dipadankan oleh pencetus | ~25 fps setiap satu; ~45 % pencetus hadir pada keempat-empat |

Gunakan `processing="raw"` untuk throughput dan demosaic di pihak anda; tahap `debayered` juga membuang offset gelap pek penentukuran, yang mana de-mosaik `raw` tidak melakukannya.

#### Aliran MJPEG per-kamera (tiada RTSP)

**RTSP tidak disokong.** Antara muka penstriman adalah MJPEG melalui HTTP (`multipart/x-mixed-replace`, satu JPEG bagi setiap bahagian, setiap bahagian dengan `Content-Length`), satu URL bagi setiap kamera:

| URL | Apa yang dihidangkan |
| --- | --- |
| `GET /api/camera/stream/<serial>` | pratonton 8-bit, sehingga ~30 fps. Untuk ahli susunan, `max_h` (lalai 720, 64–2160) dan `jpeg_q` (lalai 80) menetapkan saiz dan kualiti. |
| `GET /api/camera/<serial>/stream-annotated` | Pratonton yang sama dengan lapisan GUI dilukis di atasnya (zebra, grid, garisan silang, histogram, peaking, meter titik). `ChlorosLocal.camera_stream()` membacanya untuk anda. |
| `GET /api/camera/<serial>/stream-reflectance` | Reflektan 8-bit bagi setiap jalur — lihat [Aliran reflektan langsung](#live-reflectance-stream-mjpeg). |

- **Header pencetus.** Bagi ahli tatasusunan, setiap bahagian daripada ketiga-tiga aliran membawa `X-Chloros-Group-Id`, `X-Chloros-Frame-Id` dan `X-Chloros-Timestamp-Ns`. Bahagian daripada kamera berbeza dengan `X-Chloros-Group-Id` yang sama adalah pencetus yang sama: mengumpul bahagian dan mengelompokkannya pada header tersebut untuk menyusun semula satu set. Kamera berdiri sendiri tidak mempunyai kumpulan: bahagian pratontonnya tidak membawa mana-mana header ini (bahagian pantulan ia membawa `X-Chloros-Group-Id` kosong).
- **Aliran pratonton menjatuhkan bingkai di bawah beban.** Ia menyajikan bingkai terbaru dan melangkau mana-mana yang tidak dapat dihantar tepat pada masanya, jadi pada kadar susunan yang tinggi tidak setiap pencetus sampai ke aliran setiap kamera (lihat jadual di atas). Untuk setiap pencetus, gunakan `frame_stream()` atau `continuous()`.
- **`/api/camera/stream/<serial>` berehat sementara aliran langsung gabungan array dibuka** (`/api/camera/array/stream/<array_id>` — paparan array GUI membukanya). Aliran setiap ahli kemudian menghantar satu bingkai penanda tempat &quot;Berhenti (aliran tatasusunan aktif)&quot;, tanpa sebarang pengepala `X-Chloros-*`, dan tiada apa-apa lagi sehingga aliran gabungan ditutup; ia kemudian menyambung semula secara automatik. `stream-annotated` dan `stream-reflectance` tidak berehat. Tutup paparan tatasusunan GUI apabila skrip membaca aliran pratonton bagi setiap kamera.
- **Untuk nombor, bukan gambar, gunakan `frame_stream()`.**JPEG bersifat lossy dan 8-bit; `frame_stream()` memulangkan produk yang sama tanpa sebarang codec dalam laluan.

### Rakaman Jarak untuk Tinjauan Dron

Penerbangan tinjauan memerlukan satu kumpulan imej selari setiap beberapa meter perjalanan, bukan setiap bingkai yang boleh dihasilkan oleh susunan. Dua tetapan mengawal ini:****`target_fps`**menetapkan kekerapan kamera mengambil bingkai, dan**`capture_repeated(interval_s=…)`** menetapkan kekerapan bingkai disimpan.**Kamera terus mengalir antara penyimpanan.** Setiap selang menyimpan kumpulan selari terbaru; bingkai di antaranya tidak ditulis. Pendedahan automatik disesuaikan pada setiap bingkai, jadi pendedahan telahpun stabil apabila penyimpanan berlaku. Tiada mod di mana kamera berhenti antara rakaman dan dihidupkan semula untuk setiap satu: setiap rakaman kemudiannya perlu menunggu aliran bermula dan pendedahan stabil.**Kekalkan kadar bingkai mengikut keperluan selang masa.**Jika dibiarkan pada tetapan automatik, susunan akan berjalan pada kadar tertinggi yang disokong oleh hos dan rangkaian. Menetapkannya pada kadar yang lebih rendah mengurangkan haba kamera, beban rangkaian dan penggunaan CPU hos tanpa menjejaskan apa yang disimpan. Pilih kadar yang memberikan**2–4 bingkai setiap selang masa**:

- Setiap penyimpanan menunggu bingkai seterusnya selepas ia diminta, jadi satu kumpulan yang disimpan akan tiba sehingga satu tempoh bingkai selepas masanya yang dijadualkan. Kadar yang lebih tinggi mengekalkan jurang itu kecil.
- Pendedahan automatik menyesuaikan diri sekali setiap bingkai, jadi kadar yang sangat rendah melambatkan bagaimana pendedahan mengikuti awan yang lalu.

**Kira selang masa daripada pelan penerbangan:**

```
spacing    = along-track footprint × (1 − forward overlap)
interval_s = spacing / ground speed
```

Sebagai contoh, jejak 60 m pada tumpang tindih ke hadapan 80% adalah jarak 12 m; pada 10 m/s itu adalah `interval_s=1.2`, dan `target_fps=3` yang dipin memberi antara 3 hingga 4 bingkai setiap selang. Pengambilan adalah berasaskan masa: SDK tidak mempunyai mod pencetus jarak atau GPS, jadi pastikan kelajuan darat yang konsisten pada garisan tinjauan.

**Simpan data mentah pada pesawat dan kalibrasi kemudian.** `interval_s` diukur dari permulaan setiap penyimpanan, jadi penyimpanan yang mengambil masa lebih lama daripada selang akan menyebabkan yang seterusnya bermula lewat dan jaraknya tersasar. Menyimpan data mentah serta bacaan penderia cahaya (`processing="raw"`; bacaan ini disimpan secara lalai) memastikan setiap simpanan ringkas dan membolehkan setiap bingkai ditukar kepada pantulan kemudian.

```python
import chloros_sdk

INTERVAL_S = 1.2      # from the flight plan
FLIGHT_S = 20 * 60    # bounds the loop; it also stops at count= if given

with chloros_sdk.connect_array(serials, target_fps=3.0) as arr:
    groups = arr.capture_repeated(
        "survey/", interval_s=INTERVAL_S, duration_s=FLIGHT_S,
        processing="raw", render_index=False,
        on_capture=lambda i, r: print(f"group {i}: {len(r)} frames"))
```

- **`connect_array(target_fps=…)`**menetapkan kadar dari permulaan;**`arr.set_target_fps(fps)`**menukarnya di tengah-tengah penerbangan tanpa menyambung semula, dan**`arr.set_target_fps(None)`** memulangkannya kepada auto. Kadar melebihi apa yang boleh ditampung oleh hos dan rangkaian akan diturunkan kepada had tersebut — semak `clamped` dalam hasil — dan kadar terendah ialah 0.1 fps.
- **Jangan gunakan `burst()` untuk mengurangkan bilangan bingkai.** Ia menulis setiap bingkai yang dihasilkan oleh tatasusunan; ia mengikuti `target_fps` yang dipin, jadi ia adalah alat yang tepat hanya apabila setiap bingkai diperlukan.
- **`target_fps` tidak membaiki bingkai yang rosak.** Menurunkannya mengurangkan bilangan bingkai yang dihasilkan, bukan kelajuan setiap satu dihantar; untuk bingkai GVSP yang rosak, turunkan `wire_ceiling_mbps` sebaliknya (lihat [Kesihatan Susunan](#array-health--which-subsystem-is-losing-frames)).

```python
r = arr.set_target_fps(5.0)
if r["clamped"]:
    print(f"asked for {r['requested']} fps, running at {r['target_fps']}")
```

### Menyambung ke Susunan yang Sudah Terhubung — `attach_array`

Jika susunan sudah berjalan (GUI membukanya, atau sesi SDK sebelum ini memanggil `connect_array`), gunakan `attach_array` untuk mendapatkan pemegang kepadanya bukannya menyambung semula. `connect_array` sentiasa memberi ralat dengan &quot;Camera  <sn>sudah berada dalam array <id>&quot; dalam situasi itu, kerana menghantar `/array/connect` untuk ahli-dalam-kolam tidak idempoten; `attach_array` membaca `/api/camera/array/list` dan memadankan sama ada melalui array_id atau nombor siri.

```python
import chloros_sdk

# By serials (matches if every serial is a member of one existing array)
arr = chloros_sdk.attach_array(
    ["213800234", "214000533", "214701288", "214701292"])

# By array_id (when you've already noted it down)
arr = chloros_sdk.attach_array("array-1779862544497")

# attach_array returns the same ArraySession as connect_array
arr.capture("output/", processing="reflectance")
```

Corak: Skrip SDK yang berjalan serentak dengan GUI desktop harus mencuba `attach_array` terlebih dahulu dan beralih ke `connect_array` jika tiada sebarang array dalam kolam lagi.

```python
import chloros_sdk

try:
    arr = chloros_sdk.attach_array(serials)
except chloros_sdk.ChlorosConnectError:
    arr = chloros_sdk.connect_array(serials)
```

> **Susunan yang dilampirkan dipinjam, bukan dimiliki.** Pemegang daripada `attach_array` tidak pernah membongkar tatasusunan: sama ada pada keluarnya blok `with` atau melalui `disconnect()` yang dinyatakan, ia tidak melepaskannya (panggilan yang dinyatakan mencetak satu baris ke stderr yang menyatakan ia membiarkan array berjalan). Peraturan yang sama seperti `CameraSession` / `DAQSensorSession`. Jadi, melakukan co-tenancy dengan GUI adalah selamat sama ada dengan atau tanpa `with`:
>
> ```python
> with chloros_sdk.attach_array(serials) as arr:
>     arr.capture("output/", processing="reflectance")
> # … the array stays up for the GUI
> ```
>
> Apabila anda benar-benar berhasrat untuk menamatkannya — untuk menyambung semula dengan `wire_ceiling_mbps` yang berbeza, katakan — panggil `arr.force_disconnect()`. Menyambung semula selepas `disconnect()` biasa pada pemegang yang dilampirkan akan gagal dengan &quot;Kamera <sn>sudah </sn>berada <sn>dalam tatasusunan<id>&quot;, kerana tiada apa-apa yang dibebaskan.

### Pembantu Analisis Rangkaian

Berguna sebelum membuka tatasusunan — meramalkan sama ada tetapan yang dicadangkan akan muat:

```python
result = chloros_sdk.analyze_array_network(
    master_serial="214701288",
    slave_serials=["213800234", "214000533", "214701162"],
    width=2048, height=1536,
    pixel_format="BayerRG12",
    binning=1,
)

if result["status"] == "ok":
    print("Use the requested settings.")
elif result["status"] == "auto_capped_fps":
    r = result["recommended"]
    print(f"Keep the resolution; cap the trigger rate at {r['recommended_target_fps']} fps")
elif result["status"] == "auto_shrunk":
    r = result["recommended"]
    # width / height are sensor pixels BEFORE binning (what connect_array
    # takes with binning=); out_width / out_height is what is streamed.
    print(f"Connect with width={r['width']} height={r['height']} "
          f"binning={r['binning']} ({r['out_width']}x{r['out_height']} streamed)")
elif result["status"] == "needs_force_slip":
    print("Sim-sync impossible on this wire; force_tier='slip-emit-and-capture' required")
```

```python
analyze_array_network(
    master_serial,
    slave_serials=None,
    width=2048, height=1536,              # sensor pixels before binning
    pixel_format=None,                    # None = what connect applies (Mono12 / BayerRG12)
    binning=1,                            # 1 | 2 | 4
    force_tier=None,
    packed="auto",                        # "auto" | "on" | "off"
    backend_url="http://127.0.0.1:5000",
    timeout=30.0,
) -> dict
```

Ia tidak pernah memulakan backend: jika tiada yang menjawab, ia akan menimbulkan `ChlorosConnectError`, manakala permintaan yang ditolak akan dikembalikan sebagai hasil. `packed` yang salah akan menimbulkan `ValueError`.

`status` adalah salah satu daripada `ok` / `auto_capped_fps` / `auto_shrunk` / `needs_force_slip` (sebaliknya `error` — termasuk permintaan yang ditolak: tidak log masuk, `error_code: AUTH_REQUIRED`, atau pelan di bawah ambang SDK, `PLAN_UPGRADE_REQUIRED`). `auto_capped_fps` bermaksud resolusi yang diminta hanya sesuai dengan cincin RX pada kadar pencetus terhad — kekalkan resolusi dan hantar `target_fps=result["recommended"]["recommended_target_fps"]` ke `connect_array` (lihat [Contoh 6](#6-capability-probe-before-connecting-a-4-cam-array)). `auto_shrunk` hanya muncul apabila bingkai yang diminta tidak dapat dihantar pada kadar bingkai apa pun; connect tidak pernah mengecilkan bingkai secara sendiri, jadi terapkan `recommended` sendiri.

**Cara membaca projeksi** (model yang sama seperti panel Tetapan Susunan GUI):

- **Burst (`frame_bytes_total`) dijumlahkan bagi setiap kamera mengikut format piksel sebenar setiap kamera.**Mono**M3M**kamera menyalurkan Mono12 (2 B/px), atau dengan `pixel_format` format Mono kedalamannya (`"BayerRG8"` adalah `Mono8`, 1 B/px), jadi dengan tiada penalaian, satu bingkai penuh-resolusi empat kamera adalah**~25 MB** dengan tiga kamera mono, bukan ~12.6 MB seperti yang dijangka jika semua 8-bit. Backend menentukan format setiap kamera daripada modelnya.
- **Admittance (`burst_fits_nic_ring`) sedar tentang saliran**, bukan keseluruhan-burst-vs-ring: sim-emit sesuai apabila hos mengosongkan RX ring lebih pantas daripada kamera-kamera mengisinya. Hos 10G + kamera 1 GbE**membenarkan** resolusi penuh walaupun burst melebihi ring; hos 1 GbE menyekat (`needs_force_slip` / `auto_shrunk`).
- **`achievable_fps_max` adalah had selamat untuk pengambilan bersiri** — `max(readout+emit, N×emit)` dengan keluaran per-kamera dikekang pada pautan kamera 1 GbE, tidak bergantung pada pendedahan. Contohnya ~2.8 fps untuk susunan 4-kamera resolusi penuh 12-bit (menyesuaikan dengan ukuran runtime ~2.7–3.0). Model penuh: [Rujukan CLI → Model fps &amp; burst Array](cli-reference.md#array-fps--burst-model).
- **Selepas tangkapan berterusan, projeksi menunjukkan kadar yang dicapai.** Tangkapan selama 30 saat atau lebih pada kadar automatik merekodkan kadar yang stabil pada saiz tatasusunan tersebut pada komputer ini; percubaan singkat pengawal kadar pada kadar yang lebih pantas diabaikan. Selepas itu `array-connect`&#x27;garis, analisis `connect_array()` dan panel Tetapan Susunan memetik kadar itu apabila ia di bawah model, dengan apa yang mengehadkannya dalam `cycle_limiter`: `"clock"` (array itu berjalan pada kadar yang telah ditetapkan, gelung grab menunggu jam pencetusnya), `"loop"` (gelung grab tidak mempunyai masa lagi), `"capture"` (proses tangkapan ketinggalan dan melangkau kumpulan) atau `"measured"` (tiada yang menyatakan mana satu). Angka model itu terdapat dalam `wire_fps_max`. Susunan masih meningkat mengikut apa yang dibawa oleh wayar.
- **Pendaftaran berlebihan (`oversubscribed: true`) bermaksud wayar tidak dapat membawa susunan pada mana-mana kadar yang dicapai oleh kamera** — medan fps (`achievable_fps_max` / `fps_bright` / `fps_dark`) dibaca 0, dan binning tidak dapat membetulkannya (ia menurunkan bait per bingkai, bukan bait per saat yang dikawal kelajuannya). Kabel perlahan sahaja tidak menyebabkan ini: kadar bingkai sebaliknya akan menurun, dan jika kamera tidak dapat dikawal kelajuannya dengan cukup perlahan, lapisan menjadi `slip-emit-and-capture` (siri). Penyelesaiannya ialah mengurangkan bilangan kamera, menggunakan bingkai jumbo, atau memasang NIC yang lebih pantas; `max_cams_collision_safe` melaporkan had maksimum untuk talian dan kamera anda. Respon juga mengandungi `aggregate_demand_bps`, `collision_safe_ceiling_bps`, dan `per_cam_floor_bps`. Lihat [Over-Subscription](#over-subscription-the-per-cam-floor).

### Penemuan &amp; Penyenaraian

```python
chloros_sdk.discover_lattice_cameras()   # list all cams visible to the backend
chloros_sdk.list_cameras()               # cams currently in the pool
chloros_sdk.list_arrays()                # active arrays in the pool
```

Setiap satu mengambil `backend_url=` dan `timeout=30.0` yang hanya menggunakan kata kunci, dan mengembalikan senarai dicts. `discover_lattice_cameras()` (`serial`, `ip`, `mac`, `model`, `lattice_model`, `vendor`, `connected`, `array_id`, …) juga mengambil `auto_start_backend=True`; panggilan `list_*` tidak pernah memulakan backend.

---

## Smart-AE / Smart-Capture

Susunan LATTICE menjalankan AE berterusan di latar belakang sebaik sahaja ia disambungkan, tetapi pemandangan yang baru diarahkan mengambil masa untuk mencapai konvergens. **Smart-capture** ialah kemudahan terbungkus: ia memantau pendedahan setiap kamera, menunggu sehingga susunan stabil merentasi tetingkap, kemudian mencetuskan tangkapan. Ia setara GUI: butang tangkapan &quot;pintar&quot; aplikasi desktop memanggil hujung titik belakang yang sama.

```python
import chloros_sdk

with chloros_sdk.connect_array([
        "213800234", "214000533", "214701288", "214701292"]) as arr:
    # Initial pose
    arr.capture("pose_a/", processing="reflectance", smart=True)
    input("Move the rig, then press Enter...")
    # New pose — smart-capture waits for AE to re-settle automatically
    arr.capture("pose_b/", processing="reflectance", smart=True)
```

Apabila memandu melalui `ChlorosProject` (seksyen seterusnya) anda mendapat lebih banyak tombol:

```python
proj.arrays["main_rig"].capture_smart(
    output_dir="out/",
    processing="reflectance",
    settle_timeout_s=5.0,           # max wait
    stability_window_s=1.5,         # exposure must hold steady this long
    exposure_tolerance_pct=5.0,     # %-spread allowed within the window
)
```

Dasar AE pintar secara lalai adalah konservatif. Ketatkan `exposure_tolerance_pct` untuk kerja radiometrik yang cerewet; longgarkan untuk babak yang berubah dengan pantas di mana anda hanya mahukan &quot;cukup hampir.&quot;

---

## Sesi Sensor DAQ

Kolam backend berterusan untuk penderia spektral (DAQ-U melalui USB, DAQ-M melalui BLE, DAQ-E melalui Ethernet). Meniru permukaan kamera: pengesanan pintar, penggunaan semula kolam, sambungan idempotent.

### Smart-Detect (Konfigurasian Sifar)

```python
import chloros_sdk

with chloros_sdk.connect_daq_sensor() as daq:
    print(daq.model, daq.transport, daq.address)
    for frame in daq.latest(n=10):
        spectrum = frame["spectrum"]   # list[float] (W/m²/nm if calibrated)
        is_sat = frame["is_saturated"]
        x, y, z = frame["x"], frame["y"], frame["z"]
        print(len(spectrum), is_sat)
```

Keutamaan: Ethernet → BLE → USB. Masukkan sebarang petunjuk eksplisit untuk menetapkan pengangkutan.

### Pengangkutan Terpaut

```python
# DAQ-U on a specific serial port
daq = chloros_sdk.connect_daq_sensor(transport="usb", port="COM3")

# DAQ-M over BLE by MAC (implies transport="ble")
daq = chloros_sdk.connect_daq_sensor(mac="AA:BB:CC:DD:EE:FF")

# DAQ-E over Ethernet by hostname (implies transport="eth")
daq = chloros_sdk.connect_daq_sensor(eth_host="daq-e-xxx.local")

# A specific sensor by id (as list_daq_sensors() shows it; ':' or '-' alike)
daq = chloros_sdk.connect_daq_sensor(sensor_id="CB-7C-A7-5A-73")

# Tuning knobs
daq = chloros_sdk.connect_daq_sensor(
    port="COM3",
    integration_time=64,      # ms
    frame_avg=20,
    enable_ae=True,
    start_streaming=True,
)
```

`sensor_id=` menamakan satu penderia, dan hanya penderia itu yang akan dibuka. Ia dipadankan dengan penderia yang sudah terbuka, kemudian nama hos atau IP DAQ-E (sebuah DAQ-E yang diimbas oleh rangkaian hanya melalui IP diminta, bacaan sahaja, untuk namanya, MAC dan siri, supaya mana-mana daripadanya juga menemuinya), BLE MAC DAQ-M, dan ID yang dilaporkan oleh DAQ-U atau DAQ-M setelah dibuka (mana-mana yang lain yang dibuka untuk semakan akan ditutup semula). Jika ia tidak ditemui, `connect_daq_sensor` memanggil `ChlorosConnectError` yang menyenaraikan apa yang ditemui; dengan alamat juga (`port=` / `mac=` / `eth_host=`) yang memegang sensor berbeza, ia juga akan diangkat. Ia tidak pernah kembali kepada mana-mana sensor yang kelihatan — itulah tujuan panggilan bendera sifar.</id></sn></id></sn>

### `connect_daq_sensor()` Tandatangan

```python
connect_daq_sensor(
    *,                                    # every argument is keyword-only
    sensor_id=None,                       # one named sensor (see above)
    transport=None,                       # "usb" | "ble" | "eth"; None = smart-detect
    port=None,                            # DAQ-U serial port ("COM3", "/dev/ttyUSB0")
    mac=None,                             # DAQ-M BLE MAC (implies transport="ble")
    eth_host=None,                        # DAQ-E hostname or IP (implies transport="eth")
    integration_time=None,                # ms; None = the sensor's own
    frame_avg=None,
    enable_ae=None,
    cap_id=None,                          # cap correction: "sunshine_cosine", "fov_30", ..., "none" (bare)
    start_streaming=True,
    backend_url="http://127.0.0.1:5000",
    timeout=60.0,
    auto_start_backend=True,              # spawn a local backend if none is running
) -> DAQSensorSession
```

Sensor yang sudah berada dalam kolam dikembalikan seperti sedia kala (dipinjam): `integration_time`, `frame_avg` dan `enable_ae` tidak diterapkan padanya. Putuskan sambungannya terlebih dahulu untuk membukanya dengan tetapan anda. `cap_id` adalah pengecualian: pada sensor yang dipinjam ia ditetapkan dengan `set_cap()`. Jika diabaikan, lalai backend akan digunakan (lihat nota di bawah jadual kaedah).

### Kaedah `DAQSensorSession`

| Kaedah | Deskripsi |
| --- | --- |
| `status(timeout=10.0)` | Ringkasan kemasukan kolam (status penstriman/perekodan, julat panjang gelombang, sha penentukuran, masa integrasi, frame_avg, status AE). Untuk DAQ-E yang streamnya menjadi senyap: `reconnect` (di mana sambungan semula automatik backend berkuatkuasa: `state`, `attempts`, `next_attempt_in_s`, `last_error`; `None` semasa ia menyalurkan), `stream_lost_s`, `reconnects` dan `last_reconnect`, dan `record_gaps` untuk rakaman yang sedang dijalankan; `rx_stream_fault` menunjukkan keadaan menyambung semula sehingga spektrum pertama tiba. |
| `latest(*, n=1, timeout=10.0)` | Kembalikan sehingga N bingkai spektrum terkini (masing-masing `ts_ns`, `spectrum`, `is_saturated`, `integration_time`, `x`, `y`, `z`). |
| `stream_start()` / `stream_stop()` | Sambung / jeda penstriman (tangan pemegang kekal terbuka). |
| `record_start(*, output_dir=None, device_name=None, timeout=15.0)` | Mulakan rakaman fail .daq. Kembalikan laluan fail. Ditolak untuk DAQ-U/M tanpa bundel penentukuran AWS (DAQ-E dikecualikan). |
| `record_stop()` | Hentikan rakaman. Mengembalikan `{path, rows, warning, gaps}`; `warning` ialah `None` untuk rakaman yang bersih, sekiranya tidak, diagnosis backend (contohnya fail kosong atau jurang dalam aliran), yang juga dicetak ke stderr. `gaps` menyenaraikan setiap bahagian di mana aliran tidak menyampaikan apa-apa: `start_utc`, `end_utc` (`None`: masih hilang semasa pemberhentian), `duration_s`, `reconnected`, `marked_in_file`. Ia kosong untuk rakaman berterusan. |
| `set_cap(cap_id, timeout=15.0)` | Tetapkan profil pembetulan cap yang digunakan pada bacaan dari yang seterusnya: `"sunshine_cosine"`, `"fov_15"` ... `"fov_90"`, atau `"none"` (atau `None`) untuk sensor kosong. Mengembalikan sensorringkasan, yang dibaca semula oleh `cap_id`. DAQ-E-S menyimpan `as_recorded` apa sahaja yang diminta (penyebarannya terbina dalam). Mengaktifkan `ChlorosConnectError` untuk profil yang melakukan tidak memuat. CLI: `chloros-cli daq pool-set-cap`. |
| `enable_ptp(timeout=15.0)` | DAQ-E sahaja: hidupkan semula PTP untuk unit yang PTP-nya dimatikan (`status()["rx_ptp_enabled"]` adalah `False`; tetapan disimpan pada unit, jadi ia kekal dimatikan sehingga dihidupkan semula). Mengembalikan status yang dikemas kini. Mengaktifkan `ChlorosConnectError` untuk DAQ-U / DAQ-M, atau apabila unit tidak mengesahkan. Chloros tidak pernah menghidupkan semula unit secara automatik, kerana keadaan mati mungkin disengajakan. CLI: `chloros-cli daq pool-enable-ptp`. |
| `device_info(timeout=15.0)` | Hanya DAQ-E: tetapan unit itu sendiri. `firmware` (`running`, `bundled`, `update_available`), `ip` dan `ip_mode` (`dhcp` / `linklocal` / `static`: apa yang sedang berjalan sekarang), `network` (tetapan yang disimpan: `mode`, ditambah `ip` / `mask` / `gateway` apabila statik), `network_change_pending` (benar sehingga unit menjalankan tetapan yang disimpan, satu disimpan dengan `set_network(..., reboot=False)` termasuk), `auto_stream`, `ptp`. `firmware["update_available"]` adalah `None` apabila unitversi tidak dapat dibaca. Walaupun aliran unit tidak sampai ke komputer ini, ia dibaca pada sambungan baharu dan kamus menambah `stream_silent` (`True`), `stream_fault` dan `reconnect`; unit yang tidak menjawab membangkitkan. CLI: `pool-info`. |
| `set_network(mode="auto", *, ip=None, mask=None, gateway=None, reboot=True, force=False, comeback_timeout=45.0)` | DAQ-E sahaja: DHCP (`"auto"`) atau alamat statik. Mulakan semula unit untuk menerapkannya dan menyambung semula; sesi ini terus berfungsi dan `address` mengikuti unit tersebut. `reboot=False` hanya menyimpannya. Lihat [Penyetelan Peranti DAQ-E](#daq-e-device-settings). CLI: `pool-set-network`. |
| `set_auto_stream(enabled, timeout=15.0)` | DAQ-E sahaja: mengalir semasa kuasa-hidup tanpa perisian disambungkan (disimpan pada unit). Mengembalikan tetapan yang disahkan. CLI: `pool-set-auto-stream`. |
| `reboot(*, comeback_timeout=45.0)` | Hanya DAQ-E: mulakan semula unit dan sambungkan semula dengan tetapan asal. CLI: `pool-reboot`. |
| `update_firmware(*, comeback_timeout=45.0)` | DAQ-E sahaja: flash firmware yang disertakan dengan keluaran Chloros ini ke dalam unit yang menjalankan versi lama, kemudian sambungkan semula (kira-kira satu minit; jangan matikan kuasa). Mengembalikan `updated`, `fw_before`, `fw_after`, `bundled`, dan `warning` apabila unit mengambil masa lama untuk kembali atau kembali pada versi lain; `updated` ialah `False` untuk unit yang sudah terkini. Menghasilkan `ChlorosConnectError` untuk unit yang versinya tidak dapat dibaca. CLI: `pool-update-firmware`. |
| `disconnect()` | Pembebasan daripada kolam. Tidak-op untuk pemegang yang dilampirkan tetapi tidak dimiliki. Pada sensor sesi ini dibuka ia adalah `force_disconnect()`, dan membangkitkan `ChlorosConnectError` apabila pelepasan tidak disahkan (keluar blok `with` tidak pernah diangkat). |
| `force_disconnect(timeout=30.0)` | Lepaskan sensor tanpa mengira siapa yang membukanya — contohnya sensor yang ditinggalkan dalam kolam oleh skrip yang rosak, yang hanya diakses oleh skrip baru sebagai pemegang bersambung yang `disconnect()` tidak melakukan apa-apa. Mengakhiri sensor pelanggan lain menamatkan sebarang rakaman yang sedang dibuat. Mengembalikan apabila backend mengesahkan pelepasan, atau apabila tiada apa-apa untuk dilepaskan (sensor tidak berada dalam kolam, atau tiada backend yang sedang berjalan); pelepasan yang tamat masa atau yang ditolak oleh backend akan mencetuskan `ChlorosConnectError`. CLI: `chloros-cli daq pool-disconnect --sensor-id ID`. |

Atribut: `sensor_id`, `model`, `transport`, `address` (mengikuti DAQ-E yang dialamatkan semula oleh `set_network()`), `info` dan `backend_url`.

> **Namakan penutup itu.** DAQ-U, DAQ-M dan DAQ-E dihantar tanpa penahan, dan Chloros tidak dapat melihat sama ada ia dipasang: sensor yang disambungkan tanpa `cap_id` menerima `sunshine_cosine`, satu andaian yang menurut rakaman mereka telah dibuat (`cap_id_source=auto_default`). DAQ-U tanpa penutup di bawah pembetulan itu membaca kira-kira 30x lebih tinggi, jadi pasangkan `cap_id="none"` untuk sensor tanpa penutup (atau `set_cap("none")`), dan profil topi yang anda pasang sebaliknya.

### DAQ-E-S — penyebar tetap dan sensor condong

**DAQ-E-S** adalah DAQ-E dengan dua perbezaan yang penting bagi perisian. Segala yang lain — penemuan, penyambungan, PTP, rakaman, tetapan peranti di bawah — berfungsi dengan tepat seperti DAQ-E.**Penyalurnya adalah kekal, jadi ia tidak memerlukan pembetulan penutup.** Penyebar gaya Sunshine terbina dalam dan telah dipasang pada unit semasa pengesahan kilangannya diukur, jadi pengesahan itu sudah membaca iradiasi pancaran ke bawah yang sebenar. Chloros menetapkan topinya kepada `as_recorded` (tiada profil) apa sahaja yang diminta: topi yang dinamakan untuknya (`sunshine_cosine`, `fov_*`, `none`) diabaikan dengan mesej, dan rakaman simpanannya `cap_id_source=model`, jadi amaran topi andaian tidak pernah muncul untuknya. Jangan terapkan cap pada datanya secara manual juga: `sunshine_cosine` di atas akan membaca kira-kira 11x lebih tinggi.

**Ia dilengkapi akselerometer 3-paksi.** Tindak balas kosinus penyebar cahaya matahari hanya sah selagi ia rata, jadi sensor yang condong melaporkan iradiasi yang salah — dan pantulan mewarisi ralat tersebut — tanpa sebarang simptom lain. Setiap bacaan DAQ-E-S oleh itu membawa orientasi sendiri:

| Medan | Maksud |
| --- | --- |
| `tilt_deg`, `roll_deg`, `pitch_deg` | Sudut dalam darjah. `None` apabila firmware menandakan sudut tidak ditakrifkan (kejatuhan bebas, paksi mati). |
| `x_mg`, `y_mg`, `z_mg`, `mag_mg` | Pecutan dalam mili-g. `mag_mg` jauh daripada ~1000 bermakna unit itu sedang bergerak dan sudut tidak bermakna untuk sampel tersebut. |
| `cal_applied` | Tetapkan: paksi dan sudut berada dalam bingkai diffuser (penentukuran pemasangan unit telah diterapkan). Nyah: bingkai papan mentah, yang boleh menunjukkan sehingga ~2.3° kecenderungan yang bukan sebenar. |
| `sample_age_ms` | Umur sampel akselerometer apabila spektrum dihantar. |
| `temp_raw` | **trend** suhu die yang tidak dikalibrasi — hanya perbezaan, bukan bacaan termometer. |

`DAQSensorSession.latest()` memulangkan nilai-nilai tersebut di bawah kunci `imu` bagi setiap bingkai (`None` pada DAQ-U, DAQ-M atau DAQ-E biasa). `.daq` yang direkodkan daripada DAQ-E-S menyimpan nilai yang sama dalam lajur `imu_*`-nya (`imu_tilt_deg`, `imu_roll_deg`, `imu_pitch_deg`, `imu_mag_mg`, `imu_cal_applied`, …) di sebelah setiap spektrum, supaya pembaca dapat membezakan awan dengan tripod yang terjatuh. Pada setiap model lain, lajur-lajur tersebut kosong.

Aliran sikap melaporkan medan-medan tersendiri dalam `status()` (`rx_imu_stream_fault`, `rx_imu_stream_faults`, `rx_last_imu_stream_fault`), dipisahkan daripada yang spektral supaya gangguan akselerometer tidak pernah dibaca sebagai jurang dalam rakaman. `discover_daq_sensors()` dan `model` sesi melaporkan `DAQ-E-S`.

### Tetapan Peranti DAQ-E

```python
with chloros_sdk.connect_daq_sensor(eth_host="daq-e-def330.local") as daq:
    info = daq.device_info()
    print(info["ip"], info["ip_mode"], info["firmware"])

    # A fixed address on the network this computer is on
    daq.set_network("static", ip="192.168.1.50", gateway="192.168.1.1")
    print(daq.address)              # 192.168.1.50: the session follows it

    if info["firmware"]["update_available"]:
        daq.update_firmware()
```

- **Alamat statik mesti ada di rangkaian komputer ini.** `set_network` mencetuskan `ChlorosConnectError` untuk yang subnetnya tidak termasuk komputer ini&#x27;alamat peranti pada rangkaian sensor: selepas dihidupkan semula tiada apa-apa yang boleh mencapai sensor untuk mengubahnya kembali. Hantar `force=True` hanya apabila komputer ini akan dialamatkan semula, atau dirut ke subnet baru.
- **`set_network`, `reboot` dan `update_firmware` memulakan semula sensor**, dan meningkatkan `ChlorosConnectError` semasa ia merekod, dan apabila ia tidak kembali dalam masa `comeback_timeout` saat. ID sensor dan nama hos `daq-e-<id>.local` tidak pernah berubah.
- Rakaman yang dimulakan sejurus sebelum restart ditutup olehnya, dan diberi nama. Apabila sensor kembali, dict yang dikembalikan membawa `recording_closed` (jalur fail) dan `warning` yang menyatakan demikian. Apabila ia tidak kembali, atau pemindahan firmware gagal, `ChlorosConnectError` akan menyatakan demikian sebaliknya: mesejnya diakhiri dengan amaran, dan `e.recording_closed` / `e.warning` ditetapkan (kedua-dua `None` apabila tiada rakaman ditutup). Mulakan rakaman semula sebaik sahaja sensor kembali.
- **Hanya sensor itu sendiri yang disambungkan semula.** Selepas dihidupkan semula, unit mesti menjawab di mana ia berada -- dan selepas `set_network("auto")` juga di bawah nama `.local` -- dengan namanya, nama hos dan model, daripada penyesuai rangkaian (MAC) yang dimilikinya; `address` menjadi IP yang menjawab. Jawapan di bawah nama hanya dikira dengan MAC tersebut, jadi daripada komputer yang mengakses sensor melalui penghala, atau daripada rangkaian NAT lalai WSL2, sensor yang dipindahkan oleh DHCP tidak disambung semula (`ChlorosConnectError`; sambungkannya semula pada alamat barunya).
- **Kemas kini firmware memerlukan sensor menyambung semula.** `update_firmware()` menjemput unit itu, dan unit itu membuka sambungan TCP kembali ke komputer ini untuk imej, pada port yang dipilih oleh OS. Backend mendengar pada penyesuai yang mencapai unit itu (pada setiap penyesuai hanya apabila ia tidak dapat menentukan yang mana satu) dan hanya melayani unit itu: sebarang sambungan lain ditolak (`[DAQ-FW] … refused a firmware connection from <ip>` dalam keluaran backend) sementara ia terus menunggu. Tembok api yang menyekat sambungan itu akan menyebabkan kemas kini gagal dengan `device never connected back on TCP <port>`. Pemasang Windows menambah peraturan untuknya (lihat [Langkah 1](#windows-exe)); pada firewall lain, benarkan TCP masuk ke backend daripada subnet sensor.
- **Di bawah WSL2, gunakan rangkaian cerminan** (`networkingMode=mirrored` dalam `.wslconfig`): dalam mod NAT lalai setiap alamat statik ditolak (alamat WSL sendiri tidak berada di rangkaian sensor), aliran itu tidak sampai ke WSL, dan kemas kini firmware tidak dapat diselesaikan.
- GUI mengekalkan sambungan sendiri ke sensor: permulaan semula daripada skrip menamatkan sesi GUI dengannya. GUI mempunyai tetapan yang sama (tab Penderia Cahaya, tetapan sensor, Rangkaian).

### Penemuan — mencari alamat untuk disambung

`discover_daq_sensors()` mengimbas USB / BLE / ETH untuk penderia yang *boleh* anda buka. Ia adalah padanan DAQ kepada `discover_lattice_cameras()`, dan satu-satunya cara untuk mendapatkan **MAC BLE DAQ-M** — DAQ-E mempunyai nama hos dan DAQ-U mempunyai port COM, tetapi MAC tidak dicetak pada peranti mahupun disenaraikan oleh OS.

```python
for s in chloros_sdk.discover_daq_sensors():
    print(s["transport"], s["address"], s["model"], s["extra"])
# ble  C3:D8:85:E0:0A:19  DAQ-M  {'name': 'NSP32_SPECTRUM'}
# usb  COM3               DAQ-U  {'vid': '0403', 'pid': '6001', 'manufacturer': 'FTDI'}

# `address` is exactly what connect_daq_sensor wants:
for s in chloros_sdk.discover_daq_sensors(transports=["ble"]):
    if s["model"] == "DAQ-M":
        daq = chloros_sdk.connect_daq_sensor(mac=s["address"])
```

| Medan | Keterangan |
| --- | --- |
| `transport` | `usb` \| `ble` \| `eth`. |
| `address` | Port COM / MAC BLE / nama hos — lalui ke `connect_daq_sensor` sebagai `port=` / `mac=` / `eth_host=`. |
| `display` | Label boleh dibaca manusia. |
| `model` | `DAQ-U` \| `DAQ-M` \| `DAQ-E` \| `DAQ-E-S`, atau `None`. Sensor Ethernet ialah `DAQ-E-S` atau `DAQ-E` kerana ia melaporkan dirinya sendiri apabila diminta, hanya-baca, semasa imbasan (`DAQ-E` apabila ia tidak menjawab tepat pada masanya). Port siri USB membaca `DAQ-U` apabila cip jambatan port itu adalah salah satu yang digunakan oleh DAQ-U (FTDI, CH340, CP210x, Prolific) — petunjuk daripada keterangan port tersebut, bukan probe — dan `None` untuk mana-mana peranti USB lain, yang masih disenaraikan supaya anda boleh menyamarnya sebagai `port=`. Port bersiri tanpa ID vendor USB dan tanpa cip jambatan sedemikian (UART papan induk seperti `/dev/ttyS*` pada Linux, pautan bersiri Bluetooth, Intel AMT) tidak boleh menjadi DAQ-U dan tidak disenaraikan. |
| `extra` | Butiran per-pengangkutan (nama diiklankan BLE; USB `vid`, `pid`, `manufacturer`, `serial_number`; DAQ-E ip/fw/…). Nilai kosong diabaikan. |

| Parameter | Lalai | Keterangan |
| --- | --- | --- |
| `transports` | ketiga-tiganya | Susunan (atau rentetan CSV) yang mengehadkan imbasan. Berbaloi digunakan apabila anda tahu apa yang anda mahukan — BLE adalah bahagian yang perlahan. |
| `scan_timeout` | 5 | Tetingkap imbasan per-pengangkutan dalam saat; backend mengehadkan kepada 1–20. |
| `timeout` | 60.0 | Had HTTP untuk keseluruhan panggilan (seperti di tempat lain dalam SDK). |
| `auto_start_backend` | `True` | Memulakan backend tempatan jika tiada yang sedang berjalan. Tidak pernah memulakan untuk `backend_url` jauh. |

> **Penderia yang sudah terbuka dalam kolam tidak akan muncul.** Peranti sekeliling BLE yang bersambung berhenti mengiklankan dan port COM terbuka tidak dapat diuji, jadi penemuan akan menyenaraikan apa yang *tersedia untuk disambung*. Hasil kosong sejurus selepas anda menyambungkan sesuatu adalah dijangka — gunakan `list_daq_sensors()` untuk apa yang sudah anda pegang. Pengangkut yang imbasannya tidak dapat dijalankan (tiada bleak / zeroconf dipasang) akan dilangkau daripada menimbulkan ralat, jadi mesin tanpa Bluetooth masih menerima jawapan USB dan ETH.

### Penyenaraian

```python
for s in chloros_sdk.list_daq_sensors():
    print(s["sensor_id"], s["model"], s["transport"], s["wavelength_range"])
```

### Mana Satu DAQ Menyediakan Kamera — `daq_bindings()`

Pengembalian sesuatu tangkapan, dan `.daq` yang disimpannya, menggunakan DAQ yang diikat pada kamera atau tatasusunan tersebut, jika tidak, ia menggunakan DAQ terkini yang dimiliki oleh backend. Pemilihan penderia cahaya pada GUI dan `chloros-cli daq pool-bind` menetapkannya; begitu juga:

```python
chloros_sdk.set_daq_bindings(cameras={"214701281": "COM7"},          # a sensor id or an address
                             arrays={"array-1779862544497": "daq-e-def330"})
chloros_sdk.set_daq_bindings(cameras={"214701281": None})            # None clears it
for serial, b in chloros_sdk.daq_bindings()["cameras"].items():
    print(serial, b["binding"], b["source"], b["resolved"]["in_pool"])
```

`daq_bindings()` mengembalikan `{"cameras": {serial: b}, "arrays": {array_id: b}}`: setiap `b` memegang `binding` (nilai yang ditetapkan), `source` (`project`, daripada `sensors.json` projek, atau `runtime`), `known` / `known_as` (sama ada kunci kamera atau tatasusunan menamakan satu yang diketahui oleh Chloros, dan bagaimana: `connected`, `project` — `cameras.json` projek terbuka — atau, untuk kamera, `seen`: dibuka pada komputer ini sebelum ini; `known: False` untuk kunci yang tidak menamakan apa-apa) dan `resolved` (sensor kolam yang kini dipetakannya: `in_pool`, `sensor_id`, `address`, `model`, `streaming`). Pengikatan runtime mengatasi projekuntuk sesi; fail projek tidak diubah. Mencabut sambungan kamera, tatasusunan atau DAQ akan membatalkan semua pengikatan perkakasan tersebut, tanpa mengira siapa yang menetapkannya (ahli tatasusunan bersama tatasusunan itu).

`set_daq_bindings()` hanya mengikat kamera yang disambungkan, sama ada dalam projek terbuka atau dibuka pada komputer ini sebelum ini, dan tatasusunan yang disambungkan atau dalam projek terbuka (atau kekunci yang sudah diikat). Mana-mana kekunci lain akan mencetuskan `ChlorosConnectError` yang menamakannya (`400`, `code` `UNKNOWN_BINDING_TARGET`, dengan kunci `unknown`), dan **tiada apa-apa** dalam panggilan itu diikat. `None` membersihkan mana-mana kunci.

### Reflektan Sekitar + Objek — `light_sensor_reflectance()`

Dua DAQ yang digabungkan, satu menghadap ke langit (sekitar) dan satu lagi menghadap ke sasaran (objek), memberikan indeks pantulan dan vegetasi sasaran daripada bacaan terkini mereka: pasangan GUI Sekitar + Objek, dan `chloros-cli daq reflectance` tanpa membuka semula sensor buat kali kedua. Kedua-duanya mesti disambungkan dan sedang mengalir.

```python
r = chloros_sdk.light_sensor_reflectance("daq-e-def330", "daq-e-54b774")
print(r["reflectance_type"])                       # "Apparent Reflectance" when both are calibrated
for name, band in r["bands"].items():
    print(name, band["rho"] if band["valid"] else band["reason"])
for idx in r["indices"]:
    print(idx["name"], idx["value"])
```

```python
light_sensor_reflectance(
    ambient_sensor_id, object_sensor_id, *,
    bands=None,          # [{"name", "center_nm", "fwhm_nm", "role"}]; role blue / green / red / nir
    preset=None,         # a preset id from daq_band_presets(); default: the suggested one, else "GENERIC"
    expressions=None,    # custom indices: [{"name": "MyNDVI", "expr": "(NIR_850 - Red_660) / (NIR_850 + Red_660)"}]
    builtins=True,       # include the built-in indices
    backend_url="http://127.0.0.1:5000", timeout=30.0,
) -> dict
```

- **Gelombang.** Reflektan setiap gelombang adalah gelombang objek-radiasi terintegrasi di atas latar sekitar. `daq_band_presets(filter_code=None)` menyenaraikan set jalur: satu untuk setiap penapis LATTICE M3C (`RGN`, `OCN`, `NGB`, model jalur kamera itu sendiri, jadi DAQ NDVI dan NDVI kamera adalah kuantiti yang sama) dan `GENERIC` jalur lebar. `suggested` adalah preset yang sepadan dengan `filter_code`, jika tidak, ia menggunakan penapis kamera pertama yang disambungkan (`suggested_from` menyatakan yang mana).
- **Kembalikan** `bands` (per jalur `rho`, `valid`, `reason`, `e_ambient`, `e_object`), `indices` (yang dibina-ins), `custom` (masing-masing dengan `value`, atau `valid: False` dan `error`), `invalid_bands`, `reflectance_type`, `preset` dan dua bacaan `timestamps` (`ts_ns`). Sebarang julat yang tidak dapat diukur oleh sensor persekitaran (di luar julatnya, pada paras hingar) adalah tidak sah, dengan sebab tersebut, dan setiap indeks yang memerlukannya diabaikan, tidak pernah dikira berbanding sifar.
- **Kelihatan atau relatif.** Indeks bergantung skala (SAVI, OSAVI, EVI, MSAVI2, RDVI, TDVI, LAI, NLI, MNLI, FCI, GEMI) memerlukan nisbah mutlak, jadi ia hanya diperoleh apabila kedua-dua penderia telah dikalibrasi kuasa (masing-masing mempunyai `calibration_bundle_sha`): `"Apparent Reflectance"`. Jika tidak, hasilnya adalah `"Relative Reflectance"`, hanya dengan indeks yang tidak berubah skala.
- **Dua grid panjang gelombang berbeza** dibandingkan berdasarkan tumpang tindihnya: titik-titik penderia persekitaran di dalam julat objek, spektrum objek diinterpolasi ke atasnya.
- **Dua penderia, bukan satu.** Menamakan satu penderia sebagai kedua-duanya (id yang sama, dalam apa jua ejaan yang diterima oleh kolam) akan menimbulkan `ChlorosConnectError`: bacaan penderia terhadap dirinya sendiri adalah 1.0 pada setiap jalur, yang tidak mengukur apa-apa. Backend juga menolaknya (`POST /api/daq/indices` menjawab `400`, `code` `SAME_SENSOR`, apabila `ambient_sensor_id` dan `object_sensor_id` yang dihantar oleh SDK adalah satu sensor berkongsi, contohnya id dan portnya).

### Penyewaan Bersama dengan CLI dan Skrip Lain

Kolam sensor DAQ backend dikongsi oleh setiap klien CLI dan SDK pada mesin. Jika sesuatu sensor sudah berada dalam kolam — dibuka oleh `chloros-cli daq pool-connect` atau oleh skrip lain — memanggil `connect_daq_sensor(port="COM3")` dari Python akan mengembalikan pemegang pinjaman: `disconnect()` adalah no-op, jadi skrip anda tidak mencabut sensor daripada klien yang membukanya apabila keluar daripada scope. Perkara yang sama terpakai untuk `sensor_id=` (ditaip dengan `:` atau `-`, kedua-duanya diterima), untuk DAQ-E dinamakan oleh IP-nya apabila ia dibuka oleh nama hos (atau sebaliknya), dan untuk pengesanan pintar bendera sifar apabila backend menjawab dengan sensor yang sudah dipegangnya — blok `with` hanya akan melepaskan sensor yang dibuka sendiri.

Antaramuka GUI Chloros **tidak** termasuk dalam kumpulan ini: panel DAQ-nya menjalankan sambungan sendiri ke sensor, jadi `connect_daq_sensor` tidak pernah meminjam sensor yang telah dibuka oleh GUI. Jangan gunakan sensor yang sama dari GUI dan dari skrip pada masa yang sama. DAQ-U atau DAQ-M yang dipegang oleh GUI biasanya hanya akan gagal dibuka, tetapi DAQ-E menerima sesi kedua: connect skrip mengubah tetapan pemerolehan unit di bawah GUI, dan `disconnect()` menghentikan aliran unit, yang menamatkan paparan langsung GUI dan sebarang rakaman yang sedang dibuat. Tutup sensor dalam GUI terlebih dahulu.

### PTP Grandmaster — `time_sync_status()`

Backend ini menjalankan PTP grandmaster yang menjadi sumber cap masa bagi kamera LATTICE dan unit DAQ-E. Ia dimulakan atas permintaan: `connect_camera()`, `connect_array()`, `connect_daq_sensor()` dan `connect_all()` memulakannya jika ia tidak berjalan — termasuk sambungan yang kemudian gagal, seperti `connect_all()` mencuba DAQ-E yang tidak wujud — dan begitu juga `test_array_sync()` dan setiap panggilan yang membuka kamera untuk satu arahan (`capture_cameras_transient()`, `start_multi_camera_stream()`, panggilan penjajaran, `capture_live_index()`).

- **Di mana ia diumumkan.** Pada penyesuai rangkaian berwayar yang menghadap kamera (termasuk alamat link-local `169.254.x.x`), jika tidak, pada penyesuai laluan lalai jika ia berwayar. Tidak pernah pada Wi-Fi atau penyesuai maya (Tailscale, WSL): tiada kamera atau DAQ-E di sebaliknya. Jika tiada penyesuai sedemikian yang diaktifkan, ia menjalankan pengumuman pada tiada apa-apa (`health` `NO_SOCKETS`) dan mengikat penyesuai yang diaktifkan pada sambungan kamera atau DAQ seterusnya.
- **Selepas sambungan terputus.** Apabila penyesuai kamera kehilangan sambungannya dan mendapatkannya semula (seperti kabel ditarik, suis diganti, penyesuai USB disenaraikan semula), grandmaster membina semula soketnya sendiri, tanpa perlu dihidupkan semula, dan menyemak semula setiap DAQ-E yang bersambung, mengaktifkan semula yang terhenti akibat jurang. Ia juga membina semula apabila setiap kamera dan DAQ-E yang dilayaninya menjadi senyap selama 15 saat.
- **Adakah ia mencapai wayar?** Baca `time_sync_status()` (`GET /api/time-sync/status`). `state` hanya menyatakan bahawa benang grandmaster berjalan; `health` menyatakan sama ada paketnya keluar: `OK` (tiada ralat dikesan), `STOPPED`, `NO_SOCKETS`, `SENDS_FAILING`, `DEGRADED` (sesetengah penyesuai gagal) atau `PEERS_SILENT` (kamera dan DAQ-E yang dilayaninya berhenti menjawab), dengan satu ayat dalam `health_detail`. `announce_sent` / `sync_sent` / `delay_resps_sent` hanya mengira mesej yang terhantar; `announce_send_errors`, `sync_send_errors`, `follow_up_send_errors`, `delay_resp_send_errors`, `last_send_error`, `failing_interfaces` dan `last_delay_req_age_s` menerangkan yang tidak berjaya, dan `rebinds` / `last_rebind` ialah pembinaan semula. `sync_sends_slow` mengira Syncs yang penghantaran mereka melebihi 200 us dan `sync_send_max_us` adalah yang terpanjang; setiap masa asal Follow_Up hanya dikira sehingga 200 us bagi penghantaran mereka. Grandmaster dijalankan dalam prosesnya sendiri (`own_process`, `process_pid`), dengan keutamaan tinggi di Windows (`process_priority`), sehingga backend yang sibuk tidak dapat menangguhkan masa yang dicapnya dan menggerakkan jam setiap kamera sekaligus; jika proses itu tidak dapat dimulakan ia berjalan di dalam backend seperti sebelum ini (`own_process` false).

```python
st = chloros_sdk.time_sync_status()
if st.get("health") not in (None, "OK", "STOPPED"):
    print("PTP:", st["health"], "-", st.get("health_detail"))
    chloros_sdk.restart_time_sync()
```

| Fungsi | Apa fungsinya |
| --- | --- |
| `time_sync_status()` | Keadaan, kesihatan, identiti jam, keutamaan dan pengira mesej grandmaster (di atas). `chloros-cli time-sync status`. |
| `time_sync_peers()` | Hamba PTP yang didengarinya (kamera, DAQ-Es): `peers`, setiap `port_identity`, `last_seen_ip`, `last_delay_req_age_ms`, `delay_req_count`. `time-sync peers`. |
| `time_sync_cameras()` | Setiap `PtpStatus` kamera tatasusunan yang disambungkan, `PtpOffsetFromMaster` dan `PtpMeanPathDelay` (kamera yang firmware-nya tidak mempunyai nod PTP hanya menggunakan sambungan bersiri). `time-sync cameras`. |
| `restart_time_sync()` | Hentikan dan mulakan semula grandmaster; setiap PTP DAQ-E yang dikumpulkan diperiksa semula selepas (`daq_ptp_rechecked`). Menghasilkan `ChlorosConnectError` apabila ia tidak kembali. `time-sync restart`. |
| `set_time_sync_priority(priority1=None, priority2=None)` | Keutamaan PTP-nya (0-255, nilai lebih rendah memenangi pilihan master terbaik), untuk rangkaian dengan master PTP lain. Perlu ia berjalan. `time-sync set-priority`. |

Setiap satu mengambil `backend_url=` dan `timeout=` dan memulakan backend tempatan yang tidak berjalan.

---

## Utiliti Kamera

Apa yang dilakukan `chloros-cli lattice` selain menyambung dan menangkap, sebagai fungsi biasa dalam `chloros_sdk` (modul `chloros_sdk.camera_utils`). Setiap satu memanggil laluan backend yang digunakan oleh arahan CLI, dengan peraturan CLI, jadi kedua-duanya berkelakuan sama. Mereka mengambil **nomboi siri** kamera, bukan sesi, dan kata kunci `backend_url=` / `timeout=` yang sama seperti panggilan smart-connect; backend yang tidak berjalan akan dihidupkan, seperti yang dilakukan oleh `connect_camera()`. Penolakan akan memanggil `ChlorosConnectError` dengan alasan backend tersebut.

```python
import chloros_sdk as c

c.set_camera_color_profile("214701281", profile="natural")
c.set_camera_highlights("214701281", -40)
print(c.camera_firmware_status()["cameras"])
st = c.network_adapters(estimate=True, cameras=4, pixel_format="BayerRG12")
```

| Fungsi | Apa yang dilakukannya |
| --- | --- |
| `camera_color_profile(serial)` / `set_camera_color_profile(serial, profile=, custom_cct_k=)` | Profil warna paparan: `raw`, `linear`, `natural`, `enhanced` atau `custom_temp`, dan suhunya (dibatasi kepada 2000-10000 K; dibaca apabila profil adalah `custom_temp`). |
| `camera_color_adjust(serial)` / `set_camera_color_adjust(serial, saturation=, contrast=, reset=False)` | Memaparkan ketepuan dan kontras, 0–200 % setiap satu (100 = tidak berubah); `reset=True` memulihkan kedua-duanya kepada 100. |
| `camera_highlights(serial)` / `set_camera_highlights(serial, value)` | Tarik piksel paling terang dan paling putih dalam render paparan ke bawah: -100..0 (dipotong ke arah sifar, kemudian dikekang; 0 = mati). |
| `camera_white_balance_once(serial, settle_s=None)` | Imbangan putih sekali sahaja: kamera memancarkan aliran seketika, menyatukan, dan mengembalikan `red_ratio` / `blue_ratio` (`ratios_read` False apabila ia tidak dapat melaporkannya). Ia mengekalkan imbangan putih yang telah diselesaikannya; setiap tetapan lain yang ditulis oleh open kembali seperti asal. |
| `set_camera_white_balance(serial, mode=None, red=None, blue=None)` | `mode="auto"` (terus menerus) atau `"off"`; atau nisbah imbangan tetap `red` / `blue`, yang mematikan auto terlebih dahulu. Perbandingan yang ditolak oleh kamera dinamakan di bawah `errors`. |
| `camera_presets()` / `apply_camera_preset(serial, preset)` | Tetapan kamera, dan apabila salah satu digunakan: kamera mengekalkan tetapan yang dinamakan oleh preset (balasan `settings`, seperti yang dibaca oleh kamera selepas itu, contohnya kadar bingkai yang dijalaninya; `None` untuk medan yang tidak dinamakan oleh pratetap), dan setiap tetapan lain yang ditulis olehnya dikembalikan seperti asal ditemui. `"default"` menetapkan semula kamera ke tetapan kilang asal (tetapan kilang dengan pendedahan automatik bebas, had throughput pautan dan pembetulan kecacatan dimatikan, keseimbangan putih kamera RGB yang dikalibrasi dikekalkan), tidak disimpan ke memori: balasan mempunyai `factory_defaults: True` dan setiap medan `settings`. Dalam `camera_presets()`, `default` ialah `{factory_defaults: True, description}`. |
| `camera_filter_models()` / `camera_filter_model_info(name)` | Model penapis, dan butiran satu model (gelombang, nombor keadaan tindak balas spektral). Model penapis kamera dibaca daripada rentetan modelnya; ia tidak ditetapkan secara manual. |
| `camera_firmware_status(serial=None)` | Setiap kameraperisian: `current_version`, `available_version`, `update_offered`, `needs_minimum_update`, dan `minimum_firmware`. |
| `update_camera_firmware(serial, force=False)` | Flash firmware binaan Chloros ini ke dalam satu kamera: tetapan kilang, flash, but semula, tetapan asas Chloros ditulis semula; penentukuran kamera dikekalkan. Beberapa minit; kamera mesti kekal dikuasakan. `force=True` mem-flash semula kamera semasa. |
| `camera_power_probe(serial)` | nod kuasa dan termal kamera dan suhunya. |
| `sleep_camera(serial)` / `wake_camera(serial)` | Mod tidak aktif berkuasa rendah kamera (ia kekal tidur sehingga diaktifkan semula: had kadar pemindahan pautan dihidupkan (`DeviceLinkThroughputLimitMode` `On`) pada had bawahnya, mod penjimatan kuasa dihidupkan jika kamera mempunyai satu), dengan suhu sebelum / selepas; pembangkitan mengembalikan pautan seperti keadaan semasa tidur (had, modnya, dan kelewatan paket `GevSCPD`, yang dikekalkan pada 0 oleh kamera selagi had diaktifkan; rekod disimpan pada komputer ini, jadi pembangkitan daripada backend kemudian juga akan menemuinya), atau, untuk kamera yang tidak diletakkan ke mod tidur oleh komputer ini tetapi kelihatan seperti sedang tidur (hadnya dihidupkan pada paras lantai), dengan had dimatikan dan `GevSCPD` dibiarkan pada 0 pada 80 (tetapan kilang); had yang ditetapkan di atas paras lantai dibiarkan seperti sedia kala. Hanya keadaan kuasa yang dikekalkan; semua tetapan lain dikembalikan seperti asal. |
| `calibrate_camera_with_target(serial, filter=None, attempts=30, settings=None)` | Rakam bingkai sehingga sasaran penentukuran MAPIR kelihatan, dan padankan koefisien setiap saluran daripada panelnya: `target_id`, `panel_dns`, `coefficients`. |
| `network_adapters(estimate=False, cameras=1, pixel_format="BayerRG8")` | Penyesuai Ethernet komputer ini untuk GigE Vision (MTU, kelajuan pautan, bingkai jumbo) dan mana-mana `issues`; `estimate=True` menambah kadar bingkai dan jalur lebar yang akan diperoleh oleh kamera `cameras`. |
| `fix_network_settings()` | Terapkan tetapan penyesuai GigE Vision pada komputer ini&#x27;penyesuai kamera. Ia menukar tetapan penyesuai dan hanya berfungsi apabila Chloros dijalankan sebagai Pentadbir; jika tidak, ia akan memaparkan log apa yang ditemuinya. |
| `apply_vignette_correction(input_dir, output_dir, lens_model=None)` | Betulkan kesan vignet pada imej dalam satu folder, setiap satu dengan peta medan rata bagi lensa masing-masing: `processed`, `lens_models` (lensa bagi setiap fail telah diperbetulkan), `skipped` (dengan sebab). Tanpa `lens_model`, lensa setiap fail diambil daripada Model EXIF; a `lens_model` digunakan pada fail yang namanya Model tidak menamakan mana-mana kamera, dan fail yang namanya Model bercanggah dengannya akan diabaikan. Fail sahaja. |
| `read_dls(mac, filter=None, camera_serial=None)` | Satu bacaan daripada DLS (penderia cahaya ke bawah Bluetooth): spektrum, iradiasi setiap jalur, dan magnitud jalur melalui tindak balas kamera itu sendiri apabila `camera_serial` diberikan. `discover_daq_sensors(transports=["ble"])` mencari MAC. |
| `probe_system(pixel_format="BayerRG12", discover=True)` / `system_info()` | Perkakasan hos untuk kamera dan konfigurasi yang disyorkan (`report_text`), dan runtime CPU / GPU / PyTorch sistem. |

- **Sesetengah memerlukan kamera untuk mereka sendiri.** `camera_white_balance_once()`, `apply_camera_preset()`, `sleep_camera()` / `wake_camera()`, `calibrate_camera_with_target()` dan `update_camera_firmware()` membuka kamera. Manakala Chloros memegangnya (disambungkan dalam aplikasi, dengan `connect_camera()`, sebagai ahli tatasusunan, atau sedang digunakan oleh arahan lain sekarang, seperti ujian susunan atau tangkapan yang membuka kameranya) backend menolak dengan HTTP 409 `CAMERA_HELD` (dalam kedua-dua `code` dan `error_code`), menamakan cara untuk membebaskannya. Tetapan warna, sorotan, `camera_power_probe()` dan bacaan berfungsi pada kamera yang disambungkan.
- **Warna, sorotan dan imbangan putih adalah untuk kamera RGB.** Pada kamera mono jalur tunggal (M3M), penetap tidak mengubah apa-apa dan mengembalikan `{"skipped": True, "reason": ...}`, seperti yang dilakukan oleh CLI. Ia adalah tetapan paparan: tontonan langsung dan tangkapan peringkat paparan, bukan radiasi atau pantulan.

---

## Susunan Sementara, Profil Penyelarasan &amp; Indeks

Apa yang dilakukan oleh `chloros-cli lattice` tanpa sambungan berterusan, seperti fungsi dalam `chloros_sdk` (modul `chloros_sdk.array_utils`): `continuous`, `multi-capture`, `multi-stream`, `multi-test`, `multi-detect`, `analyze-array`, `align-calibrate`, `align-apply`, `align-stream`, `align-info`, `align-reorder` dan `index`. Setiap satu menghantar permintaan yang dihantar oleh arahan CLI, dengan tetapan lalai CLI.

```python
import chloros_sdk as c

prof = c.calibrate_alignment_profile(["214000535", "214000551", "214000556"],
                                     "D:/rig/alignment_profile.json", method="orb", model="rigid")
print({sn: t["reprojection_error"] for sn, t in prof["transforms"].items()})

c.capture_aligned_multiband("D:/rig/alignment_profile.json", "D:/rig/out")
idx = c.capture_live_index("D:/rig/alignment_profile.json", preset="NDVI",
                           output_dir="D:/rig/ndvi", capture_level="reflectance")

with c.start_multi_camera_stream(["214000535", "214000551"], "D:/rig/stream", fps=5, count=50) as job:
    print(job.wait()["final_stats"])          # waits for the 50 groups, then closes the cameras
```

- **Mereka membuka kamera itu sendiri.** Setiap arahan membuka kameranya, melakukan kerjanya dan menutupnya, jadi ia memerlukan kamera yang Chloros tidak pegang (disambungkan dalam aplikasi, dengan `connect_camera()`, atau sebagai ahli tatasusunan); backend menolak dengan HTTP 409 `CAMERA_HELD` dan memberitahu cara untuk membebaskannya. Pembukaan sementara mengambil masa (kira-kira 40 saat untuk beberapa kamera). Untuk tangkapan berulang, `connect_array()` sekali adalah laluan pantas, dan ia menyimpan setiap jenis eksport di mana tangkapan sementara menyimpan imej debayered. `calibrate_alignment_profile()` adalah pengecualian: pada susunan terhubung kamera-kamera tersebut, ia menyelesaikan daripada bingkai susunan itu sendiri (`used_live_array`).
- **`serials`** ialah kamera yang hendak dibuka, dan yang pertama akan mencetuskan pemicu; `None` membuka setiap kamera di rangkaian.
- **`settings`** adalah satu dict penimbasan tetapan kamera, medannya ialah penanda aras CLI: `exposure_time` (µs), `gain` (dB), `pixel_format`, `width`, `height`, `target_brightness`, `ae_damping`, `ae_upper_limit`, `frame_rate`, `gev_packet_size`, dan `preset` (preset kamera mengikut nama). Eksposur atau penguatan yang diberikan mematikan kawalan automatik itu, seperti yang dilakukan oleh penanda.
- **`gpu`**: `True` memaksa warpan GPU, `False` warpan CPU (`--gpu` / `--no-gpu`); `None` membenarkan backend memilih.
- **`keep_settings`**/**`clear_settings`** (setiap fungsi yang membuka kamera, lalai `False`): pada akhirnya setiap kamera dikembalikan seperti sedia kala; `keep_settings=True` menyimpan tetapan larian ke dalam memori kamera itu sendiri sebaliknya (berjalan bebas), `clear_settings=True` menetapkan semula memori itu kepada tetapan kilang — lihat [Memori kamera](#camera-memory--keep_settings--clear_settings). Balasan (untuk kerja, `stop()`) membawa `camera_memory`.
- **Laluan adalah milik backend**: yang relatif dijadikan mutlak berbanding direktori kerja anda apabila backend berada pada mesin ini. Fungsi fail profil penjajaran (`alignment_profile_info()`, `reorder_alignment_profile()`) membaca fail pada komputer ini, seperti yang dilakukan oleh CLI.

| Fungsi | Apa yang dilakukannya |
| --- | --- |
| `start_camera_continuous(serial, output_dir="output", *, format="tiff", jpeg_quality=95, queue_depth=64, settings=None)` | Merakam satu kamera secara berterusan sehingga dihentikan. Mengembalikan `CliJob`. Bingkai yang dijalankan oleh `tiff` adalah fail `raw` yang ditulis oleh susunan tangkapan kamera itu (satu satah mozek pada sensor&#x27;sendiri, tidak dimampatkan); `png` dan `jpg` menyimpan 8 bit, jadi bingkai-bingkai tersebut adalah bingkai demosaic untuk tontonan. Satu DAQ yang diikat atau dikumpulkan menulis satu `.daq` bagi setiap bingkai dalam larianfolder `daq/` (`daq_files` dalam statistiknya). Setiap bingkai ditulis sekali dengan metadata penuhnya (Model, masa tangkapan, lensa, jalur, kalibrasi pada bingkai mentah); `stop()` kembali sekali apabila setiap bingkai yang telah dicatat telah ditulis (sehingga 25 s), dan hanya kemudian kameranya ditutup. Kamera yang dibiarkan dalam keadaan bersedia untuk pencetus perkakasan (`TriggerMode` `On`) ditetapkan untuk larian bebas (`trigger` dalam balasan permulaan menyatakan apa yang ditemui). Pada hentian, kamera dikembalikan seperti asal — tetapan pencetusnya dan setiap tetapan yang diubah oleh larian — kecuali `keep_settings=True`, yang menyimpan tetapan larian ke dalam memori kamera sendiri (larian bebas), atau `clear_settings=True`, yang menetapkan semula memori itu. Satu larian yang tidak merakam sebarang bingkai akan menamatkan dirinya sendiri selepas 10 s dengan `stop_reason` `no_frames` dan sebab dalam `last_error`, dan `stop()` mengembalikan sebab itu sebagai `error`. Bingkai yang terbuang kerana penulis tidak dapat mengejar dikira, dengan `drop_reason` menyatakan sebabnya. Untuk kamera Chloros memegang, gunakan `CameraSession.capture_repeated()`. `lattice continuous`. |
| `start_multi_camera_stream(serials=None, output_dir="output", *, fps=3.0, count=None, format="tiff", jpeg_quality=95, settings=None)` | Menyampaikan kumpulan bingkai yang diselaraskan pada `fps`, untuk kumpulan `count` atau sehingga dihentikan. Mengembalikan `CliJob` (`start_reply` mempunyai `n_cams` dan wayar `pacing`); statistik akhirnya mengira kumpulan yang dicekap, ditulis dan terlepas, fail yang penulis tidak dapat tulis dan kumpulan yang kamera tidak hantar apa-apa. `lattice multi-stream`. |
| `capture_cameras_transient(serials=None, output_dir="output", *, format="tiff", jpeg_quality=95, settings=None)` | Satu tangkapan diselaraskan, satu imej debayered bagi setiap kamera: `results` (`filepath`, `serial`, `frame_id`) dan `pacing`. Kumpulan yang terlepas kamera menimbulkan, menamakan berapa banyak fail yang disimpan terlebih dahulu. `lattice multi-capture` tanpa sebarang tatasusunan disambungkan. |
| `test_array_sync(serials=None, count=10, ptp_settle_s=None, timeout=None)` | `count` tangkapan diselaras pada pendedahan tetap: `sync_groups`, `master`, satu baris `group_timestamps` bagi setiap pencetus (kamera yang terlepas satu akan tiada dalam barisnya) dan `sync`: offset setiap kamera lain daripada tuan rumah, bagi setiap pencetus (`offsets_us`: bagi setiap kamera `mean_us`, `min_us`, `max_us`, `jitter_us`, `groups`), pada SATU jam (`clock_domain`: `ptp`, atau `device` — PTP dimatikan, jam masa operasi setiap kamera sendiri, jadi offsetnya ialah masa ia dihidupkan dan hanya jitter yang mengukur penyelarasan). Satu pencetus yang capnya menggabungkan jam peranti dan PTP (PTP dimatikan atau masih mengunci) diabaikan dan dikira (`groups_mixed_domain`), begitu juga satu di mana jam PTP masih lebih daripada `agree_within_us` daripada tuan rumah (`groups_clocks_disagree`); `notes` menyatakan demikian. `clock_offset_mean_us` / `clock_offset_jitter_us` adalah purata dan jitter terbesar bagi setiap kamera. `success` adalah palsu, dengan `error`, apabila pencetus terlepas, apabila kurang daripada dua pencetus mempunyai setiap kamera pada satu jam, atau apabila jam tidak pernah sepadan (`sync.problems`); baris-baris dikembalikan dalam apa jua keadaan. Ia memulakan grandmaster PTP jika ia tidak berjalan (`time_sync` mengatakan `grandmaster_running` dan `grandmaster_started_by_this_test`), menghidupkan PTP setiap kamera (seperti yang dilakukan oleh `connect_array()`), dan sebelum pencetus pertama menunggu jam PTP setiap kamera terkunci pada grandmaster dan stabil: `PtpStatus` menjadi Slave pada satu grandmaster, dalam lingkungan 100 us daripadanya dan kekal stabil selama 3 s, dan setiap kamera terkunci sedemikian, tanpa henti, selama 120 saat — kira-kira 2 minit dari kuasa-hidup. Jam yang baru sahaja terkunci boleh tersasar sehingga ~200 us selama kira-kira 2 minit selepas ia membaca sebagai Slave, dan `PtpOffsetFromMaster` pada kamera itu sendiri tidak menampilkannya; 120 saat membawa kamera-kamera ke dalam julat kira-kira 10 µs daripada jam yang telah stabil. Kamera yang masih terkunci sejak ujian sebelumnya yang mengekalkan PTP hidup, seperti yang dilihat oleh backend ini, mengira dari kunci tersebut, jadi ujian sejurus selepas satu lagi tidak menunggu semula; kamera yang dihidupkan oleh ujian, atau yang ditemui dengan PTP dihidupkan tetapi belum pernah dilihat terkunci oleh backend ini (diaktifkan di tempat lain, atau sebelum permulaan semula backend), menunggu selama 120 s, dan dalam susunan, kedua-dua kamera yang baru sahaja terkunci masih menunggu giliran mereka. Kamera yang keluar daripada mod Slave, melepasi 100 us atau mengikuti grandmaster lain untuk 3 bacaan berturut-turut (1.5 s) semasa menunggu memulakan semula kiraannya; satu atau dua bacaan sedemikian direkodkan (`[array-sync] PTP SN:<serial> off lock at <s> s (<n> of 3): <status> <±x> us gm <id> -- locked <s> s, counting on`) dan kiraan diteruskan, dan id grandmaster yang tidak dapat dibaca dianggap tidak diketahui, bukan sebagai perubahan. Setiap kamera memulakan semula kiraan serentak (dalam masa satu saat) adalah masa pergerakan grandmaster, bukan kamera: backendlog mengatakan `[array-sync] PTP grandmaster step: all <n> cameras restarted at <s> s …`, `ptp_lock` menyenaraikannya dalam `grandmaster_steps`, dan satu menunggu yang kemudian tamat bermula kesalahannya dengan `every camera's count restarted together at <s> s -- the PTP grandmaster's time moved under them …`. Had itu tidak dilanjutkan untuknya. `ptp_settle_s` (0-600; `0` untuk tiada menunggu penyelesaian) menetapkan masa penyelesaian untuk pelaksanaan; nilai lain ditolak (HTTP 400). Tempoh menunggu dihadkan kepada masa penyelesaian ditambah 90 s (210 s secara lalai, sekurang-kurangnya 90 s), dan panggilan menunggu backend `timeout` s: secara lalai 240 ditambah masa penyelesaian (360 s), seperti yang dilakukan oleh `chloros-mcp` dan `test_array_sync`. `ptp_lock` mempunyai `converged`, `waited_s`, `timeout_s`, `settle_s`, `locked_s` (berapa lama setiap kamera telah dikunci dan stabil bersama), `found_settled` (kamera-kamera yang telah dikunci untuk masa penetapan apabila ditemui) dan, bagi setiap kamera, `status`, `offsets_ns`, `slave_s` (berapa lama ia telah dikunci; Tiada: tidak dikunci) dan `remaining_s` (Tiada apabila tiada kamera yang akan menghidupkan PTP). Log backend mempunyai satu baris setiap 5 saat dengan keadaan setiap kamera, berapa lama ia telah dikunci dan berapa lama lagi yang tinggal. Setiap kamera dihidupkan dengan PTP dimatikan; dari situ jam akan terkunci dalam kira-kira 10 saat. Selepas itu setiap kamera dikembalikan seperti keadaan asal apabila ujian menemuinya — PTP, pendedahan automatik dan penguatan yang dibiarkan dimatikan sepanjang ujian dijalankan (`ExposureAuto` / `GainAuto`, dan satu manual `ExposureTime` / `Gain`), dan selebihnya, melainkan `keep_settings=True` — jadi kamera yang ditinggalkan dengan PTP dihidupkan (`PtpEnable=True`, atau larian dengan `keep_settings=True`) tetap terkunci, dan setelah backend ini melihat ia terkunci untuk tempoh penetapan, ujian kemudian tidak menungguinya. Jika jam tidak menetap, `success` adalah palsu sebelum sebarang pencetus, dan `error` menyatakan apa yang perlu dilakukan: kamera yang tidak pernah terkunci (`not_slave`) bermaksud grandmaster tidak mencapainya (`time_sync_status()`); manakala yang sentiasa terputus dipanggil dengan berapa lama ia telah terkunci (`remaining_s`); jam masih konvergen (`far`): periksa `time_sync_status()`, dan untuk memastikan ia terkunci sebelum ujian, hidupkan PTP dan biarkan ia hidup (`PtpEnable=True` pada setiap kamera, atau `keep_settings=True`; ujian memulihkan apa yang ditemui, jadi ia kekal hidup), kemudian jalankan. `lattice multi-test`. |
| `detect_array_master(line=None)` | Kamera pada talian penyegerakan dan tuan rumah lalai: `master_serial`, `slave_serials`, `line` diuji. `lattice multi-detect`. |
| `analyze_array_models(camera_models=None, *, width=2048, height=1536, n_active=None, force_tier=None, binning=1, packed="auto", pixel_format=None)` | Betapa pelbagai model kamera ini (nama produk, `"LATT-M3C-L87-FOCN"`) akan menyambung ke rangkaian komputer ini: `tier`, `achievable_fps_max`, bajet wayar (`max_cams_collision_safe`, `oversubscribed`), `cam_caps` dan `fix_suggestions`. Secara lalai model kamera yang ditemui; tiada kamera dibuka. Untuk kamera yang anda ada, dengan tetapan yang akan digunakan oleh sambungan, lihat [`analyze_array_network()`](#network-analysis-helper). `lattice analyze-array`. |
| `calibrate_alignment_profile(serials=None, save_path="alignment_profile.json", *, frames=3, method="orb", model="rigid", reference_serial=None, checkerboard=None, control_points=None, preview=False, vignette=False, roi=None, mask_path=None, rms_threshold_px=None, per_cam_overrides=None, settings=None, ...)` | Menyelesaikan profil penjajaran daripada bingkai langsung dan menyimpannya. `method`: `orb`, `akaze`, `phase`, `checkerboard` (dengan `checkerboard="9x6"`, sudut dalaman) atau `manual` (dengan `control_points={serial: [[x, y], ...]}`, kamera rujukan juga). `model`: `translation`, `rigid`, `affine` atau `homography`. Pilihan yang lebih terperinci ialah bendera CLI: `max_features`, `ratio_threshold`, `matcher` (`bf` / `flann`), `knn_k`, `prefilter` (`none` / `gradient` / `clahe` / `blur` / `hist_match`), `ransac_threshold_px`, `ransac_max_iters`, `ransac_confidence`, `averaging`, `lock_rotation`, `lock_scale`, `lock_axis`; `per_cam_overrides` adalah `{serial: {option: value}}`. `rms_threshold_px` enggan menyimpan penyelesaian yang lebih teruk. Mengembalikan `save_path`, `output_width` / `output_height`, `crop_roi`, `reference_serial`, `transforms` (per kamera `reprojection_error`, `num_matches`) dan `preview_path`. `lattice align-calibrate`. |
| `capture_aligned_multiband(profile_path, output_dir="output", *, serials=None, format="tiff", bit_depth=None, gpu=None, no_crop=False, bands=None, order=None, vignette=False, per_band=False, per_camera=False, interpolation="linear", border_mode="constant", border_value=0, settings=None)` | Satu imej berbilang jalur selari melalui profil: `files` (TIFF berbilang jalur dengan `bands` dan saiznya, ditambah satu bagi setiap jalur dengan `per_band` dan satu bagi setiap kamera dengan `per_camera`) dan `vignette_corrected`. `bands` memilih dan `order` menyusun jalur mengikut nama (senarai atau rentetan koma). `lattice align-apply`. |
| `start_aligned_stream(profile_path, output_dir="output", *, serials=None, fps=3.0, count=None, ...)` | Mengalirkan imej berbilang jalur yang disejajarkan (pilihan warpan yang sama seperti `capture_aligned_multiband()`), untuk `count` atau sehingga dihentikan. Mengembalikan `CliJob`; statistik akhirnya mengira `frames_written` dan mana-mana `frame_groups_incomplete`. `lattice align-stream`. |
| `alignment_profile_info(path)` | Fail profil yang diringkaskan sebagai `align-info` mencetaknya: `name`, `created`, `method`, `reference_serial`, `sensor_resolution`, `output_width` / `output_height`, `crop_roi`, `camera_serials`, `transforms` (per kamera `model`, `reprojection_error`, `num_matches`, `reference`), `camera_models`, `layer_order` (per kamera `band_names`, `wavelengths_nm`, `enabled`); `profile` adalah keseluruhan fail. |
| `reorder_alignment_profile(path, order=None, enable=None, disable=None)` | Ubah fail profil di tempat: `order` ialah nama kumpulan (masing-masing menggerakkan lapisan kameranya ke atas, mengikut susunan itu), `enable` / `disable` adalah nombor siri. Mengembalikan `layer_order` baru. `lattice align-reorder`. |
| `index_catalog()` | Preset indeks (`presets`: setiap `name`, `channels`, `formula`) dan warna `gradients`. `lattice index --list-presets` / `--list-gradients`. |
| `compute_index(input_path, *, preset=None, formula=None, channel_map=None, output=None, capture_level="unknown", output_format="all", colorize=False, gradient="RdYlGn", vmin=None, vmax=None, percentile=None, bg_mode="clip")` | Satu indeks ke atas TIFF berbilang jalur selari: satu `preset` (`NDVI`, `NDRE`, ...) atau `formula` ke atas simbol `channel_map` yang memetakan kepada jalur (`{"NIR": "NIR_850", "Red": 0}`, mengikut nama atau indeks). Satu pratetangsimbol `channel_map` mengabaikan dan mengambil satu jalur fail yang namanya bermula dengannya (`red` -&gt; `Red_660`, `nir` -&gt; `NIR_850_RGN`), dinamakan oleh fail itu sendiri (senarai jalurnya, atau `Chloros:Band{R,G,B}Name` tangkapan/produk Chloros), jadi NDVI pada tangkapan pantulan Chloros tidak memerlukan `channel_map`; dua jalur yang sepadan dengan satu simbol ditolak, bukan diagak. `capture_level` menyatakan apa yang terkandung dalam jalur-jalur tersebut: `reflectance` atau `radiance` menjadikan nilai-nilai itu bersifat kuantitatif; susunan tidak dikalibrasi (`align-apply`) memberikan nilai kualitatif; `"unknown"` membacanya daripada fail, termasuk `Chloros:ProcessingLevel` dalam fail Chloros. Mengembalikan `stats` (bentuk, julat peratusan 1–99, julat LUT, `capture_level` yang digunakan) dan `files` yang ditulis di sebelah input (`<input stem>_<preset>.tif`, `..._LUT.png`) atau pada `output`, satu laluan: dengan sambungan imej, fail-fail dinamakan daripada batangannya (`output="D:/out/ndvi_FRGN.png"` menulis `ndvi_FRGN.tif` dan `ndvi_FRGN_LUT.png`), tanpa sambungan ia adalah awalan (`D:/out/ndvi` -&gt; `ndvi.tif`, `ndvi_LUT.png`); baca laluan daripada `files`. Mereka membawa rekod kamera input (Jenama / Model / siri, GPS, lensa, masa tangkapan dalam UTC, identiti penentukuran tetapi tidak penentukuran DN mentahnya), `Chloros:ProcessingLevel=index`, dan indeks sebagai satu jalur mereka (`XMP-Camera:BandName`, sama seperti TIFF dan LUT PNG). Fail sahaja; tiada kamera. `lattice index --input`. |
| `capture_live_index(profile_path, *, preset=None, formula=None, channel_map=None, serials=None, output_dir="output", output_prefix="index", capture_level="reflectance", save_multiband=False, ...)` | Rakam, kalibrasi (`reflectance` atau `radiance`; `raw` melangkauinya), selaras dan hitung indeks dalam satu langkah; `save_multiband=True` menyimpan susunan terkalibrasi, yang boleh diambil kemudian oleh `compute_index()`. Mengembalikan `stats` (dengan `serials` jalur yang digunakannya) dan `files`. Ia membuka `serials` (yang pertama mencetuskan pemicu), jika tidak, kamera-kamera profil yang kemasukannya `layer_order` diaktifkan, rujukan dahulu (dibuka walaupun jalurnya dilumpuhkan); kamera yang jalurnya dilumpuhkan tidak akan pernah dikalibrasi. `channel_map` hanya diperlukan untuk simbol yang tumpukan tidak dapat jawab, seperti dalam `compute_index()`: simbol preset mengambil satu jalur yang diaktifkan yang namanya bermula dengannya, diperiksa sebelum mana-mana kamera dibuka (jika tiada atau beberapa menjawab, ia ditolak dan dinamakan); `formula` tersuai memerlukan setiap simbol. `index.tif` dan LUT membawa kamera susunan, masa tangkapan (pendedahan kamera rujukan, UTC) dan lensa, dan kedua-duanya menamakan indeks sebagai satu-satunya jalur mereka (`XMP-Camera:BandName`, contohnya. `NDVI`: warna PNG menamakan indeks, bukan saluran warnanya). `lattice index --live`. |

### `CliJob`

Tangan yang memegang tiga aliran yang dikembalikan. Tugas ini dijalankan di backend, yang memiliki kameranya sehingga ia dihentikan.

| Ahli | Keterangan |
| --- | --- |
| `stats(timeout=10.0)` | `running`, `state`, `stats` (bingkai disimpan / ditulis, dibuang, `fps`, `elapsed`, kegagalan penulisan, `stop_reason`; kerja berterusan juga `frames_grabbed` dan `frames_queued` -- setiap bingkai yang dirakam ditulis, diletakkan dalam barisan, dibuang atau gagal -- dan `drop_reason`, `writers` dan `write_ms`, purata masa untuk menulis satu bingkai) dan `output_dir`. `state` ialah `running`, `ended` (ia menghentikan dirinya sendiri dan belum dihentikan lagi), `stopping` (sebuah hentian sedang menulis keluar baratnya: statistik langsung, dan `stopped_by`) atau `finished` (statistik akhirnya, `error` dan `stopped_by` bagi hentian itu). Rekod kerja yang dihentikan disimpan selama 10 minit (maksimum 32 terakhir), untuk setiap klien: skrip lain atau `chloros-mcp` membaca keadaan yang sama dan statistik akhir. Kerja `chloros-cli` yang dimulakan (`lattice continuous`, `multi-stream`, `align-stream`) dihentikan oleh backend apabila CLI berhenti memeriksa selama 30 saat — dibunuh, mesinnya tertidur — dengan `stop_reason` `client_gone` dan `stopped_by` backend; membaca statistiknya dari sini tidak menjaganya tetap hidup. Satu pekerjaan yang dimulakan daripada SDK berjalan sehingga ia dihentikan. Setiap permulaan menghantar `timeout` sebagai tarikh akhir backend (`client_deadline_s`): sebuah kamera yang dibuka selepas ia (satu lagi sedang memulakan) dikembalikan dan ditutup dan tiada kerja dimulakan, dan panggilan membangkitkan `ChlorosConnectError` (`CLIENT_DEADLINE_PASSED`). Kamera yang dipegang oleh sesuatu kerja ditolak kepada setiap pembukaan lain (`connect_camera()`, permulaan lain, tangkapan sementara) dengan HTTP 409 `CAMERA_HELD`, menamakan kerja dan siapa yang memulakannya. |
| `stop(timeout=30.0)` | Hentikannya dan tutup kameranya: `final_stats`, `output_dir`, dan `error` apabila pemberhentian tidak berjalan lancar. Panggilan kedua memulangkan jawapan yang sama, dan begitu juga pemberhentian daripada klien lain: satu dihantar sementara yang pertama masih menulis baris tungguannya menunggunya (sehingga 25 s; selepas itu ia menimbulkan isyarat, dan statistik akhir diikuti pada `stats()`). |
| `wait(timeout=None, poll_s=0.5)` | Tunggu sehingga ia berakhir sendiri (`count`-nya dicapai, cakera penuh, ralat), kemudian `stop()`-nya untuk mengumpul kiraan akhir. `timeout` memanggil `TimeoutError` dengan ia masih berjalan; satu kerja tanpa `count` hanya berakhir apabila dihentikan. |
| `job_id`, `kind`, `output_dir`, `start_reply`, `stopped` | ID dan jenisnya (`continuous`, `multi-stream`, `align-stream`), di mana ia menulis, balasan laluan permulaan, dan sama ada `stop()` telah dijalankan. |

Gunakan ia sebagai pengurus konteks untuk menghentikannya apabila keluar daripada skop.

---

## Folder Projek &amp; Resipi

**Folder projek** ialah folder di mana projek baru dibuat, dikongsi oleh aplikasi Chloros, CLI dan SDK (modul `chloros_sdk.system_utils`; `chloros-cli project-folder`):

```python
print(chloros_sdk.project_folder())                # the configured folder, else the default
chloros_sdk.set_project_folder("D:/Flights")       # created if missing
chloros_sdk.reset_project_folder()                 # -> {"path": <default>, "was_set": True}
```

Folder yang dikonfigurasikan yang tidak wujud lagi akan kembali ke lalai (`Documents/MAPIR/Chloros Projects`), seperti yang dilakukan oleh aplikasi. Laluan relatif yang diberikan kepada `set_project_folder()` diambil daripada direktori kerja anda apabila backend berada pada mesin ini.

**Resipi** ialah senarai tindakan YAML / JSON yang dijalankan oleh `chloros-cli project run PATH RECIPE` terhadap projek yang disimpan. `run_project_recipe()` menjalankannya dengan CLInya sendiri (dipasang dengan SDK), jadi satu resipi berkelakuan sama sama ada dari:

```python
r = chloros_sdk.run_project_recipe("D:/campaign/morning.yaml", "D:/Projects/Field_A")
print(r["ok"], r["actions"])
print(r["log"])                                    # what the CLI would have printed

chloros_sdk.run_project_recipe({"actions": [{"apply": {"name": "FrontLeft", "settings": {"exposure_us": 5000}}},
                                            {"wait": "2s"},
                                            {"capture": {"name": "FrontLeft", "output": "pose_a/", "format": "tiff"}}]},
                               "D:/Projects/Field_A")
```

```python
run_project_recipe(recipe, project_path, *, dry_run=False, echo=False,
                   backend_url="http://127.0.0.1:5000") -> dict
```

- **`recipe`** adalah `.yaml` / `.yml` / fail `.json` atau resipi itu sendiri: satu dict dengan `actions` (`apply`, `capture`, `burst`, `stream`, `sensor`, `wait`), dan secara pilihan `overrides` dan `stop_on_error` (lalai true: jalannya berhenti pada tindakan pertama yang gagal). Tindakan-tindakan adalah [Recipe DSL](cli-reference.md#recipe-dsl) rujukan CLI; `output` relatif diselesaikan terhadap direktori kerja skrip anda.
- **Kembali** `ok` (setiap tindakan berjaya), `actions` (berapa banyak), `log` (keluaran pelari; `echo=True` juga mencetaknya semasa ia berjalan) dan `validation`. Fail resipi yang hilang atau tidak dapat diparsing akan menimbulkan `ChlorosConnectError`.
- **Disemak sebelum ia dijalankan.**`validation` adalah `{"ok", "errors", "warnings", "steps"}`: setiap tindakan mesti tepat satu kata kerja yang diketahui sebagai kuncinya (`{"capture": {"name": ...}}`, bukan `{"action": "capture"}` atau `{"type": "capture"}`) menamakan kamera, tatasusunan atau sensor yang dimiliki oleh projek, daripada jenis yang betul. Resipi dengan ralat menjalankan**tiada** tindakan (`ok` palsu, ralat dalam `log`), apa sahaja yang dikatakan oleh `stop_on_error`. Kunci yang tidak digunakan, dan setiap array atau sensor cahaya yang dikawal oleh resipi yang tidak disambungkan sekarang, adalah `warnings`. Resipi tidak menyambungkan peranti: sambungkan peranti pertama projek (`ChlorosProject.connect_all()`, `chloros-cli project connect`, atau alat MCP `connect_project_devices`). `dry_run=True` tidak menjalankan apa-apa dan mengembalikan `{"recipe": ..., "validation": ...}`.
- Satu resipi dijalankan pada satu masa dalam satu proses. Resipi YAML memerlukan PyYAML.

---

## Automasi Projek — `ChlorosProject`

Projek Chloros yang disimpan adalah satu folder yang mengandungi `cameras.json` + `sensors.json` + `project.json`. `open_project(path, relink=None)` memuatkan manifest (`relink`: folder di mana fail sumber yang hilang bagi projek yang dipindahkan kini berada, digunakan apabila backend memuatkannya -- `process()` -- lihat **Projek yang dipindahkan** di bawah `ChlorosLocal`), dan `connect_all` membawa setiap kamera, tatasusunan dan penderia cahaya yang disimpan dalam talian dengan tetapan yang disimpannya — keadaan perkakasan yang sama seperti yang akan dihasilkan oleh GUI. Penderia cahaya menyambung melalui backend [kolam penderia DAQ](#daq-sensor-sessions), jadi ia memerlukan backend yang sedang berjalan (lihat [`SensorHandle`](#sensorhandle)).

### Contoh Minimum

```python
import chloros_sdk

proj = chloros_sdk.open_project("/home/user/Chloros Projects/Field_A")
report = proj.connect_all(verbose=True)
print(report)  # {'cameras': {...}, 'arrays': {...}, 'sensors': {...}}

# Cameras and arrays are addressable by name OR serial / array_id
cam = proj.cameras["FrontLeft"]
cam.capture("./out", format="tiff", processing="reflectance")

arr = proj.arrays["main_rig"]
arr.capture("./out", format="tiff", processing="reflectance")

# Light sensors are addressable by name OR slot_id
reading = proj.sensors["Sky"].read()   # (spectrum, is_saturated, ...) or None

# One frame from every camera and array, one spectrum from every sensor
proj.capture_all("./out")

proj.disconnect_all()
```

Atau sebagai pengurus konteks:

```python
with chloros_sdk.open_project("/path/to/proj") as proj:
    proj.connect_all()
    proj.arrays["main_rig"].capture("./out", processing="reflectance")
```

### Kaedah `ChlorosProject`

| Kaedah | Deskripsi |
| --- | --- |
| `connect_all(*, cameras=True, arrays=True, sensors=True, verbose=False, align=None, packed=None)` | Menemui + menyambung setiap kamera, tatasusunan dan penderia cahaya yang disimpan. Mengembalikan laporan sambungan bagi setiap kelas (`{"cameras": {...}, "arrays": {...}, "sensors": {...}}`, masing-masing dengan `connected`, `missing` dan `errors`; `arrays` juga membawa `alignment`). Dengan backend yang mendengar pada `127.0.0.1:5000`, kamera dan susunan yang disimpan ditemui dan disambungkan melalui ia. Tanpa backend, proses ini menemui dan mengendalikannya sendiri melalui `lattice_sdk`, yang memerlukan runtime Arena SDK dan OpenCV (`cv2`) — roda SDK tidak memasang kedua-duanya; apabila ia tidak dapat, setiap kamera dan susunan yang disimpan disenaraikan di bawah `errors` dengan sebab, dan `missing` kekal kosong. Sebuah backend yang sedang berjalan tetapi menolak SDK (log keluar: HTTP 401; pelan di bawah Copper: 403) bukanlah &quot;tiada backend&quot;: setiap kamera, tatasusunan dan penderia cahaya yang disimpan disenaraikan di bawah `errors` dengan sebab backend tersebut, dan tiada apa-apa dibuka dalam proses ini. Setiap panggilan menyemak semula backend, jadi `connect_all()` selepas log masuk, atau selepas memulakan Chloros, menyambung ke objek projek yang sama. Susunan yang disimpan yang sudah ada di backend dengan kamera yang sama (GUI, atau `chloros-cli project connect`) dilampirkan seperti sedia ada, tanpa menggunakan semula tetapan simpanannya, dan `disconnect_all()` membiarkannya berjalan. Penderia cahaya sentiasa melalui backend (lihat [`SensorHandle`](#sensorhandle)). Ia tidak pernah memulakan backend. |
| `disconnect_all()` | Bongkar semuanya. Setiap peranti dibebaskan terlebih dahulu; kemudian, jika backend tidak mengesahkan pembebasan kamera, tatasusunan atau penderia cahaya (pembongkaran tamat masa, atau ditolak), satu `ChlorosConnectError` menamakan setiap satu. Peranti yang sudah tiada, atau tiada backend yang berjalan, ia senyap. Susunan atau penderia cahaya yang dibuka oleh klien lain dibiarkan berjalan. `with` exit projek tidak pernah diaktifkan. |
| `capture_all(output_dir=".")` | Satu bingkai daripada setiap kamera + susunan yang disambungkan, dan satu spektrum (`SensorHandle.read()`) daripada setiap penderia cahaya yang disambungkan, diambil secara bergilir (kamera, kemudian susunan, kemudian penderia), bukan pada satu masa. |
| `stream(camera, overlays=False, fps=10.0)` | Penjana yang menghasilkan bingkai BGR `numpy` daripada kamera bernama (atau susunan). `overlays=False` membaca bingkai dengan pemegangs `grab()` pada tahap `debayered`: melalui backend apabila projek disambungkan melalui satu, jika tidak gelung tangkapan `lattice_sdk` langsung (susunan menghasilkan `{serial: frame}` dicts). `overlays=True` merutekan melalui `ChlorosLocal.camera_stream()` → suapan MJPEG `/api/camera/<serial>/stream-annotated` daripada backend, dengan blok `ui.overlay` yang disimpan oleh kamera dipindahkan sebagai parameter pertanyaan. Memerlukan mod backend, OpenCV (`cv2`) dan **kamera berdiri sendiri**: kamera mod langsung memanggil `RuntimeError` (backend tidak boleh mengambil kamera yang dimiliki oleh proses ini) dan satu array memanggil `NotImplementedError` (melapis komposit setiap kamera — menstrimkan ahli mengikut nama). Setara one-shot: `CameraHandle.capture(annotated=True)`. |
| `align_arrays(align=True, verbose=False)` | Jalankan penjajaran pada setiap tatasusunan yang kini disambungkan. |
| `process(mode="parallel", wait=True, progress_callback=None, poll_interval=2.0)` | Jalankan paip kalibrasi / indeks pada imej projek (meliputi `ChlorosLocal.process`; keempat-empat ini adalah kwargs **satu-satunya** yang diterima — `indices=` dan lain-lain. menaikkan `TypeError`; menetapkan indeks melalui `ChlorosLocal.configure()`). Secara malas membina `ChlorosLocal()`, yang secara automatik memulakan backend. |

Atribut:
- `proj.cameras` — `Dict[str, CameraHandle]` berasaskan kunci oleh nama DAN serial.
- `proj.arrays` — `Dict[str, ArrayHandle]` berasaskan nama DAN array_id.
- `proj.sensors` — `Dict[str, SensorHandle]` berasaskan nama DAN slot_id.
- `proj.config` — `project.json["config"]` kamus.

### `CameraHandle`

```python
cam = proj.cameras["FrontLeft"]

# Save a frame to disk (processing-aware)
filepath = cam.capture(
    output_dir="./out",
    format="tiff",
    processing="radiance",           # see the level table below
    apply_calibration=True,          # DSNU + flat + 3x3 unmix + NIST
    apply_white_balance=True,        # DLS-aware WB
    apply_index=False,
    index_expression=None,
)

# In-memory grab (numpy array)
frame = cam.grab(processing="debayered")
frame, header = cam.grab(processing="radiance", with_metadata=True)

# Frame iterator (generator)
for arr in cam.frame_stream(processing="debayered", fps=5, count=100):
    my_analysis(arr)
```

**Tahap pemprosesan.** `capture()`, `grab()`, dan `frame_stream()` semuanya mengambil `processing` yang sama
token, dan rantaian adalah kumulatif — setiap tahap menjalankan semua yang di atasnya:

| Tahap | Keluaran | Nota |
| --- | --- | --- |
| `raw` | Bayer 1-saluran, asli sensor | Tiada demosaik. Tindih tidak tersedia pada tahap ini. |
| `debayered` | 3-saluran warna (**lalai** untuk `CameraHandle` dan setiap `grab()` / `frame_stream()`; `ArrayHandle.capture()`, `capture_smart()` dan `stream()` secara lalai kepada `raw`): BGR dalam memori (`grab()` / `frame_stream()`), disimpan sebagai RGB oleh `capture()` | Dalam mod backend, sebagai tangkapan: offset gelap pek kalibrasi (DSNU) daripada data mentah, kemudian demosaik sedar tepi OpenCV — tiada LUT lineariti, medan rata atau pencampuran semula. Satu-satunya tahap yang berfungsi tanpa mod backend ialah demosaik bilinear biasa pustaka kamera (tiada offset gelap). Fail TIFF yang disimpan adalah RGB fotometrik, jadi terbalikkan saluran bingkai dalam memori (`frame[..., ::-1]`) untuk membandingkannya dengan fail. |
| `radiance` | float32, W/m²/sr/nm, **satu satah bagi setiap jalur** | Rantaian radiometrik penuh: demosaik + pencampuran semula 3×3 (multispektral) + DSNU + pembetulan medan rata + skala NIST, dengan pendedahan × penguatan diasingkan supaya nilai adalah mutlak. |
| `reflectance` | uint16, 32768 = 1.0 (atau uint8, 255 = 1.0 — lihat `bit_depth`), **satu satah bagi setiap jalur** | Radiasi dibahagikan oleh iradiasi menuruni (ρ = π·L/E). Perlu bacaan DLS/DAQ — lihat nota di bawah. |
| `display` | 8-bit sRGB-ish | Render setara GUI: CCM + imbangan putih + gamma melalui profil warna aktif kamera. |

Dalam mod langsung (tanpa backend) hanya `debayered` tersedia: `grab()` pada tahap lain meningkatkan
`NotImplementedError`, `capture()` mengabaikan `processing`, dan `frame_stream()` meningkatkan
`RuntimeError` pada setiap tahap. `reflectance` memerlukan bacaan downwelling yang boleh digunakan — titik akhir bingkai menarik DAQ terkumpul ke dalam slot DLS kamera secara automatik, tetapi tanpa DAQ yang diikat, rantaian menolak keluar reflektansi dan secara jujur mencatat penurunan pangkat dalam metadata yang dikembalikan daripada berdiam diri
menyerah balik produk yang lebih rendah kualitinya.

> **Skala DN reflektansi — jangan hardkodkannya.** Reflektan LATTICE menggunakan `32768` = ρ 1.0 dan mencap
> XMP `Chloros:PixelScale=32768`; reflektan Survey3 menggunakan `65535` = ρ 1.0 dan tidak membawa sebarang
> `Chloros:*` tag. Baca tag tersebut dan bahagikannya. Ia ditakrifkan dalam domain uint16, jadi ia kekal
> `32768` untuk setiap format yang menormalkan semula (TIFF 16-bit, TIFF 8-bit, PNG/JPG 8-bit, 32-bit peratus)
> — normalisasikan dtype yang disimpan kembali ke uint16 terlebih dahulu (×257 daripada 8-bit, ×65535 daripada float). Satu pengecualian ialah fail daripada binaan sebelum 1.3.0: TIFF 8-bit pada masa itu *dipotong*, bukan diskala semula, jadi
> tiada skala yang menerangkannya, dan ia tidak mengandungi tuple `PixelScale` atau MicaSense. Anggap tag yang hilang pada fail pantulan LATTICE sebagai &quot;tiada skala sah&quot;, bukan sebagai lalai.

Tag penentukuran: hanya pada `raw`. Fail `raw` bagi tangkapan mengandungi pek penentukuran kamera: muatan `CalibrationJSON` dan tag yang serasi dengan MicaSense (`BlackLevel`, `RadiometricCalibration`, `Band{R,G,B}RadiometricCalibration`, `MicaSenseVignetting*`), yang menerangkan nilai mentah sensor. Setiap fail aras lain (`debayered`, `display`, `radiance`, `reflectance`, render `index/`) dan setiap bingkai rakaman berterusan / aliran multi-kamera membawa identiti kamera (`XMP-mapir:CameraSerial`, `CameraModel`, `FilterType`, `LensModel`, `CalBundleSha256`, ...) dan rekod bingkai itu sendiri, `XMP-mapir:FrameJSON` (pendedahan, penguatan, suhu, format piksel, masa tangkapan, padanan DAQ), kerana setiap produk `process()` membawa identiti tersebut. Pemprosesan mencari penentukuran fail sedemikian melalui nombor siri dan `CalBundleSha256` dalam cache penentukuran Chloros, memuat turun pakej itu sekali jika komputer ini tidak memilikinya.

#### Fail yang dibuat daripada beberapa kamera

Fail yang dibuat daripada jalur lebih daripada satu kamera — susunan berbilang jalur (`capture_aligned_multiband()`, `start_aligned_stream()`, `capture_live_index(save_multiband=True)`, `Registration.save_multiband()`), indeks langsung yang dibuat daripada beberapa kamera (`index.tif`, `index_LUT.png`), imej `composite/` tangkapan susunan — menyenaraikan setiap kamera yang menyumbang kepadanya:

- **Per-senarai kamera, mengikut susunan jalur** (komposit: lapisan latar belakang, kemudian lapisan hadapan; lapisan indeks, kamera-kamera yang jalurnya berasal daripadanya): `XMP-mapir:SourceCameraSerials`, `SourceCameraModels`, `SourceSensorProfiles`, `SourceProductLines`, `SourceFilterTypes`, `SourceLensModels`, `SourceCalDateUTCs`, `SourceCalBundleSha256s` (pek kalibrasi yang dilalui oleh jalur setiap kamera, jika tidak — satu susunan `capture_aligned_multiband()` / `start_aligned_stream()`, yang jalurnya tidak melalui apa-apa — pek kamera Chloros telah disimpannya, yang dinamakan oleh fail tangkapan kamera), `SourceLightboxCertIds`, `SourceHalogenLampCertIds`, `SourcePackTiers` — senarai selari, item *n* bagi setiap satu menerangkan kamera yang sama; `-` di mana kamera tiada nilai (tiada pek penentukuran, lensa tidak diketahui). Setiap kamera adalah **satu** item: di mana peknya mempunyai beberapa nilai untuk satu medan — sijil lampu halogen pek tersebut — ia digabungkan dengan `;` (`F-1479-1;F-1480-1;F-1481-1`), tidak pernah koma, jadi satu senarai dibaca satu item bagi setiap kamera tidak kira bagaimana ia dicetak.
- **EXIF `SerialNumber`** memegang nombor siri yang sama, disambungkan dengan koma (`214000535,214000551`).
- Ia tidak mengandungi **tiada** identiti kamera tunggal (`XMP-mapir:CameraSerial`, `CalBundleSha256`, …): nama-nama tersebut merujuk kepada satu kamera dan peknya, dan pemprosesan akan menyelesaikan kalibrasi fail daripadanya. Satu indeks yang dikira daripada jalur beberapa kamera (`index.tif`, `index_LUT.png`) mempunyai EXIF `Model` `LATTICE` (rig), bukan model `LATT-…` satu kamera.

Satu fail jalur **satu** kamera — satu TIFF bagi setiap jalur, satu bingkai `capture_aligned_multiband(per_camera=True)`, satu susunan atau indeks langsung bagi satu kamera — membawa tag identiti kamera tersebut sendiri, seperti mana setiap fail tangkapan lain. Susunan `capture_aligned_multiband()`, fail per-band dan per-kamera, dan setiap susunan `start_aligned_stream()` serta fail per-bandnya, membawa masa tangkapan kumpulan mereka (pendedahan kamera rujukan, UTC), dan bingkai `per_camera` kamera mono adalah satu satah, pada `bit_depth` seperti susunan.**Orientasi kamera (terbalik / putar).** Rangka daripada `grab()` / `frame_stream()` sentiasa dalam orientasi asal kamera, pada setiap peringkat. Rakaman yang disimpan mengikut tetapan orientasi kamera (Cermin H / Cermin V / putaran GUI):

- Fail `raw` dan `debayered` adalah tepat seperti apa yang dibaca oleh sensor**, tidak kira tetapan — mozek Bayer yang dipusing atau dibalikkan akan menjadi corak penapis warna yang berbeza, dan peta kalibrasi adalah
  pada grid sensor itu sendiri.
- **Fail `display`, `radiance`, `reflectance` dan fail indeks disusun** — dipusingkan sekali, selepas semua
  matematik penentukuran. Topeng klamp mereka berada pada grid yang sama.
- **Setiap fail tangkapan merekodkan tetapan** dalam XMP: `XMP-Camera:ChlorosFlipHorizontal` /
  `ChlorosFlipVertical` (`true`/`false`), `ChlorosRotation` (0 / 90 / 180 / 270, mengikut arah jam, selepas
  flip) dan `ChlorosOrientationApplied` — `false` pada raw dan debayered, `true` pada produk yang diorientasikan. `ChlorosProcessing` menyenaraikan operasi yang diterapkan pada piksel fail tersebut (`none` apabila
  asli). Ini termasuk setiap bingkai rakaman berterusan (pengaturan kamera sendiri, tidak diterapkan) dan aliran pelbagai-kamera (identiti: bingkai ahlinya asli, seperti rakaman susunan).
- **Pemprosesan projek menerapkan orientasi yang dirakam** kepada produk yang dijadikannya daripada data mentah
  (penentukuran dalam koordinat penderia dahulu, diikuti pemutar selari tatasusunan, dan orientasi akhirnya), supaya ia sepadan dengan produk tangkapan langsung — termasuk produk sandaran yang tidak ditentukur dan diperbetulkan vinyet; produk debayernya kekal asli.
- **Pemapar imej Chloros memaparkan fail mentah atau ter-debayer yang dipusingkan oleh rekodnya** — untuk paparan sahaja,
  fail tersebut tidak diubah; fail tanpa rekod dipaparkan seperti yang disimpan.
- **Sambutan tanpa rekod diproses sebagai asli.** Sebelum 1.3.0, tangkapan satu kamera
  mengubah fail mentah dan ter-debayernya sendiri; Chloros tidak dapat membezakan fail mentah sedemikian daripada yang asli dan
  tidak akan meneka.
- **Tangkapan arai mengekalkan setiap fail ahli dalam keadaan asli secara lalai** dan merekodkan identitinya;
  `ArraySession.capture(orient=True)` menukar produknya (lihat kawalan tangkapan tatasusunan).

#### Keluaran setiap jalur dan susunan campuran

Pada tahap `radiance` dan `reflectance`, sebuah kamera mengembalikan **satu satah bagi setiap jalur kamera itu**— tiga untuk M3C (tiga jalur tidak bercampurnya, susunan saluran BGR), satu untuk M3M (penapis jalur sempit tunggalnya). Rata-rata kamera mono yang direplikasi digabungkan, jadi satu**campuran
Susunan M3C/M3M menghasilkan bentuk susunan yang berbeza bagi setiap siri**. Baca kunci `bands` daripada metadata bingkai untuk mengetahui apa setiap satah itu; jangan andaikannya daripada kiraan saluran:

```python
for serial, (arr, hdr) in arr_handle.grab(processing="reflectance",
                                          with_metadata=True).items():
    print(serial, arr.shape, hdr["bands"])   # ('…', (1536, 2048, 3), ['nir','green','red'])
                                             # ('…', (1536, 2048, 1), ['F685'])
    rho = arr.astype("float32") / hdr["pixel_scale"]
```

Sentiasa semak `hdr["processing_level"] == "reflectance"` sebelum menggunakan nilai-nilai tersebut. Penurunan pangkat (tiada bacaan DAQ, tiada cal pack, susun atur jalur tidak diketahui) dilaporkan dalam `processing_level` dan `radiometric_refused` dan bukannya kenaikan pangkat — susunan yang anda peroleh kembali masih merupakan bingkai yang sah, hanya
bukan produk yang anda minta. Rakaman yang disimpan yang telah diturunkan pangkat membawa kod yang sama dalam XMP
seperti `Chloros:RadiometricRefused` (contohnya `no-band-layout`, `no-dls-snapshot`).

#### Satu pencetus bagi setiap bacaan — `group_id`

**Ahli-ahli tatasusunan dilayan daripada kumpulan bingkai selaras tatasusunan, bukan daripada tampan tangkapan setiap kamera sendiri.** Kamera-kamera dalam tatasusunan yang diselaraskan perkakasan akan terdedah serentak, tetapi N bacaan bagi setiap kamera tidak *mengambil sampel* serentak — membaca bingkai bertampan terkini setiap kamera merentasi pencetus
batas kumpulan, dan satu bingkai yang terbuang menyahselaraskan set tersebut tanpa disedari.

Menghidangkan daripada kumpulan adalah perlu tetapi tidak mencukupi sendiri: setiap permintaan masih
menangkap *kumpulan terkini* pada saatnya sendiri, dan gelung grab boleh menerbitkan di antaranya.
Diukur pada susunan 4-kamera secara langsung, **12 daripada 20 bacaan selari merentasi dua kumpulan** — sentiasa
tepat bersebelahan. Jadi bacaan itu **dipin**:

1. Setiap bingkai membawa `group_id`, pengira penerbitan monotonic bagi susunan tersebut.
2. `grab()` membaca master (atau, jika master tidak dapat dilayan, kamera seterusnya yang boleh),
   kemudian mem-pin kamera-kamera yang tinggal pada id kumpulan yang dilandasinya (`?group_id=N` pada endpoint HTTP
  ).
3. Backend menyimpan kumpulan terkini sekurang-kurangnya setengah saat, jadi sibling yang sudah
   ditinggalkan oleh siaran kemudian masih dilayan untuk pencetus itu — cukup lama untuk setiap kamera
   ekspor reflektansi melakukan perjalanan pulang pergi walaupun pada susunan sensor penuh 9 kamera. Pin yang backend tidak lagi memegangnya ditolak dengan `409` dan `group_aged_out: true` daripada dijawab secara senyap
   dengan trigger yang berbeza — `grab()` membaca semula set tersebut.

Diukur semula dengan pengepinan: **20 daripada 20 bacaan memulangkan satu pencetus.** `grab()` melakukan ini untuk anda;
anda hanya memerlukan `group_id=` apabila memacu hujung akhir HTTP secara langsung.

- Kamera yang tidak dapat dilayan di kumpulan terpaut — hilang daripadanya, atau menolak permintaan
  (`band` yang tidak dimilikinya, tahap yang tidak dapat dihasilkannya) — adalah **dihapuskan daripada pada diisi semula**
  daripada pencetus yang berbeza, dan kamera-kamera lain masih dikembalikan.
- Dikt `grab()` yang dikembalikan adalah `GrabResult` (sebuah subkelas `dict` biasa;
  `from chloros_sdk import GrabResult`): `.errors` memetakan setiap
  serial yang diabaikan kepada pengecualian yang dibangkitkan semasa pembacaan, dan `.group_id` adalah kumpulan kongsi. `.errors` kosong bermaksud setiap kamera membuat kumpulan ini.
- Kamera berdiri sendiri tidak mempunyai kumpulan, melaporkan `group_id: None`, dan **menolak** pin `group_id=`
  daripada mengabaikannya.

```python
frames = arr_handle.grab(processing="reflectance")
for serial, err in frames.errors.items():
    print(f"SN:{serial} omitted: {err}")       # e.g. status=400 ... band 'F685' not in [...]

frames = arr_handle.grab(processing="reflectance", with_metadata=True)
ids = {hdr["group_id"] for _a, hdr in frames.values()}
assert len(ids) == 1, f"straddled triggers: {ids}"     # belt and braces
```

Pengira yang sama diterbitkan pada **setiap aliran MJPEG bagi setiap kamera** dalam anggota tatasusunan — pratonton (`/api/camera/stream/<serial>`), pratonton beranotasi (`/stream-annotated`) dan
aliran pantulan (`/stream-reflectance`) — sebagai `X-Chloros-Group-Id` pada setiap bahagian, di samping `X-Chloros-Frame-Id` dan `X-Chloros-Timestamp-Ns`. Aliran adalah berterusan dan bukannya pada masa tertentu, jadi ia dipadankan dengan mengumpul bahagian dan mengelompokkan mengikut penanda aras itu — tiada pin untuk ditetapkan. Kamera berdiri sendiri tidak mempunyai kumpulan. Lihat
[Aliran MJPEG per-kamera](#per-camera-mjpeg-streams-no-rtsp).

**`frame_stream()` tidak pernah senyap.**Satu tick yang gagal atas sebab yang boleh diselesaikan sendiri akan dilangkau — aliran kamera tidak menghasilkan apa-apa untuknya, manakala aliran tatasusunan menghasilkan kamera-kamera yang memberi maklum balas (mungkin tiada). Sebab-sebab tersebut ialah: tiada bingkai lagi; utas ambil kamera terhenti selama satu atau dua saat (`404 not streaming`, yang dijawab oleh backend setiap kali kamera lain menyambung atau memutuskan sambungan, atau perubahan tetapan mengimbangi semula kumpulan); permulaan semula backend atau `5xx`; penjajaran masih dalam proses penstabilan (`409 … retry shortly`); dan penolakan pantulan kerana DAQ belum mempunyai bacaan lagi (`no-dls-snapshot`, bacaan lapuk atau kosong). Penolakan pada tick seterusnya hanya akan berulang — satu `band`, `bit_depth=8` yang tidak diketahui pada radiasi, satu cal pack tanpa skala radiasi, penolakan pantulan dengan**tiada DAQ langsung** (tiada yang diikat pada kamera atau larinya, tiada yang disambungkan ke
backend: `dls_source` penolakan adalah `"none"`), mana-mana `4xx` lain —
menaikkan `chloros_sdk.FrameFetchError` (sebuah `RuntimeError`, yang membawa `.status` dan `.body`) sekaligus. Begitu juga dengan **60 saat** tick yang dilangkau tanpa sebarang bingkai di antaranya, apa jua `fps` — cukup panjang untuk menampung penyusunan penjajaran dan permulaan semula backend, cukup pendek supaya kamera yang
benar-benar hilang menamatkan aliran. Ralat menyerah kalah itu juga merupakan `FrameFetchError`, untuk kamera seperti untuk
susunan, dengan pengecualian pada bacaan terakhir sendiri pada `.errors` (dan, untuk kamera, sebagai `__cause__`). Untuk
sebarang susunan, &quot;ditolak&quot; bermaksud setiap kamera telah menolak; pengecualian `.errors` menyimpan sebab bagi setiap kamera.

**Susunan `frame_stream()` menghasilkan setiap pencetus sekali sahaja** (sama ada `ArrayHandle` dan `ArraySession`). Satu tick yang set terbarunya adalah pencetus yang telah ia hasilkan menunggu pencetus seterusnya bukannya menghasilkan set yang sama sekali lagi, jadi aliran berjalan pada kadar yang lebih perlahan antara `fps` dan bingkai array itu sendiri
 kadar, dan set berturut-turut tidak pernah berkongsi `group_id`. Susunan yang berhenti menerbitkan meningkatkan
`FrameFetchError` (&quot;tiada pencetus baru selama 60 s&quot;) daripada mengulangi set terakhirnya. Tanpa ini, aliran yang diminta lebih banyak daripada yang diterbitkan oleh susunan — atau dimulakan sejurus selepas sambungan — akan memberikan ulangan: dalam satu larian yang diukur, 20 set mengandungi 11 id kumpulan berbeza.

```python
from chloros_sdk import FrameFetchError

try:
    for arr in cam.frame_stream(processing="radiance", bit_depth=8):
        my_analysis(arr)
except FrameFetchError as exc:
    print(exc.status, exc.body)        # the HTTP status and the backend's reason
```

#### Mengeksport satu kumpulan — `band`

`grab()` / `frame_stream()` menggunakan `band="<name>"` untuk mengeksport hanya salah satu jalur kamera sebagai satu susunan saluran tunggal `(H, W, 1)` — bentuk yang sama seperti yang dihasilkan oleh kamera mono, jadi keluaran satu jalur diproses dengan cara yang sama sama ada ia berasal daripada M3M atau daripada memisahkan M3C. Jalur merah FRGN:

```python
red = cam.grab(processing="reflectance", band="red")        # (1536, 2048, 1)
```

Ejaan yang diterima ialah nama susun atur penuh (`Red_660`, `NIR_850`, `Green_550`) atau kepala ringkasnya (`red`, `nir`, `green`), tidak sensitif huruf besar/kecil. Nama yang tidak diketahui akan ditolak dengan senarai nama yang tersedia.

> **Pemilihan adalah berdasarkan nama, bukan nombor saluran — secara sengaja.** &quot;Saluran 1&quot; ialah `Green_550` dalam susunan saluran BGR tatasusunan tetapi *merah* dalam kod penapissusunan huruf (`RGN`), dan kedua-duanya tidak sepadan. Memilih yang salah menghasilkan bingkai yang munasabah tetapi daripada jalur yang salah, yang tidak dapat dikesan oleh apa-apa di hiliran. Tiada indeks numerik untuk sebab itu.

#### Penyelarasan aliran — `align`

`align=True` memutar bingkai ke dalam bingkai rujukan biasa bagi tatalayarannya. Setiap kamera diputar melalui resolusi profil dan ROI potongan **yang sama**, sehingga aliran bagi setiap kamera yang dibuka secara berasingan akan keluar**terdaftar secara bersama-sama** — anda tidak perlu memintanya bersama-sama:

```python
frames = arr_handle.grab(processing="reflectance", align=True)   # {serial: array}
# or one MJPEG stream per camera, each co-registered on its own:
#   GET /api/camera/<serial>/stream-reflectance?align=1
```

Keperluan dan tingkah laku:

- Kamera mesti berada dalam susunan **disegerakkan perkakasan** (kabel penyegerakan M8) yang mempunyai profil penjajaran
  — pendaftaran bersama menganggap ahli-ahli diekspos serentak, yang mana perisian atau
  susunan larian bebas tidak menyediakan. Ditolak dengan `409` sebaliknya.
- **Ditolak, tidak pernah tidak selari secara senyap.** Laluan tangkapan mungkin menyimpan imej yang tidak selari dengan amaran kerana fail mengekalkan tag asal-usulnya dan boleh didaftar semula kemudian; siaran langsung tidak mempunyai peluang kedua seperti itu, jadi profil yang hilang adalah ralat dan bukannya pilihan lalai.
- **Meminta penjajaran memulakannya.** Meminta `align` pada satu susunan tanpa profil memulakan
penentukuran dan mengembalikan `409` dengan apa yang sedang dianti (contohnya *&quot;menunggu pendedahan untuk
  menstabil pada 214000551 (1/3)&quot;*) — cuba semula dan ia berjaya, biasanya dalam beberapa puluh saat.
  Ini penting secara tanpa kepala: penjajaran automatik masa sambungan menyerah selepas 30 saat, dan setiap percubaan
  dalam tetingkap itu diabaikan sementara smart-AE masih menstabilkan, jadi satu susunan yang pendedahannya
  menyatu perlahan-lahan dahulu akan berakhir tanpa profil untuk keseluruhan sesi dan tiada cara untuk mendapatkan
  satu tanpa membuka pandangan gabungan desktop. `alignment_status` juga melaporkan
  `ae_align_holdouts`, menamakan kamera yang masih menstabil dan kemajuan mereka.
- Berikan setiap aliran satu susunan **`max_h`**yang**sama**, atau potongan mereka akan mendarat pada grid piksel yang berbeza.
- Penyelarasan digunakan *sebelum* pengekodan kedalaman bit, jadi warp menginterpolasi nilai ketepatan penuh.

#### `bit_depth` — menukar ketepatan untuk jalur lebar

`grab()` dan `frame_stream()` menerima `bit_depth=16` (lalai) atau `bit_depth=8`:

| `bit_depth` | Pengekodan pantulan | Kos |
| --- | --- | --- |
| `16` | uint16, 32768 = ρ 1.0, ruang lebihan sehingga ρ 2.0 | Ketepatan penuh. |
| `8` | uint8 merangkumi `0..refl_max` (ρ lalai 1.0) | Separuh bait; ~0.004 ρ kuantizasi pada had atas lalai. Nilai di atas had atas akan dipotong — lihat di bawah. |

##### Siling 8-bit — `refl_max`

`refl_max` menetapkan ρ julat 8-bit yang diliputi, jadi `pixel_scale = 255 / refl_max` dan ρ masih `pixel / pixel_scale`. `grab()` dan `frame_stream()` mengambilnya sebagai kata kunci pada kedua-dua kamera dan
penangan tatasusunan (`?refl_max=` pada hujung HTTP). Tingkatkannya untuk mendapatkan ruang tambahan bagi sorotan cermin, pada langkah yang lebih kasar secara berkadar:

```python
frame = cam.grab(processing="reflectance", bit_depth=8, refl_max=1.5)   # 0..1.5 in 0..255
```

| `refl_max` | `pixel_scale` | langkah |
| --- | --- | --- |
| `1.0` (lalai) | 255 | 0.0039 ρ |
| `1.5` | 170 | 0.0059 ρ |
| `2.0` | 127.5 | 0.0078 ρ |

> **Pendedahan automatik tidak dapat membetulkan klip 8-bit, dan menggunakannya untuk mencuba menjadikan data lebih teruk.**
> ρ = π·L/E dengan pendedahan dan penguatan dibahagikan keluar, jadi ρ adalah sifat adegan, bukan
> tetapan kamera — diukur pada susunan langsung, mengurangkan pendedahan kepada separuh memindahkan min ρ sebanyak 1.3%. Menaikkan
> pendedahan untuk &quot;menyesuaikan&quot; julat sebaliknya memacu piksel ke dalam tepu **sensor**, yang
> menganggarkan L secara berkurangan dan memiringkan ρ ke **bawah** tanpa sebarang ralat diangkat (diukur: ρ maks 1.203 → 0.985 pada
> pendedahan 2×, min ρ −23%). Pilih julat dengan `refl_max`; memastikan sensor tidak memasuki
> tepu adalah fungsi AE sedia ada, dan itu melindungi *kesahihan* ρ, bukannya julatnya.

**Baca `reflectance_clipped_fraction`, bukan bendera.** `reflectance_clipped_at_1_0` terpicu pada satu piksel — pada pemandangan sebenar ia melaporkan `True` manakala pecahan adalah `0.000001`, satu piksel
dalam sejuta. Pecahan itulah yang memberitahu anda sama ada perlu menaikkan siling.

**`bit_depth=8` hanya untuk pantulan.** Memintanya untuk `radiance` akan ditolak dan bukannya dipenuhi: reflektansi mempunyai julat tetap 0..1 yang kekal selepas pengekodan 8-bit, manakala radiasi tiada julat tetap, jadi memasukkannya ke dalam 8 bit memerlukan regangan persentil bagi setiap bingkai yang pemetaanannya berubah pada setiap bingkai — satu gambar, bukan ukuran.

#### Aliran pantulan langsung (MJPEG)

`GET /api/camera/<serial>/stream-reflectance` menyediakan pantulan 8-bit setiap jalur sebagai
`multipart/x-mixed-replace` — satu M3C sebagai JPEG 3-saluran, satu M3M sebagai skala kelabu. Parameter pertanyaan:
`fps` (lalai 10), `jpeg_q` (lalai 95), `max_h` (0 = asli), `band` (satu jalur bernama, seperti di atas), `align` (`1` untuk mendaftar bersama — peraturan yang sama seperti `align` dalam SDK), `refl_max` (had penyandian 8-bit, seperti di atas). Setiap bahagian membawa
`X-Chloros-Group-Id`, `X-Chloros-Frame-Id`, `X-Chloros-Timestamp-Ns`, `X-Chloros-Bands`,
`X-Chloros-Pixel-Scale`, `X-Chloros-Aligned` dan `X-Chloros-Reflectance-Clipped`.
**Bahagian daripada kamera yang berbeza yang membawa `X-Chloros-Group-Id` yang sama adalah pencetus yang sama** —
inilah cara beberapa aliran bagi setiap kamera daripada satu susunan disusun semula, dan ia dijamin
di pihak pelayan kerana ahli-ahli susunan dihidangkan daripada kumpulan bingkai yang diselaraskan (lihat
[Satu pencetus bagi setiap bacaan](#one-trigger-per-read--group_id)). Ia akan mengembalikan `409` apabila pantulan tidak tersedia pada masa penyambungan dan akan menamatkan aliran jika ia menjadi tidak tersedia di tengah jalan, jadi aliran yang terhenti bermaksud &quot;tidak lagi ada pantulan&quot; dan bukannya &quot;masih baik, cuma statik&quot;.

> **JPEG bersifat kehilangan — piksel-piksel ini adalah satu ukuran.** Ralat pemampatan menjejaskan terus nilai pantulan dan paling teruk di tepi, iaitu tempat sempadan bahan berada. Kualiti secara lalai adalah 95 atas sebab itu. Jika anda memerlukan nombor yang utuh, gunakan
> `grab(processing="reflectance")` / `frame_stream(...)`, yang memulangkan produk yang sama tanpa
> codec dalam laluan (dan `bit_depth=8` di sana jika kos wayar, bukan codec, adalah kekangan).

> **EXIF dibawa sehingga ke eksport.** `process()` menyalin blok GPS tangkapan sumber
> **dan ExifIFD-nya** ke setiap produk, jadi eksport membawa `FocalLength`, `FNumber`,
> `ExposureTime`, `ISO`, `DateTimeOriginal` dan `CameraSerialNumber` serta
> georujukan. `FocalLength` adalah apa yang digunakan oleh Pix4D untuk menyelesaikan jarak sampel tanah — tanpanya
> pembinaan semula kembali kepada skala yang sangat salah (sebuah kes yang diukur menukar tapak 411 m
> kepada 47.8 km). Salinan ini sengaja bukan `-all:all`: tag struktur IFD0 rosak
> keluaran LATTICE, dan `ExifImageWidth`/`Height` dikecualikan kerana ia menerangkan tangkapan sumber dan bukannya raster yang dieksport.

Sub-penanda peringkat tangkapan (terpakai pada tahap radiometrik — `radiance`, `reflectance`, `display`):

| Bendera | Lalai | Maksud |
| --- | --- | --- |
| `apply_calibration` | `True` | DSNU + flat-field + 3x3 unmix + skala radiometrik NIST. |
| `apply_white_balance` | `True` | LUT WB. Sedar DLS apabila DAQ diikat pada kamera. |
| `apply_index` | `False` | Penilaian indeks vegetasi. |
| `index_expression` | `None` | Gantikan formula. Bukan-kosong → secara automatik mengaktifkan indeks. |
| `annotated` | `False` | Tindih hiasan GUI (zebra/grid/peaking). Tidak tersedia untuk `raw`. |

### `ArrayHandle`

```python
arr = proj.arrays["main_rig"]

# Single synced capture group
files = arr.capture("./out", format="tiff", processing="reflectance")
# → {"213800234": "/path/to/x.tif", "214000533": "/path/to/y.tif", ...}

# Multi-level: each serial's value becomes an ordered LIST, not a str
files = arr.capture("./out", processing="all")
# → {"213800234": ["/raw.tif", "/debayered.tif", ...], "combined": "/idx.tif"}

# Smart capture (wait for AE to settle)
result = arr.capture_smart(
    "./out", processing="reflectance",
    settle_timeout_s=5.0,
    stability_window_s=1.5,
    exposure_tolerance_pct=5.0,
)
print(result["frames"], result["settle"])

# In-memory grab: {serial: numpy array}
frames = arr.grab(processing="debayered")
frames = arr.grab(processing="radiance", with_metadata=True)

# Stream-to-disk loop
arr.stream(count=60, output_dir="./stream", fps=5, processing="raw")

# Frame-iterator (tolerates per-cam drops; great for downstream analysis pipelines)
for frames in arr.frame_stream(processing="radiance", fps=5, count=100):
    if "213800234" in frames:
        my_analysis_pipeline(frames["213800234"])

# Preview iterator (live MJPEG-equivalent; tolerates partial cycles)
counts = arr.preview_stream("./preview", fps=3.0, duration=30.0)
print(counts)  # frames written per serial
```

> **Nilai pulangan adalah `chloros_sdk.CapturePaths`, tipe `CapturePathMap`, bukan `Dict[str, str]`.**
> `chloros_sdk.CapturePathMap` adalah `Dict[str, Union[str, List[str]]]`: satu aras tunggal
> `processing` memberikan setiap siri satu laluan, manakala `processing="all"` memberikannya
> **senarai teratur** bagi setiap produk yang disimpan untuk kamera tersebut. `ArrayHandle.capture()` mengambil
> tiada `levels=`, dan ia mengabaikan hujah kata kunci yang tidak dikenali (`levels`, `daq`, `render_index`, `force_daq`), jadi hantarkan hanya yang ditunjukkan di sini (`aligned=False` menyimpan fail asli, seperti pada `ArraySession.capture()`); gunakan `ArraySession.capture()` untuk kawalan tersebut. Komposit gabungan langsung, jika ia sedang penstriman, tiba di bawah ekstra
> kunci `"combined"` daripada di bawah serial. Kod yang menganggap `str` akan berhenti pada bentuk senarai tanpa sebarang pemeriksa jenis membantah — anotasi itu mengatakan `Dict[str, str]`
> untuk seketika selepas bentuk senarai dikeluarkan, itulah sebabnya alias itu wujud. Normalkan
> apabila anda mahukan bentuk rata:
>
> ```python
> paths = arr.capture(processing="all")
> flat = [p for v in paths.values()
>         for p in (v if isinstance(v, list) else [v])]
> ```

> **Apa yang tidak boleh ditulis: `.failed`.** Peta `capture()` yang dikembalikan adalah
> `chloros_sdk.CapturePaths` — subkelas `dict` biasa, jadi semua di atas berfungsi tanpa perubahan — yang juga membawa `.failed`: fail-fail yang backend tidak dapat tulis, entri yang sama seperti [`CaptureResult.failed`](#files-that-could-not-be-written--captureresultfailed)
> (`serial`, `level`, `error`, dan `disk_full: True` pada pemacu yang penuh). Satu tangkapan sebahagian masih akan mengembalikan setiap laluan yang telah ditulis; pada pemacu yang penuh, peta itu kosong dan `.failed` menerangkan sebabnya. `.skipped` menyenaraikan langganan bagi setiap kamera dan
> `.group_seq` kumpulan yang diselaraskan, dan `.alignment` / `.oriented` adalah backend, seperti pada `CaptureResult`. `capture_smart()` memulangkan mereka sebagai
> `result["frames"].failed` dan `result["failed"]`.
>
> ```python
> paths = arr.capture("./out", processing="all")
> if any(f.get("disk_full") for f in paths.failed):
>     raise SystemExit("drive full -- free space and capture again")
> ```

### Penyelarasan Susunan

`ArrayHandle` mendedahkan keseluruhan permukaan penyelarasan. Profil secara lalai adalah untuk sesi sahaja — panggil `export_alignment()` secara eksplisit untuk mengekalkan.

```python
from chloros_sdk import AlignmentSpec

arr = proj.arrays["main_rig"]

# Defaults: ORB / affine / one synced snapshot — same as the GUI's auto-cal
result = arr.calibrate_alignment()
print(result["profile"]["rms_residual_px"])

# Custom spec for tough scenes (low-contrast canopy)
spec = AlignmentSpec(
    method="feature_orb",         # feature_orb / feature_akaze / phase_correlation / checkerboard / manual
    model="rigid",                # translation / rigid / affine / homography
    num_frames=5,
    max_features=8000,
    ratio_threshold=0.7,
    ransac_threshold_px=2.0,
    min_matches=30,
    max_reproj_err_px=2.0,
)
arr.calibrate_alignment(spec)

# Or tweak one knob at a time
arr.calibrate_alignment(num_frames=3, model="affine")

# Manual control points: each camera's pixel coordinates of the same
# features, the reference camera's included (>= 2 per camera). Every one
# of num_frames is solved from these points.
arr.calibrate_alignment(method="manual", model="rigid", control_points={
    "214701173": [[412, 300], [1630, 310], [1020, 1180]],   # reference (master)
    "214701292": [[418, 297], [1637, 306], [1027, 1177]],
})

# Inspect / manipulate
status = arr.alignment_status()
arr.tweak_alignment("214701292", dx=2.5, dy=-1.0, rotation_deg=0.0, scale=1.0)
arr.export_alignment("/tmp/main_rig_alignment.json")
arr.import_alignment("/tmp/main_rig_alignment.json", validate=True)
arr.clear_alignment()
```

#### Penyelarasan Masa Penyambungan

`connect_all(align=...)` boleh menyelararas secara automatik setiap tatasusunan semasa penyambungan:

```python
# Align every array with defaults
proj.connect_all(align=True)

# Per-array control
proj.connect_all(align={
    "main_rig": AlignmentSpec(num_frames=5, model="affine"),
    "side_rig": True,             # use defaults
    "verify_rig": False,          # skip
})
```

Beralih kepada `project.json["config"]["auto_align_on_connect"]` apabila tidak dinyatakan.

### `SensorHandle`

Penderia cahaya yang disimpan (DAQ-U, DAQ-M atau DAQ-E), dibuka oleh `connect_all()` dalam backend [Kolam penderia DAQ](#daq-sensor-sessions) — kolam yang sama digunakan oleh `connect_daq_sensor()` dan `chloros-cli daq pool-connect`.

```python
reading = proj.sensors["Sky"].read()
if reading is not None:
    spectrum, is_saturated, integration_time, x, y, z = reading
```

- `read()` menunggu spektrum **seterusnya** penderia — satu yang sudah dibufferkan apabila anda memanggil does not count — dan memulangkan `(spectrum, is_saturated, integration_time, x, y, z)`, atau `None` jika tiada yang tiba dalam masa 5 s. Aliran yang ditangguhkan disambung semula. `spectrum` berada dalam W/m²/nm apabila penderia dikalibrasi.
- `.sensor` ialah [`DAQSensorSession`](#daq-sensor-sessions), untuk semua perkara lain: `latest(n=...)`, `status()`, `record_start()` / `record_stop()`.
- `.slot_id`, `.model`, `.serial`, `.name` dan `.settings` adalah entri yang disimpan daripada `sensors.json`.

**Apa yang dipulihkan oleh `connect_all`.** Setiap sensor dibuka pada alamat simpanannya — port COM, alamat Bluetooth atau hos DAQ-E — dan, apabila projek merekodkan ID sensor, ia mesti menjadi sensor itu: unit berbeza di alamat itu ditolak, tidak disambungkan. Masa integrasi, purata bingkai dan pendedahan automatik yang disimpannya diterapkan, serta pembetulan cap:

| Profil cap disimpan | Disambungkan dengan |
| --- | --- |
| Profil cap (contohnya `fov_30`) | Profil itu. |
| Tiada dipilih | `sunshine_cosine`, lalai sambungan, direkodkan sebagai diandaikan (`cap_id_source=auto_default`), jadi pemprosesan memberi amaran. Penderia dihantar tanpa penutup: pilih penutup yang dipasang. |
| `none` — dipilih sebagai **Tiada (sensor kosong)** dalam GUI | Tiada pembetulan topi. |

DAQ-E-S tidak mengambil profil topi, apa pun yang disimpan: penyebarnya adalah sebahagian daripada penentukuran (lihat [DAQ-E-S](#daq-e-s--fixed-diffuser-and-tilt-sensor)). Projek yang disimpan oleh Chloros sebelum ini merekodkan sensor kosong sebagai tiada pilihan, jadi ia disambung semula dengan `sunshine_cosine`; pilih **None (bare sensor)** dalam GUI sekali lagi dan simpan projek tersebut.

Sensor yang sudah dibuka oleh klien lain dalam kolam (CLI, skrip lain) disambungkan seperti sedia ada, tanpa menggunakan semula tetapan, dan `disconnect_all()` membiarkannya terus berjalan; sensor `connect_all()` yang dibuka akan dilepaskan. Seperti pada `connect_daq_sensor()`, GUI Chloros **tidak** termasuk dalam kolam: tutup sensor dalam GUI sebelum menyambungkan projek daripada skrip.**Keperluan dan laporan.** Penderia cahaya memerlukan backend Chloros yang sedang berjalan dan log masuk pada peringkat berbayar Chloros+ (Copper atau lebih tinggi), dan `connect_all()` tidak pernah memulakannya. Jika tiada yang sedang berjalan, setiap penderia yang disimpan akan disenaraikan di bawah `report["sensors"]["errors"]`, dengan makluman tersebut. Sensor yang tidak dapat dibuka oleh backend turut disenaraikan di sana, dengan sebab daripada backend; `report["sensors"]["missing"]` kekal kosong untuk sensor cahaya.

---

## Perkakasan Langsung (Tanpa Backend)

Apabila anda mahu sifar pergantungan pada backend untuk kamera LATTICE (CI, robot tanpa kepala, terbenam), import `lattice_sdk` secara langsung — ia dieksport semula oleh `chloros_sdk`. Pengawal pada `CAMERA_AVAILABLE`: `lattice_sdk` terdapat dalam roda SDK, tetapi memerlukan runtime Arena SDK dan OpenCV (`cv2`) hadir, dan roda tersebut tidak memasang kedua-duanya. Penderia cahaya menggunakan backend — [`connect_daq_sensor()`](#daq-sensor-sessions) atau `proj.sensors` projek — dengan satu pengecualian, pembantu DAQ-M band-magnitude [`DLS`](#daq-m-band-magnitudes--dlsband_magnitudes), yang juga memerlukan `bleak`.

```python
from chloros_sdk import (
    # cameras
    LatticeCamera, CameraSettings, PRESETS, CameraPool,
    Calibration, CalibrationCoefficients, FilterModel, list_filters,
    DLS, NetworkDiagnostics, gpu_info, gpu_available,
    # discovery
    discover_cameras, discover_cameras_via_backend,
    # exceptions
    LatticeError, CameraNotFoundError, StreamError, CaptureError,
    CalibrationError, NetworkError, DLSError,
)

# Find a camera and capture in one go
cams = discover_cameras(timeout_ms=3000)
print(cams)

settings = PRESETS["high_quality"]
with LatticeCamera(serial="213800234", settings=settings) as cam:
    result = cam.capture(output_dir="./out", format="tiff")
    print(result.filepath, result.width, result.height)
```

##### Preset dan pencetus

Tiga daripada empat preset **berlari bebas**: kamera mengambil gambar secara berterusan dan
`capture()` memulangkan bingkai seterusnya. `triggered` adalah pengecualian — ia mengaktifkan kamera untuk edge perkakasan pada Baris 2, jadi ia tidak menangkap apa-apa sehingga satu tiba.

| Pratetap | Pencetus | Gunakan apabila |
| --- | --- | --- |
| `default` | jalankan bebas | kegunaan umum |
| `high_speed` | jalankan bebas | 8-bit, had 60 fps, pendedahan pendek |
| `high_quality` | bebas | 12-bit, tiada had fps — pilihan biasa untuk gambar pegun |
| `triggered` | **bersedia, Baris 2** | kamera disambungkan ke kabel penyegerakan M8 dan sesuatu yang lain mencetuskannya |

Jika anda memilih `triggered` (atau menetapkan `trigger_mode="On"` sendiri) tanpa apa-apa yang menggerakkan Line 2, setiap `capture()` akan tamat masa — dengan betul, kerana anda meminta kamera untuk menunggu. SDK menerangkan perkara ini apabila ia berlaku; lihat [SC_ERR_TIMEOUT during capture](#direct-hardware-backend-free).

> **Nota — &quot;GVSP probe&quot; / `SC_ERR_TIMEOUT -1011` mesej semasa sambungan bukan ralat.**
> Semasa sambungan, SDK cuba merundingkan**jumbo frames** (paket GVSP 9000-bait) untuk kelajuan pemindahan yang lebih tinggi. Pada pautan NIC titik-ke-titik terus (contohnya alamat `169.254.x.x` link-local), rangkaian biasanya tidak dapat membawa bingkai jumbo, jadi prob ini akan tamat tempoh dan mencatat baris seperti:
>
> ```
> [Network] GVSP probe: unexpected error (TimeoutError: ... SC_ERR_TIMEOUT -1011)
> [Network] GVSP probe at 9000 did not deliver a complete buffer; reverting to ICMP-chosen size
> [Network] GVSP packet size: 1500 bytes (standard)
> ```
>
> Inilah **langkah sandaran yang direka**: SDK secara automatik kembali kepada paket 1500 bait standard dan kamera terus menyambung seperti biasa (baris `[chunk-enable …]` yang berikut adalah sebahagian daripada urutan sambungan biasa). Rakaman masih berfungsi.
>
> Anda boleh melangkau probe ini, tetapi **ia bukan sekadar pendiam log — ia mematikan bingkai jumbo.** Kamera hanya menjawab ping &#x27;Don&#x27;t-Fragment&#x27; sehingga 1500 bait tanpa mengira betapa baiknya rangkaian anda, jadi ujian ping sahaja tidak akan dapat mengesan jumbo; probe ini adalah satu-satunya yang boleh. Nyahdayakannya dan kamera akan menggunakan paket 1500 bait standard selama-lamanya, pada mana-mana rangkaian:
>
> ```bash
> CHLOROS_GVSP_PROBE_FALLBACK=0   # gives up jumbo — see the warning it prints
> ```
>
> Ia hanya berbaloi pada rangkaian yang anda *tahu* tidak boleh membawa jumbo, di mana ia menjimatkan kira-kira satu saat masa penyambungan bagi setiap kamera. Oleh kerana ia adalah satu pertukaran sebenar dan bukannya sekadar kosmetik, SDK kini memaparkan maklumat ini apabila anda menggunakannya:
>
> ```
> [Network] ⚠️ GVSP probe disabled (CHLOROS_GVSP_PROBE_FALLBACK=0) — staying at
> 1500 bytes, jumbo NOT tested. … if this network does carry it, you are giving
> up ~1.45x wire ceiling. Unset the variable to test for jumbo.
> ```
>
> **Biarkan sahaja melainkan anda mempunyai sebab.**Jika dibiarkan diaktifkan, setiap kamera diukur sekali setiap sesi backend, dan sekali lagi apabila pautannya berubah (penyesuai lain, kelajuan pautan lain, atau pautan yang terputus, seperti apabila menukar suis); sambungan semula akan menggunakan semula keputusan tersebut.**Selepas membuat perubahan di belakang suis yang masih aktif, mulakan semula backend untuk mengukur semula.** Sambungan pertama kadangkala boleh mengambil masa beberapa minit untuk semakan ini, dan log akan menunjukkan demikian apabila ia berlaku.
>
> Jika anda *mahu* throughput jumbo, aktifkan jumbo hujung-ke-hujung (NIC MTU 9000 + suis yang meneruskannya), atau tetapkan saiznya dengan `CHLOROS_GVSP_PACKET_SIZE_FORCE=9000` apabila anda tahu pautan menyokongnya — walaupun lebih baik menggunakan `CHLOROS_GVSP_PACKET_SIZE_FORCE=9000 python …` setiap arahan berbanding menetapkannya secara kekal, kerana saiz yang dipin akan melangkau probe dan menghentikan penyesuaian kepada rangkaian di hadapannya. **Setiap** peranti dalam laluan mesti menyokong jumbo — termasuk sebarang pemisah atau penyuntik PoE, yang biasanya menjadi sebab mengapa satu susunan yang sepatutnya menyokong jumbo tidak dapat memindahkannya.

> **`SC_ERR_TIMEOUT -1011` semasa `capture()` / `grab*()` adalah masalah yang berbeza — itu adalah ralat sebenar.**
> Nota di atas hanya mengenai `-1011` yang direkodkan oleh**pemeriksa masa sambungan**. Ralat yang sama yang dikesan daripada**perekodan** bermaksud kamera telah bersambung dengan baik tetapi tidak menghantar sebarang imej:
>
> ```
> File ".../lattice_sdk/camera.py", line ..., in grab_frame_with_metadata
>   buffer = self._get_buffer(timeout)
> lattice_sdk.exceptions.CaptureError: Capture failed: ... SC_ERR_TIMEOUT -1011
> ```
>
> Petandanya ialah kamera yang saluran *kawalan*nya sihat — penemuan berfungsi, tetapan dan penulisan `[chunk-enable …]` semuanya berjaya — manakala *setiap* bingkai mengalami tamat masa.
>
> **Punca biasa ialah kamera diset untuk pencetus perkakasan.** Dengan `trigger_mode="On"` dan `trigger_source="Line2"`, kamera tidak menghantar apa-apa langsung sehingga tepi elektrik tiba pada kabel penyegerakan M8. Jika anda tidak mempunyai kabel yang memacu talian itu, setiap grab menunggu selama-lamanya. Kamera tidak rosak dan rangkaian juga baik-baik sahaja — ia melakukan tepat seperti yang diarahkan.
>
> `CameraSettings()` dan `default` / `high_speed` / `high_quality` menetapkan larian bebas, dan grab yang tamat masa semasa bersedia menerangkan dirinya sendiri bukannya mencetak `-1011` kosong. `PRESETS["triggered"]` mengaktifkan Line2, mengikut reka bentuk.
>
> Untuk memaksa mana-mana kamera berjalan bebas:
>
> ```python
> import copy
> settings = copy.deepcopy(PRESETS["high_quality"])   # PRESETS entries are shared
> settings.trigger_mode = "Off"        # free-run; don't wait for an M8 edge
> ```
>
> Jika ia masih tamat masa dengan `trigger_mode="Off"`, kamera itu benar-benar tidak menghantar data — hantarkan kepada kami log dan `ip link show`.

#### Profil Warna (pra-tonton langsung RGB) — `set_color_profile`

`LatticeCamera.set_color_profile(profile, custom_cct_k=None)` memilih profil warna paparan untuk **pra-tonton langsung** pada kamera RGB (kamera multispektral mengabaikan tetapan ini):

| Profil | Makna |
| --- | --- |
| `raw` | Langkau sepenuhnya rantaian radiometrik. |
| `linear` | DSNU + rata + WB, tiada CCM, tiada gamma. |
| `natural` | Linear + CCM yang diukur + gamma sRGB, dengan kemasan murah sahaja (pelicinan kroma + penurunan ketepuan warna pada bahagian terang) — tetapan lalai yang realistik. |
| `enhanced` | `natural` ditambah kemasan pariti-habek penuh (defringe, vibrance, kontras tempatan CLAHE). Penampilan yang lebih kaya pada kos kemasan per-bingkai kira-kira **dua kali ganda**, jadi kadar bingkai LIVE yang lebih rendah. |
| `custom_temp` | `natural` tetapi WB dipaku pada `custom_cct_k` Kelvin (DLS diabaikan; dikekang pada 2000–10000 K di pihak belakang). |

Profil ini adalah tombol kelajuan/penampilan **pra-tonton langsung sahaja**: rakaman yang disimpan sentiasa mendapat kemasan kaya penuh tanpa mengira profil yang dipilih, jadi memilih `natural` untuk membeli semula masa bingkai tidak menurunkan kualiti apa yang disimpan ke cakera. Profil yang tidak diketahui akan mencetuskan `ValueError`; apabila backend kloros boleh dihubungi, perubahan itu juga di-POST kepadanya supaya bingkai pratonton seterusnya mencerminkannya (pengguna SDK terus tanpa backend masih menerima mutasi tetapan).

```python
with LatticeCamera(serial="214701292") as cam:   # RGB cam
    cam.set_color_profile("enhanced")            # richer look, lower LIVE fps
    cam.set_color_profile("custom_temp", custom_cct_k=5600)
```

#### Sorotan (paparan RGB) — `set_highlights`

`LatticeCamera.set_highlights(value)` menarik piksel paling cerah dan paling putih daripada **paparan** menurunkan render pada kamera RGB — pembetulan untuk permukaan putih terang yang dirender sebagai tompok putih rata. `value` adalah `-100`..`0` (dikehadkan); `0` dimatikan, dan render kemudian adalah tepat seperti tanpa tetapan itu. Tetapan yang lebih kuat menurunkan putih paparan (ke kira-kira 60% pada `-100`) dan melebarkan lengkung nada, sehingga permukaan yang cahaya automatik pratonton dorong ke putih tulen mendapat kembali perinciannya.

Ia adalah kawalan paparan, tetapi tidak seperti ketepuan dan kontras (yang hanya mengubah pratonton langsung), ia juga mengubah apa yang anda simpan: pratonton langsung dan tangkapan **pada tahap paparan** mengikutinya, jadi fail-fail tersebut juga keluar lebih gelap (mereka membawa `Chloros:DisplayHighlights`); eksport radiasi dan pantulan tidak pernah melakukannya, dan ia tidak dapat memulihkan data sensor yang terpotong — kurangkan pendedahan untuk itu. Kamera Multispec dan mono mengabaikannya. Nilai disimpan pada `settings.highlights` dan dihantar ke backend chloros yang boleh dihubungi, sama seperti `set_color_profile`; setara CLI ialah `lattice highlights --set N`.

```python
with LatticeCamera(serial="214701292") as cam:   # RGB cam
    cam.set_highlights(-60)                      # 0 = off, -100 = strongest
```

#### Kamera Mono (M3M) dan `Calibration`

Kamera mono **M3M** (`M3M-<lens>-F<wavelength>`) adalah jalur tunggal: satu satah skala kelabu, tiada mozek Bayer, tiada matriks silang-bicaraan spektral 3×3. `Calibration` mengenalinya dan mendedahkan bendera `is_mono`. Reflektansi masih terpakai sebagai peta radiometrik bagi setiap jalur (pencampuran semula adalah matriks identiti), tetapi pengiraan berbilang jalur pada satu kamera menimbulkan masalah dan bukannya menghasilkan keputusan yang tidak masuk akal:

```python
from chloros_sdk import Calibration, CalibrationError

calib = Calibration("M3M-L87-F685")
print(calib.is_mono)        # True  (False for any M3C / RGN Bayer cam)
print(calib.filter_type)    # 'mono'  (sentinel; not a real crosstalk key)

# NDVI needs two bands (Red + NIR); one mono band can't supply both.
try:
    calib.compute_ndvi(reflectance_frame)
except CalibrationError as e:
    print(e)   # "...single-band mono (M3M) camera. Combine multiple..."
```

Untuk membina indeks vegetasi daripada perkakasan mono, gabungkan beberapa kamera M3M pada panjang gelombang yang berbeza ke dalam susunan berbilang jalur yang selari (lihat [Penjajaran Susunan](#array-alignment)) dan hitung indeks merentasi susunan itu bukannya pada satu kamera.

#### Besaran jalur DAQ-M — `DLS.band_magnitudes`

`DLS(mac_address, filter_model="RGN-IMX265")` menyambung ke DAQ-M melalui Bluetooth dengan BLE MACnya sendiri. Ia satu-satunya kelas penderia cahaya yang berkomunikasi dengan penderia secara langsung dan bukannya melalui backend, jadi ia memerlukan apa yang diperlukan oleh kelas kamera langsung (`CAMERA_AVAILABLE`: runtime Arena SDK dan OpenCV) ditambah `bleak` untuk Bluetooth (`pip install "chloros-sdk[camera]"`, atau `pip install bleak`; tanpa ia `DLS` menimbulkan `DLSError`). DAQ-M yang disambungkan di tempat lain (kolam backend, projek, GUI) berhenti mengiklankan, jadi bebaskan ia di sana terlebih dahulu. **`mac_address` diperlukan**: tiada lalai, kerana setiap unit mempunyai MAC sendiri, dan `DLS()` tanpa MAC akan mencetuskan `DLSError` sebelum sebarang sambungan dicuba. Cari MAC dengan `discover_daq_sensors(transports=["ble"])` (lihat [Discovery](#discovery--finding-an-address-to-connect-with)) atau `chloros-cli daq pool-discover --only ble`.

`DLS.band_magnitudes(spectrum=None, *, spectral_response=None)` menggabungkan spektrum DAQ-M (bacaan terkini apabila `spectrum` adalah `None`; `DLSError` jika tiada lagi) melalui tindak balas tiga saluran kamera dan memulangkan `{"red": …, "green": …, "nir": …}` — butang menamakan saluran mengikut susunan itu; bagi lengkung kamera RGB, saluran ketiga (`nir`) ialah saluran biru.

- Tanpa `spectral_response` ia menggunakan lengkung rujukan model penapis (`filter_model` yang `DLS` dibina dengannya), yang diukur pada satu unit.
- `spectral_response` mengambil lengkung yang diukur **sendiri** oleh kamera sebaliknya: a dict `{"wavelengths": [...], "R": [...], "G": [...], "B": [...]}`, seperti yang disimpan dalam pek penentukuran kamera tersebut (`color_correction.spectral_response_smoothed` — kamera RGB; pek multispektral tidak mempunyai lengkung sedemikian). Jika dict tidak dapat digunakan, ia akan kembali menggunakan lengkung armada.

```python
from chloros_sdk import DLS

# camera_curves: {"wavelengths": [...], "R": [...], "G": [...], "B": [...]}
with DLS(mac_address="AA:BB:CC:DD:EE:FF") as dls:
    reading = dls.acquire()
    fleet = dls.band_magnitudes(reading)                         # fleet curve
    own = dls.band_magnitudes(reading,
                              spectral_response=camera_curves)   # the camera's own
```

`chloros-cli lattice dls --camera SERIAL` pada CLI melakukan perkara yang sama melalui backend, mencari lengkung kamera dalam pek penentukuran yang dimuat atau disimpan dalam cache, dan menyatakan lengkung siapa yang digunakan. Melalui HTTP, iaitu `POST /api/dls/cli-read` dengan `mac` (diperlukan: tanpanya balasan adalah HTTP 400, `code` `MAC_REQUIRED`) dan `camera_serial` dalam badan; `band_magnitudes_source` dalam balasan adalah `camera <serial>` apabila lengkung kamera itu sendiri digunakan, atau `model <filter>` untuk lengkung armada (kamera tanpa lengkung: bukan RGB, atau tiada pek dimuatkan atau disimpan dalam cache).

---

## Pengecualian

Tangkap kelas asas untuk mengendalikan &quot;apa sahaja yang salah dengan Chloros&quot;:

```python
import chloros_sdk

try:
    chloros_sdk.process_folder("/path/to/folder")
except chloros_sdk.ChlorosAuthenticationError:
    print("Run `chloros-cli login` first.")
except chloros_sdk.ChlorosLicenseError:
    print("Chloros+ subscription required.")
except chloros_sdk.ChlorosError as e:
    print(f"Chloros error: {e}")
```

> `ChlorosAuthenticationError` dan `ChlorosConfigurationError` adalah atribut `chloros_sdk` (dan `chloros_sdk.exceptions`) tetapi tidak dalam `__all__`, jadi `from chloros_sdk import *` tidak membawanya masuk: import mereka mengikut nama. `ChlorosError`keluarga OTX diangkat oleh `ChlorosLocal` dan fungsi-fungsi yang dibina di atasnya (`process_folder()`, `process_lattice_capture()`); permukaan smart-connect memanggil `ChlorosConnectError`.

Hierarki:

```

ChlorosError
├── ChlorosBackendError           (backend binary not found / failed to start)
├── ChlorosConnectionError        (HTTP transport failure; no backend running
│                                  with auto_start_backend=False)
├── ChlorosLicenseError           (403 PLAN_UPGRADE_REQUIRED: plan below Copper)
├── ChlorosAuthenticationError    (401 AUTH_REQUIRED: not logged in)
├── ChlorosConfigurationError     (bad configure() inputs)
└── ChlorosProcessingError        (pipeline failed; create/import refused)
    └── ChlorosProjectFilesMissingError  (open_project(): none of the
                                  project's source files found, even after
                                  the relink; .missing_roots, .audit)

ChlorosConnectError                (the smart-connect surface: connect_*, attach_array,
                                    list_*, discover_*, every CameraSession /
                                    ArraySession / DAQSensorSession method,
                                    RecorderHandle, ContinuousHandle,
                                    disconnect_all(), and analyze_array_network()
                                    when no backend answers; 401 and 403 included.
                                    Derives from plain Exception, NOT from
                                    ChlorosError, so `except ChlorosError` will
                                    not catch it. .warning: a warning the
                                    backend sent beside its error (also at the
                                    end of the message); .recording_closed: the
                                    recording a failed DAQ-E restart closed;
                                    both None otherwise)

FrameFetchError (RuntimeError)     (grab() / frame_stream() refusals; .status,
                                    .body, .errors)
ValueError                         (capture() arguments: PNG/JPEG with a level they
                                    cannot hold; a bad packed=)
FileNotFoundError / ImportError    (open_project(): no such folder / no project
                                    loaders)
RuntimeError                       (CameraHandle / ArrayHandle: a backend refusal)

lattice_sdk exceptions:
LatticeError
├── CameraNotFoundError
├── CameraConnectionError
├── StreamError
├── CaptureError
├── CalibrationError
├── NetworkError
└── DLSError
```

---

## Contoh Dari Akeh Sampai Akhe

### 1. Proses Folder dengan Bar Kemajuan Tersuai

```python
from chloros_sdk import ChlorosLocal

def progress(percent, message):
    bar = "#" * (percent // 5)
    print(f"\r[{bar:<20s}] {percent:3d}% {message}", end="", flush=True)

with ChlorosLocal() as cl:
    cl.create_project("FieldA_2026-05-26")
    cl.import_images("C:/DroneImages/Flight001", recursive=True)
    cl.configure(
        debayer="High Quality (Faster)",
        vignette_correction=True,
        reflectance_calibration=True,
        indices=["NDVI", "GNDVI", "OSAVI", "SAVI"],
        export_format="TIFF (16-bit)",
    )
    cl.process(progress_callback=progress)
print()
```

### 2. Susunan LATTICE Langsung → Reflektansi + Rujukan DAQ

```python
import chloros_sdk

# Open a paired sensor first so the array's reflectance step has an
# absolute reference. Smart-detect picks USB / BLE / ETH automatically.
with chloros_sdk.connect_daq_sensor() as daq:
    with chloros_sdk.connect_array([
            "213800234", "214000533", "214701288", "214701292"
    ]) as arr:
        # Smart capture: wait for AE to settle, then snap
        arr.capture("./out", processing="reflectance", smart=True)

        # Record the corresponding DAQ frames as ground truth
        daq.record_start(output_dir="./out", device_name="sky-reference")
        # ... do whatever capture campaign ...
        info = daq.record_stop()
        print(info["path"], info["rows"])
```

### 3. Kempen Rakaman Terarah Projek

```python
import time, chloros_sdk

with chloros_sdk.open_project("/home/user/Chloros Projects/Field_A") as proj:
    report = proj.connect_all(verbose=True, align=True)
    if report["arrays"]["errors"]:
        raise SystemExit(f"Array(s) failed to connect: {report['arrays']['errors']}")

    rig = proj.arrays["main_rig"]

    # Re-align right before the campaign
    rig.calibrate_alignment(num_frames=5)
    rig.export_alignment("./alignments/main_rig.json")

    # 50 sequential single-frame captures at 2 fps
    for i in range(50):
        frames = rig.capture(
            output_dir=f"./out/frame_{i:04d}",
            processing="reflectance",
            apply_calibration=True,
            apply_white_balance=True,
        )
        time.sleep(0.5)

    # End-of-day: process the captured folder. process() accepts only
    # mode/wait/progress_callback/poll_interval — indices come from the
    # project's saved config (or set them via ChlorosLocal.configure()).
    proj.process()
```

### 4. Aliran Bingkai Pelbagai Kamera → Saluran Pipa NumPy

```python
import chloros_sdk
import numpy as np

with chloros_sdk.open_project("/path/to/proj") as proj:
    proj.connect_all()
    rig = proj.arrays["main_rig"]

    for frames in rig.frame_stream(
            processing="radiance",
            fps=5.0, count=300,
            apply_calibration=True,
            apply_white_balance=True):
        # frames is {serial: numpy_array}; cams not delivering this tick are omitted
        for serial, frame in frames.items():
            print(serial, frame.shape, frame.dtype, frame.mean())
```

### 5. Skrip Rakaman Perkakasan Langsung Tanpa Antara Muka (Tiada Backend)

```python
from chloros_sdk import LatticeCamera, PRESETS, discover_cameras

cams = discover_cameras(timeout_ms=3000)
print(f"Found {len(cams)} cams")

settings = PRESETS["high_quality"]
for c in cams:
    with LatticeCamera(serial=c.serial, settings=settings) as cam:
        result = cam.capture(output_dir="./out", format="tiff")
        print(c.serial, result.filepath)
```

### 6. Ujian Kebolehan Sebelum Menyambungkan Susunan 4-Kamera

```python
import chloros_sdk

serials = ["214701288", "213800234", "214000533", "214701162"]

probe = chloros_sdk.analyze_array_network(
    master_serial=serials[0],
    slave_serials=serials[1:],
    width=2048, height=1536,
    pixel_format="BayerRG12",
)

if probe["status"] == "ok":
    arr = chloros_sdk.connect_array(
        serials, width=2048, height=1536, pixel_format="BayerRG12")
elif probe["status"] == "auto_capped_fps":
    r = probe["recommended"]
    print(f"Keeping resolution; capping trigger rate at "
          f"{r['recommended_target_fps']} fps")
    arr = chloros_sdk.connect_array(
        serials, width=2048, height=1536, pixel_format="BayerRG12",
        target_fps=r["recommended_target_fps"])
elif probe["status"] == "auto_shrunk":
    r = probe["recommended"]
    print(f"Auto-shrinking to {r['out_width']}x{r['out_height']} "
          f"binning={r['binning']} for sim-sync")
    arr = chloros_sdk.connect_array(
        serials,
        width=r["width"], height=r["height"],     # before binning
        pixel_format=r["pixel_format"], binning=r["binning"])
elif probe["status"] == "needs_force_slip":
    print("Wire can't sustain sim-sync; falling back to slip mode")
    arr = chloros_sdk.connect_array(
        serials, force_tier="slip-emit-and-capture")
else:
    raise RuntimeError(f"Probe error: {probe.get('error')}")
```

### 7. Setara Resipi Rakaman (Python Murni)

`run_project_recipe()` menjalankan resipi CLI seperti sedia ada (lihat [Folder Projek &amp; Resipi](#project-folder--recipes)). Ditulis dalam Python sebaliknya, langkah yang sama adalah:

```python
import time, chloros_sdk

with chloros_sdk.open_project("/path/to/proj") as proj:
    proj.connect_all()
    cam = proj.cameras["FrontLeft"]
    rig = proj.arrays["main_rig"]
    sky = proj.sensors["Sky"]

    # apply: connect_all() applied each camera's saved settings. CameraHandle
    # has no settings method; a CameraSession on the same pooled camera does
    # (it is borrowed, so it never releases the camera)
    chloros_sdk.connect_camera(cam.serial).set_settings(
        exposure_time=5000, gain=6.0)

    # wait
    time.sleep(2)

    # capture
    cam.capture("pose_a/", format="tiff", processing="radiance")

    # stream
    rig.stream(count=60, fps=5, output_dir="stream/", processing="raw")

    # sensor read
    print(sky.read())
```

---

## Pelayan MCP (Penolong AI)

`chloros-mcp` membolehkan pembantu AI yang bercakap [Model Context Protocol](https://modelcontextprotocol.io) (MCP), seperti Claude Desktop, Claude Code atau Cursor, mengendalikan Chloros: memproses imej (mencipta projek, mengimport folder tangkapan, memilih tetapan, memproses, melaporkan apa yang dieksport oleh larian) dan menjalankan kamera LATTICE, tatasusunan dan penderia cahaya DAQ (menyambung, pratonton, menetapkan pendedahan dan warna, merakam, merekod, mengemas kini firmware). Alatnya merangkumi apa yang dilakukan oleh SDK ini. Anda bertanya dalam bahasa biasa dan pembantu memanggil alat pelayan:

```text
You:  Process the flight in D:\Flights\2026-10-17 to reflectance and NDVI.

The assistant calls:
  create_project          {"project_name": "Flight_2026-10-17"}
  import_images           {"folder_path": "D:\\Flights\\2026-10-17"}
  configure_processing    {"indices": ["NDVI"]}
  start_processing        {}
  get_processing_status   {}      (every 30-60 s until its state is "finished")

You:  Show me what the array sees, then capture reflectance every 10 seconds
      for 5 minutes.

The assistant calls:
  list_devices            {}
  get_camera_preview      {"array_id": "array-1779862544497"}
  start_capture_run       {"array_id": "array-1779862544497", "mode": "interval",
                           "interval_s": 10, "duration_s": 300,
                           "processing": "reflectance"}
  get_job_status          {"job_id": "..."}   (until its state is "finished")
```

Pelayan dibina di atas SDK ini: alat pemprosesan memanggil [`ChlorosLocal`](#image-processing--chloroslocal) dan alat perkakasan [panggilan smart-connect](#smart-connect-for-lattice-cameras), [sesi DAQ](#daq-sensor-sessions), `ChlorosProject` dan [utiliti kamera](#camera-utilities), semuanya berkomunikasi dengan backend yang sama yang digunakan oleh aplikasi Chloros. Permintaannya membawa `X-Chloros-Client: sdk`, jadi backend menerapkan pemeriksaan log masuk dan pelan SDK, dan peraturan SDK disertakan dengannya (contohnya, Texture Aware ditolak untuk rakaman LATTICE).

### Pasang

Pelayan disertakan dalam pakej `chloros-sdk`; tiada apa-apa lagi yang perlu dipasang. SDK memasang arahan `chloros-mcp`, yang sama dengan `python -m chloros_sdk.mcp_server`:

```bash
pip install chloros-sdk        # not needed where the Chloros installer set up the SDK (Step 2)
chloros-mcp --help
```

- **Windows:** pemasang Chloros memasang SDK ke dalam Python anda melalui pip, yang meletakkan `chloros-mcp.exe` dalam folder `Scripts` Python tersebut.
- **Linux (.deb):** pakej ini memasang SDK untuk sistem `python3` dan meletakkan arahan `chloros-mcp` dalam `/usr/bin`, jadi ia terdapat pada setiap pengguna `PATH`. Dalam virtualenv, `pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl` menambah `chloros-mcp` ke dalam `bin` virtualenv tersebut.

Pelayan hanya menggunakan perpustakaan standard di atas SDK (Python 3.7+). Klien MCP memulakannya dan berkomunikasi dengannya melalui stdin/stdout; anda tidak menjalankannya sendiri, kecuali dengan `--help`.

### Penyediaan Klien

**Claude Desktop:**tambahkan pelayan ke `claude_desktop_config.json` (pada Windows `%APPDATA%\Claude\claude_desktop_config.json`;**Tetapan &gt; Pembangun &gt; Sunting Konfig** membukanya), kemudian mulakan semula Claude Desktop:

```json
{
  "mcpServers": {
    "chloros": {"command": "chloros-mcp"}
  }
}
```

**Claude Code:**

```bash
claude mcp add chloros -- chloros-mcp
```

**Penunjuk:** entri `mcpServers` yang sama seperti Claude Desktop, dalam `~/.cursor/mcp.json` (setiap projek; `%USERPROFILE%\.cursor\mcp.json` pada Windows) atau `.cursor/mcp.json` dalam satu projek.

Pilihan dimasukkan ke dalam `args`, atau selepas arahan untuk Claude Code (`claude mcp add chloros -- chloros-mcp --no-auto-start`). Untuk menggunakan Python tertentu, gunakan SDK tersebut dan bukan `chloros-mcp` pada `PATH`, jalankan modul dengan Python tersebut (jalur penuh ia sebagai `command` jika ia bukan Python pertama pada `PATH`):

```json
{
  "mcpServers": {
    "chloros": {"command": "python3", "args": ["-m", "chloros_sdk.mcp_server"]}
  }
}
```

Pelanggan memulakan pelayan tanpa shell anda, jadi jika ia melaporkan bahawa pelayan gagal dimulakan, `chloros-mcp` mungkin tidak berada pada `PATH` yang dilihat oleh pelanggan. Berikan laluan penuh sebaliknya (`where chloros-mcp` pada Windows, `which chloros-mcp` pada Linux, di mana .deb adalah `/usr/bin/chloros-mcp`), atau jalankan modul dengan Python yang mempunyai SDK.

### Pilihan

| Pilihan | Lalai | Fungsinya |
| --- | --- | --- |
| `--backend-url URL` | `http://127.0.0.1:5000` | Backend Chloros yang akan digunakan. |
| `--no-auto-start` | off | Tidak pernah memulakan backend: gunakan yang sudah berjalan, seperti aplikasi Chloros, perkhidmatan backend Linux, atau terowong ke mesin lain (lihat [Mod Backend Jauh](#remote-backend-mode-pip-only-host-via-tunnel); laluan folder kemudian mesti menamakan folder yang sama pada kedua-dua mesin). |
| `--backend-exe PATH` | di mana Chloros memasangnya | Backend untuk dimulakan apabila tiada yang sedang berjalan, untuk Chloros yang dipasang di lokasi yang tidak dicari oleh SDK. |
| `--log-level LEVEL` | `WARNING` | `DEBUG`, `INFO`, `WARNING` atau `ERROR`. Log dihantar ke stderr, yang kebanyakan klien paparkan dalam log MCP mereka; stdout hanya mengandungi mesej protokol. |

### Alat

Argumen setiap alat adalah argumen panggilan SDK yang dibungkusnya; klien menyenaraikan setiap satu dengan skema penuhnya. Laluan adalah mutlak.

**Pemprosesan**

| Alat | Panggilan SDK | Fungsinya |
| --- | --- | --- |
| `create_project` | `ChlorosLocal.create_project` | Mencipta projek dan menjadikannya projek terbuka, yang menjadi tumpuan alat pemprosesan. Nama biasa mencipta folder dalam folder projek lalai; laluan mutlak menciptanya di situ. |
| `open_project` | `ChlorosLocal.open_project` | Membuka projek yang disimpan dan menjadikannya projek terbuka. Laluan projek yang dipindahkan akan dialihkan semula ke folder tempat ia kini berada; `relink_folder` (absolut) menamakan folder di mana fail-failnya yang hilang kini berada. Mengembalikan `source_files` dan `image_count`. Tiada ditemui: ralat menyenaraikan lokasi mereka dan menamakan `relink_folder`; tiada apa-apa dibuka. |
| `import_images` | `import_images` | Memuat masuk pasangSurvey3 `.RAW`+`.JPG`, LATTICE `.tif` merakam dan DAQ `.daq` rakaman; memulangkan `count` (imej) dan `scan_count` (rakaman). |
| `configure_processing` | `configure` | Mengubah tetapan yang dihantar dan tiada yang lain (nilai: [Nilai Disokong](#supported-values)); `custom_settings` menghantar keseluruhan konfigurasi seperti yang diberikan. |
| `get_project_settings` | `get_config` | Membaca tetapan pemprosesan, eksport dan indeks yang disimpan bagi projek terbuka. |
| `start_processing` | `process(wait=False)` | Memulakan pemprosesan projek terbuka dan kembali serta-merta. |
| `get_processing_status` | `get_status` | Melaporkan `state` (`idle`, `starting`, `processing` atau `finished`) dan kemajuan; Setelah selesai, jalankan `summary`, `export_check_failure` apabila eksport tidak dapat disahkan, dan `image_failure` apabila imej hilang: produk lain telah ditulis; laporkan pelaksanaan itu sebagai gagal. |
| `process_folder` | `process_folder` / `process_lattice_capture` | Mencipta projek untuk folder, mengimportnya, mengkonfigurasikan dan memulakan larian dalam satu panggilan (`lattice: true` untuk tangkapan LATTICE). Semasa sesuatu larian sedang dijalankan, ia menolak sebelum ia mencipta apa-apa, dan backend juga menolak perubahan projek daripada mana-mana alat pada masa itu. |
| `export_light_sensor` | `export_light_sensor` | Menulis fail `.daq` dan `.csv` yang dikalibrasi untuk penderia cahaya projek rekod tanpa memproses imej, mengambil kalibrasi kilang daripada MAPIR Cloud apabila ia tidak disimpan dalam cache (lihat [Light-Sensor Recordings](#light-sensor-recordings--calibrated-daq--csv)). |
| `read_image_audit_tags` | `read_image_audit_tags` | Membaca metadata audit Chloros imej yang diproses (sumber, penentukuran, peringkat yang digunakan). |

**Kamera dan tatasusunan**

| Alat | Panggilan SDK | Fungsinya |
| --- | --- | --- |
| `list_devices` | `list_cameras`, `list_arrays`, `list_daq_sensors` | Apa yang disambungkan sekarang, dalam mana-mana klien, masing-masing dengan `connected_here`; ditambah kerja yang sedang dijalankan oleh pelayan. |
| `discover_devices` | `discover_lattice_cameras`, `discover_daq_sensors` | Kamera dan penderia cahaya yang boleh disambungkan. |
| `get_device_status` | `CameraSession.status`, `ArraySession.status` / `capability`, `DAQSensorSession.status` | Status langsung satu kamera, tatasusunan (dengan keupayaannya dan kesihatannya) atau penderia cahaya. |
| `analyze_array_network` | `analyze_array_network` | Apa yang hos dan rangkaian ini boleh bawa untuk susunan yang dicadangkan. |
| `connect_camera` / `connect_array` | `connect_camera` / `connect_array` | Sambungkan kamera, atau susunan selaras (master dahulu). |
| `connect_project_devices` | `open_project(...).connect_all` | Sambungkan setiap peranti yang diingati oleh projek yang disimpan, dengan tetapan masing-masing. |
| `disconnect_device` | `disconnect` / `force_disconnect` | Lepaskan kamera, tatasusunan atau penderia cahaya (lihat Tingkah Laku). `keep_settings: true` pada satu susunan menyimpan tetapan setiap kamera ke dalam memori tersendiri, `clear_settings: true` menetapkan semula memori itu (hasilnya membawa `camera_memory`). |
| `disconnect_project_devices`, `align_project_arrays`, `capture_project_devices` | `disconnect_all`, `align_arrays`, `capture_all` | Peranti projek, bersama-sama. |
| `read_camera_settings` / `set_camera_settings` | `CameraSession.read_nodes` / `set_settings` | Membaca atau menulis tetapan kamera, melalui nama mesra atau GenICnama nod. |
| `set_array_frame_rate` | `ArraySession.set_target_fps` | Tetapkan kadar satu susunan, atau kembalikannya kepada kadar automatik. |
| `capture_camera` / `capture_array` | `CameraSession.capture` / `ArraySession.capture` | Satu tangkapan (`fastest` dan `wait_for_exposure` adalah `capture_fastest` dan tangkapan pendedahan yang diselesaikan). Tanpa `output_dir` ia menyimpan ke dalam projek terbukafolder `captures`, dan fail-fail itu disertakan dalam projek (ditangguhkan sehingga satu larian pemprosesan selesai); balasan `project_add` menyatakan demikian. |
| `start_capture_run` | `ArraySession.continuous`, `capture_repeated` | Rakaman berterusan atau selang, sebagai satu kerja. |
| `start_array_recording` | `ArraySession.record` / `burst` | Video indeks gabungan, atau burst mentah, sebagai satu kerja. |
| `build_burst_video` | `ArraySession.build_video` | Mengproses semula burst menjadi video dikalibrasi, sebagai satu kerja. |
| `get_job_status` / `stop_job` | | Ikuti atau hentikan kerja. |
| `get_camera_preview` | | Pratonton langsung setiap kamera, dikembalikan sebagai imej. |
| `grab_frames` | `ArraySession.grab`, `CameraHandle.grab` | Satu bingkai (atau satu set selari) pada tahap pemprosesan, tanpa menyimpan: statistik bagi setiap satah dan satu imej PNG bagi setiap satu. |
| `calibrate_array_alignment`, `get_array_alignment`, `clear_array_alignment`, `nudge_array_alignment`, `export_array_alignment`, `import_array_alignment` | `ArrayHandle.calibrate_alignment`, `alignment_status`, `clear_alignment`, `tweak_alignment`, `export_alignment`, `import_alignment` | Penjajaran tatasusunan. |

**Penderia cahaya**

| Alat | Panggilan SDK | Fungsinya |
| --- | --- | --- |
| `connect_light_sensor` | `connect_daq_sensor` | Sambungkan DAQ-U, DAQ-M atau DAQ-E (`cap_id` menetapkan pembetulan capnya). |
| `get_light_sensor_reading` | `DAQSensorSession.latest` | Bacaan terkini: cap masa, kepenuhan, julat spektrum dan kemuncak, serta purata spektrum ke dalam bin panjang gelombang (`full_spectrum` untuk setiap titik). |
| `set_light_sensor_streaming` | `stream_start` / `stream_stop` | Mulakan atau hentikan aliran. |
| `start_light_sensor_recording` / `stop_light_sensor_recording` | `record_start` / `record_stop` | Rakam fail `.daq`, sebagai kerja; `duration_s` menghentikannya secara automatik. |
| `set_light_sensor_cap` | `DAQSensorSession.set_cap` | Pembetulan topi (`none` untuk sensor kosong). |
| `get_light_sensor_bindings` / `set_light_sensor_bindings` | `daq_bindings` / `set_daq_bindings` | Sensor mana yang membekalkan pantulan bagi setiap kamera dan susunan. |
| `get_light_sensor_band_presets` / `get_light_sensor_reflectance` | `daq_band_presets` / `light_sensor_reflectance` | Set jalur, dan pantulan Sekitar + Objek serta indeks bagi dua penderia penstriman. |
| `get_light_sensor_device_info`, `set_light_sensor_network`, `set_light_sensor_auto_stream`, `enable_light_sensor_ptp`, `reboot_light_sensor`, `update_light_sensor_firmware` | `device_info`, `set_network`, `set_auto_stream`, `enable_ptp`, `reboot`, `update_firmware` | Tetapan DAQ-E sendiri, disimpan dalam unit. |

**Utiliti Kamera** ([Utiliti Kamera](#camera-utilities))

| Alat | Panggilan SDK | Fungsinya |
| --- | --- | --- |
| `get_camera_color_settings` / `set_camera_color_settings` | `camera_color_profile`, `camera_color_adjust`, `camera_highlights` dan penetapnya | Warna paparan kamera RGB: profil dan suhu warna, ketepuan / kontras, sorotan. |
| `run_camera_white_balance` / `set_camera_white_balance` | `camera_white_balance_once` / `set_camera_white_balance` | Imbangan putih satu tembakan, atau auto / mati / nisbah tetap. |
| `list_camera_presets` / `apply_camera_preset` | `camera_presets` / `apply_camera_preset` | Tetapan kamera. |
| `list_filter_models` | `camera_filter_models`, `camera_filter_model_info` | Model penapis, atau satu mengikut nama. |
| `get_camera_firmware_status` / `update_camera_firmware` | `camera_firmware_status` / `update_camera_firmware` | Versi firmware, dan flash (sebuah tugas secara lalai). |
| `get_camera_power_info` / `set_camera_power` | `camera_power_probe`, `sleep_camera`, `wake_camera` | Keadaan kuasa dan terma; tidur atau sedar. |
| `calibrate_camera_with_target` | `calibrate_camera_with_target` | Punca daripada sasaran penentukuran yang dilihat. |
| `get_network_adapters` / `fix_network_settings` | `network_adapters` / `fix_network_settings` | Penyesuai rangkaian kamera, anggaran, dan pembetulan (Pentadbir sahaja). |
| `apply_vignette_correction` | `apply_vignette_correction` | Betulkan sisipan pada folder imej. |
| `read_dls` | `read_dls` | Satu bacaan DLS. |
| `probe_system` / `get_system_info` | `probe_system` / `system_info` | Perkakasan hos dan runtime. |

**Kamera sementara, profil penjajaran dan indeks** ([Susunan Sementara, Profil Penjajaran &amp; Indeks](#transient-arrays-alignment-profiles--indices)). Setiap alat di sini yang membuka kamera menggunakan `keep_settings` / `clear_settings` (lalai palsu): lihat [Memori kamera](#camera-memory--keep_settings--clear_settings).

| Alat | Panggilan SDK | Apa fungsinya |
| --- | --- | --- |
| `start_camera_continuous` / `start_multi_camera_stream` | `start_camera_continuous` / `start_multi_camera_stream` | Rakam satu kamera, atau menstrim beberapa kamera secara serentak, sebagai satu kerja (`duration_s` menghentikannya secara automatik). |
| `capture_cameras_transient` | `capture_cameras_transient` | Satu tangkapan bersepadu tanpa menyambungkan tatasusunan. |
| `test_array_sync` / `detect_array_master` | `test_array_sync` / `detect_array_master` | Semakan sinchronisasi pencetus, dan kamera mana yang berada pada talian sinchronisasi. |
| `analyze_array_models` | `analyze_array_models` | Apa yang akan diperoleh oleh satu rangkaian model-model ini pada rangkaian ini. |
| `calibrate_alignment_profile` | `calibrate_alignment_profile` | Menyelesaikan dan menyimpan fail profil penjajaran (`options` mengandungi pilihan penyelesai yang lebih terperinci). |
| `capture_aligned_multiband` / `start_aligned_stream` | `capture_aligned_multiband` / `start_aligned_stream` | Imej berbilang jalur yang diselaraskan, atau aliran daripadanya sebagai satu kerja. |
| `read_alignment_profile` / `reorder_alignment_profile` | `alignment_profile_info` / `reorder_alignment_profile` | Kandungan fail profil; susunan jalur dan kamera yang diaktifkan. |
| `list_index_presets`, `compute_index`, `capture_live_index` | `index_catalog`, `compute_index`, `capture_live_index` | Indeks pratetap dan gradien; satu indeks ke atas TIFF berbilang jalur; tangkapan ke indeks dalam satu langkah. |

**Sinkronisasi masa, folder projek dan resipi** ([PTP Grandmaster](#ptp-grandmaster--time_sync_status), [Folder Projek &amp; Resipi](#project-folder--recipes))

| Alat | Panggilan SDK | Apa fungsinya |
| --- | --- | --- |
| `get_time_sync_status` | `time_sync_status`, `time_sync_peers`, `time_sync_cameras` | Kesihatan grandmaster PTP, rakan sebayanya dan status PTP setiap kamera susunan. |
| `restart_time_sync` / `set_time_sync_priority` | `restart_time_sync` / `set_time_sync_priority` | Mulakan semula; tetapkan keutamaannya. |
| `get_project_folder` / `set_project_folder` / `reset_project_folder` | `project_folder` / `set_project_folder` / `reset_project_folder` | Folder di mana projek baru dibuat. |
| `run_project_recipe` | `run_project_recipe` | Jalankan resipi projek yang disimpan, sebagai satu kerja dengan lognya. Semakan pertama: resipi yang mempunyai ralat ditolak dan tidak menjalankan apa-apa; amaran dikeluarkan untuk susunan / penderia yang tidak bersambung (ia tidak menyambungkannya: `connect_project_devices` terlebih dahulu). `dry_run` memulangkan semakan dan tidak menjalankan apa-apa. |

Tiada alat untuk membuka projek yang disimpan untuk pemprosesan: setiap kerja pemprosesan bermula dengan `create_project` (atau `process_folder`).

### Keperluan

- **Chloros dipasang pada komputer yang sama.** Backend Chloros melakukan pemprosesan. Sebaliknya, pelayan boleh menggunakan backend yang dicapai melalui terowong (`--backend-url` dengan `--no-auto-start`, lihat [Pilihan](#options)).
- **Log masuk pada komputer tersebut**, dalam aplikasi Chloros atau dengan `chloros-cli login`. Pelayan tidak boleh log masuk untuk anda: alat yang dipanggil semasa log keluar akan menjawab &quot;Tidak log masuk ke Chloros&quot; dengan sebab backend, dan pembantu akan memberitahu anda.
- **Mana-mana pelan berbayar Chloros+ (Tembaga atau ke atas).** Pelayan mempunyai akses SDK, dan pelan Iron percuma tiada akses: alat yang dipanggil pada pelan percuma menjawab &quot;Tidak tersedia pada pelan Chloros ini&quot; dengan sebab backend.
- Python 3.7 atau lebih baru dengan SDK, dan klien MCP yang memulakan pelayan tempatan (stdio).

### Kelakuan

- **Backend.** Memulakan pelayan atau menyenaraikan alatan tidak memulakan apa-apa; panggilan alat pertama akan membuat sambungan. Jika aplikasi Chloros (atau perkhidmatan backend Linux) sedang berjalan, pelayan akan menggunakan backend tersebut dan membiarkannya terus berjalan. Jika tiada backend sedang berjalan, pelayan akan memulakan backend yang dipasang, tanpa tetingkap. Apabila pelayan keluar (anda menutup klien, atau ia memulakan semula pelayan), ia menghentikan kerja tangkapan dan rakaman penderia cahaya yang dimulakannya dan membebaskan peranti yang disambungkannya; backend terus berjalan, supaya pemprosesan yang belum selesai diteruskan sehingga selesai, dan ia menghentikan sendiri selepas 30 minit jika tiada apa-apa yang menggunakannya. Dengan `--no-auto-start` pelayan tidak pernah memulakan backend: alat yang dipanggil apabila tiada backend yang berjalan akan meminta anda membuka aplikasi Chloros, dan panggilan seterusnya selepas anda melakukannya akan membuat sambungan.
- **Satu projek terbuka.** Backend mempunyai satu projek terbuka, dikongsi dengan aplikasi Chloros apabila aplikasi dibuka, jadi jangan kerjakan projek lain dalam aplikasi sementara pembantu sedang berfungsi.
- **Hanya tetapan yang dihantar akan berubah.** `configure_processing` membiarkan setiap tetapan yang tidak diberikan kekal pada nilai simpanannya (`configure()` dalam SDK menetapkan semula debayer, pembetulan vignet, penentukuran pantulan, PPK dan format eksport kepada lalai apabila tidak diberikan; pelayan mengisinya daripada projek). Jika ia tidak dapat membaca tetapan simpanan projek, ia tidak mengubah apa-apa dan memaklumkannya, melainkan kesemua lima diberikan. `indices` menggantikan senarai indeks projek. Keputusannya menyenaraikan apa yang dihantar sebagai `settings_sent`, dan menamakan di bawah `warnings` mana-mana indeks yang jalur kamera tidak dapat hasilkan.
- **Pemprosesan adalah bermula-kemudian-menyoal.** `start_processing` akan kembali sebaik sahaja larian bermula, yang kemudian mengambil masa berminit-minit hingga berjam-jam, dan pembantu meneruskannya dengan `get_processing_status`. `state` adalah `idle` (tiada pemprosesan), `starting` (backend belum melaporkan larian, sehingga 20 s), `processing`, atau `finished`, dengan larian [ringkasan](#post-run-summary--hints): apa yang dieksport, dan petunjuk apabila ia mengeksport kurang daripada yang dijangkakan (tiada imej, tiada sasaran penentukuran ditemui). Produk ditulis di bawah folder projek, seperti dari aplikasi. `start_processing` ditolak semasa sesuatu larian sedang dijalankan, dan backend yang berhenti memberi respons dilaporkan sebagai ralat, bukan sebagai larian yang selesai.
- **Apa yang dilihat oleh pembantu.** Amaran SDK dipaparkan semula dalam keputusan alat di bawah `warnings`, dan kegagalan (keluar, pelan, laluan yang salah) sebagai teks ralat yang boleh dibaca dan disampaikan kepada anda. Senarai yang lebih panjang daripada 20 item akan dipotong kepada 20 item pertamanya, dengan panjang penuh senarai itu disimpan sebagai `<key>_total`. Laluan mesti mutlak.
- **Peranti dikongsi.** Kamera, susunan dan penderia cahaya terletak di backend, jadi pembantu boleh mengawal salah satu aplikasi Chloros yang disambungkan, dan apa yang diubahnya akan dipaparkan dalam aplikasi. `disconnect_device` membebaskan apa yang disambungkan oleh pelayan; peranti yang disambungkan oleh klien lain (aplikasi, `chloros-cli`, skrip, `chloros-mcp` lain) hanya dilepaskan dengan `force: true`, iaitu `force_disconnect` daripada SDK, dan nama penolakan yang menyambungkannya apabila backend mengetahuinya; peranti yang langsung tidak dipegang oleh backend dilaporkan tidak disambungkan (`released: false`, `connected: false`, tiada apa-apa untuk dilepaskan). Rakaman atau aliran yang dijalankan oleh backend memimpin pandangannya `get_job_status` dengan `counts` — `frames_written`, `frames_dropped`, `frames_failed`, `drop_reason` (kumpulan bingkai untuk aliran berbilang kamera) — dan satu baris `summary` (&quot;220 bingkai ditulis, 55 dibuang, 0 gagal: ...&quot;), semasa ia berjalan dan, apabila selesai, memaparkan angka akhirnya; penyenaraiannya oleh klien lain (`other_clients_jobs`) memaparkan butiran yang sama (`list_cameras()`, `list_arrays()` dan `list_daq_sensors()` memaparkan setiap perantis `connected_by`: `client`, `label`, `name`, `at`). Setiap `chloros-mcp` menamakan dirinya kepada backend sebagai `chloros-mcp (pid N)` (`chloros_sdk.connect.CLIENT_LABEL`, dihantar sebagai `X-Chloros-Client-Label`; mana-mana skrip SDK boleh menetapkannya). Menyekat rakaman penderia cahaya pelanggan lain juga memerlukan `force`.
- **Kerja panjang adalah satu tugas.** `start_capture_run`, `start_array_recording`, `build_burst_video`, `start_light_sensor_recording`, `start_camera_continuous`, `start_multi_camera_stream`, `start_aligned_stream`, `run_project_recipe`, dan apa-apa tangkapan, penyelesaian atau indeks yang diminta `background`, kembalikan `job_id` serta-merta; `get_job_status` mengikutinya dan `stop_job` menamatkan ia. Satu larian diteruskan sehingga dihentikan melainkan anda memberikannya `max_captures`, `duration_s` (atau, untuk burst, `max_frames`); `min_free_gb` menolak untuk memulakan apabila cakera mempunyai ruang kosong yang kurang. Tiada had dikenakan melainkan anda menetapkan satu. Rakaman atau aliran yang dijalankan oleh backend (`start_camera_continuous`, `start_multi_camera_stream`, `start_aligned_stream`) menyimpan ID kerja backend, jadi `chloros-mcp` lain — yang dimulakan kemudian — menemuinya melalui ID itu: `get_job_status` mengikutinya (dan menyenaraikan pekerjaan seperti `other_clients_jobs`, dari `GET /api/camera/cli-jobs`, masing-masing dengan `started_by`), dan `stop_job` menghentikannya hanya dengan `force: true`. Sebarang kerja lain (jalanan tangkapan, rakaman tatasusunan atau letupan, tangkapan latar belakang) dijalankan dalam `chloros-mcp` yang memulakannya, dan hanya pelayan itu yang mengetahuinya: yang lain yang diminta berkata demikian. Satu yang dihentikan membaca `stopping` semasa ia menulis keluar barisan tugasnya, kemudian `finished` dengan kiraan akhirnya, selama 10 minit dan daripada mana-mana `chloros-mcp`: yang memulakannya melihat hentian paksa pelanggan lain dengan cara yang sama. `get_job_status` tidak pernah menunggu hentian: semasa satu hentian (`stop_job` miliknya sendiri, pelanggan lain, atau pemberhentian yang membebaskan kamera-kamera daripada kerja yang berakhir dengan sendirinya) ia menjawab serta-merta dengan `stopping` dan kiraan langsung, dan `stop_job` daripada kerja yang sedang berhenti memberitahu demikian. Kerja yang telah selesai menyatakan sebab dalam `stop_reason`: sebabnya sendiri (`no_frames`, `disk_full`, `member_missing`, atau untuk larian berterusan susunan `count`, `duration`, `array_gone`), `limit reached` hanya apabila kiraannya menunjukkan `count` (atau `max_frames` bagi burst, satu rakamans `duration_s`) dicapai, `error`, `stopped` (dengan `stopped_by`) apabila klien menghentikannya, jika tidak `finished`.
- **Tempat rakaman disimpan.** ke folder `captures` projek terbuka, di mana aplikasi menyimpannya, melainkan `output_dir` diberikan.
- **Imej.** `get_camera_preview` dan `grab_frames` mengembalikan imej yang boleh dilihat oleh pembantu, di samping JSON mereka. Imej `grab_frames` diregangkan kontrasnya untuk tontonan; statistiknya ialah nilai sebenar.
- **Apabila pelayan keluar**, kerja yang dilancarkannya akan berhenti dan rakaman penderia cahaya yang dilancarkannya akan ditutup. Jika pelayan memulakan backend, ia juga akan melepaskan peranti yang disambungkannya sebelum menghentikan backend; dalam backend aplikasi, ia kekal bersambung.
- **Protokol.** stdio sahaja (tiada hujung HTTP), alat sahaja (tiada sumber atau arahan), keputusan teks dan imej, versi protokol MCP 2024-11-05 hingga 2025-11-25.**Apa yang tidak disertakan.** Log masuk atau log keluar (akaun adalah milik anda; pelayan tidak boleh log masuk), dan menghentikan backend yang tidak dimulakan oleh pelayan. Segala perkara lain yang dilakukan oleh SDK ini dengan pemprosesan dan perkakasan mempunyai alatnya sendiri. (Provisioning DAQ-E -- namanya, tokenauth, kata laluan OTA, keperluan tandatangan, tetapan semula kilang -- dan pelayan DAQ langsung adalah milik `chloros-cli` sahaja, dalam SDK juga: nama atau token yang salah mengunci setiap pemasangan Chloros daripada unit.)

---

## Pemulaan Automatik Backend

Titik permulaan smart-connect — `connect_camera`, `connect_array`, `connect_daq_sensor`, `discover_lattice_cameras` dan `discover_daq_sensors` — adalah klien HTTP ringan yang menganggap backend sedang mendengar di `127.0.0.1:5000` (URL lalai bagi permukaan smart-connect). Apabila GUI atau CLI sudah berjalan, ia akan. Daripada skrip kosong, mungkin tiada — jadi fungsi-fungsi ini **memulakan secara automatik binari backend yang disertakan** (tanpa tetingkap, sama seperti `ChlorosLocal`) sebelum panggilan pertama mereka, kemudian menunggu sehingga 60 saat untuk ia muncul. `list_cameras()`, `list_arrays()`, `list_daq_sensors()`, `attach_array()` dan `analyze_array_network()` tidak pernah memulakannya.

Peraturan:

- **Hanya URL tempatan akan diwujudkan.** `backend_url` yang menunjuk ke `localhost` / `127.0.0.1` / `[::1]` layak; mana-mana hos lain dianggap sebagai mesin orang lain dan tidak akan pernah diaktifkan.
- **Sebarang backend yang keluar sebelum ia menjawab akan menamatkan menunggu.** Panggilan akan terus diangkat serta-merta dengan kod keluar, arahan yang dijalankan dan baris terakhir yang dicetak oleh backend, bukannya menunggu sehingga 60 saat. Kod keluar 98 adalah pengecualian: backend lain sudah memegang port atau fail kunci (aplikasi Chloros, perkhidmatan backend, atau skrip lain yang masih dimulakan), jadi panggilan terus menunggu sehingga ia memberi maklum balas. `ChlorosLocal` melakukan perkara yang sama.
- **Backend dibiarkan berjalan untuk digunakan semula** (sama seperti CLI) — tiada penutupan secara automatik apabila skrip anda keluar. Mengjalankan semula skrip akan menggunakan semula backend yang sedang aktif.
- **Batalkan dengan `auto_start_backend=False`** pada mana-mana panggilan tersebut (contohnya apabila anda telah menunjuk ke backend jauh, atau anda mengurus kitar hayat backend sendiri).

```python
import chloros_sdk

# Fresh shell, no backend running, no GUI open — this still works:
with chloros_sdk.connect_camera("213800234") as cam:   # spawns the backend
    cam.capture("output/")

# Remote backend (via tunnel — see Remote-Backend Mode): don't spawn one locally
arr = chloros_sdk.connect_array(serials,
                                backend_url="http://127.0.0.1:5000",
                                auto_start_backend=False)
```

Jika binari yang disertakan tidak dapat ditemui atau dimulakan, panggilan HTTP seterusnya akan menimbulkan `ChlorosConnectError` yang boleh ditangani dan sedar platform, bukannya jejak sambungan ditolak kosong — pada Windows ia menunjuk anda ke aplikasi desktop atau arahan `chloros-cli`; pada Linux (tanpa GUI) ia menunjuk anda ke arahan `chloros-cli` atau `.deb`.

---

## Persekitaran &amp; Header

SDK menandakan setiap panggilan HTTP belakang dengan `X-Chloros-Client: sdk`. Backend menerapkan peraturan pelesenan SDK/CLI (log masuk **dan** pelan Chloros+ berbayar diperlukan) dan bukannya laluan peringkat percuma GUI. Ini ditetapkan secara automatik semasa pengimportan — anda tidak perlu melakukan apa-apa.

Header ini ditambah pada setiap `requests` / panggilan `urllib` yang URL-nya mengandungi `localhost` atau `127.0.0.1`, pada mana-mana port — termasuk perkhidmatan tempatan anda sendiri — dan menggantikan nilai yang anda tetapkan sendiri. Panggilan ke hos lain tidak terjejas.

Tetapkan semula URL backend dengan menghantar `backend_url=` (`ChlorosLocal` mengambil `backend_url=` atau `api_url=`; `process_folder()` dan `process_lattice_capture()` hanya mengambil `api_url=`):

```python
chloros_sdk.connect_camera("213800234", backend_url="http://127.0.0.1:5000")
chloros_sdk.connect_array(serials, backend_url="http://127.0.0.1:5000")
chloros_sdk.connect_daq_sensor(eth_host="daq-e-1.local",
                                backend_url="http://127.0.0.1:5000")
chloros_sdk.ChlorosLocal(backend_url="http://127.0.0.1:5000")
```

(Backend yang dipasang hanya mendengar pada loopback, jadi LAN `backend_url` tidak dapat mencapainya; lihat [Mod Backend Jauh](#remote-backend-mode-pip-only-host-via-tunnel) untuk corak terowong.)

---

## Versi &amp; Kebolehpadanan

- Versi SDK didedahkan sebagai `chloros_sdk.__version__`.
- SDK menetapkan tingkah laku mengikut versi backend yang disertakan. Menggabungkan SDK yang lebih lama dengan backend yang lebih baru biasanya berfungsi (titik akhir yang serasi ke hadapan), tetapi menggabungkan SDK yang lebih baru dengan backend yang lebih lama mungkin menyebabkan ralat `404` pada titik akhir baru — naik taraf aplikasi desktop untuk menyamakan.
- Antara muka smart-connect (`connect_camera` / `connect_array` / `connect_daq_sensor`) dan endpoint analisis rangkaian mengembalikan skema JSON yang stabil; medan baru adalah tambahan.

---

## Petunjuk Penyelesaian Masalah

- **`ChlorosAuthenticationError: Login required`** (daripada `ChlorosLocal`; panggilan smart-connect menimbulkan `ChlorosConnectError: Login required. …` atas sebab yang sama) → Jalankan `chloros-cli login EMAIL PASSWORD` sekali pada mesin ini, atau log masuk melalui aplikasi desktop Chloros.
- **`ChlorosProcessingError: Failed to start processing: … The project folder is too deep for this computer …`** (atau `Failed to create project: …` daripada `create_project()`) → Laluan panjang Windows telah dilumpuhkan pada mesin ini (laluan panjang adalah lalai Windows), sehingga tiada laluan boleh melebihi 260 aksara, dan produk diletakkan sehingga kira-kira 130 aksara di bawah folder projek. Tiada apa yang diproses. Mesej itu memberikan folder projek terpanjang yang muat: gunakan yang lebih pendek, atau hidupkan laluan panjang Windows dan mulakan semula Chloros. Pemasang Windows menawarkan ini sebagai &quot;Dayakan laluan panjang Windows&quot;, yang dipilih secara lalai; jika tidak, gunakan Dasar Kumpulan: *Konfigurasi Komputer &gt; Templat Pentadbiran &gt; Sistem &gt; Sistem Fail &gt; Dayakan laluan panjang Win32*.
- **`ChlorosConnectError: No Chloros backend is running …`** → Smart-connect memanggil permulaan automatik backend tempatan, jadi ini hanya muncul apabila binari yang disertakan tidak dapat ditemui/dimulakan (contohnya piphos sahaja tanpa pakej desktop). Pesanannya sedar platform: pada Windows buka aplikasi desktop atau jalankan mana-mana arahan `chloros-cli`; pada Linux jalankan arahan `chloros-cli` (tiada GUI wujud) atau pasang `.deb`. Untuk backend jauh, hantar `backend_url=` (dan `auto_start_backend=False`).
- **`CAMERA_AVAILABLE == False`** semasa import → `lattice_sdk` gagal memuatkan: runtime Arena SDK, atau OpenCV (`cv2`), tidak boleh diimport dalam Python ini. Permukaan yang dikuasakan oleh backend (`connect_*`, projek melalui backend, `ChlorosLocal`) masih berfungsi.
- **Susunan menyambung pada sensor penuh tetapi kadar bingkai rendah**→ Secara reka bentuk: auto mengekalkan sensor penuh dan membiarkan kadar jatuh mengikut apa yang boleh dibawa oleh wayar. Pass `binning=2` / `4` atau `width` / `height` yang lebih kecil untuk lebih banyak bingkai sesaat, atau aktifkan bingkai jumbo / naik taraf pautan. Binning dan ROI**tidak** merangkumi oversubscription agregat (`oversubscribed: true`, medan fps 0): Terlalu banyak kamera untuk sambungan tidak dapat diperbaiki dengan binning/ROI — kurangkan bilangan kamera, aktifkan bingkai jumbo, atau beralih ke NIC yang lebih pantas (lihat [Over-Subscription](#over-subscription-the-per-cam-floor)).
- **`analyze_array_network()` melaporkan cincin RX NIC sebagai terlalu kecil (~0.26 MB) / pintu sambungan dengan &quot;FRAMES WILL DROP&quot;** → Cincin penerima NIC hos berada pada lalai (selalunya diatur semula kepada 32 selepas kemas kini pemacu NIC). Pada penyesuai Realtek USB 10GbE tetapkan `ReceiveBufferLen=256` dan `PendingReceives=64` (dengan kebenaran tinggi), kemudian mulakan semula backend supaya ia membaca semula cincin tersebut. Prosedur penuh: [Rujukan CLI → Penyediaan &amp; Penalaan NIC Host](cli-reference.md#host-nic-setup--tuning-lattice-arrays).
- **Host tergantung semasa but semula/tutup, kemudian ralat WMI `Invalid class` / NIC tidak akan diaktifkan** → Pemacu USB 10GbE lapuk menyebabkan `DRIVER_POWER_STATE_FAILURE` (BSOD `0x9F`). Kemas kini pemacu penyesuai ke versi semasa (≥ 2026) dan terapkan semula tetapan cincin penerimaan. Rujuk [Rujukan [CLI] → Penyediaan &amp; Penalaan NIC Host](cli-reference.md#host-nic-setup--tuning-lattice-arrays).
- **Penangkapan susunan gagal dengan HTTP 503 &quot;Tiada kumpulan bingkai diselaraskan baharu&quot;** → Susunan tidak menerbitkan sebarang kumpulan bingkai baharu dalam tempoh menunggu. Semak `arr.status()`: `stalled: True` (atau `streaming: False`) dengan `actual_fps` 0 bermaksud gelung grab telah berhenti menerbitkan — putuskan sambungan dan sambungkan semula rangkaian. Jika `stalled` adalah False dan `actual_fps` melebihi 0, cuba semula. Segera selepas `connect_array()`, tangkapan sudah menunggu sehingga 20 s untuk kumpulan pertama.
- **`status()` menunjukkan `link_speed_mbps: 4294`** → Windows tidak dapat membaca kelajuan penyesuai; ini adalah perkara biasa bagi penyesuai Ethernet USB, dan bukan satu kerosakan. Lihat [Kadar Bingkai Yang Dijangkakan](#expected-frame-rates).
- **`update_firmware()` gagal dengan `device never connected back on TCP <port>`** → Tembok api menghalang sambungan DAQ-E kembali ke komputer ini. PemasangWindowsmenambah peraturan tersebut (lihat [Seting Peranti DAQ-E](#daq-e-device-settings)); jika menggunakan tembok api lain, benarkan TCP masuk ke backend dari rangkaian kecil penderia.
- **Reflectance ditolak** → DAQ langsung mesti diikat kepada kamera (atau susunan) untuk pantulan skala mutlak. Sama ada ikat melalui GUI atau gunakan `processing="radiance"` (W/m²/sr/nm) yang tidak memerlukan sensor berpasangan.
- **Pengambilan `smart=True` mengambil masa lebih lama daripada yang dijangkakan** → Konvergens AE bergantung pada dinamik adegan. Dengan `ArrayHandle.capture_smart()`, luaskan `exposure_tolerance_pct`, pendekkan `stability_window_s` atau turunkan `settle_timeout_s` untuk pencetus yang lebih pantas dan kurang stabil.

---

## Lihat Juga

- [Rujukan CLI](cli-reference.md) — setiap subperintah CLI mencerminkan panggilan SDK.
- [Penderia Cahaya DAQ](https://mapir.gitbook.io/chloros/daq-light-sensors) dalam manual Chloros — penderia, penutup dan julat kalibrasi, rakaman, rangkaian DAQ-E.
- [Kamera LATTICE](https://mapir.gitbook.io/chloros/lattice-cameras/lattice) dalam manual Chloros — penyambungan, tetapan kamera, mod tangkapan, tatasusunan.
- Dokumen dalam talian: `https://mapir.gitbook.io/chloros/api-python-sdk`
