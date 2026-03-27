---
description: Guide for Victoria 3 war goal type definitions. Use when creating, editing, or reviewing files in common/war_goal_types, especially when adding a new war demand, overriding vanilla validation, or tracing how a war goal interacts with plays, treaty articles, and AI strategy.
name: War Goal Types
---

# War Goal Types

Use this skill for Victoria 3 files in `common/war_goal_types/*.txt`.

Do not use it for:
- `common/diplomatic_plays/*.txt` edits with no war-goal type changes
- `common/diplomatic_actions/*.txt` war creation logic with no new or changed war-goal definition
- treaty article work where the real change belongs in `common/treaty_articles/*.txt`

Pair it with:
- `Diplomatic Plays` when the play grants or constrains the war goal
- `Diplomatic Action` when an action creates the play or manually adds the goal
- `Treaty Articles` when the war goal enforces or converts into treaty article content

## File Location

Write modded war goal definitions in:
- `common/war_goal_types/*.txt`

Use vanilla as the schema and pattern source:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\war_goal_types\war_goal_types.md`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\war_goal_types\*.txt`

This repo does not currently contain a local `common/war_goal_types/` folder, so if you add or override a war goal here you will usually also need to inspect the surrounding local consumers first:
- `common/diplomatic_plays/*.txt`
- `common/diplomatic_actions/*.txt`
- `common/ai_strategies/*.txt`
- `common/ai_strategies_vanilla/*.txt`
- `common/treaty_articles/*.txt`
- `common/script_values/*.txt`
- `common/scripted_triggers/*.txt`
- `common/scripted_effects/*.txt`
- `localization/english/*.yml`

In this repo, current local files already depend on vanilla war goal keys such as:
- `contain_threat`
- `humiliation`
- `take_treaty_port`
- `enforce_treaty_article`

## What These Definitions Do

A war goal type is the scripted definition of a war demand. In practice it decides:
- which hardcoded `kind` supplies the base engine behavior
- which `settings` control validation, conflict handling, targeting, and availability
- what `target_type` the diplomatic-play UI loops over
- how the goal is listed, validated, costed, and enforced
- whether AI treats it as significant

War goal types are tightly coupled to other `common/` content:
- `common/diplomatic_plays/` chooses which goal starts a play
- `common/diplomatic_actions/` may create plays or add goals directly
- `common/treaty_articles/` can be created or enforced by specific war-goal kinds
- `common/ai_strategies*` often scores attitudes toward specific war-goal keys

## Practical Rule

Do not treat a war goal type as a one-file feature.

Before editing one, check:
1. Which diplomatic play grants or expects it?
2. Whether a diplomatic action manually adds it with `add_war_goal`.
3. Whether AI strategy files mention the goal by key.
4. Whether the `kind` creates or depends on treaty articles.
5. Whether the requested change really belongs in the war goal, or instead in the play, action, treaty article, or AI file around it.

In this repo that matters immediately:
- `common/diplomatic_plays/ztr_diplomatic_plays_nuclear.txt` starts from `contain_threat`
- `common/diplomatic_actions/ztr_unprovoked_nuclear_strike.txt` manually adds `humiliation`
- `common/ai_strategies_vanilla/ztr_default_strategy.txt` reacts to `take_treaty_port` and `enforce_treaty_article`

## Workflow

1. Search the mod for the war-goal key and all direct references before changing anything.
2. Inspect the nearest vanilla war goal of the same `kind` and `target_type`.
3. Check the linked diplomatic play and any `add_war_goal` call sites.
4. Decide whether the behavior needs a new war-goal type or just different play or action scripting.
5. Write or override the war goal in `common/war_goal_types/*.txt`.
6. Re-check AI strategy files, treaty article links, and localization.
7. If the goal changes enforcement behavior, verify its execution order and conflict settings against related goals.

## Common Structure

```pdx
example_war_goal = {
	icon = "gfx/interface/icons/war_goals/example.dds"

	kind = custom

	settings = {
		require_target_be_part_of_war
		validate_conflicts_war_goals_all
	}

	execution_priority = 80
	contestion_type = control_target_country_capital
	target_type = country

	possible = { }
	valid = { }

	maneuvers = {
		value = 10
	}

	infamy = {
		value = 15
	}

	on_enforced = { }

	ai = {
		is_significant_demand = yes
	}
}
```

