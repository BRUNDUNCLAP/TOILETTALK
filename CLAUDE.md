# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

"TOILET TALK" — a single-page interactive portfolio site built with **vanilla HTML, CSS, and JavaScript**. No frameworks, no build tools, no package manager. The entire application lives in `index.html`.

## Development

Open `index.html` directly in a browser to develop. No build step or dev server required.

```bash
open index.html
```

There are no tests, linters, or CI pipelines.

## Architecture

Everything is in one self-contained HTML file with three inline sections:

- **`<style>`** — All CSS including card layout, drag states, overlay modal, giant bottom headline, responsive breakpoints, and scrollbar styling
- **`<body>`** — Static markup for the canvas container, overlay modal structure, scatter/align buttons, bottom gradient, and the "LETS TALK TOILETS" headline element
- **`<script>`** — All JS: color palette constants, card data array, DOM card builder, pointer-event drag system, resize handles, overlay expand/close logic, headline auto-fit, card headline auto-fit, gradient sizing, and dynamic scroll padding

### Key Systems

**Card Data Array (`CARDS`)** — Each card is an object with headline, body, detail (expanded text), color scheme, dimensions (w/h), position (x/y), and font sizes. Card sizes are computed from `window.innerWidth`/`innerHeight` at load time for dramatic size variation.

**Drag System** — Uses `pointerdown`/`pointermove`/`pointerup` on the canvas container with `setPointerCapture`. Z-index increments on grab to bring cards to front. Disabled on tablet/mobile.

**Resize System** — Cards have a resize handle (bottom-right corner) for freeform resizing on desktop. Disabled on tablet/mobile.

**Expand Overlay** — Full-viewport takeover (`100vw × 100vh`) triggered by the expand button on each card. Shows headline (with preserved line breaks), body, and detail text. Closes via backdrop click, X button, or Escape key. The divider line between body and detail uses the card's text color at 30% opacity (hex-to-rgba conversion). On tablet/mobile, CSS rules for hiding card body text and collapsing headline whitespace are scoped to `#canvas` so the overlay content is unaffected.

**Headline Fit** — Binary search algorithm sizes "LETS TALK TOILETS" to fill the window width exactly. On tablet/mobile, each line ("LETS TALK" and "TOILETS") is fit independently. Runs after `document.fonts.ready` and on resize.

**Card Headline Auto-Fit (`fitCardHeadlines()`)** — On tablet/mobile, collapsed card headlines are forced to a single line (newlines replaced with spaces, `white-space: nowrap`). A binary search algorithm (8–48px range) finds the largest font size that fits within the available card width minus the expand button. On desktop, original multi-line headlines and font sizes are restored. Runs after `document.fonts.ready`, on resize, and on media query changes.

**Scatter/Align Buttons** — Fixed-position buttons (bottom-right) that randomize card positions/sizes or align them in a uniform horizontal row. Desktop only.

### Responsive Layout

Three breakpoints:

- **Desktop (>1024px)** — Cards are absolutely positioned and draggable/resizable. Headline renders as a single line. Expand button is absolutely positioned (bottom-right of card).
- **Tablet (≤1024px)** — Cards stack vertically in a scrollable column (107px tall each). Cards use `flex-direction: row` with the headline content taking `flex: 1` and the expand button as a `flex-shrink: 0` element on the right (no overlap). Drag/resize disabled. Headline splits into two lines ("LETS TALK" / "TOILETS"). Cards use `z-index: 0` to stay behind the headline and gradient.
- **Mobile (≤600px)** — Same as tablet but with tighter padding and smaller cards (87px tall).

### Bottom Gradient/Fill

On tablet/mobile, a fixed `#headline-gradient` element covers the bottom of the viewport behind "LETS TALK TOILETS". It uses a solid `#0A0807` fill with a subtle transparent-to-solid feather at the top 12%. Its height is dynamically set to 1.15× the headline height + 15px via `sizeGradient()`.

### Dynamic Scroll Padding

`updateCanvasPadding()` dynamically calculates the `#canvas` bottom padding on tablet/mobile so the last card can scroll just above the top edge of the gradient fill. Runs on font load, resize, and media query changes.

## Color Palette

Reference image at `Color Palette/Colors.jpg`. The palette is defined in the `COLORS` object — each entry has `bg`, `text`, and `textSecondary` properties. Colors: Smoky Black (`#100C0B`), Eerie Light (`#282723`), Eerie Black (`#1C1B17`), Hot Orange (`#Eb4604`), Moss (`#99A57D`), Orange Wheel (`#F77E0D`), Night (`#171614`), White (`#FFFFFF`).

## Fonts

Two Google Fonts loaded via CDN:
- **Big Shoulders Display** (700, 800, 900) — giant bottom headline and card headlines
- **Inter** (400, 500) — body copy

## Deployment

- **GitHub Pages:** `https://brundunclap.github.io/TOILETTALK/` — deploys from `main` branch automatically.
- **Vercel (production):** `https://letstalktoilets.com/` — connected to the `BRUNDUNCLAP/TOILETTALK` GitHub repo, auto-deploys on push to `main`.

## Repository

GitHub: `BRUNDUNCLAP/TOILETTALK` — push requires a Personal Access Token (no SSH keys configured). Token is not stored in git config; must be provided for each push or embedded temporarily in the remote URL.
