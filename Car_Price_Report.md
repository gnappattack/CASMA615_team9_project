
# Car Prices Dataset - Exploration Summary

## 1. Dataset Overview

-   **Size:** 19,237 observations, 18 variables
-   **Target Variable:** Price (car selling price)
-   **Source:** Kaggle - used car marketplace data
-   **Predictors:** Mix of numeric (production year, cylinders, airbags)
    and categorical (manufacturer, fuel type, category, etc.)

------------------------------------------------------------------------

## 2. Key Findings

### Data Quality Issues Discovered

1.  **Critical Outlier:** One car priced at \$26,307,500 (1999 Opel
    Combo) - clearly a data error
    -   Removed 3 extreme outliers (\>\$500k) from dataset
    -   Final clean dataset: 19,234 observations
2.  **Missing Data:**
    -   Levy column: 5,819 missing values (30% of data shown as "-")
    -   All other variables complete
3.  **Data Format Problems:**
    -   **Doors column:** Excel converted numbers to dates ("04-May"
        instead of "4-5 doors")
    -   **Mileage:** Contains " km" text, needs cleaning
    -   **Engine.volume:** Contains "Turbo" text mixed with numbers
    -   **Levy:** Character format instead of numeric
4.  **Suspicious Values:**
    -   1,663 cars (8.6%) priced under \$500
    -   May indicate salvage/damaged vehicles or data errors

------------------------------------------------------------------------

### Correlation Analysis

**Impact of Outlier Removal:**

| Variable        | Before Cleaning | After Cleaning |
|-----------------|-----------------|----------------|
| Production Year | 0.013 ❌        | **0.286 ✅**   |
| Cylinders       | 0.008 ❌        | **0.103 ✅**   |
| Airbags         | -0.013 ❌       | -0.018 ⚠️      |

**Key Insight:** The outlier completely destroyed correlations! After
removal: - **Production Year** shows moderate positive correlation
(newer = more expensive) - **Cylinders** shows weak positive
correlation - **Airbags** has essentially no correlation (unexpected)

**Overall:** Numeric correlations are weak to moderate. Will need
categorical variables for better predictions.

------------------------------------------------------------------------


**Must Do:** 1. Remove extreme outliers (\>\$500k) 2. Clean Doors column
(fix Excel date bug) 3. Convert Mileage and Engine.volume to numeric 4.
Handle missing Levy values (impute or remove) 5. Apply log
transformation to Price 6. Consider removing very cheap cars (\<\$500)

**Should Do:** 7. Create dummy variables for categorical predictors 8.
Feature engineering (e.g., car age = 2020 - Prod.year) 9. Group rare
manufacturers into "Other" category 10. Create "Turbo" as separate
binary variable



