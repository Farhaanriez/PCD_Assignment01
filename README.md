# LAPORAN ANALISIS TUGAS PENGOLAHAN CITRA DIGITAL
**TOPIK: AKUISISI CITRA – DOWNSAMPLING DAN UPSAMPLING**

**Identitas Tugas**
* **Nama**: Farhan Rizky Alkarim
* **NIM**: 25/568476/PA/23997
* **Mata Kuliah**: Pengolahan Citra Digital
* **Platform Pengujian**: Google Colab (Python / NumPy Base)

---

## 1. Pendahuluan dan Latar Belakang

Akuisisi citra merupakan tahapan paling awal dalam sistem pengolahan citra digital yang melibatkan proses transformasi sinyal kontinu dari dunia nyata menjadi matriks diskrit yang dapat diproses oleh komputer. Dalam praktikum ini, evaluasi difokuskan pada dua operasi spasial utama: **Downsampling** (penurunan resolusi spasial) dan **Upsampling** (merekonstruksi kembali citra ke resolusi yang lebih tinggi).

Proses reduksi dan rekonstruksi ini dilakukan secara manual menggunakan manipulasi matriks berbasis NumPy tanpa memanfaatkan pustaka pengolah citra tingkat tinggi seperti OpenCV (`cv2.resize`). Untuk menguji ketahanan dan dampak dari masing-masing algoritma, pengujian dilakukan terhadap tiga jenis citra input yang memiliki karakteristik frekuensi spasial berbeda:
1. **Citra High Frequency (Detail/Tekstur Tajam):** Citra yang kaya akan perubahan garis ketajaman, seperti arsitektur bangunan, serat bulu hewan, atau pola berulang.
2. **Citra Low Frequency (Gradasi Mulus):** Citra dengan transisi warna yang relatif halus dan perlahan, seperti lanskap pemandangan alam, kulit, atau warna langit.
3. **Citra Biner / Line Art (Teks dan Logo):** Citra dengan kontras tinggi yang dominan memiliki batas warna tegas antara latar depan (*foreground*) dan latar belakang (*background*).

---

## 2. Analisis Metode Downsampling

Proses *downsampling* dilakukan dengan membagi citra input menjadi blok-blok kecil berukuran $4 \times 4$ piksel (faktor reduksi 4x), kemudian mengekstraksi satu nilai tunggal dari setiap blok untuk merepresentasikan piksel baru pada citra output.

### 2.1. Max Downsampling
* **Mekanisme Kerja:** Mengambil nilai intensitas kecerahan tertinggi ($\max$) dari seluruh piksel yang berada di dalam satu blok $4 \times 4$.
* **Hasil Pengamatan Visual:** Metode ini cenderung mempertahankan dan mempertegas fitur-fitur yang terang (*bright features*) serta batas tepi (*edges*). Pada citra biner atau teks, *Max Downsampling* membuat ketebalan garis teks bertambah secara visual.
* **Kelemahan:** Pada citra dengan gradasi halus (Low Frequency), terjadi distorsi kecerahan yang signifikan. Citra mengalami peningkatan kecerahan secara tidak seimbang (*overexposure*), serta kehilangan informasi pada area-area gelap/bayangan.

### 2.2. Average Downsampling
* **Mekanisme Kerja:** Menghitung nilai rata-rata aritmetika ($\text{mean}$) dari seluruh intensitas piksel di dalam blok $4 \times 4$.
* **Hasil Pengamatan Visual:** Menghasilkan citra hasil reduksi yang paling proporsional secara keseluruhan. Distribusi intensitas dan rata-rata kecerahan warna citra asli dapat dipertahankan dengan sangat baik.
* **Kelemahan:** Mengalami efek pengaburan halus (*blurring*) pada area-area yang memiliki tekstur rapat atau detail tinggi (High Frequency), karena nilai piksel ekstrim diratakan.

### 2.3. Median Downsampling
* **Mekanisme Kerja:** Mengurutkan seluruh nilai piksel di dalam blok $4 \times 4$ secara mendatar, kemudian mengambil nilai intensitas yang berada tepat di tengah-tengah (elemen *median*).
* **Hasil Pengamatan Visual:** Karakteristik visual yang dihasilkan mirip dengan *Average Downsampling*, namun dengan kemampuan mempertahankan ketajaman batas tepi (*edge sharpness*) yang sedikit lebih baik.
* **Keunggulan Khusus:** Sangat efisien dalam mengeliminasi bintik-bintik derau acak (*salt-and-pepper noise*), karena nilai piksel ekstrim yang diakibatkan oleh *noise* secara otomatis terabaikan saat pengambilan nilai median.

---

## 3. Analisis Metode Upsampling

Proses *upsampling* merekonstruksi citra hasil reduksi (*downsampled image*) agar kembali ke dimensi ukuran semula (faktor perbesaran 4.0x) dengan memperkirakan nilai piksel baru pada posisi-posisi koordinat yang kosong.

