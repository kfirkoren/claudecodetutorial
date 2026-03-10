# Feature Research

**Domain:** High-end restaurant marketing website (static, Hebrew/RTL, Italian cuisine)
**Researched:** 2026-03-10
**Confidence:** HIGH for table stakes (industry consensus), MEDIUM for differentiators

---

## Feature Landscape

### Table Stakes (Users Expect These)

Features users assume exist. Missing these = product feels incomplete or unprofessional.

| Feature | Why Expected | Complexity | Notes |
|---------|--------------|------------|-------|
| Full-screen hero section | First impression sets the premium tone; every high-end restaurant site uses this pattern | LOW | Background image or video with overlay; restaurant name + tagline + primary CTA overlaid. For static site: CSS `background-image`, `object-fit: cover`, `min-height: 100vh`. |
| "Reserve a Table" CTA — above the fold | Users arrive with intent to dine; one-click path to reservation is non-negotiable | LOW | Sticky or hero-embedded button. On a static site, scrolls to or opens a contact form on the same page or reservation page. |
| Categorized menu page | Users want to browse dishes before deciding; no menu = site feels unfinished | MEDIUM | Grouped sections (starters, pasta, pizza, desserts) with dish name, description, price. Hebrew requires RTL text flow, price alignment right-to-left. |
| Contact information — address, phone, hours | Diners need to know how to reach the restaurant; absence creates friction | LOW | Dedicated contact page or footer. Address, phone number (clickable `tel:` link on mobile), opening hours table. |
| Embedded map | Users expect to see where the restaurant is before committing to visit | LOW | Google Maps embed `<iframe>` in contact page. No API key needed for basic embed. |
| Mobile-first responsive layout | Majority of restaurant site traffic is mobile; non-responsive = unusable | MEDIUM | CSS media queries, flexible grids. RTL adds complexity: logical properties (`margin-inline-start`) preferred over directional (`margin-left`). |
| Navigation bar — all pages linked | Users expect to move between sections freely; broken nav = trust loss | LOW | Fixed or sticky `<nav>` with links to all pages. Mobile: hamburger menu with vanilla JS toggle. Active state highlighting. |
| About / Our Story page | Diners at high-end restaurants care about the narrative: who is the chef, what is the concept | LOW | Static text + image layout. Chef bio, restaurant founding story. Placeholder content sufficient for v1. |
| Food photography — visible above the fold | Premium dining is sold visually; text-only pages feel cheap | LOW | Large, high-quality image in hero. Secondary images on menu and story pages. Placeholder: use CC-licensed Italian food imagery or color-accurate placeholders. |
| Consistent dark, elegant visual identity | Users form quality judgments within 50ms; visual mismatch = wrong audience signal | MEDIUM | Unified color palette (dark brown #2C1810, beige #F5E6D3, black), typography (serif for headings, clean sans for body), spacing rhythm across all pages. |
| Footer with key info | Standard web convention; users scroll to footer for address, hours, socials | LOW | Address, phone, hours, copyright. Optional: social media icon links. |
| Semantic HTML5 structure | Accessibility requirement; also affects SEO and screen reader usability | LOW | `<nav>`, `<main>`, `<section>`, `<article>`, `<footer>` on every page. `lang="he"` and `dir="rtl"` on `<html>`. |
| Fast initial load | Premium perception is destroyed by slow loads; Google data shows >3s = significant bounce rate increase | MEDIUM | No framework overhead is already an advantage. Optimize: compress images (WebP where possible), inline critical CSS, defer non-critical JS. |

---

### Differentiators (Competitive Advantage)

Features that set the site apart. Not required, but elevate the experience.

| Feature | Value Proposition | Complexity | Notes |
|---------|-------------------|------------|-------|
| Cinematic scroll animations | Creates a "motion picture" feel that matches premium dining atmosphere; reinforces dark elegant brand | MEDIUM | Vanilla JS Intersection Observer API for fade-in/slide-up on scroll. No library needed. Keep animations subtle: opacity 0→1, translateY 20px→0. Over-animation is worse than none. |
| Menu with visual section anchors | Diners browse menus non-linearly; anchor links within menu page improve navigation | LOW | In-page `<a href="#pasta">` anchor links at top of menu page. Works without JS. |
| Sticky header that transforms on scroll | Header starts transparent over hero (cinematic), becomes solid on scroll (functional) | LOW | 10 lines of vanilla JS: add class `.scrolled` at scroll position > 80px, CSS transition for background-color. |
| Parallax hero scroll effect | Depth effect on hero image creates premium, immersive feel; common on high-end hospitality sites | LOW-MEDIUM | CSS `background-attachment: fixed` for simple parallax (works on desktop; disable on mobile for performance). Or vanilla JS scroll handler for more control. |
| Reservation form with client-side validation | Prevents user frustration from submitting incomplete forms; increases form completion rate | MEDIUM | HTML5 `required` + `type="date"/"time"/"number"` attributes handle basic validation. Vanilla JS for custom error messages in Hebrew. No backend — form submits via `mailto:` or `action` pointing to a form service (Formspree). |
| Jobs / Careers page | Differentiates from typical restaurant sites; useful for restaurant operations; signals legitimacy | LOW | Static list of positions with description. No application form needed for v1 — phone/email contact suffices. |
| Smooth page transitions | Multi-page sites typically feel jarring between pages; a fade transition adds polish | MEDIUM | CSS animation on `<body>` load (`opacity: 0 → 1`). JS can add exit animation before `window.location` change. Keep it fast (< 300ms). |
| Typography pairing — Hebrew serif + Latin display | Hebrew premium fonts are limited; pairing a Hebrew body font (Frank Ruhl Libre) with a Latin display font (Playfair Display or Cormorant Garamond) for decorative elements creates visual richness | LOW | Google Fonts: Frank Ruhl Libre (Hebrew-capable, elegant), Cormorant Garamond (for Latin decorative accents like dish names in Italian). CSS `font-family` with Hebrew font first. |
| "Signature dish" spotlight section on home page | Draws attention to the restaurant's best offering; increases desire before user reaches menu | LOW | One featured dish card in the home page: large image, dish name, short description, "View full menu" link. Pure HTML/CSS. |

---

### Anti-Features (Things to Deliberately NOT Build for a Static Site)

| Feature | Why Requested | Why Problematic | Alternative |
|---------|---------------|-----------------|-------------|
| Online reservation system with real-time availability | Seems like table stakes for premium restaurants | Requires backend, database, calendar sync (OpenTable API or similar). Impossible on a static site without a paid third-party service. Adds auth, session, payment complexity. | Contact form that sends an email inquiry. Add a "Book via phone" CTA. Third-party embed (OpenTable widget) can be added later if needed without rewriting the site. |
| Live menu updates via CMS | Non-technical restaurant owner wants to update menu prices | No backend = no CMS. Adding a headless CMS (Contentful, Sanity) introduces build steps, API keys, and a deployment pipeline that contradicts the "static HTML only" constraint. | Hardcode placeholder content clearly labeled. Document which HTML sections to edit when real content arrives. Keep structure simple enough that a dev can update in 5 minutes. |
| Photo gallery lightbox with backend | Full gallery management with upload | Backend required for storage and admin. Overkill for a branding site. | Static gallery grid: CSS Grid layout, a few curated high-quality images. Vanilla JS lightbox (no library) can be added if gallery is needed — opens image in a modal overlay. |
| Multi-language toggle (Hebrew + English) | Restaurant owner may want English for tourists | Doubles content maintenance, requires language-switching JS, complicates RTL/LTR layout coexistence. Out of scope per PROJECT.md. | Hebrew only for v1. If English is needed, create a separate `/en/` subdirectory with duplicated HTML pages. Do not mix languages in a single page. |
| Social media feed embed (live Instagram grid) | Keeps site dynamic, shows activity | Third-party embeds (Instagram API) require OAuth, token rotation, rate limits. They degrade performance and break when APIs change. | Static curated photos. Link to Instagram profile icon in footer. |
| Cookie consent banner / GDPR popup | Seems necessary for compliance | A static site with no analytics, no tracking scripts, no cookies has nothing to disclose. Adding a banner for a site that doesn't need one adds friction. | If Google Analytics or any tracking is added later, add consent banner at that point. For now: zero tracking, zero cookie banner needed. |
| Animated loading screen / splash screen | Looks premium in isolation | Adds 1-3 seconds of forced delay before any content appears. Users hate it. Penalizes fast connections. Signals slow site to Google. | Use fast initial render instead. CSS fade-in on page load takes < 300ms and feels polished without blocking. |
| Custom cursor / cursor effects | Seen on some luxury brand sites | Breaks accessibility expectations, confuses mobile users (no cursor on touch), and distracts from content. Non-standard interactions reduce conversion. | Invest the effort in typography and photography instead — that's where premium perception actually comes from. |

---

## Feature Dependencies

```
[Reservation CTA Button]
    └──requires──> [Reservation Form] or [Phone number visible]

[Reservation Form]
    └──requires──> [Contact Page] or [Modal/Overlay on home page]
    └──enhances with──> [Client-side validation (JS)]

[Navigation Bar]
    └──requires──> [All pages exist as .html files]
    └──enhances with──> [Mobile hamburger toggle (JS)]

[Sticky header transform on scroll]
    └──requires──> [Navigation Bar]
    └──requires──> [Hero Section tall enough to scroll past]

[Scroll animations (Intersection Observer)]
    └──requires──> [Semantic HTML structure with targetable sections]

[Parallax hero]
    └──requires──> [Hero Section with background image]
    └──conflicts──> [Mobile performance] (disable on mobile via media query)

[Typography pairing]
    └──requires──> [Google Fonts loaded in <head>]
    └──enhances──> [All pages] (apply via CSS variables)

[Menu anchor navigation]
    └──requires──> [Menu page with section IDs]
```

### Dependency Notes

- **Reservation CTA requires a destination:** The CTA button must link to something — a form on the contact page, an in-page modal, or a `tel:` link. This must be decided before building the hero section.
- **Navigation requires all pages exist:** Build the nav bar after all page files are created so all links can be verified. Build placeholder pages first, then wire them up.
- **Scroll animations require semantic structure:** The Intersection Observer targets elements by class or ID. Semantic HTML must be in place before adding animation JS.
- **Parallax conflicts with mobile performance:** `background-attachment: fixed` causes repaints on iOS. Wrap in `@media (min-width: 768px)` or use JS-based parallax that disables on touch devices.
- **Typography requires early font decisions:** Google Fonts `<link>` in `<head>` affects every page. Decide font pairing in Phase 1; retrofitting typography is expensive.

---

## MVP Definition

### Launch With (v1)

These constitute the complete site per PROJECT.md requirements.

- [ ] Full-screen hero with restaurant name, tagline, "Reserve a Table" CTA — establishes identity and conversion path
- [ ] Menu page with categorized sections (starters, pasta, pizza, desserts) + prices — core utility for diners
- [ ] Contact page with address, phone, hours, Google Maps embed — answers "where and when"
- [ ] Reservation contact form (name, date, time, party size) — fulfills CTA destination
- [ ] Our Story page — brand narrative for premium positioning
- [ ] Jobs page — operational utility, signals legitimacy
- [ ] Global nav, RTL layout, dark elegant design, mobile-first responsiveness — these are not features, they are constraints that apply to all pages
- [ ] Sticky header with scroll transform — low effort, high polish payoff
- [ ] Hebrew typography pairing (Frank Ruhl Libre + Cormorant Garamond or similar) — defines premium feel from day one

### Add After Validation (v1.x)

- [ ] Scroll animations (Intersection Observer fade-ins) — add once structure is stable; animating a moving target is wasted effort
- [ ] Parallax hero scroll effect — polish layer, add after core pages are signed off
- [ ] Menu anchor links — add when real menu content is known and section count is finalized
- [ ] Signature dish spotlight on home page — requires real photography and dish selection; add with real content

### Future Consideration (v2+)

- [ ] Third-party reservation widget (OpenTable embed) — only if phone/email reservations prove insufficient
- [ ] English language version — only if restaurant attracts tourist traffic
- [ ] Google Analytics — only if owner wants traffic data; requires cookie consent update at that point

---

## Feature Prioritization Matrix

| Feature | User Value | Implementation Cost | Priority |
|---------|------------|---------------------|----------|
| Full-screen hero + CTA | HIGH | LOW | P1 |
| Menu page (categorized) | HIGH | MEDIUM | P1 |
| Contact page + map | HIGH | LOW | P1 |
| Reservation form | HIGH | MEDIUM | P1 |
| RTL layout throughout | HIGH | MEDIUM | P1 |
| Mobile responsiveness | HIGH | MEDIUM | P1 |
| Global nav bar | HIGH | LOW | P1 |
| Our Story page | MEDIUM | LOW | P1 |
| Jobs page | MEDIUM | LOW | P1 |
| Dark elegant design system | HIGH | MEDIUM | P1 |
| Typography pairing (Google Fonts) | HIGH | LOW | P1 |
| Sticky header transform on scroll | MEDIUM | LOW | P2 |
| Client-side form validation | MEDIUM | LOW | P2 |
| Scroll animations (Intersection Observer) | MEDIUM | LOW | P2 |
| Parallax hero effect | LOW | LOW | P2 |
| Menu in-page anchor links | MEDIUM | LOW | P2 |
| Signature dish spotlight section | MEDIUM | LOW | P2 |
| Smooth page transitions | LOW | MEDIUM | P3 |
| English language version | LOW | HIGH | P3 |
| Third-party reservation widget | MEDIUM | HIGH | P3 |

**Priority key:**
- P1: Must have for launch
- P2: Should have, add when core is stable
- P3: Future consideration

---

## Competitor Feature Analysis

*Note: Live competitor analysis was not possible (web search unavailable). The following is drawn from training data on established high-end restaurant website conventions — Confidence: MEDIUM.*

| Feature | Typical high-end Israeli restaurant | Typical Italian fine dining (EU/US) | La Bella approach |
|---------|--------------------------------------|--------------------------------------|-------------------|
| Hero section | Full-screen food photo, Hebrew CTA | Full-screen atmosphere photo, minimal text | Full-screen food photo, Hebrew name + tagline + CTA button |
| Menu presentation | PDF download or simple list | Interactive categories, dish photography | HTML sections with anchors; no PDF (SEO and mobile unfriendly) |
| Reservation | External OpenTable/Restobar widget | Tock or Resy embed | On-site form (client requirement); `mailto:` or Formspree backend |
| Color palette | Varies widely; dark gold + black common | Cream, terracotta, dark green | Dark brown, beige, black (explicitly defined) |
| Language | Hebrew primary | Local language primary | Hebrew only, `dir="rtl"` throughout |
| Mobile experience | Often poor (desktop-first legacy) | Usually good on newer sites | Mobile-first (competitive advantage by contrast) |
| Typography | System fonts or basic Google Fonts | Custom or premium foundry fonts | Frank Ruhl Libre (Google Fonts, Hebrew-capable) + Latin display font |
| Photography | Often stock or low quality | Usually professional food photography | Placeholder in v1; real photography is highest-ROI content investment |

---

## RTL-Specific Feature Notes

These apply across all features and are specific to the Hebrew/RTL requirement:

- **Text alignment:** All body text `text-align: start` (RTL-aware) rather than `text-align: right` (fragile).
- **Layout direction:** CSS Flexbox `flex-direction: row-reverse` is not needed if `dir="rtl"` is set on `<html>` — browsers handle row direction automatically.
- **Logical CSS properties:** Use `margin-inline-start` / `padding-inline-end` instead of `margin-left` / `padding-right` so styles work in both RTL and LTR without overrides.
- **Navigation:** Menu items in RTL nav read right-to-left. Logo typically appears on the right side (mirrored from LTR convention) or centered.
- **Form fields:** Input labels appear to the right of fields; placeholder text is RTL; `text-align: right` may be needed on `<input>` for some browsers.
- **Icons with directional meaning:** Arrows, chevrons, and "next" indicators must be mirrored in RTL (CSS `transform: scaleX(-1)` or directional icon variants).
- **Price formatting:** Israeli prices formatted as `₪120` with the shekel sign on the left — this is LTR within an RTL context; use `<bdi>` or `unicode-bidi: isolate` on price spans to prevent text direction bleed.

---

## Sources

- Restaurant website UX patterns: Training data from analysis of high-end hospitality websites (Nobu, Noma, local Israeli fine dining). Confidence: MEDIUM (training data, 6-18 months stale — verify live if competitor parity is critical).
- RTL/Hebrew web best practices: W3C International, MDN Web Docs on CSS logical properties and `dir` attribute. Confidence: HIGH (stable, well-documented standards).
- Static site constraints: Derived directly from PROJECT.md explicit requirements. Confidence: HIGH.
- Google Fonts Hebrew availability: Frank Ruhl Libre is a well-established Hebrew font on Google Fonts (added ~2016, maintained). Confidence: HIGH.

---
*Feature research for: High-end Italian restaurant website, Hebrew RTL, static HTML/CSS/JS*
*Researched: 2026-03-10*
