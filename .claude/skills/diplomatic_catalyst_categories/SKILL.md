---
description: Guide for Victoria 3 diplomatic catalyst category definitions. Use when creating, editing, or reviewing files in common/diplomatic_catalyst_categories, or when a diplomatic catalyst in common/diplomatic_catalysts needs a category such as cc_* with duration, cooldown, or lobby formation behavior.
name: Diplomatic Catalyst Categories
---

# Diplomatic Catalyst Categories

Use this skill for Victoria 3 files in `common/diplomatic_catalyst_categories/*.txt`.

Do not use it for:
- `common/diplomatic_catalysts/*.txt`
- `common/diplomatic_actions/*.txt`
- `common/political_lobbies/*.txt`
- generic diplomacy scripting outside a catalyst category definition

## File Location

Write modded diplomatic catalyst category definitions in:
`common/diplomatic_catalyst_categories/*.txt`

Use the base game for reference at:
`C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\diplomatic_catalyst_categories\*.txt`

If this mod does not already contain `common/diplomatic_catalyst_categories`, create that folder and place the new file there rather than mixing the definition into another diplomacy folder.

Related files usually live in:
- `common/diplomatic_catalysts/*.txt`
- `common/diplomatic_actions/*.txt`
- `common/ai_strategies/*.txt`
- `common/script_values/*.txt`
- `common/scripted_triggers/*.txt`
- `common/scripted_effects/*.txt`
- `localization/english/*.yml`

## Workflow

1. Check whether the requested `cc_*` key already exists in the mod with `rg "cc_[^\\s=]*\\s*=\\s*\\{" common/diplomatic_catalyst_categories`.
2. If the key does not exist, inspect the vanilla `game/common/diplomatic_catalyst_categories/` files for field names, formatting, and naming patterns.
3. Read every catalyst in `common/diplomatic_catalysts/*.txt` that will reference the category, because the category only matters through those catalyst definitions.
4. Add or update the category in `common/diplomatic_catalyst_categories/*.txt`.
5. Verify the category's `duration`, `catalyst_effect_cooldown`, and optional `lobby_formation_reason` match the intended catalyst behavior.
6. Recheck the actions or effects that create the catalyst so the category timing still makes sense in play.

## Structure

```pdx
cc_example = {
	lobby_formation_reason = diplomacy

	duration = {
		years = 5
	}

	catalyst_effect_cooldown = {
		years = 1
	}
}
```

## Core Fields

- `lobby_formation_reason`: Optional. Tags the reason a lobby formed from catalysts in this category. Use valid game-supported values such as `diplomacy`, `defense`, `ideology`, `funded`, `aggression`, `religion`, `technology`, `economy`, or `none` if that matches the vanilla set you confirm.
- `duration`: Required. How long the catalyst remains in the game state before being removed. It must be greater than zero.
- `catalyst_effect_cooldown`: Optional. Cooldown before another catalyst in the same category can attempt lobby creation or AI strategic desire recalculation. It must not be longer than `duration`.

Keep time blocks explicit. Prefer the standard Paradox duration format:

```pdx
duration = {
	years = 2
}
```

## Interaction With Diplomatic Catalysts

Category definitions do not do much by themselves. They exist so entries in `common/diplomatic_catalysts/*.txt` can share behavior and timing:

```pdx
catalyst_example = {
	category = cc_example

	effect = { }
}
```

When editing a category, always verify:
- Every referencing catalyst uses the intended `category = cc_*` key.
- The cooldown is not longer than the duration.
- A category meant to influence lobby creation has a `lobby_formation_reason` that matches the political design.
- The timing still fits the diplomatic action, event, or effect that creates the catalyst.

## Mod-Aware Guidance

This mod already uses catalysts from diplomatic actions, for example in `common/diplomatic_actions/ztr_invite_to_power_bloc.txt`. When adding or changing a category:
- inspect the catalyst definition that the action creates
- inspect the action or scripted effect that calls `create_diplomatic_catalyst`
- keep names stable once actions, localization, or scripted content refer to the category

If the repo has no existing `common/diplomatic_catalyst_categories` folder yet, that is not a reason to skip the skill. Use the vanilla `common\` folder as the schema source and create the mod folder in the same structure.

## Template

```pdx
cc_example = {
	lobby_formation_reason = diplomacy

	duration = {
		months = 24
	}

	catalyst_effect_cooldown = {
		months = 6
	}
}
```

## Editing Guidance

- Prefer `cc_*` names that describe the catalyst behavior, not a single one-off action.
- Keep category definitions minimal; do not inline catalyst logic here.
- If multiple catalysts should share timing and lobby behavior, point them at the same category instead of duplicating near-identical categories.
- If the requested behavior differs only for one catalyst, consider whether the catalyst definition itself should change instead of introducing a new category.
- Do not confuse catalyst categories with diplomatic plays, actions, or AI strategies; those are neighboring systems in `common\`, not substitutes for this folder.
