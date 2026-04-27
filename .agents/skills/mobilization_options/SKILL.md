---
name: Mobilization Options
description: Guide for Victoria 3 mobilization option definitions. Use when creating, editing, or reviewing files in common/mobilization_options, adding new army mobilization options, overriding vanilla options, checking triggers and effects, or wiring options to mobilization option groups, technologies, laws, goods costs, and AI behavior.
---

# Mobilization Options

Use this skill for definitions in `common/mobilization_options/`.

## File Location

Write modded definitions in:
- `common/mobilization_options/*.txt`

Use references from:
- `common/mobilization_options/ztr_new_mobilization_option.txt`
- `common/mobilization_options/ztr_vanilla_mobilization_option.txt`
- `common/mobilization_option_groups/ztr_mobilization_option_groups.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\mobilization_options\00_mobilization_option.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\mobilization_options\mobilization_options.md`

Related files often include:
- `common/mobilization_option_groups/*.txt` for `group = ...`
- `common/technologies/*.txt` for `unlocking_technologies`
- `common/laws/*.txt` for `unlocking_laws`
- `common/modifier_type_definitions/*.txt` when introducing new modifier keys used in `unit_modifier`
- `localization/english/*.yml` for tooltips referenced by `custom_tooltip`
- `gfx/interface/icons/mobilization_options/*` for `texture`

## Scope And Defaults

Vanilla documents the main script scopes as:
- `root = country`
- `scope:military_formation = military formation`

`military_formation_type = army` is the default and can usually be omitted.

## Workflow

1. Decide whether the task is a new option or a vanilla override.
2. Inspect the nearest existing option in the same feature family before writing anything.
3. Verify the target `group = ...` already exists in `common/mobilization_option_groups/`.
4. Gate the option with the smallest necessary set of requirements:
   `unlocking_technologies`, `unlocking_laws`, `possible`, and optional market supply checks.
5. If the option conflicts with another option in the same ladder, make the exclusivity explicit with `has_mobilization_option`.
6. Keep activation and deactivation penalties symmetric unless design intentionally differs.
7. Check every goods key and modifier key for exact spelling before finishing.
8. If overriding vanilla, follow the existing mod pattern such as `REPLACE:mobilization_option_key = { ... }`.

## Core Structure

```pdx
mobilization_option_example = {
	texture = "gfx/interface/icons/mobilization_options/example.dds"
	group = supplies

	unlocking_technologies = {
		technology_example
	}

	unlocking_laws = {
		law_example
	}

	is_shown = { }
	possible = { }
	can_be_turned_off = { }

	on_activate = { }
	on_deactivate = { }
	on_activate_while_mobilized = { }
	on_deactivate_while_mobilized = { }

	upkeep_modifier = {
		goods_input_example_add = 1
	}

	upkeep_modifier_unscaled = {
		goods_input_small_arms_mult = 0.2
	}

	unit_modifier = {
		unit_offense_mult = 0.1
	}

	ai_weight = {
		value = 1
	}
}
```

## Common Patterns In This Mod

### Vanilla overrides

`common/mobilization_options/ztr_vanilla_mobilization_option.txt` overrides base-game keys with `REPLACE:`. Use that when the task is truly replacing vanilla behavior, not when adding a parallel new option.

### Mutually exclusive ladders

This mod repeatedly uses `scope:military_formation = { NOR = { has_mobilization_option = ... } }` to prevent selecting paired upgrades at the same time, such as:
- close air support vs supersonic close air support
- indirect air support vs supersonic indirect air support
- rocket artillery vs guided missiles
- first aid vs field hospitals vs penicillin medical support

### Market supply gating

Many options in this mod require the market to have potential supply for the goods they consume:

```pdx
market ?= {
	mg:aeroplanes ?= { has_potential_supply = yes }
	mg:light_fuel ?= { has_potential_supply = yes }
}
```

Use this when an option should not even be selectable without an available supply chain.

### Switching penalties

Many military equipment options apply organization or morale penalties when toggled, especially while mobilized. Reuse that pattern when changing equipment in the field should carry friction.

### Cost scaling

This mod uses both:
- `upkeep_modifier` for flat goods inputs
- `upkeep_modifier_unscaled` for percentage-based costs tied to existing mobilization load

Inspect the vanilla override file before deciding which one matches the intended design.

## Field Guidance

- `group`: Required. Must match a key in `common/mobilization_option_groups/`.
- `texture`: UI icon path for the option.
- `unlocking_technologies`: At least one listed technology is required.
- `unlocking_laws`: At least one listed law is required.
- `is_shown`: Hide the option entirely unless this passes.
- `possible`: Show the option but make it unavailable unless this passes.
- `can_be_turned_off`: Prevent disabling the option under specific conditions.
- `on_activate`, `on_deactivate`: Effects when the option is toggled.
- `on_activate_while_mobilized`, `on_deactivate_while_mobilized`: Effects when changed during active mobilization.
- `upkeep_modifier`, `upkeep_modifier_unscaled`: Goods cost while active and mobilized.
- `unit_modifier`: Active military effects. Ensure every modifier key exists.
- `ai_weight`: Script value multiplier for the AI's activation logic.

## Editing Guidance

- Prefer copying the closest vanilla or modded option and trimming it down over starting from a blank block.
- Keep `possible` readable. If the trigger grows large, move reusable logic into scripted triggers or effects.
- Match the goods consumed in `upkeep_modifier` with the goods checked in market supply gating.
- Avoid creating an upgrade path that can stack with its own earlier tier unless that is intentional.
- If a custom tooltip is used anywhere, verify the localization key exists.

## Review Checklist

- The option is in the intended file and uses the correct add or override pattern.
- `group = ...` points to a real mobilization option group.
- Every technology, law, goods key, and modifier key exists.
- Trigger scopes match `root` country and `scope:military_formation` expectations.
- Mutually exclusive options cannot be enabled together unless intended.
- Activation and deactivation penalties match the design.
- Any custom tooltip localization exists.
