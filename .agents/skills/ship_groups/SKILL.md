---
name: Ship Groups
description: Guide for Victoria 3 ship group definitions. Use when creating, editing, or reviewing files in common/ship_groups, especially when adding naval template categories, wiring ship types to groups, checking ship group icons/colors/categories, or comparing modded ship groups against vanilla.
---

# Ship Groups

Use this skill for Victoria 3 files in `common/ship_groups/*.txt`.

Pair it with:
- `Ship Types` when assigning ships to a group
- `Localization Formatting` when adding or changing player-facing group text

## File Location

Write modded ship group definitions in:
- `common/ship_groups/*.txt`

Inspect the local mod first:
- `common/ship_groups/*.txt`
- `common/ship_types/*.txt`
- `localization/english/*.yml`

Then compare against vanilla references:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\ship_groups\ship_groups.md`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\ship_groups\*.txt`

## What These Definitions Do

Ship groups are broad naval categories used by ship types and the ship designer UI. Vanilla groups include capital ships, cruisers, torpedo craft, and supply ships.

Each ship type must point to a valid `ship_group`. A new group is usually only needed when the mod is adding a genuinely new naval category, not just a new ship type inside an existing category.

## Workflow

1. Check whether an existing vanilla group already fits the intended ship type.
2. Inspect every ship type that references the group before changing its key or category.
3. Reuse vanilla category values unless a new value is proven to be supported.
4. Keep icons and colors distinct enough for the ship designer and naval UI.
5. Set `ai_weight = 0` only for groups that should not be built through normal AI template logic.
6. Verify localization and every `ship_group = <key>` reference after editing.

## Common Structure

```pdx
ship_group_example = {
	icon = "gfx/interface/icons/ships/ship_category/ship_group_example.dds"
	color = { 120 170 220 }

	category = cruiser_group
	ai_weight = 1
}
```

## Field Guidance

- `icon`: UI texture for the group. Reuse a vanilla category icon unless the change adds new art.
- `color`: RGB triplet used in naval UI contexts.
- `category`: Vanilla uses values such as `capital_group`, `cruiser_group`, `torpedo_group`, and `supply`.
- `ai_weight`: Country-scope AI build importance for this group. Keep it comparable to nearby vanilla groups.

## Review Checklist

- The group lives in `common/ship_groups/`.
- Every ship type referencing the group uses the exact key.
- `category` matches a known vanilla pattern.
- `icon` points to an existing texture or intentional new art.
- `color` is valid RGB and visually distinct.
- Localization exists if the group is player-facing.
