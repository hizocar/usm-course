# 📊 Workforce Analytics: Salary Drivers & Compensation Modeling

> **Status:** Draft proposal — pending review before official publication.
> **Edition:** 2026 | **Assessment:** Project 1 (30% of final grade)
> **Dates:** Release Apr 4 → Due Apr 20, 23:59 → Presentations Apr 21–22

---

## 🎯 Context & Objective

A multinational consulting firm has been hired by **TalentCo**, a company operating across multiple regions and departments, to analyze its **compensation structure**. The HR team suspects that salaries are not being assigned consistently — some employees feel underpaid relative to peers with similar experience, while others in the same department earn significantly different amounts depending on their region or education level.

Your role is to act as a **Data Analyst** at the consulting firm. You will use **Python**, **statistical hypothesis testing**, and **linear regression** to identify the key drivers of employee salary and deliver data-driven recommendations to TalentCo's leadership.

By the end of this project, you will be able to:

- Perform **exploratory data analysis (EDA)** on HR data.
- Apply **hypothesis testing** to validate business assumptions.
- Handle **categorical variables** using encoding techniques.
- Build and evaluate **OLS and scikit-learn regression models**.
- Check **regression assumptions** (multicollinearity, normality, homoscedasticity).
- Communicate results in a **business-oriented report**.

---

## 📂 Dataset: `employee_compensation.csv`

> Available in the [datasets section](../datasets/datasets.md).

The dataset contains records for **1,500 employees** across departments, regions, and job levels.

| Variable | Type | Description |
|---|---|---|
| `Salary` | Numerical (USD/year) | Annual gross salary — **target variable** |
| `Years_Experience` | Numerical | Total years of professional experience |
| `Age` | Numerical | Employee age |
| `Performance_Score` | Numerical (1.0–5.0) | Most recent annual performance rating |
| `Education_Level` | Categorical | `Bachelor`, `Master`, `PhD` |
| `Department` | Categorical | `Engineering`, `Sales`, `Marketing`, `HR`, `Finance` |
| `Region` | Categorical | `North`, `South`, `East`, `West` |
| `Job_Level` | Categorical | `Junior`, `Mid`, `Senior` |
| `Gender` | Categorical | `Male`, `Female` |

> **Note on dataset generation:** This dataset can be generated synthetically using `numpy` and `pandas` with realistic salary distributions per department and job level. A generation script (`generate_dataset.py`) will be included alongside the dataset.

---

## 📝 Part 1: Exploratory Data Analysis (EDA)

1. Load the dataset into a pandas DataFrame.
2. Display the first 10 rows and inspect data types.
3. Check for missing values and duplicates — report findings.
4. Compute descriptive statistics (mean, median, std, min, max) for all numerical variables.
5. Create **at least three** of the following visualizations:
   - Histogram of `Salary`.
   - Box plot of `Salary` by `Job_Level`.
   - Scatter plot of `Years_Experience` vs. `Salary`.
   - Bar plot of average `Salary` by `Department`.
   - Heatmap of correlations between numerical variables.

---

## 🧪 Part 2: Hypothesis Testing

**Business Question:** Does years of experience significantly drive salary differences?

1. Define the hypotheses:
   - **H₀:** Years of experience has no linear relationship with salary.
   - **H₁:** Years of experience is positively associated with salary.

2. Use **Pearson correlation** and a **t-test** to evaluate the relationship.
3. Report the **p-value** and interpret the result at a **5% significance level**.
4. *(Bonus)* Run a second hypothesis test: Is there a statistically significant salary difference between `Junior` and `Senior` employees? Use a **two-sample t-test**.

---

## 🤖 Part 3: Linear Regression with Categorical Variables

### Step 1: Variable Justification (before coding)

Before building any model, answer the following in your notebook:

1. Which variables do you expect to be **strong predictors** of salary, and why?
2. Are there variables that, even if they turn out to be statistically insignificant, you would still include in the model? **Justify based on business or theoretical reasoning.**
3. Do you expect any two variables to be strongly correlated with each other? Which ones, and why? What problems could that cause?

> This step should be answered in plain text — no code required yet. The goal is to articulate your prior beliefs before looking at the data, so you can later compare them against what the model tells you.

