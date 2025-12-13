---
Penerangan: Soalan yang sering ditanya
Metalinks:
  Ganti:
    - https://app.gitbook.com/s/o044kn3ws0uidvomskcr/faq
---

# FAQ

<details>

<summary>Bolehkah saya memproses imej dari kamera yang bukan jenama Mapir dengan kloros?</summary>

Tidak, kloros hanya menyokong pemprosesan imej kamera Mapir. Sila lihat senarai [model kamera yang disokong](disokong kamer.md) untuk maklumat lanjut. Kami menawarkan pemprosesan kamera lain di Mapir Cloud, lihat senarai penuh [di sini](https://mapir.gitbook.io/mapir-cloud/supported-cameras).

</details>

<details>

<summary>Bolehkah saya menentukur gambar saya untuk pemantulan tanpa sasaran penentukuran?</summary>

Tidak. Tanpa imej sasaran penentukuran yang ditangkap apabila imej bukan sasaran ditangkap, anda tidak akan dapat mengaitkan nilai piksel imej kepada peratus refleksi yang diketahui. Jika anda juga tidak memasukkan log dari sensor cahaya MAPIR maka spektrum cahaya ambien tidak akan diukur, dan hasil refleksi tidak akan tepat.

</details>

<details>

<summary>Bolehkah saya mengedit imej saya sebelum diproses di kloros?</summary>

Tidak. Chloros menganggap data input belum diubahsuai. Jangan ubah nama fail.

</details>

<details>

<summary>Bolehkah saya menetapkan kamera Mapir Survey3 saya untuk pendedahan auto dan memproses imej di kloros?</summary>

No. Dataset Imej Survey3 mesti mempunyai pendedahan tetap/terkunci, jadi tiada kelajuan pengatup auto atau ISO auto. Semua imej model kamera yang sama mesti mempunyai kelajuan pengatup yang sama dan ISO (pendedahan).

</details>

<details>

<summary>Can Chloros process or analyze orthomosaic images?</summary>

Tidak. Hanya imej kamera Mapir individu yang disokong, tidak dijahit imej seperti peta orthomosaik.

</details>

<details>

<summary>How can I speed up the target detection step of Chloros?</summary>

Dalam jadual penyemak imbas fail yang sebelum memilih imej sasaran di lajur kanan akan memberitahu kloros hanya melihat imej tersebut untuk sasaran penentukuran, sangat mempercepatkan pemprosesan.

</details>

<details>

<summary>Jika saya akan memuat naik imej saya ke <a href="https://www.mapir.camera/collections/software/products/mapir-cloud-subscription">MAPIR Cloud</a> patutkah saya memproses dalam Chloros sebelum memuat naik?</summary>

Jika anda bercadang untuk memuat naik ke platform pemprosesan dalam talian kami [Mapir Cloud](https://www.mapir.camera/collections/software/products/mapir-cloud-subscription) Jangan edit imej sebelum dimuat naik. Awan akan melakukan semua pemprosesan yang sama dan banyak lagi.

</details>

<details>

<summary>Will MAPIR ever support X feature? I really wish MAPIR offered X.</summary>

Kami sentiasa berminat untuk menerima maklum balas mengenai produk kami. Jika anda menemui masalah dengan produk kami, atau mempunyai cadangan tentang bagaimana kami dapat memperbaiki produk kami, sila [hubungi kami](https://www.mapir.camera/community/contact) untuk berkongsi pemikiran anda. Kebanyakan R \ & D kami dipandu dengan mendengar keperluan terbesar pelanggan kami.

</details>
