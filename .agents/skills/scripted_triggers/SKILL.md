---
name: Scripted Triggers
description: Guide for Victoria 3 scripted trigger definitions. Use when creating, editing, or reviewing files in common/scripted_triggers, especially when extracting reusable boolean logic from events, journal entries, scripted buttons, AI logic, graphics triggers, or other common/ content.
---

# Scripted Triggers

Use this skill for Victoria 3 files in `common/scripted_triggers/*.txt`.

Do not use it for:
- one-off inline trigger blocks that are clearer in place
- `common/scripted_rules/*.txt` engine hook rules
- `common/script_values/*.txt` numeric formulas
- `common/scripted_effects/*.txt` effect bodies that change state

Pair it with:
- `Victoria 3 Event Scripting` when events consume the trigger
- `Journal Entries` when JE availability, progress, or buttons check the trigger
- `Scripted Buttons` when button `visible`, `possible`, or AI logic consumes the trigger
- the skill for the actual consuming system, because the consumer defines the valid scope

## File Location

Write modded scripted triggers in:
- `common/scripted_triggers/*.txt`

Inspect the local mod first:
- `common/scripted_triggers/*.txt`
- the exact consuming file in `common/`, `events/`, or `decisions/`
- `common/scripted_buttons/*.txt`
- `common/scripted_effects/*.txt`
- `common/scripted_progress_bars/*.txt`
- `common/script_values/*.txt`
- `common/journal_entries/*.txt`
- `events/*.txt`
- `localization/english/*.yml` only if the trigger uses `custom_tooltip`

Then compare against vanilla references:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\scripted_triggers\*.txt`

## What These Definitions Do

A scripted trigger is a reusable boolean block. It lets other scripted systems call a named condition instead of repeating the same trigger logic inline.

These files sit in a wide `common/` network:
- `events/`, `common/journal_entries/`, and `common/decisions/` use them for availability and branching
- `common/scripted_buttons/` uses them for `visible`, `possible`, and AI behavior
- `common/script_values/` and AI content may branch on them indirectly
- graphics and presentation systems also consume them, not just gameplay logic

In this mod, local examples already show several important patterns:
- `common/scripted_triggers/ztr_event_scripted_triggers.txt` defines general gameplay and tooltip-facing triggers
- `common/scripted_triggers/ztr_vanilla_scripted_triggers.txt` uses `REPLACE:` to override vanilla classification triggers
- `common/scripted_triggers/ztr_gfx_units.txt` uses `REPLACE:` for graphics and unit presentation triggers
- `common/scripted_triggers/ztr_compatibility_scripted_triggers.txt` uses `REPLACE_OR_CREATE:` for a compatibility probe key

If you change or add a scripted trigger, search the local mod and vanilla for:
- the trigger key itself
- `<trigger_key> = yes`
- `<trigger_key> = no`
- `custom_tooltip = {`
- `hidden_trigger = {`

## Workflow

1. Inspect the consumer first and confirm the scope where the trigger will run.
2. Search the local mod and vanilla for the key before creating or overriding it.
3. Decide whether the logic should stay inline or become a named trigger. Extract it only when reuse, readability, or override-ability justifies it.
4. Choose the patch operator deliberately. Use `REPLACE:` only when you intend to fully override a vanilla key.
5. Keep player-facing tooltip behavior explicit. Use `custom_tooltip` only when the trigger will be surfaced to the player.
6. Re-check every scope chain, saved scope, variable, and referenced key after editing.

## Common Structure

```pdx
example_trigger = {
	has_variable = example_var
	var:example_var >= 10
}

example_trigger_with_tooltip = {
	custom_tooltip = {
		text = example_trigger_tt
		has_law_or_variant = law_type:law_census_suffrage
	}
}
```

## Field Guidance

- Definition key: Keep it stable once content references it.
- Trigger body: Write ordinary trigger logic exactly as the consumer scope expects.
- `custom_tooltip`: Expose a custom tooltip string while evaluating the trigger.
- `hidden_trigger`: Evaluate conditions without surfacing them in the tooltip.
- Scope chaining such as `root`, `prev`, `scope:name`, and temporary scopes: Confirm each link against the actual caller.
- Patch operators such as `REPLACE:` and `REPLACE_OR_CREATE:`: Match the surrounding file's override strategy instead of mixing styles casually.

## Important Caution

Do not treat scripted triggers as a generic dumping ground for every condition.

The main risks are structural:
- the same trigger key can mean different things in different scopes
- `custom_tooltip` changes player-facing UI, not just logic
- `REPLACE:` can silently wipe vanilla branches other systems rely on
- deeply nested helper triggers can make debugging harder than leaving the logic inline

Always verify the caller, scope, and override intent before editing.

## Vanilla Patterns

Current vanilla scripted triggers show these stable patterns:
- subsystem-specific files group triggers by diplomacy, buildings, AI, geography, graphics, and content packs
- some triggers are simple wrappers around engine-provided checks
- others are multi-step blocks with saved scopes, adjacency checks, or parameterized targets
- some keys are effectively engine-facing utility hooks reused across many systems

Current mod patterns add important extra context:
- the mod already overrides vanilla trigger keys with `REPLACE:`
- tooltip-bearing regime and ideology checks live here, not only invisible helper logic
- graphics-related triggers are maintained in the same folder as gameplay triggers

## Editing Guidance

- Start from the nearest local or vanilla trigger family for the same subsystem.
- Prefer one well-named reusable trigger over copying the same long condition into several consumers.
- Keep names about meaning, not file placement.
- Keep tooltip text aligned with the actual boolean test so the UI cannot lie.
- If a trigger is only used once and is easy to read inline, leave it inline.

## Review Checklist

- The definition lives in `common/scripted_triggers/`.
- The consumer scope matches every `root`, `prev`, saved scope, and target reference.
- The key is referenced where intended, or the override targets a real vanilla key.
- Any `custom_tooltip` localization key exists.
- `REPLACE:` or `REPLACE_OR_CREATE:` is used only when the override behavior is intentional.
- The logic does not belong in `scripted_rules`, `script_values`, or an inline trigger block instead.
