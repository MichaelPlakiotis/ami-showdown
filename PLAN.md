# AMI SHOWDOWN — Waiters Vs Kitchen Staff

A free, domain-free, mobile-first online 1v1 2D fighting game in 8-bit style.
Playable in the browser on both Android and iOS (and desktop) — no app store needed.

## The pitch

- 2D fighting game, 8-bit pixel art, landscape phone layout
- Left thumb: floating joystick (move / jump). Right thumb: PUNCH / KICK / BLOCK buttons
- 2 launch characters: **Waiter** and **Kitchen Staff** — more added over time
- No story. Two modes: **Local VS** (one keyboard / testing) and **Online 1v1** (room code)
- Milestone 1: every character shares the same move set. Later: per-character passives & combos

## Zero-cost, zero-domain hosting

| Piece | Solution | Cost |
|---|---|---|
| Game hosting | **GitHub Pages** — static site at `https://<username>.github.io/ami-showdown/` | Free |
| Online play | **PeerJS / WebRTC** — players connect directly to each other; the free public PeerJS cloud broker only does the handshake | Free |
| Domain | None needed — share the github.io link | Free |

To deploy: create a GitHub repo, push this folder, enable Pages (Settings → Pages →
deploy from `main` branch root). Both players just open the URL, one taps HOST,
tells the other the 4-letter code, done. Works on iOS Safari and Android Chrome.

No accounts, no database, no server to maintain. The only external dependency at
runtime is the PeerJS CDN script + Google Fonts (both free CDNs).

## Architecture (what's in `index.html`)

Everything is one self-contained file (easy to host anywhere):

1. **Procedural 8-bit renderer** — ported from the approved `Fighter.dc.html` design
   (Claude Design project). Characters are drawn pixel-by-pixel on canvas from a
   *palette + pose* — no image files. A pose is 11 joint angles; animations are
   keyframe timelines (idle bob, walk cycle, punch, kick, block, hitstun, KO).
   Because characters are palettes, a new character is ~20 lines of colors.
2. **Deterministic fight sim** — fixed-timestep state machine (idle / walk / jump /
   block / punch / kick / hitstun / KO). All logic reads abstract inputs, so the same
   code drives keyboard, touch, and network play.
3. **Input layer** — floating left joystick + right action buttons (multi-touch),
   plus keyboard for desktop testing (P1: WASD+F/G/S · P2: arrows+Enter+/).
4. **Netcode** — each player simulates their own fighter and streams state
   snapshots (~30/s) over a WebRTC data channel; hits are events sent by the
   attacker, applied by the defender. Trust-based — fine for a friends game.
5. **Chiptune SFX** — WebAudio square-wave whoosh + filtered-noise impacts, from the
   design file. No audio assets.

## Milestones

- **M0 — done**: local prototype (`../ami_showdown_prototype.html`, Phaser)
- **M1 — this build**: single-file game. 8-bit design renderer, 2 characters,
  character select, Local VS + Online 1v1 (room codes), touch + keyboard, SFX, timer
- **M2 — real friend sprites**: drop photos of each friend into `characters/`,
  convert to 8-bit head palettes (skin/hair colors + hair shape + accessories like
  glasses are already parameters of the renderer — see `CHARACTERS` in index.html).
  Optionally replace the procedural body with hand-pixelled spritesheets.
- **M3 — roster growth**: add friends as new palette entries; character select grid
- **M4 — per-character identity**: unique passives (e.g. Waiter: faster walk while
  blocking "carrying the tray"; Kitchen Staff: kick applies a "burn" tick), then
  unique specials/combos with their own keyframe timelines
- **M5 — polish**: best-of-3 rounds, stage backgrounds (dining room / kitchen),
  music, win quotes, simple rankings via URL-shared stats

## Adding a character from a photo (`characters/`)

1. Put the friend's photo in `characters/`
2. Pick ~6 colors from it (skin, skin shade, hair, top, bottom, accent)
3. Add an entry to `CHARACTERS` in `index.html` with those hex values and flags
   (`glasses`, `headgear`, `bowtie`) — the renderer does the rest
