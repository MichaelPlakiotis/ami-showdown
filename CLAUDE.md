# Ami Showdown — Waiters Vs Kitchen Staff

Mobile-first 8-bit 1v1 fighting game for friends. One self-contained `index.html`
(no build step, no framework). Live at https://michaelplakiotis.github.io/ami-showdown/

## Hard rules

- **PRIVACY: photos in `characters/` are gitignored and must NEVER be committed,
  uploaded, or served.** History was purposely rewritten once to purge them — do
  not re-add images, do not weaken `.gitignore`. Only hex colors derived from
  photos go online (hand-tuned `CHARACTERS` entries or `characters/manifest.json`).
- Free & domain-free forever: GitHub Pages hosting + PeerJS public cloud broker.
  Never add a paid service, server, database, or account requirement.
- Everything ships in `index.html`. Keep it dependency-light (currently: PeerJS CDN,
  Google Fonts "Press Start 2P"). No frameworks, no build step.

## Architecture (index.html, top to bottom)

| Section | What it is |
|---|---|
| `CHARACTERS` / `ROSTER` | fighters = palette + feature flags (no sprite assets) + `group:'kitchen'|'waiters'` team label; `loadRosterManifest()` merges `characters/manifest.json` |
| `drawCharacter()` + helpers | procedural pixel renderer (58x66 grid, hip at x=27, feet y=60); feature branches: toque/cap, beard/goatee, apron/halfApron, bun, bald, shortSleeves |
| poses + `keyframes()` | 11 joint angles per pose; attack timelines in `ATTACK_POSES` |
| `STAGES` | restaurant / mainhall / kitchen, drawn per-frame with mini NPCs (`drawMini`, `makeChar`); heavy static art pre-rendered to cached canvas |
| `Sound` / `Music` | WebAudio chiptune SFX; looping `music/theme.mp3` — the two toggles are independent |
| `Fighter` | deterministic state machine: idle/walk/jump/block/punch/kick/bpunch/bkick/apunch/akick/hitstun/ko |
| `MOVES` | damage/range/vert/startup/active/recovery/knockback (ms, world px) |
| `Input` / `setupTouch` | keyboard + floating left joystick + right action buttons |
| `Net` + `Game.netMsg` | PeerJS room codes; snapshots ~30/s tagged with round number; hits attacker-authoritative; `PROTO` version handshake on connect |
| `Game` | orchestration, screens (menu/online/wait/select/stage/result), HUD drawn on canvas |

## Workflows

- **New character**: use the `character-designer` agent (opus). Photo → hand-tuned
  `CHARACTERS` entry. Photos are read locally only.
- **New/changed moves, combat feel, animation**: use the `move-designer` agent (sonnet).
- **New stage**: follow an existing `STAGES` entry; NPCs via `makeChar`+`drawMini`;
  pre-render dense static art to a cached offscreen canvas (see mainhall floor).
- **Netcode changes**: if any message shape or state name changes, bump `PROTO`
  (both clients then insist on matching builds). Round-tagged messages exist to
  prevent stale-round desyncs — keep tagging anything round-scoped.
- **Verify**: `node -e "const m=require('fs').readFileSync('index.html','utf8').match(/<script>\n([\s\S]*?)<\/script>/); new Function(m[1]); console.log('OK')"`
  then test locally (any static server) before pushing.
- **Deploy**: push to `main` → GitHub Pages redeploys (~1 min). Confirm with
  `curl -s https://michaelplakiotis.github.io/ami-showdown/ | grep <new-string>`.
  Online players on mismatched builds get told to refresh by the version handshake.

## Milestones (see PLAN.md)

Done: M1 core fight + online 1v1, stages, roster (kitchen: Trifonas, Stavros,
Christi, Eirini, Ntinos; waiters: Kirsten), music, directional/aerial move
variants, scrollable grouped character select.
Next: per-character passives & combos (keep as data on character entries),
best-of-3 rounds, more friends, more stages.
