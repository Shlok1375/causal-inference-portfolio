# Causal Inference in Product Analytics
### Recovering True Treatment Effects from a Non-Random Feature Rollout

**Author:** Shlok Sheth | [Portfolio](https://shlokasheth.netlify.app) | [LinkedIn](https://www.linkedin.com/in/shlok-sheth2016/)

---

## The Problem

A product team rolled out a new ad feature to a subset of users. The rollout was **not randomized** — high-engagement users were more likely to receive the feature. A naive comparison reports a **6.45% lift** in conversion rate and recommends full launch.

Is that lift real, or mostly explained by the fact that better users were targeted to begin with?

This project answers that question using three complementary causal inference methods and demonstrates that the naive estimate overstates the true causal effect by **85.7%**.

---

## Methods

| Method | Purpose | Key Result |
|--------|---------|------------|
| Propensity Score Matching (PSM) | Correct for observed selection bias | ATE: 0.0092 |
| Difference-in-Differences (DiD) | Control for pre-period baseline differences | Estimate: 0.0189 |
| Uplift Modeling (T-Learner) | Identify which users actually respond | Top 25%: ~5x avg lift |

---

## Key Findings

```
Naive lift (biased):          0.0645   <- what a bad analyst reports
PSM ATE (corrected):          0.0092   <- true population-average effect
DiD estimate:                 0.0189   <- parallel trends approach
Ground truth ATE (synthetic): 0.0142   <- known from data generation

Naive overstatement: 85.7%

Bottom 25% (Lost Causes):     negative observed lift -- do NOT target
Top 25% (Sure Things):        ~5x the naive population lift
```

---

## Business Recommendation

**Do not ask "Did it work?" — ask "For whom, and at what cost?"**

- Restrict rollout to the **top uplift quartile** — concentrates resources on Persuadables and avoids harming Sleeping Dogs
- Validate ITE estimates via a follow-up RCT before permanent launch
- Never report naive A/B lift without checking for selection bias in treatment assignment

---

## Project Structure

```
causal-inference-portfolio/
├── data/
│   └── criteo_uplift_sample.csv      <- Synthetic dataset (Criteo v2.1 structure)
├── notebooks/
│   └── criteo_causal_analysis.ipynb  <- Full analysis notebook
├── figures/
│   ├── 01_covariate_imbalance.png
│   ├── 02_propensity_scores.png
│   ├── 03_balance_check.png
│   ├── 04_did_comparison.png
│   ├── 05_uplift_segments.png
│   ├── 06_qini_curve.png
│   └── 07_summary_dashboard.png
├── requirements.txt
└── README.md
```

---

## Dataset

This project uses a **100K-row synthetic dataset** that exactly mirrors the structure of the [Criteo Uplift Modeling Dataset (v2.1)](https://ailab.criteo.com/criteo-uplift-modeling-dataset/).

To run the notebook on the real 14M-row dataset:
1. Download `criteo-uplift-v2.1.csv.gz` from the Criteo AI Lab
2. Place it in the `data/` folder
3. In the notebook, replace:
   ```python
   df = pd.read_csv('../data/criteo_uplift_sample.csv')
   ```
   with:
   ```python
   df = pd.read_csv('../data/criteo-uplift-v2.1.csv.gz')
   ```

---

## Setup

```bash
git clone https://github.com/YOUR_USERNAME/causal-inference-portfolio.git
cd causal-inference-portfolio
pip install -r requirements.txt
jupyter notebook notebooks/criteo_causal_analysis.ipynb
```

---

## Requirements

```
pandas>=1.5
numpy>=1.23
matplotlib>=3.6
seaborn>=0.12
scikit-learn>=1.1
jupyter>=1.0
```

All dependencies are standard Python data science packages. The T-Learner uplift model is implemented from scratch using scikit-learn's GradientBoostingClassifier — no specialized causal inference libraries required.

---

## Limitations

1. PSM corrects only for observed confounders — unmeasured confounding may remain
2. DiD relies on the parallel trends assumption, which cannot be directly tested
3. Uplift model estimates are directional signals until validated via RCT
4. Results based on a synthetic sample — re-run on the full Criteo dataset for production use

---

*Part of my product data science portfolio. See [shlokasheth.netlify.app](https://shlokasheth.netlify.app) for more.*
