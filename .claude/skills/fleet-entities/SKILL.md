---
name: fleet-entities
description: Guide for Victoria 3 fleet entity definitions. Use when creating, editing, or reviewing files in gfx/map/fleet_entities, adding or overriding ship visual variants, linking ship_type keys to base entities, VFX, locators, damage decals, or modular ship modification attachments, or debugging wrong naval models selected by fleet dioramas.
---

# Fleet Entities

Use this skill for Victoria 3 files in `gfx/map/fleet_entities/*.txt`.

Pair it with:
- `Fleet Diorama` when a diorama attach selects the wrong ship visual.
- `Ship Types` when adding or renaming `ship_type_*` hulls.
- `Ship Modification Slots` and `Ship Modifications` when wiring modular visual attachments.

## File Location

Write modded fleet entity definitions in:
- `gfx/map/fleet_entities/*.txt`

Use vanilla references from:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\gfx\map\fleet_entities\fleet_entities.md`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\gfx\map\fleet_entities\01_fleet_entities.txt`

Check related data when needed:
- `common/ship_types/*.txt` for valid `ship_type` keys.
- `common/ship_modification_slots/*.txt` for valid `slot_type` keys.
- `common/ship_modifications/*.txt` for expected modification levels.
- `gfx/models/**/*.asset` for base ship, VFX, and modification entity names.
- `gfx/map/fleet_dioramas/*.txt` for the scene attachments that request these visuals.

If the mod does not already have `gfx/map/fleet_entities/`, create it.

## What These Definitions Do

Fleet entities map a logical ship type to the rendered ship entity, its state VFX, damage decal positions, motion tuning, and modular attachment entities. They are the visual bridge between `common/ship_types`, fleet diorama attachment logic, and `.asset` entity definitions under `gfx/models`.

## Workflow

1. Inspect the closest vanilla fleet entity for the same hull era and role.
2. Confirm the `ship_type` key exists in `common/ship_types`.
3. Confirm every `entity` string exists in `.asset` files under `gfx/models`.
4. Use `default = yes` or omit `default` for the primary visual of a ship type; use `default = no` plus `trigger` only for selectable or conditional custom skins.
5. Match `weapon_type`, `hull_type`, `motion_intensity`, and `length` to the ship's era and combat silhouette.
6. Add `locator` blocks only for scriptable VFX anchors that are not already present on the base entity.
7. Keep every `attach.slot_type` aligned with the ship type's modification slots.
8. Ensure each `attach` has the same number and ordering of `modification` blocks as the slot's available visual levels.
9. Verify damage decals and VFX still sit on the hull after changing `entity`, `length`, or locator positions.

## Structure

```victoria3
ship_type_example = {
	ship_type = ship_type_example
	default = yes
	weapon_type = turret
	weapon_fire_interval = 0.1
	hull_type = steel
	motion_intensity = 0.7
	length = 5.0
	entity = "example_base_entity"

	locator = {
		name = loc_foam
		position = { 0.0 0.03 0.0 }
	}

	vfx_entity = {
		entity = "vfx_ship_example_foam_entity"
		locator = loc_foam
	}

	damage_decal_positions = {
		{ -0.1 0.04 1.5 0.0 }
		{ 0.0 0.05 1.0 0.0 }
		{ 0.0 0.055 1.5 0.0 }
		{ 0.05 0.045 1.2 0.0 }
	}

	attach = {
		locator = root
		slot_type = ship_mod_slot_guns
		modification = { entity = "example_guns_light_entity" disabled = yes }
		modification = { entity = "example_guns_medium_entity" disabled = yes }
		modification = { entity = "example_guns_heavy_entity" fire_angle = 90 }
	}
}
```

## Core Fields

- Top-level key: Usually mirrors the `ship_type` key, such as `ship_type_dreadnought`.
- `ship_type`: Required logical hull key from `common/ship_types`.
- `default`: Defaults to `yes`. If `yes`, `trigger` is ignored.
- `trigger`: Evaluated for non-default custom skins. `root` is the country; `scope:combat_unit_type` can be available from fleet diorama selection.
- `weapon_type`: Valid vanilla values are `none`, `cannon`, `turret`, and `torpedo`.
- `weapon_fire_interval`: Time between salvos when multiple attachments enter the fire state.
- `hull_type`: Valid vanilla values are `wood` and `steel`; affects damage visual selection.
- `motion_intensity`: Animation amplitude multiplier. Wooden ships are usually around `1.0`; steel ships are commonly steadier, around `0.7`.
- `length`: Ship length used for spacing in route graphics.
- `entity`: Base ship entity from `.asset` files.
- `locator`: Optional scriptable anchor with `name`, `position`, optional `rotation`, and optional `scale`.
- `vfx_entity`: Visual effect entity, optionally attached to the base ship and optionally assigned to a locator.
- `damage_decal_positions`: Four-value decal transform rows used by damage states.
- `modification`: Optional named reusable modification bundle for later `attach` blocks.
- `attach`: Binds a slot's current modification level to an entity or named modification at a locator.

## Attachment Rules

Inside `attach`:
- `locator` is usually `root` or a locator on the base model, but can target script-defined locators.
- `slot_type` must match a real ship modification slot.
- Each `modification` block corresponds to one level in that slot's visual progression.
- Empty `modification = { }` intentionally attaches nothing for that level.
- Use either `name` or `entity` inside a modification block, not both.
- Use `disabled = yes` for static pieces that do not need state changes.
- Use `fire_angle` when a combat state should rotate a mounted entity to fire at a specific yaw.

## Entity Lookup

Use ripgrep against vanilla and mod assets when validating entity names:

```powershell
rg -n "name = \"example_base_entity\"" "C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\gfx" gfx
```

Vanilla modular navy assets are commonly under:
- `gfx/models/military/navy_modular/`
- `gfx/models/military/navy/`

## Review Checklist

- The file is under `gfx/map/fleet_entities/`, not `common/`.
- The top-level key and `ship_type` point to the intended hull.
- Non-default skins have a meaningful `trigger`; default skins do not rely on `trigger`.
- Every quoted `entity` exists in vanilla or mod `.asset` files.
- Every `slot_type` exists and has the expected number of visual levels.
- Attachment order matches the slot's light, medium, high, or equivalent progression.
- VFX locators, model locators, and decal positions fit the selected base entity.
- Related fleet dioramas still attach the correct combat unit group and expose the expected ship type.
