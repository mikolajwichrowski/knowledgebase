# Product Operating Model, Roles, Internal & B2B Product Management, Pricing & GTM

> **TL;DR** — The product operating model replaces "projects and feature requests" with durable, empowered product teams that own outcomes. The core unit is the **product trio** (PM/PO, product designer, tech lead). PM and PO are often the same person; PM emphasises market/discovery/strategy, PO (Scrum) emphasises backlog accountability. Internal tools and platforms are products too: users, adoption, value, roadmaps. B2B/enterprise adds buyers ≠ users, long sales cycles, customisation pressure, and contracts. Price on value, in tiers with clear fences; go-to-market needs positioning, channel, enablement and launch tiers.

---

## 1. Project model vs product operating model

| Dimension | Project / IT-delivery model | Product operating model |
|---|---|---|
| Funding | Per project, annual budget cycle | Per persistent team/product; capacity-based |
| Team | Assembled for project, disbanded after | Durable, cross-functional, long-lived |
| Success | On time, on budget, on scope | Outcomes: customer and business results |
| Requirements | From stakeholders/business, handed to IT | Discovered by the team with customers |
| Ownership after launch | Handover to "run"/maintenance | Team owns build and run ("you build it, you run it") |
| Planning | Big upfront plan, Gantt | Roadmap of problems; continuous discovery |
| Role of business | Customer of IT | Partner; stakeholders inform, teams decide solutions |
| Risk handling | Late (UAT at end) | Early (discovery tackles risks before build) |

Sources: Marty Cagan, *Transformed* (2024) — the "product model" with concepts in three dimensions: how you build (small, frequent, reliable releases; instrumentation; monitoring), how you solve problems (empowered teams, discovery, outcomes), how you decide which problems (product vision, strategy, insights). Mik Kersten, *Project to Product* (2018) — Flow Framework (flow items: features, defects, risks, debts; flow velocity, time, load, efficiency, distribution). Gartner/McKinsey reports on product-centric IT.

### Transformation signals

- Teams have **outcomes** (OKRs) rather than feature lists.
- PMs talk to customers weekly.
- Engineers participate in discovery.
- Releases are frequent and boring.
- Stakeholders bring **problems**, not solutions.
- Funding allocated to products/value streams, not projects.

---

## 2. Product team & product trio

| Role | Primary responsibility | Risk owned (Cagan) |
|---|---|---|
| **Product manager / product owner** | Value and viability; outcomes; prioritisation; stakeholder alignment; customer, data, business, industry knowledge | Value, viability |
| **Product designer** | Usability, user experience, interaction & visual design, prototyping, research | Usability |
| **Tech lead / engineers** | Feasibility, architecture, quality, technical opportunities ("enablers of innovation") | Feasibility |
| (Optional) data scientist/analyst, UX researcher, PMM, delivery manager / Scrum Master, QA | Specialist contributions | — |

**Product trio** (Torres): PM + designer + tech lead co-own discovery decisions; together they interview, map opportunities, generate solutions, test assumptions.

**Team topologies** (Skelton & Pais, 2019) relevant to product: stream-aligned teams (own a value stream/product), platform teams (internal product for other teams), enabling teams, complicated-subsystem teams. See [../09-leadership-teams-facilitation/README.md](../09-leadership-teams-facilitation/README.md).

---

## 3. PM vs PO (and other titles)

| | Product Manager | Product Owner (Scrum) |
|---|---|---|
| Origin | Procter & Gamble "brand men" (Neil McElroy memo, 1931); HP, Intuit, Microsoft tech PM | Scrum (Schwaber & Sutherland, 1995; Scrum Guide 2010+) |
| Defined as | Job title / profession | Accountability within a Scrum Team |
| Focus | Market, customers, strategy, discovery, business viability, GTM | Product Goal, Product Backlog ordering & transparency, maximising value of Increment |
| Horizon | Months–years | Sprint–quarters (but Scrum Guide intends PO to own product value fully) |
| Typical anti-pattern | PM writes specs and throws them over the wall | PO as "backlog administrator"/proxy taking orders from stakeholders |

**Practical rule**: one person should hold both where possible (Cagan: the PO is a role a strong PM plays in Scrum). In large orgs (SAFe): **Product Management** (program/ART level, features, market) vs **Product Owner** (team level, stories).

Other roles:

