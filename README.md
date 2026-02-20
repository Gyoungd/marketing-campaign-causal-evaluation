# Casual Evaluation of Marketing Campaign Performance
### A/B Testing, Regression Adjustment, and Difference-in-Differences (DiD)

## Executive Summary
This project evaluates the effectiveness of a direct marketing campaign using strctured casual analysis techniques.

Rather than relying solely on naïve conversion rate comparisions, the analysis applies:

* A/B testing (proportion tests)
* Logistic regression with confounder adjustment
* Difference-in-Differences (DiD)

to estimate the **incremental impact** of communication channels and campaign timing on customer conversion.

The objective is to determine whether observed performance diffferences are statistically improve and casually defensible, supporting data-driven campaign optimisation decisions.


## Business Context
Marketing teams often compare campaign performance using raw conversion rates. However, such comparisions may be biased due to:

* Customer selection effects
* Channel allocation differences
* Timing confounders
* Lifecycle variation

This project address the following business questions:
1. Deos communication channel (cellular vs telephone) significantly affect conversion rate?
2. Is the observed difference robust after controlling for customer characteristics?
3. Does campaign timing produce incremental casual impact?
4. What strategic recommendation should be made for future campaigns?

**Primary KPI**
* Conversion rate (binary outcome)


## Dataset
Portuges Bank Marketing Dataset (UCI Machine Learning Repository)

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

### 3. Difference-in-Differences (DiD)

**Defined**

* Treatment group: Cellular contact
* Control group: Telephone contract
* Pre/Post period: Campaign timing segmentation

**Model**

conversion ~ treated + post + treated x post

The interaction term estimates incremental treatment impact under the parallel trends assumption.

**Purpose**

Assess whether timing effects create casual performance shifts.



## Key Findings

* Cellular contact shows higher baseline conversion rate compared to telephone.
* Regression-adjusted estimates confirm that channel effect remains statistically significant after controlling for demographic and behavioural variables.
* Difference-in-Differences analysis suggests measurable incremental impact associated with timing-channel interaction.
* Results highlight the importance of casual validation before large-scale rollout decisions.


## Business Implications

* Prioritise higher-performing communication channels for budget allocation.
* Validate performance differences using regression-based controls rather than raw comparison.
* Incorporate casual frameworks in campaign evaluatrion pipelines.
* Avoid decision-making based solely on descriptive statistics.


## Limitations

* Observational dataset (not true randomised experiment)
* Parallel trends assumption in DiD
* Potential unobserved confounders

**Future improvements**

* Instrumental variable analysis
* Propensity score matching
* Robustness checks with cross-validation
* Uplift modelling for individual-level treatment effect estimation


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

