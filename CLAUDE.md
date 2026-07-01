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

1. **`index.html` (home)** — full-screen two-panel layout. Left panel (45%) contains the Mario-styled `hello` word, navigation menu, and contacts. Right panel (55%) contains scattered draggable images. A hand-drawn SVG divider animates between them. Styled exclusively by `css/menu.css`. Each `<li class="menu-item">` carries a `data-href`, but a `data-href` alone does **not** mean the item navigates there — see the menu dispatch note below.

2. **Bootstrap card pages** (`projects.html`, `fitness.html`, `music.html`, `intro.html`, `old.html`) — Bootstrap 5 (CDN) card grids / content, styled by `css/style.css`. `old.html` is the previous design, reached via the "Old Website" home-menu item, and is the **only** thing that links to `projects.html` / `fitness.html` / `music.html` (they are otherwise orphaned — the home menu no longer routes to them; see below).

3. **`games.html`** — fully self-contained PS5-style home-screen UI. All CSS and JS live inline in the file (a `<style>` block and a script); it does **not** use Bootstrap or `css/style.css`. Background crossfade slideshow, focusable game tiles, keyboard/pointer navigation.

**Home-menu dispatch (important):** the click/`Enter` handlers in `index.html` do *not* uniformly follow `data-href`. They branch by item:
- **Introduction** (first item) → opens the inline draggable *intro window*, not navigation (`intro.html` is a separate standalone page that the menu never opens).
- **Old Website** (last item), **Games**, and any `data-href` starting with `http` (currently **Music I Like** → `https://music-seven-olive.vercel.app/`) → real navigation via `go()`.
- Everything else (**Projects**, **Fitness**) → opens the **DVD screensaver**; their `data-href` is never used for navigation.

So when adding/removing menu items, update the index-based branching in both the `keydown` and `click` handlers — the dispatch keys off first/last index and specific `data-href` values, not a generic loop.

**CSS split:**
- `css/menu.css` — everything for `index.html`: fixed panel layout, SuperMario256 font, menu item size cascade (`data-pos` attribute drives font-size/opacity), scattered image positioning, lightbox, pencil divider
- `css/style.css` — Bootstrap card pages (`.listofprojects`, `.listoffitness`), responsive breakpoints
- `games.html` carries its own inline styles; there is no shared JS file — every page's scripts are inline

**Key `index.html` interactions (all vanilla JS inline at bottom of file):**
- **Menu cascade** — `data-pos` attribute on each `<li>` drives CSS font-size/opacity; active item gets red drop-shadow
- **Hello letter flip** — each `.hello-letter` toggles `.flipped` on hover for a 3D `rotateY` flip (front shows "hello", back shows "yoooo")
- **Intro window** — opens on "Introduction" click; draggable, minimize/maximize/close; flying text animates from menu item to inside the window; uses `history.pushState` so browser back button closes it
- **DVD screensaver** — opens when a middle menu item (Projects, Fitness) is clicked/entered; bouncing logo with hue rotation on wall hits
- **Scatter images** — absolute-positioned in 5 rows; mouse drag + touch long-press to move; single click/tap opens lightbox
- **Animated divider** — SVG path redrawn each frame via `requestAnimationFrame` with sine-wave x displacement; two layers (faint + dark) give pencil texture via `feTurbulence` SVG filter

**Fonts:**
- `SuperMario256.ttf` (local, `fonts/`) — used for the "hello" word only
- Montserrat (Google Fonts CDN) — used for menu items, intro window labels, index.html body
- Inter (Google Fonts CDN) — used on sub-pages

**Assets & tooling:**
- `images/` holds all local media; many scatter images and the Pokémon sprites in `index.html` are hot-linked from external CDNs (`play.pokemonshowdown.com`), so they need network access to render.
- `.tools/` (git-ignored) contains a local **ffmpeg** build used to author the `games.html` title-screen slideshow/loop clips. Not needed to run the site; don't commit it.
- `.gitignore` also excludes `.claude/` and `.env` files — keep secrets and local Claude settings out of commits.
