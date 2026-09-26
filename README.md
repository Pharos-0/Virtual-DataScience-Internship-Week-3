# Virtual DataScience Internship - Week 3

## Statistical Analysis and Hypothesis Testing in Python

Week 3 of the Virtual Data Science with Python Apprentice Internship. The three
questions raised by the Week 2 visual story are tested formally, with test
statistics, p-values, confidence intervals and effect sizes.

| Week | Repository |
|---|---|
| 1 | [Virtual-DataScience-Internship-Week-1](https://github.com/Pharos-0/Virtual-DataScience-Internship-Week-1): data acquisition, cleaning and EDA |
| 2 | [Virtual-DataScience-Internship-Week-2](https://github.com/Pharos-0/Virtual-DataScience-Internship-Week-2): visualization and storytelling |
| **3** | **Hypothesis testing (this repository)** |
| 4 | [Virtual-DataScience-Internship-Week-4](https://github.com/Pharos-0/Virtual-DataScience-Internship-Week-4): machine learning |
| 5 | [Virtual-DataScience-Internship-Week-5](https://github.com/Pharos-0/Virtual-DataScience-Internship-Week-5): final project |

## Dataset

The cleaned **IBM Telco Customer Churn** dataset from Week 1 (7,043 customers; checksum verified on
load). Original source: [IBM/telco-customer-churn-on-icp4d](https://github.com/IBM/telco-customer-churn-on-icp4d)
(Apache 2.0).

## Hypotheses and methods (alpha = 0.05, two-sided)

| | Alternative hypothesis (H1) | Test | Checks |
|---|---|---|---|
| **A** (primary) | Churn is associated with contract type | Pearson chi-square test of independence; Cramer's V; Wilson CIs; pairwise z-tests with Newcombe CIs (Holm) | Expected counts >= 5; adjusted residuals |
| A (adjusted) | The association holds within tenure bands | Cochran-Mantel-Haenszel test; Mantel-Haenszel pooled odds ratio | Breslow-Day homogeneity test |
| **B** | Churned and retained customers differ in mean monthly charge | Welch's t-test; Hedges' g | Q-Q plots, D'Agostino, Levene/Brown-Forsythe, IQR outliers; Mann-Whitney U and bootstrap CI as robustness checks; tests within each internet service (Holm) |
| **C** | Churn differs with/without OnlineSecurity and TechSupport (internet customers) | Two-proportion z-tests with Newcombe CIs (Holm) | - |

## Key results

| Test | Result | Effect size |
|---|---|---|
| A: chi-square | chi2(2) = 1184.6, p = 5.86e-258 -> reject H0 | Cramer's V = 0.41 |
| A: CMH (tenure-adjusted) | chi2(1) = 482.8, p = 5.37e-107 -> reject H0 | pooled OR = 7.11 (95% CI 5.87-8.61) |
| B: Welch t-test | t(4136) = 18.41, p = 8.59e-73 -> reject H0 | +$13.18 (95% CI $11.77-$14.58); Hedges' g = 0.45 |
| B: Mann-Whitney U | p = 3.31e-54 | rank-biserial r = 0.24 |
| C: OnlineSecurity | z = 20.86, Holm p = 2.58e-96 | 27.2 pp (95% CI 24.9-29.4) |
| C: TechSupport | z = 20.38, Holm p = 2.36e-92 | 26.5 pp (95% CI 24.2-28.7) |

- Month-to-month customers churned at 42.7% (95% CI 41.2-44.3%), against 11.3% and 2.8% for one- and
  two-year contracts. The association remains after controlling for tenure and is strongest in the
  first year.
- Churned customers pay $13.18 more per month on average (a small-to-medium effect). Within each
  internet service they pay *less*, so the overall gap reflects service mix rather than price.
- With 7,043 customers even small differences are significant, so the effect sizes and intervals
  carry the practical message. These are associations, not causal effects.

## Repository structure

```
|-- README.md
|-- requirements.txt
|-- data/
|   |-- README.md
|   `-- processed/telco_churn_clean.csv     # Week 1 output (checksum verified)
|-- notebooks/
|   |-- Week_3_Hypothesis_Testing.ipynb     # executed notebook (same code as src/)
|   `-- build_notebook.py
|-- src/
|   |-- config.py  data_utils.py  plot_style.py
|   `-- 05_statistical_analysis.py
|-- outputs/
|   |-- figures/   # fig3_1 ... fig3_6 (supporting visualizations)
|   |-- tables/    # test results as CSV (incl. hypothesis_test_summary.csv)
|   `-- results/statistical_results.json    # every statistic, p-value, CI and effect size
`-- screenshots/   # 9 screenshots of the executed notebook
```

## How to reproduce

Tested with Python 3.11.9 (pandas 3.0.6, SciPy 1.17.1, statsmodels 0.15.0, Matplotlib 3.11.2).

```bash
python -m venv .venv
source .venv/bin/activate            # Windows: .venv\Scripts\activate
pip install -r requirements.txt

python src/05_statistical_analysis.py   # all tests, tables, figures and JSON results
python notebooks/build_notebook.py      # optional: rebuild and execute the notebook
```

The only random step is the bootstrap (10,000 resamples, seed 42), so every number is
reproducible. statsmodels computes the CMH p-value as 1 - CDF, which underflows to 0, so the
script recomputes it with `scipy.stats.chi2.sf`. p-values are stored at full precision.

## Notebook and screenshots

`notebooks/Week_3_Hypothesis_Testing.ipynb` is built from `src/05_statistical_analysis.py`
(Jupytext percent format) and executed, so it contains the same code. The images in
`screenshots/` are crops of the executed notebook.

## Limitations

- Observational, single-snapshot data: the tests show association, not causation.
- The CMH test adjusts for tenure only (in 12-month bands), so other variables may still confound.
- The data describes a fictional company (per IBM).

The written report for this week is submitted separately through the internship portal.
