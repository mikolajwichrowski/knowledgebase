# UI Patterns Catalogue

> **TL;DR** — Proven solutions for recurring interface problems. Forms: single column, labels above fields, validate on blur (not on every keystroke), never placeholder-as-label, accept flexible input. Tables: right-align numbers, sticky headers, sorting/filtering/bulk actions, density options. Dashboards: answer specific questions, maximise data-ink ratio (Tufte), one screen (Stephen Few). Every component needs empty, loading, error, partial and success states. Prefer **undo over confirm** for reversible actions; use modals only for short, blocking, focused tasks; drawers/side panels for contextual detail.

Related: [Enterprise UX](enterprise-ux.md) · [UX writing](ux-writing-and-dark-patterns.md) · [Accessibility](accessibility.md) · [Design systems](design-systems-and-figma.md)

---

## 1. Forms

### 1.1 Layout

| Guideline | Rationale / evidence |
|-----------|---------------------|
| **Single column** | Faster completion, fewer missed fields (CXL 2014: single-column completed ~15 s faster); zig-zag eye path in multi-column. Exceptions: short related fields (City + Postcode, First + Last name, Date + Time) |
| **Labels above fields** | Fastest scanning & completion (Matteo Penzo eye-tracking 2006; Luke Wroblewski *Web Form Design* 2008); works for long labels, translations, mobile. Left-aligned labels beside fields: OK for dense desktop forms where vertical space is scarce and users scan labels (ERP data entry) |
| **Group related fields** | `<fieldset>` + `<legend>`; headings; Gestalt proximity |
| **Field width reflects expected input** | Postcode short, street long (Baymard: affordance of length) |
| **Mark required or optional — whichever is rarer**, but be explicit | GOV.UK: mark optional "(optional)"; ensure asterisk explained & not colour-only |
| **Only ask what's needed** | Each field costs completion rate; question every field ("Do we need this now?") |
| **Primary button left-aligned with fields** (end of form) | Follows the eye path in single-column forms (Wroblewski); dialogs often right-align — follow your design system consistently |
| **Avoid "Reset/Clear" buttons** | Accidental data loss |
| **Don't disable submit until valid** | Users don't know why it's disabled; instead validate on submit and show errors (accessibility: disabled buttons aren't focusable and give no reason) |

### 1.2 Labels, hints, placeholders

- **Never use placeholder as the label**: disappears on input (memory load), low contrast, looks pre-filled, poor for screen readers (NN/g, 2014).
- **Floating labels** (Material) mitigate but have issues (small text, contrast, truncation); acceptable when well implemented.
- **Hint text** below the label (before the input) for format/requirements: "For example, 1234 AB". Link with `aria-describedby`.
- Use `<label for>` for every input; checkbox/radio labels clickable.

### 1.3 Input types & controls

| Data | Control | Notes |
|------|---------|------|
| 2–5 mutually exclusive options | **Radio buttons** (vertical) | Visible options; preselect a sensible default unless choice must be deliberate |
| 6–15 options | Select dropdown | Or radios if space |
| Many options (countries, employees, cost centres) | **Combobox / autocomplete** | Type-ahead; search by code and name |
| Binary setting effective immediately | **Toggle switch** | Settings that apply instantly |
| Binary within a form submitted later | **Checkbox** | "I agree", options |
| Multiple selection, few options | Checkboxes | |
| Multiple selection, many options | Multi-select combobox with chips, or dual listbox | |
| Number with small range | Stepper | |
| Range / approximate | Slider (+ text input for precision) | Sliders are imprecise |
| Dates — known & far (birth date) | **Three text fields (day, month, year)** or single masked text field | GOV.UK pattern; don't force calendar navigation |
| Dates — near future/scheduling | Date picker with text entry | Show day-of-week; respect locale (dd-mm-yyyy NL; ISO week numbers in EU business contexts) |
| Long text | Textarea, with character count if limited | |
| Money | Text input with currency prefix, `inputmode="decimal"`, locale formatting on blur | Don't use `type="number"` for amounts/IDs (spinners, scroll changes values, strips leading zeros) |
| Phone, postcode, IBAN, BSN | Text input with lenient parsing + validation | `inputmode="numeric"` where appropriate |
| File | File input / dropzone + button | Show constraints (type, size), progress, remove option |

