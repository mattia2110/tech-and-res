---
description: Guide for Victoria 3 diplomatic action definitions. Use when creating, editing, or reviewing files in common/diplomatic_actions, especially when adding proposal logic, pact behavior, AI acceptance, or overrides of vanilla diplomatic actions.
name: Diplomatic Action
---

# Diplomatic Actions

Use this skill for Victoria 3 files in `common/diplomatic_actions/*.txt`.

Do not use it for:
- `common/diplomatic_plays/*.txt`
- `common/diplomatic_catalysts/*.txt`
- `common/treaty_articles/*.txt`
- general diplomacy effects outside a diplomatic action definition

## File Location

Write modded diplomatic actions in:
`common/diplomatic_actions/*.txt`

Inspect the mod's existing examples first:
- `common/diplomatic_actions/ztr_invite_to_power_bloc.txt`
- `common/diplomatic_actions/ztr_merge_power_bloc_diplomacy.txt`
- `common/diplomatic_actions/ztr_station_nuclear_weapons.txt`
- `common/diplomatic_actions/ztr_nuclear_ultimatum.txt`
- `common/diplomatic_actions/ztr_unprovoked_nuclear_strike.txt`
- `common/diplomatic_actions/ztr_war_nuclear_strike.txt`

If overriding a vanilla action, compare against the base game file in Victoria 3's `game/common/diplomatic_actions/`.

Related files often live in:
- `localization/english/*.yml`
- `common/scripted_effects/*.txt`
- `common/script_values/*.txt`
- `common/scripted_modifiers/*.txt`
- `common/modifier_types/*.txt`
- `common/diplomatic_plays/*.txt`
- `common/diplomatic_catalysts/*.txt`

## Workflow

1. Find whether the action already exists in the mod with `rg "<action_key>\\s*=\\s*\\{" common/diplomatic_actions`.
2. Read the closest existing action in `common/diplomatic_actions/` before writing a new one.
3. If the action is a vanilla override, inspect the original definition in Victoria 3 `game/common/diplomatic_actions/` and preserve engine-required fields.
4. Check whether the action also needs supporting scripted effects, script values, modifiers, catalyst logic, pact maintenance rules, or localization.
5. Keep `potential`, `possible`, and `ai` aligned. If players can propose it, AI acceptance and break logic should usually exist too.
6. Re-read related diplomatic actions in the same feature area so naming, balance, and trigger style stay consistent.

## Common Structure

```pdx
example_action = {
	groups = { general }
	requires_approval = yes
	can_use_obligations = yes
	uses_random_approval = yes
	should_notify_third_parties = yes

	unlocking_technologies = {
		example_technology
	}

	selectable = { }
	potential = { }
	possible = { }

	accept_effect = { }
	decline_effect = { }

	pact = {
		cost = 25

		requirement_to_maintain = {
			trigger = { }
		}

		manual_break_effect = { }
		auto_break_effect = { }
	}

	ai = {
		evaluation_chance = { value = 0.25 }
		accept_score = { value = 0 }
		propose_score = { value = 10 }
		will_propose = { always = yes }
	}
}
```

Not every action needs every block. In this mod:
- hostile one-shot actions often use `accept_effect` without `pact`
- long-running relationships use a `pact` block with `requirement_to_maintain`
- actions requiring consent usually define `accept_score`
- player-facing hostile actions may set `should_notify_third_parties = yes`

## Scope Guidance

Follow the scopes used by the actual diplomatic action scripts in `common/diplomatic_actions/`:
- `root`: the country currently evaluating the trigger or effect block
- `scope:actor`: the proposing country
- `scope:target_country`: the target country

Do not assume older shorthand such as `scope:target` is the convention here. The mod's actual actions consistently use `scope:target_country`.

Inside nested scopes, re-check who `root` points to before copying logic. Many bugs in diplomatic actions come from mixing up `root`, `scope:actor`, and `scope:target_country`.

## Core Blocks

### `groups`

Use the correct group for UI sorting and behavior. Existing comments in prior references mention `general`, `subject`, `overlord`, and `power_bloc`. In this mod, current actions use `general`.

### `selectable`

Use for high-level visibility checks on the initiating side. Keep this cheap and broad.

Typical uses:
- hide actions from decentralized countries
- require bloc leadership
- gate actions away from revolutionary or secessionist countries

### `potential`

Use for structural validity: whether this actor-target pair can ever use the action.

Typical uses from this mod:
- target must be sovereign, allied, in a bloc, or otherwise structurally eligible
- actor or target must not be revolutionary
- actor must have a required capability such as `nuclear_capability`

