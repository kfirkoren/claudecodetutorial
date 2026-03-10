# Architecture Research

**Domain:** Static multi-page restaurant website (Hebrew RTL, no build tools)
**Researched:** 2026-03-10
**Confidence:** HIGH — well-established patterns, no framework ambiguity

## Standard Architecture

### System Overview

```
┌──────────────────────────────────────────────────────────────┐
│                        Pages (HTML)                           │
│                                                               │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────┐ ┌───────┐  │
│  │index.html│ │menu.html │ │story.html│ │contact│ │jobs   │  │
│  └────┬─────┘ └────┬─────┘ └────┬─────┘ └──┬───┘ └───┬───┘  │
│       │            │            │           │         │       │
├───────┴────────────┴────────────┴───────────┴─────────┴───────┤
│                   Shared Assets (CSS + JS)                     │
│                                                               │
│  ┌────────────────┐  ┌─────────────────┐  ┌───────────────┐  │
│  │  css/main.css  │  │  js/components/ │  │  js/main.js   │  │
│  │  (global styles│  │  (nav, modal,   │  │  (shared init │  │
│  │   + variables) │  │   reservation)  │  │   on all pages│  │
│  └────────────────┘  └─────────────────┘  └───────────────┘  │
├───────────────────────────────────────────────────────────────┤
│                   Static Resources                             │
│                                                               │
│  ┌──────────────────────────────────────────────────────┐     │
│  │  images/  (hero photos, food photography, chef)      │     │
│  └──────────────────────────────────────────────────────┘     │
└───────────────────────────────────────────────────────────────┘
```

### Component Responsibilities

| Component | Responsibility | Typical Implementation |
|-----------|----------------|------------------------|
| `<nav>` (shared) | Site navigation, active page indicator, mobile hamburger | Inline HTML in every page + `js/nav.js` for toggle behavior |
| `<footer>` (shared) | Contact summary, social links, copyright | Inline HTML in every page — no JS needed |
| `css/main.css` | All visual styles: tokens, layout, typography, components | Single stylesheet linked in every `<head>` |
| `js/main.js` | Shared init: nav toggle, scroll effects, active link detection | Linked in every page `<body>` end |
| `js/reservation.js` | Modal open/close, form validation, form submission feedback | Linked only on pages that carry the modal |
| `js/menu.js` | Menu category tab switching (starters/pasta/pizza/desserts) | Linked only on `menu.html` |
| `images/` | All photography, optimized for web | Served directly, referenced by relative path |

---

## Recommended Project Structure

```
/                              # project root = site root
├── index.html                 # Home page (hero + reservation CTA)
├── menu.html                  # Menu page (categorized items)
├── story.html                 # Our Story page (about + chef)
├── contact.html               # Contact page (map + hours + form)
├── jobs.html                  # Jobs page (open positions)
│
├── css/
│   ├── main.css               # All styles — single file, no splitting
│   └── fonts.css              # @font-face declarations (if self-hosting fonts)
│
├── js/
│   ├── main.js                # Shared: nav toggle, scroll effects, active link
│   ├── reservation.js         # Modal logic + form validation
│   └── menu.js                # Category tabs on menu page
│
└── images/
    ├── hero/                  # Hero images (large, page-specific)
    │   └── home-hero.jpg
    ├── food/                  # Dish photography
    ├── chef/                  # Chef portrait
    └── logo.svg               # SVG logo (scales cleanly)
```

### Structure Rationale

- **Flat HTML root:** All `.html` files live at root so URLs are `/menu.html` not `/pages/menu.html`. Simpler relative paths for CSS/JS/images (`./css/main.css` from any page).
- **Single `css/main.css`:** No per-page stylesheets. The site has one design language — splitting creates drift. At this scale (5 pages, no build tool) one file is the right call. Unused rules on any given page are negligible.
- **`fonts.css` separated:** Keeps the large `@font-face` block out of `main.css` and makes it easy to swap font sources (Google Fonts CDN vs. self-hosted) without touching other styles.
- **`js/` split by responsibility:** `main.js` runs on every page. Page-specific scripts are only linked where they are needed — avoids loading reservation logic on the Jobs page.
- **`images/` subdirectories:** Prevents flat-file chaos as image count grows. Hero images are typically much larger files and benefit from being clearly separated.

---

