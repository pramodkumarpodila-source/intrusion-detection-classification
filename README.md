# 🛡️ Network Intrusion Detection Classification

**DS 301 — Midterm Project**  
Reproducing and extending the research paper:  
> *"Enhanced Intrusion Detection Systems Performance with UNSW-NB15 Data Analysis"*  
> MDPI Algorithms, Vol. 17(2), Article 64 — February 2024

---

## 👥 Team
- Sai Pramod Kumar Podila
- Geny
- Majd

---

## 📁 Repository Structure
```
intrusion-detection-classification/
│
├── data/                        ← CSV files (not pushed — too large)
│   ├── UNSW_NB15_training-set.csv
│   └── UNSW_NB15_testing-set.csv
│
├── models/
│   ├── intrusion_detection.ipynb   ← Main notebook
│   ├── target_distribution.png
│   ├── feature_correlation.png
│   ├── dt_depth_curve.png
│   ├── dt_confusion_matrix.png
│   ├── knn_elbow_curve.png
│   ├── knn_confusion_matrix.png
│   ├── lr_confusion_matrix.png
│   ├── rf_confusion_matrix.png
│   ├── model_comparison.png
│   └── cross_validation.png
│
├── .gitignore
└── README.md
```

---

## 📦 Dataset

**UNSW-NB15** — Created by the Australian Centre for Cyber Security (ACCS)  
🔗 Kaggle: [mrwellsdavid/unsw-nb15](https://www.kaggle.com/datasets/mrwellsdavid/unsw-nb15)

| Property | Value |
|----------|-------|
| Total Records | 257,673 |
| Features | 43 |
| Target | `label` (0 = Normal, 1 = Attack) |
| Class Split | 36.1% Normal / 63.9% Attack |
| Missing Values | 0 |
| Attack Types | 9 (Fuzzers, DoS, Exploits, Generic, Backdoors, Analysis, Recon, Shellcode, Worms) |

> ⚠️ Class imbalance is intentional — dataset was lab-generated using the IXIA PerfectStorm tool. Stratified splitting applied.

---

## ⚙️ Preprocessing Pipeline

1. **Drop columns** — removed `id` and `attack_cat` (leaks attack type)
2. **Label Encoding** — `proto` (133), `service` (13), `state` (11)
3. **Outlier Check** — 16 columns had >10% outliers (real attack behavior, not removed)
4. **Train/Test Split** — 80/20 and 70/30 with `stratify=y`
5. **RobustScaler** — used instead of StandardScaler due to outliers (applied to LR and KNN only)

---

## 🤖 Models

| Model | Type | Data | Source |
|-------|------|------|--------|
| Logistic Regression | Linear | Scaled | From Paper |
| Decision Tree | Tree | Unscaled | From Paper |
| K-Nearest Neighbors | Distance | Scaled | Extended |
| Random Forest | Ensemble | Unscaled | Extended |

---

## 📊 Results

### Accuracy Comparison

| Model | 80/20 | 70/30 | CV Mean (5-Fold) | CV Std Dev |
|-------|-------|-------|------------------|------------|
| Logistic Regression | 82.97% | 82.67% | 81.72% | 0.1039 |
| KNN | 92.81% | 92.68% | 89.77% | 0.0582 |
| Decision Tree | 94.07% | 94.16% | 90.86% | 0.0564 |
| **Random Forest** | **95.18%** | **95.03%** | **91.40%** | 0.0596 |

🏆 **Best Model: Random Forest** — confirmed across all evaluation methods

### Model Ranking
```
RF > DT > KNN > LR
```

---

## 🔁 Cross Validation

5-Fold Cross Validation performed on all 4 models using best hyperparameters:
- **LR**: Mean 81.72%, Std 0.1039 — high variance, struggles with class imbalance
- **KNN**: Mean 89.77%, Std 0.0582
- **DT**: Mean 90.86%, Std 0.0564
- **RF**: Mean 91.40%, Std 0.0596 — most stable and accurate

---

## 💡 Suggested Improvements

- **SMOTE** — address 63.9%/36.1% class imbalance
- **SVM with RBF kernel** — better non-linear boundaries
- **RF Feature Importance** — reduce 43 features to top 15-20
- **Hyperparameter Tuning** — GridSearchCV for all models
- **Flask API Deployment** — deploy RF as real-time IDS service

---

## 🛠️ Requirements
```bash
pip install pandas numpy matplotlib seaborn scikit-learn
```

> Download the dataset from Kaggle and place CSVs in the `data/` folder before running the notebook.