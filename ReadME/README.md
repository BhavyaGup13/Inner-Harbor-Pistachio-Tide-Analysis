# Pistachio Tide Modeling

This project develops a reproducible workflow to detect and model pistachio tide events—periods of low dissolved oxygen combined with abnormal turbidity and chlorophyll patterns—using:

High‑frequency water quality sensor data

Daily weather observations

Feature engineering at both interval and daily scales

Random Forest classification with time‑series cross‑validation

The goal is to understand environmental drivers of pistachio tide events and evaluate whether short‑term prediction is feasible.

## Repository Structure
-Inner-Harbor.py
- README.md
- Weather data
## Data Sources

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


## Data Availability
The raw weather and water quality data used in this project are not included in this repository due to size limits and data-sharing restrictions. The `data` folder is intentionally left empty in the public GitHub version. All raw files are stored locally for analysis and can be provided upon request by the PI.


## Visual Workflow
1. Raw Weather Data  
→ Clean and standardize
→ Convert to daily dataset

2. Raw Water Quality Data  
→ Clean and standardize
→ Create interval‑level pistachio event flag (based on DO, turbidity, chlorophyll)

3. Daily Event Labeling  
→ Collapse interval events into a single daily label (1 = any event that day)

4. Path A: Simple Daily Model  
→ Use daily event label
→ Merge with daily weather data
→ Produce a simple daily modeling dataset

5. Path B: Advanced Lagged Model  
→ Compute daily summary features (min/mean/max DO, Chl, Turb, etc.)
→ Compute event metrics (fraction of time in event, number of clusters, longest run)
→ Create 1‑day lag features
→ Merge lagged water features with daily weather data
→ Produce the advanced modeling dataset

6. Modeling Pipeline
→ Impute missing values
→ TimeSeriesSplit for time‑aware train/test separation
→ Train Random Forest classifier
→ Evaluate using classification report and confusion matrix
→ Analyze permutation feature importance

## Future Improvements
- Develop graded (multi‑level) event labels  
Instead of a strict binary 0/1 label, create severity levels (e.g., mild, moderate, severe) based on DO, turbidity, and chlorophyll thresholds. This increases the number of positive samples and captures early warning signals that a binary label misses.

- Use cluster‑based labeling to discover natural event patterns  
Apply unsupervised methods (PCA + KMeans or hierarchical clustering) to interval‑level water quality data to identify groups of “event‑like” days without relying on hard thresholds. These clusters can then be used as alternative labels for modeling.

- Incorporate rolling‑window features  
Compute rolling minimums, maximums, means, and trends over 2‑hour, 6‑hour, 12‑hour, and 24‑hour windows. Rolling features capture the buildup and decay of environmental stress leading to pistachio tide events.

- Add trend‑based (derivative) features  
Include first‑ and second‑order derivatives of DO, turbidity, and chlorophyll to quantify the rate of change and acceleration. These trend features often act as early warning indicators for low‑oxygen events.

- Address extreme class imbalance
Explore oversampling (SMOTE, ADASYN), undersampling, or class‑balanced loss functions to improve recall on rare event days. Models like XGBoost or LightGBM also offer imbalance‑aware parameters.
