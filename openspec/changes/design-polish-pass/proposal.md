## Why

The portfolio is now audio-free and structurally clean (see `remove-audio-beat-studio`), but it hasn't had a dedicated visual/UX design pass since the audio system was stripped out, and several things predate that work entirely. A full audit across colors, icons, layout, typography, imagery, tables, header, footer, and CTA turned up a mix of leftover dead code, inconsistencies, and missed opportunities — some are objective fixes, others are genuine design decisions the user should make rather than have made for them.

## What Changes

**Findings, by area** (audited live in both themes, desktop 1400px and mobile 390px):

- **Colors**: Warm neutral palette (`--ink`/`--mid`/`--bg`, cream `#F0EDE7` light / near-black `#0F0D09` dark) is clean and consistent across both themes. **Dead code**: `--role-kick`, `--role-chord`, `--role-lead`, `--role-hat` (4 tokens × 2 themes) are defined in `:root` and `html.dark` but referenced nowhere else in the codebase — leftover from the removed beat-studio layer-color system.
- **Icons**: Only two icons exist site-wide — the dark-mode toggle (`◐`, a Unicode text glyph, not an SVG) and the footer export button (one inline SVG). No shared icon system; the toggle glyph renders inconsistently across fonts/OSes since it depends on font glyph coverage rather than being drawn.
- **Layout**: Desktop hero (name/bio left, sticky full-bleed portrait right) is strong and reads well. **On mobile**, the portrait alone fills the entire viewport height before any text appears — a visitor sees only a photo on load and must scroll a full screen before reaching the name, title, or bio.
- **Fonts**: Single family (PP Radio Grotesk) used throughout for both headings and body — differentiated only by size/weight/tracking, never by family. `fonts.css` is 775KB of base64-embedded `@font-face` data (6 faces: weights 200/400/900, each with an italic) loaded on every visit regardless of which weights the page actually renders.
- **Picture**: Grayscale treatment on the hero portrait is a strong, consistent stylistic choice. The `<img>` has no `srcset`/`sizes` — mobile downloads the same full-resolution file as desktop.
- **Tables**: CV tables read well on desktop. On the mobile stacked-card layout, long comma-separated festival/award lists (some 15+ entries) run as dense unbroken paragraphs under a small-caps label, several screens tall for a single row.
- **Header/Toolbar**: Minimal and clean day-to-day (name + dark-mode toggle). In export mode, `Back`, `1 pg`, `DRIFT`, `100%`, and `EAVE` all render as identical plain-text links with no visual hierarchy — a navigational action (Back) and content-transforming actions (the three variant switches) read as the same kind of control.
- **Footer**: Email, Privacy Policy, location, and copyright wrap into a dense single row on narrow viewports; the print/export trigger is an icon-only button with no visible label, discoverable only via hover title text.
- **CTA**: There is no explicit call to action anywhere on the site. The only contact path is a plain-text `mailto:` link at the bottom of the footer, visually identical in weight to the copyright notice beside it.

**Proposed changes** (see design.md for the objective/subjective split and open questions):

- Remove the dead `--role-*` color tokens.
- Establish one consistent icon approach (likely: replace the `◐` glyph with an inline SVG matching the footer icon's style) — **pending user direction on visual style**.
- Fix the mobile hero so name/title/bio are reachable without a full scroll first — **pending user direction on the specific layout**.
- Add `srcset`/`sizes` to the hero portrait for responsive image delivery.
- Improve mobile table readability for long list-style cells — **pending user direction on treatment** (truncation + expand, tighter line-height, or accept as-is).
- Differentiate the export-mode toolbar's navigational vs. variant-switching controls — **pending user direction on visual treatment**.
- Consider adding a deliberate, visible CTA (e.g., a styled "Get in touch" affordance) — **pending user decision on whether this is wanted at all, and where**.
- Evaluate whether a second, differentiated typeface for headings is worth introducing, or whether the single-family system should be kept and just reinforced — **pending user preference**.

## Capabilities

### New Capabilities
- `visual-design`: Defines the required visual/UX standards for the portfolio site — color token hygiene, icon consistency, responsive image delivery, mobile layout ordering, and table readability — as a baseline the trimmed site should meet.

### Modified Capabilities
- `portfolio-site`: No requirement-level behavior change from this audit alone; any actual UI change adopted from this proposal will need a follow-up delta once directions below are decided.

## Impact

- **Files likely affected**: `styles.css` (token cleanup, hero mobile layout, table treatment, icon/CTA styling), `index.html` (icon markup, `srcset`, possible CTA markup), `fonts.css` (only if font strategy changes).
- **No content or data changes** — `content.md` is untouched by this proposal.
- **Depends on user decisions** before `tasks.md` can be finalized for the subjective items — see design.md Open Questions.
