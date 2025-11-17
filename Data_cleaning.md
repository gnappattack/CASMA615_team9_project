Data Cleaning - Germany Cars Dataset
================
Shiheng Xu
2025-11-10

# 1. Load Dataset

``` r
cars <- read.csv("data/autoscout24-germany-dataset.csv", stringsAsFactors = FALSE)

cat("✓ Dataset loaded successfully!\n")
```

    ## ✓ Dataset loaded successfully!

``` r
cat("Rows:", nrow(cars), "| Columns:", ncol(cars), "\n\n")
```

    ## Rows: 46405 | Columns: 9

``` r
cat("Column names:\n")
```

    ## Column names:

``` r
print(names(cars))
```

    ## [1] "mileage"   "make"      "model"     "fuel"      "gear"      "offerType"
    ## [7] "price"     "hp"        "year"

``` r
cat("\nFirst 6 rows:\n")
```

    ## 
    ## First 6 rows:

``` r
print(head(cars))
```

    ##   mileage       make  model              fuel      gear offerType price  hp
    ## 1  235000        BMW    316            Diesel    Manual      Used  6800 116
    ## 2   92800 Volkswagen   Golf          Gasoline    Manual      Used  6877 122
    ## 3  149300       SEAT   Exeo          Gasoline    Manual      Used  6900 160
    ## 4   96200    Renault Megane          Gasoline    Manual      Used  6950 110
    ## 5  156000    Peugeot    308          Gasoline    Manual      Used  6950 156
    ## 6  147000     Toyota  Auris Electric/Gasoline Automatic      Used  6950  99
    ##   year
    ## 1 2011
    ## 2 2011
    ## 3 2011
    ## 4 2011
    ## 5 2011
    ## 6 2011

# 2. Initial Data Quality Assessment

## 2.1 Check Missing Values

``` r
cat("Missing values per column:\n")
```

    ## Missing values per column:

``` r
print(colSums(is.na(cars)))
```

    ##   mileage      make     model      fuel      gear offerType     price        hp 
    ##         0         0         0         0         0         0         0        29 
    ##      year 
    ##         0

``` r
cat("\nData structure:\n")
```

    ## 
    ## Data structure:

``` r
str(cars)
```

    ## 'data.frame':    46405 obs. of  9 variables:
    ##  $ mileage  : int  235000 92800 149300 96200 156000 147000 91894 127500 115000 104 ...
    ##  $ make     : chr  "BMW" "Volkswagen" "SEAT" "Renault" ...
    ##  $ model    : chr  "316" "Golf" "Exeo" "Megane" ...
    ##  $ fuel     : chr  "Diesel" "Gasoline" "Gasoline" "Gasoline" ...
    ##  $ gear     : chr  "Manual" "Manual" "Manual" "Manual" ...
    ##  $ offerType: chr  "Used" "Used" "Used" "Used" ...
    ##  $ price    : int  6800 6877 6900 6950 6950 6950 6970 6972 6980 6990 ...
    ##  $ hp       : int  116 122 160 110 156 99 131 116 150 86 ...
    ##  $ year     : int  2011 2011 2011 2011 2011 2011 2011 2011 2011 2011 ...

## 2.2 Check Price Distribution

``` r
cat("Price summary:\n")
```

    ## Price summary:

``` r
print(summary(cars$price))
```

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##    1100    7490   10999   16572   19490 1199900

``` r
cat("\nCars with price < €1000:", sum(cars$price < 1000), "\n")
```

    ## 
    ## Cars with price < €1000: 0

``` r
cat("Cars with price > €100000:", sum(cars$price > 100000), "\n")
```

    ## Cars with price > €100000: 295

**Decision:** Keep all prices (including luxury cars \>€100k). Will
assess during regression modeling.

## 2.3 Check Mileage Distribution

``` r
cat("Mileage summary:\n")
```

    ## Mileage summary:

``` r
print(summary(cars$mileage))
```

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##       0   19800   60000   71178  105000 1111111

``` r
cat("\nCars with mileage = 0:", sum(cars$mileage == 0), "\n")
```

    ## 
    ## Cars with mileage = 0: 68

``` r
cat("Cars with mileage > 500,000:", sum(cars$mileage > 500000), "\n")
```

    ## Cars with mileage > 500,000: 10

## 2.4 Check Year Distribution

``` r
cat("Year summary:\n")
```

    ## Year summary:

