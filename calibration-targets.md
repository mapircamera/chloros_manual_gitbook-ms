---
description: Lab-measured panels used to calibrate captured data in post processing
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/calibration-targets
---

# Sasaran Penentukuran

MAPIR menawarkan pelbagai sasaran penentukuran untuk meliputi pelbagai aplikasi. T4-R50 padat yang dilihat di bawah mengandungi 4 panel yang telah diukur untuk pemantulan cahaya dari 250 - 2,500 nm.

<figure><img src=".gitbook/assets/t4-r50_2.jpg" alt=""><figcaption><p>MAPIR T4-R50</p></figcaption></figure>

Sasaran rujukan meresap T4 mempunyai lengkung pemantulan berikut, [muat turun data di sini](https://cdn.shopify.com/s/files/1/0972/5566/files/MAPIR_Diffuse_Reflectance_Standard_Calibration_Target_Data_T4.xlsx?v=1741759157):

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Kalibrasi Data Sasaran T4 (250-2500nm).png" alt=""><figcaption><p>MAPIR T4 Reflectance :: 250-2500nm</p></figcaption></figure>

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4 (400-1000nm).png" alt=""><figcaption><p>MAPIR T4 Reflectance :: 400-1000nm</p></figcaption></figure>

Sasaran rujukan meresap T4P mempunyai lengkung pemantulan berikut, [muat turun data di sini](https://cdn.shopify.com/s/files/1/0972/5566/files/MAPIR_Diffuse_Reflectance_Standard_Calibration_Target_Data_T4.xlsx?v=1741759157):

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Kalibrasi Data Sasaran T4P -- 350-2500nm.jpg" alt=""><figcaption><p>MAPIR :: T4P Reflectance 250-2500nm</p></figcaption></figure>

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Kalibrasi Data Sasaran T4P -- 400-1000nm.jpg" alt=""><figcaption><p>MAPIR :: T4P Reflectance 400-1000nm</p></figcaption></figure>

Melihat graf pemantulan, anda boleh melihat bahawa nilai adalah panjang gelombang (paksi-x) berbanding peratus pemantulan (paksi-y). Apabila kami menangkap imej sasaran penentukuran kami kemudian mencipta hubungan antara nilai piksel dan peratus pemantulan, dalam spektrum yang setiap jalur sensor kamera sensitif.

Ini bermakna bahawa dengan setiap imej yang anda tangkap dengan kamera kami, anda boleh menggunakan foto sasaran pemantulan kami, seperti [T4-R50](https://www.mapir.camera/collections/calibration-targets/products/diffuse-reflectance-standard-calibration-target-package-t3-r50) atau [T4-R125](https://www.mapir.camera/collections/multispectral-reflectance-reference-calibration-targets/products/diffuse-reflectance-standard-calibration-target-package-t4-r125) untuk menentukur imej bagi pemantulan. Setelah ditentukur setiap piksel dalam imej adalah sama dengan peratus pantulan.

Jika anda mengeluarkan imej yang ditentukur dalam Chloros sebagai JPG atau TIFF biasa, maka peratus pemantulan dikira dengan membahagikan nilai piksel dengan kedalaman bit format imej. Jadi untuk JPG bahagi dengan 255, dan untuk TIFF bahagi dengan 65,535. Anda juga boleh memilih output format PERCENT dalam Chloros, dan kemudian setiap piksel akan berjulat daripada nilai peratus 0.0 hingga 1.0 (0% hingga 100% pemantulan). Perlu diingat bahawa sesetengah aplikasi imej tidak boleh menerima peratus (titik terapung) imej, dan ia adalah storan bersaiz besar.

<div><figure><img src=".gitbook/assets/t3-125.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure> <figure><img src=".gitbook/assets/t3-125_2.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure> <figure><img src=".gitbook/assets/t3-125_closed.jpg" alt=""><figcaption><p>T4-R125</p></e></figtion>