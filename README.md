# Causal Inference in Product Analytics
### Recovering the True Effect of a Non-Random Feature Rollout

**Author:** Shlok Sheth | [Portfolio](https://shethshlok.netlify.app) | [LinkedIn](https://www.linkedin.com/in/shlok-sheth2016/)

## The Problem

A product team rolled out a new feature to some users, but not at random: high-engagement users were more likely to get it. A naive comparison shows a **6.45-point lift** in conversion and suggests a full launch.

How much of that lift is the feature, and how much is just that better users got it first?

The data is simulated, so the true effect is known (**0.0142**). That makes it possible to check how close each method gets.

## Key Findings

| Estimate | Value | vs. true effect (0.0142) |
|---|---|---|
| Naive comparison | 0.0645 | 4.5x too high |
| Propensity score matching (PSM) | 0.0092 (95% CI: −0.011 to 0.028) | Removes most of the bias; CI includes 0 |
| Difference-in-differences (DiD) | 0.0189 | Illustrative only (see limitations) |

**The naive estimate is 4.5x the true effect: 78% of the apparent lift is selection bias.** Launching on it would have badly overstated the feature's value.

An uplift model (T-learner) also ranks users from negative to strongly positive response, which suggests the feature helps some users and hurts others. That ranking is directional only (see limitations).

## Recommendation

Don't ask "did it work?" Ask "for whom, and by how much?"

- Don't launch broadly on the naive number
- Run a randomized test, ideally stratified by uplift score, to measure the true effect and confirm who responds
- Always check how treatment was assigned before trusting a lift

## Methods

| Method | What it does |
|---|---|
| Propensity score matching | Matches treated and untreated users with similar characteristics |
| Difference-in-differences | Compares change over time between groups |
| T-learner uplift model | Estimates which users respond to the feature |

The T-learner is built from scratch with scikit-learn's GradientBoostingClassifier; no causal inference libraries.

## Files

- `criteo_causal_analysis.ipynb`: full analysis, with outputs
- `criteo_uplift_sample.csv`: 100,000 simulated users, same structure as the Criteo Uplift Modeling Dataset v2.1
- `*.png`: figures

## Setup

```bash
git clone https://github.com/Shlok1375/causal-inference-portfolio.git
cd causal-inference-portfolio
pip install -r requirements.txt
jupyter notebook criteo_causal_analysis.ipynb
```

To run on the real 14M-row dataset, download `criteo-uplift-v2.1.csv.gz` from the Criteo AI Lab, place it in this folder, and change the `read_csv` path in the notebook. The real data has no known true effect.

## Limitations

1. **PSM** only corrects for confounders it can see. Unmeasured ones may remain.
2. **DiD** here uses visits as a stand-in for the pre-period, because the data has no earlier conversion. A valid DiD compares the same outcome before and after, so this estimate is illustrative only.
3. **Uplift segments** are scored on the same users the models were trained on, within non-random groups. Both exaggerate differences between segments, so validate with a randomized test before targeting.
4. **Simulated data:** results show how the methods behave, not real user behavior.

*Part of my product data science portfolio. See [shethshlok.netlify.app](https://shethshlok.netlify.app) for more.*
