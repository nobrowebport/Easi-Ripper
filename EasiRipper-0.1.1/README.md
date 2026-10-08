# EasiRipper

Rip models, textures, sounds and animations out of **Unity**, **GoldSrc**, **Source** and **Source 2** games and
export them ready for **Blender, Godot, Unreal, Unity, Roblox or Source**.

Run `EasiRipper.exe` for the window, or `easiripper-cli.exe --help` for the command line.

## What it reads

| Game engine | Files | What comes out |
|---|---|---|
| **Unity** (Mono and IL2CPP) | game folder / `.exe` / `.assets` / bundles | characters with skeleton, skin weights, textures and their animation clips (legacy + Mecanim generic); every texture, sprite, sound, text asset and mesh |
| Unity, decompiled | game folder | a full Unity project with C# scripts, scenes and prefabs (runs [AssetRipper](https://github.com/AssetRipper/AssetRipper)) |
| **Source** (HL2, Portal 2, TF2, GMod, CS:GO ...) | `.mdl` (+ `.vvd` `.vtx`), `.bsp`, `.vmf`, `.vtf`, `.vpk` | models with bones, weights, skins and every sequence; maps with textures, displacements and static props |
| **GoldSrc** (Half-Life 1, CS 1.6 ...) | `.mdl` (v10), `.bsp` (v30), `.wad` | models with bones and sequences (incl. `T.mdl` / `01.mdl` files), textured maps, textures |
| **Source 2** (CS2, HL:Alyx, Dota 2 ...) | `.vpk`, `*_c` files | models with skeletons and animations, materials (runs [Source2Viewer CLI](https://github.com/ValveResourceFormat/ValveResourceFormat)) |
| glTF | `.glb` / `.gltf` | re-export for another engine |

Textures for Source models and maps come from the game they sit in: point it at a file inside the game's folder
(EasiRipper finds `gameinfo.txt` and reads the VPKs). Loose files outside a game come out untextured.

## Exporting for

| Target | Files | Import |
|---|---|---|
| Blender | `.glb` | File > Import > glTF 2.0 - bones, weights, every animation as an Action |
| Godot | `.glb` | copy into the project |
| Unreal | `.glb` | Content Browser > Import (UE 5) - skeletal mesh + animation sequences |
| Unity | `.glb` + `.obj` | `.obj` imports as is; for bones/animations add **glTFast** (`com.unity.cloud.gltfast`) |
| Roblox | `.gltf` (+ `.rbxmx` for Source maps) | Studio 3D Importer. Meshes are split under 20,000 triangles, textures cut to 1024 px. Source maps also become Parts with spawns, buttons, doors and Portal 2's I/O logic. |
| Source | `.smd` + `.qc` + `.vtf`/`.vmt` | compile the `.qc` with studiomdl (or Crowbar) |

## Helper tools (optional)

EasiRipper finds these in a `tools` folder next to it, or in your Downloads:

* **AssetRipper** - only for *Unity > Decompile to a Unity project*.
* **Source2Viewer CLI** (`cli-windows-x64.zip`) - for Source 2 games.

## Run from source

```
python -m pip install -r requirements.txt
python EasiRipper.py                      # window
python EasiRipper.py game.exe -t roblox   # command line
python tests/test_goldsrc.py
```

## Build the exe

```
python -m pip install -r requirements.txt pyinstaller
python build.py
```

Output: `dist/EasiRipper/` (zip it up as the release). GitHub Actions does the same on every tag (`.github/workflows/build.yml`).

### About the "Windows protected your PC" warning

That's SmartScreen, and it appears for **any** new exe that isn't code-signed. No code change removes it.
What does:

1. **Sign the exe.** Open-source projects can get free signing from the **SignPath Foundation**
   (signpath.org); Azure Trusted Signing costs about $10/month. Signed builds stop showing the red screen as the
   certificate builds reputation.
2. Until then, publish builds through GitHub Releases from CI (so people can see they come from the source) and
   tell users to click *More info > Run anyway*.
3. If an antivirus flags it, upload it to the vendor's false-positive form (Microsoft: Submit a file for malware
   analysis). The build is a folder (`--onedir`) rather than a single self-extracting exe, which antivirus engines
   flag far less often.

## Limits (honest list)

* Unity **humanoid** (Mecanim muscle) animations need Unity's retargeting and are skipped; legacy and generic clips work.
* Source models with animations stored in external `.ani` files: those sequences are skipped.
* Source maps: brush faces, displacements and static props; dynamic props / entities are not placed (the Roblox export places Portal 2 test elements).
* GoldSrc: chrome textures get flat UVs; only the first sub-model of each body group is exported.
* Source 2 needs the Source2Viewer CLI (see above).

## Credits

Unity reading by [UnityPy](https://github.com/K0lb3/UnityPy) (MIT). Unity project decompiling by
[AssetRipper](https://github.com/AssetRipper/AssetRipper) (GPL-3, run as a separate program, not bundled).
Source 2 by [ValveResourceFormat](https://github.com/ValveResourceFormat/ValveResourceFormat) (MIT, run as a
separate program). Format knowledge from the Valve SDKs, Crowbar and the Valve Developer Wiki.

Only rip games you own, and respect the game makers' rights when you share what you ripped.
