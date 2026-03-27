---
name: Scripted Progress Bars
description: Guide for Victoria 3 scripted progress bar definitions. Use when creating, editing, or reviewing files in common/scripted_progress_bars, especially when adding journal-entry progress bars, wiring bar updates from effects or buttons, or tracing progress thresholds into events, localization, and neighboring common/ content.
---

# Scripted Progress Bars

Use this skill for Victoria 3 files in `common/scripted_progress_bars/*.txt`.

Do not use it for:
- JE variables that never surface as a visible progress bar
- generic GUI styling with no progress bar definition changes
- event logic that only reads a bar and does not define or wire one

Pair it with:
- `Journal Entries` when attaching bars to a JE
- `Scripted Buttons` when buttons alter progress
- `Victoria 3 Event Scripting` when events read or change bar state

## File Location

Write modded scripted progress bars in:
- `common/scripted_progress_bars/*.txt`

Inspect the local mod first:
- `common/scripted_progress_bars/*.txt`
- `common/journal_entries/*.txt`
- `common/scripted_buttons/*.txt`
- `common/scripted_effects/*.txt`
- `common/script_values/*.txt`
- `common/customizable_localization/*.txt`
- `events/*.txt`
- `localization/english/*.yml`

Then compare against vanilla references:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\scripted_progress_bars\scripted_progress_bars.md`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\scripted_progress_bars\*.txt`

## What These Definitions Do

A scripted progress bar defines the visible metadata and optional autonomous drift behavior for a JE-facing progress meter.

These files usually decide:
- the bar's name and description text
- whether it is one-sided or two-sided
- its numeric bounds and start value
- whether negative pressure is treated as progress through inversion
- optional weekly, monthly, or yearly progress updates with tooltip descriptions

These files sit in a wider `common/` network:
- `common/journal_entries/` attaches bars with `scripted_progress_bar = <key>`
- `common/scripted_effects/`, `events/`, and `common/scripted_buttons/` update them through `set_bar_progress` or `add_progress`
- `common/customizable_localization/` and event triggers may read them through `scripted_bar_progress(...)`
- `common/script_values/` can supply thresholds or drift values
- `localization/english/*.yml` provides visible names and descriptions

In this mod, local examples already show the typical JE wiring:
- `common/scripted_progress_bars/ztr_progress_bars.txt` defines the bars
- `common/journal_entries/ztr_je_liberty_index.txt` and `common/journal_entries/ztr_je_russia_soviet.txt` attach them
- local JEs and scripted effects update bars explicitly with variables and pulse logic

If you change or add a scripted progress bar, search `common/`, `events/`, and `localization/` for:
- the bar key itself
- `scripted_progress_bar =`
- `set_bar_progress =`
- `add_progress =`
- `scripted_bar_progress(`
- the matching localization keys

## Workflow

1. Inspect the host JE first so you know who owns the bar and when it updates.
2. Decide whether the bar should be directly set by scripted effects or drift through weekly, monthly, or yearly progress blocks.
3. Keep variable ranges and bar ranges aligned. A `0-100` variable should not feed a `0-10` bar by accident.
4. Choose the visual style that matches the gameplay meaning: neutral, good, bad, or two-sided.
5. Re-check every button, effect, event, and tooltip that changes or reads the bar after editing.
6. Test empty or edge values mentally: min, max, inversion, and two-sided descriptions.

## Common Structure

```pdx
example_progress_bar = {
	name = "example_progress_bar"
	desc = "example_progress_bar_desc"
	second_desc = "example_progress_bar_right_desc"

	double_sided_gold = yes
	start_value = 50
	min_value = 0
	max_value = 100

	monthly_progress = {
		add = {
			desc = "example_progress_bar_monthly_tt"
			value = 2
		}
	}
}
```

## Field Guidance

- Definition key: Keep it stable because JEs, effects, buttons, and events reference it directly.
- `name`, `desc`, `second_desc`: Localization keys for the visible bar labels.
- `start_value`, `min_value`, `max_value`: Core numeric bounds. Keep them aligned with the variables or thresholds that drive the bar.
- `is_inverted`: Useful when lower real values should visually count as success.
- Visual style flags such as `default`, `default_bad`, `default_green`, `double_sided_gold`, `double_sided_bad`: Pick one style that matches the intended semantics.
- `weekly_progress`, `monthly_progress`, `yearly_progress`: Optional autonomous drift blocks. These execute before the corresponding JE pulses.
- `add = { desc = ... value = ... }`: Add one explained drift contribution inside the timed progress block.

## Important Caution

Do not treat `scripted_progress_bars.md` as enough guidance by itself.

The note file lists fields, but practical behavior depends on the surrounding content:
- bars can be updated by timed progress blocks, JE pulses, buttons, scripted effects, or events
- timed progress blocks execute before JE weekly, monthly, and yearly pulses
- two-sided bars need both localization and gameplay semantics to stay coherent
- progress bars are often read by event triggers and custom loc, so a range change can break downstream logic

Always inspect the host JE and all bar readers and writers together.

## Vanilla Patterns

Current vanilla scripted progress bars show these stable patterns:
- many bars are JE-facing and event-driven rather than purely autonomous
- two-sided bars commonly use `second_desc`
- timed drift blocks usually include per-source tooltip descriptions
- events and custom loc often compare bar values through `scripted_bar_progress(...)`

Current mod patterns add important extra context:
- local bars are currently simple definitions with explicit `set_bar_progress` updates in JE pulses or scripted effects
- the mod mostly uses `0-100` ranges
- several local bars currently avoid timed drift blocks and instead compute bar state externally

## Editing Guidance

- Start from the nearest local or vanilla bar that matches the same JE pattern.
- Decide who owns truth: the bar itself through timed drift, or the JE/effect through explicit updates.
- Keep descriptions synchronized with the real mechanics. If the bar is two-sided, both sides must remain meaningful.
- Match the existing local naming style by keeping bar keys and localization keys aligned.
- When changing ranges, inspect every event, button, and localization condition that reads the bar numerically.

## Review Checklist

- The definition lives in `common/scripted_progress_bars/`.
- The bar is attached to the intended JE.
- All localization keys exist.
- `min_value`, `max_value`, and `start_value` match the intended variable or trigger scale.
- Timed progress blocks, if present, use the intended cadence and tooltip text.
- Every `set_bar_progress`, `add_progress`, and `scripted_bar_progress(...)` reference still matches the bar's range and meaning.
