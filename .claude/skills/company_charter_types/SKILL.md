---
description: Guide for Victoria 3 company charter type definitions. Use when creating, editing, or reviewing files in common/company_charter_types, including new charter types, vanilla overrides, charter availability triggers, and AI charter behavior.
name: Company Charter Types
---

# Company Charter Types

Use this skill for Victoria 3 `company_charter_types`, not for company definitions in `common/company_types` or charter-related localization by itself.

## File Location

Write modded charter definitions in:
`common/company_charter_types/*.txt`

Use the base game for reference at:
`C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\company_charter_types\00_company_charter_types.txt`

Vanilla also ships a minimal reference note at:
`C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\company_charter_types\company_charter_types.md`

Related files often include:
- `common/company_types/*.txt`
- `localization/english/*company*.yml`
- `gfx/interface/icons/company_charter_icons/*.dds`

If this mod does not already have `common/company_charter_types`, create it only when the task actually needs custom or overridden charter types.

## Workflow

1. Check whether the task is adding a brand new charter type or overriding a vanilla one such as `investment_charter` or `trade_charter`.
2. Inspect the nearest vanilla example in `game\common\company_charter_types\00_company_charter_types.txt`.
3. Confirm the intended behavior against the company definitions that will use the charter, especially any assumptions about company scope, owner scope, or target scope.
4. Add or update the charter in `common/company_charter_types/*.txt`.
5. Verify all referenced icon paths, scopes, and AI conditions are valid.
6. Recheck whether the charter should be universally available or gated by `possible`, `ai_possible`, or both.

## Structure

```pdx
charter_key = {
	type = <investment/monopoly/industry/trade/colonization>

	icon = "gfx/interface/icons/company_charter_icons/example.dds"

	cooldown = { years = 5 }

	additional_input = yes/no

	possible = { }
	ai_possible = { }
	ai_weight = { }
}
```

## Core Fields

- `type`: The gameplay bucket for the charter. Vanilla uses `investment`, `monopoly`, `industry`, `trade`, and `colonization`.
- `icon`: UI icon path for the charter. Reuse existing charter icons unless the task includes new art.
- `cooldown`: Optional delay before the charter can be taken again. If omitted, the charter has no cooldown.
- `additional_input`: Optional flag used by some vanilla charters such as `monopoly_charter`, `industry_charter`, and `colonization_charter`.
- `possible`: Company-scope trigger that controls whether the charter appears as available to a company.
- `ai_possible`: Company-scope trigger that controls whether AI may consider the charter.
- `ai_weight`: AI scoring block for picking the charter. Vanilla notes this is scaled by company relative size compared to other companies.

## Scope Notes

Treat the charter definition itself as company scope unless a field clearly changes scope.

Common scope patterns from vanilla:
- `owner = { ... }` enters country scope.
- `scope:target = { ... }` is used when the charter logic evaluates a target country or state.
- Nested `any_scope_state`, `any_scope_treaty`, or `any_scope_article` blocks change scope again inside the trigger.

Keep scope transitions explicit. Charter scripts are small, but scope mistakes are easy to hide inside `possible` and `ai_possible`.

## Vanilla Pattern

Vanilla charter files are concise and mostly differ by gating logic and AI scoring. A representative pattern is:

```pdx
trade_charter = {
	type = trade

	cooldown = { years = 5 }

	icon = "gfx/interface/icons/company_charter_icons/trade_charter.dds"

	ai_possible = {
		company_prosperity >= 50
		NOT = {
			owner = {
				has_law_or_variant = law_type:law_isolationism
			}
		}
	}

	ai_weight = {
		value = 10
	}
}
```

Follow that style unless the mod has a reason to introduce more complicated gating.

## Interaction With Company Types

Charter types do very little in isolation. Their practical behavior depends on which companies can access them and what those companies are already producing.

When editing charter types, always verify:
- The affected companies in `common/company_types/*.txt` actually support the intended gameplay.
- `possible` is used only when the charter should be hidden or blocked for some companies.
- `ai_possible` and `ai_weight` align, so AI is not given a positive weight for a charter it can never legally take.
- `owner` and `scope:target` assumptions match the design. A charter aimed at foreign expansion, colonization, or trade usually needs explicit country-level checks.

## Overriding Vanilla

If you need to replace a vanilla charter definition, use the mod's normal override pattern for the target file and make the replacement intentional.

Before overriding a vanilla charter:
- Check whether a narrower change inside company definitions would solve the problem instead.
- Preserve core vanilla fields unless the design explicitly changes them.
- Re-read every trigger for scope validity after copying vanilla logic into the mod.

## Template

```pdx
example_trade_charter = {
	type = trade

	icon = "gfx/interface/icons/company_charter_icons/trade_charter.dds"

	cooldown = { years = 5 }

	possible = {
		owner = {
			NOT = {
				has_law_or_variant = law_type:law_isolationism
			}
		}
	}

	ai_possible = {
		company_prosperity >= 50
		owner = {
			is_great_power = yes
		}
	}

	ai_weight = {
		value = 10

		if = {
			limit = {
				owner = {
					has_strategy = ai_strategy_economic_imperialism
				}
			}
			add = 10
		}
	}
}
```

## Editing Guidance

- Prefer copying the closest vanilla charter and then trimming it down over writing one from scratch.
- Keep charter responsibilities narrow. Most design complexity belongs in the trigger logic, not in sprawling field sets.
- Do not invent new `type` values unless you have confirmed the game supports them.
- Reuse existing icon assets where possible; missing icon paths create avoidable UI failures.
- If a charter depends on diplomatic, colonial, or foreign-investment conditions, verify the relevant scopes against vanilla examples before finishing.
