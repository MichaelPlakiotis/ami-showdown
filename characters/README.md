# Character photos — drop a photo, get a fighter

Put a photo of each friend in this folder (`.png` / `.jpg` / `.gif` / `.webp`),
commit and push. That's it — the game reads this folder automatically:

- **Fighter name = file name** — `MICHAEL.png` becomes **MICHAEL**
  (`-` and `_` turn into spaces: `big_nick.jpg` → **BIG NICK**).
- **Colors are sampled from the photo** at load time — skin tone, hair color, and
  shirt color are detected and turned into the 8-bit palette. Portrait-style
  photos (face in the upper half, shoulders at the bottom) sample best.

Photo characters appear alongside the built-in Waiter and Kitchen Staff on the
character-select screen. Everyone shares the same move set for now (Milestone 1).

Want to hand-tune someone (add `glasses: true`, a `'toque'`, a bowtie, or exact
hex colors)? Add an entry to `CHARACTERS` in `../index.html` instead — built-in
entries are fully manual.
