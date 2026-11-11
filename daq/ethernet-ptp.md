# Rangkaian DAQ-E &amp; Penyelarasan Masa

> Penyediaan rangkaian fizikal untuk penderia — penarahan kabel, PoE, penugasan IP dan tetapan rangkaian peranti itu sendiri — terdapat dalam **[manual pengguna DAQ](https://mapir.gitbook.io/daq/daq-e/network-setup)**. Halaman ini merangkumi bahagian**[DAQ user manual](https://mapir.gitbook.io/daq/daq-e/network-setup)**: penyambungan, penyelarasan masa, dan apa yang perlu dilakukan apabila penemuan tidak menemui sebarang peranti.

DAQ-E adalah ahli Ethernet dalam keluarga DAQ: dikuasakan melalui PoE, ditemui melalui mDNS (perkhidmatan `_daq-e._tcp`), dan boleh dialamatkan dengan nama hos yang diambil daripada ID penderiaannya — `daq-e-<6 hex>.local`, contohnya `daq-e-def330.local`. Halaman ini menerangkan bagaimana ia memindahkan data di rangkaian dan bagaimana ia mengambil bahagian dalam penyelarasan masa PTP.

## Mod Pengangkutan

| Mod | Titik akhir | Pengguna | Nota |
| --- | --- | --- | --- |
| **Multicast** (laluan lalai) | UDP `239.10.10.10:5002` | Sebarang bilangan pada LAN yang sama menerima aliran yang sama | Setiap datagram disahkan CRC-16/CCITT |
| **Mentah** | Pelabuhan TCP `5000` | Tepat satu klien (eksklusif) | Serasi bait secara langsung dengan DAQ-U |

Chloros

menggunakan multicast secara lalai, yang membolehkan GUI, CLI

, dan SDK

semua memerhati satu penderia pada satu masa.

## Keperluan rangkaian

* **Domain siaran yang sama.** Mesin yang menjalankan Chloros

mesti berada dalam segmen rangkaian L2 yang sama seperti penderia — penemuan mDNS tidak menembusi penghala.
* **Tembok api.** Pada Windows

, pemasang menambah peraturan tembok api **Chloros

Backend (UDP-In)**untuk backend, pada setiap profil rangkaian, yang merangkumi data DAQ-E (UDP 5002), mDNS (UDP 5353) dan PTP (UDP 319/320), dan peraturan**Chloros

Backend (TCP-In)**, terhad kepada subnet tempatan, untuk sambungan yang dibuat oleh DAQ-E kembali ke komputer semasa kemas kini firmware. Pada Linux

, `.deb` membuka port UDP dalam `ufw` atau `firewalld` apabila firewall tersebut diaktifkan semasa pemasangan. Firewall pihak ketiga, atau yang diaktifkan kemudian, memerlukan port yang sama dibenarkan.
* **Kuasa PoE, tiada LED status.** DAQ-E tidak mempunyai LED sendiri — sahkan kuasa melalui penunjuk pautan/PoE pada port suis atau penyuntik, dan biarkan beberapa saat selepas dihidupkan supaya ia boleh memulakan dan menyertai rangkaian.

## Menyambung

**GUI:** Tab Penderia Cahaya → Sambung Penderia → Jenis Peranti &quot;DAQ-E (Ethernet)&quot;. Penemuan hanya dijalankan selagi dialog sambungan berada di skrin (penjelajahan mDNS ditambah imbasan ARP pada Windows

), diulang setiap 15 saat; butang Segarkan mengimbas semula dengan segera. Penderia yang ditemui akan muncul dalam senarai lungsur; penderia pertama yang dikesan akan dipilih secara automatik.

<!-- SCREENSHOT-NEEDED: DAQ connect dialog with Device Type set to "DAQ-E (Ethernet)" and at least one discovered sensor listed in the Hostname/IP dropdown (e.g. daq-e-xxxxxx.local), Connect button enabled. -->

**CLI** (backend yang berjalan):

```bash
chloros-cli daq pool-connect --eth                              # auto-discover on the LAN
chloros-cli daq pool-connect --eth-host daq-e-def330.local      # explicit host — the reliable form
chloros-cli daq pool-connect --eth-host 192.168.1.57            # a plain IP works too
```

DAQ-E yang sudah ada dalam kolam dikenali sama ada melalui nama hos atau IPnya: menyambungkannya semula menggunakan alamat yang lain akan memaparkan entri kolam sedia ada dan bukannya membuka unit itu buat kali kedua.

### Hos berbilang NIC dan sambungan pertama selepas but

Pada hos dengan lebih daripada satu antara muka rangkaian aktif, **pertama** `pool-connect --eth` selepas but boleh menjadi kosong walaupun sensor sihat — penjelajahan penemuan boleh terlepas antara muka tempat sensor berada sementara cache ARP masih belum diisi. Cara yang boleh dipercayai ialah dengan melangkau penemuan dan memasukkan alamat secara eksplisit:

```bash
chloros-cli daq pool-connect --eth-host daq-e-def330.local
```

`--eth-host` menerima nama hos mDNS atau IP, sentiasa menyasarkan sensor yang betul, dan merupakan bentuk yang disyorkan untuk skrip dan pemasangan tanpa kepala. Dalam GUI, gunakan butang Segarkan pada dialog sambungan dan benarkan kitaran imbasan semula.

## Tetapan peranti dan firmware

Pengesan itu sendiri menyimpan tetapan rangkaiannya: DHCP (tetapan kilang, akan bertukar kepada alamat pautan-lokal 169.254.x.x apabila tiada pelayan DHCP yang menjawab) atau alamat statik. Ia juga menyimpan sama ada ia mula menstrim semasa kuasa dihidupkan. Ubahnya dalam GUI (tetapan gear sensor, **Rangkaian**:**Ubah…**,**Strem semasa kuasa-pada**,**Mulakan semula penderia**; lihat [Tab DAQ](gui.md)), atau daripada CLI

dan SDK

dengan `pool-set-network`, `pool-set-auto-stream` dan `pool-reboot` (`DAQSensorSession.set_network()`, `set_auto_stream()`, `reboot()`; lihat [Rujukan CLI

](../reference/cli-reference.md#daq-e-device-settings)). Penderia dihidupkan semula untuk menerapkan perubahan rangkaian, dan Chloros

menyambungkannya semula. Alamat statik mesti ada di rangkaian komputer yang menjalankan Chloros

: Chloros

menolak alamat yang tidak dapat dihubungi selepas pengaktifan semula. ID penderia dan nama hos `daq-e-<id>.local` tidak pernah berubah. Baharu dalam 1.3.0.

**Kemas kini firmware terbina dalam GUI.** Apabila DAQ-E yang disambungkan menjalankan firmware yang lebih lama daripada imej yang disertakan dengan binaan Chloros

anda, baris sensornya memaparkan pil **Kemas Kini Tersedia** berwarna amber, dan modal tetapan gear menawarkan <version>butang</version>

&quot;Kemas Kini ke <version>&quot;. Kemas kini disiarkan melalui rangkaian dalam masa kira-kira 30 saat; sensor dihidupkan semula dan disambungkan semula secara automatik, dan pemindahan yang terganggu tidak menjejaskan firmware semasa. Daripada CLI atau SDK, dan pada Linux, yang tiada GUI, jalankan `chloros-cli daq pool-update-firmware --sensor-id <id>` (`DAQSensorSession.update_firmware()`).

<!-- SCREENSHOT-NEEDED: DAQ-E per-sensor settings modal showing the DAQ-E-only rows: Hostname/IP, Firmware row with the "Update to <ver>" button (or "Up to date"), and the PTP Sync row with a live state value. -->

## Penyegerakan masa PTP

Firmware DAQ-E v1.2.0+ menyertai IEEE 1588 PTPv2 sebagai jam biasa (hanya hamba). **Backend hos Chloros ialah grandmaster PTP** — setiap DAQ-E dan setiap kamera LATTICE di LAN menjadi hamba kepadanya dalam domain 0, mengekalkan semua cop masa peranti dalam toleransi kira-kira 1 ms. Jam kongsi itulah yang membolehkan bacaan DAQ dipadankan waktunya dengan pendedahan kamera (lihat [Perekodan &amp; Format .daq](recording.md)).

Periksa penyelarasan daripada CLI:

| Perintah | Menunjukkan |
| --- | --- |
| `chloros-cli time-sync status` | Status grandmaster hos, keutamaan BMCA, identiti jam, dan `health`: `OK`, atau `SENDS_FAILING` / `DEGRADED` / `NO_SOCKETS` / `PEERS_SILENT` dengan `health_detail` yang menyatakan mengapa paketnya tidak sampai ke rangkaian |
| `chloros-cli time-sync peers` | Setiap hamba yang dilihat (penderia DAQ-E + kamera LATTICE) |
| `chloros-cli time-sync cameras` | Kesihatan PTP bagi setiap kamera (`PtpStatus`, `PtpOffsetFromMaster`, `PtpMeanPathDelay`) |
| `chloros-cli time-sync restart` | Mulakan semula proses grandmaster |

Dalam GUI, modal tetapan DAQ-E memaparkan baris **PTP Sync** secara langsung dengan keadaan PTP semasa sensor.**DAQ-E yang PTP-nya dimatikan akan kekal dimatikan.** Peranti ini menyimpan tetapan PTP hidup/mati dalam memorinya sendiri, jadi peranti yang PTP-nya dimatikan akan kekal dimatikan selepas disambung semula atau dihidupkan semula, dan cap waktunya tidak diselaraskan mengikut PTP. `chloros-cli daq pool-list` menandakan unit sedemikian (`!! PTP is switched OFF on this unit …`) dan mencetak arahan yang membetulkannya:

```bash
chloros-cli daq pool-enable-ptp --sensor-id daq-e-def330
```

Chloros tidak pernah menghidupkan semula unit secara automatik, kerana keadaannya boleh dimatikan secara sengaja.

Butiran untuk pengguna penjajaran ketat:

* Setiap datagram yang disalurkan membawa medan bendera; **bit 2 ditetapkan pada bingkai yang cap waktunya diselaraskan dengan PTP**. Saluran yang memerlukan penjajaran ketat kamera/DAQ harus mengawal berdasarkan bit tersebut.
* Sebelum pengambilan bersepadu, sahkan sensor muncul dalam `chloros-cli time-sync peers` dan `chloros-cli daq pool-list` tidak menandakan PTP-nya sebagai dimatikan. (Peralatan dalaman perkakasan terus MAPIR juga boleh mengawal rakaman berdasarkan kunci PTP dengan penanda `--wait-ptp` yang menunggu sehingga 15 saat untuk sensor mencapai keadaan SLAVE; peralatan tersebut bukan sebahagian daripada CLI yang diedarkan.)
* Semasa PTP sedang menjadi hamba, sensor menolak tolakan jam manual (&quot;PTP menyediakan jam&quot;). Ini adalah reka bentuknya — percayalah kepada PTP.

## Catatan Linux

* **Port PTP tanpa root.** PTP mengikat UDP 319/320. `.deb` menulis `/etc/sysctl.d/60-chloros-ptp.conf` (`net.ipv4.ip_unprivileged_port_start = 319`), jadi backend mengikatnya dengan menjalankan sebagai pengguna anda. Mulai versi 1.3.0, pakej ini tidak menetapkan keupayaan fail (`setcap`) pada backend dan tidak memerlukan `libcap2-bin`; jangan tambahkan keupayaan sendiri, kerana backend yang tidak dikemas akan gagal bermula. Jika PTP tidak dapat mengikat port 319, lihat [Pemasangan Linux](../linux/linux-installation.md#ptp-will-not-start--cannot-bind-port-319).

* **Jetson tanpa kepala / hos Linux:** pakej ini memasang perkhidmatan backend tetapi tidak mengaktifkan mana-mana. Untuk PTP sentiasa aktif (dan ketersediaan DAQ) tanpa sesiapa yang menjalankan arahan, aktifkan perkhidmatan sebagai pengguna anda:

  ```bash
  sudo systemctl enable --now chloros-backend@$USER.service    # $USER expands to your user name
  ```

  Tanpa ia, PTP hanya berjalan selagi sesuatu telah memulakan backend — arahan `chloros-cli` atau skrip SDK. Lihat [Pemasangan [Linux]](../linux/linux-installation.md#always-on-ptp-for-headless-hosts) untuk `chloros-backend.service` root dan cara beralih daripadanya.

## Penyelesaian Masalah: &quot;Tiada peranti DAQ-E ditemui&quot;

| Periksa | Perincian |
| --- | --- |
| Kuasa | Tiada LED pada sensor — semak penunjuk PoE dan pautan port suis/penyuntik; tunggu beberapa saat selepas dihidupkan |
| Domain siaran | Host dan sensor pada segmen L2 yang sama; mDNS tidak dialihkan |
| Firewall | Windows: peraturan **Chloros Backend (UDP-In)** pemasang membenarkan aliran DAQ-E (UDP 5002), mDNS (5353) dan PTP (319/320) masuk; firewall pihak ketiga memerlukan perkara yang sama. Linux: `.deb` membuka port ini dalam `ufw` / `firewalld` hanya jika firewall aktif semasa pemasangan — benarkan ia sendiri dalam satu yang diaktifkan kemudian |
| Host Multi-NIC | Penemuan melayari mDNS dengan sandaran ARP; `--eth-host <ip-or-hostname>` melangkau penemuan dan menyambung terus |
| Imbas semula GUI | Penemuan hanya berjalan selagi dialog sambungan terbuka; gunakan Segarkan |</version>
