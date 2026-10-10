# Scaling Agile: Frameworks and Critique

> **TL;DR** — Scaling frameworks address coordination of many teams on one product or portfolio. **SAFe 6** is comprehensive and prescriptive (Agile Release Trains, PI Planning, Lean Portfolio Management). **LeSS** is minimal: one Product Owner, one backlog, many feature teams, descaling the organisation. **Nexus** is Scrum.org's 3–9 team extension with a Nexus Integration Team. **Scrum@Scale** scales via fractal Scrum of Scrums and MetaScrums. The **Spotify model** was a snapshot, never a framework. **Disciplined Agile** (PMI) is a toolkit of choices. **Flight Levels** focuses on where to improve (team, coordination, strategy). **unFIX** provides modular org-design patterns. Before scaling, **descale**: reduce dependencies through architecture and team design. Frameworks don't fix overloaded portfolios.

---

## 1. Why scale (and why not)

| Driver | Better first step |
|---|---|
| Many teams on one product with dependencies | Re-team around value streams; reduce coupling (architecture, team topologies) |
| Portfolio overload | Portfolio Kanban, WIP limits on initiatives |
| Need for alignment on goals | Shared goals/OKRs, regular cross-team review |
| Integration problems | CI across teams, trunk-based, contract testing |
| Leadership wants visibility | Flow metrics at value stream level; Obeya |

**Conway's law** and **Team Topologies** (Skelton & Pais): stream-aligned, platform, enabling, complicated-subsystem teams; interaction modes (collaboration, X-as-a-Service, facilitating). See [../05-software-architecture/README.md](../05-software-architecture/README.md) and [../09-leadership-teams-facilitation/README.md](../09-leadership-teams-facilitation/README.md).

---

## 2. Comparison overview

