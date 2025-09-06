# 📊 Business Analytics with Python: Hypothesis Testing & Advanced Linear Regression

## 🎯 Objective
The goal of this assignment is to apply **Python programming**, **hypothesis testing**, and **machine learning (linear regression with OLS)** to solve real-world **business problems**.  
By the end, you will:
- Handle both **numerical and categorical variables**.  
- Apply **encoding techniques** (One Hot Encoding, Label Encoding).  
- Build, evaluate, and improve regression models.  
- Check **multicollinearity and regression assumptions**.  
- Interpret the results in a **business context**.  

---

## 📂 Dataset
For this assignment, we will use a **sales dataset** with the following variables:

- `Marketing_Spend` (USD) → Amount spent on advertising.  
- `Price` (USD) → Selling price of the product.  
- `Sales` (units) → Number of products sold.  
- `Month` → Time indicator.  
- `Region` (categorical) → Region where the product was sold (`North`, `South`, `East`, `West`).  
- `Product_Category` (categorical) → Category of the product (`Basic`, `Premium`).  
- `Channel` (categorical) → Sales channel (`Online`, `Retail`).  

*(You can find in the datasets section.)*

---

## 📝 Part 1: Exploratory Data Analysis (EDA)
1. Load the dataset into a pandas DataFrame.  
2. Display the first 10 rows.  
3. Compute descriptive statistics (mean, median, variance, etc.).  
4. Create at least two visualizations:
   - Histogram of sales.  
   - Scatter plot of `Marketing_Spend` vs. `Sales`.  
   - Bar plot of sales by `Region`.  

---

## 🧪 Part 2: Hypothesis Testing
**Business Question:** Does marketing spend significantly increase sales?  

1. Define the hypotheses:  
   - **Null Hypothesis (H₀):** Marketing spend has no effect on sales.  
   - **Alternative Hypothesis (H₁):** Marketing spend increases sales.  

2. Use **Pearson correlation** and a **t-test** to evaluate the relationship.  
3. Report the p-value and interpret the result at a 5% significance level.  

---

## 🤖 Part 3: Linear Regression with Categorical Variables
### Step 1: Data Preparation
1. Apply **Label Encoding** (for `Product_Category`).  
2. Apply **One Hot Encoding** (for `Region` and `Channel`).  

### Step 2: Model Building (OLS and ML)
1. Split the dataset into **training (80%)** and **testing (20%)**.  
2. Build two models:
   - **OLS regression model** using `statsmodels`.  
   - **Linear Regression model** using `scikit-learn`.  

### Step 3: Model Evaluation
1. Compute and compare metrics for **training and test sets**:  
   - **MAE** (Mean Absolute Error)  
   - **MAPE** (Mean Absolute Percentage Error)  
   - **RMSE** (Root Mean Squared Error)  
   - **R²**  
   - **Adjusted R²**  
2. Compare OLS summary results (coefficients, p-values, R²) with scikit-learn results.  

---

## 🔧 Part 4: Model Improvement
### Step 1: Variable Significance
- Remove variables with **p-value > 0.05** (not statistically significant).  

### Step 2: Multicollinearity Check
- Compute **Variance Inflation Factor (VIF)** for all independent variables.  
- If VIF > 10, consider removing the variable.  

### Step 3: Assumptions of Linear Regression
- **Normality of residuals**: Use histogram and Q-Q plot.  
- **Homoscedasticity**: Perform **Breusch-Pagan test**.  
- **Multicollinearity**: Already checked with VIF.  

---

## 📈 Part 5: Business Application
Write a **short business report (300–400 words)** answering:
1. Which variables are the most important drivers of sales?  
2. How does marketing spend impact sales?  
3. What is the effect of sales channel and region on performance?  
4. If the company increases marketing spend by $10,000, how many additional units should they expect to sell (according to your final model)?  

---

## ✅ Deliverables
- Python notebook (`.ipynb`) or script (`.py`) with all code and results.  
- Final business report in **Markdown** or **Word** format.  

---

## 💡 Tips
- Use `pandas`, `numpy`, `matplotlib`, `scikit-learn`, and `statsmodels`.  
- For VIF: `from statsmodels.stats.outliers_influence import variance_inflation_factor`.  
- For heteroscedasticity: `from statsmodels.stats.diagnostic import het_breuschpagan`.  
- Comment your code clearly.  
- Focus on **business interpretation**, not only statistical results.  

---
