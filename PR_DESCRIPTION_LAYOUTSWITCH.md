# Add a layout toggle button

## What this does

Adds a per-layout toggle button (◀▶ / ▶◀) that switches the on-screen keyboard between a primary layout and a configured alternate one, separately for landscape and portrait. Two new dropdowns in Preferences set the alternate layout per orientation, plus a switch to enable the button.

## Implementation notes

- `toggleLayoutMode()` flips two new internal-only keys, `layout-toggled-landscape` and `layout-toggled-portrait`.
- `buildUI()` reads the base layout from settings and swaps in the alt layout only when the toggled flag is set.
- Custom layout preview thumbnails in `prefs.js` render the toggle button box too, matching the real keyboard.

## Files changed

- `extension.js`: `toggleLayoutMode()`, `buildUI()` (both layout branches)
- `prefs.js`: alt-layout dropdowns, enable switch, preview mock
- `schemas/org.gnome.shell.extensions.gjsosk.gschema.xml`: 5 new keys
- `physicalLayouts.json`: `layoutToggle: true` on all built-in layouts
- `stylesheet.css`: `.layout_toggle_btn` styling

## Testing

- [x] Toggle button switches between primary and alt layout, both orientations
- [x] Toggling back returns to the exact layout you started from
- [x] Preferences dropdown for your chosen layout stays put after pressing toggle
- [x] Close and layout-toggle buttons keep the same screen position across a toggle
- [x] Custom layout preview in prefs shows the toggle box when `layoutToggle` is set
