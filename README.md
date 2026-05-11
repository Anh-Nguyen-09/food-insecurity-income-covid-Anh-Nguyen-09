# choose-your-own-hypothesis-Anh-Nguyen-09
Food Insecurity and Income During COVID-19

1. Research Question
Did low-income households (under $35,000/year) experience significantly higher food insecurity rates than high-income households ($75,000+/year) during the COVID-19 pandemic?
This matters because food insecurity has direct consequences for physical and mental health, child development, and long-term economic mobility. If income strongly predicts food insecurity even during a period of expanded government assistance, that has important implications for the design of safety-net programs.

2. Hypothesis
- Null hypothesis: There is no difference in food insecurity rates between low-income and high-income households. Any observed difference is due to chance.

- Alternative hypothesis: Low-income households had significantly higher food insecurity rates than high-income households throughout 2020–2021.

3. Data Description
- Source: U.S. Census Bureau Household Pulse Survey, 2020–2021, compiled by Jack Ogozaly on Kaggle: https://www.kaggle.com/datasets/jackogozaly/pulse-survey-food-insecurity-data

- Original source: U.S. Census Bureau — https://www.census.gov/programs-surveys/household-pulse-survey.html

- Unit of analysis: One row = one income group's aggregated food sufficiency counts for a given survey week at the national (US) level

- Number of observations: 23 survey weeks × 8 income groups = 184 rows used in analysis (after filtering to US-level, removing "Did not report" income)

- Key variables:
    - Income — income bracket (e.g., "Less than $25,000", "$75,000 – $99,999")
    - Sometimes not enough to eat — estimated population count in this food sufficiency category
    - Often not enough to eat — estimated population count in this food sufficiency category
    - Enough of the kinds of food wanted, Enough Food, but not always the kinds wanted, Did not report — remaining food sufficiency counts
    - Week, Year — survey timing (23 weeks across 2020–2021)

- Derived variable: insecurity_rate = (Sometimes not enough + Often not enough) / total population in that group and week

- Cleaning steps:
    - Filtered to national-level rows only (Location == 'US')
    - Excluded rows where Income == 'Did not report'
    - Computed insecurity_rate as a proportion for each row

- Groups compared:
    - Low-income: "Less than $25,000" and "$25,000 – $34,999"
    - High-income: "$75,000 – $99,999", "$100,000 – $149,999", "$150,000 – $199,999", "$200,000 and above"

- Summary statistics by income group:
|Income Group|Mean Rate|Median Rate|Std Dev|
|---|---|---|---|
|Less than $25,000|0.276|0.278|0.032|
|$25,000 – $34,999|0.174|0.166|0.026|
|$35,000 – $49,999|0.120|0.112|0.021|
|$50,000 – $74,999|0.077|0.074|0.015|
|$75,000 – $99,999|0.041|0.039|0.011|
|$100,000 – $149,999|0.021|0.020|0.006|
|$150,000 – $199,999|0.012|0.010|0.005|
|$200,000 and above|0.008|0.007|0.005|


4. Methods
- Permutation test:
    - Test statistic: Mean insecurity rate (low-income) − mean insecurity rate (high-income) across all survey weeks
    - Null simulation: Pool all low-income and high-income weekly rates, randomly shuffle the group labels, and recompute the mean difference — repeated 10,000 times
    - Observed statistic: 0.2047 (low-income mean of 22.5% vs. high-income mean of 2.0%)

- Bootstrap confidence interval:
    - Metric: Median food insecurity rate across weeks for the low-income group
    - Method: Resample with replacement from the 46 low-income weekly rate observations, compute the median each time — repeated 10,000 times
    - Why CLT does not apply: The median is not a mean or proportion, so the Central Limit Theorem does not guarantee a normal sampling distribution, especially with only 46 observations. Bootstrapping is used to estimate the 95% confidence interval without assuming normality.

5. Results
- Permutation test:
    - Observed difference in mean insecurity rates (low − high): 0.2047
    - Low-income mean: 22.5% | High-income mean: 2.0%
    - P-value (two-tailed): 0.0000 — none of the 10,000 random shuffles produced a difference as large as the observed one
    - The simulated null distribution ranged from approximately −0.071 to +0.066, while the observed difference of 0.205 falls far outside this range

- Bootstrap confidence interval:
    - Median insecurity rate for low-income group: 0.2225
    - 95% Bootstrap CI: [0.1883, 0.2570]
    - The bootstrap distribution ranged from 0.167 to 0.280, with a tight, approximately symmetric shape centered around 0.22

- Key finding: Low-income households reported food insecurity rates roughly 10–15x higher than high-income households throughout the entire 2020–2021 period, and this gap is highly statistically significant.

6. Uncertainty Estimation
- Number of resamples: 10,000 for both the permutation test and bootstrap

- Bootstrap distribution: The bootstrap distribution of median insecurity rates for the low-income group was approximately symmetric and centered around 0.2225, with a 95% CI of [0.1883, 0.2570]

- Interpretation: We are 95% confident that the true median weekly food insecurity rate for low-income households during 2020–2021 was between 18.8% and 25.7%. This interval is entirely above the high-income group's mean rate of 2.0%, providing strong evidence that the gap is real and not an artifact of sampling variation

- Permutation distribution: The null distribution of simulated differences was centered at 0 and ranged from roughly −0.071 to +0.066. The observed difference of 0.205 lies far outside this range, yielding a p-value of 0.0000

7. Limitations
- Aggregated data: The dataset contains population estimates rather than individual survey responses, which limits the types of statistical tests available and means each "observation" in our analysis is itself an aggregate

- Two income groups only: By collapsing income into low vs. high, we lose nuance — the data shows a smooth gradient from 27.6% (under $25k) down to 0.8% ($200k+), not just two distinct levels

- "Did not report" exclusion: Rows where income was not reported were excluded; if non-reporters are systematically different (e.g., disproportionately low-income), this could introduce bias

- Confounding factors: The dataset does not allow us to control for other factors like race, employment status, or geographic region simultaneously with income, which may confound the relationship

- Time period: Data covers only 2020–2021 during the acute phase of COVID-19; results may not generalize to other time periods

8. References
- Ogozaly, J. (2021). Pulse Survey Food Insecurity Data. Kaggle. https://www.kaggle.com/datasets/jackogozaly/pulse-survey-food-insecurity-data

- U.S. Census Bureau. Household Pulse Survey. https://www.census.gov/programs-surveys/household-pulse-survey.html

- pandas, numpy, matplotlib — Python libraries used for analysis