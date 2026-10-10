# 03 — Project Management

> **TL;DR** Reference for running projects with predictive, hybrid and adaptive approaches: foundations and lifecycle selection, frameworks and standards (PMBOK, PRINCE2, IPMA, ISO, CCPM, MSP, DSDM), initiation, estimation and scheduling, cost and earned value, risk, quality, governance and communication, procurement and contracts, and ERP/software delivery (migration, cutover, hypercare, failure and recovery). Agile frameworks themselves live in [../04-agile-kanban-scrum/README.md](../04-agile-kanban-scrum/README.md).

## Files

| File | Description |
|---|---|
| [foundations.md](foundations.md) | Project vs program vs portfolio vs operations, iron triangle, lifecycle types, Cynefin, Stacey matrix, PMO types, roles |
| [frameworks-and-standards.md](frameworks-and-standards.md) | PMBOK 7 (12 principles, 8 domains), PMBOK 6 process map, PRINCE2 7th ed., PRINCE2 Agile, IPMA ICB4, ISO 21502, Waterfall/V-model, Critical Chain, MSP, P3O, DSDM/AgilePM |
| [initiation.md](initiation.md) | Business case, charter, stakeholder analysis (power/interest, salience), RACI/RASCI/DACI, scope statement, WBS and 100% rule, assumptions/constraints, kickoff |
| [planning-and-scheduling.md](planning-and-scheduling.md) | Estimation (analogous, parametric, PERT, planning poker, #NoEstimates, reference class, Monte Carlo), Cone of Uncertainty, CPM worked example, dependencies, Gantt, resource leveling, buffers |
| [cost-and-value.md](cost-and-value.md) | Budgeting and reserves, full EVM formula set with worked example, earned schedule, ROI/NPV/IRR/payback example, TCO |
| [risk-management.md](risk-management.md) | ISO 31000, risk process, P-I matrix, EMV, decision trees, Monte Carlo, response strategies, risk register, issue management, RAID log, pre-mortem |
| [governance-communication-and-quality.md](governance-communication-and-quality.md) | Governance models, steering committee, tolerances, communication plan, RAG status reporting, change control, decision logs, escalation, lessons learned, closure; quality management (PDCA, cost of quality, quality gates, Definition of Done, reviews, metrics) |
| [procurement-and-contracts.md](procurement-and-contracts.md) | Procurement process, fixed price vs T&M vs capped vs agile contracts, SOW, SLAs, weighted vendor selection, contract clauses |
| [software-and-erp-delivery.md](software-and-erp-delivery.md) | SAP Activate, Sure Step / Success by Design, fit-to-standard, data migration, cutover, go-live, hypercare, release management, why IT projects fail (CHAOS, Flyvbjerg, Brooks), recovery, tools |
| [templates.md](templates.md) | Markdown templates: charter, RACI, risk register, status report, change request, retrospective, lessons learned, cutover/go-live checklist, RAID, decision log, stakeholder register, kickoff |
| [resources.md](resources.md) | Standards, books, papers, certifications, websites, tool links |

## Quick paths

| Need | Go to |
|---|---|
| Choosing predictive vs agile vs hybrid | [foundations.md §3–5](foundations.md#3-project-lifecycle-types) |
| Starting a new project this week | [initiation.md](initiation.md) → [templates.md §1, §2, §12](templates.md) |
| Building a defensible estimate | [planning-and-scheduling.md §2](planning-and-scheduling.md#2-estimation-techniques) |
| Explaining budget overrun to a steering committee | [cost-and-value.md §2.6](cost-and-value.md#26-worked-example) |
| Setting up a risk register | [risk-management.md](risk-management.md) → [templates.md §3](templates.md#3-risk-register) |
| Planning an ERP go-live | [software-and-erp-delivery.md §2–4](software-and-erp-delivery.md#2-data-migration) → [templates.md §8](templates.md#8-cutover-and-go-live-checklist) |
| Choosing a contract model for a vendor | [procurement-and-contracts.md §2](procurement-and-contracts.md#2-contract-types) |
| Rescuing a failing project | [software-and-erp-delivery.md §7](software-and-erp-delivery.md#7-project-recovery) |

## Key formulas cheat sheet

| Topic | Formula |
|---|---|
| PERT mean | E = (O + 4M + P) / 6 |
| PERT std dev | σ = (P − O) / 6; σ_path = √Σσ² |
| Total float | TF = LS − ES = LF − EF |
| Cost / schedule variance | CV = EV − AC; SV = EV − PV |
| CPI / SPI | CPI = EV / AC; SPI = EV / PV |
| EAC (typical) | BAC / CPI |
| EAC (atypical) | AC + (BAC − EV) |
| EAC (CPI × SPI) | AC + (BAC − EV) / (CPI × SPI) |
| ETC / VAC | ETC = EAC − AC; VAC = BAC − EAC |
| TCPI | (BAC − EV) / (BAC − AC) |
| NPV | Σ CF_t / (1 + r)^t |
| ROI | (Benefits − Costs) / Costs |
| Risk exposure (EMV) | Probability × Impact |
| Communication channels | n(n − 1) / 2 |

## Related sections

- [../01-ux-ui/README.md](../01-ux-ui/README.md)
- [../02-product-management/README.md](../02-product-management/README.md)
- [../04-agile-kanban-scrum/README.md](../04-agile-kanban-scrum/README.md)
- [../05-software-architecture/README.md](../05-software-architecture/README.md)
- [../06-cloud-architecture/README.md](../06-cloud-architecture/README.md)
- [../07-data-ai-architecture/README.md](../07-data-ai-architecture/README.md)
- [../08-engineering/README.md](../08-engineering/README.md)
- [../09-leadership-teams-facilitation/README.md](../09-leadership-teams-facilitation/README.md)
- [../10-laws-principles-mental-models/README.md](../10-laws-principles-mental-models/README.md)
- [../11-enterprise-architecture-business/README.md](../11-enterprise-architecture-business/README.md)

## References

- PMI: https://www.pmi.org/
- PRINCE2 / AXELOS: https://www.axelos.com/
- IPMA: https://www.ipma.world/
- ISO 21502:2020: https://www.iso.org/standard/74947.html
- Project management (Wikipedia): https://en.wikipedia.org/wiki/Project_management
