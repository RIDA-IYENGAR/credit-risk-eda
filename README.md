# Credit Risk EDA — Home Credit Default Analysis

![Python](https://img.shields.io/badge/Python-3.8+-blue?logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-1.x-150458?logo=pandas)
![Seaborn](https://img.shields.io/badge/Seaborn-Visualization-4C72B0)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange?logo=jupyter)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen)

## What This Project Is About

Every year, banks reject millions of loan applicants — not because they can't repay, but because they lack enough credit history. At the same time, some applicants who *do* get approved end up defaulting. The real challenge is telling these two groups apart before any money changes hands.

This project digs into the [Home Credit Default Risk dataset](https://www.kaggle.com/c/home-credit-default-risk/data) to answer one question: **what does a loan defaulter actually look like in the data?** Through systematic EDA across two datasets and 100+ features, this analysis surfaces the demographic patterns, financial signals, and behavioural cues that separate on-time payers from defaulters.

---

## Dataset

| File | Rows | Description |
|------|------|-------------|
| `application_data.csv` | ~307,000 | Current loan applications — demographics, financials, credit history |
| `previous_application.csv` | ~1.67M | Historical loan records — past application outcomes per client |

**Target variable:** `TARGET` — `0` (repaid on time) · `1` (defaulted)  
**Source:** [Kaggle — Home Credit Default Risk](https://www.kaggle.com/c/home-credit-default-risk/data)

---

## Analysis Walkthrough

### 1. Data Quality & Cleaning
- Dropped columns with **>47% missing values** to avoid noise in downstream analysis
- Applied strategy-specific imputation:
  - `OCCUPATION_TYPE` (31% missing, categorical) → filled with `"Others"`
  - `EXT_SOURCE_3` (19% missing, numerical, no outliers) → filled with **median**
  - `AMT_REQ_CREDIT_BUREAU_*` columns (99% zeros) → filled with **mode**
  - Remaining low-null categorical cols → **mode**; numerical cols → **median**
- Converted all `DAYS_*` columns (stored as negatives) to absolute values, then to years for interpretability

### 2. Feature Engineering
- Binned `AMT_CREDIT` into 5 risk tiers: `Very Low → Very High Credit`
- Created `AGE_Category` buckets: `<25 / 25–45 / 45–65 / 65–85`
- Derived `YEARS_BIRTH`, `YEARS_EMPLOYED`, `YEARS_REGISTRATION`, `YEARS_ID_PUBLISH`, `YEARS_LAST_PHONE_CHANGE` from day-based columns

### 3. Univariate Analysis
- Plotted all categorical and numerical columns — separately for `TARGET=0` and `TARGET=1`
- Identified outliers in `AMT_INCOME_TOTAL`, `AMT_CREDIT`, `AMT_ANNUITY`, `AMT_GOODS_PRICE` via boxplots and percentile comparison
- Class imbalance confirmed: ~91% non-defaulters vs ~9% defaulters

### 4. Bivariate Analysis
- **Weekday × Hour vs Default:** Defaulters concentrated in 9:30–10am and 2pm windows; non-defaulters spread 10am–3pm
- **Age × Credit Amount:** Under-25 applicants with credit >₹1.7M are higher-risk; over-65 applicants with <₹100K loans also flag as defaulters
- **AMT_ANNUITY vs AMT_GOODS_PRICE** by contract status: linear relationship holds for Approved/Refused/Cancelled; breaks down for Refused loans

### 5. Multivariate & Correlation Analysis
- Pairplots of `AMT_INCOME_TOTAL`, `AMT_CREDIT`, `AMT_ANNUITY`, `AMT_GOODS_PRICE` for each target class
- Correlation heatmaps computed separately for Target 0 and Target 1 — key finding: `AMT_CREDIT ↔ AMT_GOODS_PRICE` (r ≈ 0.99), `AMT_CREDIT ↔ AMT_ANNUITY` (r ≈ 0.77)
- Income total is much more correlated with credit/annuity amounts in non-defaulters (r = 0.34–0.42) than in defaulters (near-zero)

### 6. Previous Application Analysis
- Separate cleaning pipeline for `previous_application.csv` (dropped cols with >49% nulls)
- Segmented into: `Approved / Canceled / Refused / Unused offer`
- Correlation heatmaps computed per contract status to identify financial patterns by outcome

### 7. Merged Dataset Analysis
- Left-joined application and previous application data on `SK_ID_CURR`
- Built pivot tables: `NAME_INCOME_TYPE × NAME_CLIENT_TYPE → mean(TARGET)` across contract statuses
- Heatmap of `CODE_GENDER × NAME_SELLER_INDUSTRY → sum(AMT_GOODS_PRICE)` by target class

---

## Key Findings

| Finding | Detail |
|---------|--------|
| **Occupation risk** | Laborers, Sales Staff, Drivers, and Core Staff show disproportionately higher default rates |
| **Application timing** | Defaulters cluster at opening (9:30–10am) and post-lunch (2pm) — could indicate financial urgency |
| **Young + high credit = risk** | Applicants under 25 with >₹1.7M in credit are a high-risk segment |
| **Income-credit decoupling** | In defaulters, income total has near-zero correlation with credit amount — they borrow beyond their means |
| **New clients win approvals** | Maternity-leave income type + New client type → highest approval probability |
| **Repeaters face rejection** | Unemployed Repeaters have the highest rates of cancellation and refusal in previous applications |
| **AMT_CREDIT ↔ GOODS_PRICE** | r = 0.99 — loan amount almost perfectly tracks goods price; useful for anomaly detection |

---

## Tech Stack

```
Python 3 · pandas · numpy · matplotlib · seaborn · Jupyter Notebook
```

---

## Project Structure

```
credit-risk-eda/
├── EDA.ipynb                    # Full analysis notebook
├── application_data.csv         # Primary application dataset
├── previous_application.csv     # Historical loan data
└── README.md
```

---

## Getting Started

```bash
git clone https://github.com/RIDA-IYENGAR/credit-risk-eda.git
cd credit-risk-eda
pip install pandas numpy matplotlib seaborn jupyter
jupyter notebook EDA.ipynb
```

Download the datasets from [Kaggle](https://www.kaggle.com/c/home-credit-default-risk/data) and place them in the project root before running.

---

## About

**Rida Iyengar** · Biotechnology Engineering, PES University  
[GitHub](https://github.com/RIDA-IYENGAR)
