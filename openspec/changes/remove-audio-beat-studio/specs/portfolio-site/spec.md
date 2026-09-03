## Purpose

Defines the required behavior of the professional portfolio site after all audio, hover-tone, waveform, and beat-studio functionality is removed — covering CV rendering, section visibility, dark mode, and every print/export mode, plus an explicit guarantee that no audio system remains.

## ADDED Requirements

### Requirement: No audio or music functionality
The site SHALL contain no Web Audio API usage, no audio-triggering event listeners, and no beat-studio instrument. Hovering, clicking, or otherwise interacting with any CV table row, cell, or link SHALL NOT produce sound or musical tones.

#### Scenario: Hovering a CV table row produces no sound
- **WHEN** a visitor moves the mouse over any row of a CV table (e.g. a film credit or commercial project row)
- **THEN** no tone, note, or audio of any kind is played, and no `AudioContext` is created as a result

#### Scenario: No beat-studio UI is reachable
- **WHEN** a visitor loads the site and inspects the toolbar and page
- **THEN** there is no "Studio" button, no beat-studio 3D/2D workspace, and no way to open a beat-making interface

#### Scenario: No navbar waveform indicator
- **WHEN** a visitor loads the site
- **THEN** the toolbar does not render a pulsing waveform/vibe line, and no script drives such an indicator

### Requirement: CV content renders unchanged
The site SHALL continue to parse `content.md` and render the visitor's name, subtitle, bio, and all CV sections (with their tables and paragraphs) exactly as before the audio system's removal, with no residual audio-only markup (e.g. bank-highlight classes tied to the removed audio engine) or broken references.

#### Scenario: CV sections render from content.md
- **WHEN** the page loads
- **THEN** all sections defined in `content.md` appear in order, each with its table(s) and/or paragraphs correctly formatted

#### Scenario: Section hide/show still works
- **WHEN** a visitor clicks a section's "Hide" (or "Show") toggle
- **THEN** the section's visibility toggles and the choice persists across reloads via local storage, exactly as before

#### Scenario: No leftover audio hooks in rendering
- **WHEN** the CV render pipeline runs
- **THEN** it makes no reference to any removed audio object, function, or global (e.g. no calls tied to the removed audio engine, hover-tone system, or beat-studio, and no data attributes whose only purpose was to drive the removed audio system)

### Requirement: Dark mode toggle is preserved
The site SHALL preserve the dark/light mode toggle button and its local-storage persistence, unaffected by the audio system's removal.

#### Scenario: Toggling dark mode
- **WHEN** a visitor clicks the dark-mode toggle button
- **THEN** the page switches themes immediately and the preference persists across reloads

### Requirement: Print and export modes are preserved
The site SHALL preserve every existing print/export mode — full multi-page print, one-page mode, the DRIFT resume variant, the 100% Film resume variant, and the EAVE motivation statement variant — producing the same layout and content as before the audio system's removal.

#### Scenario: Standard print/export
- **WHEN** a visitor triggers the export/print flow without selecting a variant
- **THEN** the site renders the standard multi-page A4 CV for printing, identical in content and layout to before this change

#### Scenario: One-page mode
- **WHEN** a visitor switches to one-page mode during export
- **THEN** the CV renders as a single condensed A4 page as before

#### Scenario: DRIFT / 100% Film / EAVE variants
- **WHEN** a visitor selects the DRIFT, 100% Film, or EAVE export variant
- **THEN** the corresponding standalone document renders with its existing content, unaffected by the removal of the audio system

### Requirement: No dead references to removed code
The shipped `index.html`, `styles.css`, and any other served asset SHALL NOT reference deleted files (scripts, stylesheets, or vendored libraries that supported the audio/beat-studio system), and loading the page SHALL produce no console errors caused by missing audio-related files or undefined audio-related globals.

#### Scenario: Clean page load
- **WHEN** the site is loaded in a browser with developer tools open
- **THEN** the network panel shows no 404s for removed audio/studio assets, and the console shows no reference errors for removed audio globals or functions
