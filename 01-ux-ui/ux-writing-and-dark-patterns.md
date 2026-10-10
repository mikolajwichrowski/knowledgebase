# UX Writing, Microcopy & Dark Patterns

> **TL;DR** — Interface text is interface design. Be **clear, concise, useful, consistent**: front-load key words, use the user's vocabulary, verb-first action labels ("Save draft", not "OK"), specific error messages that say what happened and how to fix it, no blame, no jargon. Users scan — they read ~20–28% of words on a page (Nielsen, 2008). Avoid **dark patterns** (Harry Brignull, 2010; deceptive.design): confirmshaming, roach motel, sneak into basket, hidden costs, forced continuity, disguised ads, trick questions, preselection, nagging, privacy zuckering. They are increasingly illegal in the EU (DSA Art. 25, UCPD, GDPR consent rules, DMA, Data Act).

Related: [UI patterns](ui-patterns.md) · [Heuristics](heuristics-and-principles.md) · [Accessibility (plain language)](accessibility.md#10-inclusive--cognitive-accessibility)

---

## 1. Principles of UX writing

| Principle | Guidance | Bad → Good |
|-----------|---------|-----------|
| **Clear** | Plain words, user's vocabulary, one meaning per term | "Initiate absence registration" → "Request leave" |
| **Concise** | Cut filler; every word earns its place | "Please click the button below in order to proceed" → "Continue" |
| **Useful** | Tells users what they need to act | "Error" → "Upload failed — file is larger than 10 MB" |
| **Consistent** | Same term for the same thing everywhere | Don't mix Delete/Remove/Discard for the same action |
| **Front-loaded** | Important words first (F-pattern scanning) | "To change your bank account, go to Pay" → "Bank account: change it in Pay" (in navigation/lists) |
| **Active voice** | Who does what | "Your request was rejected by the manager" → "Your manager rejected your request" |
| **Conversational, human** | Write like a helpful colleague; tone varies with context | — |
| **Inclusive** | Gender-neutral, no idioms/culture-specific jokes, accessible reading level | "Hey guys" → "Hi everyone" |
| **Accessible** | Plain language (B1), descriptive links, no directional-only instructions | "Click here" → "Download the payslip (PDF, 120 KB)" |
| **Localisable** | Avoid concatenated strings, leave room for ~30–40% expansion, use ICU plural rules | `"{count, plural, one {# employee} other {# employees}}"` |

Kinneret Yifrah's "Microcopy" principles; Torrey Podmajersky, *Strategic Writing for UX* (2019): voice chart (concepts, vocabulary, verbosity, grammar, punctuation, capitalisation per brand trait); Sarah Richards, *Content Design* (2017) — GOV.UK content design origin.

---

## 2. Voice and tone

- **Voice** = consistent personality (e.g. "clear, calm, competent").
- **Tone** = adapts to context and user's emotional state (Mailchimp Content Style Guide).

| Context | Tone |
|--------|-----|
| Success / routine | Brief, neutral-positive ("Saved") |
| Error / user frustrated | Calm, specific, helpful, no jokes |
| Destructive action | Serious, explicit consequences |
| Onboarding | Encouraging, guiding |
| Legal/financial | Precise, plain, neutral |
| Enterprise tools | Efficient, professional, domain-correct; humour rarely |

NN/g four dimensions of tone of voice: funny ↔ serious, formal ↔ casual, respectful ↔ irreverent, enthusiastic ↔ matter-of-fact.

Source: <https://www.nngroup.com/articles/tone-of-voice-dimensions/> · <https://styleguide.mailchimp.com/>

---

## 3. Microcopy patterns

### 3.1 Buttons & actions

- **Verb + object**: "Create invoice", "Send for approval", "Export 245 rows".
- Avoid "OK", "Yes/No", "Submit" when a specific verb exists.
- Match the button to the dialog title: title "Delete draft?" → buttons "Delete draft" / "Keep draft" (or "Cancel").
- Sentence case for UI text (Material, Fluent, Polaris, GOV.UK) — easier to read than Title Case.
- Consistent pairs: Save/Cancel, Next/Back, Add/Remove (to/from a set), Create/Delete (exists/doesn't), Archive/Restore, Enable/Disable, Sign in/Sign out (don't mix with Log in).
- Destructive vs reversible verbs: "Remove from team" (reversible) vs "Delete user" (permanent).

### 3.2 Labels & hints

- Labels are short nouns: "Start date", "IBAN", "Cost centre".
- Hints explain format/purpose: "For example, 27-03-2025", "We'll use this to send your payslip".
- Don't ask questions in labels unless it's a one-question-per-page style ("What is your date of birth?" — GOV.UK uses question headings).
- Explain why sensitive data is needed (BSN, date of birth).

### <a id="error-messages"></a>3.3 Error messages

**Formula:** *What happened* + *why* (if useful) + *how to fix it*. Polite, specific, no blame, no codes without explanation, near the problem.

NN/g guidelines (Nielsen 2001, updated): visible (near the issue, high contrast), constructive (offer a fix), polite (no blame, no "illegal"/"invalid"/"fatal"), precise, preserve input, reduce work (offer correction).

| Bad | Good |
|-----|------|
| Invalid input | Enter an email address in the correct format, like name@example.com |
| Error 422 | We couldn't save the contract — the end date is before the start date |
| Field required | Enter your postcode |
| Something went wrong | We couldn't load payslips. Check your connection and try again. If it keeps happening, contact HR Support (reference 7F3A-21) |
| You entered a wrong password | The email or password is incorrect (don't disclose which — security) |
| Date out of range | Leave must start on or after 01-01-2025 (the current leave year) |
| Upload error | "salary.xlsx" is 14 MB. Upload a file smaller than 10 MB |
| Unauthorized | You don't have access to salary data. Ask your HR administrator for the "Payroll viewer" role |

GOV.UK error message conventions: "Enter…", "Select…", "…must be…", e.g. "Enter a date of birth", "Date of birth must be in the past", "Select if you are employed".

Source: <https://www.nngroup.com/articles/error-message-guidelines/> · <https://design-system.service.gov.uk/components/error-message/>

### 3.4 Empty states

Headline (what's here) + one-sentence explanation + action: "No leave requests yet — Requests you submit will appear here. [Request leave]".

### 3.5 Confirmation & success

"Leave request sent to Anna Jansen. You'll get an email when she responds." Include what happens next and when.

### 3.6 Loading & progress

"Calculating payroll for 980 employees… 412 done". Avoid fake cheerfulness for long waits in enterprise tools.

### 3.7 Notifications

Who + did what + to what + (when): "Anna Jansen approved your leave request (12–16 May)". Make actionable with a deep link.

### 3.8 Links

Descriptive and unique out of context (WCAG 2.4.4): "View payslip March 2025" not "View" ×12. Indicate file type/size for downloads; indicate opening in new window if you must.

### 3.9 Numbers, dates & units

- Locale-aware formats (`Intl.NumberFormat`, `Intl.DateTimeFormat`): NL `€ 1.234,56`, `27-03-2025` or `27 mrt. 2025`; EN-GB `£1,234.56`, `27 March 2025`; ISO `2025-03-27` for technical contexts.
- Unambiguous dates in international products: `27 Mar 2025` beats `03/04/2025`.
- Relative time ("2 hours ago") for recency + absolute on hover/in detail.
- Units always shown (hours vs days for leave balances is a classic error source).

### 3.10 Content style guide topics

Capitalisation, punctuation (no full stops in labels/buttons; full stops in sentences), numerals vs words, date/time formats, abbreviations, inclusive language, product terminology/glossary, voice & tone, accessibility, localisation rules.

Style guides: Mailchimp, Microsoft Writing Style Guide, Google developer documentation style guide, GOV.UK style guide, Polaris content, Atlassian content design, Rijksoverheid/Digitale Overheid "Schrijfwijzer".

---

## 4. Content design process

1. Understand user needs (research, search logs, support tickets).
2. Define the job of each piece of content.
3. Write in the user's language (vocabulary from research).
4. Test: highlighter test (green = clear, pink/red = confusing), cloze test (comprehension), comprehension questions in usability tests, 5-second tests, A/B for high-traffic copy.
5. Maintain: content inventory, ownership, review dates.

Readability formulas: Flesch Reading Ease, Flesch–Kincaid Grade Level, (NL: Flesch-Douma, LiNT); useful as indicators only.

---

## 5. Dark patterns (deceptive design)

**Definition:** interface designs that trick or pressure users into doing things they didn't intend, benefiting the business at the user's expense. Term coined by **Harry Brignull** (2010); site renamed **deceptive.design** (2022), book *Deceptive Patterns* (2023). Academic taxonomies: Gray et al. (2018: nagging, obstruction, sneaking, interface interference, forced action); Mathur et al. (2019, Princeton: 1,818 instances on 11K shopping sites, 15 types in 7 categories); OECD (2022) definition.

### 5.1 Catalogue

| Pattern | Description | Example | Ethical alternative |
|---------|------------|---------|--------------------|
| **Confirmshaming** | Guilt-tripping opt-out wording | "No thanks, I prefer paying full price" | Neutral "No thanks" |
| **Roach motel / Hard to cancel** (obstruction) | Easy to get in, hard to get out | Online signup, cancel only by phone | Cancel as easy as signup (EU/US rules increasingly require) |
| **Sneak into basket** | Adds items without consent | Insurance pre-added at checkout | Opt-in add-ons |
| **Hidden costs / Drip pricing** | Fees revealed at the last step | Service fee at payment | All-in price upfront (EU Consumer Rights Directive) |
| **Hidden subscription / Forced continuity** | Free trial silently converts to paid, no reminder | — | Reminder before charge, easy cancel |
| **Bait and switch** | Action produces unexpected result | Close (✕) button triggers install | — |
| **Disguised ads** | Ads styled as content/navigation | "Download" buttons that are ads | Clear ad labelling |
| **Trick questions / Trick wording** | Confusing double negatives in opt-ins | "Uncheck to not receive no emails" | Clear opt-in wording |
| **Preselection / default opt-in** | Pre-ticked consent/marketing boxes | — | Unchecked by default (GDPR requires for consent — *Planet49*, CJEU 2019) |
| **Visual interference / misdirection** | Highlighting the business-preferred option, greying the other | Cookie banner: big "Accept all", tiny "Settings" | Equal prominence for Accept/Reject |
| **Nagging** | Repeated interruptions | "Turn on notifications?" every session | Respect "no", ask in context once |
| **Fake urgency / scarcity** | False timers, "Only 2 left!", "12 people viewing" | — | Only true info |
| **Fake social proof** | Fabricated reviews/activity | — | Real, verifiable |
| **Privacy zuckering** | Tricking users into sharing more data than intended | Confusing privacy settings | Privacy by default (GDPR Art. 25) |
| **Obstruction / forced action** | Requiring unrelated actions (account creation, sharing contacts) | Must create account to read | Guest flows |
| **Comparison prevention** | Making options hard to compare | Different units per plan | Comparable tables |
| **Disguised / hidden information** | Key info in fine print | — | Prominent disclosure |
| **Interface interference: toying with emotion** | Manipulative imagery/text | — | — |
| **Infinite scroll / autoplay for engagement** | Attention capture | — | Natural stopping points |
| **Gamification abuse** | Streaks to drive compulsive use | — | Respect user goals |

Source: <https://www.deceptive.design/types>

### 5.2 Legal & regulatory (EU focus)

| Instrument | Relevance |
|-----------|----------|
| **GDPR** (2016/679) + ePrivacy Directive | Consent must be freely given, specific, informed, unambiguous; as easy to withdraw as to give (Art. 7(3)); no pre-ticked boxes; EDPB Guidelines 03/2022 on deceptive design patterns in social media platform interfaces |
| **Unfair Commercial Practices Directive** (2005/29/EC) | Misleading & aggressive practices; annex blacklist (e.g. false "limited time" offers) |
| **Digital Services Act** (EU 2022/2065), **Art. 25** | Online platforms shall not design interfaces that deceive, manipulate or impair users' free and informed decisions (applies to platforms since 17 Feb 2024) |
| **Digital Markets Act** (2022/1925) | Gatekeepers may not use interface design to circumvent obligations (anti-circumvention) |
| **Consumer Rights Directive** (2011/83/EU, amended by Omnibus 2019/2161) | Price transparency, no pre-ticked paid extras |
| **Data Act** (2023/2854) & **AI Act** (2024/1689) | Data Act bans dark patterns in data-sharing UIs; AI Act Art. 5 prohibits manipulative AI techniques |
| **Digital Fairness Act** (proposal expected 2026) | EU initiative specifically targeting dark patterns, addictive design, subscription traps |
| **NL** | ACM (Autoriteit Consument & Markt) guidelines on online influencing (2020/2023) |
| **US** | FTC report "Bringing Dark Patterns to Light" (2022); FTC "click-to-cancel" rule (2024, vacated by court 2025); CPRA (California) defines dark patterns for consent |

### 5.3 Ethical design checklist

- [ ] Would we be comfortable if users (or a regulator, or journalists) saw exactly how this works?
- [ ] Are accept and decline options equally visible and easy?
- [ ] Is cancelling/opting out as easy as signing up/opting in?
- [ ] Are defaults in the user's interest?
- [ ] Is all cost info shown upfront?
- [ ] Are urgency/scarcity/social proof claims true?
- [ ] Do success metrics reward user value, not just conversion/engagement?
- [ ] Have we checked the Manipulation Matrix (see [design-processes.md](design-processes.md#6-hook-model))?

Frameworks: *Ethical Design Manifesto* (Ind.ie), Center for Humane Technology, Mike Monteiro *Ruined by Design* (2019), Design Justice Network principles (Sasha Costanza-Chock, 2020).

---

## 6. Enterprise-specific writing notes

- Domain terms are fine if they're the *users'* terms (payroll specialists say "loonheffing", "bijtelling", "SV-loon") — but avoid system-internal terms (table names, status codes like "ST_04").
- Explain calculations ("Net pay = gross pay − taxes − deductions — see breakdown").
- Status names: consistent, mutually exclusive, past-participle or adjective ("Draft", "Submitted", "Approved", "Rejected", "Paid"); explain each in a tooltip/legend.
- Business-rule errors must name the rule and the fix: "Can't approve: the employee has no active contract on 14-03-2025. Add or extend the contract first."
- Bilingual products (NL/EN): maintain a terminology base; translators need context (screenshots, character limits); avoid string concatenation.

## Common mistakes

- Lorem ipsum in designs → copy written last, doesn't fit.
- Generic "OK/Cancel" buttons; "Submit".
- Errors blaming users or exposing technical details.
- Inconsistent terminology across modules.
- Humour in error states.
- Title Case Everywhere and ALL CAPS buttons hurting readability.
- Cookie banners with unequal choices (illegal in the EU per multiple DPA rulings, e.g. CNIL fines against Google/Facebook 2021–2022).

## References

- deceptive.design (Harry Brignull): <https://www.deceptive.design/>
- Dark pattern (Wikipedia): <https://en.wikipedia.org/wiki/Dark_pattern>
- NN/g — Error message guidelines: <https://www.nngroup.com/articles/error-message-guidelines/>
- NN/g — Tone of voice dimensions: <https://www.nngroup.com/articles/tone-of-voice-dimensions/>
- NN/g — How users read on the web: <https://www.nngroup.com/articles/how-users-read-on-the-web/>
- GOV.UK — Writing for GOV.UK / content design: <https://www.gov.uk/guidance/content-design>
- GOV.UK — Error message component: <https://design-system.service.gov.uk/components/error-message/>
- Mailchimp Content Style Guide: <https://styleguide.mailchimp.com/>
- Microsoft Writing Style Guide: <https://learn.microsoft.com/en-us/style-guide/welcome/>
- Polaris content guidelines: <https://polaris-react.shopify.com/content/voice-and-tone>
- Google developer documentation style guide: <https://developers.google.com/style>
- EDPB Guidelines on deceptive design patterns: <https://www.edpb.europa.eu/>
- Digital Services Act: <https://en.wikipedia.org/wiki/Digital_Services_Act>
- Unfair Commercial Practices Directive: <https://en.wikipedia.org/wiki/Unfair_Commercial_Practices_Directive>
- Outcome-Driven Innovation (Ulwick): <https://en.wikipedia.org/wiki/Outcome-Driven_Innovation>
- Princeton dark patterns study (Mathur et al. 2019): <https://webtransparency.cs.princeton.edu/dark-patterns/>
