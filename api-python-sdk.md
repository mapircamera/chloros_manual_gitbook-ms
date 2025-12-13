# API: Python SDK

** Chloros Python SDK ** menyediakan akses programatik ke enjin pemprosesan imej kloros, membolehkan automasi, aliran kerja tersuai, dan integrasi lancar dengan aplikasi Python dan saluran penyelidikan anda.

### Ciri -ciri utama

*🐍 ** Python asli ** - API Pythonic bersih untuk pemprosesan imej
*🔧 ** Akses API Penuh ** - Kawalan Lengkap ke atas Pemprosesan Chloros
*🚀 ** Automasi ** - Bina aliran kerja pemprosesan batch tersuai
*🔗 ** Integrasi ** - Kenaikan kloros dalam aplikasi python sedia ada
*📊 ** Penyelidikan -siap ** - Sempurna untuk saluran paip analisis saintifik
*⚡ ** Pemprosesan Selari ** - Skala ke teras CPU anda (chloros+)

Keperluan ###

| Keperluan | Butiran |
| -------------------- | ------------------------------------------------------------------- |
| ** Desktop Chloros ** | Mesti dipasang secara tempatan |
| ** Lesen ** | Chloros+ ([pelan berbayar diperlukan] (https://cloud.mapir.camera/pricing)) |
| ** Sistem Operasi ** | Windows 10/11 (64-bit) |
| ** Python ** | Python 3.7 atau lebih tinggi |
| ** Memory ** | 8GB RAM Minimum (16GB disyorkan) |
| ** Internet ** | Diperlukan untuk Pengaktifan Lesen |

{% hint style="warning" %}
** Keperluan Lesen **: Python SDK memerlukan langganan Chloros+ yang dibayar untuk akses API. Pelan standard (percuma) tidak mempunyai akses API/SDK. Lawati [https://cloud.mapir.camera/pricingś(https://cloud.mapir.camera/pricing) untuk menaik taraf.
{ % endhint %}

## Permulaan cepat

### Pemasangan

Pasang melalui PIP:

```bash
pip install chloros-sdk
```

{% hint style="info" %}
** Persediaan pertama kali **: Sebelum menggunakan SDK, aktifkan lesen Chloros+ anda dengan membuka kloros, chloros (pelayar) atau chloros CLI dan log masuk dengan kelayakan anda. Ini hanya perlu dilakukan sekali.
{ % endhint %}

### Penggunaan asas

Proses folder dengan hanya beberapa baris:

```python
from chloros_sdk import process_folder

# One-line processing
results = process_folder("C:\\DroneImages\\Flight001")
```

### Kawalan penuh

Untuk aliran kerja lanjutan:

```python
from chloros_sdk import ChlorosLocal

# Initialize SDK
chloros = ChlorosLocal()

# Create project
chloros.create_project("MyProject", camera="Survey3N_RGN")

# Import images
chloros.import_images("C:\\DroneImages\\Flight001")

# Configure settings
chloros.configure(
    vignette_correction=True,
    reflectance_calibration=True,
    indices=["NDVI", "NDRE", "GNDVI"]
)

# Process images
chloros.process(mode="parallel", wait=True)
```

***

## Panduan pemasangan

### Prasyarat

Sebelum memasang SDK, pastikan anda mempunyai:

1. ** Desktop Chloros ** Dipasang ([muat turun] (muat turun.md))
2. ** Python 3.7+** Dipasang ([python.org] (https://www.python.org))
3. ** Lesen AKTIF CHLOROS+ ** ([Upgrade] (https://cloud.mapir.camera/pricing))

### Pasang melalui PIP

** Pemasangan Standard: **

```bash
pip install chloros-sdk
```

** Dengan sokongan pemantauan kemajuan: **

```bash
pip install chloros-sdk[progress]
```

** Pemasangan Pembangunan: **

```bash
pip install chloros-sdk[dev]
```

### Sahkan pemasangan

Uji SDK dipasang dengan betul:

```python
import chloros_sdk
print(f"Chloros SDK version: {chloros_sdk.__version__}")
```

***

## Persediaan kali pertama

### Pengaktifan lesen

SDK menggunakan lesen yang sama seperti chloros, chloros (penyemak imbas), dan chloros cli. Aktifkan sekali melalui GUI atau CLI:

1. Buka ** Chloros atau Chloros (penyemak imbas) ** dan log masuk pada pengguna <img src = ". Gitbook/aset/icon_user.jpg" alt = "" data-size = "line"> tab. Atau, buka ** cli **.
2. Masukkan kelayakan kloros+ anda dan log masuk
3. Lesen di -cache secara tempatan (berterusan merentasi reboot)

{% hint style="success" %}
** Persediaan satu kali **: Selepas log masuk melalui GUI atau CLI, SDK secara automatik menggunakan lesen cache. Tiada pengesahan tambahan diperlukan!
{ % endhint %}

### Sambungan ujian

Sahkan SDK boleh menyambung ke kloros:

```python
from chloros_sdk import ChlorosLocal

# Initialize SDK (auto-starts backend if needed)
chloros = ChlorosLocal()

# Check status
status = chloros.get_status()
print(f"Backend running: {status['running']}")
```

***

## Rujukan API

### kelas chloroslocal

Kelas utama untuk pemprosesan imej kloros tempatan.

#### Pembina

```python
ChlorosLocal(
    api_url="http://localhost:5000",     # Backend URL
    auto_start_backend=True,             # Auto-start backend if not running
    backend_exe=None,                    # Backend path (auto-detected)
    timeout=30,                          # Request timeout (seconds)
    backend_startup_timeout=60           # Backend startup timeout
)
```

** Parameter: **

| Parameter | Jenis | Lalai | Penerangan |
| ------------------------- | ---- | ------------------------- | ------------------------------------- |
| `API_URL` | str | `" http: // localhost: 5000 "` | URL Backend Chloros Tempatan |
| `auto_start_backend` | Bool | `Benar` | Secara automatik mula backend jika diperlukan |
| `backend_exe` | str | `None` (auto-detect) | Laluan ke Backend Executable |
| `timeout` | int | `30` | Permintaan masa dalam beberapa saat |
| `backend_startup_timeout` | int | `60` | Tamat masa untuk permulaan backend (saat) |

** Contoh: **

```python
# Default (auto-start backend)
chloros = ChlorosLocal()

# Connect to running backend
chloros = ChlorosLocal(auto_start_backend=False)

# Custom backend path
chloros = ChlorosLocal(backend_exe="C:/Custom/chloros-backend.exe")

# Custom timeout
chloros = ChlorosLocal(timeout=60)
```

***

### Kaedah

#### `create_project (project_name, camera = none)`

Buat projek kloros baru.

** Parameter: **

| Parameter | Jenis | Diperlukan | Penerangan |
| -------------- | ---- | -------- | -------------------------------------------------------- |
| `project_name` | str | Ya | Nama untuk projek |
| `kamera` | str | Tidak | Templat Kamera (mis., "Survey3n \ _rgn", "Survey3w \ _Ocn") |

** Pulangan: ** `dict` - Respons Penciptaan Projek

** Contoh: **

```python
# Basic project
chloros.create_project("DroneField_A")

# With camera template
chloros.create_project("DroneField_A", camera="Survey3N_RGN")
```

***

#### `import_images (folder_path, recursif = false)`

Import imej dari folder.

** Parameter: **

| Parameter | Jenis | Diperlukan | Penerangan |
| ------------- | -------- | -------- | ---------------------------------- |
| `folder_path` | str/path | Ya | Laluan ke folder dengan imej |
| `rekursif` | Bool | Tidak | Subfolder Cari (lalai: palsu) |

** Pulangan: ** `Dict` - Import Hasil dengan kiraan fail

** Contoh: **

```python
# Import from folder
chloros.import_images("C:\\DroneImages\\Flight001")

# Import recursively
chloros.import_images("C:\\DroneImages", recursive=True)
```

***

#### `configure (** tetapan)`

Konfigurasikan tetapan pemprosesan.

** Parameter: **

| Parameter | Jenis | Lalai | Penerangan |
| ------------------------- | ---- | ----------------------- | ------------------------------- |
| `debayer` | str | "Berkualiti tinggi (lebih cepat)" | Kaedah Debayer |
| `vignette_correction` | Bool | `Benar` | Dayakan Pembetulan Vignette |
| `Reflectance_calibration` | Bool | `Benar` | Dayakan penentukuran refleksi |
| `indeks` | Senarai | `None` | Indeks Vegetasi untuk Mengira |
| `Export_Format` | str | "Tiff (16-bit)" | Format output |
| `ppk` | Bool | `False` | Dayakan Pembetulan PPK |
| `custom_settings` | dict | `None` | Tetapan Advanced Custom |

** Format Eksport: **

* `" TIFF (16 -bit) "` - Disyorkan untuk GIS/Photogrammetry
* `" TIFF (32 -bit, peratus) "` - Analisis Saintifik
* `" PNG (8 -bit) "` - Pemeriksaan Visual
* `" Jpg (8 -bit) "` - output termampat

** indeks yang ada: **

NDVI, NDRE, GNDVI, OSAVI, CIG, EVI, SAVI, MSAVI, MTVI2, dan banyak lagi.

** Contoh: **

```python
# Basic configuration
chloros.configure(
    vignette_correction=True,
    reflectance_calibration=True,
    indices=["NDVI", "NDRE"]
)

# Advanced configuration
chloros.configure(
    debayer="High Quality (Faster)",
    vignette_correction=True,
    reflectance_calibration=True,
    ppk=True,
    export_format="TIFF (32-bit, Percent)",
    indices=["NDVI", "NDRE", "GNDVI", "OSAVI", "CIG"]
)
```

***

#### `Proses (mod =" selari ", tunggu = benar, progress_callback = none)`

Proses imej projek.

** Parameter: **

| Parameter | Jenis | Lalai | Penerangan |
| ------------------- | -------- | ------------ | ----------------------------------------- |
| `mod` | str | `" selari "` | Mod Pemprosesan: "Selari" atau "Serial" |
| `tunggu` | Bool | `Benar` | Tunggu siap |
| `progress_callback` | Callable | `None` | Fungsi Panggilan Kembali Kemajuan (Kemajuan, MSG) |
| `Poll_interval` | Float | `2.0` | Pengundian Selang untuk Kemajuan (Seconds) |

** Pulangan: ** `Dict` - Hasil Pemprosesan

{% hint style="warning" %}
** Mod Paralel **: Memerlukan Lesen Chloros+. Skala secara automatik ke teras CPU anda (sehingga 16 pekerja).
{ % endhint %}

** Contoh: **

```python
# Simple processing
results = chloros.process()

# With progress monitoring
def show_progress(progress, message):
    print(f"[{progress}%] {message}")

chloros.process(
    mode="parallel",
    progress_callback=show_progress,
    wait=True
)

# Fire-and-forget (non-blocking)
chloros.process(wait=False)
```

***

#### `get_config ()`

Dapatkan konfigurasi projek semasa.

** Pulangan: ** `Dict` - Konfigurasi Projek Semasa

** Contoh: **

```python
config = chloros.get_config()
print(config['Project Settings'])
```

***

#### `get_status ()`

Dapatkan maklumat status backend.

** Pulangan: ** `dict` - Status backend

** Contoh: **

```python
status = chloros.get_status()
print(f"Running: {status['running']}")
print(f"URL: {status['url']}")
```

***

#### `shutdown_backend ()`

Shutdown backend (jika dimulakan oleh SDK).

** Contoh: **

```python
chloros.shutdown_backend()
```

***

### Fungsi kemudahan

#### `process_folder (folder_path, ** options)`

Fungsi kemudahan satu baris untuk memproses folder.

** Parameter: **

| Parameter | Jenis | Lalai | Penerangan |
| ------------------------- | -------- | --------------- | ------------------------------ |
| `folder_path` | str/path | Diperlukan | Laluan ke folder dengan imej |
| `project_name` | str | Auto-Generated | Nama Projek |
| `kamera` | str | `None` | Templat Kamera |
| `indeks` | Senarai | `[" Ndvi "]` | Indeks untuk mengira |
| `vignette_correction` | Bool | `Benar` | Dayakan Pembetulan Vignette |
| `Reflectance_calibration` | Bool | `Benar` | Dayakan penentukuran refleksi |
| `Export_Format` | str | "Tiff (16-bit)" | Format output |
| `mod` | str | `" selari "` | Mod Pemprosesan |
| `progress_callback` | Callable | `None` | Panggilan balik kemajuan |

** Pulangan: ** `Dict` - Hasil Pemprosesan

** Contoh: **

```python
from chloros_sdk import process_folder

# Simple one-liner
results = process_folder("C:\\DroneImages\\Flight001")

# With custom settings
results = process_folder(
    "C:\\DroneImages\\Flight001",
    project_name="Field_A_Survey",
    camera="Survey3N_RGN",
    indices=["NDVI", "NDRE", "GNDVI"],
    mode="parallel"
)

# With progress monitoring
def show_progress(progress, message):
    print(f"[{progress}%] {message}")

results = process_folder(
    "C:\\DroneImages\\Flight001",
    progress_callback=show_progress
)
```

***

## Sokongan Pengurus Konteks

SDK menyokong pengurus konteks untuk pembersihan automatik:

```python
from chloros_sdk import ChlorosLocal

# Auto-cleanup when done
with ChlorosLocal() as chloros:
    chloros.create_project("MyProject")
    chloros.import_images("C:\\Images")
    chloros.configure(indices=["NDVI"])
    chloros.process()
# Backend automatically shut down here
```

***

## Contoh lengkap

### Contoh 1: Pemprosesan Asas

Proses folder dengan tetapan lalai:

```python
from chloros_sdk import process_folder

# Process with default settings
results = process_folder("C:\\Datasets\\Field_A_2025_01_15")

print(f"Processing complete: {results}")
```

***

### Contoh 2: aliran kerja tersuai

Kawalan penuh ke atas saluran paip pemprosesan:

```python
from chloros_sdk import ChlorosLocal

# Initialize SDK
chloros = ChlorosLocal()

# Create project with camera template
chloros.create_project("Research_Plot_A", camera="Survey3N_RGN")

# Import images
import_results = chloros.import_images("C:\\Research\\PlotA")
print(f"Imported {len(import_results.get('files', []))} images")

# Configure advanced settings
chloros.configure(
    debayer="High Quality (Faster)",
    vignette_correction=True,
    reflectance_calibration=True,
    ppk=False,
    export_format="TIFF (16-bit)",
    indices=["NDVI", "NDRE", "GNDVI", "OSAVI"]
)

# Process with progress monitoring
def show_progress(progress, message):
    print(f"Progress: {progress}% - {message}")

chloros.process(
    mode="parallel",
    progress_callback=show_progress,
    wait=True
)

print("Processing complete!")
```

***

### Contoh 3: pemprosesan batch pelbagai folder

Memproses pelbagai dataset penerbangan:

```python
from chloros_sdk import ChlorosLocal
from pathlib import Path

# Initialize SDK once
chloros = ChlorosLocal()

# List of flight folders
flights = [
    "C:\\Datasets\\Flight_001",
    "C:\\Datasets\\Flight_002",
    "C:\\Datasets\\Flight_003"
]

for flight_path in flights:
    flight_name = Path(flight_path).name
    print(f"\n{'='*60}")
    print(f"Processing: {flight_name}")
    print('='*60)
    
    try:
        # Create project
        chloros.create_project(flight_name, camera="Survey3N_RGN")
        
        # Import images
        chloros.import_images(flight_path)
        
        # Configure
        chloros.configure(
            vignette_correction=True,
            reflectance_calibration=True,
            indices=["NDVI", "NDRE", "GNDVI"]
        )
        
        # Process
        chloros.process(mode="parallel", wait=True)
        
        print(f"✓ {flight_name} completed successfully")
    
    except Exception as e:
        print(f"✗ {flight_name} failed: {e}")

print("\n" + "="*60)
print("All flights processed!")
```

***

### Contoh 4: Integrasi Paip Penyelidikan

Mengintegrasikan kloros dengan analisis data:

```python
from chloros_sdk import ChlorosLocal
import pandas as pd
import matplotlib.pyplot as plt

# Initialize Chloros
chloros = ChlorosLocal()

# Field survey data
surveys = [
    {"name": "Plot_A", "folder": "C:\\Research\\PlotA", "biomass": 4500},
    {"name": "Plot_B", "folder": "C:\\Research\\PlotB", "biomass": 3800},
    {"name": "Plot_C", "folder": "C:\\Research\\PlotC", "biomass": 5200}
]

results = []

for survey in surveys:
    # Process with Chloros
    chloros.create_project(survey['name'])
    chloros.import_images(survey['folder'])
    chloros.configure(indices=["NDVI", "NDRE"])
    chloros.process(mode="parallel", wait=True)
    
    # Get results
    config = chloros.get_config()
    
    # Extract NDVI values (example - adjust based on your needs)
    # In real implementation, you would read the processed TIFF files
    
    results.append({
        'plot': survey['name'],
        'biomass': survey['biomass'],
        # Add your NDVI extraction here
    })

# Statistical analysis
df = pd.DataFrame(results)
print("\nResults:")
print(df)

# Create correlation plot
# plt.scatter(df['ndvi'], df['biomass'])
# plt.xlabel('NDVI')
# plt.ylabel('Biomass (kg/ha)')
# plt.title('NDVI vs Biomass Correlation')
# plt.show()
```

***

### Contoh 5: Pemantauan Kemajuan Custom

Penjejakan kemajuan lanjutan dengan pembalakan:

```python
from chloros_sdk import ChlorosLocal
from datetime import datetime
import logging

# Setup logging
logging.basicConfig(
    filename=f'processing_{datetime.now():%Y%m%d_%H%M%S}.log',
    level=logging.INFO,
    format='%(asctime)s - %(message)s'
)

# Progress callback with logging
def log_progress(progress, message):
    log_msg = f"[{progress}%] {message}"
    logging.info(log_msg)
    print(log_msg)

# Process with logging
chloros = ChlorosLocal()
chloros.create_project("LoggedProcess")
chloros.import_images("C:\\DroneImages")
chloros.configure(indices=["NDVI", "NDRE"])

logging.info("Starting processing...")
chloros.process(
    mode="parallel",
    progress_callback=log_progress,
    wait=True
)
logging.info("Processing complete!")
```

***

### Contoh 6: Pengendalian ralat

Pengendalian ralat yang teguh untuk kegunaan pengeluaran:

```python
from chloros_sdk import ChlorosLocal
from chloros_sdk.exceptions import (
    ChlorosError,
    ChlorosBackendError,
    ChlorosLicenseError,
    ChlorosProcessingError
)

def process_safely(folder_path):
    """Process with comprehensive error handling"""
    try:
        with ChlorosLocal() as chloros:
            chloros.create_project("SafeProcess")
            chloros.import_images(folder_path)
            chloros.configure(indices=["NDVI"])
            chloros.process()
            
        return True, "Success"
    
    except ChlorosLicenseError as e:
        return False, f"License error: {e}. Upgrade to Chloros+ at cloud.mapir.camera/pricing"
    
    except ChlorosBackendError as e:
        return False, f"Backend error: {e}. Ensure Chloros Desktop is installed."
    
    except ChlorosProcessingError as e:
        return False, f"Processing error: {e}"
    
    except FileNotFoundError as e:
        return False, f"Folder not found: {e}"
    
    except ChlorosError as e:
        return False, f"Chloros error: {e}"
    
    except Exception as e:
        return False, f"Unexpected error: {e}"

# Use the safe function
success, message = process_safely("C:\\DroneImages\\Flight001")
if success:
    print(f"✓ {message}")
else:
    print(f"✗ {message}")
```

***

### Contoh 7: Alat baris arahan

Bina alat CLI tersuai dengan SDK:

```python
#!/usr/bin/env python
"""
Custom Chloros CLI Tool
Process multiple folders from command line
"""

import sys
import argparse
from pathlib import Path
from chloros_sdk import process_folder

def main():
    parser = argparse.ArgumentParser(description='Custom Chloros Processor')
    parser.add_argument('folders', nargs='+', help='Folders to process')
    parser.add_argument('--indices', nargs='+', default=['NDVI'],
                       help='Indices to calculate (default: NDVI)')
    parser.add_argument('--camera', default=None,
                       help='Camera template')
    parser.add_argument('--format', default='TIFF (16-bit)',
                       help='Export format')
    
    args = parser.parse_args()
    
    successful = []
    failed = []
    
    for folder in args.folders:
        folder_path = Path(folder)
        
        if not folder_path.exists():
            print(f"✗ Skipping {folder}: not found")
            failed.append(folder)
            continue
        
        print(f"\nProcessing: {folder_path.name}...")
        
        try:
            process_folder(
                folder_path,
                camera=args.camera,
                indices=args.indices,
                export_format=args.format
            )
            print(f"✓ {folder_path.name} complete")
            successful.append(folder)
        
        except Exception as e:
            print(f"✗ {folder_path.name} failed: {e}")
            failed.append(folder)
    
    # Summary
    print(f"\n{'='*60}")
    print(f"Summary: {len(successful)} successful, {len(failed)} failed")
    
    return 0 if not failed else 1

if __name__ == '__main__':
    sys.exit(main())
```

** Penggunaan: **

```bash
python my_processor.py "C:\Flight001" "C:\Flight002" --indices NDVI NDRE GNDVI
```

***

## Pengendalian pengecualian

SDK menyediakan kelas pengecualian khusus untuk jenis ralat yang berbeza:

### Pengecualian hierarki

```python
ChlorosError                    # Base exception
├── ChlorosBackendError        # Backend startup/connection issues
├── ChlorosLicenseError        # License validation issues
├── ChlorosConnectionError     # Network/connection failures
├── ChlorosProcessingError     # Image processing failures
├── ChlorosAuthenticationError # Authentication failures
└── ChlorosConfigurationError  # Configuration errors
```

### Contoh pengecualian

```python
from chloros_sdk import ChlorosLocal
from chloros_sdk.exceptions import *

try:
    chloros = ChlorosLocal()
    chloros.process()

except ChlorosLicenseError:
    print("Chloros+ license required. Upgrade at cloud.mapir.camera/pricing")

except ChlorosBackendError:
    print("Backend failed to start. Ensure Chloros Desktop is installed.")

except ChlorosProcessingError as e:
    print(f"Processing failed: {e}")

except ChlorosError as e:
    print(f"General Chloros error: {e}")
```

***

## Topik lanjutan

### Konfigurasi backend tersuai

Gunakan lokasi backend tersuai atau konfigurasi:

```python
chloros = ChlorosLocal(
    backend_exe="C:\\Custom\\chloros-backend.exe",
    api_url="http://localhost:5001",  # Custom port
    timeout=60,                        # Longer timeout
    backend_startup_timeout=120        # 2 minutes startup
)
```

### pemprosesan yang tidak menyekat

Mula memproses dan teruskan dengan tugas lain:

```python
# Start processing (non-blocking)
chloros.process(wait=False)

# Do other work here...
print("Processing started in background...")

# Check status later
import time
while True:
    status = chloros.get_config()
    if status.get('processing_complete'):
        break
    time.sleep(5)

print("Processing complete!")
```

### Pengurusan memori

Untuk dataset yang besar, proses dalam kelompok:

```python
from pathlib import Path

base_folder = Path("C:\\LargeDataset")
batch_size = 100

# Get all image files
images = list(base_folder.glob("*.RAW"))

# Process in batches
for i in range(0, len(images), batch_size):
    batch = images[i:i+batch_size]
    batch_folder = base_folder / f"batch_{i//batch_size}"
    
    # Create batch folder and move images
    # ... (implementation details)
    
    # Process batch
    process_folder(batch_folder)
```

***

## Penyelesaian masalah

### backend tidak bermula

** Isu: ** SDK gagal memulakan backend

** Penyelesaian: **

1. Sahkan desktop kloros dipasang:

```python
import os
backend_path = r"C:\Program Files\MAPIR\Chloros\resources\backend\chloros-backend.exe"
print(f"Backend exists: {os.path.exists(backend_path)}")
```

2. Periksa Windows Firewall tidak menyekat
3. Cuba jalan backend manual:

```python
chloros = ChlorosLocal(backend_exe="C:\\Path\\To\\chloros-backend.exe")
```

***

### Lesen tidak dikesan

** Isu: ** SDK memberi amaran mengenai lesen yang hilang

** Penyelesaian: **

1. Buka kloros, kloros (penyemak imbas) atau chloros cli dan login.
2. Sahkan lesen adalah cache:

```python
from pathlib import Path
import os

# Check cache location (Windows)
cache_path = Path(os.getenv('APPDATA')) / 'Chloros' / 'cache'
print(f"Cache exists: {cache_path.exists()}")
```

3. Sokongan Hubungi: info@mapir.camera

***

### Kesalahan import

** Isu: ** `ModulenotFoundError: Tiada modul bernama 'chloros_sdk'`

** Penyelesaian: **

```bash
# Verify installation
pip show chloros-sdk

# Reinstall if needed
pip uninstall chloros-sdk
pip install chloros-sdk

# Check Python environment
python -c "import sys; print(sys.path)"
```

***

### tamat masa pemprosesan

** Isu: ** Pemprosesan masa keluar

** Penyelesaian: **

1. Meningkatkan masa tamat:

```python
chloros = ChlorosLocal(timeout=120)  # 2 minutes
```

2. Proses kelompok yang lebih kecil
3. Periksa ruang cakera yang ada
4. Memantau sumber sistem

***

### port sudah digunakan

** Isu: ** Backend Port 5000 Diduduki

** Penyelesaian: **

```python
# Use different port
chloros = ChlorosLocal(api_url="http://localhost:5001")
```

Atau mencari dan menutup proses yang bercanggah:

```powershell
# PowerShell
Get-NetTCPConnection -LocalPort 5000
```

***

## Petua Prestasi

### mengoptimumkan kelajuan pemprosesan

1. ** Gunakan mod selari ** (memerlukan chloros+)

```python
chloros.process(mode="parallel")  # Up to 16 workers
```

2. ** Kurangkan resolusi output ** (jika boleh diterima)

```python
chloros.configure(export_format="PNG (8-bit)")  # Faster than TIFF
```

3. ** Lumpuhkan indeks yang tidak perlu **

```python
# Only calculate needed indices
chloros.configure(indices=["NDVI"])  # Not all indices
```

4. ** Proses pada SSD ** (bukan HDD)

***

### Pengoptimuman memori

Untuk dataset besar:

```python
# Process in batches instead of all at once
# See "Memory Management" in Advanced Topics
```

***

### Pemprosesan latar belakang

Percuma Python untuk tugas lain:

```python
chloros.process(wait=False)  # Non-blocking

# Continue with other work
# ...
```

***

## Contoh integrasi

### Integrasi Django

```python
# views.py
from django.http import JsonResponse
from chloros_sdk import process_folder

def process_images_view(request):
    if request.method == 'POST':
        folder_path = request.POST.get('folder_path')
        
        try:
            results = process_folder(folder_path)
            return JsonResponse({'success': True, 'results': results})
        except Exception as e:
            return JsonResponse({'success': False, 'error': str(e)})
```

### Flask API

```python
# app.py
from flask import Flask, request, jsonify
from chloros_sdk import process_folder

app = Flask(__name__)

@app.route('/api/process', methods=['POST'])
def process():
    data = request.get_json()
    folder_path = data.get('folder_path')
    
    try:
        results = process_folder(folder_path)
        return jsonify({'success': True, 'results': results})
    except Exception as e:
        return jsonify({'success': False, 'error': str(e)}), 500

if __name__ == '__main__':
    app.run()
```

### Jupyter Notebook

```python
# notebook.ipynb
from chloros_sdk import ChlorosLocal
import matplotlib.pyplot as plt

# Initialize
chloros = ChlorosLocal()

# Process
chloros.create_project("JupyterTest")
chloros.import_images("C:\\Data")
chloros.configure(indices=["NDVI"])

# Progress in notebook
from IPython.display import clear_output

def notebook_progress(progress, message):
    clear_output(wait=True)
    print(f"Progress: {progress}%")
    print(message)

chloros.process(progress_callback=notebook_progress)

# Visualize results
# ... (your visualization code)
```

***

## Soalan Lazim

### Q: Adakah SDK memerlukan sambungan internet?

** A: ** Hanya untuk pengaktifan lesen awal. Selepas log masuk melalui Via Chloros, Chloros (pelayar) atau chloros CLI, lesen itu di -cache secara tempatan dan berfungsi di luar talian selama 30 hari.

***

### Q: Bolehkah saya menggunakan SDK pada pelayan tanpa GUI?

** A: ** Ya! Keperluan:

* Windows Server 2016 atau Lagi
* Dipasang kloros (satu kali)
* Lesen diaktifkan pada mana -mana mesin (lesen cache disalin ke pelayan)

***

### Q: Apakah perbezaan antara desktop, CLI, dan SDK?

| Ciri | Desktop GUI | CLI COMMAND LINE | Python SDK |
| --------------- | ----------- | ---------------- | ----------- |
| ** Interface ** | Klik titik | Perintah | Python Api |
| ** Terbaik untuk ** | Kerja Visual | SCRIPTING | Integrasi |
| ** Automasi ** | Terhad | Baik | Cemerlang |
| ** Fleksibiliti ** | Asas | Baik | Maksimum |
| ** Lesen ** | Chloros+ | Chloros+ | Chloros+ |

***

### Q: Bolehkah saya mengedarkan aplikasi yang dibina dengan SDK?

** A: ** Kod SDK boleh diintegrasikan ke dalam aplikasi anda, tetapi:

* Pengguna akhir memerlukan kloros dipasang
* Pengguna Akhir Memerlukan Chloros+ Lesen Aktif
* Pengagihan komersial memerlukan pelesenan OEM

Hubungi info@mapir.camera untuk pertanyaan OEM.

***

### Q: Bagaimana saya mengemas kini SDK?

```bash
pip install --upgrade chloros-sdk
```

***

### Q: Di manakah imej diproses disimpan?

Secara lalai, di jalan projek:

```
Project_Path/
└── MyProject/
    └── Survey3N_RGN/          # Processed outputs
```

***

### Q: Bolehkah saya memproses imej dari skrip python yang berjalan mengikut jadual?

** A: ** Ya! Gunakan Windows Tugas Scheduler dengan skrip Python:

```python
# scheduled_processing.py
from chloros_sdk import process_folder

# Process today's flights
results = process_folder("C:\\Flights\\Today")
```

Jadual melalui penjadual tugas untuk dijalankan setiap hari.

***

### Q: Adakah SDK menyokong async/menunggu?

** A: ** Versi semasa adalah segerak. Untuk tingkah laku async, gunakan `tunggu = palsu` atau lari dalam benang berasingan:

```python
import threading

def process_thread():
    chloros.process()

thread = threading.Thread(target=process_thread)
thread.start()

# Continue with other work...
```

***

## Mendapatkan bantuan

### Dokumentasi

*** rujukan API **: Halaman ini

### saluran sokongan

*** E -mel **: info@mapir.camera
*** Laman web **: [https://www.mapir.camera/community/contact n(https://www.mapir.camera/community/contact)
*** Harga **: [https://cloud.mapir.camera/pricingś(https://cloud.mapir.camera/pricing)

### Contoh kod

Semua contoh yang disenaraikan di sini diuji dan siap pengeluaran. Salin dan menyesuaikannya untuk kes penggunaan anda.

***

## Lesen

** Perisian Proprietari ** - Hak Cipta (c) 2025 Mapir Inc.

SDK memerlukan langganan kloros+ aktif. Penggunaan, pengedaran, atau pengubahsuaian yang tidak dibenarkan adalah dilarang.
