---
description: Guide for Victoria 3 diplomatic play definitions. Use when creating, editing, or reviewing files in common/diplomatic_plays, especially when adding a new dp_* play, overriding a vanilla play, or tracing how a play is launched from diplomatic actions, events, AI, war goals, and other neighboring common/ folders.
name: Diplomatic Plays
---

# Diplomatic Plays

Use this skill for Victoria 3 files in `common/diplomatic_plays/*.txt`.

Do not use it for:
- `common/diplomatic_actions/*.txt`
- `common/war_goal_types/*.txt`
- `common/diplomatic_catalysts/*.txt`
- event-only `create_diplomatic_play` call sites when no play definition is being changed

## File Location

Write modded diplomatic play definitions in:
`common/diplomatic_plays/*.txt`

Use the mod's local examples first:
- `common/diplomatic_plays/ztr_diplomatic_plays_nuclear.txt`

Then inspect the surrounding mod files that launch, constrain, or react to the play:
- `common/diplomatic_actions/ztr_unprovoked_nuclear_strike.txt`
- `common/diplomatic_actions/ztr_nuclear_ultimatum.txt`
- `common/ai_strategies/ztr_default_strategy.txt`
- `common/ai_strategies_vanilla/ztr_default_strategy.txt`
- `events/**/*.txt` files containing `create_diplomatic_play`

Use the base game as the schema source:
`C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\diplomatic_plays\*.txt`

Related files usually live in:
- `common/diplomatic_actions/*.txt`
- `common/war_goal_types/*.txt`
- `common/ai_strategies/*.txt`
- `common/ai_strategies_vanilla/*.txt`
- `common/scripted_effects/*.txt`
- `common/scripted_triggers/*.txt`
- `common/script_values/*.txt`
- `events/*.txt`
- `localization/english/*.yml`

## Practical Rule

