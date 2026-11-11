# Menyambungkan Kamera

<figure><img src="../.gitbook/assets/image (37).png" alt=""><figcaption><p>Tab Kamera sebelum apa-apa disambungkan</p></figcaption></figure>

Chloros mengesan kamera LATTICE pada pautan secara automatik — daripada tab Kamera GUI, daripada `chloros-cli lattice`, atau daripada Python SDK. Rentetan model kamera menentukan segala-galanya seterusnya: Chloros menyelesaikan profil sensor, susun atur jalur, dan kalibrasi kilang daripada `DeviceUserID` + `DeviceSerialNumber` kamera, jadi **tiada apa yang perlu dikonfigurasikan bagi setiap kamera**.

Sebelum menyambung, pastikan rangkaian hos telah disediakan — penentuan alamat link-local, bingkai jumbo dan, untuk tatasusunan, tetapan buffer penerimaan NIC. Ini adalah persediaan pihak perkakasan dan terdapat dalam manual LATTICE: [**Persediaan Rangkaian**](https://mapir.gitbook.io/lattice-camera/setup/network-setup).

## Menyambung dari GUI

Buka tab **Kamera**dalam bar sisi**Chloros**(tab perkakasan akan muncul setelah backend selesai dimulakan), atau gunakan menu utama →**Sambung ke Kamera**. Kedua-duanya akan membuka dialog**Sambung Kamera(s)**.

### Dialog Sambungkan Kamera(s)

Dialog ini mengimbas rangkaian sebaik sahaja ia dibuka ("Mengimbas rangkaian..."), dan menyenaraikan setiap kamera yang ditemuinya. Setiap baris memaparkan **model**kamera (contohnya `LATT-M3M-L41-F550`),**nombor siri**, dan**alamat IP**.

* **Klik baris untuk memilihnya**(penandaan hijau). Anda boleh memilih**beberapa kamera** dan menyambungkannya sekaligus — Chloros menyambungkannya secara bersiri.
* Baris dengan lencana **"Disambungkan"** sudah disambungkan dan tidak boleh dipilih semula.
* Baris dengan lencana **"Dalam Susunan"** tergolong dalam susunan kamera yang sedang disambungkan. Putuskan sambungan susunan terlebih dahulu untuk menggunakan kamera tersebut secara bersendirian.
* **Sambung** — menyambungkan kamera yang dipilih; butang akan memaparkan bilangan, contohnya "Sambung (3)", apabila lebih daripada satu dipilih.
* **Imbas Semula** — menjalankan penemuan semula.
* **Tutup** — menutup dialog.
* Jika imbasan selesai tanpa sebarang keputusan, dialog akan memaparkan **"Tiada kamera ditemui di rangkaian"** — lihat [Pemecahan Masalah](connecting.md#troubleshooting) di bawah.

<figure><img src="../.gitbook/assets/image (38).png" alt=""><figcaption><p>Dialog Sambungkan Kamera(s) — dipaparkan di sini tanpa sebarang kamera di rangkaian</p></figcaption></figure>

### Sambungan pertama: muat turun pek penentukuran

**Untuk kali pertama** kamera tertentu disambungkan pada mesin, Chloros akan memuat turun pek penentukuran kilang kamera (~3.8 MB) daripada kamera itu sendiri melalui GigE. Semasa proses ini dijalankan, dialog akan memaparkan panel hijau **"Muat turun data penentukuran daripada kamera"**dengan bar kemajuan bagi setiap siri — jangkakan lebih kurang**70 saat** bagi setiap kamera. Dengan lebih daripada enam kamera, baris kemajuan akan beralih di dalam panel. Pakej ini disimpan dalam penimbal pada hos, jadi sambungan semula kamera yang sama kemudian akan melangkau muat turun sepenuhnya (dan tidak akan memaparkan panel tersebut).

### Analisis Sistem

Butang **Analyze System** pada dialog akan mengimbas hos dan rangkaian (labelnya akan bertulis "Analyzing..." semasa ia berjalan) dan memaparkan laporan diagnostik:

* **Host** — teras CPU dan RAM; nama GPU dan memori, atau "GPU: Tiada dikesan".
* **Antara Muka Rangkaian** — nama setiap NIC, kelajuan pautan, MTU (dengan tag "jumbo" jika aktif), status naik/turun, dan sama ada ia terletak pada bus USB.
* **Kamera**— siri, model, IP, dan**pada NIC mana setiap kamera dipasang**.
* **Prestasi** — fps semasa berbanding ideal bagi setiap kamera untuk format piksel, dengan baris hijau "Potensi: Penambahbaikan N× mungkin" apabila nilai ideal melebihi nilai semasa.
* **Amaran dan cadangan bernombor** — atau "Sistem kelihatan baik untuk bilangan kamera semasa." apabila tiada apa-apa yang perlu diperbaiki.

Jalankan ia bila-bila masa pengesanan atau penstriman berkelakuan tidak dijangka — ia mengenal pasti kebanyakan masalah di pihak NIC (MTU salah, kamera pada antara muka yang salah, had penyesuai USB) tanpa meninggalkan tetingkap dialog.

### Menghubungkan rangkaian

Untuk menghubungkan dua atau lebih kamera sebagai **rangkaian selari**, gunakan wizard sambungan rangkaian (**Sambungkan Rangkaian Kamera**) sebaliknya: ia membimbing anda melalui pemilihan tuan/hamba (dipelengkapkan terlebih dahulu oleh pengesan kabel selari — mana-mana kamera yang disambungkan dengan kabel boleh menjadi tuan, dan kamera  RGB dipilih secara automatik apabila ia disambungkan dengan kabel), pilihan mod paparan (Jubin Berasingan vs. Digabungkan), dan satu senario tetapan array dengan unjuran langsung fps dan jalur lebar kabel yang boleh dicapai sebelum anda mengesahkannya. Aliran kerja wizard dan array dibincangkan dalam seksyen susunan kamera berbilang manual ini; setara CLI ialah "LATTICE Camera First-Connect Workflow" dalam [Rujukan CLI](../reference/cli-reference.md).

## Menyambung daripada CLI dan SDK
Akses

CLI dan SDK memerlukan pelan Chloros+ berbayar dan log masuk; ini dikuatkuasakan di pihak pelayan (`401 AUTH_REQUIRED` apabila tidak log masuk, `403 PLAN_UPGRADE_REQUIRED` pada pelan percuma).

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

Tandatangan penuh, pilihan, dan aliran kerja tangkapan: [Rujukan CLI](../reference/cli-reference.md) § `chloros-cli lattice`, [Rujukan SDK](../reference/sdk-reference.md) § `connect_camera()` / `connect_array()`.

## Bagaimana penentukuran diselesaikan semasa sambungan

Setiap kamera LATTICE membawa pek penentukuran kilang **pada kamera**, dan Chloros juga menyemak awan MAPIR apabila kamera disambungkan:

| Situasi   | Apa yang digunakan oleh Chloros                                                                                                                                                                                                           |
| ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Dalam Talian**|**penentukuran terbaru yang diterbitkan untuk siri tersebut** — salinan awan diutamakan berbanding salinan pada kamera. Oleh itu, kamera yang telah ditentukur semula atau dikemas kini oleh MAPIR akan mengemas kini dirinya secara automatik; tiada tindakan pengguna diperlukan. |
| **Luar Talian**|**Pakej pada kamera**, seperti sedia ada. Aliran kerja sepenuhnya luar talian terus berfungsi; ia hanya tidak akan mengambil kalibrasi baharu sehingga kamera disambungkan ke talian sekali (atau diflash semula di kilang). |

Pada masa pengambilan, koefisien yang sebenarnya digunakan akan **dikunci ke dalam metadata XMP setiap imej**. Kemas kini penentukuran kemudian tidak akan secara senyap mengubah imej yang telah anda rakam — memproses semula rakaman lama menggunakan koefisien yang dicop dalam XMPnya, bukan apa sahaja yang terkini hari ini.

## Penyelesaian Masalah

* **"Tiada kamera ditemui di rangkaian"**— sahkan tetapan link-local dalam [Network Setup](https://mapir.gitbook.io/lattice-camera/setup/network-setup): hos NIC statik `169.254.x.x/16`, kamera pada pautan yang sama, tiada DHCP/gateway dijangka. Kemudian gunakan**Analyze System**dalam dialog sambungan untuk memeriksa NIC mana setiap kamera kelihatan (atau tidak).**Rescan** selepas sebarang perubahan kabel atau NIC.
* **Peralatan yang sebelum ini berfungsi enggan menyambung** (gate panel array dengan `FRAMES WILL DROP` / `Reduce ROI to enable`) — kemas kini pemacu NIC telah menetapkan semula tetapan cincin-penerima secara senyap. Terapkan semula tetapan tersebut atau jalankan `chloros-cli lattice network --fix` dari terminal yang dinaikkan taraf; lihat [Penyediaan Rangkaian](https://mapir.gitbook.io/lattice-camera/setup/network-setup).
* **Kamera memaparkan "In Array"** — ia milik sesi array yang disambungkan. Putuskan sambungan array untuk menggunakan kamera secara berdiri sendiri.
