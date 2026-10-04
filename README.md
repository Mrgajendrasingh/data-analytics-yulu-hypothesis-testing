# Yulu Bike-Sharing Demand Analysis \| Hypothesis Testing

A data analysis project exploring hourly bike-rental demand and its
relationship with working days, weather conditions, and seasons. The
project combines exploratory data analysis (EDA), statistical hypothesis
testing, and business interpretation using Python.

## Project Overview

Yulu is a micro-mobility service provider offering shared electric
two-wheelers. Understanding when and under what conditions people rent
bikes can help with demand planning and fleet operations.

In this project, I analyzed hourly rental data to investigate whether
rental demand differs across working-day status, weather categories, and
seasons, and whether weather category is associated with season.

## Business Objectives

-   Understand the distribution and variability of hourly bike rentals.
-   Explore demand patterns across working days, weather conditions, and
    seasons.
-   Apply statistical tests to assess whether observed group differences
    are statistically significant.
-   Examine the association between weather category and season.
-   Translate the findings into practical, evidence-based business
    recommendations.

## Dataset

-   **Rows:** 10,886 hourly records
-   **Columns:** 12
-   **Target variable:** `count` --- total bike rentals per hour
-   **Date/time field:** `datetime`

The dataset includes:

  Column         Description
  -------------- -----------------------------------------
  `datetime`     Date and time of the observation
  `season`       Season category
  `holiday`      Whether the day is a holiday
  `workingday`   Whether the day is a working day
  `weather`      Weather category
  `temp`         Temperature
  `atemp`        Feels-like temperature
  `humidity`     Humidity
  `windspeed`    Wind speed
  `casual`       Casual-user rentals
  `registered`   Registered-user rentals
  `count`        Total rentals (`casual` + `registered`)

**Data quality checks:** The notebook found no missing values and no
duplicate rows. The `datetime` field was converted to datetime type, and
`season`, `holiday`, `workingday`, and `weather` were treated as
categorical variables.

> **Target leakage note:** `casual` and `registered` are components of
> `count`. They are useful for descriptive analysis, but should not be
> used as predictors when building a model to predict `count`.

## Tools & Libraries

-   Python
-   Pandas
-   NumPy
-   SciPy (`scipy.stats`)
-   Matplotlib
-   Seaborn
-   Google Colab / Jupyter Notebook

## Analysis Workflow

1.  **Data understanding and cleaning**
    -   Inspected dataset dimensions, columns, and data types.
    -   Checked for missing values and duplicate records.
    -   Converted date/time and categorical columns to suitable data
        types.
2.  **Exploratory Data Analysis (EDA)**
    -   Examined the distribution of hourly rental counts.
    -   Compared demand across working-day status, seasons, and weather
        categories.
    -   Used visualizations to identify patterns and potential unusual
        observations.
3.  **Outlier analysis**
    -   Applied the Interquartile Range (IQR) rule to `count`.
    -   Flagged observations above the upper IQR threshold for further
        investigation.
    -   Kept potential outliers rather than removing them automatically.
4.  **Hypothesis testing**
    -   Used a Welch independent two-sample t-test for working-day
        versus non-working-day rental counts.
    -   Used Welch's ANOVA to compare mean rentals across weather
        categories 1--3.
    -   Used one-way ANOVA to compare mean rentals across seasons.
    -   Used a chi-square test of independence to assess the association
        between weather category and season.
5.  **Business interpretation**
    -   Interpreted p-values using a 0.05 significance level.
    -   Connected the results to demand planning while documenting
        statistical limitations.

## Hypothesis Tests & Results

All tests used a significance level of **α = 0.05**.

### 1. Working day vs. non-working day --- Welch's t-test

-   **Non-working-day mean:** 188.51 rentals per hour (`n = 3,474`)
-   **Working-day mean:** 193.01 rentals per hour (`n = 7,412`)
-   **p-value:** 0.2164
-   **Decision:** Fail to reject the null hypothesis.

**Interpretation:** The test did not find sufficient statistical
evidence of a difference in mean hourly rentals between working and
non-working days. The observed means are close relative to the variation
in rental counts.

### 2. Weather category vs. rental count --- Welch's ANOVA

Weather category 4 had only one observation, so the primary comparison
included categories 1--3.

  Weather category     Mean hourly rentals   Observations
  ------------------ --------------------- --------------
  1                                 205.24          7,192
  2                                 178.96          2,834
  3                                 118.85            859

-   **Welch's ANOVA F-statistic:** 140.90
-   **p-value:** approximately 1.20 × 10⁻⁵⁸
-   **Decision:** Reject the null hypothesis.

**Interpretation:** Mean hourly rentals differ significantly across at
least some of the included weather categories. Category 1 had the
highest observed mean and category 3 the lowest. ANOVA alone does not
establish which individual pairs differ significantly; a suitable
post-hoc analysis would be needed for that.

### 3. Season vs. rental count --- one-way ANOVA

  Season category     Mean hourly rentals   Median hourly rentals
  ----------------- --------------------- -----------------------
  1                                116.34                      78
  2                                215.25                     172
  3                                234.42                     195
  4                                198.99                     161

