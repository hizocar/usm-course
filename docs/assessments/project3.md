# 🧠 Classification Challenge — Predicting Customer Churn for ABC Multistate Bank

## 🎯 Context & Objective

ABC Multistate Bank has recently observed a growing number of customers closing their accounts. Losing customers not only reduces immediate revenue but also increases marketing costs to acquire new ones. To address this issue, the **data team** has been asked to develop a **classification model** that predicts whether a customer is likely to **churn** (leave the bank) in the near future.

Your mission is to act as a **Data Expert** at ABC Bank and build predictive models that can help the business team identify customers at risk of leaving — so the bank can take action before it’s too late.

---

## 🧩 About the Dataset

You are provided with a dataset containing information about 10,000 bank customers.  
Your task is to **predict the variable `churn`**, which indicates whether a customer has left the bank (`1`) or not (`0`).

| Column | Description |
|--------|--------------|
| `customer_id` | Unique ID (not used for modeling) |
| `credit_score` | Customer credit score |
| `country` | Customer’s country (France, Spain, Germany) |
| `gender` | Male or Female |
| `age` | Customer’s age |
| `tenure` | Years of relationship with the bank |
| `balance` | Account balance |
| `products_number` | Number of products held |
| `credit_card` | Whether the customer has a credit card (1/0) |
| `active_member` | Whether the customer is active (1/0) |
| `estimated_salary` | Estimated annual salary |
| `churn` | **Target variable** (1 = churned, 0 = retained) |

---

## 🧭 Project Roadmap (Challenge Structure)

You will deliver a **full analytical report**, covering both **technical** and **business** aspects.  
Your notebook or report must include the following sections:

---

### **1. Introduction**
- Introduce the business problem in your own words.  
- Explain why predicting churn is valuable for ABC Bank.  
- Define the project goal clearly.

---

### **2. Exploratory Data Analysis (EDA)**
Perform a **strong exploratory analysis** to understand the data and extract insights.

Your EDA must include:

- **Data overview:** structure, types, duplicates, missing values.
- **Univariate analysis:** distributions of numerical variables, proportions of categorical variables.
- **Bivariate analysis:** relationships between predictors and churn.
- **Outlier detection:** identify extreme values using IQR or z-scores.
- **Missing values analysis:** describe how many and where, and hypothesize why they may be missing.

Then, **decide and justify**:
- How will you treat outliers? (e.g., winsorization, removal, transformation)
- How will you impute missing data? (e.g., mean/median, mode, predictive methods)
- Are there variables to be dropped or transformed?

---

### **3. Data Preparation**
- Encode categorical variables (e.g., One-Hot Encoding).
- Scale numerical features where necessary.
- Split your data into **training and test sets** using stratification.
- Consider if there is **class imbalance** and how you would handle it (e.g., SMOTE, class weights).

---

### **4. Modeling Phase**
You must build **four classification models**:

1. **Logistic Regression**  
   - Use as a baseline model.  
   - Explain coefficients and interpret them in terms of churn probability.

2. **Decision Tree**  
   - Perform **hyperparameter tuning** (e.g., `max_depth`, `min_samples_split`, `min_samples_leaf`).

3. **Random Forest**  
   - Use **randomized or grid search** for hyperparameters (e.g., number of trees, depth, leaf size).

4. **Gradient Boosting**  
   - Tune learning rate, depth, and number of estimators.  
   - Discuss why boosting might outperform other models.

---

### **5. Model Evaluation**
Evaluate all models using the following metrics:

- **ROC-AUC (primary metric)**  
- **Precision, Recall, F1-score**
- **Confusion matrix**

Create a **comparison table** summarizing performance for all models.  
Discuss which model performs best and why.

---

### **6. Optimal Threshold & Business Tradeoff**

After choosing your best model:

- Determine the **optimal decision threshold** (not necessarily 0.5).  
- Compare performance at different thresholds.
- Discuss:  
  > “Is it more important for ABC Bank to maximize Recall or Precision?”

Explain your answer in business terms:  
- **Recall** → catching as many churners as possible (minimize lost clients).  
- **Precision** → avoiding false alarms and wasting retention budget.

---

### **7. Business Analysis & Recommendations**

Once your model identifies the most important features:

- Discuss **which factors most strongly influence churn** (e.g., low credit score, high balance but inactive).  
- Suggest **business actions** for each key factor.  
  - Example: Offer personalized incentives to inactive but high-value clients.  
  - Example: Review pricing or benefits for customers with high churn probability in certain countries.

Also consider:
- What could the bank do **next quarter** to reduce churn by 10%?
- How could this model be integrated into a **customer retention campaign**?

---

### **8. Deliverables**

Each student or group must submit the following materials:

