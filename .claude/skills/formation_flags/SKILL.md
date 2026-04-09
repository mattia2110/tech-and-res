---
description: Guide for Victoria 3 military formation flag definitions. Use when creating, editing, or reviewing files in common/military_formation_flags, adding new army or navy identity icons, or checking links between formation flag keys and military icon assets.
name: Formation Flags
---

# Military Formation Flags

Use this skill for formation identity definitions in `common/military_formation_flags`.

## File Location

Write modded definitions in:
`common/military_formation_flags/*.txt`

Use vanilla references from:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\military_formation_flags\00_formation_flags.txt`

Related assets:
- `gfx/interface/icons/military_icons/military_formation_identity_icons/*.dds`

If the mod does not already have `common/military_formation_flags/`, create it.

## Workflow

1. Decide whether the icon is for an `army` or `navy` formation.
2. Reuse the vanilla naming pattern where possible, especially for bulk additions.
3. Confirm the icon path exists before writing the definition.
4. Keep the key stable; other script or UI references may point to it later.
5. Prefer adding new keys instead of overriding vanilla ones unless the task explicitly requires replacement.

## Structure

```victoria3
formation_flag_key = {
	icon = "gfx/interface/icons/military_icons/military_formation_identity_icons/example.dds"
	type = army
}
```

## Core Fields

- `icon`: Texture path for the formation identity icon. Use a valid `.dds` asset path.
- `type`: Category used by the game. Vanilla uses `army` and `navy`.

## Editing Guidance

- Keep paths absolute from the game root, matching vanilla style.
- Follow the existing asset directory unless the mod already has a separate icon convention.
- Do not use `army` icons for navy entries or the reverse unless the design is intentionally shared.
- If you add new icon files, verify they are included in the mod and named consistently with the definition key.

## Template

```victoria3
ztr_army_16 = {
	icon = "gfx/interface/icons/military_icons/military_formation_identity_icons/ztr_army_16.dds"
	type = army
}

ztr_fleet_16 = {
	icon = "gfx/interface/icons/military_icons/military_formation_identity_icons/ztr_fleet_16.dds"
	type = navy
}
```

## Review Checklist

- The file is in `common/military_formation_flags/`.
- The icon path exists and points at a `.dds`.
- `type` is `army` or `navy`.
- The key does not accidentally collide with an unrelated vanilla or modded entry.
- Any new icon assets are present in the mod package.
