---
name: Subject Types
description: Guide for Victoria 3 subject type definitions. Use when creating, editing, or reviewing files in common/subject_types, especially when adding new subject relationships, overriding vanilla subject types, or tracing how subject rules connect to diplomatic actions, autonomy ladders, ranks, country types, AI, and neighboring diplomatic content.
---

# Subject Types

Use this skill for Victoria 3 files in `common/subject_types/*.txt`.

Do not use it for:
- `common/diplomatic_actions/*.txt` edits with no subject type definition change
- treaty article or war goal work that only references an existing subject type
- country rank or country type changes by themselves

Pair it with:
- `Diplomatic Action` when the subject type needs a new or changed `diplomatic_action`
- `Treaty Articles` when treaty content creates, transfers, or checks the subject relation
- `War Goal Types` when the subject relation is enforced through war demands

## File Location

Write modded subject type definitions in:
- `common/subject_types/*.txt`

Inspect the local mod first:
- `common/diplomatic_actions/*.txt`
- `common/treaty_articles/*.txt`
- `common/war_goal_types/*.txt`
- `common/scripted_rules/*.txt`
- `common/scripted_triggers/*.txt`
- `localization/english/*.yml`

Then compare against vanilla references:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\subject_types\00_subject_types.txt`

In this repo there is currently no local `common/subject_types/` folder, so subject-type work will usually also require checking the downstream diplomatic content that creates or consumes the relation.

## What These Definitions Do

A subject type definition is the rules package for one overlord-subject relationship. It usually decides:
- whether the subject keeps its own ruler, map color, and diplomacy
- whether it joins overlord wars or can have subjects of its own
- whether it is valid for release, annexes on formation, or requires protection
- which `diplomatic_action` creates or represents it
- which autonomy level and category it belongs to
- which higher, lower, or same-level alternatives it can swap to
- which country types and ranks are valid for overlord and subject
- how attractive the relation is to AI through `ai_value`

These definitions sit in a wider `common/` chain:
- `common/diplomatic_actions/` usually creates or upgrades the subject relation
- `common/war_goal_types/` and `common/treaty_articles/` can transfer, enforce, or break it
- `common/scripted_rules/` and `common/scripted_triggers/` often gate diplomacy around it

## Workflow

1. Inspect the nearest vanilla subject type before adding or overriding anything.
2. Decide whether the task needs a new key or a full override of a vanilla key.
3. Map the autonomy ladder first: higher, lower, and same-level alternatives should form a coherent graph.
4. Confirm the intended overlord and subject country types and ranks before writing any filters.
5. Check the linked `diplomatic_action` and any downstream treaty or war-goal content that expects the type.
6. Re-check AI and autonomy behavior after editing so the new relation is actually reachable and stable.

## Common Structure

```pdx
subject_type_example = {
	allow_change_country_flag = yes
	use_overlord_map_color = yes
	use_overlord_ruler = no
	annex_on_country_formation = no
	can_start_own_diplomatic_plays = yes
	breaks_if_subject_not_protected = yes
	join_overlord_wars = yes
	can_have_subjects = no
	overlord_must_be_higher_rank = yes
	overlord_must_be_same_country_type = no
	use_for_release_country = no
	gives_prestige_to_overlord = yes

	diplomatic_action = example_subject_action

	autonomy_level = 2
	category = same_as_puppet

	lower_autonomy_subject_type_alternatives = {
		subject_type_example_lower
	}

	same_autonomy_subject_type_alternatives = {
		subject_type_example_peer
	}

	valid_overlord_country_types = {
		recognized
	}

	valid_subject_country_types = {
		recognized
		unrecognized
	}

	valid_overlord_ranks = {
		great_power
		major_power
	}

	valid_subject_ranks = {
		minor_power
		insignificant_power
	}

	ai_value = {
		value = 1
	}
}
```

## Field Guidance

- Definition key: Keep it stable once diplomacy or scripted logic references it.
- Boolean behavior flags: These control the gameplay identity of the subject relation. Small flag changes can have large diplomatic consequences.
- `diplomatic_action`: The action key that creates or represents the relation.
- `autonomy_level`: Relative step in the subject ladder.
- `category`: Shared UI and classification family such as `same_as_puppet`.
- `higher_autonomy_subject_type_alternatives`, `lower_autonomy_subject_type_alternatives`, `same_autonomy_subject_type_alternatives`: Allowed conversion paths. Keep them intentional and readable.
- `valid_overlord_country_types` and `valid_subject_country_types`: Structural country-type filters.
- `valid_overlord_ranks` and `valid_subject_ranks`: Structural rank filters.
- `ai_value`: Relative desirability for AI strategies. This affects both having the subject and becoming that subject type.

## Editing Guidance

- Start from the closest vanilla subject type and change only what the design actually needs.
- Keep autonomy alternatives symmetric enough that diplomacy can move between intended states.
- Do not create impossible ladders where the alternatives point to relations blocked by contradictory rank or country-type filters.
- Treat `use_for_release_country`, `annex_on_country_formation`, and war-participation flags as high-impact behavior, not flavor toggles.
- If the subject type is meant to appear in sway offers, transfers, or special diplomatic actions, verify those consumers separately instead of assuming the type alone is enough.

## Review Checklist

- The definition lives in `common/subject_types/`.
- `diplomatic_action` points to a real action key.
- Country type and rank filters allow the intended overlord and subject pairs.
- Autonomy alternatives form a coherent ladder with no dead-end or contradictory path.
- War, release, and annexation behavior is intentional.
- AI value is sensible relative to adjacent vanilla subject types.
- Any downstream diplomatic action, treaty article, war goal, trigger, and localization changes use the same subject type key.
