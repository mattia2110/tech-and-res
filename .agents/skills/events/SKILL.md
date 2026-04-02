---
name: Victoria 3 Event Scripting
description: Guide for creating, editing, and reviewing Victoria 3 event files. Use when working in the `events/` folder, adding or debugging event chains, wiring events from `common/on_actions`, journal entries, decisions, scripted effects, or other events, or checking scopes, placement, cooldowns, UI windows, conditional localization, option behavior, and follow-up firing.
---

# Victoria 3 Event Scripting

Use this skill for event definitions stored in `events/`.

## Check Vanilla First

- Inspect base-game examples in `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\events\`.
- Use `game/events/test_events.txt` for syntax edge cases and orphan/debug patterns.
- Use `game/events/tech_events/rubber_events.txt` for `state_event`, `cooldown`, and hidden helper events.
- Use `game/events/acceptance_events.txt` or `game/events/commander_events.txt` for `gui_window`, `left_icon`, `right_icon`, and `on_opened_soundeffect`.
- Use `game/events/alaska_events.txt` or `game/events/american_civil_war/acw_je_events.txt` for conditional `title`, `desc`, and `flavor`.
- Use `game/events/iberia_events/spanish_events.txt` for `show_as_unavailable` plus `highlighted_option`.
- Use `game/events/bulgaria_events.txt` for rare `after = { ... }` usage.

## Target Files

- Put event scripts in `events/`.
- Follow existing foldering by topic, region, or feature.
- Add matching localization in `localization/` as UTF-8-BOM `.yml`.
- Check callers in `common/on_actions/`, `common/journal_entries/`, `common/decisions/`, `common/scripted_effects/`, `common/scripted_triggers/`, and neighboring content that fires the event.

## Organize Files and Namespaces

- Start each file with exactly one `namespace = some_name`.
- Keep one namespace per file.
- Keep ids unique inside that namespace.
- Match the namespace to the file subject or event chain.
- Keep related chains together and split unrelated systems into separate files.
- Avoid vanilla namespaces unless intentionally overriding vanilla content.

## Choose the Right Root

- Prefer `type = country_event` for country-rooted events. Vanilla overwhelmingly uses this.
- Use `type = state_event` when `ROOT` must be a state. Vanilla uses this for resource, decree, epidemic, and production events.
- Avoid inventing new event types. Verify unusual patterns in vanilla before using them.
- Treat `ROOT` according to event type: country for `country_event`, state for `state_event`.
- Reach country context from a `state_event` through `owner = { ... }` or saved scopes.

## Place the Event Correctly

- Use `placement = ROOT` or `placement = root` for normal country or state placement.
- Use `placement = scope:some_saved_scope` when the popup should anchor to a state, capital, expedition location, or other saved scope.
- Use direct scoped references like `placement = c:TAG.capital` when vanilla already does so.
- Save any placement scope before the event is fired. Do not rely on `immediate` to create a scope that `placement` needs.

## Start from a Vanilla-Shaped Skeleton

```victoria3
namespace = my_feature

