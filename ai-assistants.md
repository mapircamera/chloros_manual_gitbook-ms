# Menggunakan Chloros dengan Pembantu AI

Manual ini ditulis untuk dua khalayak: manusia, dan pembantu AI yang semakin kerap digunakan oleh manusia. Setiap halaman menerbitkan nilai tepat, lalai, dan arahan yang boleh disalin dan ditampal supaya pembantu (Claude, ChatGPT, Copilot, ejen pengaturcaraan, …) dapat menulis automasi Chloros yang berfungsi pada percubaan pertama.

Chloros versi: **

1.2.0**. CLI / SDK platform: Windows 10/11 x64 dan Linux (x86_64 / Jetson aarch64).

## Apa yang perlu diberikan kepada pembantu anda

| Sumber | URL | Kegunaannya |
| --- | --- | --- |
| **llms.txt** | `https://mapir.gitbook.io/chloros/llms.txt` | Indeks boleh dibaca mesin bagi setiap halaman dalam manual ini. |
| **Rujukan CLI** | `https://mapir.gitbook.io/chloros/reference/cli-reference` | Permukaan arahan `chloros-cli` yang lengkap: setiap arahan, bendera, lalai, kod keluar, dan peraturan folder keluaran. Ditulis untuk kegunaan LLM. |
| **Rujukan SDK** | `https://mapir.gitbook.io/chloros/reference/sdk-reference` | Permukaan arahan `chloros_sdk` yang lengkap: setiap arahan, bendera, lalai, kod keluar, dan peraturan folder keluaran. Ditulis untuk penggunaan LLM. |
| **Sebarang halaman sebagai Markdown mentah** | sambungkan `.md` ke halaman URL | contohnya `https://mapir.gitbook.io/chloros/reference/sdk-reference.md` memulangkan halaman sebagai Markdown mentah — sesuai untuk ditampal ke tetingkap konteks atau diambil daripada ejen. |

Pautan dalam manual: [Rujukan CLI](reference/cli-reference.md) · [Rujukan SDK](reference/sdk-reference.md).

{% hint style="info" %}
Kedua-dua halaman rujukan ini lengkap berdiri sendiri: seorang pembantu yang telah membaca salah satunya tidak memerlukan baki manual untuk menulis skrip yang betul.
{% endhint %}

## Resipi pantas

Salin, isi `<placeholders>`, dan tampal ke dalam pembantu anda.

### 1. Proses folder penerbangan menjadi NDVI

```

Read https://mapir.gitbook.io/chloros/reference/cli-reference.md.
Then write a script for <Windows PowerShell | bash> that:
1. logs in with `chloros-cli login <email> '<password>'` (only needed once per machine),
2. processes the folder <path/to/flight_001> with reflectance and the NDVI index,
3. prints where each output product landed, using the reference's
   "Where the outputs land" folder rules.
```

### 2. Pantau secara pukal direktori tangkapan

```

Read https://mapir.gitbook.io/chloros/reference/sdk-reference.md (sections
"Quickstart" and "Post-Run Summary & Hints"). Write a Python script that
watches <path/to/captures> for new flight subfolders and runs
chloros_sdk.process_folder() with indices=["NDVI"] on each new one.
After each run, print every hint from result["summary"]["hints"] and treat
a run with zero image products as a failure for that folder.
```

### 3. Sambungkan susunan LATTICE dan rakam

```

Read https://mapir.gitbook.io/chloros/reference/sdk-reference.md (section
"connect_array"). Write a Python script that connects my LATTICE cameras
with serials <213800234, 214000533, ...> as one synchronized array, captures
a reflectance image set into <output/> every 10 seconds for one hour, and
disconnects cleanly when done (use the context-manager form).
```

### 4. Rakam spektra penderia cahaya DAQ

```

Read https://mapir.gitbook.io/chloros/reference/cli-reference.md (section
"chloros-cli daq" — use only the pool-* commands). Write a script that:
1. connects my DAQ-E sensor with `chloros-cli daq pool-connect --eth-host <daq-e-xxxxxx.local>`,
2. lists the pool with `pool-list` to get the sensor id,
3. records a 10-minute calibrated .daq file named "<field-A>" with `pool-record`,
4. disconnects with `pool-disconnect`.
```

