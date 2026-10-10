# Visual Design: Gestalt, Hierarchy, Typography, Color, Spacing, Layout, Motion & Touch

> **TL;DR** — Visual design is functional: it creates grouping (Gestalt), order (hierarchy), legibility (typography), meaning (colour) and rhythm (spacing). Defaults that work: a modular type scale (ratio 1.2–1.25 for dense apps), body 16px, line-height 1.4–1.6, line length 45–75ch, a 4/8pt spacing scale, 12-column grid, contrast ≥ 4.5:1 for text, motion 100–500 ms with ease-out for entering. Touch targets ≥ 44pt (iOS) / 48dp (Android), WCAG minimum 24×24 CSS px.

Related: [Laws of UX](laws-of-ux.md) · [Accessibility](accessibility.md) · [Design systems & Figma](design-systems-and-figma.md)

---

## 1. Gestalt principles

Gestalt psychology (Max Wertheimer, Wolfgang Köhler, Kurt Koffka; Berlin, 1910s–1920s): "the whole is *other* than the sum of its parts" (Koffka).

| Principle | Definition | UI application | Anti-pattern |
|-----------|-----------|----------------|-------------|
| **Proximity** | Close elements are perceived as a group | Label 4–8px above its input, 24–32px to the next field; card padding > gap inside | Equal spacing everywhere → ambiguous grouping |
| **Similarity** | Elements sharing colour, shape, size are related | All links one colour; status chips share shape | Primary-coloured headings that look like links |
| **Common region** | Elements within a boundary are grouped | Cards, fieldsets, table zebra rows, panels | Boxes inside boxes inside boxes ("card soup") |
| **Uniform connectedness** | Visually connected elements are related | Stepper connectors, timelines, tree lines | — |
| **Continuity** (good continuation) | Eye follows lines/curves | Aligned left edges in forms; carousels hinting continuation | Ragged alignment |
| **Closure** | Mind completes incomplete shapes | Icons, partially visible card signalling scroll | — |
| **Figure–ground** | We separate object from background | Modals with scrim; elevation/shadows | Low-contrast overlays |
| **Prägnanz** (simplicity) | Perceive the simplest interpretation | Simple icon forms | Ambiguous illustrations |
| **Common fate** | Elements moving together are grouped | Animate related items together (expand group) | — |
| **Symmetry & order** | Symmetric elements perceived as unified | Balanced layouts | — |
| **Focal point** | Distinct element captures attention (≈ Von Restorff) | Primary CTA | Multiple competing focal points |

Sources: <https://en.wikipedia.org/wiki/Gestalt_psychology> · <https://lawsofux.com/law-of-proximity/> · <https://www.nngroup.com/articles/gestalt-proximity/>

---

## 2. Visual hierarchy

Tools to signal importance, in rough order of strength:

1. **Size** (largest = most important)
2. **Colour & contrast** (saturated/high contrast pops; low contrast recedes)
3. **Weight** (bold vs regular)
4. **Position** (top-left in LTR cultures; above the fold)
5. **Spacing / whitespace** (isolation draws attention)
6. **Typography style** (case, typeface)
7. **Depth / elevation** (shadow)
8. **Motion** (strongest attention grab — use sparingly)

**Practical rules (Refactoring UI, Wathan & Schoger):**
- Not everything can be important. Emphasise by *de-emphasising* the rest (grey secondary text rather than bold primary).
- Use 2–3 text colours (e.g. primary ~gray-900, secondary ~gray-600, tertiary ~gray-500 — check contrast) and 2 weights (400/600) rather than many sizes.
- Labels are a last resort: `€ 12.400` and "Due 14 Mar" can stand alone; where needed, de-emphasise the label, emphasise the value.
- Button hierarchy: primary (solid), secondary (outline/tonal), tertiary (text/link). Destructive actions aren't automatically primary red — red solid only when it *is* the main action in a confirm dialog.
- Separate visual hierarchy from document hierarchy: an `<h2>` can look small if semantically correct.

**Scanning patterns (NN/g eye-tracking):**

