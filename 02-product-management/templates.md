# Product Templates

> **TL;DR** — Copy-paste templates for the core product documents: product one-pager, opportunity assessment (Cagan's 10 questions), product brief, PRD, Amazon Working Backwards PR/FAQ, experiment/learning cards, release notes, decision log, and a stakeholder update. Keep documents short, problem-first, outcome-focused, and living (link from the backlog; update as you learn). The document is a tool for thinking and alignment, not a contract.

---

## Which document when?

| Document | Length | Stage | Audience | Purpose |
|---|---|---|---|---|
| **Opportunity assessment** | ½ page | Before discovery | Leadership, trio | Should we invest in exploring this problem? |
| **One-pager** | 1 page | Early discovery | Stakeholders, team | Align on problem, outcome, scope boundaries |
| **PR/FAQ (Working Backwards)** | 1 page PR + ≤ 5 pages FAQ | Before committing to build | Leadership, team | Force customer-centric clarity; test if the idea is compelling |
| **Product brief** | 1–3 pages | End of discovery | Team, design, eng | Bridge discovery → delivery; what we learned, what we'll build |
| **PRD** | 2–10 pages | Delivery (living) | Team, QA, stakeholders | Detailed requirements, scope, NFRs, release criteria |
| **Experiment card / learning card** | ¼ page | Discovery | Trio | Hypothesis-driven tests |
| **Release notes** | Short | Release | Users, support, sales | What changed and why it matters |
| **Decision record** | ½ page | Any | Team, future team | Capture decisions and rationale |

---

## 1. Opportunity assessment (Marty Cagan, *Inspired*)

Answer these ten questions before investing in a product opportunity:

1. **Exactly what problem will this solve?** (value proposition)
2. **For whom do we solve that problem?** (target market)
3. **How big is the opportunity?** (market size)
4. **What alternatives are out there?** (competitive landscape)
5. **Why are we best suited to pursue this?** (our differentiator)
6. **Why now?** (market window)
7. **How will we get this product to market?** (go-to-market strategy)
8. **How will we measure success / make money from this product?** (metrics / revenue strategy)
9. **What factors are critical to success?** (solution requirements)
10. **Given the above, what's the recommendation?** (go or no-go)

In *Inspired* 2nd ed. Cagan notes many teams only need four of these per opportunity: objective (which outcome?), key results (how to measure?), customer problem (what problem?), target market (for whom?).

**Filled example (short)**

| Q | Answer |
|---|---|
| Problem | 40% of B2B orders arrive as email/PDF and are re-typed by 6 FTE customer service staff; 3.5% error rate |
| For whom | Customer service team (users), B2B customers who email orders (beneficiaries) |
| Size | ~1,800 email orders/week; ~9,000 staff hours/year; error cost ≈ €120k/year |
| Alternatives | Manual entry (status quo), EDI (only for top-20 accounts), portal adoption, OCR vendors |
| Why us | Own ERP data for matching SKUs/prices; in-house data/AI capability |
| Why now | LLM extraction quality now sufficient; staff shortage in CS |
| GTM | Internal rollout to CS team; no change for customers |
| Success metric | % email orders auto-created with ≥98% line accuracy; handling time per order |
| Critical factors | SKU matching accuracy, ERP API, human-in-the-loop review UI, GDPR (email content) |
| Recommendation | Go for 6-week discovery: Wizard of Oz with 2 CS agents, offline evaluation on 500 historical emails |

---

## 2. Product one-pager

```markdown
# [Initiative name] — One-pager
Owner: [PM] · Trio: [PM, designer, tech lead] · Status: Exploring | Discovery | Delivery | Done
Last updated: YYYY-MM-DD

## Problem
What problem, for whom, how do we know (evidence: data, interviews, tickets)?

## Why it matters / why now
Link to strategy, OKR, North Star input. Cost of delay.

## Desired outcome
Metric, baseline → target, by when. Guardrails.

## Target users / segments
Primary, secondary. Out of scope.

## What we know / don't know
Key insights. Riskiest assumptions (value, usability, feasibility, viability).

## Possible solutions (options, not commitments)
Option A / B / C — trade-offs.

## Scope boundaries
In: ... Out (non-goals): ...

## Plan
Next discovery steps / experiments; decision date.

## Open questions / risks / dependencies
```

---

## 3. Amazon Working Backwards — PR/FAQ

Origin: Amazon (early 2000s; Ian McAllister's 2012 description; Colin Bryar & Bill Carr, *Working Backwards*, 2021). Start from the customer and write the press release **before** building. Typically reviewed in a meeting where everyone reads silently first (Amazon's "narrative" culture; 6-pagers, no slides).

### Press release (≤ 1 page)

```markdown
**Heading:** [Product name] lets [target customer] [key benefit]
**Subheading:** One sentence on who the customer is and what benefit they get.

**[City] — [Launch date]** — Summary paragraph: the product and its benefit.

**Problem:** Describe the problem from the customer's point of view.

**Solution:** How the product elegantly solves the problem.

**Quote from a company spokesperson:** Why we built it.

**How it works / getting started:** How easy it is to start.

**Customer quote:** A hypothetical customer describing their experience/benefit.

**Call to action:** Where to go next.
```

### FAQ

**External (customer) FAQ** — questions customers would ask: How much does it cost? How does it work with X? What if I…? Is my data safe?

**Internal (stakeholder) FAQ** — questions leadership would ask:

- How big is the opportunity (TAM, # customers, value per customer)?
- What are the unit economics / P&L impact? Cost to build and run?
- What are the riskiest assumptions; how will we validate them?
- What are the dependencies (teams, partners, data, legal)?
- Why will customers choose this over alternatives?
- What's the minimal lovable version and how do we expand?
- What could make this fail? What would we have to believe?
- What are we **not** doing?
- How do we measure success?

**Example headline**: "Ordering Assistant turns emailed purchase orders into confirmed ERP orders in under 2 minutes — no re-typing."

**Tips**: write in plain language (no jargon); if the PR isn't exciting, rethink the product; iterate the PR/FAQ many times; it's a discovery tool, not marketing.

---

## 4. Product brief (discovery → delivery)

```markdown
# Product brief: [Feature/initiative]

## 1. Summary
2–3 sentences: what, for whom, why.

## 2. Outcome & metrics
Primary metric (baseline → target), secondary, guardrails. Measurement plan & dashboard link.

## 3. Customer & problem
Persona/segment, job to be done, current workflow, pains. Evidence: interviews (#), data, quotes.

## 4. Discovery summary
Experiments run, results, what we learned, what we discarded and why.

## 5. Solution overview
Key user flows, prototype/Figma link, story map link. Principles/design decisions.

## 6. Scope
MVP / first release slice. Later slices. Non-goals.

## 7. Risks & assumptions remaining
Value / usability / feasibility / viability. Mitigations.

## 8. Dependencies & stakeholders
Teams, systems, legal/security reviews, GTM/enablement needs.

## 9. Timeline & milestones
Target windows, decision points, release strategy (flags, beta).
```

---

## 5. PRD — Product Requirements Document

Modern PRDs are living documents (Confluence/Notion/Google Docs) linked to epics and designs; keep the "why" rich and the "how" open where possible.

```markdown
# PRD: [Feature name]
| Field | Value |
|---|---|
| Owner | [PM] |
| Contributors | Design, Eng lead, QA, Data, PMM |
| Status | Draft / In review / Approved / In delivery / Released |
| Target release | Window / version |
| Epic / tickets | Links |
| Related docs | One-pager, brief, ADRs, designs |

## 1. Background & problem statement
Context, evidence, links to research. Why now?

## 2. Goals & non-goals
Goals (outcomes) | Non-goals (explicitly out of scope)

## 3. Success metrics
| Metric | Baseline | Target | Measurement | Guardrail? |

## 4. Users & personas / jobs
Who, context, job stories.

## 5. User journeys / use cases
Main flows, alternate flows, edge cases. Story map link.

## 6. Requirements
### 6.1 Functional requirements
| ID | Requirement / user story | Priority (MoSCoW) | Acceptance criteria | Notes |
|---|---|---|---|---|
| FR-1 | As a buyer I can reorder a previous order | Must | Given/When/Then … | |

### 6.2 Non-functional requirements (ISO 25010 checklist)
Performance · Reliability/availability · Security & privacy · Accessibility · Compatibility/integrations · Maintainability · Scalability · Compliance · Observability · Localisation

### 6.3 Data & analytics
Data model changes, data sources, retention, tracking events (tracking plan).

### 6.4 Permissions
Role × action matrix.

## 7. Design
Figma links, key screens, content/copy guidelines, empty/error states.

## 8. Technical considerations
Architecture notes, APIs, migrations, feature flags, ADR links. (Owned by eng.)

## 9. Dependencies & risks
| Risk | Likelihood | Impact | Mitigation | Owner |

## 10. Release plan
Rollout strategy (beta, % rollout), release criteria, GTM tier, enablement, docs, support readiness.

## 11. Open questions
| Question | Owner | Due | Answer |

## 12. Appendix / change log
| Date | Change | By |
```

**PRD quality checklist**

- [ ] Problem and evidence are clear without reading the solution
- [ ] Goals measurable; non-goals explicit
- [ ] Requirements testable; priorities set
- [ ] NFRs considered (performance, security, privacy, accessibility)
- [ ] Edge cases and error states covered
- [ ] Analytics/tracking defined before build
- [ ] Reviewed by design, engineering, QA, and relevant stakeholders (legal/security)
- [ ] Linked to backlog; kept up to date

---

## 6. Experiment card & learning card (Strategyzer-style)

```markdown
### Test card
Hypothesis: We believe that …
Test: To verify that, we will …
Metric: And measure …
Criteria: We are right if …
Assumption type: value | usability | feasibility | viability
Cost: €/hours · Duration: … · Evidence strength: weak | medium | strong

### Learning card
Hypothesis: We believed that …
Observation: We observed …
Learnings & insights: From that we learned that …
Decisions & actions: Therefore we will … (persevere | pivot | kill | iterate)
```

---

## 7. Release notes

**Principles**: lead with user benefit, not implementation; group by audience; link to docs; be honest about breaking changes; keep a public/internal changelog; consistent versioning (SemVer for APIs: MAJOR.MINOR.PATCH). "Keep a Changelog" categories: Added, Changed, Deprecated, Removed, Fixed, Security.

```markdown
# Release 2.4 — 14 October 2026

## Highlights
**Reorder in one click** — Repeat any previous order from your order history and
adjust quantities before submitting. Buyers in our pilot placed weekly orders
3× faster. [How it works →]

## New
- **Contract prices in the catalogue** — See your negotiated price and its validity date on every product.

## Improved
- Quantities are now rounded to the packaging unit automatically, with a notice.
- Search finds products by your own internal article numbers.

## Fixed
- Delivery date sometimes showed in UTC instead of local time.

## Deprecated / breaking changes
- API: `GET /v1/prices` is deprecated; use `GET /v2/prices` (removal on 2027-04-01).

## For admins
- New permission "Can approve orders > €5,000".

Questions? Contact your account manager or support@… (placeholder)
```

Internal release note additions: support FAQ, known issues, rollout schedule, feature flags state, training links, metrics to watch.

---

## 8. Decision record (product)

```markdown
# Decision: [title]  — Date · Status: proposed | decided | superseded
Context: what forces/evidence led here
Options considered: A / B / C with pros/cons
Decision: …
Rationale: …
Consequences / follow-ups: …
Decision maker (DACI): Driver … Approver … Contributors … Informed …
Revisit when: [trigger or date]
```

(Technical decisions → ADRs; see [../05-software-architecture/README.md](../05-software-architecture/README.md).)

---

## 9. Stakeholder update (monthly)

```markdown
## Product update — [Month]
**TL;DR:** 3 bullets
**Outcomes:** KR progress table (baseline, current, target, confidence)
**Shipped:** what & impact observed
**Learned:** key discovery insights / experiments (incl. failed ones)
**Next:** Now/Next changes and why
**Risks & asks:** decisions or help needed from stakeholders
```

---

## 10. Interview snapshot (Torres)

| Section | Content |
|---|---|
| Name / segment / date | |
| Memorable quote | |
| Quick facts | Role, company size, tools used |
| Experience map | Sketch of the story they told |
| Opportunities | Needs/pains/desires heard (customer language) |
| Insights | Surprising learnings; follow-ups |

---

## 11. Customer feedback / feature request intake

| Field | Example |
|---|---|
| Customer / segment / ARR | ACME, wholesale, €48k |
| Requested by (role) | Purchasing manager |
| Request (verbatim) | "We need an Excel upload for orders" |
| Underlying problem (after follow-up) | 40 locations order the same assortment weekly; manual entry takes 2 h |
| Frequency / severity | Weekly / high |
| Workaround | Emails Excel to AM, who re-keys |
| Linked opportunity | "Bulk ordering for multi-location accounts" |
| Status | Under consideration / Planned / Declined (reason) |

---

## Common mistakes with documents

- Writing the solution spec before agreeing on the problem and outcome.
- PRD as a contract between "business" and "IT" (kills collaboration).
- Documents that are never updated after discovery changes the plan.
- No non-goals → scope creep.
- Release notes written for engineers rather than users.
- Skipping the internal FAQ hard questions in PR/FAQ.

## Related sections

- ADRs, architecture docs: [../05-software-architecture/README.md](../05-software-architecture/README.md)
- Project charters and status reports: [../03-project-management/README.md](../03-project-management/README.md)
- Facilitation of review meetings: [../09-leadership-teams-facilitation/README.md](../09-leadership-teams-facilitation/README.md)

## References

- SVPG — Opportunity assessment / articles — https://www.svpg.com/articles/
- Working Backwards (book site) — https://www.workingbackwards.com/
- Product requirements document (Wikipedia) — https://en.wikipedia.org/wiki/Product_requirements_document
- Keep a Changelog — https://keepachangelog.com/
- Semantic Versioning — https://semver.org/
- Strategyzer (test & learning cards) — https://www.strategyzer.com/
- Atlassian — Product requirements (PRD) guide — https://www.atlassian.com/agile/product-management/requirements
- Lenny's Newsletter (templates collection) — https://www.lennysnewsletter.com/
- Product Talk — interview snapshots — https://www.producttalk.org/
