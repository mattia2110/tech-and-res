---
name: Pop Needs
description: Guide for Victoria 3 pop need definitions. Use when creating, editing, or reviewing files in common/pop_needs, especially when adding new pop needs, overriding vanilla need baskets, or tracing how goods substitution, buy packages, prestige goods, and pop consumption behavior connect across common/ content.
---

# Pop Needs

Use this skill for Victoria 3 files in `common/pop_needs/*.txt`.

Do not use it for:
- buy package quantity tuning with no pop need definition change
- goods production or price balance changes by themselves
- pop type edits that only reference existing need keys

Pair it with:
- `Pop Types` when the demand change is part of a wider profession or lifestyle rebalance
- `Localization Formatting` when a new `popneed_*` key needs player-facing text

## File Location

Write modded pop need definitions in:
- `common/pop_needs/*.txt`

Inspect the local mod first:
- `common/pop_needs/*.txt`
- `common/buy_packages/*.txt`
- `common/goods/*.txt`
- `common/pop_types/*.txt`
- `common/prestige_goods/*.txt`
- `localization/english/*.yml`

Then compare against vanilla references:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\pop_needs\00_pop_needs.txt`

Current local pattern matters here:
- `ztr_pop_needs.txt` uses `INJECT:` and `REPLACE:`
- `ztr_mr_pop_needs.txt` also uses `REPLACE_OR_CREATE:`

## What These Definitions Do

A pop need definition is a substitution basket for one demand category. It usually decides:
- which good is the fallback `default`
- which goods can satisfy the need
- how strongly each good is favored through `weight`
- how much market supply share can influence that weight
- whether obsession and prestige-good logic amplify demand for the basket

These files are only one half of pop consumption:
- `common/buy_packages/` decides how much of each `popneed_*` pops try to buy at each wealth level
- `common/pop_needs/` decides which goods can satisfy that demand

If you add a new pop need but never reference it from buy packages, it will exist on disk but have no gameplay effect.

## Workflow

1. Search the local mod and vanilla for the target `popneed_*` key before editing.
2. Decide whether the change should be `INJECT:`, `REPLACE:`, or `REPLACE_OR_CREATE:`.
3. Check the relevant `common/buy_packages/*.txt` entries so the need amount and basket stay aligned.
4. Verify every referenced good exists and is realistically supplied in the economy you are designing for.
5. Re-check obsession, prestige-good, and fallback behavior after editing.
6. Add localization if the task introduces a truly new `popneed_*` key.

## Common Structure

```pdx
REPLACE_OR_CREATE:popneed_example = {
	default = services

	obsession_demand_min = 0.5
	obsession_demand_mult = 1.5
	prestige_goods_demand_increase = 0.5

	entry = {
		goods = services
		weight = 1
		max_supply_share = 1.0
		min_supply_share = 0.0
	}

	entry = {
		goods = radios
		weight = 1.25
		max_supply_share = 0.25
		min_supply_share = 0.0
	}
}
```

## Field Guidance

- Definition key: Usually a `popneed_*` key. Keep it stable once buy packages or localization reference it.
- Patch operator: In this mod, `INJECT:` appends entries to an existing vanilla key, `REPLACE:` replaces the whole key, and `REPLACE_OR_CREATE:` handles mixed environments.
- `default`: Fallback good when substitution logic has no better option.
- `obsession_demand_min`, `obsession_demand_mult`, `prestige_goods_demand_increase`: Optional demand amplifiers for obsession and prestige behavior.
- `entry`: One good option inside the basket.
- `goods`: Good key from `common/goods/`.
- `weight`: Base attractiveness before supply-share effects.
- `max_supply_share`: Cap on how much supply share can increase effective weight.
- `min_supply_share`: Minimum share of the base weight applied even when supply is scarce.

## Important Caution

Pop need edits are economy-wide.

Common failure modes are:
- replacing a vanilla basket and accidentally dropping an important fallback good
- adding a new luxury or utility good with no practical supply path
- creating a new `popneed_*` key but never adding it to any buy package
- forgetting localization for a new player-facing need key

Always check both `common/pop_needs/` and `common/buy_packages/` before treating the change as complete.

## Editing Guidance

- Prefer `INJECT:` for small basket extensions and `REPLACE:` only when the whole substitution logic truly changes.
- Keep the basket explainable. If you cannot summarize why each good belongs in the need, the basket is probably doing too much.
- Compare replacement baskets against vanilla line by line. Missing one fallback entry can create odd price spikes or dead consumption paths.
- Use `REPLACE_OR_CREATE:` only when compatibility or mod-stack uncertainty justifies it.
- When adding a new high-tech or prestige consumption path, confirm the wider mod already has the goods, production, and buy-package support to sustain it.

## Review Checklist

- The definition lives in `common/pop_needs/`.
- The patch operator matches the intended override behavior.
- Every referenced good key exists.
- The `default` good is sensible and still available.
- Any obsession or prestige-demand fields are intentional.
- New or changed baskets are reflected appropriately in `common/buy_packages/`.
- New `popneed_*` keys have localization.
- The edited basket does not remove an important vanilla fallback by accident.
