# NYC 311 Service Complaint Prediction
### Predicting daily city service complaints with Poisson and Negative Binomial regression

Predictive Analytics mini-project · B.Tech CSE · Manipal University Jaipur · 2026–2027

**Submitted by:** Kanakpreet Kaur (2427030168) · Bhoomi Saraf (2427010306)

---

## Problem
How many 311 service requests of each type will Staten Island (New York City) receive on a given day? Which factors are associated with higher complaint volumes?

The target is a **count** (complaints per day per category), so the project uses count GLMs: **Poisson regression** and **Negative Binomial regression**.

## Data
- **Source:** NYC Open Data, [311 Service Requests from 2020 to Present](https://data.cityofnewyork.us/Social-Services/311-Service-Requests-from-2020-to-Present/erm2-nwe9) (dataset `erm2-nwe9`).
- **Subset:** borough = Staten Island, created 1 Jan 2021 to 31 Dec 2024, 12 columns. That is **546,250 requests** (~106 MB as CSV).
- **In this repo:** `Data/nyc311_staten_island_2021_2024.zip` (~20 MB). The notebook reads the zip directly, so you don't need to unzip it.

## Repository structure
```
├── NYC311_Complaints_Poisson_NB.ipynb   # full analysis (executed, with outputs)
├── NYC311_Complaints_Poisson_NB.html    # same notebook as a web page
├── Data/
│   └── nyc311_staten_island_2021_2024.zip
├── report/
│   ├── NYC311_Complaint_Prediction_Report.docx
│   └── NYC311_Complaint_Prediction_Report.pdf
├── requirements.txt
└── README.md
```

## How to run
```bash
pip install -r requirements.txt
jupyter notebook NYC311_Complaints_Poisson_NB.ipynb   # then Run All (~2 minutes)
```
In VS Code you can instead open the folder, open the notebook, select a Python kernel and click **Run All**.

## Method (mapped to the syllabus)
| Module | Implemented |
|---|---|
| Introduction | Descriptive / predictive / prescriptive analytics, industry roles, ethics (location data used only in aggregate) |
| Data preparation | Type fixing, duplicate check, case standardisation (224 → 212 types), renamed-type merge, removal of non-complaint appointment rows, ZIP imputation from coordinates, logical-error correction (536 closed-before-created), IQR outliers, EDA, feature engineering, likelihood-ratio feature selection |
| GLMs | Poisson regression, overdispersion tests (Pearson, Cameron–Trivedi), Negative Binomial regression, IRR inference |
| Validation | Chronological split (train 2021–23, test 2024), expanding-window CV, RMSE/MAE, grid & random search for α, over-/under-fitting ladder |

## Final model
Negative Binomial GLM (log link):
```
count ~ category + weekday + month + holiday + trend
        + category×trend + category×weekday + category×month
```

## Key results (unseen test year 2024)
| Metric | Value |
|---|---|
| Poisson dispersion (should be ≈ 1) | 7.72, i.e. strongly overdispersed |
| AIC, Poisson → Negative Binomial | 81,920 → **68,039** |
| Test RMSE / MAE (final NB) | **18.00 / 11.19** complaints per category-day |
| Baseline RMSE / MAE (category × weekday average) | 19.91 / 13.42 |
| Coverage of the 90% plan-for level | **89.4%** of category-days |

- **Federal holidays:** about 24% fewer complaints.
- **Trends differ by category:** Housing +14% per year, Streets & Traffic −14% per year.
- **Weekends:** Noise rises, while most other categories fall.

## Limitations
Weather is not included, although it drives the largest spikes. Trends are extrapolated linearly on the log scale. The analysis covers one borough only.
