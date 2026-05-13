# Pistachio Tide Modeling

This project develops a reproducible workflow to detect and model pistachio tide events—periods of low dissolved oxygen combined with abnormal turbidity and chlorophyll patterns—using:

High‑frequency water quality sensor data

Daily weather observations

Feature engineering at both interval and daily scales

Random Forest classification with time‑series cross‑validation

The goal is to understand environmental drivers of pistachio tide events and evaluate whether short‑term prediction is feasible.

## Repository Structure
- notebooks/ — clean Jupyter notebook
- src/ — Python pipeline
- figures/ — plots
- data/ — empty (raw data not included)
- ## Data Sources

### Weather Data
- Source 1: Weather Underground (Wunderground) — Baltimore/Washington International Airport (KBWI).
  - URL used at time of download: https://www.wunderground.com/history/monthly/us/md/linthicum-heights/KBWI/date/2025-5
  - Variables: Daily atmospheric measurements (temperature, dew point, humidity, wind, precipitation, pressure).
  - Notes: Wunderground provides historical daily summaries but station metadata may change over time.

- Source 2: Global Historical Climatology Network (GHCN) — Daily.

  - URL used: https://www.ncei.noaa.gov/cdo-web/search
  - Station originally used: CATONSVILLE 1.2 NW, MD US (Station ID: GHCND:US1MDBL0039).
  - Variables: Daily precipitation.
  - Notes: This station is inland and not ideal for Inner Harbor modeling.

- Recommended Station (for future work):
  - Maryland Science Center (Station ID: USW00093784).
  - Reason: This station is located directly in Baltimore’s Inner Harbor and better represents local microclimate conditions.


### Water Quality Data (15-minute intervals)
- Source: Maryland Department of Natural Resources (DNR) — Eyes on the Bay program.
- Station: Patapsco River, Aquarium East Surface.
- Variables: Dissolved oxygen (DO), turbidity, chlorophyll, pH, salinity, temperature.
- Sampling Frequency: 15-minute intervals.
- Notes: The Eyes on the Bay website has since changed, but the data was originally downloaded from their public monitoring portal.
- Access: Raw files stored locally in the `data` folder (not included in this repository).

## Data Availability
The raw weather and water quality data used in this project are not included in this repository due to size limits and data-sharing restrictions. The `data` folder is intentionally left empty in the public GitHub version. All raw files are stored locally for analysis and can be provided upon request by the PI.


## Visual Workflow
Raw Weather Data  ───┐
                     │
                     ▼
             Clean & Standardize
                     │
                     ▼
            Daily Weather Dataset
                     │
                     │
Raw Water Data ──────┘
                     ▼
             Clean & Standardize
                     │
                     ▼
     Interval-Level Pistachio Event Flag
                     │
                     ▼
     ┌───────────────────────────────────────┐
     │  Path A: Simple Daily Model           │
     │  - Collapse to daily event flag       │
     │  - Merge with weather                 │
     └───────────────────────────────────────┘
                     │
                     ▼
     ┌───────────────────────────────────────┐
     │  Path B: Advanced Lagged Model        │
     │  - Daily feature engineering          │
     │  - 1-day lag features                 │
     │  - Merge with weather                 │
     └───────────────────────────────────────┘
                     │
                     ▼
              Modeling Pipeline
       - Imputation
       - TimeSeriesSplit
       - Random Forest
       - Evaluation
       - Feature Importance

