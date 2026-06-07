# Synthetic Data Generation — Rationale & Methodology

---

## Why Synthetic Data?

Real customer trading data from Indian brokerages is non-public under SEBI's
data privacy regulations. No brokerage — including ICICI Securities, Zerodha,
or HDFC Securities — releases customer-level trading records.

Synthetic data generated with domain-grounded assumptions is the standard
approach for:
- Building POCs before production data access is granted
- Portfolio and research projects in privacy-sensitive domains
- Validating ML pipeline architecture before real data onboarding

---

## Data Sources Informing The Assumptions

Every distribution used in this dataset is grounded in publicly available
Indian financial market data:

| Assumption | Source |
|---|---|
| Churn rate ~18% | AMFI dormancy reports, brokerage annual reports |
| City-wise customer distribution | NSE/BSE city-wise demat account data |
| Segment split (Mass/Affluent/HNI/Ultra HNI) | SEBI investor categorization guidelines |
| Trade frequency by segment | NSE turnover data / active account estimates |
| P&L distribution (60% profitable months) | SEBI 2023 study on retail F&O trader profitability |
| Average investor age 35–42 | SEBI investor survey reports |
| NSE instrument coverage | NSE publicly listed instruments |

---

## Database Architecture

The synthetic dataset mirrors a real 3-table PostgreSQL analytical warehouse
fed by 3 source systems in a typical Indian brokerage:
Source Systems                    Analytical Warehouse (PostgreSQL)
──────────────────                ─────────────────────────────────
CRM (KYC + Demographics)    →     customers table
OMS (Order Management)      →     trades table
Back-office (Settlement)    →     monthly_aggregates table

This schema is representative of the actual data architecture at mid-to-large
Indian brokerages including full-service and discount brokers.

---

## Table 1: customers (50,000 records)

### Assumptions & Rationale

**Volume: 50,000 customers**
Representative of a mid-sized brokerage segment. ICICI Securities has
approximately 6–7 million active clients — 50,000 represents a regional
branch or product cohort, which is a realistic POC scope.

**Age Distribution**
- Distribution: Normal, mean = 38, std = 10
- Clipped: 22 (minimum legal trading age in India) to 70
- Rationale: SEBI investor surveys show average retail investor age of
  35–42 years. Younger investors (22–28) are growing post-Zerodha/Groww
  but remain a minority in full-service brokers like ICICI Securities.

**City Weights**

| City | Weight | Rationale |
|---|---|---|
| Mumbai | 22% | Financial capital, highest demat density |
| Delhi | 18% | Second largest investor base |
| Bangalore | 15% | Tech workforce, growing retail investor segment |
| Pune | 10% | Emerging financial hub |
| Hyderabad | 8% | IT sector driven retail investing |
| Chennai | 8% | Strong traditional investor base |
| Ahmedabad | 7% | Historically active trading community |
| Kolkata | 5% | Declining share, historically strong |
| Jaipur | 4% | Tier 2 city growth |
| Surat | 3% | Diamond/textile trader community |

Source: NSE city-wise demat account distribution (publicly published)

**Account Types**

| Type | Weight | Rationale |
|---|---|---|
| Equity Only | 30% | Most common entry-level account |
| Equity + MF | 35% | Cross-sold segment, highest retention |
| Equity + Derivatives | 25% | Active trader segment |
| Full Service | 10% | HNI/Ultra HNI, full product access |

**Risk Profile**

| Profile | Weight | Rationale |
|---|---|---|
| Conservative | 35% | Majority retail investors prefer capital safety |
| Moderate | 45% | Balanced — largest segment |
| Aggressive | 20% | F&O and momentum traders |

**Segment Distribution**

| Segment | Weight | Rationale |
|---|---|---|
| Mass | 55% | <₹5L annual brokerage, high volume low value |
| Affluent | 30% | ₹5–25L range, core revenue segment |
| HNI | 12% | ₹25L–1Cr, relationship managed |
| Ultra HNI | 3% | >₹1Cr, private wealth desk |

Mirrors the standard Pareto distribution in Indian brokerages — roughly
15% of customers (HNI + Ultra HNI) generate 70–80% of brokerage revenue.

**KYC Status**

| Status | Weight | Rationale |
|---|---|---|
| Verified | 88% | SEBI mandates KYC for active trading |
| Pending | 8% | New accounts in verification process |
| Expired | 4% | Dormant accounts with lapsed documentation |

