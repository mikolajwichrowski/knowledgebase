# CSS and Styling

> **TL;DR** — Understand the box model, the cascade (origin → layers → specificity → order), and pick flexbox for one-dimensional flow and grid for two-dimensional layout. Modern CSS (cascade layers, container queries, `:has()`, nesting, custom properties, logical properties) removes most reasons for preprocessors. Choose one styling strategy per codebase (Tailwind or CSS Modules are the pragmatic defaults) and drive it from design tokens.

Related: [React](react.md) · [Vue](vue.md) · [UX/UI](../01-ux-ui/README.md)

---

## 1. Box model

```
+---------------------------------------+  margin (transparent, can collapse vertically)
|  +---------------------------------+  |  border
|  |  +---------------------------+  |  |  padding (has background)
|  |  |        content            |  |  |
|  |  +---------------------------+  |  |
|  +---------------------------------+  |
+---------------------------------------+
```

| Property | Effect |
|---|---|
| `box-sizing: content-box` (default) | `width` = content only; padding/border add to it |
| `box-sizing: border-box` | `width` includes padding + border — set globally |
| Margin collapsing | Adjacent vertical margins of block elements merge (largest wins). Not in flex/grid containers |
| `display` outer/inner | `block`/`inline` outer; `flow`/`flex`/`grid` inner (`display: inline flex`) |
| Formatting context | `display: flow-root` creates a new BFC (contains floats, stops margin collapse) |

Baseline reset:

```css
*, *::before, *::after { box-sizing: border-box; }
* { margin: 0; }
html { -webkit-text-size-adjust: none; text-size-adjust: none; }
body { min-height: 100dvh; line-height: 1.5; -webkit-font-smoothing: antialiased; }
img, picture, video, canvas, svg { display: block; max-width: 100%; }
input, button, textarea, select { font: inherit; }
p, h1, h2, h3, h4, h5, h6 { overflow-wrap: break-word; }
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after { animation-duration: .01ms !important; transition-duration: .01ms !important; scroll-behavior: auto !important; }
}
```

---

## 2. The cascade

Order in which conflicting declarations are resolved:

1. **Origin and importance** — user-agent < user < author; `!important` reverses the order.
2. **Context** — shadow DOM encapsulation.
3. **Element-attached styles** (`style=""`).
4. **Cascade layers** (`@layer`) — earlier layers lose to later ones; unlayered styles beat all layers (for normal declarations).
5. **Specificity**.
6. **Scope proximity** (`@scope`).
7. **Order of appearance**.

### Specificity

Calculated as a triple **(ID, CLASS, TYPE)**, compared left to right.

| Selector | Specificity |
|---|---|
| `*`, `:where(...)` | (0,0,0) |
| `p`, `::before` | (0,0,1) |
| `.btn`, `[type=text]`, `:hover` | (0,1,0) |
| `#nav` | (1,0,0) |
| `nav .btn:hover` | (0,2,1) |
| `:is(#a, .b)`, `:not(#a)`, `:has(#a)` | takes the most specific argument → (1,0,0) |
| inline `style` | beats all selectors |
| `!important` | beats everything normal (avoid outside utilities/resets) |

Tips: keep specificity flat (single class), use `:where()` in libraries/resets so consumers can override easily, avoid IDs for styling.

### Cascade layers

```css
@layer reset, tokens, base, components, utilities;

@layer reset { /* … */ }
@layer components {
  .card { padding: var(--space-4); }
}
@layer utilities {
  .p-0 { padding: 0; }      /* wins over .card regardless of specificity */
}
/* Third-party CSS into a low-priority layer */
@import url("vendor.css") layer(vendor);
```

Layers solve specificity wars between resets, third-party CSS, components and utilities. Tailwind v4 is built on native cascade layers.

---

## 3. Flexbox vs Grid

| | Flexbox | Grid |
|---|---|---|
| Dimension | One (row *or* column) | Two (rows *and* columns) |
| Sizing driven by | Content (content-out) | Container (layout-in) |
| Best for | Nav bars, button groups, centering, distributing items in a line, wrapping tags | Page layouts, card grids, forms with aligned labels, overlapping items |
| Alignment | `justify-content`, `align-items`, `gap` | Same plus `justify-items`, `place-*`, named areas |
| Gotcha | `min-width: auto` prevents shrinking → set `min-width: 0` on flex children with long content | Implicit tracks; `1fr` has `min-content` minimum → `minmax(0, 1fr)` |

