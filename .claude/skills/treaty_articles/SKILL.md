---
description: Guide for Victoria 3 treaty article definitions. Use when creating, editing, or reviewing files in common/treaty_articles, especially when adding article inputs, maintenance rules, AI behavior, or war-goal enforcement hooks.
name: Treaty Articles
---

# Treaty Articles

Use this skill for Victoria 3 files in `common/treaty_articles/*.txt`.

Do not use it for:
- `common/diplomatic_actions/*.txt` proposal definitions with no article definition changes
- `common/diplomatic_plays/*.txt` play setup with no treaty article work
- `common/war_goal_types/*.txt` unless the task is specifically about the article side of enforcement

Pair it with:
- `Diplomatic Action` when an action proposes or maintains the treaty relationship
- `Diplomatic Plays` when the article affects automatic support or war behavior
- `War Goal Types` when the article is enforceable through `enforce_treaty_article` or treaty-port style wars

## File Location

Write modded treaty article definitions in:
- `common/treaty_articles/*.txt`

Inspect the local mod first:
- `common/treaty_articles/ztr_goods_transfer.txt`
- `common/treaty_articles/ztr_nuclear_de_escalation_treaty.txt`
- `common/treaty_articles/ztr_pause_nuclear_weapons_development.txt`

Then inspect the surrounding local diplomacy files that create, score, or react to those articles:
- `common/diplomatic_actions/*.txt`
- `common/diplomatic_plays/*.txt`
- `common/ai_strategies/*.txt`
- `common/ai_strategies_vanilla/*.txt`
- `common/script_values/*.txt`
- `common/scripted_triggers/*.txt`
- `common/scripted_effects/*.txt`
- `common/goods/*.txt`
- `common/laws/*.txt`
- `common/company_types/*.txt`
- `common/technology/**/*.txt`
- `localization/english/*.yml`

Use vanilla as the schema source:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\treaty_articles\treaty_articles.md`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\treaty_articles\*.txt`

## What These Definitions Do

A treaty article defines one negotiable clause inside the treaty system. In practice it decides:
- whether the clause is `directed` or `mutual`
- which inputs it requires such as `quantity`, `goods`, `state`, `law_type`, or `country`
- when the clause is visible, possible, ratifiable, maintainable, or withdrawable
- what modifiers and scripted effects apply while active
- how AI proposes, accepts, and values the clause
- whether it can be enforced through a war goal and what that enforcement costs

These files are not isolated. Article behavior is usually split across:
- `common/treaty_articles/` for the article definition itself
- `common/diplomatic_actions/` for proposal entry points or related diplomacy
- `common/diplomatic_plays/` and `common/war_goal_types/` for enforcement
- `common/ai_strategies*` for AI strategic valuation
- `common/script_values/` and `common/scripted_*` for shared logic and large payloads

## Practical Rule

