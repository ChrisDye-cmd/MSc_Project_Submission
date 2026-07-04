# Deadline-Driven Dynamics in Pre-Match Betting Exchange Prices

Microstructure evidence and regime-adaptive filtering for pre-match football betting exchange prices.

This repository contains the analytical pipeline for an MSc Data Science dissertation studying pre-match Betfair exchange prices as finite-horizon, bounded, asynchronously-quoted markets. The code reproduces every reported table, figure, and model summary from a single input file (`market_data.csv`).

## Overview

The analysis treats the pre-match window as an evolving limit-order-book environment whose trading conditions and measured cross-market dependence change systematically with time-to-kick-off. It is organised as four ordered notebooks, each addressing one part of the analysis:

| Notebook | Focus | Main outputs |
|----------|-------|--------------|
| `01_microstructure_analysis` | Limit-order-book diagnostics over event time (spreads, quote intensity, volatility) | Microstructure figures and tables |
| `02_correlation_analysis` | Cross-market dependence: Epps curve, lead-lag cross-correlation, staleness audit | Table 5.1, Table 5.5, Epps and lead-lag figures |
| `03_cointegration_analysis` | Unit-root and cointegration stress tests (clock-time vs tick-time; deadline trend) | Tables 5.2–5.4, rolling-beta figure |
| `04_hmm_kalman_analysis` | Three-state HMM regimes and a regime-adaptive switching Kalman filter | Tables 5.5–5.6, regime and forecast figures |

## Repository structure

```
.
├── README.md
├── requirements.txt
├── LICENSE
├── .gitignore
├── notebooks/
│   ├── 01_microstructure_analysis.ipynb
│   ├── 02_correlation_analysis.ipynb
│   ├── 03_cointegration_analysis.ipynb
│   └── 04_hmm_kalman_analysis.ipynb
├── data/
│   └── raw/
│       └── market_data.csv        # NOT included — see "Data" below
└── outputs/                       # created automatically when notebooks run
    ├── 01_microstructure_analysis/
    ├── 02_correlation_analysis/
    ├── 03_cointegration_analysis/
    └── 04_hmm_kalman_analysis/
```

Each notebook writes its figures (PNG and PDF), tables (CSV and LaTeX), and a `run_metadata.json` file to its own subdirectory under `outputs/`.

## Data

**The raw data is not included in this repository.** The analysis uses pre-match best-quote updates for football markets from the Betfair exchange. Under Betfair's terms, raw historical market data cannot be redistributed, so this repository publishes only the analytical pipeline. Reproducing the results requires supplying an equivalently formatted `market_data.csv` from authorised data.

The data-acquisition step (converting the raw Betfair feed into `market_data.csv`) is not part of this repository. The notebooks run from `market_data.csv` onward.

### Expected input file

Place a file named `market_data.csv` in `data/raw/`. The notebooks require the following columns:

| Column | Type | Description |
|--------|------|-------------|
| `event_id` | integer/string | Unique identifier for a match |
| `ts_utc` | datetime (UTC) | Timestamp of the quote update |
| `market_time` | datetime (UTC) | Scheduled kick-off time for the event |
| `market_name` | string | Market name (see below) |
| `selection_name` | string | Selection within the market (see below) |
| `best_back_price` | float | Best available back price (decimal odds) |
| `best_lay_price` | float | Best available lay price (decimal odds) |

Each row is one best-quote update for one selection in one market of one event. Timestamps must parse as UTC. Rows where `ts_utc` is on or after `market_time` are treated as in-play and dropped; the analysis uses the final 360 minutes before kick-off.

### Markets and selections used

The cross-market analysis uses three core markets:

| `market_name` | Selections used (`selection_name`) |
|---------------|-------------------------------------|
| `Match Odds` | `The Draw` |
| `Over/Under 2.5 Goals` | `Over 2.5 Goals`, `Under 2.5 Goals` |
| `Both teams to Score?` | `Yes` |

Market and selection names must match these strings exactly (the code maps them to short codes MO, OU25, BTTS).

## Reproducing the analysis

### Requirements

- Python 3.9 or later
- Dependencies listed in `requirements.txt`

### Setup

```bash
# Clone the repository
git clone <repository-url>
cd <repository-name>

# (Recommended) create a virtual environment
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### Add the data

Place your `market_data.csv` in `data/raw/`. The notebooks expect the path `../data/raw/market_data.csv` relative to the `notebooks/` directory.

### Run

Run the notebooks in order, 01 through 04:

```bash
cd notebooks
jupyter notebook
```

Each notebook is self-contained: it loads `market_data.csv`, runs its analysis, and writes outputs to `outputs/<notebook_name>/`. A fixed random seed (42) is set in the first cell of each notebook for reproducibility.

Notebook 04 depends only on the same input file, not on the outputs of 01–03, so the notebooks can be run independently. Running them in order is recommended for a clean output set.

## Outputs

Each notebook produces:

- **Figures** in `outputs/<notebook>/figures/` as both PNG (300 dpi) and PDF.
- **Tables** in `outputs/<notebook>/tables/` as both CSV and LaTeX (`.tex`). The LaTeX files correspond to the numbered tables in the dissertation.
- **Run metadata** in `outputs/<notebook>/run_metadata.json` recording the timestamp, seed, Python version, and input file.

## Notes on reproducibility

- All random seeds are fixed. Numerical results are deterministic given the same input file and library versions.
- Cleaning rules (forward-filling the standing quote, treating zero prices as missing, the relative-spread filter) are applied inside the analysis code and described in the dissertation appendix.
- Cointegration tests use `statsmodels` `coint` (MacKinnon critical values) rather than a plain ADF on estimated residuals.

## Citation

If referring to this work, please cite the dissertation:

> Dye, C. (2026). *Deadline-Driven Dynamics in Pre-Match Betting Exchange Prices: Microstructure Evidence and Regime-Adaptive Filtering*. MSc Data Science dissertation, University of the West of England.

## License

See `LICENSE`. Note that the licence covers the code in this repository only; it does not extend to any Betfair data, which is subject to Betfair's own terms.
