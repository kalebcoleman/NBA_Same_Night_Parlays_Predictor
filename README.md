NBA Same‑Night Parlays Predictor



A daily‑refreshed pipeline that scrapes morning player‑prop lines, blends them with rolling performance features, and flags mis‑priced markets to build smarter same‑night NBA parlays.

🌟 Why this project matters

Sports‑tech showcase – End‑to‑end workflow (scrape → feature store → ML model → Shiny dashboard).

AI chops in native R – Uses tidymodels + xgboost for classification and automatic hyper‑tuning.

DevOps discipline – Reproducible renv lockfile and a GitHub Actions cron that ships new predictions every game day.

🚀 Quick start

# clone
$ git clone https://github.com/kalebcoleman/nba-parlay-predictor
$ cd nba-parlay-predictor

# install R deps reproducibly
$ R -e 'install.packages("renv"); renv::restore()'


# run the full pipeline locally (about 3–4 min)
$ R -e 'source("scripts/run_pipeline.R")'

# launch the Shiny dashboard
$ R -e 'shiny::runApp("app/")'

Note: You’ll need API tokens for player stats and odds (see Data sources below).  Store them as environment variables or GitHub Secrets.

### Reproducing the package environment

```r
# inside the project folder
install.packages("renv")
renv::restore()
```

🔧 Tech stack

Layer

Package(s) / Service

Scraping

rvest, httr2

Feature store

pins (board on data/raw & data/processed)

Modelling

tidymodels, xgboost, stacks

Automation

GitHub Actions (.github/workflows/cron.yml)

Dashboard

shiny, reactable, ggplot2

Environment

renv, targets

📁 Project structure

├── app/                 # Shiny UI + server
├── data/
│   ├── raw/             # Auto‑saved daily lines & stats via pins
│   └── processed/       # Feature matrices & labelled data
├── models/              # Parquet files of fitted workflows
├── scripts/
│   ├── scrape_odds.R    # 1️⃣ gets morning prop lines
│   ├── scrape_stats.R   # 1️⃣ pulls player game logs
│   ├── make_features.R  # 2️⃣ engineering & label
│   ├── train_model.R    # 3️⃣ cross‑validated fit
│   └── run_pipeline.R   # orchestrates 1‑3 in order
├── targets.R            # declarative pipeline
├── .github/workflows/
│   ├── R-CMD-check.yaml # CI
│   └── cron.yml         # 9 AM ET daily prediction run
├── renv.lock            # reproducible deps
└── README.md            # you’re here

📊 How the model works

Label creation – Line = O/U 25.5 pts; after the game we mark value = 1 if model‑predicted P(>25.5) – implied odds ≥ +10 ppt.

Feature set – Last‑10 rolling averages, usage %, true‑shooting, opponent defensive rating, rest days, injury status.

Algorithm – Gradient‑boosted trees (xgboost) with Bayesian hyper‑tuning via tune_bayes().

Threshold – Select cutoff maximizing expected Kelly‑Criterion bankroll growth in back‑test.

Explainability – SHAP value plots via DALEX.

📡 Data sources & API keys

Source

What

Free?

ENV var

https://www.balldontlie.io/ (or the NBA Stats API)

Historical player game logs

✅

NBA_STATS_KEY

https://the‑odds‑api.com/

Morning player‑prop odds

limited

ODDS_API_KEY

https://www.basketball‑reference.com/

Fallback box scores (web‑scrape)

✅

–

Add keys to .Renviron locally and to Repository → Settings → Secrets → Actions in GitHub.

🏗️ Daily workflow (CI/CD)

09:00 ET GitHub Action cron.yml

Restore renv cache

Rscript scripts/run_pipeline.R

Commit & push JSON predictions to predictions/YYYY‑MM‑DD.json

Netlify (or shinyapps.io) auto‑deploys updated dashboard.



⚖️ Disclaimer

This repository is for educational purposes.  No wagering advice is provided.  Use at your own risk and respect your jurisdiction’s regulations.

🗺️ Roadmap



Happy handicapping! 🏀