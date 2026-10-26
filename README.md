# LA County Community Health Profiles: Education and Self-Rated Health

An exploratory data analysis of how educational attainment, income and poverty relate to the percentage of adults reporting fair or poor health across LA County communities. The analysis describes associations and does not establish cause and effect.

This project is a revision of an earlier analysis completed through a collaboration between Extern and TruBridge, reworked to separate overlapping geographies and to add a distinct neighborhood-level analysis.

## Research Questions

**Question 1 (Primary Analysis): Which measures are most strongly related to fair or poor health across cities?**

**Question 2 (Secondary Analysis): Do the same relationships hold across Los Angeles neighborhoods?**

## Data

- **Source:** LA County Department of Public Health, Community Health Profiles, Theme 2: Social Determinants of Health (data download and metadata files).
- **Size:** 182 rows and 66 columns. Each row is a geography. Most indicators come with 95% confidence limits (`_LCL`, `_UCL`).
- **Geography types:** neighborhoods, incorporated cities, city council districts, unincorporated areas, service planning areas (SPAs), supervisorial districts, the county, and a Healthy People 2030 target row.

### Columns used in this analysis

| Group | Columns |
|---|---|
| Identifiers | `Geo_ID`, `Geography_Name`, `Geography_Type` |
| Outcome | `Poor_health` (percentage of adults 18+ reporting fair or poor health, 2023 LA County Health Survey), `Poor_health_EST` (estimation method) |
| Education (adults 25+) | `Edu_LessHS_Pct`, `Edu_HSGrad_Pct`, `Edu_College_Pct`, `Edu_Bach_Pct` |
| Income and poverty | `MHI` (median household income, 2022 dollars), `100_FPL_Pct`, `200_FPL_Pct` |

Full definitions for each column are in the data dictionary section of the notebook.

## Key Data Decisions

Rows in this dataset describe the same people at different geographic scales, so analyzing them together would count residents more than once. Each overlap was tested by comparing summed population counts (the `_Pop` columns).

| Geography type | Rows | Finding | Decision |
|---|---|---|---|
| Healthy People 2030 target | 1 | A benchmark, not a geography | Dropped |
| County | 1 | Contains every other geography | Dropped |
| Supervisorial districts | 5 | Add up to 100% of the county | Dropped |
| Service planning areas | 8 | Add up to 100% of the county | Dropped |
| City council districts | 15 | Add up to about 100% of the City of Los Angeles | Dropped |
| Incorporated cities | 64 | Includes the City of Los Angeles as one row | Kept (primary analysis) |
| Unincorporated areas | 14 | Do not overlap with cities | Kept |
| Los Angeles City neighborhoods | 74 | Cover roughly 82% to 90% of the City of Los Angeles | Kept as a separate (secondary) analysis |

Because the neighborhoods overlap with the City of Los Angeles row, they are analyzed separately instead of being pooled with the cities. Incorporated cities and unincorporated areas together account for roughly 93% to 96% of county totals, so some communities are not in the data.

## Project Structure

The notebook is organized as a report:

1. **Research Questions**
2. **Data Dictionary**
3. **Load and Inspect the Data:** setup, first look, missing values, geography types and estimation method
4. **Geography Structure and Overlap:** tests for overlap between geographies, with a finding and a decision for each
5. **Build the Analysis Datasets:** drop duplicated rows, select columns, split into `df_cities` and `df_neighborhoods`, and define reusable EDA functions
6. **EDA: Cities (Primary Analysis):** summary statistics, distributions, correlations, and education versus poor health
7. **EDA: Neighborhoods (Secondary Analysis):** the same four steps on the neighborhood dataset
8. **Findings**
9. **Limitations**

## Methods

- Data cleaning and filtering with pandas
- Population-sum checks to detect overlapping geographies
- Summary statistics, histograms with density curves, correlation heatmaps, and scatterplots with trend lines (seaborn and matplotlib)
- The same set of functions applied to both datasets so the two analyses are directly comparable

## Key Findings

Question 1: Which measures are most strongly related to fair or poor health across cities?

Across 78 cities and unincorporated areas, median household income showed the strongest relationship with fair or poor health (r = -0.86), followed by the share of residents below 200% of the poverty level (r = 0.82). Among the education measures, the share of adults without a high school diploma (r = 0.80) and the share with a bachelor's degree or higher (r = -0.78) were nearly as strong, while some college showed only a weak relationship (r = -0.20).

These measures also overlap heavily with one another. For example, less than high school and bachelor's or higher correlate at r = -0.88, and the 100% and 200% poverty rates correlate at r = 0.99. Because the measures move together, this analysis cannot separate their individual contributions.

Question 2: Do the same relationships hold across Los Angeles neighborhoods?

The direction of every relationship is the same across neighborhoods, but the education correlations are weaker (less than high school r = 0.65 vs. 0.80 in cities; bachelor's or higher r = -0.59 vs. -0.78). Poverty and income remain strong (200% poverty r = 0.79; median household income r = -0.77). Education and poverty overlap even more in neighborhoods than in cities (less than high school and 200% poverty r = 0.85).

Neighborhoods also have a higher and more varied share of residents in fair or poor health (average 14.7%, range 3.9% to 35.0%) than cities (average 12.2%, range 3.8% to 20.1%).

## Limitations

- **Modeled outcome.** `Poor_health` for cities, neighborhoods and unincorporated areas is a small area estimate, not a direct survey measure. If the model uses Census characteristics as inputs, some of the associations found here may reflect the modeling itself.
- **Incomplete coverage.** Neighborhoods cover only part of the City of Los Angeles, and some communities are not in the data.
- **Ecological data.** Each row is a geography, not a person, so associations describe communities and do not necessarily apply to individuals. Correlation does not establish causation.
- **Small samples and overlapping predictors.** Each dataset has under 100 rows. The education measures are mutually exclusive categories, and education, income and poverty are strongly correlated with each other.
- **Missing values.** `Edu_LessHS_Pct` is missing for one row in each dataset.

## How to Run

1. Download the Community Health Profiles Theme 2 data file and place it where the notebook can read it.
2. Update the file path in the data-loading cell (section 3.1) of the notebook.
3. Install the requirements:

   ```
   pip install numpy pandas matplotlib seaborn jupyter
   ```

4. Open the notebook in Jupyter and use **Restart & Run All**.

## Requirements

- Python 3
- numpy
- pandas
- matplotlib
- seaborn
- Jupyter Notebook
