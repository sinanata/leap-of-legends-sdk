# Leap of Legends — Mod SDK

<img src="demo/thumbnail.png" alt="Demo Mod Thumbnail" width="256" height="256">

Create custom game modes for **Leap of Legends** using JSON configuration files.
No coding, no compilation, no DLLs — just define your mode in `mod.json` and play.

## Getting Started

1. **Create a folder** for your mod (e.g., `com.yourname.yourmod/`)
2. **Create `mod.json`** with your game mode configuration (see schema below).
   Add `"$schema": "./mod.schema.json"` (copy [mod.schema.json](mod.schema.json)
   next to it) for editor autocomplete + validation in VS Code and friends.
3. **Add a thumbnail** (`thumbnail.png`, 512x512 recommended) — it shows on
   your mod's card in the mode selection screen
4. **Copy to game**: place folder in `<Game>/LeapOfLegends_Data/StreamingAssets/Mods/`
   (the **Open Mods Folder** button on the mode selection screen takes you there)
5. **Launch the game** — your mod appears in the mode selection screen.
   While iterating, press **Reload Mods** there instead of restarting.
   A broken mod shows up as a red card with the exact error (line/column for
   JSON syntax); a mod with suspicious config shows a warnings badge.
6. **Draw your map** (optional) — Mods screen → **Map Editor** paints a
   fixed `map.layout` straight into your mod.json (or scaffolds a whole new
   mod with **New Mod**). Random generation stays the default if you skip it.
7. **Add flair** (optional) — drop `background.png` and `music.ogg` next to
   mod.json for a custom backdrop + soundtrack, and a `voice/` folder to
   replace announcer nudges. See the schema reference for details.
8. **Upload**: use the in-game Steam Workshop UI — the whole folder
   (json, images, audio) ships with your Workshop item

That's it. No build tools, no dependencies, no version conflicts.

## Repository Structure

```
leap-of-legends-sdk/
├── demo/                    ← Complete example mod (Arena Showdown)
│   ├── mod.json             ← Full mod configuration
│   ├── background.png       ← Custom match background (api 1.1)
│   └── README.md            ← Demo-specific docs
├── examples/
│   └── fixed-arena/         ← Hand-authored map.layout example (api 1.1)
├── docs/                    ← Detailed documentation
│   ├── MOD_SCHEMA.md        ← Complete JSON schema reference
│   ├── SECURITY.md          ← Security model
│   └── WORKSHOP_UPLOAD.md   ← Steam Workshop guide
├── mod.schema.json          ← JSON Schema for editor autocomplete
└── README.md                ← This file
```

## Demo Mod — Arena Showdown

The `demo/` folder contains a **complete example** demonstrating every feature:

| Feature | How It's Configured |
|---------|-------------------|
| Teams (Red vs Blue) | `teams.enabled`, `teams.definitions` |
| Hand-built map | `map.layout` text rows (api 1.1) — bases, hill, pits, ledges |
| Named regions for mechanics | `map.regions` (hill rect, hazard pits) |
| Timer + score-target win | `win_conditions` array |
| Team kill scoring + streaks | `scoring.type`, `scoring.streaks` |
| Team-base spawning | `spawn.strategy: "team_base"` |
| HUD: timer, scores, team leaderboard, capture bar | `hud` widget array |
| Custom effects (zone buff + on-kill debuff) | `effects` array + `apply_effect` trigger |
| Effect zones, hazards, portals, king-of-the-hill | `mechanics` array |
| AI tuning | `ai` config |
| **30-language localization** | `name_french`, `description_german`, etc. |
| Custom background image | `background` block + `background.png` (api 1.1) |

## Quick Reference

### mod.json Structure

