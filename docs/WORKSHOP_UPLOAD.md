# Steam Workshop Upload Guide

How to create, test, and publish your mod on Steam Workshop.

## Prerequisites

- Leap of Legends installed on Steam
- A text editor (any will do — VS Code, Notepad++, even Notepad)

No build tools, SDKs, or compilers needed.

## Step 1: Create Your Mod

1. Create a folder named with your mod ID: `com.yourname.mymod/`
2. Create `mod.json` inside it (copy from `demo/mod.json` and customize)
3. Add a `thumbnail.png` (512x512 recommended)

Your mod folder:
```
com.yourname.mymod/
├── mod.json          ← Game mode configuration (required)
├── thumbnail.png     ← Preview image (recommended)
├── background.png    ← Custom match backdrop (optional, api 1.1)
├── music.ogg         ← Custom soundtrack (optional, api 1.1)
└── voice/            ← Voice-nudge overrides (optional, api 1.1)
```

Everything in the folder uploads with the Workshop item — subscribers get
your images and audio automatically.

### mod.json Checklist

Before uploading, verify:
- `id` is globally unique (reverse-domain: `com.yourname.modname`)
- `name` is clear and descriptive
- `version` follows SemVer (`"1.0.0"`)
- `description` is filled in
- `tags` are relevant (e.g., `"pvp"`, `"teams"`, `"casual"`)
- `steam_workshop_id` is `0` for first upload (updated automatically after)
- JSON is valid (no trailing commas, proper quoting)
- **Localization** (optional but recommended): add `name_french`, `description_german`, etc. for international reach. Uses Steam API language codes — see [MOD_SCHEMA.md](MOD_SCHEMA.md#localization) for the full list

## Step 2: Local Testing

1. Find the game's mods folder:
   - **Windows**: `<Steam>/steamapps/common/LeapOfLegends/LeapOfLegends_Data/StreamingAssets/Mods/`
   - **macOS**: `<Steam>/steamapps/common/LeapOfLegends/LeapOfLegends.app/Contents/Resources/Data/StreamingAssets/Mods/`
   - **Linux**: `<Steam>/steamapps/common/LeapOfLegends/LeapOfLegends_Data/StreamingAssets/Mods/`

2. Copy your mod folder there (e.g., `Mods/com.yourname.mymod/`) — the
   **Open Mods Folder** button on the mode selection screen jumps straight
   there

3. Launch the game — your mod appears in the mode selection screen

4. Iterate without restarting: edit `mod.json`, then press **Reload Mods**
   on the mode selection screen. If the JSON is broken, your mod shows as a
   red card with the exact error (line/column + hint); questionable config
   (unknown types, bad references, oversized state) shows a warnings badge —
   hover it for the list, or check the log for `[ModRegistry]`

5. Test thoroughly:
   - Single player with AI bots
   - Local multiplayer (if applicable)
   - Online multiplayer with a friend (mod state, announcements and effects
     replicate to joiners; friends need the mod installed — invites deep-link
     them to your Workshop page if they don't have it)
   - Edge cases: all players dead, ties, disconnects

## Step 3: Upload via In-Game UI

1. Launch Leap of Legends
2. Go to **Online Multiplayer**
3. Click **Steam Workshop**
4. Select your mod and click Upload
5. The game creates a new Workshop entry via `SteamUGC.CreateItem()`
6. After upload, `mod.json` is updated with the `steam_workshop_id`

## Step 4: Workshop Item Settings

After uploading, visit your item on Steam Workshop to:
- Add a detailed description with screenshots
- Set visibility (Public / Friends-Only / Hidden)
- Add change notes for updates
- Manage tags and categories

## Updating Your Mod

1. Edit your `mod.json`
2. Increment `version`
3. Upload again via in-game UI — it detects the existing `steam_workshop_id` and updates

## Troubleshooting

### Mod doesn't appear in game
- Look at the mode selection screen (All or Community filter): broken mod
  folders show a red "Failed to load" card with the exact reason — JSON
  syntax errors include line and column
- Check that `id` field is present and not empty
- Check the game log for `[ModRegistry]` messages
- Ensure the folder is directly inside `StreamingAssets/Mods/` (not nested deeper)
- If it declares `api_version` `2.0`+ it won't load on current game builds

### Mod loads but plays wrong
- Check the warnings badge on your mod's card first — the in-game validator
  flags unknown types, misspelled anchors, broken region/effect references,
  missing state keys and more, with the JSON path of each problem
- Double-check win_conditions — did you forget to include `"type": "timer"`?
- Verify team definitions have correct `id` values (1, 2, etc.)
- Map regions: ensure min < max for both x and y

### Invalid JSON errors
- No trailing commas after the last item in arrays/objects
- All strings must be double-quoted
- No comments in JSON (use `description` field for notes)
- Number values don't use quotes

## Tips

- Start from the demo `mod.json` and modify — don't write from scratch
- Only include fields you want to change — defaults handle the rest
- Use a 512x512 PNG thumbnail with clear text
- Test with AI bots before multiplayer testing
- Write a good Workshop description with screenshots
- Respond to user feedback on your Workshop page