### 1.4 Input masks

- **Use** for fixed-format input where formatting aids reading: credit card (`4111 1111 1111 1111`), IBAN groups, phone, dates.
- **Risks:** fight users' typing/pasting, break with international formats, confuse screen readers, cursor jumps.
- **Prefer:** lenient input + format on blur ("Postel's Law") over strict keystroke masks; if masking, allow paste of formatted/unformatted values, and don't mask fields with variable formats (names, international phone numbers).

### 1.5 Validation timing

| Timing | Use | Avoid |
|--------|-----|-------|
| **On submit** | Always (as a safety net); show an error summary + inline errors | Only on submit for long forms (late discovery) |
| **On blur (leaving field)** — "reward early, punish late" | Default for inline validation: validate when the user leaves the field; once a field is in error, re-validate on each keystroke to clear the error as soon as it's fixed | Showing errors on fields the user hasn't touched |
| **On input (keystroke)** | Password strength meters, character counters, username availability (debounced), clearing an existing error | Premature errors while typing ("invalid email" after 1 char) |
| **Server-side async** | Uniqueness, existence (employee number exists) — debounce 300–500 ms, show checking state | Blocking typing |

Luke Wroblewski (2009) inline validation study: on-blur inline validation → 22% increase in success rates, 22% fewer errors, 31% higher satisfaction, 42% faster completion vs submit-only.

### 1.6 Error messages in forms

- Show **next to the field** (below label/above input, or below input — be consistent), with icon + text, `aria-invalid="true"`, linked via `aria-describedby`.
- On submit with errors: **error summary** at the top with links to each field (GOV.UK pattern), focus moved to summary; page title prefixed "Error: ".
- Keep the user's input (never clear a form on error).
- Message formula: *what's wrong + how to fix* — "Enter a date after the start date (14-03-2025)" not "Invalid date". See [ux-writing-and-dark-patterns.md](ux-writing-and-dark-patterns.md#error-messages).

### 1.7 Long forms

- Break into steps (wizard) or sections with progress; save drafts automatically.
- "One thing per page" (GOV.UK) for public services with infrequent users; dense single page for expert repeated use.
- Conditional fields: reveal inline under the triggering radio/checkbox (progressive disclosure).
- Review/check-answers page before submission for consequential forms (WCAG 3.3.4).
- Autofill: `autocomplete` tokens; prefill from known data (Redundant Entry 3.3.7).

Sources: <https://www.nngroup.com/articles/web-form-design/> · <https://design-system.service.gov.uk/patterns/> · <https://baymard.com/blog> · <https://www.lukew.com/ff/entry.asp?883>

---

## 2. Tables & data grids (enterprise)

### 2.1 Table vs grid vs list

| Component | Use |
|-----------|-----|
| **Static table** (`<table>`) | Read-only comparison; small datasets |
| **Data table** | Sort, filter, paginate, select rows, row actions |
| **Data grid** (`role="grid"`) | Spreadsheet-like: cell navigation, inline editing, column resize/reorder/pin, virtualization (AG Grid, MUI X, TanStack Table + virtual, Handsontable, Syncfusion, DevExtreme, PrimeVue/PrimeReact DataTable) |
| **List / card list** | Heterogeneous items, mobile, few attributes |
| **Tree table** | Hierarchical rows (cost centres, BOMs, org units) |
| **Pivot table** | Ad-hoc analysis (dimensions × measures) |

### 2.2 Anatomy

