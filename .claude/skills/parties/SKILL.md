---
name: Parties
description: Guide for Victoria 3 party definitions. Use when creating, editing, or reviewing files in common/parties, especially when adding new party archetypes, overriding vanilla parties, or tracing how party names, availability, and interest-group join weights depend on ideologies, laws, technology, country context, AI, and localization.
---

# Parties

Use this skill for Victoria 3 files in `common/parties/*.txt`.

Do not use it for:
- ideology, interest group, or law edits that only influence existing parties indirectly
- journal entry or event work that merely checks whether a party exists
- localization-only changes with no party definition change

Pair it with:
- `Scripted Triggers` when repeated party eligibility logic should be extracted into a reusable trigger
- `Localization Formatting` when the party adds new dynamic name keys or tooltip text

## File Location

Write modded party definitions in:
- `common/parties/*.txt`

Inspect the local mod first:
- `common/ideologies/*.txt`
- `common/interest_groups/*.txt`
- `common/laws/*.txt`
- `common/technology/**/*.txt`
- `common/journal_entries/*.txt`
- `common/script_values/*.txt`
- `common/scripted_triggers/*.txt`
- `localization/english/*.yml`

Then compare against vanilla references:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\parties\conservative_party.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\parties\liberal_party.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\parties\social_democrats_party.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\parties\radical_party.txt`

In this repo there is currently no local `common/parties/` folder, so party work will usually also require adding localization and checking the ideology, law, and journal systems that drive the new behavior.

## What These Definitions Do

A party definition is the political archetype that interest groups can join. It usually decides:
- the party's color and icon
- how the party chooses its displayed name through `first_valid` triggered descriptions
- whether the party is valid for a country at all
- which interest groups are even eligible to join it
- how strongly each eligible IG wants to join through `join_weight`

These files sit in a wider political chain:
- `common/ideologies/` drives much of `available_for_interest_group` and `join_weight`
- `common/laws/`, `common/technology/`, and `common/journal_entries/` often change party naming or coalition behavior
- `localization/english/*.yml` supplies the `party_*` keys referenced by dynamic naming

## Workflow

1. Inspect the closest vanilla party family before creating or overriding anything.
2. Decide whether the task needs a new party archetype or a targeted override of a vanilla party.
3. Build the dynamic `name` block from most specific to most general, with a true fallback last.
4. Keep `valid_for_country` and `available_for_interest_group` aligned so the party is both legal and reachable.
5. Tune `join_weight` carefully, watching both additive and multiplicative gates.
6. Re-check localization, icon paths, ideology keys, law keys, and any country- or journal-specific triggers after editing.

## Common Structure

```pdx
example_party = {
	color = { 120 140 180 }
	name = {
		first_valid = {
			triggered_desc = {
				desc = party_example_historical
				trigger = {
					exists = c:EXM
					c:EXM ?= THIS
				}
			}
			triggered_desc = {
				desc = party_example_default
				trigger = { always = yes }
			}
		}
	}

	icon = {
		default = "/gfx/interface/icons/political_parties/example.dds"
	}

	valid_for_country = {
		always = yes
	}

	available_for_interest_group = {
		owner = { country_has_voting_franchise = yes }
		NOT = { is_interest_group_type = ig_trade_unions }
	}

	join_weight = {
		value = {
			value = -5
			desc = "base_value"
		}
		if = {
			limit = { has_ideology = ideology:ideology_example }
			add = {
				value = 5
				desc = "from_example_ideology"
			}
		}
	}
}
```

## Field Guidance

- Definition key: Party archetype identifier such as `conservative_party`.
- `color`: UI color for the party.
- `name = { first_valid = { ... } }`: Ordered list of country-, law-, culture-, event-, or era-specific names. Last fallback should always be safe.
- `icon`: Usually a `default` DDS path.
- `valid_for_country`: Country-scope gate. Vanilla notes show this is often evaluated only once at game start.
- `available_for_interest_group`: IG-scope eligibility gate, usually with `owner` checks for the country.
- `join_weight`: IG-scope support math. Vanilla commonly uses a negative base, then layered `add` and `multiply` blocks.
- `scope:number`: In `join_weight`, this often means how many IGs would join before the current IG and is used to discourage overfull coalitions.

## Important Caution

Party definitions are easy to make unreachable.

Common failure modes are:
- a `valid_for_country` gate that excludes the party before any later logic matters
- an `available_for_interest_group` block that no real IG can satisfy
- a `join_weight` chain that multiplies the value down to zero
- a `name` block with no reliable fallback

Also treat the comment in vanilla seriously: `valid_for_country` is commonly run only once at game start, so do not rely on it for dynamic mid-game behavior unless you have confirmed the system supports it.

## Editing Guidance

- Copy the nearest vanilla party family and trim it down rather than starting from scratch.
- Order dynamic names from narrowest historical case to broadest fallback.
- Keep extremely large join-weight values for true hard pushes or hard exclusions, not normal preference tuning.
- When the party depends on country tags, laws, technologies, or journal state, verify those keys exist and can coexist in real saves.
- If several parties share the same complex eligibility logic, move the boolean into a scripted trigger instead of copying it.

## Review Checklist

- The definition lives in `common/parties/`.
- Every `desc = party_*` key exists in localization.
- The `name` block has a safe final fallback.
- `valid_for_country` and `available_for_interest_group` allow the party to exist where intended.
- `join_weight` math does not accidentally zero out or explode the party's membership.
- Every ideology, law, technology, country tag, journal entry, and icon path referenced by the party exists.
- Country-specific or historical naming branches are ordered correctly.
