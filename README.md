<p align="center">
  <img src="assets/banner.png" alt="Austin Police Department badge, City of Austin logo and seal" width="720">
</p>

# SDS 336 Group Project: Austin Crime Reports

**📊 [View the midterm checkpoint slides](https://palashpawar.github.io/sds336-group-project/)**

Analysis of Austin Police Department crime reports from 2003 to 2026, for SDS 336 at UT Austin.
This is a student project and is not affiliated with or endorsed by the City of Austin or APD.

## Team
Palash Pawar · Karthik Kasa · Varun Bandaru · Sai Koduri · Vinay Devarakonda · Saisarath Bolneni

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
  01_eda.ipynb          exploratory analysis (run first; builds the parquet cache)
  02_hypotheses.ipynb   midterm checkpoint: five data-backed hypotheses
  03_modeling.ipynb     final project: predicting case clearance
figures/           charts saved by the notebooks (final/ = final project)
docs/              midterm slides, served by GitHub Pages
assets/            images used in this README
```

## EDA highlights
See the end of [`01_eda.ipynb`](notebooks/01_eda.ipynb) for the full takeaways.

- **Reported incidents fell 38%** from the 2008 peak (143k) to 2025 (89k). Auto theft roughly tripled from 2019 to 2023, and murder spiked in 2021.
- **Clearance coding changed around 2009.** Use 2009 and later for anything about whether cases are cleared (solved).
- **Clearance depends heavily on crime type** (2009 onward): murder 94%, aggravated assault 55%, rape and robbery 38% each, and theft, burglary and auto theft 10-13%.
- **Occurrence times are often placeholders.** 26% of incidents are logged exactly on the hour.
- **UCR category exists only for Part I crimes** (37% of rows). Theft makes up 69% of those.

## Midterm checkpoint hypotheses
**Slides:** https://palashpawar.github.io/sds336-group-project/ · Full analysis in [`02_hypotheses.ipynb`](notebooks/02_hypotheses.ipynb).

| # | Hypothesis | Key evidence | Method |
|---|---|---|---|
| H1 | Reporting delay lowers clearance beyond crime type | Odds ratio 0.42 per 10x longer delay, offense held fixed | Logistic regression |
| H2 | A time rounded to `HH:00` marks an unwitnessed crime, which is cleared less often | Lower clearance in 10/10 offenses; odds ratio 0.61 after controlling for offense and delay | Anomaly analysis + logistic regression |
| H3 | The post-2017 clearance decline is mostly within offenses, not a change in crime mix | 59% of the 2009-2021 drop remains with the mix held fixed | Direct standardization |
| H4 | Family violence rises on holidays while other crime falls | Christmas: family violence +38%, other incidents -29% | Linear regression with fixed effects |
| H5 | Neighborhoods vary along a commercial-to-domestic crime axis that relates to clearance | PC1 = 22% of variance; cluster clearance 13-23% | K-means + PCA |

Tested and not supported: a family-violence spike during the 2020 lockdown (+2%, within noise).

## Final project: predicting which cases get cleared
Full analysis in [`03_modeling.ipynb`](notebooks/03_modeling.ipynb).

- **Task:** binary classification. Will a victim-reported incident be cleared (arrest or exception), using only information known at intake?
- **Data:** 1.42M incidents, 2009-2025. We drop 39 arrest-generated offenses (DWI, warrant arrests and similar), which are cleared 90-99% of the time by definition.
- **Split by time:** train 2009-2019, tune on 2020-2021, test once on 2022-2025.
- **Models:** an offense-only baseline, logistic regression, random forest and gradient boosting, each tuned on the validation years.

| Model (test 2022-25) | PR-AUC | Top-20% capture | Within-offense AUC |
|---|---|---|---|
| Offense-only baseline | 0.446 | 56% | 0.500 |
| Logistic regression | 0.521 | 65% | 0.727 |
| Random forest | 0.552 | 65% | 0.731 |
| **Gradient boosting** | **0.565** | **67%** | **0.735** |

**Key findings:**
- After crime type, **reporting delay** matters most. Predicted clearance drops sharply between 4 and 8 hours after the incident.
- **Family-violence** cases have about 2.9× the odds of clearance for the same offense.
- A **random split overstates performance**: PR-AUC is 0.70 with shuffled years vs. 0.57 on future years.
- The model **ranks cases well, but its probabilities run high** on recent years, so they need recalibration before use.

## Possible project questions
1. Predict whether a case is cleared (classification, 2009 onward)
2. Predict a Part I incident's category from when, where and how quickly it was reported
3. Forecast incident counts by APD sector and week
4. Cluster census block groups by their crime mix
5. Does reporting delay predict clearance?

## Contributing
Work on a branch, keep notebooks numbered (`02_...`, `03_...`), and don't commit anything in `data/`.
