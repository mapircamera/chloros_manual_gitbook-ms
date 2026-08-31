# Kamera Mono &amp; Indeks Vegetasi

## Satu kamera = satu jalur

Kamera **M3M**ialah kembar mono kepada**M3C** Bayer: sebuah sensor monokrom IMX265 di belakang satu penapis interferens jalur sempit. Rentetan model menamakan jalur tersebut — `M3M-<lens>-F<wavelength>`, contohnya `M3M-L87-F685` (dipaparkan dalamChloros

sebagai `LATT-M3M-L87-F685`). Pengesan ini menghasilkan **satu jalur skala kelabu** tanpa mozek Bayer: tiada apa yang perlu didemosai, tiada gangguan silang antara saluran yang perlu dipisahkan, dan tiada imbangan putih yang perlu ditetapkan.

Kesan yang perlu diketahui sebelum anda merancang sistem mono:

* **Radiasi dan pantulan ditakrifkan sepenuhnya bagi setiap jalur.**Mereka adalah peta radiometrik bagi setiap jalur, jadi satu kamera M3M menghasilkan sinaran float32 yang dikalibrasi (W/m²/sr/nm) dan pantulan uint16 (`32768` = ρ 1.0) dengan tepat seperti yang dilakukan oleh jalur M3C. Sisipan mono membawa matriks**identiti** tindak balas sensor — tiada pencampuran semula 3×3 diperlukan atau digunakan.
* **Satu kamera mono tidak dapat menghasilkan indeks vegetasi.**NDVI

,NDRE

