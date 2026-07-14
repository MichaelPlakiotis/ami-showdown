# Characters — photos stay private

Friends' photos are **never uploaded**: every image format in this folder is
gitignored, so pictures you drop here stay on your machine only. What ships to
the public site is colors, not photos.

## Adding a friend to the roster

1. Open `../tools/sample.html` in your browser (double-click the file — it runs
   entirely locally, the photo is not sent anywhere).
2. Drop their photo, check the detected skin/hair/shirt swatches, set the
   key/name (`nick` → fighter named **NICK**).
3. Copy the JSON and paste it into `characters/manifest.json`, inside the
   top-level `{ }` (add a comma between entries).
4. Commit and push `manifest.json` — only hex colors go online.

## Hand-tuned characters

For full control (glasses, `'toque'` headgear, bowtie, full/half `apron`,
`beard`, `bald`, `bun`, body build `sx`/`sy`), either add those fields to the
manifest entry or add the character to `CHARACTERS` in `../index.html` like
Stavros and Christi.
