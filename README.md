<p align="center">
  <img src="assets/banner.png" alt="PokeBedrock 3D Spawn Filter" width="100%"/>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/PokeBedrock-v4.4.2_beta-DC0A2D?style=for-the-badge&labelColor=1a1a2e" alt="PokeBedrock"/>
  <img src="https://img.shields.io/badge/BDS-1.26.14.1-f5f5f5?style=for-the-badge&labelColor=1a1a2e" alt="BDS"/>
  <img src="https://img.shields.io/badge/blocked-692_pokemon-3B4CCA?style=for-the-badge&labelColor=1a1a2e" alt="Blocked"/>
  <img src="https://img.shields.io/badge/license-MIT-FFDE00?style=for-the-badge&labelColor=1a1a2e" alt="License"/>
</p>

<br/>

<table>
<tr>
<td width="140" align="center">
  <img src="assets/sprite.png" alt="" width="100"/>
</td>
<td>

**What is this?** A drop-in behavior pack for Bedrock Dedicated Servers running [PokeBedrock](https://pokebedrock.com). It removes all Pokemon that only have 2D sprites — no 3D models — so your world stays fully immersive. 692 Pokemon filtered, verified by cross-referencing every entity file against the actual geometry definitions in `pokebedrock_models.geo.json`. Covers base forms, megas, Gigantamax, regional variants, alternate forms, and more.

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
│   Method 2: Tick scanner (every 3s, all dimensions)         │
│   ► Backup sweep for script-spawned entities that           │
│     bypass the spawn event — covers overworld, nether,      │
│     and the end                                             │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

<br/>

---

## Installation

Pick **one** of the two methods below. The `.mcpack` is easier if you're not comfortable with FTP.

---

### Option A: Download the `.mcpack` (easiest)

<p align="center">
  <a href="https://github.com/blochsam/pokebedrock-3d-spawn-filter/raw/master/PokeBedrock-3D-Spawn-Filter.mcpack">
    <img src="https://img.shields.io/badge/⬇_Download-PokeBedrock--3D--Spawn--Filter.mcpack-DC0A2D?style=for-the-badge&labelColor=1a1a2e" alt="Download .mcpack"/>
  </a>
</p>

1. Click the download button above (or [this direct link](https://github.com/blochsam/pokebedrock-3d-spawn-filter/raw/master/PokeBedrock-3D-Spawn-Filter.mcpack))
2. Rename the downloaded file from `PokeBedrock-3D-Spawn-Filter.mcpack` to `PokeBedrock-3D-Spawn-Filter.zip`
3. Unzip it — you'll get a folder called `PokeBedrock-3D-Spawn-Filter`
4. Upload that whole folder into your server's `behavior_packs/` directory via FTP or your host's file manager, so it ends up at `behavior_packs/PokeBedrock-3D-Spawn-Filter/`
5. Jump to **"Register the pack"** below

### Option B: Clone from GitHub

```bash
cd your_server/behavior_packs/
git clone https://github.com/blochsam/pokebedrock-3d-spawn-filter.git
```

Or download the repo as a ZIP from the green **Code** button on GitHub.

---

### Register the pack

Open `worlds/<your_world>/world_behavior_packs.json` and add this entry to the array:

```json
{
  "pack_id": "7cf770a8-b181-4291-8443-d5cf5e66626d",
  "version": [1, 3, 0]
}
```

> **Don't** remove your existing PokeBedrock entry — just add this one alongside it. The file should look something like:
> ```json
> [
>   { "pack_id": "your-pokebedrock-uuid-here", "version": [4, 4, 2] },
>   { "pack_id": "7cf770a8-b181-4291-8443-d5cf5e66626d", "version": [1, 3, 0] }
> ]
> ```

### Check permissions

Make sure `@minecraft/server` is allowed. Open `config/default/permissions.json` and confirm it contains:

```json
{
  "allowed_modules": ["@minecraft/server"]
}
```

If PokeBedrock is already working on your server, this is already set — you can skip this step.

### Restart and verify

Restart your server and look for this line in the console:

```
[Scripting] [3D Spawn Filter] v1.3.0 loaded - blocking 692 Pokemon without 3D models across all dimensions.
```

If you see that, you're good.

<br/>

---

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

See [`DENYLIST.md`](DENYLIST.md) for the full table of all 692 blocked Pokemon with their typeIds.

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

- Already-spawned 2D Pokemon get cleaned up within 3 seconds, or clear them manually with `/kill @e[type=pokemon:<name>]`
- This does **not** modify PokeBedrock — it's a standalone add-on that runs alongside it
- The tick scanner covers overworld, nether, and the end
- Future PokeBedrock updates may add 3D models for listed Pokemon. Remove them from the blacklist when that happens.
- **Not tested in PokeBedrock battles.** The filter removes entities from world spawns — we haven't verified whether it interferes with battle encounters. If you run into issues, let us know via [Issues](https://github.com/blochsam/pokebedrock-3d-spawn-filter/issues).
- **This was a weekend project and probably won't be updated.** The code is simple and easy to fork.

<br/>

## Changelog

### v1.3.0
Geometry-verified blacklist. Cross-referenced all 1,328 entity files against the 863 geometry definitions in `pokebedrock_models.geo.json` to produce a ground-truth blacklist. Removed 51 false positives (Pokemon like Alcremie, Ponyta, Rockruff, Goodra, and Noivern that actually have 3D models) and added 189 previously missed entries (Togekiss, Talonflame, Weavile, Ursaluna, all Silvally type forms, Ogerpon forms, Castform variants, and more). **692 total entries.**

### v1.2.0
Expanded blacklist to cover alternate forms of already-blocked Pokemon. Added mega evolutions (Absol, Altaria, Audino, Banette, Glalie, Manectric, Mawile, Medicham, Salamence), Rotom appliance forms, Gigantamax forms, legendary/mythical alternate forms (Kyurem Black/White, Necrozma forms, Calyrex forms, Deoxys forms, Therian forms, Zacian/Zamazenta Crowned), alternate forms (Gourgeist/Pumpkaboo sizes, Darmanitan Zen, Cramorant forms, Morpeko Hangry, Wishiwashi School), and the Starly + Fletchling evolution lines. **554 total entries.**

### v1.1.0
Initial release. Manual blacklist of 488 Pokemon identified as having only 2D sprites.

<br/>

## License

MIT. See [LICENSE](LICENSE).

This project is not affiliated with or endorsed by PokeBedrock or its developers.
