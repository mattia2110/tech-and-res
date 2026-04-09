---
description: Guide and template structure for Journal Entries in Victoria 3.
name: Journal Entries
---

# Journal Entries

Use this skill for Victoria 3 files in `common/journal_entries/*.txt`.

Do not use it for:
- `common/journal_entry_groups/*.txt` edits with no JE definition changes
- event-only work in `events/` when the journal entry definitions are untouched
- `common/scripted_progress_bars/*.txt` or `common/scripted_buttons/*.txt` edits when the JE attachment itself does not change

Pair it with:
- `Victoria 3 Event Scripting` when the JE starts, maintains, or resolves through events
- `Scripted Progress Bars` when the JE uses `scripted_progress_bar`
- `Scripted Buttons` when the JE exposes player actions
- `Objective Subgoals` when the JE is started by the player-objective system
- `Localization Formatting` when adding or updating JE localization

## File Location

Write local mod JEs in:
- `common/journal_entries/*.txt`

Inspect the local mod first:
- `common/journal_entries/*.txt`
- `common/journal_entry_groups/*.txt`
- `common/scripted_buttons/*.txt`
- `common/scripted_progress_bars/*.txt`
- `common/scripted_effects/*.txt`
- `common/scripted_triggers/*.txt`
- `common/on_actions/*.txt`
- `events/*.txt`
- `localization/english/*.yml`

Then compare against the main pattern sources:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\journal_entries\journal_entries.md`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\journal_entries\*.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\journal_entry_groups\00_journal_entries.txt`
- `C:\Program Files (x86)\Steam\steamapps\workshop\content\529340\3385002128\common\journal_entries\*.txt`

## What These Definitions Do

A journal entry is a player-facing state machine. In practice, a JE definition decides:
- who can see it in the Journal UI and while inactive
- when it activates and whether it can deactivate again
- how it tracks progress, if any
- which events, buttons, modifiers, and scripted effects fire on activation, completion, failure, invalidation, or timeout
- whether it belongs to one country or to a contextless involved-country setup

JEs rarely stand alone. They usually connect to:
- `common/journal_entry_groups/` for scope context
- `events/` for narrative and state transitions
- `common/scripted_buttons/` for JE actions
- `common/scripted_progress_bars/` for custom progress displays
- `common/scripted_effects/` and `common/scripted_triggers/` for reusable logic
- `common/objective_subgoals/` for tutorial or objective-driven JE starts
- `common/static_modifiers/` and localization for visible rewards, penalties, and tooltips

## Scope and Context

Group context matters more than the raw field list.

- `group = ...` is effectively required in practice. The group definition in `common/journal_entry_groups/00_journal_entries.txt` decides whether the JE context is `country` or `none`.
- For country-context groups, `ROOT` is the owning country.
- For contextless groups, the JE itself exists without a country root and you usually work through involved-country hooks such as `should_be_involved` and `*_all_involved`.
- `scope:journal_entry` is the JE instance. Use it for JE variables, `is_goal_complete`, involved-country iteration, and scripted bar reads.
- `scope:target` is whatever scope was passed through `add_journal_entry`. Target-scoped JEs are common in tutorial, objective, war, building, relation, and IG content.

Important visibility distinction:
- `is_shown_in_lobby` controls visibility in the Journal UI for many historical or always-relevant entries. Vanilla uses this heavily even when the JE is not purely inactive-gated.
- `is_shown_when_inactive` controls whether a not-yet-active JE is visible before activation. This is commonly bypassed when the JE is added directly through `add_journal_entry`.
- `possible` is the activation gate. If `can_deactivate = yes`, it can also return an active JE to the inactive state.

## Workflow

