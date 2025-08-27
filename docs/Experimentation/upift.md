---
title: Uplift Models
layout: page
---

# 📈 Uplift Models (Incrementality-First Targeting)

> **What:** Predict the **causal lift** of treating a user (email variant, personalization) instead of just the probability of an outcome.  
> **Why:** Spend contact budget on people whose behavior **changes** because of the email — not on “sure things” or “lost causes”.

---

## 🧠 Uplift vs Propensity (quick recap)

- **Propensity model:** estimates \(p(Y=1\mid X)\) → “Who is likely to buy?”  
  ⚠️ Sends to **sure things** (buy anyway) and **lost causes** (never buy).

- **Uplift model:** estimates **incremental effect**  
  \[
  u(x) \equiv \Pr(Y=1\mid T=1,X=x) - \Pr(Y=1\mid T=0,X=x)
  \]
  → “**Who changes** because of the email?”

> ✈️ **LATAM examples:**  
> - Subject line variants → uplift on **open_rate** (with caution: should drive downstream metrics).  
> - Content/personalization → uplift on **CTOR** or **conversion**.  
> - Commercial push → uplift on **conversion** and **revenue per recipient (RPR)**.

---

## 🗂️ Outcomes we commonly model at LATAM Email

- **Open Rate (OR)** — for **subject lines**.  
- **Click-to-Open Rate (CTOR)** — for **content/personalization**.  
- **Conversion** (ticket purchase) — for **sales** impact.  
- **RPR (Revenue per Recipient)** — continuous & skewed; summarize with robust stats.

> 🔗 For OR/CTOR uplift, check that gains propagate to **conversion/RPR**; otherwise it’s vanity.

---

## 🧩 Core modeling approaches

### 1) Two-Model (T-learner)
Train two models, one per arm:
- \(\hat p_1(x) = \Pr(Y=1\mid T=1, X=x)\)  
- \(\hat p_0(x) = \Pr(Y=1\mid T=0, X=x)\)  
Then **uplift**: \(\hat u(x) = \hat p_1(x) - \hat p_0(x)\)

**Pros:** simple, flexible. **Cons:** can be data-hungry, unstable with imbalance.

---

### 2) Class Transformation (single classifier)
Transform labels to encode “incrementality signal” and fit one model.  
A common transformation (for randomized data) marks examples as **1** when the observed outcome is **consistent with a positive treatment effect** (e.g., treated-success or control-failure) and **0** otherwise.  
**Pros:** easy with standard classifiers. **Cons:** choice of transform matters; may need weighting for arm sizes.

---

### 3) Direct Uplift Models (trees/forests)
**Uplift trees/forests** choose splits that **maximize treatment–control outcome differences** in leaves.  
**Pros:** directly optimizes uplift; interpretable leaves (“who gains”). **Cons:** careful regularization & min leaf size to avoid noise.

---

### 4) Meta-learners & DR/Orthogonal learners
- **S-, T-, X-learners** (good defaults; **X** helps with arm imbalance).  
- **Doubly-Robust / Orthogonal learners** (e.g., CausalForestDML/DRLearner): model **propensity** \(e(x)\) and **outcomes** \(m_t(x)\) with cross-fitting to reduce bias.
  
**Pros:** strong statistical properties. **Cons:** slightly more complex pipeline.

---

## 🧱 Data & features (email context)

- **Treatment \(T\):** variant indicator (e.g., subject B vs A; personalized tiles vs generic).  
- **Outcome \(Y\):** OR / CTOR / conversion / RPR.  
- **Features \(X\) (pre-send only):** country/market, device, language, loyalty tier, last open/click, search history (routes/dates), price sensitivity proxies, RFM (recency/frequency/monetary), seasonality (dow/holiday).

> 🔒 **No leakage:** include only signals known **before** sending the email.

---

## 🧪 Training & validation recipe

1. **Start from randomized test data** (or robustly adjust if observational).  
2. **Split** data (or use **cross-fitting**): train/validation/test.  
3. **Fit** the uplift approach (T/S/X-learner, uplift trees, DR-learner).  
4. **Calibrate**: \(\text{mean}(\hat u(X))\) on holdout ≈ **ATE** from A/B.  
5. **Rank** users by \(\hat u(x)\); build **decile** tables.  
6. **Policy simulation** on holdout: treat top-k% and estimate **incremental conversions/RPR** (see below).  
7. **Ship** with a **value threshold** and guardrails.

---

## 💸 From scores to policy (value threshold)

Let
- \(v\) = net margin per conversion (expected, after costs)  
- \(c\) = incremental cost per treated recipient  
- **Treat if** \(\hat u(x)\cdot v \ge c\)  ⇒  \(\hat u(x) \ge c/v\)

**Email example:** \(c = \$0.002\), \(v = \$25\) → threshold = **0.008% (0.008 pp)**.  
Apply frequency caps and eligibility rules.