### Step 2: Data Preparation
1. Apply **Label Encoding** for `Job_Level` (ordinal variable: Junior < Mid < Senior).
2. Apply **One Hot Encoding** for `Education_Level`, `Department`, `Region`, and `Gender`.
3. Drop the first dummy variable in each group to avoid perfect multicollinearity (use `drop_first=True`).

### Step 3: Model Building
1. Split the dataset into **training (80%)** and **test (20%)** sets. Use `random_state=42`.
2. Build two models using **all available variables**:
   - **OLS regression** using `statsmodels`.
   - **Linear Regression** using `scikit-learn`.

### Step 4: Model Evaluation
Compute and report metrics for **both training and test sets**:

| Metric | Description |
|---|---|
| **MAE** | Mean Absolute Error |
| **MAPE** | Mean Absolute Percentage Error |
| **RMSE** | Root Mean Squared Error |
| **R²** | Coefficient of determination |
| **Adjusted R²** | R² penalized for number of predictors |

Compare the OLS summary (coefficients, p-values, R²) with the scikit-learn output.

---

## 🔧 Part 4: Model Analysis & Refinement

> ⚠️ **Important note:** The goal of this section is **not** to apply mechanical rules. There is no universal threshold that tells you whether to include or exclude a variable. Every decision must be argued from the context of the problem.

### Step 1: Interpreting Coefficient p-values

Examine the p-values from the OLS summary and answer the following questions for **each variable with p-value > 0.05**:

- Is the variable **statistically insignificant** because it has no real effect, or could there be another explanation (e.g., it is correlated with another variable already in the model)?
- Is this variable **conceptually important** for the salary analysis, even if the model does not find a significant effect? Would dropping it change the interpretation of other coefficients?
- What is the **business cost** of removing this variable from the model?

Based on this analysis, **decide which variables to keep or remove — and justify each decision explicitly**. Re-train the model with your selected variables and compare metrics against the full model.

> Example of the reasoning expected: *"Gender has p = 0.18 in our model. However, we choose to keep it because (1) salary equity by gender is one of TalentCo's core questions, and (2) its insignificance may be driven by collinearity with Job_Level rather than absence of effect. Removing it would obscure a key dimension of the analysis."*

### Step 2: Multicollinearity Analysis (VIF)

Compute the **Variance Inflation Factor (VIF)** for all independent variables and answer:

- Which pairs of variables are likely causing high VIF, and **why does that correlation exist** in this context?
- Does the presence of multicollinearity **invalidate the model's predictions**, or does it mainly affect the reliability of individual coefficients?
- For each variable with high VIF: what would be **lost** if you removed it? Is that loss acceptable given your research question?

**Decide whether to act on the multicollinearity and justify your decision.** Valid answers include keeping collinear variables if the reasoning supports it.

> Example of the reasoning expected: *"`Age` and `Years_Experience` show VIF = 8.4 and 9.1 respectively, reflecting that older employees tend to have more experience. Both variables capture different dimensions — biological age vs. accumulated professional time — so we keep both and acknowledge the inflated standard errors as a limitation."*

### Step 3: Regression Assumptions

Verify the following assumptions of linear regression on your **final model**:

- **Normality of residuals:** Histogram and Q-Q plot of residuals.
- **Homoscedasticity:** Perform the **Breusch-Pagan test** (`het_breuschpagan`).

For each assumption, state whether it holds, show the evidence, and briefly discuss what it means for the validity of your conclusions.

---

## 📈 Part 5: Business Report

Write a **short business report (300–400 words)** addressed to TalentCo's HR Director, answering:

1. Which variables are the **strongest drivers** of employee salary?
2. How does **years of experience** quantitatively impact annual compensation?
3. Are there **significant salary differences** across departments or regions? What do they suggest?
4. Based on your final model: if TalentCo promotes an employee from `Mid` to `Senior` level, what is the **expected salary increase** (in USD)?
5. What **recommendations** would you make to TalentCo to improve compensation fairness and transparency?

---

## ✅ Deliverables

Each student or group must submit the following:

