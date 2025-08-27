---
title: 2 - A/B Testing
layout: page
---

# 🧪 A/B Testing

## 🔎 Definition
A randomized experiment comparing two versions:

- **A (Control):** current baseline.
- **B (Treatment):** new variant.

> 🎨 *Optional hero image (place in `/assets/ab-testing/`):*
>
> <figure>
>   <img src="/assets/ab-testing/ab-hero.png" alt="Illustration of traffic split into A (control) and B (treatment) with metric comparison panels" />
>   <figcaption>High-level view of a randomized split and metric comparison.</figcaption>
> </figure>
>
> <details>
> <summary>🧑‍💻 Suggested AI prompt</summary>
> Minimal flat illustration of an A/B test: traffic enters a split node and flows into two panels labeled **A (Control)** and **B (Treatment)**, each showing a conversion gauge. Clean, modern, vector style, white background, subtle shadows, brand-neutral colors.
> </details>

---

## 🧠 Statistical framework
- **Null hypothesis (H₀):** no difference between A and B.
- **Alternative (H₁):** a difference exists (two-sided by default; use one-sided only with strong prior and pre-registration) ⚠️.
- **Why A/B tests?**  
  - ✅ Simple, robust design  
  - 🎲 Randomization minimizes selection bias  
  - 📏 Quantifies uncertainty for clear decisions

---

## 🧭 Minimal workflow
1. 🎯 Define the **primary metric** (e.g., conversion rate, revenue per user).
2. 👤 Choose **unit of randomization** (user, session, PNR, household).
3. 🚦 Set **exposure rules** (who can/cannot be re-exposed).
4. 📝 Pre-register **α, power, MDE, duration, guardrails**.
5. 📊 Run, monitor for **sample ratio mismatch (SRM)**.
6. 🧾 Analyze and **decide** (see decision rules below).

---

## 🧩 Design choices that matter

### 🆔 Unit of randomization
- Prefer **stable identifiers** (user ID, PNR) over sessions/cookies when possible.
- If cross-device is common, consider **household** or **account** level to reduce spillovers.

### ⚖️ Split & allocation
- **50/50** maximizes power; skewed splits (e.g., 90/10) need **larger total N** for the same MDE.
- For risky changes, start with a **10–20% ramp**, then escalate if guardrails are healthy.

### 🧱 Stratification (blocking)
- Randomize **within strata** (e.g., country, device, recency tiers) to keep groups balanced on key covariates.
- Report results overall and by strata; pre-specify subgroup analyses.

### 🔒 Exposure policy
- One user → one arm for the full test window.
- Avoid cross-arm exposure (e.g., receiving both Email and WhatsApp).
- Define **frequency caps** and **eligibility** rules up front.

---

## 📐 Metrics & statistical tests

### ✅ Binary metrics (conversion rate, click-through rate)
Let \( p_A \), \( p_B \) be conversion rates; \( n_A, n_B \) sample sizes.

- **Difference:** \( \Delta = p_B - p_A \)
- **Pooled SE (for H₀: \( p_A = p_B \)):**
  \[
  \hat{p} = \frac{x_A + x_B}{n_A + n_B}, \quad
  SE_{\text{pooled}} = \sqrt{\hat{p}(1-\hat{p})\left(\frac{1}{n_A}+\frac{1}{n_B}\right)}
  \]
- **z-statistic:** \( z = \frac{\Delta}{SE_{\text{pooled}}} \)
- **Two-sided p-value:** compare |z| to standard normal.

> **95% CI (unpooled)**  
> \[
> CI = \Delta \pm 1.96 \cdot \sqrt{\frac{p_A(1-p_A)}{n_A} + \frac{p_B(1-p_B)}{n_B}}
> \]

### 💵 Continuous metrics (Revenue per User, Order Value)
- Use **Welch’s t-test** (unequal variances).
- For skewed revenue: **trimmed means**, **winsorization**, or **bootstrap CIs**.
- Report both **mean** and **median**; consider **RPU** and **RPT**.

### ⏱️ Ratio & time-based metrics
- If numerator/denominator vary per user, consider **delta method** or **bootstrap**.
- Guardrails (refund rate, complaint rate) are tested but **not optimized**.

---

## 📏 Sample size, Power, and MDE

- **Type I error (α):** false positive rate (commonly 0.05).
- **Type II error (β):** false negative rate; **Power = 1 − β** (commonly 0.8).

