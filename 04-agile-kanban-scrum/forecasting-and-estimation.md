# Forecasting and Estimation

> **TL;DR** — Deterministic estimates (hours, story points × velocity) produce single-number answers that hide uncertainty. Probabilistic forecasting uses historical throughput and cycle time to answer "**When will it be done?**" and "**How many items by date X?**" with confidence levels (e.g. "85% chance by 14 March"). Monte Carlo simulation does this in seconds from data you already have. Story points and planning poker remain useful as *conversation tools* for shared understanding; #NoEstimates argues for right-sizing and counting items instead. Always forecast in ranges, re-forecast continuously, and account for scope growth and split rate.

---

## 1. Why estimate at all?

| Purpose | Need for estimation | Lighter alternative |
|---|---|---|
| Decide whether to do something (value vs cost) | Rough order of magnitude | T-shirt sizes, reference class |
| Commit to dates / coordinate dependencies | Forecast with confidence | Monte Carlo from throughput |
| Sequence work (WSJF/CD3 needs duration) | Relative size | T-shirt or "cheap / expensive" |
| Shared understanding within the team | Discussion uncovers hidden complexity | Three amigos, example mapping |
| Capacity planning for a sprint | Fit work into timebox | Count items (right-sized) |
| Budgeting | Order of magnitude | Fund teams/value streams, not projects |

Ask "what decision will this estimate support?" before estimating.

---

## 2. Deterministic vs probabilistic

| Aspect | Deterministic | Probabilistic |
|---|---|---|
| Output | Single date/number | Range with probabilities |
| Input | Expert estimates per item | Historical data (throughput, cycle time) |
| Effort | High (estimate every item) | Low (data from tools) |
| Handles uncertainty | Poorly (buffers, padding) | Explicitly |
| Typical accuracy | Systematic optimism (planning fallacy) | Calibrated if process stable |
| Communication | "It'll be done 1 June" | "50% by 20 May, 85% by 3 June, 95% by 17 June" |
| Risk | False certainty | Requires explaining probability |

**Planning fallacy** (Kahneman & Tversky): people underestimate time even with knowledge of past overruns. Fix: **reference class forecasting** — base on what actually happened in similar cases (which is what throughput-based forecasting does).

---

## 3. Story points

### 3.1 Concept
- Relative measure of effort/complexity/uncertainty, not time.
- Originated in XP ("ideal days" → "points"). Ron Jeffries (a co-inventor) has later expressed regret about how they're used: "I may have invented story points, and if I did, I'm sorry."
- Team-specific scale; meaningless across teams.

### 3.2 Scales

| Scale | Values | Rationale |
|---|---|---|
| Modified Fibonacci | 0, ½, 1, 2, 3, 5, 8, 13, 20, 40, 100, ? , ☕ | Gaps grow with size — reflects increasing uncertainty (Weber–Fechner: humans distinguish relative differences) |
| Pure Fibonacci | 1, 2, 3, 5, 8, 13, 21 | Same idea |
| Powers of 2 | 1, 2, 4, 8, 16 | Simple doubling |
| T-shirt | XS, S, M, L, XL | Non-numeric, avoids false arithmetic; good for roadmaps |

### 3.3 Velocity
- Sum of points of items Done per sprint. Use rolling average/range of last 3–5 sprints for planning.
- **Pitfalls:** see [scrum.md](scrum.md) §velocity — inflation, comparison, targets, partial credit, changing team composition.

### 3.4 Story points debate

| Pro | Contra |
|---|---|
| Fast relative sizing; avoids hour arguments | Abstract; stakeholders translate back to time anyway |
| Planning poker conversation reveals misunderstandings | Overhead of estimating everything |
| Velocity enables rough release planning | Velocity becomes a target → gamed |
| Accounts for complexity & uncertainty | Point/time correlation often weak in practice |
| Team-owned | Misused for productivity comparisons |

Empirical observation (Vacanti, Magennis, others): in many teams, **item count throughput forecasts as well as point-based velocity** when items are reasonably right-sized — because size variation within a team's items is smaller than flow variation (waiting, blockers).

---

## 4. Planning Poker

### 4.1 Procedure
1. PO/requester presents the item; team asks questions.
2. Each estimator privately selects a card.
3. All reveal simultaneously (avoids anchoring).
4. Highest and lowest explain their reasoning.
5. Re-vote; converge (typically 2–3 rounds) or take the higher/park the item.
6. Timebox per item (e.g. 5 min); "?" = need info, "☕" = need a break.

Origin: James Grenning (2002), popularised by Mike Cohn, *Agile Estimating and Planning* (2005). Based on Wideband Delphi.

### 4.2 Variants

| Technique | When | How |
|---|---|---|
| **Affinity estimation / silent sorting** | Many items (50+) | Silently arrange items on a wall from small to large; group into buckets |
| **T-shirt sizing** | Roadmap, epics | XS–XL; optional mapping to ranges |
| **Bucket system** | Large backlogs fast | Buckets 0,1,2,3,5,8,...; divide-and-conquer placement |
| **Dot voting** | Prioritisation (not sizing) | Stakeholders allocate dots |
| **Right-sizing check** | Kanban/#NoEstimates | Single question: "Does it fit our SLE?" yes → go, no → split |
| **Three-point (PERT)** | Single large item | (O + 4M + P) / 6; SD ≈ (P − O)/6 |

