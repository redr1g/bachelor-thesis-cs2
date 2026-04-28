# CS2 Price Predictability

> Bachelor thesis project — Ukrainian Catholic University, Faculty of Applied Sciences (2026)

An analysis of price predictability in the virtual goods market based on market characteristics, using Counter-Strike 2 weapon skins traded on the Steam Community Market as a case study.

📄 **Thesis PDF:** [thesis.pdf](./thesis.pdf)
👤 **Author:** Taras Martsin
🎓 **Supervisor:** Roman Mishchenko

---

## Overview

This work investigates whether prices of virtual in-game items exhibit learnable patterns and, if so, which items carry such signal. The core contribution is a **two-stage pipeline**:

1. **Gating Model** — a Decision Tree classifier that filters items by forecastability *before* any forecasting model sees them. The threshold is chosen automatically via the Kneedle algorithm, with three additional variants based on instance-selection methods (ENN, RENN, DROP3) and a Random Forest alternative.
2. **Directional Forecasting** — fourteen models spanning six architectural families benchmarked across eight data pools of varying filtering stringency.

**Key results:**
- Best model: **LSTM**, ROC-AUC = **0.6567** on the Kneedle-filtered pool
- Gating Decision Tree precision: **99.2%** at threshold *t* = 0.7
- Gating consistently improves downstream model performance by **1–4% AUC**
- Classical econometrics (AutoARIMA) yields AUC ≈ 0.50 — no exploitable linear signal
- Cross-domain validation on M4 Daily places CS2 at **OWA = 1.29** vs M4 baseline 1.24

---

## Repository Structure

```
├── MAIN.ipynb           # Full pipeline: gating + 14-model benchmark
├── data/
│   └── README.md        # Dataset, models download instructions
├── thesis.pdf           # Final thesis document
├── requirements.txt
└── README.md
```
---

## Dataset

The primary dataset contains **13,823 CS2 weapon items** with **~7.85M daily observations** spanning January 2024 – December 2025, collected from the Steam Community Market API and enriched with:
- Esports tournament schedules (S-Tier, A-Tier) from Liquipedia
- Steam global sale dates (Winter / Summer / Lunar New Year, etc.)
- Daily peak concurrent CS2 player counts from SteamDB
- Collection (case) release dates

A secondary **Cosmetics** sub-sample (9,681 items, ~6.08M observations) covers stickers, graffiti, containers, music kits, charms, patches, agents, and collectibles — used for the validation experiment in Appendix B.

> ⚠ Datasets are too large for git — see [`data/README.md`](./data/README.md) for download links.

---

## Pipeline

### Stage 1: Gating Model

Per-item baseline MAE (lag-1 naïve predictor on log-prices) → Kneedle thresholding → Decision Tree classifier on six aggregate features:

| Feature | Importance |
|---|---|
| `ar1` (autocorrelation lag-1) | 43.0% |
| `mean_liquidity` | 38.3% |
| `coverage_ratio` | 12.2% |
| `zero_return_frac` | 6.0% |
| `mean_volume` | 0.5% |
| `pct_gaps_gt7` | 0.0% |

**Eight experimental pools** are constructed: 100% baseline, Kneedle, DT (t=0.5/0.7), ENN, RENN, DROP3, and RF Gating (t=0.7).

### Stage 2: Forecasting Benchmark

Fourteen models grouped by architectural class:

- **Classical / Linear:** Logistic Regression, SVM, KNN, Naive Bayes
- **Econometrics:** AutoARIMA
- **Tree-based:** Decision Tree, Random Forest
- **Gradient Boosting:** CatBoost, XGBoost, LightGBM
- **Deep Learning:** MLP, LSTM, 1D-CNN
- **SOTA Tabular:** TabNet

Target: **7-day forward log-return direction** (binary classification). Primary metric: **ROC-AUC**.

---

## How to Run

```bash
# 1. Clone
git clone https://github.com/redr1g/bachelor-thesis-cs2.git
cd bachelor-thesis-cs2

# 2. Install dependencies
pip install -r requirements.txt

# 3. Download the dataset (see data/README.md)
#    Place weapons.csv into ./data/

# 4. Open and run the notebook
jupyter lab MAIN.ipynb
```

Adjust file paths in the first few cells (`F:/analysis/data/...`) to match your local layout.

### Hardware

The full benchmark was run on:
- Intel Core i7 CPU
- 16 GB RAM
- NVIDIA GPU (used for LSTM, MLP, 1D-CNN, TabNet)
- Python 3.10, TensorFlow 2.15, scikit-learn 1.3, CatBoost 1.2

Full LSTM training across all 8 pools takes several hours on a mid-range GPU.

---

## Citation

If you use this work or the dataset, please cite:

```bibtex
@thesis{martsin2026cs2,
  author = {Taras Martsin},
  title  = {An Analysis of Price Predictability in the Virtual Goods Market Based on Market Characteristics},
  school = {Ukrainian Catholic University},
  year   = {2026},
  type   = {Bachelor's thesis}
}
```

---

## License

Code: MIT.
Dataset: CC BY 4.0 (with attribution).

---

## Acknowledgements

Steam Community Market API · Liquipedia · SteamDB · CS2 community case databases.
