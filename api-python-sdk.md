# API : Python SDK

**Chloros Python SDK** menyediakan akses terprogram kepada enjin pemprosesan imej Chloros, membolehkan automasi, aliran kerja tersuai dan penyepaduan yang lancar dengan aplikasi dan saluran paip penyelidikan Python anda.

### Ciri Utama

* 🍅 **Python asli** - Bersih, Pythonic API untuk pemprosesan imej
* 🔧 **Akses API Penuh** - Kawalan sepenuhnya ke atas pemprosesan Chloros
* 🚀 **Automasi** - Bina aliran kerja pemprosesan kelompok tersuai
* 🔗 **Integrasi** - Benamkan Chloros dalam aplikasi Python sedia ada
* 📊 **Sedia Penyelidikan** - Sesuai untuk saluran paip analisis saintifik
* ⚡ **Pemprosesan Selari** - Skala kepada teras CPU anda (Chloros+)

### Keperluan

| Keperluan | Butiran |
| -------------------- | ------------------------------------------------------------------- |
| **Chloros Dipasang** | Windows: Pemasang desktop; Linux: Pakej `.deb` |
| **Lesen** | Chloros+ ([pelan berbayar diperlukan](https://cloud.mapir.camera/pricing)) |
| **Sistem Pengendalian** | Windows 10/11 (64-bit), Linux x86_64 (amd64), Linux arm64 (NVIDIA Jetson JetPack 6) |
| **Python** | Python 3.7 atau lebih tinggi |
| **Memori** | 8GB RAM minimum (16GB disyorkan) |
| **Internet** | Diperlukan untuk pengaktifan lesen |

{% hint style="warning" %}
**Keperluan Lesen**: Python SDK memerlukan langganan Chloros+ berbayar untuk akses API. Pelan standard (percuma) tidak mempunyai akses API/SDK. Lawati [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing) untuk menaik taraf.
{% endhint %}

## Mula Pantas

### Pemasangan

Pasang melalui pip:

```bash
pip install chloros-sdk
```

{% hint style="info" %}
**Persediaan Kali Pertama**: Sebelum menggunakan SDK, aktifkan lesen Chloros+ anda dengan membuka Chloros, Chloros (Pelayar) atau Chloros XPROTX000272 inlogging dengan cPROTX00272 anda. Ini hanya perlu dilakukan sekali sahaja. Pada Linux (tiada GUI), gunakan: `chloros-cli login user@example.com 'password'`
{% endhint %}

### Penggunaan Asas

Proses folder dengan hanya beberapa baris:

```python
from chloros_sdk import process_folder

# One-line processing (Windows)
results = process_folder("C:\\DroneImages\\Flight001")

# One-line processing (Linux)
results = process_folder("/home/user/drone_images/flight001")
```

{% hint style="info" %}
**Laluan Merentas Platform**: Contoh kod pada halaman ini menggunakan laluan gaya Windows (cth., `C:\\DroneImages\\Flight001`). Pada Linux, gunakan laluan Linux sebaliknya (cth., `/home/user/drone_images/flight001` atau `~/drone_images/flight001`). SDK berfungsi sama pada kedua-dua platform.
{% endhint %}

### Kawalan Penuh

Untuk aliran kerja lanjutan:

```python
from chloros_sdk import ChlorosLocal

# Initialize SDK
chloros = ChlorosLocal()

# Create project
chloros.create_project("MyProject", camera="Survey3N_RGN")

# Import images
chloros.import_images("C:\\DroneImages\\Flight001")  # Windows
# chloros.import_images("/home/user/drone_images/flight001")  # Linux

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

## Panduan Pemasangan

### Prasyarat

Sebelum memasang SDK, pastikan anda mempunyai:

1. **Chloros dipasang** — Windows: Pemasang desktop ([muat turun](download.md)); Linux: Pakej `.deb` ([Pemasangan Linux](linux/linux-installation.md))
2. **Python 3.7+** dipasang ([python.org](https://www.python.org))
3. **lesen Chloros+ aktif** ([naik taraf](https://cloud.mapir.camera/pricing))

### Pasang melalui pip

**Pemasangan standard:**

```bash
pip install chloros-sdk
```

**Dengan sokongan pemantauan kemajuan:**

```bash
pip install chloros-sdk[progress]
```

**Pemasangan pembangunan:**

```bash
pip install chloros-sdk[dev]
```

### Sahkan Pemasangan

Uji bahawa SDK dipasang dengan betul:

```python
import chloros_sdk
print(f"Chloros SDK version: {chloros_sdk.__version__}")
```

***

## Persediaan Kali Pertama

### Pengaktifan Lesen

SDK menggunakan lesen yang sama seperti Chloros, Chloros (Pelayar) dan Chloros CLI. Aktifkan sekali melalui GUI atau CLI:**Windows:**Buka**Chloros atau Chloros (Pelayar)** dan log masuk pada Pengguna <img src=".gitbook/assets/icon_user.JPG" alt="" XPROTX000178 atau tab Pengguna CLI.**Linux:** Gunakan CLI (tiada GUI tersedia):

```bash
chloros-cli login user@example.com 'your_password'
```

Lesen dicache secara setempat dan berterusan sepanjang but semula.

{% hint style="success" %}
**Sediaan Satu Kali**: Selepas log masuk melalui GUI atau CLI, SDK secara automatik menggunakan lesen cache. Tiada pengesahan tambahan diperlukan!
{% endhint %}

{% hint style="info" %}
**Log Keluar**: Pengguna SDK boleh mengosongkan bukti kelayakan cache secara pemrograman menggunakan kaedah `logout()`. Lihat [kaedah log keluar()](#logout) dalam Rujukan API.
{% endhint %}

### Sambungan Ujian

Sahkan SDK boleh bersambung ke Chloros:

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

### Kelas ChlorosLocal

Kelas utama untuk pemprosesan imej Chloros tempatan.

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

**Parameter:**

| Parameter | Taip | Lalai | Penerangan |
| -------------------------- | ---- | -------------------------- | ------------------------------------- |
| `api_url` | str | `"http://localhost:5000"` | URL daripada bahagian belakang Chloros tempatan |
| `auto_start_backend` | bool | `True` | Mulakan bahagian belakang secara automatik jika perlu |
| `backend_exe` | str | `None` (autopengesan) | Laluan ke bahagian belakang boleh laku |
| `timeout` | int | `30` | Minta tamat masa dalam beberapa saat |
| `backend_startup_timeout` | int | `60` | Tamat masa untuk permulaan bahagian belakang (saat) |

**Contoh:**

```python
# Default (auto-start backend, auto-detect path on Windows and Linux)
chloros = ChlorosLocal()

# Connect to running backend
chloros = ChlorosLocal(auto_start_backend=False)

# Custom backend path (Windows)
chloros = ChlorosLocal(backend_exe="C:/Custom/chloros-backend.exe")

# Custom backend path (Linux)
chloros = ChlorosLocal(backend_exe="/opt/mapir/chloros/backend/chloros-backend")

# Custom timeout with longer startup (e.g., for Jetson)
chloros = ChlorosLocal(timeout=60, backend_startup_timeout=120)
```

{% hint style="info" %}
**Pengesanan automatik merentas platform**: SDK secara automatik mencuba laluan hujung belakang yang betul untuk platform anda:
* **Windows**: `C:\Program Files\MAPIR\Chloros\resources\backend\chloros-backend.exe`
* **Linux (.deb)**: `/usr/lib/chloros/chloros-backend`
* **Linux (manual)**: `/opt/mapir/chloros/backend/chloros-backend`
{% endhint %}

***

### Kaedah

#### `create_project(project_name, camera=None)`

Buat projek Chloros baharu.

**Parameter:**

| Parameter | Taip | Diperlukan | Penerangan |
| -------------- | ---- | -------- | -------------------------------------------------------- |
| `project_name` | str | Ya | Nama untuk projek |
| `camera` | str | Tidak | Templat kamera (cth., "Survey3N\_RGN", "Survey3W\_OCN") |

**Pemulangan:** `dict` - Respons penciptaan projek**Contoh:**

```python
# Basic project
chloros.create_project("DroneField_A")

# With camera template
chloros.create_project("DroneField_A", camera="Survey3N_RGN")
```

***

#### `import_images(folder_path, recursive=False)`

Import imej dari folder.

**Parameter:**

| Parameter | Taip | Diperlukan | Penerangan |
| ------------- | -------- | -------- | ------------------------------------ |
| `folder_path` | str/Laluan | Ya | Laluan ke folder dengan imej |
| `recursive` | bool | Tidak | Cari subfolder (lalai: Palsu) |

**Pemulangan:** `dict` - Import hasil carian dengan kiraan fail**Contoh:**

```python
# Import from folder
chloros.import_images("C:\\DroneImages\\Flight001")

# Import recursively
chloros.import_images("C:\\DroneImages", recursive=True)
```

***

#### `configure(**settings)`

Konfigurasikan tetapan pemprosesan.

**Parameter:**

| Parameter | Taip | Lalai | Penerangan |
| -------------------------- | ---- | ------------------------ | ------------------------------- |
| `debayer` | str | "Standard (Pantas, Kualiti Sederhana)" | Kaedah Debayer |
| `vignette_correction` | bool | `True` | Dayakan pembetulan vignet |
| `reflectance_calibration` | bool | `True` | Dayakan penentukuran pantulan |
| `indices` | senarai | `None` | Indeks tumbuh-tumbuhan untuk dikira |
| `export_format` | str | "TIFF (16-bit)" | Format output |
| `ppk` | bool | `False` | Dayakan pembetulan PPK |
| `custom_settings` | dict | `None` | Tetapan tersuai lanjutan |

**Format Eksport:**

* `"TIFF (16-bit)"` - Disyorkan untuk GIS/fotogrametri
* `"TIFF (32-bit, Percent)"` - Analisis saintifik
* `"PNG (8-bit)"` - Pemeriksaan visual
* `"JPG (8-bit)"` - Output termampat

**Indeks yang Tersedia:**NDVI, NDRE, GNDVI, OSAVI, CIG, EVI, SAVI, MSAVI0, MSAVI0, MSAVI0, MSAVI0, 334XPROTX0, MSAVI0, 334XPROTX0, MSAVI0,334XPROTX0, MSAVI0, 334MSAVI000332XPROTX0, NDVI, NDVI, NDVI, NDVI, XPROTX000328**Contoh:**

```python
# Basic configuration
chloros.configure(
    vignette_correction=True,
    reflectance_calibration=True,
    indices=["NDVI", "NDRE"]
)

# Advanced configuration
chloros.configure(
    debayer="Standard (Fast, Medium Quality)",
    vignette_correction=True,
    reflectance_calibration=True,
    ppk=True,
    export_format="TIFF (32-bit, Percent)",
    indices=["NDVI", "NDRE", "GNDVI", "OSAVI", "CIG"]
)
```

***

#### `process(mode="parallel", wait=True, progress_callback=None)`

Memproses imej projek.

**Parameter:**

| Parameter | Taip | Lalai | Penerangan |
| ------------------- | -------- | ------------ | ---------------------------------------- |
| `mode` | str | `"parallel"` | Mod pemprosesan: "selari" atau "siri" |
| `wait` | bool | `True` | Tunggu sehingga selesai |
| `progress_callback` | boleh dipanggil | `None` | Fungsi panggil balik kemajuan(progres, msg) |
| `poll_interval` | terapung | `2.0` | Selang pengundian untuk kemajuan (saat) |

**Pemulangan:** `dict` - Memproses hasil

{% hint style="warning" %}
**Mod Selari**: Memerlukan lesen Chloros+. Skala secara automatik kepada teras CPU anda (sehingga 16 pekerja).
{% endhint %}

**Contoh:**

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

#### `get_config()`

Dapatkan konfigurasi projek semasa.

**Pemulangan:** `dict` - Konfigurasi projek semasa**Contoh:**

```python
config = chloros.get_config()
print(config['Project Settings'])
```

***

#### `get_status()`

Dapatkan maklumat status bahagian belakang termasuk kemajuan pemprosesan setiap utas.

**Pemulangan:** `dict` - Status hujung belakang dengan struktur berikut:

```python
{
    "running": True,
    "url": "http://localhost:5000",
    "processing": {
        "percent": 75.0,
        "phase": "processing"
    },
    "export": {
        "percent": 50.0,
        "phase": "exporting",
        "active": True
    }
}
```

**Contoh:**

```python
status = chloros.get_status()
print(f"Running: {status['running']}")
print(f"URL: {status['url']}")
print(f"Processing: {status['processing']['percent']}%")
print(f"Export: {status['export']['percent']}% - Active: {status['export']['active']}")
```

***

#### `shutdown_backend()`

Matikan bahagian belakang (jika dimulakan oleh SDK).

**Contoh:**

```python
chloros.shutdown_backend()
```

***

#### `logout()`

Kosongkan bukti kelayakan cache daripada sistem setempat.

**Penerangan:**

Log keluar secara pemrograman dengan mengalih keluar bukti kelayakan pengesahan cache. Ini berguna untuk:
* Bertukar antara akaun Chloros+ yang berbeza
* Membersihkan kelayakan dalam persekitaran automatik
* Tujuan keselamatan (cth., mengalih keluar bukti kelayakan sebelum menyahpasang)

**Pemulangan:** `dict` - Hasil operasi Log Keluar**Contoh:**

```python
from chloros_sdk import ChlorosLocal

# Initialize SDK
chloros = ChlorosLocal()

# Clear cached credentials
result = chloros.logout()
print(f"Logout successful: {result}")

# After logout, login required via GUI/CLI/Browser before next SDK use
```

{% hint style="info" %}
**Pengesahan Semula Diperlukan**: Selepas memanggil `logout()`, anda mesti log masuk semula melalui Chloros, Chloros (Pelayar) atau Chloros CLI sebelum menggunakan CLI.3000208XPROTX.
{% endhint %}

***

### Fungsi Keselesaan

#### `process_folder(folder_path, **options)`

Fungsi kemudahan satu baris untuk memproses folder.

**Parameter:**

| Parameter | Taip | Lalai | Penerangan |
| -------------------------- | -------- | --------------- | ------------------------------ |
| `folder_path` | str/Laluan | Diperlukan | Laluan ke folder dengan imej |
| `project_name` | str | Dijana secara automatik | Nama projek |
| `camera` | str | `None` | Templat kamera |
| `indices` | senarai | `["NDVI"]` | Indeks untuk dikira |
| `vignette_correction` | bool | `True` | Dayakan pembetulan vignet |
| `reflectance_calibration` | bool | `True` | Dayakan penentukuran pantulan |
| `export_format` | str | "TIFF (16-bit)" | Format output |
| `mode` | str | `"parallel"` | Mod pemprosesan |
| `progress_callback` | boleh dipanggil | `None` | Panggil balik kemajuan |

**Pemulangan:** `dict` - Memproses hasil**Contoh:**

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

## Contoh Lengkap

{% hint style="info" %}
**Pengguna Linux**: Semua contoh di bawah menggunakan laluan Windows. Gantikan laluan `C:\\...` dengan laluan Linux anda (cth., `/home/user/...` atau `~/...`). Semua fungsi SDK adalah sama merentas platform.
{% endhint %}

### Contoh 1: Pemprosesan Asas

Proses folder dengan tetapan lalai:

```python
from chloros_sdk import process_folder

# Process with default settings
results = process_folder("C:\\Datasets\\Field_A_2025_01_15")

print(f"Processing complete: {results}")
```

***

### Contoh 2: Aliran Kerja Tersuai

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
    debayer="Standard (Fast, Medium Quality)",
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

### Contoh 3: Memproses Kelompok Berbilang Folder

Memproses beberapa set data penerbangan:

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

### Contoh 4: Penyepaduan Saluran Paip Penyelidikan

Sepadukan Chloros dengan analisis data:

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

### Contoh 5: Pemantauan Kemajuan Tersuai

Penjejakan kemajuan lanjutan dengan pengelogan:

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

### Contoh 6: Pengendalian Ralat

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
        return False, f"Backend error: {e}. Ensure Chloros is installed (Windows installer or Linux .deb package)."
    
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

### Contoh 7: Pengurusan Akaun dan Log Keluar

Urus kelayakan secara pemrograman:

```python
from chloros_sdk import ChlorosLocal

def switch_account():
    """Clear credentials to switch to a different account"""
    try:
        chloros = ChlorosLocal()
        
        # Clear current credentials
        result = chloros.logout()
        print("✓ Credentials cleared successfully")
        print("Please log in with new account via Chloros, Chloros (Browser), or CLI")
        
        return True
    
    except Exception as e:
        print(f"✗ Logout failed: {e}")
        return False

def secure_cleanup():
    """Remove credentials for security purposes"""
    try:
        chloros = ChlorosLocal()
        chloros.logout()
        print("✓ Credentials removed for security")
        
    except Exception as e:
        print(f"Warning: Cleanup error: {e}")

# Switch accounts
if switch_account():
    print("\nRe-authenticate via Chloros GUI/CLI/Browser before next SDK use")

# Or perform secure cleanup
# secure_cleanup()
```

***

### Contoh 8: Alat Baris Perintah

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
    parser.add_argument('--logout', action='store_true',
                       help='Clear cached credentials before processing')
    
    args = parser.parse_args()
    
    # Handle logout if requested
    if args.logout:
        from chloros_sdk import ChlorosLocal
        chloros = ChlorosLocal()
        chloros.logout()
        print("Credentials cleared. Please re-login via Chloros GUI/CLI/Browser.")
        return 0
    
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

**Penggunaan:**

```bash
# Process multiple folders
python my_processor.py "C:\Flight001" "C:\Flight002" --indices NDVI NDRE GNDVI

# Clear cached credentials
python my_processor.py --logout
```

***

## Pengendalian Pengecualian

SDK menyediakan kelas pengecualian khusus untuk jenis ralat yang berbeza:

### Hierarki Pengecualian

```python
ChlorosError                    # Base exception
├── ChlorosBackendError        # Backend startup/connection issues
├── ChlorosLicenseError        # License validation issues
├── ChlorosConnectionError     # Network/connection failures
├── ChlorosProcessingError     # Image processing failures
├── ChlorosAuthenticationError # Authentication failures
└── ChlorosConfigurationError  # Configuration errors
```

### Contoh Pengecualian

```python
from chloros_sdk import ChlorosLocal
from chloros_sdk.exceptions import *

try:
    chloros = ChlorosLocal()
    chloros.process()

except ChlorosLicenseError:
    print("Chloros+ license required. Upgrade at cloud.mapir.camera/pricing")

except ChlorosBackendError:
    print("Backend failed to start. Ensure Chloros is installed (Windows installer or Linux .deb package).")

except ChlorosProcessingError as e:
    print(f"Processing failed: {e}")

except ChlorosError as e:
    print(f"General Chloros error: {e}")
```

***

## Topik Lanjutan

### Konfigurasi Bahagian Belakang Tersuai

Gunakan lokasi atau konfigurasi bahagian belakang tersuai:

```python
chloros = ChlorosLocal(
    backend_exe="C:\\Custom\\chloros-backend.exe",
    api_url="http://localhost:5001",  # Custom port
    timeout=60,                        # Longer timeout
    backend_startup_timeout=120        # 2 minutes startup
)
```

### Pemprosesan Tidak Menyekat

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

### Pengurusan Memori

Untuk set data yang besar, proses dalam kelompok:

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

## Menyelesaikan masalah

### Bahagian Belakang Tidak Bermula

**Isu:** SDK gagal untuk memulakan hujung belakang**Penyelesaian:**

1. Sahkan Chloros dipasang:

```python
import os
import platform

# Auto-detect backend path
if platform.system() == "Windows":
    backend_path = r"C:\Program Files\MAPIR\Chloros\resources\backend\chloros-backend.exe"
else:
    backend_path = "/usr/lib/chloros/chloros-backend"

print(f"Backend exists: {os.path.exists(backend_path)}")
```

2. Semak firewall (Windows) atau ketersediaan port (Linux: `lsof -i :5000`)
3. Cuba laluan hujung belakang manual:

```python
# Windows
chloros = ChlorosLocal(backend_exe="C:\\Path\\To\\chloros-backend.exe")

# Linux
chloros = ChlorosLocal(backend_exe="/opt/mapir/chloros/backend/chloros-backend")
```

***

### Lesen Tidak Dikesan**Isu:** SDK memberi amaran tentang kehilangan lesen**Penyelesaian:**

1. Buka Chloros, Chloros (Pelayar) atau Chloros CLI dan log masuk.
2. Sahkan lesen dicache:

```python
from pathlib import Path
import os
import platform

# Check cache location
if platform.system() == "Windows":
    cache_path = Path(os.getenv('APPDATA')) / 'Chloros' / 'cache'
else:
    cache_path = Path.home() / '.cache' / 'chloros'

print(f"Cache exists: {cache_path.exists()}")
```

3. Jika mengalami isu kelayakan, kosongkan bukti kelayakan cache dan log masuk semula:

```python
from chloros_sdk import ChlorosLocal

# Clear cached credentials
chloros = ChlorosLocal()
chloros.logout()

# Then login again via Chloros, Chloros (Browser), or Chloros CLI
```

4. Hubungi sokongan: info@mapir.camera

***

### Ralat Import**Isu:** `ModuleNotFoundError: No module named 'chloros_sdk'`**Penyelesaian:**

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

### Tamat Masa Pemprosesan**Isu:** Tamat masa pemprosesan**Penyelesaian:**

1. Tingkatkan tamat masa:

```python
chloros = ChlorosLocal(timeout=120)  # 2 minutes
```

2. Proses kelompok yang lebih kecil
3. Semak ruang cakera yang tersedia
4. Pantau sumber sistem

***

### Pelabuhan Sudah Digunakan**Isu:** Port hujung belakang 5000 diduduki**Penyelesaian:**

```python
# Use different port
chloros = ChlorosLocal(api_url="http://localhost:5001")
```

Atau cari dan tutup proses bercanggah:

```powershell
# Windows PowerShell
Get-NetTCPConnection -LocalPort 5000
```

```bash
# Linux
lsof -i :5000
kill $(lsof -t -i :5000)
```

***

## Petua Prestasi

### Optimumkan Kelajuan Pemprosesan

1. **Gunakan Mod Selari** (memerlukan Chloros+)

```python
chloros.process(mode="parallel")  # Up to 16 workers
```

2. **Kurangkan Resolusi Output** (jika boleh diterima)

```python
chloros.configure(export_format="PNG (8-bit)")  # Faster than TIFF
```

3. **Lumpuhkan Indeks Tidak Perlu**

```python
# Only calculate needed indices
chloros.configure(indices=["NDVI"])  # Not all indices
```

4. **Proses pada SSD** (bukan HDD)***

### Pengoptimuman Memori

Untuk set data yang besar:

```python
# Process in batches instead of all at once
# See "Memory Management" in Advanced Topics
```

***

### Pemprosesan Latar Belakang

Kosongkan Python untuk tugasan lain:

```python
chloros.process(wait=False)  # Non-blocking

# Continue with other work
# ...
```

***

## Contoh Integrasi

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

### Kelalang API

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

### Buku Nota Jupyter

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

### S: Adakah SDK memerlukan sambungan internet?

**J:** Hanya untuk pengaktifan lesen awal. Selepas log masuk melalui Chloros, Chloros (Pelayar) atau Chloros CLI, lesen dicache secara setempat dan berfungsi di luar talian selama 30 hari.***

### S: Bolehkah saya menggunakan SDK pada pelayan tanpa GUI?**J:** Ya! SDK berfungsi tanpa kepala pada kedua-dua pelayan Windows dan Linux.**Linux (disyorkan untuk tanpa kepala):**
* Pasang melalui pakej `.deb`
* Aktifkan lesen: `chloros-cli login user@example.com 'password'`

**Windows Server:**
* Windows Server 2016 atau lebih baru
* Chloros dipasang (sekali)
* Lesen diaktifkan melalui CLI atau pada mana-mana mesin

***

### S: Apakah perbezaan antara Desktop, CLI dan SDK?

| Ciri | GUI Desktop | CLI Baris Perintah | Python SDK |
| --------------- | ----------- | ---------------- | ----------- |
| **Antaramuka** | Klik titik | Perintah | Python API |
| **Terbaik Untuk** | Kerja visual | Skrip | Integrasi |
| **Automasi** | Terhad | Baik | Cemerlang |
| **Fleksibiliti** | Asas | Baik | Maksimum |
| **Lesen** | Chloros+ | Chloros+ | Chloros+ |***

### S: Bolehkah saya mengedarkan apl yang dibina dengan SDK?**J:** Kod SDK boleh disepadukan ke dalam aplikasi anda, tetapi:

* Pengguna akhir memerlukan Chloros dipasang
* Pengguna akhir memerlukan lesen aktif Chloros+
* Pengedaran komersial memerlukan pelesenan OEM

Hubungi info@mapir.camera untuk pertanyaan OEM.

***

### S: Bagaimanakah cara saya mengemas kini SDK?

```bash
pip install --upgrade chloros-sdk
```

***

### S: Di manakah imej yang diproses disimpan?

Secara lalai, dalam Laluan Projek :

```

Project_Path/
└── MyProject/
    └── Survey3N_RGN/          # Processed outputs
```

***

### S: Bolehkah saya memproses imej daripada skrip Python berjalan mengikut jadual?**J:** Ya! Gunakan penjadual OS anda dengan skrip Python:

```python
# scheduled_processing.py
from chloros_sdk import process_folder

# Process today's flights
results = process_folder("/data/flights/today")  # Linux
# results = process_folder("C:\\Flights\\Today")  # Windows
```

**Windows:** Jadualkan melalui Penjadual Tugasan untuk dijalankan setiap hari.**Linux:** Jadual melalui cron:

```cron
# Run at 2 AM daily
0 2 * ** /usr/bin/python3 /home/user/scheduled_processing.py >> /var/log/chloros.log 2>&1
```

***

### S: Adakah SDK menyokong async/menunggu?**J:** Versi semasa adalah segerak. Untuk tingkah laku tak segerak, gunakan `wait=False` atau jalankan dalam urutan berasingan:

```python
import threading

def process_thread():
    chloros.process()

thread = threading.Thread(target=process_thread)
thread.start()

# Continue with other work...
```

***

### S: Bagaimanakah cara saya bertukar antara akaun Chloros+ yang berbeza?**J:** Gunakan kaedah `logout()` untuk mengosongkan bukti kelayakan cache, kemudian log masuk semula dengan akaun baharu:

```python
from chloros_sdk import ChlorosLocal

# Clear current credentials
chloros = ChlorosLocal()
chloros.logout()

# Re-login via Chloros, Chloros (Browser), or Chloros CLI with new account
```

Selepas log keluar, sahkan dengan akaun baharu melalui GUI, Penyemak Imbas atau CLI sebelum menggunakan SDK sekali lagi.

***

## Mendapatkan Bantuan

### Dokumentasi

* **Rujukan API**: Halaman ini

### Saluran Sokongan

* **E-mel**: info@mapir.camera
* **Tapak web**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* **Harga**: [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing)

### Kod Contoh

Semua contoh yang disenaraikan di sini telah diuji dan sedia pengeluaran. Salin dan sesuaikan mereka untuk kes penggunaan anda.

***

## Lesen**Perisian Milik** - Hak Cipta (c) 2025 MAPIR Inc.

SDK memerlukan langganan Chloros+ yang aktif. Penggunaan, pengedaran atau pengubahsuaian yang tidak dibenarkan adalah dilarang.