# 🛡️ IEEE-CIS-Fraud-Detection-Analysis

Pipeline Machine Learning untuk mendeteksi transaksi penipuan. Dibangun dengan LightGBM dan NetworkX untuk menangani data tidak seimbang (*imbalance*) serta mencari titik optimal antara mencegah kerugian dan menjaga kenyamanan nasabah. 

Proyek ini mengimplementasikan *end-to-end machine learning pipeline* untuk mendeteksi transaksi penipuan (*fraud*) pada jaringan pembayaran e-commerce tingkat global. Alih-alih hanya berfokus pada akurasi model, sistem ini didesain untuk mengoptimalkan *trade-off* antara pencegahan kerugian finansial (*Loss Prevention*) dan meminimalkan gesekan pada pelanggan asli (*Customer Friction*).

Berdasarkan hasil evaluasi pada data simulasi masa depan (*Time-Based Splitting*):
* **🛡️ Loss Prevention Focus (Threshold 0.5):** Model mampu menangkap **72% - 80%** total transaksi penipuan, memitigasi mayoritas ancaman *Account Takeover* (ATO) sebelum *settlement*.
* **🚦 Friction Optimization (Threshold 0.7):** Dengan melakukan *threshold tuning*, *Precision* berhasil ditingkatkan menjadi **38%** (hampir 2x lipat dari *baseline* 21%). Artinya, beban *Customer Service* akibat komplain salah blokir (*False Positives*) berkurang drastis, menjaga *Customer Trust*.
* **⚙️ Network & Velocity Indicators:** Ekstraksi fitur berbasis *Graph Theory* (*Network Centrality*) dan kecepatan transaksi (*Velocity*) terbukti mengalahkan ratusan fitur bawaan dataset, membuktikan bahwa penipu bekerja dalam sindikat/jaringan.

---

## ⚖️ Dataset & Legal Disclaimer (IEEE-CIS Kaggle Rules)

Dataset yang digunakan dalam proyek ini bersumber dari kompetisi [IEEE-CIS Fraud Detection (Kaggle, 2019)](https://www.kaggle.com/c/ieee-fraud-detection).

**Klarifikasi Aturan Kompetisi:**
* **Tujuan Penggunaan:** Proyek ini dibuat murni untuk tujuan portofolio, edukasi, dan riset akademik (*skill-based showcase*). Tidak ada komersialisasi dari model maupun data yang digunakan.
* **Lisensi Open-Source:** Sesuai dengan aturan *"Winner License Type: Open-Source"*, seluruh kode (*source code*) dalam repositori ini dibagikan secara publik di bawah lisensi *Open Source* untuk memajukan komunitas Data Science.
* **Kepemilikan Data:** Seluruh data transaksi merupakan hak milik *Vesta Corporation* dan *IEEE Computational Intelligence Society*.

---

## 🔬 Technical Highlights (Why this is "Production-Ready")

Proyek ini menghindari teknik *modelling* dasar dan menerapkan standar *Senior/Quant Data Science*:

### 1. Robust Validation (Time-Based Splitting) ⏳
Menghindari "dosa besar" *Data Leakage* akibat *Random Split*. Karena pola fraud berubah seiring waktu (*Concept Drift*), data dibagi berdasarkan urutan kronologis (`TransactionDT`). Model dilatih menggunakan 70% data masa lalu untuk memprediksi 30% data di masa depan secara "buta".

### 2. Advanced Feature Engineering 🧠
* **Velocity / Time-Decay:** Menghitung frekuensi harian (`card1_daily_count`) dan selisih waktu antar transaksi (`time_since_last_txn_card`) dari identitas yang sama.
* **Bipartite Graph Network:** Menggunakan `NetworkX` untuk memetakan hubungan antara Kartu (`card1`) dan Perangkat (`DeviceInfo`). Fitur `Degree Centrality` mendeteksi *fraud rings* di mana satu perangkat digunakan oleh banyak kartu bajakan.

### 3. Native Categorical Handling (LightGBM) 🌳
Menghentikan pembengkakan memori akibat *One-Hot Encoding* (OHE). Model **LightGBM** digunakan untuk menelan langsung fitur kategorikal ber-kardinalitas tinggi (seperti `P_emaildomain` dan `DeviceInfo`), menjadikan domain email sebagai prediktor terkuat (Top 2 Feature).

### 4. Honest Evaluation Metrics (PR-AUC) 📉
Dataset memiliki tingkat *imbalance* yang ekstrem (< 3% Fraud). Penggunaan ROC-AUC dihindari karena bersifat manipulatif (menghasilkan skor palsu > 0.90). Evaluasi difokuskan pada **Precision-Recall Curve (PR-AUC)** untuk merepresentasikan performa model di dunia nyata secara jujur.

---

## 📊 Feature Importance

Dari 430+ fitur yang diproses, fitur hasil *engineering* berhasil mendominasi daftar prediktor paling penting:

1. **`P_emaildomain`** *(Categorical Native)* - Sinyal kuat dari *throwaway email*.
2. **`card1` & `card2`** - Identitas instrumen pembayaran.
3. **`time_since_last_txn_card`** *(Engineered)* - Kecepatan transaksi.
4. **`Transaction_Hour`** *(Engineered)* - Musiman waktu transaksi tak wajar.
5. **`card1_degree`** *(Engineered)* - Deteksi jaringan/sindikat (Graph).

---

## 🚀 How to Run the Pipeline

**1. Clone Repository:**
```bash
git clone [https://github.com/](https://github.com/)[username-kamu]/ieee-fraud-detection.git
cd ieee-fraud-detection
