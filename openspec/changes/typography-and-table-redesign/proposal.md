## Why

Following the `design-polish-pass`, two more issues surfaced under closer review: the site loads two entirely unused font weights (~500KB of dead data on every visit) with no weight-based hierarchy to show for it, and the CV table's rigid 7-column grid breaks down badly for projects with long festival/award lists — a single row (MAILIN) wraps its Awards cell into 12+ narrow lines while the Role/Premiere cells in the same row sit mostly empty, making the table feel bloated and uneven.

Both directions below were reviewed and agreed with the user before drafting this proposal.

## What Changes

**Fonts:**
- Remove the two unused `@font-face` weights (200 Thin, 900 Black — normal + italic, 4 of 6 declared faces) from `fonts.css`, keeping only 400 and 500 (the only weights ever referenced in `styles.css`).
- Introduce deliberate weight contrast using the two weights that remain: the name and section labels move to 500 where they currently sit at 400, reinforcing hierarchy without adding font data.

**Table layout** — replace the rigid multi-column `<table>` grid with a per-project "record" layout, for every table kind (film, commercial, event, education):
- A compact top line per project: status dot, entity (title + subtitle), role, premiere — laid out horizontally, same information as today, same visual weight.
- Long-form fields (festivals/clients, awards, support/details) become full-width labeled blocks stacked below the top line, each getting the entire row's width to wrap into instead of a narrow fixed column — so a 15-item festival list wraps into 2-3 lines instead of 7, and a project with nothing to say in a field renders no block at all (no empty space).
- This brings desktop closer to the existing mobile stacked-card pattern (already proven, already shipped) rather than inventing a new layout language — continuity, not reinvention.

## Capabilities

### New Capabilities
- `visual-design`: `design-polish-pass` introduced this capability's spec but hasn't been archived yet, so it doesn't exist in the canonical `openspec/specs/` tree — this change adds two more requirements to the same capability path (font-weight hygiene, record-style project layout), to be reconciled with `design-polish-pass`'s delta when both are archived.

### Modified Capabilities
(none)

## Impact

- **Files affected**: `fonts.css` (remove 4 `@font-face` blocks), `styles.css` (new screen-only `.cv-table` layout rules; a few `font-weight` tweaks).
- **No content changes** — `content.md` is untouched; same data, same fields, just laid out differently.
- **No JS/HTML changes** — `buildTable()`/`normalizeTable()` keep emitting the exact same `<table><thead><tr><th>`/`<td>` markup they do today. The record layout is achieved purely by restyling that existing markup with CSS (extending the same `display: block` technique the mobile stacked-card view already uses), the same approach `remove-audio-beat-studio` and `design-polish-pass` both favored: surgical, minimal-diff changes over rewrites.
- **Print/export unaffected** — `print.css` already forces `display: table-*` on every `.cv-table` element under `html.a4-mode` with its own column widths (verified: lines 182-238, 387-399). Since the HTML markup isn't changing, those existing rules keep working exactly as they do today with zero edits needed. Still verified against every export variant as a task, not assumed.
- **Accessibility note**: the mobile stacked-card view already restyles this same table with `display: block`, which drops native table semantics for screen readers on some browser/AT combinations. Extending that presentation to desktop makes this pre-existing trade-off universal rather than introducing a new one — noted in design.md, not treated as a new regression.