1. Identify the owning system before writing the JE: country flavor, tutorial, objective, diplomatic chain, global crisis, or utility state machine.
2. Check the target group so you know whether the JE is country-scoped or contextless.
3. Find the caller that activates the JE, especially if it uses `add_journal_entry` and passes a `scope:target`.
4. Decide whether progress is built-in with `current_value` and `goal_add_value`, custom with `scripted_progress_bar`, or event-driven with no bar at all.
5. Write `complete`, `fail`, `invalid`, and `timeout` as separate transitions with separate meanings.
6. Keep repeated gameplay logic in scripted effects or events instead of building giant inline JE blocks.
7. If the JE pulses weekly, monthly, or yearly, make the pulse idempotent with variables, flags, modifiers, or hidden triggers.
8. Re-check the full chain: group, caller, events, buttons, progress bars, modifiers, and localization.

## Common Archetypes

### Country-scoped tracked goal JE

Purpose:
- Long-running country content with normal activation, completion, failure, and optional timeout.

Typical fields:
- `group`
- `is_shown_in_lobby` or `is_shown_when_inactive`
- `possible`
- `complete`
- `fail`
- `timeout`
- `on_complete`, `on_fail`, `on_timeout`
- `event_outcome_*`

Representative sources:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\journal_entries\00_opium_wars.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\journal_entries\06_portugal_politics.txt`

Use this pattern when the JE is the main player-facing container for a country feature rather than a thin event trigger.

### Target-scoped tutorial or objective JE

Purpose:
- Track progress against a specific passed-in building, character, IG, law, or country.

Typical fields:
- `scope:target` checks in `complete`, `fail`, or `invalid`
- `current_value` and `goal_add_value`
- `is_progressing`
- `should_update_on_player_command` when player-issued commands should refresh it immediately
- `on_invalid` cleanup if the target disappears or changes owner

Representative sources:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\journal_entries\00_tutorial.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\journal_entries\00_player_objectives_economic_dominance.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\journal_entries\00_ig_suppression.txt`

If a JE depends on `scope:target`, always decide how it invalidates when that target no longer exists or is no longer valid.

### Event-launcher or dummy choice-gateway JE

Purpose:
- Surface an event, preview the outcome of that event, and then hand off the real chain elsewhere.

Typical fields:
- `immediate`
- `show_as_tooltip = { trigger_event = ... }`
- `event_outcome_activated_effect_desc`
- intentionally trivial `complete` or `fail`

Representative source:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\journal_entries\06_spanish_new_world.txt`

Use this for JE-backed gateways only when the Journal UI needs to frame the decision. Do not use it as a replacement for a normal event chain when a JE adds no real state.

### Pulse-maintained event-chain JE

Purpose:
- Run a long-lived state machine through weekly, monthly, or yearly pulses that trigger events, grow timers, or refresh saved state.

Typical fields:
- `immediate`
- `status_desc`
- `on_weekly_pulse`, `on_monthly_pulse`, `on_yearly_pulse`
- JE variables and saved scopes
- `complete`, `fail`, `invalid`

Representative sources:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\journal_entries\00_peoples_springtime_je.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\journal_entries\00_ig_suppression.txt`

Pulse JEs are where repeated-firing bugs happen. Guard rewards, event triggers, and timer changes carefully.

### Numeric progress JE

Purpose:
- Show built-in JE progress through the engine goal system instead of a separate scripted progress bar.

Typical fields:
- `current_value`
- `goal_add_value`
- `progressbar = yes`
- `display_progressbar_as_months = yes` for rare time-like displays
- `is_progressing`
- `progress_desc`

Representative sources:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\journal_entries\00_tutorial.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\journal_entries\00_opium_wars.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\journal_entries\01_french_monarchism.txt`

Vanilla uses both scalar and block forms here. `current_value = legitimacy` and `current_value = { value = gdp }` are both valid patterns. The same applies to `goal_add_value`.

### Scripted-progress-bar and button-driven JE

Purpose:
- Drive active JE gameplay through custom progress bars and clickable JE actions.

Typical fields:
- repeated `scripted_button = ...`
- repeated `scripted_progress_bar = ...`
- `status_desc`
- pulse hooks or events that update the bars

Representative sources:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\journal_entries\06_the_carlist_wars.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\journal_entries\00_peoples_springtime_je.txt`
- `C:\Program Files (x86)\Steam\steamapps\workshop\content\529340\3385002128\common\journal_entries\com_progress_in_style.txt`