1. 🧮 **Jupyter Notebook**  
   - Must include the **full analysis**, reasoning, and all visualizations.  
   - Provide **clear justifications** for preprocessing decisions, treatment of outliers, handling of missing values, and choice of models.  
   - Show the **comparison of models** using relevant metrics (ROC-AUC, Precision, Recall, F1).  
   - End with **business-oriented conclusions** and data-driven recommendations.

2. 📄 **Executive Summary (1–2 paragraphs)**  
   - A concise written summary of your key findings.  
   - Include the selected best-performing model, a short explanation of why it was chosen, and clear recommendations for ABC Bank’s management team.

3. 🎤 **Final Presentation (12–15 minutes)**  
   - Each team must prepare a **presentation** summarizing the project.  
   - The presentation should include:  
     - The business problem and objective.  
     - Key findings from the exploratory analysis.  
     - Model comparison and results.  
     - Chosen threshold and interpretation of metrics.  
     - Business implications and recommended actions.  
   - Visuals (plots, charts, dashboards) are highly encouraged to make the presentation clear and engaging.

---

> ⚠️ **Important Evaluation Note:**  
> The final **score** for this project will be based on **both** the written report (Jupyter Notebook + Executive Summary) **and** the **oral presentation**.  
> - If a team submits only the report but does **not** present, or presents without submitting the report, the project will receive the **minimum grade**.  
> - Both components are **mandatory** and evaluated **jointly** as part of the same assessment.


--- 

# 🎤 Oral Presentation Rubric — ABC Multistate Bank Churn Prediction

Each group will be evaluated on a scale from **10 to 100 points**.  
The following rubric outlines the specific criteria and their respective weightings.  
All presentations must be delivered in **English or Spanish** and last between **12–15 minutes**.

---

## **Evaluation Criteria**

| **Category** | **Description** | **Points Range** | **Weight** |
|---------------|-----------------|------------------|-------------|
| 🧭 **1. Problem Understanding & Context** | Demonstrates a clear understanding of the business problem, the importance of predicting churn, and the project’s objective. Connects data science work to real business impact. | 10–20 | **20%** |
| 📊 **2. Data Exploration & Insights** | Presents key EDA findings clearly and concisely. Highlights meaningful patterns, trends, or anomalies. Explains how the analysis guided model development. | 10–20 | **20%** |
| 🤖 **3. Model Explanation & Comparison** | Explains models, tuning strategy, and performance metrics (AUC, precision, recall, etc.) accurately. Shows a clear comparison between models and justifies the final choice. | 10–20 | **20%** |
| 🎯 **4. Business Interpretation & Recommendations** | Translates technical results into actionable business insights. Explains what the bank should do with the results and how the model can support decisions. | 10–20 | **20%** |
| 🗣️ **5. Communication & Delivery** | Clear and professional communication in English. Logical flow, engaging tone, correct pronunciation, and confidence. Effective teamwork and time management. | 10–15 | **15%** |
| 🖼️ **6. Visual Aids & Presentation Design** | Uses slides or dashboards effectively. Visuals are clear, professional, and enhance understanding (charts, tables, key metrics). Avoids text-heavy or disorganized slides. | 10–15 | **15%** |

---

## **Scoring Guide**

| **Score Range** | **Performance Level** | **Description** |
|------------------|------------------------|-----------------|
| **90–100** | ⭐ **Excellent** | Outstanding understanding, flawless communication, and strong link between data and business. Engaging and insightful presentation. |
| **80–89** | ✅ **Very Good** | Clear explanation, strong visual support, minor issues in flow or depth. Shows solid technical and business grasp. |
| **70–79** | ⚙️ **Good** | Covers main points adequately, but lacks depth or clarity in some areas. Reasonable structure and teamwork. |
| **60–69** | ⚠️ **Acceptable** | Basic coverage of project with limited insight or weak connection to business problem. Presentation somewhat unclear or rushed. |
| **10–59** | ❌ **Insufficient** | Poor structure, unclear explanation, missing key content, or failure to communicate findings effectively. May exceed/under time or show lack of teamwork. |

---

## **Final Score Calculation**

Each category will be graded individually.  
The **final presentation score** is the weighted sum of all categories, scaled to **100 points**.

\[
\text{Final Score} = 0.20(Problem) + 0.20(EDA) + 0.20(Models) + 0.20(Business) + 0.15(Delivery) + 0.15(Visuals)
\]

---

### 🧾 Notes for Evaluators
- Deduct up to **10 points** for presentations outside the allowed time range (under 8 min or over 15 min).  
- Deduct up to **5 points** if not all team members participate.  
- Give **bonus up to +5 points** for exceptional creativity, clarity, or real business insight.

---

**Total:** 100 points maximum  
**Minimum passing score:** 60 points

