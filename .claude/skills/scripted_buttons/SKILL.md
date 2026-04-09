---
name: Scripted Buttons
description: Guide for Victoria 3 scripted button definitions. Use when creating, editing, or reviewing files in common/scripted_buttons, especially when adding journal-entry buttons, gating button availability, or wiring buttons to events, modifiers, progress bars, and localization.
---

# Scripted Buttons

Use this skill for Victoria 3 files in `common/scripted_buttons/*.txt`.

Do not use it for:
- journal entry logic that should live entirely in the JE pulse
- general UI layout work in `.gui` files with no scripted button definition changes
- standalone event options that are not exposed as reusable JE buttons

Pair it with:
- `Journal Entries` when attaching buttons with `scripted_button = ...`
- `Victoria 3 Event Scripting` when the button triggers events
- `Scripted Progress Bars` when the button changes bar state

## File Location

Write modded scripted buttons in:
- `common/scripted_buttons/*.txt`

Inspect the local mod first:
- `common/scripted_buttons/*.txt`
- `common/journal_entries/*.txt`
- `common/scripted_effects/*.txt`
- `common/scripted_progress_bars/*.txt`
- `common/scripted_triggers/*.txt`
- `events/*.txt`
- `localization/english/*.yml`

Then compare against vanilla references:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\scripted_buttons\scripted_buttons.md`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\scripted_buttons\*.txt`

## What These Definitions Do

A scripted button exposes a scripted action to UI, most commonly through a journal entry.

These files usually decide:
- the visible button name and description
- whether the player can currently see the button
- whether the button is currently clickable
- what effect happens on click
- whether and how the AI should ever use it

These files sit in a wider `common/` network:
- `common/journal_entries/` attaches buttons through `scripted_button = <key>`
- `common/scripted_effects/` often holds the effect body the button should call
- `common/scripted_progress_bars/` and JE variables are common button targets
- `events/` often provide the real outcome after the button fires
- `localization/english/*.yml` provides player-facing text for `name`, `desc`, and effect text

In this mod, local examples already show the usual pattern:
- `common/scripted_buttons/ztr_buttons.txt` defines JE-facing buttons
- `common/journal_entries/ztr_je_liberty_index.txt` and `common/journal_entries/ztr_je_russia_soviet.txt` attach them
- buttons trigger events, add modifiers, and enforce cooldowns through variables or modifiers

If you change or add a scripted button, search `common/`, `events/`, and `localization/` for:
- the button key itself
- `scripted_button =`
- the `name` and `desc` localization keys
- affected variables, modifiers, progress bars, and event ids

## Workflow

1. Inspect the JE or UI surface that will host the button before writing the button block.
2. Confirm the scope. Most JE buttons are country-scoped and assume `root` is the country.
3. Decide separately what should hide the button and what should merely disable it.
4. Keep repeatability explicit. If the button is one-shot or cooldown-gated, enforce that in `possible` or via a timed variable/modifier.
5. Move complex outcomes into scripted effects or events instead of bloating the button body.
6. Re-check localization, JE attachment, and every effect target after editing.

## Common Structure

```pdx
example_button = {
	name = "example_button"
	desc = "example_button_desc"

	visible = {
		is_ai = no
		has_journal_entry = je_example
	}

	possible = {
		NOT = { has_variable = example_button_cooldown }
	}

	effect = {
		set_variable = {
			name = example_button_cooldown
			months = 6
		}
		trigger_event = example_events.1
	}

	ai_chance = {
		value = 0
	}
}
```

## Field Guidance

- Definition key: Keep it stable because journal entries reference it directly.
- `name`, `desc`: Localization keys shown in the UI.
- `visible`: Hide the button entirely when the context is wrong.
- `possible`: Disable the button while still showing it. Use this for cooldowns, missing requirements, or soft lockouts.
- `effect`: Execute the button result. Keep it short if it just hands off to scripted effects or events.
- `ai_chance`: Optional AI use logic. Most player-facing JE buttons set this to zero or omit AI use entirely.
- `hidden_trigger` and `hidden_effect`: Useful when you want clean UI behavior without noisy tooltip text. Local mod buttons already use both patterns.

## Important Caution

Do not treat `scripted_buttons.md` as enough guidance by itself.

The note file shows the field list, but it does not emphasize the practical failure modes:
- `visible` and `possible` do different jobs, and mixing them up changes the UX
- a button with no cooldown or gating can be spammed every frame it is available
- button effects often outgrow the button block and belong in `common/scripted_effects/` or `events/`
- buttons attached to JEs must keep JE variables, modifiers, and progress bars in sync

Always verify the host JE and the post-click state changes together.

## Vanilla Patterns

Current vanilla scripted buttons show these stable patterns:
- most buttons are attached to journal entries rather than free-floating GUI panels
- many buttons trigger events or add/remove progress on scripted bars
- visibility often checks journal state, modifiers, tags, or laws
- repeatable buttons usually gate themselves with variables, timed modifiers, or state checks

Current mod patterns add important extra context:
- local buttons are mostly player-only and start with `is_ai = no`
- the mod uses variables and timed modifiers as lightweight cooldowns
- Russian and liberty-index content uses buttons as JE control surfaces rather than putting all logic in event options

## Editing Guidance

- Start from the nearest local or vanilla button for the same JE pattern.
- Keep button effects legible. If the button needs several branches, move the work into a scripted effect.
- Prefer `visible` for wrong-context hiding and `possible` for temporary lockouts.
- Match the local localization key style by keeping `name` and `desc` aligned with the button key.
- When the button changes a bar or JE variable, inspect the next pulse or follow-up event so the state cannot drift.

## Review Checklist

- The definition lives in `common/scripted_buttons/`.
- The button is attached where intended through `scripted_button = <key>`.
- `name` and `desc` localization keys exist.
- `visible` and `possible` reflect the intended UX.
- The effect cannot be spammed accidentally.
- Every referenced variable, modifier, event id, and progress bar exists.
- AI usage is explicitly correct instead of left ambiguous.
