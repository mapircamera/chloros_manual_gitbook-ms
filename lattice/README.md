# Kamera LATTICE

LATTICE ialah sistem kamera multispektral modular MAPIR untuk pengimejkan pertanian dan saintifik. Setiap kamera LATTICE dibina berdasarkan sensor global-shutter Sony IMX265 (**

3.1 MP, piksel 3.45 µm**) dan disambungkan melalui Ethernet sebagai peranti**GigE Vision**.

Chloros 1.3.0 mengawal kamera LATTICE secara langsung — penemuan, pratonton langsung, tangkapan, dan susunan pelbagai kamera bersepadu — daripada tiga permukaan:

| Permukaan | Di mana                                                          | Platform                                                |
| ---------- | -------------------------------------------------------------- | -------------------------------------------------------- |
| GUI        | Tab **Kamera** dalam bar sisi Chloros                                  | Windows 10/11 x64                                        |
| CLI        | Keluarga arahan `chloros-cli lattice`                           | Windows 10/11 x64, Linux x86\_64, Linux aarch64 (Jetson) |
| Python SDK | `chloros_sdk.connect_camera()` / `chloros_sdk.connect_array()` | Windows 10/11 x64, Linux x86\_64, Linux aarch64 (Jetson) |

> **Mencari perkakasan?**Modul kamera, lensa, penapis dan jalur, bingkai dan pemasangan, kabel, PoE dan pendawaian pencetus didokumenkan dalam [**manual pengguna LATTICE**](https://mapir.gitbook.io/lattice-camera). Bab ini merangkumi pengendalian kamera daripada Chloros.

Rakaman LATTICE adalah fail standard `.tif`/`.tiff`, dan Chloros sentiasa memprosesnya bermula daripada rakaman mentah. Rujuk [Rujukan [CLI]](../reference/cli-reference.md) dan [Rujukan [SDK]](../reference/sdk-reference.md) untuk keseluruhan permukaan arahan dan API.

## Dua konfigurasi sensor

| Konfigurasi | Sensor       | Penapis                                | Apa yang disampaikan oleh satu kamera                                          |
| ------------- | ------------ | ------------------------------------- | ----------------------------------------------------------------- |
| **M3C**| warna Bayer | penapis jalur-lepasan tiga                |**Tiga jalur yang dikalibrasi daripada satu pendedahan tunggal**                 |
| **M3M**| Monochrom   | penapis gangguan jalur sempit tunggal |**Satu jalur kalibrasi**; gabungkan beberapa kamera M3M untuk indeks |

Kerana kamera M3M adalah monochrom di belakang satu penapis, setiap jalur mendapat pendedahan tersendiri. Kamera M3C merangkumi ketiga-tiga jalurnya dengan satu pendedahan sensor.

## Rantaian model dan penamaan

Setiap kamera menyimpan identitinya dalam GenICam `DeviceUserID` sebagai rantaian model:

```
<sensor>-<lens>-F<filter>       e.g.  M3C-L41-FRGN,  M3M-L87-F450
```

Chloros memaparkannya dengan awalan `LATT-` (contohnya `LATT-M3M-L87-F450`). Rentetan `LATT-…` yang sama ditulis ke dalam tag EXIF `Model` bagi setiap eksport dan digunakan sebagai nama folder keluaran kamera dalam projek yang telah diproses.

| Komponen | Nilai                                                   | Maksud                                                                                            |
| --------- | -------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| Sensor    | `M3C` / `M3M`                                            | Warna Bayer / monokrom                                                                          |
| Lensa      | `L41` / `L87`                                            | Nombor adalah **medan pandangan mendatar dalam darjah**: L41 = sempit (41°), L87 = lebar (87°)    |
| Penapis    | `FRGB` / `FRGN` / `FOCN` / `FNGB` (M3C) atau `F<nm>` (M3M) | Lihat [Penapis & Julat Spektral](https://mapir.gitbook.io/lattice-camera/hardware/filters-and-bands) |

Rentetan model memacu segala-galanya seterusnya: Chloros menyelesaikan profil penderia, susun atur jalur, dan penentukuran kilang daripada `DeviceUserID` + `DeviceSerialNumber`. Tiada apa-apa yang perlu dikonfigurasikan bagi setiap kamera — lihat [Menyambungkan Kamera](connecting.md).

## Penapis dan jalur

Pusat jalur, sempadan FWHM dan keseluruhan katalog M3M 23-SKU adalah spesifikasi produk, jadi ia terdapat dalam manual perkakasan: [**Penapis & Jalur Spektral**](https://mapir.gitbook.io/lattice-camera/hardware/filters-and-bands).

Apa yang penting pada bahagian perisian: kod penapis dalam rentetan model menentukan produk mana yang boleh dibina oleh Chloros. Kamera penapis RGB (`FRGB`) hanya mengeluarkan produk debayered dan pratonton — sinaran dan pantulan bagi setiap jalur tidak bermakna untuk penderia jalur lebar, jadi Chloros melangkauinya dan menyatakan demikian. Setiap penapis lain menghasilkan rantaian penuh sinaran → pantulan → indeks.

## Kalibrasi Radiometrik Sekilas

Setiap kamera LATTICE dikalibrasi secara individu di kilang menggunakan rantaian yang boleh dijejaki ke NIST dan dihantar bersama sijil bagi setiap kamera. Apa yang diliputinya, bagaimana ia diukur dan ketepatan yang boleh anda nyatakan terdapat dalam manual perkakasan: [**Kalibrasi Radiometrik Kilang**](https://mapir.gitbook.io/lattice-camera/calibration/factory-radiometric-calibration).

Pada bahagian perisian, apa yang penting ialah Chloros menyelesaikan penentukuran yang betul apabila kamera disambungkan dan membekukan koefisien yang digunakan dalam setiap eksport — lihat [Menyambungkan Kamera](connecting.md).

## Dalam bab ini

* [Menyambungkan Kamera](connecting.md) — penemuan automatik, dialog sambungan GUI, padanan CLI/SDK, dan bagaimana penentukuran kilang diselesaikan (pakej pada kamera berbanding awan) apabila kamera disambungkan.
* [Tetapan Kamera](camera-settings.md) — setiap kawalan dalam tab Kamera: senarai kamera, panel tetapan bagi setiap kamera, dan panel tetapan susunan (termasuk penyunting ROI dan penjajaran).
* [Tetapan & Mod Rakaman](capture.md) — Rakam Semua, rakaman Tunggal / Berterusan / Selang, jenis eksport, dan susun atur folder rakaman.
* [Susunan Pelbagai Kamera](arrays.md) — wizard sambungan susunan, probe kabel penyegerakan, jalur lebar, PTP, dan paparan susunan secara langsung.
* [Kamera Mono & Indeks Vegetasi](mono-indices.md) — kamera M3M dan aliran kerja selaras → susun → indeks.

Permukaan arahan lengkap terdapat dalam [Rujukan CLI](../reference/cli-reference.md) dan [Rujukan SDK](../reference/sdk-reference.md).
