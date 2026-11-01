# Sistem Pendukung Keputusan Rekomendasi Laptop untuk Mahasiswa Menggunakan K-Means dan TOPSIS

**Metode:** K-Means Clustering + TOPSIS (Technique for Order Preference by Similarity to Ideal Solution)
**Bahasa dan alat:** Python (pandas, scikit-learn), Jupyter Notebook / Google Colab
**Tahun:** 2026

---

## 1. Judul dan Ringkasan

Banyak pilihan laptop dengan spesifikasi dan harga yang berbeda, sehingga mahasiswa sulit membandingkannya secara terstruktur. Proyek ini mengelompokkan **1.283 laptop** ke dalam tiga tingkat spesifikasi (Rendah, Menengah, Tinggi) dengan K-Means, lalu meranking seluruh laptop dengan TOPSIS berdasarkan harga dan spesifikasi utama.

## 2. Identitas Kelompok

| No | NIM | Nama |
|----|-----|------|
| 1 | 412024034 | Jonathan Siswoyo |
| 2 | 412024040 | Zakharia Melkisedek Maranatha Nababan |
| 3 | 412025005 | Juan Raffles Fernando |
| 4 | 412025006 | Michael Valentino Susanto |

## 3. Metode yang Dipilih

| Tahap | Metode | Fungsi |
|---|---|---|
| Pengelompokan | K-Means (k = 3) | Mengelompokkan laptop berdasarkan kemiripan spesifikasi |
| Pemeringkatan | TOPSIS | Meranking laptop dari harga dan spesifikasi |

## 4. Alasan Pemilihan Metode

1. **K-Means:** dataset tidak memiliki label kelas, sehingga pengelompokan dilakukan dengan unsupervised learning. Jumlah cluster 3 dipilih agar hasilnya mudah dibaca sebagai tingkat Rendah, Menengah, dan Tinggi.
2. **TOPSIS:** pemilihan laptop melibatkan beberapa kriteria sekaligus, yaitu harga (semakin kecil semakin baik) dan spesifikasi (semakin besar semakin baik). TOPSIS menangani kriteria *cost* dan *benefit* dalam satu perhitungan.
3. **Gabungan keduanya:** K-Means menyederhanakan pilihan menjadi kelompok, TOPSIS menentukan urutan rekomendasi.

## 5. Rumus Utama

**Standardisasi (sebelum K-Means)**

z = (x − μ) / σ

**TOPSIS**

1. Normalisasi: r_ij = x_ij / √(Σ x_ij²)
2. Normalisasi terbobot: v_ij = w_j · r_ij
3. Solusi ideal positif (A⁺) dan negatif (A⁻): untuk kriteria *benefit* A⁺ = maksimum, A⁻ = minimum; untuk *cost* sebaliknya.
4. Jarak ke solusi ideal: D⁺ dan D⁻ (jarak Euclidean)
5. Nilai preferensi: **V_i = D⁻ / (D⁺ + D⁻)**. Nilai terbesar = ranking 1.

## 6. Tahapan Algoritma

1. Membaca dataset laptop, menghapus duplikat dan kolom `Operating System Version`.
2. Mengubah teks menjadi angka: `RAM` (`8GB` → 8), `Weight` (`1.37kg` → 1,37), `Screen Size`, `Price`, dan `Storage` (1 TB = 1024 GB, kolom `Storage_GB`).
3. Mengubah harga dari Euro ke Rupiah dengan kurs tetap **Rp19.000/EUR** (kolom `Price_IDR`).
4. Membuat kolom kategori sederhana (`Storage_Type`, `CPU_Brand`, `CPU_Family`, `GPU_Brand`) dengan aturan kata kunci. Kolom ini tidak di-encode dan tidak dipakai pada K-Means maupun TOPSIS.
5. Membuang baris yang `Weight_KG`-nya kosong, sehingga tersisa **1.283 laptop**.
6. Menyeragamkan nama CPU/GPU (`normalize_name`), lalu menggabungkannya dengan data benchmark untuk mendapatkan `CPU_Score` dan `GPU_Score`.
7. Standardisasi (`StandardScaler`) pada `CPU_Score`, `GPU_Score`, `RAM_GB`, `Storage_GB`.
8. K-Means (k = 3, `random_state = 42`, `n_init = 10`), lalu cluster diberi label Rendah, Menengah, Tinggi berdasarkan rata-rata spesifikasi.
9. Perhitungan TOPSIS dengan lima kriteria, lalu pemeringkatan dan penyimpanan hasil ke Excel.

```
Dataset -> Preprocessing -> CPU_Score & GPU_Score -> StandardScaler -> K-Means -> TOPSIS -> Preference Value -> Ranking
```

## 7. Arti Setiap Kriteria

