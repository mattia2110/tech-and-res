---
description: Guide for Victoria 3 diplomatic catalyst definitions. Use when creating, editing, or reviewing files in common/diplomatic_catalysts, or when diplomatic actions, events, scripted effects, or on_actions create or depend on a catalyst such as catalyst_*.
name: Diplomatic Catalysts
---

# Diplomatic Catalysts

Use this skill for Victoria 3 files in `common/diplomatic_catalysts/*.txt`.

Do not use it for:
- `common/diplomatic_catalyst_categories/*.txt`
- `common/diplomatic_actions/*.txt`
- `common/diplomatic_plays/*.txt`
- `common/treaty_articles/*.txt`
- generic AI strategy or relations tuning outside a catalyst definition

## File Location

Write modded catalyst definitions in:
`common/diplomatic_catalysts/*.txt`

Use the base game for reference at:
`C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\diplomatic_catalysts\*.txt`

Use the matching category schema from:
`C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\diplomatic_catalyst_categories\*.txt`

If this mod does not already contain `common/diplomatic_catalysts`, create that folder and place the new file there instead of mixing catalyst definitions into diplomatic actions or scripted effects.

Related files usually live in:
- `common/diplomatic_catalyst_categories/*.txt`
- `common/diplomatic_actions/*.txt`
- `common/diplomatic_plays/*.txt`
- `common/on_actions/*.txt`
- `common/scripted_effects/*.txt`
- `common/scripted_triggers/*.txt`
- `common/script_values/*.txt`
- `common/ai_strategies/*.txt`
- `events/*.txt`
- `localization/english/*.yml`

## Practical Rule