1. **Jupyter Notebook (`.ipynb`)** with all code, outputs, and inline comments.
   - Code must be clean, well-commented, and reproducible.
   - All plots must include titles, axis labels, and legends where applicable.

2. **Business Report** in Markdown (`.md`) or Word (`.docx`) format — 300 to 400 words, addressed to a non-technical audience.

3. **Oral Presentation** — 10 to 12 minutes (see rubric below).

> ⚠️ **Important:** Both the written submission and the oral presentation are **mandatory**. Missing either component results in the **minimum grade** for the project.

---

## 💡 Tips

- Use `pandas`, `numpy`, `matplotlib`, `seaborn`, `scikit-learn`, and `statsmodels`.
- For VIF: `from statsmodels.stats.outliers_influence import variance_inflation_factor`
- For Breusch-Pagan: `from statsmodels.stats.diagnostic import het_breuschpagan`
- For Q-Q plot: `import scipy.stats as stats; stats.probplot(residuals, plot=plt)`
- Focus on **business interpretation**, not just statistical output.
- In your report, write for an HR Director — no formulas, no code, no p-values. Just clear insights.

---

---

# 🎤 Oral Presentation Rubric — Workforce Analytics Project

Each group will be evaluated on a scale from **10 to 100 points**.
All presentations must be delivered in **English or Spanish** and last between **10–12 minutes**.

---

## Evaluation Criteria

| **Category** | **Description** | **Points Range** | **Weight** |
|---|---|---|---|
| 🧭 **1. Problem Understanding & Context** | Demonstrates a clear understanding of TalentCo's business problem and the value of data-driven compensation analysis. Connects statistical findings to real HR decisions. | 10–20 | **20%** |
| 📊 **2. Data Exploration & Insights** | Presents key EDA findings clearly. Highlights meaningful patterns (e.g., salary by department, experience trends). Explains how the analysis guided modeling choices. | 10–20 | **20%** |
| 🤖 **3. Model Explanation & Results** | Explains regression models, encoding decisions, and evaluation metrics (MAE, RMSE, R²) accurately. Compares OLS vs. sklearn outputs and justifies the final model. | 10–20 | **20%** |
| 🎯 **4. Business Interpretation & Recommendations** | Translates model coefficients and test results into actionable HR recommendations. Explains what TalentCo should do and why, without using technical jargon. | 10–20 | **20%** |
| 🗣️ **5. Communication & Delivery** | Clear and professional delivery in English or Spanish. Logical flow, confident tone, and effective teamwork. Stays within the time limit. | 10–15 | **15%** |
| 🖼️ **6. Visual Aids & Slide Design** | Slides are clear, professional, and support the narrative. Charts and tables are legible and well-labeled. Avoids text-heavy or cluttered slides. | 10–15 | **15%** |

---

## Scoring Guide

| **Score Range** | **Level** | **Description** |
|---|---|---|
| **90–100** | ⭐ Excellent | Outstanding understanding, strong business insight, and engaging delivery. Clear link between data analysis and HR strategy. |
| **80–89** | ✅ Very Good | Solid explanation and visuals, minor gaps in depth or flow. Good technical and business command. |
| **70–79** | ⚙️ Good | Covers main points adequately but lacks depth or clarity in some areas. Reasonable structure. |
| **60–69** | ⚠️ Acceptable | Basic coverage with limited business insight or weak connection to the HR problem. Somewhat unclear or rushed. |
| **10–59** | ❌ Insufficient | Poor structure, missing key content, or failure to communicate findings. May significantly exceed or fall short of time. |

---

## Final Score Calculation

\[
\text{Final Score} = 0.20(\text{Problem}) + 0.20(\text{EDA}) + 0.20(\text{Models}) + 0.20(\text{Business}) + 0.15(\text{Delivery}) + 0.15(\text{Visuals})
\]

---

### Notes for Evaluators
- Deduct up to **10 points** for presentations outside the time range (under 7 min or over 13 min).
- Deduct up to **5 points** if not all team members participate actively.
- Award **bonus up to +5 points** for exceptional insight, creativity, or real-world connection.

---

**Total:** 100 points maximum | **Minimum passing score:** 60 points
