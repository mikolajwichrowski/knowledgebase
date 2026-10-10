# UX Metrics

> **TL;DR** — Measure **behaviour** (task success, time on task, errors, adoption, retention) and **attitude** (SUS, UMUX-Lite, SEQ, CSAT, CES, NPS). SUS (John Brooke, 1986) is a 10-item, 0–100 score with a global average of **68**; UMUX-Lite is a 2-item proxy; SEQ is a single 7-point post-task question (average ≈ 5.5). NPS is popular but a weak diagnostic. Use Google's **HEART** framework with **Goals → Signals → Metrics** to pick metrics that matter. Always report confidence intervals for small samples.

Related: [Research methods](research-methods.md) · [Product management](../02-product-management/README.md) · [Data & AI architecture](../07-data-ai-architecture/README.md)

---

## 1. Types of UX metrics

| Category | Examples | Source |
|----------|---------|--------|
| **Performance (behavioural)** | Task success, time on task, errors, efficiency (clicks/steps), learnability (time to proficiency) | Usability tests, analytics |
| **Self-reported (attitudinal)** | SUS, UMUX-Lite, SEQ, CSAT, CES, NPS, SUPR-Q, confidence ratings | Questionnaires |
| **Issue-based** | # of usability problems, severity, frequency per participant | Usability tests, heuristic evaluation |
| **Behavioural/physiological** | Eye-tracking fixations, heart rate, facial expression | Labs |
| **Product analytics** | Adoption, activation, DAU/MAU, retention, feature usage, funnel conversion, drop-off | Telemetry |
| **Business** | Revenue, cost-to-serve, support tickets, training time, process cycle time | Business systems |
| **Technical UX** | Core Web Vitals (LCP ≤ 2.5 s, INP ≤ 200 ms, CLS ≤ 0.1), error rate, uptime | RUM |

Reference: Tullis & Albert, *Measuring the User Experience* (3rd ed. 2022, Albert & Tullis).

---

## 2. Task-level performance metrics

### 2.1 Task success (completion rate)

- **Binary:** success = 1, failure = 0; completion rate = successes / attempts.
- **Levels:** complete success / success with minor problems / major problems / failure (gave up or wrong answer).
- Average task completion rate across studies ≈ **78%** (Sauro, MeasuringU meta-analysis of 1,100+ tasks).
- For small samples, report the **Adjusted Wald (Agresti–Coull) confidence interval**: add z²/2 successes and z² trials (≈ +2 / +4 at 95%):
  `p_adj = (x + 1.92) / (n + 3.84)`; CI = `p_adj ± 1.96 · sqrt(p_adj(1 − p_adj) / (n + 3.84))`.
  Example: 4/5 succeed → p_adj ≈ 0.67, 95% CI ≈ 0.31–1.00. *Tell stakeholders this before they quote "80%".*

### 2.2 Time on task

- Measure only successful attempts (or report both).
- Task time data is positively skewed → report the **geometric mean** for n < 25, median for larger samples (Sauro & Lewis).
- Compare against an expert baseline (KLM/GOMS predictions or expert user time).
- **Keystroke-Level Model (Card, Moran & Newell, 1980):** K (keystroke) ≈ 0.2 s (average typist; 0.08–1.2), P (point with mouse) ≈ 1.1 s, B (button press) 0.1 s, H (home hands between keyboard & mouse) ≈ 0.4 s, M (mental preparation) ≈ 1.35 s. Useful to compare designs for high-frequency enterprise tasks without users.

### 2.3 Error rate

- Errors = unintended actions, slips, mistakes, wrong data. Error rate = errors / opportunities for error (per task).
- Average ~0.7 errors per task (Sauro); ~2/3 of tasks have at least one error.
- In data-entry-heavy ERP/HR systems, track **data quality errors post-hoc**: corrections, reversals, cancelled transactions, support tickets per 1,000 transactions.