| Role | Focus |
|---|---|
| Group PM / Director / VP / CPO | Strategy, portfolio, coaching PMs |
| Technical PM / platform PM | APIs, developer experience, infrastructure as products |
| Data PM / AI PM | Data products, ML features, evaluation, data governance |
| Growth PM | Funnel optimisation, experiments, monetisation |
| Product Marketing Manager (PMM) | Positioning, messaging, launches, sales enablement, competitive intel |
| Product Ops | Tooling, insights repositories, processes, data for PMs |
| Business analyst | Detailed requirements, process analysis (often in enterprise/ERP) |
| Project / programme manager | Coordination, schedules, budgets, dependencies (see [../03-project-management/README.md](../03-project-management/README.md)) |
| Delivery manager / Scrum Master | Flow, team process, impediments |

### PM competencies (useful self-assessment)

| Area | Examples |
|---|---|
| Customer insight | Interviewing, research synthesis, JTBD |
| Data | Analytics, SQL basics, experimentation statistics |
| Business | Unit economics, pricing, market, sales/GTM, legal constraints |
| Domain / industry | Regulations, competitors, trends |
| Product execution | Backlog, specs, release, cross-team coordination |
| Strategy | Vision, OKRs, prioritisation, saying no |
| Leadership & influence | Stakeholder management, storytelling, facilitation, decision making |
| Technology | Architecture literacy, AI/ML literacy, APIs, technical debt trade-offs |

---

## 4. Stakeholder management for PMs

- Map stakeholders (power/interest — see [requirements-engineering.md](requirements-engineering.md)).
- Meet key stakeholders 1:1 regularly; understand their constraints and goals.
- **Pre-wire** decisions: no surprises in big meetings.
- Share evidence (interview clips, data) not opinions.
- Transparent prioritisation: show the framework and the trade-offs.
- Disagree and commit; document decisions (decision log, DACI: Driver, Approver, Contributors, Informed).
- Regular demos/sprint reviews as the main feedback channel.

---

## 5. Internal product management (internal tools, ERP, portals, platforms)

Internal products serve employees, partners, or other engineering teams. They fail when treated as one-off projects with no owner after go-live.

### What changes vs external products

| Aspect | External product | Internal product |
|---|---|---|
| Users | Choose to buy/use | Often mandated — adoption ≠ value |
| Revenue | Direct | Indirect: cost savings, risk reduction, speed, enabling revenue |
| Competition | Market alternatives | Spreadsheets, email, shadow IT, workarounds, SaaS bought by departments |
| Feedback | Analytics, churn | Complaints, escalations via management, workarounds |
| Stakeholders | Customers, market | Department heads, process owners, finance, IT, works council |
| Research access | Recruiting effort | Easy — users are colleagues (use it!) |
| Funding | Revenue-driven | Budget/allocation; must show business case |

### Practices

- Define **customers and value** explicitly: who uses it, what job, what business outcome (cost per order, cycle time, error rate).
- Treat **process owners** as key stakeholders but research **actual users**.
- Measure **voluntary** signals: workaround retirement, CES/SUS, task time, error rates, tickets.
- **Build vs buy vs configure**: core differentiating processes → build/customise; commodity processes (HR, finance ledger) → buy SaaS/standard ERP and adapt process. Use Wardley maps ([product-strategy.md](product-strategy.md)).
- **Platform as a product** (Team Topologies, Evan Bottcher "What I talk about when I talk about platforms", 2018): self-service, documentation, golden paths, SLAs, roadmap, developer experience research, optional adoption ("compelling, not mandatory").
- **Internal marketing**: release notes, demos, key-user networks, champions, training.
- **Total cost of ownership**: hosting, licences, support, maintenance, upgrades; sunset legacy tools explicitly.
- **Governance**: product council / steering group decides on priorities across departments (avoid every department having its own backlog).
- **Change management**: ADKAR (Prosci: Awareness, Desire, Knowledge, Ability, Reinforcement), Kotter's 8 steps — adoption is a product outcome.

### Internal product canvas (quick)

| Field | Example |
|---|---|
| Users & jobs | Customer service reps: answer order status questions within 1 call |
| Current alternatives | ERP screens (5 transactions), email to warehouse |
| Business outcome | Average handling time −30%; first-contact resolution +15 pp |
| Key metrics | AHT, FCR, % calls using the new screen |
| Stakeholders | Head of CS, IT ERP team, warehouse lead, works council |
| Constraints | SAP data, GDPR, existing telephony |
| Build/buy | Build thin UI on ERP APIs; buy telephony integration |

---

## 6. B2B / enterprise product management

