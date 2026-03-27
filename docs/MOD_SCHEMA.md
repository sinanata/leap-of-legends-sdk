# mod.json — Complete Schema Reference

Every field, every option, every default. Copy what you need into your `mod.json`.

## Identity (required)

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `id` | string | **required** | Unique mod ID (reverse-domain: `com.yourname.modname`) |
| `name` | string | **required** | Display name shown in mode selection |

## Identity (optional)

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `version` | string | `"1.0.0"` | SemVer version |
| `api_version` | string | `"1.0"` | Framework compatibility |
| `author` | string | `""` | Creator name |
| `description` | string | `""` | Short description |
| `thumbnail` | string | `"thumbnail.png"` | Preview image filename |
| `tags` | string[] | `[]` | Searchable tags |
| `steam_workshop_id` | int | `0` | Set automatically after first Workshop upload |

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
  "regions": []
}
```

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `width` | int | `26` | Map width in cells |
| `height` | int | `16` | Map height in cells |
| `open_right` | bool | `false` | Right boundary open (for scrolling) |
| `open_left` | bool | `false` | Left boundary open |
| `open_top` | bool | `false` | Top boundary open |
| `water_height` | int | `2` | Water level (rows from bottom) |

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
| `cell_type` | string | `"air"`, `"rock"`, `"water"`, `"ice"` |

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
  { "type": "checkpoint_race", "target": 10 }
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
| `checkpoint_race` | `target` | Ends when any player hits `target` total checkpoints |

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
| `respawn_delay` | float | `2.0` | Seconds before respawn |
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
  "type": "fullmap",
  "padding": 1.0,
  "scroll_speed": 0.0
}
```

| Type | Description |
|------|-------------|
| `fullmap` | Orthographic overview of entire map |
| `follow` | Follows the local player |
| `scrolling` | Auto-scrolling camera |

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
Sorted player score list.

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
| `"none"` | Visual only, no gameplay effect |

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

### Zone Button (Crossroads-style)

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

Places effect-giving zones at specific positions. Players entering the zone receive the configured effect.

```json
{
  "type": "effect_zone",
  "positions": [10, 8, 20, 8, 15, 12],
  "effect_id": 1,
  "effect_duration": 5.0,
  "effect_magnitude": 1.5,
  "zone_radius": 1.5,
  "persistent": true,
  "one_shot": false
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
| `one_shot` | bool | `false` | Zone consumed after first use |

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

Dangerous areas that apply effects to players inside them. References map regions.

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

Declare custom replicated state fields. These are synced to clients and readable by HUD widgets.

```json
"mod_state": [
  { "key": "round_number", "type": "int", "default_value": 1 },
  { "key": "danger_level", "type": "float", "default_value": 0.0 },
  { "key": "overtime", "type": "bool", "default_value": 0 }
]
```

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `key` | string | — | Unique state key |
| `type` | string | `"int"` | `"int"`, `"float"`, `"bool"` |
| `default_value` | float | `0` | Initial value (cast to declared type) |

Read these values in HUD widgets via `counter_key`, `progress_key`, or in triggers via `key`.

## Lifecycle Triggers

Execute actions when game events occur. Powerful for mode-specific logic without code.

```json
"triggers": [
  {
    "event_name": "on_match_start",
    "actions": [
      { "action": "set_mod_int", "key": "round_number", "int_value": 1 },
      { "action": "broadcast_event", "event_broadcast": "game_started" }
    ]
  },
  {
    "event_name": "on_kill",
    "actions": [
      { "action": "add_mod_int", "key": "total_kills", "int_value": 1 },
      { "action": "add_score", "int_value": 2, "target": "killer" }
    ]
  },
  {
    "event_name": "on_custom_event",
    "custom_event": "checkpoint_reached",
    "actions": [
      { "action": "apply_effect", "effect_id": 1, "effect_duration": 3.0, "effect_magnitude": 1.5, "target": "player" }
    ]
  }
]
```

### Trigger Events

| Event | When | Available targets |
|-------|------|-------------------|
| `on_match_start` | Match begins | `all` |
| `on_match_end` | Match ends | `all` |
| `on_player_joined` | Player enters match | `player` |
| `on_player_left` | Player leaves match | `player` |
| `on_kill` | A player kills another | `killer`, `victim`, `all` |
| `on_death` | A player dies | `player` (the dead player) |
| `on_custom_event` | Named custom event fires | `player` |

### Trigger Actions

| Action | Fields | Description |
|--------|--------|-------------|
| `set_mod_int` | `key`, `int_value` | Set mod state integer |
| `set_mod_float` | `key`, `float_value` | Set mod state float |
| `set_mod_bool` | `key`, `bool_value` | Set mod state boolean |
| `add_mod_int` | `key`, `int_value` | Add to mod state integer |
| `add_score` | `int_value`, `target` | Add points to target player(s) |
| `broadcast_event` | `event_broadcast` | Fire a named event |
| `apply_effect` | `effect_id`, `effect_duration`, `effect_magnitude`, `target` | Apply effect to target |

### Action Targets

| Target | Description |
|--------|-------------|
| `"player"` | The triggering player |
| `"killer"` | The killer (on_kill only) |
| `"victim"` | The victim (on_kill only) |
| `"all"` | All players |
| `"team"` | All players on same team as triggering player |

## Complete Example

See `demo/mod.json` for a fully-featured example mod using mechanics, effects, triggers, and custom state.
