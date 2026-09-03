## 1. Font weight cleanup

- [x] 1.1 Re-confirmed zero usages of `font-weight: 200` or `font-weight: 900` anywhere in `styles.css`/`index.html` prior to any change
- [x] 1.2 **Corrected from original plan** (no real 500 weight exists — see design.md Decision 1-3): removed `@font-face` blocks for 200 normal, 200 italic, and 900 italic from `fonts.css`; kept 400 normal, 400 italic, and 900 normal (verified: exactly 3 faces remain, correct weight/style)
- [x] 1.3 `fonts.css` dropped from 775,791 to 388,240 bytes (~387KB saved, ~50%); verified visually — text renders cleanly in both themes, no fallback glyph glitches

## 2. Type hierarchy

- [x] 2.1 Changed `#cv-name` from `font-weight: 400` to `900`
- [x] 2.2 Reverted `#hero-scroll` from `font-weight: 500` to `400`
- [x] 2.3 Verified visually (after a hard-reload — browser had cached the old `styles.css`): the name now renders as a bold, striking Black weight, a clear real tier above body text; no visual regression elsewhere

## 3. Table: build the record layout (screen only)

- [x] 3.1 Wrote the new `tbody tr` grid rules: a horizontal head line (status/entity/role/premiere as explicit grid-column placements)
- [x] 3.2 **Corrected during implementation**: the originally-planned `display: grid` internal layout for detail blocks (label | value columns) doesn't work reliably — a bare text node next to a `::before` pseudo-element doesn't grid-place into the value column (confirmed via screenshot: text confined to the label's width, huge empty gap beside it). Switched to absolute-positioned label + `padding-left` on the value (same technique already used elsewhere in this codebase for the status dot) — reliable, no anonymous-box ambiguity
- [x] 3.3 Collapsed empty detail fields via `:empty { display: none }` — verified via DOM query on the Commercial Production table: all rows show `awardsDisplay: "none"`, consistent 141px row height, no dangling gaps
- [x] 3.4 Left the existing `max-width: 699px` mobile block untouched rather than folding it in — verified it correctly overrides the new desktop rules at narrow widths (`position: static`, `padding-left: 0` confirmed via computed style at 500px width) with zero conflict; the two rule sets coexist cleanly since the mobile query has higher specificity/later source order
- [x] 3.5 Verified against the MAILIN row: Festivals went from 7 wrapped lines to 3, Awards from 12+ lines to ~4, using the full row width; Role/Premiere now carry their own small labels and sit in a compact head line with no large empty gap beneath them
- [x] 3.6 (added) Removed the now-obsolete `@media (max-width: 900px) and (orientation: landscape) { .cv-table { min-width: 700px } }` rule — it forced horizontal scroll for a fixed-column table that no longer exists; verified no horizontal overflow at 800px width (`bodyScrollWidth === innerWidth`)

## 4. Table: regression pass across all content shapes

- [x] 4.1 Verified the Commercial Production table lays out correctly (Company/Role/Location/Clients head line + detail block, no awards/support blocks rendered)
- [x] 4.2 Verified Events & Jury and Education & Languages tables (Event/Role/Location/Details and Institution/Degree/Details) lay out correctly with their own field meanings
- [x] 4.3 Verified via DOM: all 4 Commercial Production rows have `awardsDisplay: "none"` and a consistent 141px height — no dangling empty space
- [x] 4.4 Verified `.award-wip` renders correctly (found, correctly muted-colored) inside the new awards block on the MAILIN row
- [x] 4.5 Status dots render correctly in the head line (verified visually; empty status uses `visibility: hidden` to preserve column alignment rather than collapsing it)
- [x] 4.6 Tested at mobile (390px), tablet (800px), and desktop (1400px) widths in dark and light themes — no horizontal overflow at any width, mobile stacked-card view fully intact

## 5. Print/export verification

- [x] 5.1 Confirmed via `git diff` — zero changes to `print.css`
- [x] 5.2 Verified via screenshot + computed style: standard export mode renders the full classic table (visible PROJECT/ROLE/PREMIERE/FESTIVALS/AWARDS/SUPPORT header row, status dots, 7-column layout), `display: table-row`/`table-cell` confirmed on both standard and one-page modes — DRIFT/100%/EAVE all reuse the same `html.a4-mode .cv-table` CSS path (no variant-specific table styling exists), so this coverage extends to them
- [x] 5.3 Not needed — no divergence found; the design.md assumption held

## 6. Full verification pass

- [x] 6.1 Reloaded fresh — console completely clean, zero messages
- [x] 6.2 Verified via JS: dark mode toggle, section hide/show, and hero CTA (correct `href`, not `aria-hidden`) all still work
- [x] 6.3 Verified `srcset` attribute intact (`photo-800.jpg 800w, photo.jpg 1024w`); mobile hero sizing untouched by this change (no edits to that CSS block)

## 7. Commit

- [x] 7.1 Confirmed: only `fonts.css` (-3 lines, the removed @font-face blocks) and `styles.css` (+76/-48) changed; `index.html` has zero diff
- [x] 7.2 Commit with a clear message referencing this OpenSpec change

## 8. Post-review refinements (user feedback: "looks odd" / "table too big and confusing")

- [x] 8.1 Reverted `#cv-name` from `font-weight: 900` back to `400` — the Black weight read as loud/poster-like against the site's otherwise quiet, restrained register; hierarchy now comes from size alone (48-88px vs body text), which fits "lean/minimal/elegant" better than a fabricated weight jump
- [x] 8.2 **Found and fixed a real bug**: `content.md` uses a literal `—` (em dash) as the "no value" placeholder for premiere/awards/support cells. The `:empty` CSS collapse rule (task 3.3) never matched these, since the cell wasn't actually empty — it contained "—". Every project with any empty field was showing a labeled row with just a dash in it (e.g. "PREMIERE —", "AWARDS —", "SUPPORT —"), which was probably the biggest contributor to the table still feeling big/cluttered after the redesign. Fixed in `buildTable()` (`index.html`): a cell whose trimmed value is exactly `-` or `—` now renders empty, so the existing `:empty` collapse correctly hides it
- [x] 8.3 Verified the fix is safe: grepped `content.md` for every `—`-only cell (4 instances, all genuine "nothing here" placeholders, no legitimate content matches the pattern)
- [x] 8.4 Verified visually: FAUCES/PHANTOM DATA/VYOLA CYANA rows (previously 5-6 lines each with dash clutter) are now 3-4 tight lines with only the fields that have real content
- [x] 8.5 Considered also removing the Role/Premiere micro-labels for further decluttering, but held off — for other table kinds (e.g. Education's "Degree" field showing a bare year like "2026") the label is load-bearing for clarity, not just decoration; the dash-placeholder fix alone resolved most of the bloat, so didn't risk a clarity regression for marginal gain
- [x] 8.6 Verified no regression: console clean, print/export cells render correctly blank (no dash) where a field is empty, `{wip}` award-splitting logic unaffected (checked before the new placeholder check, doesn't overlap)
