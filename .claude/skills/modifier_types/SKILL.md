---
name: Modifier Types
description: Guide for Victoria 3 modifier type definitions. Use when creating, editing, or reviewing files in common/modifier_type_definitions, adding new modifier keys consumed by buildings, laws, scripted modifiers, static modifiers, mobilization options, or other common/ content, or checking display metadata and game_data behavior.
---

# Modifier Types

Use this skill for definitions in `common/modifier_type_definitions/`.

## File Location

Write modded definitions in:
- `common/modifier_type_definitions/*.txt`

Use references from:
- `common/modifier_type_definitions/ztr_bool_modifier_types.txt`
- `common/modifier_type_definitions/ztr_building_modifier_types.txt`
- `common/modifier_type_definitions/ztr_extraction_modifier_types.txt`
- `common/modifier_type_definitions/ztr_military_modifier_types.txt`
- `common/modifier_type_definitions/ztr_mr_modifier_types.txt`
- `common/modifier_type_definitions/ztr_new_goods_modifier_types.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\modifier_type_definitions\00_modifier_types.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\modifier_type_definitions\modifier_types.md`

Related consumers may live in many places, especially:
- `common/scripted_modifiers/*.txt`
- `common/static_modifiers/*.txt`
- `common/buildings/*.txt`
- `common/laws/*.txt`
- `common/technologies/*.txt`
- `common/mobilization_options/*.txt`

Search for the modifier key before and after editing. A modifier type without a consumer does nothing; a consumer without a matching modifier type usually displays badly or fails to behave as intended.

## Key Rule

The modifier key prefix determines where the modifier flows in the modifier graph. Choose the prefix deliberately.

Examples:
- `country_...`
- `state_...`
- `building_...`
- `unit_...`
- `character_...`

If the prefix is wrong, the modifier may apply in the wrong scope chain even if the rest of the definition looks valid.

## Workflow

1. Identify the gameplay consumer first: building, law, mobilization option, scripted modifier, or static modifier.
2. Inspect the closest vanilla or modded modifier type with the same scope family.
3. Name the key so its prefix matches the intended modifier flow.
4. Set display metadata: `decimals`, `color`, `percent`, and optional `prefix` or `suffix`.
5. Use `boolean = yes` only for true on/off flags.
6. Add `game_data` fields only when code or existing vanilla patterns actually use them.
7. Search the repo for every use of the final key to catch typos and stale references.

## Structure

```pdx
modifier_type_key = {
	decimals = 0
	color = good
	percent = yes
	prefix = "MONEY_PREFIX"
	suffix = "PER_CAPITA"

	game_data = {
		ai_value = 0
		translate = battle_offense_add
		type_set = { cultural_acceptance }
	}
}
```

Most custom modifier types only need a subset of these fields.

## Core Fields

- `decimals`: Display precision.
- `color`: UI signposting such as `good`, `bad`, or `neutral`.
- `percent`: Display value as a percentage without changing the stored script value.
- `prefix`, `suffix`: Optional display formatting tokens.
- `boolean`: Treat the modifier as an on/off flag. Only `yes` values make sense for consuming content.
- `game_data.ai_value`: AI valuation hook used by some systems.
- `game_data.translate`: Alternate modifier definition used by bespoke code paths.
- `game_data.type_set`: Tags the modifier into code-recognized sets used by specific systems.

Do not add `translate` or `type_set` speculatively. Follow a known vanilla pattern when using them.

## Mod Patterns

This mod already shows two useful extremes:

### Straightforward percent modifiers

`common/modifier_type_definitions/ztr_military_modifier_types.txt` defines many `unit_combat_unit_type_..._offense_mult` and `..._defense_mult` keys with:
- `decimals = 0`
- `color = good`
- `percent = yes`
- `game_data = { ai_value = 0 }`

Use that pattern for ordinary displayed percentage bonuses.

### Boolean capability flags

`common/modifier_type_definitions/ztr_bool_modifier_types.txt` defines keys like `country_allow_superpower_mechanics_bool` with:
- `boolean = yes`
- `color` chosen by whether enabling the flag is presented as good or bad

Use that pattern for script-facing enable or disable flags, not for numeric bonuses.

## Editing Guidance

- Keep similarly named modifier families together in the same file.
- Match the display style of neighboring vanilla or modded keys unless there is a clear reason not to.
- Prefer `percent = yes` for `_mult` style keys and plain numbers for `_add` style keys.
- Do not invent suffix or prefix tokens; reuse established ones from vanilla.
- If a modifier is only meant for script branching, a boolean type is usually clearer than an arbitrary numeric convention.

## Review Checklist

- The key prefix matches the intended modifier flow.
- The definition file grouping matches the mod's existing organization.
- Any consumer of the modifier uses the exact same key spelling.
- Display metadata matches the intended player-facing presentation.
- `boolean`, `translate`, and `type_set` are only used when justified by the design.
