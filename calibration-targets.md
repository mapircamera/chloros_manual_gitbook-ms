---
description: Lab-measured panels used to calibrate captured data in post processing
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/calibration-targets
---

# Sasaran Kalibrasi

MAPIRmenawarkan pelbagai sasaran kalibrasi untuk merangkumi pelbagai aplikasi. T4-R50 yang padat seperti yang dilihat di bawah mengandungi 4 panel yang telah diukur untuk pantulan cahaya dari 250 - 2,500 nm.

<figure><img src=".gitbook/assets/t4-r50_2.jpg" alt=""><figcaption><p>MAPIR T4-R50</p></figcaption></figure>Sasaran rujukan T4 seragam mempunyai lengkung pantulan berikut, [muat turun data di sini](https://cdn.shopify.com/s/files/1/0972/5566/files/MAPIR_Diffuse_Reflectance_Standard_Calibration_Target_Data_T4.xlsx?v=1741759157):

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4 (250-2500nm).png" alt=""><figcaption><p>MAPIR T4 Pantulan :: 250-2500nm</p></figcaption></figure>

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4 (400-1000nm).png" alt=""><figcaption><p>MAPIR T4 Pantulan :: 400-1000nm</p></figcaption></figure>Sasaran rujukan berselerak T4P mempunyai lengkung pantulan berikut, [muat turun data di sini](https://cdn.shopify.com/s/files/1/0972/5566/files/MAPIR_Diffuse_Reflectance_Standard_Calibration_Target_Data_T4.xlsx?v=1741759157):

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4P -- 350-2500nm.jpg" alt=""><figcaption><p>MAPIR T4P Reflektan :: 250-2500nm</p></figcaption></figure>

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4P -- 400-1000nm.jpg" alt=""><figcaption><p>MAPIR T4P Reflektan :: 400-1000nm</p></figcaption></figure>Dengan melihat graf pantulan, anda boleh melihat bahawa nilai-nilai adalah panjang gelombang (paksi x) berbanding peratus pantulan (paksi y). Apabila kami mengambil imej sasaran penentukuran, kami kemudiannya mewujudkan hubungan antara nilai piksel dan peratus pantulan, dalam spektrum yang setiap jalur sensor kamera peka.

Ini bermakna dengan setiap imej yang anda rakam menggunakan kamera kami, anda boleh menggunakan foto sasaran pantulan kami, seperti [T4-R50](https://www.mapir.camera/collections/calibration-targets/products/diffuse-reflectance-standard-calibration-target-package-t3-r50) atau [T4-R125](https://www.mapir.camera/collections/multispectral-reflectance-reference-calibration-targets/products/diffuse-reflectance-standard-calibration-target-package-t4-r125) untuk mengkalibrasi imej bagi pantulan. Setelah dikalibrasi, setiap piksel dalam imej bersamaan dengan peratus pantulan.

Untuk keluaran **Survey3**, jika anda mengeluarkan imej yang telah dikalibrasi dalamChloros

sebagai JPG atauTIFF

biasa, maka peratus pantulan dikira dengan membahagikan nilai piksel dengan kedalaman bit format imej. Jadi untuk JPG bahagikan dengan 255, dan untukTIFF

bahagikan dengan 65,535. Anda juga boleh memilih format keluaran PERCENT dalamChloros

, dan setiap piksel akan berangka daripada nilai peratusan 0.0 hingga 1.0 (0% hingga 100% pantulan). Cuma ingat bahawa sesetengah aplikasi imej tidak dapat menerima imej peratus (titik apung), dan saiz storan mereka besar.

{% hint style="info" %}
**Reflektan LATTICE menggunakan skala piksel yang berbeza.** Reflektan LATTICE disimpan dengan DN 32768 = 100% pantulan (bukan 65535), dan setiap fail mengandungi tag XMP `Chloros:PixelScale` yang menyatakan skala nya. Baca tag tersebut dan bahagikannya dengannya daripada menganggapnya sebagai pemalar — lihat [Format Imej Keluaran](output-image-formats.md).
{% endhint %}

## Sasaran penentukuran dengan kamera LATTICE

Dengan kamera LATTICE, sasaran penentukuran adalah **pilihan** untuk pantulan:Chloros

sebaliknya boleh merujuk pantulan kepada iradiasi ke bawah yang diukur oleh penderia cahaya DAQ (ρ = π·L/E). Rujukan dipilih dengan tetapan sumber pantulan (Project Settings dalam GUI; `--reflectance-source` dalamCLI

; `reflectance_source` dalamSDK

):

| Nilai | Perilaku |
| --- | --- |
| `auto` *(lalai)* | Sasaran dalam bingkai yang lulus QA adalah **rujukan mutlak**; apabila tiada sasaran atau QA gagal,Chloros

beralih kepada pembahagi DAQ ke bawah. |
| `target` | Sasaran ketat sahaja — tiada penggantian DAQ. |
| `daq` | DAQ berkuasa — pengukuran DAQ sentiasa menjadi rujukan. |

Perilaku sasaran tambahan untuk LATTICE:

* **Geometri sasaran** — Panel berpenandaan ArUco, panel ROI tetap, dan sasaran jalur semuanya disokong; geometri diambil daripada konfigurasi sasaran projek.
* **Data sasaran yang diukur bagi setiap unit** — `--target-reflectance-dir DIR` menunjuk ke direktori imbasan pantulan sasaran yang diukur bagi setiap unit (`<serial>.csv`, dicari berdasarkan nombor siri/QR unit sasaran). Sekiranya sasaran tidak dikesan,Chloros

akan kembali kepada spektra T3/T4P nominal.
* **Penambatan temporal** — sasaran yang dikesan akan mengkalibrasi bingkai di sekelilingnya dan dikekalkan antara pengesanan sasaran.

Semantik dan contoh penuh bendera terdapat dalam [RujukanCLI

](reference/cli-reference.md) (lihat &quot;Per-Product Export Toggles&quot;).

### F988

&quot;Reflektan F988 dikalibrasi menggunakan panel reflektansi dalam adegan: jalur itu terletak di luar julat kalibrasi penderia cahaya DAQ, jadiChloros

menerapkan tangkapan panel terkini anda dan menahannya di antara penampakan panel.&quot;

Jika F988 dijalankan dengan penentukuran DAQ sahaja,Chloros

menolak pantulan berasaskan DAQ untuk jalur tersebut dan menerangkan sebabnya (sebab langkau `dls-uncalibrated-band-988`); aliran kerja panel adalah laluan yang disokong.

<div><figure><img src=".gitbook/assets/t3-125.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure> <figure><img src=".gitbook/assets/t3-125_2.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure> <figure><img src=".gitbook/assets/t3-125_closed.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure></div>