> For **OR/CTOR uplift**, convert expected lift to **conversion/RPR** via a simple funnel (OR→click→conversion) to compare against cost.

---

## 📊 Offline evaluation (Qini, AUUC, deciles)

With constant treatment rate \(e\) in randomized data, the **incremental outcome** for a scored set \(S\) is
\[
\widehat{U}(S) \;=\; \sum_{i\in S}\Big[\frac{T_i Y_i}{e} \;-\; \frac{(1-T_i) Y_i}{1-e}\Big]
\]

- **Uplift curve / Qini curve:** sort by \(\hat u(x)\), plot \(\widehat{U}\) vs share of targeted users.  
- **AUUC / Qini coefficient:** area under uplift curve (higher is better).  
- **Deciles:** table of incremental conversions/RPR by top-10%, 20%, …  
- **Calibration:** bin by predicted \(\hat u\), compare **predicted vs realized** uplift.

> ✅ **Success:** top deciles show steep incremental gains and positive **net value** after cost.

---

## ⚠️ Pitfalls (and fixes)

- **Leakage:** future info in features → restrict to **pre-send** signals.  
- **Treatment imbalance:** use **X-learner** / weighting.  
- **Sparse outcomes (conversion):** extend window, aggregate, or model **RPR** with robust stats.  
- **Tiny leaves / overfit:** uplift trees need **min leaf size** and regularization.  
- **Non-stationarity:** seasonality/holidays; retrain and monitor drift.  
- **“OR-only” wins:** ensure OR/CTOR uplift **propagates** to conversion/RPR.

---

## ✈️ Worked LATAM examples

### A) Subject line — Uplift on **Open Rate**
- **T:** subject **with route + urgency** vs baseline.  
- **X:** device, country, last-7d opens, route search recency, loyalty tier.  
- **Finding:** top 20% by \(\hat u\) (mobile + recent LIM search) → **+2.1 pp OR** incremental vs random target.  
- **Action:** send the urgent route subject to **top-20% uplift decile** only; keep baseline for others.  
- **Check:** CTOR and conversion stable (no dilution).

### B) Personalization — Uplift on **CTOR**
- **T:** **Top-3 personalized tiles** vs **generic grid**.  
- **X:** recent search intensity, short-haul vs long-haul interest, loyalty, language.  
- **Finding:** top 30% uplift users show **+1.6 pp CTOR** incremental; bottom deciles ≈ 0.  
- **Action:** serve personalized tiles only to top uplift deciles; generic to the rest.  
- **Result:** downstream **conversion** lift in treated deciles.

### C) Commercial push — Uplift on **Conversion / RPR**
- **T:** fare callouts + date suggestions vs generic promo.  
- **X:** price sensitivity proxy, weekend searcher flag, recency, route family.  
- **Finding:** top 10% uplift users deliver **65 extra purchases / 100k recipients** and **positive RPR** after cost.  
- **Action:** target top-10% uplift; skip low-uplift segments to save budget & fatigue.

---

## 🔁 Online validation (must-do)

- **Policy A/B:** Compare **“Treat-Top-k% by uplift”** vs **Random** (or vs Propensity targeting).  
- **KPIs:** incremental **conversion** and **RPR**, plus guardrails (unsubs/complaints).  
- **Stability:** check markets/devices; confirm patterns hold.

---

## 🧾 Deployment checklist

- [ ] Objective & **primary outcome** (OR/CTOR/Conversion/RPR) fixed  
- [ ] Clean **pre-send** feature set; no leakage  
- [ ] Chosen learner (T/S/X, DR, uplift forest) & hyperparameters  
- [ ] **Calibration**: mean \(\hat u\) ≈ ATE on holdout  
- [ ] **Uplift/Qini** and **deciles** pass value threshold \(c/v\)  
- [ ] Policy A/B designed; frequency caps & eligibility set  
- [ ] Monitoring: drift, seasonality, guardrails

---

## 🛠️ Tooling quick-start

- **Python:** `causalml` (uplift trees/forests, Qini/AUUC), `econml` (DRLearner, CausalForestDML), `scikit-uplift` / `sklift`.  
- **R:** `uplift`, `grf` (Causal Forest).  
- **Key plots:** uplift/Qini curve, decile bars, calibration by bins.

---

## 📚 Glossary
- **Uplift \(u(x)\):** \( \Pr(Y=1\mid T=1,X) - \Pr(Y=1\mid T=0,X) \).  
- **ATE / CATE:** average / conditional average treatment effect.  
- **AUUC / Qini:** area/measure of uplift ranking quality.  
- **Policy:** treat users with \(\hat u(x)\) above a **value-based** threshold \(c/v\).  
- **Guardrails:** unsubscribes, complaints, latency, frequency caps.

