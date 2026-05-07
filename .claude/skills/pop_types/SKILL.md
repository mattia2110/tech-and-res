---
name: Pop Types
description: Guide for Victoria 3 pop type definitions. Use when creating, editing, or reviewing files in common/pop_types, especially when adding a new pop profession, overriding a vanilla pop type, or tracing how a pop type interacts with production methods, laws, technology, scripted logic, localization, and other neighboring common/ folders.
---

# Pop Types

Use this skill for Victoria 3 files in `common/pop_types/*.txt`.

Do not use it for:
- production method staffing changes by themselves
- building balance that only changes `building_employment_<pop>_add`
- localization-only edits with no pop type definition changes

Pair it with:
- `Localization Formatting` when adding or fixing localization text

## File Location

Write modded pop type definitions in:
- `common/pop_types/*.txt`

Inspect the local mod first:
- `common/pop_types/*.txt`
- `common/production_methods/*.txt`
- `common/buildings/*.txt`
- `common/pop_needs/*.txt`
- `common/laws/*.txt`
- `common/technology/**/*.txt`
- `common/ideologies/*.txt`
- `common/script_values/*.txt`
- `common/scripted_triggers/*.txt`
- `common/scripted_effects/*.txt`
- `common/journal_entries/*.txt`
- `common/on_actions/*.txt`
- `localization/english/*.yml`

Then compare against vanilla references:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\pop_types\pop_types.md`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\pop_types\*.txt`

## What These Definitions Do

A pop type definition is the gameplay identity for a profession such as `engineers` or `capitalists`. It usually decides:
- the pop's icon and map/UI color
- baseline expected literacy, education access, and quality of life
- how strongly wages matter to its standard of living
- whether it can be unemployed and how it behaves when poor
- how politically engaged the pop tends to be
- which other pop types can qualify into it, and how quickly
- how portraits should be generated for that profession

These files sit in a wider `common/` network:
- `common/production_methods/` and `common/buildings/` create demand for the pop type through employment and workforce composition
- `common/pop_needs/` and market balance determine whether the pop can afford its expected lifestyle
- `common/laws/`, `common/technology/`, `common/ideologies/`, and scripted logic often affect literacy, discrimination, agitation, and qualification math
- `localization/english/*.yml` provides the pop name and `*_QUALIFICATIONS_DESC` text shown to players

If you change a pop type's qualification flow or economic profile, search `common/` for:
- `building_employment_<pop>_add`
- `is_pop_type = <pop>`
- `pop_type = <pop>`
- `<pop>_QUALIFICATIONS_DESC`

## Workflow

1. Inspect the nearest current vanilla pop type before writing or overriding anything.
2. Search the local mod for where the pop is employed, referenced, or checked in neighboring `common/` folders.
3. Decide whether the change belongs in the pop type itself or in production methods, laws, technology, or scripted logic instead.
4. Copy the nearest vanilla block and trim or extend it instead of starting from the old note-file template.
5. Keep qualification logic readable: define the entry gate, the positive growth factors, the favored-source pop types, and the decay case.
6. Re-check localization and every referenced pop type, trigger, script value, and `desc` key after editing.

## Common Structure

```pdx
example_pop = {
	texture = "gfx/interface/icons/pops_icons/example_pop.dds"
	color = hsv{ 0.10 0.40 0.75 }
	start_quality_of_life = 10
	wage_weight = 3
	paid_private_wage = yes
	literacy_target = 0.80
	education_access = 0.25
	dependent_wage = 0.5
	unemployment = yes
	unemployment_wealth = 10

	political_engagement_base = 0.5
	political_engagement_literacy_factor = 1.0
	political_engagement_mult = {
		value = 1
	}

	qualifications_growth_desc = "EXAMPLE_POP_QUALIFICATIONS_DESC"
	qualifications = {
		if = {
			limit = {
				is_peasant_under_serfdom = no
				literacy_rate > 0.2
			}
			add = {
				value = literacy_rate
				multiply = 10
				desc = "QUALIFICATIONS_LITERACY_FACTOR"
			}
			if = {
				limit = { is_pop_type = machinists }
				multiply = {
					value = 5
					desc = "QUALIFICATIONS_FAVORED_TYPE"
				}
			}
		}
		else = {
			value = {
				desc = "QUALIFICATIONS_INSUFFICIENT_LITERACY_DECAY"
				value = -2
			}
		}
	}

	portrait_age = {
		integer_range = {
			min = define:NPortrait|GRACEFUL_AGING_START
			max = define:NPortrait|GRACEFUL_AGING_END
		}
	}
	portrait_pose = { value = 0 }
	portrait_is_female = { always = yes }
}
```

