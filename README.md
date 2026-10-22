# Tugas Kelompok Fundamen Sains Data: Linear Regression dan Logistic Regression

Pengganti kehadiran Pertemuan 10 dan 11 · Fundamen Sains Data · Informatika UII

## Anggota Kelompok

| Nama | NIM | Kontribusi |
|---|---|---|
| Gusti Surya Aditama | 25523053 | Mengerjakan bagian A Linear Regression |
| Muhammad Lutfi Syarif | 25523021 |Mengerjakan B Logistic Regression |
| Naufal Alaam Najib | 25523063 | Mereview hasil dan membantu analisis |

## Struktur Repositori

```
├── README.md
├── A_linear_regression.ipynb     # Bagian A: Linear Regression (Auto MPG)
├── B_logistic_regression.ipynb   # Bagian B: Logistic Regression (Telco Churn)
└── data/
    ├── mpg.csv                        # dataset Bagian A
    └── Telco_customer_churn.xlsx      # dataset Bagian B
```

Kedua notebook dibuat untuk Google Colab dan sudah dijalankan dari awal sampai akhir tanpa error (output tersimpan). Notebook A memuat data dari URL sumber, dengan cadangan salinan di `data/`. Notebook B membaca `data/Telco_customer_churn.xlsx`; kalau file tidak ditemukan (misalnya di Colab tanpa repo), notebook meminta file itu diunggah.

## Sumber Dataset

| Bagian | Dataset | Deskripsi singkat | Sumber |
|---|---|---|---|
| A | Auto MPG (398 baris, 7 fitur) | Spesifikasi mobil 1970 sampai 1982; target `mpg` (numerik kontinu) | UCI: <https://archive.ics.uci.edu/dataset/9/auto+mpg> · CSV: <https://github.com/mwaskom/seaborn-data/blob/master/mpg.csv> |
| B | Telco Customer Churn, IBM (7.043 baris, 33 kolom) | Data pelanggan telekomunikasi; target `Churn Label` (Yes/No, biner) | Kaggle: <https://www.kaggle.com/datasets/yeanzc/telco-customer-churn-ibm-dataset> |

## Ringkasan Hasil

### Bagian A: Linear Regression (prediksi `mpg`)

- Preprocessing: 6 baris `horsepower` yang kosong dibuang, outlier dipertahankan (nilainya wajar), `origin` di-one-hot.
- Seleksi fitur: `displacement` dan `cylinders` dibuang karena multikolinearitas (VIF 22,9 dan 10,7). Dipertahankan: `horsepower`, `weight`, `acceleration`, `model_year`, `origin`.
- Hasil:

| | MAE | MSE | RMSE | R² |
|---|---|---|---|---|
| Latih | 2,530 | 11,006 | 3,318 | 0,826 |
| Uji (79 baris) | 2,510 | 11,183 | 3,344 | 0,781 |

- Metrik latih dan uji berdekatan, jadi tidak ada tanda overfitting. R² rata-rata 5-fold cross-validation sekitar 0,81.
- Fitur paling berpengaruh: `weight` (sekitar -5,5 mpg per +1000 lbs), lalu `model_year` (sekitar +0,77 mpg per tahun).
- Keterbatasan utama: hubungan non-linear. Dengan target `log(mpg)`, R² uji naik ke 0,853.

### Bagian B: Logistic Regression (prediksi churn)

- Preprocessing: `Total Charges` diubah ke angka (11 nilai kosong adalah pelanggan baru dengan tenure 0, diisi 0). Kolom `Churn Value`, `Churn Score`, `Churn Reason`, `CLTV`, ID, dan lokasi dibuang. Kelas tidak seimbang (26,5% churn) ditangani dengan `class_weight='balanced'`.
- Seleksi fitur: chi-square dan VIF. Dibuang `Total Charges` (redundan dengan `tenure`, r = 0,83), `Gender`, dan `Phone Service`.
- Hasil di data uji (1.409 baris, split stratified):

| Model | Accuracy | Precision | Recall | F1 | ROC-AUC |
|---|---|---|---|---|---|
| Tanpa class_weight | 0,803 | 0,646 | 0,570 | 0,605 | 0,846 |
| class_weight='balanced' (utama) | 0,743 | 0,511 | 0,762 | 0,612 | 0,847 |

- Metrik yang paling relevan adalah recall kelas churn. Confusion matrix model utama: TN 762, FP 273, FN 89, TP 285.
- Faktor yang paling terkait dengan churn: layanan fiber optic, jenis kontrak, lama berlangganan, dan pembayaran electronic check.
- Keterbatasan: precision sekitar 51%, asumsi linear, multikolinearitas pada `Monthly Charges`, satu kali split tanpa cross-validation, dan koefisien bukan bukti sebab-akibat.

## Cara Menjalankan

1. Buka notebook di Google Colab (File, Open notebook, GitHub, lalu tempel URL repositori ini).
2. Jalankan Runtime, Run all. Library yang dipakai (pandas, scikit-learn, seaborn, statsmodels, scipy, openpyxl) sudah tersedia di Colab.
