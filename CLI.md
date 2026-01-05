# CLI : Baris Perintah

<figure><img src=".gitbook/assets/cli.JPG" alt=""><figcaption></figcaption></figure>

**Chloros CLI** menyediakan akses baris perintah yang berkuasa kepada enjin pemprosesan imej Chloros, membolehkan automasi, penskripan dan operasi tanpa kepala untuk aliran kerja pengimejan anda.

### Ciri Utama

* 🚀 **Automasi** - Pemprosesan kelompok skrip berbilang set data
* 🔗 **Integrasi** - Benamkan dalam aliran kerja dan saluran paip sedia ada
* 💻 **Operasi Tanpa Kepala** - Jalankan tanpa GUI
* 🌍 **Berbilang Bahasa** - Sokongan untuk 38 bahasa
* ⚡ **Pemprosesan Selari** - Skala secara dinamik kepada CPU anda (sehingga 16 pekerja selari)

### Keperluan

| Keperluan | Butiran |
| -------------------- | ------------------------------------------------------------------- |
| **Sistem Pengendalian** | Windows 10/11 (64-bit) |
| **Lesen** | Chloros+ ([pelan berbayar diperlukan](https://cloud.mapir.camera/pricing)) |
| **Memori** | 8GB RAM minimum (16GB disyorkan) |
| **Internet** | Diperlukan untuk pengaktifan lesen |
| **Ruang Cakera** | Berbeza mengikut saiz projek |

{% gaya petunjuk="amaran" %}
**Keperluan Lesen**: CLI memerlukan langganan Chloros+ berbayar. Pelan standard (percuma) tidak mempunyai akses CLI. Lawati [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing) untuk menaik taraf.
Petua {% %}

## Mula Pantas

### Pemasangan

CLI disertakan secara automatik dengan pemasang Chloros:

1. Muat turun dan jalankan **Chloros Installer.exe**

2. Lengkapkan wizard pemasangan
3. CLI dipasang pada: `C:\Program Files\Chloros\resources\cli\chloros-cli.exe`

{% gaya pembayang="berjaya" %}
Pemasang secara automatik menambah `chloros-cli` pada PATH sistem anda. Mulakan semula terminal anda selepas pemasangan.
Petua {% %}

### Persediaan Kali Pertama

Sebelum menggunakan CLI, aktifkan lesen Chloros+ anda:

```bash
# Login with your Chloros+ account
chloros-cli login user@example.com 'your_password'

# Check license status
chloros-cli status

# Process your first project
chloros-cli process "C:\Images\Dataset001"
```

### Penggunaan Asas

Proses folder dengan tetapan lalai:

```powershell
chloros-cli process "C:\Images\Dataset001"
```

***

## Rujukan Perintah

### Sintaks Umum

```
chloros-cli [global-options] <command> [command-options]
```

***

## Perintah

### `process` - Proses Imej

Proses imej dalam folder dengan penentukuran.

**Sintaks:**

```bash
chloros-cli process <input-folder> [options]
```

**Contoh:**

```powershell
chloros-cli process "C:\Datasets\Survey_001" --vignette --reflectance
```

#### Pilihan Perintah Proses

| Pilihan | Taip | Lalai | Penerangan |
| ---------------------- | ------- | -------------- | ------------------------------------------------------------------------------------ |
| `<input-folder>` | Laluan | _Diperlukan_ | Folder yang mengandungi imej berbilang spektrum RAW/JPG |
| `-o, --output` | Laluan | Sama seperti input | Folder output untuk imej yang diproses |
| `-n, --project-name` | Rentetan | Dijana secara automatik | Nama projek tersuai |
| `--vignette` | Benderakan | Didayakan | Dayakan pembetulan vignet |
| `--no-vignette` | Benderakan | - | Lumpuhkan pembetulan vignet |
| `--reflectance` | Benderakan | Didayakan | Dayakan penentukuran pantulan |
| `--no-reflectance` | Benderakan | - | Lumpuhkan penentukuran pantulan |
| `--ppk` | Benderakan | Dilumpuhkan | Gunakan pembetulan PPK daripada data penderia cahaya .daq |
| `--format` | Pilihan | TIFF (16-bit) | Format output: `TIFF (16-bit)`, `TIFF (32-bit, Percent)`, `PNG (8-bit)`, `JPG (8-bit)` |
| `--min-target-size` | Integer | Auto | Saiz sasaran minimum dalam piksel untuk pengesanan panel penentukuran |
| `--target-clustering` | Integer | Auto | Ambang pengelompokan sasaran (0-100) |
| `--exposure-pin-1` | Rentetan | Tiada | Dedahan kunci untuk model kamera (Pin 1) |
| `--exposure-pin-2` | Rentetan | Tiada | Dedahan kunci untuk model kamera (Pin 2) |
| `--recal-interval` | Integer | Auto | Selang penentukuran semula dalam saat |
| `--timezone-offset` | Integer | 0 | Zon waktu diimbangi dalam jam |

***

### `login` - Sahkan Akaun

Log masuk dengan bukti kelayakan Chloros+ anda untuk mendayakan pemprosesan CLI.

**Sintaks:**

```bash
chloros-cli login <email> <password>
```

**Contoh:**

```powershell
chloros-cli login user@example.com 'MyP@ssw0rd123'
```

{% gaya petunjuk="amaran" %}
**Watak Khas**: Gunakan petikan tunggal di sekitar kata laluan yang mengandungi aksara seperti `$`, `!` atau ruang.
Petua {% %}

**Keluaran:**<figure><img src=".gitbook/assets/cli login_w.JPG" alt=""><figcaption></figcaption></figure>***

### `logout` - Kosongkan Bukti Kelayakan

Kosongkan bukti kelayakan yang disimpan dan log keluar daripada akaun anda.

**Sintaks:**

```bash
chloros-cli logout
```

**Contoh:**

```powershell
chloros-cli logout
```

**Keluaran:**

```
✓ Logout successful
ℹ Credentials cleared from cache
```

{% hint style="info" %}
**Pengguna SDK**: Python SDK juga menyediakan kaedah `logout()` terprogram untuk mengosongkan bukti kelayakan dalam skrip Python. Lihat dokumentasi [Python SDK](api-python-sdk.md#logout) untuk butiran.
Petua {% %}

***

### `status` - Semak Status Lesen

Paparkan status lesen dan pengesahan semasa.

**Sintaks:**

```bash
chloros-cli status
```

**Contoh:**

```powershell
chloros-cli status
```

**Keluaran:**

```
╔══════════════════════════════════════╗
║     LICENSE & ACCOUNT INFORMATION    ║
╚══════════════════════════════════════╝

📧 Email: user@example.com
📋 Plan: Chloros+ Professional
🔓 API/CLI Access: Enabled
✓ Status: Active
```

***

### `export-status` - Semak Kemajuan Eksport

Pantau kemajuan eksport Thread 4 semasa atau selepas pemprosesan.

**Sintaks:**

```bash
chloros-cli export-status
```

**Contoh:**

```powershell
chloros-cli export-status
```

**Kes Penggunaan:** Panggil arahan ini semasa pemprosesan sedang berjalan untuk menyemak kemajuan eksport.***

### `language` - Urus Bahasa Antara Muka

Lihat atau tukar bahasa antara muka CLI.

**Sintaks:**

```bash
# Show current language
chloros-cli language

# List all available languages
chloros-cli language --list

# Set a specific language
chloros-cli language <language-code>
```

**Contoh:**

```powershell
# View current language
chloros-cli language

# List all 38 supported languages
chloros-cli language --list

# Change to Spanish
chloros-cli language es

# Change to Japanese
chloros-cli language ja
```

#### Bahasa yang Disokong (38 Jumlah)

| Kod | Bahasa | Nama Asli |
| ------- | ---------------------- | ---------------- |
| `en` | Inggeris | Inggeris |
| `es` | Sepanyol | Español |
| `pt` | Portugis | Português |
| `fr` | Perancis | Français |
| `de` | Jerman | Deutsch |
| `it` | Itali | Italiano |
| `ja` | Jepun | 日本語 |
| `ko` | Korea | 한국어 |
| `zh` | Cina (Mudah) | 简体中文 |
| `zh-TW` | Cina (Tradisional) | 繁體中文 |
| `ru` | Rusia | Русский |
| `nl` | Belanda | Belanda |
| `ar` | Bahasa Arab | العربية |
| `pl` | Poland | Polski |
| `tr` | Turki | Türkçe |
| `hi` | Hindi | हिंदी |
| `id` | Indonesia | Bahasa Indonesia |
| `vi` | Vietnam | Tiếng Việt |
| `th` | Thai | ไทย |
| `sv` | Sweden | Svenska |
| `da` | Denmark | Dansk |
| `no` | bahasa Norway | Norsk |
| `fi` | Bahasa Finland | Suomi |
| `el` | Greek | Ελληνικά |
| `cs` | Czech | Čeština |
| `hu` | Bahasa Hungary | Magyar |
| `ro` | Bahasa Romania | Română |
| `uk` | Ukraine | Українська |
| `pt-BR` | Portugis Brazil | Português Brasileiro |
| `zh-HK` | Kantonis | 粵語 |
| `ms` | Melayu | Bahasa Melayu |
| `sk` | Bahasa Slovak | Slovenčina |
| `bg` | Bahasa Bulgaria | Български |
| `hr` | Bahasa Croatia | Hrvatski |
| `lt` | Bahasa Lithuania | Lietuvių |
| `lv` | Bahasa Latvia | Latviešu |
| `et` | Estonia | Eesti |
| `sl` | Bahasa Slovenia | Slovenščina |

{% gaya petunjuk="berjaya" %}
**Kegigihan Automatik**: Pilihan bahasa anda disimpan ke `~/.chloros/cli_language.json` dan berterusan merentas semua sesi.
Petua {% %}

***

### `set-project-folder` - Tetapkan Folder Projek Lalai

Tukar lokasi folder projek lalai (dikongsi dengan GUI).

**Sintaks:**

```bash
chloros-cli set-project-folder <folder-path>
```

**Contoh:**

```powershell
chloros-cli set-project-folder "C:\Projects\2025"
```

***

### `get-project-folder` - Tunjukkan Folder Projek

Paparkan lokasi folder projek lalai semasa.

**Sintaks:**

```bash
chloros-cli get-project-folder
```

**Contoh:**

```powershell
chloros-cli get-project-folder
```

**Keluaran:**

```
ℹ Current project folder: C:\Projects\2025
```

***

### `reset-project-folder` - Tetapkan Semula kepada Lalai

Tetapkan semula folder projek ke lokasi lalai.

**Sintaks:**

```bash
chloros-cli reset-project-folder
```

***

## Pilihan Global

Pilihan ini digunakan untuk semua arahan:

| Pilihan | Taip | Lalai | Penerangan |
| --------------- | ------- | ------------- | ------------------------------------------------ |
| `--backend-exe` | Laluan | Auto dikesan | Laluan ke bahagian belakang boleh laku |
| `--port` | Integer | 5000 | Bahagian belakang API nombor port |
| `--restart` | Benderakan | - | Paksa mulakan semula hujung belakang (membunuh proses sedia ada) |
| `--version` | Benderakan | - | Tunjukkan maklumat versi dan keluar |
| `--help` | Benderakan | - | Tunjukkan maklumat bantuan dan keluar |

**Contoh dengan Pilihan Global:**

```powershell
chloros-cli --port 5001 process "C:\Datasets\Survey_001"
```

***

## Panduan Tetapan Pemprosesan

### Pemprosesan Selari

Chloros+ CLI **skala automatik**pemprosesan selari untuk dipadankan dengan keupayaan komputer anda:**Cara Ia Berfungsi:**

* Mengesan teras CPU dan RAM anda
* Memperuntukkan pekerja: **2× teras CPU** (menggunakan hyperthreading)
* **Maksimum: 16 pekerja selari** (untuk kestabilan)**Tingkat Sistem:**

| Jenis Sistem | CPU | RAM | Pekerja | Persembahan |
| ------------- | ---------- | -------- | -------- | --------------- |
| **High-End** | 16+ teras | 32+ GB | Sehingga 16 | Kelajuan maksimum |
| **Julat Pertengahan** | 8-15 teras | 16-31 GB | 8-16 | Kelajuan yang sangat baik |
| **Low-End** | 4-7 teras | 8-15 GB | 4-8 | Kelajuan yang baik |

{% gaya pembayang="berjaya" %}
**Pengoptimuman Automatik**: CLI secara automatik mengesan spesifikasi sistem anda dan mengkonfigurasi pemprosesan selari yang optimum. Tiada konfigurasi manual diperlukan!
Petua {% %}

### Kaedah Debayer

CLI menggunakan **Kualiti Tinggi (Lebih Cepat)** sebagai algoritma debayer lalai dan disyorkan:

| Kaedah | Kualiti | Kelajuan | Penerangan |
| --------------------------- | ------- | ----- | ------------------------------------------ |
| **Kualiti Tinggi (Lebih Cepat)** ⭐ | ⭐⭐⭐⭐ | ⚡⚡⚡ | Algoritma sedar tepi (lalai, disyorkan) |

### Pembetulan Vignette

**Apa yang dilakukannya:** Membetulkan kejatuhan cahaya pada tepi imej (sudut gelap biasa dalam imejan kamera).

* **Didayakan secara lalai** - Kebanyakan pengguna harus memastikan ini didayakan
* Gunakan `--no-vignette` untuk melumpuhkan

{% gaya petunjuk="berjaya" %}
**Cadangan**: Sentiasa dayakan pembetulan vignet untuk memastikan kecerahan seragam merentasi bingkai.
Petua {% %}

### Penentukuran Pantulan

Menukar nilai sensor mentah kepada peratusan pemantulan piawai menggunakan panel penentukuran.

* **Didayakan secara lalai** - Penting untuk analisis tumbuh-tumbuhan
* Memerlukan panel sasaran penentukuran dalam imej
* Gunakan `--no-reflectance` untuk melumpuhkan

{% hint style="info" %}
**Keperluan**: Pastikan panel penentukuran didedahkan dengan betul dan kelihatan dalam imej anda untuk penukaran pantulan yang tepat.
Petua {% %}

### Pembetulan PPK

**Apa yang dilakukannya:** Menggunakan pembetulan Kinematik Pasca Diproses menggunakan data log DAQ-A-SD untuk ketepatan GPS yang dipertingkatkan.

* **Dilumpuhkan secara lalai**
* Gunakan `--ppk` untuk mendayakan
* Memerlukan fail .daq dalam folder projek daripada penderia cahaya MAPIR DAQ-A-SD.

### Format Output

<table><thead><tr><th width="197">Format</th><th width="130.20001220703125">Kedalaman Bit</th><th width="116.5999755859375">Saiz Fail</th><th>Terbaik Untuk</th></tr></thead><tbody><tr><td><strong>TIFF (16-bit)</strong> ⭐</td><td>integer 16-bit</td><td>Besar</td><td>Analisis GIS, fotogrametri (disyorkan)</td></tr><tr><td><strong>TIFF (32-bit, Peratus)</strong></td><td>Apungan 32-bit</td><td>Sangat Besar</td><td>Analisis saintifik, penyelidikan</td></tr><tr><td><strong>X4PROTX000TX2 (8-bit)</strong></td><td>8-bit integer</td><td>Sederhana</td><td>Pemeriksaan visual, perkongsian web</td></tr><tr><td><strong>JPG (8-bit)</strong></td><td>8-bit integer</td><td>Kecil</td><td> pratonton, output termampat</td></tr></tbody></table>

***

## Automasi & Skrip

### Pemprosesan Kelompok PowerShell

Proses berbilang folder set data secara automatik:

```powershell
# process_all_datasets.ps1

$datasets = Get-ChildItem "C:\Datasets\2025" -Directory

foreach ($dataset in $datasets) {
    Write-Host "Processing $($dataset.Name)..." -ForegroundColor Cyan
    
    chloros-cli process $dataset.FullName `
        --vignette `
        --reflectance
    
    if ($LASTEXITCODE -eq 0) {
        Write-Host "✓ $($dataset.Name) complete" -ForegroundColor Green
    } else {
        Write-Host "✗ $($dataset.Name) failed" -ForegroundColor Red
    }
}

Write-Host "All datasets processed!" -ForegroundColor Green
```

### Skrip Kelompok Windows

Gelung mudah untuk pemprosesan kelompok:

```batch
@echo off
echo Starting batch processing...

for /d %%i in (C:\Datasets\2025\*) do (
    echo.
    echo ========================================
    echo Processing: %%i
    echo ========================================
    chloros-cli process "%%i"
    
    if %ERRORLEVEL% EQU 0 (
        echo SUCCESS: %%i processed
    ) else (
        echo ERROR: %%i failed
    )
)

echo.
echo All datasets processed!
pause
```

### Skrip Automasi Python

Automasi lanjutan dengan pengendalian ralat:

```python
import subprocess
import os
import sys
from pathlib import Path
from datetime import datetime

def process_dataset(input_folder):
    """Process a folder using Chloros CLI"""
    cmd = ['chloros-cli', 'process', str(input_folder)]
    
    # Execute command
    result = subprocess.run(
        cmd, 
        capture_output=True, 
        text=True,
        encoding='utf-8'
    )
    
    return result.returncode == 0, result.stdout, result.stderr

def main():
    """Process all datasets in a directory"""
    datasets_dir = Path('C:/Datasets/2025')
    log_file = Path('processing_log.txt')
    
    successful = []
    failed = []
    
    # Start processing
    print(f"Starting batch processing: {datetime.now()}")
    print(f"Scanning: {datasets_dir}")
    print("=" * 60)
    
    for dataset_folder in sorted(datasets_dir.iterdir()):
        if not dataset_folder.is_dir():
            continue
        
        print(f"\nProcessing: {dataset_folder.name}")
        
        success, stdout, stderr = process_dataset(dataset_folder)
        
        if success:
            print(f"✓ {dataset_folder.name} - SUCCESS")
            successful.append(dataset_folder.name)
        else:
            print(f"✗ {dataset_folder.name} - FAILED")
            failed.append(dataset_folder.name)
            
            # Log error details
            with open(log_file, 'a', encoding='utf-8') as f:
                f.write(f"\n=== {dataset_folder.name} - {datetime.now()} ===\n")
                f.write(f"STDOUT:\n{stdout}\n")
                f.write(f"STDERR:\n{stderr}\n")
    
    # Print summary
    print("\n" + "=" * 60)
    print(f"SUMMARY - Completed: {datetime.now()}")
    print(f"  Successful: {len(successful)}")
    print(f"  Failed: {len(failed)}")
    
    if failed:
        print(f"\nFailed folders:")
        for folder in failed:
            print(f"  - {folder}")
        print(f"\nCheck {log_file} for error details")
        sys.exit(1)
    else:
        print("\nAll datasets processed successfully!")
        sys.exit(0)

if __name__ == '__main__':
    main()
```

***

## Memproses Aliran Kerja

### Aliran Kerja Standard

1. **Input**: Folder yang mengandungi pasangan imej RAW/JPG
2. **Penemuan**: CLI auto-imbasan untuk fail imej yang disokong
3. **Pemprosesan**: Skala mod selari kepada teras CPU anda (Chloros+)
4. **Output**: Mencipta subfolder model kamera dengan imej yang diproses

### Contoh Struktur Output

```

MyProject/
├── project.json                             # Project metadata
├── 2025_0203_193056_008.JPG                # Original JPG
├── 2025_0203_193055_007.RAW                # Original RAW
└── Survey3N_RGN/                           # Processed outputs ✓
    ├── 2025_0203_193056_008_Reflectance.tif   # Calibrated reflectance
    ├── 2025_0203_193056_008_Target.tif        # Target detection
    └── ...
```

### Anggaran Masa Pemprosesan

Masa pemprosesan biasa untuk 100 imej (12MP setiap satu):

| Mod | Masa | Perkakasan |
| ----------------- | --------- | ------------------------------------------ |
| **Mod Selari** | 5-10 min | i7/Ryzen 7, 16GB RAM, SSD (sehingga 16 pekerja) |
| **Mod Selari** | 10-15 min | i5/Ryzen 5, 8GB RAM, HDD (sehingga 8 pekerja) |

{% hint style="info" %}
**Petua Prestasi**: Masa pemprosesan berbeza-beza berdasarkan kiraan imej, peleraian dan spesifikasi komputer.
Petua {% %}

***

## Menyelesaikan masalah

### CLI Tidak Ditemui

**Ralat:**

```
'chloros-cli' is not recognized as an internal or external command
```

**Penyelesaian:**

1. Sahkan lokasi pemasangan:

```powershell
dir "C:\Program Files\Chloros\resources\cli\chloros-cli.exe"
```

2. Gunakan laluan penuh jika tidak dalam PATH:

```powershell
"C:\Program Files\Chloros\resources\cli\chloros-cli.exe" process "C:\Datasets\Field_A"
```

3. Tambahkan pada PATH secara manual:
   * Sifat Sistem Terbuka → Pembolehubah Persekitaran
   * Edit pembolehubah PATH
   * Tambah: `C:\Program Files\Chloros\resources\cli`
   * Mulakan semula terminal

***

### Bahagian Belakang Gagal Dimulakan**Ralat:**

```

Backend failed to start within 30 seconds
```

**Penyelesaian:**

1. Semak sama ada bahagian belakang sudah berjalan (tutup dahulu)
2. Periksa Windows Firewall tidak menyekat
3. Cuba port yang berbeza:

```powershell
chloros-cli --port 5001 process "C:\Datasets\Field_A"
```

4. Paksa mulakan semula hujung belakang:

```powershell
chloros-cli --restart process "C:\Datasets\Field_A"
```

***

### Isu Lesen / Pengesahan**Ralat:**

```

Chloros+ license required for CLI access
```

**Penyelesaian:**

1. Sahkan anda mempunyai langganan Chloros+ yang aktif
2. Log masuk dengan kelayakan anda:

```powershell
chloros-cli login user@example.com 'password'
```

3. Semak status lesen:

```powershell
chloros-cli status
```

4. Hubungi sokongan: info@mapir.camera

***

### Tiada Imej Ditemui**Ralat:**

```

No images found in the specified folder
```

**Penyelesaian:**

1. Sahkan folder mengandungi format yang disokong (.RAW, .TIF, .JPG)
2. Semak laluan folder adalah betul (gunakan petikan untuk laluan dengan ruang)
3. Pastikan anda telah membaca kebenaran untuk folder tersebut
4. Semak sambungan fail adalah betul

***

### Memproses Gerai atau Gantung**Penyelesaian:**

1. Semak ruang cakera yang tersedia (pastikan cukup untuk output)
2. Tutup aplikasi lain untuk membebaskan memori
3. Kurangkan kiraan imej (proses dalam kelompok)

***

### Pelabuhan Sudah Digunakan**Ralat:**

```

Port 5000 is already in use
```

**Penyelesaian:**

Tentukan port yang berbeza:

```powershell
chloros-cli --port 5001 process "C:\Datasets\Field_A"
```

***

## Soalan Lazim

### S: Adakah saya memerlukan lesen untuk CLI?

**J:**Ya! CLI memerlukan lesen**Chloros+ berbayar.

* ❌ Pelan standard (percuma): CLI dilumpuhkan
* ✅ Pelan Chloros+ (berbayar): CLI didayakan sepenuhnya

Langgan di: [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing)

***

### S: Bolehkah saya menggunakan CLI pada pelayan tanpa GUI?**J:** Ya! CLI berjalan tanpa kepala sepenuhnya. Keperluan:

* Windows Server 2016 atau lebih baru
* Visual C++ Redistributable dipasang
* RAM yang mencukupi (minimum 8GB, disyorkan 16GB)
* Satu kali pengaktifan lesen GUI pada mana-mana mesin

***

### S: Di manakah imej yang diproses disimpan?**J:**Secara lalai, imej yang diproses disimpan dalam**folder yang sama seperti input** dalam subfolder model kamera (cth., `Survey3N_RGN/`).

Gunakan pilihan `-o` untuk menentukan folder output yang berbeza:

```powershell
chloros-cli process "C:\Input" -o "D:\Output"
```

***

### S: Bolehkah saya memproses berbilang folder sekaligus?**J:** Tidak secara langsung dalam satu arahan, tetapi anda boleh menggunakan skrip untuk memproses folder secara berurutan. Lihat bahagian [Automasi & Skrip](CLI.md#automation--scripting).***

### S: Bagaimanakah cara saya menyimpan output CLI ke fail log?**PowerShell:**

```powershell
chloros-cli process "C:\Datasets\Field_A" | Tee-Object -FilePath "processing.log"
```

**Batch:**

```batch
chloros-cli process "C:\Datasets\Field_A" > processing.log 2>&1
```

***

### S: Apakah yang berlaku jika saya menekan Ctrl+C semasa pemprosesan?**J:** CLI akan:

1. Hentikan pemprosesan dengan baik
2. Tutup bahagian belakang
3. Keluar dengan kod 130

Imej yang diproses separa mungkin kekal dalam folder output.

***

### S: Bolehkah saya mengautomasikan pemprosesan CLI?**J:** Sudah tentu! CLI direka untuk automasi. Lihat [Automasi & Skrip](CLI.md#automation--scripting) untuk contoh PowerShell, Batch dan Python.***

### S: Bagaimanakah cara saya menyemak versi CLI?**J:**

```powershell
chloros-cli --version
```

**Keluaran:**

```

Chloros CLI 1.0.2
```

***

## Mendapatkan Bantuan

### Bantuan Baris Perintah

Lihat maklumat bantuan terus dalam CLI:

```powershell
# General help
chloros-cli --help

# Command-specific help
chloros-cli process --help
chloros-cli login --help
chloros-cli language --help
```

### Saluran Sokongan

* **E-mel**: info@mapir.camera
* **Tapak web**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* **Harga**: [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing)***

## Contoh Lengkap

### Contoh 1: Pemprosesan Asas

Proses dengan tetapan lalai (vignet, pantulan):

```powershell
chloros-cli process "C:\Datasets\Field_A_2025_01_15"
```

***

### Contoh 2: Output Saintifik Berkualiti Tinggi

Terapung 32-bit TIFF:

```powershell
chloros-cli process "C:\Datasets\Field_A" ^
  --format "TIFF (32-bit, Percent)" ^
  --vignette ^
  --reflectance
```

***

### Contoh 3: Pemprosesan Pratonton Pantas

8-bit PNG tanpa penentukuran untuk semakan pantas:

```powershell
chloros-cli process "C:\Datasets\Field_A" ^
  --format "PNG (8-bit)" ^
  --no-vignette ^
  --no-reflectance
```

***

### Contoh 4: Pemprosesan Dibetulkan PPK

Gunakan pembetulan PPK dengan pemantulan:

```powershell
chloros-cli process "C:\Datasets\Field_A" ^
  --ppk ^
  --reflectance
```

***

### Contoh 5: Lokasi Output Tersuai

Proses ke pemacu yang berbeza dengan format tertentu:

```powershell
chloros-cli process "C:\Input\Raw_Images" ^
  -o "D:\Output\Processed" ^
  --format "TIFF (16-bit)"
```

***

### Contoh 6: Aliran Kerja Pengesahan

Aliran pengesahan lengkap:

```powershell
# Step 1: Login
chloros-cli login user@example.com 'MyP@ssw0rd'

# Step 2: Verify status
chloros-cli status

# Step 3: Process images
chloros-cli process "C:\Datasets\Field_A"

# Step 4: Logout (optional, when switching accounts)
chloros-cli logout
```

***

### Contoh 7: Penggunaan Pelbagai Bahasa

Tukar bahasa antara muka:

```powershell
# List available languages
chloros-cli language --list

# Change to Spanish
chloros-cli language es

# Process with Spanish interface
chloros-cli process "C:\Vuelos\Campo_A"

# Change back to English
chloros-cli language en
```