# SDS 336 Group Project

## Dataset
`data/raw/Crime_Reports_20261012.csv`: Austin Police Department crime reports, downloaded 2026-10-12.
- 2,676,798 incidents, 530 MB
- Occurred dates run from 2003 to 2026
- Columns: Incident Number, Highest Offense Description, Highest Offense Code, Family Violence,
  Occurred Date Time / Date / Time, Report Date Time / Date / Time, Location Type, Council District,
  APD Sector, APD District, Clearance Status, Clearance Date, UCR Category, Category Description,
  Census Block Group

## Layout
- `data/raw/`: original data (do not edit)
- `data/processed/`: cleaned or subsetted data
- `notebooks/`: analysis notebooks
  - `01_eda.ipynb`: exploratory analysis. The first run caches `data/processed/crime.parquet`
- `figures/`: saved plots

## Note
The raw CSV is too big for GitHub (100 MB limit). If you put this project in a repo, keep `data/` out of git.