Do not treat a diplomatic play as an isolated block in `common/diplomatic_plays`. Always inspect the wider `common\` chain first:

1. Find what creates or references the play.
2. Confirm the referenced `war_goal = ...` exists and matches the intended design.
3. Check AI files for `is_diplomatic_play_type`, boldness, neutrality, or support rules tied to the play.
4. Re-read any diplomatic action or event that calls `create_diplomatic_play`.
5. Verify localization and tooltips still describe the play's real behavior.

In this repo, `dp_annex_war_historical` is defined in `common/diplomatic_plays/ztr_diplomatic_plays_nuclear.txt` but actually matters because:
- `common/diplomatic_actions/ztr_unprovoked_nuclear_strike.txt` creates it
- `common/ai_strategies/ztr_default_strategy.txt` and `common/ai_strategies_vanilla/ztr_default_strategy.txt` react to `dp_contain_threat`
- multiple events create plays directly with `create_diplomatic_play`

That is the pattern to reuse.

## Workflow

1. Check whether the requested play key already exists with `rg "dp_[^\\s=]*\\s*=\\s*\\{" common/diplomatic_plays`.
2. Find every caller and reference with `rg -n "<play_key>|create_diplomatic_play|is_diplomatic_play_type|target_is =|initiator_is =" common events`.
3. If overriding a vanilla play, inspect the original in Victoria 3 `game/common/diplomatic_plays/` and preserve engine-required fields.
4. Read the closest local play in `common/diplomatic_plays/` before writing a new one.
5. Inspect the linked `war_goal` type and any launch site in diplomatic actions or events.
6. Add or update the play definition in `common/diplomatic_plays/*.txt`.
7. Recheck AI strategy files, event callers, and localization so the play behaves coherently outside the definition itself.

## Common Structure

```pdx
dp_example = {
	requires_interest_marker = yes
	enable_switch_sides = no
	allow_negotiated_peace = yes
	mirror_war_goal = no
	initiator_can_add_war_goals = yes
	target_can_add_war_goals = yes
	add_infamy_for_starting_initiator_wargoals = yes
	add_infamy_for_starting_target_wargoals = no
	ai_acceptance_max = 100

	war_goal = humiliation
	is_epic = no

	texture = "gfx/interface/icons/war_goals/humiliation.dds"

	selectable_in_lens = {
		always = yes
	}

	possible = {
		always = yes
	}

	additional_involvement_trigger = {
		always = yes
	}

	on_weekly_pulse = { }
	on_war_begins = { }
	on_war_end = { }
	on_demand_accepted = { }
	on_demand_rejected = { }
}
```

Not every play needs every field. The mod's current nuclear example is intentionally narrow:
- hidden from normal UI with `selectable_in_lens = { always = no }`
- launched from scripted content instead of normal lens flow
- locked to a fixed starting war goal and no added war goals

## Core Fields

### `war_goal`

Required in practice. This is the starting war goal granted by the play.

Before changing it:
- inspect the referenced type in `common/war_goal_types/*.txt`
- check whether callers also add war goals manually with `add_war_goal`
- make sure the play fantasy and the war goal payload still match

In this repo, `dp_annex_war_historical` points at `contain_threat` while the caller in `ztr_unprovoked_nuclear_strike.txt` also adds `humiliation`, so the play should be reviewed together with both layers.

### `selectable_in_lens`

Use this for player-facing availability in the diplomatic lens.

Common uses:
- hide script-only plays with `always = no`
- gate plays behind country type, law, ideology, or feature conditions
- keep niche or historical plays out of generic diplomacy UI

If a play is only created from events or diplomatic actions, it is often correct to keep this block disabled.

### `possible`

Use for whether the play can currently be launched when selected or scripted.

Keep it focused on live validity:
- target eligibility
- diplomatic relevance
- truce or war-state constraints
- subject and revolutionary restrictions

If the play is script-only, `possible = {}` may be enough, but only after checking the caller enforces the real rules.

### `additional_involvement_trigger`

Use this to control which outside countries may join or intervene.

The mod's nuclear example uses:
- non-decentralized checks
- `aggressive_diplomatic_plays_permitted = yes`
- subject/overlord relationship logic
- interest checks tied to the initiator's capital region

This block often has bigger gameplay consequences than the top-level play flags. Re-read it carefully whenever a play should stay local, escalate globally, or invite bloc politics.

### `on_weekly_pulse`, `on_war_begins`, `on_war_end`, `on_demand_accepted`, `on_demand_rejected`

Use these only when the play needs lifecycle behavior beyond the default engine handling.

Good uses:
- setting or cleaning variables
- firing scripted effects
- synchronizing follow-up events
- handling acceptance or rejection side effects that belong to the play itself

Keep large payloads in scripted effects when possible instead of burying them directly in the play definition.

## Scope Guidance

A diplomatic play definition is not the same scope environment as a diplomatic action or event. Do not copy scope assumptions blindly.

Before reusing logic:
- confirm which scopes the target file actually uses
- compare against a vanilla play with the same kind of trigger block
- check whether surrounding callers already save scopes or add war goals elsewhere

Most mistakes here come from copying triggers from `common/diplomatic_actions` into `common/diplomatic_plays` without re-validating scope names and evaluation context.

## Mod-Aware Guidance

Current local pattern:

`common/diplomatic_plays/ztr_diplomatic_plays_nuclear.txt`

Key things it demonstrates:
- `REPLACE_OR_CREATE:` is acceptable for a play override or hybrid create/override pattern
- a play can be effectively script-only
- intervention logic may be the main reason the play exists

The strongest local caller is:

`common/diplomatic_actions/ztr_unprovoked_nuclear_strike.txt`

That file:
- creates the play with `create_diplomatic_play`
- forces `war = yes`
- adds an extra war goal in the caller

This means the real behavior is split across folders. When editing a play in this mod, always inspect `common\` neighbors first instead of assuming everything important lives in `common/diplomatic_plays`.

## Template

```pdx
dp_my_play = {
	requires_interest_marker = yes
	initiator_can_add_war_goals = yes
	target_can_add_war_goals = yes

	war_goal = my_war_goal_type
	texture = "gfx/interface/icons/war_goals/my_icon.dds"

	selectable_in_lens = {
		always = yes
	}

	possible = {
		always = yes
	}

	additional_involvement_trigger = {
		always = yes
	}

	on_war_begins = { }
	on_war_end = { }
}
```

## Editing Checklist

- Confirm the play key is unique, or intentionally uses `REPLACE:` or `REPLACE_OR_CREATE:`.
- Confirm the base `war_goal = ...` exists and still fits the intended conflict.
- Check whether the caller also uses `add_war_goal`.
- Verify `selectable_in_lens` matches whether the play is player-facing or script-only.
- Re-read `additional_involvement_trigger` because it strongly affects escalation behavior.
- Search `common/ai_strategies*` for `is_diplomatic_play_type = <play_key>` or related reactions.
- Search `common\` and `events\` for every `create_diplomatic_play` caller that might need updates.
- Check localization and tooltips if the play's visibility, war goals, or escalation rules changed.
