# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the site

No build system. Open any `.html` file directly in a browser, or serve the directory with any static server:

```
npx serve .
# or
python -m http.server
```

## Architecture

Static personal website — no frameworks, no bundlers, no compilation.

**Page types:**

1. **`index.html` (home)** — full-screen two-panel layout. Left panel (45%) contains the Mario-styled `hello` word, navigation menu, and contacts. Right panel (55%) contains scattered draggable images. A hand-drawn SVG divider animates between them. Styled exclusively by `css/menu.css`. Menu items link via `data-href` (e.g. `intro.html`, `old.html`).

2. **Bootstrap card pages** (`projects.html`, `fitness.html`, `music.html`, `intro.html`, `old.html`) — Bootstrap 5 (CDN) card grids / content, styled by `css/style.css`. `old.html` is the previous design, still linked from the home menu as "Old Website".

3. **`games.html`** — fully self-contained PS5-style home-screen UI. All CSS and JS live inline in the file (a `<style>` block and a script); it does **not** use Bootstrap or `css/style.css`. Background crossfade slideshow, focusable game tiles, keyboard/pointer navigation.

Note the "Introduction" menu item: clicking it opens a draggable *intro window* rendered inline in `index.html` (not the page navigation); `intro.html` is a separate standalone page.

**CSS split:**
- `css/menu.css` — everything for `index.html`: fixed panel layout, SuperMario256 font, menu item size cascade (`data-pos` attribute drives font-size/opacity), scattered image positioning, lightbox, pencil divider
- `css/style.css` — Bootstrap card pages (`.listofprojects`, `.listoffitness`), responsive breakpoints
- `games.html` carries its own inline styles; there is no shared JS file — every page's scripts are inline

**Key `index.html` interactions (all vanilla JS inline at bottom of file):**
- **Menu cascade** — `data-pos` attribute on each `<li>` drives CSS font-size/opacity; active item gets red drop-shadow
- **Hello letter flip** — each `.hello-letter` toggles `.flipped` on hover for a 3D `rotateY` flip (front shows "hello", back shows "yoooo")
- **Intro window** — opens on "Introduction" click; draggable, minimize/maximize/close; flying text animates from menu item to inside the window; uses `history.pushState` so browser back button closes it
- **DVD screensaver** — opens on middle menu item clicks; bouncing logo with hue rotation on wall hits
- **Scatter images** — absolute-positioned in 5 rows; mouse drag + touch long-press to move; single click/tap opens lightbox
- **Animated divider** — SVG path redrawn each frame via `requestAnimationFrame` with sine-wave x displacement; two layers (faint + dark) give pencil texture via `feTurbulence` SVG filter

**Fonts:**
- `SuperMario256.ttf` (local, `fonts/`) — used for the "hello" word only
- Montserrat (Google Fonts CDN) — used for menu items, intro window labels, index.html body
- Inter (Google Fonts CDN) — used on sub-pages
