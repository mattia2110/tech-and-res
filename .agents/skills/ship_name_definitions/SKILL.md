---
name: Ship Name Definitions
description: Guide for Victoria 3 ship name definitions. Use when creating, editing, or reviewing files in common/ship_name_definitions, especially when adding historical ship name pools, country-specific prefixes, allowed ship type lists, name_list properties, or ship naming localization.
---

# Ship Name Definitions

Use this skill for Victoria 3 files in `common/ship_name_definitions/*.txt`.

Pair it with:
- `Ship Types` when adding new ship types that need names
- `Localization Formatting` when adding ship name, prefix, or naming-format localization

## File Location

Write modded ship name definitions in:
- `common/ship_name_definitions/*.txt`

Inspect the local mod first:
- `common/ship_name_definitions/*.txt`
- `common/ship_types/*.txt`
- `common/country_definitions/*.txt`
- `common/laws/*.txt`
- `localization/english/*.yml`

Then compare against vanilla references:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\ship_name_definitions\*.txt`

## What These Definitions Do

Ship name definitions provide country-specific pools and naming formats for constructed ships. A definition usually selects a country, lists allowed ship types, and builds the final name from properties such as prefix text and a name list.

Vanilla often groups definitions by cultural or regional file, then creates separate pools for capital ships, cruisers, destroyers, torpedo craft, submarines, or broad fallback lists.

## Workflow

1. Inspect a vanilla country or region with a similar naming pattern.
2. Decide whether the new names belong in an existing regional file or a new country-focused file.
3. Set `country = TAG` to a valid country definition.
4. Include every relevant ship type under `allowed_ship_types`, including new modded ship types if they should draw from this pool.
5. Use `selection_weight` to prioritize specific lists over broader fallback lists.
6. Add localization for `ship_prefix_*`, `sn_*`, and any naming format keys used by `name`.

## Common Structure

```pdx
ship_names_historical_example_capital_ships = {
	name = "shipname_default_format"
	selection_weight = 10
	country = EXA

	allowed_ship_types = {
		ship_type_ship_of_the_line
		ship_type_dreadnought
	}

	properties = {
		{
			type = custom_text
			key = "PREFIX"
			custom_text = "ship_prefix_blank"
		}
		{
			type = name_list
			key = "SHIP_NAMESAKE"
			name_list = {
				sn_example_one sn_example_two
			}
		}
	}
}
```

## Field Guidance

- `name`: Naming format localization key, commonly `shipname_default_format`.
- `selection_weight`: Higher values are preferred when several definitions are valid.
- `country`: Country tag that can use the pool.
- `allowed_ship_types`: Ship type keys that draw from the pool.
- `properties`: Ordered name components consumed by the naming format.
- `type = custom_text`: Supplies fixed or conditional text such as a prefix.
- `quick_trigger_required_law`: Optional quick gate used by vanilla prefixes such as monarchy-only HMS-style prefixes.
- `type = name_list`: Supplies `sn_*` localization keys for ship namesakes.

## Review Checklist

- The definition lives in `common/ship_name_definitions/`.
- `country` references a valid country tag.
- `allowed_ship_types` includes all intended vanilla and modded ship types.
- Every `ship_prefix_*`, `sn_*`, and `name` format key has localization.
- Fallback lists have lower or broader `selection_weight` behavior than specific historical lists.
- Prefix laws and conditions match the intended government forms.
