# Causal-Oriented Evaluation of Marketing Campaign Performance A/B Testing and Regression Adjustment

## Executive Summary
This project evaluates the effectiveness of a direct marketing campaign using structured causal analysis techniques.

Rather than relying solely on naïve conversion rate comparisons, the analysis applies:

* A/B testing (proportion tests)
* Logistic regression with confounder adjustment

to estimate the association between communication channels and customer conversion, while accounting for observable confounders and timing heterogeneity.

The objective is to determine whether observed performance differences are statistically improved and causally defensible, supporting data-driven campaign optimisation decisions.


## Business Context
Marketing teams often compare campaign performance using raw conversion rates. However, such comparisons may be biased due to:

* Customer selection effects
* Channel allocation differences
* Timing confounders
* Lifecycle variation

This project address the following business questions:
1. Deos communication channel (cellular vs telephone) significantly affect conversion rate?
2. Is the observed difference robust after controlling for customer characteristics?
3. Does campaign timing moderate the association between contact method and conversion?
4. What strategic recommendation should be made for future campaigns?

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


## Methodology

### 1. Naïve A/B Testing
* Compared conversion rates by contact method
* Conducted two-proportion z-test
* Computed confidence intervals

**Purpose**
Establish baseline performance difference without adjustment.


### 2. Logistic Regression (Confounder Adjustment)

**Model specification**

conversion ~ contact + age + job + loan + previous + macro variables

**Objectives**

* Control for observable confounders
* Estimate adjusted channel impact
* Interpret odds ratios

This step evaluates whether channel performance remains significant after accounting for customer heterogeneity.

### 3. Timing Effect and Interaction Analysis

**Motivation**
While the dataset does not provide an explicit channel policy change timestamp, we assess whether channel performance varies across campaign timing segments. This serves as a robustness check to assess timing heterogeneity.

**Model**
` conversion ~ contact + C(month) + contact x C(month) + controls`

**Objective**

- Evaluate whether channel impact is stable across months
- Examine timing heterogeneity in campaign effectivness
- Ensure robustness of baseline and regression-adjusted findings

**Interpretation**
Interaction coefficients capture *month-specific deviations* from the baseline channel effect, allowing assessment of temporal variation without assuming a structural policy shift.

## Key Findings

* Cellular contact shows higher baseline conversion rate compared to telephone.
* Interaction-based analysis indicates that channel performance varies across campaign timing segments.
* Results highlight the importance of causal validation before large-scale rollout decisions.
* Regression-adjusted estimates (see [03_logistic_regression]('https://github.com/Gyoungd/marketing-campaign-causal-evaluation/blob/1116494bd7a23d33a51aa90144cf99a6e2dd6fe6/notebooks/03_logistic_regression.ipynb')) indicate a statistically significant positive association between cellular contact and conversion. However, timing heterogeneity analysis (see [04_timing_effect_analysis]('https://github.com/Gyoungd/marketing-campaign-causal-evaluation/blob/1116494bd7a23d33a51aa90144cf99a6e2dd6fe6/notebooks/04_timing_effect_analysis.ipynb')) suggests that this effect is not uniformly persistent across months.

## Business Implications

* Prioritise higher-performing communication channels for budget allocation.
* Validate performance differences using regression-based controls rather than raw comparison.
* Incorporate causal frameworks in campaign evaluation pipelines.
* Avoid decision-making based solely on descriptive statistics.


## Limitations

* Observational dataset (not true randomised experiment)
* Interaction model assumes stable baseline differences across timing segments
* Potential unobserved confounders
* The dataset does not provide longitudinal indexing across years, preventing formal Difference-in-Differences implementation.

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


## Data Access

The dataset used in this project is provided by StrataScratch for educational purposes.

Due to licensing restrictions, the raw dataset is not included in this repository.

To reproduce the analysis:
1. Download the dataset from the UCI Machine Learning Repository.
2. Place the CSV file inside the `/data` directory.
3. Run the notebooks in sequential order.

This project is intended for analytical demonstration purposes only.

