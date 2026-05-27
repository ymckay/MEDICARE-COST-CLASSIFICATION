# MEDICARE-COST-CLASSIFICATION
Binary classification of high-cost Medicare beneficiaries using logistic regression, decision tree, random forest, gradient boosting, and KNN ,  built in R with the CMS DE-SynPUF dataset.

# Medicare Beneficiary Cost Classification

> Can machine learning identify high-cost Medicare patients without relying on demographic variables?  
> This R-based analysis compares five supervised classifiers on 112K+ synthetic CMS records — with logistic regression achieving a recall of **0.829** for the high-cost class.

---

## Overview

Health insurance pricing depends on accurately estimating the expected cost of claims per policyholder. Mispricing can lead to profitability issues, poor resource allocation, and inequitable outcomes for patients. This project builds a binary classification model to predict whether a Medicare beneficiary falls into a **High Cost** or **Standard Cost** category, using clinical and demographic variables from a large synthetic Medicare dataset.

The analysis prioritizes **recall for the high-cost class**, reflecting the real-world consequence that failing to identify a high-cost patient carries greater risk than a false positive. Fairness is also evaluated — specifically whether demographic variables such as race, sex, and geographic region disproportionately drive predictions.

---

## Dataset

- **Source:** [CMS Linkable 2008–2010 Medicare Data Entrepreneurs' Synthetic Public Use File (DE-SynPUF)](https://www.cms.gov/research-statistics-data-and-systems/downloadable-public-use-files/synpufs)
- **File Used:** DE1.0 Sample 1 — 2009 Beneficiary Summary File
- **Raw Records:** 114,538 synthetic Medicare beneficiary records
- **Final Modeling Dataset:** 112,752 records (deceased beneficiaries excluded), 20 predictor variables
- **Note:** This dataset is synthetic and designed to preserve the structure of real Medicare claims data while protecting privacy. Findings are intended for methodological demonstration only.

---

## Problem Formulation

Total Medicare cost was calculated by summing reimbursements across inpatient, outpatient, and carrier services. Because healthcare cost data is heavily right-skewed, a `log(x + 1)` transformation was applied before defining the binary target:

| Class | Definition |
|---|---|
| **High Cost (1)** | Top 33% of log-transformed total cost |
| **Standard Cost (0)** | Remaining 67% |

---

## Features

| Category | Variables |
|---|---|
| Demographics | Age (derived), Sex, Race, Region (grouped from state codes) |
| Coverage | Months enrolled in Medicare Part A, B, HMO, Part D |
| Chronic Conditions | 11 binary indicators (Alzheimer's, Heart Failure, Chronic Kidney Disease, Cancer, COPD, Depression, Diabetes, Ischemic Heart Disease, Osteoporosis, RA/OA, Stroke/TIA) |
| Other | End-Stage Renal Disease (ESRD) indicator |

Reimbursement amount variables were used only to construct the target and were excluded from model inputs to prevent data leakage.

---

## Models Evaluated

Five supervised binary classification models were trained and tuned on a validation set before final evaluation on a held-out test set. Data was split 70% train / 15% validation / 15% test using stratified sampling.

| Model | Key Tuning Parameters |
|---|---|
| Logistic Regression | Classification threshold (0.5, 0.6, 0.7, 0.8) |
| Decision Tree | Complexity parameter (`cp`), `minsplit` |
| Random Forest | `mtry`, `min.node.size` |
| Gradient Boosting | `shrinkage`, `n.minobsinnode` |
| K-Nearest Neighbors | Number of neighbors (`k`) |

---

## Results

### Final Test Set Performance

| Model | Recall | F1 | Kappa |
|---|---|---|---|
| **Logistic Regression** | **0.829** | **0.737** | 0.585 |
| Gradient Boosting | 0.708 | 0.727 | 0.598 |
| Random Forest | 0.696 | 0.722 | 0.593 |
| Decision Tree | 0.693 | 0.711 | 0.574 |
| KNN | 0.687 | 0.712 | 0.577 |

Logistic regression achieved the highest recall and F1 score when the classification threshold was tuned to 0.7. McNemar's test confirmed statistically significant differences in classification decisions across all model pairs (p ≈ 0).

### Key Findings

- **Chronic disease indicators were the primary drivers** of high-cost classification across all models. Chronic kidney disease, ischemic heart disease, and diabetes consistently ranked in the top three predictors.
- **Demographic variables showed limited influence.** Race, sex, and geographic region contributed minimally to predictions, suggesting models are capturing clinical risk rather than demographic proxies.
- **Logistic regression outperformed ensemble methods** when the classification threshold was optimized — demonstrating that model configuration and alignment with the objective function can matter more than model complexity.
- **Ensemble methods outperformed the single decision tree**, with gradient boosting edging out random forest due to its sequential, error-correcting learning process.
- **KNN performed weakest**, likely due to the challenges of distance-based classification in a high-dimensional space.

---

## Tech Stack

- **Language:** R
- **Key Packages:** `caret`, `rpart`, `rpart.plot`, `ranger`, `gbm`, `ggplot2`, `dplyr`, `car`

---

## File Structure

```
├── README.md
├── classification_health_insurance_cost.R   # Full modeling pipeline
├── Final_Midterm_Report.pdf                 # Full written report
```

---

## How to Run

1. Download the DE1.0 Sample 1 2009 Beneficiary Summary File from the [CMS DE-SynPUF page](https://www.cms.gov/research-statistics-data-and-systems/downloadable-public-use-files/synpufs)
2. Place the CSV in the same directory as the R script
3. Install required packages:
```r
install.packages(c("dplyr", "caret", "rpart", "rpart.plot", "randomForest", 
                   "ranger", "gbm", "ggplot2", "car"))
```
4. Run `classification_health_insurance_cost.R` end to end

---

## Course Context

This project was completed as part of **ISYE 7406 — Data Mining and Statistical Learning** at Georgia Tech (Spring 2026).

---

## Author

**Yakira McKay**  
Georgia Institute of Technology  
ymckay3@gatech.edu
