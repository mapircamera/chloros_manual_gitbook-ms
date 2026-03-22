# Linux Gambaran Keseluruhan

Chloros 1.1.0 membawakan sokongan Linux asli untuk **CLI**dan**Python SDK**, membolehkan pemprosesan imej berbilang spektrum tanpa kepala pada peranti CLI**dan**Python SDK**, mendayakan pemprosesan imej berbilang spektrum tanpa kepala pada peranti XPROXsonPROdges, Jetprodges dan peranti NVIDIA.

{% hint style="info" %}
**Tiada GUI pada Linux.** GUI Desktop Chloros tersedia pada Windows sahaja. Pengguna Linux berinteraksi dengan Chloros melalui [CLI](../CLI.md) dan [Python SDK](..XPROTX000009).
{% endhint %}

***

## Matriks Sokongan Platform

| Ciri | Windows (GUI) | Windows (CLI/SDK) | Linux amd64 (CLI/SDK) | Linux arm64 / Jetson (CLI/SDK) |
| --- | --- | --- | --- | --- |
| **GUI Desktop** | Ya | T/T | Tidak | Tidak |
| **CLI** | Ya | Ya | Ya | Ya |
| **Python SDK** | Ya | Ya | Ya | Ya |
| **Pecutan GPU (CUDA)** | Ya | Ya | Ya | Ya (JetPack 6) |
| **Texture Aware Debayer** | Ya (Chloros+) | Ya (Chloros+) | Ya (Chloros+) | Ya (Chloros+) |
| **Penyesuaian Pengiraan Dinamik** | Ya | Ya | Ya | Ya |***

## Senibina yang Disokong

| Seni Bina | Penerangan | Kaedah Pemasangan |
| --- | --- | --- |
| **amd64 (x86_64)** | Pemproses desktop/pelayan standard (Intel, AMD) | Pakej `.deb` |
| **arm64 (aarch64)** | Pemproses berasaskan ARM, terutamanya NVIDIA Jetson | Pakej `.deb` (JetPack 6) |

## Disokong Pengedaran Linux

* **Ubuntu 20.04+** (amd64)
* **Debian 11+** (amd64)
* **NVIDIA JetPack 6** (arm64 — platform Jetson)***

## Apa yang Pengguna Linux Dapat

* **Chloros CLI** — Antara muka baris arahan penuh untuk pemprosesan kelompok, automasi dan skrip
* **Chloros Python SDK** — Antara muka Python terprogram (`pip install chloros-sdk`) untuk penyepaduan ke dalam saluran paip penyelidikan dan alatan tersuai
* **Pecutan GPU** — Pemprosesan dipercepatkan CUDA pada GPU NVIDIA (desktop dan Jetson)
* **Penyesuaian Pengiraan Dinamik** — Pengesanan perkakasan automatik dan pengoptimuman strategi pemprosesan
* **Semua Ciri Pemprosesan** — Saluran paip pemprosesan berbilang spektrum yang sama seperti Windows (penentukuran, pembetulan vignet, indeks tumbuh-tumbuhan, semua format eksport)
* **Ciri Chloros+** — Pemprosesan berbilang benang, Debayer Texture Aware, indeks tersuai (dengan lesen Chloros+)

## Perkara yang Pengguna Linux Tidak Dapat

* **GUI Desktop** — Tiada antara muka grafik; semua interaksi adalah melalui CLI atau Python SDK
* **Pemapar Imej** — Tiada pemapar imej interaktif, paparan grid atau penanda peta
* **Pengurusan Projek Visual** — Projek diuruskan melalui arahan CLI dan panggilan SDK***

## Bermula pada Linux

1. **Pasang Chloros** — Lihat [Pemasangan Linux](linux-installation.md) untuk pemasangan pakej `.deb`
2. **Pasang Python SDK** (pilihan) — `pip install chloros-sdk`
3. **Aktifkan lesen anda** — `chloros-cli login your@email.com 'password'`
4. **Proses set data pertama anda** — `chloros-cli process ~/datasets/flight001`

Untuk pengguna NVIDIA Jetson, lihat [Panduan NVIDIA Jetson](nvidia-jetson-guide.md) khusus untuk persediaan dan pengoptimuman khusus platform.

***

## Langkah Seterusnya

* [Linux Installation](linux-installation.md) — Arahan pemasangan terperinci untuk amd64 dan arm64
* [Panduan NVIDIA Jetson](nvidia-jetson-guide.md) — Persediaan khusus Jetson, pengurusan terma dan penggunaan medan
* [CLI : Command Line](../CLI.md) — Rujukan penuh CLI
* [API : Python SDK](../api-python-sdk.md) — Rujukan penuh SDK
* [Penyesuaian Pengiraan Dinamik](../processing-architecture/dynamic-compute-adaptation.md) — Cara Chloros menyesuaikan diri dengan perkakasan anda