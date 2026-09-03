## Purpose

Defines the visual and UX quality standards the portfolio site must meet — clean design tokens, a consistent icon system, responsive imagery, and a mobile layout that surfaces key content without unnecessary scrolling — independent of any specific color or type choice.

## ADDED Requirements

### Requirement: No unused design tokens
The stylesheet SHALL NOT define CSS custom properties that are not referenced by any selector in the shipped codebase.

#### Scenario: Dead token audit
- **WHEN** every CSS custom property defined in `:root` or a theme override is grepped against the rest of the codebase
- **THEN** every defined token has at least one consuming usage outside its own definition

### Requirement: Consistent icon rendering
Every icon-like control on the site SHALL be rendered the same way (as inline SVG), not as a mix of Unicode text glyphs and SVG markup, so appearance does not depend on the visitor's font/OS glyph coverage.

#### Scenario: Dark mode toggle renders as a drawn icon
- **WHEN** the toolbar renders the dark-mode toggle control
- **THEN** it is an inline SVG icon consistent in style with any other icon on the page, not a Unicode character relying on font glyph support

### Requirement: Responsive hero image delivery
The hero portrait SHALL be served at a resolution appropriate to the viewport, not the same full-resolution file on every device.

#### Scenario: Mobile viewport requests a smaller image
- **WHEN** the site loads on a narrow (mobile-width) viewport
- **THEN** the browser requests an image variant sized for that viewport rather than the full desktop-resolution file

### Requirement: Key content reachable without a full scroll on mobile
On a mobile viewport, a visitor SHALL be able to see the site owner's name and title without scrolling past a full screen of unrelated content first.

#### Scenario: Mobile first view
- **WHEN** the site loads on a mobile-width viewport
- **THEN** the name and title (or an equivalent identifying element) are visible in the initial viewport, or reachable within a small fraction of a screen's scroll — not hidden behind a full-viewport-height image alone

### Requirement: Long-form table cell content stays readable on mobile
On the mobile stacked-card table layout, a cell containing a long list of comma-separated items (e.g. festivals, awards) SHALL remain scannable rather than rendering as an undifferentiated dense paragraph spanning multiple screens.

#### Scenario: A row with 15+ festival credits on mobile
- **WHEN** a CV table row with a long festival or awards list renders on a mobile-width viewport
- **THEN** the list is presented in a way a visitor can scan without losing track of where one row's content ends and the next begins (e.g. improved line spacing, item separation, or a truncate/expand affordance)