Repeated `scripted_button` and `scripted_progress_bar` lines are normal. Treat them as lists even though the syntax is repeated fields.

### Contextless or global multi-country JE

Purpose:
- Run one JE across many countries, with individual involved-country effects and visibility.

Typical fields:
- group with `context = none`
- `should_be_involved`
- `should_show_when_not_involved`
- `immediate_all_involved`
- `on_complete_all_involved`
- `on_fail_all_involved`
- `on_timeout_all_involved`
- `on_become_involved_after_activation`
- `on_no_longer_involved`

Representative sources:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\journal_entries\00_peoples_springtime_je.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\journal_entries\06_the_carlist_wars.txt`
- `C:\Program Files (x86)\Steam\steamapps\workshop\content\529340\3385002128\common\journal_entries\com_rise_of_communism.txt`

Contextless JEs need much stricter scope discipline. When a field says it runs per involved country, write it as such and do not assume country-root behavior from a normal JE.

### Deactivating or invalidating master and child JE chains

Purpose:
- Maintain a master JE that can pause, vanish silently, or control a set of child JEs.

Typical fields:
- `can_deactivate`
- `invalid`
- `on_invalid`
- child JE `fail` conditions tied to the master JE's continued existence
- variables or modifiers that prevent duplicate rewards

Representative source:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\journal_entries\06_economic_regeneration.txt`

Use invalidation for silent cleanup. Use failure when the player should be told they failed.

### Workshop-only advanced patterns

Purpose:
- Extend JE behavior into custom systems beyond normal vanilla JE flow.

Representative sources:
- `C:\Program Files (x86)\Steam\steamapps\workshop\content\529340\3385002128\common\journal_entries\com_rise_of_communism.txt`
- `C:\Program Files (x86)\Steam\steamapps\workshop\content\529340\3385002128\common\journal_entries\com_progress_in_style.txt`

These examples show:
- custom situation setup from JE `immediate`
- custom factions and button groups
- JE-scoped progress bar styling and drift control

Treat them as advanced references, not baseline JE structure. They rely on supporting scripted systems from that workshop mod.

## Field Guidance

### Visibility and activation

- `group`: Determines the JE context through the referenced journal entry group.
- `is_shown_in_lobby`: Common in historical and flavor JEs. Use when the player should see the JE in the Journal UI even outside the basic inactive-gated pattern.
- `is_shown_when_inactive`: Controls visibility before activation for non-added JEs.
- `possible`: Activation gate for inactive JEs.
- `can_deactivate`: Rare. Use only when the JE should genuinely return to inactive if `possible` stops being true.
- `invalid`: Silent removal when the JE is no longer relevant.
- `on_invalid`: Cleanup for invalidation.

### State transitions and pulses

- `immediate`: Activation payload. Save scopes, set variables, add modifiers, or trigger entry events here.
- `immediate_all_involved`: Contextless equivalent that runs once per involved country.
- `complete` and `on_complete`: Success condition and success payload.
- `fail` and `on_fail`: Failure condition and failure payload.
- `timeout` and `on_timeout`: Forced transition after a set duration.
- `on_weekly_pulse`, `on_monthly_pulse`, `on_yearly_pulse`: Maintenance hooks. Use them carefully and make them repeat-safe.

### Progress and UI

- `current_value` and `goal_add_value`: Support both scalar and block forms. Use the simplest form that matches the real calculation.
- `progressbar = yes`: Enables the built-in goal bar.
- `display_progressbar_as_months = yes`: Rare. Use only when the bar should read as elapsed or remaining months.
- `scripted_progress_bar`: Attaches one or more custom bars from `common/scripted_progress_bars/`.
- `is_progressing`: Lets the UI know whether the JE is actively making progress.
- `status_desc`: Dynamic JE status text.
- `progress_desc`: Rare dynamic text above the progress bar.
- `scripted_button`: Attaches one or more JE actions. The buttons themselves live elsewhere.
- `should_update_on_player_command`: Rare and mostly tutorial-facing. Use when the JE must refresh from direct player-issued commands rather than waiting on the usual pulse cadence.

