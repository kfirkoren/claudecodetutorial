# Stack Research

**Domain:** Static multi-page restaurant marketing website with Hebrew RTL layout
**Researched:** 2026-03-10
**Confidence:** HIGH (HTML/CSS/RTL standards are stable; font and form recommendations based on training data through Aug 2025)

---

## Recommended Stack

### Core Technologies

| Technology | Version | Purpose | Why Recommended |
|------------|---------|---------|-----------------|
| HTML5 | Living standard | Page structure and semantics | `<html lang="he" dir="rtl">` is the canonical RTL entry point; semantic elements (`<nav>`, `<main>`, `<section>`, `<article>`, `<footer>`) are required for accessibility and search indexing |
| CSS3 | Living standard | All visual presentation and layout | CSS Grid + Flexbox handle RTL-aware layout without JavaScript; CSS custom properties enable a single-source design token system for the dark theme |
| Vanilla JS (ES2020+) | No transpilation needed | Mobile nav toggle, smooth scroll, form validation | No build step, no dependencies; ES2020 is supported by every browser that matters in 2025 |

### Font Stack

| Font | Source | Purpose | Why Recommended |
|------|--------|---------|-----------------|
| Heebo | Google Fonts (hebrew subset) | Primary body and UI text | Most popular Hebrew font on Google Fonts; clean geometric sans-serif; covers full Hebrew Unicode block including niqqud; weights 300–800 available; renders well at small sizes on mobile |
| Frank Ruhl Libre | Google Fonts (hebrew subset) | Display/heading text | Classic Hebrew serif with editorial quality; conveys elegance appropriate for fine dining; pairs well with Heebo as the display face |
| System font stack (fallback) | OS-native | Fallback when Google Fonts unavailable | `'Arial Hebrew', 'David', sans-serif` — both are standard Hebrew system fonts on macOS/Windows/iOS/Android |

**Google Fonts embed (both fonts, Hebrew subset only):**
```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Frank+Ruhl+Libre:wght@400;700&family=Heebo:wght@300;400;500;700&display=swap&subset=hebrew" rel="stylesheet">
```

Using `subset=hebrew` cuts transfer size by ~60% vs full Unicode. Using `display=swap` prevents invisible text during font load.

### CSS Architecture

| Technique | Purpose | Why This Approach |
|-----------|---------|-------------------|
| CSS custom properties (variables) | Design token system for colors, spacing, typography scale | Single source of truth; `--color-bg`, `--color-text`, `--font-display`, `--font-body` defined on `:root` and consumed everywhere; trivial to adjust the palette |
| CSS logical properties | RTL-safe spacing and positioning | `margin-inline-start` instead of `margin-left`; `padding-inline-end` instead of `padding-right`; `border-inline-start` instead of `border-left`. With `dir="rtl"` on `<html>`, logical properties automatically flip — no per-property overrides needed |
| CSS Grid (page layout) | Multi-column layouts (menu cards, story section) | Grid's `gap` property is direction-agnostic; column ordering respects document flow direction automatically |
| Flexbox (component layout) | Navigation bar, card internals, CTA groups | `flex-direction: row` reverses correctly under RTL; `gap` is logical |
| Mobile-first media queries | Responsive breakpoints | Start with 375px base; add `min-width` breakpoints at 768px (tablet) and 1200px (desktop); never use `max-width` queries as primary approach |

### Color Palette (CSS Custom Properties)

```css
:root {
  /* Dark elegant base */
  --color-bg:          #1A0F0A;   /* Near-black warm brown */
  --color-surface:     #2C1810;   /* Dark brown — cards, nav */
  --color-surface-alt: #3D2216;   /* Slightly lighter brown — hover states */
  --color-border:      #5C3420;   /* Muted brown — dividers */

  /* Text */
  --color-text:        #F5E6D3;   /* Warm beige — primary text */
  --color-text-muted:  #C4A882;   /* Muted beige — secondary text, captions */
  --color-accent:      #D4A04A;   /* Gold — CTA buttons, highlights */
  --color-accent-hover:#E8B85E;   /* Lighter gold — hover state */

  /* Typography */
  --font-display: 'Frank Ruhl Libre', 'Arial Hebrew', serif;
  --font-body:    'Heebo', 'Arial Hebrew', sans-serif;

  /* Spacing scale */
  --space-xs:  0.5rem;
  --space-sm:  1rem;
  --space-md:  1.5rem;
  --space-lg:  2.5rem;
  --space-xl:  4rem;
  --space-2xl: 6rem;
}
```