## Architectural Patterns

### Pattern 1: Copy-Paste Nav and Footer (no JS includes)

**What:** The `<nav>` and `<footer>` HTML blocks are duplicated verbatim across all five `.html` files. The nav's active state is set via a CSS class added inline (`class="nav-link nav-link--active"`).

**When to use:** Always — for a plain HTML/no-build-tool project this is the correct approach. Fetch-based includes (`fetch('nav.html')`) require a running web server, break when opened as `file://`, add async complexity, and cause layout shift. Web Components are over-engineered for five pages.

**Trade-offs:**
- Pro: Zero dependencies, works on `file://`, no flash of unstyled nav, no JavaScript required for nav rendering.
- Con: Updating nav requires editing 5 files. Acceptable for a 5-page site; becomes painful at 20+ pages.

**Active link approach:**
```html
<!-- In menu.html only -->
<a href="menu.html" class="nav-link nav-link--active" aria-current="page">תפריט</a>
<!-- All other pages -->
<a href="menu.html" class="nav-link">תפריט</a>
```

### Pattern 2: CSS Custom Properties for Design Tokens

**What:** All colors, spacing scale, font sizes, and transition durations are declared as CSS custom properties on `:root`. All other rules reference variables — never raw values.

**When to use:** Always on a project with a strong visual identity (dark/elegant palette). Makes future color tweaks a one-line change. Critical for consistency with a bespoke palette.

**Trade-offs:**
- Pro: Single source of truth, easy theme changes, readable selectors.
- Con: IE11 incompatibility (irrelevant for a Hebrew-language site targeting Israeli diners in 2026).

**Example:**
```css
:root {
  /* Color tokens */
  --color-bg:         #1a0f0a;   /* near-black warm */
  --color-surface:    #2c1810;   /* dark brown */
  --color-accent:     #c9a96e;   /* warm gold */
  --color-text:       #f5e6d3;   /* beige */
  --color-text-muted: #a89080;

  /* Spacing scale */
  --space-xs:  0.5rem;
  --space-sm:  1rem;
  --space-md:  2rem;
  --space-lg:  4rem;
  --space-xl:  8rem;

  /* Typography */
  --font-serif: 'Noto Serif Hebrew', serif;
  --font-sans:  'Heebo', sans-serif;
  --text-base:  1rem;
  --text-lg:    1.25rem;
  --text-xl:    2rem;
  --text-hero:  clamp(2.5rem, 8vw, 6rem);
}
```

### Pattern 3: Flat CSS with BEM-lite Naming

**What:** One stylesheet, selectors use a simplified BEM convention — block names map to semantic sections (`.nav`, `.hero`, `.menu-card`, `.footer`). No deep nesting. Modifiers use `--modifier` suffix.

**When to use:** Always for a vanilla CSS project of this size. Full BEM is verbose without a preprocessor. CSS Modules or scoped styles require a build tool. Flat BEM-lite gives predictable specificity without tooling.

**Trade-offs:**
- Pro: Low specificity, easy to override, no cascade surprises.
- Con: Requires naming discipline; no enforcement beyond convention.

**Recommended CSS file order:**
```
1. @import fonts.css
2. :root { /* custom properties */ }
3. Reset / base (*, body, a, img)
4. Typography utilities (.text-hero, .text-section-title)
5. Layout utilities (.container, .grid-2col)
6. Components — in page-build order:
   .nav → .hero → .section → .menu → .story → .contact → .jobs → .footer
7. Modal / overlay
8. Utilities / states (.sr-only, .hidden, .active)
9. @media queries at bottom (mobile-first overrides for ≥768px, ≥1200px)
```

---

## RTL Architecture Decisions

RTL is not an afterthought — it must be baked into the CSS foundation from day one.

### The `dir` and `lang` Root Declaration

Every `.html` file must carry both attributes on `<html>`. This is the single source of RTL truth:

```html
<html lang="he" dir="rtl">
```

### Logical Properties Over Physical Properties

Use CSS logical properties everywhere — they respect the document writing direction automatically:

| Physical (avoid) | Logical (prefer) | Effect in RTL |
|------------------|------------------|---------------|
| `margin-left` | `margin-inline-start` | Applies to right side |
| `margin-right` | `margin-inline-end` | Applies to left side |
| `padding-left` | `padding-inline-start` | Right-facing padding |
| `text-align: left` | `text-align: start` | Aligns to right |
| `float: right` | `float: inline-end` | RTL-correct float |

