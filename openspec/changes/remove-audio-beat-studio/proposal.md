## Why

The portfolio site (`esteban-cuenca-site`) currently ships an entire Web Audio engine, hover-triggered musical tones on CV table rows, a navbar waveform indicator, and a 3D beat-studio instrument — none of which belong on a professional producer portfolio. This code and its matching musical/creative direction is being extracted into a separate `beat-studio` repository (already duplicated and pushed) for independent development. The portfolio repo needs to be trimmed down to a clean, professional site with zero traces of the audio/music system, while every piece of actual portfolio content — the CV, its data-driven tables, dark mode, and every printable/export variant (A4 print, one-page mode, DRIFT resume, 100% Film resume, EAVE motivation statement) — must keep working exactly as it does today.

## What Changes

- **BREAKING**: Remove the entire Web Audio hover-synth system (CV row hover → musical tone playback), including its settings drawer ("Table hover" panel) and toolbar toggle button.
- **BREAKING**: Remove the 3D beat-studio instrument entirely: its UI panel, its 3D/2D workspace markup, its toolbar "Studio" button, and the Three.js dependency it pulls in.
- **BREAKING**: Remove the navbar waveform indicator (`#nav-vibe` pulse line) and its controlling script.
- Delete all now-unused JS under `js/audio/`, `js/hover/`, `js/studio/`, and `js/vendor/three*.js` (and any Three.js addons used only by beat-studio), plus `studio.css`.
- Remove all `<script>` tags and the `studio.css` `<link>` that load the above from `index.html`.
- Remove the audio-coupled logic inside the CV render pipeline: `ECAudio.MIDI_BANK_SLUGS` bank-highlighting, the `midi-bank`/`data-bank` markup on CV sections, and every post-render call into `ECAudio`/`NavVibe`/`TrackSeq`/`initInfluenceZones`/`syncHoverPanelUI`/`syncMusicSequencer`.
- Preserve, unchanged in behavior: CV markdown parsing and table rendering, section show/hide, dark mode toggle, scroll reveal, A4/print export (one-page toggle, DRIFT, 100% Film, EAVE variants), footer/export buttons, and all portfolio content and images.
- Update `README.md`/any docs that reference the audio/beat-studio features as if they still lived in this repo (point to the new `beat-studio` repo instead where relevant).

## Capabilities

### New Capabilities
- `portfolio-site`: Defines the required behavior of the trimmed, audio-free professional portfolio — CV rendering from `content.md`, section visibility controls, dark mode, and every print/export mode — with an explicit requirement that no audio, hover-tone, waveform, or beat-studio functionality exists anywhere in the shipped page.

### Modified Capabilities
(none — no pre-existing spec covers this repo yet; the audio/beat-studio system is being deleted wholesale rather than having its requirements changed, so no delta spec is written for it)

## Impact

- **Files deleted**: `js/audio/*` (16 files), `js/hover/*` (6 files), `js/studio/*` (18 files), `js/vendor/three.min.js`, `js/vendor/three.module.min.js`, `js/vendor/addons/**` (if only used by studio), `studio.css`.
- **Files edited**: `index.html` (remove ~24 `<script>`/`<link>` tags, the `#nav-vibe` markup, the `hover-panel`/`studio-panel` asides, the `#beat-studio` workspace div, the `Studio`/hover-settings toolbar buttons, and the `ECAudio`/`NavVibe`/`TrackSeq` calls inside the inline render script), `styles.css` (remove `#nav-vibe`/`.nav-vibe-*`/`.audio-drawer` and other audio-only selectors, keeping every CV/print/layout rule).
- **Unaffected**: `content.md`, `print.css`, `fonts.css`, all portfolio images, CV data, all export/print JS logic inside `index.html`'s inline script, `CNAME`, `robots.txt`, `sitemap.xml`, `privacy.html`.
- **No server/backend impact** — this is a static site; removal is purely front-end code deletion.