my_feature.1 = {
	type = country_event
	placement = ROOT

	title = my_feature.1.t
	desc = my_feature.1.d
	flavor = my_feature.1.f

	event_image = { video = "unspecific_signed_contract" }
	on_created_soundeffect = "event:/SFX/UI/Alerts/event_appear"
	icon = "gfx/interface/icons/event_icons/event_newspaper.dds"

	duration = 3
	cooldown = { days = normal_modifier_time }

	trigger = {
		# can this event appear?
	}

	immediate = {
		# save scopes and prepare state before options render
	}

	option = {
		name = my_feature.1.a
		default_option = yes

		# effects
	}
}
```

## Use Event-Level Fields Deliberately

### Core flow

- Write `trigger = { ... }` on discovery conditions, not on UI polish.
- Leave `trigger = {}` intentionally empty only when the caller fully controls firing and the empty block documents that.
- Use `immediate = { ... }` to save scopes, roll targets, cache variables, or prepare loc before options render.
- Use `cancellation_trigger = { ... }` when a timed or pending event should disappear if the world changes.
- Use `cooldown = { days|months|years = ... }` on pulse-driven events to stop spam. Vanilla uses this constantly.
- Use extra variables, flags, or modifiers when cooldown must live on a wider scope than `ROOT`.
- Use rare `after = { ... }` only when you need post-event follow-up work. Copy a vanilla example and test it instead of guessing.

### Visibility and routing

- Use `hidden = yes` for maintenance or utility events.
- Use `orphan = yes` only for debug, test, or direct-only helpers that are not meant to surface naturally.
- Use `is_popup = yes` only for specialized presentation flows that need popup behavior beyond a normal event.
- Use `dlc = ...` when the event must disappear without a specific DLC.
- Permit hidden or orphan utility events to omit visible text only when they can never surface to the player.

### UI and presentation

- Set `title`, `desc`, and normally `flavor`.
- Set `duration = 3` or another short window for visible prompt events. Hidden helper events often omit it.
- Set `event_image = { video = "..." }` with a valid media alias from `gfx/media_aliases/`.
- Set `icon = "gfx/interface/icons/event_icons/....dds"` for visible events.
- Set `on_created_soundeffect` on visible events. Vanilla almost always does.
- Add `gui_window`, `left_icon`, `right_icon`, and `on_opened_soundeffect` only when the event uses a character-focused window.
- Match the window to the icon layout. Vanilla commonly uses `event_window_1char_tabloid`, `event_window_2char`, and `event_window_1char_propaganda`.

## Use Conditional Localization When the Text Must Branch

- Write plain loc keys when one text path is enough.
- Use `first_valid` plus `triggered_desc` blocks when title, desc, or flavor change by country, scope, or chain state.
- Reuse vanilla syntax exactly. `title = { ... }` still uses `triggered_desc` entries internally.

```victoria3
title = {
	first_valid = {
		triggered_desc = {
			trigger = { has_variable = my_branch_var }
			desc = my_feature.1.t_alt
		}
		triggered_desc = {
			trigger = { always = yes }
			desc = my_feature.1.t
		}
	}
}
```

- Use repeated `name = { trigger = { ... } text = ... }` blocks when an option label changes by branch or tag.

```victoria3
option = {
	name = {
		trigger = { c:SPA ?= this }
		text = my_feature.1.a_spa
	}
	name = {
		trigger = { c:SPC ?= this }
		text = my_feature.1.a_spc
	}
	default_option = yes
}
```

## Build Options the Way Vanilla Does

- Use `default_option = yes` on the branch the UI should preselect.
- Use option-level `trigger = { ... }` to gate whether a choice can actually be picked.
- Use `show_as_unavailable = { always = yes }` with a `trigger` when the player should see a locked option instead of having it vanish.
- Use `highlighted_option = yes` sparingly to call attention to a branch.
- Use `ai_chance = { ... }` to tune AI picks. Do not treat `default_option` as AI logic.
- Use `custom_tooltip` to explain non-obvious or bundled effects.
- Use `show_as_tooltip = { ... }` to preview effects without executing them in the visible branch.
- Use `hidden_effect = { ... }` to keep tooltip noise down while still performing follow-up work.
- Use `trigger_event = { id = other_namespace.N days = 0 popup = yes }` or a delayed version to hand off to the next event in a chain.

## Save and Reuse Scopes Explicitly

- Save random or derived targets in `immediate` before using them in options, loc, or chained events.
- Prefer `save_scope_as = ...` over recomputing the same target in multiple places.
- Keep option effects and localization aligned to the same saved scopes so the player sees the same target they act on.
- Remember that visible events often depend on scopes prepared by the caller, especially for `placement = scope:...`.

Example:

```victoria3
immediate = {
	random_scope_state = {
		limit = { is_capital = yes }
		save_scope_as = target_state
	}
}
```

## Keep Callers and Callees Consistent

- Decide how the event fires before writing it: on action, journal entry, decision, scripted effect, or another event.
- Make the caller and callee agree on `ROOT`, saved scopes, variables, and follow-up ids.
- Gate pulse-fired or JE-pulse events with cooldowns, flags, or persistent variables so they stay idempotent.
- Move large repeated effect blocks into `common/scripted_effects/`.
- Move repeated boolean logic into `common/scripted_triggers/`.

## Copy Strong Vanilla Patterns

### Player-facing choice event

- Use visible loc, image, icon, sound, and a short `duration`.
- Save targets in `immediate`.
- Use `default_option`, optional AI weights, and disabled options when the choice set matters.

### Hidden maintenance event

- Use `hidden = yes`.
- Do work in `immediate`.
- Keep trigger and cooldown logic tight so pulses do not spam it.

### Character-focused event

- Use `gui_window` plus `left_icon` or `right_icon`.
- Use matching opened sound effects.
- Save character scopes before the window opens.

### State-focused event

- Use `state_event`.
- Anchor with `placement = ROOT` or a saved state scope.
- Reach country context through `owner`.

## Review Against This Checklist

- Put the file in a sensible place under `events/`.
- Use one namespace and unique ids.
- Match `type` to the intended `ROOT` scope.
- Make `placement` point to a scope that already exists when the event fires.
- Give every visible event proper loc, image or icon choices, and sensible sound or UI fields.
- Give every pulse or repeatable event an anti-spam plan: `cooldown`, flags, variables, or all three.
- Save scopes before using them in loc, options, or follow-up events.
- Use conditional loc blocks only when the text truly branches.
- Make option gating, unavailable display, highlighting, and AI chance intentional.
- Keep chain handoffs explicit with `trigger_event`.
- Move repeated logic into scripted effects or scripted triggers.
- Verify the caller, callee, and localization all agree on names, scopes, and behavior.
