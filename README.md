
# Causal-Oriented Evaluation of Marketing Campaign Channel Performance
### Observational Comparison and Regression Adjustment

![Page1-Executive_Overview](reporting/Page%201-Executive%20Overview.jpg)

![Page2-Forest_Plot](reporting/Page%202-Forest%20Plot.jpg)

![Page3-Timing](reporting/Page%203-Timing.jpg)

## Purpose

This project evaluates channel performance in a direct marketing campaign using observational
data, in which contact method was recorded rather than randomly assigned.

A raw conversion-rate gap cannot be read as a channel effect here, because channel allocation is
correlated with both customer characteristics and campaign timing. The project asks four
questions:

1. How large and how precise is the raw conversion gap between cellular and telephone contact?
2. How much of that gap survives once observable customer and macroeconomic differences are
   controlled?
3. Is the remaining association stable across campaign months, or concentrated in particular
   periods?
4. What can and cannot be recommended for future channel decisions on this evidence?

**Primary KPI**
* Conversion rate (binary outcome)


## Dataset

Portuguese Bank Marketing Dataset (UCI Machine Learning Repository)

* ~41,000 observations
* Direct marketing campaign outcomes
* Customer demographics and campaign features

**Target variable**
* `y` (conversion: yes/no)

**Key explanatory variables**
* contact method
* month (campaign timing)
* customer characteristics (age, job, marital, education, loan status, etc.)
* previous campaign interactions
* Macroeconomic indicators

**Excluded variable**
* `duration` (call length) is dropped. It is only known after a call ends, so it cannot inform
  prospective decisions. The UCI documentation flags this explicitly.


## Process

### Step 1. Unadjusted Two-Group Comparison (notebook 02)

* Computed conversion rate, absolute lift (percentage points) and relative lift by contact method
* Conducted a two-proportion z-test (H0: p_cellular = p_telephone, two-sided)
* Computed 95% confidence intervals for each group rate and for the difference between groups
* Recorded the group allocation (26,144 cellular / 15,044 telephone) as an observed property of
  the data, not as a design parameter

**Purpose**
Establish the size and statistical precision of the baseline gap before any adjustment, so that
the effect of adjustment in Step 2 can be measured against it.

**What this step does not establish**
Causality. A two-proportion z-test answers whether the difference exceeds sampling noise. It does
not identify the channel as the cause. Because contact method was not randomly assigned, the gap
at this stage still mixes the channel effect with customer selection and campaign timing.

### Step 2. Logistic Regression with Confounder Adjustment (notebook 03)

**Model specification**
`conversion ~ contact + age + job + loan + previous + macroeconomic variables`

* Estimated the adjusted odds ratio for contact method, holding observable confounders constant
* Compared the adjusted estimate against the unadjusted odds ratio, to quantify how much of the
  raw gap is explained by group composition
* Reported the odds ratio with its 95% confidence interval and p-value
* The channel indicator enters the model unscaled, so its exponentiated coefficient reads
  directly as the telephone-to-cellular odds ratio

**Purpose**
Test whether the channel association survives adjustment for customer heterogeneity and
macroeconomic conditions, and measure how much of the raw gap that adjustment absorbs.

**Assumption being made**
Conditional exchangeability: that the measured covariates capture the differences between the two
groups that also affect conversion. This is an assumption, not a verified fact. Unmeasured
confounders remain possible, so the adjusted estimate is read here as an association net of what
was measured (see Limitations).

### Step 3. Timing Heterogeneity (Contact x Month Interaction, notebook 04)

**Motivation**
The dataset provides no explicit channel policy change timestamp, so this step does not test an
intervention. It asks a narrower question: does the channel association hold across campaign
timing, or is it concentrated in particular months?

**Model**
`conversion ~ contact + C(month) + contact x C(month) + controls`

* Estimated month-specific deviations from the baseline channel effect
* Reported each month's odds ratio together with its 95% confidence interval
* Checked how channel allocation is distributed across months