**Back-of-the-envelope N for two proportions** *(per arm)*:
\[
n \approx \frac{2\,p(1-p)\,(z_{1-\alpha/2}+z_{1-\beta})^2}{\delta^2}
\]
Where \( p \) is baseline rate; \( \delta \) is **absolute** lift (MDE, in pp).

> 🧮 **Interpretation:** Smaller MDE ⇒ larger sample or longer duration.

---

## 🎯 Variance reduction (optional but powerful)

- **Stratified randomization:** balances covariates ⇒ lower variance.
- **Covariate adjustment (ANCOVA):** regress outcome on treatment + pre-exposure covariates.
- **CUPED (pre-exposure correction):**  
  \( y' = y - \theta (x - \bar{x}) \), with \( \theta = \frac{\text{Cov}(y,x)}{\text{Var}(x)} \).


⚠️ Pre-specify the method; ensure treatment does **not** affect the covariate \( x \).

---

## 🩺 Randomization health & SRM

**Sample Ratio Mismatch (SRM):** observed allocation deviates from expected (e.g., expect 50/50, observe 49/51 out of 200k).

- Diagnose with a **chi-square test** on assignment counts.
- Common causes: bot traffic, bucketing bugs, eligibility filters applied **after** randomization, cross-device leakage, late-day traffic spikes.


 <details>
 <summary>🧑‍💻 Suggested AI prompt</summary>
 Clean bar chart comparing expected 50/50 vs observed 49/51 assignment with a red highlight on the mismatch. Minimalist dashboard style.
 </details>

**Action:** If SRM is significant, **invalidate** the test and fix instrumentation before re-running.

---

## ⏳ Stopping rules & sequential looks

- **Fixed-horizon testing:** set duration and N in advance; **no peeking**.
- **Sequential testing:** allowed with proper **alpha-spending** (e.g., Pocock, O’Brien–Fleming) or **always-valid** methods.
- If monitoring periodically, use **group-sequential** plans and document spending rules.

---

## ➗ Multiple comparisons

If you test many variants/metrics/subgroups, control false discoveries:

- **Bonferroni** (conservative), **Holm–Bonferroni**, or **Benjamini–Hochberg (FDR)**.
- Prefer a **single primary metric**; treat others as **guardrails** or **exploratory**.

---

## 🛠️ Worked examples

### 📈 Example 1 — Conversion rate lift
- \( n_A = n_B = 50{,}000 \)
- \( p_A = 3.0\% = 0.030 \)
- \( p_B = 3.6\% = 0.036 \)
- \( \Delta = 0.006 \) (**+0.6 pp**)

Pooled \( \hat{p} = 0.03299 \)  
\( SE_{\text{pooled}} \approx 0.00113 \)  
\( z \approx 5.31 \) → **p-value ≈ 1.1e−7** ✅  
95% CI (unpooled) for \( \Delta \): **[+0.38 pp, +0.82 pp]**  
**Decision:** reject H₀. Also check **business impact** (incremental revenue, margin).

### 💰 Example 2 — Revenue per user (skewed)
- Use **Welch’s t-test** or **bootstrap CI** on mean RPU.  
- If heavy tails: **winsorize** top 0.1–1% or report **trimmed mean (e.g., 5%)**.  
- Complement with **conversion rate** and **AOV** to explain drivers.

---

## 🧯 Common pitfalls & remedies

- 👀 **Peeking without correction:** inflates Type I error → use fixed horizon or sequential designs.  
- 🔁 **Inconsistent exposure:** users switch arms → lock assignment, deduplicate IDs.  
- 🌐 **Interference/spillovers:** cross-channel effects → consider **geo experiments** or **cluster randomization**.  
- 📅 **Seasonality & novelty:** ensure runtime covers weekly cycles; consider ramps and post-holdouts.  
- 🧮 **Metric drift:** freeze definitions during the test.  
- 🧪 **Underpowered tests:** align MDE with business value; extend duration or traffic.  

---

## ✅ Decision checklist
- [ ] Primary metric defined; guardrails listed.  
- [ ] α, power, MDE pre-registered; sample size computed.  
- [ ] Unit of randomization & exposure rules documented.  
- [ ] Stratification keys (if any) fixed pre-launch.  
- [ ] SRM monitored; assignment counts logged.  
- [ ] Fixed horizon **or** sequential plan documented.  
- [ ] Analysis plan: test type, CI, variance reduction (if any).  
- [ ] Result meets **statistical** and **business** significance.  
- [ ] Rollout plan & post-launch monitoring defined.  

---

