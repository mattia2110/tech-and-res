---
name: Scripted Modifiers
description: Guide for Victoria 3 scripted modifier definitions. Use when creating, editing, or reviewing files in common/scripted_modifiers, especially when defining reusable conditional modifier blocks for script consumers that expect factor-style logic, or when verifying whether a change belongs here instead of in static modifiers, modifier types, or scripted effects.
---

# Scripted Modifiers

Use this skill for Victoria 3 files in `common/scripted_modifiers/*.txt`.

Do not use it for:
- `common/static_modifiers/*.txt` content that grants actual country, state, building, or character modifiers
- `common/modifier_type_definitions/*.txt` display metadata and modifier key declarations
- scripted effects that directly apply or remove modifiers

Pair it with:
- `Modifier Types` when a new modifier key is also needed elsewhere
- `Victoria 3 Event Scripting` when an event or effect consumes the scripted modifier logic
- the skill for the actual consuming system, because the consumer decides what fields are legal

## File Location

Write modded scripted modifiers in:
- `common/scripted_modifiers/*.txt`

Inspect the local mod first:
- `common/scripted_modifiers/*.txt`
- the exact consumer file that wants to use the scripted modifier
- any neighboring `common/` folders tied to that consumer
- `localization/english/*.yml` only if the consuming system exposes text

Then compare against vanilla references:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\scripted_modifiers\scripted_modifiers.md`

## What These Definitions Do

A scripted modifier is a reusable conditional modifier block. Unlike `static_modifiers`, it does not represent a persistent gameplay modifier by itself. Instead, it stores conditional numeric logic that another scripted system can evaluate.

Victoria 3 ships only a minimal vanilla stub for this folder, and this mod currently has no local definitions. That means the consumer is the real specification.

Treat `common/scripted_modifiers/` as a rare helper layer for cases where:
- the target field explicitly expects a scripted modifier block
- the same conditional weighting logic would otherwise be duplicated
- a named block is clearer than repeating the same `if` branches in several places

## Workflow

1. Inspect the consuming field first and confirm it actually accepts a scripted modifier key.
2. Search vanilla and the local mod for the exact consumer pattern before creating a new scripted modifier.
3. Determine which numeric fields are legal in that consumer. Do not assume every consumer accepts `factor`.
4. Keep the scripted modifier narrow and reusable. If it only makes sense in one place and one file, inline it instead.
5. Re-check every trigger, scope assumption, and referenced key after editing.

## Common Structure

```pdx
example_scripted_modifier = {
	if = {
		limit = { is_accepted_culture = no }
		factor = 0.20
	}
	if = {
		limit = { is_state_religion = no }
		factor = 0.20
	}
}
```

## Field Guidance

- Definition key: Keep it stable once a consumer references it.
- `if` blocks: Gate the modifier logic conditionally. Match the consumer scope exactly.
- `limit`: Define when that branch applies.
- Numeric payload such as `factor`: The legal payload is consumer-dependent. Confirm it from the field you are wiring into.

## Important Caution

Do not overgeneralize from the vanilla stub.

The real risks here are structural:
- Victoria 3 provides almost no first-party examples beyond the schema note
- this mod currently has no local scripted modifier definitions to copy
- different consumers may expect different payload fields or scope assumptions
- it is easy to confuse this folder with `static_modifiers`, `modifier_type_definitions`, or ordinary inline `modifier = { ... }` blocks

If the target field does not clearly accept a scripted modifier key, stop and verify the consumer before adding anything.

## Vanilla Patterns

What vanilla tells you reliably:
- the folder exists
- conditional branches are legal
- factor-style payloads are at least one supported pattern

What vanilla does not give you:
- a broad library of working examples
- a guarantee that all consumers behave the same way
- a substitute for reading the consuming definition

Current mod patterns add important extra context:
- there are no local `common/scripted_modifiers/` files yet
- most current mod logic is expressed through inline modifier blocks, scripted effects, variables, and static modifiers instead

## Editing Guidance

- Default to inlining the logic unless reuse or clarity clearly justifies a scripted modifier.
- Read the surrounding system first. The consumer defines scope, supported fields, and performance characteristics.
- Keep the block small and purpose-built.
- Name it after the meaning of the weighting logic, not the file you happened to add it in.
- If the consumer is unclear, document that uncertainty in your review or ask before making the change.

## Review Checklist

- The target field truly accepts a scripted modifier reference.
- The definition lives in `common/scripted_modifiers/`.
- The scripted modifier key is referenced by the intended consumer.
- Every trigger and scope assumption matches the consumer context.
- The numeric payload fields are valid for that consumer.
- The logic does not really belong in `static_modifiers/`, `modifier_type_definitions/`, or an inline block instead.