```json
{
  "id": "com.yourname.yourmod",
  "name": "Display Name",
  "version": "1.0.0",
  "api_version": "1.0",
  "author": "Your Name",
  "description": "Short description",
  "thumbnail": "thumbnail.png",
  "tags": ["pvp", "teams"],

  "name_french": "Nom Français",
  "name_german": "Deutscher Name",
  "description_french": "Description en français",
  "description_german": "Beschreibung auf Deutsch",

  "players": { "min": 1, "max": 6 },

  "ai": {
    "enabled": true,
    "count": 3,
    "chase_range": 14.0,
    "stomp_range": 4.0
  },

  "teams": {
    "enabled": false,
    "count": 2,
    "friendly_fire": false,
    "definitions": [
      { "id": 1, "name": "Red", "color": "#FF4444" },
      { "id": 2, "name": "Blue", "color": "#4444FF" }
    ]
  },

  "map": {
    "width": 26,
    "height": 16,
    "water_height": 2,
    "regions": [
      { "id": "my_zone", "min_x": 1, "min_y": 1, "max_x": 5, "max_y": 10, "cell_type": "air" }
    ]
  },

  "timer": 360,

  "win_conditions": [
    { "type": "timer" },
    { "type": "team_score", "target": 25 }
  ],

  "scoring": {
    "type": "kills",
    "label": "Kills",
    "streaks": { "enabled": true, "announce_at": 3 }
  },

  "spawn": { "strategy": "maximin" },
  "movement": { "jump": 1.2, "gravity": 0.9 },

  "hud": [
    { "type": "timer", "anchor": "top_center", "font_size": 36, "flash_below": 30 },
    { "type": "leaderboard", "anchor": "top_right", "max_entries": 6 }
  ],

  "post_match": { "title": "MY MODE", "show_mvp": true }
}
```

### Win Condition Types

| Type | Description | Fields |
|------|-------------|--------|
| `timer` | Ends when timer expires | (uses top-level `timer` value) |
| `score` | Any player reaches target | `target` |
| `team_score` | Any team reaches target | `target` |
| `all_dead` | All players dead | — |
| `all_humans_dead` | All human players dead (AI ignored) | — |
| `captures` | Team capture count | `target` |
| `zone_hold` | Team holds the zone long enough | `hold_time` |
| `checkpoint_race` | Checkpoint / lap target reached | `target` |
| `state` | Custom: a mod state comparison holds | `key`, `op`, `value` |
| `last_standing` | One player left alive | — |

### Scoring Types

| Type | Description |
|------|-------------|
| `kills` | +1 per kill (FFA) |
| `team_kills` | +1 per kill to player and team |
| `resource` | Score = collected resource amount |
| `captures` | Score = flag captures |
| `distance` | Score = distance traveled |

### Spawn Strategies

| Strategy | Description |
|----------|-------------|
| `maximin` | Maximizes distance from other players (default) |
| `random` | Random valid position |
| `team_zones` | Spawn within team's zone (configure `zones`) |
| `team_base` | Spawn at team's base position |
| `behind_leader` | Spawn behind furthest player (scrolling modes) |

### Camera

Every 2D mode uses the game's single tuned **follow** camera (with built-in
zoom). `camera.type` accepts `fullmap`/`scrolling` as legacy aliases that run
as follow. You can skip the `camera` section entirely.

### Movement (api 1.1)

`"movement": { "move_speed": 1.0, "jump": 1.0, "gravity": 1.0 }` — mod-wide
multipliers for every player, clamped to 0.25-4.0. Low gravity arenas, speed
modes, bunny-hop modes: one line each.

### HUD Widget Types

| Type | Description | Key Fields |
|------|-------------|------------|
| `timer` | Countdown display | `flash_below`, `flash_color` |
| `team_scores` | Team score bar | `show_target`, `target` |
| `leaderboard` | Ranked player list | `max_entries` |
| `streak_popup` | Kill streak announcement | `min_streak`, `duration`, `color` |
| `player_count` | Alive/total display | — |
| `message` | Custom text | `text` (supports `{target}`, `{time_remaining}`) |
| `resource` | Resource counter | `resource`, `label` |
| `progress_bar` | Fillable bar from mod state | `progress_key`, `fill_color` |
| `kill_feed` | Recent kills ticker | `feed_entries`, `feed_duration` |
| `custom_counter` | Formatted mod state value | `counter_key`, `format` |
| `flag_status` | Per-team flag state | (teams required) |
| `button_cooldown` | Zone button readiness | — |
| `announcement` | Fading text from `announce` trigger actions (api 1.1) | `font_size`, `color` |

### HUD Anchor Positions

