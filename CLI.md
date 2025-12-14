# CLI: baris perintah

<figure><img src=".gitbook/assets/cli.JPG" alt=""><figcaption></figcaption></figure>

** CLI ** CLI ** menyediakan akses baris arahan yang kuat ke enjin pemprosesan imej kloros, membolehkan automasi, skrip, dan operasi tanpa kepala untuk aliran kerja pengimejan anda.

### Ciri -ciri utama

*🚀 ** Automasi ** - pemprosesan batch skrip pelbagai dataset
*🔗 ** Integrasi ** - Benamkan dalam aliran kerja dan saluran paip yang ada
*💻 ** Operasi tanpa kepala ** - berlari tanpa GUI
*🌍 ** pelbagai bahasa ** - Sokongan untuk 38 bahasa
*⚡ ** Pemprosesan Selari ** - Skala secara dinamik ke CPU anda (sehingga 16 pekerja selari)

Keperluan ###

| Keperluan | Butiran |
| -------------------- | ------------------------------------------------------------------- |
| ** Sistem Operasi ** | Windows 10/11 (64-bit) |
| ** Lesen ** | Chloros+ ([pelan berbayar diperlukan](https://cloud.mapir.camera/pricing)) |
| ** Memory ** | 8GB RAM Minimum (16GB disyorkan) |
| ** Internet ** | Diperlukan untuk Pengaktifan Lesen |
| ** ruang cakera ** | Berbeza mengikut saiz projek |

{% hint style="warning" %}
** Keperluan Lesen **: CLI memerlukan langganan Chloros+ yang dibayar. Pelan standard (percuma) tidak mempunyai akses CLI. Lawati [https://cloud.mapir.camera/pricingś(https://cloud.mapir.camera/pricing) untuk menaik taraf.
{% endhint %}

## Permulaan cepat

### Pemasangan

CLI secara automatik disertakan dengan pemasang kloros:

1. Muat turun dan jalankan ** chloros installer.exe **
2. Lengkapkan Wizard Pemasangan
3. CLI Dipasang ke: `C: \ Program Files \ Chloros \ Resources \ Cli \ Chloros-cli.exe`

{% hint style="success" %}
Pemasang secara automatik menambah `chloros-cli` ke laluan sistem anda. Mulakan semula terminal anda selepas pemasangan.
{% endhint %}

### Persediaan kali pertama

Sebelum menggunakan CLI, aktifkan lesen Chloros+ anda:

```bash
# Login with your Chloros+ account
chloros-cli login user@example.com 'your_password'

# Check license status
chloros-cli status

# Process your first project
chloros-cli process "C:\Images\Dataset001"
```

### Penggunaan asas

Proses folder dengan tetapan lalai:

```powershell
chloros-cli process "C:\Images\Dataset001"
```

***

## Rujukan arahan

### Sintaks Umum

```
chloros-cli [global-options] <command> [command-options]
```

***

## Perintah

### `Proses` - Proses gambar

Proses imej dalam folder dengan penentukuran.

** Sintaks: **

```bash
chloros-cli process <input-folder> [options]
```

** Contoh: **

```powershell
chloros-cli process "C:\Datasets\Survey_001" --vignette --reflectance
```

#### Pilihan arahan proses

| Pilihan | Jenis | Lalai | Penerangan |
| --------------------- | ------- | -------------- | -------------------------------------------------------------------------------------- |
| `<input-folder>` | Jalan | _Required_ | Folder yang mengandungi imej multispektral mentah/jpg |
| `-O, --Output` | Jalan | Sama seperti input | Folder Output untuk Imej yang Diproses |
| `-n,-project-name` | String | Auto-Generated | Nama Projek Kustom |
| `-Vignette` | Bendera | Didayakan | Dayakan Pembetulan Vignette |
| `--no-vignette` | Bendera | - | Lumpuhkan Pembetulan Vignette |
| `--Reflectance` | Bendera | Didayakan | Dayakan penentukuran refleksi |
| `--no-reflectance` | Bendera | - | Lumpuhkan penentukuran refleksi |
| `--ppk` | Bendera | Dilumpuhkan | Sapukan pembetulan PPK dari .daq Light Sensor Data |
| `--Format` | Pilihan | TIFF (16-bit) | Format output: `tiff (16-bit)`, `tiff (32-bit, peratus)`, `png (8-bit)`, `jpg (8-bit)` |
| `--min-target-saiz` | Integer | Auto | Saiz sasaran minimum dalam piksel untuk pengesanan panel penentukuran |
| `-target-clustering` | Integer | Auto | Sasaran Clustering Ambang (0-100) |
| `-exposure-pin-1` | String | Tiada | Pendedahan kunci untuk model kamera (pin 1) |
| `-exposure-pin-2` | String | Tiada | Pendedahan kunci untuk model kamera (pin 2) |
| `--recal-interval` | Integer | Auto | Selang pengubahsuaian dalam beberapa saat |
| `--TimeZone-offset` | Integer | 0 | TimeZone diimbangi dalam jam |

***

### `Login` - Mengesahkan akaun

Log masuk dengan kelayakan Chloros+ anda untuk membolehkan pemprosesan CLI.

** Sintaks: **

```bash
chloros-cli login <email> <password>
```

** Contoh: **

```powershell
chloros-cli login user@example.com 'MyP@ssw0rd123'
```

{% hint style="warning" %}
** Watak Khas **: Gunakan petikan tunggal di sekitar kata laluan yang mengandungi aksara seperti `$`, `!`, Atau ruang.
{% endhint %}

** output: **

<figure><img src=".gitbook/assets/cli login_w.JPG" alt=""><figcaption></figcaption></figure>

***

### `logout` - kelayakan jelas

Kosongkan kelayakan tersimpan dan logout dari akaun anda.

** Sintaks: **

```bash
chloros-cli logout
```

** Contoh: **

```powershell
chloros-cli logout
```

** output: **

```
✓ Logout successful
ℹ Credentials cleared from cache
```

***

### `status` - periksa status lesen

Paparkan lesen semasa dan status pengesahan.

** Sintaks: **

```bash
chloros-cli status
```

** Contoh: **

```powershell
chloros-cli status
```

** output: **

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

### `eksport -status` - periksa kemajuan eksport

Pantau Thread 4 Kemajuan eksport semasa atau selepas diproses.

** Sintaks: **

```bash
chloros-cli export-status
```

** Contoh: **

```powershell
chloros-cli export-status
```

** Gunakan Kes: ** Panggil arahan ini semasa pemprosesan sedang berjalan untuk memeriksa kemajuan eksport.

***

### `Bahasa` - Menguruskan bahasa antara muka

Lihat atau ubah bahasa antara muka CLI.

** Sintaks: **

```bash
# Show current language
chloros-cli language

# List all available languages
chloros-cli language --list

# Set a specific language
chloros-cli language <language-code>
```

** Contoh: **

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

#### bahasa yang disokong (38 jumlah)

| Kod | Bahasa | Nama asli |
| ------- | --------------------- | ---------------- |
| `en` | Bahasa Inggeris | Bahasa Inggeris |
| `es`    | Spanish               | Español          |
| `pt`    | Portuguese            | Português        |
| `fr`    | French                | Français         |
| `de` | Jerman | Deutsch |
| `It` | Itali | Italiano |
| `ja`    | Japanese              | 日本語              |
| `ko`    | Korean                | 한국어              |
| `ZH`    | Chinese (Simplified)  | 简体中文             |
| `ZH-TW` | Chinese (Traditional) | 繁體中文             |
| `ru`    | Russian               | Русский          |
| `nl` | Belanda | Nederlands |
| `ar`    | Arabic                | العربية          |
| `pl` | Poland | Polski |
| `tr`    | Turkish               | Türkçe           |
| `Hi`    | Hindi                 | हिंदी            |
| `id` | Indonesia | Bahasa Indonesia |
| `VI`    | Vietnamese            | Tiếng Việt       |
| `th`    | Thai                  | ไทย              |
| `sv` | Sweden | Svenska |
| `da` | Denmark | Dansk |
| `Tidak` | Norway | Norsk |
| `fi` | Finland | Suomi |
| `El`    | Greek                 | Ελληνικά         |
| `cs`    | Czech                 | Čeština          |
| `hu` | Hungary | Magyar |
| `ro`    | Romanian              | Română           |
| `UK`    | Ukrainian             | Українська       |
| `pt-br` | Brazilian Portuguese  | Português Brasileiro |
| `ZH-HK` | Cantonese             | 粵語             |
| `MS` | Melayu | Bahasa Melayu |
| `SK`    | Slovak                | Slovenčina       |
| `bg`    | Bulgarian             | Български        |
| `hr` | Croatian | Hrvatski |
| `lt`    | Lithuanian            | Lietuvių         |
| `lv`    | Latvian               | Latviešu         |
| `et` | Estonian | Eesti |
| `SL`    | Slovenian             | Slovenščina      |

{% hint style="success" %}
**Automatic Persistence**: Your language preference is saved to `~/.Chloros/cli_language.json` dan berterusan di semua sesi.
{% endhint %}

***

### `Set-Project-Folder`-Tetapkan Folder Projek Lalai

Tukar lokasi folder projek lalai (dikongsi dengan GUI).

** Sintaks: **

```bash
chloros-cli set-project-folder <folder-path>
```

** Contoh: **

```powershell
chloros-cli set-project-folder "C:\Projects\2025"
```

***

### `Get-Project-Folder`-Tunjukkan Folder Projek

Paparkan lokasi folder projek lalai semasa.

** Sintaks: **

```bash
chloros-cli get-project-folder
```

** Contoh: **

```powershell
chloros-cli get-project-folder
```

** output: **

```
ℹ Current project folder: C:\Projects\2025
```

***

### `Reset-Project-Folder`-Tetapkan semula ke Lalai

Tetapkan semula folder projek ke lokasi lalai.

** Sintaks: **

```bash
chloros-cli reset-project-folder
```

***

## Pilihan global

Pilihan ini dikenakan untuk semua arahan:

| Pilihan | Jenis | Lalai | Penerangan |
| --------------- | ------- | ------------- | ------------------------------------------------ |
| `-backend-exe` | Jalan | Auto-detected | Laluan ke Backend Executable |
| `--port` | Integer | 5000 | Nombor Port API Backend |
| `--Restart` | Bendera | - | Memaksa memulakan semula backend (membunuh proses sedia ada) |
| `--version` | Bendera | - | Tunjukkan maklumat versi dan keluar |
| `--elp` | Bendera | - | Tunjukkan Maklumat Bantuan dan Keluar |

** Contoh dengan pilihan global: **

```powershell
chloros-cli --port 5001 process "C:\Datasets\Survey_001"
```

***

## Processing Settings Guide

### Parallel Processing

Chloros+ CLI **automatically scales** parallel processing to match your computer's capabilities:

**How It Works:**

* Detects your CPU cores and RAM
* Allocates workers: **2× CPU cores** (uses hyperthreading)
* **Maximum: 16 parallel workers** (for stability)

**System Tiers:**

| System Type   | CPU        | RAM      | Workers  | Performance     |
| ------------- | ---------- | -------- | -------- | --------------- |
| **High-End**  | 16+ cores  | 32+ GB   | Up to 16 | Maximum speed   |
| **Mid-Range** | 8-15 cores | 16-31 GB | 8-16     | Excellent speed |
| **Low-End**   | 4-7 cores  | 8-15 GB  | 4-8      | Good speed      |

{% hint style="success" %}
**Automatic Optimization**: The CLI automatically detects your system specs and configures optimal parallel processing. No manual configuration needed!
{% endhint %}

### Debayer Methods

The CLI uses **High Quality (Faster)** as the default and recommended debayer algorithm:

| Method                      | Quality | Speed | Description                                 |
| --------------------------- | ------- | ----- | ------------------------------------------- |
| **High Quality (Faster)** ⭐ | ⭐⭐⭐⭐    | ⚡⚡⚡   | Edge-aware algorithm (default, recommended) |

### Vignette Correction

**What it does:** Corrects light falloff at image edges (darker corners common in camera imagery).

* **Enabled by default** - Most users should keep this enabled
* Use `--no-vignette` untuk melumpuhkan

{% hint style="success" %}
** Cadangan **: Sentiasa aktifkan pembetulan vignette untuk memastikan kecerahan seragam merentasi bingkai.
{% endhint %}

### penentukuran refleksi

Menukar nilai sensor mentah ke peratusan refleksi standard menggunakan panel penentukuran.

* **Diaktifkan secara lalai** - penting untuk analisis tumbuh -tumbuhan
* Memerlukan panel sasaran penentukuran dalam gambar
* Gunakan `--no-reflectance` untuk melumpuhkan

{% hint style="info" %}
** Keperluan **: Pastikan panel penentukuran terdedah dengan betul dan dapat dilihat dalam imej anda untuk penukaran refleksi yang tepat.
{% endhint %}

### pembetulan ppk

** Apa yang dilakukannya: ** Menggunakan pembetulan kinematik selepas diproses menggunakan data log DAQ-A-SD untuk ketepatan GPS yang lebih baik.

* **dilumpuhkan secara lalai** * Gunakan `--ppk` to enable
* Requires .daq files in project folder from MAPIR DAQ-A-SD light sensor.

### Output Formats

<table><thead><tr><th width="197">Format</th><th width="130.20001220703125">Bit Depth</th><th width="116.5999755859375">File Size</th><th>Best For</th></tr></thead><tbody><tr><td><strong>TIFF (16-bit)</strong> ⭐</td><td>16-bit integer</td><td>Large</td><td>GIS analysis, photogrammetry (recommended)</td></tr><tr><td><strong>TIFF (32-bit, Percent)</strong></td><td>32-bit float</td><td>Very Large</td><td>Scientific analysis, research</td></tr><tr><td><strong>PNG (8-bit)</strong></td><td>8-bit integer</td><td>Medium</td><td>Visual inspection, web sharing</td></tr><tr><td><strong>JPG (8-bit)</strong></td><td>8-bit integer</td><td>Small</td><td>Quick preview, compressed output</td></tr></tbody></table>

***

## Automation & Scripting

### PowerShell Batch Processing

Process multiple dataset folders automatically:

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

### skrip batch windows

Gelung mudah untuk pemprosesan batch:

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

### skrip automasi python

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

## Pemprosesan aliran kerja

### aliran kerja standard

1. ** Input **: Folder yang mengandungi pasangan gambar mentah/jpg
2. ** Discovery **: CLI Auto-Scan untuk fail imej yang disokong
3. ** Pemprosesan **: Skala mod selari ke teras CPU anda (chloros+)
4. ** Output **: Membuat subfolder model kamera dengan imej yang diproses

### Contoh struktur output

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

### anggaran masa pemprosesan

Masa pemprosesan biasa untuk 100 imej (12MP setiap satu):

| Mod | Masa | Perkakasan |
| ----------------- | --------- | -------------------------------------------- |
| ** Mod selari ** | 5-10 min | I7/Ryzen 7, 16GB RAM, SSD (sehingga 16 pekerja) |
| ** Mod selari ** | 10-15 min | I5/Ryzen 5, 8GB RAM, HDD (sehingga 8 pekerja) |

{% hint style="info" %}
** Petua Prestasi **: Masa pemprosesan berbeza -beza berdasarkan kiraan imej, resolusi, dan spesifikasi komputer.
{% endhint %}

***

## Penyelesaian masalah

### CLI tidak dijumpai

** Ralat: **

```
'chloros-cli' is not recognized as an internal or external command
```

** Penyelesaian: **

1. Sahkan Lokasi Pemasangan:

```powershell
dir "C:\Program Files\Chloros\resources\cli\chloros-cli.exe"
```

2. Gunakan jalan penuh jika tidak di jalan:

```powershell
"C:\Program Files\Chloros\resources\cli\chloros-cli.exe" process "C:\Datasets\Field_A"
```

3. Add to PATH manually:
   * Open System Properties → Environment Variables
   * Edit PATH variable
   * Add: `C: \ Program Files \ Chloros \ Resources \ Cli`
   * Mulakan semula terminal

***

### backend gagal bermula

** Ralat: **

```
Backend failed to start within 30 seconds
```

** Penyelesaian: **

1. Periksa jika backend sudah berjalan (tutup terlebih dahulu)
2. Periksa Windows Firewall tidak menyekat
3. Cuba pelabuhan yang berbeza:

```powershell
chloros-cli --port 5001 process "C:\Datasets\Field_A"
```

4. Force Restart backend:

```powershell
chloros-cli --restart process "C:\Datasets\Field_A"
```

***

### Lesen / Isu Pengesahan

** Ralat: **

```
Chloros+ license required for CLI access
```

** Penyelesaian: **

1. Sahkan anda mempunyai langganan Chloros+ aktif
2. Log masuk dengan kelayakan anda:

```powershell
chloros-cli login user@example.com 'password'
```

3. Periksa status lesen:

```powershell
chloros-cli status
```

4. Sokongan Hubungi: info@mapir.camera

***

### tiada gambar yang dijumpai

** Ralat: **

```
No images found in the specified folder
```

** Penyelesaian: **

1. Sahkan folder mengandungi format yang disokong (.raw, .tif, .jpg)
2. Semak laluan folder betul (gunakan petikan untuk laluan dengan ruang)
3. Pastikan anda telah membaca kebenaran untuk folder
4. Semak sambungan fail betul

***

### memproses gerai atau menggantung

** Penyelesaian: **

1. Semak ruang cakera yang tersedia (pastikan cukup untuk output)
2. Tutup aplikasi lain untuk memori percuma
3. Kurangkan kiraan imej (proses dalam kelompok)

***

### port sudah digunakan

** Ralat: **

```
Port 5000 is already in use
```

** Penyelesaian: **

Tentukan port yang berbeza:

```powershell
chloros-cli --port 5001 process "C:\Datasets\Field_A"
```

***

## FAQ

### Q: Do I need a license for the CLI?

**A:** Yes! The CLI requires a paid **Chloros+ license**.

* ❌ Standard (free) plan: CLI disabled
* ✅ Chloros+ (paid) plans: CLI fully enabled

Subscribe at: [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing)

***

### Q: Can I use the CLI on a server without GUI?

**A:** Yes! The CLI runs completely headless. Requirements:

* Windows Server 2016 or later
* Visual C++ Redistributable installed
* Sufficient RAM (8GB minimum, 16GB recommended)
* One-time GUI license activation on any machine

***

### Q: Where are processed images saved?

**A:** By default, processed images are saved in the **same folder as input** in camera-model subfolders (e.g., `Survey3n_rgn/`).

Gunakan pilihan `-O` untuk menentukan folder output yang berbeza:

```powershell
chloros-cli process "C:\Input" -o "D:\Output"
```

***

### Q: Bolehkah saya memproses pelbagai folder sekaligus?

** A: ** Tidak secara langsung dalam satu arahan, tetapi anda boleh menggunakan skrip untuk memproses folder secara berurutan. Lihat [Automasi & Skrip](CLI.md#Automasi-Scripting).

***

### Q: Bagaimana saya menyimpan output CLI ke fail log?

** PowerShell: **

```powershell
chloros-cli process "C:\Datasets\Field_A" | Tee-Object -FilePath "processing.log"
```

** batch: **

```batch
chloros-cli process "C:\Datasets\Field_A" > processing.log 2>&1
```

***

### Q: Apa yang berlaku jika saya menekan Ctrl+C semasa pemprosesan?

** A: ** CLI akan:

1. Hentikan pemprosesan dengan anggun
2. Tutup backend
3. Keluar dengan kod 130

Imej yang diproses sebahagiannya mungkin kekal dalam folder output.

***

### Q: Bolehkah saya mengautomasikan pemprosesan CLI?

** A: ** Sudah tentu! CLI direka untuk automasi. Lihat [Automasi & Skrip](CLI.md#Automasi-Scripting) untuk contoh PowerShell, Batch, dan Python.

***

### Q: Bagaimana saya menyemak versi CLI?

**A:**

```powershell
chloros-cli --version
```

** output: **

```
Chloros CLI 1.0.2
```

***

## Mendapatkan bantuan

### Bantuan baris arahan

Lihat maklumat bantuan secara langsung di CLI:

```powershell
# General help
chloros-cli --help

# Command-specific help
chloros-cli process --help
chloros-cli login --help
chloros-cli language --help
```

### Support Channels

* **Email**: info@mapir.camera
* **Website**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* **Pricing**: [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing)

***

## Complete Examples

### Example 1: Basic Processing

Process with default settings (vignette, reflectance):

```powershell
chloros-cli process "C:\Datasets\Field_A_2025_01_15"
```

***

### Contoh 2: Output saintifik berkualiti tinggi

Tiff terapung 32-bit:

```powershell
chloros-cli process "C:\Datasets\Field_A" ^
  --format "TIFF (32-bit, Percent)" ^
  --vignette ^
  --reflectance
```

***

### Contoh 3: Pemprosesan Pratonton Cepat

8-bit PNG tanpa penentukuran untuk semakan cepat:

```powershell
chloros-cli process "C:\Datasets\Field_A" ^
  --format "PNG (8-bit)" ^
  --no-vignette ^
  --no-reflectance
```

***

### Contoh 4: Pemprosesan yang diperbetulkan PPK

Sapukan pembetulan PPK dengan pemantulan:

```powershell
chloros-cli process "C:\Datasets\Field_A" ^
  --ppk ^
  --reflectance
```

***

### Contoh 5: Lokasi output tersuai

Proses ke pemacu yang berbeza dengan format tertentu:

```powershell
chloros-cli process "C:\Input\Raw_Images" ^
  -o "D:\Output\Processed" ^
  --format "TIFF (16-bit)"
```

***

### Contoh 6: Aliran kerja pengesahan

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

### Contoh 7: Penggunaan pelbagai bahasa

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
