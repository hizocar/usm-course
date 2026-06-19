# 💳 Credit Risk Analytics: Predicting Loan Default

> **Edition:** 2026 | **Assessment:** Project 2 (30% of final grade)
> **Dates:** Release May 6 → Due May 14, 23:59 → Presentations Jun 2–4

---

## 🎯 Context & Objective

**FinTrust Bank** is a regional lender that processes thousands of personal and business loan applications each year. Over the last two years, the bank's default rate has been rising — causing significant losses and putting pressure on the credit risk team.

The Chief Risk Officer has commissioned the data team to build a **predictive model that identifies applicants who are likely to default** before a loan is approved. A reliable model would allow the bank to adjust interest rates, require collateral, or decline high-risk applications early in the process.

Your role is to act as a **Data Scientist** on the credit risk team. You will clean and explore the data, build and compare multiple classification models, and deliver actionable recommendations to FinTrust's risk committee.

By the end of this project, you will be able to:

- Detect and fix **data quality issues** (missing values, outliers, inconsistent categories, duplicates).
- Perform a **comprehensive EDA** to understand credit risk drivers.
- Build and tune **four classification models**: Logistic Regression, Decision Tree, Random Forest, and XGBoost.
- Evaluate and compare models using **business-relevant metrics**.
- Extract and interpret **feature importance**.
- Communicate technical findings to a non-technical audience.

---

## 📂 Dataset: `loan_default.csv`

> Available in the [datasets section](../datasets/datasets.md).

The dataset contains **~10,000 loan applications** processed by FinTrust Bank.  
Your task is to **predict the variable `default`**: whether a borrower defaulted (`1`) or repaid the loan (`0`).

> ⚠️ **Warning:** This dataset was extracted directly from the bank's legacy CRM system and has **not been cleaned**. You will encounter missing values, outliers, inconsistent category labels, duplicate records, and impossible values. Identifying and fixing these issues is a core part of this project.

| Column | Type | Description |
|---|---|---|
| `loan_id` | ID | Unique loan identifier — do not use as a feature |
| `age` | Numerical | Applicant's age (years) |
| `annual_income` | Numerical | Gross annual income (USD) |
| `employment_type` | Categorical | `full_time`, `part_time`, `self_employed`, `unemployed` |
| `years_employed` | Numerical | Years at current employer |
| `loan_amount` | Numerical | Total loan amount requested (USD) |
| `loan_term_months` | Numerical | Loan duration in months (12, 24, 36, 48, 60) |
| `interest_rate` | Numerical | Annual interest rate (%) |
| `loan_purpose` | Categorical | `personal`, `home`, `car`, `education`, `business` |
| `credit_score` | Numerical | Credit bureau score (valid range: 300–850) |
| `num_credit_lines` | Numerical | Number of active credit lines |
| `debt_to_income_ratio` | Numerical | Total debt payments / gross income (0 to 1) |
| `has_collateral` | Binary | Whether the loan is backed by collateral (1 = yes, 0 = no) |
| `num_late_payments` | Numerical | Number of late payments in borrower's history |
| `region` | Categorical | `North`, `South`, `East`, `West` |
| `default` | Binary | **Target variable** — 1 = defaulted, 0 = repaid |

---

## 📝 Part 1: Data Cleaning

Before any analysis, you must audit and fix the dataset. Document every issue you find and every decision you make.

### 1.1 Initial Audit

1. Load the dataset and inspect its shape, dtypes, and the first 10 rows.
2. Report:
   - Number of **duplicate rows** — drop them.
   - **Missing values** per column (count and percentage).
   - **Data type issues** — are all columns stored in the correct type?

### 1.2 Fix Data Type Issues

- Identify the column stored as a string that should be numeric. Fix it.
- After fixing, ensure all numerical columns are `float` or `int`.

### 1.3 Fix Inconsistent Categories

- Inspect the unique values of `employment_type` and `loan_purpose`.
- Standardize all values to the canonical lowercase form (e.g., `"Full-Time"`, `"FULL TIME"`, `"fulltime"` → `"full_time"`).
- Justify your mapping decisions.

### 1.4 Fix Impossible / Outlier Values

For each numerical column, check for values that are **logically impossible**:

| Column | Valid Range | Action |
|---|---|---|
| `age` | 18–100 | Replace out-of-range with `NaN` |
| `credit_score` | 300–850 | Replace out-of-range with `NaN` |
| `years_employed` | ≥ 0 | Replace negative values with `NaN` |
| `num_late_payments` | ≥ 0 | Replace negative values with `NaN` |
| `annual_income` | Reasonable range | Investigate extreme values — decide and justify |

### 1.5 Handle Missing Values

For each column with missing values, **choose and justify** an imputation strategy:

- Numerical variables: mean, median, or model-based imputation.
- Consider whether the missingness pattern is random or informative.

> 💡 After imputation, verify that no missing values remain before modeling.

### 1.6 Cleaning Summary Table

Provide a summary table listing every issue found, the fix applied, and the number of rows affected.

---

## 📊 Part 2: Exploratory Data Analysis (EDA)

