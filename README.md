# AltCredit Hackathon - Alternative Credit-Scoring Platform

This repository contains the unified **Rule-Based & ML Credit-Scoring Platform** for the **AltCredit** hackathon project.

## 📌 Scoring Engine Architecture

- **Rule-Based Engine (`ml/rule_engine.py`):** Implements the exact 0–1000 point credit scoring framework defined by BNP Paribas across 4 components:
  1. **Lifestyle (Max 350 pts):** Employment Stability (150), Housing Status (80), Digital Footprint (70), Education/Skill (50)
  2. **Spending Behavior (Max 350 pts):** Spend-to-Income Ratio (120), Expense Diversity (80), Cash-flow Volatility (70), Savings Reserve (80)
  3. **Repayment Discipline (Max 570 pts):** On-time Payment Rate (200), Debt-to-Income Ratio (120), Credit Utilization (100), Delinquency Severity (150)
  4. **Bonus / Penalty Adjustments:** Positive Habits (+20 each, max +50), Risk Flags (-20 each, max -50)
- **ML Probability of Default (PD) Model:** XGBoost Classifier ($\text{ROC-AUC} = 0.9910$)
- **ML Credit Score:** $\text{ML Score} = 1000 \times (1 - \text{PD})$
- **XAI & Factor Analysis:** SHAP TreeExplainer + Rule-Based Subfactor Breakdown
- **What-If Simulator:** Interactive scenario simulations (`saving_boost`, `autopay_builder`, `discretionary_spike`, `delinquency_spike`)
- **Product Recommendation Engine:** Auto-matches pre-approved cards and loans based on calculated credit score.

---

## 📂 Project Structure

```
.
├── run_pipeline.py           # End-to-end execution script
├── demographic_data.json     # Raw demographic dataset
├── id_mapping.json           # Raw user_id / applicant_id mapping
├── merged_data.json          # Combined raw demographic & alternative data
├── new_age_sample_data.json  # Raw alternative credit metrics
├── product_catalog.json      # Financial products catalog
├── transactional_data.csv    # Granular transaction history
├── models/                   # Saved models & preprocessor artifacts
│   ├── xgboost_model.pkl
│   ├── logistic_model.pkl
│   ├── preprocessor.pkl
│   └── selected_features.json
└── ml/                       # Modular ML codebase
    ├── __init__.py
    ├── rule_engine.py        # Rule-Based Credit Score Engine (0-1000 pts)
    ├── data_loader.py
    ├── preprocessing.py
    ├── feature_engineering.py
    ├── feature_selection.py
    ├── target_generation.py
    ├── train.py
    ├── evaluate.py
    ├── predict.py
    ├── explain.py
    └── README.md
```

---

## ⚡ Quick Start

Execute the complete end-to-end pipeline with a single command:

```bash
python run_pipeline.py
```

### Programmatic Usage

```python
from ml.predict import predict_credit_risk
from ml.explain import explain_applicant_risk
from ml.rule_engine import run_what_if_simulation

applicant_data = {
    "applicant_id": "APP_123",
    "months_at_job": 36,
    "housing": "owner",
    "monthly_income": 6500,
    "monthly_spend": 2100,
    "essential_pct": 0.72,
    "credit_util": 0.15,
    "on_time_rate": 0.95,
    "dti": 0.22,
    "savings_days": 180,
    "delinq_90plus": 0,
    "positive_habits": 3,
    "risk_flags": 0
}

# 1. Calculate Rule-Based Credit Score & ML Risk
res = predict_credit_risk(applicant_data)
print(f"Rule Score: {res['rule_credit_score']} | ML Score: {res['ml_score']} | Risk Tier: {res['risk_info']['tier']}")

# 2. Run What-If Simulation
sim = run_what_if_simulation(applicant_data, "autopay_builder")
print(f"Autopay Builder Delta: {sim['score_delta']:+d} pts")

# 3. Get Factor Analysis & SHAP Explanation
explanation = explain_applicant_risk(applicant_data)
print(explanation)
```

