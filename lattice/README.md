# Kamera LATTICE

LATTICE ialah sistem kamera multispektral modular MAPIR untuk pengimejkan pertanian dan saintifik. Setiap kamera LATTICE dibina berdasarkan sensor global-shutter Sony IMX265 (**

3.1 MP, piksel 3.45 µm**) dan disambungkan melalui Ethernet sebagai peranti**GigE Vision**.

Chloros1.2.0 mengawal kamera LATTICE secara langsung — penemuan, pratonton langsung, tangkapan, dan susunan multi-kamera bersepadu — daripada tiga permukaan:

| Permukaan    | Di mana                                                          | Platforms                                                |
| ---------- | -------------------------------------------------------------- | -------------------------------------------------------- |
| GUI        | Tab **Kamera** dalam bar sisi Chloros                         | Windows 10/11 x64                                        |
| CLI        | Keluarga arahan `chloros-cli lattice`                           | Windows 10/11 x64, Linux x86\_64, Linux aarch64 (Jetson) |
| Python SDK | `chloros_sdk.connect_camera()` / `chloros_sdk.connect_array()` | Windows 10/11 x64, Linux x86\_64, Linux aarch64 (Jetson) |

> **Mencari perkakasan?**Modul kamera, lensa, penapis dan jalur, bingkai dan pemasangan, kabel, PoE dan pendawaian pencetus didokumenkan dalam [**manual pengguna LATTICE**](https://mapir.gitbook.io/lattice-camera). Bab ini merangkumi pengendalian kamera daripada Chloros.

Sisipan LATTICE adalah fail standard `.tif`/`.tiff`, dan Chloros sentiasa memprosesnya bermula daripada tangkapan mentah. Rujuk [Rujukan CLI](../reference/cli-reference.md) dan [Rujukan SDK](../reference/sdk-reference.md) untuk arahan lengkap dan permukaan API.

## Dua konfigurasi sensor

| Konfigurasi | Sensor       | Penapis                                | Apa yang disampaikan oleh satu kamera                                          |
| ------------- | ------------ | ------------------------------------- | ----------------------------------------------------------------- |
| **M3C**| warna Bayer | penapis jalur-lepas tiga                |**Tiga jalur kalibrasi daripada satu pendedahan**                 |
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
| Penapis    | `FRGB` / `FRGN` / `FOCN` / `FNGB` (M3C) atau `F<nm>` (M3M) | Lihat [Penapis &amp; Julat Spektral](https://mapir.gitbook.io/lattice-camera/hardware/filters-and-bands) |

Rentetan model memacu segala-galanya seterusnya: Chloros menyelesaikan profil sensor, susun atur jalur, dan penentukuran kilang daripada `DeviceUserID` + `DeviceSerialNumber`. Tiada apa-apa yang perlu dikonfigurasikan bagi setiap kamera — lihat [Menyambungkan Kamera](connecting.md).

## Penapis dan jalur

Pusat jalur, sempadan FWHM dan katalog penuh 23-SKU M3M adalah spesifikasi produk, jadi ia terdapat dalam manual perkakasan: [**Penapis &amp; Jalur Spektral**](https://mapir.gitbook.io/lattice-camera/hardware/filters-and-bands).

Apa yang penting di bahagian perisian: kod penapis dalam rentetan model menentukan produk mana yang boleh dibina oleh Chloros. Kamera RGB-filter (`FRGB`) hanya memancarkan produk debayered dan pratonton — sinaran dan pantulan bagi setiap jalur tidak bermakna untuk sensor jalur lebar, jadi Chloros melangkauinya dan menyatakan demikian. Setiap penapis lain menghasilkan rantaian sinaran → pantulan → indeks sepenuhnya.

## Kalibrasi Radiometrik Sekilas

Setiap kamera LATTICE dikalibrasi secara individu di kilang menggunakan rantaian yang boleh dijejaki ke NIST dan dihantar bersama sijil bagi setiap kamera. Apa yang diliputinya, bagaimana ia diukur dan ketepatan yang boleh anda nyatakan terdapat dalam manual perkakasan: [**Kalibrasi Radiometrik Kilang**](https://mapir.gitbook.io/lattice-camera/calibration/factory-radiometric-calibration).

Pada bahagian perisian, apa yang penting ialah Chloros menentukan penentukuran yang betul apabila kamera disambungkan dan membekukan koefisien yang digunakan dalam setiap eksport — lihat [Menyambungkan Kamera](connecting.md).

## Dalam bab ini

* [Menyambungkan Kamera](connecting.md) — penemuan automatik, dialog sambungan GUI, setara [CLI](connecting.md) / [SDK](connecting.md), dan bagaimana kalibrasi kilang diselesaikan (pakej pada kamera berbanding awan) apabila kamera disambungkan.

Topik LATTICE lanjut — tetapan kamera dan kawalan langsung, mod tangkapan, susunan multi-kamera, dan pemprosesan mono (M3M) serta indeks — dibincangkan dalam seksyen tersendiri dalam manual ini, dan keseluruhan permukaan arahan terdapat dalam [Rujukan CLI](../reference/cli-reference.md) dan [SDK Reference](../reference/sdk-reference.md).
