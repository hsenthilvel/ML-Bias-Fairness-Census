# ML Bias and Fairness in Historical and Recent Census Data

MSc dissertation project. A measurement-only fairness audit that identifies and
characterises demographic bias in the UCI Adult Census Income dataset (1994), then
applies the same pipeline to ACS PUMS income data (2024) as a secondary comparison, to
see whether the measured patterns also appear in a more recent dataset. The primary
focus is detecting and measuring bias in the historical benchmark; the 1994-to-2024
comparison is an extension rather than a longitudinal study of bias over time.

Two classifiers (Logistic Regression and Random Forest) are evaluated across five
fairness metrics (Statistical Parity Difference, Disparate Impact, Equal Opportunity
Difference, Equalised Odds and Predictive Parity) and two protected attributes (sex and
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

Developed on Python 3.11 with the pinned dependencies in requirements.txt. The pins are
what matter for reproducibility; the code is not tied to a single interpreter version and
also runs cleanly on 3.12 and 3.13. Install with:

    pip install -r requirements.txt

Then open the notebooks in notebooks/ and run each from a clean kernel, in order:
01 -> 02 -> 03 -> 04 -> 05 -> 06. The first run creates data/ and results/ at the repo
root. Every figure reported in the write-up is read from a generated CSV, so each number
traces back to the notebook that produced it.

To re-run the whole pipeline headlessly from a clone, from inside notebooks/:

    for nb in 01_uci_adult_eda 02_acs_pums_eda 03_comparison_eda \
              04_modelling_uci 05_modelling_acs_pums 06_comparison_results; do
        jupyter nbconvert --to notebook --execute --inplace \
            --ExecutePreprocessor.timeout=600 "$nb.ipynb"
    done

A clean-clone run of this pipeline reproduces the tables and figures reported in the
dissertation.

## Data sources

- UCI Adult Census Income (1994), via sklearn.datasets.fetch_openml (version 2)
- ACS PUMS (2024, 1-Year), via the folktables package, ACSIncome task (Ding et al., 2021)

## Running in Google Colab

The repository also runs in Colab from a clean clone, with no path changes needed. In a
notebook cell:

    !git clone https://github.com/hsenthilvel/ML-Bias-Fairness-Census.git
    %cd ML-Bias-Fairness-Census
    !pip install -r requirements.txt

Installing the pinned versions upgrades some of Colab's preinstalled packages, so after
the install use Runtime -> Restart session once, then continue. Change into the notebooks
folder before running so the relative paths (../data/, ../results/) resolve:

    %cd /content/ML-Bias-Fairness-Census/notebooks

The notebooks can then be run from the file browser, or headlessly with the nbconvert
loop above. The data needs no manual download; it is fetched live (UCI Adult via
fetch_openml, ACS PUMS via folktables). Note that the ACS pull in notebook 02 downloads
the national file before subsampling, which is the slowest step and the most
memory-intensive.