, dan rakan-rakannya memerlukan sekurang-kurangnya dua jalur. Untuk mengira indeks daripada perkakasan mono, anda menggabungkan beberapa kamera M3M — lihat di bawah.
* Kamera M3M menyalurkan **Mono12** (12-bit, 2 bait/piksel pada talian), yang penting untuk [peruntukan jalur lebar tatasusunan](arrays.md#bandwidth-the-rules-of-thumb).

## Apa yang dilangkau olehChloros

untuk mono — dan bagaimana ia memberitahu anda

Tahap saluran warna tidak terpakai pada sensor jalur tunggal.Chloros

**melangkau mereka dengan mesej satu baris** daripada menghasilkan ralat, dan masih menjalankannya seperti biasa untuk mana-mana kamera M3C (Bayer) dalam sesi yang sama:

| Peringkat | Kelakuan Mono (M3M) | Kelakuan M3C |
| --- | --- | --- |
| Demosaic / debayer | Dilangkau — tahap eksport `debayered` adalah imej skala kelabu 1-saluran. | Demosaik 3-saluran. |
| Imbangan putih (`lattice white-balance`) | Dilangkau dengan mesej satu baris. | Berjalan dengan normal. |
| Profil warna (`lattice color-profile`) | Dilangkau dengan mesej satu baris. | Berfungsi seperti biasa. |
| Kepenuhan/kontras (`lattice color`) | Dilangkau dengan mesej satu baris. | Berfungsi dengan normal. |
| Pencampuran semula silang spektral | Identiti (tiada matriks 3×3). | Matriks 3×3 bagi setiap kamera digunakan. |
| Radiasi / pantulan | **Berfungsi** — bagi setiap jalur, diselaras sepenuhnya. | Berfungsi bagi setiap jalur. |

GUI menerapkan gating yang sama: untuk kamera mono, panel tetapan per-kamera menyembunyikan baris yang hanya berkaitan denganRGB

(Imbangan Putih, Gamma, Profil Warna, Kepenuhan Warna, Kontras, pemisahan saluran), dan histogram langsung dikunci pada satu jejak **MONO**. Pembezal di seluruh susunan ialah token `M3M` dalam rentetan model, yang dipaparkan kepada GUI/SDK

sebagai `is_mono`.

## Indeks memerlukan ≥ 2 jalur: selaras → susun → indeks

Aliran kerja indeks mono sentiasa mempunyai tiga langkah yang sama:

1. **Sejajarkan** — tujukan beberapa kamera M3M pada panjang gelombang yang berbeza (contohnya F650 &quot;Red

&quot; dan F850 &quot;NIR

&quot;), sambungkan mereka sebagai [susunan multi-kamera](arrays.md), dan biarkanChloros

mengira pemutar bersama (co-registration warp) antara kamera.
2. **Susun** — bingkai yang diselaraskan menjadi satu imej berbilang jalur (setiap kamera menyumbang satu jalur bernama).
3. **Indeks** — menilai formula indeks ke atas jalur-jalur susunan, secara pilihan memaparkannya melalui LUT.

Dalam GUI, keseluruhan rantaian ini adalah mod paparan susunan **Kamera Gabungan**: komposit langsung sudah selari, dan Pengira Indeks susunan (di bawah) mentakrifkan formula yang dipaparkannya. Eksport rakaman yang dirakam boleh diwarp ke penjajaran yang sama dengan pilihan rakaman**Aligned**.

## Pengira Indeks

Pengira Indeks membina ungkapan indeks yang digunakan oleh paparan langsung dan eksport indeks bagi setiap kamera. Ia adalah satu permukaan yang dikongsi, dibuka dari dua tempat dalam bar sisi tab Kamera:

* **Per-kamera**— Pratonton Langsung → gear**Index** (RGN

/OCN

/NGB

hanya untuk kamera Bayer; kamera mono tunggal tidak mempunyai kawalan indeks kerana satu jalur tidak boleh membuat indeks).
* **Setiap-susunan**— tetapan susunan → Pratonton Langsung → gear**Indeks**. Ini adalah laluan mono: senarai jalur merangkumi**semua kamera ahli**, jadi sepasang mono menyumbang kedua-dua jalurnya di sini.

<!-- SCREENSHOT-NEEDED: Index Calculator pane opened for a combined array of two mono cameras (e.g. F650 + F850): band chips row showing the two bands with wavelength labels, the operator buttons, the expression textarea containing "(NIR - Red) / (NIR + Red)", the green "Valid expression" banner, the LUT controls (Apply LUT checked, Level 7-stop, Min 0.2 / Max 1), and the live histogram with p2/p98 percentile lines. -->

Kawalnya, dari atas ke bawah:

* **Cip jalur** (&quot;Jalur — klik untuk menambah ke ekspresi&quot;) — satu butang bagi setiap jalur yang tersedia, dilabelkan nama warna + panjang gelombang dalam nm (nama warna yang berulang dibezakan seperti contohnya &quot;Warna 850&quot;). Mengklik akan menyisipkan token jalur pada tanda karet. Gelombang daripada kamera yang tidak dapat menghasilkan sinaran bagi setiap gelombang (RGB

/FRGB) akan ditapis.
* **Butang pengendali dan fungsi** — `+ - * / ( ) ^ ,` dan `abs() sqrt() log() log10() exp() min() max() pow()`.
* **Kawasan teks ekspresi** — formula taip bebas; penanda tempat menunjukkan bentuk klasikNDVI

`(NIR - Red) / (NIR + Red)`. Pratonton token bersaiz baca sahaja di atasnya memaparkan cip jalur, nombor, dan bendera token yang tidak diketahui.
* **Bendera kesahihan**— kelabu &quot;Kosong — tiada indeks akan digunakan&quot;; hijau &quot;Ungkapan sah&quot;; merah dengan ralat parse tertentu (gelombang tidak diketahui, gelombang samar yang terdedah oleh pelbagai kamera, kurungan hilang, …); atau jingga apabila ungkapan sah tetapi**konstan** (contohnya `X/X`, atau pembilang denominatorNDVI

yang ditaip dengan `−` dan bukannya `+`) — satu konstanta memetakan keseluruhan bingkai kepada satu warna.
* Amaran amber berasingan akan muncul jika ekspresi yang digunakan adalah baik tetapi **rangka hidup adalah seragam** (adegan rata atau tepu) — pengecutan histogram dikesan untuk anda.
* **Terapkan LUT**(laluan lalai; mati = regangan skala kelabu),**Peringkat**2/3/5/7-henti (laluan lalai 7-henti), dan input**Min / Max**di kedua-dua belah bar gradien. Min secara lalai adalah**

0.2**— ia memperbesarkan ramp warna ke dalam julat yang relevan dengan vegetasi manakala nilai di bawah dipaparkan sebagai skala kelabu; tetapkan Min kepada −1 untuk julat indeks penuh (butang**Reset** memulihkan kepada −1…+1). Max secara lalai adalah 1.
* **Histogram langsung** pengagihan indeks — bar berskala sqrt, garisan persentil p2/p98 jingga, garisan median putih, dan bacaan ekor di luar julat (&quot;◀ N% &lt; lo&quot; / &quot;hi &lt; N% ▶&quot;) yang bertukar menjadi jingga melebihi 1% sebagai petunjuk untuk melebarkan tetingkap Min/Max.
* **Terapkan** (Apply) menetapkan ungkapan pada aliran langsung; pelarasan LUT digunakan secara langsung tanpa menekan Terapkan. Ungkapan sengaja hanya untuk sesi — ia tidak disimpan antara sesi.



<!-- SCREENSHOT-NEEDED: Combined-array live tile rendering NDVI from a mono pair through the default 7-stop LUT, with the array name pill and fps readout visible — the result of applying the expression from the previous screenshot. -->

## LaluanCLI



Rantaian selaras → susun → indeks yang sama, boleh diprogramkan dari awal hingga akhir:

```bash
chloros-cli lattice array-connect --serials SN_RED,SN_NIR
chloros-cli lattice index --live --profile align.json \
  --preset NDVI --channel red=Red_660 --channel nir=NIR_850 \
  --save-multiband -o output/
```

`--channel` memetakan simbol preset kepada nama jalur susunan. Dua peraturan menjimatkan anda daripada percubaan yang gagal:

* **Simbol adalah sensitif huruf besar/kecil** dan mesti sepadan dengan nama saluran pratetap dengan tepat — pratetap menggunakan huruf kecil (NDVI

adalah `red`,`nir`; semak `--list-presets`). `--channel red=Red_660` berfungsi; `--channel RED=660` gagal dengan ralat `channel_map missing entries`.
* Pihak jalur mesti menamakan jalur dalam susunan selari (`lattice align-info --profile align.json` menyenaraikannya). Mod luar talian juga menerima indeks jalur berasaskan 0, contohnya `--channel red=0 --channel nir=1`.

`lattice index` juga dijalankan sepenuhnya secara luar talian terhadapTIFF

berbilang jalur selari yang disimpan:

```bash
chloros-cli lattice index --input aligned.tif --preset NDVI \
  --output ndvi.tif --colorize --gradient RdYlGn
```

### Pratetap indeks

`lattice index --preset` (dan [Index/LUT sandbox](../image-viewer-gui/index-lut-sandbox.md) pada tab Imej, yang menggunakan enjin yang sama) disertakan dengan **22 pratetap** ini:

`NDVI, GNDVI, BNDVI, NDRE, ENDVI, SAVI, OSAVI, MSAVI, EVI, EVI2, CVI, MSR, TDVI, LAI, GLI, NGRDI, VARI, TGI, EXG, CIRE, CIGREEN, NDWI`

Jalankan `chloros-cli lattice index --list-presets` untuk formula dan simbol saluran setiap pratetap, dan `--list-gradients` untuk gradien warna yang tersedia. Formula tersuai menggunakan `--formula EXPR` dengan sintaks yang sama seperti Pengira Indeks. Nota: senarai pratetap ini khusus untuk enjin indeks LATTICE — senarai lungsur pemprosesan Tetapan Projek untuk imej yang diimport adalah berbeza (lihat [Formula Indeks Multispektral](../project-settings/multispectral-index-formulas.md)).

Set penuhnya (`--output-format`, `--vmin/--vmax/--percentile`, `--bg-mode`, tombol warp penjajaran untuk `--live`, dan banyak lagi) didokumenkan dalam [RujukanCLI

§ Index / Vegetation Maths](../reference/cli-reference.md#index--vegetation-maths);SDK

padanan terdapat dalam [RujukanSDK

](../reference/sdk-reference.md).

## Menangkap produk indeks daripada susunan mono

Dengan susunan disambungkan dan ungkapan indeks diterapkan, `array-capture` (atau GUI **Capture All**) menyimpan tahap eksport bagi setiap kamera *dan* render indeks — `--index`/`--no-index` mengaktifkannya padaCLI

, dan tangkapan secara lalai merangkumi setiap tahap yang terpakai. Sumbangan kamera mono kepada setiap kumpulan tangkapan ialah satu jalurnya pada tahap mentah/debayered (skala kelabu)/radiasi/refleksi, ditambah komposit indeks gabungan yang dikongsi apabila susunan berjalan dalam mod gabungan. Lihat [Susunan Pelbagai Kamera § Pengambilan](arrays.md#capturing-monitoring-vs-analysis).
