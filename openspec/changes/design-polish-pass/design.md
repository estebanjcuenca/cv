## Context

See proposal.md for the full audit. This design splits the findings into two buckets: **objective fixes** (no aesthetic judgment required — dead code, missing responsive attributes, accessibility gaps) that can be implemented directly, and **subjective design decisions** (color, iconography style, layout treatment, whether to add a CTA at all) that belong to the user, not to an assumed default. Per the site's existing visual vocabulary — warm neutral tones, plain-text link-style buttons, no rounded-corner card chrome, no gradients, restrained type scale via `clamp()` — any new UI should extend that vocabulary rather than introduce a new one, unless the user asks for a genuine redesign.

## Goals / Non-Goals

**Goals:**
- Fix everything in this pass that has one clearly correct answer (dead tokens, missing `srcset`, icon/glyph inconsistency's *mechanism* even if its *exact icon* needs a look).
- Surface every genuinely subjective choice as an explicit question rather than silently picking an answer for a personal portfolio's visual identity.
- Keep the existing, working visual vocabulary intact — this is a polish pass, not a rebrand, unless the user says otherwise in response to the open questions.

**Non-Goals:**
- Not redesigning the color palette, typography pairing, or overall layout structure unless the user explicitly asks for that after seeing these findings.
- Not touching `content.md` or any CV data.
- Not re-opening the audio-removal work from `remove-audio-beat-studio`.

## Decisions

1. **Dead `--role-*` tokens: remove outright.** No behavior depends on them (confirmed via full-codebase grep); no design judgment needed.
   - Alternative considered: keep them in case beat-studio-style color coding returns to the portfolio. Rejected — that feature now lives entirely in the `beat-studio` repo; if it ever returns here, it returns with its own tokens.

2. **Icon consistency: fix the *mechanism* now, defer the *look*.** The technical decision (draw every icon as inline SVG, none as a Unicode glyph) is objective and safe to implement immediately for the dark-mode toggle. The *shape* of that new SVG icon (a half-circle matching `◐`'s silhouette vs. a sun/moon pair vs. something else) is a visual choice — Open Question 1.

3. **Responsive image: add `srcset`/`sizes`, generate the smaller variants at implementation time.** Objective web-performance fix; no aesthetic decision involved. Requires generating 1-2 additional resolutions of `photo.jpg` (e.g. a ~800px-wide mobile variant alongside the original) — a mechanical step, not a design one.

4. **Mobile hero fix: hold for direction, not a specific implementation.** There are several reasonable ways to solve "name is off-screen on mobile" — reduce the photo's mobile height, put the text above the photo, overlay the name on the photo, or shrink the photo into a smaller framed portrait like the desktop layout's proportions suggest. Each has different visual consequences. Open Question 2.

5. **Toolbar hierarchy and CTA: both held for direction.** Distinguishing "Back" from "DRIFT/100%/EAVE" and deciding whether to add a visible CTA are both identity-level decisions for a personal portfolio — the design skill's own guidance is explicit that this kind of call belongs to the user when no aesthetic direction has been given yet. Open Questions 3 and 4.

6. **Mobile table long-list readability: propose a mechanical fix, but confirm before applying.** The likely correct fix (tighter `line-height` + a subtle separator between comma-joined items, no truncation) is close to objective, but truncation-with-expand is a legitimate alternative with real UX trade-offs (hides information vs. keeps rows compact) — Open Question 5.

## Risks / Trade-offs

- **[Risk]** Removing `--role-*` tokens turns out to be relied on by an inline `style="color: var(--role-kick)"` somewhere not caught by the grep → **Mitigation**: re-run the grep as a task step immediately before deletion, same discipline as the audio-removal change.
- **[Trade-off]** Deferring the subjective items to user Q&A means this change cannot reach `tasks.md` completion in one pass — `tasks.md` here covers only the objective fixes; a follow-up delta (or an amendment to this change) will cover whatever the user decides on the open questions.

## Open Questions — resolved

1. **Icon style**: ✅ Half-shaded circle — redraw the current `◐` silhouette as a crisp inline SVG, no visual change, just consistent rendering.
2. **Mobile hero layout**: ✅ Shrink the photo on mobile so name/title/bio reach the first screen, closer to how desktop balances the two.
3. **Toolbar hierarchy**: ✅ Keep uniform — no change to `Back`/`1 pg`/`DRIFT`/`100%`/`EAVE` styling.
4. **CTA**: ✅ Upgrade the existing subtle "Scroll" link into a real, visible CTA that prompts visitors into the CV sections. No separate contact CTA.
5. **Mobile table long lists**: Defaulting to the lower-risk, more objective option — tighter `line-height` and a subtle separator between comma-joined items in long cells, no truncation (nothing hidden, just easier to scan). Flagging this default rather than treating it as fully user-confirmed; revisit if it doesn't look right in practice.
