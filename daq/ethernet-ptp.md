# Rangkaian DAQ-E &amp; Penyelarasan Masa

> Penyediaan rangkaian fizikal untuk penderia — pengekabelan, PoE, penugasan IP dan tetapan rangkaian peranti itu sendiri — terdapat dalam **[manual pengguna DAQ](https://mapir.gitbook.io/daq/daq-e/network-setup)**. Halaman ini merangkumi bahagian rangkaian DAQ-E: penyambungan, penyelarasan masa, dan apa yang perlu dilakukan apabila penemuan tidak menemui sebarang peranti.

DAQ-E adalah ahli Ethernet dalam keluarga DAQ: dikuasakan melalui PoE, dikesan melalui mDNS (perkhidmatan `_daq-e._tcp`), dan boleh dialamatkan dengan nama hos yang diambil daripada ID penderiaannya — `daq-e-<6 hex>.local`, contohnya `daq-e-def330.local`. Halaman ini menerangkan bagaimana ia memindahkan data di rangkaian dan bagaimana ia menyertai penyelarasan masa PTP.

## Mod Pengangkutan

| Mod | Titik hujung | Pengguna | Nota |
| --- | --- | --- | --- |
| **Multicast** (laluan lalai) | UDP `239.10.10.10:5002` | Sebarang bilangan pada LAN yang sama menerima aliran yang sama | Setiap datagram disahkan CRC-16/CCITT |
| **Mentah** | Pelabuhan TCP `5000` | Tepat satu klien (eksklusif) | Serasi bait secara langsung dengan DAQ-U |

Chloros

menggunakan multicast secara lalai, yang membolehkan GUI,CLI

, danSDK

semua memerhati satu penderia pada satu masa.

## Keperluan rangkaian

* **Domain siaran yang sama.** Mesin yang menjalankanChloros

mesti berada dalam segmen rangkaian L2 yang sama dengan penderia — penemuan mDNS tidak merentasi penghala.
* **Prompt tembok apiWindows

: terimanya.** Pada kali pertamaChloros

mengikat soket multicast,Windows

Defender akan bertanya sekali. Mengizinkannya merangkumi data DAQ-E (UDP 5002), mDNS (UDP 5353), dan PTP (UDP 319/320). PadaLinux

ini dilakukan secara senyap.
* **Kuasa PoE, tiada LED status.** DAQ-E tidak mempunyai LED tersendiri — sahkan kuasa melalui penunjuk pautan/PoE pada port suis atau penyuntik, dan tunggu beberapa saat selepas dihidupkan supaya ia boleh memulakan dan menyertai rangkaian.

## Menyambung

**GUI:** Tab Penderia Cahaya → Sambungkan Penderia → Jenis Peranti &quot;DAQ-E (Ethernet)&quot;. Penemuan hanya dijalankan selagi dialog sambungan dipaparkan di skrin (melayari mDNS dan imbasan ARP padaWindows

), berulang setiap 15 saat; butang Segarkan mengimbas semula dengan segera. Penderia yang ditemui akan muncul dalam senarai lungsur; penderia pertama yang dikesan akan dipilih secara automatik.

<!-- SCREENSHOT-NEEDED: DAQ connect dialog with Device Type set to "DAQ-E (Ethernet)" and at least one discovered sensor listed in the Hostname/IP dropdown (e.g. daq-e-xxxxxx.local), Connect button enabled. -->

**CLI** (backend sedang berjalan):

```bash
chloros-cli daq pool-connect --eth                              # auto-discover on the LAN
chloros-cli daq pool-connect --eth-host daq-e-def330.local      # explicit host — the reliable form
chloros-cli daq pool-connect --eth-host 192.168.1.57            # a plain IP works too
```

### Hos berbilang NIC dan sambungan pertama selepas but

Pada hos dengan lebih daripada satu antara muka rangkaian aktif, `pool-connect --eth` **pertama** selepas but boleh menjadi kosong walaupun sensor sihat — carian imbasan boleh terlepas antara muka tempat sensor berada sementara cache ARP masih sejuk. Cara yang boleh dipercayai ialah dengan melangkau penemuan dan memasukkan alamat secara eksplisit:

```bash
chloros-cli daq pool-connect --eth-host daq-e-def330.local
```

`--eth-host` menerima nama hos mDNS atau IP, sentiasa menyasarkan sensor yang betul, dan merupakan bentuk yang disyorkan untuk skrip dan pemasangan tanpa kepala. Dalam GUI, gunakan butang Refresh pada dialog sambungan dan benarkan kitaran imbasan semula.

## Tetapan peranti dan firmware

Penderia itu sendiri menyimpan tetapan rangkaian — IP statik berbanding DHCP + alamat pautan-lokal, nama peranti, penstriman automatik semasa but, kata laluan OTA. Tetapan di pihak peranti ini tidak dipaparkan sebagai arahan dalamCLI

yang disertakan; ia diuruskan melalui GUIChloros

di mana ia dipaparkan, atau dengan sokonganMAPIR

.

Kemas kini firmware terbina dalam GUI. Apabila DAQ-E yang disambungkan menjalankan firmware lebih lama daripada imej yang disertakan dengan binaanChloros

anda, barisan sensornya akan memaparkan pil amber **Kemas Kini Tersedia**, dan modal tetapan gear menawarkan <version>butang</version>

&quot;Kemas Kini ke <version>&quot;. Kemas kini diflask melalui rangkaian dalam kira-kira 30 saat; sensor akan dihidupkan semula dan disambungkan semula secara automatik, dan pemindahan yang terganggu tidak menjejaskan firmware semasa.

<!-- SCREENSHOT-NEEDED: DAQ-E per-sensor settings modal showing the DAQ-E-only rows: Hostname/IP, Firmware row with the "Update to <ver>" button (or "Up to date"), and the PTP Sync row with a live state value. -->

## Penyelarasan masa PTP

Firmware DAQ-E v1.2.0+ menyertai IEEE 1588 PTPv2 sebagai jam biasa (hanya hamba). Backend hos Chloros ialah grandmaster PTP — setiap DAQ-E dan setiap kamera LATTICE di LAN menjadi hamba kepadanya dalam domain 0, mengekalkan semua cop masa peranti dalam toleransi kira-kira 1 ms. Jam kongsi itulah yang membolehkan cap masa bacaan DAQ dipadankan dengan pendedahan kamera (lihat [Perekodan &amp; Format .daq](recording.md)).

Periksa penyelarasan daripada CLI:

| Perintah | Menunjukkan |
| --- | --- |
| `chloros-cli time-sync status` | Status grandmaster hos, keutamaan BMCA, identiti jam |
| `chloros-cli time-sync peers` | Setiap peranti bawahan yang dilihat (penderia DAQ-E + kamera LATTICE) |
| `chloros-cli time-sync cameras` | Kesihatan PTP bagi setiap kamera (`PtpStatus`, `PtpOffsetFromMaster`, `PtpMeanPathDelay`) |
| `chloros-cli time-sync restart` | Mulakan semula proses grandmaster |

Dalam GUI, modal tetapan DAQ-E memaparkan baris **PTP Sync** secara langsung dengan keadaan PTP semasa sensor.

Butiran untuk pengguna penjajaran ketat:

* Setiap datagram yang disalurkan membawa medan bendera; **bit 2 diaktifkan pada bingkai yang cap waktunya diselaraskan dengan PTP**. Saluran yang memerlukan penjajaran ketat kamera/DAQ harus mengawal berdasarkan bit tersebut.
* Sebelum pengambilan bersepadu, pastikan sensor muncul dalam `chloros-cli time-sync peers`. (Peralatan perkakasan langsung dalaman MAPIR juga boleh mengawal rakaman berdasarkan kunci PTP dengan bendera `--wait-ptp` yang menunggu sehingga 15 saat untuk sensor mencapai keadaan SLAVE; peralatan tersebut bukan sebahagian daripada CLI yang diedarkan.)
* Semasa PTP sedang menjadi hamba (slaving), sensor menolak arahan menolak jam secara manual (&quot;PTP menyediakan jam&quot;). Ini adalah reka bentuk — percayalah kepada PTP.

## Nota Linux

* **PTP memerlukan `libcap2-bin` semasa pemasangan.** Postinst `.deb` memberikan hak `cap_net_bind_service=+ep` pada `/usr/lib/chloros/chloros-backend` supaya ia boleh mengikat port PTP 319/320 tanpa hak root. Jika `libcap2-bin` tiada, langkah itu akan dilangkau dan PTP akan gagal dimulakan. Penyelesaian:

  ```bash
  sudo apt install libcap2-bin
  sudo apt reinstall chloros
  ```

* **Jetson / Raspberry Pi tanpa paparan:** pada pemasangan pertama unit systemd `chloros-backend.service` dijana tetapi tidak diaktifkan. Untuk PTP sentiasa-hidup (dan ketersediaan DAQ) tanpa GUI:

  ```bash
  sudo systemctl enable --now chloros-backend.service
  ```

  Tanpa ia, PTP hanya berjalan selagi GUI Chloros dibuka.

## Penyelesaian Masalah: &quot;Tiada peranti DAQ-E ditemui&quot;

| Periksa | Perincian |
| --- | --- |
| Kuasa | Tiada LED pada sensor — semak penunjuk PoE dan pautan port suis/penyuntik; tunggu beberapa saat selepas dihidupkan |
| Domain siaran | Host dan sensor pada segmen L2 yang sama; mDNS tidak dirute |
| Firewall Windows | Terima prompt Defender pada larian pertama (UDP 5002, 5353, 319/320) |
| Host Multi-NIC | Penemuan pertama selepas but mungkin terlepas pengesan — sambung dengan `--eth-host <ip-or-hostname>` |
| Imbas semula GUI | Penemuan hanya dijalankan semasa dialog sambungan terbuka; gunakan Segarkan |</version>
