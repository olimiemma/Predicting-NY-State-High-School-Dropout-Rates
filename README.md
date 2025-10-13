# Predicting NY State High School Dropout Rates using Count Regression Models

![Python](https://img.shields.io/badge/Python-3.9+-blue?style=for-the-badge&logo=python)
![Pandas](https://img.shields.io/badge/Pandas-2.0-blue?style=for-the-badge&logo=pandas)
![Statsmodels](https://img.shields.io/badge/Statsmodels-0.14-blue?style=for-the-badge)
![Scikit-learn](https://img.shields.io/badge/Scikit--Learn-1.3-blue?style=for-the-badge&logo=scikit-learn)

A comprehensive data science project applying the full modeling lifecycle to predict the number of student dropouts (`dropout_cnt`) from the 2018-2019 NY State High School graduation metrics dataset. This project emphasizes a statistically rigorous approach, moving from a baseline Linear Regression to more appropriate Poisson and Negative Binomial count models.

The final, preferred model is a **Negative Binomial Generalized Linear Model (GLM)** that successfully accounts for the severe overdispersion in the target variable and uses a log-transformed offset to model dropout rates fairly across school districts of varying sizes.

**Final Model Performance (on Unseen Test Data):**
*   **Root Mean Squared Error (RMSE): 23.64**

---

## Table of Contents

1.  [Project Objective](#project-objective)
2.  [Dataset](#dataset)
3.  [Methodology & Workflow](#methodology--workflow)
4.  [Key Findings & Insights](#key-findings--insights)
5.  [Repository Structure](#repository-structure)
6.  [How to Run This Project](#how-to-run-this-project)
7.  [Key Dependencies](#key-dependencies)

---

## Project Objective

The primary goal of this project is to construct, evaluate, and compare a series of regression models to predict the count of student dropouts within specific NY State school districts and student subgroups. The project serves as a practical application of regression techniques for numeric and count-based response variables, emphasizing the importance of model selection based on data characteristics.

The project fulfills the following requirements:
*   Implementation of the full data science lifecycle: Data Cleaning → EDA → Feature Engineering → Modeling → Evaluation.
*   Construction of at least two variants each of Multiple Linear Regression, Poisson Regression, and Negative Binomial Regression.
*   Rigorous model comparison using appropriate statistical metrics (AIC, BIC, Log-Likelihood, RMSE).
*   Demonstration of model stability using K-Fold Cross-Validation.
*   Interpretation of final model coefficients to derive actionable insights.

---

## Dataset

*   **Source:** NY State Education Department (NYSED)
*   **File:** `Project1_Data.csv` (not included in repo, loaded via URL in notebook)
*   **Description:** The dataset contains over 73,000 observations pertaining to NY State High School graduation metrics for the 2018-2019 school year. Each row represents a specific student subgroup within a school district.
*   **Target Variable:** `dropout_cnt` (a non-negative integer representing the number of students who discontinued enrollment).

---

## Methodology & Workflow

The project follows a structured, multi-stage workflow, documented in `regression.ipynb`.

### 1. Exploratory Data Analysis (EDA)
The initial analysis of the raw data revealed two critical characteristics that guided our entire modeling strategy:
*   **Severe Overdispersion:** A histogram and statistical summary of `dropout_cnt` showed that its variance was significantly greater than its mean. This finding immediately suggested that a standard Poisson model would be inadequate and that a **Negative Binomial model** would be required.
*   **Need for Rate Modeling:** To fairly compare school districts of different sizes, we identified that modeling the raw `dropout_cnt` was statistically unsound. This led to the decision to model the *rate* of dropouts.

### 2. Data Cleaning & Preparation
*   Handled suppressed data placeholders (`-`) by converting them to `NaN` and removing rows with missing target values.
*   Removed redundant, constant, and high-cardinality columns (`aggregation_name`) that were not suitable for modeling.
*   Explicitly removed all percentage-based (`_pct`) columns to prevent data leakage and multicollinearity.

### 3. Feature Engineering
*   **The Offset Term:** The most critical feature engineering step was the creation of a log-transformed offset variable (`log(enroll_cnt)`). Using this in our GLM models effectively transforms the prediction from a raw count to a rate, ensuring fair comparisons across different cohort sizes.
*   **Train/Test Split:** The data was split into a 70% training set and a 30% holdout test set for unbiased final evaluation.

### 4. Modeling & Selection
*   **Six Models Built:** We constructed "Simple" (trimmed feature set) and "Complex" (full feature set) variants of OLS, Poisson, and Negative Binomial models.
*   **Model Comparison:** The six models were formally compared using AIC, BIC, and Log-Likelihood. The **Complex Negative Binomial model** was the clear winner on all metrics, confirming our initial EDA hypothesis.
*   **Stability Check:** A 5-fold cross-validation was performed on the winning model, which showed highly consistent performance and confirmed its stability.

## Modeling Approach

| Model                     | Description                                 | Evaluation Metrics                  | Findings                                         |
| ------------------------- | ------------------------------------------- | ----------------------------------- | ------------------------------------------------ |
| **OLS Regression**        | Baseline linear model                       | R², Adj-R², RMSE                    | Poor handling of variance; unrealistic negatives |
| **Poisson GLM**           | Log-link count model                        | Log-Likelihood, AIC, Deviance       | Better fit, but over-dispersion present          |
| **Negative Binomial GLM** | Poisson extension with dispersion parameter | Log-Likelihood, AIC, Deviance, RMSE | Best overall fit; handles variance effectively   |


## Model Performance Summary
| Model                 | Log-Likelihood | Deviance   | RMSE (Hold-out) | Remarks                     |
| --------------------- | -------------- | ---------- | --------------- | --------------------------- |
| OLS                   | −123,370       | 246,900    | 39.12           | Baseline model              |
| Poisson               | −90,703        | 100,600    | 27.45           | Improved but over-dispersed |
| **Negative Binomial** | **−71,972**    | **15,841** | **23.64**       | **Preferred model**         |

The Negative Binomial model captures variability most effectively, offering strong predictive stability and interpretable coefficients.

 ## Cross-Validation Results (5-Fold)
| Fold          | Log-Likelihood          | Deviance             |
| ------------- | ----------------------- | -------------------- |
| 1             | −14,319.26              | 3,144.95             |
| 2             | −14,401.47              | 3,212.94             |
| 3             | −14,651.54              | 3,047.33             |
| 4             | −14,272.19              | 3,297.41             |
| 5             | −14,371.44              | 3,226.63             |
| **Mean ± SD** | **−14,403.18 ± 131.83** | **3,185.85 ± 84.50** |
Interpretation:
Low standard deviations confirm that the model generalizes well across folds — a key indicator of robustness.

### 5. Final Evaluation
The winning Complex Negative Binomial model was applied to the unseen test set, where it achieved a final **RMSE of 23.64**.

---

## Key Findings & Insights

The final model provided clear and interpretable insights into the factors driving dropout risk:

*   **Top Risk Factors:** The model identified vulnerable populations as having the highest risk. The **English Language Learner** subgroup had the strongest association with increased dropouts (a nearly 5x multiplicative effect on the rate).
*   **Top Protective Factors:** District resources were the most protective. Being in a **"Low Needs"** district was associated with a ~74% reduction in the expected dropout rate compared to the baseline.

---

## Repository Structure
