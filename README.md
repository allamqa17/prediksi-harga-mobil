# Prediksi Harga Mobil Bekas

## Tentang Project

Project ini bertujuan membangun model machine learning untuk
memprediksi harga mobil bekas berdasarkan karakteristik kendaraan
seperti merek, model, tahun, kilometer, transmisi, bahan bakar,
lokasi, umur kendaraan, dan kilometer per tahun.

## Dataset

Dataset berisi data mobil bekas dengan informasi mengenai:

- merek
- model
- tahun
- transmisi
- bahan bakar
- kilometer
- warna
- lokasi
- harga

Target yang diprediksi adalah `harga`.

Beberapa fitur tambahan dibuat selama proses feature engineering:

- `umur` = 2025 - tahun
- `km_per_tahun` = kilometer / umur

## Preprocessing

Preprocessing dilakukan menggunakan Pipeline dan ColumnTransformer.

Fitur numerik:
- missing value diisi dengan median
- dilakukan StandardScaler

Fitur kategorikal:
- missing value diisi dengan nilai paling sering muncul
- dilakukan One-Hot Encoding

Target harga ditransformasi menggunakan `log1p()` karena
distribusi harga sangat skewed. Setelah prediksi, hasil dikembalikan
ke skala Rupiah menggunakan `expm1()`.

## Model

Beberapa model dibandingkan menggunakan 5-fold cross-validation:

- Linear Regression
- Ridge
- Random Forest
- XGBoost

Hasil cross-validation menunjukkan Linear Regression dan Ridge
memiliki performa terbaik dengan R² sekitar 0.964.

Ridge kemudian dituning menggunakan RandomizedSearchCV.

Hyperparameter terbaik Ridge:

- alpha = 0.1578


## Hasil Evaluasi

| Model | MAE | RMSE | R² |
|---|---:|---:|---:|
| Baseline | Rp87,304,533 | Rp163,372,100 | -0.098 |
| Ridge Tuned | Rp17,035,379 | Rp41,348,773 | 0.930 |

Model Ridge Tuned menghasilkan:

- MAE sebesar Rp17,035,379
- RMSE sebesar Rp41,348,773
- R² sebesar 0.930

Artinya, secara rata-rata prediksi model meleset sekitar Rp17 juta
dari harga aktual. Model mampu menjelaskan sekitar 93% variasi harga
pada test set.


### Prediksi vs Aktual

![Prediksi vs Aktual](images\prediksi vs aktual & distribusi eror.png)

### Feature Interpretation

![Feature Interpretation](images\feature_importance.png)


## Temuan Bisnis

Model Ridge Tuned mampu memprediksi harga mobil bekas dengan rata-rata
kesalahan sekitar Rp17,0 juta (MAE) dan menjelaskan sekitar 93% variasi
harga pada test set (R² = 0.930). Model ini jauh mengungguli baseline
tebak-rata-rata yang menghasilkan MAE sekitar Rp87,3 juta dan R² = -0.098.

Hasil interpretasi model menunjukkan bahwa merek dan model kendaraan
merupakan faktor yang sangat berpengaruh terhadap prediksi harga.
BMW dan Mercedes-Benz memiliki koefisien positif terbesar, sedangkan
Daihatsu, Suzuki, Agya, dan Brio memiliki koefisien negatif yang cukup
besar. Pola tersebut secara umum sesuai dengan perbedaan segmen harga
kendaraan dalam dataset.

Namun, analisis prediksi vs aktual menunjukkan bahwa model cenderung
underestimate kendaraan dengan harga tinggi. Hal ini kemungkinan
berkaitan dengan jumlah sampel yang lebih sedikit pada segmen kendaraan
mahal sehingga model memiliki lebih sedikit contoh untuk dipelajari.

### Rekomendasi

Untuk penggunaan nyata, dataset sebaiknya diperbanyak terutama pada
segmen kendaraan premium dan kendaraan dengan harga tinggi.

Evaluasi model juga sebaiknya dilakukan berdasarkan kelompok harga,
karena performa keseluruhan R² = 0.930 belum tentu menggambarkan
akurasi secara merata pada seluruh rentang harga.

Untuk segmen premium, dapat dipertimbangkan penggunaan model atau
strategi khusus jika error tetap tinggi setelah penambahan data.

## Batasan

Beberapa batasan project ini:

1. Model hanya belajar dari data historis yang tersedia.
2. Model belum tentu akurat untuk kendaraan yang sangat langka atau
   memiliki sedikit contoh dalam dataset.
3. Error cenderung lebih besar pada kendaraan dengan harga tinggi.
4. Harga mobil merupakan data yang dapat berubah seiring waktu.
5. Model belum memasukkan faktor eksternal seperti kondisi kendaraan,
   jumlah pemilik, kondisi pasar, atau lokasi penjualan secara lebih
   mendalam.

## Rencana Pengembangan

Pengembangan berikutnya:

- menambah data kendaraan premium dan kendaraan dengan harga tinggi
- melakukan evaluasi MAE berdasarkan kelompok harga
- menguji model tree-based seperti XGBoost dengan tuning lebih lanjut
- menambahkan fitur kondisi kendaraan jika tersedia
- melakukan retraining secara berkala untuk menghadapi perubahan harga pasar
- membuat API untuk melakukan prediksi harga mobil baru