| Framework | Origin | Team range | Prescriptiveness | Core structure | Roles added | Cadence |
|---|---|---|---|---|---|---|
| **SAFe 6.0** | Dean Leffingwell, Scaled Agile Inc., 2011 (6.0 in 2023) | 50–125 per ART; multiple ARTs; up to thousands | High | Agile Release Train (ART), Solution Train, Portfolio | RTE, Product Management, System Architect, Business Owners, Solution Train Engineer, LPM roles | PI (8–12 weeks, typically 4 dev iterations + IP iteration) |
| **LeSS / LeSS Huge** | Craig Larman & Bas Vodde, 2005–2014 | LeSS 2–8 teams; LeSS Huge 8+ | Low (rules few, principles many) | One product, one PO, one Product Backlog, feature teams | Area Product Owners (LeSS Huge) | One shared Sprint |
| **Nexus** | Ken Schwaber, Scrum.org, 2015 | 3–9 Scrum teams | Low–medium | Nexus = Scrum teams + Nexus Integration Team | Nexus Integration Team | Shared Sprint; Nexus events |
| **Scrum@Scale** | Jeff Sutherland, 2014+ | Scale-free (fractal) | Low–medium | Scrum of Scrums (≤ 5 teams), Scrum of Scrum of Scrums; Executive Action Team; Executive MetaScrum | Scrum of Scrums Master, Chief PO | Scaled Daily Scrum, MetaScrum |
| **Spotify "model"** | Kniberg & Ivarsson, 2012 whitepaper | 100s | None (it's a description) | Squads, Tribes, Chapters, Guilds | Chapter lead, tribe lead | Varies |
| **Disciplined Agile (DA)** | Scott Ambler & Mark Lines, 2012; acquired by PMI 2019 | Any | Toolkit ("choose your WoW") | Lifecycles (Agile, Lean, Continuous Delivery, Exploratory, Program) | Team lead, architecture owner, product owner, + specialists | Varies |
| **Flight Levels** | Klaus Leopold, ~2014+ | Any | Very low (thinking model) | Level 1 operational, Level 2 coordination, Level 3 strategy | None | Flight-level-specific |
| **unFIX** | Jurgen Appelo, 2022 | Any | Low (pattern library) | Base, Crews (value stream, platform, facilitation, capability, experience, governance), Forums, Turf | Various | Varies |
| **Enterprise Kanban / Kanban at scale** | Kanban University | Any | Low | Network of kanban systems; cadences | SRM, SDM | Kanban cadences |
| **Large-scale: Holacracy/Sociocracy** | Org governance | — | — | Circles | — | — |

---

## 3. SAFe 6.0

### 3.1 Configurations
- **Essential SAFe** — the core: teams + Agile Release Train.
- **Large Solution SAFe** — multiple ARTs + suppliers in a Solution Train.
- **Portfolio SAFe** — + Lean Portfolio Management (LPM), strategic themes, portfolio Kanban.
- **Full SAFe** — all of the above.

### 3.2 Core values and principles
- Core values: Alignment, Transparency, Respect for people, Relentless improvement (SAFe 6).
- Ten Lean-Agile principles (paraphrased): take an economic view; apply systems thinking; assume variability, preserve options; build incrementally with fast integrated learning cycles; base milestones on objective evaluation of working systems; make value flow without interruptions (SAFe 6 revised: "visualize and limit WIP, reduce batch sizes, manage queue lengths" was the previous #6); apply cadence and synchronise with cross-domain planning; unlock the intrinsic motivation of knowledge workers; decentralise decision-making; organise around value.

### 3.3 Agile Release Train (ART)
- Long-lived team of agile teams (typically 5–12 teams, 50–125 people) delivering a solution in a value stream.
- Key roles: **Release Train Engineer (RTE)** — chief Scrum Master/servant leader for the ART; **Product Management** — owns ART backlog/features; **System Architect** — architectural runway; **Business Owners** — key stakeholders accountable for value.
- Teams can use **ScrumXP** or **Team Kanban**.
- Shared **System Team** (integration, environments) often present.

### 3.4 PI Planning
- Planning Interval (renamed from Program Increment in 6.0): 8–12 weeks.
- Two-day, face-to-face (or remote) event for the entire ART.

| Day 1 | Day 2 |
|---|---|
| Business context (Business Owners) | Planning adjustments from management review |
| Product/solution vision & top features (Product Management) | Team breakouts #2 |
| Architecture vision, development practices | Final plan review; team PI objectives with business value assigned by Business Owners |
| Planning context & lunch | Program risks — **ROAM**: Resolved, Owned, Accepted, Mitigated |
| Team breakouts #1 (draft plans, capacity, dependencies) | **Confidence vote** (fist of five); rework if low |
| Draft plan review | Planning retrospective |
| Management review & problem solving (evening) | |

Outputs: committed **PI objectives** per team (committed + uncommitted/stretch), **ART planning board** (features & dependencies — the "string board"), risks ROAMed.

### 3.5 Other SAFe events & concepts
- **Innovation & Planning (IP) iteration** — buffer for innovation, PI planning, Inspect & Adapt, education.
- **Inspect & Adapt (I&A)** — PI system demo, quantitative and qualitative measurement, problem-solving workshop.
- **ART Sync** (Coach Sync + PO Sync), **System Demo** each iteration.
- **WSJF** for prioritising features/epics (see [queueing-and-flow-theory.md](queueing-and-flow-theory.md)).
- **Lean Portfolio Management (LPM)**: strategy & investment funding (lean budgets per value stream), agile portfolio operations, lean governance; **Portfolio Kanban** with epic hypothesis statements and Lean business cases.
- **Architectural runway**, **enablers**, **Continuous Delivery Pipeline** (continuous exploration, integration, deployment, release on demand).
- SAFe 6 additions: **flow accelerators**, eight flow properties, emphasis on OKRs, "business agility value stream", team and technical agility.

### 3.6 Critique of SAFe
| Criticism | Notes |
|---|---|
| Heavyweight, many roles and artefacts | "Big picture" complexity; risk of bureaucracy |
| Top-down, often used to preserve existing structures | Rebrands managers into new roles without change |
| PI planning as 10-week waterfall | Fixed plans for a quarter can reduce responsiveness |
| Normalised story points | Encourages cross-team velocity comparison |
| Certification industry | Commercial incentives |
| Agile community critique | Several manifesto signatories have publicly criticised SAFe |
| Positive | Gives large enterprises a structured start, alignment, cadence; PI planning surfaces dependencies; LPM addresses funding |

---

## 4. LeSS (Large-Scale Scrum)

### 4.1 Principles (selection)
Large-scale Scrum is Scrum; more with less; whole-product focus; customer-centric; continuous improvement towards perfection; lean thinking; systems thinking; empirical process control; queueing theory; transparency.

### 4.2 Structure
- **One Product Owner**, **one Product Backlog**, **one Definition of Done**, **one Potentially Shippable Product Increment**, **one Sprint** for all teams.
- Feature teams (cross-component, cross-functional, long-lived).
- Scrum Master per 1–3 teams.
- **LeSS Huge** (> 8 teams): Requirement Areas each with Area Product Owner; still one product backlog and one PO.

### 4.3 Events
- Sprint Planning One (all teams or representatives: which items) and Sprint Planning Two (per team or multi-team: how).
- Daily Scrum per team; coordination via "just talk", communicate in code, cross-team meetings, travellers, scouts.
- **Overall Product Backlog Refinement** + team-level refinement.
- One Sprint Review for all teams (bazaar style).
- Team Retrospectives + **Overall Retrospective** (system-level).

### 4.4 LeSS guides — rules of thumb
- Prefer feature teams over component teams.
- Organisational change: "descaling" — remove roles, layers, and coordination overhead rather than adding them.
- Managers optional; focus on capability building.

### 4.5 Critique
- Requires deep organisational change (feature teams, no component teams, PO with real authority) — hard to adopt partially.
- Less guidance on portfolio/finance.
- Positive: lean, conceptually coherent; addresses root causes (structure).

---

## 5. Nexus

- Scrum.org's framework for **3–9 Scrum Teams** on a single product.
- **Nexus Integration Team (NIT)**: Product Owner, Scrum Master, NIT members — accountable for ensuring an integrated increment each Sprint; coaching, tooling, integration.
- Artifacts: Product Backlog, **Nexus Sprint Backlog**, **Integrated Increment**; commitments: Product Goal, **Nexus Sprint Goal**, DoD.
- Events: **Cross-team refinement**, **Nexus Sprint Planning**, **Nexus Daily Scrum**, **Nexus Sprint Review** (replaces team reviews), **Nexus Sprint Retrospective**.
- **Nexus+** for > 9 teams (multiple Nexuses).
- Emphasis: dependencies identification and minimisation; integration issues.

---

## 6. Scrum@Scale

- Jeff Sutherland; "scale-free architecture" via fractal replication.
- **Scrum Master Cycle** (how): team-level process improvement, cross-team coordination, deployment.
- **Product Owner Cycle** (what): strategic vision, backlog prioritisation, decomposition, release planning, metrics.
- **Scrum of Scrums (SoS)**: ~5 teams; Scaled Daily Scrum; SoS Master.
- **Executive Action Team (EAT)**: removes impediments at org level; owns agile transformation.
- **Executive MetaScrum (EMS)**: executive-level Product Owner team; sets priorities.
- Lightweight guide at scrumatscale.com.

---

## 7. The Spotify "model"

### 7.1 Structure (2012 whitepaper by Henrik Kniberg & Anders Ivarsson)

| Unit | Description |
|---|---|
| **Squad** | Small cross-functional autonomous team (like a Scrum team), long-term mission |
| **Tribe** | Collection of squads in a related area (≤ ~100–150, Dunbar's number) |
| **Chapter** | People with similar skills within a tribe (e.g. backend devs); chapter lead is line manager |
| **Guild** | Community of interest across tribes (voluntary) |
| Trio / Alliance | Later additions: tribe lead + product lead + design lead; alliances of tribes |

Also: "Spotify Engineering Culture" videos (2014), Squad Health Check model (see [retrospectives-and-team-health.md](retrospectives-and-team-health.md)).

### 7.2 Caveats

| Caveat | Detail |
|---|---|
| Never a framework | Authors described a snapshot of how Spotify worked at the time; explicitly warned against copying |
| Spotify itself evolved | Former employees (e.g. Jeremiah Lee, "Spotify's Failed #SquadGoals", 2020) described that the matrix was more aspirational than real, chapters/guilds were inconsistent, and autonomy lacked alignment |
| Matrix management issues | Chapter leads managing people whose daily work they don't see |
| Copying names, not culture | Renaming teams "squads" and departments "tribes" changes nothing |
| Autonomy requires alignment | "Aligned autonomy" depends on strong product strategy and architecture (decoupled services) |
| Kniberg's own advice | Use it as inspiration, not a template |

---

## 8. Disciplined Agile (DA)

- Scott Ambler & Mark Lines; acquired by PMI (2019). Now part of PMI's offerings.
- "**Choose your WoW**" (Way of Working) — process-decision toolkit, context-sensitive.
- Layers: Disciplined DevOps, Disciplined Agile Delivery (DAD), Value Streams, Disciplined Agile Enterprise.
- DAD lifecycles: Agile (Scrum-based), Lean (Kanban-based), Continuous Delivery: Agile, Continuous Delivery: Lean, Exploratory (Lean Startup), Program (team of teams).
- Phases: Inception, Construction, Transition.
- Principles: delight customers, be awesome, context counts, be pragmatic, choice is good, optimise flow, organise around products/services, enterprise awareness.
- Goal diagrams with decision points and options + trade-offs.
- Critique: complex, large body of knowledge; less community adoption than SAFe/Scrum.

---

## 9. Flight Levels (Klaus Leopold)

| Level | Question | Board | Typical cadences |
|---|---|---|---|
| **FL1 Operational** | How does the team deliver? | Team Kanban/Scrum board | Daily, replenishment, retros |
| **FL2 Coordination** | How do multiple teams deliver end-to-end value together? | Value stream / product board (features across teams) | Weekly coordination, replenishment |
| **FL3 Strategic** | What should we work on? How does strategy translate into work? | Portfolio board (initiatives); strategy | Monthly/quarterly strategy and portfolio replenishment |

Five Flight Levels activities: visualise, focus (limit WIP/finish), coordinate, align with strategy, measure/improve. Key insight (Leopold's "Rethinking Agile"): optimising teams (FL1) without FL2/FL3 creates "agile islands"; business agility comes from coordination and strategy levels. Method-agnostic — can host Scrum, Kanban, SAFe.

---

## 10. unFIX model (Jurgen Appelo)

- Pattern library for versatile organisation design (2022).
- **Base**: a unit of ~ up to 150 people (Dunbar-like), relatively autonomous.
- **Crew** types: Value Stream Crew, Platform Crew, Facilitation Crew, Capability Crew, Experience Crew, Partnership Crew, Governance Crew.
- **Forums** (cross-crew communities, like chapters/guilds), **Turf** (area of the Base), **Chief Facilitator**, **Captain**.
- Supports dynamic reteaming and fluid structures; complements Team Topologies.
- Not a process framework; combine with Scrum/Kanban.

---

## 11. Critique of scaling frameworks (general)

| Concern | Description |
|---|---|
| "Scaling agile" vs "descaling the organisation" | Adding coordination layers treats symptoms; removing dependencies treats causes |
| Framework-first transformations | Big-bang rollouts conflict with evolutionary change and local context |
| Certification economy | Incentivises selling frameworks |
| Governance unchanged | Funding, HR, procurement stay project-oriented → friction |
| Portfolio overload persists | No framework survives 40 parallel initiatives |
| Technical practices neglected | Integration, CI/CD, architecture determine scaling feasibility |
| Copy-paste of Spotify | Labels without culture |
| Positive | Shared vocabulary, cadence, alignment events, visibility of dependencies |

### 11.1 Choosing an approach

| Context | Fit |
|---|---|
| Large enterprise needing structure, many stakeholders, regulated | SAFe (carefully tailored), or Flight Levels + Kanban |
| Single product, willing to restructure into feature teams | LeSS |
| 3–9 Scrum teams on one product, Scrum-based already | Nexus |
| Org already Kanban-based, wants evolutionary scaling | Kanban Method at scale + Flight Levels |
| Want design patterns for structure | Team Topologies + unFIX |
| Need context-specific process choices | Disciplined Agile |

### 11.2 Pre-scaling checklist
- [ ] Have we reduced the number of teams needed (descale)?
- [ ] Are teams organised around value streams/products?
- [ ] Is architecture decoupled enough for independent deployment?
- [ ] Is there portfolio WIP limit?
- [ ] Do teams share a DoD and integrate continuously?
- [ ] Are flow metrics visible at value-stream level?
- [ ] Is leadership changing its own behaviour (funding, governance)?

---

## References

- Scaled Agile Framework — <https://scaledagileframework.com/>
- SAFe PI Planning — <https://scaledagileframework.com/pi-planning/>
- LeSS — <https://less.works/>
- Scrum.org (Nexus Guide) — <https://www.scrum.org/>
- Scrum@Scale — <https://www.scrumatscale.com/>
- Disciplined Agile (PMI) — <https://www.pmi.org/disciplined-agile>
- Flight Levels — <https://www.flightlevels.io/>
- unFIX — <https://unfix.com/>
- Team Topologies — <https://teamtopologies.com/>
- Spotify Engineering — <https://engineering.atspotify.com/>
- Wikipedia: Scaled agile framework — <https://en.wikipedia.org/wiki/Scaled_agile_framework>
- Wikipedia: Large-scale scrum — <https://en.wikipedia.org/wiki/Large-scale_scrum>
- Wikipedia: Disciplined agile delivery — <https://en.wikipedia.org/wiki/Disciplined_agile_delivery>
- Wikipedia: Conway's law — <https://en.wikipedia.org/wiki/Conway%27s_law>
- Craig Larman & Bas Vodde, *Large-Scale Scrum: More with LeSS* (2016)
- Dean Leffingwell, *SAFe Reference Guide* / *SAFe Distilled*
- Klaus Leopold, *Rethinking Agile* (2020)
- Matthew Skelton & Manuel Pais, *Team Topologies* (2019)
- Scott Ambler & Mark Lines, *Choose Your WoW!* (2019)
- Henrik Kniberg & Anders Ivarsson, "Scaling Agile @ Spotify with Tribes, Squads, Chapters & Guilds" (2012 whitepaper)