```css
/* Centering */
.center { display: grid; place-items: center; }

/* Responsive card grid without media queries */
.cards { display: grid; gap: 1rem; grid-template-columns: repeat(auto-fill, minmax(min(16rem, 100%), 1fr)); }

/* Holy grail with named areas */
.layout {
  display: grid; min-height: 100dvh;
  grid-template: "header header" auto "nav main" 1fr "footer footer" auto / 16rem 1fr;
}
.layout > header { grid-area: header; }

/* Toolbar: push last item right */
.toolbar { display: flex; gap: .5rem; align-items: center; }
.toolbar > .spacer { margin-inline-start: auto; }

/* Subgrid: align card internals across siblings */
.card { display: grid; grid-row: span 3; grid-template-rows: subgrid; }
```

---

## 4. Modern CSS features (baseline in evergreen browsers)

| Feature | Use |
|---|---|
| Custom properties `--x` | Theming, tokens, runtime values; inherit; can be typed with `@property` |
| Native nesting | `.card { &:hover {…} .title {…} }` |
| `:has()` | Parent/relational selector: `form:has(:invalid) button { opacity: .5 }` |
| `:is()`, `:where()` | Grouping (with/without specificity) |
| Container queries | Components respond to their container, not the viewport |
| Container query units | `cqi`, `cqb` |
| Logical properties | `margin-inline`, `padding-block`, `inset-inline-start` — RTL-ready |
| Viewport units | `dvh`, `svh`, `lvh` fix mobile toolbar issues |
| `clamp()`, `min()`, `max()` | Fluid typography/spacing |
| `color-mix()`, `oklch()`, relative colors | Perceptual colour manipulation |
| `light-dark()` + `color-scheme` | Theme-aware values |
| `@scope` | Scoped styles with proximity |
| View Transitions API | Animated page/state transitions |
| Anchor positioning | Tooltips/popovers tethered to elements without JS (Chromium first, spreading) |
| Popover API (`popover` attr) | Native top-layer popovers |
| `text-wrap: balance / pretty` | Better headline wrapping |
| Scroll-driven animations | `animation-timeline: scroll()` |

### Container queries

```css
.card-list { container: cards / inline-size; }

.card { display: grid; gap: 1rem; }
@container cards (min-width: 40rem) {
  .card { grid-template-columns: 12rem 1fr; }
}
```

Use media queries for page-level layout and user preferences (`prefers-color-scheme`, `prefers-reduced-motion`, `hover`, `pointer`), container queries for reusable components.

### Fluid type

```css
:root { --step-0: clamp(1rem, 0.95rem + 0.25vw, 1.125rem); --step-3: clamp(1.75rem, 1.4rem + 1.6vw, 2.5rem); }
h1 { font-size: var(--step-3); }
```

---

## 5. Styling strategies

| Approach | Pros | Cons | Fit |
|---|---|---|---|
| Plain CSS + BEM | No tooling, standard | Global namespace discipline needed | Small sites, server-rendered apps |
| **CSS Modules** | Local class names by default, plain CSS, zero runtime | Separate files, composing variants manually | Component libraries, teams fluent in CSS |
| **Tailwind CSS** | Fast iteration, consistent tokens, no naming, tiny output, co-located | Long class strings, learning curve, needs component abstraction for reuse | Product apps, design systems with shadcn/ui |
| Vue scoped styles | Built in, simple | Vue-only | Vue apps |
| Runtime CSS-in-JS (styled-components, Emotion) | Dynamic styles, colocated | Runtime cost, problems with RSC/streaming; styled-components in maintenance mode | Legacy |
| Zero-runtime CSS-in-JS (vanilla-extract, Panda CSS, StyleX, Linaria) | Type-safe, extracted at build | Build config complexity | Type-safe design systems |
| Sass/Less | Mixins, loops | Mostly superseded by native features | Legacy |

### Tailwind essentials (v4)

```css
/* app.css */
@import "tailwindcss";

@theme {
  --color-brand-500: oklch(0.62 0.19 255);
  --font-sans: "Inter", system-ui, sans-serif;
  --radius-card: 0.75rem;
}
```

```tsx
<button className="inline-flex items-center gap-2 rounded-card bg-brand-500 px-4 py-2 text-white
                   hover:bg-brand-500/90 focus-visible:outline-2 focus-visible:outline-offset-2
                   disabled:opacity-50 md:px-6">
  Save
</button>
```

- Manage variants with `cva` / `tailwind-variants`, merge conflicting classes with `tailwind-merge` (`cn()` helper).
- Extract **components**, not `@apply` classes, for reuse.
- Use the Prettier plugin to sort classes.
- v4: CSS-first config (`@theme`), automatic content detection, native cascade layers, Lightning CSS-based engine.

### CSS Modules

```css
/* Button.module.css */
.root { padding: var(--space-2) var(--space-4); border-radius: var(--radius-md); }
.primary { background: var(--color-action-primary-bg); color: var(--color-action-primary-fg); }
```

