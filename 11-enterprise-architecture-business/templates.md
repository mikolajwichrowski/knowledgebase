# Templates: Principles, Capability Map, Inventory, ERP Scoring, Assessment, Business Case

> **TL;DR** Copy-paste starting points for the most common enterprise architecture and advisory deliverables: 15 architecture principles (TOGAF format), a cross-industry capability map skeleton, application inventory columns, an ERP selection scoring matrix, an IT assessment checklist, and a one-page business case. Adapt names, weights and scales to the organization; delete what you don't need.

---

## 1. Architecture principles (15 examples)

Format: **Name** · **Statement** · **Rationale** · **Implications** (TOGAF). Aim for 10–15 that the organization actually uses in decisions. Background: [ea-frameworks-togaf-archimate-zachman.md](ea-frameworks-togaf-archimate-zachman.md#25-architecture-principles).

### Business principles

**P1. Business value drives IT decisions**
- *Statement:* Every IT investment is justified by a measurable contribution to business objectives.
- *Rationale:* Scarce budget and people must go to the highest value.
- *Implications:* Initiatives above a threshold need a business case with a named benefit owner; post-implementation reviews check benefits; portfolio prioritization uses value scoring.

**P2. Standard processes unless differentiating**
- *Statement:* We adopt standard (vendor/industry best-practice) processes for non-differentiating capabilities.
- *Rationale:* Lower implementation and upgrade cost, faster change, easier staffing.
- *Implications:* Fit-to-standard in ERP projects; deviations need business justification and board approval; process owners accept changing their way of working.

**P3. Processes have owners**
- *Statement:* Every end-to-end process (O2C, P2P, R2R, H2R, P2P-production) has an accountable business owner.
- *Rationale:* Cross-departmental processes break at handoffs without ownership.
- *Implications:* Process owners decide on process design, KPIs and change requests; IT supports but does not own processes.

**P4. Compliance by design**
- *Statement:* Legal, regulatory and contractual requirements (GDPR, NIS2, fiscal retention, sector rules) are built into solutions from the start.
- *Rationale:* Retrofitting compliance is expensive and risky.
- *Implications:* Privacy and security checks in design review; DPIA when needed; retention rules configured; audit trails enabled.

### Data principles

**P5. Data is an asset with an owner**
- *Statement:* Each critical data domain (customer, supplier, item, employee, finance) has a business data owner and steward.
- *Rationale:* Data quality and definitions require accountability.
- *Implications:* Ownership recorded in the data catalog/inventory; owners approve definitions, access and quality rules; stewardship time is budgeted.

**P6. Single source of truth per data object**
- *Statement:* Each master data object is created and maintained in one designated system of record and distributed from there.
- *Rationale:* Prevents inconsistency, duplicates and reconciliation work.
- *Implications:* No creation of master data in secondary systems; integrations distribute changes; reporting uses the golden source.

**P7. Common vocabulary and definitions**
- *Statement:* Business terms and KPIs have one agreed definition across the organization.
- *Rationale:* Avoids "two versions of revenue" debates.
- *Implications:* Business glossary maintained; BI uses certified datasets; new reports reuse definitions.

**P8. Data is secured and classified**
- *Statement:* Data is classified (public, internal, confidential, secret/special category) and protected accordingly.
- *Rationale:* Proportionate protection and regulatory compliance.
- *Implications:* Classification attribute in application inventory; access by role; encryption and DLP for confidential data.

### Application principles

**P9. Reuse, then buy, then build**
- *Statement:* New needs are met first by existing platforms, then by configured standard SaaS/COTS, and only then by custom build.
- *Rationale:* Lower TCO, faster delivery; engineering focus on differentiation.
- *Implications:* Proposals document reuse/buy options; custom build requires approval and an owning team.

**P10. Keep the core clean**
- *Statement:* Systems of record (ERP, HRIS, payroll) remain close to vendor standard; extensions are made side-by-side or through released extension frameworks.
- *Rationale:* Fast, cheap upgrades; vendor support; lower risk.
- *Implications:* No modifications of vendor code or direct database writes; extension ladder applied; technical debt register for exceptions.

**P11. SaaS and cloud first**
- *Statement:* We prefer SaaS, then PaaS, then IaaS, and on-premises only with justification.
- *Rationale:* Reduced operational burden, scalability, faster innovation.
- *Implications:* Vendor due diligence (security, data location, exit) mandatory; skills shift to vendor management and integration; FinOps practice.

**P12. Ease of use for the role**
- *Statement:* Applications are designed for the tasks of each role; occasional users get simple interfaces.
- *Rationale:* Adoption, productivity and data quality depend on usability.
- *Implications:* Role-based workspaces; approvals via mobile/Teams; usability tested with real users; training budgeted.

### Technology & integration principles

**P13. Integrate via APIs and the integration platform**
- *Statement:* Systems exchange data via documented APIs/events on the central integration platform; no point-to-point database links.
- *Rationale:* Decoupling, monitoring, reuse, security.
- *Implications:* Interface catalog maintained; integration standards (REST/JSON, events, EDI via provider); error handling with business owners.

**P14. Secure by default, identity-centric**
- *Statement:* All access is through the corporate identity provider with MFA, least privilege and automated joiner-mover-leaver.
- *Rationale:* Identity is the primary security perimeter; NIS2/ISO 27001 requirements.
- *Implications:* SSO (SAML/OIDC) and SCIM required in vendor selection; no shared accounts; privileged access just-in-time; periodic access reviews.

**P15. Control technical diversity**
- *Statement:* We limit the number of technologies per purpose to those on the technology radar (Adopt/Trial).
- *Rationale:* Lower support cost, skills concentration, security.
- *Implications:* Radar maintained; "Hold" technologies need dispensation; retirement plans for duplicates.

---

## 2. Capability map skeleton

Cross-industry L1/L2 skeleton for a mid-size product company (manufacturing/distribution). Adapt; delete what does not apply. Method: [ea-practice-capabilities-portfolio.md](ea-practice-capabilities-portfolio.md#2-business-capability-maps).

| L0 | L1 | L2 capabilities |
|---|---|---|
| **Strategic** | Strategy & governance | Strategy management · Enterprise risk management · Portfolio & investment management · Enterprise architecture · Legal & compliance · Internal audit |
| | Performance management | Planning & budgeting · Performance reporting · Business intelligence |
| | Innovation & change | Innovation management · Change & transformation management · Continuous improvement |
| **Core** | Product & service development | Product lifecycle management · Product data management · Engineering/design · Product costing |
| | Marketing | Market analysis · Brand & communication · Campaign management · Digital marketing |
| | Sales | Lead & opportunity management · Account management · Pricing & quotation · Contract management (sales) · Sales order management · E-commerce |
| | Supply chain planning | Demand planning/forecasting · Sales & operations planning · Supply planning (MRP) · Production scheduling · Inventory planning |
| | Sourcing & procurement | Spend analysis · Strategic sourcing · Supplier management · Purchasing (operational) · Supplier quality |
| | Manufacturing / operations | Production execution · Quality management · Maintenance (EAM) · Health, safety & environment |
| | Logistics | Inbound logistics · Warehouse management · Inventory management · Outbound/transport management · Returns management |
| | Customer service | Customer service management · Field service · Warranty & claims · Customer self-service |
| **Supporting** | Finance | General accounting · Accounts payable · Accounts receivable & credit management · Treasury & cash management · Fixed asset management · Cost accounting/controlling · Tax management · Consolidation & statutory reporting |
| | Human resources | Workforce planning · Recruitment · Onboarding & offboarding · Employee administration · Payroll · Time & absence · Learning & development · Performance & reward |
| | Information technology | IT strategy & architecture · IT service management · Application management · Infrastructure & cloud · Information security · Identity & access management · Data management |
| | Facilities & assets | Facility management · Fleet management · Workplace services |
| | Corporate communication | Internal communication · Investor/stakeholder relations · Sustainability (ESG) reporting |

**Heat map columns to add per L2:** Strategic importance (1–5) · Performance (1–5) · IT support (1–5) · Supporting applications · Owner · Pace layer · Planned initiatives.

---

## 3. Application inventory columns

Usage: [ea-practice-capabilities-portfolio.md](ea-practice-capabilities-portfolio.md#5-application-portfolio-management-apm). Mark **(M)** = minimum viable.

| # | Column | Description / allowed values |
|---|---|---|
| 1 | App ID **(M)** | Unique key (APP-001) |
| 2 | Name **(M)** | Common name |
| 3 | Description **(M)** | One sentence: what it does for whom |
| 4 | Vendor / product / edition | e.g. Microsoft / Business Central / Essentials |
| 5 | Version / release | Current version, update cadence |
| 6 | Type | SaaS, PaaS custom, IaaS hosted COTS, on-prem COTS, custom, low-code, end-user computing (Excel/Access) |
| 7 | Business owner **(M)** | Accountable person (business) |
| 8 | Application manager / IT owner **(M)** | Responsible for operation |
| 9 | Supplier / partner | Implementation/support partner |
| 10 | Business capabilities supported **(M)** | L2 capability IDs |
| 11 | Processes supported | O2C, P2P, R2R… |
| 12 | Departments / user groups | |
| 13 | Number of users | Named / concurrent |
| 14 | Lifecycle status **(M)** | Plan · Phase-in · Active · Phase-out · Retired |
| 15 | End-of-support / contract end date | |
| 16 | Hosting location / data residency | EU / NL / US / other |
| 17 | Authentication | SSO (SAML/OIDC) · local accounts · MFA Y/N · SCIM Y/N |
| 18 | Business criticality **(M)** | Tier 1 (critical) – Tier 4 (low) from BIA |
| 19 | RTO / RPO | e.g. 4 h / 1 h |
| 20 | Data classification **(M)** | Public · Internal · Confidential · Secret |
| 21 | Personal data | None · Regular · Special categories · BSN |
| 22 | DPA in place | Y/N, date |
| 23 | Security assessment | Date, result, certifications (ISO 27001, SOC 2) |
| 24 | Interfaces | Count + link to interface catalog |
| 25 | Master data mastered here | Customer, item, supplier… |
| 26 | Annual license/subscription cost **(M)** | € |
| 27 | Annual other cost | Hosting, support, internal FTE |
| 28 | Contract notice period / auto-renewal | |
| 29 | Customization level | None · Configured · Extended · Heavily customized |
| 30 | Functional fit **(M)** | 1–5 (business-scored) |
| 31 | Technical fit **(M)** | 1–5 (IT-scored) |
| 32 | TIME disposition **(M)** | Tolerate · Invest · Migrate · Eliminate |
| 33 | Pace layer | Record · Differentiation · Innovation |
| 34 | Radar status | Adopt · Trial · Assess · Hold |
| 35 | Planned action & date | e.g. "Replace by ERP finance module, Q3 2027" |
| 36 | Documentation link | Architecture docs, runbook |
| 37 | Last reviewed | Date |

---

## 4. ERP selection scoring matrix

Process: [erp-implementation-selection.md](erp-implementation-selection.md#5-erp-selection-process). Score 0–5 per criterion (0 = not supported, 1 = poor, 3 = adequate via configuration, 4 = good standard, 5 = excellent standard). Weighted score = Σ (weight x score) / 5.

### 4.1 Knock-out criteria (pass/fail before scoring)

- [ ] Dutch localization (VAT, ICP, XAF auditfile, SEPA, e-invoicing via Peppol) available in standard.
- [ ] Deployment model acceptable (SaaS in EU data centres, or …).
- [ ] Supports multi-entity / multi-currency as required.
- [ ] Implementation partner with ≥ 3 references in our industry and size, in NL.
- [ ] Indicative 5-year TCO within budget envelope.
- [ ] SSO with corporate IdP; API availability.

### 4.2 Weighted scoring

| Category | Criterion | Weight | Vendor A | Vendor B | Vendor C |
|---|---|---|---|---|---|
| **Functional fit (35%)** | Finance (GL, AP, AR, FA, consolidation) | 8 | | | |
| | Procurement (P2P, approvals, contracts) | 5 | | | |
| | Sales & order management (O2C, pricing) | 6 | | | |
| | Inventory & warehouse | 5 | | | |
| | Planning & production (MRP, shop floor) | 6 | | | |
| | Reporting & analytics | 3 | | | |
| | Industry-specific requirements | 2 | | | |
| **Technical (15%)** | Integration (APIs, events, connectors, EDI) | 5 | | | |
| | Extensibility (upgrade-safe, low-code) | 3 | | | |
| | Security & identity (SSO, roles, SoD, audit) | 3 | | | |
| | Usability & mobile (scenario demo score) | 4 | | | |
| **Vendor & partner (20%)** | Vendor viability & roadmap | 5 | | | |
| | Partner expertise, team, references | 10 | | | |
| | Support model & SLAs | 3 | | | |
| | Ecosystem (add-ons, talent availability) | 2 | | | |
| **Cost (20%)** | 5–7 year TCO (lowest = 5, scaled) | 15 | | | |
| | Commercial terms (price caps, flexibility, exit) | 5 | | | |
| **Implementation & risk (10%)** | Approach & plan realism | 5 | | | |
| | Data migration approach | 3 | | | |
| | Change & training approach | 2 | | | |
| **Total** | | **100** | | | |

**Scenario demo scoring sheet (per scenario, per evaluator):**

| Scenario | Fit (0–5) | Ease of use (0–5) | Workarounds needed | Notes |
|---|---|---|---|---|
| S1: Quote to order with customer-specific pricing | | | | |
| S2: Purchase requisition to payment incl. 3-way match exception | | | | |
| S3: MRP run and production order with scrap | | | | |
| S4: Month-end close and VAT return | | | | |
| S5: Return of goods and credit note | | | | |
| S6: New employee to first payroll (if in scope) | | | | |

Tips: score independently before discussing; aggregate per role; record evidence; sensitivity-test weights (does the winner change if cost weight ±10%?).

---

## 5. IT assessment checklist

Score each item: 0 = absent, 1 = ad hoc, 2 = partial, 3 = defined, 4 = managed, 5 = optimized. Method: [consultancy-practice.md](consultancy-practice.md#4-current-state-assessment-checklist).

### Business & governance
- [ ] Business strategy known and translated into IT objectives.
- [ ] IT strategy/roadmap documented and approved.
- [ ] IT steering/governance forum with business participation.
- [ ] Demand intake and portfolio prioritization process.
- [ ] Process owners for end-to-end processes; KPIs measured.
- [ ] IT budget with run/grow/transform view; cost transparency.

### Applications
- [ ] Complete application inventory with owners.
- [ ] Lifecycle and end-of-support dates tracked.
- [ ] TIME assessment done in last 12 months.
- [ ] ERP close to standard; customizations documented.
- [ ] Licenses right-sized; usage reviewed.
- [ ] End-user computing (critical spreadsheets/Access DBs) inventoried and controlled.

### Data & integration
- [ ] Master data owners and quality rules for customer, supplier, item, employee.
- [ ] Single BI platform with certified datasets.
- [ ] Interface catalog; integration platform with monitoring and alerting.
- [ ] Data retention and deletion rules implemented.

### Infrastructure & cloud
- [ ] Documented infrastructure/cloud architecture; landing zone governance.
- [ ] Hardware and OS within support; replacement cycle planned.
- [ ] Backups: 3-2-1 incl. immutable/offline copy; restore tested in last 6 months.
- [ ] DR plan for Tier 1 systems tested; RTO/RPO defined and met.
- [ ] Monitoring of availability and capacity.

### Security & compliance
- [ ] Information security policy approved by management; roles (CISO/security officer) assigned.
- [ ] Risk assessment current; risk register maintained.
- [ ] MFA for all users (100%), phishing-resistant for admins.
- [ ] EDR on all endpoints and servers; patch SLAs met.
- [ ] Privileged access management; admin accounts separate.
- [ ] Central logging/SIEM or MDR service.
- [ ] Incident response plan with NIS2/GDPR reporting timelines; exercised.
- [ ] Supplier security assessments and DPAs.
- [ ] Awareness training and phishing simulations.
- [ ] Applicability of NIS2/Cbw determined; ISO 27001 status known.
- [ ] GDPR register of processing; DPIAs where needed.
- [ ] E-mail security (SPF, DKIM, DMARC enforced).

### IT operations & service management
- [ ] Single service desk; ticketing for all requests/incidents.
- [ ] Priority definitions and SLAs; reporting monthly.
- [ ] Change management with standard changes.
- [ ] Knowledge base and documentation current.
- [ ] Joiner-mover-leaver automated from HR; access reviews performed.
- [ ] Endpoint management (Intune/MDM) with compliance policies.

### People & sourcing
- [ ] IT roles and responsibilities clear; key-person risks mitigated.
- [ ] Skills matrix and training plan.
- [ ] Contract register with renewal dates and owners.
- [ ] Partner performance reviewed periodically.
- [ ] Business–IT relationship (satisfaction survey or NPS).

**Output:** heat map per area (average score vs target), top 10 risks, quick wins (≤ 3 months), structural recommendations, roadmap.

---

## 6. Business case one-pager

Detailed guidance: [business-models-and-business-cases.md](business-models-and-business-cases.md#7-business-case).

```text
BUSINESS CASE — <Initiative name>                 Date: ____  Version: __
Sponsor: ________   Benefit owner: ________   Author: ________

1. PROBLEM / OPPORTUNITY (diagnosis, with numbers)
   - ...
2. OBJECTIVES (SMART) & STRATEGIC LINK
   - ...
3. OPTIONS CONSIDERED
   0. Do nothing / minimum:  cost __  risk __
   A. ...                    cost __  benefits __
   B. ...                    cost __  benefits __
   Recommended: __ because ...
4. COSTS (recommended option)         One-time      Annual recurring
   External (licences, partner)       €            €
   Internal (people, backfill)        €            €
   Contingency (__%)                  €
   5-year TCO: €
5. BENEFITS                     Type (cash/efficiency/risk/revenue)   Amount/yr   From
   - ...
6. FINANCIALS   NPV (__% , 5y): €___   IRR: __%   Payback: __ months   ROI: __%
7. KEY RISKS & MITIGATIONS
   - ...
8. PLAN   Phases & milestones: ...   Go-live: ...   Resources: ...
9. KPIs & BENEFITS TRACKING   KPI | baseline | target | owner | when measured
10. DECISION REQUESTED   Approve € ___ and ___ FTE for ___ by ___ (date)
```

---

## 7. Bonus: small templates

**Architecture decision record (short):** Title · Status (proposed/accepted/superseded) · Context · Decision · Alternatives considered · Consequences (positive/negative) · Date & deciders. More: [../05-software-architecture/README.md](../05-software-architecture/README.md).

**Interface catalog row:** ID · Source · Target · Business object · Trigger (event/schedule) · Frequency/volume · Pattern (API/event/file/EDI) · Platform · Error handling & owner · SLA · Security (auth, encryption) · Status.

**RACI for an ERP program (excerpt):**

| Activity | Sponsor | Process owner | Project manager | Solution architect | Partner | Key users |
|---|---|---|---|---|---|---|
| Approve scope & budget | A | C | R | C | I | I |
| Process design (fit-to-standard) | I | A | C | C | R | R |
| Solution design & integrations | I | C | C | A | R | I |
| Data cleansing | I | A | C | C | C | R |
| UAT | I | A | C | C | C | R |
| Go/no-go | A | R | R | C | C | I |

## References

- The Open Group — TOGAF (principles): https://www.opengroup.org/togaf
- APQC Process Classification Framework: https://www.apqc.org/process-frameworks
- Business Architecture Guild: https://www.businessarchitectureguild.org/
- Wikipedia — Responsibility assignment matrix (RACI): https://en.wikipedia.org/wiki/Responsibility_assignment_matrix
- Related: [README.md](README.md), [consultancy-practice.md](consultancy-practice.md), [../03-project-management/README.md](../03-project-management/README.md)
