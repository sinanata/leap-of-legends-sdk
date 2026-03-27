# Mod Security

Leap of Legends uses a **data-driven modding system** where mods are pure JSON
configuration files. This architecture eliminates entire categories of security risks.

## Why JSON Mods Are Safe

Mods contain **zero executable code**. A mod is a JSON file that configures the game's
built-in systems. The game interprets the data — it never executes mod-provided code.

This means mods **cannot**:
- Access the file system
- Open network connections
- Execute processes or commands
- Generate or load code at runtime
- Call native (P/Invoke) functions
- Use reflection to bypass restrictions
- Create threads
- Access cryptographic APIs

There is no assembly loading, no IL scanning, and no security dialog needed — because
there is nothing to scan. JSON data is inherently safe.

## What Mods CAN Do

Mods configure the game's existing systems through JSON:

- Define map layouts (dimensions, regions, cell types)
- Set win conditions (timer, score targets, team scores)
- Configure scoring rules (kills, resources, captures)
- Define HUD widgets (timer, scores, leaderboards, messages)
- Set spawn strategies (random, team-based, maximin distance)
- Configure camera modes (fullmap, follow, scrolling)
- Tune AI behavior (chase range, stomp range, behaviors)
- Define teams (count, colors, base positions)
- Activate built-in mechanics (collectibles, flags, zone buttons)
- Define custom effect types (using the reserved 0x40-0xFF range)

## Platform Behavior

| Platform | Mod Source |
|----------|-----------|
| Windows/Mac | StreamingAssets/Mods/ + Steam Workshop |
| iOS/Android | Bundled official mods only |

Mobile platforms only load official game modes that ship with the game.
Desktop platforms load JSON mods from the local mods folder and Steam Workshop.

## Steam Workshop Trust

Even though JSON mods are inherently safe, Steam Workshop provides additional trust:
- Author identity through Steam accounts
- Community reporting for inappropriate content
- Valve's content moderation tools

## Previous Approach

Earlier versions of the SDK used a DLL-based modding system with IL-level assembly
scanning and security confirmation dialogs. This was replaced with the JSON-based
system to eliminate version conflicts, compilation requirements, and security risks.
