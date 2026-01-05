# Chloros+ Log Masuk

## Log Masuk Chloros dan Chloros (Pelayar)

Menu bar sisi <img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line"> pengguna membolehkan anda log masuk ke akaun Chloros+ anda dan membuka kunci ciri tambahan.

Apabila log masuk, butiran akaun anda akan ditunjukkan:

<figure><img src=".gitbook/assets/user_account.JPG" alt="" data-size="line"><figcaption></figcaption></figure>

## CLI Log Masuk

Log masuk dengan bukti kelayakan Chloros+ anda untuk mendayakan pemprosesan CLI.

**Sintaks:**

```bash
chloros-cli login <email> <password>
```

{% hint style="info" %}
**Pengguna SDK**: Python SDK juga menyediakan kaedah `logout()` terprogram untuk mengosongkan bukti kelayakan cache. Lihat dokumentasi [Python SDK](api-python-sdk.md#logout) untuk mendapatkan butiran.
Petua {% %}

**Contoh:**

```powershell
chloros-cli login user@example.com 'MyP@ssw0rd123'
```

{% gaya petunjuk="amaran" %}
**Watak Khas**: Gunakan petikan tunggal di sekitar kata laluan yang mengandungi aksara seperti `$`, `!` atau ruang.
Petua {% %}

**Keluaran:**

<figure><img src=".gitbook/assets/cli login_w.JPG" alt=""><figcaption></figcaption></figure>

### Tamat Tempoh Pelan

Pelan tamat tempoh dalam GUI menunjukkan bila lesen anda akan menjadi tidak sah. Untuk langganan bulanan berulang, tamat tempoh adalah pada penghujung bulan. Untuk langganan tahunan adalah setahun selepas anda memulakan langganan. Semakan lesen memerlukan sambungan internet bulanan untuk mengesahkan, dengan tempoh tangguh 30 hari.

### Had Peranti

Setiap pelan Chloros+ menawarkan bilangan peranti berdaftar yang berbeza. Setiap peranti yang anda log masuk dengan akaun Chloros+ akan dikira dalam bilangan peranti berdaftar anda. Anda boleh menamakan semula dan mengalih keluar peranti pada halaman akaun Cloud MAPIR anda.

<table><thead><tr><th width="168.5999755859375" align="right">Chloros+ Plan</th><th align="center">TEMBAGA</th><th align="center">GANGSA</th><th align="center">PERAK</th><th align="center">GOLD</th></tr></thead><tbody><tr><td align="right">Peranti Disokong</td><td align="center">2</td><td align="center">2</td><td align="center">5</td><td align="center">10</td></tr></tbody></table>