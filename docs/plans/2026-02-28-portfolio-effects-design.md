# Portfolio Effects & Project Grid Design

**Date:** 2026-02-28
**Status:** Approved

## Summary

Enhance the portfolio with two visual effects and a restructured projects section. All changes are vanilla JS/CSS — no framework migration.

## Changes

### 1. Scramble Text on Headings
All `h2` section headings scramble random characters and resolve to the real text when scrolled into view. Uses the existing `IntersectionObserver` pattern already in the site.

- Characters cycle through a set of random alphanumerics during scramble (~600ms)
- Resolves letter-by-letter from left to right
- Fits the monospace font and hacker/tech aesthetic

### 2. Project Cards → Responsive Grid
Replace the vertical list in `#projects` with a CSS grid of cards.

- 2–3 columns on desktop, 1 on mobile
- Each card shows: project image (or placeholder), title, brief description (1–2 sentences), skill tags
- Full bullet-point details move to the side panel

### 3. 3D Tilt on Project Cards
Cards tilt in 3D based on mouse position on hover.

- Max tilt: ~10–15 degrees
- Smooth spring-like return on mouse leave
- Subtle glare/shine layer that tracks the cursor

### 4. Side Panel (Slide-in Project Detail)
Clicking a card slides in a panel from the right (~45% viewport width on desktop, full-width on mobile).

- Overlay backdrop dims the rest of the page
- Panel contains: full project title, date, team size, full description, bullet highlights, skill tags, links
- Close via × button, backdrop click, or Escape key
- Smooth CSS transition (transform: translateX)

## Files to Modify
- `index.html` — restructure projects section, add panel markup, add overlay
- `global.css` — grid layout, card styles, tilt styles, panel styles, scramble animation
- Inline `<script>` in index.html — scramble text logic, tilt logic, panel open/close logic

## Out of Scope
- No React migration
- No new HTML pages per project
- No changes to experience, about, or contact sections
