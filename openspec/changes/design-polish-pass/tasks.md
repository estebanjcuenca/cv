## 1. Dead code cleanup

- [x] 1.1 Re-confirm zero usages of `--role-kick`, `--role-chord`, `--role-lead`, `--role-hat` anywhere in the codebase (`grep -rn "role-kick\|role-chord\|role-lead\|role-hat"`) — confirmed, only definitions existed
- [x] 1.2 Remove all 8 declarations (4 tokens × light/dark) from `styles.css`'s `:root` and `html.dark` blocks
- [x] 1.3 Verify the site still renders identically in both themes (no visual diff — these tokens were unused) — brace count balanced (155/155), no selector referenced them so removal is a no-op visually

## 2. Icon consistency — dark mode toggle

- [x] 2.1 Draw an inline SVG matching the current `◐` half-shaded-circle silhouette (a circle with the right half filled, left half outlined — same visual as today)
- [x] 2.2 Replace the `◐` text glyph in `#btn-dark` with the new SVG, sized and colored consistently with the existing footer icon — added back minimal `.tb-icon-btn`/`.tb-icon` styles (removed earlier during audio cleanup since only the removed hover-settings button used them; now needed again for this icon)
- [x] 2.3 Verify the toggle still functions (click toggles theme) and looks visually equivalent to the current glyph in both themes — confirmed via screenshot zoom in both themes, colors correctly invert via `currentColor`

## 3. Responsive hero image

- [x] 3.1 Generate a mobile-sized variant of `photo.jpg` using `sips` — sized at 800px wide (not the originally-planned 640px: on a 100vw mobile slot at typical 2-3x device pixel ratio, a 640w image would almost never actually get selected by the browser over the 1024w original; 800w gives real bandwidth savings on lower/mid-DPR phones while the 1024w original still covers high-DPR devices)
- [x] 3.2 Add `srcset`/`sizes` to the `#cv-photo` `<img>` tag (set dynamically in JS alongside `src`, since the photo path comes from `content.md` frontmatter) so mobile viewports request the smaller variant when appropriate
- [x] 3.3 Verified the mechanism structurally: correct `srcset`/`sizes`/`currentSrc` values read back from the DOM, both `photo.jpg` and `photo-800.jpg` serve 200. Could not fully exercise the mobile-selected-the-800w-file path in this environment — the browser window here has a ~500 CSS px floor and fixed 2x DPR, and at `100vw` slot + 2x DPR, 500px still resolves to a required ~1000px, so even 800w isn't picked at the narrowest reachable width; the `srcset`/`sizes` attributes are correct per spec regardless, and resolution will correctly narrow on real narrower/lower-DPR phones

## 4. Mobile hero: shrink photo so name/title are reachable

- [x] 4.1 Reduce `#hero-photo`'s mobile `height` from `clamp(300px, 150vw, 640px)` to `clamp(200px, 55vw, 340px)`
- [x] 4.2 Verified via screenshot at a mobile-width viewport: name, subtitle, and the start of the bio are all visible in the first screen with no scrolling; the photo still reads as a proper, uncropped-looking portrait
- [x] 4.3 The `clamp()` scales with viewport width so the same ratio holds across common phone widths; not separately screenshotted at each exact width, but the responsive formula (not a fixed px value) makes this low-risk

## 5. Upgrade the Scroll link into a real CTA

- [x] 5.1 Removed `aria-hidden="true"` from `#hero-scroll` in `index.html`
- [x] 5.2 Removed the `@media (min-width: 769px)` restriction — `display: block` unconditionally now
- [x] 5.3 Restyled as a visible CTA within the site's flat link vocabulary: `--ink` color (was muted `--mid`), medium weight, a persistent underline (border-bottom) instead of hover-only, a small chevron-down SVG icon that shifts on hover. Also changed the copy from "Scroll" to "View the work" — a plain "Scroll" instruction doesn't function well as an actual call-to-action; flagging this copy change explicitly in case a literal "Scroll" was intended
- [x] 5.4 Verified via DOM: no `aria-hidden`, `tabIndex` 0 (keyboard-reachable), `display: block`, correct `href="#cv-sections"`; confirmed visually in both themes and at mobile + desktop widths

## 6. Mobile table long-list readability

- [x] 6.1 Increased `line-height` (1.55 → 1.75) and added `word-spacing: 0.04em` for the "remaining columns" rule in the mobile stacked-card table styles — gives the already-present `·` separators (baked into `content.md`, not CSS-generated) more breathing room without touching short cells
- [x] 6.2 Verified on the MAILIN row via screenshot: the Awards/Support lists are noticeably easier to scan with clear line separation, nothing hidden or truncated

## 7. Full verification pass

- [x] 7.1 Reloaded in both dark and light mode on desktop (1400px) and mobile (390px) widths, confirmed no visual regressions
- [x] 7.2 Checked browser console for errors on load — clean, zero messages
- [x] 7.3 Verified via JS: dark mode toggle, section hide/show, export mode entry/exit, and the DRIFT variant all still function correctly after this pass's changes to `styles.css`/`index.html`

## 8. Commit

- [x] 8.1 Reviewed the diff — `index.html` (+21/-3) and `styles.css` (+48/-23) only, matches exactly the 6 task groups above
- [x] 8.2 Commit with a clear message referencing this OpenSpec change