### Outcome previews

- `event_outcome_activated_desc`
- `event_outcome_activated_effect_desc`
- `event_outcome_completed_desc`
- `event_outcome_completed_effect_desc`
- `event_outcome_failed_desc`
- `event_outcome_failed_effect_desc`
- `event_outcome_timeout_desc`
- `event_outcome_timeout_effect_desc`

These are preview helpers for the UI. The `*_effect_desc` blocks generate tooltips from effects but do not execute those effects by themselves.

### Headers, inheritance, and tracking

- `custom_completion_header`, `custom_failure_header`, `custom_on_completion_header`, `custom_on_failure_header`: Flavor headers for the JE UI.
- `modifiers_while_active`: Applies modifiers while the JE is active.
- `weight`: Goal-tracker priority.
- `transferable`: Whether the JE follows the player on country switch or subject release.
- `can_revolution_inherit`: Whether a victorious revolution can inherit the JE.
- `should_be_pinned_by_default`: Default outliner behavior.
- `how_tutorial` and `why_tutorial`: Tutorial-only guidance fields used heavily in `00_tutorial.txt`.

## Important Caution

Do not treat `journal_entries.md` as enough guidance by itself.

The note file lists syntax, but the real behavior of a JE depends on:
- group context
- how the JE is added or activated
- supporting events
- buttons and progress bars
- saved scopes and JE variables
- localization and modifier payloads

Always inspect the surrounding chain, not just the JE block.

## Compact Template

```victoria3
my_journal_entry = {
	group = je_group_internal_affairs
	icon = "gfx/interface/icons/event_icons/event_portrait.dds"

	# For directly shown historical JEs; optional
	is_shown_in_lobby = {
		c:TAG ?= this
	}

	# Used for naturally discovered JEs; ignored when added directly
	is_shown_when_inactive = {
		c:TAG ?= this
	}

	possible = {
		has_technology_researched = nationalism
	}

	immediate = {
		set_variable = my_journal_entry_started
		# scope:target exists only if add_journal_entry passed a target
	}

	scripted_button = my_journal_entry_button
	scripted_progress_bar = my_journal_entry_bar

	# Scalar and block forms are both valid in real content
	current_value = legitimacy
	goal_add_value = {
		value = 15
	}

	progressbar = yes
	is_progressing = {
		government_legitimacy >= 50
	}

	status_desc = {
		desc = my_journal_entry_status
	}

	progress_desc = {
		desc = my_journal_entry_progress
	}

	complete = {
		scope:journal_entry = { is_goal_complete = yes }
	}

	on_complete = {
		trigger_event = { id = my_events.1 popup = yes }
	}

	fail = {
		in_default = yes
	}

	on_fail = {
		add_modifier = {
			name = my_failure_modifier
			days = 365
		}
	}

	invalid = {
		NOT = { c:TAG ?= root }
	}

	on_invalid = {
		remove_variable = my_journal_entry_started
	}

	timeout = 730

	on_timeout = {
		trigger_event = { id = my_events.2 popup = yes }
	}

	event_outcome_completed_effect_desc = {
		header = my_events.1.a
		effect = {
			add_modifier = {
				name = my_reward_modifier
				days = 365
			}
		}
	}

	weight = 200
	transferable = no
	can_revolution_inherit = yes
	should_be_pinned_by_default = yes
}
```

## Review Checklist

- The JE lives in `common/journal_entries/` and uses a sensible key.
- The chosen group matches the intended `country` or `none` context.
- Visibility fields match the intended discovery model.
- `scope:target` is only used when the caller can actually provide it.
- `complete`, `fail`, `invalid`, and `timeout` are distinct and intentional.
- Pulse hooks cannot repeatedly grant the same reward by accident.
- Every referenced button, progress bar, event, modifier, scripted effect, scripted trigger, and localization key exists.
- Rare UI fields such as `is_shown_in_lobby`, `should_update_on_player_command`, `display_progressbar_as_months`, and `progress_desc` are used deliberately rather than copied blindly.