### `possible`

Use for current executable conditions: timing, relations, cooldowns, adjacency, truce checks, and similar live state.

Typical uses from this mod:
- `has_diplomatic_relevance = scope:target_country`
- relation thresholds
- adjacency or delivery-range checks
- `NOT = { has_modifier = nuclear_attack_cooldown }`
- `NOT = { has_truce_with = scope:target_country }`

Keep `possible` readable. If the same logic also governs a long-running pact, consider sharing it through scripted triggers or mirrored `requirement_to_maintain`.

### `accept_effect` and `decline_effect`

Use these for the immediate result of the diplomatic proposal.

Common patterns in this mod:
- `change_relations`
- `create_incident`
- `join_power_bloc`
- lobby appeasement effects
- `custom_tooltip` wrapping impactful scripted effects
- hidden setup for wars, events, or catalyst creation

If the action is destructive or surprising, prefer clear tooltips and keep hidden effects narrow.

### `pact`

Use a `pact` block when the action creates an ongoing diplomatic relationship.

Common pact fields seen in this mod:
- `cost`
- `can_be_used_in_sway_offers`
- income transfer fields
- `requirement_to_maintain`
- `manual_break_effect`
- `auto_break_effect`

If a pact has maintenance conditions, make sure proposal logic and maintenance logic do not drift apart unless the difference is intentional.

## AI Block

The `ai` block is not optional in practice if the action should behave sensibly in AI hands.

Common fields used by this mod:
- `evaluation_chance`
- `accept_score`
- `propose_score`
- `propose_break_score`
- `will_propose`
- `will_propose_even_if_not_accepted`
- `use_obligation_chance`
- `owe_obligation_chance`
- `check_acceptance_for_will_propose`
- `check_acceptance_for_will_break`

Guidance:
- Start `accept_score` at `value = 0` and add or subtract clear reasons.
- Use `desc = "..."` labels for acceptance contributors when the engine supports breakdown display.
- Keep `will_propose` stricter than `possible` if the action is expensive, niche, or dangerous.
- For player-only or intentionally inactive AI actions, make that explicit with `will_propose = { always = no }` or `propose_score = { value = 0 }`.

## Mod Patterns To Reuse

### Power Bloc Actions

Use `ztr_invite_to_power_bloc.txt` and `ztr_merge_power_bloc_diplomacy.txt` as references for:
- vanilla action replacement style such as `REPLACE:invite_to_power_bloc`
- bloc leadership gating
- leverage-based AI acceptance
- cohesion and ideological scoring

### Nuclear Actions

Use `ztr_station_nuclear_weapons.txt`, `ztr_nuclear_ultimatum.txt`, `ztr_unprovoked_nuclear_strike.txt`, and `ztr_war_nuclear_strike.txt` as references for:
- high-impact hostile actions
- adjacency and delivery-capability gating
- cooldown modifiers
- `custom_tooltip` around large scripted effects
- pact maintenance for stationed weapons

## Editing Checklist

- Confirm the action key is unique, or intentionally use `REPLACE:<vanilla_key>`.
- Keep scope usage consistent: `root`, `scope:actor`, `scope:target_country`.
- Check whether the action needs localization for name, description, tooltips, or acceptance reasons.
- Check whether scripted effects or script values should be reused instead of inlining large logic blocks.
- If using a pact, verify `requirement_to_maintain` and break effects.
- If using obligations, make sure AI obligation chances exist and make sense.
- If targeting wars, blocs, or treaties, inspect the related `common/` files before finalizing the action.

## Template

```pdx
my_diplomatic_action = {
	groups = { general }
	requires_approval = yes
	can_use_obligations = yes

	potential = {
		scope:target_country = {
			always = yes
		}
	}

	possible = {
		has_diplomatic_relevance = scope:target_country
	}

	accept_effect = {
		change_relations = {
			country = scope:target_country
			value = 10
		}
	}

	decline_effect = {
		change_relations = {
			country = scope:target_country
			value = -10
		}
	}

	ai = {
		evaluation_chance = {
			value = 0.25
		}

		accept_score = {
			value = 0
		}

		propose_score = {
			value = 10
		}

		will_propose = {
			would_accept_diplomatic_action = {
				actor = scope:target_country
				type = my_diplomatic_action
			}
		}
	}
}
```

## Practical Rule

Do not treat diplomatic actions as isolated files. Always inspect the relevant `common/` neighbors first, especially existing `common/diplomatic_actions/` files in the same feature area and any supporting scripted content they call.