```tsx
import styles from "./Button.module.css";
<button className={clsx(styles.root, variant === "primary" && styles.primary)} />
```

---

## 6. Design tokens

Tokens are named design decisions stored in a platform-agnostic format and transformed into CSS variables, Tailwind theme, iOS/Android resources, etc.

| Tier | Example | Purpose |
|---|---|---|
| Primitive / reference | `--blue-600: oklch(...)`, `--space-4: 1rem` | Raw palette and scales |
| Semantic / system | `--color-bg-surface`, `--color-text-danger`, `--color-action-primary-bg` | Meaning; theme switches remap these |
| Component | `--button-primary-bg` | Optional, component-level overrides |

```css
:root {
  --blue-600: oklch(0.55 0.2 255);
  --gray-50: oklch(0.98 0 0);
  --gray-900: oklch(0.2 0 0);

  --color-bg-surface: var(--gray-50);
  --color-text-default: var(--gray-900);
  --color-action-primary-bg: var(--blue-600);
}
[data-theme="dark"] {
  --color-bg-surface: var(--gray-900);
  --color-text-default: var(--gray-50);
}
```

Tooling: W3C Design Tokens Community Group format (stable spec released 2025), Style Dictionary, Tokens Studio for Figma, Figma Variables. Components reference **semantic** tokens only.

See [UX/UI](../01-ux-ui/README.md) for design systems and accessibility.

---

## 7. Accessibility in CSS

- Never `outline: none` without a replacement; style `:focus-visible`.
- Contrast ≥ 4.5:1 (text), ≥ 3:1 (large text, UI components, focus indicators).
- Don't hide content with `display:none` if it must be read — use a `.sr-only` (visually-hidden) utility.
- Respect `prefers-reduced-motion`, `prefers-contrast`, `forced-colors`.
- Use `rem` for font sizes so user zoom/preferences work; test at 200% zoom and 320 px width (reflow).
- Minimum target size 24×24 CSS px (WCAG 2.2 AA), 44×44 recommended.

---

## 8. Performance

- Critical CSS inlined for above-the-fold; load the rest non-blocking.
- Avoid layout thrashing: animate `transform` and `opacity` only.
- `content-visibility: auto` + `contain-intrinsic-size` for long offscreen sections.
- `will-change` sparingly.
- `font-display: swap`, preload key fonts, subset, use variable fonts; `size-adjust` to reduce CLS.
- Reserve space for images/embeds (`width`/`height` attributes or `aspect-ratio`) → avoid CLS.
- Purge unused CSS (automatic in Tailwind).

---

## 9. Common mistakes

| Mistake | Fix |
|---|---|
| Fighting specificity with `!important` | Cascade layers, flat class selectors |
| Fixed heights on text containers | `min-height`, let content flow |
| `100vh` on mobile | `100dvh` / `100svh` |
| Flex child overflows | `min-width: 0` |
| `z-index: 9999` arms race | Define z-index tokens; understand stacking contexts (`transform`, `opacity < 1`, `position` + `z-index`, `isolation: isolate`) |
| Media queries for components | Container queries |
| Physical properties in i18n apps | Logical properties |
| Magic numbers | Tokens/scales |
| Colour-only state indication | Add icon/text |
| Hover-only interactions | Ensure keyboard/touch alternatives (`@media (hover: hover)`) |

## References

- MDN CSS — https://developer.mozilla.org/en-US/docs/Web/CSS
- MDN Box model — https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/Styling_basics/Box_model
- MDN Cascade — https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_cascade/Cascade
- MDN Specificity — https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_cascade/Specificity
- MDN `@layer` — https://developer.mozilla.org/en-US/docs/Web/CSS/@layer
- MDN Flexbox — https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_flexible_box_layout
- MDN Grid — https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_grid_layout
- MDN Container queries — https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_containment/Container_queries
- web.dev Learn CSS — https://web.dev/learn/css
- Baseline (web platform feature status) — https://web.dev/baseline
- Can I use — https://caniuse.com
- Tailwind CSS docs — https://tailwindcss.com/docs
- CSS Modules — https://github.com/css-modules/css-modules
- Design Tokens Community Group — https://www.designtokens.org
- Style Dictionary — https://styledictionary.com
- Every Layout — https://every-layout.dev
- Josh W. Comeau, CSS reset — https://www.joshwcomeau.com/css/custom-css-reset/
- CSS-Tricks flexbox guide — https://css-tricks.com/snippets/css/a-guide-to-flexbox/
- CSS-Tricks grid guide — https://css-tricks.com/snippets/css/complete-guide-grid/
