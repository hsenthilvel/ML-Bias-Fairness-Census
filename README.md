# ML Bias and Fairness: Historical vs Recent Census Data

MSc dissertation project. A measurement-only fairness audit comparing the UCI Adult
Census Income dataset (1994) with ACS PUMS income data (2024), looking at how bias
patterns differ across a roughly thirty-year gap.

Two classifiers (Logistic Regression and Random Forest) are evaluated across five
fairness metrics and five performance metrics on two protected attributes (sex and
race), using Fairlearn's MetricFrame. The project measures fairness rather than
attempting mitigation.

## Structure

    notebooks/   analysis notebooks (run order below)
    data/        input data, fetched live on run (gitignored)
    results/     tables and figures, written on run (gitignored)

data/ and results/ are not committed. The UCI data is pulled via fetch_openml and the
ACS data via folktables, so data/ rebuilds itself on a run. results/ is regenerated the
same way, which is why neither is tracked.

## Notebooks

Currently in the repo (EDA stage):

- 01_uci_adult_eda.ipynb  — UCI Adult (1994): missingness, proxy attributes, income gaps by sex and race
- 02_acs_pums_eda.ipynb   — ACS PUMS (2024), mirroring notebook 01 so the two are comparable
- 03_comparison_eda.ipynb — cross-dataset comparison; reads the summary tables 01 and 02 write

01 and 02 stand alone. 03 depends on the CSVs they export to results/tables/, so run
01 and 02 first, then 03.

The modelling notebooks (04-06) are part of the same pipeline and will be added once
that stage is finalised.

## Running it

Developed on Python 3.12. Install the pinned dependencies:

    pip install -r requirements.txt

Then open the notebooks in notebooks/ and run each from a clean kernel, in order:
01 -> 02 -> 03. The first run creates data/ and results/ at the repo root.

## Data sources

- UCI Adult Census Income (1994), via sklearn.datasets.fetch_openml (version 2)
- ACS PUMS (2024, 1-Year), via the folktables package, ACSIncome task (Ding et al., 2021)