Exception: `margin-inline` and `padding-inline` shorthand (sets both sides equally) needs no RTL adjustment — use freely.

### Flexbox in RTL

`flex-direction: row` naturally reverses in RTL — items flow right-to-left. **Do not set `flex-direction: row-reverse` to "fix" RTL.** That compensates in the wrong layer.

```css
/* This is correct — flex row in RTL flows items RTL automatically */
.nav-links {
  display: flex;
  gap: var(--space-md);
  /* No direction override needed */
}
```

Watch for: icon + text combinations. In LTR a chevron pointing right (→) means "next". In RTL the same chevron should point left (←). Use `transform: scaleX(-1)` on directional icons when the HTML direction is RTL, or use bidirectional icon variants.

### Fonts

Hebrew text requires a Hebrew-capable typeface. Two recommended pairings for this aesthetic:
- **Display/headings:** `Noto Serif Hebrew` — elegant, has serif weight for premium feel
- **Body:** `Heebo` — clean, high legibility at small sizes, widely used in Israeli product design

Both are available on Google Fonts. For this no-build-tool project, load via `<link>` in `<head>`:

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Heebo:wght@300;400;600&family=Noto+Serif+Hebrew:wght@400;700&display=swap">
```

`font-display: swap` is set by Google Fonts by default when using `display=swap` — prevents invisible text during font load.

---

## Data Flow

### User Interaction Flow

```
Page loads
    |
    v
main.js runs:
  - Sets active nav link for current page (reads window.location.pathname)
  - Initializes nav mobile toggle listener
  - Initializes scroll-based nav transparency
    |
    v
User clicks "Reserve a Table" CTA
    |
    v
reservation.js:
  - Opens modal overlay (adds .is-open class, sets aria-hidden="false")
  - Traps focus inside modal
    |
    v
User submits form
    |
    v
reservation.js:
  - Validates fields (name required, date >= today, time selected, party size 1-20)
  - On error: shows inline Hebrew error messages
  - On success (no backend): replaces form with Hebrew success message
    |
    v
User closes modal
    |
    v
reservation.js:
  - Removes .is-open, restores focus to trigger element, resets form
```

### Menu Page Tab Flow

```
User clicks category tab (e.g., "פסטה")
    |
    v
menu.js:
  - Removes .active from all tabs
  - Adds .active to clicked tab
  - Hides all .menu-category sections
  - Shows matching .menu-category[data-category="pasta"]
```

---

## Build Order

Build the project in this sequence — each step produces a deliverable that can be reviewed:

1. **Shared shell (skeleton)** — `index.html` with final nav, footer, and linked CSS/JS. Establishes file structure, confirms fonts load, confirms RTL renders. All other pages copy this shell.
2. **Design token layer** — `css/main.css` with `:root` variables, reset, typography, and `.container`. No component styles yet. Confirms palette and font scale.
3. **Home page** — Hero section, CTA button, reservation modal (no validation yet). The highest-impact page; sets the visual bar for the whole site.
4. **Reservation form** — Add validation logic to `reservation.js`. Done here because the modal is already built and it unblocks the core user journey.
5. **Menu page** — Menu grid, category tabs, `menu.js`. This is content-heavy but structurally straightforward.
6. **Remaining pages** — `story.html`, `contact.html` (with Google Maps embed), `jobs.html`. Each is a simple content layout reusing established CSS patterns.
7. **Polish pass** — Scroll animations, nav transparency on scroll, hover effects, responsive breakpoints audit across all pages.

---

## Anti-Patterns

### Anti-Pattern 1: Per-Page Stylesheets

**What people do:** Create `index.css`, `menu.css`, `contact.css` etc. to "keep styles organized."
**Why it's wrong:** Visual inconsistencies creep in when the same component (a button, a card) gets styled slightly differently in two files. Debugging requires hunting across files. No build tool means no deduplication.
**Do this instead:** One `main.css`. Use clear section comments (`/* ===== MENU PAGE ===== */`) to navigate. Modern editors handle large CSS files fine.

### Anti-Pattern 2: JS Include Hacks for Shared Nav

**What people do:** Use `fetch()` to load a `nav.html` fragment into each page, or use an iframe.
**Why it's wrong:** `fetch()` requires HTTP (breaks `file://` local development), adds async complexity, causes layout shift (nav appears after page renders), and complicates active-link detection. Iframes add unnecessary nesting and styling friction.
**Do this instead:** Copy-paste the nav HTML. 5 files is not a maintenance burden. Update all 5 when the nav changes.

