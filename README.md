# Predatory Pricing in Airlines

This repository analyzes pricing behavior and market dynamics in the US airline industry, focusing on competitive responses to Southwest Airlines' entry into Hawaiian inter-island routes. It combines public BTS (Bureau of Transportation Statistics) data on airline routes, fares, and market shares with fixed-effects econometric analysis and bootstrap confidence intervals.

## Features

- **Automated BTS data extraction** via Selenium web scraping for:
  - DB1B Market data (quarterly, 2019–2024)
  - DB1B Ticket data (quarterly, 2019–2024)  
  - T100D Schedule data (annual, 2005–2024)
  - T-F41 Financial data (annual, 2005–2024)
- **Panel data construction** merging routes, fares, capacity, and market structure variables
- **Event study regression** with fixed effects (time, market, state×COVID interactions)
- **Bootstrap confidence intervals** (1,000 replications with market-level clustering)
- **Robustness checks** including placebo tests and pre-trend tests (Wald, z-test, t-test equivalence)
- **Publication-ready visualizations** using Plotly (PNG + interactive HTML exports)

## Repository Structure

```
predatory_pricing_airlines/
├── code/
│   ├── data_extraction.ipynb           # BTS web scraping & ZIP extraction
│   ├── main_panel_creation.ipynb       # Data cleaning, merging, DuckDB setup
│   └── main_analysis.ipynb             # Event study, bootstrap, visualizations
├── data/
│   ├── DB1B/
│   │   ├── Market/                     # Origin-destination fares (quarterly)
│   │   │   ├── raw/
│   │   │   └── zip/
│   │   └── Ticket/                     # Ticket-level itineraries (quarterly)
│   │       ├── raw/
│   │       └── zip/
│   ├── T100/                           # Airline capacity/schedule (annual)
│   │   ├── raw/
│   │   └── zip/
│   ├── T-F41/                          # Financial statements (annual)
│   │   ├── raw/
│   │   └── zip/
│   ├── dict/
│   │   ├── L_CARRIERS.csv              # Carrier lookup table
│   │   └── routes.csv                  # Route ID mapping
│   └── duckdb/
│       └── airlines.duckdb             # Main analytical database
├── plots/                              # Output figures (PNG + HTML)
└── README.md
```

## Quickstart

### 1. Download Data from BTS

Open `data_extraction.ipynb` and execute cells in order:

- **T100D** (annual carrier schedule): Years 2005–2024
- **T-F41** (annual financial statements): Years 2005–2024
- **DB1B Market** (quarterly origin-destination survey): Quarters 2019 Q1–2024 Q4
- **DB1B Ticket** (quarterly ticket-level itinerary): Quarters 2019 Q1–2024 Q4

Each section downloads .zip files from transtats.bts.gov, extracts CSV files to `data/[DATASET]/raw/`, and logs progress.

**Note:** BTS downloads may take 4–6 hours.

### 2. Build Panel Database

Run `main_panel_creation.ipynb` to:

- Merge raw CSV files into DuckDB tables
- Filter valid routes (min 6 departures/quarter, >250 passengers)
- Create `final_panel` with Southwest entry dates, relative time periods, and covariates

**Output:** `airlines.duckdb` (used by analysis)

### 3. Run Econometric Analysis

Execute `main_analysis.ipynb` to:

- **Event study regression:** Fixed-effects model with market, time, and state×COVID interactions
- **Bootstrap:** 1,000 market-level cluster resamples with 95% confidence intervals
- **Robustness checks:**
  - Placebo test (visual inspection; no bootstrap)
  - Pre-trend tests (Wald, z-test, equivalence t-test)
- **Visualizations:** Price, cost, markup trends; event study plot with confidence bands

**Output:** PNG and interactive HTML files in `plots/`
