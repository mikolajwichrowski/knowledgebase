# Project Management Templates

> **TL;DR** Copy-paste Markdown templates for the core project artefacts: charter, RACI, risk register, status report, change request, retrospective, lessons learned, cutover/go-live checklist, plus RAID log, decision log, stakeholder register and kickoff agenda. Replace `<placeholders>`; delete rows that don't apply. Background and guidance for each lives in the topic files linked from each template.

---

## 1. Project charter

Guidance: [initiation.md](initiation.md#3-project-charter)

### Project charter — `<Project name>`

| Field | Value |
|---|---|
| Project ID | `<ID>` |
| Version / date | `<v1.0 / yyyy-mm-dd>` |
| Sponsor (accountable) | `<name, role>` |
| Project manager | `<name>` |
| Steering committee | `<names/roles>` |
| Business case reference | `<link>` |

**1. Purpose / problem statement**
`<Why does this project exist? What problem or opportunity? Evidence?>`

**2. Objectives and success criteria**

| # | Objective (SMART) | Measure | Baseline | Target | Measured by / when |
|---|---|---|---|---|---|
| O1 | `<objective>` | `<KPI>` | `<value>` | `<value>` | `<owner, date>` |
| O2 | | | | | |

**3. Scope**

| In scope | Out of scope |
|---|---|
| `<item>` | `<item>` |
| | |

**4. Key deliverables**

| # | Deliverable | Acceptance criteria | Accepted by |
|---|---|---|---|
| D1 | | | |

**5. Milestones**

| Milestone | Target date | Owner |
|---|---|---|
| Charter approved | | Sponsor |
| Design sign-off | | |
| Go-live | | |
| Project closure | | |

**6. Budget**

| Item | Amount |
|---|---|
| External costs | |
| Internal effort (FTE/days) | |
| Contingency | |
| Management reserve (held by sponsor) | |
| **Total** | |

**7. Constraints and priorities (flexibility matrix)**

| Dimension | Least flexible | Moderate | Most flexible |
|---|---|---|---|
| Scope | | | |
| Schedule | | | |
| Cost | | | |
| Quality | | | |

**8. Assumptions**
- `<assumption — owner — validate by>`

**9. Top risks**

| Risk | P | I | Response | Owner |
|---|---|---|---|---|
| | | | | |

**10. Governance and authority**
- Steering committee cadence: `<monthly>`
- PM authority: `<budget/change limits>`
- Tolerances: time `<±x weeks>`, cost `<±x%>`, scope `<…>`
- Escalation path: `<team → PM → steering → sponsor>`

**11. Key stakeholders**: see stakeholder register (§12).

**12. Approval**

| Role | Name | Signature / date |
|---|---|---|
| Sponsor | | |
| Project manager | | |
| `<Other>` | | |

---

## 2. RACI matrix

Guidance: [initiation.md](initiation.md#5-responsibility-assignment-matrices). Rules: exactly one **A** per row; at least one **R**.

| Activity / deliverable | Sponsor | Steering cttee | PM | Architect | Process owner | Key user | Vendor | IT ops | PMO |
|---|---|---|---|---|---|---|---|---|---|
| Business case | A | C | R | C | C | I | I | I | C |
| Project plan | I | C | A/R | C | C | I | C | C | S |
| Requirements / design | I | I | C | R | A | R | R | C | I |
| Build / configuration | I | I | I | A | C | C | R | C | I |
| Test strategy | I | I | A | C | C | C | R | C | I |
| UAT | I | I | C | C | A | R | S | I | I |
| Data migration sign-off | I | I | C | C | A | R | R | C | I |
| Training | I | I | C | I | A | R | S | I | I |
| Go/no-go decision | A | R | R | C | C | I | C | C | I |
| Handover to support | I | I | A | C | C | I | R | R | I |
| `<activity>` | | | | | | | | | |

R = Responsible, A = Accountable, C = Consulted, I = Informed, S = Support.

---

## 3. Risk register

Guidance: [risk-management.md](risk-management.md#7-risk-register)

| ID | Date raised | Category | Risk statement (Because … there is a risk that … leading to …) | Threat/Opp | P (1–5) | I (1–5) | Score | Proximity | Owner | Strategy | Actions (owner, due) | Trigger | Residual P×I | Contingency | Status | Last review |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| R-001 | | Technical | | Threat | | | | | | Mitigate | | | | | Open | |
| R-002 | | Organisational | | Threat | | | | | | Avoid | | | | | Open | |
| R-003 | | Commercial | | Opportunity | | | | | | Enhance | | | | | Open | |

Scoring: 1–4 🟩 monitor · 5–12 🟨 active response · 15–25 🟥 steering committee attention.

---

## 4. Status report

Guidance: [governance-communication-and-quality.md](governance-communication-and-quality.md#3-status-reporting)

### Status report — `<Project>` — period ending `<date>`

| Overall | Trend | PM | Sponsor |
|---|---|---|---|
| 🟢 / 🟡 / 🔴 | ↑ / → / ↓ | `<name>` | `<name>` |

**Executive summary**
- `<what happened>`
- `<what's next>`
- `<what is needed from the steering committee>`

**Status by dimension**

| Dimension | RAG | Trend | Comment |
|---|---|---|---|
| Schedule | | | |
| Cost | | | |
| Scope | | | |
| Quality | | | |
| Resources | | | |
| Risks / issues | | | |
| Benefits | | | |

**Milestones**

| Milestone | Baseline | Forecast | Actual | Status |
|---|---|---|---|---|
| | | | | |

**Accomplished this period**
- 

**Planned next period**
- 

**Top risks & issues**

| ID | Description | RAG | Owner | Action / due |
|---|---|---|---|---|
| | | | | |

**Decisions required**

| # | Decision | Options | Recommendation | Needed by |
|---|---|---|---|---|
| | | | | |

**Financials**

| Budget (BAC) | Actual to date (AC) | Earned value (EV) | Forecast (EAC) | Variance (BAC − EAC) | CPI | SPI |
|---|---|---|---|---|---|---|
| | | | | | | |

**Change requests**

| CR | Title | Impact | Status |
|---|---|---|---|
| | | | |

---

## 5. Change request

Guidance: [governance-communication-and-quality.md](governance-communication-and-quality.md#5-change-control)

| Field | Value |
|---|---|
| CR ID | `CR-<nnn>` |
| Title | |
| Requested by / date | |
| Priority | Critical / High / Medium / Low |
| Category | Scope / schedule / cost / quality / contract / defect-related |
| Description of change | |
| Reason / business justification | |
| Consequence of not doing it | |
| Options considered | 1. Do nothing 2. … 3. … |

**Impact assessment**

| Dimension | Impact |
|---|---|
| Scope (deliverables added/removed) | |
| Schedule (days; milestones; critical path?) | |
| Cost (effort, €; source: contingency / new funding) | |
| Quality / testing | |
| Risks (new/changed) | |
| Benefits | |
| Other projects / contracts / SOW | |
| Resources | |

| Field | Value |
|---|---|
| Recommendation | |
| Impact assessed by / date | |
| Decision | Approved / Rejected / Deferred / Approved with conditions |
| Decision authority | PM / CCB / Steering committee |
| Decided by / date | |
| Conditions | |
| Baselines updated (plan, budget, scope, contract) | ☐ |
| Communicated to stakeholders | ☐ |
| Closed date | |

### Change request log

| CR | Title | Raised | Requester | Cost impact | Time impact | Status | Decision date | Decided by |
|---|---|---|---|---|---|---|---|---|
| | | | | | | | | |

---

## 6. Retrospective

Guidance: [governance-communication-and-quality.md](governance-communication-and-quality.md#8-lessons-learned-and-retrospectives) · Facilitation: [../09-leadership-teams-facilitation/README.md](../09-leadership-teams-facilitation/README.md)

### Retrospective — `<team>` — `<sprint/phase>` — `<date>`

**Facilitator:** `<name>` · **Participants:** `<names>` · **Format:** `<Start/Stop/Continue | 4Ls | Sailboat>`

**1. Set the stage** — check-in question: `<e.g., one word for this sprint>`

**2. Review previous actions**

| Action | Owner | Done? | Effect |
|---|---|---|---|
| | | | |

**3. Gather data**

| Went well / Keep | Didn't go well / Stop | Ideas / Start | Puzzles / Questions |
|---|---|---|---|
| | | | |

Key data: velocity/throughput `<…>`, sprint goal met `<Y/N>`, escaped defects `<n>`, cycle time `<…>`

**4. Generate insights** — top themes (dot-voted) and root causes (5 Whys):

| Theme | Votes | Root cause |
|---|---|---|
| | | |

**5. Decide what to do** (max 1–3 actions)

| Action (specific, measurable) | Owner | Due | How we'll know it worked |
|---|---|---|---|
| | | | |

**6. Close** — ROTI (return on time invested, 1–5): `<avg>`

---

## 7. Lessons learned

Guidance: [governance-communication-and-quality.md](governance-communication-and-quality.md#8-lessons-learned-and-retrospectives)

### Lessons log

| ID | Date | Phase / area | Category | What happened (context) | Impact | Root cause | Lesson / recommendation | Type (repeat / avoid) | Action to embed (template, checklist, estimate data) | Owner | Status |
|---|---|---|---|---|---|---|---|---|---|---|---|
| L-01 | | Data migration | Process | Mock 1 started late due to missing source access | 3-week delay | Access request not in plan | Request source system access in initiation | Avoid | Add to initiation checklist | PMO | Embedded |
| L-02 | | | | | | | | | | | |

### Lessons report (end of phase/project)

| Section | Content |
|---|---|
| Project summary | Objectives, scope, outcome |
| Performance vs baseline | Schedule, cost, scope, quality (planned vs actual and why) |
| Estimation data | Actual effort per deliverable type (feed into parametric models) |
| What went well | Practices to repeat |
| What didn't | Practices to avoid |
| Top 5 recommendations | For future projects, with owners |
| Process/template changes | Specific changes to the organisation's method |
| Distribution | Who receives it; where stored |

---

## 8. Cutover and go-live checklist

Guidance: [software-and-erp-delivery.md](software-and-erp-delivery.md#3-cutover-planning)

### 8.1 Cutover runbook

| ID | Phase | Task | Owner | Backup | Predecessor | Planned start | Duration | Planned end | Actual start | Actual end | Status | Verification / sign-off | Rollback step |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| C-001 | Pre-cutover | Announce change freeze | PM | | — | T−14d | 1h | | | | | Email sent | n/a |
| C-010 | Pre-cutover | Load final master data delta | Data lead | | C-001 | T−3d | 6h | | | | | Record counts reconciled | Delete load batch |
| C-020 | Execution | Set legacy to read-only | IT ops | | Go/no-go #1 | T0 18:00 | 0.5h | | | | | Login test | Restore permissions |
| C-030 | Execution | Extract open items from legacy | Data lead | | C-020 | T0 18:30 | 2h | | | | | Control totals | n/a |
| C-040 | Execution | Load open AR/AP, stock, balances | Data lead | | C-030 | T0 20:30 | 8h | | | | | Reconciliation signed by finance | Restore DB snapshot |
| C-050 | Execution | Switch interfaces to production | Integration lead | | C-040 | T+1 06:00 | 2h | | | | | Test messages OK | Re-point to legacy |
| C-060 | Execution | Business smoke tests | Key users | | C-050 | T+1 08:00 | 3h | | | | | Sign-off per process | — |
| C-070 | Decision | Final go/no-go (point of no return) | Sponsor | | C-060 | T+1 12:00 | 0.5h | | | | | Decision logged | Execute rollback plan |
| C-080 | Go-live | Open system to all users; send comms | PM / change lead | | C-070 | T+1 13:00 | 1h | | | | | Comms sent | — |

### 8.2 Go/no-go criteria

| # | Area | Criterion | Evidence | Owner | Status (Go / Conditional / No-go) |
|---|---|---|---|---|---|
| 1 | Testing | UAT signed off; 0 open sev 1/2 | UAT report | Test manager | |
| 2 | Data | Dress rehearsal reconciled; final load within thresholds | Reconciliation report | Data owners | |
| 3 | Cutover | Rehearsal within window | Rehearsal log | Cutover manager | |
| 4 | Integrations | End-to-end tested with all partners | Test evidence | Integration lead | |
| 5 | Infrastructure | Prod sized, monitored, backup/DR tested | Ops checklist | IT ops | |
| 6 | Security | Roles provisioned; SoD reviewed | Access report | Security | |
| 7 | Training | ≥ 90% users trained | LMS report | Change lead | |
| 8 | Support | Hypercare rota, service desk ready, vendor support confirmed | Support plan | Support lead | |
| 9 | Business continuity | Manual fallbacks for critical processes | BCP | Process owners | |
| 10 | Compliance | Tax / e-invoicing / statutory ready | Confirmation | Finance | |
| 11 | Rollback | Plan documented; decision deadline agreed | Rollback plan | Cutover manager | |

### 8.3 Go-live day checklist

- [ ] Go/no-go decision recorded (decision log)
- [ ] Legacy read-only / access removed
- [ ] Final loads reconciled and signed off
- [ ] Interfaces live; certificates and credentials valid
- [ ] Batch jobs scheduled and running
- [ ] Number ranges, open periods, exchange rates set
- [ ] Output (print, labels, e-mail, e-invoices) verified
- [ ] User access verified per role
- [ ] Smoke tests on critical end-to-end processes passed
- [ ] Monitoring and alerting active
- [ ] War room staffed; hypercare rota published
- [ ] "We are live" communication sent with support contacts and known issues
- [ ] External parties (customers, suppliers, banks) informed of changes

### 8.4 Hypercare exit checklist

- [ ] No open sev 1; sev 2 with plan
- [ ] Incident volume stable/declining ≥ 2 weeks
- [ ] First month-end close completed
- [ ] Business KPIs at or above baseline
- [ ] Knowledge transfer and documentation handed over
- [ ] Support SLAs and AMS contract active
- [ ] Sponsor / process owner sign-off

---

## 9. RAID log

Guidance: [risk-management.md](risk-management.md#9-raid-log)

| Type | ID | Description | Owner | Raised | Due / review | Priority | Status | Linked items | Notes |
|---|---|---|---|---|---|---|---|---|---|
| Risk | R-001 | | | | | | Open | | |
| Assumption | A-001 | | | | Validate by | | Open | | If false → R-xxx |
| Issue | I-001 | | | | Resolve by | High | In progress | | |
| Dependency | D-001 | `<we depend on X from team Y>` | | | Needed by | | On track | | |

---

## 10. Decision log

Guidance: [governance-communication-and-quality.md](governance-communication-and-quality.md#6-decision-log)

| ID | Date | Decision | Context / problem | Options considered | Rationale | Decided by | Consulted | Impact (scope/time/cost) | Reversible? | Links |
|---|---|---|---|---|---|---|---|---|---|---|
| D-001 | | | | | | | | | One-way / two-way door | |

---

## 11. Stakeholder register

Guidance: [initiation.md](initiation.md#4-stakeholder-analysis)

| Name | Role / org | Category | Interest (H/M/L) | Power (H/M/L) | Grid quadrant | Attitude | Current engagement | Desired engagement | Key concerns | Engagement actions | Owner |
|---|---|---|---|---|---|---|---|---|---|---|---|
| | | | | | Manage closely | | | | | | |

---

## 12. Kickoff agenda

Guidance: [initiation.md](initiation.md#9-kickoff-meeting)

| # | Item | Presenter | Time |
|---|---|---|---|
| 1 | Welcome & introductions | Sponsor | 10' |
| 2 | Why: context, problem, objectives, benefits | Sponsor | 20' |
| 3 | What: scope in/out, deliverables, success criteria | PM | 20' |
| 4 | How: approach, phases, milestones | PM | 20' |
| 5 | Who: governance, roles, RACI | PM | 15' |
| 6 | Plan: timeline and next 4–6 weeks | PM | 15' |
| 7 | Risks, assumptions, dependencies | PM / team | 15' |
| 8 | Ways of working: cadence, tools, comms, DoD | PM / Scrum Master | 15' |
| 9 | Team agreements | Team | 15' |
| 10 | Q&A | All | 15' |
| 11 | Actions & next steps | PM | 10' |

---

## 13. Weekly PM checklist

- [ ] Update schedule progress; check critical path and milestone forecasts
- [ ] Review RAID log: new items, overdue actions, closed items
- [ ] Review budget: actuals, commitments, EAC
- [ ] Check change requests pending decision
- [ ] Check team capacity / upcoming absences
- [ ] Vendor deliverables and invoices on track
- [ ] Stakeholder touchpoints done (sponsor 1:1, key stakeholders)
- [ ] Status report issued
- [ ] Decisions logged
- [ ] Lessons captured

## References

- PMI templates & standards: https://www.pmi.org/
- PRINCE2 management products (Wikipedia overview): https://en.wikipedia.org/wiki/PRINCE2
- Atlassian Team Playbook: https://www.atlassian.com/team-playbook
- Retromat: https://retromat.org/
- Section index: [README.md](README.md)
