# Demo Mod — Arena Showdown

A complete example mod demonstrating every feature of the JSON mod system.

## What It Shows

| Feature | JSON Config |
|---------|-------------|
| Teams (Red vs Blue) | `teams.enabled`, `teams.definitions` |
| Hand-built map (api 1.1) | `map.layout` text rows — every platform placed on purpose |
| Named regions for mechanics | `map.regions` (capture hill rect, hazard pits) |
| Dual win conditions | `win_conditions` (timer + team score target) |
| Team kill scoring + streaks | `scoring.type: "team_kills"`, `scoring.streaks` |
| Team-base spawning | `spawn.strategy: "team_base"` + `base_x`/`base_y` |
| HUD widgets | `hud` array (timer, team scores, team leaderboard, capture bar, kill feed, streaks, announcements) |
| Custom effects | `effects` array (Team Rally zone buff, Arena Fatigue on-kill debuff) |
| All four zone mechanics | `effect_zone`, `zone_control`, `hazard_zone`, `portal` |
| Trigger logic | `on_kill` counters + `apply_effect`, `on_interval` reminders, mod state |
| AI tuning | `ai` config (chase/stomp ranges) |
| 30-language localization | `name_french`, `description_koreana`, etc. (Steam API codes) |
| Custom background image | `background` block + `background.png` (api 1.1) |

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

The arena is fully hand-authored via `map.layout` (30×16 cells, drawn as
text rows — or paint it in the in-game Map Editor). The design:

```
  ###############################
  #                             #
  #P   ██                  ██  P#   ← portal gates, high by the perches
  #                             #
  # ○    ███            ███   ○ #   ← rally rings + high routes
  #                             #
  ###                         ###   ← side ledges
  #  B ┌────── HILL ──────┐ B   #   ← team bases (spawn platforms)
  # ███│    capture       │███  #
  #    │     zone         │     #
  # █  │ █            █   │  █  #   ← stepping blocks over the pits
  #GGGG│GG  ICE ARENA  GG│GGGGG #   ← ground (ice under the hill)
  #####▓▓▓##############▓▓▓#####   ← hazard pits (lava / acid)
  ###############################
```

- **Bases** (x3 / x26): spawn platforms; the Team Rally speed ring (○)
  floats overhead — jump up through it to grab the buff.
- **Portals** (x2 / x27, floating high by the side perches): leap in from
  a perch for cross-map travel, 3s cooldown.
- **Capture hill** (x12-17): hold the center to tick up `zone_progress`
  and score bonus points — the framed volume + HUD bar show live progress.
- **Hazard pits** (x8-10 / x19-21): periodic damage; hop the stepping
  blocks or take the high route.
- **Ice arena floor** (x13-16): low-traction fights on the hill.

## File Structure

```
demo/
├── mod.json          ← Complete game mode configuration (this is the entire mod)
├── background.png    ← Custom match backdrop (api 1.1)
└── thumbnail.png     ← Preview image (512x512 PNG, create your own)
```

No code, no DLLs, no build tools — one JSON file defines everything.
