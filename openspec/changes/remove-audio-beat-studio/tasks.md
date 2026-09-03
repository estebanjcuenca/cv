## 1. Pre-flight verification

- [x] 1.1 Confirmed `beat-studio` has the full audio/hover/studio codebase (js/audio, js/hover, js/studio, three.min.js, studio.css all present and pushed) — HEADs have since diverged from this repo's own unrelated commits, but the content being deleted here is fully backed up there
- [x] 1.2 Re-run the cross-reference greps from design.md Context (`three.min.js`/`three.module`, `audio-drawer`, `nav-vibe`) against `privacy.html` and `print.css` (not `styles.css` itself, which legitimately contains the `#nav-vibe` rules scheduled for removal in section 6) and confirm zero hits
- [x] 1.3 `disc-atlas.html` / `disc-atlas-assembly_3.jsx` copied to the `beat-studio` repo (committed, pushed, un-ignored there) and deleted from this repo's working tree — done ahead of the main task list per user request during review

## 2. Remove audio-coupled DOM from index.html

- [x] 2.1 Remove the `#nav-vibe` markup block from the toolbar and verify `index.html` still has valid, balanced HTML (open it in a browser, no parse errors in devtools)
- [x] 2.2 Remove the `#btn-studio` ("Studio") and `#btn-hover-settings` toolbar buttons
- [x] 2.3 Remove the `<aside id="hover-panel">` block in full
- [x] 2.4 Remove the `<aside id="studio-panel">` block in full
- [x] 2.5 Remove the `<div id="beat-studio">` workspace block in full
- [x] 2.6 Load `index.html` in a browser and verify the toolbar shows only the dark-mode button plus the existing export/back/variant buttons, with no audio-related buttons or panels present — confirmed: toolbar shows Toggle dark mode, Back, Switch to one-page layout, EAVE only; CV content renders correctly

## 3. Remove audio-coupled logic from index.html's inline script

- [x] 3.1 In the CV section render loop, remove the `isBank`/`ECAudio.MIDI_BANK_SLUGS` line and the `${isBank ? ' midi-bank' : ''}` / `data-bank="${slug}"` markup, leaving the `<section>` tag with only its non-audio classes/attributes
- [x] 3.2 Remove the post-render calls: `initInfluenceZones()`, the `ECAudio.BrowseSound.applyEngine` block, `syncHoverPanelUI()`, the `ECAudio.Markers.*` block
- [x] 3.3 Remove the `soundEnabled`/`syncMusicSequencer`/`TrackSeq.unmount`/`ECAudio.runSoundDiagnostics` block inside the `requestAnimationFrame` callback, keeping `initScrollReveal()` itself
- [x] 3.4 Remove the `NavVibe.init()` and `ECAudio.loadSoundPrefs()` boot calls
- [x] 3.5 Remove the now-orphaned `toggleStudio()` and `toggleHoverPanel()` function definitions — confirmed neither was ever defined in `index.html`'s inline script (they lived in the deleted `js/hover/` files), so nothing to remove here
- [x] 3.6 Verify `render()` and the rest of the inline script still parse and run with no `ReferenceError` by loading the page and checking the console — confirmed zero console messages on a fresh load

## 4. Remove script/link tags

- [x] 4.1 Remove all `<script src="js/audio/...">`, `<script src="js/hover/...">`, and `<script src="js/studio/...">` tags from `index.html`
- [x] 4.2 Remove the `<link rel="stylesheet" href="studio.css">` tag from `index.html`
- [x] 4.3 Verify `index.html` no longer contains the substring `js/audio`, `js/hover`, `js/studio`, or `studio.css` (`grep -c` returns 0 for each) — confirmed

## 5. Delete now-unused files

- [x] 5.1 Delete the `js/audio/` directory (`git rm -r js/audio`)
- [x] 5.2 Delete the `js/hover/` directory (`git rm -r js/hover`)
- [x] 5.3 Delete the `js/studio/` directory (`git rm -r js/studio`) — also caught `machines-spec.md`/`beat-mix-stages.md` internal design docs living inside those dirs; confirmed both already exist in `beat-studio`
- [x] 5.4 Delete `js/vendor/three.min.js` (the only vendor file present; `three.module.min.js`/`addons/` mentioned during planning turned out to be transient session artifacts, not real repo content, and are already gone)
- [x] 5.5 Delete `studio.css`
- [x] 5.6 Verify `git status` shows these paths removed and no other paths were touched unintentionally — confirmed: only `index.html` (modified) plus the js/audio, js/hover, js/studio, js/vendor/three.min.js, studio.css deletions staged; also caught and fixed a stale pre-session edit to `js/studio/studio-input.js` that hadn't synced to `beat-studio` yet

## 6. Clean up styles.css