Do not treat a catalyst as an isolated block in `common/diplomatic_catalysts`. Always inspect the surrounding `common\` chain first:

1. Find what creates the catalyst.
2. Find the `cc_*` category it uses.
3. Check any script values, scripted effects, or scripted triggers it depends on.
4. Re-read the diplomatic action, event, or on_action that will fire it.

In this repo, `common/diplomatic_actions/ztr_invite_to_power_bloc.txt` already creates `catalyst_declined_bloc_invite`, so it is the best local example of how catalyst usage connects back to a calling site.

## Workflow

1. Check whether the requested `catalyst_*` key already exists in the mod with `rg "catalyst_[^\\s=]*\\s*=\\s*\\{" common/diplomatic_catalysts`.
2. If the key does not exist in the mod, inspect the nearest vanilla examples in `game/common/diplomatic_catalysts/`, especially the catalyst with the closest political intent.
3. Find where the catalyst is created or checked in the mod with `rg -n "<catalyst_key>|create_diplomatic_catalyst|any_diplomatic_catalyst|is_diplomatic_catalyst_type" common events`.
4. Confirm the category exists and fits the design. If not, also use the `Diplomatic Catalyst Categories` skill and add or update `common/diplomatic_catalyst_categories/*.txt`.
5. Add or update the catalyst definition in `common/diplomatic_catalysts/*.txt`.
6. Recheck the caller so its tooltip text, cooldown expectations, and gameplay timing still match the catalyst definition.
7. Verify any localization that mentions cooldowns, relationship shifts, bloc invites, incidents, or other catalyst outcomes still matches the data.

## Common Structure

```pdx
catalyst_example = {
	category = cc_example

	political_lobby_creation = {
		trigger = {
			always = yes
		}

		lobby_pro_country = {
			value = 0.05
		}
	}

	ai_country_goal_recalculation = {
		type = more_friendly

		trigger = {
			always = yes
		}

		chance = {
			value = 0.1
		}
	}

	effect = {
		change_relations = {
			country = scope:target_country
			value = 10
		}
	}
}
```

Not every catalyst needs every block:
- some catalysts only recalculate AI strategy
- some only create or influence political lobbies
- some only run an `effect`
- some combine all three

## Scope Guidance

Catalyst logic is usually evaluated with:
- `root`: the country receiving the catalyst
- `scope:target_country`: the other country involved in the diplomatic interaction

This is the same pair used in vanilla catalyst definitions and in the mod's bloc invite action.

Before copying logic into a catalyst:
- confirm that the calling effect sets the intended target country
- check whether relation checks like `"relations(scope:target_country)"` make sense from `root`
- keep nested scopes explicit, especially inside `effect` blocks

## Core Fields

### `category`

Required. Points to a `cc_*` definition in `common/diplomatic_catalyst_categories`.

Use categories to control:
- duration
- catalyst effect cooldown
- lobby formation reason

Do not encode category behavior directly into the catalyst if a shared `cc_*` already exists.

### `political_lobby_creation`

Optional. Use when the catalyst can help create pro or anti lobbies.

Common patterns from vanilla:
- `trigger` to skip evaluation when lobby creation should not be attempted
- one or more lobby type blocks such as `lobby_pro_country`, `lobby_anti_country`, `lobby_pro_overlord`, `lobby_anti_overlord`
- `value` plus `if` or `else_if` modifiers rather than huge monolithic expressions

Use the trigger for cheap gating. If the result should never happen under certain conditions, do not rely only on `value <= 0`.

### `ai_country_goal_recalculation`

Optional. Use when the catalyst should push AI strategic desire toward a new diplomatic posture.

Valid `type` values include:
- `all`
- `more_friendly`
- `more_hostile`
- `only_friendly`
- `only_hostile`
- `only_neutral`

Use `trigger` to avoid unnecessary evaluation, then set `chance` as either:
- a scalar such as `chance = 1.0`
- a block starting with `value = ...`

Keep the political intent clear:
- use `more_friendly` or `only_friendly` for rapprochement
- use `more_hostile` or `only_hostile` for aggression, betrayal, incidents, or failed diplomacy

### `effect`

Optional. Runs when the catalyst occurs.

Common effect patterns from vanilla:
- `change_relations`
- lobby appeasement helpers
- truce setup
- cohesion changes
- tightly scoped diplomatic consequences

Prefer calling existing scripted effects when the outcome is already standardized elsewhere in `common/scripted_effects`.

## Design Guidance

- Prefer copying the closest vanilla catalyst and trimming it down over inventing structure from memory.
- Keep catalyst names descriptive and event-like: `catalyst_declined_bloc_invite`, `catalyst_event_negative`, `catalyst_market_opened`.
- Use category reuse aggressively. If the new catalyst shares timing and cooldown with an existing one, point at the same `cc_*`.
- Keep balance logic readable. Several small `if` multipliers are easier to tune than a single opaque formula.
- If the catalyst is tied to a player-facing diplomatic action, keep tooltips and loc synchronized with duration and cooldown comments in the caller.
- Do not hide major gameplay payload in a catalyst if the same logic belongs more clearly in the action, event, or scripted effect that creates it.

## Mod-Aware Guidance

This mod does not currently contain a `common/diplomatic_catalysts` folder, but it already contains catalyst usage from `common\` callers. The strongest local reference is:

`common/diplomatic_actions/ztr_invite_to_power_bloc.txt`

That file already:
- checks for an existing catalyst with `any_diplomatic_catalyst`
- checks a specific type with `is_diplomatic_catalyst_type = catalyst_declined_bloc_invite`
- creates a catalyst with `create_diplomatic_catalyst`

When adding catalyst support to this mod:
- inspect the caller first
- keep the new catalyst file in `common/diplomatic_catalysts/`
- create or update the matching category in `common/diplomatic_catalyst_categories/`
- re-read nearby diplomacy files so the feature behaves coherently across `common\`

## Template

```pdx
catalyst_my_feature = {
	category = cc_my_feature

	political_lobby_creation = {
		trigger = {
			"relations(scope:target_country)" < relations_threshold:cordial
		}

		lobby_anti_country = {
			value = 0.1

			if = {
				limit = {
					NOT = { has_strategic_adjacency = scope:target_country }
				}
				multiply = 0.5
			}
		}
	}

	ai_country_goal_recalculation = {
		type = more_hostile

		trigger = {
			"relations(scope:target_country)" < relations_threshold:cordial
		}

		chance = {
			value = 0.25

			if = {
				limit = {
					"relations(scope:target_country)" <= relations_threshold:poor
				}
				multiply = 2
			}
		}
	}

	effect = {
		change_relations = {
			country = scope:target_country
			value = -15
		}
	}
}
```

## Editing Checklist

- Confirm the `catalyst_*` key is unique, or intentionally matches a vanilla override plan.
- Confirm the referenced `category = cc_*` exists and has fitting duration and cooldown.
- Check where the catalyst is created, queried, or documented across `common\`, `events\`, and localization.
- Keep `root` and `scope:target_country` consistent with the caller.
- Verify any AI hostility or friendliness shift matches the diplomatic design.
- Recheck tooltips or comments in the calling action if they mention time windows or cooldowns.
