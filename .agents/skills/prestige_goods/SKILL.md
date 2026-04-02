---
name: Prestige Goods
description: Guide for Victoria 3 prestige good definitions. Use when creating, editing, or reviewing files in common/prestige_goods, especially when adding a new prestige good, overriding a vanilla prestige good, or tracing how prestige goods interact with company types, journal entries, pop needs, localization, and neighboring common/ content.
---

# Prestige Goods

Use this skill for Victoria 3 files in `common/prestige_goods/*.txt`.

Do not use it for:
- company setup changes by themselves with no prestige-good definition changes
- journal-entry-only work with no prestige-good key changes
- localization-only edits with no gameplay definition changes

Pair it with:
- `Companies` when wiring prestige goods into `possible_prestige_goods` and `prestige_goods_trigger`
- `Journal Entries` when adding or revising prestige-good unlock chains
- `Localization Formatting` when adding or fixing visible prestige-good text

## File Location

Write modded prestige-good definitions in:
- `common/prestige_goods/*.txt`

Inspect the local mod first:
- `common/prestige_goods/*.txt`
- `common/company_types/*.txt`
- `common/journal_entries/*.txt`
- `common/pop_needs/*.txt`
- `common/goods/*.txt`
- `common/script_values/*.txt`
- `common/scripted_triggers/*.txt`
- `localization/english/*.yml`

Then compare against vanilla references:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\prestige_goods\prestige_goods.md`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\prestige_goods\00_prestige_goods.txt`

## What These Definitions Do

A prestige good is a branded or culturally famous version of a base good. It usually decides:
- which normal good it is derived from
- how much prestige bonus it grants to the producing country
- which UI texture represents it

These files sit in a wider `common/` network:
- `common/company_types/` often exposes prestige goods through `possible_prestige_goods` and gates them with `prestige_goods_trigger`
- `common/journal_entries/` may create unlock paths that set company variables before a prestige good can be produced
- `common/pop_needs/` can change how strongly pops care about prestige goods
- `common/goods/` provides the base good key consumed by `base_good`
- `localization/english/*.yml` provides the player-facing prestige-good name

If you change or add a prestige good, search `common/` and `localization/` for:
- the prestige-good key itself
- `possible_prestige_goods`
- `prestige_goods_trigger`
- `can_potentially_produce_prestige_goods`
- `is_producing_prestige_goods`
- the matching localization key

## Workflow

1. Inspect the nearest current vanilla prestige good and the nearest local mod example for the same product category.
2. Search the local mod for company, journal entry, and localization references tied to the prestige-good key.
3. Decide whether the prestige good is always available, company-gated, or unlocked through a journal entry or company variable.
4. Copy the nearest vanilla or mod block and adapt it instead of relying on `prestige_goods.md`.
5. Keep the base good, texture path, and company/journal wiring consistent across all referenced files.
6. Re-check every referenced good key, company variable, journal-entry key, and localization key after editing.

## Common Structure

```pdx
prestige_good_example = {
	base_good = luxury_clothes
	prestige_bonus = 0.1
	texture = "gfx/interface/icons/goods_icons/prestige_goods/example_prestige.dds"
}
```

## Field Guidance

- Definition key: Referenced directly by companies, journal entries, scripted checks, and localization. Renaming it breaks all downstream wiring.
- `base_good`: Goods key that this prestige good is derived from. Verify it exists in `common/goods/`.
- `prestige_bonus`: Country prestige multiplier granted by producing the prestige good. Current vanilla consistently uses `0.1`.
- `texture`: UI texture path. Current vanilla and this mod use `texture`, not `icon`.

## Important Caution

Do not trust `prestige_goods.md` as a complete or current template by itself.

The bundled note file is especially misleading here:
- it shows `icon`, while current vanilla and this mod use `texture`
- it shows an `is_possible` stub, but current vanilla `00_prestige_goods.txt` does not use it
- it does not explain the very common downstream wiring through `common/company_types/` and `common/journal_entries/`

Always verify against `00_prestige_goods.txt` and the local mod's prestige-good company flow before inventing or deleting a field.

## Vanilla Patterns

Current vanilla prestige-good files show these stable patterns:
- each entry is usually just `base_good`, `prestige_bonus`, and `texture`
- vanilla consistently uses `prestige_bonus = 0.1`
- keys usually follow `prestige_good_<theme>` naming
- generic fallback prestige goods exist for several common goods alongside historical named goods

Current mod patterns add important extra context:
- overrides and additions often use `REPLACE_OR_CREATE:`
- company files frequently expose prestige goods through `possible_prestige_goods`
- some prestige goods are gated by journal entries that set company variables before production can start

## Editing Guidance

- Start from the nearest vanilla entry and then check the nearest local mod example for the same wiring style.
- Treat prestige goods as cross-file content. The definition block is small, but the real behavior often lives in companies and journal entries.
- Keep the prestige-good key stable once other files reference it.
- If this mod uses `REPLACE_OR_CREATE:` or a similar patch operator for the target key, match the local file convention instead of silently switching styles.
- When adding a new prestige good for a company-driven unlock flow, update the company file, the JE or variable flow if needed, and localization in the same pass.

## Review Checklist

- The definition lives in `common/prestige_goods/`.
- `base_good` exists in `common/goods/`.
- `texture` path is valid.
- The prestige-good key matches any company, JE, variable, and localization references exactly.
- Any `possible_prestige_goods` and `prestige_goods_trigger` logic in `common/company_types/` still points at the right key.
- Any JE or company-variable unlock flow for the prestige good still works after the rename or override.
- No outdated `prestige_goods.md` placeholder syntax such as `icon` was copied blindly.
