# Log Masuk Chloros+

## Log Masuk GUI

Menu sisi <img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line">membolehkan anda log masuk ke akaun Chloros+ anda dan membuka ciri tambahan.

**Anda hanya perlu log masuk sekali setiap mesin.** GUI, CLI, dan Python SDK berkongsi sesi penimbal yang sama — log masuk melalui GUI desktop juga mengaktifkan CLI dan SDK pada mesin tersebut (dan sebaliknya melalui `chloros-cli login`).

Apabila log masuk, butiran akaun anda akan dipaparkan:

<figure><img src=".gitbook/assets/user_account.JPG" alt="" width="375"><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: re-shoot the logged-in user account panel in Chloros 1.3.0 — plan name display and the registered-device list UI may have changed; must show plan name, expiration, and device list. -->

## Tahap Pelan

| Pelan | `plan_id` | Jenis |
| --- | --- | --- |
| Iron | `0` | Percuma |
| Tembaga | `1` | Berbayar (Chloros

+) |
| Gangsa | `2` | Berbayar (Chloros

+) |
| Perak | `3` | Berbayar (Chloros

+) |
| Emas | `4` | Berbayar (Chloros

+) |

Lihat [rancangan dan harga](https://cloud.mapir.camera/pricing) untuk mengetahui apa yang disertakan dalam setiap peringkat berbayar.

### Akses CLI

/ SDK

memerlukan peringkat berbayar

Akses CLI

dan Python

SDK

memerlukan **mana-mana peringkat berbayar Chloros

+ (Copper atau ke atas)**. Ini dikuatkuasakan**di pihak pelayan** — setiap permintaan CLI

/SDK

mesti mempunyai kedua-dua sesi langsung dan pelan berbayar:

| Status HTTP

| `error_code` | Maksud | Penyelesaian |
| --- | --- | --- | --- |
| `401` | `AUTH_REQUIRED` | Tidak log masuk pada mesin ini | `chloros-cli login <email> <password>` |
| `403` | `PLAN_UPGRADE_REQUIRED` | Log masuk, tetapi peringkat pelan terlalu rendah (peringkat Iron percuma) | Tingkatkan ke mana-mana pelan berbayar Chloros

+ |

`chloros-cli status` kekal boleh diakses pada peringkat percuma, jadi anda sentiasa boleh melihat pelan semasa anda dan mengapa akses ditolak.

### Had perkakasan bersambung setiap pelan

Setiap pelan mengehadkan berapa banyak kamera LATTICE dan penderia cahaya DAQ yang boleh disambungkan secara langsung pada satu-satu masa:

| Pelan | Kamera LATTICE | Penderia cahaya DAQ |
| --- | --- | --- |
| Iron (percuma / tidak log masuk) | 4 | 2 |
| Copper / Gangsa | 6 | 3 |
| Perak | 10 | 6 |
| Emas | 20 | 12 |

## Log Masuk CLI



Log masuk dengan kelayakan Chloros

+ anda untuk mengaktifkan pemprosesan CLI

. Pada Linux

(tanpa GUI), ini adalah satu-satunya cara untuk mengaktifkan lesen anda.

**Tatabahasa:**

```bash
chloros-cli login <email> <password>
```

{% hint style="info" %}
**Pengguna SDK**: Python

SDK

juga menyediakan kaedah `logout()` secara berperogram untuk memadam kelayakan yang disimpan dalam cache. Lihat [Rujukan SDK

](reference/sdk-reference.md) untuk maklumat lanjut.
{% endhint %}

**Contoh:**

```powershell
chloros-cli login user@example.com 'MyP@ssw0rd123'
```

{% hint style="warning" %}
**Karakter Khas**: Gunakan tanda petik tunggal di sekeliling kata laluan yang mengandungi karakter seperti `$`, `!`, atau ruang.
{% endhint %}

**Keluaran:**

<figure><img src=".gitbook/assets/cli login_w.JPG" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: re-shoot the CLI login output — the banner now prints "Chloros CLI 1.3.0"; capture a successful login with the current output format. -->

### Penyimpanan Kredensial

Kredensial yang disimpan dalam cache dan konfigurasi disimpan dalam folder `.chloros` dalam direktori rumah pengguna anda di **semua platform**:

| Platform | Laluan Penyimpanan Sijil |
| --- | --- |
| **Windows** | `%USERPROFILE%\.chloros\` |
| **Linux** | `~/.chloros/` |

Mengpasang versi baharu di atas versi sedia ada mengekalkan log masuk anda, pada Windows

(pemasang) dan pada Linux

(`.deb`, termasuk `chloros-cli update`). Pemupusan Chloros

— atau penghapusan pakej Linux

— akan memadam log masuk yang disimpan dalam cache, jadi anda perlu log masuk semula selepas pemasangan baharu.

### Tamat Pelan dan Tempoh Kelonggaran Luar Talian

Tarikh tamat pelan dalam GUI menunjukkan bila lesen anda akan menjadi tidak sah. Untuk langganan bulanan berulang, tarikh luput adalah pada akhir bulan; untuk langganan tahunan pula, ia adalah setahun selepas anda memulakan langganan.

Chloros

mengesahkan lesen anda secara dalam talian, tetapi bekerja secara luar talian disokong dalam tempoh kemaafan:

* Pengesahan pelayan yang berjaya disimpan dalam tumpuan selama **5 minit**, jadi penggunaan biasa menghasilkan sangat sedikit panggilan lesen.
* Cache lesen yang ditandatangani dan terikat pada mesin merangkumi tempoh luar talian yang lebih lama: **30 hari untuk pelan bulanan**, dan**sehingga tarikh tamat langganan anda (maksimum 365 hari) untuk pelan tahunan**.
* Apabila tempoh lanjutan tamat, pelan akan bertukar kepada peringkat Iron percuma sehingga mesin dapat mencapai pelayan lesen sekali; akses akan disambung semula pada pemeriksaan berjaya seterusnya.

### Had Peranti

Setiap pelan Chloros

+ menawarkan bilangan peranti berdaftar yang berbeza. Setiap peranti yang anda log masuk menggunakan akaun Chloros

+ dikira ke dalam bilangan peranti berdaftar anda. Anda boleh menamakan semula dan mengeluarkan peranti di halaman akaun MAPIR

Cloud anda.

<table><thead><tr><th width="168.5999755859375" align="right">Pelan Chloros+</th><th align="center">TAMBAGA</th><th align="center">PERAK</th><th align="center">PERAK</th><th align="center">EMAS</th></tr></thead><tbody><tr><td align="right">Peranti Disokong</td><td align="center">2</td><td align="center">2</td><td align="center">5</td><td align="center">10</td></tr></tbody></table>

Had tepat peranti akaun anda dipaparkan di halaman akaun Awan MAPIR anda. Log keluar daripada peranti secara boleh dipercayai akan membebaskan slotnya, dan peranti yang telah didaftarkan sentiasa boleh log masuk semula walaupun akaun telah mencapai had peranti.
