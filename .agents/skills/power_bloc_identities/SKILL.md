---
name: Power Bloc Identities
description: Guide for Victoria 3 power bloc identity definitions. Use when creating, editing, or reviewing files in common/power_bloc_identities, especially when adding a new identity, overriding a vanilla identity, or tracing how an identity interacts with cohesion, mandate progress, invite logic, AI weighting, and neighboring power bloc content in common/.
---

# Power Bloc Identities

Use this skill for Victoria 3 files in `common/power_bloc_identities/*.txt`.

Do not use it for:
- naming-only changes in `common/power_bloc_names/`
- principle tier or principle-group edits by themselves
- localization-only edits with no identity definition changes

Pair it with:
- `Localization Formatting` when adding or fixing localization text

## File Location

Write modded identity definitions in:
- `common/power_bloc_identities/*.txt`

Inspect the local mod first:
- `common/power_bloc_identities/*.txt`
- `common/power_bloc_principles/*.txt`
- `common/power_bloc_principle_groups/*.txt`
- `common/power_bloc_names/*.txt`
- `common/diplomatic_actions/*.txt`
- `common/decisions/*.txt`
- `common/script_values/*.txt`
- `common/scripted_effects/*.txt`
- `common/scripted_triggers/*.txt`
- `common/ai_strategies/*.txt`
- `common/ai_strategies_vanilla/*.txt`
- `localization/english/*.yml`

Then compare against vanilla references:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\power_bloc_identities\power_bloc_identities.md`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\power_bloc_identities\00_power_bloc_identities.txt`

## What These Definitions Do

A power bloc identity is the bloc's central gameplay profile. It usually decides:
- who is allowed to form or select the identity
- which bonuses or penalties the bloc, leader, and members receive
- how weekly mandate progress is calculated
- how cohesion target is calculated and which bloc traits increase or reduce it
- whether members can leave under certain leverage conditions
- how attractive the identity is to AI countries during bloc formation
- which diplomatic acceptance modifiers the bloc exposes through `power_bloc_modifier`

These files sit in a wider `common/` network:
- `common/power_bloc_principles/` adds the selectable principle effects that sit on top of the identity
- `common/power_bloc_principle_groups/` decides which principle groups are primary, blocked, or unlocked for the identity
- `common/power_bloc_names/` uses `scope:selected_identity` to theme generated bloc names
- `common/diplomatic_actions/` often reads identity-driven modifiers such as `power_bloc_invite_acceptance_*`
- `common/decisions/` and scripted content may create blocs directly with a chosen identity
- `common/script_values/`, `common/scripted_triggers/`, and `common/scripted_effects/` often provide the math and hooks used by `mandate_progress`, `cohesion`, and `on_created`

If you change or add an identity, search `common/` for:
- `has_identity = identity:<key>`
- `identity = <key>`
- `power_bloc_invite_acceptance_`
- `create_power_bloc = {`
- `is_in_same_power_bloc`

## Workflow

1. Inspect the nearest current vanilla identity before creating or overriding anything.
2. Search the local mod for `has_identity`, `create_power_bloc`, and invite-related modifiers so you know what else depends on the identity.
3. Decide whether the effect belongs in the identity itself, a principle, a principle group, a diplomatic action, or a script value.
4. Copy the nearest vanilla block and adapt it instead of starting from `power_bloc_identities.md`.
5. Keep `cohesion` readable and scoped correctly, especially if it uses `scope:with_country` or `scope:without_country`.
6. Re-check every referenced script value, scripted effect, modifier key, law key, and texture path after editing.

## Common Structure

```pdx
example_identity = {
	visible = {
		has_dlc_feature = power_bloc_features
	}

	possible = {
		has_law = law_type:law_monarchy
	}

	can_leave = {
		OR = {
			is_power_bloc_leader = yes
			power_bloc = {
				leverage_advantage = {
					target = root
					value <= 30
				}
			}
		}
	}

	icon = "gfx/interface/icons/central_identity_pillars_icons/example.dds"
	background = "gfx/interface/power_blocs/backgrounds/pb_background_generic.dds"

	power_bloc_modifier = {
		power_bloc_cohesion_per_member_add = -1
	}

	leader_modifier = {
		country_influence_mult = 0.1
	}

	non_leader_modifier = {
		country_authority_mult = -0.1
	}

	member_modifier = {
		state_construction_mult = 0.1
	}

	mandate_progress = power_bloc_mandate_progress_by_rank
	on_created = { relations_hit_with_power_bloc_leaders = yes }

	cohesion = {
		add = {
			add = 0.3
			multiply = define:NPowerBlocs|COHESION_TARGET_MAX
			desc = POWER_BLOC_COHESION_BASE
		}
	}

	ai_weight = {
		value = 100
	}
}
```

