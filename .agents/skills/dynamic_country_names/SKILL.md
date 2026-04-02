---
name: Dynamic Country Names
description: Guide for Victoria 3 dynamic country name definitions. Use when creating, editing, or reviewing files in common/dynamic_country_names, adding conditional names or adjectives for a tag, or checking how dynamic country names interact with country definitions, scripted triggers, revolutions, subjects, and localization.
---

# Dynamic Country Names

Use this skill for country renaming logic stored in `common/dynamic_country_names`.

## Target Files

- Put mod definitions in `common/dynamic_country_names/`.
- Group entries by tag inside one or more `.txt` files.
- Add matching localization keys in `localization/`, saved as UTF-8-BOM `.yml`.
- Check nearby dependencies when needed:
  - `common/country_definitions/` for the base tag and fallback name
  - `common/scripted_triggers/` if the trigger should be reused
  - `common/scripted_effects/`, `common/on_actions/`, `common/journal_entries/`, or events if variables or laws used by the trigger are set elsewhere

If the mod does not already have `common/dynamic_country_names/`, create it.

## Structure

Each top-level key is a country tag or `DEFAULT`.

```victoria3
TAG = {
	dynamic_country_name = {
		name = some_loc_key
		adjective = some_loc_key_adj

		is_main_tag_only = yes
		priority = 0

		trigger = {
			# country-scope trigger
		}
	}
}
```

Use `DEFAULT` only for generic rules that can apply to many countries, such as revolutionary or subject naming patterns. Use specific tags for country-specific names.

## Field Reference

### `name`

- Localization key for the displayed country name.
- May be omitted or left empty to keep the base country name.

### `adjective`

- Localization key for the adjective.
- May be omitted or left empty to keep the base adjective.

### `is_main_tag_only`

- Default is `no`.
- Set to `yes` when the name should only apply to the primary tag definition, not to dynamic splinters or composite countries using that tag.
- Vanilla uses this frequently for formables, regional variants, and subject identities that should not leak to derived tags.

### `priority`

- Default is `0`.
- Higher priority wins when multiple valid entries exist.
- If priorities tie, the first valid entry in file order wins.
- Reserve very high values for hard overrides and emergency fallbacks only.

### `trigger`

- Evaluated in country scope.
- Keep it narrow and deterministic.
- Prefer checking the actor country's laws, government type, subject status, capital region, variables, primary cultures, religion, and formation history.
- If the same logic is reused in multiple places, move it to `common/scripted_triggers/`.

## Common Patterns

### Government or ideology rename

Use for republic, monarchy, communist, fascist, theocratic, or corporate-state variants.

```victoria3
TAG = {
	dynamic_country_name = {
		name = dyn_c_example_republic
		adjective = TAG_ADJ
		is_main_tag_only = yes
		priority = 10

		trigger = {
			has_law_or_variant = law_type:law_parliamentary_republic
		}
	}
}
```

### Subject or overlord rename

Use when the country's name depends on being a colony, dominion, chartered company, or other subject.

Check for:
- `is_subject = yes`
- `is_subject_of = c:TAG`
- `is_subject_type = ...`
- `any_overlord_or_above = { ... }`

### Regional rename

Use when the name depends on capital location, owned states, or broader region.

Common checks:
- `capital = { state_region = s:STATE_* }`
- `capital = { region = sr:region_* }`
- `owns_entire_state_region = STATE_*`
- `any_scope_state = { ... }`

### Revolutionary or civil war rename

Use `DEFAULT` when the pattern is generic across tags. Vanilla commonly checks `scope:actor`, revolution state, secession status, and movement variables.

## Workflow

1. Identify whether the rule belongs under a specific tag or `DEFAULT`.
2. Check vanilla `game/common/dynamic_country_names/00_dynamic_country_names.txt` for an existing pattern before inventing a new one.
3. Write the narrowest trigger that matches the intended political, regional, or subject condition.
4. Set `priority` relative to existing competing names for the same tag.
5. Add `name` and `adjective` localization keys.
6. Verify the base tag still has a normal fallback in `common/country_definitions/` and localization.

## Template

```victoria3
TAG = {
	dynamic_country_name = {
		name = dyn_c_example_name
		adjective = dyn_c_example_name_adj

		is_main_tag_only = yes
		priority = 1

		trigger = {
			exists = scope:actor
			scope:actor ?= {
				# add the smallest set of conditions needed
			}
		}
	}
}
```

## Review Checklist

- The file is in `common/dynamic_country_names/`.
- The tag key is valid, or `DEFAULT` is intentionally used.
- The trigger is country-scope and references valid game data.
- `priority` is chosen relative to competing names for that tag.
- `is_main_tag_only` matches the intended behavior.
- Every `name` and `adjective` key exists in localization.
- Localization keeps fallback vanilla or modded base names intact when the trigger is false.