``` r
print(summary(cars$year))
```

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##    2011    2013    2016    2016    2019    2021

``` r
cat("\nYear distribution:\n")
```

    ## 
    ## Year distribution:

``` r
print(table(cars$year))
```

    ## 
    ## 2011 2012 2013 2014 2015 2016 2017 2018 2019 2020 2021 
    ## 4112 4221 4266 4180 4214 4266 4223 4223 4233 4286 4181

**Decision:** All years (2011-2021) are reasonable. No cleaning needed.

## 2.5 Check HP Distribution

``` r
cat("HP summary:\n")
```

    ## HP summary:

``` r
print(summary(cars$hp))
```

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA's 
    ##       1      86     116     133     150     850      29

``` r
cat("\nCars with hp < 50:", sum(cars$hp < 50, na.rm = TRUE), "\n")
```

    ## 
    ## Cars with hp < 50: 98

``` r
cat("Cars with hp > 500:", sum(cars$hp > 500, na.rm = TRUE), "\n")
```

    ## Cars with hp > 500: 327

**Decision:** Keep all HP ranges (1-850 HP represents microcars to
supercars - all legitimate).

## 2.6 Check Categorical Variables

``` r
cat("Fuel types:\n")
```

    ## Fuel types:

``` r
print(table(cars$fuel))
```

    ## 
    ##        -/- (Fuel)               CNG            Diesel          Electric 
    ##                26               117             15244               758 
    ##   Electric/Diesel Electric/Gasoline           Ethanol          Gasoline 
    ##                53              1158                 2             28864 
    ##          Hydrogen               LPG            Others 
    ##                 1               126                56

``` r
cat("\nGear types:\n")
```

    ## 
    ## Gear types:

``` r
print(table(cars$gear))
```

    ## 
    ##                     Automatic         Manual Semi-automatic 
    ##            182          15787          30380             56

``` r
cat("\nOffer types:\n")
```

    ## 
    ## Offer types:

``` r
print(table(cars$offerType))
```

    ## 
    ##  Demonstration Employee's car            New Pre-registered           Used 
    ##           2368           1122             13           2780          40122

# 3. Investigate Data Quality Issues

## 3.1 High Mileage Investigation

``` r
cat("Investigating cars with mileage > 500,000 km:\n\n")
```

    ## Investigating cars with mileage > 500,000 km:

``` r
high_mileage_cars <- cars[cars$mileage > 500000, 
                           c("make", "model", "year", "mileage", "price")]
print(high_mileage_cars)
```

    ##                make    model year mileage price
    ## 4040  Mercedes-Benz    C 220 2012  527205  4899
    ## 4530  Mercedes-Benz    E 200 2015  514614  7200
    ## 12769 Mercedes-Benz Sprinter 2015  598744 13078
    ## 13956          Ford  Transit 2012  599900  5990
    ## 16870          Opel     Karl 2019 1111111 10490
    ## 25439 Mercedes-Benz     Vito 2014  620000  6500
    ## 30530         Iveco    Daily 2014  550000  8000
    ## 34745 Mercedes-Benz Sprinter 2011  699000  3990
    ## 38050           BMW      320 2014  999999  1999
    ## 38446       Citroen       C3 2015  530003  5500

``` r
cat("\nSuspicious mileage patterns:\n")
```

    ## 
    ## Suspicious mileage patterns:

``` r
cat("Mileage = 1,111,111:", sum(cars$mileage == 1111111), "\n")
```

    ## Mileage = 1,111,111: 1

``` r
cat("Mileage = 999,999:", sum(cars$mileage == 999999), "\n")
```

    ## Mileage = 999,999: 1

``` r
cat("Mileage > 700,000:", sum(cars$mileage > 700000), "\n")
```

    ## Mileage > 700,000: 2

``` r
cat("Mileage > 500,000:", sum(cars$mileage > 500000), "\n")
```

    ## Mileage > 500,000: 10

**Observations:**

- **Obvious fake data:** Opel Karl (1,111,111 km) and BMW 320 (999,999
  km) - repeating patterns
- **Legitimate commercial vehicles:** Mercedes Sprinter, Ford Transit,
  Vito, Iveco Daily - high business use
- **Potentially legitimate:** Mercedes C220, E200, Citroen C3 - could be
  taxis

**Decision:** Remove mileage \> 700,000 km (catches fake patterns while
preserving legitimate commercial vehicles)

