---
name: Opinion Modifiers
description: Guide for Victoria 3 opinion modifier definitions. Use when creating, editing, or reviewing files in common/opinion_modifiers, adding bilateral country opinion effects, tuning opinion and decay script values, or wiring opinion modifiers from diplomatic actions, events, scripted effects, or on_actions.
---

# Opinion Modifiers

Use this skill for definitions in `common/opinion_modifiers/`.

## File Location

Write modded definitions in:
- `common/opinion_modifiers/*.txt`

Use references from:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\opinion_modifiers\opinion_modifiers.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\opinion_modifiers\opinion_modifiers.md`

Also inspect the caller that applies the modifier, typically in:
- `common/diplomatic_actions/*.txt`
- `common/scripted_effects/*.txt`
- `common/on_actions/*.txt`
- `events/*.txt`

## What These Definitions Do

An opinion modifier defines:
- how much bilateral opinion it adds or subtracts
- whether that opinion decays over time
- optional script-value logic that varies the result

Vanilla examples show `scope:opinion_target` inside these script-value blocks, so write them as bilateral logic rather than single-country logic.

## Workflow

1. Inspect the feature that applies the opinion modifier before writing the modifier itself.
2. Decide whether the modifier should be temporary and decaying or effectively permanent.
3. Start with a constant `value = ...` and only add conditional logic if the gameplay actually needs it.
4. If the modifier is asymmetric, use separate keys for initiator and recipient rather than burying both behaviors in one block.
5. Search for every place the modifier key is applied or removed so durations and cleanup stay coherent.

## Structure

```pdx
opinion_modifier_example = {
	opinion = {
		value = 25
		if = {
			limit = {
				# condition using root and scope:opinion_target
			}
			multiply = 2
		}
	}

	decay = {
		value = 1
		if = {
			limit = {
				# optional faster or slower decay
			}
			multiply = 0.5
		}
	}
}
```

`opinion` and `decay` are script-value style blocks. They can be constant or conditional.

## Vanilla Patterns

Base-game examples show several useful cases:
- positive and negative decaying modifiers such as `opinion_friendly_nation` and `opinion_unfriendly_nation`
- non-decaying modifiers such as `opinion_no_decay_test`
- asymmetric keys such as `opinion_rivals_initiator` and `opinion_rivals_recipient`

Use separate keys when each side should receive a different magnitude.

## Editing Guidance

- Keep the key name descriptive and relationship-focused.
- Use `decay` only when the opinion should naturally fade; omit it for non-decaying effects.
- Keep math readable. Small constant values are easier to reason about than deeply nested conditionals.
- Prefer explicit separate keys over hidden asymmetry.
- If the applying event, action, or scripted effect also removes the modifier later, verify the keys match exactly.

## Review Checklist

- The modifier key is defined in `common/opinion_modifiers/`.
- The caller applies the intended key and removes it when appropriate.
- `opinion` magnitude matches the design and sign.
- `decay` is present only when the effect should fade over time.
- Any conditional logic uses bilateral scope correctly, especially `scope:opinion_target`.