### 3.1. Nearest Neighbor Interpolation
* **Mekanisme Kerja:** Mengisi nilai piksel pada posisi koordinat baru dengan menyalin nilai intensitas dari piksel terdekat pada citra asal tanpa pembobotan matematika.
* **Hasil Pengamatan Visual:** Menghasilkan efek berpetak-petak atau gerigi yang sangat tajam (*pixelated/staircase effect*), terutama pada batas garis diagonal dan kurva melengkung.
* **Analisis Kinerja:** Memiliki kompleksitas komputasi yang paling rendah dan proses eksekusi paling cepat. Meskipun kurang memuaskan untuk fotografi alami, metode ini sangat cocok untuk mempertahankan ketajaman grafik piksel (*pixel art*) atau dokumen teks tanpa menimbulkan area buram di sekitar huruf.

### 3.2. Bilinear Interpolation
* **Mekanisme Kerja:** Menghitung nilai piksel baru berdasarkan rata-rata terbobot dari 4 piksel tetangga terdekat ($2 \times 2$) yang mengelilingi posisi target, menggunakan interpolasi linier pada sumbu horizontal dan vertikal.
* **Hasil Pengamatan Visual:** Mengeliminasi efek gerigi berkotak-kotak secara signifikan dan menghasilkan transisi gradasi warna yang jauh lebih mulus jika dibandingkan dengan *Nearest Neighbor*.
* **Analisis Kinerja:** Memberikan keseimbangan yang baik antara kualitas visual dan beban komputasi. Namun, pada batas tepi yang tajam, hasil rekonstruksi cenderung terlihat sedikit buram (*fuzzy/soft edges*).

### 3.3. Bicubic Interpolation
* **Mekanisme Kerja:** Memperhitungkan matriks $4 \times 4$ (total 16 piksel tetangga) mengelilingi koordinat target dengan menerapkan fungsi kurva pembobotan kubik (Kernel Catmull-Rom).
* **Hasil Pengamatan Visual:** Menghasilkan kualitas rekonstruksi visual tertinggi dari seluruh metode yang diuji. Garis tepi objek dipertahankan secara tajam (*sharp edges*) dengan gradasi antar-piksel yang sangat natural.
* **Analisis Kinerja:** Metode ini meminimalkan efek artefak visual secara maksimal, namun membutuhkan daya komputasi dan waktu pemrosesan paling tinggi karena kompleksitas perhitungan fungsi kubik pada 16 tetangga piksel.

---

## 4. Evaluasi Komparatif Berdasarkan Karakteristik Citra Input

### 4.1. Uji Coba Citra High Frequency (Detail & Tekstur)
* Pada saat dilakukan *downsampling*, metode *Average* dan *Median* menghasilkan citra yang paling mendekati penampilan citra asli, sedangkan *Max* cenderung menghilangkan tekstur gelap.
* Saat proses *upsampling*, teknik *Bicubic Interpolation* terbukti paling unggul dalam merekonstruksi kembali tekstur-tekstur rapat tanpa memunculkan efek garis patah-patah yang mengganggu.

### 4.2. Uji Coba Citra Low Frequency (Gradasi Mulus)
* *Average Downsampling* mempertahankan kehalusan transisi warna pada area langit/pemandangan secara sempurna. Sebaliknya, *Max Downsampling* merusak gradasi alami dengan memunculkan kontur buatan yang terlalu terang.
* Pada rekonstruksi *upsampling*, *Bilinear* dan *Bicubic* mampu menghasilkan permukaan yang mulus tanpa efek *banding*. *Nearest Neighbor* gagal menghasilkan visualisasi yang baik karena memecah gradasi mulus menjadi kotak-kotak warna terpisah.

### 4.3. Uji Coba Citra Biner / Line Art (Teks & Logo)
* *Max Downsampling* mempertegas keterbacaan garis teks yang tipis, sementara *Average Downsampling* menyebabkan area tepi teks menjadi agak abu-abu/buram.
* Untuk *upsampling* citra teks, *Nearest Neighbor* mempertahankan ketajaman garis batas latar hitam-putih secara tegas, sedangkan *Bilinear* dan *Bicubic* menghasilkan efek bayangan buram (*halo effect*) di sekeliling karakter huruf.

---

## 5. Kesimpulan

Berdasarkan pengujian dan analisis yang telah dilakukan, dapat disimpulkan beberapa poin utama:
1. Tidak ada satu metode tunggal yang sempurna untuk semua kondisi; pemilihan algoritma *sampling* harus disesuaikan dengan karakteristik frekuensi spasial citra dan tujuan akhir pengolahan.
2. Untuk proses **Downsampling**, metode *Average* merupakan pilihan terbaik secara umum untuk mempertahankan distribusi warna alami. *Max Downsampling* lebih tepat digunakan untuk aplikasi ekstraksi fitur/tepi, sedangkan *Median Downsampling* sangat efektif untuk eliminasi *noise*.
3. Untuk proses **Upsampling**, terdapat *trade-off* yang jelas antara kualitas estetika visual dan kecepatan komputasi. *Bicubic Interpolation* memberikan rekonstruksi paling natural dan tajam untuk fotografi, sementara *Nearest Neighbor* unggul dalam kecepatan pemrosesan dan efisiensi pada citra teks atau grafik biner.