{% hint style="warning" %}
Skrip DAQ dari baris perintah sentiasa melalui keluarga `daq pool-*` (`pool-connect`, `pool-list`, `pool-latest`, `pool-stream`, `pool-record`, `pool-set-cap`, `pool-disconnect`). Subperintah `daq` lain yang mungkin dicipta oleh pembantu anda tidak tersedia dalam binaan yang dihantar dan akan keluar dengan ralat.
{% endhint %}

## Mengapa skrip yang ditulis oleh AI berfungsi dengan baik bersama Chloros

Setiap satunya adalah tingkah laku sebenar yang disahkan bagi Chloros 1.2.0 — ia menghapuskan mod kegagalan klasik dalam automasi yang ditulis oleh mesin:

* **Tiada tarian persediaan.**Pembantu sambungan pintar SDK (`connect_camera`, `connect_array`, `connect_daq_sensor`) dan titik kemasukan pemprosesan (`ChlorosLocal`, `process_folder`)**memulakan secara automatik backend tempatan**. Skrip yang dijana tidak memerlukan GUI dibuka atau pelayan yang dimulakan secara manual — ia hanya memerlukan pakej desktop/CLI dipasang.
* **Seluruh aliran kerja adalah satu panggilan.** `chloros_sdk.process_folder("path", indices=["NDVI"])` menjalankan import → penentukuran → pantulan → eksport indeks dari awal hingga akhir. Luas permukaan yang lebih kecil, lebih sedikit tempat bagi skrip yang dijana untuk melakukan kesilapan.
* **Jalankan tanpa output mengesan sendiri.** Selepas `process()`, ringkasan jalankan dilampirkan pada keputusan, dan setiap petunjuk pemprosesan (contohnya *mengapa* sesuatu larian tidak menghasilkan sebarang output) turut disiarkan semula sebagai Python `UserWarning` — jadi walaupun skrip yang tidak pernah memeriksa result dict akan memaparkan diagnosis tersebut.
* **CLI gagal dengan bunyi nyaring.**Jalankan `chloros-cli process` yang meminta produk tetapi tidak menulis sebarang produk mencetak `Processing finished but wrote no image products.` dan**keluar dengan nilai bukan sifar**, jadi skrip shell dan CI mengesaninya dengan pemeriksaan kod keluar biasa. Jalankan yang berjaya melaporkan `Image products written: N`.

Satu asimetri yang perlu diketahui oleh pembantu: `process()` pada SDK sengaja **tidak** memaparkan ralat untuk larian tanpa hasil — sebaliknya ia melaporkannya melalui ringkasan/petunjuk. Jika saluran Python perlu berhenti pada larian kosong, semak ringkasan (resipi 2 melakukannya).

## Peringatan

* **Log masuk Chloros diperlukan.** CLI dan SDK memerlukan tahap Chloros berbayar, dikuatkuasakan di pihak pelayan: permintaan akan gagal dengan `401 AUTH_REQUIRED` apabila tidak log masuk dan `403 PLAN_UPGRADE_REQUIRED` pada tahap percuma. Jalankan `chloros-cli login` sekali pada setiap mesin sebelum menjalankan skrip yang dijana. Lihat [Chloros+ Login](chloros+-login.md).
* **Perintah tangkapan mengendalikan perkakasan sebenar.** Perintah `lattice` / `daq` / `project` dan objek sesi SDK menyambung, menstrim, dan mencetuskan kamera serta penderia fizikal. Semak skrip yang dijana sebelum ia dijalankan buat kali pertama, dan jalankan ia dengan kehadiran perkakasan.
* **Semak keluaran secara rawak.** Sahkan folder produk dan beberapa nilai piksel sebelum menerbitkan keputusan. Khususnya, TIFF reflektansi diskalakan mengikut sumber — baca tag XMP `Chloros:PixelScale` (LATTICE: 32768 = 1.0 pantulan; Survey3: 65535) bukannya menganggap pembahagi. Kedua-dua halaman rujukan mendokumentasikan perkara ini di bawah &quot;Membaca piksel pantulan&quot;.
* **Kesilapan kecil yang boleh mengganggu kod yang dijana:**`pool-record` menulis ke dalam sistem fail**hos belakang** (default `~/Documents/DAQ Live View/`); pada mesin dengan beberapa antara muka rangkaian, utamakan `daq pool-connect --eth-host <ip-or-hostname>` berbanding penemuan automatik; dan gunakan `http://127.0.0.1:5000` (jangan sekali-kali `localhost`) di mana-mana URL belakang muncul.
