# Laporan Analisis PCD Assignment 01: Downsampling dan Upsampling
**Nama:** Farhan Rizky Alkarim  
**NIM:** 25/568476/PA/23997   
**Mata Kuliah:** Pengolahan Citra Digital  

---

## 1. Pendahuluan
Praktikum ini bertujuan untuk mengevaluasi pengaruh reduksi spasial (downsampling) dan rekonstruksi spasial (upsampling) pada citra digital menggunakan berbagai pendekatan algoritma manual berbasis operasi matriks NumPy.

Pengujian dilakukan menggunakan 3 citra uji dengan karakteristik visual yang berbeda:
* **Citra 1 (High Frequency):** Gambar dengan detail tajam dan tekstur tinggi (Gerbang UGM).
* **Citra 2 (Low Frequency):** Gambar dengan gradasi warna mulus (Langit).
* **Citra 3 (Binary / Line Art):** Gambar berisi teks atau logo berkarakter tegas.

---

## 2. Analisis Metode Downsampling

Proses downsampling dilakukan dengan mereduksi ukuran piksel menggunakan kernel blok berukuran 4 x 4 (faktor reduksi 4x).

* **Max Downsampling:**
  * **Karakteristik Visual:** Mengambil nilai intensitas tertinggi pada setiap blok piksel.
  * **Analisis Hasil:** Sangat efektif dalam mempertegas garis, batas tepi (edge), dan fitur-fitur terang. Namun, pada citra gradasi (Low Frequency), metode ini menyebabkan citra terlihat lebih terang secara tidak alami (overexposed) dan kehilangan detail pada area bernuansa gelap. Pada citra teks, garis teks cenderung menebal.
* **Average Downsampling:**
  * **Karakteristik Visual:** Menghitung rata-rata aritmetika dari seluruh piksel dalam blok.
  * **Analisis Hasil:** Menghasilkan citra reduksi yang paling seimbang dan natural secara keseluruhan. Kecerahan rata-rata citra asli tetap terjaga dengan baik. Kerugiannya adalah terjadinya efek pengaburan (blurring) ringan pada area berpola tajam (High Frequency).
* **Median Downsampling:**
  * **Karakteristik Visual:** Mengambil nilai tengah dari kumpulan piksel blok yang telah diurutkan.
  * **Analisis Hasil:** Menghasilkan citra yang mirip dengan Average, namun memiliki keunggulan utama dalam meredam bintik acak (noise jenis salt-and-pepper). Garis ketajaman tepi terjaga lebih baik dibanding Average, meskipun membutuhkan waktu proses sorting yang sedikit lebih lama secara komputasi.

---

## 3. Analisis Metode Upsampling

Proses upsampling dilakukan untuk memperbesar kembali citra hasil reduksi ke ukuran semula (faktor perbesaran 4.0x) menggunakan tiga teknik interpolasi spasial:

* **Nearest Neighbor (NN):**
  * **Prinsip Kerja:** Mengisi nilai piksel baru berdasarkan piksel terdekat tanpa pembobotan matematika.
  * **Karakteristik Visual:** Menghasilkan efek gerigi atau piksel berkotak-kotak (pixelated / staircase effect) yang sangat jelas pada area garis diagonal dan lengkungan.
  * **Kelebihan & Kekurangan:** Secara komputasi paling cepat, namun kualitas visualnya paling rendah untuk foto kontinyu. Sangat cocok hanya untuk citra seni piksel (pixel art) atau gambar biner/teks jika ingin mempertahankan batas tegas tanpa efek blur.
* **Bilinear Interpolation:**
  * **Prinsip Kerja:** Merata-ratakan nilai 4 piksel tetangga terdekat dengan pembobotan linier berdasarkan jarak koordinat.
  * **Karakteristik Visual:** Menghilangkan efek gerigi kotak-kotak secara signifikan dan menghasilkan transisi warna yang mulus.
  * **Kelebihan & Kekurangan:** Tampilan jauh lebih alami dibanding NN, namun garis tepi objek resolusi tinggi terlihat agak buram (soft / fuzzy).
* **Bicubic Interpolation:**
  * **Prinsip Kerja:** Memperhitungkan matriks 4 x 4 (16 piksel tetangga) menggunakan fungsi pembobotan kurva kubik (Kernel Catmull-Rom).
  * **Karakteristik Visual:** Menghasilkan citra akhir dengan kualitas terbaik, gradasi sangat mulus, dan tingkat ketajaman tepi (edge sharpness) yang lebih tinggi dibanding Bilinear.
  * **Kelebihan & Kekurangan:** Paling optimal secara estetika visual, namun memiliki beban perhitungan komputasi yang paling tinggi.

---

## 4. Rangkuman Perbandingan Metode

**A. Downsampling**
* **Max Downsampling**
  * Kelebihan: Mempertegas fitur terang dan batas tepi.
  * Kekurangan: Merusak warna gradasi dan area gelap.
  * Rekomendasi: Deteksi tepi dan ekstraksi fitur.
* **Average Downsampling**
  * Kelebihan: Menjaga keseimbangan warna dan kecerahan secara alami.
  * Kekurangan: Efek blur ringan pada detail halus.
  * Rekomendasi: Kompresi citra umum.
* **Median Downsampling**
  * Kelebihan: Menghilangkan noise bintik acak.
  * Kekurangan: Waktu komputasi sedikit lebih lama.
  * Rekomendasi: Citra ber-noise dan citra medis.

**B. Upsampling**
* **Nearest Neighbor (NN)**
  * Kelebihan: Sangat cepat dan efisien secara komputasi.
  * Kekurangan: Terjadi efek kotak-kotak (pixelated).
  * Rekomendasi: Pixel art dan grafik biner/teks.
* **Bilinear Interpolation**
  * Kelebihan: Transisi warna mulus.
  * Kekurangan: Hasil sedikit buram/soft pada garis tepi.
  * Rekomendasi: Perbesaran citra standar.
* **Bicubic Interpolation**
  * Kelebihan: Hasil paling tajam dan alami.
  * Kekurangan: Beban komputasi tertinggi.
  * Rekomendasi: Restorasi citra dan fotografi profesional.

---

## 5. Kesimpulan
1. Pemilihan metode downsampling bergantung pada tujuan pengolahan: Average paling cocok untuk mempertahankan distribusi warna alami, sedangkan Median unggul jika terdapat noise pada citra.
2. Pemilihan metode upsampling menunjukkan trade-off antara kualitas visual dan beban komputasi. Bicubic Interpolation terbukti secara konsisten memberikan rekonstruksi visual terbaik untuk citra alamiah, sedangkan Nearest Neighbor hanya efisien untuk citra non-gradasi.
