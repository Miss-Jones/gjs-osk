# Add auto-capitalize

Optional sentence-case help, off by default, behind two independent switches in Preferences.

**Auto-capitalize** (`auto-capitalize`): after a sentence terminator (`.`, `!`, `?`, `:`) followed by a space, or after Enter, the next letter comes out capital.

**Capitalize first letter** (`capitalize-first-letter`): the first letter you type after a text field gets focus comes out capital.

Both reuse the on-screen Shift, which already clears itself after one letter, so the capital is a one-shot: type the letter and Shift releases on its own. While Shift is armed you get the usual feedback, the key highlights and the labels switch to uppercase, exactly as if you'd tapped it.

## Details

The terminator case waits for the following space on purpose. That keeps `e.g.` and `3.14` from being read as sentence ends. Arming is skipped when Caps-lock or Shift is already engaged, or when the layout has no Shift key.

`releaseAllKeys()` (called on every close) now releases any modifier the virtual device is still holding. Without it, arming Shift on focus and then closing without typing, which happens every time the keyboard auto-hides on focus loss, would leave Shift pressed and leak into the physical keyboard. This also covers a manually-tapped Shift left unused. The release is guarded because the constructor calls `close()` before that state exists.

## Limitation

The keyboard can't see whether the focused field is empty or where the cursor sits, so "first letter on focus" really means "first letter after the keyboard opens." Tap into the middle of existing text and that next letter capitalizes too. It's one-shot, so the worst case is a single stray capital. Honoring input-method content hints could tighten this later, but they aren't reliably exposed here.

## Settings

Two new keys, both booleans defaulting to false: `auto-capitalize` and `capitalize-first-letter`. Each gets a switch under the Behavior group in Preferences.

## Testing

- Enable both. Type "hello. world" and confirm "World". Add a newline and confirm the next letter capitalizes.
- Confirm `e.g.` and `3.14` stay lowercase after the dot.
- Focus an empty field and confirm the first letter capitalizes.
- Open then close the keyboard without typing, then check Shift isn't stuck on your hardware keyboard.

Tested on GNOME 50.4 (Wayland).
