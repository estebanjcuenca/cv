## Purpose

Defines the visual and UX quality standards the portfolio site must meet — clean design tokens, a consistent icon system, responsive imagery, and a mobile layout that surfaces key content without unnecessary scrolling — independent of any specific color or type choice.

## ADDED Requirements

### Requirement: No unused font weights
The site SHALL NOT embed a font weight or style that no rule in the shipped CSS ever sets.

#### Scenario: Font weight audit
- **WHEN** every `font-weight` value used across the stylesheets is compared against the weights declared via `@font-face`
- **THEN** every declared `@font-face` weight/style combination has at least one consuming `font-weight` (and, for italics, `font-style: italic`) usage elsewhere in the codebase

### Requirement: Deliberate type hierarchy
Headings and emphasized labels SHALL be visually distinguished from body text by weight, not only by size, using the weights the site actually ships.

#### Scenario: Name heading vs. body paragraph
- **WHEN** the site owner's name (`#cv-name`) and a body paragraph render side by side
- **THEN** the name uses a heavier font weight than the body paragraph, in addition to its larger size

### Requirement: Project records with long lists stay proportionate
A CV table row (project, engagement, or entry) SHALL NOT render with wildly uneven internal spacing where one field's long wrapped content stretches the row height while sibling fields in the same row sit mostly empty.

#### Scenario: A project with a long festival or award list
- **WHEN** a project entry has a festivals or awards field containing many comma-separated items
- **THEN** that field's text wraps using the full available row width (not a narrow fixed column), and fields with short or empty content in the same entry do not carry large amounts of unused vertical space as a result

#### Scenario: A project with no content in an optional field
- **WHEN** a project entry has no value for an optional field (e.g. no awards, no support)
- **THEN** no empty labeled block renders for that field

### Requirement: Table redesign doesn't affect print/export output
The print and export CV layouts (standard, one-page, DRIFT, 100% Film, EAVE) SHALL continue to render as classic tabular columns, unaffected by the screen-view record layout change.

#### Scenario: Exporting after the table redesign
- **WHEN** any CV export variant is generated after the screen table layout changes
- **THEN** the exported/printed output looks the same as it did before the change — a conventional multi-column table, not the screen's stacked record layout
