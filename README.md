<p align="center">
  <img src="assets/austin_logos.png" alt="City of Austin logo and seal" height="160">
  &nbsp;&nbsp;&nbsp;
  <img src="assets/apd_badge.png" alt="Austin Police Department badge" height="160">
</p>

# SDS 336 Group Project: Austin Crime Reports

Analysis of Austin Police Department crime reports from 2003 to 2026, for SDS 336 at UT Austin.
This is a student project and is not affiliated with or endorsed by the City of Austin or APD.

## Dataset
[Crime Reports](https://data.austintexas.gov/Public-Safety/Crime-Reports/fdj4-gpfu), City of Austin Open Data Portal (public domain). This snapshot was downloaded 2026-10-12.

- 2,676,798 incidents (one row per incident), 530 MB CSV
- Incidents occurred from Jan 1, 2003 to Oct 3, 2026 (2026 is a partial year)
- Columns: offense description and code, family-violence flag, when the incident occurred and was reported,
  location type, council district, APD sector and district, clearance status and date, UCR category, and census block group

## Setup
The raw data is too big for GitHub, so each person downloads it separately.

1. Clone the repo:
   ```bash
   git clone https://github.com/palashpawar/sds336-group-project.git
   cd sds336-group-project
   ```
2. Download the CSV from the portal (Export → CSV) and save it as `data/raw/Crime_Reports_20261012.csv`.
   A newer export will have a different filename. Either rename it, or change `RAW` in the notebook's first code cell.
3. Install the dependencies and open the notebook:
   ```bash
   pip install -r requirements.txt
   jupyter notebook notebooks/01_eda.ipynb
   ```
   The first run reads the CSV (about 30 seconds) and caches a cleaned copy at `data/processed/crime.parquet`. Later runs load the cache, which is much faster.

## Layout
```
data/raw/          original CSV (not in git; do not edit)
data/processed/    cleaned / cached data (not in git)
notebooks/
  01_eda.ipynb     exploratory analysis
figures/           charts saved by the notebooks
```

## EDA highlights
See the end of [`01_eda.ipynb`](notebooks/01_eda.ipynb) for the full takeaways.

- **Reported incidents fell 38%** from the 2008 peak (143k) to 2025 (89k). Auto theft roughly tripled from 2019 to 2023, and murder spiked in 2021.
- **Clearance coding changed around 2009.** Use 2009 and later for anything about whether cases are cleared (solved).
- **Clearance depends heavily on crime type** (2009 onward): murder 94%, aggravated assault 55%, rape and robbery 38% each, and theft, burglary and auto theft 10-13%.
- **Occurrence times are often placeholders.** 26% of incidents are logged exactly on the hour.
- **UCR category exists only for Part I crimes** (37% of rows). Theft makes up 69% of those.

## Possible project questions
1. Predict whether a case is cleared (classification, 2009 onward)
2. Predict a Part I incident's category from when, where and how quickly it was reported
3. Forecast incident counts by APD sector and week
4. Cluster census block groups by their crime mix
5. Does reporting delay predict clearance?

## Contributing
Work on a branch, keep notebooks numbered (`02_...`, `03_...`), and don't commit anything in `data/`.
