# bfsi-churn-risk-scoring
End-to-end ML project : Customer Churn Prediction &amp; Risk Scoring Engine for Retail Brokerage (BFSI Domain)

# BFSI Churn & Risk Scoring Engine
### End-to-End Machine Learning Project | Retail Brokerage Domain

---

## Project Summary

A brokerage wants to solve two critical business problems:
1. **Predict which customers will stop trading (churn)** in the next 30 days
2. **Flag high-risk trading behavior** for compliance and surveillance
3. **Generate AI-powered retention strategies** using a GenAI decision layer

This project simulates a production-grade ML pipeline as it would exist 
at an Indian retail brokerage — from raw data ingestion through model 
deployment to GenAI-powered decision support.

---

## Business Context

| Metric | Value | Source |
|---|---|---|
| Simulated customer base | 50,000 customers | Mid-sized brokerage cohort |
| Trade records | ~2.3 million | 18 months OMS data |
| Churn rate | ~18% | AMFI dormancy benchmarks |
| Domain | Retail Brokerage / Securities | NSE-listed instruments |
| Regulatory context | SEBI compliance | KYC, surveillance requirements |

---

## Architecture
Data Sources (Simulated)        ML Pipeline                  Output
─────────────────────────       ───────────────              ──────
CRM → customers.csv        →    EDA & Feature Eng.   →    Churn Score (0-1)
OMS → trades.csv           →    Churn Model (RF)     →    Risk Score (0-100)
Back-office → monthly.csv  →    Anomaly Detection    →    GenAI Retention Plan
GenAI Layer (LLM)

## Project Structure
bfsi-churn-risk-scoring/
├── notebooks/
│   ├── 01_data_generation.ipynb    # Synthetic data — 3-table PostgreSQL schema
│   ├── 02_eda.ipynb                # EDA, feature engineering, correlation analysis
│   ├── 03_churn_model.ipynb        # Classification model, SMOTE, ROC-AUC
│   ├── 04_risk_scoring.ipynb       # Risk scoring + anomaly detection
│   └── 05_genai_layer.ipynb        # LLM-powered retention strategy generator
├── data/
│   └── README.md                   # Schema docs (data regenerated from notebook)
├── outputs/                        # Saved plots and model artifacts
├── DATA_RATIONALE.md               # Full methodology and source documentation
└── README.md

---

## Tech Stack

| Layer | Tools |
|---|---|
| Data Processing | Python, Pandas, NumPy |
| Machine Learning | Scikit-learn, Imbalanced-learn (SMOTE) |
| Visualization | Matplotlib, Seaborn |
| Anomaly Detection | Isolation Forest |
| GenAI Layer | LangChain, Groq API, RAGAS |
| Environment | Google Colab |
| Database Simulated | PostgreSQL (3-table warehouse schema) |

---

## Key Results

| Model | Metric | Score |
|---|---|---|
| Churn Classifier (Random Forest) | ROC-AUC | TBD |
| Churn Classifier | Precision | TBD |
| Churn Classifier | Recall | TBD |
| Anomaly Detection | Isolation Forest contamination | 5% |

*Results will be updated after each notebook is completed*

---

## ML Features Engineered

| Feature | Business Logic |
|---|---|
| `login_to_trade_ratio` | High logins + low trades = disengagement signal |
| `pnl_ratio` | Loss-making customers churn faster |
| `trade_consistency` | Skipping months = early churn warning |
| `active_months` | Longer engagement = lower churn risk |
| `avg_unique_stocks` | Diversification = stickier customer |
| `products_used` | Cross-sold customers retain better |

---

## How To Run

1. Open any notebook in **Google Colab**
2. Run `01_data_generation.ipynb` first — generates all CSV files
3. Run notebooks in sequence: 01 → 02 → 03 → 04 → 05
4. Each notebook saves plots to the `outputs/` folder

---

## Data Methodology

Synthetic data generated with domain-grounded assumptions from 
publicly available Indian financial market regulatory documents:

- [SEBI F&O Trader P&L Study (Jan 2023)](https://www.sebi.gov.in/reports-and-statistics/research/jan-2023/study-analysis-of-profit-and-loss-of-individual-traders-dealing-in-equity-fando-segment_67525.html)
- [SEBI Updated F&O Study FY22-24 (Sep 2024)](https://www.sebi.gov.in/reports-and-statistics/research/sep-2024/study-analysis-of-profits-and-losses-in-the-equity-derivatives-segment-fy22-fy24-_86905.html)
- [SEBI Intraday Equity Study (Jul 2024)](https://www.sebi.gov.in/media-and-notifications/press-releases/jul-2024/sebi-study-finds-that-7-out-of-10-individual-intraday-traders-in-equity-cash-segment-make-losses_84948.html)
- [SEBI Investor Survey 2025](https://www.sebi.gov.in/reports-and-statistics/research/jan-2026/investor-survey-2025-_99170.html)
- [SEBI Annual Report 2022-23](https://www.sebi.gov.in/reports-and-statistics/publications/aug-2023/annual-report-2022-23_74990.html)

Full methodology documented in [DATA_RATIONALE.md](./DATA_RATIONALE.md)

---

## Business Impact



---

## Author

**Aakanksha Kadam**
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue)](https://www.linkedin.com/in/aakanksha-kadam/)

