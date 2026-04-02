---
name: Production Methods
description: Guide for Victoria 3 production method definitions. Use when creating, editing, or reviewing files in common/production_methods, especially when adding a new production method, overriding a vanilla PM, or tracing how a PM interacts with production method groups, buildings, goods, technology, laws, power bloc unlocks, and neighboring common/ content.
---

# Production Methods

Use this skill for Victoria 3 files in `common/production_methods/*.txt`.

Do not use it for:
- PMG-only wiring changes with no PM definition edits
- pure economic tuning discussion with no PM structure work
- localization-only edits with no PM gameplay changes

Pair it with:
- `Production Method Groups` when changing PM membership inside a slot
- `Production Method Balancing` when tuning inputs, outputs, and workforce math
- `Localization Formatting` when adding or fixing PM names and descriptions

## File Location

Write modded PM definitions in:
- `common/production_methods/*.txt`

Inspect the local mod first:
- `common/production_methods/*.txt`
- `common/production_method_groups/*.txt`
- `common/buildings/*.txt`
- `common/goods/*.txt`
- `common/laws/*.txt`
- `common/technology/**/*.txt`
- `common/power_bloc_principles/*.txt`
- `common/power_bloc_identities/*.txt`
- `common/scripted_triggers/*.txt`
- `common/scripted_effects/*.txt`
- `common/modifier_type_definitions/*.txt`
- `localization/english/*.yml`

Then compare against vanilla references:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\production_methods\production_methods.md`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\production_methods\*.txt`

## What These Definitions Do

A production method is one selectable option inside a production method group on a building. It usually decides:
- which goods the building consumes and produces
- which pops it employs or removes
- which building, state, or country modifiers apply while the PM is active
- which technologies, laws, principles, identities, or other PMs gate activation
- whether the PM is the default, hidden until valid, or auto-replaced by a stronger variant
- how much pollution or other side effects the PM creates

These files sit in a wider `common/` network:
- `common/production_method_groups/` defines which PMs compete in the same slot
- `common/buildings/` attaches PMGs to buildings and therefore decides where a PM can appear
- `common/goods/` supplies the good keys and base prices used by PM inputs and outputs
- `common/technology/` and `common/laws/` provide the most common unlock gates
- `common/power_bloc_principles/` and `common/power_bloc_identities/` can unlock special PM variants
- `common/scripted_triggers/` and `common/scripted_effects/` may consume PM keys or modifiers
- `localization/english/*.yml` provides player-facing PM text

If you change or add a PM, search `common/` and `localization/` for:
- the PM key itself
- `production_methods = {`
- `production_method_groups = {`
- `replacement_if_valid =`
- `unlocking_technologies =`
- `unlocking_principles =`
- `unlocking_identity =`

## Workflow

1. Inspect the PMG and building slot first so you know what the PM competes with.
2. Inspect the nearest current vanilla PM family and the nearest local mod PM family for the same building function.
3. Search the local mod for the PM key, PMG membership, building attachment, and any scripted references.
4. Decide whether the change belongs in the PM itself, the PMG, the building, the goods file, or the technology/law gate.
5. Copy the nearest working PM block and adapt it instead of starting from `production_methods.md`.
6. Re-check every good key, employment modifier, unlock requirement, texture path, and localization key after editing.

## Common Structure

```pdx
pm_example = {
	texture = "gfx/interface/icons/production_method_icons/example.dds"
	is_default = yes
	is_hidden_when_unavailable = yes
	replacement_if_valid = pm_example_upgrade

	disallowing_laws = {
		law_industry_banned
	}

	unlocking_technologies = {
		mechatronics
	}

	unlocking_principles = {
		principle_external_trade_2
	}

	building_modifiers = {
		workforce_scaled = {
			goods_input_tools_add = 5
			goods_input_electricity_add = 2
			goods_output_steel_add = 20
		}

		level_scaled = {
			building_employment_laborers_add = -1000
			building_employment_machinists_add = 300
		}
	}

	state_modifiers = {
		workforce_scaled = {
			state_pollution_generation_add = 5
		}
	}

	required_input_goods = electricity
	ai_weight = 1.0
	pollution_generation = 0.0
}
```

