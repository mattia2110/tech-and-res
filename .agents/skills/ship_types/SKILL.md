---
name: Ship Types
description: Guide for Victoria 3 ship type definitions. Use when creating, editing, or reviewing files in common/ship_types, especially when adding naval hulls, assigning ship groups, wiring ship modification slots, tuning construction and materiel goods, setting obsolescence, or connecting ship types to ship name definitions.
---

# Ship Types

Use this skill for Victoria 3 files in `common/ship_types/*.txt`.

Pair it with:
- `Ship Groups` when assigning or creating `ship_group`
- `Ship Modification Slots` when changing slot structure
- `Ship Modifications` when adding selectable ship designer options
- `Ship Name Definitions` when new ship types need historical name pools
- `Localization Formatting` when adding or changing ship type text

## File Location

Write modded ship type definitions in:
- `common/ship_types/*.txt`

Inspect the local mod first:
- `common/ship_types/*.txt`
- `common/ship_groups/*.txt`
- `common/ship_modification_slots/*.txt`
- `common/ship_modifications/*.txt`
- `common/ship_name_definitions/*.txt`
- `common/goods/*.txt`
- `common/technology/technologies/*.txt`
- `common/modifier_type_definitions/*.txt`
- `localization/english/*.yml`

Then compare against vanilla references:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\ship_types\ship_types.md`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\ship_types\*.txt`

## What These Definitions Do

Ship types are naval hull definitions. They set the ship group, base stats, construction and materiel costs, technology unlocks, obsolescence logic, AI build preference, icons, and available modification slots.

They are the hub of the ship system: group keys come from `ship_groups`, slot keys come from `ship_modification_slots`, modification keys come from `ship_modifications`, and ship naming pools in `ship_name_definitions` must include any new type that should receive historical names.

## Workflow

1. Inspect the nearest vanilla ship type in the same group and era.
2. Confirm the intended `ship_group` exists or add it first.
3. Define the base ship stats, goods costs, unlock tech, icons, and obsolescence triggers.
4. If `use_modifications = yes` or omitted, wire every intended slot under `modifications`.
5. Ensure every listed modification has `type = <same_slot_key>`.
6. Add `default_modifications` for required starting choices or leave it to the global fallback intentionally.
7. Add the new ship type to relevant ship name definitions.
8. Verify goods, technologies, modifier keys, art paths, AI weight, and localization.

## Common Structure

```pdx
ship_type_example = {
	ship_group = ship_group_cruisers

	modification_construction_cost = 50
	can_be_flagship = yes

	is_obsolete = {
		has_technology_researched = example_later_tech
	}

	modifier = {
		ship_hit_points_max_add = 1200
		ship_crew_max_add = 500
		ship_movement_speed_add = 6.0
		ship_hull_damage_add = 20
		ship_armor_add = 10
		ship_blockade_strength_add = 100
	}

	construction_goods = {
		goods_input_iron_add = 500
		goods_input_engines_add = 100
	}

	materiel_goods = {
		goods_input_groceries_add = 1
		goods_input_ammunition_add = 0.1
	}

	distance_to_port_modifier = {
		ship_hull_damage_mult = -0.8
	}

	unlocking_technologies = { ironclad_tech }

	icon = "gfx/interface/icons/ships/ship_category/ship_group_cruisers.dds"
	profile_texture = "gfx/interface/icons/ships/ship_types/silhouette_example.dds"

	ai_weight = 1.5

	modifications = {
		ship_mod_slot_armor = {
			ship_mod_example_armor_light
			ship_mod_example_armor_medium
		}
		ship_mod_slot_guns = {
			ship_mod_example_guns_light
			ship_mod_example_guns_medium
		}
	}

	default_modifications = {
		ship_mod_slot_armor = ship_mod_example_armor_light
	}
}
```

## Field Guidance

- `ship_group`: Required. Must reference a valid ship group.
- `modifier`: Core ship stats, including hit points, crew, speed, damage, armor, range, detection, visibility, screening, readiness, and blockade strength.
- `construction_goods`: Goods cost when constructing the ship.
- `materiel_goods`: Goods cost while consuming naval materiel.
- `distance_to_port_modifier`: Penalties applied when the ship exceeds its maximum distance to port.
- `unlocking_technologies`: Optional list; if multiple entries are present, any one can unlock the ship type.
- `use_modifications`: Optional. Defaults to `yes`; set to `no` for fixed designs such as supply ships.
- `base_construction_cost`: Base construction cost not affected by modification levels.
- `modification_construction_cost`: Variable construction cost multiplied by total modification levels.
- `combat_power_multiplier`: Optional AI/combat evaluation multiplier.
- `can_be_flagship`: Optional. Use on ship types that should serve as fleet flagships.
- `is_obsolete` and `is_very_obsolete`: Trigger blocks used to mark outdated hulls as technology advances.
- `icon` and `profile_texture`: UI art paths.
- `ai_weight`: Country-scope AI build preference.
- `modifications`: Slot-to-modification mapping. Each listed modification must use the same slot in its own definition.
- `default_modifications`: Optional slot defaults; values must be present in the corresponding `modifications` list.

## Review Checklist

- The ship type lives in `common/ship_types/`.
- `ship_group` exists and fits the hull role.
- Required slots and listed modifications are internally consistent.
- `default_modifications` only names modifications available in that slot.
- Goods, technologies, modifiers, icons, and profile textures exist.
- Obsolescence triggers point to sensible successor technologies.
- Relevant ship name definitions include the new ship type.
- Localization exists for player-facing ship type text.
