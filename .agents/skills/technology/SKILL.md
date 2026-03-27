---
name: Technology
description: Guide for Victoria 3 technology definitions. Use when creating, editing, or reviewing files in common/technology/technologies, especially when adding new technologies, overriding vanilla techs, or tracing how technology links to eras, production methods, buildings, laws, institutions, modifiers, AI, and localization.
---

# Technology

Use this skill for Victoria 3 files in `common/technology/technologies/*.txt`.

Do not use it for:
- era cost changes by themselves
- production method or building edits that only reference an existing tech
- localization-only or icon-only changes with no technology definition change

Pair it with:
- `Eras` when editing `common/technology/eras/*.txt`
- `Production Methods` when the tech unlocks or retunes PMs
- `Modifier Types` when the tech adds a modifier key that does not already exist
- `Institutions` when the tech changes institution caps or costs

## File Location

Write modded technology definitions in:
- `common/technology/technologies/*.txt`

Inspect the local mod first:
- `common/technology/technologies/*.txt`
- `common/technology/eras/*.txt`
- `common/production_methods/*.txt`
- `common/production_method_groups/*.txt`
- `common/buildings/*.txt`
- `common/laws/*.txt`
- `common/institutions/*.txt`
- `common/modifier_type_definitions/*.txt`
- `common/ai_strategies/**/*.txt`
- `common/scripted_effects/*.txt`
- `common/scripted_triggers/*.txt`
- `localization/english/*.yml`

Then compare against vanilla references:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\technology\technologies\10_production.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\technology\technologies\20_military.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\technology\technologies\30_society.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\technology\eras\00_eras.txt`

Current local pattern matters here:
- `ztr_new_*.txt` adds original techs
- `ztr_modified_vanilla_*.txt` overrides vanilla techs
- `ztr_modified_mr_*.txt` and `ztr_mr_*.txt` carry compatibility or alternate-tree edits

## What These Definitions Do

A technology definition is one node in the research graph. It usually decides:
- which era and category the tech belongs to
- which icon it uses
- which prerequisite techs unlock it
- which persistent modifiers it grants
- which one-time effects fire in `on_researched`
- how attractive it is to AI through `ai_weight`

Technology files sit in a wider `common/` network:
- `common/technology/eras/` sets the research-cost layer
- `common/production_methods/`, `common/buildings/`, and `common/laws/` usually do the actual unlocking by checking the tech key
- `common/institutions/` and `common/modifier_type_definitions/` support institution-cap or boolean tech effects
- AI, journal entries, scripted effects, and many other systems check `has_technology_researched = <key>`

If you add or rename a tech, search `common/` for:
- `has_technology_researched =`
- `unlocking_technologies =`
- `<tech_key>`

## Workflow

1. Decide whether the task is a new tech, a vanilla override, or a compatibility override.
2. Pick the era and category before tuning the effect package.
3. Map the prerequisite chain first so the tech sits cleanly in the local graph.
4. Check every building, production method, law, institution, or script that is supposed to unlock off the tech.
5. Use `modifier` for persistent effects and `on_researched` only for one-time scripted consequences.
6. Re-check AI, localization, icon paths, and graph connectivity after editing.

## Common Structure

```pdx
example_technology = {
	era = era_4
	texture = "gfx/interface/icons/invention_icons/example_technology.dds"
	category = society

	unlocking_technologies = {
		prerequisite_technology
	}

	on_researched = {
		custom_tooltip = {
			text = example_technology_tt
		}
	}

	modifier = {
		country_example_modifier_add = 1
	}

	ai_weight = {
		value = 1
	}
}
```

## Field Guidance

- Definition key: Keep it stable once other systems reference it.
- `era`: Must match a real era key from `common/technology/eras/`.
- `texture`: DDS icon path shown in the research UI.
- `category`: Vanilla categories are typically `production`, `military`, or `society`.
- `unlocking_technologies`: Prerequisite tech keys. This is the research graph.
- `modifier`: Persistent effects granted after research.
- `on_researched`: One-time scripted effects. Use it sparingly and make it safe for override chains.
- `ai_weight`: Country-scope AI evaluation block.

## Important Caution

Do not treat the comments inside technology files as the real unlock implementation.

In Victoria 3, comments like "Unlocks Railways" are documentation. The actual unlock usually lives in neighboring files such as:
- `common/buildings/*.txt`
- `common/production_methods/*.txt`
- `common/laws/*.txt`

If you add a tech but do not wire the consumers, the tech will research correctly but unlock nothing.

## Editing Guidance

- Keep new techs in the local file family that matches their category and ownership.
- Start from the nearest vanilla or local tech block instead of inventing a new shape.
- When overriding vanilla techs, compare the whole original block. Missing `on_researched`, `modifier`, or AI logic can silently change behavior.
- If the tech grants institution caps, boolean permissions, or new modifier keys, verify the modifier types and localization exist.
- Keep prerequisite chains readable. A tech tree with disconnected or circular logic is harder to debug than almost any individual modifier bug.

## Review Checklist

- The definition lives in `common/technology/technologies/`.
- `era`, `category`, and `texture` are valid.
- Every prerequisite tech exists.
- Every consumer that should unlock from the tech actually checks the tech key.
- `modifier` entries use real modifier names.
- Any `on_researched` effects are one-time safe and intentional.
- AI weights still make sense for the tech's role.
- Name, description, tooltip, and modifier localization exist where needed.