### Anti-Pattern 3: Setting `direction: ltr` on Individual Components

**What people do:** Notice that some component "looks wrong" in RTL and fix it by adding `direction: ltr` to that component.
**Why it's wrong:** This defeats the RTL contract. Text inside that component will render LTR, breaking readability for Hebrew users. Numbers and mixed-script content become unpredictable.
**Do this instead:** Identify what "looks wrong" — it's almost always a directional icon or a physical-property margin. Fix the specific property with a logical property or icon transform instead of overriding direction.

### Anti-Pattern 4: Inline Styles for Layout

**What people do:** Use `style="margin-left: 20px"` on individual elements for "quick fixes."
**Why it's wrong:** Inline styles have higher specificity than stylesheets, cannot be overridden cleanly, are invisible in CSS tooling, and will conflict with logical properties in RTL.
**Do this instead:** Add a utility class or modify the component's CSS rule. Keep all layout decisions in `main.css`.

### Anti-Pattern 5: Non-Semantic Container Soup

**What people do:** Use `<div>` for everything — nav, content sections, footer.
**Why it's wrong:** Screen readers used by visually impaired Hebrew users rely on semantic landmarks. It also fails accessibility requirements and makes the HTML harder to maintain.
**Do this instead:** `<nav>`, `<main>`, `<section aria-labelledby="...">`, `<article>`, `<footer>`. Reserve `<div>` for purely visual layout containers with no semantic meaning.

---

## Integration Points

### External Services

| Service | Integration Pattern | Notes |
|---------|---------------------|-------|
| Google Fonts | `<link>` in `<head>` with `preconnect` | Add `display=swap`; include Heebo + Noto Serif Hebrew |
| Google Maps | `<iframe>` embed on `contact.html` | Get embed URL from Google Maps "Share > Embed"; no API key required for basic embed |
| Reservation form | Client-side only (no backend) | On submit: validate in JS, show Hebrew success message, do not actually POST anywhere |

### Internal Boundaries

| Boundary | Communication | Notes |
|----------|---------------|-------|
| HTML pages ↔ CSS | `<link rel="stylesheet" href="css/main.css">` in each `<head>` | Relative path works from flat root |
| HTML pages ↔ JS | `<script src="js/main.js" defer>` before `</body>` | `defer` ensures DOM is ready; no DOMContentLoaded wrapper needed |
| `main.js` ↔ `reservation.js` | None — they are independent | `reservation.js` handles its own event listeners |
| `reservation.js` ↔ modal DOM | `document.getElementById('reservation-modal')` | Modal must have `id="reservation-modal"` in HTML |

---

## Scaling Considerations

| Scale | Architecture Adjustments |
|-------|--------------------------|
| Current (5 pages, 1 language) | This architecture — no changes needed |
| +5 pages added | Still fine — copy shell, update nav in all files |
| English version added | Add `lang="en" dir="ltr"` variants as separate files (`/en/index.html`). Shared CSS handles both directions via logical properties |
| CMS integration later | Replace static HTML with templates (e.g., 11ty) — the file structure maps directly to Eleventy's layout system |
| Real reservation backend | Add `action` attribute to form, change JS to actual `fetch()` POST — no structural changes needed |

---

## Sources

- MDN Web Docs — CSS Logical Properties: https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_logical_properties_and_values
- MDN Web Docs — Writing Modes and RTL: https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_writing_modes
- W3C Internationalization — RTL layout: https://www.w3.org/International/articles/inline-bidi-markup/uba-basics
- Google Fonts — Heebo: https://fonts.google.com/specimen/Heebo
- Google Fonts — Noto Serif Hebrew: https://fonts.google.com/noto/specimen/Noto+Serif+Hebrew
- Confidence: HIGH — all patterns are well-established; no experimental APIs used

---
*Architecture research for: Static multi-page Hebrew RTL restaurant website (La Bella)*
*Researched: 2026-03-10*
