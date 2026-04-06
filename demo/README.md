# Demo Mod — Arena Showdown

A complete example mod demonstrating every feature of the JSON mod system.

## What It Shows

| Feature | JSON Config |
|---------|-------------|
| Teams (Red vs Blue) | `teams.enabled`, `teams.definitions` |
| Custom map with 4 regions | `map.regions` (bases, arena, ice patch) |
| Dual win conditions | `win_conditions` (timer + team score target) |
| Team kill scoring + streaks | `scoring.type: "team_kills"`, `scoring.streaks` |
| Team-based spawning | `spawn.strategy: "team_zones"`, `spawn.zones` |
| Full-map camera | `camera.type: "fullmap"` |
| 5 HUD widgets | `hud` array (timer, team scores, streak, count, message) |
| Custom effects | `effects` array (Team Rally, Arena Fatigue) |
| AI tuning | `ai` config (chase/stomp ranges) |
| 30-language localization | `name_french`, `description_koreana`, etc. (Steam API codes) |

## How to Use

1. Copy this folder to your game's mods directory:
   ```
   <Game>/LeapOfLegends_Data/StreamingAssets/Mods/com.example.arena-showdown/
   ```
2. Launch the game — the mod appears in mode selection
3. Play!

## How to Create Your Own Mod

1. Copy `mod.json` to a new folder named with your mod ID
2. Change `id`, `name`, and `author`
3. Modify any gameplay settings
4. Only include fields you want to change — defaults apply for everything else

## Map Layout

```
                    30 columns
  +------+----------+------+----------+------+
  |      | corridor |ARENA | corridor |      |
  | RED  |  left    |CENTER|  right   | BLUE |
  | BASE | (air)    |(air) | (air)    | BASE |
  |      |          | ice  |          |      |
  +------+----------+------+----------+------+
  |              water (2 rows)               |
  +-------------------------------------------+
```

## File Structure

```
demo/
├── mod.json          ← Complete game mode configuration (this is the entire mod)
└── thumbnail.png     ← Preview image (512x512 PNG, create your own)
```

No code, no DLLs, no build tools — one JSON file defines everything.
