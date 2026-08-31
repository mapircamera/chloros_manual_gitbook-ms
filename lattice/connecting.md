# Menyambungkan Kamera

<figure><img src="../.gitbook/assets/image (37).png" alt=""><figcaption><p>Tab Kamera sebelum apa-apa disambungkan</p></figcaption></figure>

Chloros

menemui kamera LATTICE pada pautan secara automatik — daripada tab Kamera GUI, daripada `chloros-cli lattice`, atau daripadaPythonSDK

. Rentetan model kamera menentukan segala-galanya seterusnya:Chloros

menyelesaikan profil sensor, susun atur jalur, dan penentukuran kilang daripada `DeviceUserID` + `DeviceSerialNumber` kamera, jadi **tiada apa yang perlu dikonfigurasikan bagi setiap kamera**.

Sebelum menyambung, pastikan rangkaian hos telah disediakan — penentuan alamat link-local, bingkai jumbo dan, untuk tatasusunan, tetapan tampanan penerimaan kad antara muka rangkaian (NIC). Ini adalah persediaan pihak perkakasan dan terdapat dalam manual LATTICE: [**Persediaan Rangkaian**](https://mapir.gitbook.io/lattice-camera/setup/network-setup).

## Menyambung dari GUI

Buka tab **Kamera**dalam bar sisi**Chloros

**(tab perkakasan akan muncul setelah backend selesai dimulakan), atau gunakan menu utama →**Sambung ke Kamera**. Kedua-duanya akan membuka dialog**Sambung Kamera(s)**.

### Dialog Sambungkan Kamera(s)

Dialog ini mengimbas rangkaian sebaik sahaja ia dibuka (&quot;Mengimbas rangkaian...&quot;), dan menyenaraikan setiap kamera yang ditemuinya. Setiap baris memaparkan **model**kamera (contohnya `LATT-M3M-L41-F550`),**nombor siri**, dan**alamat IP**.

* **Klik baris untuk memilihnya**(penandaan hijau). Anda boleh memilih**beberapa kamera**dan menyambungkannya sekaligus — butang**Sambung** menyambungkannya secara bersiri.
* Baris dengan lencana **&quot;Disambungkan&quot;** sudah disambungkan dan tidak boleh dipilih semula.
* Baris dengan lencana **&quot;Dalam Susunan&quot;** tergolong dalam susunan kamera yang sedang disambungkan. Putuskan sambungan rangkaian terlebih dahulu untuk menggunakan kamera tersebut secara bersendirian.
* **Sambung** — menyambungkan kamera yang dipilih; butang akan memaparkan bilangan, contohnya &quot;Sambung (3)&quot;, apabila lebih daripada satu dipilih.
* **Imbas Semula** — menjalankan penemuan semula.
* **Tutup** — menutup dialog.
* Jika imbasan selesai tanpa sebarang keputusan, dialog akan memaparkan **&quot;Tiada kamera ditemui di rangkaian&quot;** — lihat [Pemecahan Masalah](connecting.md#troubleshooting) di bawah.

<figure><img src="../.gitbook/assets/image (38).png" alt=""><figcaption><p>Dialog Sambungkan Kamera(s) — dipaparkan di sini tanpa sebarang kamera di rangkaian</p></figcaption></figure>

### Sambungan pertama: muat turun pek penentukuran

**Untuk kali pertama** kamera tertentu disambungkan pada mesin,Chloros

akan memuat turun pek penentukuran kilang kamera (~3.8 MB) daripada kamera itu sendiri melalui GigE. Semasa proses ini dijalankan, dialog akan memaparkan panel hijau **&quot;Muat turun data penentukuran daripada kamera&quot;**dengan bar kemajuan bagi setiap siri — jangkakan lebih kurang**70 saat** bagi setiap kamera. Pakej ini disimpan dalam penimbal pada hos, jadi penyambungan semula kamera yang sama kemudian akan melangkau muat turun sepenuhnya (dan tidak akan memaparkan panel tersebut).

### Analisis Sistem

Butang **Analyze System** pada dialog akan mengimbas hos dan rangkaian (label akan bertulis &quot;Analyzing...&quot; semasa ia berjalan) dan memaparkan laporan diagnostik:

* **Hos** — teras CPU dan RAM; nama GPU dan memori, atau &quot;GPU: Tiada dikesan&quot;.
* **Antara Muka Rangkaian** — nama setiap NIC, kelajuan pautan, MTU (dengan tag &quot;jumbo&quot; jika aktif), keadaan atas/bawah, dan sama ada ia terletak pada bus USB.
* **Kamera**— siri, model, IP, dan**pada NIC mana setiap kamera dipasang**.
* **Prestasi** — fps semasa berbanding ideal bagi setiap kamera untuk format piksel, dengan baris hijau &quot;Potensi: Penambahbaikan N× mungkin&quot; apabila nilai ideal melebihi nilai semasa.
* **Amaran dan cadangan bernombor** — atau &quot;Sistem kelihatan baik untuk bilangan kamera semasa.&quot; apabila tiada apa-apa yang perlu diperbaiki.

Jalankan ia bila-bila masa pengesanan atau penstriman berkelakuan tidak dijangka — ia mengenal pasti kebanyakan masalah di pihak NIC (MTU salah, kamera pada antara muka yang salah, had penyesuai USB) tanpa meninggalkan tetingkap dialog.

### Menghubungkan satu susunan

Untuk menghubungkan dua atau lebih kamera sebagai **susunan bersepadu**, gunakan wizard sambungan susunan (**Sambungkan Susunan Kamera**) sebaliknya: Ia membimbing anda melalui pemilihan master/slave (dipelengkapkan terlebih dahulu oleh probe pendawaian GPIO), pilihan mod paparan (Jubin Berasingan vs. Digabungkan), dan satu adegan tetapan tatasusunan dengan unjuran langsung fps dan jalur lebar pendawaian yang boleh dicapai sebelum anda mengesahkannya. Aliran kerja wizard dan array dibincangkan dalam seksyen susunan kamera berbilang (multi-camera arrays) manual ini; setara bagiCLI

ialah &quot;LATTICE Camera First-Connect Workflow&quot; dalam [RujukanCLI

](../reference/cli-reference.md).

## Penyambungan daripadaCLI

danSDK



CLI

danSDK

memerlukan tahap berbayarChloros

+ dan log masuk; ini dikuatkuasakan di pihak pelayan (`401 AUTH_REQUIRED` apabila tidak log masuk, `403 PLAN_UPGRADE_REQUIRED` pada tahap percuma).

```bash
# List cameras on the network (vendor, model, serial, IP, MAC)
chloros-cli lattice info

# Single-camera smoke test: capture one frame (saves every applicable export type)
chloros-cli lattice capture -o output/

# Connect a synchronized array — same smart-prep flow as the GUI
chloros-cli lattice array-connect --serials 213800234,214000533
```

```python
import chloros_sdk

# Persistent live-camera session through the backend
with chloros_sdk.connect_camera("213800234") as cam:
    ...

# Array session (smart-prep: network probe, tier auto-pick, PTP, AE seeding, trigger config)
with chloros_sdk.connect_array(["213800234", "214000533"]) as array:
    ...
```

Tandatangan penuh, pilihan, dan aliran kerja tangkapan: [RujukanCLI

](../reference/cli-reference.md) § `chloros-cli lattice`, [RujukanSDK

](../reference/sdk-reference.md) § `connect_camera()` / `connect_array()`.

## Bagaimana penentukuran diselesaikan semasa sambungan

Setiap kamera LATTICE membawa pek kalibrasi kilang **pada kamera**, danChloros

juga memeriksa awanMAPIR

apabila kamera disambungkan:

| Situasi   | Apa yang digunakan olehChloros

                                                                                                                                                                                                          |
| ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Dalam Talian**| Kalibrasi**terbaru yang diterbitkan untuk siri tersebut** — salinan awan diutamakan berbanding salinan pada kamera. Kamera yang telah dikalibrasi semula atau dikemas kini olehMAPIR

oleh itu akan mengemas kini dirinya secara automatik; tiada tindakan pengguna diperlukan. |
| **Luar Talian**|**Pakej pada kamera**, seperti sedia ada. Aliran kerja sepenuhnya luar talian terus berfungsi; ia hanya tidak akan mengambil kalibrasi baharu sehingga kamera disambungkan ke talian sekali (atau diflash semula di kilang). |

Pada masa tangkapan, koefisien yang sebenarnya digunakan **dibekukan ke dalam metadata XMP setiap imej**. Kemas kini penentukuran kemudian tidak akan mengubah secara senyap imej yang telah anda rakam — memproses semula tangkapan lama menggunakan koefisien yang dicop dalam XMPnya, bukan apa yang paling baru hari ini.

## Penyelesaian Masalah

* **&quot;Tiada kamera ditemui di rangkaian&quot;**— sahkan tetapan link-local dalam [Network Setup](https://mapir.gitbook.io/lattice-camera/setup/network-setup): hos NIC statik `169.254.x.x/16`, kamera pada pautan yang sama, tiada DHCP/gateway dijangka. Kemudian gunakan**Analyze System**dalam dialog sambungan untuk memeriksa NIC mana setiap kamera kelihatan (atau tidak).**Rescan** selepas sebarang perubahan kabel atau NIC.
* **Peralatan yang sebelum ini berfungsi enggan menyambung** (pintu panel tatasusunan dengan `FRAMES WILL DROP` / `Reduce ROI to enable`) — kemas kini pemacu NIC telah menetapkan semula tetapan cincin-penerima secara senyap. Terapkan semula tetapan tersebut atau jalankan `chloros-cli lattice network --fix` dari terminal yang dinaikkan taraf; lihat [Penyediaan Rangkaian](https://mapir.gitbook.io/lattice-camera/setup/network-setup).
* **Kamera memaparkan &quot;In Array&quot;** — ia milik sesi array yang disambungkan. Putuskan sambungan array untuk menggunakan kamera secara berdiri sendiri.