### 2.1 Target Variable

- Plot the distribution of `default` (bar chart with proportions).
- Report the **class imbalance ratio**.
- Discuss: is the imbalance severe enough to require special treatment during modeling?

### 2.2 Univariate Analysis

For **numerical variables**: histograms or KDE plots highlighting the distribution shape.  
For **categorical variables**: bar charts showing value frequencies.

### 2.3 Bivariate Analysis — Relationship with Default

Explore how each feature relates to the target variable. Include at least:

- Box plots of `credit_score`, `annual_income`, `debt_to_income_ratio`, and `num_late_payments` grouped by `default`.
- Default rate bar charts for each categorical variable (`employment_type`, `loan_purpose`, `region`).
- A **correlation heatmap** of all numerical features.

For each finding, write **one sentence** summarizing the business implication.

### 2.4 Key Insights (4–6 bullet points)

Summarize the most important patterns discovered during EDA. These insights should directly motivate your modeling decisions.

---

## 🤖 Part 3: Data Preparation for Modeling

1. **Encode categorical variables:**
   - Apply **One-Hot Encoding** to `employment_type`, `loan_purpose`, and `region`.
   - Drop the first dummy in each group (`drop_first=True`) to avoid multicollinearity.

2. **Feature scaling:**
   - Apply **StandardScaler** to numerical features for Logistic Regression.
   - Tree-based models (Decision Tree, Random Forest, XGBoost) do not require scaling — explain why.

3. **Train/test split:**
   - Split into **80% training / 20% test** using `random_state=42`.
   - Use **stratification** on `default` to preserve the class ratio.

4. **Class imbalance:**
   - Check the default rate in the training set.
   - If imbalance is significant, apply `class_weight='balanced'` in the models that support it, or use **SMOTE** on the training set only.
   - Justify your choice.

---

## 🧠 Part 4: Modeling

Build four models in this order. For each model, report training and test performance.

---

### Model 1: Logistic Regression (Baseline)

- Train with default hyperparameters first, then with `class_weight='balanced'`.
- Report the **confusion matrix** and the **classification report** (precision, recall, F1).
- Interpret the **top 5 coefficients** in business terms:  
  > *"A one-unit increase in `num_late_payments` is associated with an increase of X in the log-odds of default, holding all else constant."*

---

### Model 2: Decision Tree

- Perform **hyperparameter tuning** using `GridSearchCV` on at least:
  - `max_depth`: [3, 5, 7, 10]
  - `min_samples_leaf`: [10, 20, 50]
- Plot the **pruned tree** (max depth 3 for readability).
- Discuss: what is the risk of setting `max_depth` too high?

---

### Model 3: Random Forest

- Tune with `RandomizedSearchCV` on:
  - `n_estimators`: [100, 200, 300]
  - `max_depth`: [5, 10, 15, None]
  - `min_samples_leaf`: [5, 10, 20]
- Use `class_weight='balanced'`.
- Report **out-of-bag (OOB) error** if applicable.

---

### Model 4: XGBoost

- Tune at least:
  - `learning_rate`: [0.01, 0.05, 0.1]
  - `n_estimators`: [100, 200, 300]
  - `max_depth`: [3, 5, 7]
- Use `scale_pos_weight` to handle class imbalance (set to `n_negative / n_positive`).
- Discuss why **boosting** can outperform a single decision tree.

---

## 📈 Part 5: Model Evaluation & Comparison

### 5.1 Metrics Table

For each model, compute on the **test set**:

| Model | ROC-AUC | Precision | Recall | F1-Score | Accuracy |
|---|---|---|---|---|---|
| Logistic Regression | | | | | |
| Decision Tree | | | | | |
| Random Forest | | | | | |
| XGBoost | | | | | |

> **Primary metric: ROC-AUC.** Accuracy alone is misleading with imbalanced classes.

### 5.2 ROC Curves

Plot all four ROC curves on a single figure. Include the AUC in the legend for each model.

### 5.3 Threshold Analysis

For your **best model**:

- Plot Precision, Recall, and F1-score as a function of the classification threshold (0.1 to 0.9).
- Answer: *Is it more important for FinTrust Bank to maximize Recall or Precision?*
  - **Recall** → catch as many defaulters as possible, even if some good borrowers are rejected.
  - **Precision** → avoid rejecting too many good borrowers, even if some defaulters slip through.
- Select an **optimal threshold** and justify the business reasoning.

### 5.4 Best Model Selection

Choose one model as your final recommendation. Justify your choice considering:

- Predictive performance (AUC, F1).
- Interpretability for the risk committee.
- Computational cost at inference time.

---

## 🔍 Part 6: Feature Importance

For your **two best models**, extract and visualize feature importance:

- **Random Forest:** `feature_importances_` (Gini impurity reduction) — bar chart of top 10 features.
- **XGBoost:** `plot_importance()` using `importance_type='gain'`.
- **Logistic Regression:** plot the absolute value of standardized coefficients.

Then answer:

1. Which **three features** are consistently the most important across models?
2. Are there features that appear important in one model but not another? Why might that happen?
3. Is there a feature that is **statistically important** but might raise ethical concerns in lending decisions? Discuss briefly.

