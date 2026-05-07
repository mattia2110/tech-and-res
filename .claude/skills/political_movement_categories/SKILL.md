---
description: Guide for Victoria 3 political movement category definitions. Use when creating, editing, or reviewing files in common/political_movement_categories, or when political movements need a new movement_category_* key with support thresholds, identity behavior, or ordering.
name: Political Movement Categories
---

# Political Movement Categories

Use this skill for Victoria 3 files in `common/political_movement_categories/*.txt`.

Do not use it for:
- individual political movement definitions by themselves
- pop support factor labels
- journal entry or event logic that only reacts to movements

## File Location

Write modded movement category definitions in:
- `common/political_movement_categories/*.txt`

Inspect the local mod first:
- `common/political_movement_categories/*.txt`
- `common/political_movements/*.txt`

Then compare against vanilla references:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\political_movement_categories\political_movement_categories.md`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\political_movement_categories\00_political_movement_categories.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\political_movements\*.txt`

## What These Definitions Do

A movement category defines high-level behavior shared by many political movements:
- iconography
- whether movements in the category are identity-bound
- support thresholds for creation and maintenance
- display ordering relative to other categories

Individual movement definitions in `common/political_movements/` then reference the category key with `category = movement_category_*`.

## Workflow

1. Inspect the movement definitions that will use the category before creating a new one.
2. Reuse an existing category unless the support thresholds or identity rules genuinely differ.
3. Set identity flags first, then choose support thresholds that make sense for that identity model.
4. Check the intended display order against vanilla categories so the UI remains predictable.
5. Re-read the consuming movement files after editing to make sure the new category is actually referenced.

## Common Structure

```pdx
movement_category_example = {
	icon = "gfx/interface/icons/political_movement_icons/movement_example.dds"

	cultural_identity = no
	religious_identity = no
	minimum_support_is_within_identity = no

	minimum_support_to_create = 0.05
	minimum_support_to_maintain = 0.025

	sorting_order = 1
}
```

## Field Guidance

- `icon`: 2D icon path shown for movements in this category.
- `cultural_identity`: If `yes`, movements in the category are created with an explicit cultural identity.
- `religious_identity`: If `yes`, movements in the category are created with an explicit religious identity.
- `minimum_support_is_within_identity`: If `yes`, support thresholds only count support within the movement's cultural and/or religious identity.
- `minimum_support_to_create`: Predicted support threshold required to create the movement.
- `minimum_support_to_maintain`: Predicted support threshold required to keep the movement alive after its grace period.
- `sorting_order`: UI ordering field used by current vanilla category files.

## Identity Rules

- `minimum_support_is_within_identity` has no practical effect unless `cultural_identity` or `religious_identity` is enabled.
- Vanilla religious and cultural categories both set their corresponding identity flag and also set `minimum_support_is_within_identity = yes`.
- Non-identity categories such as ideological and pan-national movements use support thresholds without identity scoping.

## Vanilla Patterns

Vanilla currently uses categories such as:
- `movement_category_ideological`
- `movement_category_pan_national`
- `movement_category_cultural`
- `movement_category_cultural_majority`
- `movement_category_religious`

These show that:
- support thresholds vary by category rather than by every movement
- identity-based movements generally require higher support to form
- `sorting_order` is part of the actual current schema even though the vanilla note file is minimal

## Mod Integration

Movement categories are only half the system. Always inspect the political movements that consume them in:
- `common/political_movements/*.txt`

Those files decide:
- ideology and character support
- `pop_support_trigger`
- `pop_support_factors`
- `pop_support_weight`

Do not try to encode per-movement support math in the category file.

## Editing Guidance

- Create a new category only when several movements should share the same identity and threshold rules.
- If the change affects only one movement, prefer editing that movement instead.
- Keep `minimum_support_to_maintain` meaningfully below `minimum_support_to_create` unless the design intentionally creates brittle movements.
- Reuse a vanilla icon path unless the feature also ships matching art.

## Review Checklist

- The category key is defined in `common/political_movement_categories/`.
- Consuming movement files reference the category key correctly.
- Identity flags match the intended type of movement.
- Support thresholds are plausible relative to vanilla categories.
- `sorting_order` places the category where intended in the UI.
