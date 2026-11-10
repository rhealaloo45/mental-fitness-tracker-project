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

## Known issue: the target column is mislabelled

The column rename in step 1 (`data.set_axis([...])`) assigns names by position, and the list is one place out. The DALYs column is named `Schizophrenia`, each disorder takes the next disorder's name, and the column used as the target, `mental_fitness`, actually holds alcohol-use-disorder prevalence. As saved, the models predict alcohol-use prevalence from the other columns, not the DALY share.

Until that is fixed, the scores in the notebook describe the wrong target, so they are not quoted here. The fix is to rename by column name instead of position, so that the DALYs column becomes `mental_fitness`, and then re-run the notebook.
