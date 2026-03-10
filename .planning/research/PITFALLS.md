# Pitfalls Research

**Domain:** Static RTL Hebrew restaurant website (HTML/CSS/vanilla JS, dark theme, no framework)
**Researched:** 2026-03-10
**Confidence:** HIGH for RTL/CSS/HTML patterns (well-established); MEDIUM for Google Maps embed specifics (API changes frequently); HIGH for static form UX patterns

---

## Critical Pitfalls

### Pitfall 1: Mixing logical and physical CSS properties, breaking RTL layout

**What goes wrong:**
Physical directional properties (`margin-left`, `padding-right`, `text-align: left`, `float: left`, `left: 0`) are hard-coded assuming LTR. When `dir="rtl"` is applied, these do not flip automatically. The result is icons, buttons, and spacing that appear on the wrong side, text that starts from the visually incorrect edge, and navigation that looks inverted.

**Why it happens:**
Developers write CSS muscle memory from LTR experience. `padding-left: 1rem` on a nav item feels natural. Under RTL it produces the opposite visual intent (padding on the trailing side instead of the leading side).

**How to avoid:**
Use CSS logical properties throughout: `margin-inline-start`, `padding-inline-end`, `inset-inline-start`, `border-inline-start`. Use `text-align: start` instead of `text-align: left`. Avoid `float: left/right` entirely — use flexbox or grid with `gap`. Set `dir="rtl"` and `lang="he"` on the `<html>` element immediately and verify every spacing rule under that context before committing.

**Warning signs:**
- Navigation links have extra spacing on the wrong side
- Bullet list markers appear on the right (correct for RTL) but list text is pushed incorrectly
- Icons inside buttons sit on the wrong edge
- Any `text-align: left` in the stylesheet that hasn't been reviewed

**Phase to address:**
Foundation / HTML+CSS structure phase (first CSS pass). Establish the logical-property convention before any component-level CSS is written.

---

### Pitfall 2: CSS icons and directional glyphs not flipped for RTL

**What goes wrong:**
Arrow icons, chevrons, breadcrumb separators, and carousel controls that point right-to-left in LTR now need to point left-to-right in RTL — but they don't, because they're `background-image` SVGs or Unicode characters applied without a flip transform. The result: "back" arrows point forward, "next" buttons point backward.

**Why it happens:**
Icons are set once and forgotten. The `dir` attribute flips block layout but does not automatically mirror individual SVG or `content`-injected glyphs.

**How to avoid:**
For SVG icons used as directional indicators, apply `[dir="rtl"] .icon-arrow { transform: scaleX(-1); }`. Prefer inline SVGs so they can respond to `currentColor` and direction without separate asset variants. Unicode arrows (→ ←) in content should be replaced with logical equivalents or flipped via CSS. Audit every icon that communicates direction before considering any page complete.

**Warning signs:**
- A "read more" link has an arrow pointing left (toward the start of text) instead of toward the destination
- Slider/carousel prev/next buttons behave backwards
- Menu hamburger icon animations open in the wrong direction

**Phase to address:**
Component build phase (nav, buttons, interactive elements). Add a directional-icon audit as an explicit checklist item in that phase.

---

### Pitfall 3: Contact/reservation form submits silently with no backend

**What goes wrong:**
The form looks complete — fields, validation, submit button — but when the user clicks "Reserve a Table" nothing happens, or the page reloads to itself. With no backend (`action` points nowhere, or is omitted), the restaurant owner receives no reservation requests. Users see no confirmation message and may submit multiple times or assume the site is broken.

**Why it happens:**
A static site constraint means there is no server to receive `POST` data. Developers either leave `action=""` (reload) or wire up `preventDefault()` with no follow-through, thinking "the form part is done."

**How to avoid:**
Choose one of two valid static approaches before building the form:
1. Use a third-party form endpoint service (Formspree, Netlify Forms, EmailJS) and set `action` to that URL.
2. Use `fetch()` to POST to the service's API endpoint and show an inline success/error message.
Show a visible, accessible success state ("Your reservation request has been received") and an error state ("Something went wrong — please call us") after every submission attempt. Never leave the post-submit state undesigned.

**Warning signs:**
- Form `action` attribute is empty or missing
- No `fetch`/`XMLHttpRequest` call in the form JS
- No success or error UI state in the HTML/CSS
- Form reloads the page on submit without any user feedback

**Phase to address:**
Contact/reservation page phase. The decision between form services must be made before that phase begins — treat the choice as a prerequisite task.

