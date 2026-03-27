---
name: Mobilization Option Groups
description: Guide for Victoria 3 mobilization option group definitions. Use when creating, editing, or reviewing files in common/mobilization_option_groups, adding new UI groups for mobilization options, overriding vanilla group ordering, or checking group icons and weights.
---

# Mobilization Option Groups

Use this skill for group definitions in `common/mobilization_option_groups/`.

## File Location

Write modded definitions in:
- `common/mobilization_option_groups/*.txt`

Use references from:
- `common/mobilization_option_groups/ztr_mobilization_option_groups.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\mobilization_option_groups\00_mobilization_option_groups.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\mobilization_option_groups\mobilization_option_groups.md`

Always inspect `common/mobilization_options/*.txt` before editing a group. Every `group = ...` on an option must point to a real group key.

## Workflow

1. Decide whether the task is a new group, a vanilla override, or a rebalance of UI ordering.
2. Search `common/mobilization_options/` for all options that use the group key.
3. Keep the group key stable unless every referencing mobilization option is updated as well.
4. Set a valid `icon` path that matches the feature family.
5. Set `weight` deliberately; mobilization option groups sort from highest to lowest weight in the UI.
6. If the task is an override, follow the mod's existing wrapper pattern such as `REPLACE_OR_CREATE:group_key = { ... }`.

## Structure

```pdx
REPLACE_OR_CREATE:group_key = {
	icon = "gfx/interface/icons/generic_icons/generic_concept_icon.dds"
	weight = 0
}
```

## Core Fields

- `icon`: UI art for the group header.
- `weight`: Higher values sort earlier in the mobilization UI; lower or negative values push the group later.

This definition layer is only about presentation and ordering. It does not contain gameplay triggers, costs, or unit effects.

## Mod Patterns

This mod currently defines groups like:
- `tactical_weapons`
- `air_support`
- `extra`
- `nuclear_weapons`
- `electronic_support`
- `remote_support`

All of them live in `common/mobilization_option_groups/ztr_mobilization_option_groups.txt` and use `REPLACE_OR_CREATE:`.

## Editing Guidance

- Prefer reusing an existing icon family for related military concepts.
- Keep weights spaced enough that later inserts do not force a full renumbering.
- Do not create a new group when a feature clearly belongs in an existing group.
- If a group is renamed, update every mobilization option that references it before finishing.

## Review Checklist

- The group key is defined exactly once in the modded file set.
- Every mobilization option using the key still resolves to this group.
- The icon path exists and matches the intended UI category.
- The weight places the group in the intended UI order relative to neighboring groups.