## Field Guidance

- Definition key: Referenced as `identity:<key>` elsewhere. Renaming the key breaks principle-group links, name triggers, bloc creation, and AI checks.
- `visible`: Gates whether the identity appears in the formation UI. Vanilla often uses DLC feature checks here.
- `possible`: Formation-time requirements. Use it for law, ideology, or country-state restrictions on selecting the identity.
- `can_leave`: Current vanilla uses this, not `can_join`. It controls when a member can leave the bloc.
- `icon` and `background`: UI asset paths. Reuse vanilla conventions unless you are also providing custom art.
- `power_bloc_modifier`: Bloc-wide effects and many invite-acceptance modifiers consumed by diplomatic actions.
- `leader_modifier`: Effects only for the bloc leader.
- `non_leader_modifier`: Effects for all non-leader members. Current vanilla uses this field explicitly.
- `member_modifier`: Effects for all members, including the leader when appropriate.
- `mandate_progress`: Usually a script value key, not an inline block in current vanilla.
- `on_created`: Optional scripted effect fired when the bloc is created.
- `cohesion`: Scripted-value-style math for the bloc's cohesion target. Keep descriptions and scope handling readable.
- `ai_weight`: AI preference when choosing an identity. Vanilla usually starts with `value = 100` and layers conditional modifiers.

## Important Caution

Do not trust `power_bloc_identities.md` as a complete or current schema guide by itself.

The bundled note file still shows fields that do not match current vanilla usage well:
- it shows `can_join`, while current vanilla identities use `can_leave`
- it shows `participant_modifier`, while current vanilla identities use `non_leader_modifier`
- it does not show `on_created`
- it does not show `ai_weight`

Always verify against `00_power_bloc_identities.txt` before copying or deleting a field.

## Vanilla Patterns

Current vanilla identity files show these stable patterns:
- `visible` usually gates DLC content such as `power_bloc_features` or `ip4_content`
- `possible` is often used for law-based identity restrictions
- `can_leave` commonly uses a leverage threshold check against the bloc leader
- `mandate_progress` often points at `power_bloc_mandate_progress_by_rank`
- `cohesion` often supports `scope:with_country` and `scope:without_country` so the game can preview membership changes
- `power_bloc_modifier` frequently includes `power_bloc_invite_acceptance_*` modifiers that matter outside the identity file
- `on_created` commonly fires a scripted effect such as `relations_hit_with_power_bloc_leaders`

## Editing Guidance

- Start from the nearest vanilla identity, not from the minimal note-file stub.
- Treat identity edits as systems work. A small modifier change can alter formation, cohesion, invite AI, and downstream principle balance.
- Keep `cohesion` math explainable. If you cannot summarize why countries fit or destabilize the bloc, the script is probably too opaque.
- Prefer putting durable bloc identity in the identity file and narrower bonuses in principles.
- When overriding vanilla, compare the whole original block. Omitting a field like `can_leave`, `visible`, or `ai_weight` can silently change behavior.
- If the identity exposes a tooltip-only or AI-only modifier, confirm where it is consumed before deleting it.

## Review Checklist

- The definition lives in `common/power_bloc_identities/`.
- Every referenced script value, effect, trigger, law, or modifier key exists.
- `icon` and `background` paths are valid.
- `cohesion` scope usage is valid and intentional.
- The identity's modifier keys are consistent with any invite, merge, or bloc-creation logic in `common/diplomatic_actions/` or `common/decisions/`.
- Any new identity mentioned by a name rule, principle group, or bloc-creation effect is referenced correctly there.
- No outdated `power_bloc_identities.md` placeholder syntax such as `can_join` or `participant_modifier` was copied blindly.
