---
title: Deciding Whether to Reject H₀
layout: page
---

# ✅ Deciding Whether to Reject H₀

> This page focuses on **email experiments at LATAM** (subject lines, personalized content, templates).  
> Primary metrics you may use per test type:
> - ✉️ **Subject line tests:** `open_rate`
> - 🧩 **Content/personalization tests:** `click_to_open_rate` (CTOR), `conversion` (ticket purchase), `revenue` (ticket value / recipient)

---

## 🔑 Key quantities (recap & deep dive)
- **Test statistic:** compares the primary metric across groups.
  - For a conversion or rate metric, compare proportions \(p_A\) vs \(p_B\).
- **p-value:** probability of observing an effect as extreme as measured **if H₀ were true**.
- **Significance (α):** commonly 0.05 (5% Type I error).
- **Confidence interval (CI):** if the CI for \(p_B - p_A\) **excludes 0**, the effect is statistically significant.
- **Effect size (practical):** absolute lift \(\Delta = p_B - p_A\) and **business lift** (e.g., Δrevenue per recipient).

> 🧠 We usually don’t “accept H₀.” We **reject H₀** or **fail to reject H₀**.  
> To *claim no meaningful difference*, use **non-inferiority** or **equivalence** tests.

---

## 🧪 Metrics used in LATAM email tests

- **Open rate (OR):**  
  \[
  \text{open\_rate} = \frac{\text{unique opens}}{\text{delivered}}
  \]
  *Primary* for **subject line** tests.

- **Click-to-open rate (CTOR):**  
  \[
  \text{CTOR} = \frac{\text{unique clicks}}{\text{unique opens}}
  \]
  *Primary* for **content/personalization** tests.  
  👉 Test CTOR **conditional on opens** (denominator = opens per arm), not on recipients.

- **Conversion (purchase):**  
  \[
  \text{conversion} = \frac{\text{ticket purchases}}{\text{delivered}}
  \]
  *Primary* for tests whose goal is **sales**.

- **Revenue (ticket value):**  
  - **Revenue per recipient (RPR):** total revenue / delivered.  
  - Continuous & skewed → use **Welch’s t-test**, **trimmed mean**, or **bootstrap CI**.

---

## 🧭 Practical decision rule
1. 🎯 **Pre-register** α, power, MDE, duration, primary metric.  
2. 🧮 Compute \(\Delta\) and its **CI** on the **primary** metric.  
3. 🔍 Check **p-value** *or* CI.  
4. 💼 Decide only with **statistical** evidence **and** **business** relevance (break-even).

---

## ⚖️ Business significance (break-even thinking)

Let:
- \(v\) = expected **net margin per purchase** (after costs),
- \(c_T - c_C\) = **incremental cost per email** (e.g., template B with extra enrichments),
- \(\Delta p\) = absolute lift in **purchase conversion**.

**Break-even:**  
\[
\Delta p \cdot v \ \ge\ (c_T - c_C)
\]

If \(c_T - c_C = \$0.002\) (0.2¢) and \(v = \$25\), then:
\[
\Delta p \ge \frac{0.002}{25} = 0.00008 = 0.008\% \text{ (0.008 pp)}
\]
➡️ Even tiny **conversion** lifts can be profitable; ensure **guardrails** (complaints, unsubscribes) are steady.

> 🧩 For **subject lines** (primary: open rate), require a **minimum downstream** effect path:  
> OR ↑ should translate to **clicks** and ideally **conversion** ≈ otherwise it’s vanity.

---

## 🧷 Type I & II errors, Power, and MDE

- **Type I (α):** false positive—declaring a win when there is none.  
- **Type II (β):** false negative—missing a real win.  
- **Power = 1 − β:** probability to detect the MDE if it is real.  
- **MDE:** smallest effect you commit to detect (e.g., +1.2 pp OR; +0.10 pp conversion).

**Rule of thumb for two proportions (per arm):**
\[
n \approx \frac{2\,p(1-p)\,(z_{1-\alpha/2}+z_{1-\beta})^2}{\delta^2}
\]
Where:
- \(p\) = baseline rate (OR, CTOR, conversion),  
- \(\delta\) = **absolute** lift (MDE, in proportion points).

**Planning examples (α=0.05, power=0.80):**
- 📬 **Open rate:** baseline 22%, MDE = **+1.2 pp** → **~18.7k recipients/arm**  
- 🖱️ **CTOR:** baseline 12%, MDE = **+1.0 pp** → **~16.6k opens/arm** → with 22% OR, **~75k recipients/arm**  
- 🛒 **Conversion:** baseline 0.8%, MDE = **+0.10 pp** → **~124k recipients/arm**

> 🔁 Sequential peeks **change** α/β. If you monitor mid-test, use **alpha-spending** or **always-valid** methods and expect **more** total N.

---

## 🧯 Guardrails to always check
- 📉 **Unsubscribes / spam complaints**  
- 🐢 **Latency / deliverability**  
- 🔁 **SRM** (sample ratio mismatch) before outcomes  
- 🌍 **Segment balance** if stratified (country, device, recency)