## Field Guidance

- Definition key: Usually a vanilla profession key such as `engineers` or `capitalists`. Override carefully; a renamed key breaks every downstream reference.
- `texture`: UI icon path. Reuse the vanilla path unless you intentionally provide a custom icon asset.
- `color`: Profession color. Vanilla commonly uses `hsv{}` values rather than raw RGB blocks.
- `working_adult_ratio`: Optional workforce participation ratio. Only add it when the profession design genuinely needs custom workforce behavior.
- `start_quality_of_life`: Baseline quality of life expectation for the pop.
- `wage_weight`: How strongly wages drive the pop's wealth and behavior.
- `paid_private_wage`: Whether private buildings directly pay this pop type.
- `literacy_target`: Expected literacy level for the profession.
- `education_access`: Optional extra education access. Current vanilla uses it on educated professions such as `engineers`.
- `consumption_mult`: Optional goods-consumption multiplier. Do not add it unless you intend to change broad balance.
- `dependent_wage`: Wage contribution from dependents.
- `unemployment`: Whether the pop type can exist unemployed.
- `unemployment_wealth`: Optional wealth threshold where unemployed pops fall back into this profession.
- `political_engagement_base` and `political_engagement_literacy_factor`: Baseline and literacy-driven political engagement.
- `political_engagement_mult`: Scripted modifier block. Use it for dynamic engagement factors such as starvation or technology-driven agitation.
- `qualifications_growth_desc`: Localization key shown in the UI for the profession's qualification growth explanation. Prefer this exact field name; current vanilla `.txt` files use it.
- `qualifications`: Scripted value block that controls promotion and decay. This is the most important balance section.
- `portrait_age`, `portrait_pose`, `portrait_is_female`: Portrait-generation rules. Current vanilla often uses an age range based on `define:NPortrait|GRACEFUL_AGING_*`.

## Important Caution

Do not trust `pop_types.md` as a complete schema or best-practice template by itself. It is a lightweight note file and lags behind current vanilla usage.

Current vanilla `.txt` files also show important patterns not captured well by the note file:
- `qualifications_growth_desc` is used explicitly
- `education_access` appears on some professions
- portrait setup often uses `integer_range` with portrait defines, not a fixed age
- qualification blocks usually include favored-source pop types and an explicit decay case

Always verify against the nearest current vanilla profession before copying, deleting, or inventing a field.

## Vanilla Patterns

Current vanilla pop type files show these stable patterns:
- educated urban professions such as `engineers` and `capitalists` gate qualifications on literacy or wealth
- favored-source pop types are usually represented with `is_pop_type = ...` checks inside `qualifications`
- `political_engagement_mult` often starts at `value = 1` and layers conditional `add` or `multiply` blocks
- localization for qualification growth uses uppercase keys such as `ENGINEERS_QUALIFICATIONS_DESC`
- portrait age usually uses the graceful aging define range rather than a hardcoded number

## Editing Guidance

- Start from the closest vanilla profession and keep the block shape familiar unless you have a design reason to diverge.
- Treat pop type edits as economy-wide changes. A stronger qualifications block can be more impactful than a large staffing edit in one building file.
- Keep qualification math explainable. If you cannot summarize why a pop qualifies into the role, the script is probably too opaque.
- Prefer moving demand-side balance into `common/production_methods/` and supply-side identity into `common/pop_types/`.
- When overriding a vanilla pop type, compare the entire original block. Dropping an optional field can silently change AI and economic behavior.
- If the profession depends on literacy, discrimination, or technology progression, verify those assumptions in neighboring `common/` files instead of embedding guesses into the pop type.

## Review Checklist

- The definition lives in `common/pop_types/`.
- Every referenced pop type inside `qualifications` exists.
- Every `desc` key used inside scripted values exists or already exists in vanilla localization.
- `qualifications_growth_desc` has matching localization.
- The profession is actually demanded somewhere relevant in `common/production_methods/` or other employment-driving content.
- Any change to literacy, education access, or wages is intentional relative to laws, technology, and building staffing.
- Portrait fields use valid syntax and sensible values.
- No old `pop_types.md` placeholder syntax was copied blindly when the nearest current vanilla `.txt` does something different.
