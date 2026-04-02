---
description: Guide for Victoria 3 commander order definitions. Use when creating, editing, or reviewing files in common/commander_orders, including new army or navy orders, trait-gated order variants, vanilla order overrides, and order AI behavior.
name: Orders
---

# Commander Orders

Use this skill for Victoria 3 files in `common/commander_orders/*.txt`.

Do not use it for:
- battle condition definitions by themselves
- mobilization options
- character interactions outside an order definition

## File Location

Write modded order definitions in:
- `common/commander_orders/*.txt`

Inspect the local mod first:
- `common/commander_orders/*.txt`

Then compare against vanilla references:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\commander_orders\orders.md`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\commander_orders\*.txt`

Related files often include:
- `localization/english/*.yml`
- `gfx/interface/icons/commander_order_icons/*`
- neighboring military content that the order's modifiers interact with

## Workflow

1. Inspect the closest vanilla order with the same intended behavior before writing a new one.
2. Decide whether the task is a new order, a trait-specific variant of an existing order, or a vanilla override.
3. Keep `visible` broad and UI-facing, and keep `possible` to the actual selection gate.
4. Reuse an existing `behavior` unless there is strong evidence the engine supports a new one.
5. Treat the modifier block as the gameplay core of the order and keep the math easy to compare against vanilla.
6. Check icon path, animation, sound, and localization before finishing.

## Common Structure

```pdx
order_example = {
	texture = "gfx/interface/icons/commander_order_icons/advance.dds"

	military_type = army
	behavior = advance

	is_basic_order_type = yes

	visible = {
		has_role = general
	}

	possible = {
		has_role = general
	}

	modifier = {
		character_advancement_speed_add = 5
		unit_offense_mult = 0.1
	}

	entity_animation = "advance"
	clicksound = "event:/SFX/UI/Military/order_general_front_advance"
	experience = 0.1

	ai_weight = {
		value = 1
	}
}
```

## Field Guidance

- `texture`: Order icon path. Reuse an existing commander-order icon unless the feature also adds new art.
- `military_type`: Must match the intended command branch, typically `army` or `navy`.
- `behavior`: Core engine behavior such as `advance`, `defend`, `intercept`, `blockade`, `raid_convoys`, or `escort_convoys`. Copy a valid vanilla behavior.
- `is_basic_order_type`: Mark the baseline order in a behavior family. Trait variants usually omit this.
- `visible`: Whether the order appears in the UI for that commander.
- `possible`: Whether the commander can currently select the order.
- `modifier`: Character and combat modifiers applied while the order is active.
- `entity_animation`: World-map animation key, commonly aligned with the behavior.
- `clicksound`: UI sound event.
- `escape_power_ratio`: Optional. Used by some naval orders such as convoy raiding.
- `experience`: Extra character experience gained while using the order.
- `ai_weight`: Script-value style AI preference block used by current vanilla order files.

## Important Caution

Vanilla's note file warns that `has_trait` inside `possible` is brittle for UI datatrait parsing. A negative check like `NOT = { has_trait = child }` can be interpreted as though the trait unlocks the order. Prefer positive trait gates and straightforward conditions when possible.

## Vanilla Patterns

Vanilla commander orders currently follow these patterns:
- one base order per behavior with `is_basic_order_type = yes`
- trait-based variants sharing the same `behavior`, icon, and animation
- `ai_weight` values rather than the older `ai_order_type` note in `orders.md`
- `entity_animation` on army front orders such as `advance` and `defend`
- `escape_power_ratio` only on specific naval behaviors such as convoy raiding

## Editing Guidance

- Copy the nearest vanilla order family and trim it down instead of inventing structure from scratch.
- Keep variants within the same behavior family visually consistent unless the change is meant to be obvious in the UI.
- Do not assume `orders.md` is exhaustive; verify fields against the current `.txt` files.
- If creating a trait-specific variant, make sure a non-trait baseline order still exists for commanders without that trait.
- Keep `ai_weight` proportional to nearby variants so the AI does not ignore or overuse the new order.

## Review Checklist

- The order lives in `common/commander_orders/`.
- `military_type` and `behavior` match a valid existing pattern.
- `visible` and `possible` use commander scope correctly.
- `modifier` values are in line with comparable vanilla orders.
- `texture`, `entity_animation`, and `clicksound` point to real assets or reused vanilla assets.
- Trait gating is readable and does not rely on brittle negative `has_trait` parsing.
