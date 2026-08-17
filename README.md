 # Customer Churn Prediction & Revenue Risk Analytics

End-to-end churn analytics pipeline in Python — statistical testing, machine learning, explainability, and customer health scoring — with a Power BI reporting layer for stakeholder use.

## Business Problem
Subscription/telecom businesses lose recurring revenue when customers churn. This project identifies **which customers are at risk, why, and how much revenue is exposed**, so retention efforts can be targeted rather than reactive.

## Dataset
IBM Telco Customer Churn dataset (~7,000 customers, 21 features: demographics, account info, services subscribed, billing).

## Approach

**1. Data Cleaning**
Fixed data type issues (e.g., `TotalCharges` stored as text), handled missing values, removed non-predictive identifiers.

**2. Exploratory & Statistical Analysis**
- Chi-square tests confirmed contract type, payment method, and internet service are all significantly associated with churn (p < 0.001)
- T-test confirmed churned customers have significantly lower tenure than retained customers (p < 0.001)
- **Key finding:** month-to-month contract customers churn at **15x the rate** of two-year contract holders (42.7% vs. 2.8%)
- **Key finding:** electronic check users churn at **45.3%**, roughly 3x higher than automatic payment methods

**3. Feature Engineering**
Tenure buckets, service adoption score (add-ons subscribed / total available), categorical encoding.

**4. Modeling**
Compared Logistic Regression (baseline) against XGBoost, using stratified train/test split and class-weighting to handle imbalance (~27% churn rate).

| Model | AUC-ROC | Recall (churn) | Precision (churn) |
|---|---|---|---|
| Logistic Regression | 0.845 | 0.80 | 0.51 |
| XGBoost (default) | 0.820 | 0.68 | 0.54 |
| **XGBoost (tuned)** | **0.846** | **0.82** | 0.51 |

Tuned XGBoost was selected as the final model — modest AUC improvement over the linear baseline, with better recall on actual churners, while confirming the churn signal is largely linear in nature.

**5. Explainability (SHAP)**
SHAP analysis confirmed contract type and tenure as the dominant global drivers, and surfaced an additional insight not evident from statistical testing alone: **fiber optic internet customers churn at a disproportionately higher rate**, suggesting a possible pricing or service-quality issue worth further investigation.

**6. Customer Health & Opportunity Scoring**
Built a composite health score (0–100) combining churn risk, tenure, and service adoption, plus a revenue opportunity estimate per customer based on unadopted add-on services — enabling prioritized, not just reactive, customer outreach.

**7. Reporting**
Final scored dataset (churn probability, risk tier, health score, opportunity value) exported and visualized in Power BI: executive summary, churn/risk breakdown, and customer-level drill-through.

## Tech Stack
Python (pandas, numpy, scipy, scikit-learn, XGBoost, SHAP, imbalanced-learn), Power BI, DAX

## Key Business Takeaways
- Contract type is the single strongest churn predictor — incentivizing longer contracts could materially reduce churn
- New customers (low tenure) are highest-risk — early lifecycle engagement matters most
- Fiber optic customers churn disproportionately — worth a dedicated investigation into pricing/service quality
- Electronic check payment users churn ~3x more than automatic payment methods — a candidate for a targeted payment-method migration campaign

## Repository Structure
```
├── data/               # raw and processed data
├── notebooks/          # analysis notebook (cleaning → EDA → modeling → SHAP → scoring)
├── outputs/             # final scored customer dataset
└── README.md
```