**Purpose**
Serve as a robustness check on Step 2, and locate where in the campaign calendar the association
is concentrated.

**Allocation caveat**
Channel and campaign timing are heavily entangled: 84.7% of telephone contacts fall in May and
June, against 24.2% of cellular contacts. The interaction model also excludes macroeconomic
controls because they are collinear with month. Month-level estimates therefore absorb whatever
else changed in that month, and are read as heterogeneity to be explained rather than as
month-specific channel effects.


## Findings

* **The raw gap is large and precisely estimated.** Cellular converts at 14.74% against 5.23% for
  telephone: +9.51 percentage points (95% CI 8.95-10.07), z = 29.38, p < 0.001. This is a
  description of the data, not a channel effect.
* **Adjustment absorbs about a third of the gap.** The unadjusted odds ratio of 3.13 falls to
  2.16 (95% CI 1.87-2.49) once customer and macroeconomic covariates are controlled. The size of
  that shift is the footprint of non-random channel allocation.
* **The estimate is not sensitive to specification.** A second model with a different control set
  and no macroeconomic variables (notebook 04) gives an average adjusted odds ratio of 2.33
  (95% CI 2.14-2.53), close to the 2.16 from notebook 03.
* **The association is concentrated in time, not uniform.** Four of ten months show intervals
  excluding 1 (May 3.19, June 8.70, July 1.82, September 3.11); the remaining six do not. June
  carries the largest estimate (95% CI 7.00-10.81), but June is also where the two channels
  overlap least, so month-level figures are reported as heterogeneity, not as month-specific
  channel effects.


## Outputs

| Notebook | What it does | Exports (`data/powerbi_exports/`) | Power BI page |
|---|---|---|---|
| `01_data_preparation` | Cleaning and preprocessing | `preprocessed-bank-data.csv` (not tracked) | - |
| `02_unadjusted_channel_comparison` | Rates, lift, z-test, confidence intervals | `channel_kpi.csv`, `channel_comparison_meta.csv` | Page 1: Executive Overview |
| `03_logistic_regression` | Adjusted odds ratio (GLM), interaction check | `glm_main_coefficients.csv`, `glm_interaction_coefficients.csv` | Page 2: Forest Plot |
| `04_timing_effect_analysis` | Contact x month interaction, monthly ORs | `timing_monthly_or_ci.csv`, `monthly_channel_conversion.csv` | Page 3: Timing |

The Power BI report lives in `visuals/`; its three pages are exported as JPGs in `reporting/` and
shown at the top of this README.


## Business Implications

* Treat the adjusted estimate (OR 2.16), not the raw gap (OR 3.13), as the planning number for
  channel budget allocation.
* Validate performance differences with regression-based controls before acting on raw
  comparisons.
* Where a channel decision carries real budget, run a randomised holdout with contemporaneous
  exposure of both channels rather than relying on historical logs.


## Limitations

* Observational dataset (not true randomised experiment)
* Interaction model assumes stable baseline differences across timing segments
* Potential unobserved confounders
* The dataset does not provide longitudinal indexing across years, preventing formal
  Difference-in-Differences implementation.
* Macroeconomic controls are collinear with month, so the interaction model cannot separate
  month-specific channel effects from month-specific conditions.

**Future Improvements**

* Instrumental variable analysis
* Propensity score matching
* Robustness checks with cross-validation
* Causal modelling with clearly defined intervention events


## Tech Stack

Python

Pandas

Statsmodels

Scikit-learn

Matplotlib / Seaborn

Power BI


## Data Access

The dataset used in this project is provided by UCI for educational purposes.

Due to licensing restrictions, the raw dataset is not included in this repository.

To reproduce the analysis:
1. Download the dataset from the UCI Machine Learning Repository.
2. Place the CSV file inside the `/data` directory.
3. Run the notebooks in sequential order.

This project is intended for analytical demonstration purposes only.