### 2.4 Efficiency & learnability

- **Efficiency** = task success / time (or "completion rate per minute"); number of steps/clicks vs optimal path ("lostness" — Smith 1996: `L = sqrt((N/S − 1)² + (R/N − 1)²)`, N = unique pages visited, S = total pages visited, R = minimum needed; L < 0.4 not lost, > 0.5 lost).
- **Learnability:** measure time/errors across repeated trials; plot learning curve; time-to-proficiency for new employees.

---

## 3. Standardised questionnaires

### 3.1 SUS — System Usability Scale

- **Author:** John Brooke, Digital Equipment Corporation, 1986 (published 1996). "Quick and dirty", free, the most widely used usability questionnaire.
- **10 items**, alternating positive (odd) and negative (even), 5-point agreement scale (1 = strongly disagree … 5 = strongly agree):

| # | Item |
|---|------|
| 1 | I think that I would like to use this system frequently. |
| 2 | I found the system unnecessarily complex. |
| 3 | I thought the system was easy to use. |
| 4 | I think that I would need the support of a technical person to be able to use this system. |
| 5 | I found the various functions in this system were well integrated. |
| 6 | I thought there was too much inconsistency in this system. |
| 7 | I would imagine that most people would learn to use this system very quickly. |
| 8 | I found the system very cumbersome to use. (originally "awkward") |
| 9 | I felt very confident using the system. |
| 10 | I needed to learn a lot of things before I could get going with this system. |

**Scoring formula:**

```
For odd items (1,3,5,7,9):   contribution = response − 1
For even items (2,4,6,8,10): contribution = 5 − response
SUS = (sum of contributions) × 2.5          → range 0–100
```

```js
const sus = r => 2.5 * r.reduce((s, v, i) => s + (i % 2 === 0 ? v - 1 : 5 - v), 0); // r = 10 answers, 1..5
```

**Interpretation:**

| SUS | Percentile (Sauro & Lewis) | Curved grade | Adjective (Bangor, Kortum & Miller 2009) |
|-----|---------------------------|--------------|------------------------------------------|
| ≥ 84.1 | 96–100 | A+ | Best imaginable (≈ 90+) |
| 80.8–84.0 | 90–95 | A / A− | Excellent |
| 78.9–80.7 | 85–89 | A− | |
| 72.6–78.8 | 65–84 | B+ / B | Good (≈ 71–72) |
| **68** | **50** | **C** | Average / OK |
| 62.7–64.9 | 35–40 | C− | |
| 51.7–62.6 | 15–34 | D | OK/Poor (≈ 50) |
| < 51.7 | < 15 | F | Awful (≈ 25–35) |

- SUS is **not a percentage**; 68 = 50th percentile.
- Two factors: usability (8 items) and learnability (items 4 & 10) — Lewis & Sauro 2009.
- Reliable with small samples (~12–14 for stable means); report 95% CI.
- Enterprise software typically scores lower (B2B average ≈ 60s; Sauro reports B2B software ~5–10 points below consumer).
- Translated validated versions exist (incl. Dutch, Polish, German).
- An "all-positive" SUS variant is equally valid and reduces miscoding (Sauro & Lewis 2011).

Sources: <https://en.wikipedia.org/wiki/System_usability_scale> · <https://measuringu.com/sus/> · <https://www.usability.gov/how-to-and-tools/methods/system-usability-scale.html>

### 3.2 UMUX and UMUX-Lite

- **UMUX** (Kraig Finstad, 2010): 4 items aligned with ISO 9241-11 (effectiveness, efficiency, satisfaction).
- **UMUX-Lite** (Lewis, Utesch & Maher, 2013): 2 items, 7-point scale:
  1. "[This system]'s capabilities meet my requirements." (usefulness)
  2. "[This system] is easy to use." (usability)
