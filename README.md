<p align="center">
  <img src="assets/skin.png" alt="Sam's Minecraft skin" width="200"/>
</p>

<h1 align="center">PokeBedrock 3D Spawn Filter</h1>

<p align="center">
  <em>Because nobody wants flat Pokemon ruining the vibes.</em>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/PokeBedrock-v4.4.2_beta-red?style=flat-square" alt="PokeBedrock version"/>
  <img src="https://img.shields.io/badge/BDS-1.26.14.1-green?style=flat-square" alt="BDS version"/>
  <img src="https://img.shields.io/badge/Pokemon_blocked-488-blue?style=flat-square" alt="Pokemon blocked"/>
  <img src="https://img.shields.io/badge/maintained%3F-nah-yellow?style=flat-square" alt="Maintained?"/>
</p>

---

## What is this?

A drop-in behavior pack for Bedrock Dedicated Servers running [PokeBedrock](https://pokebedrock.com). It removes all the Pokemon that only have 2D sprites (no 3D models) so your world doesn't look like someone taped trading cards to the ground.

**488 Pokemon filtered** — including Alolan, Galarian, Hisuian, and Paldean regional forms.

## The backstory

I was setting up a PokeBedrock server for my friends and we kept running into flat 2D Pokemon spawning everywhere. Tried deleting spawn rules. Didn't work. Tried renaming entity files. Nope. Turns out PokeBedrock's entire spawn system lives inside a **56MB obfuscated JavaScript file** that ignores everything on disk.

So I wrote a script that just... waits for them to spawn and immediately deletes them. Sometimes brute force is the answer.

> *"What are they gonna do, throw me in Boat Jail?"*
> — the 2D Pokemon, probably

## How it works

Two methods, because these flat guys are persistent:

**`entitySpawn` listener** — catches and removes blacklisted Pokemon the instant they appear

**Tick scanner (every 3s)** — backup sweep for entities spawned by PokeBedrock's script engine, which sometimes bypasses the spawn event

## Quick start

**1.** Drop this folder into your server's `behavior_packs/` directory:

```
your_server/
└── behavior_packs/
    └── pokebedrock-3d-spawn-filter/
        ├── manifest.json
        └── scripts/
            └── main.js
```

**2.** Register the pack in `worlds/world/world_behavior_packs.json` — add this to the array:

```json
{
  "pack_id": "a1b2c3d4-e5f6-4a7b-8c9d-0e1f2a3b4c5d",
  "version": [1, 0, 0]
}
```

**3.** Make sure `@minecraft/server` is in your `config/default/permissions.json`:

```json
{
  "allowed_modules": ["@minecraft/server"]
}
```

(If PokeBedrock already works on your server, this is already set.)

**4.** Restart. Look for this in the console:

```
[Scripting] [3D Spawn Filter] Loaded - blocking 488 Pokemon without 3D models.
```

You're done. Go catch some *real* Pokemon.

## Customizing the blacklist

Open `scripts/main.js` and find the `BLACKLIST` set at the top. Add or remove entries:

```js
const BLACKLIST = new Set([
  "pokemon:abomasnow",
  "pokemon:absol",
  // ... your list here
]);
```

Format is `"pokemon:<name>"` matching the entity typeId.

## Requirements

- [PokeBedrock](https://pokebedrock.com) (tested with **v4.4.2 beta**)
- Bedrock Dedicated Server (tested with **BDS 1.26.14.1**)
- `@minecraft/server` module enabled
- Experiments enabled: Upcoming Creator Features, GameTest, Data Driven Items

## Good to know

- Already-spawned 2D Pokemon get cleaned up within 3 seconds, or nuke them with `/kill @e[type=pokemon:<name>]`
- This does **not** modify PokeBedrock in any way — it's a standalone add-on
- Future PokeBedrock updates may add 3D models for some of these Pokemon. When that happens, just remove them from the blacklist
- **I probably won't be updating this.** It was a weekend project. But the code is simple and easy to fork.

## License

MIT — do whatever you want with it. See [LICENSE](LICENSE) for details.

This project is not affiliated with or endorsed by PokeBedrock or its developers.

---

<p align="center">
  <em>Built with mild frustration and a lot of FTP uploads by <a href="https://github.com/blochsam">@blochsam</a></em>
</p>
