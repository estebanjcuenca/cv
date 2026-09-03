## Context

See `proposal.md` - Why. Verified during review (recorded here since it drove the decisions below):

- Audio/hover/studio code is fully isolated in `js/audio/`, `js/hover/`, `js/studio/`, `js/vendor/three*`, and `studio.css` — no other page (`privacy.html`) references any of it, and `styles.css`/`print.css` have no `@import` of `studio.css`.
- `disc-atlas.html` and `disc-atlas-assembly_3.jsx` (a hardware component layout tool for the beat-studio physical controller) were gitignored local-only files, never committed to this repo. They have already been copied to the `beat-studio` repo and deleted from this working tree, ahead of and independent from the rest of this change's task list — the user identified them as beat-studio-related mid-review. Listed here for the record; no remaining task depends on them.
- **Incident on this change**: an earlier session mistake (`git reset --hard` run to recover from an unrelated subdomain experiment, before this change existed) discarded uncommitted pre-session edits to several tracked files and appeared to destroy an untracked `Controller/` directory (OpenSCAD source for the same physical controller). The tracked-file edits were recovered from the reflog. `Controller/` was initially believed unrecoverable (a `git log --all` check missed it because `--all` only searches ref-reachable history, not dangling reflog commits) but was in fact fully recovered: it had been accidentally committed mid-session by a `git add -A`, then orphaned by the same reset. Recovered from those dangling commits, copied to the `beat-studio` repo (source `.scad`/`.py` tracked, generated `stl/` gitignored there per the original intent), committed, and pushed. Removed from this repo afterward. This is unrelated to the audio/beat-studio removal itself but is recorded here since it surfaced during this change's review.
- `.audio-drawer` (the class on both `#hover-panel` and `#studio-panel`) is not styled by any stylesheet in the repo — it can be deleted with its host elements with zero visual side effect elsewhere.
- `#nav-vibe` / `.nav-vibe-*` styles exist only in `styles.css` (lines 89-119) and are not reused by non-audio UI.
- The CV render pipeline in `index.html`'s inline `<script>` has exactly one behavioral coupling to audio: the `isBank`/`data-bank`/`midi-bank` logic on rendered `<section>` elements (driven by `ECAudio.MIDI_BANK_SLUGS`), plus five post-render calls (`initInfluenceZones`, `ECAudio.BrowseSound.applyEngine`, `syncHoverPanelUI`, `ECAudio.Markers.*`, `syncMusicSequencer`/`TrackSeq.unmount`) and the two boot calls `NavVibe.init()` / `ECAudio.loadSoundPrefs()`. Everything else in that script (markdown parsing, table building, section toggling, dark mode, A4/print/export logic) has no audio coupling.

## Goals / Non-Goals

**Goals:**
- Zero runtime trace of the audio/beat-studio system: no files, no script/link tags, no DOM nodes, no function calls, no console errors.
- Byte-for-byte-equivalent behavior for everything in the `portfolio-site` spec: CV rendering, section hide/show, dark mode, and all print/export variants.
- Small, reviewable diff on `index.html` and `styles.css` rather than a rewrite — delete precisely the identified blocks, touch nothing else.

**Non-Goals:**
- Not restructuring or refactoring the surviving CV/print/export code, even where style could be improved — this change is subtractive only.
- Not touching the `beat-studio` repository (it already has its own full copy; this change only affects `esteban-cuenca-site`).
- Not changing `content.md`, portfolio copy, or visual design of the remaining site beyond removing the now-orphaned toolbar buttons.

## Decisions

1. **Delete files outright rather than comment out.** The user's explicit ask is "no traces" — commenting out (what caused confusion earlier this session) leaves dead code, dead weight, and a maintenance trap. Decision: `git rm` the audio/hover/studio directories and `studio.css` entirely.
   - Alternative considered: keep files but unreferenced. Rejected — doesn't satisfy "no traces," and this repo's git history already preserves them (plus the full duplicate lives in `beat-studio`), so nothing is lost by deleting.