- **Scoring:** raw = `((i1 − 1) + (i2 − 1)) / 12 × 100`. To map to SUS scale: `SUS ≈ 0.65 × raw + 22.9` (Lewis et al. 2013 regression). Recent work suggests the raw score already tracks SUS closely enough for many uses.
- Great for in-product intercept surveys (short); correlated ~0.8 with SUS. Basis of TAM-like usefulness + ease-of-use thinking.

Source: <https://measuringu.com/umux-lite/>

### 3.3 SEQ — Single Ease Question

- Asked **immediately after each task**: "Overall, how difficult or easy was the task to complete?" 1 = Very difficult … 7 = Very easy.
- Average ≈ **5.5** (Sauro, MeasuringU, across hundreds of tasks); scores < 5 are below average.
- Follow up low scores with "Why?".
- Correlates with completion and time.

Source: <https://measuringu.com/seq10/>

### 3.4 Other questionnaires

| Instrument | Items | Notes |
|-----------|-------|-------|
| **SUPR-Q** (Sauro 2015) | 8 | Websites: usability, trust, loyalty, appearance; percentile normed (licensed) |
| **PSSUQ / CSUQ** (IBM, Lewis 1992) | 16 (v3) | Post-study; system usefulness, information quality, interface quality; lower = better |
| **QUIS** (Chin, Diehl, Norman 1988) | 27 | University of Maryland; licensed |
| **ASQ** (After-Scenario Questionnaire) | 3 | Ease, time, support materials |
| **NASA-TLX** (Hart & Staveland 1988) | 6 | Workload: mental, physical, temporal demand, performance, effort, frustration — great for complex enterprise tasks |
| **UEQ / UEQ-S** (Laugwitz, Held, Schrepp 2008) | 26 / 8 | Attractiveness, perspicuity, efficiency, dependability, stimulation, novelty; free, many languages; benchmark available |
| **AttrakDiff** (Hassenzahl 2003) | 28 | Pragmatic vs hedonic quality |
| **TAM** (Davis 1989) | — | Perceived usefulness & ease of use → intention to use; relevant to enterprise adoption |
| **Microsoft Product Reaction Cards** | 118 words | Desirability |

---

## 4. Customer/business attitudinal metrics

### 4.1 NPS — Net Promoter Score

- **Author:** Fred Reichheld (Bain & Company), HBR "The One Number You Need to Grow", 2003. Trademark of Bain, Satmetrix, Reichheld.
- Question: "How likely are you to recommend [X] to a friend or colleague?" **0–10**.
- **Promoters 9–10, Passives 7–8, Detractors 0–6.**
- `NPS = %Promoters − %Detractors` → range −100 to +100.
- Example: 50% promoters, 30% passives, 20% detractors → NPS = +30.

**Critique:**
- Single item, low diagnostic value — tells you *that*, not *why* (always add an open "why?" question).
- Arbitrary cut-offs; 11-point scale collapsed into 3 loses information; same NPS from very different distributions; high variance → large sample needed (margin of error typically ±10+ points with n ≈ 100).
- Predictive validity for growth disputed (Keiningham et al., 2007, *Journal of Marketing*: no evidence NPS is superior to other measures like satisfaction).
- Cultural response styles (e.g. Dutch, German and Japanese respondents rarely give 9–10) → don't compare across countries.
- Meaningless for **mandatory internal tools** — employees can't choose; "recommend" makes no sense. Use SUS/UMUX-Lite, CES or eNPS-style questions only with care.
- Gaming via survey timing & coaching ("please give us a 10").

Source: <https://en.wikipedia.org/wiki/Net_promoter_score>

### 4.2 CSAT — Customer Satisfaction Score

- "How satisfied were you with [interaction/feature]?" typically 1–5 (or 1–7, emoji scales).
- `CSAT % = (# of 4 and 5 responses) / total responses × 100` (top-2-box).
- Transactional — best right after an interaction (support ticket closed, order delivered).
- Benchmarks vary by industry (ACSI index in the US).