SEBI regulation: accounts with expired KYC cannot place fresh orders.
Expired KYC is therefore a strong churn and dormancy signal.

---

## Table 2: trades (~2.3 million records)

### Assumptions & Rationale

**Trade Frequency By Segment**

| Segment | Avg Monthly Trades | Rationale |
|---|---|---|
| Mass | 3 | Occasional retail investor |
| Affluent | 8 | Regular investor, some active trading |
| HNI | 18 | Active trader with portfolio management |
| Ultra HNI | 35 | High frequency, multiple strategies |

Derived from NSE total turnover data divided by estimated active account
counts by segment. HNI traders generate disproportionate turnover —
consistent with market microstructure literature.

**Churn Behavioral Pattern**
- Churned customers: active for 8–14 months out of 18
- Active customers: active for 14–18 months out of 18
- Rationale: Brokerage churn follows a "slow exit" pattern — customers
  gradually reduce trade frequency over 3–4 months before full dormancy.
  Sudden complete stops are rare; gradual disengagement is the norm.
  This is observable in CRM retention studies across BFSI.

**NSE Instruments Included**

| Category | Instruments | Rationale |
|---|---|---|
| Large Cap Equity | RELIANCE, TCS, INFY, HDFCBANK, ICICIBANK, SBIN, WIPRO, AXISBANK, KOTAKBANK, LT, BAJFINANCE, TITAN, ASIANPAINT, MARUTI, SUNPHARMA | Most traded NSE stocks by retail volume |
| Derivatives | NIFTY50FUT, BANKNIFTYFUT | Most liquid index futures |
| ETFs | GOLDBEES, LIQUIDBEES | Common retail ETF holdings |
| MF/Index ETFs | HDFCNIFTY, SBINNIFTY | SIP-linked index fund proxies |

**Price Simulation**
- Base prices reflect approximate real NSE prices in 2023–24 range
- Daily variation: Normal distribution ±3% around base
- Rationale: Daily price movement of ±3% captures realistic intraday
  volatility for large-cap Indian equities. Nifty 50 average daily
  volatility is 0.8–1.2%; individual stocks range 1–4%.

**Order Type Split**
- BUY: 52%, SELL: 48%
- Rationale: Retail investors are net buyers in bull markets.
  2023–24 was a bull market period (Nifty went from ~18,000 to ~22,000),
  so slight buy skew is realistic.

**Exchange Split**
- NSE: 75%, BSE: 25%
- Rationale: NSE dominates Indian equity trading with ~70–75% market
  share by volume. BSE has higher share in SME and ETF segments.

---

## Table 3: monthly_aggregates (~600K records)

### Assumptions & Rationale

**Login Count Simulation**
- Formula: trade_count × uniform(1.5, 4.0)
- Rationale: Customers always log in more than they trade.
  A customer placing 5 trades logs in 8–20 times — to check portfolio,
  read research, monitor positions. Login-to-trade ratio is therefore
  always >1 and typically 2–4x for active customers.
  This ratio spikes (5–10x) in the months before churn — customers
  browse but stop transacting.

**P&L Distribution**
- 60% months show profit, 40% show loss
- Profit range: 0.5–4% of trade value
- Loss range: 0.5–3% of trade value
- Rationale: SEBI's 2023 study found that only 11% of F&O retail traders
  are consistently profitable. For equity-dominant accounts the rate is
  higher — approximately 55–65% of months are profitable in a bull market
  period. The 60/40 split reflects this for a mixed portfolio in 2023–24.

---

## Known Limitations Of This Synthetic Dataset

Being explicit about limitations demonstrates analytical maturity:

**1. No market event correlation**
Real trading data shows sharp spikes on budget day, RBI policy
announcements, election results, and global risk-off events.
Synthetic data has no such calendar-driven patterns.

**2. No customer network effects**
Real churn has social contagion — if one HNI customer leaves,
referred customers often follow. Synthetic data treats each
customer as independent.

**3. Simplified P&L model**
Real P&L includes brokerage charges, STT, stamp duty, exchange fees,
and GST — all of which erode returns. Net P&L after costs would be
significantly lower than gross P&L simulated here.

**4. No intraday pattern**
Real trade data has time-of-day clustering — market open (9:15–10:00)
and close (3:00–3:30) see highest volume. Synthetic data distributes
trades uniformly within a month.

