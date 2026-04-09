---
name: Scripted Guis
description: Guide for Victoria 3 scripted GUI definitions. Use when creating, editing, or reviewing files in common/scripted_guis, especially when adding scripted GUI actions or tooltip/list builders and tracing links between localization, GUI files, journal entries, and neighboring common/ content.
---

# Scripted Guis

Use this skill for Victoria 3 files in `common/scripted_guis/*.txt`.

Do not use it for:
- ordinary `.gui` layout work with no scripted GUI definitions
- journal entry or event logic that should stay entirely in script with no GUI or localization call site
- scripted buttons unless the work truly belongs to an SGUI definition

Pair it with:
- `Journal Entries` when a JE or JE localization calls the SGUI
- `Victoria 3 Event Scripting` when saved scopes or variables are prepared by events
- `Localization Formatting` when wiring `ExecuteTooltip(...)` text

## File Location

Write modded scripted GUIs in:
- `common/scripted_guis/*.txt`

Inspect the local mod first:
- `common/scripted_guis/*.txt`
- `common/journal_entries/*.txt`
- `common/scripted_effects/*.txt`
- `common/scripted_triggers/*.txt`
- `events/*.txt`
- `gui/*.gui`
- `localization/english/*.yml`

Then compare against vanilla references:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\scripted_guis\scripted_guis.md`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\scripted_guis\journal_entry_sguis.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\scripted_guis\debug_sguis.txt`

## What These Definitions Do

A scripted GUI definition exposes scripted logic to the UI layer. In Victoria 3, the most common real use is not a clickable action but a tooltip or localization helper that builds a dynamic list from script scope.

These files usually decide:
- which scope the SGUI is valid for
- whether it should be shown or usable
- what effect or tooltip-building script runs
- which extra scopes need to be saved and re-used
- whether AI can use it

These files sit in a wider `common/` network:
- `localization/english/*.yml` often calls SGUIs with `GetScriptedGui(...).ExecuteTooltip(...)`
- `gui/*.gui` may invoke SGUIs directly from widgets
- `common/journal_entries/`, `events/`, and `common/scripted_effects/` often prepare variable lists, saved scopes, or state that the SGUI reads
- `common/scripted_triggers/` may support `is_shown` and `is_valid`

This mod currently has no local `common/scripted_guis/` definitions, so vanilla references are the primary source of structure. Treat that absence as a signal to inspect the exact caller before adding the first local SGUI.

If you change or add a scripted GUI, search `common/`, `events/`, `gui/`, and `localization/` for:
- the SGUI key itself
- `GetScriptedGui(`
- `ExecuteTooltip(`
- any variable lists, saved scopes, or tooltip localization keys it consumes

## Workflow

1. Inspect the call site first. Determine whether the SGUI is used from localization, GUI code, or a confirmable action.
2. Confirm the root scope and any required saved scopes before writing the SGUI block.
3. If the SGUI is only building tooltip text, keep it side-effect free.
4. Prefer preparing expensive state elsewhere, then let the SGUI only render it.
5. Add explicit empty-state handling when the tooltip is list-driven.
6. Re-check the localization string or GUI widget that calls the SGUI after editing.

## Common Structure

```pdx
example_states_sgui = {
	scope = country

	effect = {
		if = {
			limit = { has_variable_list = example_states }
			every_in_list = {
				variable = example_states
				custom_tooltip = "EXAMPLE_STATE_LIST_ENTRY"
			}
		}
		else = {
			custom_tooltip = "EXAMPLE_STATE_LIST_EMPTY"
		}
	}
}
```

## Field Guidance

- Definition key: Keep it stable because localization and GUI references use the key directly.
- `scope`: Required root scope type for the SGUI.
- `is_shown`: Hide the GUI helper or action entirely when the context is wrong.
- `is_valid`: Keep it visible but unusable when requirements are not met.
- `effect`: Either execute the action or build tooltip output through `custom_tooltip`.
- `saved_scopes`: Declare extra scopes that triggers or effects need to reference later.
- `notification_key`, `confirm_title`, `confirm_text`: Relevant for interactive SGUIs that ask for confirmation.
- `ai_is_valid`, `ai_chance`, `ai_frequency`: Rare compared with tooltip-builder SGUIs, but available for true interactive content.

## Important Caution

Do not treat `scripted_guis.md` as enough guidance by itself.

The note file lists fields, but the practical behavior depends on the caller:
- an SGUI used through `ExecuteTooltip(...)` can be refreshed often while the UI is visible
- expensive loops or broad scope iteration can become a UI performance problem
- tooltip-builder SGUIs should not mutate state unless you are certain the caller expects that
- saved scopes and variable lists must exist before the UI asks for them

Always inspect the GUI or localization call site together with the SGUI definition.

## Vanilla Patterns

Current vanilla scripted GUIs show these stable patterns:
- many SGUIs are JE-facing tooltip builders rather than general-purpose buttons
- variable lists are a common input, iterated with `every_in_list`
- `custom_tooltip` is the normal way to emit one line per matching scope
- empty-state handling is optional but useful when the list can be blank

Current mod patterns add important extra context:
- there are no local SGUI definitions yet
- local JE and event content already create variables, lists, and scoped state that could become SGUI inputs later
- if you add the first local SGUI, you must document its call site clearly because there is no existing house pattern yet

## Editing Guidance

- Start from the nearest vanilla SGUI that matches the same caller type.
- Treat tooltip-builder SGUIs as rendering helpers, not hidden effect containers.
- Keep the scope model obvious. If the call site passes country scope, do not silently rely on state or character assumptions.
- Prefer a small SGUI plus prepared data over a giant SGUI that computes everything every refresh.
- Add localization for every tooltip entry and empty state you emit.

## Review Checklist

- The definition lives in `common/scripted_guis/`.
- The caller in `gui/` or localization references the correct key.
- The passed root scope matches `scope =`.
- Every saved scope, variable list, and tooltip localization key exists.
- Tooltip-builder SGUIs avoid unintended state mutation.
- Empty-state behavior is acceptable when no entries match.
