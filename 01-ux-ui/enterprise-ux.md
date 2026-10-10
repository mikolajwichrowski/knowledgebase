# Enterprise, B2B & ERP UX

> **TL;DR** — Enterprise users are **captive, expert, high-frequency and domain-driven**; the buyer is often not the user. Optimise for **efficiency, accuracy and throughput** over engagement and first-use delight — while still supporting occasional users (managers approving monthly, employees doing annual reviews). Key levers: role-based workspaces, dense but scannable data grids, keyboard support, bulk actions, saved views, sensible defaults from master data, clear statuses and audit trails, robust error prevention, permissions-aware UI, and integration with Excel/Outlook/Teams. Research in context (contextual inquiry) — workarounds reveal requirements.

Related: [UI patterns (tables, forms)](ui-patterns.md) · [Information architecture](information-architecture.md) · [Metrics for internal tools](metrics.md#7-ux-metrics-for-enterprise--internal-tools) · [Enterprise architecture](../11-enterprise-architecture-business/README.md) · [Software architecture](../05-software-architecture/README.md)

---

## 1. How enterprise UX differs from consumer UX

| Dimension | Consumer | Enterprise / B2B / ERP |
|-----------|----------|-----------------------|
| Choice to use | Voluntary | Mandatory (captive) — poor UX shows up as errors, workarounds, support cost, attrition |
| Buyer vs user | Same person | Different (procurement, IT, CFO buy; clerks use) — demos sell, daily use matters |
| Usage frequency | Occasional | Daily, hours a day (power users) + occasional (managers, employees) |
| Expertise | Novice-friendly | Domain experts; deep jargon is real vocabulary |
| Task complexity | Simple, linear | Complex, multi-step, exceptions, long-running processes, multi-actor workflows |
| Data | Small | Large volumes, many attributes, relations |
| Success metric | Engagement, conversion | Efficiency, accuracy, throughput, compliance, cost |
| Environment | Personal device | Multiple monitors, shop floor, warehouse scanners, call centres, Citrix/VDI, locked-down browsers, old laptops at 125% scaling |
| Customisation | Limited | Configurable per tenant (fields, workflows, roles, languages) |
| Release & change | Fast | Change management, training, release notes, regulated validation |
| Regulation | GDPR | + sector rules (SOX, GxP, payroll law, tax, audit), EN 301 549 in public procurement |
| Legacy | — | Decades-old processes and systems, migrations, integrations |

Sources: <https://www.nngroup.com/articles/> (enterprise/application design topic) · <https://experience.sap.com/fiori-design-web/>

---

## 2. User types in enterprise systems

| Role archetype | Characteristics | Design focus |
|---------------|-----------------|--------------|
| **Power user / back-office specialist** (payroll admin, buyer, accountant) | Hours per day, expert, high volume | Density, keyboard, bulk actions, shortcuts, saved views, minimal clicks, no forced wizards |
| **Occasional transactional user** (manager approving, employee requesting leave) | Infrequent, goal-driven, low training | Self-explanatory, task-focused flows, mobile, notifications with deep links, plain language |
| **Executive / viewer** | Consumes summaries | Dashboards, exceptions, export, mobile |
| **Administrator / configurator** | Sets up system, roles, rules | Safe configuration, preview, validation, audit, documentation |
| **External / partner** (supplier portal, customer portal) | Low loyalty, many systems | Consumer-like simplicity, onboarding, branding |
| **Auditor / compliance** | Read-only, traceability | History, audit trails, exports, filtering by time |

Design one system, multiple **role-based experiences** (SAP Fiori principle: role-based, adaptive, simple, coherent, delightful).

---

## 3. Core enterprise design principles

1. **Efficiency for experts, learnability for occasional users** — support both through progressive disclosure and multiple paths (mouse + keyboard + search + shortcuts).
2. **Get users to the work, not to the module** — role-based home with work queues ("12 timesheets to approve"), recent records, alerts.
3. **Object-based consistency** — every business object (Employee, Order, Invoice) has a consistent page structure: header (identity, status, key facts, primary actions) → tabs/sections → related objects → activity/history.
4. **Status visibility** — explicit lifecycle states (Draft → Submitted → Approved → Posted → Paid) with badges, timelines, and who/when.
5. **Error prevention over error messages** — validate against master data, defaults from context, constraints, duplicate detection, warnings with override for soft rules.
6. **Bulk over single** — anything done 50× a day needs a bulk path.
7. **Data integrity & trust** — show data freshness, source, calculation explanations ("How is this calculated?"), audit trail.
8. **Configurability without chaos** — saved views, personalised columns, user preferences; tenant configuration with governance.
9. **Interoperability** — export/import Excel, copy-paste ranges, deep links, email/Teams notifications, API-first.
10. **Performance as UX** — Doherty threshold matters most for high-frequency tasks; server-side filtering; avoid full page reloads.

---

## 4. Patterns specific to ERP/HR/BRP-style systems

### 4.1 Object page (record detail)

```
┌────────────────────────────────────────────────────────────────────┐
│ ‹ Employees / J. de Vries                                          │
│ Jan de Vries  ● Active   Emp. no. 104332   Finance · Controller     │
│ [Edit] [Request change ▾]                               [⋯]        │
├── Overview ── Contract ── Pay ── Absence ── Documents ── History ──┤
│ Key facts card │ Current contract card │ Open tasks card            │
│ ...                                                                 │
└────────────────────────────────────────────────────────────────────┘
```

- Header: identifier (human name + system ID, copyable), status, key attributes, primary actions.
- Tabs/anchored sections for facets; preserve tab in URL.
- **Effective dating** (HR, prices, org structures): show "as of" date selector, history timeline, future-dated changes clearly marked, "valid from/to" columns. Typical in HR/payroll and Dutch BRP-like registries (date of entry vs date of validity — *datum ingang geldigheid* vs *datum opneming*).
- Related objects as linked lists with counts.
- Activity/audit tab: who changed what, when, old → new values.

### 4.2 List report / worklist

Filter bar + data table + bulk actions + saved views (SAP Fiori "List Report" floorplan; Fluent "DetailsList"; Carbon data table). See [ui-patterns.md](ui-patterns.md#2-tables--data-grids-enterprise).

### 4.3 Work queues & approvals

- Inbox-style list of tasks: requester, type, key data inline (dates, amount), age/SLA, priority.
- Approve/reject inline for simple items; detail panel for complex ones; bulk approve with summary of what's being approved.
- Rejection requires reason; delegation/substitution while on leave; escalation rules visible.
- Notifications with deep links to the exact item; approve from email/Teams (actionable messages) for occasional approvers.

### 4.4 Data entry at volume

- Keyboard-first: logical tab order, Enter to move/submit where conventional, shortcuts for save (`Ctrl+S`), new line, duplicate line.
- Grid entry for line items (order lines, journal entries, timesheets) with Excel-like behaviour: arrow navigation, copy-paste ranges, fill down, inline validation per cell, totals live.
- Smart defaults: last used values, context (current period, user's department), master data lookups (enter postcode + house number → address via BAG; KvK number → company data; IBAN → BIC).
- Autocomplete on codes **and** names (cost centre "4100" or "Marketing").
- Duplicate detection ("An employee with this BSN/birth date already exists").
- Draft/autosave; concurrent edit handling (optimistic locking with clear conflict UI: "Changed by A. Jansen 2 min ago — Review changes").
- Hard vs soft validation: **errors** block (invalid IBAN); **warnings** allow override with reason (salary above scale max).

### 4.5 Batch processes & long-running jobs

- Payroll runs, imports, period closes, report generation.
- Preview/simulate before committing ("dry run" with differences report).
- Progress with counts; run in background; notify on completion; downloadable logs; partial failure handling; ability to reverse/rollback where business rules allow.

### 4.6 Imports & exports

- Import wizard: download template → upload → map columns (auto-match by header) → validate with row-level errors → preview → import → summary report.
- Exports: respect current filters/columns; Excel-friendly types (dates as dates, numbers as numbers, no thousand separators as text); large exports async.

### 4.7 Reporting

- Standard reports with parameters + scheduled delivery; ad-hoc analysis via BI tools; drill-through from dashboards to records.
- Clearly define KPIs (formula, source, refresh time).

### 4.8 Permissions & multi-tenancy in the UI

- Hide what a role can never access; disable (with explanation) what's temporarily unavailable (status-based, e.g. "Can't edit: payroll period closed").
- Show *why* data is masked (field-level security: "Salary hidden — HR only").
- "View as role" for admins to test configuration.
- Multi-company/tenant switcher with clear current context (company name in header, colour cue) to prevent posting to the wrong entity.
- Impersonation / support access visibly indicated and audited.

### 4.9 Audit, compliance & privacy

- Audit trails: immutable, filterable, exportable.
- GDPR: data minimisation, purpose limitation, retention indicators, subject access/export, consent logs; mask BSN/IBAN partially (`NL** **** **** **43 00`) with reveal-on-demand + logging.
- Four-eyes principle (maker–checker) for sensitive changes (bank account change → second approval).
- Electronic signatures and timestamps where required.

### 4.10 Configuration UIs

- Rules engines and workflow designers need visual representations (flow diagrams, decision tables) plus testing/preview ("simulate with employee X").
- Versioned configuration with effective dates and rollback.
- Guardrails: validation of rule conflicts, impact analysis ("affects 340 employees").

---

## 5. Density & layout for productivity apps

- Offer density modes; default to "standard" with compact option for experts.
- Use full width; side navigation collapsible; avoid wasted whitespace that forces scrolling in data-heavy screens — *but* density without hierarchy is clutter.
- Multi-pane layouts (list | detail | context) on wide screens (≥ 1440px), stacking on smaller.
- Sticky headers/toolbars/totals.
- Typical base size 14px in dense apps (Fluent/Carbon productive use 14px body), 13px minimum for secondary text, never below 12px.
- Test on the real fleet: 1366×768 and 1920×1080 at 125–150% Windows scaling, Citrix, Edge/Chrome.

---

## 6. Keyboard & power-user features

| Feature | Notes |
|---------|-------|
| Global shortcuts | `Ctrl/Cmd+K` command palette, `/` focus search, `?` shortcut help, `g` + key navigation (GitHub/Gmail style — must be disable-able per WCAG 2.1.4) |
| Form shortcuts | `Ctrl+S` save, `Ctrl+Enter` submit, `Esc` cancel |
| Grid navigation | Arrows, Home/End, `Ctrl+Home`, `F2`/Enter edit, `Space` select row, `Shift+click` range |
| Saved views & favourites | Personal and shared |
| Recent items | Global, cross-module |
| Bulk edit & multi-select | |
| Templates & duplication | "Copy last week's timesheet", "Duplicate order" |
| Macros/automation | Rules, scheduled actions |
| Undo | For bulk operations where feasible |

---

## 7. Legacy modernisation & redesign

- **Don't break muscle memory without reason** (Jakob's Law applies to the old system!). Map current keyboard flows; keep or improve them.
- Run contextual inquiry to discover *why* workarounds exist before removing "weird" features.
- Incremental strangler-fig modernisation (see [../05-software-architecture/README.md](../05-software-architecture/README.md)) also applies to UX: module by module, with a consistent design system bridging old and new.
- Offer side-by-side or opt-in beta; collect feedback in-app; measure time-on-task before and after.
- Training & change management: release notes, in-app "What's new", champions network, short videos (see [../03-project-management/README.md](../03-project-management/README.md) for change management).
- Expect a temporary productivity dip after redesign (J-curve); measure after the learning period.

---

## 8. Enterprise design systems & floorplans

| System | Vendor | Notable for enterprise |
|--------|--------|-----------------------|
| **SAP Fiori** | SAP | Floorplans: List Report, Object Page, Overview Page, Worklist, Analytical List Page, Wizard; launchpad tiles; role-based |
| **Fluent 2** | Microsoft | Dynamics 365/M365 consistency; DetailsList/DataGrid; Teams integration |
| **Carbon** | IBM | Data tables, productive vs expressive modes, dense UI, strong a11y |
| **Salesforce Lightning (SLDS)** | Salesforce | Record pages, list views, utility bar; origin of "design tokens" term (2014) |
| **Atlassian Design System** | Atlassian | Collaboration tools, dense issue views |
| **Oracle Redwood** | Oracle | Fusion apps |
| **Workday Canvas** | Workday | HR/finance SaaS |
| **ServiceNow Horizon** | ServiceNow | ITSM workflows |
| **Ant Design** | Ant Group | Rich enterprise component set (React), popular in admin UIs |
| **PrimeVue/PrimeReact, Vuetify, Quasar, MUI X, AG Grid** | OSS/commercial | Component libraries with data grids for React/Vue |

Sources: <https://experience.sap.com/fiori-design-web/> · <https://www.lightningdesignsystem.com/> · <https://ant.design/> · <https://atlassian.design/>

---

## 9. Research in enterprise contexts

- Access is the bottleneck: work via customer success, account managers, internal sponsors; build a customer advisory board / sponsor users (IBM).
- **Contextual inquiry** is the highest-value method: observe actual work, collect artefacts (spreadsheets, printouts, sticky notes).
- Shadow support and training sessions; analyse support tickets and training materials.
- Usability test with real data (anonymised) and realistic volumes; experts test differently (fast, keyboard).
- Measure task time with experts; KLM for comparisons.
- Mind the **buyer–user gap**: research both; demo requirements (checkbox features) vs usage requirements (efficiency).
- Works councils (ondernemingsraad in NL) may need to be consulted for research involving employee monitoring/analytics.

## 10. Enterprise UX checklist

- [ ] Role-based home showing the user's work (tasks, approvals, alerts)
- [ ] Consistent object page structure with status, key facts, actions, history
- [ ] Data tables: sort, filter, saved views, column chooser, density, bulk actions, export
- [ ] Keyboard operable end-to-end; shortcuts discoverable
- [ ] Defaults and lookups reduce typing; autocomplete by code and name
- [ ] Hard vs soft validation; duplicate detection; draft/autosave; conflict handling
- [ ] Long-running jobs: preview, progress, background, notification, logs
- [ ] Permissions reflected (hidden/disabled with reason, masked fields)
- [ ] Company/tenant context always visible
- [ ] Audit trail and effective dating where relevant
- [ ] Deep links for every record/view; works with back button
- [ ] Performance budget for high-frequency tasks (INP ≤ 200 ms)
- [ ] Works at 1366×768 at 125% scaling and in the customer's browser/VDI
- [ ] WCAG 2.2 AA / EN 301 549 (public sector customers will require it)
- [ ] Release notes and in-app change communication

## Common mistakes

- Designing for the demo (buyer) instead of daily users.
- Consumer-style whitespace and oversized components that force scrolling through data.
- Wizards for expert, high-frequency tasks.
- Mirroring the database: one screen per table, field labels = column names.
- Ignoring occasional users (managers) who need simple, mobile approvals.
- Removing "ugly" legacy features that experts rely on.
- Building custom grids instead of using a mature grid component.
- Generic error messages for business rule violations ("Validation failed").
- Infinite scroll and modals stacked on modals in record-heavy apps.

## References

- NN/g articles (application design, enterprise topics): <https://www.nngroup.com/articles/>
- SAP Fiori Design Guidelines: <https://experience.sap.com/fiori-design-web/>
- Salesforce Lightning Design System: <https://www.lightningdesignsystem.com/>
- Fluent 2: <https://fluent2.microsoft.design/>
- Carbon Design System: <https://carbondesignsystem.com/>
- Atlassian Design System: <https://atlassian.design/>
- Ant Design: <https://ant.design/>
- Oracle Redwood: <https://redwood.oracle.com/>
- AG Grid: <https://www.ag-grid.com/>
- Rosenfeld Media — Enterprise UX (book series/community): <https://rosenfeldmedia.com/>
- Object-Oriented UX: <https://ooux.com/>
- Keystroke-level model: <https://en.wikipedia.org/wiki/Keystroke-level_model>