---

## File and Folder Structure

```
/
├── index.html          ← Home (hero + CTA)
├── menu.html           ← Menu page
├── story.html          ← Our Story page
├── contact.html        ← Contact + map
├── jobs.html           ← Open positions
├── css/
│   ├── base.css        ← Reset, :root variables, html/body defaults
│   ├── layout.css      ← Nav, footer, page-wrapper, grid containers
│   ├── components.css  ← Buttons, cards, form elements, section headers
│   └── pages/
│       ├── home.css    ← Hero section, home-specific styles
│       ├── menu.css    ← Menu grid, category headers
│       ├── story.css   ← Two-column story layout
│       ├── contact.css ← Map embed, contact grid
│       └── jobs.css    ← Job listing cards
├── js/
│   ├── nav.js          ← Mobile hamburger toggle
│   └── form.js         ← Reservation form validation + submission
└── assets/
    ├── images/
    │   ├── hero.jpg
    │   ├── food/       ← Dish photography
    │   └── team/       ← Chef and staff photos
    └── favicon.ico
```

**Why this structure:**
- Each page is a standalone `.html` file — no client-side routing, no SPA complexity, no JS required for navigation
- CSS is split by concern not by page — `base.css` and `layout.css` are shared across all pages via `<link>` in `<head>`; page-specific CSS only loads on its page
- No `src/` or `dist/` directories — no build step, files are served directly

---

## RTL Implementation Details

### HTML Root Declaration

Every `.html` file must have:
```html
<html lang="he" dir="rtl">
```

`lang="he"` — tells browsers, screen readers, and search engines the content is Hebrew. Affects hyphenation, font fallback selection, and spell-check.

`dir="rtl"` — establishes the base directionality for the entire document. All block elements, flex containers, grid containers, and text alignment inherit this unless overridden.

### Text Alignment

Never write `text-align: right` to achieve RTL alignment. Use `text-align: start` instead. Under `dir="rtl"`, `start` resolves to `right` automatically. If you hardcode `right`, the layout breaks if direction ever changes.

### Logical Properties Reference (use these, not physical)

| Physical (avoid) | Logical (use) | RTL effect |
|-----------------|---------------|-----------|
| `margin-left` | `margin-inline-start` | Becomes right margin |
| `margin-right` | `margin-inline-end` | Becomes left margin |
| `padding-left` | `padding-inline-start` | Becomes right padding |
| `padding-right` | `padding-inline-end` | Becomes left padding |
| `border-left` | `border-inline-start` | Becomes right border |
| `float: left` | (avoid floats entirely) | Use Flexbox/Grid |
| `text-align: right` | `text-align: start` | Resolves to right under RTL |
| `text-align: left` | `text-align: end` | Resolves to left under RTL |

**Browser support for logical properties:** Fully supported in all modern browsers (Chrome 69+, Firefox 66+, Safari 12.1+, Edge 79+). No polyfill needed for 2025 targets. [MEDIUM confidence — verified via training data; MDN was inaccessible for live verification]

### Navigation Bar (RTL-specific)

The nav logo/brand sits on the right side (the "start" in RTL) and the menu links flow rightward. Achieve this with:
```css
nav {
  display: flex;
  flex-direction: row;        /* RTL flips this to right-to-left automatically */
  align-items: center;
  justify-content: space-between;
}
```
No additional RTL overrides needed — Flexbox direction respects `dir="rtl"` on the document root.

### Hebrew Typography Rules

