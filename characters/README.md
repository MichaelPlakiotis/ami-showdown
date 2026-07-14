# Character photos

Drop a photo of each friend in this folder (any format). Photos are the *source
material* for their 8-bit look — they are not loaded by the game.

To turn a photo into a fighter (Milestone 2+):

1. Sample ~6 colors from the photo: skin, skin shadow, hair, top, bottom, accent.
2. Copy an entry in `CHARACTERS` inside `../index.html` and swap in those hex values.
3. Set the flags that match the person: `glasses: true/false`,
   `headgear: 'none' | 'toque'`, `bowtie: true/false`.
   (New headgear/hair shapes = a few extra `rect()` lines in `drawCharacter`.)

The renderer draws everything else — poses, animation, outline, shading.
