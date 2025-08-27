---
title: Causal Forests & Heterogeneous Treatment Effects
layout: page
---

# 🌲 Causal Forests & Heterogeneous Effects (HTE)

> Goal: move beyond a **single average lift** and learn **for whom** the treatment works best.  
> We estimate **individualized effects** (a.k.a. CATE) and turn them into **targeting policies**.

---

## 🧠 Concepts in 60 seconds

- **ATE (average treatment effect):** single number: “on average, B beats A by +0.4 pp.”
- **CATE / HTE:** effect **by user profile** \(x\):  
  \[
  \tau(x) = \mathbb{E}[Y(1) - Y(0) \mid X=x]
  \]
  Think: *“How much lift do we expect if we email **this** customer with **this** subject/personalization?”*

- **Why it matters for an airline:**
  - ✉️ Some flyers open *any* LATAM email (subject doesn’t matter).  
  - 🧭 Others only click when **personalized routes** (e.g., SCL→LIM) appear.  
  - 💳 High-value/loyalty segments may **convert** with milder nudges; casual browsers may need **stronger offers**.  
  ⇒ **Different people, different effects** → smarter targeting.

---

## ✈️ LATAM-flavored practical examples

1. **Subject lines (Open Rate as outcome):**  
   - Segment by **recency of engagement**, **device**, **country**.  
   - Causal forest finds higher lift for “✅ Limited-time fares SCL→LIM” among **recent searchers of LIM** on **mobile** in **Chile**.

2. **Content personalization (CTOR as outcome):**  
   - Tiles with **Top-3 destinations** vs **generic grid**.  
   - Lift is larger for **users with high search intensity** in the last 7 days and **Medallion-like loyalty tiers**.

3. **Conversion / Revenue per recipient:**  
   - Personalized fare blocks vs generic offers.  
   - Lift concentrated among **price-sensitive** flyers (historically buy with low fares), **short-haul** routes, and **weekend searchers**.

> 🎯 **Policy:** target only users with **predicted \(\hat{\tau}(x)\)** above a **break-even threshold** (see below).

---

## 🧩 What a Causal Forest does (high-level)

- Learns splits on features \(X\) (recency, route interest, loyalty, device, country, etc.) that best reveal **differences in treatment effect**.
- Produces a **local estimate** \(\hat{\tau}(x)\) for each user.
- Often implemented as part of **generalized random forests** (GRF) or via **uplift trees/forests**.
- Related approaches: **T-/S-/X-learners**, **DR/orthogonal learners**, **meta-learners**.

---

## 🧪 Data you need

- **Treatment** \(T \in \{0,1\}\): e.g., *Personalized content = 1*, *Generic = 0*.
- **Outcome** \(Y\): depends on test goal  
  - subject lines → `open_rate` (binary at user-level: opened or not),
  - personalization → `CTOR` (binary click given open) **or** click (binary) with opens as a covariate,
  - sales → `conversion` (binary purchase) and/or **RPR** (continuous revenue per recipient).
- **Covariates** \(X\):  
  - demographics/geo (country/market), device, language, loyalty tier, prior engagement (opens/clicks), search history (routes/dates), price sensitivity proxies, recency/frequency/monetary (RFM).

> 🔒 **No leakage:** only include covariates known **before** sending the email.

---

## 🧮 Identification checklist (observational or randomized)

- **Randomized email A/B:** great—positivity is likely OK.  
- **Observational:** you must model **propensity** \(e(x)=\Pr(T=1\mid X)\) and **outcomes** \(m_t(x)=\mathbb{E}[Y\mid X,T=t]\). Use **orthogonal/doubly robust** estimators + **cross-fitting** to reduce bias.

> ✅ **Overlap/positivity:** ensure \(0.05 \le e(x) \le 0.95\) across key segments; trim if needed.

---

## 🛠️ Training recipe (conceptual)

1. **Split data:** train/validation/test (or cross-fitting folds).  
2. **Nuisance models:** estimate \( \hat{e}(x) \) and \( \hat{m}_0(x), \hat{m}_1(x) \) (GBM/trees/logit).  
3. **Orthogonal signal (DR score):** form a pseudo-outcome that isolates treatment effect.  
4. **Causal forest / GRF:** fit on the pseudo-outcome to learn \(\hat{\tau}(x)\).  
5. **Calibrate:** check that average \(\hat{\tau}(x)\) ≈ ATE on a holdout.  
6. **Policy rule:** score users; target top deciles where \(\hat{\tau}(x)\) clears **break-even**.

> 🧪 Alternatives: **Uplift forests/trees** (split criteria maximize outcome difference), **X-learner** (good with treatment imbalance), **T-learner** (two models), **S-learner** (single model with T as a feature).

---

## 💸 Turning \(\hat{\tau}(x)\) into a business policy

Let:
- \(v\) = net margin per conversion (or expected value per purchase),
- \(c\) = incremental **per-recipient** cost of the treatment,
- \(\hat{\tau}(x)\) = predicted **absolute lift** in conversion for user \(x\).

**Target if:**  
\[
\hat{\tau}(x)\cdot v \ \ge\ c \quad\Rightarrow\quad \hat{\tau}(x) \ge \frac{c}{v}
\]

