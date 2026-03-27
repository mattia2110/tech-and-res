---
name: Power Bloc Principles
description: Guide for Victoria 3 power bloc principle definitions. Use when creating, editing, or reviewing files in common/power_bloc_principles, especially when adding a new principle, overriding a vanilla principle tier, or tracing how a principle interacts with principle groups, institutions, AI weighting, diplomatic logic, and neighboring common/ content.
---

# Power Bloc Principles

Use this skill for Victoria 3 files in `common/power_bloc_principles/*.txt`.

Do not use it for:
- principle-group wiring by itself with no principle effect changes
- identity-only changes with no principle edits
- localization-only edits with no gameplay definition change

Pair it with:
- `Localization Formatting` when adding or fixing visible principle text

## File Location

Write modded principle definitions in:
- `common/power_bloc_principles/*.txt`

Inspect the local mod first:
- `common/power_bloc_principles/*.txt`
- `common/power_bloc_principle_groups/*.txt`
- `common/power_bloc_identities/*.txt`
- `common/diplomatic_actions/*.txt`
- `common/decisions/*.txt`
- `common/ai_strategies/*.txt`
- `common/ai_strategies_vanilla/*.txt`
- `common/institutions/*.txt`
- `common/production_methods/*.txt`
- `common/buildings/*.txt`
- `common/scripted_triggers/*.txt`
- `common/scripted_effects/*.txt`
- `common/script_values/*.txt`
- `localization/english/*.yml`

Then compare against vanilla references:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\power_bloc_principles\power_bloc_principles.md`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\power_bloc_principles\00_power_bloc_principles.txt`

## What These Definitions Do

A power bloc principle is an individual selectable tier inside a principle group. It usually decides:
- what modifiers the bloc, leader, members, or non-leader members receive at that tier
- whether the principle is visible or possible to select
- whether the principle adds institution-specific bonuses
- whether the principle sets special booleans or top-level flags that other content reads
- how attractive that principle tier is to AI

These files sit in a wider `common/` network:
- `common/power_bloc_principle_groups/` defines the tier order and family the principle belongs to
- `common/power_bloc_identities/` interacts with principle availability and often shapes which principles AI prefers
- `common/diplomatic_actions/` and AI scripts may test for specific principles or groups
- `common/institutions/` supplies institution keys referenced by `institution =`
- `common/production_methods/` and `common/buildings/` may contain unlock triggers tied to principle booleans or specific principle keys
- `common/scripted_triggers/`, `common/scripted_effects/`, and `common/script_values/` may consume tooltip-only modifiers or principle checks

If you change or add a principle, search `common/` for:
- `has_principle = principle:<key>`
- `has_principle_group = principle_group:<group>`
- the principle key itself
- any modifier key or boolean field introduced by the principle
- the referenced `institution =`

## Workflow

1. Inspect the principle group first so you understand the full tier progression you are editing.
2. Inspect the nearest current vanilla principle family, especially adjacent tiers.
3. Search the local mod for `has_principle`, principle-group checks, and any unlock logic tied to the principle's modifiers or booleans.
4. Decide whether the behavior belongs in the principle, the group, the identity, a production method trigger, or a diplomatic action.
5. Copy the nearest vanilla tier block and preserve cumulative behavior intentionally.
6. Re-check every referenced institution key, modifier key, trigger, and texture path after editing.

## Common Structure

```pdx
principle_example_1 = {
	visible = {
		has_dlc_feature = power_bloc_features
	}

	possible = {
		NOT = { has_law_or_variant = law_type:law_closed_borders }
	}

	icon = "gfx/interface/icons/principles_icons/example.dds"
	background = "gfx/interface/icons/principles_icons/principle_tier_1.dds"

	power_bloc_modifier = {
		power_bloc_disallow_war_bool = yes
	}

	leader_modifier = {
		country_influence_mult = 0.1
	}

	member_modifier = {
		state_construction_mult = 0.1
	}

	non_leader_modifier = {
		country_authority_mult = -0.1
	}

	institution = institution_schools
	institution_modifier = {
		country_tech_spread_mult = 0.05
	}

	ai_weight = {
		value = 100
	}
}
```

## Field Guidance

- Definition key: Usually a family-style key such as `principle_example_1`. It is referenced directly by effects and scripted checks.
- `visible`: Gates whether the principle appears in the UI. Vanilla often uses DLC feature checks on tier 1.
- `possible`: Optional selection restrictions for the principle.
- `icon` and `background`: UI assets. Background usually matches the tier level.
- `power_bloc_modifier`: Bloc-wide effects and bloc-level booleans.
- `leader_modifier`: Effects only for the bloc leader.
- `member_modifier`: Effects for all members.
- `non_leader_modifier`: Current vanilla uses this on some principles. It is not just an identity-only concept.
- `institution`: Institution key that receives extra modifiers from this principle tier.
- `institution_modifier`: Institution-scaling effects applied through the chosen institution.
- top-level flags: Current vanilla also uses top-level fields such as `allows_foreign_investment_in_lower_rank = yes` on some principles. Do not assume every effect lives inside a modifier block.
- `ai_weight`: AI selection logic for the principle tier. Vanilla often gives new group entries a lower base weight and upgrade tiers `value = 1000`.

## Important Caution

Do not trust `power_bloc_principles.md` as a complete or current best-practice template by itself.

The bundled note file lags behind current vanilla usage in important ways:
- it shows `participant_modifier`, while current vanilla uses `non_leader_modifier` where needed
- it does not show top-level fields such as `allows_foreign_investment_in_lower_rank`
- it mentions `incompatible_with`, but current vanilla `.txt` files do not demonstrate its usage

Always verify against `00_power_bloc_principles.txt` before inventing or deleting a field.

## Vanilla Patterns

Current vanilla principle files show these stable patterns:
- tier 1 often carries `visible` gating, while higher tiers may omit it
- higher tiers usually restate the full cumulative bonus rather than relying on inheritance
- `ai_weight = { value = 1000 }` is common for upgrade tiers so AI prefers upgrading an existing group
- some principles use booleans or dummy modifiers only to feed tooltips or unlock logic elsewhere
- some principle families grant PM unlocks or special scripted behavior outside the principle file itself
- institution-oriented families repeat `institution = ...` and grow `institution_modifier` across tiers

## Editing Guidance

- Start from the nearest vanilla principle family, not from the note-file stub.
- Treat principles as cross-file features, not isolated modifier lists. Many of them rely on triggers or tooltip helpers elsewhere.
- Preserve cumulative tier behavior intentionally. If tier 2 or tier 3 should still include earlier bonuses, restate them explicitly as vanilla does.
- If a modifier appears to do nothing on its own, search for where it is consumed before removing it.
- When overriding vanilla, compare adjacent tiers and the parent principle group so progression still makes sense.
- If you add a new principle family, make sure the group, localization, and any downstream unlock logic are updated too.

## Review Checklist

- The definition lives in `common/power_bloc_principles/`.
- The principle key is referenced correctly by its group in `common/power_bloc_principle_groups/`.
- Every referenced institution key, modifier key, trigger, and texture path exists.
- Any cumulative bonuses from earlier tiers were preserved intentionally.
- Any tooltip-only booleans, PM unlock hooks, or scripted checks tied to the principle were verified in neighboring `common/` files.
- `ai_weight` still matches the intended new-pick versus upgrade behavior.
- No outdated `power_bloc_principles.md` placeholder syntax such as `participant_modifier` was copied blindly.
