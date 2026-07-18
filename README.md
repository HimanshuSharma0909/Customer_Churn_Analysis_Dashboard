# 📊 Customer Churn Analysis — Streaming Subscription Service

A full end-to-end churn analysis project: **SQL → Python (pandas) → Interactive Dashboard using Claude AI**, built to identify *when, why, and who* is churning for a subscription-based streaming service, and to turn that into concrete retention actions.

  **Go Through it:**
🔗 **Live Dashboard:** [north-wreath-37949898.figma.site](https://north-wreath-37949898.figma.site/)

<img width="1917" height="926" alt="dashboard-executive-overview" src="https://github.com/user-attachments/assets/28ab544a-6fb3-4b9a-990f-e493e4d62405" />

For More: Click on the link above this dashboard picture and you can interact with live dashboard.
---

## 📌 Overview

This project analyses subscription behaviour for **200 customers**, of whom **56 (28.0%)** have cancelled. The core idea driving the whole analysis is simple: the `cancellation_date` field turns the abstract question *"who leaves?"* into the operational question *"when and why do they leave?"* — which is what actually lets a business intervene **before** a cancellation happens.

**Key numbers at a glance:**

| Metric | Value |
|---|---|
| Customers analysed | 200 |
| Overall churn rate | 28.0% |
| Retained customers | 144 |
| Monthly recurring revenue at risk | $9 |
| Estimated CLTV lost | $118 |

---

## 🎯 Business Problem

The company runs a streaming subscription service where revenue depends entirely on recurring monthly/annual payments. Acquiring a new customer costs **5–7× more** than retaining an existing one — so every avoidable cancellation quietly erodes both current revenue and future growth.

> **The core problem:** churn was being *counted*, not *prevented*. Reporting could answer "how many left last quarter?" but never "who is about to leave, and why?"

**Core question this project answers:**
> When are customers cancelling, why are they cancelling, and which customers should we save first?

**Stakeholders served:**

| Stakeholder | Need |
|---|---|
| 🎧 Retention / CRM team | A daily list of at-risk customers to act on |
| 💼 Leadership & finance | The revenue impact quantified, to justify retention spend |
| 🛠️ Product & support | Which experiences (price, content, service) push people out |

**Objectives:**
1. Quantify overall churn and the revenue/CLTV it destroys
2. Identify the strongest drivers of churn (contract, plan, price, service quality)
3. Segment customers by risk so retention effort is spent where it matters
4. Recommend concrete, prioritised actions to reduce churn


---

## 🗂️ Data

The dataset originates from a relational database (`customer_churn.db`) with **three tables** joined on `customerid`:

| Table | What it holds |
|---|---|
| `db_customer` | Demographics — name, country, state, gender, dob, interests, pincode |
| `db_subscription` | Plan & billing — dates, plan/contract type, charges, cltv, churn_score, cancellation |
| `db_support` | Service quality — complaint_date, escalations, csat_score, comments |

The analysis began with a raw extract of 21 customer records, cleaned and joined in SQL, then expanded to **200 records** (preserving the original schema, value distributions, and business logic) to build a dashboard robust enough to reveal statistically meaningful patterns.

### Key fields

- `cancellation_date` ⭐ — the core analytical field (null if the customer is still active)
- `cancellation_reason` — stated reason for leaving
- `subscription_type` — acquisition source (Organic / Paid / Referral)
- `plan_type` / `contract_type` — Basic·Standard·Premium / Monthly·Annual
- `monthly_charges` / `cltv` — recurring revenue / customer lifetime value
- `churn_score` — 0–100 propensity score
- `complaint_date` / `escalations` / `csat_score` / `complaint_count` — service-quality signals
- `churn_flag` — target variable (1 = churned, 0 = retained)

### Engineered features

- `customer_age` — from date of birth
- `tenure_days` — days between subscription start and cancellation (or today, if active)
- `churn_risk` — banded from `churn_score`: Low (<50) · Medium (50–69) · High (≥70)
- `status` — human-readable "Churned" / "Retained"
- `escalated_flag` — 1 if the customer's complaint was escalated

---

## 🧹 Data Cleaning & Preparation

Real subscription exports are messy. Issues found and how they were handled:

| Issue | How it was handled |
|---|---|
| Inconsistent date formats (DD-MM-YYYY text) | Parsed into real date objects; validated |
| Blank `cancellation_date`/reason for active users | Kept as NULL (not 0/empty) so churn logic stays correct |
| Missing `csat_score` & `complaint_count` | Treated as NULL and excluded from averages, not imputed as 0 |
| Misspelling "Refferal" in `subscription_type` | Documented and grouped under "Referral" |
| Invalid leap-year date (29-02-2020) | Handled safely by the date parser |
| Revenue outliers | Retained but flagged and reviewed so they don't distort means |
| Free-text escalations ('Y'/'N'/blank) | Encoded into a clean numeric `escalated_flag` (1/0) |

**Guiding principle:** missing values were preserved as `NULL` rather than silently replaced, so a missing satisfaction score is never mistaken for a satisfaction score of zero — keeping every rate and average honest.

---

## 🛠️ Methodology & Tools

| Stage | Tool |
|---|---|
| Data storage & querying | SQL (`customer_churn.db`, 3 tables joined on `customerid`) |
| Data cleaning, EDA & analysis | Python + Jupyter Notebook (pandas, numpy, matplotlib, seaborn) |
| Raw data & exploration | Excel / CSV |
| Interactive dashboard | React + Recharts (Tailwind CSS) |
| AI coding assistant | Claude AI — built the dashboard front-end |
| Reporting style reference | Microsoft Power BI conventions |

**Pipeline:**
1. Join the three source tables on `customerid` using SQL
2. Audit & clean the data in a Jupyter Notebook (pandas)
3. Feature engineering (tenure, age, risk bands, flags)
4. Exploratory Data Analysis (EDA) to surface drivers & relationships
5. Segmentation by contract, plan, geography, demographics & risk
6. Financial quantification (revenue at risk, CLTV lost)
7. Dashboard design following Power BI reporting conventions
8. Recommendations tied directly to the evidence

---

## 🔍 Key Findings

**1. Contract type is the strongest churn lever**
Monthly contracts churn at **51%** vs just **5.9%** for annual contracts.

**2. Competition & price dominate exit reasons**
"Switched to competitor" (14.3%) and "Moving/relocation" (14.3%) top the list of cancellation reasons, followed by technical issues, budget cuts, and poor streaming quality.

**3. The churn score works**
Customers flagged **High-risk** (churn_score ≥ 70) churned at **100%** — validating it as a reliable early-warning signal. Risk distribution: 41 High-risk, 39 Medium-risk, 120 Low-risk.

**4. Service failures are lethal**
Escalated customers churn at **93.5%** vs **8.4%** for non-escalated. Churned customers averaged a CSAT of **33** vs **68** for retained customers.

**5. Churn happens early**
Churned customers lasted on average **404 days** vs **1,502 days** for those still active.

**6. Plan tier matters**
Churn rate by plan: Basic **47.1%** → Standard **26.7%** → Premium **7%**.

### Root causes (synthesis)

Churn in this business is driven by four reinforcing forces:
1. **Low commitment** — monthly plans have no lock-in, so dissatisfaction converts to cancellation instantly
2. **Competition & price sensitivity** — customers perceive better value elsewhere
3. **Service quality & unresolved complaints** — a poor support experience is a direct on-ramp to cancellation
4. **Weak content & onboarding value** — customers never reach the "aha" moment before leaving

> **In one sentence:** customers on flexible monthly plans who hit a price, content, or service disappointment early in their tenure — and are not proactively engaged — cancel before they ever become loyal.

---

## ✅ Recommendations

| Priority | Action |
|---|---|
| **1. Convert monthly → annual** | Offer a discount/bonus content for switching to annual billing, targeted at first renewal. Annual churn is only 5.9% — every conversion structurally lowers risk. |
| **2. Proactively save High-risk customers** | Trigger a retention workflow whenever `churn_score` ≥ 70 (this cohort churns at 100%). Use a High-Risk Watchlist as the retention team's daily action list. |
| **3. Fix the service-failure pipeline** | Treat any escalation as a churn event in progress. Set SLA-backed resolution targets and monitor CSAT as a leading indicator. |
| **4. Defend against price & competition** | Introduce a lower-cost/ad-supported tier for price-sensitive users; communicate value before renewal. |
| **5. Strengthen early onboarding** | Since churn is early (~404 days), invest in first-30-day engagement and trial-end reminders. |

---

## 📈 Dashboard

The interactive dashboard (built with **React + Recharts**, styled with Tailwind CSS, following Power BI design conventions) includes:

- KPI cards (churn rate, retention rate, revenue/CLTV at risk)
- Cancellations-by-month trend chart
- Top cancellation reasons
- Churn rate by contract type, plan type, and state
- Churn-risk distribution donut chart
- Correlation heatmap (churn score, escalations, contract type, etc.)
- Filterable/drillable views for live exploration

👉 **[View the live dashboard here](https://north-wreath-37949898.figma.site/)**

<img width="1917" height="926" alt="dashboard-executive-overview" src="https://github.com/user-attachments/assets/3e60d2dd-e252-46c0-90c6-4dc45220953f" />

<img width="1917" height="927" alt="dashboard-churn-drivers" src="https://github.com/user-attachments/assets/d206eeff-41e3-46c6-b9bc-97c77f0f50d7" />

<img width="1891" height="902" alt="dashboard-customer-segments" src="https://github.com/user-attachments/assets/6d48ccb3-72e2-442d-8680-08d53e236a45" />

<img width="1896" height="907" alt="dashboard-financial-impact" src="https://github.com/user-attachments/assets/378a9c1d-adb9-4f56-a577-306696e78fb3" />



---

## 📁 Repository Structure

```
├── analysis.ipynb                          # Full Jupyter Notebook: cleaning, feature engineering, EDA
├── customer_churn.db                       # Raw SQLite source database (3 tables)
├── customer_churn_data_raw.xlsx            # Raw data export
├── exported_churn_data_expanded.csv        # Cleaned, merged, analysis-ready dataset
├── Customer_Churn_Analysis_Report.pdf      # Full business intelligence report
└── README.md                               # Project documentation (this file)
└── Live_Dashboard                          # Having a link of interactive dashboard Fully Working
```

---

## ⚠️ Limitations

- The dataset was expanded from a small original sample (21 real records); findings show direction and structure rather than fully audited financials
- `churn_score` is provided in the source data rather than modelled here; a production system would train and monitor it continuously
- External factors (marketing campaigns, competitor pricing) are not captured in the data

## 🚀 Next Steps

- Operationalise the High-risk watchlist into a CRM as automated retention triggers
- A/B test the annual-conversion and win-back offers, and measure churn lift
- Add cohort-retention curves and a predictive churn model on live data
- Connect the dashboard to a live data source

---

## 🧰 Tech Stack

`SQL` · `Python` (`pandas`, `numpy`, `matplotlib`, `seaborn`) · `Jupyter Notebook` · `Excel/CSV` · `React` · `Recharts` · `Tailwind CSS`

---

**Prepared by:** Data Analytics Portfolio Project
**Date:** July 2026
**Report type:** Customer retention / churn analysis