### 4.3 Planning poker anti-patterns
- Manager/architect votes first or states "that's a 3" (anchoring).
- Converting points to hours ("1 point = 1 day").
- Re-estimating done items to "correct" velocity.
- Estimating bugs and tasks with points (inflates velocity without value).
- Averaging divergent votes instead of discussing.
- Spending hours estimating items that may never be built.

---

## 5. #NoEstimates

- Movement (Woody Zuill, Vasco Duarte, Neil Killick, ~2012) questioning the value of estimates.
- Not "never forecast" but: **use data (counts of items) instead of estimating effort**; break work into small similar-sized pieces; deliver early to reduce the need for prediction.

| #NoEstimates practice | Description |
|---|---|
| Slice small | Split stories until roughly 1–3 days; size variation becomes noise |
| Count items | Throughput in items per week |
| Forecast from data | Monte Carlo or simple range math |
| Deliver value early | Reduce dependency on prediction |
| Ask "what decision?" | Estimate only when it changes a decision |

Book: Vasco Duarte, *NoEstimates: How to Measure Project Progress Without Estimating* (2016).

---

## 6. Monte Carlo forecasting

### 6.1 Concept
Simulate the future thousands of times by randomly sampling from historical data (bootstrap), then read off percentiles of the outcomes.

### 6.2 "When will N items be done?" (completion date)

**Algorithm:**
```
inputs: historical daily (or weekly) throughput samples T[], backlog size N
repeat 10,000 times:
    remaining = N; days = 0
    while remaining > 0:
        remaining -= random_choice(T)
        days += 1
    record days
sort results; report 50th, 85th, 95th percentiles as dates
```
**Important:** read the percentiles from the *high* end — 85% means "85% of simulations finished on or before this date".

### 6.3 "How many items by date D?"

```
inputs: T[], number of days d until date D
repeat 10,000 times:
    total = sum(random_choice(T) for each of d days)
    record total
sort results; report the 15th percentile as "85% confident at least X items",
5th percentile as "95% confident at least Y"
```
**Important:** for "how many", read percentiles from the *low* end — 85% confidence = the value that 85% of simulations *exceeded*.

### 6.4 Worked example (weekly throughput)

Last 10 weeks throughput: `[3, 5, 4, 6, 2, 5, 4, 7, 3, 5]` (mean 4.4).
Backlog: 30 items. Add scope growth assumption: +20% (discovered work / splits) → 36 items.

Simulation outcome (illustrative):

| Percentile | Weeks to complete 36 items | Interpretation |
|---|---|---|
| 50th | 8.2 → 9 weeks | Coin flip |
| 85th | 9.5 → 10 weeks | Recommended commitment |
| 95th | 10.4 → 11 weeks | Conservative |

Naïve deterministic: 36 / 4.4 = 8.2 weeks → "8 weeks" → ~50% chance of being late.

"How many in 6 weeks?" → 85% confidence ≥ 23 items; 95% ≥ 21 items; 50% ≈ 26 items.

### 6.5 Python snippet

```python
import random

def when(throughput, backlog, trials=10_000):
    results = []
    for _ in range(trials):
        remaining, periods = backlog, 0
        while remaining > 0:
            remaining -= random.choice(throughput)
            periods += 1
        results.append(periods)
    results.sort()
    return {p: results[int(trials * p / 100) - 1] for p in (50, 85, 95)}

def how_many(throughput, periods, trials=10_000):
    results = sorted(sum(random.choice(throughput) for _ in range(periods))
                     for _ in range(trials))
    # confidence c% => value exceeded in c% of trials => (100-c)th percentile
    return {c: results[int(trials * (100 - c) / 100)] for c in (50, 85, 95)}

weekly = [3, 5, 4, 6, 2, 5, 4, 7, 3, 5]
print(when(weekly, 36))
print(how_many(weekly, 6))
```

Note: if your sample has zeros (weeks with no throughput), the "when" loop still terminates as long as at least one sample is > 0.

### 6.6 Inputs and pitfalls

| Factor | Guidance |
|---|---|
| Sample window | Recent, representative (e.g. last 8–12 weeks / 30–90 days); exclude holiday anomalies or include if they'll recur |
| Sample granularity | Daily samples give more variety; weekly smoother. Be consistent |
| Scope growth | Backlogs grow (split rate 1.0–3.0×, discovered work). Multiply backlog by split/growth factor or model a range |
| Work mix | Throughput includes defects and support; if backlog only counts features, adjust for allocation |
| Team changes | New members/holidays → adjust or reduce confidence |
| Dependencies | Monte Carlo per team doesn't model cross-team waits; add risk factors |
| Stability | If WIP/arrivals unstable, forecasts degrade — fix flow first |
| Re-forecast | Weekly; forecasts are not commitments set in stone |