-   **F-statistic:** approximately 236.95
-   **p-value:** approximately 6.16 × 10⁻¹⁴⁹
-   **Decision:** Reject the null hypothesis.

**Interpretation:** The analysis found statistically significant
differences in mean hourly rentals across seasons. Season 3 had the
highest observed mean, while season 1 had the lowest. The omnibus ANOVA
result does not identify every significant pairwise difference.

### 4. Weather category vs. season --- chi-square test of independence

Weather category 4 was excluded because it contained only one
observation.

-   **Chi-square statistic:** 46.10
-   **Degrees of freedom:** 6
-   **p-value:** approximately 2.83 × 10⁻⁸
-   **Minimum expected cell frequency:** approximately 211.89
-   **Decision:** Reject the null hypothesis.

**Interpretation:** The analysis found a statistically significant
association between weather category and season in the included data.
This is an association, not evidence that season causes a particular
weather condition.

## Outlier Analysis

Using the IQR rule on hourly `count`:

-   **Q1:** 42
-   **Q3:** 284
-   **IQR:** 242
-   **Upper threshold:** 647 rentals per hour
-   **Potential high outliers:** 300 observations (approximately 2.76%)

These observations may represent genuine periods of high demand. They
were flagged for investigation and were not automatically treated as
errors.

## Key Business Insights

-   **Seasonality matters:** Observed rental demand varies across
    seasons, so historical seasonal patterns can inform capacity
    planning.
-   **Weather is relevant to demand:** Mean rental counts differ across
    the analyzed weather categories; weather conditions can be
    considered alongside other operational signals.
-   **Working-day status alone may be insufficient:** The t-test did not
    find a statistically significant difference in mean hourly rentals
    between working and non-working days in this analysis.
-   **Investigate demand peaks:** High hourly counts should be examined
    in context before deciding whether they represent anomalies or
    genuine demand spikes.
-   **Use results carefully:** Statistical significance does not
    automatically mean a difference is operationally large or
    commercially important.

## Recommendations

1.  Use historical seasonal demand patterns as one input to fleet and
    staffing plans.
2.  Monitor weather and demand together, validating whether
    weather-aware adjustments improve service availability.
3.  Investigate peak-demand hours by time of day, location, and user
    segment where those fields are available.
4.  Do not use working-day status as the only demand-planning signal;
    consider season, weather, time, and other relevant factors.
5.  Add post-hoc comparisons and effect sizes to understand which group
    differences are practically meaningful.

## Limitations

-   **Temporal dependence:** Hourly observations may be correlated over
    time, so the independence assumption of standard statistical tests
    may not fully hold.
-   **Weather category 4:** Only one observation was present in category
    4; it was excluded from the weather ANOVA and weather-versus-season
    chi-square analysis.
-   **Unequal variances:** Levene's test indicated unequal variances
    across weather groups, motivating Welch's ANOVA for the weather
    comparison.
-   **Pairwise differences:** The omnibus ANOVA tests show whether any
    group means differ, but do not identify all differing pairs without
    post-hoc tests.
-   **Association vs. causation:** The results describe patterns and
    associations; they do not establish causal relationships.
-   **Outlier interpretation:** IQR flags unusual values, not
    necessarily data errors.
-   **No predictive model:** This project focuses on EDA and statistical
    inference; it does not yet evaluate a machine-learning model for
    demand prediction.

## How to Run

### 1. Clone the repository

``` bash
git clone <YOUR_GITHUB_REPOSITORY_URL>
cd <YOUR_REPOSITORY_FOLDER>
```

### 2. Install dependencies

Create a `requirements.txt` file containing the packages used in the
notebook, for example:

``` text
pandas
numpy
scipy
matplotlib
seaborn
jupyter
```

Install them:

``` bash
pip install -r requirements.txt
```

### 3. Open and run the notebook

Open the `.ipynb` file in Jupyter Notebook or upload it to Google Colab.
Update the dataset path in the notebook to match your environment, then
run the cells from top to bottom.

## Suggested Repository Structure

Update this example to match the files actually in your repository:

``` text
yulu-bike-sharing-analysis/
├── README.md
├── yulu_bike_sharing_analysis.ipynb
├── data/
│   └── README.md
└── requirements.txt
```

If dataset licensing or file size prevents redistribution, omit the raw
dataset and explain how to obtain it in `data/README.md`.

## Future Improvements

-   Add post-hoc tests for season and weather comparisons.
-   Report effect sizes and confidence intervals alongside p-values.
-   Investigate temporal patterns by hour, weekday, and month.
-   Validate assumptions and consider methods that account for time
    dependence.
-   Build and evaluate a demand-prediction model using a time-aware
    train/test split and appropriate regression metrics.

## Author

**Gajendra Singh**

Data Analytics \| Python \| Statistics \| SQL

------------------------------------------------------------------------

If you have suggestions or feedback, feel free to open an issue or
connect with me.