### 4.3 CES — Customer Effort Score

- **Source:** Dixon, Freeman & Toman, CEB, HBR "Stop Trying to Delight Your Customers", 2010. Reducing effort drives loyalty more than delight.
- CES 2.0: "[Company] made it easy for me to handle my issue." 1 = Strongly disagree … 7 = Strongly agree. Report mean or % agree (5–7).
- Good for self-service flows, support, and **internal processes** (expense claim, leave request).

### 4.4 Other

- **eNPS** — employee NPS (same caveats).
- **CLV, churn, retention cohorts** — product/business level.
- **Top-task performance indicator (TPI)** — Gerry McGovern: success & time on top tasks measured repeatedly.

---

## 5. HEART framework (Google)

- **Source:** Kerry Rodden, Hilary Hutchinson & Xin Fu, Google, CHI 2010, "Measuring the User Experience on a Large Scale: User-Centered Metrics for Web Applications".

| Dimension | Definition | Example metrics (HR self-service portal) |
|-----------|-----------|----------------------------------------|
| **H**appiness | Attitudes: satisfaction, perceived ease | UMUX-Lite, CSAT after task, SUS quarterly |
| **E**ngagement | Level of involvement (for voluntary products) | Sessions/user/week, features used per session |
| **A**doption | New users of a product/feature | % employees submitting leave via portal vs email; new users of mobile app |
| **R**etention | Rate at which users return | % of managers active month over month |
| **T**ask success | Efficiency, effectiveness, errors | Leave request completion rate, time to submit, validation error rate |

**Goals → Signals → Metrics** process:

| Step | Question | Example |
|------|---------|---------|
| Goal | What is the user/product trying to achieve? | Managers can approve requests quickly |
| Signal | What user action/attitude indicates success or failure? | Approval actions within 24 h; abandonment of approval screen |
| Metric | How do we quantify it over time? | Median time-to-approval; % approved within 24 h; bulk approvals per manager |

- Not every dimension applies: **Engagement is often irrelevant (or inverse) for enterprise tools** — less time in the tool may be better. Pick 1–3 dimensions per feature.
- Pair with guardrail metrics (error rate, support tickets).

Source: <https://research.google/pubs/measuring-the-user-experience-on-a-large-scale-user-centered-metrics-for-web-applications/>

---

## 6. Other metric frameworks

| Framework | Origin | Content |
|-----------|--------|---------|
| **AARRR (Pirate metrics)** | Dave McClure, 2007 | Acquisition, Activation, Retention, Referral, Revenue |
| **North Star Metric** | Amplitude / growth practice | One metric capturing core value delivered (e.g. "invoices processed without manual correction") + input metrics |
| **PULSE** | Google (business/tech) | Page views, Uptime, Latency, Seven-day active users, Earnings — HEART was proposed to complement it |
| **OKRs** | Andy Grove (Intel), John Doerr | UX outcomes as key results (see [../02-product-management/README.md](../02-product-management/README.md)) |
| **Single Usability Metric (SUM)** | Sauro & Kindlund 2005 | Standardised combination of completion, time, errors, satisfaction |
| **DORA** | DevOps | Not UX, but deployment frequency enables UX iteration (see [../08-engineering/README.md](../08-engineering/README.md)) |

---

## 7. UX metrics for enterprise / internal tools

Because users are captive and engagement isn't the goal, focus on **efficiency, quality and cost**:

| Metric | Why |
|--------|-----|
| Time-on-task for top tasks (measured or KLM-predicted) | Labour cost: 30 s saved × 200 users × 50 times/day = 83 h/day |
| Error/correction rate, reversals, data quality issues | Downstream cost (payroll corrections, wrong invoices) |
| Support tickets / training hours per user | Cost-to-serve; learnability |
| Time to proficiency for new hires | Onboarding cost |
| Process cycle time (request → approval) | Business outcome |
| Workaround usage (Excel exports, shadow tools) | Indicates unmet needs |
| Adoption of self-service vs assisted channels | Channel shift savings |
| SUS / UMUX-Lite trend per release | Perceived usability |
| NASA-TLX for complex tasks | Workload |
| Accessibility conformance (# open WCAG issues) | Compliance (EN 301 549) |

**ROI of UX (rough model):** `annual saving = time saved per task × task frequency × users × loaded hourly rate`. Present with ranges, not point estimates.

---

## 8. Statistics essentials

| Need | Method |
|------|-------|
| CI for completion rate (small n) | Adjusted Wald |
| CI for mean rating (SUS, SEQ) | t-interval: `mean ± t(0.975, n−1) · s/√n` |
| Compare 2 completion rates | N−1 two-proportion test (independent), McNemar (within-subject) |
| Compare 2 means | Two-sample t-test (between), paired t-test (within) |
| Compare times | t-test on log-transformed times |
| Compare to benchmark | One-sample t-test (e.g. SUS vs 68) |
| Sample size for desired margin of error | `n ≈ (z·s/E)²` (means); `n ≈ z²·p(1−p)/E²` (proportions) |
| Practical significance | Effect size (Cohen's d) — small 0.2, medium 0.5, large 0.8 |

Reference: Sauro & Lewis, *Quantifying the User Experience* (2nd ed. 2016).

---

## 9. Instrumentation checklist

- [ ] Define events per HEART goal before building (event taxonomy: `object_action`, e.g. `leave_request_submitted`)
- [ ] Capture task start and end events to compute completion and duration
- [ ] Capture validation errors (field, error type) — not values (privacy)
- [ ] Capture search queries & zero-result searches
- [ ] Respect consent and GDPR (analytics on internal tools: works council / OR (ondernemingsraad) consent may be required in NL for employee monitoring)
- [ ] Dashboards per feature with baseline before release
- [ ] Intercept survey (UMUX-Lite/CES) after key tasks, sampled to avoid fatigue
- [ ] Tools: Matomo (self-hosted), PostHog, Amplitude, Mixpanel, Google Analytics 4, Microsoft Clarity, Application Insights

## Common mistakes

- Reporting percentages from 5 users without confidence intervals.
- Using NPS for internal mandatory tools.
- Measuring engagement (time in app) as success for productivity software.
- Averaging task times arithmetically on skewed data.
- Changing questionnaire wording and still comparing with benchmarks.
- Vanity metrics (page views) without goals.
- Measuring only after release — no baseline.

## References

- MeasuringU (Jeff Sauro): <https://measuringu.com/>
- SUS — MeasuringU: <https://measuringu.com/sus/>
- SUS — Wikipedia: <https://en.wikipedia.org/wiki/System_usability_scale>
- SUS — usability.gov (archived content): <https://www.usability.gov/how-to-and-tools/methods/system-usability-scale.html>
- UMUX-Lite: <https://measuringu.com/umux-lite/>
- SEQ: <https://measuringu.com/seq10/>
- Net Promoter Score: <https://en.wikipedia.org/wiki/Net_promoter_score>
- Customer satisfaction: <https://en.wikipedia.org/wiki/Customer_satisfaction>
- HEART paper (Google Research): <https://research.google/pubs/measuring-the-user-experience-on-a-large-scale-user-centered-metrics-for-web-applications/>
- NASA-TLX: <https://en.wikipedia.org/wiki/NASA-TLX>
- UEQ: <https://www.ueq-online.org/>
- Keystroke-level model: <https://en.wikipedia.org/wiki/Keystroke-level_model>
- Web Vitals: <https://web.dev/articles/vitals>
- NN/g — UX metrics and ROI: <https://www.nngroup.com/reports/ux-metrics-roi/>
- Tullis & Albert, *Measuring the User Experience*: <https://www.measuringux.com/>
