# Accessibility Audit Report
**Site under test:** TrailBrew Coffee Co. (demo site, `/before/index.html`)
**Standard:** WCAG 2.2 Level AA, with WAI-ARIA Authoring Practices Guide (APG) referenced for widget patterns
**Scope:** Single-page storefront — header/nav, hero carousel, product grid, newsletter form, quick-view modal, footer

## Methodology

1. **Automated scan** — axe DevTools / Lighthouse-style rule check for contrast, missing alt text, label associations, and ARIA misuse.
2. **Keyboard-only pass** — unplugged the mouse and tabbed through the entire page (Tab / Shift+Tab / Enter / Space / Esc / Arrow keys), checking focus order, focus visibility, and whether every interactive element could be reached and operated.
3. **Screen reader pass** — walked the page with VoiceOver (macOS Safari, VO+Right Arrow to move by element, VO+A to read all) to check that images, controls, form fields, and dynamic content announce correctly. The same checks apply directly to NVDA/JAWS since the underlying issues are in the HTML/ARIA, not the screen reader.
4. **Manual visual check** — contrast ratios verified against WCAG 1.4.3 thresholds (4.5:1 for normal text, 3:1 for large text/UI components).

Severity scale: **Critical** (blocks task completion for AT users), **Serious** (major friction or misleading info), **Moderate** (usable but degraded experience).

---

## Findings

| # | Issue | WCAG SC | Severity |
|---|-------|---------|----------|
| 1 | `<html>` missing `lang` attribute | 3.1.1 Language of Page (A) | Moderate |
| 2 | No skip-to-content link | 2.4.1 Bypass Blocks (A) | Serious |
| 3 | Logo `<img>` has no `alt` attribute at all | 1.1.1 Non-text Content (A) | Serious |
| 4 | Product/hero images use filename as alt text (`alt="bag1.jpg"`) | 1.1.1 Non-text Content (A) | Serious |
| 5 | Global `*:focus { outline: none; }` removes all focus indicators | 2.4.7 Focus Visible (AA) | Critical |
| 6 | Nav links `#d8c9b8` on `#2b1d14` background — contrast ≈ 4.0:1 (fails 4.5:1 for normal text) | 1.4.3 Contrast (Minimum) (AA) | Moderate |
| 7 | Price text `#999` on white — contrast ≈ 2.8:1 | 1.4.3 Contrast (Minimum) (AA) | Serious |
| 8 | "Add to cart" is a `<div onclick>` — not focusable, no keyboard handler, no role | 2.1.1 Keyboard (A), 4.1.2 Name, Role, Value (A) | Critical |
| 9 | Icon-only search button has no accessible name (just an emoji) | 4.1.2 Name, Role, Value (A) | Serious |
| 10 | Section headings use `<h4>` styled to look right, skipping `h2`/`h3` — breaks the heading outline | 1.3.1 Info and Relationships (A) | Moderate |
| 11 | Visible page `<h1>` is `display:none`, so the real landmark heading is invisible to sighted users while an `h4` visually plays the role of top heading | 1.3.1 Info and Relationships (A) | Moderate |
| 12 | Form inputs use `placeholder` only, no `<label>` — placeholder disappears on input and isn't reliably exposed to AT | 1.3.1, 3.3.2 Labels or Instructions (A) | Critical |
| 13 | Validation error shown only as a red border (`input.error`), no text, no `aria-invalid`, nothing announced by AT | 1.4.1 Use of Color (A), 3.3.1 Error Identification (A) | Serious |
| 14 | Social icon links are empty `<a href="...">` with a decorative circle only — no name for AT, and mouse-only affordance | 2.4.4 Link Purpose (A), 4.1.2 Name, Role, Value (A) | Critical |
| 15 | "Click here" used as link text for all three quick-view links — identical, non-descriptive names when list-of-links is read out of context | 2.4.4 Link Purpose (In Context) (A) | Moderate |
| 16 | Quick-view modal has no `role="dialog"`/`aria-modal`, no focus moved into it on open, no focus trap, no `Escape` to close, and focus isn't returned to the trigger on close | 2.1.2 No Keyboard Trap (A), 4.1.2 Name, Role, Value (A), 2.4.3 Focus Order (A) | Critical |
| 17 | Hero carousel auto-rotates every 3s with no pause/stop control | 2.2.2 Pause, Stop, Hide (A) | Serious |
| 18 | Carousel slide changes are not announced to AT — screen reader users get no indication content changed | 4.1.3 Status Messages (AA) | Moderate |

