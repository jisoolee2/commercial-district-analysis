# 🏪 Commercial District Closure Risk Analysis

> **Predicting business survival using 720K+ merchant records across South Korea**  
> `Python` `XGBoost` `LightGBM` `Folium` `Pandas` `Scikit-learn`

---

## 📌 Project Overview

Many entrepreneurs make location decisions without data-driven analysis — leading to high early closure rates. This project analyzes **what actually drives business closure** using nationwide credit card merchant data, and builds a predictive model to estimate 5-year survival probability.

---

## 📊 Dataset

| Attribute | Details |
|---|---|
| Source | National Credit Card Merchant Data (South Korea) |
| Records | 720,582 (raw) → 500,000 (after preprocessing) |
| Period | 1980 – 2024 |
| Coverage | 17 provinces · 230 districts (nationwide) |
| Unit | Individual merchant (business entity) |

---

## 🔍 Key Findings

### 1. Industry Type is the #1 Survival Predictor
| Industry | Closure Rate |
|---|---|
| F&B / Entertainment | 5.7% 🔴 |
| Food & Grocery | 3.5% |
| Beauty | 3.4% |
| National Average | 2.7% |
| Medical | 1.6% |
| Travel / Transport | 1.2% 🟢 |

### 2. Competition Density Drives Risk
- Higher same-industry density → monotonically higher closure rate
- F&B sector is most sensitive to competition pressure

### 3. Optimal Commercial Diversity = 7–9 Types
- Districts with 7–9 distinct industry categories show the **lowest closure rates**
- Over-diversified districts (13+) show rising closure risk again

### 4. Regional Patterns
- Seoul (3.5%), Incheon (3.1%), Gyeonggi (3.0%) — above national average
- Jeonnam (2.2%), Gangwon (2.5%) — below national average

---

## 🤖 Predictive Modeling

**Target variable:** 5-year survival (1 = operated 1,825+ days, 0 = closed within 5 years)

| Model | ROC-AUC |
|---|---|
| Logistic Regression | 0.886 |
| Random Forest | 0.898 |
| LightGBM | 0.895 |
| **XGBoost ⭐** | **0.899** |

**XGBoost — Best Model**
- ROC-AUC: **0.899**
- Closure Recall: **98.1%**
- Data leakage prevention applied (only pre-opening features used)

### Feature Importance