| Pattern | When it occurs | Design implication |
|---------|---------------|-------------------|
| **F-pattern** (2006) | Text-heavy pages without strong formatting | Front-load headings and first words; use subheads, bullets |
| **Layer-cake** | Pages with clear headings | Most efficient — scanners read headings only |
| **Spotted** | Looking for something specific (numbers, links) | Make key data visually distinct |
| **Marking** | Mobile, eyes fixed while scrolling | — |
| **Bypassing** | Repeated first words skipped | Vary list item openings |
| **Commitment** | Highly motivated readers | Read fully |
| **Z-pattern** | Sparse, low-text layouts (landing pages) | Logo → nav → hero → CTA |

Source: <https://www.nngroup.com/articles/f-shaped-pattern-reading-web-content/> · <https://www.refactoringui.com/>

**Squint test / blur test:** blur the screen; the remaining visible structure should match the intended priority.

---

## 3. Typography

### 3.1 Anatomy & vocabulary

Baseline, x-height, cap height, ascender, descender, kerning (pair spacing), tracking/letter-spacing (global), leading (line-height), measure (line length), weight, width, optical size, serif/sans/mono/slab, humanist/grotesque/geometric sans, tabular vs proportional figures, ligatures.

### 3.2 Type scales

A modular scale multiplies a base size by a constant ratio.

| Ratio | Name | Steps from 16px base (−1 … +5) | Use |
|-------|------|-------------------------------|-----|
| 1.067 | Minor second | 15, 16, 17, 18.2, 19.4, 20.7, 22.1 | Very dense data UIs |
| 1.125 | Major second | 14.2, 16, 18, 20.3, 22.8, 25.6, 28.8 | Dense apps, dashboards |
| 1.2 | Minor third | 13.3, 16, 19.2, 23, 27.6, 33.2, 39.8 | General product UI |
| 1.25 | Major third | 12.8, 16, 20, 25, 31.3, 39.1, 48.8 | Product UI / marketing |
| 1.333 | Perfect fourth | 12, 16, 21.3, 28.4, 37.9, 50.5, 67.3 | Content sites |
| 1.5 | Perfect fifth | 10.7, 16, 24, 36, 54, 81 | Editorial/marketing |
| 1.618 | Golden ratio | 9.9, 16, 25.9, 41.9, 67.8 | Display-heavy |

**Pragmatic product-UI scale** (most design systems hand-tune instead of strict ratios): 12 / 14 / 16 / 18 / 20 / 24 / 30 / 36 / 48 / 60 (Tailwind-like). Material 3 type roles: Display (L/M/S 57/45/36), Headline (32/28/24), Title (22/16/14), Body (16/14/12), Label (14/12/11). Carbon uses "productive" (dense) vs "expressive" (fluid) type sets.

**Fluid typography:** `font-size: clamp(1rem, 0.9rem + 0.5vw, 1.25rem);` — always include a `rem` component so user zoom still works (WCAG 1.4.4). Generator: <https://utopia.fyi/>.

### 3.3 Readability rules

| Parameter | Recommendation | Source / note |
|-----------|---------------|---------------|
| Body size | 16px (1rem) default on web; 14px acceptable in dense enterprise grids; never < 12px | Browser default 16px |
| Line length (measure) | **45–75 characters**, ~66 ideal (Bringhurst); 30–40 on mobile | *The Elements of Typographic Style*; CSS `max-width: 65ch` |
| Line-height (leading) | Body 1.4–1.6 (WCAG 1.4.12 tests 1.5); headings 1.1–1.3; dense tables 1.25–1.4 | Longer lines need more leading |
| Paragraph spacing | ~0.75–1.5em between paragraphs; layout must survive 2× (WCAG 1.4.12) | |
| Letter-spacing | 0 for body; slightly positive (+0.02–0.1em) for ALL CAPS & small labels; negative for large display | |
| Alignment | Left-aligned (ragged right) for LTR; avoid justified text on web (rivers) and centred multi-line text | |
| Weights | 2–3 weights per family is plenty | |
| Number of families | 1 (UI) or 2 (UI + display/mono) | |
| Numbers | Tabular figures (`font-variant-numeric: tabular-nums`) in tables and amounts; right-align numbers | |
| Units | `rem` for font sizes; `em`/unitless for line-height | |
| ALL CAPS | Short labels only; reduces word-shape recognition | |
| Italic | Emphasis, not for long passages | |