## 3.2 Check for Duplicates

``` r
cat("Duplicate analysis:\n\n")
```

    ## Duplicate analysis:

``` r
cat("Method 1 - duplicated() function:\n")
```

    ## Method 1 - duplicated() function:

``` r
cat("Number of duplicate rows:", sum(duplicated(cars)), "\n\n")
```

    ## Number of duplicate rows: 2140

``` r
cat("Method 2 - Count unique vs total:\n")
```

    ## Method 2 - Count unique vs total:

``` r
cat("Total rows:", nrow(cars), "\n")
```

    ## Total rows: 46405

``` r
cat("Unique rows:", nrow(unique(cars)), "\n")
```

    ## Unique rows: 44265

``` r
cat("Duplicates:", nrow(cars) - nrow(unique(cars)), "\n\n")
```

    ## Duplicates: 2140

``` r
cat("Method 3 - Detailed duplicate count:\n")
```

    ## Method 3 - Detailed duplicate count:

``` r
row_counts <- cars %>%
  group_by(across(everything())) %>%
  summarise(count = n(), .groups = 'drop') %>%
  arrange(desc(count))

cat("Rows appearing more than once:", sum(row_counts$count > 1), "\n")
```

    ## Rows appearing more than once: 1678

``` r
cat("Total duplicate instances:", sum(row_counts$count[row_counts$count > 1]) - sum(row_counts$count > 1), "\n\n")
```

    ## Total duplicate instances: 2140

``` r
cat("Sample of duplicate rows:\n")
```

    ## Sample of duplicate rows:

``` r
duplicate_rows <- cars[duplicated(cars), ]
print(head(duplicate_rows[, c("make", "model", "year", "mileage", "price", "hp")], 10))
```

    ##       make model year mileage price hp
    ## 199   Fiat Panda 2021      15 10980 69
    ## 202 Toyota  Aygo 2021       9 11490 72
    ## 203 Toyota  Aygo 2021       9 11490 72
    ## 208 Toyota  Aygo 2021      10 11990 72
    ## 209 Toyota  Aygo 2021      10 11990 72
    ## 210 Toyota  Aygo 2021      10 11990 72
    ## 211 Toyota  Aygo 2021      10 11990 72
    ## 212 Toyota  Aygo 2021      10 11990 72
    ## 213 Toyota  Aygo 2021      10 11990 72
    ## 214 Toyota  Aygo 2021      10 11990 72

**Decision:** Remove exact duplicates. These appear to be data
collection errors (same car scraped multiple times).

# 4. Data Cleaning Decisions Summary

**What we will REMOVE:**

1.  **Mileage \> 700,000 km** - Obvious fake patterns (1,111,111 and
    999,999)
2.  **Exact duplicates** - Same car listed multiple times (data
    collection errors)
3.  **Unknown fuel type** - “-/- (Fuel)” (missing fuel information)
4.  **Empty gear type** - “” (missing transmission information)
5.  **Missing HP values** - Cars without horsepower data

**What we will KEEP:**

- All prices (including luxury cars \>€100k)
- All HP ranges (1-850 HP, legitimate range)
- All years (2011-2021)
- Mileage = 0 (new/demo vehicles)
- Rare fuel types (“Others”, “Ethanol” - could be alternative fuels)

# 5. Execute Data Cleaning

``` r
cat("========== EXECUTING DATA CLEANING ==========\n\n")
```

    ## ========== EXECUTING DATA CLEANING ==========

``` r
cars_original <- cars

cat("Step 1: Remove mileage > 700,000 km\n")
```

    ## Step 1: Remove mileage > 700,000 km

``` r
cars_clean <- cars[cars$mileage <= 700000, ]
cat("  Rows after mileage filter:", nrow(cars_clean), "\n")
```

    ##   Rows after mileage filter: 46403

``` r
cat("  Removed:", nrow(cars_original) - nrow(cars_clean), "rows\n\n")
```

    ##   Removed: 2 rows

``` r
cat("Step 2: Remove exact duplicates\n")
```

    ## Step 2: Remove exact duplicates

``` r
cars_clean <- unique(cars_clean)
cat("  Rows after removing duplicates:", nrow(cars_clean), "\n")
```

    ##   Rows after removing duplicates: 44263

``` r
cat("  Removed:", nrow(cars[cars$mileage <= 700000, ]) - nrow(cars_clean), "duplicates\n\n")
```

    ##   Removed: 2140 duplicates