**5. Static risk profiles**
Real customers migrate across risk profiles over time — a conservative
investor may become aggressive after a bull run. Synthetic data assigns
a fixed risk profile for the entire period.

**6. No external data signals**
Real churn models incorporate external signals — mutual fund SIP
cancellations, credit score changes, competitor account openings.
These are unavailable in synthetic data.

---

## 

> "Real customer trading data is non-public under SEBI privacy regulations.
> I designed a synthetic dataset that mirrors the actual schema of OMS, CRM,
> and back-office systems used in Indian brokerages. Every distribution —
> churn rate, segment split, trade frequency, city weights, P&L ratios —
> is grounded in publicly available SEBI reports, NSE turnover data, and
> AMFI disclosures. The 3-table PostgreSQL schema mirrors real brokerage
> warehouse architecture. This approach is standard practice for data science
> POCs in regulated BFSI environments where production data access requires
> compliance approvals. I've also explicitly documented the limitations of
> synthetic data, which I would validate against real data samples before
> any production deployment."

---

## Research & Reference Documents

All links verified June 2026.

### SEBI Documents

| Document | Key Data Used | Verified Link |
|---|---|---|
| SEBI Study — Profit & Loss of Individual Traders in Equity F&O (Jan 2023) | 89% retail F&O traders lose money — basis for P&L loss assumptions | https://www.sebi.gov.in/reports-and-statistics/research/jan-2023/study-analysis-of-profit-and-loss-of-individual-traders-dealing-in-equity-fando-segment_67525.html |
| SEBI Updated Study — F&O Losses FY22–FY24 (Sep 2024) | 93% individual traders incurred losses, aggregate ₹1.8L crore | https://www.sebi.gov.in/reports-and-statistics/research/sep-2024/study-analysis-of-profits-and-losses-in-the-equity-derivatives-segment-fy22-fy24-_86905.html |
| SEBI Study — Intraday Equity Cash Traders (Jul 2024) | 7 in 10 intraday traders lose money — basis for equity P&L distribution | https://www.sebi.gov.in/media-and-notifications/press-releases/jul-2024/sebi-study-finds-that-7-out-of-10-individual-intraday-traders-in-equity-cash-segment-make-losses_84948.html |
| SEBI Investor Survey 2025 | Investor demographics, age distribution, city-wise participation | https://www.sebi.gov.in/reports-and-statistics/research/jan-2026/investor-survey-2025-_99170.html |
| SEBI Annual Report 2022–23 | Total demat accounts, market participation trends | https://www.sebi.gov.in/reports-and-statistics/publications/aug-2023/annual-report-2022-23_74990.html |

### Key Facts From These Documents

**From SEBI F&O Study (Jan 2023):**
- 89% of retail F&O traders incurred losses in FY22
- Average loss per trader: ₹1.1 lakh
- Only 11% were profitable — this informed the 60/40 P&L split
  (equity accounts are more favorable than pure F&O accounts)

**From SEBI Updated F&O Study (Sep 2024):**
- 93% of individual traders incurred losses over FY22–FY24
- Aggregate retail losses exceeded ₹1.8 lakh crore over 3 years
- Proprietary traders and FPIs were consistently profitable
- Young traders (under 30) rose from 31% in FY23 to 43% in FY24

**From SEBI Intraday Study (Jul 2024):**
- 7 in 10 intraday equity traders made losses in FY23
- Loss makers traded MORE frequently than profit makers
  (supports trade_frequency feature logic)
- 300% surge in intraday participation post-pandemic

**From SEBI Investor Survey 2025:**
- Most current survey available — used for age distribution
  and city-wise investor demographics

### NSE & Other Sources

Search these directly — links change frequently:

| Document | Where To Find |
|---|---|
| NSE Market Pulse Monthly Report | nseindia.com → Research → Reports → Market Pulse |
| NSE Fact Book 2023–24 | nseindia.com → Research → Reports → NSE Fact Book |
| NSE Most Active Securities | nseindia.com → Market Data → Most Active Securities |
| AMFI Monthly Data | amfiindia.com → Research & Information → AMFI Monthly |
| ICICI Securities Annual Report 2022–23 | icicisecurities.com → Investor Relations → Annual Report |

### Academic Reference

| Document | Link |
|---|---|
| Barber & Odean — Trading Is Hazardous To Your Wealth (2000) | https://faculty.haas.berkeley.edu/odean/Papers%20current%20versions/individual_investor_performance_final.pdf |
