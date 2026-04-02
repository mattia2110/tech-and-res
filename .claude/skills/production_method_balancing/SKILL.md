---
name: Production Method Balancing
description: Guide for balancing Victoria 3 production methods using Standard Productivity and Pessimistic Productivity relative to the unlocking technology era and the current mod's goods prices. Use when evaluating or tuning PM inputs, outputs, and workforce in common/production_methods with references to common/goods and common/technology.
---

# Production Method Balancing

Use this skill when balancing PM economics in `common/production_methods/*.txt`.

Do not use it for:
- PM schema or slot-attachment work with no economic tuning
- building balance that only changes throughput or modifiers outside PM economics
- localization-only edits

Pair it with:
- `Production Methods` when editing the PM definition itself
- `Production Method Groups` when the balance issue is really a slot-structure issue

## File Location

Inspect these local mod files first:
- `common/production_methods/*.txt`
- `common/production_method_groups/*.txt`
- `common/buildings/*.txt`
- `common/goods/*.txt`
- `common/technology/eras/*.txt`
- `common/technology/technologies/*.txt`

Use vanilla as a comparison baseline when useful:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\production_methods\*.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\goods\*.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\technology\eras\*.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\technology\technologies\*.txt`

## What This Skill Does

This skill provides a fast balancing workflow for PMs using two core metrics:
- Standard Productivity (`SP`)
- Pessimistic Productivity (`PP`)

The goal is to compare a PM's economic strength against the era of its effective unlock while using the current mod's live goods costs, not a stale remembered price table.

## Core Concepts

- Goods value: `goods_cost * goods_quantity`
- Output value: sum of all output goods value
- Input value: sum of all input goods value
- Worker number: total employment used by the PM, usually from `building_employment_*_add`

### Standard Productivity

`SP = (Output Value - Input Value) / Worker Number`

This estimates the PM under normal price conditions.

### Pessimistic Productivity

`PP = ((Output Value / 4) - Input Value) / Worker Number`

This estimates the PM when output prices crash to 25% of base value while inputs stay at normal cost.

## Era Targets

Use the highest relevant unlocking tech era and compare against these rough targets:

- Target SP: `~ (Era**2) / 20`
- Target PP: `~ -(Era**2) / 30`

Quick target table:

| Era | Target SP | Target PP |
| :--- | :--- | :--- |
| 1 | 0.05 | -0.033 |
| 2 | 0.20 | -0.133 |
| 3 | 0.45 | -0.300 |
| 4 | 0.80 | -0.533 |
| 5 | 1.25 | -0.833 |
| 6 | 1.80 | -1.200 |
| 7 | 2.45 | -1.633 |
| 8 | 3.20 | -2.133 |
| 9 | 4.05 | -2.700 |
| 10 | 5.00 | -3.333 |
| 11 | 6.05 | -4.033 |

## Full PM vs Delta PM

Do not evaluate every PM the same way.

### Full PM

Use direct SP and PP when the PM defines a complete production profile for the slot, usually with:
- clear output goods
- clear employment counts
- a full economic role for that PM choice

Typical examples:
- base process PMs
- major production-step PMs
- whole-category output PMs

### Delta PM

Evaluate on the marginal change relative to the PM it replaces when the PM mostly adds or subtracts from an existing baseline instead of defining the whole slot by itself.

Typical examples:
- automation PMs
- auxiliary refinement PMs
- transport PMs
- principle-gated upgrade variants

For delta PMs:
1. Identify the PM it replaces in the same PMG.
2. Compute delta inputs, delta outputs, and delta employment between the two PMs.
3. Run SP and PP on that delta, not on the raw block alone.

Without this step, many automation PMs in this mod will look nonsensical because they mostly remove workers and add a few inputs.

## Workflow

1. Identify the PM and its PMG context first.
2. Decide whether it is a full PM or a delta PM.
3. Pull goods inputs and outputs from `building_modifiers.workforce_scaled`.
4. Pull worker counts from `building_modifiers.level_scaled`.
5. Read live goods prices from `common/goods/*.txt` using `cost =`.
6. Find the highest effective unlock from `unlocking_technologies` or the PM progression around it, then map that tech to `era = era_x` in `common/technology/technologies/*.txt`.
7. Compare the calculated SP and PP to the era targets.
8. Adjust output, input, or employment numbers until the PM sits near the intended era band.
9. Re-check any extra side benefits such as infrastructure, authority, pollution, or power-bloc gating that may justify a controlled deviation.

## Important Caution

Do not treat the old hardcoded price table as authoritative.

This mod overrides and adds goods in `common/goods/`, so the correct source of truth is the live `cost =` values in the mod files.

Also:
- PM comments with rough profit notes are useful hints, not authoritative balance math
- the highest effective tech gate is sometimes the prerequisite PM chain, not just the visible `unlocking_technologies` block
- SP and PP do not capture every side effect; pollution, infrastructure, legitimacy, authority, or special unlock effects can justify deviations

## Practical Guidance

- Compare against sibling PMs in the same PMG before comparing against distant buildings.
- For automation PMs, pay close attention to whether labor removal is too generous for the extra input burden.
- For luxury or late-era PMs, a strong SP is acceptable, but PP should still not collapse so badly that the PM becomes a trap.
- If a PM adds powerful non-economic benefits, bias its raw SP and PP slightly downward.
- If a PM is locked behind a late-era tech but performs like an early-era option, fix the PM instead of trusting the tech gate to carry it.

## Review Checklist

- The PM was classified correctly as full or delta.
- Inputs, outputs, and employment were taken from the current mod files, not memory.
- Goods costs came from `common/goods/*.txt`.
- Tech era came from the actual unlocking technology in `common/technology/technologies/*.txt`.
- SP and PP are in the right neighborhood for the effective era.
- Any major non-economic side effects were considered explicitly.
- Sibling PMs in the same PMG still form a coherent progression after the change.
