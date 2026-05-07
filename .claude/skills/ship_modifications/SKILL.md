---
name: Ship Modifications
description: Guide for Victoria 3 ship modification definitions. Use when creating, editing, or reviewing files in common/ship_modifications, including the common typo ship_modifciations, especially when adding armor, guns, propulsion, range, or utility options for ship types.
---

# Ship Modifications

Use this skill for Victoria 3 files in `common/ship_modifications/*.txt`.

Do not create `common/ship_modifciations/`; that is a typo. The real folder is `common/ship_modifications/`.

Pair it with:
- `Ship Modification Slots` when adding or changing slot keys
- `Ship Types` when exposing modifications on a ship type
- `Production Method Balancing` only if the task asks for economic cost balancing
- `Localization Formatting` when adding or changing modification names and descriptions

## File Location

Write modded ship modification definitions in:
- `common/ship_modifications/*.txt`

Inspect the local mod first:
- `common/ship_modifications/*.txt`
- `common/ship_modification_slots/*.txt`
- `common/ship_types/*.txt`
- `common/goods/*.txt`
- `common/technology/technologies/*.txt`
- `common/script_values/*.txt`
- `common/modifier_type_definitions/*.txt`
- `localization/english/*.yml`

Then compare against vanilla references:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\ship_modifications\ship_modifications.md`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\ship_modifications\*.txt`

## What These Definitions Do

Ship modifications are selectable ship designer options. They attach to a single slot through `type = <ship_modification_slot>` and can change combat stats, ship costs, materiel usage, technology unlocks, and AI preference.

Most vanilla families use tiered keys such as light, medium, and high for the same ship type and slot. Utility modifications usually live with the utility modification file and are exposed through utility slots on ship types.

## Workflow

1. Inspect the closest vanilla modification family for the same ship type and slot.
2. Confirm the slot exists in `common/ship_modification_slots`.
3. Add or edit the modification, keeping modifier values and goods costs comparable to neighboring tiers.
4. Add the modification key to every intended ship type under the matching slot.
5. Add `incompatible_with` only for options that must not coexist across slots or utility choices.
6. Check technology keys, goods keys, modifier keys, icon paths, sounds, AI weights, and localization.

## Common Structure

```pdx
ship_mod_example_armor_medium = {
	type = ship_mod_slot_armor
	icon = "gfx/interface/icons/military_icons/navy_icons/ship_designer_icons/ship_designer_armor_mid.dds"
	clicksound = "event:/SFX/UI/ShipDesigner/shipdesigner_modification_armor_medium"

	modifier = {
		ship_armor_add = 7
		ship_hit_points_max_add = 150
	}

	construction_goods = {
		goods_input_iron_add = 10
	}

	materiel_goods = {
		goods_input_ammunition_add = 0.1
	}

	unlocking_technologies = { ironclad_tech }

	incompatible_with = ship_mod_example_other

	ai_weight = {
		value = 2
		multiply = ship_mod_market_multiplier
	}
}
```

## Field Guidance

- `type`: Required. Must match a valid ship modification slot key.
- `icon`: UI art for the modification. Vanilla ship designer icons are the safest baseline.
- `clicksound`: Optional but commonly present in vanilla modification families.
- `modifier`: Ship stat changes such as armor, hit points, speed, accuracy, carrying capacity, and combat damage.
- `construction_goods`: Extra goods cost for building or retrofitting the ship.
- `materiel_goods`: Extra goods consumed when the ship uses materiel.
- `unlocking_technologies`: Optional list; if multiple entries are present, any one can unlock the modification.
- `incompatible_with`: Repeatable. Use exact modification keys.
- `ai_weight`: Country-scope AI preference. Vanilla often multiplies by script values such as `ship_mod_market_multiplier`.

## Review Checklist

- The modification lives in `common/ship_modifications/`.
- `type` references an existing slot.
- Every ship type that should use it lists it under the matching slot.
- Goods, technologies, modifiers, icons, sounds, and script values exist.
- AI weight is in the same scale as nearby alternatives.
- Localization exists for visible modification keys.
