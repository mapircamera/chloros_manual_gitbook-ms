# Menggunakan Chloros dengan Pembantu AI

Manual ini ditulis untuk dua khalayak: manusia, dan pembantu AI yang semakin kerap digunakan oleh manusia. Setiap halaman menerbitkan nilai tepat, lalai, dan arahan yang boleh disalin dan ditampal supaya pembantu (Claude, ChatGPT, Copilot, ejen pengaturcaraan, …) dapat menulis automasi Chloros yang berfungsi pada percubaan pertama.

Chloros versi: **

1.3.0**. Platform CLI/SDK: Windows 10/11 x64 dan Linux (x86\_64 / Jetson aarch64).

## Apa yang perlu diberikan kepada pembantu anda

| Sumber | URL | Kegunaannya |
| **llms.txt** | `https://mapir.gitbook.io/chloros/llms.txt` | Indeks boleh dibaca mesin bagi setiap halaman dalam manual ini. |
| **Rujukan**CLI** | `https://mapir.gitbook.io/chloros/reference/cli-reference` | Antara muka arahan `chloros-cli` yang lengkap: setiap arahan, penanda, lalai, kod keluar, dan peraturan folder keluaran. Ditulis untuk penggunaan LLM. |
| **Rujukan SDK** | `https://mapir.gitbook.io/chloros/reference/sdk-reference` | Antara muka Python `chloros_sdk` yang lengkap: setiap kelas, tandatangan, pengecualian, dan contoh kerja. Ditulis untuk kegunaan LLM. |
| **Sebarang halaman sebagai Markdown mentah** | sambungkan `.md` ke URL halaman | contohnya `https://mapir.gitbook.io/chloros/reference/sdk-reference.md` memulangkan halaman sebagai Markdown mentah — sesuai untuk ditampal ke dalam tetingkap konteks atau diambil daripada ejen. |

Pautan dalam manual: [Rujukan CLI](reference/cli-reference.md) · [Rujukan SDK](reference/sdk-reference.md).

{% hint style="info" %}
Kedua-dua halaman rujukan ini lengkap bersendirian: seorang pembantu yang telah membaca salah satunya tidak memerlukan baki manual untuk menulis skrip yang betul.
{% endhint %}

## Biarkan pembantu anda menjalankan Chloros secara langsung: `chloros-mcp`

