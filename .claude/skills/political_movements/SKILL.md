---
description: Guide for Victoria 3 political movement definitions. Use when creating, editing, or reviewing files in common/political_movements, especially when adding new movement_* definitions, overriding vanilla movements, or tracing how movements connect to movement categories, pop support factors, ideologies, laws, revolutions, and neighboring common/ content.
name: Political Movements
---

# Political Movements

Use this skill for Victoria 3 files in `common/political_movements/*.txt`.

Do not use it for:
- movement category definitions by themselves
- movement pop support factor definitions by themselves
- journal entry, event, or scripted logic that only checks whether a movement exists

Pair it with:
- `Political Movement Categories` when adding or changing `movement_category_*`
- `Political Movement Pop Support` when adding or changing `movement_support_*`

## File Location

Write modded movement definitions in:
- `common/political_movements/*.txt`

Inspect the local mod first:
- `common/political_movements/*.txt`
- `common/political_movement_categories/*.txt`
- `common/political_movement_pop_support/*.txt`
- `common/ideologies/*.txt`
- `common/laws/*.txt`
- `common/technology/**/*.txt`
- `common/script_values/*.txt`
- `common/journal_entries/*.txt`
- `common/on_actions/*.txt`
- `common/scripted_effects/*.txt`

Then compare against vanilla references:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\political_movements\political_movements.md`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\political_movements\00_ideological_movements.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\political_movements\01_religious_movements.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\political_movements\02_cultural_movement.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\political_movements\03_pan_national_movements.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\political_movements\04_country_specific_ideological_movements.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\political_movements\04_national_movements.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\political_movements\05_slave_revolt_movements.txt`

## What These Definitions Do

A political movement definition is the gameplay core for a movement type. It usually decides:
- when the movement can appear and disappear
- which ideology and agitators it attracts
- which pops are eligible to support it
- which support reasons are shown in the UI
- how much support eligible pops actually give it
- whether it can cause revolution or secession
- which factors increase or decrease its radicalism

These files sit at the center of a wider `common/` network:
- `common/political_movement_categories/` sets identity and support-threshold behavior shared across movement families
- `common/political_movement_pop_support/` defines reusable UI-facing support labels
- `common/ideologies/` supplies `ideology_*` and often IG ideology checks used by movement logic
- `common/laws/`, `common/technology/`, `common/journal_entries/`, and `common/script_values/` often drive triggers and weighting

If you add custom movements, search `common/` for:
- `create_political_movement`
- `any_political_movement`
- `every_political_movement`
- `is_political_movement_type`

## Workflow

1. Inspect the closest vanilla movement family before writing a new definition.
2. Decide whether the change belongs in the movement, its category, or its pop support factor definitions.
3. Reuse an existing `movement_category_*` and `movement_support_*` key unless the behavior or UI reason truly needs a new shared key.
4. Keep `creation_trigger` and `disband_trigger` aligned so the movement lifecycle is predictable.
5. Build support logic in layers: `pop_support_trigger` for eligibility, `pop_support_factors` for UI reasons, and `pop_support_weight` for the actual math.
6. Add `revolution` or `secession` only when the movement should genuinely be allowed to become insurrectionary.
7. Re-check adjacent `common/` files after editing so every referenced law, ideology, factor, script value, and tag actually exists.

## Common Structure

```pdx
movement_example = {
	category = movement_category_ideological

	ideology = ideology_example_movement
	character_ideologies = {
		ideology_reformer
	}

	creation_trigger = {
		has_technology_researched = nationalism
	}
	creation_weight = {
		value = 100
	}
	on_created = {
		add_movement_enthusiasm_modifier = yes
	}

	pop_support_trigger = {
		NOT = { is_pop_type = slaves }
	}

	pop_support_factors = {
		movement_support_high_literacy
		movement_support_high_urbanization
	}

	pop_support_weight = {
		add = {
			value = 1
			desc = "POP_BASE_SUPPORT"
		}
		multiply = {
			value = literacy_rate
			add = 0.10
			desc = "POP_LITERACY"
		}
	}

	revolution = {
		possible = {
			political_movement_support >= 0.05
		}
		weight = {
			value = 100
		}
		state_weight = {
			value = 1
		}
	}

	secession = {
		possible = {
			political_movement_support >= 0.05
			scope:clout <= 0
		}
		weight = {
			value = 1000
		}
	}

	additional_radicalism_factors = {}
}
```

