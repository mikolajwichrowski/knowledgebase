# Statistics for Engineers

> **TL;DR** — Most engineering data (latency, file sizes, incident durations, cycle times, user activity) is skewed and heavy-tailed, so averages mislead: report medians and high percentiles, look at the distribution, and never average percentiles. Respect base rates, sample sizes and confounders before claiming an effect. Plot the data before trusting a summary statistic (Anscombe). Correlation is a hint, not a cause.

Entry format: **Statement** / **Origin** / **In practice** / **Example** / **Link**. Data and ML architecture topics: [../07-data-ai-architecture/README.md](../07-data-ai-architecture/README.md).

Contents:
1. [Summary statistics](#summary-statistics)
2. [Latency and performance measurement](#latency-and-performance-measurement)
3. [Distributions](#distributions)
4. [Inference and experiments](#inference-and-experiments)
5. [Paradoxes and pitfalls](#paradoxes-and-pitfalls)
6. [Empirical laws of numbers](#empirical-laws-of-numbers)
7. [Simulation and resampling](#simulation-and-resampling)
8. [Cheat sheet: which statistic when](#cheat-sheet-which-statistic-when)

---

## Summary statistics

### Mean
- **Statement:** The arithmetic average: sum divided by count. Sensitive to outliers.
- **Origin:** Ancient; formalised in error theory by Gauss and Legendre (early 19th century).
- **In practice:** Useful for additive quantities (total cost = mean × count, capacity planning, Little's law). Misleading for skewed data: a few 10-second requests can make "average latency" look fine or terrible.
- **Example:** Mean income in a bar rises dramatically when a billionaire walks in; median barely moves.
- **Link:** https://en.wikipedia.org/wiki/Arithmetic_mean

### Median
- **Statement:** The middle value (50th percentile); half the observations are below it.
- **Origin:** Concept from Edward Wright (1599); popularised by Francis Galton, 1880s.
- **In practice:** Robust "typical" value for skewed data (latency, cycle time, house prices). Report alongside tail percentiles.
- **Example:** Median cycle time 3 days, mean 7 days — a few long-running items drag the mean.
- **Link:** https://en.wikipedia.org/wiki/Median

### Percentiles (p50, p95, p99)
- **Statement:** The value below which a given percentage of observations fall.
- **Origin:** Galton, 1885 (term "percentile").
- **In practice:** SLOs are typically defined on percentiles ("p99 < 300 ms"). p99 means 1 in 100 requests is slower — a user loading a page with 100 resources almost surely hits it. Need enough samples: estimating p99.9 needs many thousands of points.
- **Example:** p50 = 40 ms, p99 = 1.2 s reveals a tail problem the mean (90 ms) hides.
- **Link:** https://en.wikipedia.org/wiki/Percentile

### Variance and standard deviation
- **Statement:** Variance is the average squared deviation from the mean; standard deviation is its square root, in the original units.
- **Origin:** Karl Pearson (standard deviation, 1894); Ronald Fisher (variance, 1918).
- **In practice:** Variability drives queueing delay (Kingman), forecast uncertainty and planning risk. Reducing variance (smaller, uniform work items) often matters more than reducing the mean. Standard deviation is less meaningful for heavy-tailed data.
- **Example:** Two teams with the same mean cycle time; the one with lower variance is far more predictable.
- **Link:** https://en.wikipedia.org/wiki/Standard_deviation

### Coefficient of variation
- **Statement:** Standard deviation divided by the mean; a unitless measure of relative variability.
- **Origin:** Karl Pearson, 1896.
- **In practice:** Appears in Kingman's formula; compares variability across metrics of different scales. CV ≈ 1 for exponential distributions.
- **Example:** Service time CV of 2 means queues will be much worse than for a deterministic process.
- **Link:** https://en.wikipedia.org/wiki/Coefficient_of_variation

### Histograms and distributions over summaries
- **Statement:** Visualise the full distribution (histogram, CDF, heatmap) before choosing summary statistics.
- **Origin:** Karl Pearson coined "histogram", 1891; John Tukey, *Exploratory Data Analysis*, 1977.
- **In practice:** Latency heatmaps reveal bimodality (cache hit vs miss) that a single percentile hides. Use HDR-style histograms that preserve tail accuracy and can be merged.
- **Example:** A latency histogram with peaks at 5 ms and 200 ms → two code paths.
- **Link:** https://en.wikipedia.org/wiki/Histogram

---

## Latency and performance measurement

### Never average percentiles
- **Statement:** Percentiles are not additive: the average of per-host p99s is not the fleet p99, and percentiles over time windows cannot be averaged into a longer window.
- **Origin:** Basic order-statistics property; widely discussed in observability practice (e.g., Gil Tene's talks on latency measurement).
- **In practice:** Aggregate raw data or mergeable histograms/sketches (HDR histograms, t-digest, DDSketch), then compute percentiles.
- **Example:** Ten hosts at p99 = 100 ms and one at 5 s; the averaged "p99" hides that the fleet p99 is in seconds.
- **Link:** https://en.wikipedia.org/wiki/Percentile

### Tail latency amplification
- **Statement:** In fan-out systems, the probability that a request hits at least one slow backend grows rapidly: P(slow) = 1 − (1 − p)ⁿ.
- **Origin:** Jeffrey Dean and Luiz André Barroso, "The Tail at Scale", *Communications of the ACM*, 2013.
- **In practice:** With 100 backends each slow 1% of the time, 63% of requests are slow. Use hedged requests, tied requests, timeouts and reduce fan-out.
- **Example:** A search page querying 50 shards is only as fast as the slowest shard.
- **Link:** https://research.google/pubs/the-tail-at-scale/

### Coordinated omission
- **Statement:** Load generators that wait for responses before sending the next request fail to record the latency real users would have experienced during stalls, severely understating tail latency.
- **Origin:** Gil Tene (Azul Systems), ~2013; addressed in HdrHistogram and wrk2.
- **In practice:** Use open-model (constant arrival rate) load testing; correct for missed intervals.
- **Example:** A 2-second GC pause recorded as one slow request instead of hundreds of delayed ones.
- **Link:** https://en.wikipedia.org/wiki/Load_testing

### Utilisation vs latency
- **Statement:** Response time rises non-linearly as utilisation approaches 100% (for M/M/1, mean response = service time / (1 − ρ)).
- **Origin:** Queueing theory (Erlang, Kendall, Kingman); see Kingman's formula in [software-and-engineering-laws.md](software-and-engineering-laws.md).
- **In practice:** At 50% utilisation response time is 2× service time; at 90% it is 10×; at 99% it is 100×. Applies equally to people and teams.
- **Example:** A database at 95% CPU shows erratic latency spikes even though "it still has 5% left".
- **Link:** https://en.wikipedia.org/wiki/Queueing_theory

---

## Distributions

### Normal distribution
- **Statement:** The symmetric bell curve defined by mean and standard deviation; ~68/95/99.7% of values fall within 1/2/3 standard deviations.
- **Origin:** Abraham de Moivre, 1733; Carl Friedrich Gauss, 1809.
- **In practice:** Arises from sums of many small independent effects (measurement error, averages of samples). Rarely fits raw latency, sizes or human productivity. Don't assume normality for SLOs or outlier detection without checking.
- **Example:** Manufacturing tolerances; average of 1,000 request latencies (via CLT).
- **Link:** https://en.wikipedia.org/wiki/Normal_distribution

### Log-normal distribution
- **Statement:** A variable whose logarithm is normally distributed; right-skewed, always positive.
- **Origin:** Francis Galton and Donald McAlister, 1879.
- **In practice:** Arises from multiplicative processes: latencies, task durations, file sizes, project overruns. Analyse on a log scale; use the geometric mean or median.
- **Example:** Task estimates are often off by a multiplicative factor (×2, ×3), not an additive amount.
- **Link:** https://en.wikipedia.org/wiki/Log-normal_distribution

### Power law
- **Statement:** One quantity varies as a power of another: P(x) ∝ x^(−α). Scale-invariant, heavy-tailed; the mean or variance may be undefined for small α.
- **Origin:** Pareto (1896), Zipf (1935), Barabási & Albert (scale-free networks, 1999).
- **In practice:** Popularity of content/keys (hot partitions), customer revenue, bug-prone files, outage sizes. Plan for hot keys and tail customers; averages are meaningless. Verify claims carefully (Clauset, Shalizi & Newman, 2009) — many "power laws" are log-normal.
- **Example:** 1% of keys receive 50% of cache traffic.
- **Link:** https://en.wikipedia.org/wiki/Power_law

### Pareto distribution
- **Statement:** A specific power-law probability distribution underlying the 80/20 rule.
- **Origin:** Vilfredo Pareto, 1896.
- **In practice:** Use for modelling wealth-like quantities (revenue per customer, size of incidents). Its parameter α determines the "80/20" vs "99/1" split.
- **Example:** Revenue concentration across enterprise customers.
- **Link:** https://en.wikipedia.org/wiki/Pareto_distribution

### Heavy-tailed distribution
- **Statement:** A distribution whose tail decays more slowly than exponential — extreme values are far more common than a normal model predicts.
- **Origin:** Probability theory; Mandelbrot's work on cotton prices, 1963.
- **In practice:** Means converge slowly; sample maxima keep growing; capacity must be planned for tails. Taleb's "Extremistan".
- **Example:** A single customer's data import is 1,000× the median size.
- **Link:** https://en.wikipedia.org/wiki/Heavy-tailed_distribution

### Poisson distribution
- **Statement:** Number of events in a fixed interval when events occur independently at a constant average rate λ; mean = variance = λ.
- **Origin:** Siméon Denis Poisson, 1837; Ladislaus Bortkiewicz's Prussian horse-kick deaths, 1898.
- **In practice:** Models request arrivals, error counts, incidents per week — when events are independent. Bursty/correlated traffic violates it (overdispersion).
- **Example:** Averaging 4 incidents/month, a month with 8 has ~3% probability under Poisson — notable but not necessarily special-cause.
- **Link:** https://en.wikipedia.org/wiki/Poisson_distribution

### Exponential distribution
- **Statement:** Time between events in a Poisson process; memoryless — the probability of waiting another t is independent of how long you've waited.
- **Origin:** Follows from Poisson processes; used by Erlang in teletraffic.
- **In practice:** Inter-arrival times, M/M/1 service times, time-to-failure for components with constant hazard rate. Many real durations are *not* memoryless (they are log-normal or heavy-tailed).
- **Example:** Arrival gaps between independent user requests.
- **Link:** https://en.wikipedia.org/wiki/Exponential_distribution

### Multimodal distribution
- **Statement:** A distribution with two or more peaks, usually indicating a mixture of populations.
- **Origin:** Statistics; Karl Pearson's analysis of crab measurements, 1894.
- **In practice:** Segment before summarising: cache hit/miss, mobile/desktop, free/paid users. Means of bimodal data describe nobody.
- **Example:** API latency peaks at 10 ms (cached) and 300 ms (uncached).
- **Link:** https://en.wikipedia.org/wiki/Multimodal_distribution

---

## Inference and experiments

### Law of large numbers
- **Statement:** As sample size grows, the sample average converges to the expected value.
- **Origin:** Jacob Bernoulli, *Ars Conjectandi*, 1713.
- **In practice:** Small samples are noisy; daily metrics on low-traffic features swing wildly. Convergence is slow for heavy-tailed data.
- **Example:** A new feature's conversion rate on day 1 (n = 40) is unreliable.
- **Link:** https://en.wikipedia.org/wiki/Law_of_large_numbers

### Law of small numbers
- **Statement:** People wrongly expect small samples to be representative of the population.
- **Origin:** Amos Tversky and Daniel Kahneman, "Belief in the Law of Small Numbers", 1971.
- **In practice:** Five user interviews reveal usability issues but cannot estimate prevalence; two sprints don't establish a velocity.
- **Example:** Small counties show both the highest and lowest cancer rates — because they're small.
- **Link:** https://en.wikipedia.org/wiki/Faulty_generalization

### Central limit theorem
- **Statement:** The sum or mean of many independent random variables with finite variance tends towards a normal distribution, regardless of the original distribution.
- **Origin:** de Moivre (1733), Laplace (1810), Lyapunov (1901).
- **In practice:** Justifies normal-based confidence intervals for means with large samples; requires finite variance (fails for some power laws) and independence.
- **Example:** Averages of daily latency samples are approximately normal even though individual latencies are not.
- **Link:** https://en.wikipedia.org/wiki/Central_limit_theorem

### Confidence intervals
- **Statement:** A range computed from data such that, over repeated sampling, a stated proportion (e.g., 95%) of such intervals would contain the true parameter.
- **Origin:** Jerzy Neyman, 1937.
- **In practice:** Report effect estimates with intervals ("+2.1% conversion, 95% CI [0.3%, 3.9%]"), not just "significant". A 95% CI does not mean 95% probability the true value is inside for this specific interval (that's a Bayesian credible interval).
- **Example:** A wide CI on a benchmark means you need more runs.
- **Link:** https://en.wikipedia.org/wiki/Confidence_interval

### Margin of error
- **Statement:** Half the width of a confidence interval; for proportions roughly 1/√n at 95% confidence.
- **Origin:** Survey sampling theory, 20th century.
- **In practice:** A survey of 100 users has about ±10% margin; 1,000 users about ±3%.
- **Example:** "52% prefer the new design" from 100 responses is not a clear majority.
- **Link:** https://en.wikipedia.org/wiki/Margin_of_error

### P-values and their misuse
- **Statement:** A p-value is the probability of observing data at least as extreme as yours *if the null hypothesis were true*. It is not the probability the hypothesis is true, nor the size or importance of an effect.
- **Origin:** Karl Pearson (1900), Ronald Fisher (1925); ASA Statement on p-values, 2016.
- **In practice:** p < 0.05 with a tiny effect may be useless; p > 0.05 isn't proof of no effect. Pre-register metrics, report effect sizes and intervals, consider practical significance.
- **Example:** A statistically significant 0.01% conversion lift that doesn't cover engineering cost.
- **Link:** https://en.wikipedia.org/wiki/Misuse_of_p-values

### Type I and Type II errors
- **Statement:** Type I = false positive (rejecting a true null); Type II = false negative (missing a real effect).
- **Origin:** Jerzy Neyman and Egon Pearson, 1928–1933.
- **In practice:** Alerting is a hypothesis test: tune thresholds to balance false pages (Type I) against missed incidents (Type II), considering base rates.
- **Example:** A sensitive anomaly detector pages nightly for noise.
- **Link:** https://en.wikipedia.org/wiki/Type_I_and_type_II_errors

### Statistical power and sample size
- **Statement:** Power is the probability of detecting an effect of a given size if it exists; it depends on sample size, effect size, variance and significance level.
- **Origin:** Neyman–Pearson; Jacob Cohen, *Statistical Power Analysis*, 1969/1988.
- **In practice:** Compute required sample size *before* an A/B test; low-traffic products cannot detect small effects — test bigger changes or use qualitative methods. Underpowered significant results overestimate effects ("winner's curse").
- **Example:** Detecting a 1% relative lift on a 5% conversion rate needs millions of visitors per variant.
- **Link:** https://en.wikipedia.org/wiki/Sample_size_determination

### Effect size
- **Statement:** A quantitative measure of the magnitude of a phenomenon (difference in means, Cohen's d, relative lift), independent of sample size.
- **Origin:** Jacob Cohen, 1960s.
- **In practice:** Decide on the minimum effect worth detecting (business-wise) before experimenting.
- **Example:** "Latency reduced by 120 ms at p95" is more useful than "p = 0.003".
- **Link:** https://en.wikipedia.org/wiki/Effect_size

### Multiple comparisons and p-hacking
- **Statement:** Testing many hypotheses inflates the chance of false positives; with 20 independent tests at α = 0.05, expect one "significant" result by chance.
- **Origin:** Carlo Bonferroni (1936); Benjamini & Hochberg false discovery rate, 1995; Simmons, Nelson & Simonsohn, "False-Positive Psychology", 2011.
- **In practice:** Pre-register primary metrics; correct for multiple comparisons; treat post-hoc segment findings as hypotheses for a new test.
- **Example:** "The variant won for left-handed Android users in Belgium."
- **Link:** https://en.wikipedia.org/wiki/Multiple_comparisons_problem

### Peeking in A/B tests
- **Statement:** Repeatedly checking results and stopping when significant greatly inflates false-positive rates.
- **Origin:** Armitage, McPherson & Rowe, 1969; sequential analysis (Abraham Wald, 1945).
- **In practice:** Fix the sample size in advance or use sequential/always-valid methods designed for continuous monitoring.
- **Example:** Stopping the test on day 3 when p dipped below 0.05.
- **Link:** https://en.wikipedia.org/wiki/Sequential_analysis

### Bayesian vs frequentist inference
- **Statement:** Frequentist methods treat parameters as fixed and data as random; Bayesian methods treat parameters as uncertain and update prior beliefs with data into posterior distributions.
- **Origin:** Bayes (1763), Laplace; Fisher, Neyman–Pearson (frequentist, 1920s–30s).
- **In practice:** Bayesian outputs ("94% probability B is better") are easier for stakeholders; priors must be justified. Both need good experimental design.
- **Example:** A Bayesian A/B test with a weakly informative prior on conversion.
- **Link:** https://en.wikipedia.org/wiki/Bayesian_inference

### Rule of three (statistics)
- **Statement:** If zero events are observed in n trials, the 95% upper confidence bound on the event rate is about 3/n.
- **Origin:** Hanley & Lippman-Hand, *JAMA*, 1983.
- **In practice:** "No failures in 300 deploys" means the failure rate could still be up to ~1%.
- **Example:** Zero errors in 1,000 test transactions → error rate plausibly up to 0.3%.
- **Link:** https://en.wikipedia.org/wiki/Rule_of_three_(statistics)

---

## Paradoxes and pitfalls

### Correlation does not imply causation
- **Statement:** Two variables moving together may be caused by a third factor, reverse causation or coincidence.
- **Origin:** Karl Pearson (correlation, 1890s); principle long established; causal inference formalised by Judea Pearl (*Causality*, 2000) and Rubin's potential outcomes.
- **In practice:** Use randomised experiments, natural experiments or causal diagrams (DAGs) before acting. Spurious correlations are abundant in large dashboards.
- **Example:** Teams that write more tests have more bugs — because they work on the most complex code.
- **Link:** https://en.wikipedia.org/wiki/Correlation_does_not_imply_causation

### Confounding
- **Statement:** A confounder influences both the supposed cause and the effect, creating a spurious association.
- **Origin:** Epidemiology and statistics, 20th century.
- **In practice:** Compare like with like; randomise; stratify. Users who adopt feature X might be power users who'd retain anyway.
- **Example:** "Users of dark mode have higher retention" (confounded by engagement).
- **Link:** https://en.wikipedia.org/wiki/Confounding

### Simpson's paradox
- **Statement:** A trend appearing in several groups can reverse when the groups are combined, due to unequal group sizes or a lurking variable.
- **Origin:** Edward H. Simpson, 1951 (earlier Pearson 1899, Yule 1903). UC Berkeley admissions case, 1973.
- **In practice:** Always segment metrics by key dimensions (platform, region, customer size) before concluding; mix shifts can create false trends.
- **Example:** Overall error rate rises after a release though every endpoint's error rate fell — traffic shifted to an inherently error-prone endpoint.
- **Link:** https://en.wikipedia.org/wiki/Simpson%27s_paradox

### Berkson's paradox
- **Statement:** Selecting on a combination of two variables creates a spurious (often negative) correlation between them.
- **Origin:** Joseph Berkson, 1946 (hospital patients).
- **In practice:** Among hired candidates, interview score and experience may appear negatively correlated because you hire people strong in at least one.
- **Example:** Among popular open-source libraries, code quality and documentation quality appear inversely related.
- **Link:** https://en.wikipedia.org/wiki/Berkson%27s_paradox

### Selection bias
- **Statement:** The sample is not representative because of how it was selected.
- **Origin:** Statistics and epidemiology; e.g., *Literary Digest* 1936 poll.
- **In practice:** Feedback from beta users, NPS respondents or GitHub issue reporters over-represents engaged users. Survivorship bias is a special case (see [cognitive-biases.md](cognitive-biases.md)).
- **Example:** Bug reports come from users on the latest version, hiding problems with old clients.
- **Link:** https://en.wikipedia.org/wiki/Selection_bias

### Regression toward the mean
- **Statement:** Extreme measurements tend to be followed by values closer to the average, purely due to random variation.
- **Origin:** Francis Galton, heights of parents and children, 1886.
- **In practice:** A team's worst sprint is usually followed by a better one regardless of intervention; praising after great performance "fails" and criticism after bad "works" — illusorily. Use control groups.
- **Example:** The "Sports Illustrated cover jinx"; an incident-heavy month followed by a quiet one after a new process.
- **Link:** https://en.wikipedia.org/wiki/Regression_toward_the_mean

### Prosecutor's fallacy
- **Statement:** Confusing P(evidence | innocent) with P(innocent | evidence).
- **Origin:** Thompson & Schumann, 1987.
- **In practice:** "This anomaly only happens 0.1% of the time under normal conditions, so it's surely an attack" ignores how rare attacks are (base rate).
- **Example:** A fraud rule with 0.1% false-positive rate on 10M transactions still flags 10,000 legitimate ones.
- **Link:** https://en.wikipedia.org/wiki/Prosecutor%27s_fallacy

### Ecological fallacy
- **Statement:** Inferring individual-level behaviour from group-level aggregates.
- **Origin:** William S. Robinson, 1950.
- **In practice:** "Customers in region X churn more" does not mean any particular customer there will churn; per-team averages don't describe individuals.
- **Example:** Assuming every developer on a high-velocity team is high-performing.
- **Link:** https://en.wikipedia.org/wiki/Ecological_fallacy

### Anscombe's quartet
- **Statement:** Four datasets with nearly identical means, variances, correlations and regression lines that look completely different when plotted.
- **Origin:** Francis Anscombe, 1973. Modern variant: the "Datasaurus Dozen" (Matejka & Fitzmaurice, 2017).
- **In practice:** Always plot data; summary dashboards can hide outliers, non-linearity and clusters.
- **Example:** Two services with the same mean/SD latency: one stable, one with periodic spikes.
- **Link:** https://en.wikipedia.org/wiki/Anscombe%27s_quartet

### Replication crisis
- **Statement:** Many published findings, especially in psychology and medicine, fail to replicate, due to small samples, p-hacking and publication bias.
- **Origin:** John Ioannidis, "Why Most Published Research Findings Are False", 2005; Open Science Collaboration, 2015.
- **In practice:** Treat single-study claims (including popular management and productivity research) as tentative; prefer meta-analyses and your own measurements.
- **Example:** Ego depletion and power posing effects weakened under large replications.
- **Link:** https://en.wikipedia.org/wiki/Replication_crisis

---

## Empirical laws of numbers

### Benford's law
- **Statement:** In many naturally occurring datasets, the leading digit d occurs with probability log₁₀(1 + 1/d): "1" leads ~30% of the time, "9" under 5%.
- **Origin:** Simon Newcomb, 1881; Frank Benford, 1938.
- **In practice:** Used for fraud and data-quality detection in financial data and expense claims; applies to data spanning several orders of magnitude, not to assigned numbers (IDs, phone numbers).
- **Example:** Fabricated invoice amounts often deviate from Benford's distribution.
- **Link:** https://en.wikipedia.org/wiki/Benford%27s_law

### Zipf's law
- **Statement:** The frequency of an item is inversely proportional to its rank: the second most common occurs ~1/2 as often as the first, the third ~1/3, etc.
- **Origin:** George Kingsley Zipf, 1935/1949 (word frequencies; earlier observed by Jean-Baptiste Estoup and Felix Auerbach).
- **In practice:** Cache sizing (a small cache serves most requests), search query distributions, tokenisation and vocabulary in NLP, popularity of products.
- **Example:** Caching the top 1% of URLs serves a majority of traffic.
- **Link:** https://en.wikipedia.org/wiki/Zipf%27s_law

### Long tail
- **Statement:** In a heavy-tailed distribution, the many low-frequency items can collectively exceed the few high-frequency ones.
- **Origin:** Chris Anderson, *Wired*, 2004.
- **In practice:** Long-tail features, queries or customers matter in aggregate; also a source of support cost. Decide deliberately whether to serve the tail.
- **Example:** Niche search queries make up a large share of total search volume.
- **Link:** https://en.wikipedia.org/wiki/Long_tail

---

## Simulation and resampling

### Monte Carlo simulation
- **Statement:** Estimate outcomes by running many random simulations from input distributions.
- **Origin:** Stanislaw Ulam and John von Neumann, Los Alamos, 1946.
- **In practice:** Delivery forecasting from historical throughput ("85% chance of finishing 40 items by date X"), capacity planning, risk analysis. See [../04-agile-kanban-scrum/README.md](../04-agile-kanban-scrum/README.md).
- **Example:** Sampling 10,000 futures from the last 12 weeks of throughput.
- **Link:** https://en.wikipedia.org/wiki/Monte_Carlo_method

### Bootstrapping
- **Statement:** Estimate the sampling distribution of a statistic by resampling the observed data with replacement.
- **Origin:** Bradley Efron, 1979.
- **In practice:** Confidence intervals for medians, percentiles and other statistics without distribution assumptions — good for benchmark comparisons.
- **Example:** Bootstrapped 95% CI for the difference in p95 latency between two builds.
- **Link:** https://en.wikipedia.org/wiki/Bootstrapping_(statistics)

---

## Cheat sheet: which statistic when

| Question | Use | Avoid |
|---|---|---|
| Typical user latency | Median (p50) | Mean of skewed data |
| Worst-case experience / SLO | p95, p99, p99.9 (with enough samples) | Max (single outlier), averaged percentiles |
| Capacity / total cost | Mean × volume | Median (not additive) |
| Delivery forecast | Percentiles of cycle time; Monte Carlo on throughput | Average velocity as a single point |
| Is a change real? | Control chart, effect size + CI, A/B with power analysis | Comparing two single numbers |
| Compare groups | Segment first (Simpson), randomise | Aggregates across shifting mixes |
| Rare events | Poisson/rule of three, base rates | Small-sample percentages |
| Data shape unknown | Plot histogram/CDF/heatmap | Summary stats alone (Anscombe) |

---

## References
- Tukey, J. *Exploratory Data Analysis* (1977)
- Dean, J. & Barroso, L. A. "The Tail at Scale" (2013) — https://research.google/pubs/the-tail-at-scale/
- Wasserstein & Lazar, "The ASA Statement on p-Values" (2016) — https://en.wikipedia.org/wiki/Misuse_of_p-values
- Kohavi, Tang & Xu, *Trustworthy Online Controlled Experiments* (2020) — https://en.wikipedia.org/wiki/A/B_testing
- Pearl, J. & Mackenzie, D. *The Book of Why* (2018) — https://en.wikipedia.org/wiki/The_Book_of_Why
- Clauset, Shalizi & Newman, "Power-law distributions in empirical data" (2009) — https://en.wikipedia.org/wiki/Power_law
- Spiegelhalter, D. *The Art of Statistics* (2019)
- Related sections: [../07-data-ai-architecture/README.md](../07-data-ai-architecture/README.md), [../04-agile-kanban-scrum/README.md](../04-agile-kanban-scrum/README.md), [../08-engineering/README.md](../08-engineering/README.md)
