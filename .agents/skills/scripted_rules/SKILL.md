---
name: Scripted Rules
description: Guide for Victoria 3 scripted rule definitions. Use when creating, editing, or reviewing files in common/scripted_rules, especially when overriding engine hook rules such as treaty, war, or power bloc permission checks, or when a specific consumer explicitly expects a scripted rule key.
---

# Scripted Rules

Use this skill for Victoria 3 files in `common/scripted_rules/*.txt`.

Do not use it for:
- general reusable boolean helpers that belong in `common/scripted_triggers/*.txt`
- campaign setup options in `common/game_rules/*.txt`
- inline `rule = { trigger = { ... } ... }` blocks that belong to another definition file
- scripted effects or values

Pair it with:
- `Diplomatic Actions`, `Diplomatic Plays`, or `Treaty Articles` when the rule gates diplomatic behavior
- `Power Bloc Principles` when the rule controls principle-slot availability
- `Game Rules` only when the scripted rule checks `has_game_rule = ...`
- the skill for the actual consuming system, because scripted rules are usually consumer-specific hooks

## File Location

Write modded scripted rules in:
- `common/scripted_rules/*.txt`

Inspect the local mod first:
- `common/scripted_rules/*.txt`
- the exact consuming file or subsystem in `common/`, `events/`, or UI definitions
- nearby `common/` folders tied to that subsystem
- `common/scripted_triggers/*.txt` for reusable helper conditions the rule may call
- `localization/english/*.yml` only if the rule uses `custom_tooltip`

Then compare against vanilla references:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\scripted_rules\00_scripted_rules.txt`

## What These Definitions Do

A scripted rule is a named boolean rule hook. It looks similar to a scripted trigger, but it is not a general-purpose trigger library.

In Victoria 3, scripted rules are usually one of two things:
- hardcoded engine hooks with exact reserved keys, such as treaty, war, voting, or power bloc checks
- narrowly scoped rule keys that a specific consumer explicitly expects

That distinction matters because arbitrary new keys in `common/scripted_rules/` can be dead code if no engine hook or scripted consumer ever reads them.

Current vanilla patterns show that clearly:
- `can_sign_treaty_with` is an engine-evaluated country rule
- `violate_sovereignty_war_check_rule` delegates to a regular scripted trigger
- `unlock_power_bloc_principle_slot_*` controls hardcoded slot unlock checks

Current mod patterns add an important constraint:
- this mod currently has no local `common/scripted_rules/` files, so vanilla is the main reference

## Workflow

1. Identify the consumer before touching `common/scripted_rules/`. Do not start from the rule file.
2. Confirm whether the key is a hardcoded vanilla hook or an explicitly referenced consumer key.
3. Search vanilla and the local mod for the exact key and for the surrounding subsystem behavior.
4. If the rule logic is reusable, move helper conditions into `common/scripted_triggers/` and keep the scripted rule itself thin.
5. Preserve the expected scope exactly. Many rules are evaluated in unusual scopes such as war, power bloc, or paired-country contexts.
6. Re-check every referenced trigger, game rule, law, modifier, and saved scope after editing.

## Common Structure

```pdx
can_sign_treaty_with = {
	scope:other_country = {
		NOT = { is_country_type = decentralized }
	}
	root = {
		NOT = { is_diplomatic_play_enemy_of = scope:other_country }
	}
	has_diplomatic_relevance = scope:other_country
}

example_rule_wrapper = {
	example_scripted_trigger = yes
}
```

## Field Guidance

- Definition key: Treat it as an API surface. If vanilla or a consumer expects an exact key, renaming it breaks the hook.
- Trigger body: Write ordinary trigger logic, but only within the exact scope contract of that rule.
- Helper trigger calls: Prefer delegating reusable subconditions to `common/scripted_triggers/`.
- `custom_tooltip`: Use only when the rule is exposed to the player and the consumer supports visible tooltips.
- Scope contracts such as `root`, `scope:target_country`, or other named scopes: Copy them from the real caller, not from memory.

## Important Caution

Do not treat `common/scripted_rules/` as interchangeable with `common/scripted_triggers/`.

The main failure modes are:
- adding a new rule key that nothing ever calls
- overriding a hardcoded vanilla hook without understanding every system that depends on it
- assuming the scope is country-scoped when the engine is actually evaluating war, power bloc, or paired-country state
- putting broad reusable logic here instead of keeping the rule as a narrow entry point

If you cannot point to the exact consumer for a scripted rule key, stop and verify it before adding the file.

## Vanilla Patterns

Current vanilla scripted rules show these stable patterns:
- the folder is small and centralized compared with `common/scripted_triggers/`
- many keys are high-impact engine callbacks rather than ordinary reusable helpers
- several rules are thin wrappers around other scripted triggers
- comments often document scope directly because the caller is not obvious from search results alone

## Editing Guidance

- Start by reading the consumer or vanilla hook comment, then write the rule.
- Keep rules narrow. Put shared boolean logic in scripted triggers and call it from the rule.
- Preserve comments that explain scope contracts or engine behavior when overriding vanilla hooks.
- Avoid inventing new scripted rule keys unless you have a confirmed consumer that reads them.
- When in doubt between `scripted_rules` and `scripted_triggers`, default to `scripted_triggers`.

## Review Checklist

- The definition lives in `common/scripted_rules/`.
- The exact key is confirmed as a real engine hook or explicit consumer reference.
- The consumer scope matches every `root`, named scope, and target reference.
- Any helper trigger or game rule it calls exists.
- The rule is thin and consumer-specific instead of being a generic helper dump.
- The change does not belong in `common/scripted_triggers/` or `common/game_rules/` instead.