**Font choice for enterprise UIs:** high x-height, clear distinction between `Il1` and `O0` (critical for codes/IDs), tabular figures, wide language support (diacritics for NL/PL/DE/TR), variable font for performance. Common: Inter, Roboto, IBM Plex Sans, Source Sans 3, Segoe UI Variable (Fluent), SF Pro (Apple), Noto Sans (coverage), system font stack. Government: Rijksoverheid Sans (NL), GDS Transport (UK). Mono: JetBrains Mono, IBM Plex Mono, Fira Code.

System font stack:

```css
font-family: system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial, "Noto Sans", sans-serif;
```

Sources: <https://practicaltypography.com/> · <https://en.wikipedia.org/wiki/The_Elements_of_Typographic_Style> · <https://m3.material.io/styles/typography/overview>

---

## 4. Colour

### 4.1 Colour models

| Model | Description | Use |
|-------|------------|-----|
| RGB / Hex | Additive, device-dependent (sRGB) | Implementation |
| HSL / HSB | Hue, saturation, lightness/brightness — intuitive but *not perceptually uniform* (HSL yellow at 50% L looks far lighter than blue at 50%) | Quick edits |
| **OKLCH / OKLab** | Perceptually uniform (Björn Ottosson, 2020); CSS Color Level 4 `oklch()` | Generating consistent palettes and tints/shades |
| CIELAB / LCh | Perceptual, older | Colour science, Delta-E |
| HCT | Google's Hue-Chroma-Tone (Material 3 dynamic colour) | Material palettes |
| Display P3 | Wider gamut | Modern Apple displays, `color(display-p3 …)` |
| CMYK / Pantone | Print | Brand guides |

### 4.2 Colour harmony (colour wheel, Itten)

Monochromatic · analogous (neighbours) · complementary (opposites) · split-complementary · triadic · tetradic/square. In UI, harmony matters less than **role-based palettes**.

### 4.3 Role-based UI palette

| Role | Purpose | Typical count |
|------|---------|---------------|
| Neutral/gray | Text, borders, backgrounds, surfaces (~80–90% of the UI) | 9–12 steps (50–950) |
| Primary/brand | Primary actions, links, selection, focus | 9–12 steps |
| Secondary/accent | Optional emphasis | few steps |
| Semantic: success (green), warning (amber/orange), danger/error (red), info (blue) | Status | 3–5 steps each (bg, border, text, icon) |
| Data-viz categorical | Charts (6–10 distinguishable hues, colour-blind safe) | 8–12 |
| Data-viz sequential/diverging | Heatmaps, choropleth | ramps |