---

## 🧰 Worked Examples (LATAM Email)

### ✉️ Example A — Subject line test (primary: open_rate)
- **Design:** Subject A vs Subject B  
- **n:** \(n_A = n_B = 100{,}000\) delivered  
- **Results:** \( \text{OR}_A=22.0\% \), \( \text{OR}_B=23.5\% \) ⇒ \(\Delta = +1.5\) pp  

**Inference (two-proportion z):**  
- Two-sided **p-value ≪ 0.001** (significant)  
- **95% CI** for \(\Delta\) ≈ **[+1.13 pp, +1.87 pp]** (excludes 0)

**Decision:** **Reject H₀** (B improves OR).  
**Business check:** Ensure **CTOR** and **conversion** don’t fall—otherwise OR gains may be superficial.

---

### 🧩 Example B — Personalized content (primary: CTOR, conditional on opens)
- **Design:** Template A (generic) vs Template B (personalized destination tiles)  
- **Opens:** \(n_A^{open}=22{,}000\), \(n_B^{open}=23{,}500\)  
- **Results:** \( \text{CTOR}_A=12.0\% \), \( \text{CTOR}_B=13.3\% \) ⇒ \(\Delta = +1.3\) pp

**Inference (two-proportion z on opens):**  
- Two-sided **p-value ≈ 0.00003** (significant)  
- **95% CI** ≈ **[+0.69 pp, +1.91 pp]** (excludes 0)

**Decision:** **Reject H₀** (B increases CTOR).  
**Business path:** Higher CTOR should propagate to **conversion**. Verify **conversion** and **RPR** uplifts.

---

### 🛒 Example C — Personalization impact on **conversion**
- **Design:** A (generic grid) vs B (personalized top-3)  
- **n:** \(n_A = n_B = 150{,}000\) delivered  
- **Results:** \( p_A=0.80\% \), \( p_B=0.95\% \) ⇒ \(\Delta = +0.15\) pp

**Inference:**  
- Two-sided **p-value ≈ 1.0e−5** (significant)  
- **95% CI** ≈ **[+0.083 pp, +0.217 pp]** (excludes 0)

**Decision:** **Reject H₀**.  
**Business check:** Compare CI to **break-even** (often tiny for email). Consider **RPR** to include ticket value effects.

---

## 🧮 Revenue analysis (continuous, skewed)

- Use **Welch’s t-test** (unequal variances), or **bootstrap CI** for mean **RPR** (revenue per recipient).  
- Heavy zeros & right tail → consider **trimmed mean (e.g., 5%)** or **winsorization**.  
- Report both **mean RPR** and **conversion** to separate “more buyers” vs “higher ticket value”.

---

## ⏳ One-sided, Non-inferiority, Equivalence

- **Two-sided** is default.  
- **One-sided** only if the *only* acceptable outcome is \(B \ge A\) (pre-register).  
- **Non-inferiority (NI):** accept B if \( \Delta \ge -\delta_{NI} \) (B not worse by more than margin).  
- **Equivalence (TOST):** show \( -\delta \le \Delta \le \delta \).

> 🧪 Use NI when B is **cheaper/simpler** (e.g., removing dynamic blocks). Pick \(\delta_{NI}\) in **pp** on the **primary** metric (e.g., allow up to −0.20 pp OR if ops cost drops meaningfully).

---

## ➗ Multiple metrics & segments (avoid p-hacking)

- Choose **one primary** metric per test (e.g., OR for subject lines; CTOR or conversion for content).  
- Treat the rest as **guardrails** or **supporting** metrics.  
- If many variants/subgroups are reviewed, control errors (e.g., **Benjamini–Hochberg FDR**).  
- **Pre-specify** segments (country, device, recency). Exploratory wins ⇒ confirm with a **follow-up** test.

---

## 🧪 Decision cheat sheet (copy-paste)
- [ ] α, power, MDE **pre-registered**  
- [ ] Primary metric CI **excludes 0** (or meets NI/TOST)  
- [ ] CI **clears break-even** (practical significance)  
- [ ] **SRM** passed; exposure rules respected  
- [ ] Guardrails (unsubs/complaints/latency) steady  
- [ ] Subgroup reads per **pre-spec** / FDR control  
- [ ] Rollout & post-launch monitoring defined

---

## 📚 Glossary
- **Reject H₀ / Fail to reject H₀:** Evidence against/no evidence against the null.  
- **Power (1−β):** Probability to detect the MDE if real.  
- **MDE:** Smallest effect you plan to detect (absolute pp).  
- **CTOR:** Clicks divided by opens (conditional rate).  
- **RPR:** Revenue per recipient.  
- **Non-inferiority margin (\(\delta_{NI}\))**: Tolerated shortfall when B brings offsetting benefits.  
- **FDR:** False discovery rate control across multiple reads.