| Kriteria | Arti | Bobot | Tipe |
|---|---|---|---|
| Price_IDR | Harga laptop dalam Rupiah | 30% | Cost |
| CPU_Score | Skor performa prosesor (benchmark) | 25% | Benefit |
| GPU_Score | Skor performa kartu grafis (benchmark) | 20% | Benefit |
| RAM_GB | Kapasitas RAM | 15% | Benefit |
| Storage_GB | Total kapasitas penyimpanan | 10% | Benefit |

Total bobot = 100%. Bobot ditetapkan oleh kelompok sebagai asumsi prioritas (harga paling besar).

Fitur K-Means hanya empat (`CPU_Score`, `GPU_Score`, `RAM_GB`, `Storage_GB`) karena K-Means bertugas mengelompokkan berdasarkan spesifikasi. Harga baru dipakai pada TOPSIS.

## 8. Interpretasi Hasil

### 8.1 Hasil K-Means (rata-rata per cluster)

| Cluster | Jumlah laptop | CPU_Score | GPU_Score | RAM (GB) | Storage (GB) |
|---|---|---|---|---|---|
| Rendah | 727 | 2.658,25 | 808,23 | 5,87 | 402,38 |
| Menengah | 421 | 5.203,95 | 2.205,52 | 9,98 | 783,12 |
| Tinggi | 135 | 7.025,56 | 9.872,94 | 17,16 | 1.242,07 |

**Evaluasi clustering** (data terstandardisasi, k = 3):

- Silhouette Score: **0,3682** (semakin mendekati 1 semakin baik)
- Davies-Bouldin Index: **1,1992** (semakin kecil semakin baik)

Label Rendah, Menengah, dan Tinggi adalah hasil pengelompokan, bukan label aktual dari dataset.

### 8.2 Lima laptop teratas (TOPSIS)

| Rank | Laptop | Harga | Cluster | Preference Value |
|---|---|---|---|---|
| 1 | Asus Rog Strix | Rp29,431 jt | Tinggi | 0,612562 |
| 2 | Asus G701VO-IH74K | Rp24,301 jt | Tinggi | 0,598602 |
| 3 | Asus Rog Strix | Rp24,681 jt | Tinggi | 0,595100 |
| 4 | Asus Rog Strix | Rp41,781 jt | Tinggi | 0,578213 |
| 5 | Lenovo IdeaPad Y910-17ISK | Rp50,597 jt | Tinggi | 0,577078 |

Seluruh 10 laptop teratas berada pada cluster Tinggi. Hal ini wajar karena empat kriteria *benefit* (spesifikasi) memiliki total bobot 70%, lebih besar dari bobot harga (30%).

### 8.3 Batasan

- Bobot kriteria ditetapkan oleh kelompok, bukan hasil metode pembobotan formal. Jika bobot diubah, peringkat dapat berubah.
- Jumlah cluster (k = 3) belum dibandingkan dengan nilai k lain.
- Kurs Euro ke Rupiah bersifat tetap (Rp19.000).
- Pemilihan fitur dilakukan berdasarkan kebutuhan metode, belum memakai metode seleksi fitur formal.

## 9. Keterkaitan dengan Materi Kuliah

| Materi | Penerapan pada proyek |
|---|---|
| 05 Transformation dan Feature Engineering | Konversi harga, ekstraksi teks menjadi angka, `CPU_Score` dan `GPU_Score`, `StandardScaler` |
| 06 Feature Selection dan Data Representation | Empat fitur K-Means dan lima kriteria TOPSIS, setiap laptop direpresentasikan sebagai vektor numerik |
| 07 Classification | Belum diterapkan. Proyek memakai unsupervised learning (K-Means), dievaluasi dengan Silhouette dan Davies-Bouldin |

## 10. Struktur Proyek dan Cara Menjalankan

```
spk-rekomendasi-laptop/
├── README.md
├── requirements.txt
├── notebooks/
│   └── 01_spk_laptop_kmeans_topsis.ipynb
├── data/
│   ├── raw/           (dataset mentah + file benchmark CPU/GPU)
│   └── processed/
│       └── hasil_spk_rekomendasi_laptop.xlsx
└── docs/              (PPT, laporan, proposal)
```

**Menjalankan:**

1. Buka `notebooks/01_spk_laptop_kmeans_topsis.ipynb` di Google Colab (notebook memakai `google.colab.files.upload()`).
2. Jalankan sel dari atas. Saat diminta, upload dataset laptop (sel 2), lalu dua file benchmark (sel 14). Daftar file ada di `data/raw/README.md`.
3. Hasil akhir tersimpan sebagai `hasil_spk_rekomendasi_laptop.xlsx`.

Untuk menjalankan di komputer sendiri: `pip install -r requirements.txt`, ganti pemanggilan `files.upload()` dengan `pd.read_excel(...)` ke file di `data/raw/`, dan hapus baris `files.download(...)`.
