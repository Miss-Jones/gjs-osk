# Fix blank key labels and keyboard position resetting

Two unrelated bugs, one branch. They're independent, so say the word if you'd rather have them as separate PRs.

## Blank key labels

`updateKeyLabels()` set each key's label straight from `key.char.layers[layer]`. Toggle a modifier combo a key has no glyph for (Alt, Shift+Alt, a Num/Caps-lock layer) and that lookup returns `undefined`, so the key goes blank and stays blank until you switch back to a layer it defines.

Now it falls back to the `default` layer when the active one is missing, so the key keeps showing something. Icon keys still render null on every layer, same as before.

## Keyboard position snapping back

The dragged position was never saved. It lived only as a translation on the actor, and every open, close, and refresh recomputed the position from the `default-snap` setting. So focus changes, settings edits, language switches, and monitor changes all pulled the keyboard back to its default slot.

The dropped position now persists to a new `saved-position` setting and gets resolved through one `computeRestPosition()` helper, clamped into the current monitor so a stale value can't strand it off-screen. Writing `saved-position` is excluded from the settings-changed rebuild, since saving would otherwise trigger the exact reset it's meant to prevent. Changing `default-snap` clears it.

Dismissing the keyboard on purpose (the close key or the panel toggle) forgets the saved spot so it returns to the default slot. Auto-close on focus loss keeps it.

## Also

Set `this.extensionObject` in the `Keyboard` constructor. `buildUI()` already referenced it (`this.extensionObject.fail(...)`) but nothing assigned it, so a custom-layout parse error would have thrown instead of reporting.

## Settings

One new key: `saved-position` (string, default empty). No UI, it's written by dragging.

## Testing

- Drag the keyboard around and confirm it stays put through focus changes, a language switch, and opening/closing Preferences.
- Hit the close key and the panel toggle, and confirm it returns to the default slot on the next open.
- Toggle Shift, Alt, and Num-lock and watch the labels stay populated.

Tested on GNOME 50.4 (Wayland).