Treat article definitions as treaty payloads, not full features. Before editing one, inspect the whole `common\` chain first:

1. What creates or proposes the article?
2. What inputs does it rely on?
3. Does AI know when to propose or accept it?
4. Can it be enforced through war?
5. Do its effects and maintenance rules match the diplomacy fantasy?

In this repo:
- `ztr_goods_transfer.txt` shows a directed, input-heavy article with AI, non-fulfillment, and `wargoal` support
- `ztr_nuclear_de_escalation_treaty.txt` shows a mutual diplomatic article with maintenance rules and large lifecycle effects

Those are the local patterns to reuse.

## Directed vs Mutual

This distinction matters more than any individual field:
- `kind = directed` gives you article roles `source_country` and `target_country`
- `kind = mutual` gives you `first_country` and `second_country`

Do not mix those role assumptions. A treaty as a whole does not have a single source or target country. Only a directed article does.

## Useful Scope Reminder

When you have an article scope, you can typically access:
- `source_country` and `target_country` for directed articles
- `first_country` and `second_country` for mutual articles
- `treaty`
- `input_<name>` for configured inputs
- `input_market_goods(...)` for goods-based articles when the needed country or market context exists

When you have a treaty scope, you can typically access:
- `first_country`
- `second_country`
- `enforced_on_country`
- `enforcer_country`
- `amended_treaty`
- `remaining_binding_period`
- `binding_period`

## Workflow

1. Check whether the article key already exists in the mod or vanilla.
2. Read the nearest local article of the same shape before writing a new one.
3. Decide up front whether the article is `directed` or `mutual`.
4. Decide which inputs are required and whether each needs a `*_valid_trigger` or AI input filter.
5. Write validation in layers: `visible`, `possible`, `requirement_to_maintain`, then `can_ratify`.
6. Keep ongoing consequences in modifiers, maintenance rules, or lifecycle effects rather than stuffing everything into one block.
7. If the article is enforceable, inspect the relevant war-goal flow before editing `wargoal`.
8. Re-check AI scoring, localization, and any surrounding diplomatic action or play that expects the article to exist.

## Common Structure

```pdx
example_article = {
	kind = directed
	cost = 10

	icon = "gfx/interface/icons/diplomatic_treaties_articles_icons/example.dds"
	maintenance_paid_by = source_country

	flags = {
		can_be_renegotiated
		can_be_enforced
	}

	required_inputs = {
		quantity
		goods
	}

	unlocked_by_technologies = {
		international_trade
	}

	visible = { }
	possible = { }

	requirement_to_maintain = {
		trigger = { }
	}

	can_ratify = { }
	can_withdraw = { }

	goods_valid_trigger = { }
	quantity_min_value = { value = 10 }
	quantity_max_value = { value = 50 }

	on_entry_into_force = { }
	on_withdrawal = { }
	on_break = { }

	ai = {
		treaty_categories = { trade }
		evaluation_chance = { value = 0.1 }
		article_ai_usage = { offer request }
		inherent_accept_score = { value = 0 }
	}

	wargoal = {
		execution_priority = 21
		contestion_type = control_any_target_incorporated_state
		maneuvers = { value = 5 }
		infamy = { value = 10 }
	}
}
```

## Field Guidance

- `kind`: Required. Set to `directed` or `mutual` explicitly.
- `maintenance_paid_by`: Directed-only in practice. Do not set it on mutual articles.
- `flags`: Hardcoded behavior switches. Common current patterns include `can_be_renegotiated`, `can_be_enforced`, and article-identity flags such as `is_goods_transfer` or `is_treaty_port`.
- `required_inputs`: Supported article inputs include `quantity`, `goods`, `state`, `strategic_region`, `company`, `building_type`, `law_type`, and `country`.
- `mutual_exclusions`: Prevents incompatible article combinations inside the same treaty.
- `visible`: First visibility layer. Keep it cheap and broad.
- `possible`: Structural pair validity. This already implies `visible`.
- `requirement_to_maintain`: Ongoing conditions that, when broken, can force the article to fail on update.
- `can_ratify`: Final ratification gate. This already checks maintain requirements, so do not duplicate them unless needed for messaging.
- `can_withdraw`: Optional withdrawal gate for already active treaties.
- `*_valid_trigger`: Filters valid options for each configured input type.
- `quantity_min_value` and `quantity_max_value`: Bound quantity input selection.
- `non_fulfillment`: Use for contravention rules. If `consequences = none`, leave its conditions empty and the contravention count at zero.
- `source_modifier`, `target_modifier`, `mutual_modifier`: Role-based passive effects. `source_modifier` and `target_modifier` must stay empty for mutual articles.
- `on_entry_into_force`, `on_enforced`, `on_withdrawal`, `on_break`: Lifecycle effects. Prefer moving large payloads into scripted effects when possible.
- `ai`: Proposal, filter, and acceptance logic.
- `wargoal`: Defines how article enforcement behaves in war.

## AI Guidance

Treat the `ai` block as required in practice unless the article is intentionally player-only or dormant.

Useful local patterns:
- `ztr_goods_transfer.txt` uses goods-aware filters, quantity picking, acceptance caps, and large `inherent_accept_score` logic
- `ztr_nuclear_de_escalation_treaty.txt` uses `proposal_weight`, `article_ai_usage`, and a diplomacy-heavy acceptance score

Guidance:
- keep `evaluation_chance` nonzero if AI should ever consider the article
- use `article_ai_usage = { offer request }` only when both roles make sense
- keep most reusable math in script values or shared triggers if the block gets large
- use `contextual_accept_score` only when treaty-level context is actually needed

## War Goal Link

`can_be_enforced` and the `wargoal` block connect treaty articles to war-goal gameplay.

Before editing that part:
- inspect `common/war_goal_types/enforce_treaty_article` in vanilla
- check whether the article's inputs make sense when converted into a war demand
- verify `contestion_type`, `maneuvers`, and `infamy` fit the article's actual stakes
- search `common/ai_strategies*` for AI logic that already values enforcement of this article type

## Mod-Aware Guidance

Local article patterns matter more than the old note file alone:
- `ztr_goods_transfer.txt` shows a vanilla override with custom goods, AI, and quantity math
- `ztr_nuclear_de_escalation_treaty.txt` shows a new mutual article that uses rank checks, nuclear capability gating, and treaty lifecycle effects
- article behavior in this repo is spread across diplomacy, AI, scripted effects, and technology files, not just `common/treaty_articles`

Always inspect the nearby local `common\` files before copying from vanilla.

## Review Checklist

- The definition lives in `common/treaty_articles/`.
- `kind` matches the scopes used everywhere in the file.
- Directed-only fields are not used on mutual articles.
- Every required input has the needed validation and AI support.
- `visible`, `possible`, `requirement_to_maintain`, and `can_ratify` are layered cleanly instead of duplicating each other.
- Non-fulfillment rules are internally coherent.
- Every referenced good, law, company, technology, play, or script key exists.
- If the article is enforceable, its `wargoal` block and related AI logic still make sense.
- Localization exists for the article name, description, and any custom tooltips or acceptance reasons.