Rules:
- **60-30-10** guideline (dominant neutral, secondary, accent) — rough heuristic from interior design.
- Tint grays slightly toward the brand hue (cooler/warmer) for cohesion.
- Generate shades in OKLCH for uniform perceived steps.
- Don't use colour as the only signal (WCAG 1.4.1): add icons, text, patterns.
- ~8% of men and ~0.5% of women of Northern European descent have colour vision deficiency (most commonly red–green: deuteranomaly). Avoid red/green-only status pairs; use blue/orange or add shape.
- Cultural meanings differ (red = danger in the West, luck/prosperity in China; red/green for stock markets inverted in East Asia).
- Dark mode is not inverted light mode: use dark grays (#121212 Material baseline) not pure black, desaturate brand colours, express elevation with lighter surfaces, re-check contrast.

### 4.4 Contrast

**WCAG 2.x contrast ratio:** `(L1 + 0.05) / (L2 + 0.05)` where L = relative luminance of the lighter (L1) and darker (L2) colour.

Relative luminance: `L = 0.2126·R + 0.7152·G + 0.0722·B`, with each channel linearised: `c ≤ 0.04045 ? c/12.92 : ((c+0.055)/1.055)^2.4`.

| Requirement | AA | AAA |
|-------------|-----|-----|
| Normal text | **4.5:1** (1.4.3) | 7:1 (1.4.6) |
| Large text (≥ 24px regular or ≥ 18.66px / 14pt bold) | 3:1 | 4.5:1 |
| UI components & graphical objects (borders of inputs, icons, focus indicators, chart lines) | **3:1** (1.4.11) | — |
| Disabled controls, logos, decorative | exempt | exempt |

Reference values: #767676 on white = 4.54:1 (lightest gray passing AA); #949494 on white ≈ 3.03:1 (3:1 only); pure black on white = 21:1.

**APCA** (Advanced Perceptual Contrast Algorithm, Andrew Somers) — perceptually better model with Lc values (e.g. Lc 75 for body text, Lc 60 for content text, Lc 45 for large/headlines); was a candidate for WCAG 3, still draft — use for guidance, but compliance is still WCAG 2.x ratio.

Tools: WebAIM contrast checker <https://webaim.org/resources/contrastchecker/>, Figma plugins (Stark, Contrast), Chrome DevTools contrast picker, Colour Contrast Analyser (TPGi).

---

## 5. Spacing systems

### 5.1 The 8pt grid (with 4pt half-step)

- All spacing, sizing and (ideally) line-heights are multiples of 8 (or 4 for fine adjustments).
- Rationale: most screen densities (1×, 1.5×, 2×, 3×) render multiples of 8 as whole pixels; fewer arbitrary decisions; easy communication with developers.
- Popularised by Google Material (dp grid), Bryn Jackson's "The 8-Point Grid" (2016).
- **Hard grid**: elements snap to 8pt boxes. **Soft grid**: only spacing between elements is a multiple of 8 (more practical in code).

Typical scale (tokens):

| Token | px | Typical use |
|-------|----|------------|
| space-0 | 0 | — |
| space-1 / xxs | 2 | Icon–text hairline adjustments |
| space-2 / xs | 4 | Tight inline gaps, chip padding |
| space-3 / sm | 8 | Label ↔ input, icon ↔ text |
| space-4 / md | 12 | Dense component padding |
| space-5 | 16 | Default component padding, field gap |
| space-6 / lg | 24 | Between form groups, card padding |
| space-7 / xl | 32 | Section gap |
| space-8 | 40 | — |
| space-9 / 2xl | 48 | Page section separation |
| space-10 | 64 | Large layout gaps |
| space-11 | 80–96 | Marketing hero spacing |

Rules:
- **Internal ≤ external**: padding inside a group is smaller than space between groups (Gestalt proximity).
- Start with too much whitespace, then remove (Refactoring UI).
- Use a non-linear scale; adjacent values should differ by ≥ ~25%.
- Component heights: 24/32/40/48 (small/medium/large buttons & inputs; 40 default desktop, 48 touch).
- **Density modes** (enterprise): compact (row height 32), default (40), comfortable (48). Carbon, Fluent, Material and AG Grid all support density.

Source: <https://m3.material.io/foundations/layout/understanding-layout/spacing> · <https://spec.fm/specifics/8-pt-grid>

### 5.2 Layout grids

| Grid type | Description | Use |
|-----------|------------|-----|
| **Column grid** | N columns + gutters + margins | Page layout; 12 columns (divisible by 2, 3, 4, 6) on desktop, 8 on tablet, 4 on mobile |
| **Modular grid** | Columns + rows → modules | Dashboards, card galleries |
| **Baseline grid** | Horizontal lines (4 or 8px) to align text baselines | Editorial; rarely enforced strictly in product UI |
| **Manuscript** | Single wide column | Long text, documentation |
| **Hierarchical** | Irregular, content-driven | Portals, home pages |
| **Compound** | Two grids overlaid (e.g. 4 + 3 = 12) | Complex editorial |

Typical values: gutter 16–24px (desktop 24, mobile 16), outer margin 16 (mobile) / 24–32 (tablet) / auto with max-width 1200–1440px content container (desktop). Enterprise apps often go **full-width fluid** with a side nav (240–280px expanded, 56–64px collapsed) — data tables benefit from width.

Implementation: CSS Grid (`grid-template-columns: repeat(12, 1fr); gap: 24px;`), container queries (`@container`) for component-level responsiveness, intrinsic layouts (`repeat(auto-fit, minmax(280px, 1fr))`) — see *Every Layout* (Heydon Pickering & Andy Bell).

### 5.3 Responsive breakpoints

Design for content, not devices; add a breakpoint where the layout breaks. Common sets:

| System | Breakpoints (min-width) |
|--------|------------------------|
| Bootstrap 5 | sm 576 · md 768 · lg 992 · xl 1200 · xxl 1400 |
| Tailwind CSS | sm 640 · md 768 · lg 1024 · xl 1280 · 2xl 1536 |
| Material 3 window size classes | Compact < 600 · Medium 600–839 · Expanded 840–1199 · Large 1200–1599 · Extra-large ≥ 1600 |
| Carbon | sm 320 · md 672 · lg 1056 · xlg 1312 · max 1584 |
| Fluent 2 (web) | small 320–479 · medium 480–639 · large 640–1023 · x-large 1024–1365 · xx-large 1366–1919 · xxx-large ≥ 1920 |

Practical minimum set: **< 600 (mobile) · 600–1023 (tablet) · ≥ 1024 (desktop) · ≥ 1440 (wide)**. Also test at **320 CSS px** width for WCAG 1.4.10 Reflow (= 1280px at 400% zoom).

Responsive strategies: mostly fluid · column drop · layout shifter · off-canvas (Luke Wroblewski, 2012). Mobile-first CSS (`min-width` queries) is the convention. For data-heavy enterprise UIs, consider **"responsive but desktop-optimised"**: tables → card lists on mobile, or a separate mobile task-focused app (approvals, time entry).

---

## 6. Iconography & imagery

- Icons should be accompanied by text labels except for universally known ones (search, close ✕, home, print, menu ☰ — and even ☰ is debated). NN/g: icon-only buttons are ambiguous; labels improve usability.
- Consistent set: one style (outline vs filled), one stroke width (1.5–2px at 24px), grid 24×24 with 2px padding (Material), sizes 16/20/24/32.
- Libraries: Material Symbols, Fluent System Icons, Carbon icons, Lucide, Heroicons, Phosphor, Tabler.
- `aria-hidden="true"` on decorative icons; `aria-label` on icon-only buttons.
- Illustrations for empty states/onboarding — keep culturally neutral and inclusive.

Source: <https://www.nngroup.com/articles/icon-usability/>

---

## 7. Elevation, borders & shape

- Elevation levels communicate layering: base → raised card (1) → sticky header/dropdown (2–3) → dialog (4–5). Material 3 uses tonal surface colours + shadow levels 0–5.
- Use shadows *or* borders; combining heavy borders and shadows looks dated.
- Border radius signals brand tone: 0–2px (serious/enterprise/Carbon), 4–8px (neutral; Fluent 4px, Polaris 8px…), 12–28px (friendly/Material 3 large shapes), full (pills/chips).
- Dividers: prefer whitespace and subtle background changes over lines (fewer lines = less noise — Tufte's data-ink thinking applied to UI).

---

## 8. Motion

### 8.1 Purpose

Motion should be **functional**: feedback (pressed state), orientation (where did this come from / go to), relationships (shared element transitions), state change (expand/collapse), attention (sparingly), perceived performance. Disney's 12 principles (Thomas & Johnston, 1981) — easing ("slow in, slow out"), anticipation, follow-through, staging — inform UI motion.

### 8.2 Durations

| Interaction | Duration |
|-------------|---------|
| Micro feedback (hover, press, toggle, ripple) | 50–150 ms |
| Small component changes (checkbox, tooltip, fade) | 100–200 ms |
| Medium (dropdown, expand, card) | 200–300 ms |
| Large / full-screen (modal, page transition, drawer) | 300–500 ms |
| Exits | ~20–30% shorter than entrances |
| Mobile vs desktop | Mobile ~300 ms; desktop shorter (150–200 ms); tablets ~30% longer than mobile (Material guidance) |
| Anything > 500 ms | Feels sluggish (unless decorative/onboarding) |

Material 3 duration tokens: short1–4 = 50/100/150/200 ms, medium1–4 = 250/300/350/400 ms, long1–4 = 450/500/550/600 ms, extra-long 700–1000 ms. Carbon: fast-01 70ms, fast-02 110ms, moderate-01 150ms, moderate-02 240ms, slow-01 400ms, slow-02 700ms (productive vs expressive curves).

### 8.3 Easing

| Easing | Use | CSS |
|--------|-----|-----|
| **Ease-out (decelerate)** | Elements *entering* the screen | `cubic-bezier(0, 0, 0.2, 1)` (Material "decelerate") |
| **Ease-in (accelerate)** | Elements *leaving* the screen | `cubic-bezier(0.4, 0, 1, 1)` |
| **Ease-in-out (standard)** | Elements moving between on-screen positions | `cubic-bezier(0.4, 0, 0.2, 1)` (Material standard) |
| Linear | Opacity fades, progress bars, rotation spinners, colour | `linear` |
| Spring / emphasised | Expressive, physical (iOS, Material 3 Expressive) | `cubic-bezier(0.2, 0, 0, 1)` emphasized; spring physics in Framer Motion / SwiftUI |

### 8.4 Rules

- Respect `prefers-reduced-motion: reduce` — replace movement with fades or no animation (WCAG 2.3.3 AAA Animation from Interactions; 2.2.2 Pause, Stop, Hide for auto-moving content > 5 s; 2.3.1 three flashes threshold, Level A).
- Animate only `transform` and `opacity` for 60fps (compositor-only properties).
- Don't block input during animations.
- Use a consistent motion token set (duration + easing) from the design system.
- Choreography: stagger list items 20–50 ms each, capped total.

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after { animation-duration: 0.01ms !important; transition-duration: 0.01ms !important; scroll-behavior: auto !important; }
}
```

Sources: <https://m3.material.io/styles/motion/overview> · <https://carbondesignsystem.com/elements/motion/overview/> · <https://www.nngroup.com/articles/animation-duration/>

---

## 9. Mobile & touch

### 9.1 Touch target sizes

| Source | Minimum | Notes |
|--------|---------|-------|
| Apple HIG | **44 × 44 pt** | |
| Material Design | **48 × 48 dp** (≈ 9 mm physical) | Visual element can be smaller (24dp icon) with padded touch area; 8dp spacing between targets |
| WCAG 2.2 **2.5.8 Target Size (Minimum), AA** | **24 × 24 CSS px** (or sufficient spacing so a 24px circle doesn't overlap another target) | Exceptions: inline links in text, user-agent default controls, essential |
| WCAG 2.5.5 Target Size (Enhanced), AAA | 44 × 44 CSS px | |
| Microsoft Fluent | 40 × 40 epx (touch), 32 for mouse-dense | |
| MIT Touch Lab (Dandekar et al., 2003) | Average finger pad 10–14 mm, fingertip 8–10 mm | |

### 9.2 Thumb zone

Steven Hoober's field research (2013, 1,333 observations): ~49% one-handed use, 36% cradled, 15% two-handed; people shift grips constantly; touch accuracy is best in the **centre** of the screen and worse at edges and corners. Josh Clark's *Designing for Touch* (2015) popularised the "thumb zone" heat map: bottom-centre = easy, top corners = hard.

Implications:
- Primary actions and navigation at the bottom (tab bar, bottom sheets, FAB); destructive actions away from easy reach.
- Larger targets at screen edges/corners (Hoober: ~11 mm at corners vs ~7 mm centre).
- Avoid critical controls in the top-left on large phones (back is the exception, backed by edge-swipe gesture).

### 9.3 Mobile patterns

- Bottom navigation: 3–5 destinations. Navigation drawer for more. Tab bars always labelled.
- Bottom sheets for contextual actions/filters.
- Pull to refresh; swipe actions (always provide a visible alternative — discoverability + accessibility 2.5.1 Pointer Gestures: any multipoint/path gesture needs a single-pointer alternative; 2.5.7 Dragging Movements needs a non-drag alternative).
- Input types: `type="email" | "tel" | "number" | "url"`, `inputmode="numeric" | "decimal"`, `autocomplete` tokens, `enterkeyhint`.
- Avoid hover-dependent UI.
- Respect safe areas (`env(safe-area-inset-bottom)`), dynamic type / font scaling, orientation (WCAG 1.3.4 — don't lock orientation).
- Gestures: tap, double-tap, long-press, swipe, pinch, drag, edge-swipe — hidden by nature; teach with hints.

Sources: <https://developer.apple.com/design/human-interface-guidelines/> · <https://m3.material.io/foundations/designing/structure> · <https://www.w3.org/WAI/WCAG22/Understanding/target-size-minimum.html> · <https://www.uxmatters.com/mt/archives/2013/02/how-do-users-really-hold-mobile-devices.php>

---

## 10. Visual design checklist

- [ ] One clear focal point/primary action per view
- [ ] Squint test reveals intended hierarchy
- [ ] Type scale with ≤ 6 sizes in use; body ≥ 14–16px; line length ≤ 75ch
- [ ] Line-height 1.4–1.6 for body; tabular numbers in tables
- [ ] All spacing from the token scale (4/8)
- [ ] Grouping via proximity before borders/boxes
- [ ] Text contrast ≥ 4.5:1, UI component contrast ≥ 3:1, not colour-only
- [ ] Semantic colours used consistently (red only for errors/destructive)
- [ ] Icons labelled or with tooltip + accessible name
- [ ] Works at 320px width and 200%/400% zoom
- [ ] Motion purposeful, ≤ 500 ms, reduced-motion respected
- [ ] Touch targets ≥ 44/48 on touch, ≥ 24 CSS px everywhere
- [ ] Dark mode (if offered) re-checked for contrast

## Common mistakes

- Too many font sizes and weights; hierarchy via size only.
- Light-gray placeholder/secondary text failing contrast.
- Pure black (#000) on pure white for long reading → fine contrast-wise but harsh; slightly off-black (#1a1a1a) is common.
- Equal spacing everywhere (no grouping).
- Centred body text; justified text without hyphenation.
- Designing at 1440px only, never checking 1280 laptops at 125% Windows scaling (effective ~1024px viewport) — very common in enterprise fleets.
- Animating layout properties (width/height/top), long or bouncy animations in productivity tools.

## References

- Refactoring UI (Wathan & Schoger): <https://www.refactoringui.com/>
- Butterick's Practical Typography: <https://practicaltypography.com/>
- Bringhurst, *The Elements of Typographic Style*: <https://en.wikipedia.org/wiki/The_Elements_of_Typographic_Style>
- Utopia fluid type/space: <https://utopia.fyi/>
- Type scale calculator: <https://typescale.com/>
- OKLCH colour picker: <https://oklch.com/>
- WebAIM contrast checker: <https://webaim.org/resources/contrastchecker/>
- Material 3 — typography, colour, layout, motion: <https://m3.material.io/>
- Carbon motion: <https://carbondesignsystem.com/elements/motion/overview/>
- Apple HIG: <https://developer.apple.com/design/human-interface-guidelines/>
- Every Layout: <https://every-layout.dev/>
- NN/g F-pattern: <https://www.nngroup.com/articles/f-shaped-pattern-reading-web-content/>
- NN/g icon usability: <https://www.nngroup.com/articles/icon-usability/>
- Gestalt psychology: <https://en.wikipedia.org/wiki/Gestalt_psychology>
- Color blindness: <https://en.wikipedia.org/wiki/Color_blindness>
- WCAG 2.2 Target size: <https://www.w3.org/WAI/WCAG22/Understanding/target-size-minimum.html>
- Hoober, How do users really hold mobile devices: <https://www.uxmatters.com/mt/archives/2013/02/how-do-users-really-hold-mobile-devices.php>