Semua yang di atas membolehkan pembantu anda *menulis* skrip atau arahan. Dengan `chloros-mcp`, seorang pembantu yang bertutur dalam [Model Context Protocol](https://modelcontextprotocol.io) (MCP), seperti Claude Desktop, Claude Code atau Cursor, mengendalikan Chloros itu sendiri. Anda bertanya dalam bahasa biasa dan ia memanggil alat pelayan:

* **Proses imej:** buat projek, import folder rakaman, pilih tetapan, proses, dan laporkan apa yang dieksport oleh larian.
* **Jalankan perkakasan:** sambungkan kamera LATTICE, susunan dan penderia cahaya DAQ, pratonton, tetapkan pendedahan dan warna, rakam, rekod, dan kemas kini firmware.

```text
You:  Process the flight in D:\Flights\2026-10-17 to reflectance and NDVI.

You:  Show me what the array sees, then capture reflectance every 10 seconds
      for 5 minutes.
```

Alat pelayan merangkumi apa yang dilakukan oleh [Python SDK](api-python-sdk.md). Ia dipasang bersama SDK, jadi biasanya tiada apa-apa lagi yang perlu dipasang: pada Windows, pemasang Chloros meletakkan `chloros-mcp.exe` dalam folder `Scripts` Python anda, dan pemasang Linux meletakkan `chloros-mcp` dalam `/usr/bin`.

**Claude Code:**

```bash
claude mcp add chloros -- chloros-mcp
```

**Claude Desktop:**tambahkan ini ke `claude_desktop_config.json` (**Tetapan > Pembangun > Sunting Konfig**membukanya), kemudian mulakan semula Claude Desktop.**Cursor** menggunakan kemasukan yang sama dalam `~/.cursor/mcp.json`.

```json
{
  "mcpServers": {
    "chloros": {"command": "chloros-mcp"}
  }
}
```

Jika klien mengatakan pelayan gagal dimulakan, `chloros-mcp` mungkin tidak berada pada `PATH` yang dilihatnya: masukkan laluan penuh sebaliknya (`where chloros-mcp` pada Windows, `which chloros-mcp` pada Linux).

Anda perlu memasang Chloros pada komputer yang sama dan log masuk di sana, dalam aplikasi atau dengan `chloros-cli login`, pada mana-mana peringkat berbayar Chloros+ (Copper atau ke atas). Pelayan tidak boleh log masuk untuk anda.

{% hint style="info" %}
Penyediaan klien untuk setiap aplikasi, pilihan, setiap alat dan bagaimana sesuatu pelaksanaan berkelakuan: [Pelayan MCP (Penolong AI)](reference/sdk-reference.md#mcp-server-ai-assistants) dalam Rujukan SDK.
{% endhint %}

## Resipi arahan

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

### 2. Pantau secara bersiri direktori tangkapan

```

Read https://mapir.gitbook.io/chloros/reference/sdk-reference.md (sections
"Quickstart" and "Post-Run Summary & Hints"). Write a Python script that
watches <path/to/captures> for new flight subfolders and runs
chloros_sdk.process_folder() with indices=["NDVI"] on each new one.
After each run, print every hint from result["summary"]["hints"] and treat
a run with zero image products as a failure for that folder.
```

### 3. Sambungkan tatasusunan LATTICE dan rakam

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
Skrip DAQ dari baris perintah sentiasa melalui keluarga `daq pool-*` (`pool-connect`, `pool-list`, `pool-latest`, `pool-stream`, `pool-record`, `pool-set-cap`, `pool-disconnect`). Subperintah `daq` lain yang mungkin dicipta oleh pembantu anda tidak tersedia dalam binaan yang diedarkan dan akan keluar dengan ralat.
{% endhint %}

## Mengapa skrip yang ditulis oleh AI berfungsi dengan baik bersama Chloros

Setiap satunya adalah tingkah laku sebenar yang disahkan bagi Chloros 1.3.0 — ia menghapuskan mod kegagalan klasik dalam automasi yang ditulis oleh mesin:

* **Tiada tarian persediaan.**Pembantu sambungan pintar SDK (`connect_camera`, `connect_array`, `connect_daq_sensor`) dan titik permulaan pemprosesan (`ChlorosLocal`, `process_folder`)**memulakan backend tempatan secara automatik**. Skrip yang dijana tidak memerlukan GUI dibuka atau pelayan yang dimulakan secara manual — ia hanya memerlukan pakej desktop/CLI dipasang.
* **Seluruh paip adalah satu panggilan.** `chloros_sdk.process_folder("path", indices=["NDVI"])` menjalankan import → penentukuran → pantulan → eksport indeks dari awal hingga akhir. Luas permukaan yang lebih kecil, lebih sedikit tempat bagi skrip yang dijana untuk melakukan kesilapan.
* **Jalankan tanpa output mendiagnosis sendiri.** Selepas `process()`, ringkasan jalankan dilampirkan pada keputusan, dan setiap petunjuk pemprosesan (contohnya *mengapa* sesuatu pelaksanaan tidak menghasilkan sebarang output) turut disiarkan semula sebagai `UserWarning` **Python** — jadi walaupun skrip yang tidak pernah memeriksa result dict akan memaparkan diagnosis tersebut.
* **CLI gagal dengan jelas.**Sebuah larian `chloros-cli process` yang meminta produk tetapi tidak menulis sebarang produk mencetak `Processing finished but wrote no image products.` dan**keluar dengan nilai tidak sifar**, jadi skrip shell dan CI mengesaninya dengan pemeriksaan kod keluar biasa. Jalankan yang berjaya melaporkan `Image products written: N`, ditambah `(plus M clamp masks)` apabila topeng ditulis. Perintah tangkapan juga menamakan setiap fail yang tidak dapat mereka tulis dan keluar dengan `1`.
* **Laluan relatif bermaksud apa yang dijangkakan oleh skrip.** Dengan backend tempatan, laluan output relatif (`-o out/` dalam CLI, `output_dir="out/"` dalam SDK) diselesaikan berdasarkan folder di mana arahan atau skrip dijalankan.

Satu ketidaksymmetri yang perlu diketahui oleh pembantu: `process()` dalam SDK sengaja **tidak** membangkitkan ralat pada larian sifar-hasil — sebaliknya ia melaporkan melalui ringkasan/petunjuk. Jika paip Python perlu berhenti apabila dijalankan kosong, semak ringkasan (resipi 2 melakukannya).

## Perlu Diingat

* **Log masuk Chloros+ diperlukan.**CLI dan SDK memerlukan tahap**berbayar** Chloros+, dikuatkuasakan di pihak pelayan: permintaan akan gagal dengan `401 AUTH_REQUIRED` apabila tidak log masuk dan `403 PLAN_UPGRADE_REQUIRED` pada tahap percuma. Jalankan `chloros-cli login` sekali pada setiap mesin sebelum menjalankan skrip yang dijana. Lihat [Log Masuk Chloros+](chloros+-login.md).
* **Perintah tangkapan mengendalikan perkakasan sebenar.** `lattice` / `daq` / Perintah `project`, objek sesi SDK dan alat `chloros-mcp` menyambung, menstrim, dan mencetuskan kamera serta sensor fizikal. Semak skrip yang dijana sebelum ia dijalankan buat pertama kali, dan jalankan ia dengan perkakasan hadir.
* **Semak keluaran secara rawak.** Sahkan folder produk dan beberapa nilai piksel sebelum menerbitkan keputusan. Secara khusus, TIFF reflektansi diskalakan mengikut sumber — baca tag XMP `Chloros:PixelScale` (fail LATTICE membawanya: 32768 = 1.0 reflektansi; Fail Survey3 tidak melakukannya, dan menggunakan 65535 = 1.0) bukannya menganggap pembahagi. Kedua-dua halaman rujukan mendokumentasikan perkara ini di bawah "Membaca piksel pantulan".
* **Kesilapan kecil yang boleh mengganggu kod yang dijana:**`pool-record` menulis ke dalam sistem fail**hos belakang** (default `~/Documents/DAQ Live View/`); pada mesin dengan beberapa antara muka rangkaian, utamakan `daq pool-connect --eth-host <ip-or-hostname>` berbanding penemuan automatik; gunakan `http://127.0.0.1:5000` (jangan sekali-kali `localhost`) di mana-mana URL belakang muncul; skrip yang menelusuri pokok keluaran projek harus melangkau folder `clamp_masks/` (fail `*_clampmask.tif` mereka adalah topeng, bukan imej — lihat [Format Imej Keluaran](output-image-formats.md#clamp-masks)); dan pada Windows di mana laluan panjang dilumpuhkan, pastikan folder projek pendek — folder projek yang lebih dalam daripada kira-kira 130 aksara akan ditolak sebelum pemprosesan bermula.