| Characteristic | Implication |
|---|---|
| **Buyer ≠ user ≠ decision maker** (economic buyer, technical buyer/IT, champion, end users, procurement, legal) | Research and value proposition per role; usability still matters (renewals depend on usage) |
| Long sales cycles (3–18 months) | Product decisions affect pipeline; sales asks for roadmap commitments |
| Few, large customers | Risk of becoming a custom-development shop for the loudest customer |
| Contracts & SLAs | Uptime, support, data processing agreements (DPA), security questionnaires, SOC 2 / ISO 27001 |
| Integrations | ERP/CRM/IdP integrations often decide the deal |
| Admin & governance features | SSO/SAML/OIDC, SCIM, roles/permissions, audit logs, data residency |
| Configurability vs customisation | Configuration (supported settings) scales; customisation (per-customer code) doesn't |
| Implementation services | Onboarding projects; time-to-value matters for renewal |
| Renewals & expansion | NRR is the key business metric |

### Handling customer feature requests

1. Capture **request + problem + customer + revenue context** in a feedback/insights tool (not straight to backlog).
2. Look for patterns across customers (≥3 customers with the same problem).
3. Talk to the requester about the underlying job.
4. Decide: generalise into the product, configure, partner/integrate, or decline.
5. Close the loop with sales and the customer.

**"Special" requests** from big deals: evaluate as investments (does it serve the strategy/segment?), consider **customer development partners** (Cagan: 6–8 reference customers co-developing a solution that must work for all of them, not one).

### Enterprise "table stakes" checklist

- [ ] SSO (SAML 2.0 / OIDC), MFA, SCIM provisioning
- [ ] Role-based access control, account hierarchies, delegated admin
- [ ] Audit log & export
- [ ] Data export / APIs, webhooks
- [ ] Data residency (EU), DPA, GDPR tooling
- [ ] Security certifications (ISO 27001, SOC 2), pen test reports
- [ ] SLA and status page
- [ ] Sandbox / test environments
- [ ] Accessibility (WCAG 2.1/2.2 AA; EAA in EU)
- [ ] Localisation (languages, currencies, VAT)

---

## 7. Pricing basics

### Pricing strategies

| Strategy | Basis | When |
|---|---|---|
| Cost-plus | Cost + margin | Commodities, services; leaves money on the table for software |
| Competitor-based | Market prices | Crowded mature markets |
| **Value-based** | Customer's perceived/economic value | Recommended for software/B2B; requires understanding value drivers |
| Penetration | Low price to gain share | Network effects, land-grab |
| Skimming | High price early, lower later | Innovative, few competitors |
| Freemium | Free tier + paid upgrades | Low marginal cost, viral/self-serve products |
| Free trial | Time-limited full access | Products with fast time-to-value |

### Value-based pricing — economic value estimation

```
Economic value to customer = Reference value (next-best alternative price) + Differentiation value (positive − negative)
Price is set between cost floor and economic value; capture typically 10–30% of value created (rule of thumb)
```

Example: portal saves a customer 10 hours/week of ordering admin at €40/h ≈ €20k/year plus fewer errors (€5k). Alternative (EDI setup) costs €8k/year. Price somewhere in €6–12k/year is defensible.

### Pricing metric (value metric)

The unit you charge for. Should scale with value received, be easy to understand, predictable. Examples: per seat (Slack), per usage (AWS, tokens), per transaction (Stripe 1.5–2.9% + fixed fee), per active user, per order/document processed, per site/location, tiered by feature.

AI products: costs scale with usage (inference) → usage-based or credit-based pricing, or hybrid (seat + usage), with guardrails on margin.

### Tiers (good–better–best)

| | Starter | Professional | Enterprise |
|---|---|---|---|
| Target | Small teams | Mid-market | Large orgs |
| Value metric | Up to 5 users | Per user | Custom / volume |
| Key features | Core ordering | + reorder templates, analytics | + SSO, SCIM, audit log, ERP connectors, SLA |
| Support | Email | Business hours chat | Dedicated CSM, 24/7 |
| Price anchor | €X | ~2–3× Starter | "Contact us" |

**Tiering principles**: fences based on customer segments' willingness to pay (enterprise features like SSO, audit, compliance are classic fences — "SSO tax"); 3–4 tiers; anchor with the highest tier; decoy effect; annual discount (~15–20%).

### Willingness-to-pay research

| Method | Notes |
|---|---|
| **Van Westendorp Price Sensitivity Meter** (1976) | 4 questions: too cheap, cheap/bargain, expensive, too expensive → acceptable price range |
| **Gabor–Granger** | Ask purchase likelihood at successive price points → demand curve |
| **Conjoint analysis** | Trade-offs between features and price; statistically rigorous |
| **Pricing pages A/B / smoke tests** | Real behaviour |
| Sales win/loss analysis | B2B reality check |

Books: Madhavan Ramanujam & Georg Tacke, *Monetizing Innovation* (2016) — design the product around the price ("talk about willingness to pay early"); Patrick Campbell / ProfitWell (Paddle) research on SaaS pricing.

---

## 8. Go-to-market (GTM) basics

### GTM components