**Email example:** \(c= \$0.002\), \(v=\$25\) ⇒ threshold \(=0.008\%\) (0.008 pp).  
Score all recipients; **email only** those above the threshold (and respect caps/guardrails).

> For **open rate** / **CTOR** policies, convert lift to **expected conversion/revenue** via a simple funnel model (OR → click → conversion → revenue) to compare against cost.

---

## 📊 How to evaluate HTE targeting

- **Uplift/Qini curves & AUUC:** rank users by \(\hat{\tau}(x)\); plot incremental outcomes vs random.  
- **Deciles:** report incremental conversions/revenue for top 10%, 20%, …  
- **Policy risk:** simulate “treat top-k%” on a **held-out** dataset.  
- **Calibration:** average predicted uplift vs realized uplift by bins.  
- **Guardrails:** unsubscribes/complaints/latency by decile—don’t concentrate harm.

> 📈 **Success:** top deciles show **steeper uplift** and **positive net value** after costs.

---

## 🔍 Interpretability & debugging

- **Global view:** which features drive high \(\hat{\tau}(x)\)? (e.g., recent route search for LIM, weekend searchers, mobile users in CL).  
- **Profiles:** partial dependence or ICE on key features.  
- **Sanity checks:**  
  - \(\text{mean}(\hat{\tau}(X)) \approx \widehat{\text{ATE}}\) on holdout.  
  - Overlap: are extreme segments only in one arm?  
  - Stability: similar patterns across weeks/markets?

---

## ⚠️ Pitfalls (and fixes)

- **Leakage:** future info or post-send signals in \(X\) → restrict to **pre-send** features.  
- **Sparse segments:** tiny leaves → high-variance effects → set **min leaf size**, enforce **regularization**.  
- **Imbalance:** if treatment≪control, prefer **X-learner** or weight samples.  
- **Outcome rarity:** conversion is low → consider longer windows, composite targets, or model **revenue** with robust stats.  
- **Non-stationarity:** effects drift by season/holiday → retrain, include time features, monitor.

---

## 🧰 Worked LATAM examples (from HTE to action)

### 1) Subject line uplift (Open Rate)
- **Setup:** `T=1` if subject includes **route + time-bound** (e.g., “SCL→LIM fares this week”).  
- **HTE finding:** largest \(\hat{\tau}(x)\) among **recent searchers of LIM**, **mobile**, **Spanish CL locale**.  
- **Policy:** ship that subject only to the **top 30%** by \(\hat{\tau}(x)\); control overall volume via caps.  
- **Check:** top 30% shows uplift in OR **and** no drop in **CTOR** or **conversion**.

### 2) Content personalization uplift (CTOR)
- **Setup:** `T=1` personalized tiles (Top-3 likely destinations) vs generic grid.  
- **HTE finding:** lift concentrated in **high recency searches (≤7 days)** + **loyalty tier L1+** + **short-haul** interest.  
- **Policy:** send personalized block to **top deciles by \(\hat{\tau}\)**; generic to the rest.  
- **Outcome:** higher **CTOR**, and in holdout, **conversion** lifts for top deciles.

### 3) Purchase/revenue uplift (Conversion or RPR)
- **Setup:** `T=1` includes **fare callouts** and **date suggestions**.  
- **HTE finding:** largest revenue uplift for **price-sensitive** histories + **weekend searchers**.  
- **Policy:** treat top deciles where \(\hat{\tau}(x)\cdot v \ge c\); skip others.  
- **Result:** positive **net value** after cost, guardrails steady.

---

## 🧪 Non-inferiority within HTE (when simplicity helps)
If a **simpler** template has slightly lower predicted uplift, adopt it for users where \(\hat{\tau}(x)\) is **below** a small **NI margin** \(\delta_{NI}\), but keep the complex/personalized variant for **high-\(\hat{\tau}\)** users. Mixed policy = **cost-effective**.

---

## 🧾 Deployment checklist

- [ ] Clear **objective & outcome** (OR, CTOR, conversion, RPR).  
- [ ] Clean **pre-send** features \(X\); no leakage.  
- [ ] Overlap check \(0.05 \le \hat{e}(x) \le 0.95\).  
- [ ] Proper **cross-fitting/DR** (if observational).  
- [ ] Holdout for **calibration** and **policy evaluation**.  
- [ ] Decision rule: **\(\hat{\tau}(x)\) vs break-even**.  
- [ ] Frequency caps, eligibility, **guardrails**.  
- [ ] Monitoring: uplift drift, seasonality, market shifts.  

---

## 🧰 Tooling (pick your stack)
- **Python:** `econml` (DRLearner, CausalForestDML), `causalml` (uplift trees/forests, metrics), `sklift`.  
- **R:** `grf` (Causal Forest), `uplift`.  
- **Metrics:** Qini/AUUC, decile tables, policy simulation.

---

## 📚 Glossary
- **CATE / HTE:** Individualized effect \(\tau(x)\).  
- **Propensity \(e(x)\):** \(\Pr(T=1\mid X)\).  
- **DR / Orthogonal:** Estimators robust to misspecification of nuisance models.  
- **Qini / AUUC:** Measures of uplift ranking quality.  
- **Policy rule:** Treat when \(\hat{\tau}(x)\) exceeds value-based threshold.

