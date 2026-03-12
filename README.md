# 🏦 Credit Risk Assessment & Loan Default Prediction System

![Python](https://img.shields.io/badge/Python-3.10-blue?logo=python)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-15-336791?logo=postgresql)
![XGBoost](https://img.shields.io/badge/XGBoost-ML-orange)
![Power BI](https://img.shields.io/badge/Power%20BI-Dashboard-yellow?logo=powerbi)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen)

---

## 📌 Business Problem

A non-banking financial company (NBFC) processes thousands of loan applications monthly. Without a reliable early-warning system, high-risk applicants are approved alongside low-risk ones — leading to significant default losses and portfolio degradation.

**This project builds an end-to-end credit risk intelligence platform** that:
- Identifies high-risk applicants before loan approval using machine learning
- Quantifies portfolio-level risk using SQL analytics
- Delivers actionable insights to business stakeholders via a Power BI dashboard

---

## 📊 Dataset

| Attribute | Detail |
|-----------|--------|
| Source | [Home Credit Default Risk — Kaggle](https://www.kaggle.com/c/home-credit-default-risk) |
| Total Records | 307,511 loan applications (50,000 sampled) |
| Features | 122 columns |
| Target Variable | `TARGET` — 1 = Defaulted, 0 = Repaid |
| Data Type | Real anonymised data from Home Credit Group (active in 9 countries) |

> ⚠️ Raw data is not included in this repository. Download `application_train.csv` from the Kaggle link above.

---

## 🛠️ Tools & Technologies

| Layer | Tools |
|-------|-------|
| Data Storage | PostgreSQL 15, pgAdmin 4 |
| Data Analysis | Python, Pandas, NumPy, SQLAlchemy |
| Machine Learning | XGBoost, Scikit-learn, SHAP |
| Visualisation | Matplotlib, Seaborn |
| Dashboard | Microsoft Power BI Desktop |
| Version Control | Git, GitHub |

---

## 📁 Project Structure

```
credit-risk-loan-default/
├── data/
│   └── load_data.py              # Load CSV → PostgreSQL
├── notebooks/
│   ├── 01_eda.ipynb              # Exploratory Data Analysis
│   └── 02_model.ipynb            # XGBoost + SHAP Explainability
├── sql/
│   └── analysis_queries.sql      # 7 business SQL queries
├── reports/
│   ├── 01_class_distribution.png
│   ├── 02_missing_values.png
│   ├── 03_credit_scores_vs_default.png
│   ├── 04_default_by_age.png
│   ├── 05_emi_burden_vs_default.png
│   ├── 06_shap_summary.png
│   └── 07_feature_importance.png
├── docs/
│   └── BRD_Credit_Risk_Platform.md
├── .gitignore
└── README.md
```

---

## 🔍 SQL Analysis Highlights

7 business queries were written and executed against a PostgreSQL database:

```sql
-- EMI Burden Bands vs Default Rate
SELECT
    CASE
        WHEN AMT_ANNUITY/NULLIF(AMT_INCOME_TOTAL,0) < 0.10 THEN 'Low (<10%)'
        WHEN AMT_ANNUITY/NULLIF(AMT_INCOME_TOTAL,0) < 0.30 THEN 'Medium (10-30%)'
        WHEN AMT_ANNUITY/NULLIF(AMT_INCOME_TOTAL,0) < 0.50 THEN 'High (30-50%)'
        ELSE 'Very High (>50%)'
    END AS emi_burden_band,
    COUNT(*) AS applicants,
    ROUND(AVG("TARGET")::numeric * 100, 2) AS default_rate_pct
FROM loan_applications
GROUP BY emi_burden_band
ORDER BY emi_burden_band;
```

See full query set → [`sql/analysis_queries.sql`](sql/analysis_queries.sql)

---

## 📈 Key Business Findings

| Finding | Insight |
|---------|---------|
| **Default Rate** | ~9.8% of applicants defaulted in this dataset |
| **Top Risk Feature** | External credit scores (`EXT_SOURCE_2`, `EXT_SOURCE_3`) |
| **EMI Burden** | Applicants with EMI > 50% of income default at 3× the rate of low-burden group |
| **Age Group** | Youngest applicants (20s) show highest default risk |
| **Credit Score** | Very Low score band has 4× higher default rate than High score band |
| **Contract Type** | Revolving loans carry higher default risk than cash loans |

---

## 🤖 Machine Learning Model

### Model: XGBoost Classifier

| Parameter | Value |
|-----------|-------|
| n_estimators | 300 |
| max_depth | 6 |
| learning_rate | 0.05 |
| Class Imbalance | `scale_pos_weight` (auto-calculated) |
| Train/Test Split | 80% / 20% stratified |

### Model Performance

| Metric | Score |
|--------|-------|
| **AUC-ROC** | *(fill in your score)* |
| **Recall (Defaulters)** | *(fill in your score)* |
| **Precision** | *(fill in your score)* |
| **F1 Score** | *(fill in your score)* |

> Update this table with your actual scores after running `notebooks/02_model.ipynb`

---

## 🔬 SHAP Explainability

SHAP (SHapley Additive exPlanations) was used to explain the model's predictions — making it interpretable for business stakeholders.

**Top 3 features by SHAP importance:**
1. `EXT_SOURCE_2` — External credit score (most important)
2. `EXT_SOURCE_3` — Second external credit score
3. `DAYS_BIRTH` — Age of applicant (negative = older = lower risk)

> SHAP summary plot: [`reports/06_shap_summary.png`](reports/06_shap_summary.png)

---

## 📊 Power BI Dashboard

4-page interactive dashboard connected live to PostgreSQL via DirectQuery:

| Page | Content |
|------|---------|
| **Executive Summary** | Portfolio KPIs, total applicants, default rate, avg loan |
| **Risk Segmentation** | Default rate by education, EMI band, age, contract type |
| **Loan Intelligence** | Income vs loan scatter, credit score bands, distribution |
| **Model Insights** | SHAP chart, risk tier distribution, top high-risk applicants |

**Row-Level Security (RLS):** Analyst role filters to specific contract types — simulating enterprise access control.

---

## ▶️ How to Run

### 1. Prerequisites
```bash
pip install pandas numpy scikit-learn xgboost shap matplotlib seaborn plotly sqlalchemy psycopg2-binary openpyxl joblib
```

### 2. Database Setup
- Open pgAdmin → Create database: `credit_risk_db`

### 3. Load Data
```bash
# Edit YOUR_PASSWORD in the script first
python data/load_data.py
```

### 4. Run Notebooks (in order)
```
notebooks/01_eda.ipynb       → EDA + 5 charts
notebooks/02_model.ipynb     → XGBoost + SHAP
```

### 5. Open Dashboard
- Open `powerbi/credit_risk_dashboard.pbix` in Power BI Desktop
- Update the PostgreSQL connection with your local credentials

---

## 💬 Interview Talking Points

**Q: Why XGBoost?**
> XGBoost handles class imbalance well via `scale_pos_weight`, performs well on tabular data, and provides feature importance natively. It also supports early stopping to prevent overfitting.

**Q: What does SHAP show?**
> SHAP assigns each feature a contribution value for each individual prediction. For example, a low `EXT_SOURCE_2` score pushes the default probability *up*, while a higher age (negative `DAYS_BIRTH`) pushes it *down*. This makes the model explainable to a credit manager.

**Q: How did you handle class imbalance?**
> Used stratified train/test split (80/20) and set `scale_pos_weight = count(non-default)/count(default)` in XGBoost, which automatically weights the minority class (defaulters) higher during training.

---

## 👤 Author

**Ajaya Kumar Pradhan**
- 📧 ajayapradhan210@gmail.com
- 💼 [LinkedIn](https://linkedin.com/in/ajaya-pradhan-1945341b0)
- 🐙 [GitHub](https://github.com/Ajaya210)

---

*Part of a 3-project Data Analyst portfolio. See also: [Retail Analytics](https://github.com/Ajaya210/retail-sales-customer-analytics) | [Supply Chain Analytics](https://github.com/Ajaya210/supply-chain-vendor-performance)*
