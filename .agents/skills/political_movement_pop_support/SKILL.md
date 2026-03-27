---
description: Guide for Victoria 3 political movement pop support factor definitions. Use when creating, editing, or reviewing files in common/political_movement_pop_support, especially when political movements need new movement_support_* labels or visibility-gated support factors.
name: Political Movement Pop Support
---

# Political Movement Pop Support

Use this skill for Victoria 3 files in `common/political_movement_pop_support/*.txt`.

Do not use it for:
- full political movement definitions
- movement categories
- the numeric support weighting logic inside a movement

## File Location

Write modded pop support factor definitions in:
- `common/political_movement_pop_support/*.txt`

Inspect the local mod first:
- `common/political_movement_pop_support/*.txt`
- `common/political_movements/*.txt`

Then compare against vanilla references:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\political_movement_pop_support\political_movement_pop_support.md`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\political_movement_pop_support\00_political_movement_pop_support.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\political_movements\*.txt`

## What These Definitions Do

A `movement_support_*` definition is a support-factor label and visibility gate used by political movements.

It does not define the amount of support by itself. The actual attraction math is assembled in the movement definition through:
- `pop_support_trigger`
- `pop_support_factors`
- `pop_support_weight`

Use this file to define:
- the localization key shown in the movement UI
- whether a specific factor should be displayed for the current movement

## Scope Guidance

Vanilla documents this scope as:
- `root = political_movement`

Inside `visible`, check scope carefully when referencing:
- `owner` for the country that owns the movement
- movement identity such as `culture` or `religion`
- state or technology conditions through the owner

## Workflow

1. Inspect the movement file that will reference the factor in `pop_support_factors`.
2. Reuse an existing `movement_support_*` key if the UI reason is the same.
3. Add a `visible` block only when the factor should be hidden unless a condition is met.
4. Keep the factor name reusable across multiple movements when possible.
5. Verify the factor is actually listed by at least one movement after adding it.

## Common Structure

```pdx
movement_support_example = {
	name = POP_EXAMPLE

	visible = {
		owner ?= {
			always = yes
		}
	}
}
```

## Field Guidance

- `name`: Localization key shown in the movement interface.
- `visible`: Optional trigger for whether the factor should appear in the interface for the current movement.

If `visible` is omitted, the factor is generally always eligible to be shown when referenced.

## Vanilla Patterns

Vanilla `movement_support_*` entries usually fall into one of these groups:
- simple always-visible labels such as literacy, urbanization, radicals, or pop types
- conditional identity labels gated by `culture` or `religion`
- owner-country conditions such as slavery, colonization, or researched technology
- content-specific factors tied to journal entries, country variables, or regional situations

Vanilla also shows that these definitions are intentionally lightweight. Large support formulas belong in `common/political_movements/*.txt`, not here.

## Mod Integration

The consuming movement files use these keys in `pop_support_factors`, for example:

```pdx
pop_support_factors = {
	movement_support_high_literacy
	movement_support_capitalists
	movement_support_nationalism_tech
}
```

If you add a new factor here but never reference it from a movement, it does nothing.

## Editing Guidance

- Name keys after the support reason, not after a single movement.
- Prefer localization reuse when two factors would display the same message.
- Use `visible` to suppress noisy or misleading factors, not to implement support math.
- When a factor depends on technology, state traits, or country variables, gate visibility through `owner` rather than assuming direct scope access.

## Review Checklist

- The factor key is defined in `common/political_movement_pop_support/`.
- `name` points to a real localization key.
- `visible`, if present, uses political movement scope correctly.
- At least one movement lists the factor in `pop_support_factors`.
- Numeric weighting remains in the movement definition, not in this support-factor file.
