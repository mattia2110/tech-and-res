---
name: Production Method Groups
description: Guide for Victoria 3 production method group definitions. Use when creating, editing, or reviewing files in common/production_method_groups, especially when adding a new PMG, overriding a vanilla PMG, or tracing how a PMG interacts with production methods, buildings, localization, AI selection, and neighboring common/ content.
---

# Production Method Groups

Use this skill for Victoria 3 files in `common/production_method_groups/*.txt`.

Do not use it for:
- production method balance changes by themselves with no PMG membership or PMG metadata changes
- building edits that only reorder or attach existing PMGs
- localization-only edits with no PMG definition changes

Pair it with:
- `Production Methods` when creating or revising the PMs listed inside the group
- `Localization Formatting` when adding or fixing PMG names

## File Location

Write modded PMG definitions in:
- `common/production_method_groups/*.txt`

Inspect the local mod first:
- `common/production_method_groups/*.txt`
- `common/production_methods/*.txt`
- `common/buildings/*.txt`
- `common/scripted_triggers/*.txt`
- `common/script_values/*.txt`
- `localization/english/*.yml`

Then compare against vanilla references:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\production_method_groups\production_method_groups.md`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\production_method_groups\*.txt`

## What These Definitions Do

A production method group is a switch slot inside a building. A building can have one active PM from each PMG at a time.

These files usually decide:
- which production methods belong to the same mutually exclusive slot
- which icon the building panel shows for mixed configurations
- whether unavailable PMs in the group should stay visible
- whether the AI should optimize for profitability or productivity when switching that group

These files sit in a wider `common/` network:
- `common/production_methods/` contains every PM key listed in `production_methods`
- `common/buildings/` attaches PMG keys to buildings through `production_method_groups = { ... }`
- `localization/english/*.yml` provides the visible group name
- scripted content may check PM or building state, so moving PMs between groups can affect more than UI

If you change or add a PMG, search `common/` and `localization/` for:
- the PMG key itself
- every PM key listed in `production_methods`
- `production_method_groups = {`
- the matching localization key

## Workflow

1. Inspect the nearest vanilla PMG and the nearest local mod PMG for the same building function.
2. Search the local mod for the PMG key in `common/buildings/` so you know where it is attached.
3. Confirm every PM listed in the group exists and is meant to compete in the same slot on the same building family.
4. Decide whether the group is a base slot, automation slot, refining slot, transport slot, military slot, or a custom category, then pick texture and AI behavior accordingly.
5. Keep group membership, building attachment, and localization aligned in the same change.
6. Re-check every PM key, PMG key, and building reference after editing.

## Common Structure

```pdx
pmg_example_building = {
	texture = "gfx/interface/icons/generic_icons/mixed_icon_base.dds"

	production_methods = {
		pm_example_1
		pm_example_2
		pm_example_3
	}

	ai_selection = most_profitable
	is_hidden_when_unavailable = no
}
```

## Field Guidance

- Definition key: Usually uses the `pmg_` prefix. Keep it stable because buildings and localization reference it directly.
- `production_methods`: Ordered list of mutually exclusive PM keys for this slot. Every listed PM must exist and make sense for the same building context.
- `ai_selection`: Controls AI switching behavior. Current documented values are `most_profitable` and `most_productive`. Omit it when default behavior is fine.
- `is_hidden_when_unavailable`: Controls whether currently unavailable PMs are still shown in the UI.
- `texture`: Mixed-configuration icon shown for the group. Match the group's gameplay role and local mod conventions.

## Texture Guidance

Common texture patterns in this mod:
- base or general process groups: `gfx/interface/icons/generic_icons/mixed_icon_base.dds`
- automation or transport groups: `gfx/interface/icons/generic_icons/mixed_icon_automation.dds`
- refining or chemical process groups: `gfx/interface/icons/generic_icons/mixed_icon_refining.dds`
- military groups: `gfx/interface/icons/generic_icons/mixed_icon_military.dds`
- environmental or recycling groups: `gfx/interface/icons/generic_icons/environment_icon_i_o.dds`

## Important Caution

Do not treat `production_method_groups.md` as enough guidance by itself. It gives the field list, but not the practical workflow.

The real behavior depends on things the stub does not emphasize:
- a PMG does nothing until a building includes it in `production_method_groups = { ... }`
- every PM in the group must exist in `common/production_methods/`
- moving a PM into a different group changes which choices are mutually exclusive
- local mod files often use patch operators such as `INJECT:`, `REPLACE:`, or `REPLACE_OR_CREATE:` when altering vanilla PMGs

Always verify the PMG block together with its building attachment and listed PMs.

## Vanilla Patterns

Current vanilla PMG files show these stable patterns:
- PMG keys usually follow `pmg_<role>_<building>` naming
- many PMGs only need `texture` and `production_methods`
- automation-oriented groups commonly use `mixed_icon_automation.dds`
- some groups set `ai_selection = most_productive` for infrastructure- or throughput-oriented choices
- `is_hidden_when_unavailable` is optional and often omitted

Current mod patterns add important extra context:
- many overrides use `INJECT:`, `REPLACE:`, or `REPLACE_OR_CREATE:`
- PMG localization is spread across `localization/english/` files and must stay in sync with the key
- custom PMGs are often grouped by building family across multiple files such as `ztr_new_*`, `ztr_vanilla_*`, and specialized content packs

## Editing Guidance

- Start from the nearest vanilla or local mod PMG for the same building family.
- Treat PMG edits as structural changes. A wrong PM list can break building slots, hide intended options, or make unrelated PMs mutually exclusive.
- Keep PMs in a group conceptually coherent. If two PMs should be stackable rather than alternative, they probably belong in different PMGs.
- Match the local override style in the file you are editing instead of switching patch operators casually.
- When adding a new PMG, wire all three parts together: the PMG definition, the building's `production_method_groups` list, and localization.

## Review Checklist

- The definition lives in `common/production_method_groups/`.
- Every PM key in `production_methods` exists in `common/production_methods/`.
- The PMG key is attached to the intended building or buildings in `common/buildings/`.
- The listed PMs are meant to be mutually exclusive in a single slot.
- `texture`, `ai_selection`, and `is_hidden_when_unavailable` fit the intended gameplay role.
- Localization exists for the PMG key.
- No PM was moved between groups accidentally when overriding vanilla content.