## Field Guidance

- Definition key: Must stay stable once referenced by PMGs, buildings, scripted logic, or localization.
- `texture`: PM icon path.
- `is_default`: Optional. If omitted everywhere in the group, the first PM in the PMG acts as default.
- `is_hidden_when_unavailable`: Useful for gated variants that should not clutter the UI before unlock.
- `replacement_if_valid`: Lets a PM auto-upgrade to a better PM when the replacement becomes legal.
- `country_modifiers`, `state_modifiers`, `building_modifiers`: Current vanilla heavily relies on these. Most PM economics live in `building_modifiers`.
- `workforce_scaled`: Usually holds goods input and output changes, scaled by staffing.
- `level_scaled`: Usually holds employment and other per-level effects.
- `unscaled`: Use only when the modifier should not scale with workforce or building level.
- `timed_modifiers`: Optional post-switch effects. Rare compared with the main modifier blocks.
- `required_input_goods`: Common on electrified or infrastructure-dependent PMs. Current vanilla uses keys like `electricity` here.
- `unlocking_laws`, `disallowing_laws`, `unlocking_technologies`, `unlocking_production_methods`, `unlocking_global_technologies`: Main activation gates.
- `unlocking_principles` and `unlocking_identity`: Power-bloc-gated PM variants.
- `ai_weight`: Base AI preference.
- `pollution_generation`: Extra visual pollution value. Separate from any state pollution modifier you may also add.

## Important Caution

Do not treat `production_methods.md` as a complete or current best-practice template by itself.

The note file is only a schema stub. Current vanilla and this mod rely on patterns it does not emphasize well:
- `replacement_if_valid` is used for principle-gated or upgraded PM variants
- `disallowing_laws` is widely used
- `is_hidden_when_unavailable` is common on gated alternates
- many PMs are not full standalone economic models; they are slot-specific deltas inside a PMG
- local mod files often use patch operators such as `REPLACE:`, `INJECT:`, or `REPLACE_OR_CREATE:`

Always verify a PM together with its PMG, building slot, and neighboring PMs before editing.

## Vanilla Patterns

Current vanilla PM files show these stable patterns:
- most economic PM logic lives in `building_modifiers`
- mining, infrastructure, and environmental PMs often add `state_modifiers` for pollution or infrastructure effects
- many electrified PMs require `required_input_goods = electricity`
- gated alternates often combine `replacement_if_valid`, `is_hidden_when_unavailable`, and `unlocking_principles`
- company and private-infrastructure PMs make heavy use of availability gating

Current mod patterns add important extra context:
- many PMs are split across thematic files such as `ztr_new_*`, `ztr_modified_*`, and `ztr_vanilla_*`
- automation PMs often change inputs and workforce without defining a full production chain on their own
- local PM overrides frequently depend on mod goods and later-era technologies that do not exist in vanilla

## Editing Guidance

- Start from the nearest vanilla or local PM family for the same slot.
- Treat a PM as part of a slot system, not an isolated block. Changing one PM can change the whole PMG's progression.
- Keep goods, employment, and unlock logic explainable. If you cannot summarize why the PM is stronger or different, the script is probably too opaque.
- Match the local override style in the file you are editing instead of switching patch operators casually.
- When adding a new PM, wire all relevant pieces together: the PM block, PMG membership, building attachment if needed, and localization.

## Review Checklist

- The definition lives in `common/production_methods/`.
- Every referenced good, law, technology, principle, identity, and PM key exists.
- The PM belongs to the intended PMG and building slot.
- `required_input_goods` only references goods that exist and are intended as hard activation gates.
- `replacement_if_valid` and hidden-alt PM logic still point to valid PM keys.
- The texture path is valid.
- Localization exists for the PM key.
- No outdated note-file assumptions were copied blindly when current vanilla or local PMs use a different pattern.