- **Line height:** Hebrew text needs slightly more line height than Latin. Use `line-height: 1.7` for body text (vs 1.5 for Latin). Ascenders and descenders in Hebrew occupy more space.
- **Font size floor:** Never go below `16px` (`1rem`) for body text in Hebrew — Hebrew glyphs are more complex and become unreadable at smaller sizes.
- **Letter spacing:** Do not use `letter-spacing` on Hebrew text. Unlike Latin, Hebrew is an abjad and inter-character spacing is determined by the font's internal metrics. Manually adding letter-spacing breaks glyph connections.
- **Font weight for body:** Heebo at `weight: 400` is the correct body weight. `300` is too thin for Hebrew on dark backgrounds. Use `500` for UI labels and navigation items.
- **Frank Ruhl Libre for headings:** Use at `weight: 700` for H1/H2. At `weight: 400` it reads as editorial body copy, not display text.

---

## Reservation Form: Recommended Approach

**Recommendation: Formspree (free tier) with HTML-only integration**

**Why Formspree over alternatives:**

| Option | Verdict | Reason |
|--------|---------|--------|
| `mailto:` link | Reject | Opens the user's email client; most mobile users don't have email configured; loses the on-site UX requirement |
| `<form action="mailto:...">` | Reject | Deprecated behavior; unreliable across browsers and mobile; exposes restaurant email in source |
| Formspree | Recommended | Form posts to Formspree's endpoint via standard HTML `action` attribute; no JavaScript required; free tier is 50 submissions/month which is adequate for a restaurant inquiry volume; redirects back to a thank-you page on success |
| Netlify Forms | Conditional | Only works if hosting on Netlify; excellent option if that's the host |
| Pure JS + fetch | Reject for MVP | Requires a backend or serverless function to send email; adds complexity out of scope |

**Formspree HTML integration (no JS required):**
```html
<form action="https://formspree.io/f/YOUR_FORM_ID" method="POST" dir="rtl">
  <input type="hidden" name="_language" value="he">
  <input type="hidden" name="_next" value="https://yoursite.com/thanks.html">
  <label for="name">שם מלא</label>
  <input type="text" id="name" name="name" required>
  <!-- ... remaining fields ... -->
  <button type="submit">שלח הזמנה</button>
</form>
```

`_language: he` configures Formspree's notification email to use Hebrew labels. `_next` redirects to a custom thank-you page on your own domain.

**Form fields for reservation:**
- Name (text, required)
- Phone (tel, required) — Israeli diners prefer phone callback over email confirmation
- Date (date, required)
- Time (select with time slots, required)
- Party size (select 1–12, required)
- Special requests (textarea, optional)

**Client-side validation (form.js):**
Only add JS validation to improve UX — the form must also work without JS (Formspree validates server-side). The JS should:
1. Prevent submission if required fields are empty
2. Show inline Hebrew error messages (`שדה חובה`, `תאריך לא תקין`)
3. Disable submit button after first click to prevent double-submission

---

## What NOT to Use

| Avoid | Why | Use Instead |
|-------|-----|-------------|
| CSS `float` for layout | Floats predate flexbox/grid; break under RTL without manual direction overrides; create clearfix hacks | CSS Flexbox or Grid |
| `text-align: right` for RTL alignment | Hardcodes physical direction; breaks if `dir` changes; misleads future developers | `text-align: start` |
| `margin-left` / `margin-right` / `padding-left` / `padding-right` | Physical properties ignore `dir`; must be manually mirrored for RTL | CSS logical properties (`margin-inline-start`, etc.) |
| Bootstrap or Tailwind CSS | Project explicitly requires no frameworks; both add significant CSS overhead (100KB+ unpurged); both have their own RTL configuration complexity that defeats the simplicity goal | Hand-written CSS with custom properties |
| jQuery | No DOM manipulation complexity justifies the 87KB payload; all required JS (nav toggle, form validation) fits in under 50 lines of vanilla ES2020 | Vanilla JS with `querySelector`, `addEventListener` |
| `<font>` tag or inline `style=` attributes | Unmaintainable; impossible to theme; violates separation of concerns | CSS classes with custom properties |
| Google Fonts `subset=latin` only | Default Google Fonts embeds do not include Hebrew glyphs; Hebrew text will fall back to system fonts and look inconsistent | Explicitly add `&subset=hebrew` or use `text=` parameter for display strings |
| Absolute pixel positioning for layout | Breaks on mobile; fights the normal document flow | Flexbox/Grid with `gap` and `padding` |
| CSS `direction: rtl` on individual elements | Redundant when `dir="rtl"` is on `<html>`; causes confusing specificity; only use `direction` CSS when you need to override a specific element to LTR | `dir="rtl"` on `<html>` once |

