---
description: Guide for Victoria 3 harvest condition definitions. Use when creating, editing, or reviewing files in common/harvest_condition_types, adding seasonal, disaster, or agricultural state conditions, or checking how harvest conditions interact with modifiers, terrain, geographic regions, icons, and scripted triggers.
name: Harvest Condition Types
---

# Harvest Condition Types

Use this skill for state-level harvest and disaster conditions in `common/harvest_condition_types`.

## File Location

Write modded definitions in:
`common/harvest_condition_types/*.txt`

Use vanilla references from:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\harvest_condition_types\00_harvest_condition_types.txt`

Related files often include:
- `localization/*harvest_conditions*.yml`
- `common/geographic_regions/` for seismic or climate gating
- `common/terrain/` for `incompatible_terrain`
- `gfx/interface/icons/harvest_condition_icons/*.dds`
- `gfx/map/map_painting/harvest_conditions_map_patterns/*.dds`

If the mod does not already have `common/harvest_condition_types/`, create it.

## Workflow

1. Decide whether the condition is negative, positive, or a scripted disaster.
2. Copy the closest vanilla condition and trim it down.
3. Keep `trigger` and `time` narrow so the condition only rolls where it should.
4. Set incompatibilities deliberately; many conditions need to block or replace nearby weather states.
5. Tune `range`, `duration`, `intensity`, and `chance` together rather than in isolation.
6. Add icon and localization entries before finishing.

## Structure

```victoria3
harvest_condition_type_key = {
	trigger = { always = yes }
	time = { always = yes }

	incompatible_with = other_harvest_condition_type_key

	range = {
		integer_range = {
			min = 0
			max = 2
		}
	}

	duration = {
		fixed_range = {
			min = 5
			max = 10
		}
	}

	modifier = {
		state_infrastructure_mult = -0.05
	}

	intensity = {
		fixed_range = {
			min = 1
			max = 3
		}
	}

	chance = {
		value = 0.001
	}

	color = { 166 67 0 }
	icon = "gfx/interface/icons/harvest_condition_icons/example.dds"
	graphics = none
	incompatible_terrain = { terrain_mountains }
	map_texture = "gfx/map/map_painting/harvest_conditions_map_patterns/harvest_conditions_bad.dds"
}
```

## Scope Notes

These blocks are evaluated for the state being checked:
- `trigger`
- `time`
- `chance`
- `intensity`

Vanilla regularly uses state-scope checks such as:
- `is_in_geographic_region = geographic_region_ring_of_fire`
- `region = sr:region_indonesia`
- `has_harvest_condition = drought`
- `s:STATE_LUZON ?= this`

If logic is reused across several conditions, move it to `common/scripted_triggers/` or `common/script_values/`.

## Core Fields

- `trigger`: Base eligibility check for the state.
- `time`: Additional time-window gating. Default behavior is effectively always valid if omitted.
- `incompatible_with`: Repeated key listing conditions that cannot coexist with this one.
- `range`: How far the condition spreads from its epicenter.
- `duration`: Lifetime in weeks.
- `modifier`: Effects applied while active.
- `intensity`: Multiplier applied to the numerical effects in `modifier`.
- `chance`: Roll chance when the condition is eligible.
- `color`: UI map color.
- `icon`: Player-facing icon path.
- `graphics`: Map visual override such as `drought`, `flood`, `frost`, `wildfire`, or `none`.
- `incompatible_terrain`: Terrain types where map graphics should not appear.
- `map_texture`: Pattern texture used in the map mode.

## Localization Pattern

At minimum, add:
- `<harvest_condition_key>`
- `<harvest_condition_key>_desc`

Vanilla uses pairs such as `drought` and `drought_desc`.

## Editing Guidance

- Keep incompatibility lists symmetric when two conditions should mutually exclude each other.
- Use comments sparingly to document intentional replacements or unusual balancing rules.
- Prefer small modifier packages with clear direction rather than stacking many weak effects.
- Positive conditions should not silently outclass the penalties from major disasters unless that is intentional.
- If a condition relies on a geographic region, verify that region already exists or create it first.

## Template

```victoria3
ztr_example_condition = {
	trigger = {
		is_in_geographic_region = geographic_region_oceania
	}

	time = {
		game_date > 1900.1.1
	}

	incompatible_with = drought

	range = {
		integer_range = {
			min = 1
			max = 2
		}
	}

	duration = {
		fixed_range = {
			min = 24
			max = 48
		}
	}

	modifier = {
		building_group_bg_agriculture_throughput_add = -0.05
	}

	intensity = {
		fixed_range = {
			min = 2
			max = 4
		}
	}

	chance = {
		value = 0.0015
	}

	color = { 85 65 8 }
	icon = "gfx/interface/icons/harvest_condition_icons/drought.dds"
	graphics = drought
	map_texture = "gfx/map/map_painting/harvest_conditions_map_patterns/harvest_conditions_bad.dds"
}
```

## Review Checklist

- The file is in `common/harvest_condition_types/`.
- Every referenced harvest condition, geographic region, terrain key, and icon path exists.
- `trigger`, `time`, `chance`, and `intensity` use valid state-scope logic.
- `range`, `duration`, and `chance` are balanced together.
- Incompatibilities match the intended coexistence rules.
- Name and description localization keys exist.
- `graphics` and `map_texture` match the visual intent.
