---
description: Guide for Victoria 3 institution definitions. Use when creating, editing, or reviewing files in common/institutions, adding new institution types, overriding vanilla institutions, or checking how institution keys connect to laws, technology modifiers, AI strategy institution scores, modifier types, icons, and localization.
name: Institutions
---

# Institutions

Use this skill for institution type definitions in `common/institutions`.

## File Location

Write modded definitions in:
`common/institutions/*.txt`

Use vanilla references from:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\institutions\00_institutions.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\institutions\institutions.md`

In this mod, the existing institution definition lives in:
`common/institutions/ztr_environmental_policy.txt`

Related files often include:
- `common/laws/` because laws usually grant and scale the institution through `institution = ...` and `institution_modifier = { ... }`
- `common/technology/technologies/` for `country_institution_<key>_max_investment_add` and cost modifiers
- `common/modifier_type_definitions/` when a new institution needs a new max-investment modifier type
- `common/ai_strategies_vanilla/` for `institution_scores`
- `localization/*institution*.yml` for name, description, and any custom modifier text
- `gfx/interface/icons/` and `gfx/interface/illustrations/institutions/` for UI art

## Workflow

1. Decide whether the task is adding a new institution or overriding an existing vanilla one.
2. Inspect vanilla institutions first; they are structurally simple and usually only define art plus an optional per-level `modifier`.
3. Check which law or law group will expose the institution, because institutions do not function in isolation.
4. Add or update technology and modifier support for max investment if the institution needs progression beyond law effects alone.
5. Add matching localization and verify the icon and background texture paths exist.
6. Search the mod for the institution key to confirm AI, journal entries, scripted logic, and modifiers reference the final name consistently.

## Structure

```victoria3
institution_example = {
	icon = "gfx/interface/icons/institution_icons/example.dds"
	background_texture = "gfx/interface/illustrations/institutions/example.dds"

	modifier = {
		state_example_modifier_add = 0.05
	}
}
```

## Core Fields

- Top-level key: The institution identifier, typically following the vanilla pattern `institution_<name>`.
- `icon`: DDS path for the institution icon shown in UI.
- `background_texture`: DDS illustration shown in the institution panel.
- `modifier`: Optional per-investment-level effect package applied by the institution.

The `modifier` block is scaled by institution investment. Keep it concise and focused on the institution's core gameplay purpose.

## Mod Pattern

This mod already defines:

```victoria3
institution_environmental_policy = {
	icon = "gfx/interface/icons/institution_icon/law_active_restoration.dds"
	background_texture = "gfx/interface/illustrations/institutions/institution_environmental_policy.dds"
}
```

That institution is then wired into neighboring content:
- `common/laws/ztr_environmental_policy.txt` assigns `institution = institution_environmental_policy`
- `common/technology/technologies/` adds `country_institution_environmental_policy_max_investment_add`
- `common/modifier_type_definitions/ztr_bool_modifier_types.txt` defines the corresponding modifier type
- `localization/english/ztr_institution_l_english.yml` provides the institution name and description

When adding a new institution, treat `common/institutions/` as only one piece of the feature.

## Editing Guidance

- Prefer creating a new institution key instead of overloading an unrelated vanilla one unless the task is explicitly an override.
- Copy a close vanilla example first, then add only the fields you actually need.
- Keep icon and background naming consistent with the institution key where practical; mismatched art names make maintenance harder.
- Do not add a `modifier` block just because the schema allows it. Some institutions intentionally derive most of their gameplay from laws and investment caps instead.
- If the institution needs custom technology support, verify both the modifier name and its localization exist.
- If AI behavior should change, update `institution_scores` rather than assuming the AI will value the new institution automatically.

## Localization Pattern

At minimum, add:
- `<institution_key>`
- `<institution_key>_desc`

If you introduce custom modifier types for investment caps or institution costs, also add the relevant modifier localization keys, for example:
- `country_institution_<institution_key_without_prefix>_max_investment_add`
- `country_institution_<institution_key_without_prefix>_max_investment_add_desc`

For `institution_environmental_policy`, this mod uses:
- `institution_environmental_policy`
- `institution_environmental_policy_desc`
- `country_institution_environmental_policy_max_investment_add`
- `country_institution_environmental_policy_max_investment_add_desc`

## Template

```victoria3
institution_example = {
	icon = "gfx/interface/icons/institution_icons/example.dds"
	background_texture = "gfx/interface/illustrations/institutions/institution_example.dds"

	modifier = {
		country_example_mult = 0.1
	}
}
```

## Review Checklist

- The file is in `common/institutions/`.
- The institution key is stable and follows the `institution_<name>` pattern.
- `icon` and `background_texture` point to real DDS assets.
- Any `modifier` entries use valid modifier names.
- The institution is granted by the intended law content in `common/laws/`.
- Any max-investment or cost modifiers referenced by technology, companies, or other content exist and are localized.
- Name and description localization keys exist.
- AI, journal entries, scripted triggers, and other script references use the same institution key.
