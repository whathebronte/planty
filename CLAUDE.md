# Planty — Plant Watering Tracker

## Overview
Single-page plant watering tracker app built with vanilla HTML/CSS/JS and Firebase Firestore. Everything lives in `index.html` — no build step, no framework.

## Architecture
- **Single file**: All CSS, HTML, and JS are inline in `index.html`
- **Backend**: Firebase Firestore (client SDK, compat mode v10.12.2)
- **Auth**: UID-based via URL query param (`?id=<uuid>`), auto-generated if missing. UID is persisted in `localStorage` (`plantTracker_uid`) so returning visitors on the same browser are automatically redirected to their existing data. Visiting a different UID URL updates the stored value. A "Start fresh" link clears `localStorage` and generates a new UID.
- **Data model**: `users/{uid}/plants/{plantId}` with fields: `name`, `environment`, `lastWatered`, `customInterval`, `createdAt`
- **Plant database**: 50 hardcoded plants in `PLANT_DATABASE` array with default watering intervals for indoor/outdoor

## Key Patterns
- `render()` is the main re-render function — called after any state change, rebuilds the entire `#app` container innerHTML
- State is held in module-level variables (`plants`, `isLoading`, `expandedPlantId`, etc.)
- Firestore operations are async functions that update both Firestore and local state, then call `render()`
- The confirm dialog is shared between delete and water-all actions — `confirmDelete()` and `confirmWaterAll()` configure it dynamically
- Watering uses optimistic UI with an undo toast: writes to Firestore immediately, and on undo writes the previous value back (Option A). State: `undoToastTimeout`, `undoCallback`. Only one toast at a time — watering again while a toast is showing commits the previous action.

## UI Layout
- Compact list view with expandable detail rows (accordion pattern, one open at a time)
- "Water All" bulk action button at top of plant list
- Status communicated via left border color (green/yellow/red/gray)
- Water button on each row works without expanding
- Add plant modal with autocomplete from `PLANT_DATABASE`
- Undo toast appears at bottom of screen after watering (single or all), auto-dismisses after 5 seconds with countdown bar

## Styling
- Airbnb-inspired: white background, rounded corners, soft shadows, Inter font, teal-green accent (`#2D9F7C`)
- CSS custom properties in `:root` for theming
- Mobile-first, max-width 600px container
- No external CSS framework

## Development
- No build step — just open `index.html` in a browser
- Firebase config is hardcoded in the file
- No tests, no linting configured
