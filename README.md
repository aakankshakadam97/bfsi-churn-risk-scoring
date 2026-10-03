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
│   ├── 01_data_generation_and_EDA.ipynb   # Synthetic data — 3-table PostgreSQL schema, EDA, feature engineering, correlation analysis
│   ├── 02_churn_model.ipynb       # Classification model, SMOTE, ROC-AUC
│   ├── 03_Risk_scoring.ipynb      # Risk scoring + anomaly detection
│   └── 04_genai_layer.ipynb       # LLM-powered retention strategy generator
├── data/
│   └── README.md                  # Schema docs (data regenerated from notebook)
├── outputs/
│   ├── plots/
│   │   ├── eda/                   # EDA visualizations
│   │   └── models/                # Model performance plots
│   └── genai_layer/               # Retention strategies + executive summary
├── DATA_RATIONALE.md              # Full methodology and source documentation
└── README.md

---

## Tech Stack

| Layer | Tools |
|---|---|
| Data Processing | Python, Pandas, NumPy |
| Machine Learning | Scikit-learn, Imbalanced-learn (SMOTE) |
| Visualization | Matplotlib, Seaborn |
| Anomaly Detection | Isolation Forest |
| GenAI Layer | Groq API (`openai/gpt-oss-120b`) |
| Environment | Google Colab |
| Database Simulated | PostgreSQL (3-table warehouse schema) |

---

## Key Results

### Churn Prediction Model

| Metric | Logistic Regression | Random Forest (tuned) |
|---|---|---|
| ROC-AUC | 0.9824 | 0.9813 |
| Precision | — | 0.8974 |
| Recall | — | 0.8621 |
| F1 Score | — | 0.8794 |
Random Forest was selected as the production model for its balance of precision and recall on the minority (churn) class after SMOTE balancing, despite a marginally lower ROC-AUC than Logistic Regression.
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

### Risk & Anomaly Detection

| Metric | Value |
|---|---|
| Isolation Forest contamination | 5% |
| Risk tiers | Low / Medium / High / Critical |
| Customer segments | Mass / Affluent / HNI / Ultra HNI |

---

## How To Run

1. Open any notebook in **Google Colab**
2. Run `01_data_generation.ipynb` first — generates all CSV files
3. Run notebooks in sequence: 01 → 02 → 03 → 04 → 05
4. Each notebook saves plots to the `outputs/` folder

## GenAI Action Layer (Notebook 05)

The final stage converts quantitative risk scores into human-readable, actionable outputs:

- **Retention strategies**: generated via Groq API (`openai/gpt-oss-120b`) for 6 representative customers — one per `recommended_action` category — grounded strictly in fields present in `risk_scores_final.csv` (no fabricated customer detail).
- **Executive summary**: a leadership-facing briefing where all quantitative claims (churn %, trade value at risk, segment distribution) are computed deterministically in pandas and passed to the LLM as ground truth; the model handles narrative synthesis and prioritization, not calculation.

**Outputs**: `outputs/genai_layer/retention_strategies.json`, `retention_strategies.txt`, `executive_summary.txt`

**Design note**: During development, the Groq `llama3-*` model family was deprecated from the free/developer tier mid-project — the notebook now includes a live model-availability check against `client.models.list()` to avoid silent failures on future deprecations. Separately, a rule-priority gap was identified and documented: anomaly detection currently overrides segment/tier when assigning `recommended_action`, so no Ultra HNI Critical customer can receive the "Dedicated RM call within 24 hours" action if they also trip the anomaly flag — flagged as a candidate fix for notebook 04 rather than silently worked around.

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

Based on risk scoring across [10,000 / 50,000] customers:

| Risk Category | Customers | % of Base | Trade Value Exposed |
|---|---|---|---|
| Critical tier | [critical_count] | [X]% | ₹[critical_trade_value] |
| High tier | [high_count] | [X]% | — |
| Flagged anomalous trading | [anomaly_count] | [X]% | — |

**Segment concentration**: [X]% of Critical-tier customers fall in the HNI/Ultra HNI segments, representing disproportionate trade-value risk relative to their share of the customer base — these customers warrant the highest-touch retention response (dedicated RM outreach within 24–48 hours).

**Operational translation**: the risk scoring + GenAI layer converts a [10,000 / 50,000]-customer base into a prioritized action queue — [815] customers routed to automated campaigns, [457] to personalized RM email outreach, [167] to urgent RM calls, and [360] flagged for compliance/algo-trading review — rather than leaving relationship teams to manually triage the full customer base.

**Caveat**: this is a synthetic dataset built for methodology demonstration; the figures above illustrate the *shape* of risk concentration a real brokerage might expect to find, not validated production numbers.


---

## Author

**Aakanksha Kadam**
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue)](https://www.linkedin.com/in/aakanksha-kadam/)

