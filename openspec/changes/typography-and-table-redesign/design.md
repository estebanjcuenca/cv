## Context

See proposal.md for the audit and the user-agreed direction (strip unused font weights + add weight hierarchy; editorial record layout for tables). This design covers the concrete technical approach for both, verified against the actual current markup/CSS before writing tasks.

Current relevant facts, verified in-session:
- `styles.css` only ever sets `font-weight: 400` or `500` — the embedded 200 (Thin) and 900 (Black) `@font-face` weights (4 of 6 declared faces, normal + italic each) are pure dead weight in `fonts.css`'s 775KB of base64 font data.
- The CV table's column widths are `status 3% / entity 22% / role 12% / premiere 13% / festivals 26% / awards 13% / support 11%` (`styles.css` `.col-*` rules) — fixed percentages in a `<table>`, shared across every table kind via `MASTER_COLS` in `index.html`.
- Only the "film" table kind (`buildTable()`/`normalizeTable()` in `index.html`) populates the `awards`/`support` fields; other kinds (`commercial`, `event`, `education`) only ever use `entity`/`role`/`premiere`/`festivals`.
- The mobile view (`@media (max-width: 699px)`) already breaks the table out of tabular display (`display: block` on `table`/`tbody`/`tr`/`td`) into a stacked label+value card per row — this pattern already works and already ships.
- `print.css` forces `display: table-*` back on every `.cv-table` element under `html.a4-mode`, with its own independent column widths (`html.a4-mode .col-*` at lines 229-235, plus a one-page variant at 387-399) — these rules don't care what the screen-mode CSS does, only what the DOM/classes are, which aren't changing.

## Goals / Non-Goals

**Goals:**
- Fix the row-height imbalance for long-list fields by giving them the full row width to wrap into, at every screen width (not just mobile).
- Keep the existing `<table>` HTML markup and `buildTable()`/`normalizeTable()` JS completely untouched — this is a CSS restyle of existing markup, the same technique the mobile view already uses, extended and refined.
- Keep print/export completely unaffected — verified as a task, not assumed.
- Land the font-weight cleanup and the table redesign as one change since both touch `styles.css`'s type/table rules and were reviewed together, but keep the diffs for each independently reviewable.

**Non-Goals:**
- Not changing `content.md` or shortening any actual festival/award list — the fix is layout, not content trimming.
- Not introducing a second typeface — still PP Radio Grotesk, still exactly the two weights (400, 500) already in use; no new font files.
- Not touching print.css — if verification in tasks turns up a real conflict, that's a stop-and-reassess moment, not a silent scope expansion.

## Decisions

1. **Correction found during implementation**: there is no real 500-weight font file — only 200 (Thin), 400 (Regular), 900 (Black) are declared. `font-weight: 500` (used once, on `#hero-scroll` from `design-polish-pass`) was silently resolving to 400 via CSS font-weight matching, achieving no visual difference. Flagged to the user; resolved as follows.

2. **Font cleanup: drop 200 (both styles) and 900 italic; keep 900 normal.** This delivers most of the file-size win (~250KB from dropping Thin entirely) while keeping a real, visible heavy weight available for hierarchy. `fonts.css` ends up with 3 faces instead of 6: 400 normal, 400 italic, 900 normal.

3. **Weight hierarchy: use real 900 for the name, real 400 for everything else.** `#cv-name` moves to `font-weight: 900`. The `#hero-scroll` and `.sec-label` "500" idea is dropped (no such weight exists) — `#hero-scroll` reverts to explicit `font-weight: 400` (its actual rendered weight all along, just now honest about it) and `.sec-label` stays 400. Hierarchy comes from the name's real weight jump (400 → 900) plus its existing large size, not from a fabricated mid-weight tier.

3. **Table redesign: CSS-only, via a `display: grid` restyle of the existing `<table>`, not a markup rewrite.** The mobile view proves the technique (breaking `<table>` semantics visually via CSS while keeping the HTML) already works and ships. The desktop version differs from the mobile one only in how the "top line" (status/entity/role/premiere) is arranged — horizontally in a grid row on desktop/tablet widths, stacked on narrow mobile as it is today:
   - `tbody tr` becomes `display: grid` with a template area splitting the row into a "head" line (status, entity, role, premiere as grid items) and stacked "detail" rows for whichever of festivals/awards/support have content.
   - Each detail field (`.col-festivals`, `.col-awards`, `.col-support`) spans the full row width (`grid-column: 1 / -1`) and is itself `display: grid` with a `label | value` two-column split (a fixed-width label column, e.g. `110px`, then the value flowing into the remaining width) — this is what actually fixes the wrapping problem, since a 15-item list now wraps against (row width − 110px) instead of 13% of it.
   - Empty fields (no awards, no support, `raw[col.key]` falsy) already don't get a value rendered by `buildTable()` today, but currently still occupy a `<td>` — need to also collapse that `<td>`'s box when empty (`display: none` via a CSS attribute/`:empty` selector, or reuse the existing `col-status-empty` pattern's logic if it generalizes) so an empty field doesn't reserve a labeled block with nothing in it.
   - This subsumes the current `max-width: 699px` mobile-only rules — they become the default (small-viewport-tuned) case within the same rule set, with a wider breakpoint adding the horizontal top-line treatment for tablet/desktop widths.

4. **Print stays completely separate and untouched.** `print.css`'s `html.a4-mode .cv-table` rules already `!important`-force `display: table-*`, independent of whatever the non-a4-mode screen rules say. No edit needed there; verified as a task by actually exercising the standard/one-page/DRIFT/100% Film/EAVE export flows after the screen CSS changes.

## Risks / Trade-offs

- **[Risk]** The `display: grid` restyle interacts unexpectedly with an existing rule that assumes `display: table-*` outside of `a4-mode` (something not caught by grepping `.cv-table`/`.col-*` selectors) → **Mitigation**: full regression pass across all four table kinds (film, commercial, event, education) and every content shape (empty fields, `{wip}` awards split, `{dev}` rows) as an explicit task, not just the one MAILIN row that surfaced the problem.
- **[Trade-off]** Extending the "table restyled as non-tabular via CSS" pattern from mobile-only to universal makes the existing screen-reader/table-semantics trade-off (noted in proposal.md) apply everywhere, not just on narrow viewports. Accepted per the user-agreed direction; not re-litigated here since it's a continuation of a pattern already shipped, not a new one.
- **[Risk]** Collapsing empty detail fields cleanly (no stray label, no stray gap) needs care in the grid's `row-gap` handling — an entry with zero long-form fields (e.g. a short commercial-production row) shouldn't leave a dangling gap where the (absent) detail rows would have been → **Mitigation**: verify visually against a short entry (e.g. a commercial-production row with no clients/details content) as well as a long one.

## Open Questions

None — direction was agreed with the user before this design was written; the technical approach (CSS-only, no print.css changes) is the implementer's call and has one clearly correct answer given the constraints found.
