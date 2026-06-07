# Data

## Why No CSV Files Here?

Raw data files are not committed to this repository for two reasons:

1. **Size** — the trades table contains ~2.3 million records (~150MB) 
   which exceeds GitHub's recommended file size limits
2. **Reproducibility** — all data is fully synthetic and regeneratable 
   from `notebooks/01_data_generation.ipynb` in under 2 minutes

## How To Regenerate Data

1. Open `notebooks/01_data_generation.ipynb` in Google Colab
2. Run Cell 1 — all 3 CSV files will be generated automatically
3. Files generated:
   - `customers.csv` — 50,000 customer records
   - `trades.csv` — ~2.3M trade records
   - `monthly_aggregates.csv` — ~600K monthly behavioral records

## Schema

| Table | Source System Simulated | Records |
|---|---|---|
| customers | CRM (KYC + Demographics) | 50,000 |
| trades | OMS (Order Management System) | ~2.3M |
| monthly_aggregates | Back-office + Derived | ~600K |
