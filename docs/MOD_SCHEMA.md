# mod.json — Complete Schema Reference

Every field, every option, every default. Copy what you need into your `mod.json`.

> **Mod API 1.1** (game 0.1.106+) adds: global `movement` multipliers, trigger
> `when` conditions, repeating `on_interval` triggers, a working `on_death`
> event, new actions (`announce`, `kill_player`, `teleport_player`,
> `set_timer`, `add_timer`, `end_match`, `add_mod_float`), the `announcement`
> HUD widget, `state` / `last_standing` win conditions, live mod-state
> replication to multiplayer clients, and the `collectible` / `flag` /
> `zone_button` mechanics opened up to JSON mods. It also adds hand-authored
> maps (`map.layout` + the in-game **Map Editor**), a custom [background
> image](#background-api-11), [custom music and voice-nudge
> overrides](#audio--voice-overrides-api-11), and visible, replicated
> zones: effect pads, striped hazard volumes, the framed capture hill,
> pair-colored portals (with working one-shot respawns), plus effect auras
> and team colors on the characters themselves. Set `"api_version": "1.1"`
> when you use any of them. Broken or misconfigured mods now show up in the
> in-game Mods screen with the exact error, and the **Reload Mods** button
> re-scans your folder without restarting the game.

## Identity (required)

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `id` | string | **required** | Unique mod ID (reverse-domain: `com.yourname.modname`) |
| `name` | string | **required** | Display name shown in mode selection |

## Identity (optional)

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `version` | string | `"1.0.0"` | SemVer version |
| `api_version` | string | `"1.0"` | Mod API compatibility (`"major.minor"`). Current: `"1.1"`. A mod declaring a newer **major** than the game supports refuses to load (with a clear message); a newer **minor** loads with a warning and the unknown fields are ignored. |
| `author` | string | `""` | Creator name |
| `description` | string | `""` | Short description |
| `thumbnail` | string | `"thumbnail.png"` | Preview image filename |
| `tags` | string[] | `[]` | Searchable tags |
| `steam_workshop_id` | int | `0` | Set automatically after first Workshop upload |

## Localization

Localize your mod's name and description for all 30 Steam-supported languages using flat keys
with **Steam API language code** suffixes. This format aligns directly with Steam Workshop
metadata so the same keys serve both in-game display and the Workshop store page.

The top-level `name` and `description` fields serve as the default (English) fallback.

```json
"name": "My Mod",
"description": "English description (default)",

"name_french":       "Mon Mod",
"name_german":       "Mein Mod",
"name_japanese":     "マイモッド",
"name_koreana":      "나의 모드",

"description_french":   "Description en français",
"description_german":   "Beschreibung auf Deutsch",
"description_japanese": "日本語の説明",
"description_koreana":  "한국어 설명"
```

### Steam API Language Codes

Use these exact codes as suffixes for `name_` and `description_` keys:

| Code | Language | Code | Language |
|------|----------|------|----------|
| `arabic` | Arabic | `koreana` | Korean |
| `bulgarian` | Bulgarian | `norwegian` | Norwegian |
| `schinese` | Chinese (Simplified) | `polish` | Polish |
| `tchinese` | Chinese (Traditional) | `portuguese` | Portuguese |
| `czech` | Czech | `brazilian` | Portuguese-Brazil |
| `danish` | Danish | `romanian` | Romanian |
| `dutch` | Dutch | `russian` | Russian |
| `english` | English | `spanish` | Spanish-Spain |
| `finnish` | Finnish | `latam` | Spanish-Latin America |
| `french` | French | `swedish` | Swedish |
| `german` | German | `thai` | Thai |
| `greek` | Greek | `turkish` | Turkish |
| `hungarian` | Hungarian | `ukrainian` | Ukrainian |
| `indonesian` | Indonesian | `vietnamese` | Vietnamese |
| `italian` | Italian | `japanese` | Japanese |

> **Note:** You don't need to provide all 30 languages. Only include the ones you can provide
> quality translations for. The game falls back to the default `name`/`description` for any
> missing language.

See `demo/mod.json` for a complete example with all 30 languages translated.

## Players

```json
"players": {
  "min": 1,
  "max": 6
}
```

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `min` | int | `1` | Minimum players |
| `max` | int | `6` | Maximum players |

## AI

```json
"ai": {
  "enabled": true,
  "count": 3,
  "chase_range": 14.0,
  "stomp_range": 4.0,
  "bias_right": false,
  "custom_behavior": "",
  "lab_seek_probability": 0.0,
  "button_press_probability": 0.0,
  "guard_x": 15.0,
  "guard_y": 8.0,
  "guard_radius": 8.0,
  "objective_seek_probability": 0.6,
  "aggression": 1.0,
  "patrol_waypoints": [5, 8, 15, 8, 25, 8]
}
```

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `enabled` | bool | `true` | Whether AI bots are supported |
| `count` | int | `3` | Number of AI bots |
| `chase_range` | float | `14.0` | Horizontal range for chasing players |
| `stomp_range` | float | `4.0` | Range for attempting stomps |
| `bias_right` | bool | `false` | AI moves rightward (scrolling modes) |
| `custom_behavior` | string | `""` | Behavior key (see table below) |
| `lab_seek_probability` | float | `0.0` | `lab_aware`: probability to seek lab |
| `button_press_probability` | float | `0.0` | `lab_aware`: probability to press button |
| `guard_x` | float | `0` | `guard_position`: X coordinate to guard |
| `guard_y` | float | `0` | `guard_position`: Y coordinate to guard |
| `guard_radius` | float | `8.0` | `guard_position`: max distance before returning |
| `objective_seek_probability` | float | `0.6` | `seek_objective`: probability to chase nearest objective |
| `aggression` | float | `1.0` | `aggressive`: chase range multiplier (2.0 = double range) |
| `patrol_waypoints` | float[] | `[]` | `patrol`: x,y pairs `[x1,y1,x2,y2,...]` |

### AI Behaviors

| Behavior | Description |
|----------|-------------|
| `""` (default) | Standard chase-and-stomp |
| `"lab_aware"` | Seeks lab/button area (for zone_button mechanic) |
| `"guard_position"` | Returns to guard point when too far away |
| `"seek_objective"` | Navigates toward nearest ModEntity (coins, flags, etc.) |
| `"aggressive"` | Extended chase range (multiplied by `aggression`) |
| `"passive"` | Flees from nearby players |
| `"patrol"` | Follows waypoint path in order, looping |

## Teams

```json
"teams": {
  "enabled": true,
  "count": 2,
  "friendly_fire": false,
  "auto_balance": true,
  "definitions": [
    { "id": 1, "name": "Red", "color": "#FF4444", "base_x": 3, "base_y": 8 },
    { "id": 2, "name": "Blue", "color": "#4444FF", "base_x": 32, "base_y": 8 }
  ]
}
```

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `enabled` | bool | `false` | Enable team mode |
| `count` | int | `2` | Number of teams |
| `friendly_fire` | bool | `false` | Teammates can kill each other |
| `auto_balance` | bool | `true` | Auto-assign to smallest team |

### Team Definition

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `id` | int | — | Team ID (1, 2, ...) |
| `name` | string | `"Team"` | Display name |
| `color` | string | `"#FFFFFF"` | Hex color for UI and tint |
| `base_x` | int | `0` | Base X position (for `team_base` spawn and flag placement) |
| `base_y` | int | `0` | Base Y position |

## Map

```json
"map": {
  "width": 26,
  "height": 16,
  "open_right": false,
  "open_left": false,
  "open_top": false,
  "water_height": 2,
  "jumpers_enabled": true,
  "jumper_min_spacing": 15,
  "jumper_min_air_above": 10,
  "regions": []
}
```

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `width` | int | `26` | Map width in cells (supported range 12-64) |
| `height` | int | `16` | Map height in cells (supported range 10-32) |
| `open_right` | bool | `false` | Right boundary open (for scrolling) |
| `open_left` | bool | `false` | Reserved — only honored by 3D modes today |
| `open_top` | bool | `false` | Top boundary open |
| `water_height` | int | `2` | Water level (rows from bottom) |
| `jumpers_enabled` | bool | `true` | Spawn jumper pads on eligible rock cells |
| `jumper_min_spacing` | int | `15` | Minimum cells between two jumper pads |
| `jumper_min_air_above` | int | `10` | Air cells required above a rock cell to place a jumper |
| `layout` | string[] | — | Hand-authored fixed map — see [Fixed Layout](#fixed-layout-api-11). When set, `width`/`height`/`water_height`/`open_*` and region cell painting are ignored |

### Map Regions

Regions override WFC-generated cells with forced types. Referenced by mechanics via their `id`.

```json
"regions": [
  { "id": "my_zone", "min_x": 1, "min_y": 1, "max_x": 5, "max_y": 10, "cell_type": "air" }
]
```

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Region identifier (referenced by hazard_zone, zone_control, etc.) |
| `min_x`, `min_y` | int | Bottom-left corner |
| `max_x`, `max_y` | int | Top-right corner |
| `cell_type` | string | `"air"`, `"rock"`, `"water"`, `"ice"`, `"sand"`, `"dirt"`, `"gravel"`, `"grass"` |

> **Cell type honesty:** `air`, `water` and `ice` force exactly that cell.
> Every solid type (`rock`, `sand`, `dirt`, `gravel`, `grass`) becomes solid
> ground whose visible biome is then re-derived from map position — so use
> them as "solid here", not as a guaranteed surface look. The in-game
> validator reminds you when a mod relies on a specific solid look.

### Fixed Layout (api 1.1)

Skip random generation entirely and draw the map yourself. One string per
**row, top row first**; every row the same length. The easiest way to author
one is the in-game **Map Editor** — see [The Map Editor](#the-map-editor)
below.

```json
"map": {
  "layout": [
    "############",
    "#..........#",
    "#....GG....#",
    "#..........#",
    "#~~......~~#",
    "############"
  ]
}
```

| Character | Cell |
|-----------|------|
| `.` | Air |
| `#` | Rock |
| `~` | Water |
| `I` | Ice |
| `S` | Sand |
| `D` | Dirt |
| `V` | Gravel |
| `G` | Grass |

Rules and behavior:

- **Size**: 8-64 columns × 6-32 rows. Characters are case-insensitive.
- The layout **replaces** WFC generation, the `water_height` flood, and
  region **cell painting**. Regions stay available as pure named rectangles
  for mechanics (`hazard_zones`, `zone_control`, teleport targets).
- Water exists exactly where you paint `~`. Engine limit: swim physics only
  track water in the first 32 columns (the validator warns).
- **Borders are always solid** — the engine turns any non-solid edge cell
  into rock so players can't fall out of the arena (open edges are an
  Endless Runner thing, and Endless Runner doesn't use layouts). The Map
  Editor blocks painting air/water on edge cells for the same reason.
- Unlike WFC regions, solid cells keep their **exact painted surface** —
  paint `G` and you get grass, not "solid that might be grass".
- Jumper pads still auto-place on eligible rock unless you set
  `"jumpers_enabled": false`.
- Leave at least one air cell with solid ground below it, or nobody can
  spawn (the validator refuses quietly broken layouts and falls back to WFC,
  telling you why on the mod card).
- Deterministic by construction — every peer builds the identical grid, and
  the map checksum still guards against mismatched files.

See [`examples/fixed-arena/`](../examples/fixed-arena/mod.json) for a
complete hand-built arena with launch pads and slow-zone hazards.

### The Map Editor

**Mods screen → Map Editor** (desktop only, like all modding). What it does:

- **Mod picker** — lists your local mods (folders in
  `StreamingAssets/Mods/`). Workshop-installed copies are deliberately
  excluded: Steam overwrites those folders on update, so edits there would
  be lost — edit your local source folder and re-upload instead.
- **New Mod** — scaffolds a fresh mod folder (`my-map-N/` with a minimal
  mod.json and a bordered starter arena) and selects it, so you can go from
  nothing to painting in one click.
- **Painting** — pick a cell type from the palette; left-click / drag
  paints, right-click erases to air.
- **Size** — width × height fields (8-64 × 6-32). Resizing keeps your
  cells anchored to the bottom-left corner.
- **Borders stay solid** — the editor refuses to paint air or water on an
  edge cell and re-seals the border after every load and resize, matching
  the engine rule above. **Clear** empties the interior only; **Seal
  Borders** is there for one-click cleanup while sketching.
- **Save** — splices ONLY the `layout` array into your mod.json,
  textually. Every other byte of your file is preserved — Steam
  localization keys, field order, fields the editor doesn't know about.
  The result is syntax-checked before anything is written, your first save
  leaves a one-time `mod.json.bak` next to the original, and mods
  hot-reload immediately so the next match plays the new map.

## Timer

```json
"timer": 360
```

Top-level field. Duration in seconds. Set to `0` for no timer.

## Win Conditions

Array of conditions — game ends when **any** condition is met.

```json
"win_conditions": [
  { "type": "timer" },
  { "type": "team_score", "target": 25 },
  { "type": "zone_hold", "hold_time": 120.0 },
  { "type": "checkpoint_race", "target": 10 },
  { "type": "state", "key": "bank_gold", "op": ">=", "value": 500 },
  { "type": "last_standing" }
]
```

| Type | Fields | Description |
|------|--------|-------------|
| `timer` | — | Ends when `timer` seconds expire |
| `score` | `target` | Ends when any player reaches target score |
| `team_score` | `target` | Ends when any team reaches target score |
| `all_dead` | — | Ends when all players are dead simultaneously |
| `all_humans_dead` | — | Ends when all human players are dead (AI ignored) |
| `captures` | `target` | Ends when any team reaches target captures |
| `zone_hold` | `hold_time` | Ends when a team holds zone for `hold_time` total seconds |
| `checkpoint_race` | `target` | Ends when any player hits `target` total checkpoints. With `laps` set on the checkpoint mechanic, laps win instead of raw checkpoint count. |
| `state` | `key`, `op`, `value` | **api 1.1** — ends when a mod state comparison holds (`op`: `==`, `!=`, `>`, `>=`, `<`, `<=`; bools compare as 0/1). Your fully custom win condition — drive the key from triggers. |
| `last_standing` | — | **api 1.1** — ends when one (or zero) players remain alive in a match that started with at least two |

Triggers can also end the match directly with the `end_match` action.

## Scoring

```json
"scoring": {
  "type": "kills",
  "label": "Kills",
  "resource": "",
  "points_per_kill": 1,
  "points_per_event": 1,
  "streaks": {
    "enabled": false,
    "announce_at": 3
  }
}
```

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `type` | string | `"kills"` | Scoring model (see below) |
| `label` | string | `"Kills"` | Score column label |
| `resource` | string | `""` | Resource name (for `resource` type) |
| `points_per_kill` | int | `1` | Points per kill. Set `0` to disable kill scoring. |
| `points_per_event` | int | `1` | Points per custom event (capture, checkpoint, etc.) |
| `streaks.enabled` | bool | `false` | Track kill streaks |
| `streaks.announce_at` | int | `3` | Streak threshold for popup |

### Scoring Types

| Type | Behavior |
|------|----------|
| `kills` | +`points_per_kill` per kill to killer |
| `team_kills` | +`points_per_kill` per kill to killer AND their team |
| `resource` | Score = collected resource amount |
| `captures` | Score = flag captures (uses `points_per_event`) |
| `distance` | Score = distance traveled |
| `zone_time` | Team scoring from zone_control captures (uses `points_per_event`) |
| `checkpoints` | Score = total checkpoints hit * `points_per_event` |

## Spawn

```json
"spawn": {
  "strategy": "maximin",
  "respawn_delay": 2.0,
  "zones": [
    { "team": 1, "x_min_pct": 0.0, "x_max_pct": 0.4 },
    { "team": 2, "x_min_pct": 0.6, "x_max_pct": 1.0 }
  ]
}
```

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `strategy` | string | `"maximin"` | Spawn strategy (see below) |
| `respawn_delay` | float | `2.0` | **Reserved.** The engine currently uses a fixed 0.5s respawn; this field is accepted but not yet applied. |
| `zones` | array | — | Team zone definitions (for `team_zones`) |

### Spawn Strategies

| Strategy | Description |
|----------|-------------|
| `maximin` | Furthest point from all players (default) |
| `random` | Random valid position |
| `team_zones` | Within team's X-range (configure `zones`) |
| `team_base` | Near team's base position (from `teams.definitions`) |
| `behind_leader` | Behind the furthest-right player |

## Camera

```json
"camera": {
  "type": "follow"
}
```

Since the camera overhaul, every 2D mode uses the game's single tuned
**follow** camera (with the built-in zoom controls). `"fullmap"` and
`"scrolling"` are accepted as legacy aliases and run as follow — the in-game
validator points this out. `padding` and `scroll_speed` are legacy no-ops.

## Movement (api 1.1)

Mod-wide movement multipliers, applied to **every** player (humans and AI).
They multiply with effect modifiers — a `1.5` speed_boost effect on top of
`"move_speed": 1.2` gives 1.8x. Values are clamped to `0.25`–`4.0`.

```json
"movement": {
  "move_speed": 1.0,
  "jump": 1.25,
  "gravity": 0.8
}
```

| Field | Default | Description |
|-------|---------|-------------|
| `move_speed` | `1.0` | Horizontal speed multiplier |
| `jump` | `1.0` | Jump power multiplier |
| `gravity` | `1.0` | Gravity multiplier (`0.5` = floaty moon arena, `2.0` = leaden) |

## Background (api 1.1)

Ship a custom match background. Drop a `background.png` next to your
mod.json and it just works — the block below is only needed for a custom
filename or brightness. Purely cosmetic and loaded locally by every peer
(everyone in a modded lobby has your files); child accounts keep the stock
backgrounds.

```json
"background": {
  "image": "background.png",
  "brightness": 0.9
}
```

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `image` | string | `""` | Image file relative to the mod folder (`.png` / `.jpg`). Empty = auto-detect `background.png` |
| `brightness` | float | `1.0` | Tint 0.1-1.0. Stock art renders at 0.44 so gameplay reads on top — dim yours if players get lost in it |

1920×1080 is plenty; the image cover-fills the camera at any aspect ratio.

## Audio & Voice Overrides (api 1.1)

Custom match music and voice-nudge replacement. All files load locally from
your mod folder — `.ogg` recommended (`.wav` and `.mp3` also work). Child
accounts always keep the curated stock audio; your overrides are skipped for
them entirely.

```json
"audio": {
  "music": "music.ogg",
  "music_volume": 0.9,
  "voice_dir": "voice"
}
```

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `music` | string | `""` | Music file relative to the mod folder. Empty = auto-detect `music.ogg` / `.wav` / `.mp3` |
| `music_volume` | float | `1.0` | Extra scale 0.05-1.0 on top of the player's music volume setting |
| `voice_dir` | string | `"voice"` | Folder scanned for voice override files |

**Music playlists**: numbered variants next to the base file —
`music.ogg`, `music_1.ogg`, `music_2.ogg`, … — form a shuffled playlist with
crossfades, exactly like the stock soundtrack. A single file self-loops
seamlessly. The match starts on stock music and crossfades to yours the
moment decoding finishes (usually before the first jump).

**Voice nudges**: put files in `voice/` named after the built-in clip keys:

```
voice/
├── Voice_DoubleKill.ogg
├── Voice_TripleKill.ogg
├── Voice_FirstBlood.ogg
├── Voice_Yeah.ogg          ← single-kill pool
├── Voice_Yeah_1.ogg        ← optional variants, picked at random per nudge
└── Crush.ogg               ← kill impact SFX
```

The complete list of overridable keys and when each one plays:

| Key | Plays when |
|-----|------------|
| `Voice_Yeah` | Your kill — random pick from the single-kill pool |
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
| `Crush` | Kill impact SFX at the kill position (gore enabled) |
| `Poof` | Kill impact SFX at the kill position (gore off / child-safe) |

Keys you don't provide keep the stock clip. Any key accepts `_1`, `_2`, …
suffixed variant files — one is picked at random per play.

## HUD Widgets

Array of widget definitions. Rendered in order, every frame during gameplay.

### Common Fields (all widgets)

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `type` | string | **required** | Widget type |
| `anchor` | string | `"top_center"` | Screen position anchor |
| `font_size` | int | varies | Font size in pixels |
| `color` | string | `"#FFFFFF"` | Text color (hex) |
| `offset_x` | int | `0` | Pixel offset from anchor |
| `offset_y` | int | `0` | Pixel offset from anchor |

### Anchor Positions

`top_left`, `top_center`, `top_right`, `center`, `bottom_left`, `bottom_center`, `bottom_right`, `top_bar`

Widgets sharing an anchor stack into a column automatically (declaration
order = top to bottom) — they never overlap each other. `offset_x` /
`offset_y` apply on top of the stacking as extra margins.

### Widget Types

#### `timer`
Countdown display with optional flash effect.

| Field | Type | Default |
|-------|------|---------|
| `flash_below` | float | `0` (disabled) |
| `flash_color` | string | `"#FF0000"` |

#### `team_scores`
Shows team score labels.

| Field | Type | Default |
|-------|------|---------|
| `show_target` | bool | `false` |
| `target` | int | `0` |

#### `leaderboard`
Sorted player score list. In team modes (`teams.enabled` with 2+
definitions) rows group under colored team headers showing each team's
live score, and `max_entries` applies per team.

| Field | Type | Default |
|-------|------|---------|
| `max_entries` | int | `6` |

#### `streak_popup`
Temporary kill streak announcement.

| Field | Type | Default |
|-------|------|---------|
| `min_streak` | int | `3` |
| `duration` | float | `2.0` |
| `color` | string | `"#FFD700"` |

#### `player_count`
Shows alive/total player count.

#### `message`
Static or dynamic text. Supports template variables:
- `{target}` — win condition target
- `{time_remaining}` — formatted timer
- `{alive_count}` — alive players
- `{player_count}` — total players

| Field | Type | Default |
|-------|------|---------|
| `text` | string | `""` |

#### `resource`
Shows a resource counter.

| Field | Type | Default |
|-------|------|---------|
| `resource` | string | `""` |
| `label` | string | `""` (template: `{value}`) |

#### `progress_bar`
Visual progress bar reading from mod state. Ideal for zone capture, checkpoint progress, etc.

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `progress_key` | string | `""` | Mod state key to read (0.0-1.0 float) |
| `bar_width` | int | `200` | Bar width in pixels |
| `bar_height` | int | `20` | Bar height in pixels |
| `fill_color` | string | `"#00FF00"` | Fill color (hex) |
| `bg_color` | string | `"#333333"` | Background color (hex) |
| `label` | string | `""` | Optional label (supports `{value}` for percentage) |

If a `{progress_key}_team` mod int exists, the controlling team's name is shown above the bar.

#### `kill_feed`
Scrolling kill notifications. Reads from `kill_feed_latest_id`, `kill_feed_killer`, `kill_feed_victim` mod state keys (set automatically by the framework on kills).

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `feed_entries` | int | `5` | Max visible entries |
| `feed_duration` | float | `4.0` | Seconds each entry stays visible |

#### `custom_counter`
Displays a single value from mod state with custom formatting.

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `counter_key` | string | `""` | Mod state key to read (int) |
| `format` | string | `"{value}"` | Format string (`{value}` replaced with the int) |

#### `flag_status`
Shows per-team flag status (base/taken/dropped). Requires teams enabled.

Reads from `flag_{teamId}_status` mod state: `0`=base, `1`=carried, `2`=dropped.

#### `button_cooldown`
Shows zone button ready state and cooldown timer.

Reads from `button_cooldown` (float) and `button_ready` (bool) mod state.

#### `announcement` (api 1.1)
Big fading text driven by the `announce` trigger action. Shows on every peer
(each client resolves the text from its own copy of your mod.json, so nothing
heavy crosses the network). One widget handles all announce actions.

| Field | Type | Default |
|-------|------|---------|
| `font_size` | int | `30` |
| `color` | string | `"#FFFFFF"` |

The display duration comes from each announce action's `duration` field.

## Post Match

```json
"post_match": {
  "title": "Team {winning_team_name} Wins!|Draw!",
  "show_mvp": true,
  "show_team_result": true
}
```

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `title` | string | `"MATCH OVER"` | Post-match title (supports templates) |
| `show_mvp` | bool | `true` | Highlight top player |
| `show_team_result` | bool | `false` | Show winning team |

### Title Templates

The title supports placeholders:
- `{winner_name}` — name of the highest-scoring player
- `{winner_team}` — team ID of the winning team
- `{winning_team_name}` — name of the winning team
- `{top_score}` — highest score value

**Conditional syntax** using `|`: text before `|` is used when there's a clear winner, text after `|` is used for draws.

```
"title": "{winner_name} Wins!|It's a Draw!"
"title": "Team {winning_team_name} Victory!|Teams Tied!"
```

## Effects

Custom effect type definitions (IDs 64-255 / 0x40-0xFF). Map them to built-in behaviors with `apply_as`.

```json
"effects": [
  {
    "type_id": 64,
    "name": "Hyper Speed",
    "behavior": "buff",
    "color": "#00FF88",
    "apply_as": "speed_boost",
    "magnitude": 2.0,
    "duration": 8.0
  },
  {
    "type_id": 65,
    "name": "Heavy Gravity",
    "behavior": "debuff",
    "color": "#FF4400",
    "apply_as": "gravity",
    "magnitude": 2.5,
    "duration": 5.0
  }
]
```

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `type_id` | int | — | Effect ID (64-255 for mods) |
| `name` | string | `""` | Display name |
| `behavior` | string | `"buff"` | `"buff"`, `"debuff"`, `"trigger"`, `"periodic"` |
| `color` | string | `"#FFFFFF"` | Tint color (hex) |
| `apply_as` | string | `"none"` | Built-in effect to apply (see table) |
| `magnitude` | float | `1.0` | Effect strength (meaning depends on `apply_as`) |
| `duration` | float | `5.0` | Default duration in seconds |

### `apply_as` Values

| Value | Magnitude Meaning |
|-------|-------------------|
| `"speed_boost"` | Speed multiplier (1.5 = 50% faster) |
| `"jump_boost"` | Jump power multiplier |
| `"speed_slow"` | Speed multiplier (0.5 = half speed) |
| `"invincibility"` | Duration only (magnitude ignored) |
| `"damage"` | Damage per tick |
| `"instant_kill"` | Kills on contact |
| `"gravity"` | Gravity multiplier (2.0 = double gravity) |
| `"teleport"` | Reserved — accepted but no system consumes it yet |
| `"none"` | Visual only, no gameplay effect |

Effects are the framework's fully network-replicated primitive: an applied
effect (from zones, hazards or the `apply_effect` trigger action) reaches all
clients automatically. At most **4 effects** can be active on a player at
once; re-applying an active effect refreshes it in place.

Two rules keep effects predictable:

- **Buffs and debuffs never stack.** Applying a debuff removes every active
  buff and vice versa, no matter how the effect was granted (zone, hazard,
  trigger). Polarity comes from `behavior` — `"buff"` is positive,
  `"debuff"` and `"periodic"` are negative, `"trigger"` is neutral (neither
  purges nor gets purged); if `behavior` is missing, the `apply_as`
  built-in decides. In the demo, Arena Fatigue strips Team Rally on kill,
  and stepping back on the rally pad cleanses the fatigue.
- **Death clears all effects.** A respawning player starts clean — a lethal
  damage-over-time can never carry across the respawn and kill the player
  again.

### Built-in Effect IDs

| ID | Effect |
|----|--------|
| 1 | SpeedBoost |
| 2 | JumpBoost |
| 3 | SpeedSlow |
| 4 | Invincibility |
| 5 | Damage |
| 6 | InstantKill |
| 7 | Teleport |
| 8 | Gravity |
| 9-63 | Reserved |
| 64-255 | Mod-defined (use `effects` array) |

## Game Mechanics

Activate built-in configurable game mechanics. Each mechanic type maps to compiled ECS systems that the framework ships with. Your JSON mod just activates and configures them.

```json
"mechanics": [
  { "type": "collectible", ... },
  { "type": "effect_zone", ... }
]
```

Notes:

- **All 8 mechanics are available to JSON mods.** Before game 0.1.106,
  `collectible`, `flag` and `zone_button` silently only worked for the
  official modes — declaring them spawned nothing. They now spawn their
  fully replicated entities (coins, flags + towers, the lab button) for any
  mod that declares them. `flag` needs `teams.enabled` and reads flag/base
  positions from `teams.definitions` (`base_x`/`base_y`).
- **One mechanic of each type per mod** — a second entry of the same type is
  ignored (the validator warns).
- The `effect_zone`, `hazard_zone`, `portal`, `checkpoint` and `zone_control`
  gameplay runs fully server-side, so multiplayer behavior is authoritative;
  their zone/marker positions are part of your mod.json, which every peer
  has. Applied effects replicate as ghosts.

### Collectible

Spawns and manages collectible items (coins, gems, etc.).

```json
{
  "type": "collectible",
  "resource": "gold",
  "values": [1, 5, 10],
  "initial_count": 50,
  "max_on_map": 80,
  "respawn_interval": 8.0,
  "respawn_batch": 3,
  "pickup_radius": 1.2,
  "scatter_on_death": true,
  "scatter_velocity": 5.0,
  "max_scatter_coins": 20
}
```

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `resource` | string | `""` | Resource name for scoring |
| `values` | int[] | `[1]` | Coin value distribution |
| `initial_count` | int | `30` | Coins spawned at match start |
| `max_on_map` | int | `80` | Max coins on map at once |
| `respawn_interval` | float | `8.0` | Seconds between respawn batches |
| `respawn_batch` | int | `3` | Coins per respawn batch |
| `pickup_radius` | float | `1.2` | Collection distance |
| `scatter_on_death` | bool | `true` | Drop coins on death |
| `scatter_velocity` | float | `5.0` | Scatter speed |
| `max_scatter_coins` | int | `20` | Max coins scattered per death |

### Flag (Capture the Flag)

Team-based flag capture. Requires `teams.enabled = true`.

```json
{
  "type": "flag",
  "auto_return_time": 30.0,
  "flag_pickup_radius": 1.5,
  "capture_radius": 2.0
}
```

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `auto_return_time` | float | `30.0` | Seconds before dropped flag returns to base |
| `flag_pickup_radius` | float | `1.5` | Distance to pick up flag |
| `capture_radius` | float | `2.0` | Distance to capture at own base |

Flag positions come from `teams.definitions[].base_x/base_y`.

### Zone Button (Crossfire-style)

A button that kills all players outside a safe zone.

```json
{
  "type": "zone_button",
  "id": "lab_button",
  "safe_zone": "lab",
  "cooldown": 45.0,
  "kill_outside": true,
  "credit_to_presser": true,
  "button_radius": 1.0,
  "button_x": 0,
  "button_y": 0
}
```

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `id` | string | `""` | Button identifier |
| `safe_zone` | string | `""` | Map region ID defining the safe area |
| `cooldown` | float | `45.0` | Seconds between presses |
| `kill_outside` | bool | `true` | Kill players outside safe zone on press |
| `credit_to_presser` | bool | `true` | Presser gets kill credit |
| `button_radius` | float | `1.0` | Activation distance |
| `button_x` | float | `0` | Button X position (0 = auto-center in safe zone) |
| `button_y` | float | `0` | Button Y position (0 = auto-center in safe zone) |

### Effect Zone

Places effect-giving zones at specific positions. Players entering the zone
receive the configured effect. Zones are replicated and **visible to every
player** as a pulsing disc in the effect's color (custom effects use their
`color`; built-ins use the standard palette).

```json
{
  "type": "effect_zone",
  "positions": [10, 8, 20, 8, 15, 12],
  "effect_id": 1,
  "effect_duration": 5.0,
  "effect_magnitude": 1.5,
  "zone_radius": 1.5,
  "persistent": true,
  "one_shot": false,
  "zone_respawn_time": 0
}
```

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `positions` | float[] | — | Flat array of x,y pairs: `[x1,y1,x2,y2,...]` |
| `effect_id` | int | — | Effect type ID (built-in 1-8 or custom 64-255) |
| `effect_duration` | float | `5.0` | Effect duration in seconds |
| `effect_magnitude` | float | `1.0` | Effect strength |
| `zone_radius` | float | `1.5` | Activation radius |
| `persistent` | bool | `true` | Effect persists after leaving zone |
| `one_shot` | bool | `false` | Zone consumed on first use (hidden while waiting to respawn) |
| `zone_respawn_time` | float | `0` | Seconds until a consumed one-shot zone comes back. `0` = gone for the match |

### Zone Control (King of the Hill)

Teams compete to hold a zone. Score points by maintaining control.

```json
{
  "type": "zone_control",
  "zone_id": "hill",
  "capture_time": 10.0,
  "points_per_capture": 1,
  "reset_on_contest": true,
  "progress_key_prefix": "zone_progress"
}
```

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `zone_id` | string | `""` | Map region ID defining the zone bounds |
| `capture_time` | float | `10.0` | Seconds to hold for one point |
| `points_per_capture` | int | `1` | Points per successful hold |
| `reset_on_contest` | bool | `true` | Progress resets when both teams are in zone |
| `progress_key_prefix` | string | `"zone_progress"` | Mod state key for HUD progress bar |

Writes to mod state:
- `{progress_key_prefix}` (float 0.0-1.0): current capture progress
- `{progress_key_prefix}_team` (int): controlling team ID

Fires `zone_capture` scoring event with team ID.

### Portal

Paired teleportation portals. Step on one, teleport to its partner.

```json
{
  "type": "portal",
  "portal_pairs": [5, 8, 25, 8, 10, 3, 20, 12],
  "portal_radius": 1.0,
  "portal_cooldown": 2.0
}
```

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `portal_pairs` | float[] | — | Flat array: `[ax1,ay1,bx1,by1, ax2,ay2,bx2,by2, ...]` |
| `portal_radius` | float | `1.0` | Activation distance |
| `portal_cooldown` | float | `2.0` | Per-player cooldown between uses (seconds) |

Each group of 4 values defines one pair: `(ax,ay)` is endpoint A, `(bx,by)` is endpoint B. Bidirectional.

### Hazard Zone

Dangerous areas that apply effects to players inside them. References map
regions. The danger rectangle is replicated and **visible to every player**
as a pulsing translucent overlay tinted by the hazard's effect color.

```json
{
  "type": "hazard_zone",
  "hazard_zones": ["lava_pit", "acid_pool"],
  "hazard_type": "periodic_damage",
  "hazard_magnitude": 1.0
}
```

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `hazard_zones` | string[] | — | Map region IDs to turn into hazards |
| `hazard_type` | string | `"periodic_damage"` | Damage mode (see below) |
| `hazard_effect_id` | int | `5` | Override: specific effect ID |
| `hazard_magnitude` | float | `1.0` | Effect strength |

### Hazard Types

| Type | Effect |
|------|--------|
| `"instant_kill"` | Kills on contact |
| `"periodic_damage"` | Damage over time while inside |
| `"slow"` | Reduces movement speed |

### Checkpoint

Race-style checkpoints. Players must reach them in sequence (or any order).

```json
{
  "type": "checkpoint",
  "checkpoint_positions": [5, 8, 15, 8, 25, 8, 15, 12],
  "checkpoint_radius": 2.0,
  "ordered": true,
  "laps": 3
}
```

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `checkpoint_positions` | float[] | — | Flat array: `[x1,y1,x2,y2,...]` |
| `checkpoint_radius` | float | `2.0` | Activation distance |
| `ordered` | bool | `true` | Must reach checkpoints in index order |
| `laps` | int | `0` | Number of laps (0 = one-way, no looping) |

Fires `checkpoint_reached` scoring event. Writes per-player progress to mod state:
- `checkpoints_{netId}` (int): total checkpoints hit
- `laps_{netId}` (int): laps completed (only if `laps > 0`)

## Custom Mod State

Declare custom replicated state fields. These are synced to every multiplayer
client (live since game 0.1.106 — earlier builds only showed them on the
host) and readable by HUD widgets.

```json
"mod_state": [
  { "key": "round_number", "type": "int", "default_value": 1 },
  { "key": "danger_level", "type": "float", "default_value": 0.0 },
  { "key": "overtime", "type": "bool", "default_value": 0 }
]
```

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `key` | string | — | Unique state key (unique across all three types) |
| `type` | string | `"int"` | `"int"`, `"float"`, `"bool"` |
| `default_value` | float | `0` | Initial value (cast to declared type) |

Read these values in HUD widgets via `counter_key`, `progress_key`, or in triggers via `key` / `when` conditions.

**Replication budget:** all replicated state must fit **126 bytes** — team
scores (4 bytes per team), streak tracking (8 bytes when enabled), your
`mod_state` fields (4 bytes each, 1 for bools) and the announce channel
(8 bytes when you use `announce`). That's room for ~25 custom ints; the
validator warns if you exceed it. Clients receive updates within ~250 ms of
a change.

**Reserved keys** the framework maintains itself (don't declare them):
`best_streak`, `best_streak_player`, `team_<n>_score`, `announce_seq`,
`announce_idx`, `mod_end_match`, `kill_feed_*`, `checkpoints_max`,
`checkpoints_leader`, `laps_max`.

## Lifecycle Triggers

Execute actions when game events occur — the mod scripting layer, no code
required. Triggers run **server-side** in declaration order; their state
writes reach every client through mod-state replication.

```json
"triggers": [
  {
    "event_name": "on_match_start",
    "actions": [
      { "action": "set_mod_int", "key": "round_number", "int_value": 1 },
      { "action": "announce", "text": "Round 1 — fight!", "duration": 3.0 }
    ]
  },
  {
    "event_name": "on_kill",
    "when": [
      { "key": "overtime", "op": "==", "value": 1 }
    ],
    "actions": [
      { "action": "add_score", "int_value": 2, "target": "killer" }
    ]
  },
  {
    "event_name": "on_interval",
    "interval": 60,
    "first_delay": 30,
    "actions": [
      { "action": "announce", "text": "Sudden death in 60s!", "duration": 2.5 },
      { "action": "apply_effect", "effect_id": 1, "effect_duration": 5.0, "effect_magnitude": 1.4, "target": "all" }
    ]
  },
  {
    "event_name": "on_custom_event",
    "custom_event": "flag_capture",
    "actions": [
      { "action": "add_timer", "float_value": 30 }
    ]
  }
]
```

### Trigger Events

| Event | When | `player` / `victim` meaning |
|-------|------|------------------------------|
| `on_match_start` | Match begins | — |
| `on_match_end` | Match ends | — |
| `on_player_joined` | Player enters match | joiner |
| `on_player_left` | Player leaves match | leaver |
| `on_kill` | A player kills another | player = killer, victim = victim |
| `on_death` | A player dies to another (**api 1.1** — now actually fires) | player = victim, victim target = killer |
| `on_interval` | Every `interval` seconds during play (**api 1.1**). `first_delay` overrides the first firing; minimum interval 1s. | — |
| `on_custom_event` | Named event fires | event-specific player |

**Custom events you can listen for** (broadcast by mechanics):
`gold_pickup`, `gold_scatter`, `flag_capture`, `flag_return`, `zone_capture`,
`checkpoint_reached` — plus anything your own `broadcast_event` actions fire
(chains up to 8 deep).

### Conditions (`when`, api 1.1)

Any trigger can carry a `when` array — it only runs when **all** comparisons
hold. Each condition is `{ "key", "op", "value" }` over mod state
(`==`, `!=`, `>`, `>=`, `<`, `<=`; bools compare as 0/1, missing keys as 0).
Combined with `on_interval` and `set_mod_*` you can build simple state
machines (phases, overtime, escalation).

### Trigger Actions

| Action | Fields | Description |
|--------|--------|-------------|
| `set_mod_int` | `key`, `int_value` | Set mod state integer |
| `set_mod_float` | `key`, `float_value` | Set mod state float |
| `set_mod_bool` | `key`, `bool_value` | Set mod state boolean |
| `add_mod_int` | `key`, `int_value` | Add to mod state integer |
| `add_mod_float` | `key`, `float_value` | **api 1.1** — add to mod state float |
| `add_score` | `int_value`, `target` | Add points to target player(s) (negative = penalty) |
| `broadcast_event` | `event_broadcast`, `int_value` | Fire a named event (other triggers can chain on it) |
| `apply_effect` | `effect_id`, `effect_duration`, `effect_magnitude`, `target` | Apply an effect — replicated to all clients (**works since api 1.1**) |
| `kill_player` | `target` | **api 1.1** — kill the target player(s) |
| `teleport_player` | `target`, `key` (region id) or `x`/`y` | **api 1.1** — teleport target(s) to a region center or coordinates |
| `set_timer` | `float_value` | **api 1.1** — set the match clock (seconds) |
| `add_timer` | `float_value` | **api 1.1** — add/subtract match time |
| `end_match` | — | **api 1.1** — end the match through the normal flow |
| `announce` | `text`, `duration` | **api 1.1** — show text on every peer's `announcement` HUD widget |

### Action Targets

| Target | Description |
|--------|-------------|
| `"player"` | The triggering player |
| `"killer"` | Alias of `"player"` on kill events |
| `"victim"` | The secondary player (victim on `on_kill`, killer on `on_death`) |
| `"all"` | All players (humans and AI) |
| `"team"` | Everyone on the triggering player's team |

## Complete Example

See `demo/mod.json` for a fully-featured example mod using mechanics, effects, triggers, and custom state.
