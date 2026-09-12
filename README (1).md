# WCAG 2.2 Audit & Remediation — TrailBrew Coffee Co. (demo)

A self-contained accessibility audit project: a demo storefront page built with **18 realistic, intentional accessibility bugs**, a full **audit report** mapping each to a WCAG 2.2 success criterion with severity ratings, and a **remediated version** with the fixes implemented in code.

This mirrors the actual workflow of an accessibility evaluation: automated scan → manual keyboard pass → screen reader pass → documented findings → code-level fixes.

## Structure

```
before.html        the buggy demo site (images embedded inline, no external files needed)
after.html          the same site with 18 issues fixed (images embedded inline)
AUDIT_REPORT.md      full findings table, SR walkthrough notes, fix summary
```

## How to use this project

1. Open `before.html` in a browser.
2. Unplug your mouse and try to: tab to "Add to cart," submit the newsletter form, open Quick View and close it with Escape, and tell what the three footer icons link to. All five are impossible or broken.
3. Turn on VoiceOver (`Cmd+F5` on Mac) and repeat the same walkthrough — note what does and doesn't get announced.
4. Open `after.html` and repeat the same steps — everything above now works with keyboard and screen reader alone.
5. Read `AUDIT_REPORT.md` for the full findings table and the exact code fix behind each one.

## What this project demonstrates

- Reading and applying **WCAG 2.2** success criteria and **WAI-ARIA** patterns (dialog, live region, focus management) rather than just running a scanner and copy-pasting its output
- Testing with an **assistive technology** (VoiceOver) and describing actual announced behavior, not just visual/DOM inspection
- Distinguishing **automated-detectable** issues (missing alt, contrast, missing labels) from ones that only show up in **manual keyboard/AT testing** (focus traps, silent auto-rotating content, color-only error states)
- Writing findings the way a consulting engineer would hand them to a product team: issue → criterion → severity → concrete before/after code, not just "make it accessible"

## Next steps if extended
- Wire up `axe-core` as an automated regression check
- Add a custom `<select>`/combobox pattern per the ARIA Authoring Practices Guide
- Full state-by-state (hover/focus/active/disabled) contrast audit