## Scope Guidance

The common scopes for `possible`, `valid`, `maneuvers`, `infamy`, and `on_enforced` are:
- `root` for the holder country
- `creator_country`
- `diplomatic_play`
- `target_country`
- `target_state`
- `stakeholder`
- `target_region`
- `article_options`

Do not assume all of those scopes are populated meaningfully for every `target_type`. For example, `article_options` mainly matters for treaty-article enforcement flows.

## Field Guidance

- `icon`: Usually something in `gfx/interface/icons/war_goals/`.
- `kind`: Required engine behavior package. Many war goals only work because their kind and settings match.
- `settings`: Hardcoded validation and conflict switches. Common important patterns include target participation checks, subject validation, and conflict-marking settings.
- `execution_priority`: Peace-deal execution order. Higher values execute earlier.
- `contestion_type`: What must be controlled for enforcement.
- `target_type`: Usually `country`, `state`, or `treaty_article` in script terms. This heavily affects how the game generates possible target options.
- `possible`: Whether the goal appears for selection.
- `valid`: Extra script validation on top of code-side checks.
- `maneuvers`: Cost to add the goal.
- `infamy`: Infamy cost to claim the goal.
- `on_enforced`: Extra script effect in addition to the predefined `kind` payload.
- `ai.is_significant_demand`: High-level AI importance flag.

## Kind Guidance

The `kind` is not cosmetic. It is the engine contract for the war goal.

Common current vanilla kinds include:
- conquest and annexation kinds such as `annex_country` and `conquer_state`
- subject and autonomy kinds such as `make_dominion`, `reduce_autonomy`, and `independence`
- treaty-conversion kinds such as `enforce_treaty_article`, `foreign_investment_rights`, `take_treaty_port`, and `ban_slavery`
- `custom` when only `on_enforced` should do the real work

If a kind converts into treaty content, inspect the target treaty article too. Do not change one side and ignore the other.

## Settings Guidance

Do not memorize a static list of settings and assume they are always safe to mix freely. Use the nearest vanilla file as the model.

Common settings categories:
- participation and targeting: `require_target_be_part_of_war`, `can_add_for_other_country`, `requires_interest`
- relationship validation: `validate_subject_relation`, specialized `validate_*` settings for specific kinds
- conflict handling: `validate_conflicts_*` and `conflicts_with_*`
- targeting shape: `targets_enemy_subject`, `targets_enemy_claims`, `has_other_stakeholder`
- UI and availability: `skip_build_list`

If a war goal stops appearing, conflicts strangely, or enforces against the wrong thing, the problem is often in `settings`, not `possible`.

## Treaty Article Link

Some war goals are really diplomacy-to-treaty bridges.

Examples from vanilla:
- `enforce_treaty_article` targets `treaty_article`
- `foreign_investment_rights` converts into an investment-rights article
- `take_treaty_port` converts into a treaty-port article
- `ban_slavery` converts into a law-commitment article

For those, always inspect:
- the article definition in `common/treaty_articles/`
- whether the article is actually enforceable or valid for the intended target
- any AI strategy logic that reacts to the war-goal key or resulting treaty condition

## Mod-Aware Guidance

Although this repo lacks a local `common/war_goal_types/` folder today, war goals already matter across local `common\` files:
- the nuclear diplomatic play uses vanilla `contain_threat`
- the unprovoked nuclear strike action adds `humiliation`
- local AI strategy overrides check for war goals such as `take_treaty_port` and `enforce_treaty_article`

That means any new local war-goal override here should be reviewed together with local plays, actions, and AI strategy files before finalizing it.

## Review Checklist

- The definition lives in `common/war_goal_types/`.
- `kind`, `target_type`, and `settings` match the intended gameplay.
- `possible` and `valid` are not fighting the hardcoded kind requirements.
- `execution_priority` still makes sense relative to related war goals.
- `contestion_type` matches the demand fantasy and target shape.
- Maneuvers and infamy scales are coherent with similar vanilla goals.
- Every linked play, action, treaty article, AI strategy reference, and localization key still makes sense after the change.
- If the goal bridges into treaty content, the article side was checked too.