```
┌───────────────────────────────────────────────────────────────────────────┐
│ Title (count)                         [Search…] [Filters ▾] [⚙ Columns] [Export] [+ New] │ ← toolbar
│ Active filters: [Status: Open ✕] [Dept: Finance ✕]  Clear all             │ ← filter chips
├─☐─┬─Name ▲────────┬─Department─┬─Start date─┬────Salary─┬─Status──┬─⋯─┤ ← sticky header
│ ☐ │ de Vries, J.  │ Finance    │ 01-03-2021 │  4.250,00 │ ● Active│ ⋯ │
│ ☑ │ Kowalski, A.  │ IT         │ 15-08-2023 │  5.100,00 │ ○ Leave │ ⋯ │
├───┴───────────────┴────────────┴────────────┴───────────┴─────────┴───┤
│ 1 selected  [Approve] [Export selected] [⋯]   ← bulk action bar        │
│ Rows per page: 50 ▾          1–50 of 1,284          ‹ 1 2 3 … 26 ›     │ ← pagination
└───────────────────────────────────────────────────────────────────────────┘
```

### 2.3 Guidelines

**Content & alignment**
- **Left-align text, right-align numbers** (and their headers) so decimals line up; use **tabular figures**; consistent decimals; thousands separators per locale (NL: `4.250,00`).
- Centre-align almost nothing (short status icons at most).
- Dates in a consistent, sortable, locale-aware format; consider relative time + absolute on hover for recency.
- Truncate long text with ellipsis + tooltip/expand; let users resize columns; wrap where reading matters.
- Avoid zebra stripes *and* borders together; zebra helps wide tables, subtle row hover helps all.
- Status as text + colour + icon (chip/badge), never colour-only.
- Put the identifying column (name/ID) first and freeze it when scrolling horizontally.

**Sorting**
- Clickable header with sort indicator (▲/▼) and `aria-sort`; one default sort (most useful, e.g. date desc); support multi-sort for experts (Shift+click).
- Sort the whole dataset server-side, not just the current page.

**Filtering**
- Quick search (global text) + structured filters (per column or filter panel).
- Show active filters as chips with remove + "Clear all"; show result count.
- **Saved views** (filters + columns + sort) — essential for enterprise; share views with team.
- Persist filters in the URL.
- Filter panel options: inline column filters (spreadsheet-like), toolbar dropdown filters (few facets), side filter panel (many facets), advanced query builder (AND/OR for experts).
- Apply immediately (small data/fast) vs "Apply" button (expensive queries, many filters) — be consistent.

**Selection & bulk actions**
- Checkbox column; header checkbox selects page; offer "Select all 1,284 results" explicitly (Gmail pattern).
- Contextual bulk action bar appears on selection with count.
- Bulk edit dialog for setting a field on many records; show results summary (succeeded/failed with reasons).
- Keep selection across pages only if clearly communicated.

**Row actions**
- Primary row action: click the row/name to open detail.
- Secondary: overflow menu `⋯` at row end; reveal-on-hover icons are faster for mouse users but must remain keyboard/touch accessible (visible on focus, always visible on touch).
- Inline editing: for quick, frequent edits of few fields; show edit affordance, Enter to save, Esc to cancel, validation inline, clear save state. Full edit in drawer/page for complex records.

**Density**
- Offer density settings: compact (row 32px), standard (40px), comfortable (48–56px). Experts prefer dense; touch needs comfortable.

**Columns**
- Column chooser (show/hide, reorder), pin/freeze, resize; remember per user.
- Default to the 5–8 most important columns; more on demand.

