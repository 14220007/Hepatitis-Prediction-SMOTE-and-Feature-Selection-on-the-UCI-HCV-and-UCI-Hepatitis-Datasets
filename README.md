# Re-analisis Kebocoran Data pada Prediksi Hepatitis

Paket replikasi untuk artikel **"Re-analisis Kebocoran Data pada Prediksi Hepatitis: Pemisahan Efek Imputasi, SMOTE, dan Seleksi Fitur pada Dataset UCI HCV dan UCI Hepatitis"**.

> *English summary.* Replication package for a re-analysis of two public UCI hepatitis datasets (HCV data, n = 615; Hepatitis, n = 155). The notebook compares a leakage-free pipeline (all preprocessing fitted inside training folds) with four incorrect preprocessing orders (imputation, SMOTE, feature selection on the full data, and their combination), separates true leakage from test-set composition effects (V2 vs V2b), and tests paired differences with the corrected resampled t-test (Nadeau & Bengio, 2003) and Holm correction.

## Isi repositori

| Folder / berkas | Isi |
|---|---|
| `hepatitis_reanalisis.ipynb` | Notebook utama (output dikosongkan, path relatif). Jalankan dari folder utama repositori. |
| `notebook_hasil_eksekusi/hepatitis_reanalisis_v2_dieksekusi.ipynb` | Notebook persis seperti dijalankan untuk artikel, lengkap dengan seluruh output. |
| `data/` | Dataset UCI HCV dan UCI Hepatitis (salinan dari UCI Machine Learning Repository, lihat `data/SUMBER_DATA.md`). |
| `results/` | Seluruh hasil yang dipakai di artikel: skor per lipatan, selisih terhadap P-C, dekomposisi V2/V2b, uji sensitivitas, grafik, checksum data, dan konfigurasi eksperimen. |
| `requirements.txt` | Versi paket yang dipakai saat eksperimen dijalankan. |

### Berkas hasil utama

| Berkas | Isi |
|---|---|
| `hasil_semua_mentah.csv` | Skor 7 metrik untuk setiap lipatan (50 lipatan) per dataset, model, dan prosedur (P-A, P-B, P-C, V1–V4b) |
| `selisih_variasi_vs_pc.csv` | Selisih setiap variasi terhadap P-C, selang kepercayaan 95%, nilai p, p Holm, p Benjamini-Hochberg, dan kesimpulan |
| `dekomposisi_V2_ringkas.csv`, `dekomposisi_V4_ringkas.csv` | Pemisahan selisih menjadi kebocoran sungguhan dan efek komposisi data uji |
| `hasil_sensitivitas_mentah.csv`, `selisih_sensitivitas.csv` | Uji sensitivitas: seed 7, seed 2024, dan imputasi KNN |
| `konfigurasi_eksperimen.json` | Versi Python, sistem operasi, dan parameter eksperimen |

## Prosedur singkat

- **Prosedur benar:** imputasi median → standardisasi (KNN) → SelectKBest (k = 8) → penanganan ketidakseimbangan kelas → klasifikasi, seluruhnya di dalam pipeline sehingga hanya dipelajari dari data latih.
  - P-A: tanpa penanganan; P-B: pembobotan kelas (DT, RF, XGB); P-C: SMOTE di dalam lipatan (acuan).
- **Variasi keliru:** V1 imputasi pada seluruh data; V2 SMOTE sebelum pembagian; V2b seperti V2 tetapi data uji hanya baris asli; V3 seleksi fitur pada seluruh data; V4/V4b gabungan.
- **Model:** Naive Bayes, Decision Tree, KNN, Random Forest, Gradient Boosting, XGBoost.
- **Validasi:** repeated stratified 5-fold cross-validation, 10 ulangan (50 lipatan); tuning dengan grid search 3-fold di dalam lipatan (PR-AUC).
- **Uji statistik:** corrected resampled t-test, koreksi Holm dan Benjamini-Hochberg per metrik.

## Cara menjalankan

```bash
git clone <URL-repositori-ini>
cd hepatitis-leakage-reanalysis
pip install -r requirements.txt
jupyter notebook hepatitis_reanalisis.ipynb
```

Atur `FAST_MODE = True` di sel konfigurasi untuk uji coba cepat. Dengan `FAST_MODE = False` (pengaturan artikel), waktu jalan sekitar 3–4 jam pada komputer pribadi.

Lingkungan saat eksperimen: Python 3.11.8, Windows 10/11, NumPy 1.26.4, pandas 2.2.1, SciPy 1.12.0, scikit-learn 1.3.0, imbalanced-learn 0.14.2, XGBoost 2.1.4, Matplotlib 3.8.0.

## Catatan transparansi

- Notebook di folder utama adalah salinan dari notebook yang dieksekusi dengan tiga perubahan: path data dibuat relatif terhadap folder `data/`, sel checksum diaktifkan kembali, dan sel penyimpanan konfigurasi diganti dengan versi yang tidak gagal bila ada variabel yang belum terdefinisi. Logika analisis tidak diubah.
- Pada notebook yang dieksekusi, sel checksum sempat dinonaktifkan dan path data ditulis absolut; berkas `results/checksum_data.csv` dihitung ulang dari salinan data di repositori ini.

## Lisensi

- Kode: MIT License (lihat `LICENSE`).
- Data: UCI Machine Learning Repository, lisensi Creative Commons Attribution 4.0 International (CC BY 4.0). Mohon sitasi sumber data asli (lihat `data/SUMBER_DATA.md`).

## Sitasi

Lihat `CITATION.cff`. DOI Zenodo akan ditambahkan setelah rilis pertama dibuat.
