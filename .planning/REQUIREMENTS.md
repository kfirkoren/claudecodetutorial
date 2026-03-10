# Requirements: La Bella — Italian Restaurant Website

**Defined:** 2026-03-10
**Core Value:** A visitor who lands on the home page must immediately feel the restaurant's atmosphere and be able to reserve a table or view the menu within one click.

## v1 Requirements

### Foundation

- [ ] **FOUND-01**: Site renders with `dir="rtl"` and `lang="he"` on `<html>`, and all text is right-to-left throughout
- [ ] **FOUND-02**: CSS design token system uses custom properties for the full dark palette (dark brown ~#2C1810, beige ~#F5E6D3, black) and Hebrew font pair (Frank Ruhl Libre for headings, Heebo for body)
- [ ] **FOUND-03**: Site is mobile-first and fully responsive from 375px upward with no horizontal overflow
- [ ] **FOUND-04**: All foreground/background color pairs pass WCAG AA contrast ratio (4.5:1 for normal text, 3:1 for large text)
- [ ] **FOUND-05**: All CSS uses logical properties (`margin-inline-start`, `padding-inline-end`, `text-align: start`) — zero physical directional properties (`margin-left`, `padding-right`, `text-align: left/right`)

### Navigation

- [ ] **NAV-01**: Global nav bar links to all five pages (Home, Menu, Our Story, Contact, Jobs) with the active page visually indicated
- [ ] **NAV-02**: On mobile, nav collapses into a hamburger toggle that opens/closes a mobile menu (vanilla JS)
- [ ] **NAV-03**: Header is transparent when overlaying the hero and transitions to a solid background when user scrolls down (vanilla JS, scroll event)
- [ ] **NAV-04**: Footer appears on all pages with restaurant name, brief contact info, and copyright

### Home Page

- [ ] **HOME-01**: Full-screen hero section displays the restaurant name (לה בלה), tagline, and a "הזמן שולחן" (Reserve a Table) CTA button over a large food photograph
- [ ] **HOME-02**: Clicking "הזמן שולחן" navigates to the Contact page and scrolls to the reservation form
- [ ] **HOME-03**: Signature dish spotlight section below hero showcases 2–3 featured dishes with placeholder food photography
- [ ] **HOME-04**: Mini about teaser section with 1–2 Hebrew sentences about the restaurant, linking to the Our Story page
- [ ] **HOME-05**: Opening hours snippet displayed on the home page (days and hours in Hebrew)

### Menu Page

- [ ] **MENU-01**: Menu page displays four categories: מתאבנים (Starters), פסטה (Pasta), פיצה (Pizza), קינוחים (Desserts)
- [ ] **MENU-02**: Each dish entry shows a Hebrew name, short description, and price in Israeli shekels (₪) using `<bdi>` for correct price directionality
- [ ] **MENU-03**: Category tab switching — clicking a category tab shows that category's dishes and hides the others (vanilla JS)
- [ ] **MENU-04**: Each category contains at least 4 placeholder dishes with realistic Hebrew names and descriptions

### Contact Page

- [ ] **CONT-01**: Contact page displays the restaurant's address and a clickable `tel:` phone number link
- [ ] **CONT-02**: Opening hours table lists hours for each day of the week in Hebrew
- [ ] **CONT-03**: Google Maps is embedded as an iframe showing the restaurant's location; on mobile, a pointer-events overlay prevents accidental map scrolling
- [ ] **CONT-04**: Reservation form collects: full name, phone number, preferred date, preferred time, and number of diners — submitted via Formspree to restaurant email
- [ ] **CONT-05**: Reservation form includes client-side Hebrew validation (required fields, date in future); native browser validation bubbles are suppressed (`novalidate`) in favor of custom Hebrew error messages

### Our Story Page

- [ ] **STOR-01**: Our Story page displays the chef's portrait (placeholder image) and a Hebrew bio paragraph
- [ ] **STOR-02**: Restaurant founding story and philosophy displayed as Hebrew narrative content (placeholder)

### Jobs Page

- [ ] **JOBS-01**: Jobs page lists open positions with Hebrew job title, short role description, and an "שלח קורות חיים" (Send CV) mailto link for each position
- [ ] **JOBS-02**: At least 3 placeholder job listings (e.g., מלצר/ית, שף סו, קופאי/ת)

---

## v2 Requirements

### Enhancements

- **ENH-01**: Scroll animations — fade-in/slide-up on content sections via Intersection Observer
- **ENH-02**: Parallax effect on the hero section (desktop only, disabled on mobile via media query)
- **ENH-03**: In-page anchor links for menu categories (jump directly to a section without tab switching)
- **ENH-04**: Signature dish spotlight with lightbox gallery for food photography

### Future Pages

- **FUTR-01**: English version of the site under `/en/` with `dir="ltr"` layout
- **FUTR-02**: Online reservation integration via OpenTable or Wolt if phone/email proves insufficient

---

## Out of Scope

| Feature | Reason |
|---------|--------|
| Backend / server-side code | Static HTML/CSS/JS only — no database, no Node.js, no PHP |
| Online ordering / payment | This is a branding site, not a food delivery platform |
| User accounts or login | No authentication system needed for a restaurant marketing site |
| CMS or admin panel | Content is hardcoded placeholder; no dynamic content management |
| React, Vue, or any JS framework | User requirement — plain vanilla JS only |
| Google Analytics / tracking | Adds GDPR cookie consent complexity; out of scope for v1 |
| Animated splash/loading screen | Harms performance, blocks content — never build this |
| CSS frameworks (Bootstrap, Tailwind) | Adds 100KB+ overhead and RTL configuration complexity |

---

## Traceability

| Requirement | Phase | Status |
|-------------|-------|--------|
| FOUND-01 | Phase 1 | Pending |
| FOUND-02 | Phase 1 | Pending |
| FOUND-03 | Phase 1 | Pending |
| FOUND-04 | Phase 1 | Pending |
| FOUND-05 | Phase 1 | Pending |
| NAV-01 | Phase 2 | Pending |
| NAV-02 | Phase 2 | Pending |
| NAV-03 | Phase 2 | Pending |
| NAV-04 | Phase 2 | Pending |
| HOME-01 | Phase 3 | Pending |
| HOME-02 | Phase 3 | Pending |
| HOME-03 | Phase 3 | Pending |
| HOME-04 | Phase 3 | Pending |
| HOME-05 | Phase 3 | Pending |
| MENU-01 | Phase 4 | Pending |
| MENU-02 | Phase 4 | Pending |
| MENU-03 | Phase 4 | Pending |
| MENU-04 | Phase 4 | Pending |
| STOR-01 | Phase 4 | Pending |
| STOR-02 | Phase 4 | Pending |
| JOBS-01 | Phase 4 | Pending |
| JOBS-02 | Phase 4 | Pending |
| CONT-01 | Phase 5 | Pending |
| CONT-02 | Phase 5 | Pending |
| CONT-03 | Phase 5 | Pending |
| CONT-04 | Phase 5 | Pending |
| CONT-05 | Phase 5 | Pending |

**Coverage:**
- v1 requirements: 27 total
- Mapped to phases: 27
- Unmapped: 0

---
*Requirements defined: 2026-03-10*
*Last updated: 2026-03-10 after roadmap creation*