---

### Pitfall 4: Hebrew fonts falling back to system sans-serif, breaking typographic tone

**What goes wrong:**
The design calls for refined Hebrew typography (e.g., a high-quality serif or elegant sans) to match the dark premium aesthetic. If the Google Fonts or font-face declaration is wrong, missing, or only covers Latin glyphs, Hebrew text renders in the OS default (`Arial` on Windows, `Lucida Grande` on macOS). The premium feel collapses immediately.

**Why it happens:**
Many popular Google Fonts only ship Latin subsets by default. Developers add `font-family: 'Playfair Display', serif` and it looks perfect in Latin preview tools, but Hebrew characters are not in that font file and fall back silently.

**How to avoid:**
Select a Google Font that explicitly supports Hebrew unicode range, or use a font that is known to cover Hebrew — for example `Frank Ruhl Libre` (classical Hebrew serif, excellent for high-end restaurant feel), `Noto Serif Hebrew`, or `Assistant` (clean sans-serif). In the Google Fonts URL, confirm the `subset=hebrew` parameter is included or the font listing shows Hebrew support. Test by typing actual Hebrew characters in browser devtools with the font applied. Define a safe fallback stack: `font-family: 'Frank Ruhl Libre', 'David Libre', serif`.

**Warning signs:**
- Font renders beautifully in the Latin preview but looks plain/incorrect in actual Hebrew text
- DevTools shows a different font family applied to Hebrew characters than the declared family
- `font-family` stack ends with only `serif` or `sans-serif` with no Hebrew-aware intermediate fallback

**Phase to address:**
Foundation / typography phase. Font selection must be validated with actual Hebrew content before any other design work depends on it.

---

### Pitfall 5: Dark theme creates WCAG contrast failures on body text

