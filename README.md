# PokeBedrock 3D Spawn Filter

A simple Bedrock Dedicated Server (BDS) behavior pack add-on that removes Pokemon without 3D models from spawning on your PokeBedrock server.

## The story

I run a small PokeBedrock server for friends. After installing PokeBedrock v4.4.2 beta, we noticed a bunch of Pokemon were spawning as flat 2D sprites instead of proper 3D models. It was immersion-breaking and honestly kind of ugly.

PokeBedrock's spawn system is baked into a 56MB obfuscated script, so you can't just delete spawn rules or entity files to stop them — the script ignores those changes and spawns them anyway. The solution: a separate behavior pack that listens for entity spawns and instantly removes any Pokemon on a blacklist.

This was built for **PokeBedrock v4.4.2 beta** running on **BDS 1.26.14.1**. It blocks 488 Pokemon (including regional variants like Alolan, Galarian, Hisuian, and Paldean forms) that lacked 3D models at the time of writing. Your mileage may vary on other versions.

**I probably won't be updating this** — it was a weekend project. But the blacklist is just a plain array in `scripts/main.js` that's easy to edit yourself.

## How it works

Two redundant methods to make sure nothing slips through:

1. **`entitySpawn` event** — catches and removes blacklisted Pokemon the instant they spawn
2. **Tick scanner (every 3s)** — backup sweep that catches any entities spawned programmatically by PokeBedrock's script (which may not trigger the entitySpawn event)

## Installation

1. **Download or clone** this repo into your server's `behavior_packs/` directory:
   ```
   your_server/
   └── behavior_packs/
       └── pokebedrock-3d-spawn-filter/   <-- this folder
           ├── manifest.json
           └── scripts/
               └── main.js
   ```

2. **Register the pack** by adding it to your world's `worlds/world/world_behavior_packs.json`:
   ```json
   {
     "pack_id": "a1b2c3d4-e5f6-4a7b-8c9d-0e1f2a3b4c5d",
     "version": [1, 0, 0]
   }
   ```
   Just add that object to the existing array. Don't remove your PokeBedrock entry.

3. **Make sure `@minecraft/server` is allowed** in your `config/default/permissions.json`:
   ```json
   {
     "allowed_modules": ["@minecraft/server"]
   }
   ```
   If you already have PokeBedrock running, this is probably already set.

4. **Restart your server.** You should see this in the console:
   ```
   [Scripting] [3D Spawn Filter] Loaded - blocking 488 Pokemon without 3D models.
   ```

## Editing the blacklist

Open `scripts/main.js` and find the `BLACKLIST` set near the top. Add or remove entries:

```js
const BLACKLIST = new Set([
  "pokemon:abomasnow",
  "pokemon:absol",
  // ... add or remove lines here
]);
```

Each entry is the entity's `typeId` in the format `"pokemon:<name>"`.

## Requirements

- PokeBedrock (tested with v4.4.2 beta)
- Bedrock Dedicated Server (tested with BDS 1.26.14.1)
- `@minecraft/server` module enabled
- Experiments: Upcoming Creator Features, GameTest, Data Driven Items

## Notes

- Existing Pokemon that spawned before installing this pack won't be removed until the tick scanner catches them (within 3 seconds) or you run `/kill @e[type=pokemon:<name>]`
- This does NOT modify PokeBedrock in any way — it's a standalone add-on that runs alongside it
- The blacklist was built for v4.4.2 beta. Future PokeBedrock updates may add 3D models for some of these Pokemon, at which point you'd want to remove them from the list
