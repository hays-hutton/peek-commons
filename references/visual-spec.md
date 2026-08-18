# peek — Visual Design Specification

This document is the complete visual contract for peek implementations. The
`visual-defaults` constraint in `commonsformat.md` binds an implementation to the defaults
defined here; every value maps to a key in the user configuration file and is a default, not
a hard-coded absolute.

## Design principles

1. **Companion, not destination.** peek sits beside an editor. It should feel like a natural
   extension of the workspace, not a separate application demanding attention.
2. **Warmth without softness.** Calm and comfortable for long reading sessions, with the
   precision expected of technical documentation.
3. **One good answer.** No dark mode, no theme picker. One carefully considered design.

## Typography

The [IBM Plex](https://www.ibm.com/plex/) family provides both prose and code faces. They
were designed together, sharing proportions and stroke weights, which creates seamless
transitions between paragraph text and inline code. Implementations should embed the fonts
(IBM Plex is OFL-licensed) or fall back gracefully.

| Role  | Font           | Fallbacks        |
|-------|----------------|------------------|
| Prose | IBM Plex Serif | Georgia, serif   |
| Code  | IBM Plex Mono  | Menlo, monospace |

### Scale and rhythm

| Element     | Size | Line height |
|-------------|------|-------------|
| Body text   | 17px | 1.75        |
| Code blocks | 14px | 1.6         |
| H1          | 28px | 1.3         |
| H2          | 22px | 1.3         |
| H3          | 18px | 1.3         |

The generous body line-height (1.75) creates vertical breathing room that makes long
documents easier to scan.

## Color

### Background and surface

| Name            | Hex       | Usage                    |
|-----------------|-----------|--------------------------|
| Background      | `#faf8f5` | Page background          |
| Code background | `#e8e2d9` | Code blocks, inline code |

The warm cream background reduces eye strain compared to pure white. Code blocks use a
slightly darker warm tone to create distinction without the jarring light-to-dark shift of
typical dark code themes.

### Text

| Name    | Hex       | Usage                        |
|---------|-----------|------------------------------|
| Body    | `#3d3835` | Primary text                 |
| Heading | `#2a2622` | H1, H2, H3, strong           |
| Muted   | `#6a635d` | Secondary text, blockquotes  |
| Faint   | `#8a837a` | Tertiary text, comments      |

### Accent

| Name         | Hex       | Usage                       |
|--------------|-----------|-----------------------------|
| Accent       | `#4a6a7a` | Links                       |
| Accent light | `#8aaaba` | Blockquote borders          |
| Accent faint | `#c8d0d4` | Horizontal rules, dividers  |

The slate blue accent provides cool contrast against the warm background. Use it sparingly:
links, blockquote borders, and horizontal rules only.

### Syntax highlighting

Code uses a muted, earthy palette that remains readable without demanding attention:

| Element   | Hex       |
|-----------|-----------|
| Keywords  | `#7a4a30` |
| Functions | `#3a6040` |
| Strings   | `#6a5030` |
| Numbers   | `#7a5010` |
| Comments  | `#8a837a` |

## Layout

### Content width

Content is constrained to **720px** maximum width, centered in the viewport. This gives a
comfortable reading measure of roughly 65-75 characters per line. When the window is narrower
than 720px (for example, tiled beside an editor), content fills the available width with
**20px** horizontal padding.

### Code block behavior

Code blocks respect the same content width as prose. Long lines trigger **horizontal
scrolling inside the block** rather than breaking the layout or wrapping.

### Spacing

| Element          | Margin                 |
|------------------|------------------------|
| Paragraphs       | 16px bottom            |
| H2               | 48px top, 16px bottom  |
| H3               | 32px top, 12px bottom  |
| Code blocks      | 16px bottom            |
| Horizontal rules | 32px top and bottom    |

## Interaction affordances

- Code blocks show a **copy button** in the top-right corner on hover; clicking copies the
  block text and swaps the button for a checkmark for roughly a second.
- Anchor links (`#heading`) **smooth-scroll** to the target heading within the document.
- Text selection and copying work naturally throughout the document.

## Non-goals

- **Dark mode.** One good theme, fully considered.
- **Theme customization UI.** Edit the config file.
- **Print styles.** peek is a viewer, not a publisher.
- **Responsive breakpoints.** Content reflows naturally; no special narrow-window treatment
  beyond the padding rule above.
