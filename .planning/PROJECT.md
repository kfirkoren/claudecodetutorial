# La Bella — Italian Restaurant Website

## What This Is

A marketing and discovery website for "La Bella," an Italian restaurant. Written entirely in Hebrew (RTL layout), it presents the restaurant's menu, story, contact information, and open job positions to potential diners. The site is designed to feel dark and elegant — conveying premium dining through large food photography, rich dark tones, and refined typography.

## Core Value

A visitor who lands on the home page must immediately feel the restaurant's atmosphere and be able to reserve a table or view the menu within one click.

## Requirements

### Validated

(None yet — ship to validate)

### Active

- [ ] Home page: full-screen hero with restaurant name, tagline, and "Reserve a Table" CTA button
- [ ] Menu page: categorized menu (starters, pasta, pizza, desserts) with Hebrew dish names and prices — placeholder content
- [ ] Our Story page: about the restaurant and the chef — placeholder Hebrew content
- [ ] Contact page: address, phone number, opening hours, and embedded Google Maps
- [ ] Jobs page: open positions listing — placeholder Hebrew content
- [ ] Reservation: on-site contact form (name, date, time, party size) triggered by CTA button
- [ ] RTL layout throughout — all pages use `dir="rtl"` and Hebrew text
- [ ] Dark elegant design: dark brown, beige, and black color palette
- [ ] Mobile-first, fully responsive across all pages
- [ ] Semantic HTML5 structure on every page
- [ ] Smooth navigation between all pages (single nav bar)

### Out of Scope

- Backend / server-side code — static HTML/CSS/JS only; no database or CMS
- Online payment or ordering — this is a branding site, not a food delivery platform
- User accounts or authentication — no login system needed
- CMS or admin panel — content is hardcoded as placeholder
- React, Vue, or any JS framework — plain vanilla JS only
- English version — Hebrew only for now

## Context

- Pure front-end project: HTML5, CSS3, vanilla JavaScript — no build tools or frameworks
- Target audience: Hebrew-speaking diners in Israel; RTL is a hard requirement
- Visual reference: high-end restaurant websites with large hero imagery, minimal UI chrome, and cinematic photography
- Placeholder content will be realistic-sounding Hebrew restaurant copy (dishes, chef bio, job descriptions)

## Constraints

- **Tech Stack**: Plain HTML, CSS, JavaScript only — no frameworks, no npm dependencies
- **Language**: Hebrew (RTL) throughout — `dir="rtl"` and `lang="he"` on `<html>`
- **Design**: Dark and elegant — dark brown (#2C1810 range), beige (#F5E6D3 range), black; no bright or pastel colors
- **Responsiveness**: Mobile-first — all layouts must work on 375px width and up
- **Accessibility**: Semantic HTML5 elements (`<nav>`, `<main>`, `<section>`, `<footer>`, `<article>`)

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| No framework — plain HTML/CSS/JS | User's explicit requirement; keeps site simple and dependency-free | — Pending |
| On-site reservation form (not external service) | User prefers keeping users on-site; simpler UX | — Pending |
| Placeholder Hebrew content | Real content not available yet; realistic placeholders allow full design implementation | — Pending |
| Multi-page site (not SPA) | Simpler for plain JS; each page is a separate .html file | — Pending |

---
*Last updated: 2026-03-10 after initialization*
