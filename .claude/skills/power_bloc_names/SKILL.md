---
name: Power Bloc Names
description: Guide for Victoria 3 power bloc name definitions. Use when creating, editing, or reviewing files in common/power_bloc_names, especially when adding conditional bloc names tied to identities, tags, cultures, laws, geography, allies, rivals, or other nearby scripted content.
---

# Power Bloc Names

Use this skill for Victoria 3 files in `common/power_bloc_names/*.txt`.

Do not use it for:
- identity mechanics or cohesion changes
- principle or principle-group balance changes
- localization-only edits with no new or changed name triggers

Pair it with:
- `Localization Formatting` when adding or fixing the displayed name text

## File Location

Write modded power bloc name definitions in:
- `common/power_bloc_names/*.txt`

Inspect the local mod first:
- `common/power_bloc_names/*.txt`
- `common/power_bloc_identities/*.txt`
- `common/diplomatic_actions/*.txt`
- `common/decisions/*.txt`
- `common/scripted_triggers/*.txt`
- `common/country_definitions/*.txt`
- `localization/english/*.yml`

Then compare against vanilla references:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\power_bloc_names\power_bloc_names.md`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\power_bloc_names\00_power_bloc_names.txt`

## What These Definitions Do

A power bloc name entry is a conditional naming rule. The entry key is also the localization key used for the displayed bloc name.

These files usually decide:
- which name the game selects for a bloc leader and identity combination
- when a highly specific historical or cultural name should override a generic fallback
- how identity, laws, cultures, rivals, allies, geography, religion, and country variables affect bloc naming

These files sit in a wider network:
- `common/power_bloc_identities/` provides the `scope:selected_identity` values most name rules use
- `common/scripted_triggers/` may expose reusable checks such as `coa_monarchy_trigger` or ideology helpers
- `common/decisions/` and scripted effects may create a bloc whose generated name must make sense immediately
- `common/country_definitions/` supplies country tags used in tag-specific naming rules
- `localization/english/*.yml` must contain the visible text for every name key

If you change or add a bloc name, search for:
- `scope:selected_identity`
- the proposed localization key
- the country tags, variables, or scripted triggers used in the rule

## Workflow

1. Inspect the nearest current vanilla names for the same identity, culture, or geography pattern.
2. Search the local mod for any existing use of the name key and for related scripted triggers or country variables.
3. Decide how specific the trigger should be and where it should sit relative to generic fallbacks.
4. Add the rule above broader fallbacks if it is meant to win selection priority.
5. Add or update localization for the entry key in the same change.
6. Re-check every referenced tag, state region, culture, religion, and scripted trigger after editing.

## Common Structure

```pdx
example_bloc_name = {
	trigger = {
		scope:selected_identity = identity:identity_trade_league
		any_primary_culture = {
			has_discrimination_trait = heritage_italic
		}
	}
}
```

## Field Guidance

- Definition key: This is also the localization key for the displayed bloc name. Missing localization produces a broken player-facing result immediately.
- `trigger`: Evaluated in leader scope. Current vanilla explicitly relies on `scope:selected_identity` inside these triggers.
- Rule order: Extremely important. The game walks a list of candidates, so specific names need to appear before generic `always = yes` fallbacks.

## Important Caution

Do not treat `power_bloc_names.md` as sufficient guidance by itself. It only shows the smallest possible stub.

Current vanilla name files demonstrate important behavior that the note file does not explain well:
- order matters because many broad fallback names use `always = yes`
- triggers are evaluated in leader scope
- `scope:selected_identity` is a key special scope used throughout the file
- highly specific names often combine identity checks with tags, laws, cultures, geography, allies, rivals, or variables

Always verify against `00_power_bloc_names.txt` before deciding where to place a new rule.

## Vanilla Patterns

Current vanilla name files show these stable patterns:
- a block of generic names near the top acts as fallback coverage
- many names are identity-specific through `scope:selected_identity = identity:<key>`
- tag-specific names often check `c:<TAG> ?= this`
- geography-driven names often look at capital or owned state regions
- ideology or government-form names often rely on scripted triggers such as `coa_monarchy_trigger`, `coa_republic_trigger`, `coa_fascist_trigger`, or `coa_communist_trigger`
- some names depend on allies, rivals, or `was_formed_from`

## Editing Guidance

- Start from the nearest vanilla naming pattern rather than inventing a trigger style from scratch.
- Keep rules as specific as they need to be, but not broader. A sloppy trigger can steal selection from many unrelated blocs.
- Put historical or country-specific names above generic identity names, and generic identity names above broad fallback names.
- If a trigger depends on a scripted trigger, variable, tag, or region, verify that dependency exists in the local mod or vanilla.
- When overriding vanilla ordering, review the surrounding entries, not just the one you are editing.

## Review Checklist

- The definition lives in `common/power_bloc_names/`.
- The localization key exists and matches the entry key exactly.
- Every referenced tag, scripted trigger, culture, religion, region, or variable exists.
- The trigger is written for leader scope and uses `scope:selected_identity` correctly when identity-specific behavior is intended.
- The rule appears in the correct order relative to more specific and more generic names.
- No generic fallback such as `always = yes` accidentally shadows the new rule above it.