**Large data**
- Pagination (with page size choice 25/50/100) or virtual scrolling for grids; show total count (or "1,000+" if expensive).
- Server-side sort/filter/paginate for > ~1,000–10,000 rows.
- Sticky header and first column.
- Export (CSV/XLSX) of *current view with filters* — users will export to Excel anyway; make it good.
- Loading: skeleton rows on first load; subtle progress bar on refresh — keep previous data visible while refetching (don't blank the table).

**Expandable rows / master–detail**
- Expand row for secondary info; side panel (drawer) for detail while keeping list context; full page for complex editing.

**Empty states:** no data yet (CTA to create/import), no results for filters (show filters, "Clear filters"), no permission, error loading (retry).

**Accessibility:** real `<table>` with `<th scope="col">`, `<caption>`; for interactive grids follow APG grid pattern (arrow-key cell navigation); `aria-sort`; announce result counts via live region; virtualised grids need `aria-rowcount`/`aria-rowindex`.

Sources: <https://www.nngroup.com/articles/data-tables/> · <https://carbondesignsystem.com/components/data-table/usage/> · <https://www.w3.org/WAI/ARIA/apg/patterns/grid/>

---

## 3. Dashboards & data visualisation

### 3.1 Principles

- **Stephen Few** (*Information Dashboard Design*, 2006/2013): "A dashboard is a visual display of the most important information needed to achieve one or more objectives; consolidated and arranged on a single screen so the information can be monitored at a glance."
- **Edward Tufte** (*The Visual Display of Quantitative Information*, 1983):
  - **Data-ink ratio** = data-ink / total ink used in the graphic → maximise (within reason); erase non-data ink and redundant data ink.
  - Avoid **chartjunk** (3D, heavy gridlines, gradients, decorative images).
  - **Lie factor** = size of effect shown in graphic / size of effect in data → should be ≈ 1.
  - **Small multiples**, **sparklines** (word-sized graphics, Tufte 2006).
- Ben Shneiderman's **Visual Information-Seeking Mantra** (1996): "Overview first, zoom and filter, then details-on-demand."

### 3.2 Dashboard types

| Type | Users | Content | Refresh |
|------|-------|---------|--------|
| **Operational** | Front-line staff, team leads | Current status, queues, alerts, SLAs | Real-time / minutes |
| **Analytical** | Analysts | Trends, drill-down, segmentation, exploration | Daily |
| **Strategic / executive** | Leadership | KPIs vs targets, high-level trends | Weekly/monthly |
| **Tactical** | Middle managers | Performance of processes/teams | Daily/weekly |

### 3.3 Chart selection

| Question | Chart |
|----------|-------|
| Compare categories | Horizontal bar (sorted), column |
| Trend over time | Line (continuous), column (discrete periods) |
| Part-to-whole | Stacked bar, 100% bar; pie/donut only for ≤ 3–5 slices and when rough proportions suffice |
| Distribution | Histogram, box plot, strip/dot plot |
| Relationship | Scatter plot, bubble |
| Target vs actual | **Bullet graph** (Stephen Few, 2005 — replaces gauges), bar with target line |
| Single KPI | Big number + comparison (vs target / previous period, delta with direction) + sparkline |
| Geographic | Choropleth, symbol map |
| Flow | Sankey, funnel |
| Schedule | Gantt |
| Correlation matrix / density | Heatmap |

**Avoid:** 3D charts, dual-axis charts (misleading), gauges/speedometers (space-hungry, low info), pie charts with many slices, truncated y-axis on bar charts (bars must start at zero; line charts may not), rainbow colour scales.

### 3.4 Layout & design

- Start from **questions/decisions** users must make, not available data.
- Most important KPIs top-left; 5–9 key elements; group by theme; consistent time frame shown explicitly.
- Every number needs context: target, previous period, trend, benchmark.
- Use colour sparingly: neutral by default; colour signals exceptions (Von Restorff).
- Consistent scales across comparable charts; label directly instead of legends where possible.
- Show data freshness ("Updated 10:42") and definitions (tooltip with KPI formula).
- Enable drill-down to underlying records (in ERP: from "12 overdue invoices" to the filtered invoice list).
- Accessible: colour-blind-safe palettes (Okabe–Ito, IBM CVD-safe palette, Viridis for sequential), patterns/direct labels, data table alternative, 3:1 contrast for chart elements.
- Tools: Power BI, Tableau, Grafana, Superset, Metabase; libraries: ECharts, Highcharts (a11y module), Recharts, Chart.js, Vega-Lite, D3, Observable Plot.

Sources: <https://www.perceptualedge.com/> · <https://www.edwardtufte.com/> · <https://en.wikipedia.org/wiki/Chartjunk>

---

## 4. States: empty, loading, error, partial, success

Scott Hurff's **UI stack** (2015): every screen has **Ideal, Empty, Error, Partial, Loading** states (plus offline, no-permission, overflow).

### 4.1 Empty states

| Type | Content |
|------|--------|
| **First use** (no data yet) | What this area is for + primary CTA ("Create first project", "Import employees from CSV") + optional illustration/help link |
| **User-cleared** (inbox zero) | Positive confirmation ("All approvals done") |
| **No results** (search/filter) | State the query/filters, suggest fixes ("Clear filters", check spelling), offer to create |
| **No permission** | Explain and who to ask |
| **Error loading** | See errors |

Rule: never a blank white area. Empty states are onboarding opportunities (Paradox of the active user).

### 4.2 Loading states

| Duration | Pattern |
|----------|---------|
| < 300 ms | No indicator (avoid spinner flash; delay showing by ~300 ms, then show for ≥ 500 ms to avoid flicker) |
| 0.3–2 s | Spinner/inline indicator in the button/area that triggered it; **skeleton screens** for page/content loads |
| 2–10 s | Skeleton + progress hint; looped indicator |
| > 10 s | **Determinate progress bar** with % / time remaining, allow background processing and notify on completion |

- **Skeleton screens** (Luke Wroblewski, 2013): grey placeholders mimicking layout; reduce perceived wait and layout shift; for full-page or container loads, not for small components. Avoid shimmer that's too strong (reduced-motion).
- **Optimistic UI:** reflect the change immediately, roll back on failure with an explanation — for low-risk, high-success actions (star, reorder, toggle).
- Keep stale content visible while revalidating (stale-while-revalidate).
- Disable double submissions (button loading state with `aria-busy`), but keep it focusable.
- Announce load completion for screen readers when content changes significantly.

Source: <https://www.nngroup.com/articles/skeleton-screens/> · <https://www.nngroup.com/articles/progress-indicators/>

### 4.3 Error states

- **Field-level** (validation) → inline.
- **Form/page-level** → error summary, banner within the content area.
- **System/global** (outage, offline, session expired) → banner/page with retry and preserved data.
- **404 / 403 / 500 pages** → plain explanation, way forward (search, home, contact), no blame; include a reference ID for support.
- Offline/sync conflicts → show state, queue changes, conflict resolution UI.

### 4.4 Success & partial states

- Confirm completion near the action or on a confirmation page with next steps (Peak–end rule).
- Partial: "47 of 50 records imported — 3 failed" with downloadable error report and retry for failures.

---

## 5. Feedback & notifications

| Component | Use | Behaviour | Don't |
|-----------|-----|----------|------|
| **Inline message** | Contextual info/validation tied to an element | Persistent until resolved | — |
| **Banner / alert (page-level)** | System status, warnings affecting the page (maintenance, read-only mode) | Persistent, dismissible if not critical | Stack many banners |
| **Toast / snackbar** | Brief, low-priority confirmation of a user action ("Saved", "Moved to archive — Undo") | Bottom (Material) or top-right; auto-dismiss 4–10 s (Material: 4–10 s); pause on hover/focus; max 1 action; `role="status"` | Errors that need action; critical info; multiple stacked toasts; important actions only available in the toast |
| **Notification centre** | Async events (approval requested, export ready) | Persistent list, unread count badge | Badges for everything (badge fatigue) |
| **Modal alert dialog** | Critical decisions needing immediate response | Blocks; `role="alertdialog"` | Routine info |
| **Push / email** | Out-of-app events | User-controllable preferences, batching/digests | Overnotification |
| **Badge / dot** | Count/status indicator | Numeric when count matters; cap at 99+ | — |
| **Tooltip** | Supplementary label/explanation on hover/focus | Short, no interactive content; shows on focus too; Esc closes (WCAG 1.4.13) | Essential info only in tooltip; tooltips on disabled buttons (not focusable) |
| **Popover / toggletip** | Richer contextual help on click | Can contain links | — |

Severity colours: info (blue), success (green), warning (amber), error (red) — always with icon + text.

Source: <https://m3.material.io/components/snackbar/guidelines> · <https://www.nngroup.com/articles/indicators-validations-notifications/>

---

## 6. Modals vs drawers vs pages vs inline

| Pattern | Use when | Avoid when |
|---------|---------|-----------|
| **Modal dialog** | Short, focused task or decision that must be completed/dismissed before continuing (confirm destructive action, quick create with 1–5 fields, critical alert) | Long forms; content users need to compare with the page behind; nested modals; on page load (unrequested pop-ups); multi-step flows (prefer page) |
| **Non-modal dialog / panel** | Tool palettes, chat, find-replace — user continues working | — |
| **Drawer / side sheet / side panel** | Viewing/editing details while keeping list context (master–detail), filters, secondary forms of moderate length | Primary long-form workflows; mobile (becomes full screen) |
| **Bottom sheet** (mobile) | Contextual actions, filters | Complex content |
| **Full page** | Complex creation/editing, multi-step, deep-linkable content, anything users may need to bookmark/share | — |
| **Inline expansion / inline edit** | Small edits in place, revealing details | Large forms |
| **Popover** | Small contextual content anchored to a trigger (date picker, quick info) | Large content |

**Modal guidelines:** clear title stating the purpose; specific action labels ("Delete 3 invoices", not "OK"); Esc and visible close; focus trap and return focus (see [accessibility.md](accessibility.md#52-focus-management-rules)); don't close on backdrop click if it would lose entered data; max width ~480–640px for dialogs; avoid scrolling content in modals where possible; warn on unsaved changes.

Source: <https://www.nngroup.com/articles/modal-nonmodal-dialog/>

---

## 7. Wizards (multi-step flows)

**Use:** linear processes, infrequent users, steps with dependencies, complex configuration (onboarding a new employee, setting up payroll, creating a contract).
**Don't use:** for expert, frequent tasks where users know what they need (a single dense form is faster), or when steps aren't really sequential.

Guidelines:
- Step indicator showing names (not just numbers) and position: "Step 2 of 4: Contract details" (Goal-gradient).
- 3–7 steps; group logically; one topic per step.
- Back without losing data; Save & exit / draft; resume later.
- Validate per step; allow jumping back to completed steps.
- Summary/review step before final submit with edit links.
- Confirmation step with outcome and next steps.
- Don't show steps that are conditional until known (or update the indicator dynamically).
- In URLs: each step addressable (supports back button).

---

## 8. Search patterns

See also [information-architecture.md](information-architecture.md#5-search-systems).

- Visible search field on desktop; icon-triggered expanding field on mobile.
- Autosuggest with grouped results (records, pages, actions); keyboard navigation; recent searches.
- Instant results for small datasets (debounce ~200–300 ms); explicit submit for heavy queries.
- Results page: query echo, result count, facets, sort, highlighted matches, "did you mean".
- Scoped search within a list (filter-as-you-type) vs global search — make the scope clear.
- Advanced search/query builder for power users (field: value syntax, saved searches).

---

## 9. Pagination vs infinite scroll vs "Load more"

| Pattern | Good for | Problems |
|---------|---------|---------|
| **Pagination** | Goal-oriented finding, comparing, returning to a position, enterprise tables, SEO, shareable positions, footer access | Extra clicks; page size choice |
| **Infinite scroll** | Feeds, browsing/discovery of homogeneous content on mobile (social, images) | No sense of end/position, footer unreachable, back-button loses position, memory/performance, accessibility (keyboard/screen reader), hard to return to an item (NN/g) |
| **"Load more" button** | Hybrid: user-controlled, footer reachable, good on mobile catalogues | Still loses position on back unless handled |
| **Virtual scrolling** | Large data grids (10k+ rows) with known total | Find-in-page (Ctrl+F) doesn't work; needs ARIA row indices |

Enterprise default: **pagination** (with total count and page size) or virtualised grids; never infinite scroll for records users act on.

Source: <https://www.nngroup.com/articles/infinite-scrolling/>

---

## 10. Onboarding

| Pattern | Description | Notes |
|---------|------------|------|
| **Empty-state onboarding** | Teach via the first-use empty states | Most effective & least intrusive |
| **Onboarding checklist** | 3–7 setup tasks with progress (Zeigarnik, goal-gradient) | Good for admin set-up of SaaS/ERP |
| **Contextual tips / hotspots** | Just-in-time hints on first encounter of a feature | One at a time, dismissible, don't block |
| **Product tours (coach marks)** | Step-by-step overlay | Often skipped; NN/g: deck-of-cards tutorials are forgotten; keep ≤ 3–5 steps or avoid |
| **Welcome screen** | Value proposition, choose path/role | |
| **Sample data / templates** | Pre-populated demo data | Helps understanding of dashboards/reports |
| **Progressive onboarding** | Unlock complexity over time | |
| **Interactive walkthrough with real task** | Learn by doing | Best for complex tools |

Rules: let users skip; get them to the "aha moment" (first value) fast; personalise by role; make help re-findable later ("What's new", help centre, replay tour); don't ask for data you don't need yet (account creation deferred where possible).

Source: <https://www.nngroup.com/articles/mobile-app-onboarding/>

---

## 11. Progressive disclosure

- **Show** frequently used options; **defer** advanced/rare ones to secondary UI (expanders, "Advanced", "More options", drill-down, tabs, modal details).
- Staged disclosure = wizards (sequential); progressive disclosure = hierarchical (on demand).
- Requirements: correct split (research usage frequency), obvious access to deferred features (clear label + affordance), ≤ 2 levels.
- Examples: basic vs advanced search; summary row with expandable detail; "Show 12 more fields" in a record; collapsed filter groups.
- Anti-pattern: hiding critical info (fees, terms) — becomes a dark pattern.

Source: <https://www.nngroup.com/articles/progressive-disclosure/>

---

## 12. Undo vs confirm

| Approach | Use when | Notes |
|----------|---------|------|
| **Undo** | Action is reversible and frequent (delete email, archive, move, bulk status change) | Preferred: no interruption; act immediately, show "Undo" in toast for ≥ 5–10 s (and make it keyboard reachable), or soft-delete with trash/restore. Aza Raskin, "Never use a warning when you mean undo" (2007) |
| **Confirmation dialog** | Action is irreversible, destructive, rare and high-impact (permanently delete account, run payroll, send to 5,000 customers) | State the specific consequence and object ("Permanently delete 3 employees? Their contracts and payslips will be removed. This can't be undone."); action button names the action ("Delete 3 employees"), not "OK/Yes"; default focus on the safe option |
| **Type-to-confirm** (forcing function) | Extremely destructive (delete repository/production database) | GitHub pattern — type the name |
| **Two-step / review page** | Financial, legal, data submissions (WCAG 3.3.4) | Summary + submit |
| **Delayed execution** | Send with delay ("Undo send" 5–30 s) | Gmail |
| **Versioning / history** | Documents, records | Restore previous version; audit trail |

Why avoid confirmations for frequent actions: users habituate and click "Yes" automatically (Raskin; Norman's capture errors). Confirmation is not a substitute for good design.

---

## 13. Other common patterns (quick reference)

| Pattern | Key guidance |
|---------|-------------|
| **Buttons** | Verb-first labels ("Save changes"); one primary per area; loading state; min height 32–40px desktop; destructive styling only for destructive actions |
| **Links vs buttons** | Links navigate (`<a>`), buttons act (`<button>`) |
| **Tabs** | Peer views of the same context; ≤ 7; don't use for steps; keep selected tab in URL |
| **Accordions** | Long content where users need only parts (FAQ, settings); avoid for content most users need |
| **Breadcrumbs** | Hierarchy ≥ 3 levels; current page not linked |
| **Cards** | Heterogeneous content collections; whole card clickable with one primary link (accessible card pattern) |
| **Chips/tags** | Filters, selections, attributes |
| **Steppers** | Wizards (see §7) |
| **Date range pickers** | Presets ("Last 7 days", "This month", "Previous payroll period") + custom |
| **File upload** | Drag & drop + button; constraints shown; progress; errors per file; virus-scan states |
| **Command palette** | `Cmd/Ctrl+K`; fuzzy search over navigation & actions |
| **Keyboard shortcuts** | Discoverable (shown in tooltips/menus, `?` overlay); avoid single-key unless toggleable (WCAG 2.1.4) |
| **Inline editing** | Click/Enter to edit; Esc cancel; clear saved state |
| **Autosave** | Show status ("Saving…", "Saved 10:42", "Offline — changes queued"); version history |
| **Drag and drop** | Visible handle, drop zone highlighting, keyboard & menu alternative (WCAG 2.5.7) |
| **Settings** | Group by goal; search; apply immediately (toggles) vs Save (forms) — don't mix on one screen |
| **Permissions / disabled states** | Explain why something is disabled (tooltip/inline text), or hide if never available to the role |
| **Timeouts** | Warn before session expiry with "Stay signed in"; preserve data |
| **Comments / activity feed** | Chronological with filters; mention users; audit trail separate from conversation |

## Common mistakes

- Designing only the happy path with perfect data.
- Disabling submit buttons instead of explaining errors.
- Toasts for errors; modals for everything.
- Infinite scroll on record lists.
- Gauges and 3D charts on dashboards.
- Confirmation dialogs for every action.
- Product tours as a substitute for usable UI.
- Using `type="number"` for IDs/postcodes/amounts.

## References

- NN/g articles index: <https://www.nngroup.com/articles/>
- NN/g — Web form design: <https://www.nngroup.com/articles/web-form-design/>
- NN/g — Data tables: <https://www.nngroup.com/articles/data-tables/>
- NN/g — Modal & nonmodal dialogs: <https://www.nngroup.com/articles/modal-nonmodal-dialog/>
- NN/g — Skeleton screens: <https://www.nngroup.com/articles/skeleton-screens/>
- NN/g — Progress indicators: <https://www.nngroup.com/articles/progress-indicators/>
- NN/g — Infinite scrolling: <https://www.nngroup.com/articles/infinite-scrolling/>
- NN/g — Progressive disclosure: <https://www.nngroup.com/articles/progressive-disclosure/>
- NN/g — Indicators, validations, notifications: <https://www.nngroup.com/articles/indicators-validations-notifications/>
- GOV.UK Design System patterns: <https://design-system.service.gov.uk/patterns/>
- Baymard Institute: <https://baymard.com/>
- Luke Wroblewski: <https://www.lukew.com/>
- Carbon data table: <https://carbondesignsystem.com/components/data-table/usage/>
- APG grid pattern: <https://www.w3.org/WAI/ARIA/apg/patterns/grid/>
- Material 3 components: <https://m3.material.io/components>
- Perceptual Edge (Stephen Few): <https://www.perceptualedge.com/>
- Edward Tufte: <https://www.edwardtufte.com/>
- Chartjunk: <https://en.wikipedia.org/wiki/Chartjunk>
- UI Patterns library: <https://ui-patterns.com/>
- Aza Raskin, "Never use a warning when you mean undo" (A List Apart): <https://alistapart.com/article/neveruseawarning/>
