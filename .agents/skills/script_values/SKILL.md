---
name: Script Values
description: Guide for Victoria 3 script value definitions. Use when creating, editing, or reviewing files in common/script_values, moving reusable numeric logic out of events or journal entries, or tracing named values into AI, buttons, progress bars, localization, and neighboring common/ content.
---

# Script Values

Use this skill for Victoria 3 files in `common/script_values/*.txt`.

Do not use it for:
- one-off inline math that is only used once and is clearer in place
- plain variable storage with `set_variable` or `change_variable`
- modifier definitions that belong in `common/static_modifiers/` or `common/modifier_type_definitions/`

Pair it with:
- `Journal Entries` when a JE pulse computes or consumes the value
- `Scripted Progress Bars` when a named value feeds bar drift or thresholds
- `Victoria 3 Event Scripting` when events compare or apply the value

## File Location

Write modded script values in:
- `common/script_values/*.txt`

Inspect the local mod first:
- `common/script_values/*.txt`
- `common/ai_strategies*/**/*.txt`
- `common/journal_entries/*.txt`
- `common/scripted_buttons/*.txt`
- `common/scripted_effects/*.txt`
- `common/scripted_progress_bars/*.txt`
- `common/scripted_triggers/*.txt`
- `common/customizable_localization/*.txt`
- `events/*.txt`
- `localization/english/*.yml`

Then compare against vanilla references:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\script_values\script_values.md`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\script_values\*.txt`

## What These Definitions Do

A script value is a reusable numeric expression. It can be:
- a static constant
- a scoped formula
- a random range
- a formula that saves temporary intermediate values before returning a final number

These files sit in a wider `common/` network:
- `common/ai_strategies*/` often uses named script values for army, navy, economy, and expansion logic
- `common/journal_entries/` and `events/` compare or consume script values in pulses, triggers, and outcomes
- `common/scripted_buttons/` and `common/scripted_progress_bars/` may use script values for AI behavior or drift
- `common/customizable_localization/` can compare values indirectly through scripted content
- `common/scripted_effects/` often becomes the place that applies results calculated by script values

In this mod, local examples already show that pattern:
- `common/script_values/ztr_ai_script_values.txt` overrides AI values with `REPLACE:`
- `common/ai_strategies_vanilla/ztr_default_strategy.txt` consumes those values
- journal entries and scripted effects use variables and bar updates that can be fed from named values

If you change or add a script value, search `common/`, `events/`, and `localization/` for:
- the script value key itself
- `value = <script_value_key>`
- `add = <script_value_key>`
- `multiply = <script_value_key>`
- `divide = <script_value_key>`
- `ai_chance = {`

## Workflow

1. Inspect the consumer first. The value's scope, cadence, and performance cost all depend on where it runs.
2. Search the local mod for the key and for the nearest existing value family before inventing a new pattern.
3. Decide whether the logic should stay inline or become a named value. Name it only if reuse or clarity justifies it.
4. Keep the formula scoped to the consumer. If the caller is country-scoped, write and test it as country-scoped.
5. Re-check every referenced variable, temporary value, trigger, technology, modifier, or saved scope after editing.
6. If the value runs in a frequently refreshed context, simplify it aggressively.

## Common Structure

```pdx
example_country_score = {
	value = average_sol

	if = {
		limit = { has_modifier = example_modifier }
		add = 5
	}

	add = {
		value = population
		divide = 1000000
	}

	save_temporary_value_as = pre_cap_value

	max = 100
	min = 0
	round = yes
}

example_constant = 25
```

## Field Guidance

- Definition key: Keep it stable once AI, events, buttons, bars, or localization logic references it.
- Static values: Use for constants that should be named and reused.
- `value`: Reset the current calculation to a literal, scoped value, variable, or another script value.
- `add`, `subtract`, `multiply`, `divide`, `modulo`: Apply math in order. Treat `divide` and `modulo` as danger points.
- `if`, `else_if`, `else`: Gate math by context. Use these instead of duplicating almost-identical values.
- `min`, `max`: Clamp the current result or compare against another value block.
- `round`, `ceiling`, `floor`, `round_to`: Control the final numeric shape when whole numbers matter.
- `integer_range`, `fixed_range`: Use for randomized output when the consumer expects a range.
- `save_temporary_value_as`: Use when later parts of the formula need an earlier intermediate result.
- Scope chaining: Named values can be accessed from chained scopes such as `mother.example_age`.

## Important Caution

Do not treat `script_values.md` as enough guidance by itself.

The note file lists math operations, but it does not tell you the real risks:
- the same value may run inside AI evaluations, JE pulses, UI-driven tooltips, or event chains
- expensive loops and repeated scope changes can become performance costs if the value is called often
- a named value with the wrong assumed scope can silently return nonsense
- this mod already overrides vanilla AI values with `REPLACE:`, so key stability matters

Always verify the consumer, scope, and call frequency before editing.

## Vanilla Patterns

Current vanilla script values show these stable patterns:
- thematic files group values by subsystem such as diplomacy, AI, politics, occupation, and journal content
- many values are not tiny constants; they are multi-step formulas with temporary values and clamps
- AI-facing values frequently scale population, income, rank, technologies, or strategies in sequence
- some files mix plain constants and full formulas in the same file

Current mod patterns add important extra context:
- `common/script_values/ztr_ai_script_values.txt` uses large `REPLACE:` overrides instead of isolated helper values
- local AI values depend heavily on technologies, strategies, laws, and temporary values
- the mod often applies the computed result elsewhere instead of keeping all logic in the original consumer

## Editing Guidance

- Start from the nearest local or vanilla value family for the same subsystem.
- Keep formulas explainable. If you cannot summarize what each stage is doing, the value is too opaque.
- Prefer named values for reused logic and large AI formulas, not for every small arithmetic step.
- Match the local override style in the file you are editing instead of switching patch operators casually.
- Comment only when the formula would otherwise be hard to re-derive from the surrounding logic.

## Review Checklist

- The definition lives in `common/script_values/`.
- The consumer scope matches the formula's assumptions.
- Every referenced variable, saved scope, technology, modifier, and strategy key exists.
- No `divide` or `modulo` path can hit zero unintentionally.
- Reused logic is named once instead of duplicated in multiple places.
- The key does not collide with an unrelated vanilla or mod value.
- The edited formula is appropriate for its execution frequency.