## Field Guidance

- `category`: Shared movement category key from `common/political_movement_categories/`.
- `ideology`: Core movement ideology. This strongly shapes law alignment and supporting actors.
- `character_ideologies`: Common leader ideologies that support or lead the movement.
- `creation_trigger`: Country-scope gate for whether the movement can spawn.
- `creation_weight`: Country-scope weight against other eligible movements.
- `on_created`: Effect run when the movement is created. Vanilla often adds enthusiasm here.
- `disband_trigger`: Optional lifecycle stop condition. Use it when the movement should go away after the issue is resolved.
- `on_disbanded`: Optional follow-up effects when the movement disappears.
- `culture_selection_trigger` and `culture_selection_weight`: Use for cultural or pan-national movements that need a culture identity.
- `religion_selection_trigger` and `religion_selection_weight`: Use for religious movements that need a religion identity.
- `character_support_trigger`: Character-scope eligibility for agitators and leaders.
- `character_support_weight`: Character-scope relative attraction.
- `can_pressure_interest_group`: Optional extra gate for which IGs the movement can pressure.
- `pop_support_trigger`: Pop-scope eligibility. Keep this simple and structural.
- `pop_support_factors`: UI-facing `movement_support_*` labels only.
- `pop_support_weight`: Real support math. This is where most balance work lives.
- `revolution` and `secession`: Civil war behavior blocks. Current vanilla uses separate blocks, even though the note file shows `revolution/secession`.
- `forced_tags`: Optional tag selection inside a rebellion block when a specific country should form.
- `state_weight`: Inside rebellion blocks, controls which states lean toward the revolt.
- `target_fraction_of_states`: Optional rebellion-state share target, mostly used on identity-heavy movements.
- `law_enactment_radicalism_multiplier`: Multiplies radicalism changes from ongoing law enactment.
- `active_law_radicalism_multiplier`: Multiplies radicalism from currently active laws.
- `additional_radicalism_factors`: Extra activism or radicalism logic for special cases.

## Important Caution

Do not trust `political_movements.md` as a complete schema. Current vanilla `.txt` files also use fields such as:
- `disband_trigger`
- `on_disbanded`
- `can_cause_obstinance`
- `can_have_slave_supporters`

Always verify against the nearest current vanilla movement family before copying or omitting a field.

## Vanilla Patterns

Current vanilla movement files show these stable patterns:
- ideological movements usually skip culture and religion selection entirely
- religious, cultural, national, and pan-national movements rely heavily on identity selection and identity-weighted rebellion logic
- many `on_created` blocks add `add_movement_enthusiasm_modifier = yes`
- many `pop_support_weight` blocks start with the same suppression or bolstering stanza and then layer pop-type, literacy, urbanization, acceptance, or radicalism math
- `secession` often requires `scope:clout <= 0`, while `revolution` usually does not
- `forced_tags` appears only when rebellion outcome should map to a specific historical or scripted country

## Editing Guidance

- Copy the nearest vanilla movement family and trim it down instead of starting from the old note-file template.
- Keep lifecycle logic readable. If the movement forms because of one problem and disbands because of a different unrelated condition, that is usually a design mistake.
- Keep `pop_support_factors` in sync with the actual math in `pop_support_weight`. Do not show support reasons that the movement never uses.
- Put reusable UI reasons in `common/political_movement_pop_support/`, not duplicated desc logic in every movement.
- Put shared identity or threshold behavior in `common/political_movement_categories/`, not repeated in every movement.
- For identity movements, make sure `pop_support_trigger`, identity selection, and rebellion `state_weight` all point in the same direction.
- When overriding a vanilla movement, compare the entire original block. Missing one optional field can silently change how the movement behaves.

## Review Checklist

- The movement lives in `common/political_movements/`.
- `category` points to a valid `movement_category_*` key.
- `ideology` and every entry in `character_ideologies` exist.
- Every `movement_support_*` entry in `pop_support_factors` exists.
- Identity fields are only used when the movement category and design actually need them.
- `creation_trigger`, `disband_trigger`, and any `on_disbanded` effects form a coherent lifecycle.
- `revolution` and `secession` behavior are intentional, especially `possible`, `state_weight`, and any `forced_tags`.
- Every referenced law, technology, journal entry, script value, country tag, and scope path exists in neighboring `common/` content.
- New localization is added if the movement introduces new `desc` keys, new support factors, or any other new player-facing text.
