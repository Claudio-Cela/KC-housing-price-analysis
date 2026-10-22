# King County Housing — Price Modeling and Quality Classification

Two linked models on 21,613 home sales from King County, Washington: a multiple linear regression predicting sale price, and a logistic regression classifying whether a home qualifies as "good quality."

## What this does

**Price prediction.** Starting from the full feature set, both forward and backward stepwise selection were run and compared, then repeated on a log-transformed response once diagnostics showed the untransformed model violating constant variance. Predictors span the obvious (square footage, bedrooms, bathrooms, grade) and the geographic — latitude, longitude, and zipcode carry real signal in a market this segmented.

**Quality classification.** A logistic model predicting good-quality homes from listing characteristics, reduced to `condition` and `price` as predictors after p-value screening, with every remaining coefficient significant at 0.05.

## Results

The classifier reached an **AUC of 0.919**. At the default 0.5 threshold it gave 88.5% accuracy but only 0.548 sensitivity — it was missing nearly half the genuinely good-quality homes, which for a buyer-facing screening tool is the wrong error to make.

Lowering the threshold to **0.25** traded almost nothing on accuracy (88.2%) for a meaningful sensitivity gain to **0.650**, holding specificity at 0.915. That tradeoff — accepting more false positives to stop missing real candidates — is the substantive decision in the project, and it's driven by what the model is for rather than by the metric alone.

## Method

- Date parsing and cleaning; `grade` binned into low/average/high quality tiers; `waterfront` and renovation status coded as factors
- Stepwise selection in both directions, compared rather than assumed
- Log transformation of the response after residual diagnostics
- Multicollinearity checked via VIF
- Univariate, bivariate, and multivariate visualization before modeling
- Geographic visualization via `ggmap`
- ROC curves and confusion matrices at multiple thresholds

## Repository layout

```
kc_housing_analysis.Rmd      Full analysis — narrative, code, and figures
data/kc_house_data.csv       21,613 King County home sales
report/                      Knitted PDF
```

## Running it

```r
install.packages(c("MASS", "dplyr", "tidyr", "readr", "ggmap", "scales",
                   "ggplot2", "tidyverse", "GGally", "car", "ROCR", "pROC"))
```

The `ggmap` geographic plots need a Google Maps API key. Set it as an environment variable rather than hardcoding it:

```r
Sys.setenv(GOOGLE_MAPS_API_KEY = "your-key-here")
```

Or add it to a `.Renviron` file, which `.gitignore` already excludes. Everything except the map visualizations runs without a key.

## Notes

Group project (Group 18), MS Data Science, University of Virginia — with Maneesh Malpeddi, Philip Sadighian, and David Kim.
