# Dataset

The CS2 weapons price dataset used in this work is hosted on Kaggle due to its size.

## Download

**Kaggle:** [theredrig/cs2-weapons](https://www.kaggle.com/datasets/theredrig/cs2-weapons)

## Files

- `weapons.csv` — primary dataset, 13,823 items × ~7.85M daily observations
- `cosmetics.csv` — cosmetics sub-sample (Appendix B), 9,681 items

## Format

Each row represents one item-day cross-section with the following columns:

- `steam_market_hash_name` — unique item identifier
- `date` — observation date (YYYY-MM-DD)
- `price` — median daily sale price (USD)
- `amount` — units sold per day
- `liquidity` — static liquidity score
- `avg_players` — peak concurrent CS2 players that day
- `sale_pressure`, `tournament_pressure` — engineered event indicators
- `exterior`, `weapon`, `skin`, `collection`, `steam_rarity` — item attributes
- `is_stattrak`, `is_souvenir` — binary flags
- `skin_age_days` — days since item market introduction

## Setup

After downloading, place the files in this `data/` directory:

```
data/
├── weapons.csv
├── cosmetics.csv
└── README.md
```

Update the path in `MAIN.ipynb` (cell 1) if you store the data elsewhere.

# Models

The main experiments (according to Table 6.4) are stored as models on Huggingface. 

## Download

**Huggingface:** [redr1g/models](https://huggingface.co/redr1g/final-thesis-experiments)


## Inference

```python
from huggingface_hub import hf_hub_download
from tensorflow.keras.models import load_model

# Load the best model
model_path = hf_hub_download(
    repo_id="redr1g/final-thesis-experiments", 
    filename="lstm_final_kneedle.keras"
)
model = load_model(model_path)
```