# Mod SDK Changelog

## Mod API 1.1 visual & feedback pass — 2026-07 (game 0.1.106+)

No new mod.json fields — this pass makes everything you already declare
*legible in the world*. Existing mods pick all of it up automatically.

### Effect rules

- **Buffs and debuffs never stack** — applying a debuff purges active buffs
  and vice versa, at every application site (zones, hazards, triggers).
  `behavior` decides polarity (`buff` +, `debuff`/`periodic` −, `trigger`
  neutral), falling back to the `apply_as` built-in.
- **Death clears all effects** — a lethal damage-over-time no longer
  survives the respawn and kills the player a second time.

### Effects now actually happen

- **Effects apply to players again.** The replicated per-player effect buffer
  was missing from the player ghost, so effect zones, hazard zones and
  `apply_effect` triggers silently did nothing. Fixed — and speed / slow /
  jump / gravity effects (plus the mod-wide `movement` multipliers) are now
  wired into the predicted movement path on both server and client.
- **Effects are visible on the player** — an additive aura in the effect's
  color follows the affected character, plus a colored rim on 3D characters
  and a matching tint in sprite mode (low-end devices get the same feedback).
- **Team colors on characters** — team modes now rim/tint every character in
  its team color (the old team-color hook targeted a shader property that
  didn't exist).

### Zones you can read at a glance

- **Effect zones** — cell-depth colored volume + crisp spinning ring + soft
  glow (replaces the low-res flat disc).
- **Hazard zones** — cell-depth danger volume with animated diagonal warning
  stripes (replaces the unexplained red rectangle).
- **Zone control (King of the Hill)** — the hill is finally *visible*: it was
  never replicated to clients at all. Now a framed volume with a bottom-up
  capture fill in the capturing team's color, flashing while contested —
  mirroring the HUD progress bar.
- **Portals** — also never replicated (and thus invisible + untrusted).
  Now pair-colored gates: ring + rotating swirl + glow; both endpoints of a
  pair share a color. Teleports route through the proper predicted-teleport
  path instead of raw position writes.

### HUD

- **Same-anchor widgets stack** — widgets sharing an anchor now flow into a
  column (declaration order = top to bottom) instead of overlapping.
  `offset_x`/`offset_y` still apply on top.
- **Team-grouped leaderboard** — in team modes the `leaderboard` widget
  groups players under colored team headers with live team scores.

### Demo mod (Arena Showdown 1.2.0)

- Hand-built symmetric arena via `map.layout` (was random WFC with regions):
  team bases with rally pads, portal ledges, a visible capture hill over an
  ice arena, hazard pits on both approaches, stepping blocks and high routes.
- Team-grouped leaderboard widget; capture bar relabeled "Center Control"
  and no longer overlaps the bottom message.
- "Arena Fatigue" (declared since 1.0, never granted) now applies to the
  killer for 4s after each kill via `apply_effect` — a working anti-snowball
  example.

## Mod API 1.1 — 2026-07 (game 0.1.106+)

The big "everything works" update. Declare `"api_version": "1.1"` to use the
new fields; 1.0 mods keep working unchanged.

### New freedom

- **`movement` section** — mod-wide `move_speed` / `jump` / `gravity`
  multipliers for every player. Low-gravity arenas and speed modes are one
  line now.
- **Trigger `when` conditions** — any trigger can be gated on mod state
  comparisons. Build phases, overtime rules, escalation.
- **`on_interval` triggers** — repeat actions every N seconds
  (`interval`, optional `first_delay`).
- **`on_death` event** — now actually fires (victim as the primary player).
- **New actions** — `announce`, `kill_player`, `teleport_player` (region id
  or x/y), `set_timer`, `add_timer`, `end_match`, `add_mod_float`.
- **`announcement` HUD widget** — big fading text driven by the `announce`
  action, shown on every peer.
- **New win conditions** — `state` (any mod-state comparison — your fully
  custom win rule) and `last_standing`. `checkpoint_race` is implemented
  (checkpoint target or laps).
- **`collectible`, `flag`, `zone_button` mechanics unlocked** — previously
  these silently only worked for official modes; JSON mods declaring them now
  spawn real, replicated coins / flags + towers / the lab purge button.
- **`apply_effect` works** — from triggers and custom events, replicated to
  all clients (4 concurrent effects per player, re-apply refreshes).
- **`team` action target** — implemented (was documented but inert).
- **Hand-authored maps** — `map.layout` draws the entire map as text rows
  (8 cell types), replacing random generation. Author it with the new
  in-game **Map Editor** (Mods screen): pick a mod, paint, Save — it splices
  ONLY the layout array into your mod.json (everything else in your file is
  untouched, syntax-checked before writing, one-time `.bak` kept) and
  hot-reloads. **New Mod** scaffolds a fresh mod folder for you. Edges must
  stay solid — the editor blocks air/water border cells and the engine
  seals any non-solid edge to rock (open edges are Endless-Runner-only).
  See `examples/fixed-arena/`.
- **Custom background image** — `background.png` next to mod.json (or the
  `background` block for filename/brightness) replaces the match backdrop.
- **Custom music** — `music.ogg` (+ `music_1.ogg`, … for a shuffled
  playlist) replaces the match soundtrack, with the stock crossfade
  machinery. `audio.music_volume` scales it.
- **Voice-nudge overrides** — a `voice/` folder with files named after the
  built-in clip keys (`Voice_DoubleKill.ogg`, `Crush.ogg`, …) replaces
  announcer lines and kill SFX. Child accounts always keep the curated
  stock audio.
- **Effect & hazard zones are finally visible** — zone entities are now
  ghost-replicated with pulsing color-coded visuals on every client (they
  used to exist only in the server world: gameplay applied, but nobody —
  including the host — could see them, and coins/zones from the GitHub demo
  looked dead). `zone_respawn_time` on one-shot effect zones works now too.
- **Position sanity checks** — the validator warns when effect zones,
  portals, or checkpoints sit outside the map bounds.

### Multiplayer correctness

- **Mod state replicates.** `mod_state` fields, team scores and streaks now
  reach joining clients (~250 ms after changes) — before, every mod-state
  HUD widget (counters, progress bars, resources) showed zeros for everyone
  but the host. Budget: 126 bytes total (validator warns).
- Joining a lobby whose mod you don't have now bounces you safely with a
  message (and deep-links the mod's Workshop page) instead of loading a
  desynced match.

### Modder experience

- **In-game error surfacing** — broken mod folders appear in the mode
  selection screen as red cards with the exact error; JSON syntax errors
  include line + column and a hint (trailing comma, missing quote…).
- **Validation warnings** — ~40 semantic checks (unknown types, dangling
  region/effect references, reserved state keys, payload budget, ranges) show
  as a badge on the mod card and in the log. Warnings never block loading.
- **Reload Mods button** — rescan `StreamingAssets/Mods` + Workshop without
  restarting the game. **Open Mods Folder** button next to it.
- **Thumbnails** — `thumbnail.png` now actually displays on mode cards.
- **Subscribed Workshop mods register at first mode-list visit** — no longer
  hidden until the Workshop overlay was opened.
- **`mod.schema.json`** — JSON Schema for editor autocomplete/validation
  (`"$schema": "./mod.schema.json"`).
- **api_version is enforced** — newer-major mods refuse to load with a clear
  message instead of silently misbehaving.

### Honesty fixes (docs now match the engine)

- `camera.type` — all 2D modes use the single tuned follow camera;
  `fullmap`/`scrolling` are legacy aliases (validator notes it).
- `spawn.respawn_delay` — documented as reserved (engine uses 0.5s).
- Solid region cell types (`rock`/`sand`/`dirt`/`gravel`/`grass`) become
  solid ground re-themed by position; documented.
- `teams.auto_balance` — accepted; balancing is always on.

## 1.0.x

Initial public SDK: identity + 30-language localization, players/AI/teams,
map regions, win conditions, scoring + streaks, spawn strategies, HUD
widgets, post-match templates, custom effects, 8 mechanics, mod state,
lifecycle triggers, Steam Workshop upload via the in-game UI.
