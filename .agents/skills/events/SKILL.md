---
name: Victoria 3 Event Scripting
description: Guide for creating, editing, and reviewing Victoria 3 event files. Use when working in the events/ folder, adding or debugging event chains, wiring events to common/on_actions, common/journal_entries, decisions, or scripted effects, or checking scopes, triggers, options, localization, and event UI fields.
---

# Victoria 3 Event Scripting

Use this skill for event definitions stored in `events/`.

## Target Files

- Put event scripts in `events/`.
- Follow the repo's existing foldering when possible, for example:
  - `events/china/...`
  - `events/india/...`
  - `events/colonies/...`
  - top-level `events/ztr_*.txt` for broad mod systems
- Add matching localization in `localization/`, saved as UTF-8-BOM `.yml`.
- Check related source folders when an event is triggered or maintained elsewhere:
  - `common/on_actions/`
  - `common/journal_entries/`
  - `common/decisions/`
  - `common/scripted_effects/`
  - `common/scripted_triggers/`
  - `common/diplomatic_actions/` or other `common/` definitions that may call the event

## File and Namespace Rules

- Start each file with one `namespace = some_name`.
- Keep one namespace per file.
- Keep event ids under that namespace, for example `ztr_india.1`, `ztr_india.2`.
- Prefer a namespace that matches the file's subject, event chain, or folder.
- Do not reuse vanilla namespaces unless intentionally overriding vanilla content.
- Keep related chains together; split unrelated systems into separate files.

Vanilla `game/events/` and the mod's own `events/` folder both organize content by topic, region, or feature. Follow that pattern instead of building one giant events file.

## Core Structure

```victoria3
namespace = my_namespace

my_namespace.1 = {
	type = country_event
	placement = ROOT

	title = my_namespace.1.t
	desc = my_namespace.1.d

	trigger = {
		# can this event appear?
	}

	immediate = {
		# save scopes, set variables, prep state
	}

	option = {
		name = my_namespace.1.a
		default_option = yes

		# effects
	}
}
```

## Common Fields

### Required in practice

- `type = country_event` is the most common event type in both vanilla and this mod.
- `title`, `desc`, and option `name` should normally use localization keys.
- `option = { ... }` is required unless the event is intentionally hidden and handled through `immediate`.

### Frequently used

- `placement = ROOT` for country events with normal popup placement
- `trigger = { ... }`
- `immediate = { ... }`
- `duration = 1` or another short window for simple prompt events
- `hidden = yes` for utility events that should not surface to the player
- `event_image = { video = "..." }`
- `icon = "gfx/interface/icons/event_icons/....dds"`
- `on_created_soundeffect = "event:/SFX/..."`
- `dlc = ...` when the event is gated by the mod or DLC setup

### Event Image

For `event_image` videos, look up valid media aliases in `gfx/media_aliases/`. Check both this mod's `gfx/media_aliases/` folder and the base Victoria 3 game directory's `gfx/media_aliases/`.

### Situational

- `cancellation_trigger = { ... }` when a pending event should disappear if conditions change
- `is_popup = yes` for popup-style helper/test events
- `orphan = yes` for events not meant to be reached by normal discovery logic
- `flavor = some_loc_key` when extra flavor text is needed
- option-level `trigger = { ... }`
- option-level `show_as_unavailable = { always = yes }`
- `highlighted_option = yes`

## Event Types and Scope

Use the event type that matches the expected root scope.

- `country_event`: default choice; most vanilla and mod events use this
- `state_event`: use when the event should root on a state rather than a country

When in doubt, inspect nearby vanilla examples in `game/events/` first. Vanilla `test_events.txt` is especially useful for event syntax and saved-scope examples.

## Workflow

1. Decide where the event lives in `events/` based on feature, country, or chain.
2. Choose a namespace and reserve a clean id range for that chain.
3. Decide how the event is reached:
   - on action
   - journal entry
   - decision
   - scripted effect
   - another event via `trigger_event`
4. Write the narrowest valid `trigger`.
5. Use `immediate` to save scopes or prepare data before options render.
6. Keep option bodies focused on visible outcomes; move repeated logic into scripted effects.
7. Add localization for title, desc, flavor, and options.
8. Verify the caller and callee both use the same scopes and assumptions.

## Trigger Sources

Events are often not self-starting. Check the caller.

Common sources:
- `common/on_actions/` for pulses, startup hooks, law changes, wars, etc.
- `common/journal_entries/` for JE-driven chains
- `common/decisions/` for player-fired events
- `common/scripted_effects/` for reusable chain entry points
- other event files using `trigger_event`

If the event should fire repeatedly from a pulse, make the trigger idempotent. Hidden maintenance events in this mod often rely on flags, modifiers, variables, or state checks to avoid repeated unintended execution.

## Scope and Saved Scope Guidance

- Use `ROOT` according to event type.
- Save random or derived scopes in `immediate` before referencing them in options or localization.
- Prefer explicit saved scopes when an option needs to show or act on the same target the player sees.

Example:

```victoria3
immediate = {
	random_scope_state = {
		limit = { is_capital = yes }
		save_scope_as = target_state
	}
}
```

Then use `scope:target_state` in option effects, triggers, or localization.

## Organization Patterns

### Player-facing choice event

- Use visible title, desc, and one or more localized options.
- Use `default_option = yes` on a sensible fallback.
- Use option-level triggers for gated choices.

### Hidden maintenance event

- Use `hidden = yes`.
- Put state preparation and effect logic in `immediate`.
- Gate it carefully so pulse hooks do not spam or repeat the same work.

### Chain event

- Keep ids sequential inside the same namespace.
- Pass control with `trigger_event`.
- Save scopes early if later events need stable targets.

## Keep Events Lean

- Put large repeated effect blocks in `common/scripted_effects/`.
- Put repeated condition logic in `common/scripted_triggers/`.
- Let journal entries, modifiers, and variables carry long-term state instead of duplicating checks in every event.

This mod already uses that pattern heavily. Preserve it.

## Template

```victoria3
namespace = my_feature

my_feature.1 = {
	type = country_event
	placement = ROOT

	title = my_feature.1.t
	desc = my_feature.1.d
	duration = 1

	trigger = {
		# root-scope conditions
	}

	immediate = {
		# save scopes or prep variables
	}

	option = {
		name = my_feature.1.a
		default_option = yes

		# primary effect
	}

	option = {
		name = my_feature.1.b

		trigger = {
			# optional choice gating
		}

		show_as_unavailable = { always = yes }
	}
}
```

## Review Checklist

- The file sits in a sensible place under `events/`.
- The namespace is unique and matches the chain.
- Event ids are unique inside that namespace.
- The chosen event type matches the intended root scope.
- The event has a real caller, or is intentionally hidden/orphaned.
- `trigger` prevents accidental repeat firing.
- Saved scopes are created before they are referenced.
- Repeated logic is moved to scripted effects or scripted triggers.
- All title, desc, flavor, and option keys exist in localization.
- Player-facing events have a sane default option and UI fields.