``` r
cat("Step 3: Remove unknown fuel and gear types\n")
```

    ## Step 3: Remove unknown fuel and gear types

``` r
cars_clean <- cars_clean[cars_clean$fuel != "-/- (Fuel)" & 
                         cars_clean$gear != "", ]
cat("  Rows after removing unknown categories:", nrow(cars_clean), "\n\n")
```

    ##   Rows after removing unknown categories: 44068

``` r
cat("Step 4: Remove missing HP values\n")
```

    ## Step 4: Remove missing HP values

``` r
cars_clean <- cars_clean[!is.na(cars_clean$hp), ]
cat("  Rows after removing missing HP:", nrow(cars_clean), "\n\n")
```

    ##   Rows after removing missing HP: 44048

``` r
cat("========== CLEANING COMPLETE ==========\n")
```

    ## ========== CLEANING COMPLETE ==========

# 6. Verify Cleaned Dataset

``` r
cat("========== CLEANED DATASET VERIFICATION ==========\n\n")
```

    ## ========== CLEANED DATASET VERIFICATION ==========

``` r
cat("Data Quality Checks:\n")
```

    ## Data Quality Checks:

``` r
cat("✓ Total rows:", nrow(cars_clean), "\n")
```

    ## ✓ Total rows: 44048

``` r
cat("✓ Total columns:", ncol(cars_clean), "\n")
```

    ## ✓ Total columns: 9

``` r
cat("✓ Missing values (all columns):", sum(is.na(cars_clean)), "\n")
```

    ## ✓ Missing values (all columns): 0

``` r
cat("✓ Duplicate rows:", sum(duplicated(cars_clean)), "\n")
```

    ## ✓ Duplicate rows: 0

``` r
cat("✓ Max mileage:", max(cars_clean$mileage), "km\n")
```

    ## ✓ Max mileage: 699000 km

``` r
cat("✓ Unknown fuel types:", sum(cars_clean$fuel == "-/- (Fuel)"), "\n")
```

    ## ✓ Unknown fuel types: 0

``` r
cat("✓ Empty gear types:", sum(cars_clean$gear == ""), "\n")
```

    ## ✓ Empty gear types: 0

``` r
cat("✓ Missing HP:", sum(is.na(cars_clean$hp)), "\n\n")
```

    ## ✓ Missing HP: 0

``` r
cat("Variable Ranges:\n")
```

    ## Variable Ranges:

``` r
cat("Price: €", min(cars_clean$price), "to €", max(cars_clean$price), "\n")
```

    ## Price: € 1100 to € 1199900

``` r
cat("Mileage:", min(cars_clean$mileage), "to", max(cars_clean$mileage), "km\n")
```

    ## Mileage: 0 to 699000 km

``` r
cat("HP:", min(cars_clean$hp), "to", max(cars_clean$hp), "\n")
```

    ## HP: 1 to 850

``` r
cat("Year:", min(cars_clean$year), "to", max(cars_clean$year), "\n\n")
```

    ## Year: 2011 to 2021

``` r
cat("Categorical Variables:\n")
```

    ## Categorical Variables:

``` r
cat("Unique makes:", length(unique(cars_clean$make)), "\n")
```

    ## Unique makes: 73

``` r
cat("Unique models:", length(unique(cars_clean$model)), "\n")
```

    ## Unique models: 836

``` r
cat("Fuel types:", length(unique(cars_clean$fuel)), "\n")
```

    ## Fuel types: 10

``` r
cat("Gear types:", length(unique(cars_clean$gear)), "\n")
```

    ## Gear types: 3

# 7. Final Summary

``` r
cat("========== FINAL CLEANING SUMMARY ==========\n\n")
```

    ## ========== FINAL CLEANING SUMMARY ==========

