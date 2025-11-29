# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

App Store Screenshot Generator - a browser-based tool for creating App Store marketing screenshots. Built with vanilla JavaScript, HTML5 Canvas, and CSS. No build process or dependencies.

## Development

To run locally, serve via a web server (required for IndexedDB persistence):

```bash
python3 -m http.server 8000
# or
npx serve .
```

Open `http://localhost:8000` in browser. Opening `index.html` directly from filesystem will break persistence.

## Architecture

**Single-page app with three files:**

- `index.html` - UI structure with modals for settings, about, project management, translations, and language selection
- `styles.css` - Dark theme styling, responsive layout with CSS Grid (3-column: left sidebar, canvas, right sidebar)
- `app.js` - All application logic (~2300 lines)

**Key patterns in app.js:**

- `state` object at top holds all application state (screenshots, settings, text, background config)
- `updateCanvas()` is the main render function - call after any state change
- `saveState()` persists to IndexedDB, called automatically in `updateCanvas()`
- `syncUIWithState()` updates all UI controls to reflect current state
- Project management uses IndexedDB with two stores: `projects` (data) and `meta` (project list)

**Canvas rendering pipeline (in updateCanvas):**
1. `drawBackground()` - gradient/solid/image with optional blur and overlay
2. `drawScreenshot()` - positioned, scaled, rotated screenshot with shadow
3. `drawText()` - headline and subheadline with multi-language support
4. `drawNoise()` - optional noise texture overlay

**Multi-language text:**
- `state.text.headlines` and `state.text.subheadlines` are objects keyed by language code
- `getTextSettings()` returns either global or per-screenshot text depending on toggle state
- AI translation calls Claude API directly from browser (requires API key in settings)

## Key Functions

- `createProject()` / `deleteProject()` / `switchProject()` - async, must await and call `updateProjectSelector()` after
- `handleFiles()` - processes uploaded images into screenshot array
- `exportCurrent()` / `exportAll()` - generates PNG downloads from canvas
- `applyPositionPreset()` - applies preset screenshot positioning (centered, bleed, tilt, etc.)
