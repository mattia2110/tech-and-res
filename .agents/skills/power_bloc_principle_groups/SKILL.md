---
name: Power Bloc Principle Groups
description: Guide for Victoria 3 power bloc principle group definitions. Use when creating, editing, or reviewing files in common/power_bloc_principle_groups, especially when adding a new group, wiring principle level progression, or checking how identities unlock, block, or prioritize groups.
---

# Power Bloc Principle Groups

Use this skill for Victoria 3 files in `common/power_bloc_principle_groups/*.txt`.

Do not use it for:
- editing the actual bonuses of an individual principle without changing its grouping
- identity-only changes with no group linkage changes
- localization-only edits

Pair it with:
- `Localization Formatting` when adding or fixing visible principle-group text

## File Location

Write modded principle-group definitions in:
- `common/power_bloc_principle_groups/*.txt`

Inspect the local mod first:
- `common/power_bloc_principle_groups/*.txt`
- `common/power_bloc_principles/*.txt`
- `common/power_bloc_identities/*.txt`
- `common/diplomatic_actions/*.txt`
- `common/decisions/*.txt`
- `common/ai_strategies/*.txt`
- `common/ai_strategies_vanilla/*.txt`
- `common/scripted_triggers/*.txt`
- `localization/english/*.yml`

Then compare against vanilla references:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\power_bloc_principle_groups\power_bloc_principle_groups.md`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\power_bloc_principle_groups\00_power_bloc_principle_groups.txt`

## What These Definitions Do

A principle group defines the upgrade family for a set of principles. It usually decides:
- which principle keys make up level 1, level 2, and level 3 of the same thematic branch
- which identities treat the group as primary
- which identities unlock or block the group entirely
- which group key other scripts can test with `has_principle_group`

These files sit in a wider `common/` network:
- `common/power_bloc_principles/` contains the actual principle entries listed in `levels`
- `common/power_bloc_identities/` provides the identity keys used by `blocking_identity`, `unlocking_identity`, and `primary_for_identity`
- `common/diplomatic_actions/` and AI scripts may check for specific groups when scoring bloc behavior
- `common/decisions/` and effects may add principles directly, which should still align with the intended group progression

If you change or add a principle group, search `common/` for:
- `has_principle_group = principle_group:<key>`
- `blocking_identity =`
- `unlocking_identity =`
- `primary_for_identity =`
- every principle key listed in `levels`

## Workflow

1. Inspect the nearest current vanilla group and the full principle family it points to.
2. Search the local mod for `has_principle_group` so you know what scripts or AI assumptions depend on the group key.
3. Confirm every listed principle exists and belongs to the same intended progression family.
4. Decide whether the group should be generic, identity-primary, identity-unlocked, or identity-blocked.
5. Preserve level order carefully because the order inside `levels` defines progression tier.
6. Re-check all identity references and every principle key after editing.

## Common Structure

```pdx
principle_group_example = {
	primary_for_identity = identity_trade_league
	unlocking_identity = identity_trade_league
	blocking_identity = identity_sovereign_empire

	levels = {
		principle_example_1
		principle_example_2
		principle_example_3
	}
}
```

## Field Guidance

- Definition key: Referenced elsewhere as `principle_group:<key>`. Renaming it breaks group checks in AI and scripted logic.
- `primary_for_identity`: Marks the group as a natural or favored branch for one or more identities.
- `unlocking_identity`: Restricts the group so only listed identities unlock it.
- `blocking_identity`: Prevents listed identities from taking the group.
- `levels`: Ordered list of principle keys in progression order. Current vanilla uses the order to define level 1, 2, and 3.

## Important Caution

Do not treat `power_bloc_principle_groups.md` as enough guidance by itself. It is only a schema stub.

The real gameplay behavior depends on things the stub does not emphasize:
- level order is gameplay-critical, not cosmetic
- group keys are referenced elsewhere through `has_principle_group`
- identity gating needs to match real identity keys in `common/power_bloc_identities/`

Always verify against `00_power_bloc_principle_groups.txt` and the linked principle entries before editing.

## Vanilla Patterns

Current vanilla principle-group files show these stable patterns:
- most groups define a three-step progression
- principle keys in `levels` usually share a naming family such as `_1`, `_2`, `_3`
- some groups are general-purpose and omit identity gating entirely
- some groups are identity-primary without being identity-exclusive
- some groups are both `primary_for_identity` and `unlocking_identity`
- some groups block one identity while remaining available to others

## Editing Guidance

- Start from the nearest vanilla group with the same kind of identity relationship.
- Keep group keys stable and descriptive because other content often tests the group rather than individual principles.
- Treat `levels` as a progression contract. Reordering entries changes which principle is treated as tier 1, 2, or 3.
- If you add a new group, verify whether AI or scripted logic should care about the group as a whole.
- When overriding vanilla, review the entire principle family, not just the group block.

## Review Checklist

- The definition lives in `common/power_bloc_principle_groups/`.
- Every principle key listed in `levels` exists in `common/power_bloc_principles/`.
- Every referenced identity key exists in `common/power_bloc_identities/`.
- The order inside `levels` matches the intended tier progression.
- Any `has_principle_group` checks in AI or scripted logic still point at the right key.
- No level was moved or removed accidentally during an override.