- [x] 6.1 Remove the `#nav-vibe`, `.nav-vibe-svg`, `.nav-vibe-path`, `#nav-vibe.active .nav-vibe-path`, and the `#nav-vibe { display: none; }` media-query rule from `styles.css`
- [x] 6.2 Grep `styles.css` for any other audio-only selector (`audio-drawer`, `beat-studio`, `hover-panel`, `studio-panel`, `sl-`, `sp-`) and remove any found that are not used by surviving CV/print/layout markup — found and removed far more than anticipated: `.toolbar-sound`, `.tb-icon-btn`/`.tb-icon` (only used by removed hover-settings button), `#btn-studio*`/`#btn-hover-settings*`, `.beat-studio-loading`, `body.hover-panel-open` (dead since its JS toggle is gone), three full "STUDIO+SOUND" marked sections (~670 lines: browse-mode/sound-markers, role-segment buttons, beat-overlay+track/sequencer grid), a duplicate `sound-on`/`track-*` block inside a mobile media query, the single `html.a4-mode #hover-panel` rule, and the entire trailing hover-panel/`.sl-`/`.sp-` drawer CSS block (268 lines) to EOF. File went from 1661 → 639 lines, braces/comments balanced (155/155, 33/33)
- [x] 6.3 Verify the site's remaining visual layout (toolbar, hero, CV sections, footer) is unchanged by comparing a screenshot before/after this task group — confirmed via live screenshots: hero, dark mode, CV tables (including newly-restored VYOLA CYANA entry), commercial production section all render correctly

## 7. Full-repo dead-reference sweep

- [x] 7.1 Grep the entire repo (excluding `openspec/`, `.git/`, and this change's own files) for `ECAudio`, `NavVibe`, `TrackSeq`, `soundEnabled`, `audio-drawer`, `nav-vibe`, `beat-studio`, `midi-bank`, `data-bank` and confirm zero matches — confirmed zero in all shipped files (`index.html`, `styles.css`, `print.css`, `privacy.html`, `content.md`, `CNAME`); only hits are in `README.md` (handled in section 9) and this change's own planning artifacts
- [x] 7.2 Serve the site locally and check the browser console + network tab for any 404s or reference errors on page load — confirmed: 9 requests, all HTTP 200, zero audio/studio assets requested, console completely clean

## 8. Manual functional verification

- [x] 8.1 Verify CV sections render correctly from `content.md` (names, tables, paragraphs all present and correctly formatted) — confirmed clean: only the 4 real sections show as headers (Film & Immersive Work, Commercial Production, Events & Jury, Education & Languages)
- [x] 8.2 Verify section hide/show toggling works and persists across a page reload — confirmed via toggle + localStorage `ec-hidden` check
- [x] 8.3 Verify dark mode toggling works and persists across a page reload — confirmed via toggle + localStorage `ec-dark` check
- [x] 8.4 Verify standard multi-page print/export renders correctly — confirmed: hero on page 1, tables+footer on page 2
- [x] 8.5 Verify one-page export mode renders correctly — confirmed via screenshot, correctly condensed with footer pinned at bottom
- [x] 8.6 Verify the DRIFT resume variant renders correctly — confirmed via screenshot: custom subtitle, bio, and two-column skills block
- [x] 8.7 Verify the 100% Film resume variant renders correctly — confirmed: custom subtitle/bio swap, correctly mutually exclusive with DRIFT
- [x] 8.8 Verify the EAVE motivation statement variant renders correctly — confirmed via screenshot, plus the bonus EAVE-IPP applicant-notes page

**Significant finding during this section**: while testing 8.6/8.7, discovered that `index.html` was missing an entire pre-existing, non-audio feature set — the DRIFT/100% Film CV variant system, `{eave-ipp}` applicant-notes page, dev-only row filtering (`{dev}` tag), and one-page layout auto-fit improvements (`distributeOnePageSectionGap`, footer-aware `autoFitWorkScale`, `a4-page-1-body` wrapper). This was NOT caused by the audio removal — it was lost earlier in this session by an unrelated `git reset --hard` mistake (see design.md's "Incident on this change" note) that discarded an uncommitted pre-session edit to `index.html`. The full feature set was recovered intact from a dangling commit (`7eef32f`, still in the reflog) and merged in cleanly alongside this change's audio removal, since both touched overlapping code. Also found and removed one more leftover audio reference while there: a `typeof exitBeatStudio === 'function'` guard block in `setA4Mode()` that called removed beat-studio/hover-panel close functions.

## 9. Documentation cleanup

- [x] 9.1 Update `README.md` to remove or redirect any audio/beat-studio feature descriptions, pointing readers to the `beat-studio` repo where relevant — rewrote from 431 to ~90 lines: removed the Beat Studio, Ableton/M4L roadmap, and Audio system sections (~350 lines, all now living in `beat-studio`), added a pointer to that repo, updated Structure/Features/TODO to reflect the trimmed, audio-free site, and documented the DRIFT/100%Film/EAVE export variants recovered in section 8
- [x] 9.2 Leave `BEATSTUDIO.md` as historical record — left untouched; still documents work now living in `beat-studio`; flagged to user, pending their preference on deleting it later

## 10. Commit

- [x] 10.1 Review the full diff (`git diff --stat` and a manual read of `index.html`/`styles.css` changes) to confirm only intended files changed — confirmed: 50 files changed, 288 insertions(+), 17725 deletions(-); `index.html`/`styles.css`/`README.md` are the only non-deleted files touched, plus a cleaned-up `.design-review/` scratch folder removed before commit
- [x] 10.2 Commit with a clear message summarizing the removal and referencing this OpenSpec change
- [ ] 10.3 Push to `origin` (the `esteban-cuenca-site` repo) once the user confirms the local verification in section 8 passed
