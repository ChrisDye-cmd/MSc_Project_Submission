# Data Schema: `market_data.csv`

This document describes the exact format the analysis notebooks expect. A
replicator supplying their own authorised Betfair data must format it to match
this schema. The raw data itself is not included in this repository (see the
README).

## File

A single CSV file named `market_data.csv`, placed in `data/raw/`.

Each row is one best-quote update: one selection, in one market, of one event,
at one point in time.

## Columns

| Column | Type | Required | Description |
|--------|------|----------|-------------|
| `event_id` | integer or string | Yes | Unique identifier for a single match/event. Used to group and to count events. |
| `ts_utc` | datetime (UTC) | Yes | Timestamp of the quote update. Must parse with `pandas.to_datetime(..., utc=True)`. |
| `market_time` | datetime (UTC) | Yes | Scheduled kick-off time of the event. Same for every row of a given `event_id`. |
| `market_name` | string | Yes | Name of the market (see allowed values below). |
| `selection_name` | string | Yes | Name of the selection within the market (see allowed values below). |
| `best_back_price` | float | Yes | Best available back price, in decimal odds. Zero or missing is treated as no quote. |
| `best_lay_price` | float | Yes | Best available lay price, in decimal odds. Zero or missing is treated as no quote. |

Additional columns may be present; they are ignored.

## Allowed market and selection names

The cross-market analysis (notebooks 02 and 03) uses three markets and the
following selections. These strings must match exactly.

| `market_name` | `selection_name` values used |
|---------------|------------------------------|
| `Match Odds` | `The Draw` |
| `Over/Under 2.5 Goals` | `Over 2.5 Goals`, `Under 2.5 Goals` |
| `Both teams to Score?` | `Yes` |

Notebook 01 (microstructure) uses the same markets; notebook 04 (HMM/Kalman)
derives its features from the Match Odds market.

The code maps the long market names to short codes:

| Market name | Short code |
|-------------|------------|
| `Match Odds` | `MO` |
| `Over/Under 2.5 Goals` | `OU25` |
| `Both teams to Score?` | `BTTS` |

## How the data is processed

The notebooks apply the following steps (see the dissertation appendix for full
detail):

1. **Parse and filter time.** Timestamps are parsed as UTC. Rows where `ts_utc`
   is on or after `market_time` (i.e. in-play) are dropped. The analysis keeps
   the final 360 minutes before kick-off (`tau` = minutes to kick-off).

2. **Build a standing-quote panel.** Ticks are pivoted wide (one column per
   market + selection) and resampled to bars (1-minute by default). The last
   quote in each bar is taken and forward-filled to represent the standing
   quote. Zero prices are set to missing *before* the forward-fill, so they are
   not carried forward as real quotes.

3. **Filter quotes.** A bar is kept only if both back and lay sides are present
   and the relative spread is below a threshold (default 20%).

4. **Transform.** The mid price is taken in odds space, converted to an implied
   probability, clipped just inside (0, 1), and transformed to log-odds-prob
   (the logit of the implied probability).

## Minimal example

```
event_id,ts_utc,market_time,market_name,selection_name,best_back_price,best_lay_price
1001,2015-08-08T13:00:00Z,2015-08-08T15:00:00Z,Match Odds,The Draw,3.45,3.50
1001,2015-08-08T13:00:00Z,2015-08-08T15:00:00Z,Over/Under 2.5 Goals,Over 2.5 Goals,1.95,1.98
1001,2015-08-08T13:00:05Z,2015-08-08T15:00:00Z,Match Odds,The Draw,3.46,3.51
...
```