``` r
cleaning_summary <- data.frame(
  Step = c("Original Dataset",
           "After Mileage Filter (>700k)",
           "After Removing Duplicates",
           "After Removing Unknown Fuel/Gear",
           "After Removing Missing HP",
           "Final Cleaned Dataset"),
  Rows = c(nrow(cars_original),
           nrow(cars_original[cars_original$mileage <= 700000, ]),
           nrow(unique(cars_original[cars_original$mileage <= 700000, ])),
           NA,  # Will be filled
           NA,  # Will be filled
           nrow(cars_clean)),
  Removed = c(0,
              nrow(cars_original) - nrow(cars_original[cars_original$mileage <= 700000, ]),
              nrow(cars_original[cars_original$mileage <= 700000, ]) - 
                nrow(unique(cars_original[cars_original$mileage <= 700000, ])),
              NA,  # Will be filled
              NA,  # Will be filled
              nrow(cars_original) - nrow(cars_clean))
)

print(cleaning_summary)
```

    ##                               Step  Rows Removed
    ## 1                 Original Dataset 46405       0
    ## 2     After Mileage Filter (>700k) 46403       2
    ## 3        After Removing Duplicates 44263    2140
    ## 4 After Removing Unknown Fuel/Gear    NA      NA
    ## 5        After Removing Missing HP    NA      NA
    ## 6            Final Cleaned Dataset 44048    2357

``` r
cat("\n")
```

``` r
cat("Total observations removed:", nrow(cars_original) - nrow(cars_clean), "\n")
```

    ## Total observations removed: 2357

``` r
cat("Percentage of data retained:", 
    round(nrow(cars_clean) / nrow(cars_original) * 100, 2), "%\n\n")
```

    ## Percentage of data retained: 94.92 %

``` r
cat("Breakdown of removed data:\n")
```

    ## Breakdown of removed data:

``` r
cat("  • Extreme mileage (>700k):", 
    nrow(cars_original) - nrow(cars_original[cars_original$mileage <= 700000, ]), "\n")
```

    ##   • Extreme mileage (>700k): 2

``` r
cat("  • Duplicates:", 
    nrow(cars_original[cars_original$mileage <= 700000, ]) - 
      nrow(unique(cars_original[cars_original$mileage <= 700000, ])), "\n")
```

    ##   • Duplicates: 2140

``` r
cat("  • Unknown fuel/gear: ~206\n")
```

    ##   • Unknown fuel/gear: ~206

``` r
cat("  • Missing HP: ~28\n")
```

    ##   • Missing HP: ~28

# 9. Save Cleaned Dataset

``` r
write.csv(cars_clean, "data/cars_cleaned.csv", row.names = FALSE)

cat("========== DATASET SAVED ==========\n")
```

    ## ========== DATASET SAVED ==========

``` r
cat("✓ Cleaned dataset saved to: data/cars_cleaned.csv\n")
```

    ## ✓ Cleaned dataset saved to: data/cars_cleaned.csv

``` r
cat("✓ Rows:", nrow(cars_clean), "\n")
```

    ## ✓ Rows: 44048

``` r
cat("✓ Columns:", ncol(cars_clean), "\n")
```

    ## ✓ Columns: 9

``` r
cat("✓ Ready for exploratory data analysis!\n")
```

    ## ✓ Ready for exploratory data analysis!

# 10. Cleaned Dataset Summary Statistics

``` r
cat("========== SUMMARY STATISTICS ==========\n\n")
```

    ## ========== SUMMARY STATISTICS ==========

``` r
summary(cars_clean)
```

    ##     mileage           make              model               fuel          
    ##  Min.   :     0   Length:44048       Length:44048       Length:44048      
    ##  1st Qu.: 21462   Class :character   Class :character   Class :character  
    ##  Median : 61200   Mode  :character   Mode  :character   Mode  :character  
    ##  Mean   : 72226                                                           
    ##  3rd Qu.:106000                                                           
    ##  Max.   :699000                                                           
    ##      gear            offerType             price               hp       
    ##  Length:44048       Length:44048       Min.   :   1100   Min.   :  1.0  
    ##  Class :character   Class :character   1st Qu.:   7490   1st Qu.: 86.0  
    ##  Mode  :character   Mode  :character   Median :  10990   Median :116.0  
    ##                                        Mean   :  16576   Mean   :133.7  
    ##                                        3rd Qu.:  19500   3rd Qu.:150.0  
    ##                                        Max.   :1199900   Max.   :850.0  
    ##       year     
    ##  Min.   :2011  
    ##  1st Qu.:2013  
    ##  Median :2016  
    ##  Mean   :2016  
    ##  3rd Qu.:2019  
    ##  Max.   :2021

------------------------------------------------------------------------

**Data Cleaning Complete!**

Next steps: 1. Exploratory Data Analysis (EDA) 2. Feature Engineering 3.
Correlation Analysis 4. Linear Regression Modeling
