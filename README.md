# ML Bias and Fairness: Historical vs Recent Census Data

MSc dissertation project. A measurement-only fairness audit comparing the UCI Adult
Census Income dataset (1994) with ACS PUMS income data (2024), looking at how bias
patterns differ across a roughly thirty-year gap.

Two classifiers (Logistic Regression and Random Forest) are evaluated across five
fairness metrics and two protected attributes (sex and race), using Fairlearn's
MetricFrame. The project measures fairness rather than attempting mitigation.

## Structure

    notebooks/   analysis notebooks (run order below)
    data/        input data, fetched live on run (gitignored)
    results/     tables and figures, written on run (gitignored)

data/ and results/ are not committed. The UCI data is pulled via fetch_openml and the
ACS data via folktables, so data/ rebuilds itself on a run. results/ is regenerated the
same way, which is why neither is tracked.

## Notebooks

Run in order. The EDA notebooks (01 to 03) write summary tables that the modelling and
comparison notebooks read, so 01 and 02 must run before 03, and 04 and 05 before 06.

- 01_uci_adult_eda.ipynb       UCI Adult (1994): missingness, proxy attributes, income gaps by sex and race
- 02_acs_pums_eda.ipynb        ACS PUMS (2024), mirroring 01 so the two are comparable
- 03_comparison_eda.ipynb      cross-dataset EDA comparison; reads the tables 01 and 02 write
- 04_modelling_uci.ipynb       UCI modelling: three experiments, plus bootstrap CIs and an intersectional audit
- 05_modelling_acs_pums.ipynb  ACS modelling, mirroring 04, plus threshold-sensitivity and multi-seed robustness
- 06_comparison_results.ipynb  stacks the two datasets' results and reports the cross-dataset comparisons

Each modelling notebook runs three experiments on its dataset: a baseline with all
features, a naive condition with the protected attributes removed, and a condition with
the protected attributes and their strongest proxies removed. The proxies differ by
dataset, which is why the EDA is run per dataset before modelling.

## Running it

Developed on Python 3.12. Install the pinned dependencies:

    pip install -r requirements.txt

Then open the notebooks in notebooks/ and run each from a clean kernel, in order:
01 -> 02 -> 03 -> 04 -> 05 -> 06. The first run creates data/ and results/ at the repo
root. Every figure reported in the write-up is read from a generated CSV, so each number
traces back to the notebook that produced it.

## Data sources

- UCI Adult Census Income (1994), via sklearn.datasets.fetch_openml (version 2)
- ACS PUMS (2024, 1-Year), via the folktables package, ACSIncome task (Ding et al., 2021)

## Notes on environment

Developed and tested locally in VS Code on Python 3.12, with the pinned versions in
requirements.txt. The notebooks live in notebooks/ and use relative paths (../data/,
../results/) that assume this repository layout, so they are intended to be run from a
local clone rather than uploaded individually.

To run in Google Colab instead, two adjustments are needed: install the dependencies at
the top of the notebook (!pip install -r requirements.txt, or the individual packages),
and set the data and results paths to a location that exists in the Colab session, since
the ../ layout above will not be present. The data itself needs no manual download, as it
is fetched live (UCI Adult via fetch_openml, ACS PUMS via folktables).
