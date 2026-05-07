---
name: Trigger Localization
description: Guide for Victoria 3 trigger localization definitions. Use when creating, editing, or reviewing files in common/trigger_localization, especially when adding or overriding human-readable text for triggers, scripted triggers, scope comparisons, comparator specializations, and any/all/count trigger tooltips.
---

# Trigger Localization

Use this skill for Victoria 3 files in `common/trigger_localization/*.txt`.

Do not use it for:
- `common/scripted_triggers/*.txt` logic changes by themselves
- normal localization `.yml` editing with no trigger-localization definition change
- tooltip text embedded directly in events, journal entries, or scripted buttons

Pair it with:
- `Scripted Triggers` when the trigger key itself is being added or changed
- `Localization Formatting` when you also need to add or correct the referenced localization strings

## File Location

Write modded trigger-localization definitions in:
- `common/trigger_localization/*.txt`

Inspect the local mod first:
- `common/scripted_triggers/*.txt`
- the exact consumer that shows the tooltip
- `localization/english/*.yml`

Then compare against vanilla references:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\trigger_localization\00_trigger_localization.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\trigger_localization\01_scripted_triggers_localization.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\trigger_localization\02_geographic_regions_localization.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\trigger_localization\trigger_localization.md`

In this repo there is currently no local `common/trigger_localization/` folder, so most work here will also require adding the referenced text keys in normal localization files.

## What These Definitions Do

Trigger localization maps a trigger key to readable tooltip text. It does not change gameplay logic. It decides:
- which text is shown in global, first-person, third-person, or none-scope contexts
- which negative text is used for `NOT`
- how comparator variants such as `_greater_or_equal` are phrased
- how `any_*`, `*_all`, `*_count`, and `*_percent` trigger families read in tooltips
- how scope comparison keys describe subject and object relationships

These files sit between scripted logic and UI presentation:
- the trigger or scripted trigger provides the boolean check
- `common/trigger_localization/` tells the game how to explain that check to the player
- `localization/english/*.yml` provides the actual text keys referenced here

## Workflow

1. Inspect the exact trigger key and tooltip context before writing localization for it.
2. Search vanilla for the key and any related suffix families before creating a new entry.
3. Define only the forms that are actually needed: `global`, `first`, `third`, `none`, and their `_not` variants.
4. For comparator triggers, check whether the generic key is enough or whether you need `_equal`, `_greater_than`, `_greater_or_equal`, `_less_than`, `_less_or_equal`, or `_not_equal`.
5. For `any_*` families, verify whether you need regular, `_all`, `_count`, or `_percent` forms.
6. Add or update the referenced localization keys in `.yml` files and verify the tooltip reads naturally in context.

## Common Structure

```pdx
example_trigger = {
	global = EXAMPLE_TRIGGER_GLOBAL
	global_not = EXAMPLE_TRIGGER_GLOBAL_NOT
	first = EXAMPLE_TRIGGER_FIRST
	first_not = EXAMPLE_TRIGGER_FIRST_NOT
	third = EXAMPLE_TRIGGER_THIRD
	third_not = EXAMPLE_TRIGGER_THIRD_NOT
}

example_trigger_greater_or_equal = {
	global = EXAMPLE_TRIGGER_GREATER_OR_EQUAL
	third = EXAMPLE_TRIGGER_GREATER_OR_EQUAL_THIRD
}

any_example_scope = {
	global = ANY_EXAMPLE_SCOPE
	first = ANY_EXAMPLE_SCOPE_FIRST
}
any_example_scope_count = {
	global = ANY_EXAMPLE_SCOPE_COUNT
	first = ANY_EXAMPLE_SCOPE_COUNT_FIRST
	third = ANY_EXAMPLE_SCOPE_COUNT_THIRD
}
```

## Field Guidance

- Top-level key: Usually the trigger key itself or a specialized suffix form.
- `global`, `first`, `third`, `none`: Context-specific localization mapping.
- `global_not`, `first_not`, `third_not`, `none_not`: Optional explicit negative override keys.
- Comparator specialization keys such as `<trigger>_greater_or_equal`: Use when one phrasing does not fit every operator.
- `any_*`, `*_all`, `*_count`, `*_percent`: Separate trigger-localization entries for each tooltip family.

## Important Caution

Trigger localization has a few easy-to-miss rules:
- missing forms fall back to the last available form, which can produce awkward person or scope wording
- negative `any_*` checks are not always simple `NOT_` versions; the engine can invert the any/all/count logic
- scope comparison text must read clearly even when the raw script expression is complex
- these files only point at localization keys; they do not replace the `.yml` text itself

Always test the tooltip wording from the player's perspective, not just the script author's perspective.

## Editing Guidance

- Copy the closest vanilla key family instead of inventing a new naming scheme.
- Keep positive and negative wording semantically aligned with the real boolean test.
- Prefer the smallest complete set of forms needed by the consuming UI.
- If a trigger is used in multiple scopes, verify each rendered sentence still makes sense when `ROOT`, `TARGET_*`, or saved scopes differ.
- Treat count and comparator trigger families as their own mini-APIs. Missing one specialized entry can make the fallback text misleading.

## Review Checklist

- The definition lives in `common/trigger_localization/`.
- The top-level key matches a real trigger or specialized trigger-localization family.
- Required `global`, `first`, `third`, `none`, and `_not` mappings are present where needed.
- Any comparator or `any_*` suffix family is complete enough for the intended usage.
- Every referenced localization key exists in `.yml`.
- The wording still reads correctly in the actual tooltip context.
