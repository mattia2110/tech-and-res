---
description: Guide for Victoria 3 fleet diorama definitions. Use when creating, editing, or reviewing files in gfx/map/fleet_dioramas, adding or overriding fleet, naval battle, or blockade dioramas, or checking how diorama locators attach entities from gfx/map/fleet_entities and naval combat unit data.
name: Fleet Diorama
---

# Fleet Diorama

Use this skill for naval diorama layout files. These are not stored in `common/`; they live under `gfx/map/fleet_dioramas`.

## File Location

Write modded fleet dioramas in:
`gfx/map/fleet_dioramas/*.txt`

Use vanilla references from:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\gfx\map\fleet_dioramas\fleet_dioramas.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\gfx\map\fleet_dioramas\naval_battle_dioramas.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\gfx\map\fleet_dioramas\blockade_dioramas.txt`

Check related data when needed:
- `gfx/map/fleet_entities/*.txt` for the actual spawned ship entities
- `common/combat_unit_groups/` for valid combat unit group keys
- `common/combat_unit_types/` for the unit types selected by fleet entity triggers

If the mod does not already have `gfx/map/fleet_dioramas/`, create it.

## Workflow

1. Identify whether the diorama is for `fleet`, `battle_side`, or `blockade`.
2. Copy the closest vanilla diorama with a similar composition instead of starting from zero.
3. Keep locator names clear and grouped by unit role, such as `light_ship_01_loc`.
4. Match `unit_composition` to the intended visual mix so the engine selects the right diorama for the formation.
5. Verify every `attach` block points at an existing locator and a valid `combat_unit_group`.
6. Check `gfx/map/fleet_entities/*.txt` if entity selection needs culture, DLC, or unit-type-specific behavior.

## Structure

```victoria3
fleet_diorama_key = {
	group = fleet
	distance = 0

	unit_composition = {
		combat_unit_group_light_ship = 1
		combat_unit_group_capital_ship = 1
		combat_unit_group_support_ship = 0
	}

	locator = {
		name = "light_ship_01_loc"
		position = { 0.0 0.0 -8.0 }
		rotation = { 90.0 0.0 0.0 }
		scale = 1.0
	}

	attach = {
		locator = light_ship_01_loc
		combat_unit_group = combat_unit_group_light_ship
		is_visible = { scope:num_units > 0 }
	}
}
```

## Core Fields

- `group`: Visual context. Valid vanilla groups are `fleet`, `battle_side`, and `blockade`.
- `distance`: Placement offset used by the renderer. Vanilla often leaves fleet and battle examples at `0`, but blockade and specialized layouts may need tuning.
- `unit_composition`: Relative weighting the selector uses when choosing a diorama for a formation. Ratios do not need to sum to 100.
- `locator`: Placement anchor for a spawned entity. `name` must be unique inside the diorama.
- `attach`: Connects a locator to a spawn rule.
- `combat_unit_group`: Optional, but normally required for ship attachments. It controls both unit counting and which combat unit type is exposed to fleet entity triggers.
- `is_visible`: Trigger for whether the attachment appears. Vanilla usually gates extra ships by `scope:num_units`.

## Trigger Semantics

Inside `attach.is_visible`:
- `scope:num_units` is the count of units in the listed `combat_unit_group`

Inside `gfx/map/fleet_entities/*.txt` triggers:
- `root` is the country
- `scope:combat_unit_type` is the selected combat unit type for that attachment

If a diorama looks correct but the wrong ship model appears, inspect `gfx/map/fleet_entities/*.txt` before changing locator data.

## Group Notes

### `fleet`

Use for the regular overworld fleet model near sea provinces.

### `battle_side`

Use for one side of a naval battle diorama. Vanilla battle-side layouts generally face the opposing fleet and often reuse similar visibility thresholds to normal fleet dioramas.

### `blockade`

Use for the blockade vignette near a port city. Validate `distance` and spacing more carefully because the scene is anchored differently from a sea-province fleet.

## Template

```victoria3
fleet_diorama_example = {
	group = fleet
	distance = 0

	unit_composition = {
		combat_unit_group_light_ship = 2
		combat_unit_group_capital_ship = 1
		combat_unit_group_support_ship = 1
	}

	locator = {
		name = "light_ship_01_loc"
		position = { -4.0 0.0 -6.0 }
		rotation = { 60.0 0.0 0.0 }
		scale = 1.0
	}

	locator = {
		name = "capital_ship_01_loc"
		position = { 0.0 0.0 8.0 }
		rotation = { -90.0 0.0 0.0 }
		scale = 1.0
	}

	attach = {
		locator = light_ship_01_loc
		combat_unit_group = combat_unit_group_light_ship
		is_visible = { scope:num_units > 0 }
	}

	attach = {
		locator = capital_ship_01_loc
		combat_unit_group = combat_unit_group_capital_ship
		is_visible = { scope:num_units > 0 }
	}
}
```

## Review Checklist

- The file is under `gfx/map/fleet_dioramas/`, not `common/`.
- `group` matches the intended scene type.
- Every locator name is unique and every `attach.locator` points to one of them.
- Every `combat_unit_group` key exists in `common/combat_unit_groups/`.
- Visibility thresholds scale sensibly with unit counts.
- The composition ratios match the intended fleet silhouette.
- Related fleet entity triggers still resolve to the desired ship models.