2. **Edit `index.html` and `styles.css` surgically, in dependency order**: remove DOM nodes and their toolbar triggers first, then the post-render function calls that reference now-removed globals, then the boot calls, then the `<script>`/`<link>` tags, then confirm no dangling selectors remain in `styles.css`. Doing DOM/JS-logic edits before deleting the files means each intermediate state is still checkable in a browser (no broken script tags pointing at deleted files mid-edit).

3. **Verification is grep-driven, not just visual.** After edits, grep `index.html`/`styles.css` for every removed global/class (`ECAudio`, `NavVibe`, `TrackSeq`, `Sound`, `soundEnabled`, `audio-drawer`, `nav-vibe`, `beat-studio`, `midi-bank`, `data-bank`) and confirm zero remaining hits outside comments/docs. This directly targets the "No dead references to removed code" requirement in the spec, which is otherwise easy to eyeball-miss (e.g., a stray call buried in a 1000+ line inline script).

4. **Three.js and its addons are deleted, not kept "just in case."** They're multi-hundred-KB vendor files used exclusively by `js/studio/beat-view3d.js`; keeping them would violate "no traces" and they're safely preserved in the `beat-studio` duplicate if ever needed again.

5. **`README.md` gets a light edit, not a rewrite**, pointing audio/beat-studio mentions at the new `beat-studio` repo — kept out of the tasks' critical path since it doesn't affect shipped behavior, but included so the docs don't mislead future readers of this repo.

## Risks / Trade-offs

- **[Risk]** A subtle runtime dependency is missed (e.g., some print/export code path indirectly calls into an audio global under a rare condition, like a variant page that isn't exercised during manual testing) → **Mitigation**: the grep-based verification in Decision 3 catches any remaining reference regardless of which code path would have hit it; additionally, manually exercise every print/export variant listed in the spec (standard, one-page, DRIFT, 100% Film, EAVE) after the edit, in a real browser, before considering the change done.
- **[Risk]** Deleting `js/vendor/three*` or `js/vendor/addons/**` turns out to break something outside `js/studio/` that wasn't caught by the grep in Context → **Mitigation**: re-run the "referenced by anything other than js/studio/*" grep as a task step immediately before deleting vendor files, not just once during planning.
- **[Trade-off]** This is a large, irreversible-feeling deletion (~14.8k lines across 40 files) in one repo — but it's fully reversible via git history, and a complete duplicate already exists at `beat-studio` (pushed to GitHub), so the risk of true data loss is effectively zero.

## Migration Plan

1. Remove DOM: toolbar buttons (`#btn-studio`, `#btn-hover-settings`), `#nav-vibe` markup, `#hover-panel`/`#studio-panel` asides, `#beat-studio` workspace div.
2. Remove the audio-coupled lines inside the inline render script (`isBank`/`data-bank`/`midi-bank`, the five post-render calls, the two boot calls) and any now-unused helper functions those leave behind (e.g. `toggleStudio`, `toggleHoverPanel` button handlers, `initScrollReveal`'s neighbors if audio-only).
3. Remove the `<script src="js/audio|hover|studio/...">` tags and the `studio.css` `<link>`.
4. Delete `js/audio/`, `js/hover/`, `js/studio/`, `js/vendor/three*`, `js/vendor/addons/**` (after re-confirming nothing outside those dirs references them), and `studio.css`.
5. Remove now-dead CSS selectors from `styles.css` (`#nav-vibe`, `.nav-vibe-*`, and any other audio-only rules found during the sweep).
6. Grep-verify zero remaining references (Decision 3's list).
7. Manually test in a browser: CV renders, section hide/show + persistence, dark mode, standard print, one-page mode, DRIFT, 100% Film, EAVE — confirm no console errors, no 404s.
8. Update `README.md` mentions of audio/beat-studio to point at the `beat-studio` repo.
9. Commit with a clear message; rollback is `git revert` if anything is discovered post-merge.

## Open Questions

None — scope, deletions, and verification approach are fully determined by the review above; nothing here needs to be deferred.
