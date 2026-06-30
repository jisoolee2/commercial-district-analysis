# 🏪 Commercial District Closure Risk Analysis
### Predicting business survival using 720K+ merchant records across South Korea

`Python` `XGBoost` `LightGBM` `Folium` `Pandas` `Scikit-learn`

📄 [Full Report (PDF)](./report/데이터_시각화_프로젝트.pdf) ｜ 📓 [Notebooks](./notebooks) ｜ 🗺️ [Interactive Maps](./visualizations)

---

## 📌 Overview

Many entrepreneurs choose business locations without data-driven analysis, contributing to high early closure rates. This project analyzes 720K+ nationwide credit card merchant records to identify what actually drives business closure, then builds a predictive model estimating 5-year survival probability — designed as a decision-support tool for prospective business owners.

**Key question:** Does industry type, competition density, or location matter more for business survival?
**Answer:** Industry choice matters most (4x gap between safest and riskiest), followed by competition density — location is a secondary factor.

## 📊 Dataset

| Attribute | Details |
|---|---|
| Source | National credit card merchant data (South Korea) |
| Records | 720,582 (raw) → 500,000 (after preprocessing & sampling) |
| Period | 1980–2024 (opening date basis) |
| Coverage | 17 provinces · 230 districts (nationwide) |
| Unit | Individual merchant |

## 🔍 Key Findings

**1. Industry type is the strongest predictor of survival (#1 in Feature Importance)**
| Industry | Closure Rate |
|---|---|
| F&B / Entertainment | 5.7% 🔴 |
| Food & Grocery | 3.5% |
| Beauty | 3.4% |
| **National Average** | **2.7%** |
| Medical | 1.6% |
| Travel / Transport | 1.2% 🟢 |

→ *F&B/Entertainment closes at 2x the national average — low entry barriers and trend sensitivity drive this.*

**2. Competition density shows a monotonic relationship with closure risk**
Districts in the highest competition quintile show meaningfully higher closure rates than the lowest quintile, with F&B/Entertainment most sensitive to this effect.

**3. Optimal commercial diversity is 7–9 industry types**
Districts with too few (1–3) or too many (13+) industry types show elevated closure rates — a moderate level of diversity correlates with the healthiest commercial ecosystem.
<img width="868" height="355" alt="image" src="https://github.com/user-attachments/assets/23d1c4dc-3f1b-45b8-b7ce-505bfdfd71e1" />

<img width="868" height="404" alt="image" src="https://github.com/user-attachments/assets/19f4c134-cd5a-46fb-b953-ed941c185b3e" />

**4. Regional effect is real but secondary to industry choice**
Seoul (3.5%), Incheon (3.1%), Gyeonggi (3.0%) exceed the national average, while Jeonnam (2.2%) and Gangwon (2.5%) sit below it — but the gap across regions is far smaller than the gap across industries.

## 🤖 Predictive Modeling

**Target:** 5-year survival (1,825+ days = survived, restricted to merchants that opened 5+ years before the reference date to avoid class imbalance from recently-opened "still operating" merchants)

**Leakage prevention:** Operating-period (`영업기간_일`) and closure date are excluded from model inputs since they're only known *after* the outcome is determined. Only pre-opening / opening-time information is used: industry, region, opening year/month, competition density, district diversity. Target Encoding for categorical variables was fit on train data only to prevent target leakage into the encoding itself.

| Model | ROC-AUC |
|---|---|
| Logistic Regression | 0.886 |
| Random Forest | 0.898 |
| LightGBM | 0.895 |
| **XGBoost** ⭐ | **0.899** |

**XGBoost (best model) — confusion matrix breakdown:**
- Closure Recall: **98.1%** — correctly flags nearly all merchants that actually closed
- Survivor Recall: 76.1% — some survivors are conservatively flagged as at-risk

*Why Recall over Precision: for a risk-warning tool, a false "at-risk" flag (lost optimism) is a far cheaper mistake than missing a real closure risk. The model is intentionally tuned toward catching closures even at the cost of some false positives — the right trade-off for a decision-support tool, not a 0.5-threshold classifier.*

**Feature importance:** Industry > Competition density > Opening year > Region > Opening month > District diversity (district diversity is weak alone but interacts meaningfully with competition density — see report).

## 🗺️ Map Visualizations

<img width="825" height="474" alt="image" src="https://github.com/user-attachments/assets/8de3f949-4433-4eb4-bf7b-638e14de4750" />


<img width="890" height="488" alt="image" src="https://github.com/user-attachments/assets/ccb2f554-2565-4856-8216-ec8eea449f59" />

- **Closure rate choropleth** — F&B/Entertainment closure rate by district
- **Risk-grade map** — districts classified into 안전권/보통권/위험권 using a weighted score (closure rate 50%, competition density 30%, diversity 20%)
- **Competition × new-openings bubble map** — identifies "overheated" districts (high competition + high new entry) vs. "blue ocean" districts (low competition + healthy new entry)
- **Model-based survival probability map** — XGBoost predictions projected onto the map, cross-validated against the actual closure-rate map for consistency

## 💡 Business Implications

- For prospective founders, **industry selection should come before location selection** — the data shows a 4x survival gap across industries vs. a much smaller gap across regions.
- High foot-traffic areas are **not automatically good locations** — they often carry the highest competition density and closure risk.
- A practical heuristic for site selection: favor districts with 7–9 commercial categories, below-median competition density in your target industry, and check the model's predicted survival probability for your specific industry × district combination.

## ⚠️ Limitations

- Rent and foot-traffic data were not available and likely explain meaningful within-district, within-industry variation.
- Binary 5-year survival collapses meaningful differences (a business that closed at 4 years 11 months is coded the same as one that closed in year 1) — survival analysis (e.g., Cox regression) would capture this better.
- Credit-card merchant data likely underrepresents cash-heavy small businesses.

## 🛠️ How to Run

```bash
pip install -r requirements.txt
jupyter notebook notebooks/
```

Data source: dongduk womens university

## 📁 Repository Structure

```
franchise-survival-prediction/
├── README.md
├── report/                  # Full Korean analysis report (PDF)
├── notebooks/                # Preprocessing, EDA, modeling notebooks
├── visualizations/           # Folium maps (HTML) + image captures
└── requirements.txt
```