---

## Alternatives Considered

| Category | Recommended | Alternative | When Alternative is Better |
|----------|-------------|-------------|---------------------------|
| Font delivery | Google Fonts with `subset=hebrew` | Self-hosted font files | When GDPR compliance is critical (Google Fonts logs IPs); when offline-first is required; adds deployment complexity for this project |
| Form backend | Formspree | Netlify Forms | When entire project is already hosted on Netlify — Netlify Forms requires zero additional config in that case |
| Form backend | Formspree | Web3Forms / Basin | Both are Formspree-equivalent free-tier alternatives if Formspree is unavailable or limits are hit |
| Hebrew body font | Heebo | Assistant | Assistant is another popular Hebrew Google Font; geometric, slightly more rounded; either works — Heebo has higher adoption and was chosen for better weight range |
| Hebrew display font | Frank Ruhl Libre | Noto Serif Hebrew | Noto is more neutral/utilitarian; Frank Ruhl Libre has more personality appropriate for fine dining branding |
| Page structure | Multi-page (separate .html files) | Single Page Application | SPA requires a JS framework or complex vanilla routing — explicitly out of scope; multi-page is simpler, more accessible, better for SEO |

---

## Stack Patterns by Condition

**If hosted on Netlify:**
- Replace Formspree with Netlify Forms by adding `netlify` attribute to `<form>` — zero additional code needed
- Add a `netlify.toml` at root to configure redirects and cache headers for assets

**If the client later wants a CMS:**
- The HTML structure (semantic, no framework) is compatible with Decap CMS (formerly Netlify CMS) as a bolt-on
- Each page's content sections can be marked with data attributes for future CMS targeting
- This is explicitly out of scope for now — do not over-engineer for it

**If Hebrew content needs niqqud (vowel diacritics):**
- Both Heebo and Frank Ruhl Libre support the full Hebrew Unicode block including niqqud (U+05B0–U+05C7)
- No font change needed — just include the characters in the HTML

**If adding an English version later:**
- Use separate `.html` files with `lang="en" dir="ltr"` on `<html>`
- CSS logical properties mean no CSS changes are needed for LTR pages
- Add a language switcher link between `index.html` (Hebrew) and `index-en.html` (English)

---

## Version Compatibility

| Technology | Browser Minimum | Notes |
|------------|----------------|-------|
| CSS logical properties | Chrome 69, Firefox 66, Safari 12.1, Edge 79 | Fully supported in all modern browsers; safe for 2025 |
| CSS custom properties | Chrome 49, Firefox 31, Safari 9.1 | Universally supported; no concern |
| CSS Grid | Chrome 57, Firefox 52, Safari 10.1 | Universally supported; no concern |
| `dir="rtl"` HTML attribute | All browsers including IE6 | Most widely supported RTL mechanism in existence |
| ES2020 (`optional chaining`, `nullish coalescing`) | Chrome 80, Firefox 72, Safari 13.1 | Safe for 2025 target audience; no transpilation needed |
| Google Fonts `display=swap` | All modern browsers | Supported since 2018; no concern |

---

## Sources

- Training knowledge (HTML5 RTL spec, CSS logical properties, Google Fonts Hebrew subset behavior) — HIGH confidence for standards; MEDIUM confidence for specific font popularity rankings
- Formspree integration pattern — MEDIUM confidence (free tier limits may have changed since training cutoff; verify at formspree.io before implementation)
- Hebrew typography rules (line height, letter spacing) — HIGH confidence (these are typographic fundamentals, not version-sensitive)
- Google Fonts Hebrew subset availability (Heebo, Frank Ruhl Libre) — HIGH confidence (both fonts have been in Google Fonts since 2017-2018 with active maintenance)

**Note:** WebSearch and WebFetch were unavailable in this research session. All claims are based on training data through August 2025. Before implementation, verify: (1) Formspree free tier limits at formspree.io, (2) current Google Fonts Hebrew catalog at fonts.google.com/?subset=hebrew.

---
*Stack research for: Static Hebrew RTL restaurant marketing website — La Bella*
*Researched: 2026-03-10*