---

## Screen reader walkthrough notes (VoiceOver, representative excerpts)

- **Add to cart (before):** Tabbing through a product card, VoiceOver never lands on the "Add to cart" control at all — it's a `<div>`, so it's skipped entirely. A screen reader or keyboard-only user cannot add a product to the cart.
- **Social icons (before):** VO+Right Arrow announces only *"link"* three times in a row with no further information — a user has no way to tell these are Instagram/Twitter/Facebook.
- **Newsletter name field (before):** VoiceOver announces *"edit text"* with no label, and the red border around the invalid field is never mentioned — a screen reader user submitting the form gets no indication anything is wrong.
- **Quick view modal (before):** Opening the modal with a mouse works, but keyboard focus stays behind it in the page body. A screen reader user who opens it has no idea a dialog appeared, and pressing Esc does nothing.
- **After fixes:** the same walkthrough on `/after/index.html` — Add to cart is announced as *"Add Ethiopia Yirgacheffe to cart, button"*; social links announce *"TrailBrew on Instagram, link"*; the name field announces *"Your name, edit text, invalid data, Please enter your name."*; and opening quick view announces *"Ethiopia Yirgacheffe, dialog"* with focus landing on the close button and Esc reliably closing it.

---

## Fix summary (implemented in `/after/index.html`)

| Issue # | Fix applied |
|---|---|
| 1 | Added `lang="en"` to `<html>` |
| 2 | Added a visually-hidden-until-focused skip link as the first focusable element |
| 3, 4 | Replaced missing/filename alt text with descriptive alt text; hero images describe the scene, not the file |
| 5 | Replaced blanket `outline:none` with a visible `:focus-visible` style (3px outline, 2px offset) |
| 6, 7 | Adjusted nav link and price colors to meet 4.5:1+ contrast against their backgrounds |
| 8 | Replaced `<div onclick>` with a real `<button>`, keyboard-operable and announced correctly by default |
| 9 | Added `aria-label="Open search"` to the icon button |
| 10, 11 | Restored a real, visible `<h1>` and used `<h2>` for section headings, producing a correct outline |
| 12 | Added `<label for>` tied to each input's `id` |
| 13 | Error is now shown with text (`.error-text`), `aria-invalid="true"`, and `aria-describedby` linking the field to its error message |
| 14 | Added `aria-label` to each social link describing its destination |
| 15 | Link text now includes the product name ("Quick view: Ethiopia Yirgacheffe") |
| 16 | Modal now has `role="dialog"`, `aria-modal="true"`, `aria-labelledby`; focus moves in on open, is trapped with Tab/Shift+Tab, closes on Escape, and returns to the trigger element on close |
| 17, 18 | Added a Pause/Play button (`aria-pressed`) and an `aria-live` region that announces the active slide's caption on change |

## Not yet remediated / recommended follow-ups
- Custom dropdown/select patterns were out of scope for this page but should follow the [APG Combobox pattern](https://www.w3.org/WAI/ARIA/apg/patterns/combobox/) if added later.
- Full color-contrast audit of hover/active/disabled states was not performed — recommend a second pass once final brand colors are locked.
- No automated CI check (e.g. axe-core in a pre-commit hook or GitHub Action) is wired up yet — recommended so these regressions can't silently return.