---

## 💼 Part 7: Business Report

Write a **short business report (300–400 words)** addressed to FinTrust Bank's Chief Risk Officer (non-technical audience). It must answer:

1. What is the **estimated financial impact** of loan defaults in the dataset? (Use average loan amount × default rate as a rough proxy.)
2. Which **borrower profiles** are most at risk of defaulting?
3. What model would you recommend, and why?
4. At the chosen threshold, how many defaults out of 1,000 new applicants would the model **correctly flag**?
5. What **three actions** should FinTrust take over the next quarter to reduce default risk?

> Write for a business audience — no code, no formulas, no p-values. Use plain language and concrete numbers.

---

## ✅ Deliverables

Each student or group must submit:

1. **Jupyter Notebook (`.ipynb`)** — full analysis with all code, outputs, and visualizations.
   - Every cleaning decision must be explicitly justified in a markdown cell.
   - All plots must have titles, axis labels, and legends.
   - Code must be clean and reproducible end-to-end.

2. **Business Report** — 300–400 words in Markdown or Word, addressed to the Chief Risk Officer.

3. **Oral Presentation** — 12–15 minutes (see rubric below).

> ⚠️ **Important:** Both the written submission and the oral presentation are **mandatory**. Missing either component results in the **minimum grade** for the project.

---

## 💡 Tips

- For XGBoost: `pip install xgboost` or `conda install xgboost`. Import with `from xgboost import XGBClassifier`.
- For SMOTE: `from imblearn.over_sampling import SMOTE` — apply **only on the training set**.
- For `scale_pos_weight` in XGBoost: compute `(df['default'] == 0).sum() / (df['default'] == 1).sum()`.
- For ROC curves: `from sklearn.metrics import roc_curve, roc_auc_score`.
- For threshold analysis: use `model.predict_proba(X_test)[:, 1]` and loop over thresholds.
- Remember: `GridSearchCV` with `scoring='roc_auc'` is more appropriate than `scoring='accuracy'` for imbalanced problems.

---

---

# 🎤 Oral Presentation Rubric — Credit Risk Analytics Project

Each group will be evaluated on a scale from **10 to 100 points**.  
All presentations must be delivered in **English or Spanish** and last between **12–15 minutes**.

---

## Evaluation Criteria

| **Category** | **Description** | **Points Range** | **Weight** |
|---|---|---|---|
| 🧭 **1. Problem Understanding & Context** | Demonstrates a clear understanding of FinTrust's credit risk problem and the business value of predicting default. Connects data science work to lending decisions. | 10–20 | **20%** |
| 📊 **2. Data Cleaning & EDA** | Clearly explains the data quality issues found, the fixes applied, and key insights from the exploratory analysis. Shows how EDA findings guided modeling decisions. | 10–20 | **20%** |
| 🤖 **3. Model Comparison & Results** | Explains the four models, tuning strategy, and evaluation metrics accurately. Shows a clear comparison table and ROC curves. Justifies the final model selection. | 10–20 | **20%** |
| 🎯 **4. Business Interpretation & Recommendations** | Translates model outputs into actionable lending decisions. Explains threshold choice and its business consequences. Provides concrete risk management recommendations. | 10–20 | **20%** |
| 🗣️ **5. Communication & Delivery** | Clear and professional delivery. Logical flow, confident tone, and effective teamwork. Stays within the time limit. | 10–15 | **15%** |
| 🖼️ **6. Visual Aids & Slide Design** | Slides are clear and professional. Charts (ROC curves, feature importance, confusion matrices) are legible and support the narrative. Avoids cluttered or text-heavy slides. | 10–15 | **15%** |

---

## Scoring Guide

| **Score Range** | **Level** | **Description** |
|---|---|---|
| **90–100** | ⭐ Excellent | Outstanding command of both technical and business dimensions. Insightful threshold discussion and engaging delivery. |
| **80–89** | ✅ Very Good | Strong explanation and visuals, minor gaps in depth or flow. Solid technical and business grasp. |
| **70–79** | ⚙️ Good | Covers main points adequately but lacks depth or clarity in some areas. Reasonable structure. |
| **60–69** | ⚠️ Acceptable | Basic coverage with limited insight or weak connection to the business problem. Somewhat unclear or rushed. |
| **10–59** | ❌ Insufficient | Poor structure, missing key content, or failure to communicate findings effectively. |

---

## Final Score Calculation

\[
\text{Final Score} = 0.20(\text{Problem}) + 0.20(\text{Cleaning \& EDA}) + 0.20(\text{Models}) + 0.20(\text{Business}) + 0.15(\text{Delivery}) + 0.15(\text{Visuals})
\]

---

### Notes for Evaluators
- Deduct up to **10 points** for presentations outside the allowed time range (under 8 min or over 16 min).
- Deduct up to **5 points** if not all team members participate actively.
- Award **bonus up to +5 points** for exceptional creativity, threshold analysis depth, or real-world business insight.

---

**Total:** 100 points maximum | **Minimum passing score:** 60 points
