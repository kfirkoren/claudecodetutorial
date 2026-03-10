# Roadmap: La Bella — Italian Restaurant Website

## Overview

Build a five-page static Hebrew-language marketing website for La Bella, an upscale Italian restaurant. The project follows a strict dependency order: the RTL CSS foundation and design token system must exist before any component is built, the shared nav/footer shell before any page, the high-complexity home page before secondary pages, and the reservation form as a discrete end-to-end deliverable. Menu and secondary pages build on established component patterns. The site delivers a fully responsive, dark-elegant, RTL experience with no framework, no backend, and no build tools.

## Phases

**Phase Numbering:**
- Integer phases (1, 2, 3): Planned milestone work
- Decimal phases (2.1, 2.2): Urgent insertions (marked with INSERTED)

Decimal phases appear between their surrounding integers in numeric order.

- [ ] **Phase 1: Foundation** - RTL CSS design system, tokens, typography, and responsive baseline
- [ ] **Phase 2: Shell and Navigation** - Global nav bar, footer, hamburger toggle, and sticky header JS
- [ ] **Phase 3: Home Page** - Full-screen hero, CTA, signature dish spotlight, about teaser, and hours snippet
- [ ] **Phase 4: Menu and Secondary Pages** - Categorized menu with tab switching, Our Story page, and Jobs page
- [ ] **Phase 5: Contact and Reservation** - Contact info, Google Maps embed, and working Formspree reservation form

## Phase Details

### Phase 1: Foundation
**Goal**: The RTL CSS design system and responsive baseline are proven correct before any visible component is built
**Depends on**: Nothing (first phase)
**Requirements**: FOUND-01, FOUND-02, FOUND-03, FOUND-04, FOUND-05
**Success Criteria** (what must be TRUE):
  1. A test HTML page renders in Hebrew with right-to-left text flow — words start at the right margin and wrap correctly at every breakpoint from 375px upward
  2. All CSS color pairs (background/foreground combinations from the dark palette) pass WCAG AA contrast ratio when run through a contrast checker
  3. The stylesheet contains zero physical directional properties — a grep for `margin-left`, `padding-right`, `text-align: left`, and `text-align: right` returns no matches
  4. Hebrew headings render in Frank Ruhl Libre and body text renders in Heebo — confirmed visually in DevTools and not falling back to Arial or system fonts
  5. The page has no horizontal scroll on a 375px-wide viewport
**Plans**: TBD

### Phase 2: Shell and Navigation
**Goal**: Visitors can navigate between all five pages using a consistent, working nav bar on any device
**Depends on**: Phase 1
**Requirements**: NAV-01, NAV-02, NAV-03, NAV-04
**Success Criteria** (what must be TRUE):
  1. The nav bar shows links to all five pages and highlights the current page with a visible active indicator
  2. On a 375px mobile viewport, the nav collapses to a hamburger icon; tapping it opens the mobile menu, tapping again closes it
  3. When the user scrolls down from the top of any page, the header transitions from transparent to a solid background color
  4. The footer appears at the bottom of every page with restaurant name, brief contact info, and copyright text
**Plans**: TBD

### Phase 3: Home Page
**Goal**: A visitor landing on the home page immediately feels the restaurant's premium atmosphere and can reserve a table or reach the menu in one click
**Depends on**: Phase 2
**Requirements**: HOME-01, HOME-02, HOME-03, HOME-04, HOME-05
**Success Criteria** (what must be TRUE):
  1. The hero section fills the full viewport height, displays the restaurant name (לה בלה), a Hebrew tagline, and a clearly visible "הזמן שולחן" CTA button over a food photograph
  2. Clicking the "הזמן שולחן" button navigates to the Contact page and the reservation form is in view (scrolled to)
  3. Below the hero, 2-3 featured dishes are displayed with placeholder food photography in the signature dish spotlight section
  4. An about teaser section shows 1-2 Hebrew sentences about the restaurant with a link that navigates to the Our Story page
  5. Opening hours for the week are visible on the home page in Hebrew
**Plans**: TBD

### Phase 4: Menu and Secondary Pages
**Goal**: Visitors can browse the full categorized menu and read about the restaurant's story and available jobs
**Depends on**: Phase 3
**Requirements**: MENU-01, MENU-02, MENU-03, MENU-04, STOR-01, STOR-02, JOBS-01, JOBS-02
**Success Criteria** (what must be TRUE):
  1. The Menu page shows four category tabs (מתאבנים, פסטה, פיצה, קינוחים); clicking a tab shows only that category's dishes and hides the others
  2. Each dish entry displays a Hebrew name, a short Hebrew description, and a price in ₪ — the shekel amount is readable and not reversed by the RTL layout
  3. The Our Story page displays a chef portrait image, a Hebrew chef bio, and a Hebrew restaurant founding narrative
  4. The Jobs page lists at least 3 open positions with Hebrew job titles, short Hebrew role descriptions, and a "שלח קורות חיים" mailto link for each position
**Plans**: TBD

### Phase 5: Contact and Reservation
**Goal**: Visitors can find the restaurant's location and contact details, and can submit a reservation request that reaches the restaurant's email inbox
**Depends on**: Phase 4
**Requirements**: CONT-01, CONT-02, CONT-03, CONT-04, CONT-05
**Success Criteria** (what must be TRUE):
  1. The Contact page displays the restaurant's address and a phone number that opens the phone dialer when tapped on mobile
  2. An opening hours table lists each day of the week in Hebrew with its operating hours
  3. A Google Maps iframe is embedded and shows the restaurant location; on mobile, a single tap does not accidentally scroll the map — a tap-to-activate overlay is present
  4. The reservation form collects full name, phone number, preferred date, preferred time, and number of diners — submitting the form sends the data to the restaurant's email via Formspree and the user sees a Hebrew success confirmation
  5. Submitting the reservation form with missing or invalid fields shows Hebrew error messages below the relevant fields — no English browser-default validation bubbles appear
**Plans**: TBD

## Progress

**Execution Order:**
Phases execute in numeric order: 1 → 2 → 3 → 4 → 5

| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. Foundation | 0/TBD | Not started | - |
| 2. Shell and Navigation | 0/TBD | Not started | - |
| 3. Home Page | 0/TBD | Not started | - |
| 4. Menu and Secondary Pages | 0/TBD | Not started | - |
| 5. Contact and Reservation | 0/TBD | Not started | - |