| Component | Questions |
|---|---|
| Target market & ICP (ideal customer profile) | Which segment first (beachhead)? |
| Positioning & messaging | Category, alternatives, differentiators, proof (April Dunford) |
| Pricing & packaging | See §7 |
| Channels | Direct sales, inside sales, self-serve/PLG, partners/resellers, marketplaces (Azure/AWS) |
| Sales motion | Product-led growth (PLG), sales-led, partner-led, hybrid |
| Enablement | Sales decks, demos, battle cards, FAQs, training, support docs |
| Launch plan | Tiered launches, timing, comms |
| Success metrics | Pipeline, win rate, activation, adoption, NRR |

### PLG vs sales-led

| | Product-led growth | Sales-led |
|---|---|---|
| Entry | Self-serve signup, free/trial | Demo, sales cycle |
| ACV | Low–mid | Mid–high |
| Key metric | PQLs (product-qualified leads), activation | SQLs, pipeline, win rate |
| Examples | Slack, Figma, Atlassian, Notion | SAP, Salesforce enterprise, Workday |
| Hybrid | "Product-led sales": PLG funnel + sales assist for large accounts | |

(OpenView Partners popularised "product-led growth", ~2016; Wes Bush, *Product-Led Growth*, 2019.)

### Launch tiers

| Tier | Example | Activities |
|---|---|---|
| Tier 1 — major | New product, major pricing change | Press, events, campaigns, full enablement, customer webinars |
| Tier 2 — significant | Major feature | Blog, email to customers, in-app announcement, sales briefing |
| Tier 3 — minor | Improvements | Release notes, changelog, in-app tooltip |

Internal tools GTM: key-user network, training sessions, intranet posts, office hours, quick reference cards, champions per site, measuring adoption per department.

---

## 9. PM rituals & artefacts (typical week)

| Cadence | Activity |
|---|---|
| Weekly | Customer interviews/touchpoints; trio sync on discovery; metrics review; refinement |
| Sprint (2 weeks) | Planning, review/demo with stakeholders, retrospective |
| Monthly | Roadmap review; stakeholder 1:1s; experiment review (learning cards) |
| Quarterly | OKR planning & scoring; strategy review; customer advisory board (B2B) |
| Annually | Vision refresh; budget/capacity allocation; portfolio review |

## 10. Common mistakes

- Product managers acting as project managers or backlog administrators only.
- Separating "the business" (decides) from IT (delivers) under a product label ("product theatre").
- Internal tools without a product owner after go-live.
- B2B: building one-off features for the biggest customer; roadmaps sold as contracts.
- Pricing set once, never revisited; pricing based on cost.
- Launches with no enablement; adoption treated as someone else's job.
- Treating AI features like deterministic features (no evaluation, no fallback, no cost model).

## Related sections

- Team structures, leadership, facilitation: [../09-leadership-teams-facilitation/README.md](../09-leadership-teams-facilitation/README.md)
- Project and programme management: [../03-project-management/README.md](../03-project-management/README.md)
- Enterprise architecture, capability-based planning, business models: [../11-enterprise-architecture-business/README.md](../11-enterprise-architecture-business/README.md)
- Platform engineering, cloud: [../06-cloud-architecture/README.md](../06-cloud-architecture/README.md)
- Data/AI products: [../07-data-ai-architecture/README.md](../07-data-ai-architecture/README.md)

## References

- SVPG — https://www.svpg.com/
- Project to Product / Flow Framework — https://flowframework.org/
- Team Topologies — https://teamtopologies.com/
- Product manager (Wikipedia) — https://en.wikipedia.org/wiki/Product_manager
- Product owner — Scrum Guide — https://scrumguides.org/
- Scrum.org — https://www.scrum.org/
- Martin Fowler — Platform engineering / What I talk about when I talk about platforms — https://martinfowler.com/articles/talk-about-platforms.html
- Pricing strategies (Wikipedia) — https://en.wikipedia.org/wiki/Pricing_strategies
- Value-based pricing (Wikipedia) — https://en.wikipedia.org/wiki/Value-based_pricing
- Van Westendorp's Price Sensitivity Meter (Wikipedia) — https://en.wikipedia.org/wiki/Van_Westendorp%27s_Price_Sensitivity_Meter
- Conjoint analysis (Wikipedia) — https://en.wikipedia.org/wiki/Conjoint_analysis
- Go-to-market strategy (Wikipedia) — https://en.wikipedia.org/wiki/Go-to-market_strategy
- Product-led growth (Wikipedia) — https://en.wikipedia.org/wiki/Product-led_growth
- Prosci ADKAR — https://www.prosci.com/methodology/adkar
- Kotter — 8 steps — https://www.kotterinc.com/
