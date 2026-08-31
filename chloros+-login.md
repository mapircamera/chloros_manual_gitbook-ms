# Chloros+ Log Masuk

## Log Masuk GUI

Menu sisi pengguna <img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line"> membolehkan anda log masuk ke akaun Chloros+ anda dan membuka ciri tambahan.

**Anda hanya perlu log masuk sekali setiap mesin.** GUI, CLI, dan Python SDK berkongsi sesi tumpuk yang sama — log masuk melalui GUI desktop juga akan mengaktifkan CLI dan SDK pada mesin tersebut (dan sebaliknya melalui `chloros-cli login`).

Apabila log masuk, butiran akaun anda akan dipaparkan:

<figure><img src=".gitbook/assets/user_account.JPG" alt="" width="375"><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: re-shoot the logged-in user account panel in Chloros 1.2.0 — plan name display and the registered-device list UI may have changed; must show plan name, expiration, and device list. -->
## Tahap Pelan

| Pelan | `plan_id` | Jenis |
| --- | --- | --- |
| Keluli | `0` | Percuma |
| Tembaga | `1` | Berbayar (Chloros
+) |
| Gangsa | `2` | Berbayar (Chloros
+) |
| Perak | `3` | Berbayar (Chloros
+) |
| Emas | `4` | Berbayar (Chloros
+) |

Lihat [rancangan dan harga](https://cloud.mapir.camera/pricing) untuk mengetahui apa yang disertakan dalam setiap peringkat berbayar.

Akses keCLI
/SDK
memerlukan pelan berbayar

Akses keCLI
danPython
SDK
memerlukan **mana-mana pelan berbayarChloros
+ (Tembaga atau ke atas)**. Ini dikuatkuasakan**di pihak pelayan** — setiap permintaanCLI
/SDK
mesti mengandungi kedua-dua sesi langsung dan pelan berbayar:

| StatusHTTP
| `error_code` | Maksud | Penyelesaian |
| --- | --- | --- | --- |
| `401` | `AUTH_REQUIRED` | Tidak log masuk pada mesin ini | `chloros-cli login <email> <password>` |
| `403` | `PLAN_UPGRADE_REQUIRED` | Log masuk, tetapi peringkat pelan terlalu rendah (peringkat Iron percuma) | Tingkatkan ke mana-mana pelan berbayarChloros
+ |

`chloros-cli status` kekal boleh diakses pada peringkat percuma, jadi anda sentiasa boleh melihat pelan semasa anda dan mengapa akses ditolak.

### Had perkakasan bersambung setiap pelan

Setiap pelan mengehadkan berapa banyak kamera LATTICE dan penderia cahaya DAQ yang boleh disambungkan secara langsung pada satu masa:

| Pelan | Kamera LATTICE | Penderia cahaya DAQ |
| --- | --- | --- |
| Iron (percuma / belum log masuk) | 4 | 2 |
| Copper / Bronze | 6 | 3 |
| Silver | 10 | 6 |
| Emas | 20 | 12 |

## Log MasukCLI


Log masuk dengan kelayakanChloros
+ anda untuk mengaktifkan pemprosesanCLI
. PadaLinux
(tanpa GUI), ini adalah satu-satunya cara untuk mengaktifkan lesen anda.

**Tatahias:**

```bash
chloros-cli login <email> <password>
```

{% hint style="info" %}
**PenggunaSDK**:Python
SDK
juga menyediakan kaedah `logout()` secara programatik untuk memadam kelayakan yang disimpan dalam cache. Lihat [RujukanSDK
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
<!-- SCREENSHOT-UPDATE: re-shoot the CLI login output — the banner now prints "Chloros CLI 1.2.0"; capture a successful login with the current output format. -->
### Penyimpanan Kredensial

Kredensial yang disimpan sementara dan konfigurasi disimpan dalam folder `.chloros` dalam direktori rumah pengguna anda di **semua platform**:

| Platform | Laluan Penyimpanan Kredensial |
| --- | --- |
| **Windows** | `%USERPROFILE%\.chloros\` |
| **Linux** | `~/.chloros/` |

### Tamat Pelan dan Tempoh Pertengahan Luar Talian

Tamat pelan dalam GUI menunjukkan bila lesen anda akan menjadi tidak sah. Untuk langganan bulanan berulang, tarikh luput adalah pada akhir bulan; untuk langganan tahunan pula, ia adalah setahun selepas anda memulakan langganan.

Chloros
mengesahkan lesen anda secara dalam talian, tetapi bekerja secara luar talian disokong dalam tempoh kebenaran:

* Pengesahan pelayan yang berjaya disimpan dalam tumpuan selama **5 minit**, jadi penggunaan biasa hanya membuat beberapa panggilan lesen.
* Cache lesen yang ditandatangani dan terikat pada mesin merangkumi tempoh luar talian yang lebih lama: **30 hari untuk pelan bulanan**, dan**sehingga tarikh tamat langganan anda (maksimum 365 hari) untuk pelan tahunan**.
* Apabila tempoh lanjutan tamat, pelan akan bertukar kepada peringkat Iron percuma sehingga mesin dapat mencapai pelayan lesen sekali; akses akan disambung semula pada pemeriksaan berjaya seterusnya.

### Had Peranti

Setiap pelanChloros
+ menawarkan bilangan peranti berdaftar yang berbeza. Setiap peranti yang anda log masuk menggunakan akaunChloros
+ dikira ke dalam bilangan peranti berdaftar anda. Anda boleh menamakan semula dan mengeluarkan peranti pada halaman akaun Akaun AwanMAPIR
anda.

<table><thead><tr><th width="168.5999755859375" align="right">Pelan Chloros+</th><th align="center">TAMBAGA</th><th align="center">PERAK</th><th align="center">PERAK</th><th align="center">EMAS</th></tr></thead><tbody><tr><td align="right">Peranti Disokong</td><td align="center">2</td><td align="center">2</td><td align="center">5</td><td align="center">10</td></tr></tbody></table>

Had tepat peranti untuk akaun anda dipaparkan di halaman akaun Cloud MAPIR anda. Log keluar daripada peranti secara boleh dipercayai akan membebaskan slotnya, dan peranti yang telah didaftarkan sentiasa boleh log masuk semula walaupun akaun telah mencapai had peranti.
