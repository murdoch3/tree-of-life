# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A static, single-page Kabbalistic Tree of Life study tool. No build system, no framework, no dependencies — just `index.html`, `data.json`, and `imgs/`.

## Running locally

`data.json` is loaded via `fetch()`, so the page must be served over HTTP (not opened as a file):

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

## Architecture

**Everything lives in `index.html`** — CSS, JavaScript, and HTML are all in one file. There is no bundler or separate JS/CSS files.

**`data.json`** holds two top-level arrays:
- `sephiroth` — each entry has `id`, `n` (number 1–10), `x`/`y` (SVG coords), `label`, `title`, and either `fields[]` (structured key/value pairs) or `text` (raw HTML string).
- `paths` — each entry has `n` (11–32), `from`/`to` (sephirah ids), `letter` (Hebrew letter name), `heb` (Hebrew character), and the same `fields`/`text` content shape.

**SVG rendering** (`render()` in `index.html`) builds the tree diagram entirely from DOM APIs using the SVG namespace. Sephiroth are circles; paths are `<g class="path-group">` elements containing two line segments split around a centred text label. The `SCALE_Y` variable (controlled by the slider) stretches the tree vertically without re-fetching data.

**Info panel** (`showInfo()`) populates `#info-content` from the clicked sephirah or path. `renderContent()` chooses between `fields[]`-based structured rendering and raw `item.text` HTML fallback.

**Mobile layout** (≤700px): the `#app` div becomes 200vw wide; clicking a node adds `.show-info` which `translateX(-100vw)` slides the right panel into view. The back button calls `mobileBack()` to remove that class.

## Adding content

To add or update sephirah/path descriptions, edit `data.json`. Use `fields` (array of `{label, value}`) for structured data or `text` for arbitrary HTML. The `value` strings in existing entries start with a leading space — preserve that convention.

Images in `imgs/` are Thoth Tarot card scans; they are referenced in `data.json` field values, not hard-coded in `index.html`.
