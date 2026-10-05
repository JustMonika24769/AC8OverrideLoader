<div align="center">

# AC8 Override Loader

**A general-purpose UE4SS IoStore override container loader for ACE COMBAT 8**

Version `0.2.0` · Game version `1.1.2.0` · Unreal Engine `5.4`

[简体中文](README.md) | [English](README_EN.md)

</div>

---

## Overview

AC8 Override Loader temporarily permits unsigned custom containers after the game mounts the official `pakchunk0-Windows.utoc`, then recursively discovers every `.utoc` file under `AC8OverrideLoader\payloads`.

The loader does not restrict asset types. A valid UE5.4 override container may include data tables, Blueprints, textures, materials, models, audio, and other cooked assets.

> [!IMPORTANT]
> The internal signatures and ABI are currently verified only against ACE COMBAT 8 `1.1.2.0`. After a game update, wait for compatibility to be confirmed before using the loader.

## Installation

### 1. Prepare the environment

- Install **UE4SS 3.0.1 Beta #0**.
- Completely remove or disable the old `IoStoreLoaderMod`, the AC8 PGM-specific loader, and any other loader that mounts the same assets.

### 2. Install the loader

Extract the release archive into the `ACE COMBAT 8` game root and allow its `Game` directory to merge with the existing one.

Then add or enable the loader in:

```text
Game\Binaries\Win64\ue4ss\Mods\mods.txt
```

```text
AC8OverrideLoader : 1
```

### 3. Add mod containers

Place each mod's matching `.utoc`, `.ucas`, and optional `.pak` files in a separate subdirectory:

```text
Game\Binaries\Win64\ue4ss\Mods\AC8OverrideLoader\
└── payloads\
    └── MyMod\
        ├── MyMod_P.utoc
        ├── MyMod_P.ucas
        └── MyMod_P.pak
```

| File | Requirement |
| --- | --- |
| `.utoc` | Required; the loader discovers containers through this file |
| `.ucas` | Required and must have the same base name as the `.utoc` file |
| `.pak` | Optional; if included in the distribution, keep the same base name and directory |

> [!NOTE]
> Traditional Pak mods that contain only a `.pak` file without a matching `.utoc/.ucas` pair are outside the scope of this loader.

## Load Order

The loader sorts containers by their **full paths, case-insensitively**, then mounts them using order values starting at `1000`. To make the order explicit, prefix each subdirectory with a number:

```text
payloads\
├── 0010_BaseMod\...
└── 0020_OverrideMod\...
```

Mods that override the same `/Game/...` path will conflict. The mod mounted later will usually take priority.

## Log

The log file is located at:

```text
Game\Binaries\Win64\ue4ss\Mods\AC8OverrideLoader\AC8OverrideLoader.log
```

Each successfully mounted container should report:

```text
custom mount code=0
```

## Important Notes

- This loader bypasses the **signature requirement for unsigned custom IoStore containers**. It does not decrypt the game's original containers.
- Custom containers should be standard, unencrypted UE5.4 IoStore sets. Extracting encrypted original assets still requires a legally obtained AES key.
- Use this loader only for offline single-player campaigns and development testing. Do not use it in multiplayer, online services, leaderboards, or processes with EAC enabled.
- The loader does not modify the game's original `pakchunk0-Windows.*` files.

## Uninstallation

1. Delete or move every `payloads` subdirectory that depends on this loader.
2. Remove or disable `AC8OverrideLoader` in `mods.txt`.
3. Delete the entire `AC8OverrideLoader` folder.

## Related Tooling

Use the separate `AC8OverrideToolkit` for asset extraction, modification, packaging, and Legacy -> IoStore -> Legacy round-trip validation. The Toolkit is not required if you already have ready-to-use containers.

## License and Third-Party Components

This project is licensed under the [MIT License](LICENSE). The native loader uses MinHook and is derived from the IoStoreLoaderMod reference implementation. See [Third-party notices](THIRD_PARTY.md) for details.
