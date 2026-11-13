# Mental fitness tracker

Regression models that estimate a country's mental-health burden for a given year, using the prevalence of seven mental and substance-use disorders. The data covers 228 countries and regions from 1990 to 2019. The notebook merges two country-year datasets, explores them, and trains a linear regression and a random forest regressor.

Built during the AICTE – IBM SkillsBuild internship (May–July 2023). The completion certificate is [`learning plan SkillsBuild.pdf`](learning%20plan%20SkillsBuild.pdf).

## Data

Both CSVs are in the repo and have one row per country and year (6,840 rows each, 1990–2019):

| File | What it holds |
|---|---|
| `mental-and-substance-use-as-share-of-disease.csv` | Mental disorders as a share of total disease burden (DALYs, %) |
| `prevalence-by-mental-and-substance-use-disorder.csv` | Age-standardised prevalence (%) of schizophrenia, bipolar disorder, eating disorders, anxiety, drug use, depression and alcohol use disorders |

## How it works

All of this is in [`Mental fitness tracker.ipynb`](Mental%20fitness%20tracker.ipynb):

1. **Merge** the two tables on country and year to get 6,840 rows. Drop the ISO-code column, which is empty for regions.
2. **Explore** with a correlation heatmap, a per-country Plotly line chart over time, and a breakdown by year.
3. **Encode** the country name with `LabelEncoder`. Year stays as a number.
4. **Train** on an 80/20 split (`random_state=2`): `LinearRegression` and `RandomForestRegressor`, each scored with MSE, RMSE and R².

## Stack

Python, pandas, NumPy, scikit-learn, Matplotlib, Seaborn, Plotly, Jupyter.

## Run it

```bash
pip install pandas numpy scikit-learn matplotlib seaborn plotly jupyter
jupyter notebook "Mental fitness tracker.ipynb"
```

The notebook reads both CSVs from the repo folder, so run it from there.

## Results

The target is `mental_fitness`: mental disorders as a share of total disease burden (DALYs, %). Scores from the saved run of the notebook, on the 20% held-out rows:

| Model | RMSE | R² |
|---|---|---|
| Random forest | 0.18 | 0.993 |

(The linear regression was only scored on its training rows: R² 0.74, RMSE 1.18.)

Read the 0.993 with care. The split is random across country-years, so the same country's neighbouring years sit in both the training and the test set, and a country's burden barely changes from one year to the next. The score shows the model interpolates well within countries it has seen; it says little about forecasting a future year or an unseen country. A fairer test would hold out the latest years, or whole countries.

## Fixed: the target column was mislabelled

An earlier version of the notebook named the merged columns by position, and the list was one place out: the DALYs column was called `Schizophrenia`, each disorder took the next one's name, and the target `mental_fitness` actually held alcohol-use prevalence. The columns are now named in the order the merge produces them, so `mental_fitness` is the DALY share, and the notebook was re-run end to end (it also needed small changes for current pandas: text columns are encoded by "not numeric" rather than `dtype == 'object'`, the correlation uses `numeric_only=True`, and one stray cell that ran before its data existed was removed).
