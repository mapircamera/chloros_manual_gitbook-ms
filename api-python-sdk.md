# API: Python SDK

**Chloros Python SDK** menyediakan akses programatik ke enjin pemprosesan imej kloros, membolehkan automasi, aliran kerja tersuai, dan integrasi lancar dengan aplikasi Python dan saluran penyelidikan anda.

### Ciri -ciri utama

* 🐍 **Python asli** - API Pythonic bersih, bersih untuk pemprosesan imej
* 🔧 **Akses API Penuh** - Kawalan Lengkap ke atas Pemprosesan Kloros
* 🚀 **Automasi** - Bina aliran kerja pemprosesan batch tersuai
* 🔗 **Integrasi** - Kenaikan kloros dalam aplikasi python sedia ada
* 📊 **Penyelidikan -siap** - Sempurna untuk Paip Analisis Saintifik
* ⚡ **Pemprosesan Selari**- Skala ke teras CPU anda (chloros+)

### Keperluan

| Keperluan          | Perincian                                                             |
| -------------------- | ------------------------------------------------------------------- |
| **desktop kloros** | Mesti dipasang secara tempatan                                           |
| **Lesen** | Chloros+ ([paid plan required](https://cloud.mapir.camera/pricing)) |
| **sistem operasi** | Windows 10/11 (64-bit)                                              |
| **python** | Python 3.7 atau lebih tinggi                                                |
| **Memori** | Minimum RAM 8GB (16GB disyorkan)                                  |
| **Internet** | Diperlukan untuk pengaktifan lesen                                     |

{% hint style="warning" %}**Keperluan Lesen**: Python SDK memerlukan langganan Chloros+ yang dibayar untuk akses API. Pelan standard (percuma) tidak mempunyai akses API/SDK. Lawati [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing) untuk menaik taraf.
{% endhint %}

## Permulaan cepat

### Pemasangan

Pasang melalui PIP:

```bash
pip install chloros-sdk
```

{% hint style="info" %}**Persediaan pertama kali**: Sebelum menggunakan SDK, aktifkan lesen Chloros+ anda dengan membuka kloros, chloros (pelayar) atau chloros CLI dan log masuk dengan kelayakan anda. Ini hanya perlu dilakukan sekali.
{% endhint %}

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
```***## Panduan Pemasangan

### Prasyarat

Sebelum memasang SDK, pastikan anda mempunyai:

1.**Desktop Chloros**Dipasang ([muat turun] (muat turun.md))
2.**Python 3.7+**Dipasang ([python.org] (https://www.python.org))
3.**Lesen Kloros+ Aktif**([Upgrade] (https://cloud.mapir.camera/pricing))

### Pasang melalui PIP **Pemasangan standard:**```bash
pip install chloros-sdk
```**Dengan sokongan pemantauan kemajuan:**```bash
pip install chloros-sdk[progress]
```**Pemasangan pembangunan:**```bash
pip install chloros-sdk[dev]
```

### Sahkan pemasangan

Uji SDK dipasang dengan betul:

```python
import chloros_sdk
print(f"Chloros SDK version: {chloros_sdk.__version__}")
```***## Persediaan kali pertama

### Pengaktifan lesen

SDK menggunakan lesen yang sama seperti chloros, chloros (penyemak imbas), dan chloros cli. Aktifkan sekali melalui GUI atau CLI:

1. Buka**kloros atau kloros (penyemak imbas)**dan log masuk pada pengguna<img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line">tab. Atau, buka**cli **.
2. Masukkan kelayakan kloros+ anda dan log masuk
3. Lesen di -cache secara tempatan (berterusan merentasi reboot)

{% hint style="success" %}**Persediaan satu kali**: Selepas log masuk melalui GUI atau CLI, SDK secara automatik menggunakan lesen cache. Tiada pengesahan tambahan diperlukan!
{% endhint %}

### Sambungan ujian

Sahkan SDK boleh menyambung ke kloros:

```python
from chloros_sdk import ChlorosLocal

# Initialize SDK (auto-starts backend if needed)
chloros = ChlorosLocal()

# Check status
status = chloros.get_status()
print(f"Backend running: {status['running']}")
```***## Rujukan API

### Kelas Chloroslocal

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
 ```**Parameter:** | Parameter                 | Jenis | Lalai                   | Penerangan                           |
| ------------------------- | ---- | ------------------------- | ----------------------------------------- |
| `api_url`                 | Str  | `"http://localhost:5000"` | URL backend kloros tempatan          |
| `auto_start_backend`      | bool | `True`                    | Secara automatik memulakan backend jika diperlukan |
| `backend_exe`             | Str  | `None` (auto-detect)      | Jalan ke backend boleh dilaksanakan            |
| `timeout`                 | int  | `30`                      | Permintaan masa tamat dalam beberapa saat            |
| `backend_startup_timeout` | int  | `60`                      | Timeout untuk permulaan backend (saat) |**Contoh:**```python 
# Default (auto-start backend)
chloros = ChlorosLocal()

# Connect to running backend
chloros = ChlorosLocal(auto_start_backend=False)

# Custom backend path
chloros = ChlorosLocal(backend_exe="C:/Custom/chloros-backend.exe")

# Custom timeout
chloros = ChlorosLocal(timeout=60)
```***### Kaedah

#### `create_project(project_name, camera=None)`

 Buat projek kloros baru.**Parameter:** | Parameter      | Jenis | Diperlukan | Penerangan                                              |
| -------------- | ---- | -------- | -------------------------------------------------------- |
| `project_name` | Str  | Ya      | Nama untuk projek                                     |
| `camera`       | Str  | No       | Templat Kamera (mis., "Survey3n \ _rgn", "Survey3w \ _Ocn") |**Pulangan:**`dict` - Project creation response**Contoh:**```python 
# Basic project
chloros.create_project("DroneField_A")

# With camera template
chloros.create_project("DroneField_A", camera="Survey3N_RGN")
```***#### `import_images(folder_path, recursive=False)`

 Import imej dari folder.**Parameter:** | Parameter     | Jenis     | Diperlukan | Penerangan                        |
| ------------- | -------- | -------- | -------------------------------------- |
| `folder_path` | str/jalan | Ya      | Jalan ke folder dengan gambar         |
| `recursive`   | bool     | No       | Subfolder Cari (Lalai: Salah) |**Pulangan:**`dict` - Import results with file count**Contoh:**```python 
# Import from folder
chloros.import_images("C:\\DroneImages\\Flight001")

# Import recursively
chloros.import_images("C:\\DroneImages", recursive=True)
```***#### `configure(**settings)`

 Konfigurasikan tetapan pemprosesan.**Parameter:** | Parameter                 | Jenis | Lalai                 | Penerangan                     |
| ------------------------- | ---- | ----------------------- | ------------------------------- |
| `debayer`                 | Str  | "Berkualiti tinggi (lebih cepat)" | Kaedah debayer                  |
| `vignette_correction`     | bool | `True`                  | Dayakan pembetulan vignette      |
| `reflectance_calibration` | bool | `True`                  | Dayakan penentukuran refleksi  |
| `indices`                 | senarai | `None`                  | Indeks tumbuh -tumbuhan untuk dikira |
| `export_format`           | Str  | "Tiff (16-bit)"         | Format output                   |
| `ppk`                     | bool | `False`                 | Dayakan pembetulan PPK          |
| `custom_settings`         | dicj | `None`                  | Tetapan tersuai lanjutan        |**Format eksport:** 

* `"TIFF (16-bit)"`- Disyorkan untuk GIS/Photogrammetry
* `"TIFF (32-bit, Percent)"`- Analisis saintifik
* `"PNG (8-bit)"`- Pemeriksaan visual
* `"JPG (8-bit)"`- Output termampat

**Indeks yang ada:**NDVI, NDRE, GNDVI, OSAVI, CIG, EVI, SAVI, MSAVI, MTVI2, dan banyak lagi.**Contoh:**```python
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
```***#### `process(mode="parallel", wait=True, progress_callback=None)`

 Proses imej projek.**Parameter:** | Parameter           | Jenis     | Lalai      | Penerangan                               |
| ------------------- | -------- | ------------ | --------------------------------------------- |
| `mode`              | Str      | `"parallel"` | Mod Pemprosesan: "Selari" atau "Serial"   |
| `wait`              | bool     | `True`       | Tunggu siap                       |
| `progress_callback` | boleh dipanggil | `None`       | Fungsi Panggilan Kembali Kemajuan (Kemajuan, MSG) |
| `poll_interval`     | terapung    | `2.0`        | Selang mengundi untuk kemajuan (saat)   |**Pulangan:**`dict` - Processing results 

{% hint style="warning" %}**Mod Paralel**: Memerlukan Lesen Chloros+. Skala secara automatik ke teras CPU anda (sehingga 16 pekerja).
{% endhint %}

**Contoh:**```python
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
```***#### `get_config()`

Dapatkan konfigurasi projek semasa.**Pulangan:**`dict` - Current project configuration**Contoh:**```python
config = chloros.get_config()
print(config['Project Settings'])
```***#### `get_status()`

Dapatkan maklumat status backend.**Pulangan:**`dict` - Backend status**Contoh:**```python
status = chloros.get_status()
print(f"Running: {status['running']}")
print(f"URL: {status['url']}")
```***#### `shutdown_backend()`

Shutdown backend (jika dimulakan oleh SDK).**Contoh:**```python
chloros.shutdown_backend()
```***### Fungsi kemudahan

#### `process_folder(folder_path,**options)`

Fungsi kemudahan satu baris untuk memproses folder.**Parameter:** | Parameter                 | Jenis     | Lalai         | Penerangan                    |
| ------------------------- | -------- | --------------- | ---------------------------------- |
| `folder_path`             | str/jalan | Diperlukan        | Jalan ke folder dengan gambar     |
| `project_name`            | Str      | Auto-dihasilkan  | Nama Projek                   |
| `camera`                  | Str      | `None`          | Templat kamera                |
| `indices`                 | senarai     | `["NDVI"]`      | Indeks untuk dikira           |
| `vignette_correction`     | bool     | `True`          | Dayakan pembetulan vignette     |
| `reflectance_calibration` | bool     | `True`          | Dayakan penentukuran refleksi |
| `export_format`           | Str      | "Tiff (16-bit)" | Format output                  |
| `mode`                    | Str      | `"parallel"`    | Mod pemprosesan                |
| `progress_callback`       | boleh dipanggil | `None`          | Panggilan balik kemajuan              |**Pulangan:**`dict` - Processing results**Contoh:**```python 
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
```***## Sokongan Pengurus Konteks

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
```***## Contoh lengkap

### Contoh 1: Pemprosesan Asas

Proses folder dengan tetapan lalai:

```python
from chloros_sdk import process_folder

# Process with default settings
results = process_folder("C:\\Datasets\\Field_A_2025_01_15")

print(f"Processing complete: {results}")
```***### Contoh 2: aliran kerja tersuai

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
```***

### Contoh 3: Pemprosesan Batch Pelbagai Folder

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

***### Contoh 4: Integrasi Paip Penyelidikan

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
```***### Contoh 5: Pemantauan Kemajuan Custom

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
```***### Contoh 6: Pengendalian ralat

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
```***

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

**Penggunaan:**```bash
python my_processor.py "C:\Flight001" "C:\Flight002" --indices NDVI NDRE GNDVI
```***

## Pengendalian pengecualian

SDK menyediakan kelas pengecualian khusus untuk jenis ralat yang berbeza:

### Pengecualian Hierarki

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

### Pemprosesan yang tidak menyekat

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

***## Penyelesaian masalah

### Backend tidak bermula**Isu:**SDK fails to start backend**Penyelesaian:**
1. Sahkan desktop kloros dipasang:

```python
import os
backend_path = r"C:\Program Files\MAPIR\Chloros\resources\backend\chloros-backend.exe"
print(f"Backend exists: {os.path.exists(backend_path)}")
```

2. Semak Windows Firewall tidak menyekat
3. Cuba jalan backend manual:

```python
chloros = ChlorosLocal(backend_exe="C:\\Path\\To\\chloros-backend.exe")
```***### Lesen tidak dikesan**Isu:**SDK warns about missing license**Penyelesaian:**
1. Buka chloros, chloros (penyemak imbas) atau chloros cli dan login.
2. Sahkan lesen di -cache:

```python
from pathlib import Path
import os

# Check cache location (Windows)
cache_path = Path(os.getenv('APPDATA')) / 'Chloros' / 'cache'
print(f"Cache exists: {cache_path.exists()}")
```

3. Sokongan Hubungi: info@mapir.camera***### Kesilapan import **Isu:**`ModuleNotFoundError: No module named 'chloros_sdk'`**Penyelesaian:**```bash
# Verify installation
pip show chloros-sdk

# Reinstall if needed
pip uninstall chloros-sdk
pip install chloros-sdk

# Check Python environment
python -c "import sys; print(sys.path)"
```***### Masa tamat memproses**Isu:**Processing times out**Penyelesaian:**
1. Meningkatkan masa tamat:

```python
chloros = ChlorosLocal(timeout=120)  # 2 minutes
```

2. Proses kelompok yang lebih kecil
3. Periksa ruang cakera yang ada
4. Memantau sumber sistem***### Pelabuhan sudah digunakan **Isu:**Backend port 5000 occupied**Penyelesaian:**```python
# Use different port
chloros = ChlorosLocal(api_url="http://localhost:5001")
```

Atau mencari dan menutup proses yang bercanggah:

```powershell
# PowerShell
Get-NetTCPConnection -LocalPort 5000
```***## Petua Prestasi

### Mengoptimumkan kelajuan pemprosesan

1.**Gunakan mod selari**(memerlukan chloros+)

```python
chloros.process(mode="parallel")  # Up to 16 workers
```

2.**Kurangkan resolusi output**(jika boleh diterima)

```python
chloros.configure(export_format="PNG (8-bit)")  # Faster than TIFF
```

3.**Lumpuhkan indeks yang tidak perlu**```python
# Only calculate needed indices
chloros.configure(indices=["NDVI"])  # Not all indices
```

4.**Proses di SSD**(bukan HDD)***### Pengoptimuman Memori

Untuk dataset besar:

```python
# Process in batches instead of all at once
# See "Memory Management" in Advanced Topics
```***### Pemprosesan latar belakang

Percuma Python untuk tugas lain:

```python
chloros.process(wait=False)  # Non-blocking

# Continue with other work
# ...
```***## Contoh Integrasi

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

### API Flask

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

### Notebook Jupyter

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
```***## Soalan Lazim

### S: Adakah SDK memerlukan sambungan internet?**A:**Only for initial license activation. After logging in via Chloros, Chloros (Browser) or Chloros CLI the license is cached locally and works offline for 30 days.***### S: Bolehkah saya menggunakan SDK pada pelayan tanpa GUI?**A:** Yes! Requirements:

* Windows Server 2016 atau lebih baru
* Dipasang kloros (satu kali)
* Lesen diaktifkan pada mana -mana mesin (lesen cache disalin ke pelayan)

***### S: Apakah perbezaan antara desktop, CLI, dan SDK?

| Ciri         | Desktop GUI | Baris arahan CLI | Python Sdk  |
| --------------- | ----------- | ---------------- | ----------- |
|**antara muka ** | Klik titik | Perintah          | Python api  |
|**Terbaik untuk ** | Kerja visual | Skrip        | Integrasi |
|**Automasi ** | Terhad     | Baik             | Cemerlang   |
|**fleksibiliti ** | Asas       | Baik             | Maksimum     |
|**Lesen ** | Chloros+    | Chloros+         | Chloros+    |***### S: Bolehkah saya mengedarkan aplikasi yang dibina dengan SDK?**A:** SDK code can be integrated into your applications, but: 

* Pengguna akhir memerlukan kloros dipasang
* Pengguna Akhir Memerlukan Lesen Kloros Aktif
* Pengagihan komersial memerlukan pelesenan OEM

Hubungi info@mapir.camera untuk pertanyaan OEM.

***### S: Bagaimana saya mengemas kini SDK?

```bash
pip install --upgrade chloros-sdk
```***### S: Di manakah imej diproses disimpan?

Secara lalai, di jalan projek:

```
Project_Path/
└── MyProject/
    └── Survey3N_RGN/          # Processed outputs
```***### S: Bolehkah saya memproses imej dari skrip python yang berjalan mengikut jadual?**A:**Yes! Use Windows Task Scheduler with Python scripts:

```python
# scheduled_processing.py
from chloros_sdk import process_folder

# Process today's flights
results = process_folder("C:\\Flights\\Today")
```

Jadual melalui penjadual tugas untuk dijalankan setiap hari.***### S: Adakah SDK menyokong async/menunggu?**A:**Current version is synchronous. For async behavior, use `wait=False` or run in separate thread:

```python
import threading

def process_thread():
    chloros.process()

thread = threading.Thread(target=process_thread)
thread.start()

# Continue with other work...
```***

## Mendapat pertolongan

### Dokumentasi

* **Rujukan API**: Halaman ini

### Saluran sokongan

* **E -mel**: info@mapir.camera ***laman web**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)***harga**: [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing)

### Kod contoh

Semua contoh yang disenaraikan di sini diuji dan siap pengeluaran. Salin dan menyesuaikannya untuk kes penggunaan anda.***## Lesen**Perisian Proprietari ** - Hak Cipta (c) 2025 Mapir Inc.

SDK memerlukan langganan kloros+ aktif. Penggunaan, pengedaran, atau pengubahsuaian yang tidak dibenarkan adalah dilarang.
