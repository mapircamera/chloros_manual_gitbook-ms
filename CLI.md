# CLI: baris arahan

<figure><img src=".gitbook/assets/cli.JPG" alt=""><figcaption></figcaption></figure>

**Chloros CLI** menyediakan akses baris arahan yang kuat ke enjin pemprosesan imej kloros, membolehkan automasi, skrip, dan operasi tanpa kepala untuk aliran kerja pengimejan anda.

### Ciri -ciri utama

* 🚀 **Automasi** - pemprosesan batch skrip pelbagai dataset
* 🔗 **Integrasi** - Benamkan dalam aliran kerja dan saluran paip yang ada
* 💻 **Operasi tanpa kepala** - Jalankan Tanpa GUI
* 🌍 **pelbagai bahasa** - Sokongan untuk 38 bahasa
* ⚡ **Pemprosesan Selari**- Skala secara dinamik ke CPU anda (sehingga 16 pekerja selari)

### Keperluan

| Keperluan          | Perincian                                                             |
| -------------------- | ------------------------------------------------------------------- |
|**sistem operasi**| Windows 10/11 (64-bit)                                              |
|**Lesen**| Chloros+ ([paid plan required](https://cloud.mapir.camera/pricing)) |
|**Memori**| Minimum RAM 8GB (16GB disyorkan)                                  |
|**Internet**| Diperlukan untuk pengaktifan lesen                                     |
|**ruang cakera**| Berbeza mengikut saiz projek                                              |

{% hint style="warning" %}**Keperluan Lesen**: CLI memerlukan langganan Chloros+ yang dibayar. Pelan standard (percuma) tidak mempunyai akses CLI. Lawati [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing) untuk menaik taraf.
{% endhint %}

## Permulaan cepat

### Pemasangan

CLI secara automatik disertakan dengan pemasang kloros:

1. Muat turun dan jalankan**chloros installer.exe**2. Lengkapkan Wizard Pemasangan
3. CLI dipasang ke:`C:\Program Files\Chloros\resources\cli\chloros-cli.exe`

{% hint style="success" %}
Pemasang menambah secara automatik`chloros-cli`ke laluan sistem anda. Mulakan semula terminal anda selepas pemasangan.
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
```***## Rujukan arahan

### Sintaks Umum

```
chloros-cli [global-options] <command> [command-options]
```***## Arahan

### `process` - Process Images

Proses imej dalam folder dengan penentukuran.**Sintaks:**```bash
chloros-cli process <input-folder> [options]
```**Contoh:**```powershell
chloros-cli process "C:\Datasets\Survey_001" --vignette --reflectance
```

#### Pilihan Perintah Proses

| Pilihan                | Jenis    | Lalai        | Penerangan                                                                            |
| --------------------- | ------- | -------------- | ------------------------------------------------------------------------------ |
| `<input-folder>`      | Jalan    | _Required_     | Folder yang mengandungi gambar multispektral mentah/jpg                                         |
| `-o, --output`        | Jalan    | Sama seperti input  | Folder output untuk imej yang diproses                                                     |
| `-n, --project-name`  | Rentetan  | Auto-dihasilkan | Nama projek tersuai                                                                    |
| `--vignette`          | Bendera    | Didayakan        | Dayakan pembetulan vignette                                                             |
| `--no-vignette`       | Bendera    | -              | Lumpuhkan pembetulan vignette                                                            |
| `--reflectance`       | Bendera    | Didayakan        | Dayakan penentukuran refleksi                                                         |
| `--no-reflectance`    | Bendera    | -              | Lumpuhkan penentukuran refleksi                                                        |
| `--ppk`               | Bendera    | Kurang upaya       | Sapukan pembetulan PPK dari data sensor cahaya .daq                                      |
| `--format`            | Pilihan  | TIFF (16-bit)  | Output format: `TIFF (16-bit)`, `TIFF (32-bit, Percent)`, `PNG (8-bit)`, `JPG (8-bit)` |
| `--min-target-size`   | Integer | Auto           | Saiz sasaran minimum dalam piksel untuk pengesanan panel penentukuran                          |
| `--target-clustering` | Integer | Auto           | Ambang sasaran clustering (0-100)                                                    |
| `--exposure-pin-1`    | Rentetan  | Tiada           | Pendedahan kunci untuk model kamera (pin 1)                                                 |
| `--exposure-pin-2`    | Rentetan  | Tiada           | Pendedahan kunci untuk model kamera (pin 2)                                                 |
| `--recal-interval`    | Integer | Auto           | Selang pengubahsuaian dalam beberapa saat                                                      |
| `--timezone-offset`   | Integer | 0              | Zon waktu diimbangi dalam beberapa jam                                                               |***### `login` - Authenticate Account 

Log masuk dengan kelayakan kloros+ anda untuk membolehkan pemprosesan CLI.**Sintaks:**```bash
chloros-cli login <email> <password>
```**Contoh:**```powershell
chloros-cli login user@example.com 'MyP@ssw0rd123'
```

{% hint style="warning" %}**Watak Khas**: Gunakan petikan tunggal di sekitar kata laluan yang mengandungi aksara seperti`$`, `!`, atau ruang.
{% endhint %}**Output:**<figure><img src=".gitbook/assets/cli login_w.JPG" alt=""><figcaption></figcaption></figure>***### `logout` - Clear Credentials

Kosongkan kelayakan tersimpan dan logout dari akaun anda.**Sintaks:**```bash
chloros-cli logout
```**Contoh:**```powershell
chloros-cli logout
```**Output:**```
✓ Logout successful
ℹ Credentials cleared from cache
```***### `status` - Check License Status

Paparkan lesen semasa dan status pengesahan.**Sintaks:**```bash
chloros-cli status
```**Contoh:**```powershell
chloros-cli status
```**Output:**```
╔══════════════════════════════════════╗
║     LICENSE & ACCOUNT INFORMATION    ║
╚══════════════════════════════════════╝

📧 Email: user@example.com
📋 Plan: Chloros+ Professional
🔓 API/CLI Access: Enabled
✓ Status: Active
```***### `export-status` - Check Export Progress

Pantau Thread 4 Kemajuan eksport semasa atau selepas diproses.**Sintaks:**```bash
chloros-cli export-status
```**Contoh:**```powershell
chloros-cli export-status
```**Gunakan kes:**Call this command while processing is running to check export progress.***### `language` - Manage Interface Language

Lihat atau ubah bahasa antara muka CLI.**Sintaks:**```bash
# Show current language
chloros-cli language

# List all available languages
chloros-cli language --list

# Set a specific language
chloros-cli language <language-code>
```**Contoh:**```powershell
# View current language
chloros-cli language

# List all 38 supported languages
chloros-cli language --list

# Change to Spanish
chloros-cli language es

# Change to Japanese
chloros-cli language ja
```

#### Bahasa yang disokong (38 jumlah)

| Kod    | Bahasa              | Nama asli      |
| ------- | --------------------- | ---------------- |
| `en`    | Bahasa Inggeris               | Bahasa Inggeris          |
| `es`    | Sepanyol               | Español          |
| `pt`    | Portugis            | Português        |
| `fr`    | Perancis                | Français         |
| `de`    | Jerman                | Deutsch          |
| `it`    | Bahasa Itali               | Italiano         |
| `ja`    | Jepun              | 日本語              |
| `ko`    | Korea                | 한국어              |
| `zh`    | Cina (dipermudahkan)  | 简体中文             |
| `zh-TW` | Cina (tradisional) | 繁體中文             |
| `ru`    | Rusia               | Р й          |
| `nl`    | Belanda                 | Nederlands       |
| `ar`    | Arab                | الajar          |
| `pl`    | Menggilap                | Polski           |
| `tr`    | Turki               | Türkçe           |
| `hi`    | Hindi                 | हिंदी            |
| `id`    | Indonesia            | Bahasa Indonesia |
| `vi`    | Vietnam            | Tiếng việt       |
| `th`    | Thai                  | ไทย              |
| `sv`    | Sweden               | Svenska          |
| `da`    | Denmark                | Dansk            |
| `no`    | Norway             | Norsk            |
| `fi`    | Finland               | Suomi            |
| `el`    | Greek                 | Ελληνικά         |
| `cs`    | Czech                 | Čeština          |
| `hu`    | Hungary             | Magyar           |
| `ro`    | Romania              | Română           |
| `uk`    | Ukraine             | Ураїнаса       |
| `pt-BR` | Portugis Brazil  | Português Brasileiro |
| `zh-HK` | Kantonis             | 粵語             |
| `ms`    | Melayu                 | Bahasa Melayu    |
| `sk`    | Slovak                | Slovenčina       |
| `bg`    | Bulgaria             | Ъ ъ гси        |
| `hr`    | Croatia              | Hrvatski         |
| `lt`    | Lithuania            | Lietuvių         |
| `lv`    | Latvian               | Latviešu         |
| `et`    | Estonia              | Eesti            |
| `sl`    | Slovenia             | Slovenščina      |

{% hint style="success" %}**Kegigihan Automatik**: Keutamaan bahasa anda disimpan`~/.chloros/cli_language.json`dan berterusan di semua sesi.
{% endhint %}***### `set-project-folder` - Set Default Project Folder

Tukar lokasi folder projek lalai (dikongsi dengan GUI).**Sintaks:**```bash
chloros-cli set-project-folder <folder-path>
```**Contoh:**```powershell
chloros-cli set-project-folder "C:\Projects\2025"
```***### `get-project-folder` - Show Project Folder

Paparkan lokasi folder projek lalai semasa.**Sintaks:**```bash
chloros-cli get-project-folder
```**Contoh:**```powershell
chloros-cli get-project-folder
```**Output:**```
ℹ Current project folder: C:\Projects\2025
```***### `reset-project-folder` - Reset to Default

Tetapkan semula folder projek ke lokasi lalai.**Sintaks:**```bash
chloros-cli reset-project-folder
```***## Pilihan Global

Pilihan ini dikenakan untuk semua arahan:

| Pilihan          | Jenis    | Lalai       | Penerangan                                      |
| --------------- | ------- | ------------- | ---------------------------------------------------- |
| `--backend-exe` | Jalan    | Auto-dikesan | Jalan ke backend boleh dilaksanakan                       |
| `--port`        | Integer | 5000          | Nombor port API backend                          |
| `--restart`     | Bendera    | -             | Memaksa memulakan semula backend (membunuh proses yang ada) |
| `--version`     | Bendera    | -             | Tunjukkan maklumat versi dan keluar                |
| `--help`        | Bendera    | -             | Tunjukkan maklumat bantuan dan keluar                   |**Contoh dengan pilihan global:**```powershell 
chloros-cli --port 5001 process "C:\Datasets\Survey_001"
```***## Panduan Tetapan Pemprosesan

### Pemprosesan selari

Chloros+ cli**skala secara automatik**pemprosesan selari untuk memadankan keupayaan komputer anda:**Bagaimana ia berfungsi:**

* Mengesan teras dan RAM CPU anda
* Peruntukan Pekerja: **2 × CPU teras** (menggunakan hyperthreading)
 * **Maksimum: 16 pekerja selari**(untuk kestabilan)**Tier sistem:**| Jenis Sistem   | Cpu        | Ram      | Pekerja  | Prestasi     |
| ------------- | ---------- | -------- | -------- | --------------- |
|**High-end**| 16+ teras  | 32+ GB   | Sehingga 16 | Kelajuan maksimum   |
|**jarak pertengahan**| 8-15 teras | 16-31 GB | 8-16     | Kelajuan yang sangat baik |
|**Low-end**| 4-7 teras  | 8-15 GB  | 4-8      | Kelajuan yang baik      |

{% hint style="success" %}**Pengoptimuman automatik**: CLI secara automatik mengesan spesifikasi sistem anda dan mengkonfigurasi pemprosesan selari yang optimum. Tiada konfigurasi manual diperlukan!
{% endhint %}

### Kaedah debayer

CLI menggunakan**berkualiti tinggi (lebih cepat)**sebagai algoritma debayer lalai dan disyorkan:

| Kaedah                      | Kualiti | Kelajuan | Penerangan                                 |
| --------------------------- | ------- | --- | ----------------------------------------------- |
|**berkualiti tinggi (lebih cepat)**⭐ | ⭐⭐⭐⭐    | ⚡⚡⚡   | Algoritma kelebihan kelebihan (lalai, disyorkan) |

### Pembetulan Vignette**Apa yang berlaku:** Corrects light falloff at image edges (darker corners common in camera imagery).

* **Diaktifkan secara lalai** - Kebanyakan pengguna harus menyimpan ini diaktifkan
* Gunakan`--no-vignette`untuk melumpuhkan

{% hint style="success" %}
**Cadangan**: Sentiasa aktifkan pembetulan vignette untuk memastikan kecerahan seragam merentasi bingkai.
{% endhint %}

### Penentukuran refleksi

Menukar nilai sensor mentah ke peratusan refleksi standard menggunakan panel penentukuran.

* **Diaktifkan secara lalai** - penting untuk analisis tumbuh -tumbuhan
* Memerlukan panel sasaran penentukuran dalam gambar
* Gunakan`--no-reflectance`untuk melumpuhkan

{% hint style="info" %}
**Keperluan**: Pastikan panel penentukuran terdedah dengan betul dan dapat dilihat dalam imej anda untuk penukaran refleksi yang tepat.
{% endhint %}

### Pembetulan PPK**Apa yang berlaku:** Applies Post-Processed Kinematic corrections using DAQ-A-SD log data for improved GPS accuracy.

* **dilumpuhkan secara lalai**
* Gunakan`--ppk`untuk membolehkan
* Memerlukan fail .daq dalam folder projek dari sensor cahaya MAPIR DAQ-A-SD.

### Format output

<pable> <tr> <th width = "197"> format </th> <th width = "130.20001220703125 "> kedalaman bit </th> <th width =" 116.599975859375 " (16-bit) </strong> ⭐ </td> <td> Integer 16-bit </td> <td> large </td> <td> analisis GIS, photogrammetry (disyorkan) Besar </td> <td> Analisis saintifik, penyelidikan </td> </tr> <tr> <td> <strong> png (8-bit) </strong> </td> <td> 8-bit integer </td> </td> (8-bit) </strong> </td> <td> 8-bit integer </td> <td> kecil </td> <td> pratonton cepat, output termampat </td> </tr> </tbody>

***

## Automasi & Skrip

### Pemprosesan Batch PowerShell

Memproses pelbagai folder dataset secara automatik:

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

### Skrip Batch Windows

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

***## Memproses aliran kerja

### Aliran kerja standard

1.**Input**: folder yang mengandungi pasangan gambar mentah/jpg
2.**Discovery**: CLI Auto-Scan untuk fail imej yang disokong
3.**Pemprosesan**: Skala mod selari ke teras CPU anda (chloros+)
4.**output**: Membuat subfolder model kamera dengan imej yang diproses

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

### Anggaran masa pemprosesan

Masa pemprosesan biasa untuk 100 imej (12MP setiap satu):

| Mod              | Masa      | Perkakasan                                     |
| ----------------- | --------- | ------------------------------------------------ |
|**mod selari**| 5-10 min  | I7/Ryzen 7, 16GB RAM, SSD (sehingga 16 pekerja) |
|**mod selari**| 10-15 min | I5/Ryzen 5, 8GB RAM, HDD (sehingga 8 pekerja)   |

{% hint style="info" %}**Petua Prestasi**: Masa pemprosesan berbeza -beza berdasarkan kiraan imej, resolusi, dan spesifikasi komputer.
{% endhint %}***## Penyelesaian masalah

### CLI tidak dijumpai**Ralat:**```
'chloros-cli' is not recognized as an internal or external command
```**Penyelesaian:**

1. Sahkan Lokasi Pemasangan:

```powershell
dir "C:\Program Files\Chloros\resources\cli\chloros-cli.exe"
```

2. Gunakan jalan penuh jika tidak di jalan:

```powershell
"C:\Program Files\Chloros\resources\cli\chloros-cli.exe" process "C:\Datasets\Field_A"
```

3. Tambah ke jalan secara manual:
* Buka sifat sistem → pembolehubah persekitaran
* Edit pemboleh ubah jalan
* Tambah:`C:\Program Files\Chloros\resources\cli`
* Mulakan semula terminal

***### Backend gagal bermula**Ralat:**```
Backend failed to start within 30 seconds
```**Penyelesaian:**1. Periksa sama ada backend sudah berjalan (tutup terlebih dahulu)
2. Periksa Windows Firewall tidak menyekat
3. Cuba port yang berbeza:

```powershell
chloros-cli --port 5001 process "C:\Datasets\Field_A"
```

4. Memaksa memulakan semula backend:

```powershell
chloros-cli --restart process "C:\Datasets\Field_A"
```***### Isu lesen / pengesahan**Ralat:**```
Chloros+ license required for CLI access
```**Penyelesaian:**1. Sahkan anda mempunyai langganan kloros+ aktif
2. Log masuk dengan kelayakan anda:

```powershell
chloros-cli login user@example.com 'password'
```

3. Semak Status Lesen:

```powershell
chloros-cli status
```

4. Sokongan Hubungi: info@mapir.camera***### Tiada imej yang dijumpai**Ralat:**```
No images found in the specified folder
```**Penyelesaian:**1. Sahkan folder mengandungi format yang disokong (.raw, .tif, .jpg)
2. Semak laluan folder betul (gunakan petikan untuk laluan dengan ruang)
3. Pastikan anda membaca kebenaran untuk folder
4. Semak sambungan fail betul***### Memproses gerai atau hang**Penyelesaian:**1. Semak ruang cakera yang ada (pastikan cukup untuk output)
2. Tutup aplikasi lain untuk memori percuma
3. Kurangkan kiraan gambar (proses dalam kelompok)***### Pelabuhan sudah digunakan**Ralat:**```
Port 5000 is already in use
```**Penyelesaian:**Tentukan port yang berbeza:

```powershell
chloros-cli --port 5001 process "C:\Datasets\Field_A"
```***## Soalan Lazim

### S: Adakah saya memerlukan lesen untuk CLI?**A:**Yes! The CLI requires a paid**Chloros+ license**.

* ❌ Pelan standard (percuma): CLI dilumpuhkan
* ✅ chloros+ (dibayar) rancangan: CLI diaktifkan sepenuhnya

Langgan di: [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing)

***### S: Bolehkah saya menggunakan CLI pada pelayan tanpa GUI?**A:** Yes! The CLI runs completely headless. Requirements:

* Windows Server 2016 atau lebih baru
* Visual C ++ Redistributable Dipasang
* RAM yang mencukupi (minimum 8GB, 16GB disyorkan)
* Pengaktifan lesen GUI satu kali di mana-mana mesin

***### S: Di manakah imej diproses disimpan?**A:**By default, processed images are saved in the**same folder as input**in camera-model subfolders (e.g., `Survey3N_RGN/`).

Gunakan`-o`Pilihan untuk menentukan folder output yang berbeza:

```powershell
chloros-cli process "C:\Input" -o "D:\Output"
```***### S: Bolehkah saya memproses pelbagai folder sekaligus?**A:**Not directly in one command, but you can use scripting to process folders sequentially. See [Automation & Scripting](CLI.md#automation--scripting) section.***### S: Bagaimana saya menyimpan output CLI ke fail log?**PowerShell:**```powershell
chloros-cli process "C:\Datasets\Field_A" | Tee-Object -FilePath "processing.log"
```**Batch:**```batch
chloros-cli process "C:\Datasets\Field_A" > processing.log 2>&1
```***### S: Apa yang berlaku jika saya menekan Ctrl+C semasa pemprosesan?**A:**The CLI will:

1. Berhenti memproses dengan anggun
2. Tutup backend
3. Keluar dengan kod 130

Imej yang diproses sebahagiannya mungkin kekal dalam folder output.***### S: Bolehkah saya mengautomasikan pemprosesan CLI?**A:**Absolutely! The CLI is designed for automation. See [Automation & Scripting](CLI.md#automation--scripting) for PowerShell, Batch, and Python examples.***### S: Bagaimana saya menyemak versi CLI?**A:**```powershell
chloros-cli --version
```**Output:**```
Chloros CLI 1.0.2
```***

## Mendapat pertolongan

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

### Saluran sokongan

* **E -mel**: info@mapir.camera***laman web**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)***harga**: [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing)***## Contoh lengkap

### Contoh 1: Pemprosesan Asas

Proses dengan tetapan lalai (vignette, refleksi):

```powershell
chloros-cli process "C:\Datasets\Field_A_2025_01_15"
```***### Contoh 2: Output saintifik berkualiti tinggi

Tiff terapung 32-bit:

```powershell
chloros-cli process "C:\Datasets\Field_A" ^
  --format "TIFF (32-bit, Percent)" ^
  --vignette ^
  --reflectance
```***### Contoh 3: Pemprosesan Pratonton Cepat

8-bit PNG tanpa penentukuran untuk semakan cepat:

```powershell
chloros-cli process "C:\Datasets\Field_A" ^
  --format "PNG (8-bit)" ^
  --no-vignette ^
  --no-reflectance
```***### Contoh 4: Pemprosesan yang diperbetulkan PPK

Sapukan pembetulan PPK dengan pemantulan:

```powershell
chloros-cli process "C:\Datasets\Field_A" ^
  --ppk ^
  --reflectance
```***### Contoh 5: Lokasi output tersuai

Proses ke pemacu yang berbeza dengan format tertentu:

```powershell
chloros-cli process "C:\Input\Raw_Images" ^
  -o "D:\Output\Processed" ^
  --format "TIFF (16-bit)"
```***### Contoh 6: Aliran Kerja Pengesahan

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
```***

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