`top_left`, `top_center`, `top_right`, `center`, `bottom_left`, `bottom_center`, `bottom_right`, `top_bar`

Widgets sharing an anchor stack automatically into a column (declaration
order = top to bottom) — they never overlap. All widgets support
`offset_x`, `offset_y` for fine-tuning on top of the stacking.

In team modes the `leaderboard` widget groups players under colored team
headers with live team scores (`max_entries` then applies per team).

### Localization (Steam API Language Codes)

Localize your mod name and description using Steam's API language codes as key suffixes:

```json
"name_french": "Translated Name",
"description_french": "Translated Description"
```

All 30 Steam language codes: `arabic`, `bulgarian`, `schinese`, `tchinese`, `czech`, `danish`, `dutch`, `english`, `finnish`, `french`, `german`, `greek`, `hungarian`, `indonesian`, `italian`, `japanese`, `koreana`, `norwegian`, `polish`, `portuguese`, `brazilian`, `romanian`, `russian`, `spanish`, `latam`, `swedish`, `thai`, `turkish`, `ukrainian`, `vietnamese`

See [docs/MOD_SCHEMA.md](docs/MOD_SCHEMA.md#localization) for the full mapping table.

### Map Region Cell Types

`air`, `rock`, `water`, `ice`, `sand`, `dirt`, `gravel`, `grass` (solid types
become solid ground re-themed by position — see MOD_SCHEMA.md)

### Custom Map Layouts (api 1.1)

`map.layout` draws the whole map as text rows (top row first), replacing
random generation. Paint it in-game with the **Map Editor** (Mods screen)
or by hand:

```json
"map": {
  "layout": [
    "############",
    "#..........#",
    "#..GG..II..#",
    "############"
  ]
}
```

| Char | Cell | | Char | Cell |
|------|------|-|------|------|
| `.` | air | | `S` | sand |
| `#` | rock | | `D` | dirt |
| `~` | water | | `V` | gravel |
| `I` | ice | | `G` | grass |

Rules: 8-64 columns × 6-32 rows, all rows equal length, borders must be
solid (the engine seals non-solid edges to rock). `map.regions` still work
as named rectangles for mechanics (`zone_control`, `hazard_zone`, spawn
zones) — with a layout they no longer carve cells. See `demo/` and
`examples/fixed-arena/`.

### Built-in Game Mechanics

These activate built-in systems via the `mechanics` array (one entry per
type; all 8 available to JSON mods since game 0.1.106):

| Type | Description | Key Fields |
|------|-------------|------------|
| `collectible` | Spawned items (coins, gems) | `resource`, `values`, `initial_count`, `scatter_on_death` |
| `flag` | Capture the flag (teams required) | `auto_return_time`, `flag_pickup_radius`, `capture_radius` |
| `zone_button` | Area-effect button (Crossfire-style) | `safe_zone`, `cooldown`, `kill_outside` |
| `effect_zone` | Buff/debuff pickup areas | `positions`, `effect_id`, `zone_radius` |
| `zone_control` | King-of-the-hill capture point | `zone_id`, `capture_time` |
| `portal` | Teleporter pairs | `portal_pairs`, `portal_cooldown` |
| `hazard_zone` | Damage/kill regions | `hazard_zones`, `hazard_type` |
| `checkpoint` | Race checkpoints + laps | `checkpoint_positions`, `ordered`, `laps` |

### Effect Types (built-in)

| ID | Effect |
|----|--------|
| 1 (0x01) | Speed Boost |
| 2 (0x02) | Jump Boost |
| 3 (0x03) | Speed Slow |
| 4 (0x04) | Invincibility |
| 5 (0x05) | Damage |
| 6 (0x06) | Instant Kill |
| 7 (0x07) | Teleport |
| 8 (0x08) | Gravity |
| 64-255 | Custom mod effects |

Every active effect is visible in the world: zones render as color-coded
volumes (ring pads, striped hazard boxes, the framed capture hill, portal
gates) and affected characters carry a matching aura + rim/tint — in both
the 3D and the low-end sprite render paths.

Buffs and debuffs never stack (applying one polarity purges the other;
`behavior` decides which side an effect is on), and death clears all
effects — respawns always start clean.

### Audio Overrides — All Replaceable Sound Names (api 1.1)

Drop `music.ogg` next to mod.json for custom match music (`music_1.ogg`,
`music_2.ogg`, … form a shuffled playlist). Put voice/SFX replacements in a
`voice/` folder, named after these built-in clip keys (`.ogg` recommended;
`.wav`/`.mp3` also work). Add `_1`, `_2`, … suffixed variants of any key to
have one picked at random per play. Keys you don't provide keep the stock
sound; child accounts always keep the curated stock audio.

| Key | Plays when |
|-----|------------|
| `Voice_Yeah` | Your kill (random pick from the single-kill pool) |
| `Voice_Smasher` | Your kill (single-kill pool) |
| `Voice_OhYeah` | Your kill (single-kill pool) |
| `Voice_Ninja` | Your kill (single-kill pool) |
| `Voice_Hmm` | Your kill (single-kill pool) |
| `Voice_Crusher` | Your kill (single-kill pool) |
| `Voice_FirstBlood` | First kill of the match (heard by everyone) |
| `Voice_DoubleKill` | 2 kills in quick succession (killer only) |
| `Voice_TripleKill` | 3 kills in quick succession (killer only) |
| `Voice_QuadKill` | 4 kills in quick succession (killer only) |
| `Voice_LikeABoss` | Kill-streak tier 1 (killer only) |
| `Voice_Legendary` | Kill-streak tier 2 (killer only) |
| `Voice_Dominating` | Kill-streak tier 3 (killer only) |
| `Crush` | Kill impact SFX at the kill position (gore on) |
| `Poof` | Kill impact SFX at the kill position (gore off / child-safe) |

Optional `audio` block: `music` (filename), `music_volume` (0.05-1.0 extra
scale), `voice_dir` (folder name, default `"voice"`). See
[docs/MOD_SCHEMA.md](docs/MOD_SCHEMA.md#audio--voice-overrides-api-11).

## Example Modes

### Simple Deathmatch
```json
{
  "id": "com.you.simpledm",
  "name": "Quick Deathmatch",
  "timer": 180,
  "win_conditions": [{ "type": "timer" }],
  "scoring": { "type": "kills", "label": "Kills" },
  "hud": [
    { "type": "timer", "anchor": "top_center" },
    { "type": "leaderboard", "anchor": "top_right" }
  ]
}
```

### Team Battle (First to 30)
```json
{
  "id": "com.you.teambattle",
  "name": "Team Battle",
  "teams": {
    "enabled": true,
    "count": 2,
    "definitions": [
      { "id": 1, "name": "Red", "color": "#FF0000" },
      { "id": 2, "name": "Blue", "color": "#0088FF" }
    ]
  },
  "timer": 600,
  "win_conditions": [
    { "type": "timer" },
    { "type": "team_score", "target": 30 }
  ],
  "scoring": { "type": "team_kills", "label": "Kills" },
  "hud": [
    { "type": "timer", "anchor": "top_center" },
    { "type": "team_scores", "anchor": "top_bar", "show_target": true, "target": 30 }
  ]
}
```

### Survival (No Timer)
```json
{
  "id": "com.you.survival",
  "name": "Last Frog Standing",
  "timer": 0,
  "ai": { "count": 5 },
  "win_conditions": [{ "type": "all_dead" }],
  "scoring": { "type": "kills", "label": "Kills" },
  "hud": [
    { "type": "player_count", "anchor": "top_center", "font_size": 28 },
    { "type": "leaderboard", "anchor": "top_right" }
  ],
  "post_match": { "title": "LAST FROG STANDING" }
}
```

## Tips

- Start by copying the `demo/mod.json` and modifying values
- Only include fields you want to change — sensible defaults apply for everything else
- Test with AI bots before multiplayer
- Prefer `map.layout` for designed maps; regions carve WFC maps and name
  rectangles for mechanics
- HUD widgets on the same anchor stack automatically; use `offset_y` only
  for extra fine-tuning
- AI bots have negative NetworkIds
- Map grid is indexed as `y * Width + x`
- Keep thumbnails at 512x512 PNG for best Workshop display

## License

Mod configurations you create are yours. The demo mod.json is provided under MIT license
as a learning resource.
