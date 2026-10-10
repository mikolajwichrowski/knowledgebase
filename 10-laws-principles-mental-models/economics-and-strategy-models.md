# Economics and Strategy Models

> **TL;DR** — Economic reasoning for technology decisions: every choice has an opportunity cost; sunk costs are irrelevant; decide at the margin; quantify cost of delay; buy when transaction costs are low and the capability doesn't differentiate; expect efficiency gains to increase consumption (Jevons); expect labour-intensive services to get relatively more expensive (Baumol); and understand how networks, scale, switching costs and information asymmetries create or destroy competitive advantage. Detailed strategy frameworks (Porter, BCG, business model canvas, capability maps) live in [../11-enterprise-architecture-business/README.md](../11-enterprise-architecture-business/README.md).

Entry format: **Statement** / **Origin** / **In practice** / **Example** / **Link**.

Contents:
1. [Costs and decisions](#costs-and-decisions)
2. [Prioritisation economics](#prioritisation-economics)
3. [Scale and production](#scale-and-production)
4. [Firms, markets and make-vs-buy](#firms-markets-and-make-vs-buy)
5. [Network economics](#network-economics)
6. [Information and incentives](#information-and-incentives)
7. [Paradoxes and dynamics](#paradoxes-and-dynamics)
8. [Competitive strategy and innovation](#competitive-strategy-and-innovation)
9. [Classic strategy frameworks (quick table)](#classic-strategy-frameworks-quick-table)

---

## Costs and decisions

### Opportunity cost
- **Statement:** The cost of a choice is the value of the best alternative forgone.
- **Origin:** Friedrich von Wieser, 1914 (concept earlier: Bastiat, 1850).
- **In practice:** Every feature built is another not built; every senior engineer in meetings is not coding or mentoring. Make the alternative explicit in prioritisation.
- **Example:** Building an in-house auth system costs not just engineering time but the revenue features those engineers could have shipped.
- **Link:** https://en.wikipedia.org/wiki/Opportunity_cost

### Sunk cost
- **Statement:** Costs already incurred that cannot be recovered; rational decisions should ignore them and consider only future costs and benefits.
- **Origin:** Standard economics; the psychological "sunk cost fallacy" is in [cognitive-biases.md](cognitive-biases.md).
- **In practice:** Decide on continuing a project by asking "knowing what we know now, would we start it today?"
- **Example:** A €2M platform project with €3M remaining should be compared only on the €3M vs alternatives.
- **Link:** https://en.wikipedia.org/wiki/Sunk_cost

### Marginal cost and marginal thinking
- **Statement:** The cost of producing one more unit; rational decisions compare marginal benefit to marginal cost.
- **Origin:** Marginal revolution — Jevons, Menger, Walras, early 1870s; Alfred Marshall, 1890.
- **In practice:** Software has near-zero marginal cost of reproduction but non-zero marginal cost of serving (compute, support). Price and architect with marginal cost in mind (e.g., per-request cost in serverless or LLM inference).
- **Example:** Each additional 9 of availability has escalating marginal cost; stop where marginal benefit falls below it.
- **Link:** https://en.wikipedia.org/wiki/Marginal_cost

### Zero marginal cost
- **Statement:** When the cost of producing an additional unit approaches zero, abundance changes business models (free tiers, bundling, aggregation).
- **Origin:** Information economics (Shapiro & Varian, *Information Rules*, 1998); Jeremy Rifkin, 2014.
- **In practice:** Explains freemium, open source economics and why differentiation shifts to network effects, data and services.
- **Example:** Distributing a mobile app to one more user costs almost nothing; supporting them does not.
- **Link:** https://en.wikipedia.org/wiki/Information_good

### Diminishing returns
- **Statement:** Adding more of one input while holding others fixed eventually yields smaller increases in output.
- **Origin:** Turgot, 1767; David Ricardo and Thomas Malthus, 1815.
- **In practice:** More engineers on one codebase, more test coverage beyond the critical paths, more optimisation passes — each yields less. Find the knee of the curve.
- **Example:** Going from 70% to 80% coverage is cheap; from 95% to 100% is expensive for little value.
- **Link:** https://en.wikipedia.org/wiki/Diminishing_returns

### Time value of money and NPV
- **Statement:** Money now is worth more than the same amount later; net present value discounts future cash flows to compare investments.
- **Origin:** Martín de Azpilcueta, 16th century; Irving Fisher, *The Theory of Interest*, 1930.
- **In practice:** Value delivered earlier is worth more (supports incremental delivery); compare platform investments by discounted value, not raw totals.
- **Example:** A feature delivering €100k/year starting in month 2 beats one delivering €120k/year starting in month 12 over a 2-year horizon.
- **Link:** https://en.wikipedia.org/wiki/Net_present_value

### Total cost of ownership
- **Statement:** The full cost of an asset over its life: acquisition, operation, maintenance, training, migration and exit.
- **Origin:** Gartner popularised it for IT, 1987.
- **In practice:** "Free" open source and "cheap" self-hosting often lose to managed services once ops, on-call and upgrades are counted; include exit costs (lock-in).
- **Example:** Self-hosted Kafka: zero licence cost, two FTEs of operations.
- **Link:** https://en.wikipedia.org/wiki/Total_cost_of_ownership

### Technical debt
- **Statement:** The implied future cost of rework caused by choosing an expedient solution now; like financial debt, it accrues "interest" in slower change.
- **Origin:** Ward Cunningham, OOPSLA experience report, 1992. Martin Fowler's technical debt quadrant (reckless/prudent × deliberate/inadvertent), 2009.
- **In practice:** Debt is a tool when taken deliberately with a repayment plan; track it, quantify the interest (lead-time impact, incident load), and repay the high-interest parts first.
- **Example:** A quick schema hack for a launch, repaid in the next quarter.
- **Link:** https://en.wikipedia.org/wiki/Technical_debt

---

## Prioritisation economics

### Cost of Delay
- **Statement:** The economic impact of delaying delivery of something by a unit of time — combining value and urgency.
- **Origin:** Preston Smith and Donald Reinertsen, *Developing Products in Half the Time*, 1991; Reinertsen, *Principles of Product Development Flow*, 2009; Joshua Arnold's work on urgency profiles.
- **In practice:** Quantify (even roughly) €/week of delay; urgency profiles include standard, fixed-date, expedite and intangible. Turns priority debates into economics. See [../02-product-management/README.md](../02-product-management/README.md).
- **Example:** A compliance feature with a fixed regulatory date has low CoD until close to the date, then very high.
- **Link:** https://en.wikipedia.org/wiki/Cost_of_delay

### CD3 and WSJF
- **Statement:** Cost of Delay Divided by Duration (CD3) prioritises the work with the highest CoD per unit of time first; SAFe's Weighted Shortest Job First uses relative scores (user-business value + time criticality + risk reduction/opportunity enablement) ÷ job size.
- **Origin:** Reinertsen (WSJF scheduling, 2009); Joshua Arnold (CD3); SAFe (WSJF variant, ~2011).
- **In practice:** Favours small, valuable items; mathematically minimises total cost of delay for a queue of independent items. Relative-score WSJF is easy to game; use real numbers where possible.
- **Example:** A: CoD €10k/week, 2 weeks (CD3 = 5k); B: €30k/week, 10 weeks (CD3 = 3k) → do A first.
- **Link:** https://en.wikipedia.org/wiki/Cost_of_delay

### Real options in product development
- **Statement:** Treat investments as options: small investments buy information and the right to make bigger commitments later.
- **Origin:** Stewart Myers, 1977; Chris Matts & Olav Maassen, *Commitment*, 2013 ("options have value, options expire, never commit early unless you know why").
- **In practice:** Spikes, prototypes and staged funding; delay commitment to the last responsible moment.
- **Example:** A two-week proof of concept before committing to a vendor.
- **Link:** https://en.wikipedia.org/wiki/Real_options_valuation

### Kano model
- **Statement:** Features fall into categories by their effect on satisfaction: must-be (basic), one-dimensional (performance), attractive (delighters), indifferent and reverse; delighters decay into must-bes over time.
- **Origin:** Noriaki Kano, 1984.
- **In practice:** Reliability and security are must-bes (absence angers, presence doesn't delight); invest to the threshold then shift to performance/delight features.
- **Example:** Two-factor authentication went from delighter to expectation.
- **Link:** https://en.wikipedia.org/wiki/Kano_model

---

## Scale and production

### Comparative advantage
- **Statement:** Parties gain from trade by specialising in what they produce at the lowest opportunity cost, even if one is better at everything (absolute advantage).
- **Origin:** David Ricardo, *On the Principles of Political Economy and Taxation*, 1817 (Robert Torrens, 1815).
- **In practice:** A senior engineer who is better at both architecture and ops should still delegate ops if their opportunity cost is higher; teams should specialise where their relative advantage lies; justifies managed services.
- **Example:** A principal engineer stops doing release coordination even though they're best at it.
- **Link:** https://en.wikipedia.org/wiki/Comparative_advantage

### Economies of scale
- **Statement:** Average cost per unit falls as output grows, because fixed costs are spread and efficiencies emerge.
- **Origin:** Adam Smith (division of labour, 1776); Alfred Marshall, 1890.
- **In practice:** Shared platforms, cloud providers and central data teams exploit scale; justify them by number of consumers.
- **Example:** A single observability platform serving 50 teams vs 50 bespoke setups.
- **Link:** https://en.wikipedia.org/wiki/Economies_of_scale

### Diseconomies of scale
- **Statement:** Beyond some size, unit costs rise due to coordination overhead, bureaucracy and communication complexity.
- **Origin:** Economics of the firm; related to Brooks's and Ringelmann's observations.
- **In practice:** Large monolithic teams and central platforms that become bottlenecks; split when coordination costs exceed scale benefits.
- **Example:** A central "architecture board" for 300 teams becomes the constraint.
- **Link:** https://en.wikipedia.org/wiki/Diseconomies_of_scale

### Economies of scope
- **Statement:** Producing a variety of products together is cheaper than producing each separately, thanks to shared inputs.
- **Origin:** John Panzar and Robert Willig, 1975/1981.
- **In practice:** Shared design systems, data platforms and identity services across products; bundles.
- **Example:** One customer data platform feeding marketing, support and product analytics.
- **Link:** https://en.wikipedia.org/wiki/Economies_of_scope

### Experience curve (Wright's law)
- **Statement:** Unit cost falls by a constant percentage each time cumulative production doubles.
- **Origin:** Theodore Wright, aircraft manufacturing, 1936; BCG's experience curve, 1960s.
- **In practice:** Better than Moore's law at predicting technology costs (solar, batteries, possibly AI compute). Repeated practice (deployments, migrations) gets cheaper — do it often.
- **Example:** The 20th service migration costs a fraction of the first.
- **Link:** https://en.wikipedia.org/wiki/Experience_curve_effects

### Baumol's cost disease
- **Statement:** Wages rise in sectors with little productivity growth because they must compete with high-productivity sectors, so labour-intensive services become relatively more expensive.
- **Origin:** William Baumol and William Bowen, 1965–1966 (performing arts).
- **In practice:** Human-intensive work (code review, consulting, support, healthcare) gets relatively more expensive; automation pressure concentrates there. Explains rising engineering salaries in all industries.
- **Example:** A string quartet needs the same four musicians as in 1800; their wages rose anyway.
- **Link:** https://en.wikipedia.org/wiki/Baumol%27s_cost_disease

---

## Firms, markets and make-vs-buy

### Transaction cost economics (Coase)
- **Statement:** Firms exist because using the market has costs (search, negotiation, contracting, enforcement); activities are organised inside the firm when internal coordination is cheaper than market transactions.
- **Origin:** Ronald Coase, "The Nature of the Firm", 1937 (Nobel 1991); developed by Oliver Williamson (asset specificity, Nobel 2009).
- **In practice:** APIs, cloud marketplaces and SaaS reduce transaction costs, pushing more capabilities to "buy". High asset specificity and core differentiation favour "build".
- **Example:** Payment processing moved from in-house to Stripe-like providers as integration costs fell.
- **Link:** https://en.wikipedia.org/wiki/The_Nature_of_the_Firm

### Make-or-buy decision
- **Statement:** Decide whether to produce in-house or purchase externally, weighing cost, strategic importance, capability, risk and control.
- **Origin:** Operations and procurement practice; grounded in Coase and Williamson.
- **In practice:** Build what differentiates (Wardley genesis/custom); buy products; rent commodities. Include TCO, lock-in and exit costs. See [../11-enterprise-architecture-business/README.md](../11-enterprise-architecture-business/README.md).
- **Example:** Buy CRM; build the pricing engine that is your competitive edge.
- **Link:** https://en.wikipedia.org/wiki/Outsourcing

### Coase theorem
- **Statement:** If property rights are well-defined and transaction costs are low, parties will bargain to an efficient outcome regardless of the initial allocation of rights.
- **Origin:** Ronald Coase, "The Problem of Social Cost", 1960 (named by George Stigler).
- **In practice:** Clear ownership of services, data and budgets plus low negotiation costs (internal APIs, chargeback) enable efficient trade-offs between teams.
- **Example:** Clear data ownership lets teams negotiate access instead of escalating.
- **Link:** https://en.wikipedia.org/wiki/Coase_theorem

### Porter's five forces
- **Statement:** Industry profitability is shaped by five forces: rivalry among competitors, threat of new entrants, threat of substitutes, bargaining power of buyers and bargaining power of suppliers.
- **Origin:** Michael E. Porter, *Harvard Business Review*, 1979; *Competitive Strategy*, 1980.
- **In practice:** Assess vendor power (cloud lock-in), substitutes (open source, AI), and entrants; detailed in [../11-enterprise-architecture-business/README.md](../11-enterprise-architecture-business/README.md).
- **Example:** A sole-supplier database vendor has strong supplier power in licence renewals.
- **Link:** https://en.wikipedia.org/wiki/Porter%27s_five_forces_analysis

### Value chain
- **Statement:** A firm is a chain of primary activities (inbound logistics, operations, outbound, marketing & sales, service) and support activities (infrastructure, HR, technology, procurement); advantage comes from how they're configured.
- **Origin:** Michael Porter, *Competitive Advantage*, 1985.
- **In practice:** Map IT capabilities to value-chain activities to prioritise investment.
- **Example:** Investing in service (field-technician app) where the firm differentiates.
- **Link:** https://en.wikipedia.org/wiki/Value_chain

---

## Network economics

### Network effect
- **Statement:** A product's value to each user increases as more users join (direct), or as complementary products/sides grow (indirect).
- **Origin:** Theodore Vail, Bell Telephone, 1908; formalised by Jeffrey Rohlfs, 1974; Katz & Shapiro, 1985.
- **In practice:** Platforms, marketplaces, developer ecosystems and internal platforms (more users → more contributions → more value). Strong moat but hard cold-start.
- **Example:** A messaging app is useless with one user and indispensable when everyone you know uses it.
- **Link:** https://en.wikipedia.org/wiki/Network_effect

### Sarnoff's law
- **Statement:** The value of a broadcast network is proportional to the number of viewers (n).
- **Origin:** David Sarnoff, RCA/NBC, early broadcast era.
- **In practice:** One-to-many media (newsletters, dashboards, status pages) scale linearly — no interaction effects.
- **Example:** A company newsletter's reach grows linearly with subscribers.
- **Link:** https://en.wikipedia.org/wiki/David_Sarnoff

### Metcalfe's law
- **Statement:** The value of a telecommunications network is proportional to the square of the number of connected users (n²).
- **Origin:** Robert Metcalfe, circa 1980 (Ethernet sales pitch); named by George Gilder, 1993.
- **In practice:** Overstates value for large networks because not all connections are equally valuable; Odlyzko and Tilly proposed n·log(n) (2005–2006). Still explains winner-take-most dynamics.
- **Example:** Fax machines: one is worthless, two make one connection, five make ten.
- **Link:** https://en.wikipedia.org/wiki/Metcalfe%27s_law

### Odlyzko's law
- **Statement:** Network value grows roughly as n·log(n), since most potential connections are of little value (Zipf-like).
- **Origin:** Andrew Odlyzko and Benjamin Tilly, 2005; Briscoe, Odlyzko & Tilly, *IEEE Spectrum*, 2006.
- **In practice:** Network effects are real but weaker than n²; acquisitions justified by Metcalfe's math are often overvalued.
- **Example:** Merging two networks of equal size yields far less than double the combined value predicted by n².
- **Link:** https://en.wikipedia.org/wiki/Metcalfe%27s_law

### Reed's law
- **Statement:** The utility of networks that support group formation scales exponentially (2ⁿ), because the number of possible subgroups grows exponentially.
- **Origin:** David P. Reed, 1999.
- **In practice:** Group-forming platforms (communities, Slack workspaces, GitHub orgs) can have stronger network effects than pairwise ones; the theoretical figure is an upper bound.
- **Example:** A collaboration tool where teams form channels and groups.
- **Link:** https://en.wikipedia.org/wiki/Reed%27s_law

### Two-sided markets
- **Statement:** Platforms serve two (or more) distinct user groups that provide each other network benefits; pricing often subsidises one side.
- **Origin:** Jean-Charles Rochet and Jean Tirole, 2003 (Tirole, Nobel 2014).
- **In practice:** App stores, marketplaces, payment networks, internal developer platforms (producers and consumers of APIs). Solve the chicken-and-egg problem by seeding one side.
- **Example:** Ride-hailing subsidises riders early to attract drivers, and vice versa.
- **Link:** https://en.wikipedia.org/wiki/Two-sided_market

### Switching costs and lock-in
- **Statement:** Costs a customer incurs to change suppliers (financial, procedural, relational, data migration) — creating lock-in.
- **Origin:** Paul Klemperer, 1987; Shapiro & Varian, *Information Rules*, 1998.
- **In practice:** Evaluate exit cost of cloud services, proprietary formats and data gravity; use open standards and abstraction at strategic boundaries; as a vendor, high switching costs are a moat.
- **Example:** Egress fees and proprietary APIs make leaving a cloud provider expensive.
- **Link:** https://en.wikipedia.org/wiki/Vendor_lock-in

### Economic moat
- **Statement:** A durable competitive advantage that protects profits: network effects, switching costs, cost advantages, intangible assets (brands, patents), efficient scale.
- **Origin:** Warren Buffett, 1990s; Morningstar's moat framework.
- **In practice:** Ask which moat a product or platform investment actually builds. Hamilton Helmer's *7 Powers* (2016) extends this (scale economies, network economies, counter-positioning, switching costs, branding, cornered resource, process power).
- **Example:** Proprietary data accumulated through product use.
- **Link:** https://en.wikipedia.org/wiki/Economic_moat

### Commoditise your complement
- **Statement:** Demand for a product rises when the price of its complements falls; companies therefore try to commoditise their complements.
- **Origin:** Joel Spolsky, "Strategy Letter V", 2002, building on standard complement economics.
- **In practice:** Explains why companies open-source infrastructure (to sell cloud, devices or ads) and why cloud providers offer managed open-source.
- **Example:** IBM's open PC architecture commoditised hardware, which favoured Microsoft's software.
- **Link:** https://www.joelonsoftware.com/2002/06/12/strategy-letter-v/

### Aggregation theory
- **Statement:** On the internet, value accrues to aggregators that own the user relationship and commoditise suppliers, enabled by zero distribution and transaction costs.
- **Origin:** Ben Thompson, *Stratechery*, 2015.
- **In practice:** Explains dominance of search, app stores and marketplaces; suppliers to aggregators should diversify channels.
- **Example:** Hotels depending on booking platforms for customer acquisition.
- **Link:** https://stratechery.com/aggregation-theory/

---

## Information and incentives

### Information asymmetry
- **Statement:** One party in a transaction has more or better information than the other, leading to market inefficiencies.
- **Origin:** George Akerlof, Michael Spence, Joseph Stiglitz (Nobel 2001).
- **In practice:** Vendors know more than buyers; teams know more than managers about estimates; contractors know more than clients. Reduce via transparency, references, proofs of concept and shared metrics.
- **Example:** A vendor's SLA hides that failover is manual.
- **Link:** https://en.wikipedia.org/wiki/Information_asymmetry

### Market for lemons
- **Statement:** When buyers can't distinguish quality, they pay average prices, driving high-quality sellers out until only "lemons" remain.
- **Origin:** George Akerlof, "The Market for 'Lemons'", 1970.
- **In practice:** Hiring markets, consulting and open-source packages where quality is hard to judge; signalling (certifications, track records) and warranties counteract it.
- **Example:** Fixed-price tenders won by the lowest bidder deliver lowest quality.
- **Link:** https://en.wikipedia.org/wiki/The_Market_for_Lemons

### Moral hazard
- **Statement:** A party insulated from risk behaves differently than if fully exposed to it.
- **Origin:** Insurance industry, 17th–19th century; formalised by Kenneth Arrow, 1963.
- **In practice:** Teams that don't pay for their cloud usage or don't carry the pager take more risk; apply cost showback and "you build it, you run it".
- **Example:** A team over-provisions because a central budget pays.
- **Link:** https://en.wikipedia.org/wiki/Moral_hazard

### Adverse selection
- **Statement:** Information asymmetry before a transaction leads to selecting undesirable counterparties (e.g., high-risk customers buying insurance).
- **Origin:** Insurance economics; Akerlof, 1970; Rothschild & Stiglitz, 1976.
- **In practice:** Unlimited free tiers attract abusive users; generous unlimited plans attract the heaviest users.
- **Example:** An "unlimited storage" plan attracts users who store terabytes.
- **Link:** https://en.wikipedia.org/wiki/Adverse_selection

### Externality
- **Statement:** A cost or benefit that affects third parties who did not choose to incur it.
- **Origin:** Arthur Pigou, *The Economics of Welfare*, 1920.
- **In practice:** A team's noisy logging inflates the shared observability bill; a fragile shared library imposes costs on all consumers. Internalise via chargeback, quotas, ownership.
- **Example:** One service's retry storm degrades shared infrastructure for everyone.
- **Link:** https://en.wikipedia.org/wiki/Externality

### Free-rider problem
- **Statement:** People benefit from a shared resource without paying for it, leading to under-provision.
- **Origin:** Public goods theory; Mancur Olson, *The Logic of Collective Action*, 1965.
- **In practice:** Open-source maintenance, shared internal libraries and documentation are under-funded; fund maintainers and allocate explicit ownership.
- **Example:** Critical open-source projects maintained by one unpaid volunteer.
- **Link:** https://en.wikipedia.org/wiki/Free-rider_problem

### Gresham's law
- **Statement:** "Bad money drives out good": when two currencies have the same face value, the undervalued one is hoarded and the overvalued circulates.
- **Origin:** Sir Thomas Gresham, 16th century (named by Henry Dunning Macleod, 1858).
- **In practice:** Used metaphorically: low-quality content, meetings or processes crowd out high-quality ones when they're treated as equivalent.
- **Example:** Quick, shallow tickets crowd out deep work when everything counts as "one item".
- **Link:** https://en.wikipedia.org/wiki/Gresham%27s_law

---

## Paradoxes and dynamics

### Jevons paradox
- **Statement:** Improvements in the efficiency of using a resource can increase its total consumption, because lower effective cost increases demand.
- **Origin:** William Stanley Jevons, *The Coal Question*, 1865.
- **In practice:** Cheaper compute, storage, or AI inference leads to more usage and possibly higher total spend; faster CI leads to more pipeline runs. Budget for rebound effects.
- **Example:** Cheaper LLM tokens lead to many more AI features, raising total inference cost.
- **Link:** https://en.wikipedia.org/wiki/Jevons_paradox

### Price elasticity of demand
- **Statement:** The percentage change in quantity demanded per percentage change in price.
- **Origin:** Alfred Marshall, 1890.
- **In practice:** Elastic demand (cloud compute) responds strongly to price cuts (cf. Jevons); inelastic demand (critical compliance software) does not. Inform pricing and internal chargeback.
- **Example:** A price cut on serverless increases function invocations substantially.
- **Link:** https://en.wikipedia.org/wiki/Price_elasticity_of_demand

### Pareto efficiency
- **Statement:** An allocation where no one can be made better off without making someone else worse off.
- **Origin:** Vilfredo Pareto, 1906.
- **In practice:** Architectural trade-off analysis (latency vs cost vs consistency) seeks the Pareto frontier; then choose a point on it by priorities.
- **Example:** Comparing instance types on a cost/performance frontier and discarding dominated options.
- **Link:** https://en.wikipedia.org/wiki/Pareto_efficiency

### Hotelling's law
- **Statement:** In many markets, competitors converge on similar products and positions (minimum differentiation).
- **Origin:** Harold Hotelling, "Stability in Competition", 1929.
- **In practice:** Products in a category become look-alikes; deliberate differentiation (counter-positioning) is a strategic choice.
- **Example:** Most project management tools converge on the same board/list/timeline views.
- **Link:** https://en.wikipedia.org/wiki/Hotelling%27s_law

---

## Competitive strategy and innovation

### Disruptive innovation
- **Statement:** Entrants with simpler, cheaper offerings target overlooked or low-end segments, improve, and eventually displace incumbents who are optimising for their best customers.
- **Origin:** Clayton Christensen, *The Innovator's Dilemma*, 1997 (with Joseph Bower, *HBR*, 1995).
- **In practice:** Incumbents rationally ignore low-margin markets; protect autonomous units to pursue disruptive bets. The term is overused — not every new entrant is disruptive.
- **Example:** Cloud computing initially served startups that enterprise hardware vendors ignored.
- **Link:** https://en.wikipedia.org/wiki/Disruptive_innovation

### Diffusion of innovations
- **Statement:** Adoption follows an S-curve across innovators (2.5%), early adopters (13.5%), early majority (34%), late majority (34%) and laggards (16%).
- **Origin:** Everett Rogers, *Diffusion of Innovations*, 1962.
- **In practice:** Internal platform and practice adoption follows the same pattern — target innovators and early adopters first, then use their success stories.
- **Example:** A new CI platform piloted by two enthusiastic teams before broad rollout.
- **Link:** https://en.wikipedia.org/wiki/Diffusion_of_innovations

### Crossing the chasm
- **Statement:** A gap exists between early adopters (visionaries) and the early majority (pragmatists), who need complete solutions and references.
- **Origin:** Geoffrey Moore, *Crossing the Chasm*, 1991.
- **In practice:** Focus on one beachhead segment with a "whole product" (docs, integrations, support) before expanding.
- **Example:** An internal platform succeeds with enthusiasts but stalls until it offers migration tooling and support.
- **Link:** https://en.wikipedia.org/wiki/Crossing_the_Chasm

### Gartner hype cycle
- **Statement:** A model of technology expectations: innovation trigger, peak of inflated expectations, trough of disillusionment, slope of enlightenment, plateau of productivity.
- **Origin:** Jackie Fenn, Gartner, 1995. Empirical validity is debated; many technologies never follow the full cycle.
- **In practice:** Use it as a reminder to separate hype from proven value when adopting new technology (cf. bandwagon effect).
- **Example:** Blockchain enterprise projects peaking ~2018 then largely disappearing.
- **Link:** https://en.wikipedia.org/wiki/Gartner_hype_cycle

### Red ocean vs blue ocean
- **Statement:** Rather than competing in crowded markets (red oceans), create uncontested market space (blue oceans) by redefining value and cost.
- **Origin:** W. Chan Kim and Renée Mauborgne, *Blue Ocean Strategy*, 2005.
- **In practice:** Value innovation: eliminate, reduce, raise, create (ERRC grid) applied to product features.
- **Example:** Cirque du Soleil combining circus and theatre.
- **Link:** https://en.wikipedia.org/wiki/Blue_Ocean_Strategy

---

## Classic strategy frameworks (quick table)

Detailed treatment: [../11-enterprise-architecture-business/README.md](../11-enterprise-architecture-business/README.md) and [../02-product-management/README.md](../02-product-management/README.md).

| Framework | Core idea | Origin | Link |
|---|---|---|---|
| **SWOT analysis** | Strengths, weaknesses (internal), opportunities, threats (external) | Albert Humphrey et al., SRI, 1960s | https://en.wikipedia.org/wiki/SWOT_analysis |
| **BCG growth–share matrix** | Portfolio by market growth vs relative share: stars, cash cows, question marks, dogs | Bruce Henderson, BCG, 1970 | https://en.wikipedia.org/wiki/Growth%E2%80%93share_matrix |
| **Ansoff matrix** | Growth via market penetration, market development, product development, diversification | Igor Ansoff, 1957 | https://en.wikipedia.org/wiki/Ansoff_matrix |
| **Core competency** | Compete on a few hard-to-imitate capabilities that span products | Prahalad & Hamel, HBR, 1990 | https://en.wikipedia.org/wiki/Core_competency |
| **Generic strategies** | Cost leadership, differentiation or focus | Michael Porter, 1980 | https://en.wikipedia.org/wiki/Porter%27s_generic_strategies |
| **Business Model Canvas** | Nine building blocks of a business model | Alexander Osterwalder, 2005/2010 | https://en.wikipedia.org/wiki/Business_Model_Canvas |
| **Good strategy / bad strategy** | Kernel: diagnosis, guiding policy, coherent actions | Richard Rumelt, 2011 | https://en.wikipedia.org/wiki/Richard_Rumelt |
| **Innovator's dilemma** | Doing the right things for current customers causes incumbents to miss disruption | Clayton Christensen, 1997 | https://en.wikipedia.org/wiki/The_Innovator%27s_Dilemma |
| **Three horizons** | Balance core (H1), emerging (H2), and future options (H3) | Baghai, Coley & White (McKinsey), 1999 | https://en.wikipedia.org/wiki/Strategic_planning |

---

## References
- Reinertsen, D. *The Principles of Product Development Flow* (2009) — https://en.wikipedia.org/wiki/Cost_of_delay
- Coase, R. "The Nature of the Firm" (1937) — https://en.wikipedia.org/wiki/The_Nature_of_the_Firm
- Shapiro, C. & Varian, H. *Information Rules* (1998) — https://en.wikipedia.org/wiki/Information_Rules
- Porter, M. *Competitive Strategy* (1980), *Competitive Advantage* (1985) — https://en.wikipedia.org/wiki/Porter%27s_five_forces_analysis
- Christensen, C. *The Innovator's Dilemma* (1997) — https://en.wikipedia.org/wiki/The_Innovator%27s_Dilemma
- Helmer, H. *7 Powers* (2016)
- Spolsky, J. "Strategy Letter V" (2002) — https://www.joelonsoftware.com/2002/06/12/strategy-letter-v/
- Thompson, B. "Aggregation Theory" (2015) — https://stratechery.com/aggregation-theory/
- Related sections: [../02-product-management/README.md](../02-product-management/README.md), [../06-cloud-architecture/README.md](../06-cloud-architecture/README.md), [../11-enterprise-architecture-business/README.md](../11-enterprise-architecture-business/README.md)