**What goes wrong:**
Dark brown backgrounds (#2C1810 range) paired with mid-tone beige or gold text (#C8A882 range) often fail WCAG AA contrast ratio requirements (4.5:1 for normal text). The design looks beautiful at a glance but is inaccessible and fails in low-light or bright-light environments. Placeholder text, labels, and nav links are especially vulnerable.

**Why it happens:**
Designers pick colors visually (they look good together) without running a contrast check. The failure is invisible until specifically measured. Dark themes are more prone to this because the entire palette is compressed toward low luminance.

**How to avoid:**
Run every text/background color pair through a WCAG contrast checker (browser devtools accessibility panel, or a tool like the WebAIM contrast checker). For body text on dark backgrounds, target at least 7:1 (AAA) since the dark aesthetic allows for it — light cream (#F5E6D3) on near-black (#1A0F08) easily achieves this. For large headings (18px+ bold), 4.5:1 is the minimum. Never use mid-tone beige on mid-tone brown for any readable content. Placeholder text in form inputs is the highest-risk area — the UA default placeholder color is typically low-contrast; override it explicitly.

**Warning signs:**
- DevTools accessibility audit flags contrast errors
- Text is hard to read on a phone screen in daylight
- Color pairs that "look good" have luminance values that are close to each other

**Phase to address:**
Design tokens / CSS variables phase. Define the full color palette with contrast ratios documented before any component is styled.

---

### Pitfall 6: Mobile viewport: `100vw` causes horizontal overflow in RTL

**What goes wrong:**
Elements set to `width: 100vw` extend past the visible viewport on mobile in RTL mode because `100vw` includes the scrollbar width. Combined with RTL scroll-origin behavior (some browsers and older WebKit implementations begin scroll position at the right edge in RTL), this can cause invisible horizontal scroll, clipped content, or layout overflow that is difficult to diagnose.

**Why it happens:**
`100vw` is assumed to equal the viewport, but it includes the scrollbar gutter on desktop. On mobile it causes a 1–17px overflow. RTL complicates debugging because the overflow direction is reversed, making it non-obvious which element is the culprit.

**How to avoid:**
Use `width: 100%` on block-level containers instead of `100vw`. Reserve `100vw` only for full-bleed sections where `overflow-x: hidden` is also set on the parent. Set `box-sizing: border-box` globally. Add `overflow-x: hidden` to `body` only after verifying it is not hiding legitimate scroll. Test on a real 375px-wide device or browser DevTools mobile emulation with the RTL layout active.

**Warning signs:**
- Horizontal scrollbar appears on mobile
- `overflow-x: hidden` was added to `body` to "fix" something without diagnosing the root cause
- Any element that fills full width uses `100vw` without a corresponding overflow guard

**Phase to address:**
Foundation / global CSS phase. Establish `box-sizing: border-box`, `max-width: 100%` on images, and `overflow-x` strategy before building any sections.

---

### Pitfall 7: Google Maps embed breaks or leaks referrer / shows wrong location

**What goes wrong:**
The embed iframe URL for Google Maps (the "Share > Embed" code from Google Maps) stops working when the page is served from a different domain, or shows a generic map instead of the pinned restaurant location. Some embed URLs also expire if they reference a temporary place ID. On mobile, the embedded map can be unresponsive to touch — users try to scroll the page but end up dragging the map instead.

**Why it happens:**
Google Maps embed URLs use a place ID or coordinate. If the restaurant is fictional/placeholder, the embed may show nothing or the wrong pin. The touch-scroll conflict (map intercepts touch events) is a known UX problem with iframe embeds on mobile.

**How to avoid:**
For a placeholder embed, use a static map image (`maps.googleapis.com/maps/api/staticmap`) with an API key, or use a real prominent location as a placeholder coordinate. To prevent the touch-scroll trap on mobile, wrap the map iframe in a container that requires a two-finger scroll to activate the map, or add a transparent overlay that the user must tap to "enable" the map: `pointer-events: none` by default, `pointer-events: auto` after a click/tap. Alternatively, provide a "View on Google Maps" link button instead of an embedded iframe for mobile breakpoints.

**Warning signs:**
- Embedded map shows a grey box or "Sorry, we can't find that location"
- On mobile, scrolling the page gets stuck inside the map
- The embed `src` URL contains no identifiable place coordinates

**Phase to address:**
Contact page phase. Decide between iframe embed vs. static image vs. link-only before building the contact section.

---

### Pitfall 8: Hero image causes layout shift and slow first paint

**What goes wrong:**
The full-screen hero with large food photography loads slowly on mobile connections (3G/4G). If the `<img>` has no explicit `width`/`height` attributes or the CSS background-image has no aspect-ratio placeholder, the layout jumps when the image loads (Cumulative Layout Shift). On slow connections, users see a blank black area for 2–4 seconds before the hero appears, with no loading state.

**Why it happens:**
Large unoptimized JPEGs (3MB+) are common from photography sources. No `loading`, `srcset`, or size hints are added because "it's just one image." CLS is invisible in devtools unless Lighthouse is run.

**How to avoid:**
Compress hero images to under 400KB using tools like Squoosh or ImageOptim (WebP format with JPEG fallback). Add `width` and `height` attributes to `<img>` elements so the browser reserves layout space before load. Use `srcset` with at least two sizes: mobile (800px wide) and desktop (1920px wide). For CSS background-image heroes, set an explicit `min-height` or `aspect-ratio` on the container so it does not collapse before the image loads. Use `loading="eager"` and `fetchpriority="high"` for the above-fold hero image.

**Warning signs:**
- Hero image source file is larger than 1MB
- `<img>` tag has no `width` and `height` attributes
- Page visually "jumps" when loaded in DevTools with network throttling
- Lighthouse score shows high CLS value

**Phase to address:**
Home page / hero phase. Image optimization and `srcset` must be part of the hero implementation task, not an afterthought.

---

### Pitfall 9: CSS `transform` and `position: absolute` with RTL cause unexpected mirroring

**What goes wrong:**
Animations and positioned elements behave differently in RTL contexts. `translateX(100%)` moves an element to the right visually, but in RTL the "off-screen" direction is the left. Slide-in menu animations, tooltip positioning, and dropdown menus can appear off-screen or animate in from the wrong direction.

**Why it happens:**
CSS `transform` values are not direction-aware — they operate in the physical coordinate system, not the logical one. `translateX()` always means "move right" regardless of `dir`. Developers write animations for LTR and don't test under RTL.

**How to avoid:**
For any element that needs to slide in from the leading or trailing edge, use a RTL-conditional transform: `[dir="rtl"] .slide-in { transform: translateX(-100%); }` where the LTR rule uses `translateX(100%)`. Alternatively, use CSS custom properties to hold the transform direction value, set per direction. Test all animations in RTL before moving to the next component.

**Warning signs:**
- Drawer/sidebar animation slides in from the wrong side
- Mobile nav menu flies off the wrong edge of the screen
- Tooltip appears outside the viewport on RTL pages

**Phase to address:**
Navigation / interactive components phase.

---

### Pitfall 10: `<form>` without `novalidate` shows browser-default LTR validation bubbles in RTL context

**What goes wrong:**
Browser-native form validation tooltips ("Please fill in this field") appear in LTR layout and default system font even when the page is RTL and Hebrew. The tooltip appears in the wrong position relative to the field and shows English or system-locale text, not Hebrew. This is visually jarring and confusing.

**Why it happens:**
Native browser validation is not customizable for direction, language, or style. When `lang="he"` is set, some browsers localize the message but many do not, especially on Windows.

**How to avoid:**
Add `novalidate` to the `<form>` element and implement custom JavaScript validation. Display validation error messages using `<span>` elements with `role="alert"` positioned manually in the RTL layout. This gives full control over language (Hebrew), position (inline-start of the field), and visual style (consistent with the dark theme). Use the `Constraint Validation API` (`field.setCustomValidity()`, `field.checkValidity()`) to leverage built-in validity states while controlling the UI.

**Warning signs:**
- Form uses HTML5 validation attributes (`required`, `type="email"`) without `novalidate`
- No custom error message elements in the HTML
- No JS validation logic in the form handler

**Phase to address:**
Reservation form phase.

---

## Technical Debt Patterns

| Shortcut | Immediate Benefit | Long-term Cost | When Acceptable |
|----------|-------------------|----------------|-----------------|
| Using `direction: rtl` on individual elements instead of `dir="rtl"` on `<html>` | Faster to apply per-component | Misses browser bidi algorithm for mixed text; Unicode bidirectional rendering breaks for inline Hebrew/Latin mixed content | Never — always set on `<html>` |
| Skipping `lang="he"` on `<html>` | One less attribute | Screen readers use wrong pronunciation; search engines may mis-classify the language | Never |
| Hardcoding all Hebrew content as static HTML strings | No CMS complexity | Tedious to update; copy-paste errors in Hebrew text direction marks | Acceptable for MVP since content is placeholder |
| `overflow-x: hidden` on `<body>` as a "fix" | Stops horizontal scroll complaints | Hides real layout overflow bugs; disables `position: sticky` inside any parent with `overflow` | Only after root cause is identified |
| Using `<table>` for menu layout | Quick alignment of name/price columns | Tables do not reflow well on mobile; RTL table column order is a separate concern | Never — use CSS grid/flexbox |
| Inline `style=""` for one-off color/spacing | Fast in the moment | Accumulates into unmaintainable CSS; overrides RTL logical property rules with physical values | Never on direction-sensitive properties |
| Not providing a `<noscript>` fallback for the reservation form | Saves time | Users with JS disabled see a non-functional form with no explanation | Add one `<noscript>` message — trivial effort |

---

## Integration Gotchas

| Integration | Common Mistake | Correct Approach |
|-------------|----------------|------------------|
| Google Maps embed | Copying the iframe embed code and placing it directly — it includes no touch-scroll handling | Wrap iframe in a container; add a tap-to-activate overlay on mobile; provide a fallback "Open in Maps" link |
| Google Maps embed | Using a placeholder or made-up address that returns no pin | Use real coordinates for a placeholder Italian restaurant or a well-known landmark as proxy |
| Google Fonts (Hebrew) | Using the font URL without `subset=hebrew` or without confirming Hebrew glyph coverage | Verify Hebrew subset in font specimen; test with actual `אבג` characters |
| Formspree / EmailJS | Exposing the form endpoint or API key in public HTML source | EmailJS public keys are intentionally client-side safe; Formspree form IDs are not secret but should have CORS configuration and spam protection enabled |
| Formspree / EmailJS | No CAPTCHA or honeypot field | Static forms are a spam magnet — add a hidden honeypot field (`<input name="_gotcha" style="display:none">`) at minimum |

---

## Performance Traps

| Trap | Symptoms | Prevention | When It Breaks |
|------|----------|------------|----------------|
| Unoptimized hero food photography | 3–8 second first paint on mobile; high LCP score | Compress to WebP, provide `srcset`, set explicit dimensions | Immediately on any mobile device on a real connection |
| Loading all page CSS in one large file (no critical CSS) | Flash of unstyled content on slow connections | Inline critical above-fold CSS in `<style>` in `<head>`; defer the rest | On connections slower than ~5 Mbps |
| No font-display strategy on custom fonts | Text is invisible while font loads (FOIT) | Add `font-display: swap` to all `@font-face` declarations | On first visit (no cache), any connection speed |
| Loading Google Fonts as a `<link>` without `preconnect` hints | Font load adds an extra round-trip delay | Add `<link rel="preconnect" href="https://fonts.googleapis.com">` before the font link | Always measurable; severe on mobile |
| Full-resolution images used as menu item thumbnails | Menu page is 10MB+; scrolling lags | Resize menu thumbnails to max 400px wide, separate from hero assets | On any device with limited memory (mid-range phones) |

---

## Security Mistakes

| Mistake | Risk | Prevention |
|---------|------|------------|
| Exposing a real email address in plain text in HTML | Email scrapers harvest it; owner receives spam | Use a `mailto:` link (acceptable for a restaurant), or use a contact form, or obfuscate with `data-` attributes decoded by JS — but avoid `display:none` obfuscation (scrapers ignore CSS) |
| Static form endpoint (Formspree/EmailJS) with no honeypot or rate limiting | Bot spam floods the restaurant owner's inbox | Add a hidden honeypot input; rely on the form service's built-in spam filtering; optionally add Cloudflare Turnstile (free, no CAPTCHA for legitimate users) |
| Embedding Google Maps with a Maps JavaScript API key in client-side code | API key abuse if key is not restricted by referrer | Use the iframe embed (no API key needed) or restrict the API key to your domain in Google Cloud Console |
| Linking to external CDN scripts without Subresource Integrity (SRI) | Compromised CDN can inject malicious code | Add `integrity` and `crossorigin` attributes to any external `<script>` or `<link>` tags; or self-host the resource |

---

## UX Pitfalls

| Pitfall | User Impact | Better Approach |
|---------|-------------|-----------------|
| No success state after form submission | User submits multiple times thinking nothing happened; restaurant gets duplicate reservations | Show a clear inline success message; disable the submit button after first click; replace form with a confirmation view |
| CTA button "Reserve a Table" scrolls to the form on the same page but the form is not in the viewport on mobile | User clicks CTA, nothing visibly changes (smooth scroll is too fast), user thinks the button is broken | Use `scroll-behavior: smooth` AND add a brief focus on the form heading; ensure the scroll target is visible above the fold after scrolling |
| Menu prices/items with no visual separation in RTL | Dish name and price run together visually; Hebrew right-to-left text with a left-aligned price is confusing | Use flexbox row with `justify-content: space-between`; price aligned to `inline-end`; dot-leader or rule between name and price |
| Navigation links in RTL that are in logical order in HTML but appear visually reversed | "Home, Menu, About, Contact" reads right-to-left in the nav bar, which is correct for RTL, but dev tests with LTR assumptions and "fixes" the order in HTML | Never reorder HTML elements for visual RTL fix — use CSS `direction` and `dir` only; trust the browser RTL rendering |
| Touch targets smaller than 44x44px on mobile | Older and vision-impaired users mis-tap; frustrating on the small nav and form inputs | Set minimum `height: 44px` on all interactive elements; add `padding` rather than making elements visually large |
| Dark overlay on hero image too weak, making text illegible | Hebrew restaurant name and tagline are unreadable on complex food photography backgrounds | Use a gradient overlay (`linear-gradient(to bottom, rgba(0,0,0,0.6) 0%, rgba(0,0,0,0.2) 100%)`) and verify contrast ratio of white/beige text against the darkened image |

---

## "Looks Done But Isn't" Checklist

- [ ] **RTL layout:** Check that `dir="rtl"` and `lang="he"` are on the `<html>` element, not just `<body>` — verify in browser devtools HTML inspector
- [ ] **Reservation form:** Verify that form submission actually sends an email/notification — submit the form and confirm the restaurant owner receives it
- [ ] **Hebrew font:** Type actual Hebrew text in devtools console and confirm the correct font family is applied (not a fallback) — run `document.fonts.check('16px Frank Ruhl Libre')` in the console
- [ ] **Contact page map:** Confirm the embed shows the correct pin and loads without a grey box — test in an incognito window
- [ ] **Mobile responsiveness:** Test on a real device (or 375px DevTools emulation) with the RTL layout active — desktop testing is insufficient
- [ ] **Contrast:** Run browser DevTools accessibility audit — zero WCAG contrast failures on dark theme text
- [ ] **Arrow icons:** Inspect every directional arrow/chevron/caret in the UI — confirm they point in the semantically correct direction for RTL
- [ ] **Form validation messages:** Fill a field with invalid data and check that the error message is in Hebrew and positioned inline-start of the field
- [ ] **Image loading:** Check Lighthouse mobile score for LCP and CLS — hero image should not contribute to layout shift
- [ ] **Horizontal overflow:** On mobile, confirm no horizontal scrollbar appears and no content is clipped outside the viewport

---

## Recovery Strategies

| Pitfall | Recovery Cost | Recovery Steps |
|---------|---------------|----------------|
| RTL physical property violations found late | MEDIUM | Audit entire stylesheet for `left`/`right`/`margin-left`/`padding-right`; replace with logical properties in one focused pass; re-test all pages |
| Form silently not sending after site is live | LOW | Add Formspree or EmailJS endpoint — requires only changing `action` attribute and adding a few lines of JS; 1–2 hours |
| Wrong Hebrew font rendering | LOW | Swap Google Fonts URL to a Hebrew-supporting font; update `font-family` CSS variable in one place if CSS custom properties were used from the start |
| Dark theme contrast failures discovered post-build | MEDIUM | Update CSS color variables (low cost if variables are centralized); re-test all text/background pairs; may require redesigning specific components |
| Hero image causing slow load | LOW | Re-export at correct dimensions and quality; add `srcset`; update HTML — no structural changes needed |
| Google Maps touch-scroll trap on mobile | LOW | Wrap iframe in an overlay container; add 15 lines of JS to handle tap-to-activate |
| Directional icons pointing wrong way | LOW | Add `[dir="rtl"]` CSS rule with `transform: scaleX(-1)` for each affected icon — targeted CSS change |
| Form validation bubbles appearing in LTR | LOW-MEDIUM | Add `novalidate`; implement custom JS validation with Hebrew error messages — requires JS work but is self-contained |

---

## Pitfall-to-Phase Mapping

| Pitfall | Prevention Phase | Verification |
|---------|------------------|--------------|
| Physical vs. logical CSS properties | Phase 1: Foundation & global CSS | Grep stylesheet for `margin-left`, `padding-right`, `left:`, `right:` — must be zero (except intentional overrides) |
| Directional icon mirroring | Phase 2: Navigation & layout components | Visual RTL audit of all icons; check in browser with `dir="rtl"` active |
| Hebrew font selection | Phase 1: Foundation & typography | DevTools font panel shows correct font on Hebrew characters |
| Dark theme contrast | Phase 1: Design tokens / CSS variables | Accessibility audit panel shows no contrast failures |
| Form no-backend submission | Phase 4: Contact & reservation page | Submit live form and confirm receipt in restaurant email inbox |
| Mobile viewport overflow | Phase 1: Global CSS | No horizontal scroll on 375px viewport in both LTR and RTL test |
| Google Maps touch trap | Phase 4: Contact page | Manual touch test on mobile device; two-finger scroll works within map |
| Hero image performance | Phase 2: Home page / hero section | Lighthouse mobile LCP < 2.5s; CLS < 0.1 |
| `transform` RTL animation bugs | Phase 2: Navigation / interactive components | Test slide-in animations in DevTools with `dir="rtl"` on `<html>` |
| Native form validation in wrong language | Phase 4: Reservation form | Submit invalid data; confirm Hebrew error messages appear inline |

---

## Sources

- CSS Logical Properties (MDN): authoritative specification — HIGH confidence
- WCAG 2.1 contrast requirements (W3C): AA is 4.5:1 for normal text, 3:1 for large text — HIGH confidence
- Google Fonts Hebrew subset support: confirmed via font specimen pages for Frank Ruhl Libre, Noto Serif Hebrew, Assistant — MEDIUM confidence (font availability may change)
- Google Maps iframe embed touch behavior on mobile: well-documented community pattern; pointer-events overlay solution is widely used — MEDIUM confidence
- HTML `dir` attribute and bidi algorithm (W3C HTML spec): `dir="rtl"` on `<html>` is the authoritative approach — HIGH confidence
- Formspree honeypot field (`_gotcha`): documented in Formspree official docs — MEDIUM confidence (verify field name is current)
- CSS `font-display: swap` (MDN): standardized behavior — HIGH confidence
- `fetchpriority="high"` on LCP images: Chrome and modern browsers support it — MEDIUM confidence (not all browsers, but gracefully ignored)
- RTL `transform` coordinate system behavior: CSS specification — physical coordinates, not logical — HIGH confidence

---
*Pitfalls research for: Static RTL Hebrew restaurant website (La Bella)*
*Researched: 2026-03-10*
