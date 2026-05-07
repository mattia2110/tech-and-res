---
description: Guide for Victoria 3 political lobby definitions. Use when creating, editing, or reviewing files in common/political_lobbies, including new lobby types, lobby maintenance rules, IG join logic, and appeasement-factor availability.
name: Political Lobbies
---

# Political Lobbies

Use this skill for Victoria 3 files in `common/political_lobbies/*.txt`.

Do not use it for:
- appeasement factor definitions by themselves
- diplomatic actions unless you are editing the lobby type they feed into
- movement or interest group ideology content outside a lobby definition

## File Location

Write modded lobby definitions in:
- `common/political_lobbies/*.txt`

Inspect the local mod first:
- `common/political_lobbies/*.txt`
- `common/diplomatic_actions/*.txt`
- `common/treaty_articles/*.txt`

Then compare against vanilla references:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\political_lobbies\political_lobbies.md`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\political_lobbies\00_political_lobbies.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\scripted_effects\00_lobby_effects.txt`

Related files often include:
- `common/political_lobby_appeasement/*.txt`
- `common/scripted_effects/*.txt`
- `common/diplomatic_actions/*.txt`
- `common/treaty_articles/*.txt`
- `localization/english/*.yml`

## What These Definitions Do

A political lobby type defines:
- what kind of target relationship the lobby represents
- when a lobby can form
- when it changes type or disbands
- which appeasement factors it can use
- which interest groups can join and how strongly they want to

## Scope Guidance

The base-game lobby notes and files consistently use:
- `root`: the interest group evaluating creation, maintenance, or joining
- `scope:country`: the country the lobby belongs to
- `scope:target_country`: the country the lobby targets
- `scope:political_lobby`: the existing lobby when one already exists

Domestic lobbies use `scope:target_country = scope:country`.

Re-check scope carefully inside nested blocks. Many lobby bugs come from mixing up the lobby owner with the target country.

## Workflow

1. Inspect the nearest vanilla lobby type with the same diplomatic stance first.
2. Check which appeasement factor keys already exist before listing them in the lobby.
3. Confirm whether the feature also needs scripted effects or diplomatic actions that add appeasement.
4. Write maintenance rules before `join_weight`; if the lobby cannot stay coherent, the weight math will not matter.
5. Keep `available_for_interest_group` and `join_weight` aligned so AI and UI expectations match.
6. If a failed maintenance rule should convert the lobby instead of killing it, wire `swap_type_on_failed` and the follow-up effects together.

## Common Structure

```pdx
lobby_example = {
	category = foreign_pro_country
	texture = "gfx/interface/icons/political_lobby_icons/lobby_pro.dds"

	can_create = {
		always = yes
	}

	on_created = {
		scope:political_lobby ?= {
			initialize_lobby_custom_name = yes
		}
	}

	requirement_to_maintain = {
		trigger = {
			has_diplomatic_relevance = scope:target_country
		}
		on_failed = { }
	}

	appeasement_factors_pro = {
		appeasement_relations_increased
	}

	appeasement_factors_anti = {
		appeasement_relations_decreased
	}

	available_for_interest_group = {
		always = yes
	}

	join_weight = {
		value = 0
	}
}
```

## Core Fields

- `category`: Lobby family, such as `foreign_pro_country`, `foreign_anti_country`, or another valid lobby category used by vanilla.
- `texture`: Lobby icon path.
- `can_create`: Trigger gate for whether the lobby can form.
- `on_created`: Initialization effect after creation.
- `requirement_to_maintain`: One or more maintenance rules. Multiple blocks are normal.
- `swap_type_on_failed`: Optional per-maintenance-rule conversion target instead of disbanding.
- `appeasement_factors_pro`, `appeasement_factors_anti`: Allowed factor keys for positive and negative appeasement events.
- `available_for_interest_group`: Whether a specific IG may join.
- `join_weight`: Script value for how strongly the IG wants to join or remain in the lobby.

## Appeasement Rules

- Factors marked `is_always_usable = yes` in `common/political_lobby_appeasement/` should not be redundantly listed here.
- Factors not marked always usable must be explicitly listed in the appropriate pro or anti array.
- The lobby definition does not assign numeric appeasement by itself. Actual amounts come from effects such as `change_appeasement` or helper effects in `00_lobby_effects.txt`.

## Vanilla Patterns

Vanilla lobby files show several recurring patterns:
- multiple `requirement_to_maintain` blocks per lobby type
- maintenance failures that either disband the lobby or swap it into a subject or overlord variant
- `on_created` setup using `initialize_lobby_custom_name = yes`
- `join_weight` written as a descriptive script-value block with ideological reasons and labeled `desc` entries
- appeasement changes triggered from diplomacy and treaty content rather than from the lobby file alone

## Mod Integration

This mod already uses lobby appeasement helper effects from diplomacy content, for example in:
- `common/diplomatic_actions/ztr_station_nuclear_weapons.txt`
- `common/diplomatic_actions/ztr_nuclear_ultimatum.txt`
- `common/diplomatic_actions/ztr_unprovoked_nuclear_strike.txt`
- `common/diplomatic_actions/ztr_war_nuclear_strike.txt`
- `common/treaty_articles/ztr_pause_nuclear_weapons_development.txt`
- `common/treaty_articles/ztr_nuclear_de_escalation_treaty.txt`

If you add a new lobby type here, check whether those callers also need new appeasement factors, swap logic, or localization.

## Editing Guidance

- Copy the nearest vanilla lobby and then simplify it.
- Keep `can_create` structural and use `join_weight` for nuance.
- Use descriptive `desc` labels in `join_weight` when a breakdown will help explain AI behavior.
- Avoid hidden exclusivity bugs by checking whether the IG already belongs to another lobby targeting the same country.
- If a lobby changes type on failure, update its name or other initialization state inside `on_failed`.

## Review Checklist

- The lobby key is defined in `common/political_lobbies/`.
- `category` and icon match the intended stance.
- Every listed appeasement factor key exists and is allowed to be listed.
- `requirement_to_maintain` covers both diplomatic relevance and membership viability when needed.
- `available_for_interest_group` and `join_weight` use IG scope correctly.
- Any diplomacy or treaty content that should feed the lobby has matching appeasement calls.
