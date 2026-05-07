---
name: Ship Modification Slots
description: Guide for Victoria 3 ship modification slot definitions. Use when creating, editing, or reviewing files in common/ship_modification_slots, especially when adding ship designer slots, marking utility slots, or checking how slots connect ship_types to ship_modifications.
---

# Ship Modification Slots

Use this skill for Victoria 3 files in `common/ship_modification_slots/*.txt`.

Pair it with:
- `Ship Modifications` when creating the options that fill a slot
- `Ship Types` when wiring slots into ship type `modifications` and `default_modifications`
- `Localization Formatting` when adding or changing player-facing slot text

## File Location

Write modded slot definitions in:
- `common/ship_modification_slots/*.txt`

Inspect the local mod first:
- `common/ship_modification_slots/*.txt`
- `common/ship_modifications/*.txt`
- `common/ship_types/*.txt`
- `localization/english/*.yml`

Then compare against vanilla references:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\ship_modification_slots\ship_modification_slots.md`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\ship_modification_slots\*.txt`

## What These Definitions Do

Ship modification slots define the slot keys used by ship modifications and ship types. Vanilla separates core slots such as armor, guns, propulsion, and range from utility slots such as `ship_mod_slot_utility_1`.

Changing a slot key has wide impact: every modification using `type = <slot>` and every ship type listing that slot must stay in sync.

## Workflow

1. Prefer an existing vanilla slot unless the new ship design needs a distinct slot.
2. If adding a slot, create the slot first, then add matching modifications with `type = <slot>`.
3. Add the slot to every intended ship type under `modifications`.
4. Add a `default_modifications` entry when the default should differ from the global fallback.
5. Use `utility = yes` only for optional utility-style slots.
6. Verify slot localization, icon paths, and all references after editing.

## Common Structure

```pdx
ship_mod_slot_example = {
	icon = "gfx/interface/icons/military_icons/navy_icons/supplies_navy.dds"
	utility = yes
}
```

## Field Guidance

- `icon`: Optional UI texture. Vanilla core and utility slots all define one.
- `utility`: Optional boolean. Defaults to `no`; set to `yes` for utility slots.

## Review Checklist

- The slot lives in `common/ship_modification_slots/`.
- Every modification that uses the slot has `type = <slot_key>`.
- Every ship type that lists the slot includes at least one valid modification.
- Utility slots are intentionally marked with `utility = yes`.
- Slot icon and localization keys exist if the slot is visible to players.
