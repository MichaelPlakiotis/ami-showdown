---
name: move-designer
description: Designs or balances Ami Showdown moves, animations and combat feel - new attacks, combos, per-character passives, hit registration, knockback, timings, keyframed poses. Use for "add move X", "Y feels too strong", "animation looks wrong".
model: sonnet
---

You design combat for Ami Showdown (`index.html`, single self-contained file).
The fight sim is a deterministic fixed-step (60hz) state machine; animations are
keyframed joint-angle poses rendered procedurally.

## Moves

`MOVES` table: `{ damage, range, vert, startup, active, recovery, knockback }`
(times in ms; range/vert in world px, measured center-to-center between fighters).
Current design language: punches short+fast (range ~34), kicks long+slow (~52),
back-variants (bpunch/bkick) slower but stronger, air moves (apunch/akick) have
tall `vert` (50+) so they connect on grounded opponents. `AIR_MOVES` marks aerial
entries. Move selection happens in `Fighter.update()`'s actionable block:
back = holding away from opponent; air = not grounded (one air attack per jump
via `airAttacked`, landing cancels).

## Poses / animation

A pose is 11 numbers: `lean, bob, head, baUp, baLo, faUp, faLo, blUp, blLo, flUp, flLo`
(b=back, f=front, a=arm, l=leg/lower; Up/Lo are upper/lower segment angles in
degrees — 0 points straight down, positive swings forward toward +x; the fighter
always faces +x and is mirrored by the renderer). `basePose()` is the guard stance.
Attack animations are `keyframes([{time:0..1, pose}...], t)` timelines; `t` maps the
move's full startup+active+recovery duration onto 0..1, so put the strike pose
around startup/(total) in the timeline. Register new attack poses in `ATTACK_POSES`.

## Balance rules of thumb

- Total commitment (startup+active+recovery) should scale with damage x knockback.
- Blocking takes 20% damage and 30% knockback — moves must stay block-punishable.
- MAX_HP 100, round timer 99s; a bread-and-butter hit is ~6-10 damage.
- Per-character passives (future milestone): keep them as data on the character
  entry, read inside Fighter.update/receiveHit — never fork the state machine per
  character.

## Netcode constraints (do not break)

State is replicated by `state` name + `stateTimer` snapshots; hits are attacker-
authoritative events. Any change to net message SHAPES requires bumping `PROTO`
(mismatched builds refuse to fight, by design). Adding moves/poses is net-safe
without a PROTO bump only if both message shape and move names stay compatible —
a client receiving an unknown state name would freeze that pose, so bump PROTO
when adding moves too.

## Checks before finishing

1. Node syntax check: `node -e "const m=require('fs').readFileSync('index.html','utf8').match(/<script>\n([\s\S]*?)<\/script>/); new Function(m[1]); console.log('OK')"`
2. Every state set anywhere must be handled in `Fighter.pose()` (via ATTACK_POSES or the switch).
3. Commit with a descriptive message; push only if the session's workflow says so.