### 6.7 Single-item forecasting
For one item: use cycle time percentiles directly — "Once started, 85% of items finish within 11 days." Combine with queue position: if 6 items are ahead in Ready and throughput ~5/week, start in ~1–1.5 weeks, then + SLE.

### 6.8 Multi-feature forecasting
- Forecast features in priority order using the same throughput, accounting for capacity allocation (e.g. only 60% of throughput goes to features).
- Tools: ActionableAgile Analytics, Nave, Troy Magennis's free spreadsheets (Focused Objective), Jira/ADO marketplace apps.

---

## 7. Communicating forecasts

| Do | Don't |
|---|---|
| Give ranges with probabilities | Give a single date |
| Explain the data source | Hide assumptions |
| Update regularly and show trend | Forecast once at the start |
| Separate "commitment" (85–95%) from "target" (50%) | Treat 50% forecasts as commitments |
| Show what would move the date (scope, WIP, dependencies) | Promise overtime to hit a date |
| Use visual (histogram of simulated completion dates) | Bury in tables |

**Template:**
> "Based on our last 10 weeks of delivery and the current 30-item scope (+20% expected growth), we have a 50% chance of finishing by 12 May, 85% by 26 May, 95% by 2 June. We'll re-forecast weekly. Reducing scope by 5 items moves the 85% date to 12 May."

---

## 8. Fixed-date planning with flow

1. Determine required scope & its size in items (with growth factor).
2. Run "how many by date" → 85% confidence number X.
3. If required scope > X: cut scope (MoSCoW), add earlier start, reduce WIP elsewhere, or accept risk explicitly.
4. Fixed-date CoS items: start no later than due date − 85th/95th percentile cycle time.
5. Track weekly with re-forecast; trigger expedite if probability drops below threshold.

---

## 9. Other estimation approaches (reference)

| Method | Use | Note |
|---|---|---|
| Wideband Delphi | Expert consensus | Ancestor of planning poker |
| PERT three-point | Single large tasks | Beta distribution assumption |
| Function points / COSMIC | Contractual sizing | Heavy; used in procurement |
| COCOMO II | Parametric model | Waterfall-era; still in some public sectors |
| Reference class forecasting | Large projects | Flyvbjerg; outside view |
| Ideal days | Early XP | Replaced by points |
| Cone of uncertainty | Explains estimate ranges narrowing over time | McConnell, Boehm |

For project-level estimation and contracting see [../03-project-management/README.md](../03-project-management/README.md).

---

## 10. When to use / when not

| Situation | Recommended |
|---|---|
| Stable team, ≥ 8 weeks of data, right-sized items | Monte Carlo from throughput |
| New team, no data | Reference class (similar teams/projects), quick spikes, short initial forecast horizon; collect data ASAP |
| Single item date question | Cycle time percentiles + queue position |
| Large initiative, months away | T-shirt sizes + ranges; reforecast as discovered |
| Contractual fixed price | Ranges + risk buffer; consider phased contracts |
| Team needs shared understanding | Planning poker (value is the conversation) |

---

## 11. Common mistakes

1. Using averages for forecasts ("velocity 30 → 3 sprints") → ~50% confidence at best.
2. Ignoring scope growth.
3. Treating forecasts as commitments and punishing misses → padding.
4. Re-estimating to make burn charts look good.
5. Mixing points and hours.
6. Forecasting an unstable system (arrivals > throughput) without fixing flow.
7. Using story points for cross-team capacity in scaled planning (normalised points in SAFe are especially prone to misuse).
8. Forgetting non-feature work in throughput (defects, support).

---

## References

- Wikipedia: Monte Carlo method — <https://en.wikipedia.org/wiki/Monte_Carlo_method>
- Wikipedia: Planning poker — <https://en.wikipedia.org/wiki/Planning_poker>
- Wikipedia: User story — <https://en.wikipedia.org/wiki/User_story>
- Wikipedia: Planning fallacy — <https://en.wikipedia.org/wiki/Planning_fallacy>
- Wikipedia: Reference class forecasting — <https://en.wikipedia.org/wiki/Reference_class_forecasting>
- Wikipedia: Wideband delphi — <https://en.wikipedia.org/wiki/Wideband_delphi>
- Wikipedia: Cone of uncertainty — <https://en.wikipedia.org/wiki/Cone_of_Uncertainty>
- Focused Objective (Troy Magennis) — <https://www.focusedobjective.com/>
- ActionableAgile — <https://actionableagile.com/>
- Mountain Goat Software (Mike Cohn) — <https://www.mountaingoatsoftware.com/>
- Mike Cohn, *Agile Estimating and Planning* (2005)
- Daniel Vacanti, *When Will It Be Done?* (2020)
- Troy Magennis, *Forecasting and Simulating Software Development Projects* (2011)
- Vasco Duarte, *NoEstimates* (2016)
- Steve McConnell, *Software Estimation: Demystifying the Black Art* (2006)
- Douglas Hubbard, *How to Measure Anything* (3rd ed., 2014)
