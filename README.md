# 🚗 CO₂ Emissions Prediction — 2025 Canadian Fuel Consumption

![Python](https://img.shields.io/badge/Python-3.10+-blue?style=flat-square&logo=python)
![scikit-learn](https://img.shields.io/badge/scikit--learn-1.4+-orange?style=flat-square&logo=scikit-learn)
![Status](https://img.shields.io/badge/Status-Complete-green?style=flat-square)
![Data](https://img.shields.io/badge/Data-Canada%20Open%20Data-red?style=flat-square)

> Predict vehicle CO₂ emissions (g/km) from technical specifications using the 2025 Canadian Fuel Consumption Ratings dataset retrieved from the Canada Open Data API.

---

## Table of Contents
- [Overview](#overview)
- [Dataset](#dataset)
- [Project Structure](#project-structure)
- [Key Results](#key-results)
- [Key Findings](#key-findings)
- [How to Run](#how-to-run)
- [Tools & Libraries](#tools--libraries)
- [Author](#author)

---

## Overview

Cars are a major source of greenhouse gas emissions. This project answers the question:

> **Given a vehicle's technical specifications, can we accurately predict its CO₂ emissions?**

The full pipeline covers data ingestion from a live REST API, data cleaning, exploratory data analysis, feature engineering, training and evaluating three regression models, and SHAP-based explainability.

---

## Dataset

| Property | Detail |
|---|---|
| Source | [Canada Open Data — Fuel Consumption Ratings](https://open.canada.ca/data/en/dataset/98f1a129-f628-4ce4-b24d-6f16bf24cf44) |
| Resource ID | `2e1a460f-464d-44b7-b711-8870a6eef7b9` |
| Records | 650 vehicles |
| Model Year | 2025 only |
| Target Variable | `Emissions de CO2 (g/km)` |
| Missing Values | None |

**Features used:**

| Feature | Type | Notes |
|---|---|---|
| Cylindrée (L) | Numeric | Engine displacement |
| Combinée (L/100 km) | Numeric | Primary predictor (r = 0.989 with CO₂) |
| Catégorie de véhicule | Categorical | 12 vehicle classes → one-hot encoded |
| Transmission | Categorical | 18 types → one-hot encoded |
| Type de carburant | Categorical | 4 fuel types → one-hot encoded |

> **Dropped:** `Cylindres` (r=0.93 with Cylindrée), `Ville` (r=0.989 with Combinée) — redundant features removed to avoid multicollinearity.

---

## Project Structure

```
co2-emissions-prediction/
│
├── notebook/
│   └── Fuel_Consumption_CO2_Analysis.ipynb   # Main analysis notebook
│
├── report/
│   └── CO2_EDA_Modelling_Report.docx         # Full written report
│
├── dashboard/
│   └── co2_powerbi_dashboard.html            # Interactive dashboard
│
├── .gitignore
└── README.md
```

---

## Key Results

| Model | R² | RMSE (L/100 km) | Rank |
|---|---|---|---|
| **Linear Regression** ★ | **0.9928** | **0.238** | **1st** |
| Gradient Boosting | 0.9876 | 0.311 | 2nd |
| Random Forest | 0.9863 | 0.327 | 3rd |

> ★ **Best model.** An RMSE of 0.238 L/100 km represents approximately **1–2% relative error** — engineering-grade accuracy for real-world fuel consumption prediction.

---

## Key Findings

**1. Combined fuel consumption is the dominant predictor**
Combinée (L/100 km) has a Pearson correlation of r = 0.989 with CO₂ — consistent with the physical formula **CO₂ ≈ 23 × L/100 km**. Once this feature is included, all other features add marginal value.

**2. Linear Regression beats ensemble models**
Random Forest and Gradient Boosting both scored lower R² and higher RMSE than plain Linear Regression. The reason: the underlying relationship is physically linear and near-deterministic. There are no complex non-linear interactions for tree-based models to exploit.

**3. Multicollinearity handled correctly**
Cylinders (r=0.93 with engine size) and City consumption (r=0.989 with Combined) were dropped before modelling to prevent inflated coefficient variance in the linear model.

**4. Diesel and Ethanol reduce CO₂**
SHAP analysis confirmed that Diesel (D) and Ethanol (E) fuel types push predictions downward — lower CO₂ per km compared to Regular (X) and Premium (Z).

**5. Categorical features add minimal value**
Vehicle category, transmission type, and fuel type contribute negligibly after controlling for combined fuel consumption — the physics dominates everything else.

---

## How to Run

**1. Clone the repository**
```bash
git clone https://github.com/tpriyadata/co2-emissions-prediction
cd co2-emissions-prediction
```

**2. Install dependencies**
```bash
pip install -r requirements.txt
```

**3. Open the notebook**
```bash
jupyter notebook notebook/Fuel_Consumption_CO2_Analysis.ipynb
```

**4. View the dashboard**

Open `dashboard/co2_powerbi_dashboard.html` in any browser — no installation needed.(need to be updated)


---

## Tools & Libraries

| Tool | Purpose |
|---|---|
| `pandas` | Data manipulation and cleaning |
| `numpy` | Numerical operations |
| `matplotlib` / `seaborn` | Data visualisation |
| `scikit-learn` | Preprocessing, modelling, evaluation |
| `shap` | Model explainability |
| `urllib` / `json` | API data ingestion |

---

## Limitations

- **Single model year** — dataset contains 2025 vehicles only. Do not apply to other years without retraining.
- **Near-target feature** — Combined consumption is physically derived from CO₂. The high R² reflects this relationship, not pure model sophistication.
- **650 records** — adequate for this task but adding multiple years would improve generalization.

---

## Author

**Priya** — Data Science Portfolio  
📧 [tpriya27@gmail.com]  
🔗 

---

*Data source: Government of Canada Open Data Portal | 2025 Fuel Consumption Ratings*
