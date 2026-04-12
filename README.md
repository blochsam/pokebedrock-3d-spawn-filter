<p align="center">
  <img src="assets/banner.png" alt="PokeBedrock 3D Spawn Filter" width="100%"/>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/PokeBedrock-v4.4.2_beta-DC0A2D?style=for-the-badge&labelColor=1a1a2e" alt="PokeBedrock"/>
  <img src="https://img.shields.io/badge/BDS-1.26.14.1-f5f5f5?style=for-the-badge&labelColor=1a1a2e" alt="BDS"/>
  <img src="https://img.shields.io/badge/blocked-488_pokemon-3B4CCA?style=for-the-badge&labelColor=1a1a2e" alt="Blocked"/>
  <img src="https://img.shields.io/badge/license-MIT-FFDE00?style=for-the-badge&labelColor=1a1a2e" alt="License"/>
</p>

<br/>

<table>
<tr>
<td width="140" align="center">
  <img src="assets/skin.png" alt="" width="100"/>
</td>
<td>

**What is this?** A drop-in behavior pack for Bedrock Dedicated Servers running [PokeBedrock](https://pokebedrock.com). It removes all Pokemon that only have 2D sprites — no 3D models — so your world stays fully immersive. 488 Pokemon filtered, including Alolan, Galarian, Hisuian, and Paldean regional forms.

</td>
</tr>
</table>

<br/>

## Backstory

I was setting up a PokeBedrock server for my friends and we kept running into flat 2D Pokemon spawning everywhere. Tried deleting spawn rules — didn't work. Tried renaming entity files — nope. Turns out PokeBedrock's entire spawn system lives inside a **56MB obfuscated JavaScript file** that ignores the filesystem entirely.

So I wrote a script that intercepts entity spawns and removes anything on a blacklist before you ever see it.

<br/>

## How it works

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│   Method 1: entitySpawn listener                            │
│   ► Catches and removes blacklisted Pokemon instantly       │
│                                                             │
│   Method 2: Tick scanner (every 3s)                         │
│   ► Backup sweep for script-spawned entities that           │
│     bypass the spawn event                                  │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

<br/>

## Quick start

<details open>
<summary><b>1 — Drop into behavior_packs</b></summary>
<br/>

```
your_server/
└── behavior_packs/
    └── pokebedrock-3d-spawn-filter/
        ├── manifest.json
        └── scripts/
            └── main.js
```

</details>

<details open>
<summary><b>2 — Register the pack</b></summary>
<br/>

Add to `worlds/world/world_behavior_packs.json`:

```json
{
  "pack_id": "a1b2c3d4-e5f6-4a7b-8c9d-0e1f2a3b4c5d",
  "version": [1, 0, 0]
}
```

Don't remove your PokeBedrock entry.

</details>

<details open>
<summary><b>3 — Verify permissions</b></summary>
<br/>

Make sure `@minecraft/server` is in `config/default/permissions.json`:

```json
{
  "allowed_modules": ["@minecraft/server"]
}
```

If PokeBedrock already works, this is already set.

</details>

<details open>
<summary><b>4 — Restart</b></summary>
<br/>

Look for this in the console:

```
[Scripting] [3D Spawn Filter] Loaded - blocking 488 Pokemon without 3D models.
```

</details>

<br/>

## Customizing the blacklist

Open `scripts/main.js` and edit the `BLACKLIST` set:

```js
const BLACKLIST = new Set([
  "pokemon:abomasnow",
  "pokemon:absol",
  // add or remove entries here
]);
```

Format: `"pokemon:<name>"` matching the entity typeId.

<br/>

## Requirements

| | |
|---|---|
| **Add-on** | [PokeBedrock](https://pokebedrock.com) v4.4.2 beta |
| **Server** | Bedrock Dedicated Server 1.26.14.1 |
| **Module** | `@minecraft/server` enabled |
| **Experiments** | Upcoming Creator Features, GameTest, Data Driven Items |

<br/>

## Notes

- Already-spawned 2D Pokemon get cleaned up within 3 seconds, or clear them with `/kill @e[type=pokemon:<name>]`
- This does **not** modify PokeBedrock — it's a standalone add-on that runs alongside it
- Future PokeBedrock updates may add 3D models for listed Pokemon. Remove them from the blacklist when that happens.
- **This was a weekend project and probably won't be updated.** The code is simple and easy to fork.

<br/>

## License

MIT. See [LICENSE](LICENSE).

This project is not affiliated with or endorsed by PokeBedrock or its developers.
