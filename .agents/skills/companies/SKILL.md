---
description: Guide for Victoria 3 company definitions. Use when creating, editing, or reviewing files in common/company_types, including new company types, vanilla company overrides, dynamic naming setup, AI company behavior, and company prestige goods support.
name: Companies
---

# Company Types

Use this skill for Victoria 3 `company_types`, not for company charter types or localization by itself.

## File Location

Write modded company definitions in:
`common/company_types/*.txt`

Use vanilla references from:
`C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\company_types\*.txt`

In this mod, company files are already split by region and industry under:
`common/company_types/*.txt`

Related files often include:
- `localization/english/*companies*.yml`
- `common/prestige_goods/*.txt`
- `common/company_charter_types/*.txt`
- `gfx/interface/icons/company_icons/*`

## Workflow

1. Check whether the task is adding a new company, replacing a vanilla company, or adjusting an existing modded one.
2. Inspect the nearest vanilla or modded company in `common/company_types`.
3. Keep the company grouped in the most relevant regional or industry file instead of creating an arbitrary new file.
4. If overriding vanilla content in this mod, follow the existing mod pattern such as `REPLACE:company_key = { ... }`.
5. Verify all referenced building types, prestige goods, icons, and localization keys exist.
6. Recheck trigger scopes before finishing:
   `potential`, `attainable`, `possible` are country scope;
   `can_establish_in` is state scope;
   `prestige_goods_trigger` is company scope.

## Structure

```pdx
company_key = {
	icon = "gfx/interface/icons/company_icons/example.dds"
	background = "gfx/interface/icons/company_icons/company_backgrounds/example.dds"

	category = aristocrat_owned

	flavored_company = yes/no

	uses_dynamic_naming = yes/no
	dynamic_company_type_names = {
		"dynamic_company_type_company"
	}

	building_types = {
		building_example
	}

	extension_building_types = {
		building_example_extension
	}

	replaces_company = other_company_key

	possible_prestige_goods = {
		prestige_good_example
	}

	prestige_goods_trigger = {
		always = yes
	}

	potential = { }
	attainable = { }
	possible = { }
	can_establish_in = { }

	prosperity_modifier = { }

	ai_will_do = { }

	ai_construction_targets = {
		building_example = {
			level = 5
			state_trigger = { }
		}
	}

	ai_weight = { }
}
```

## Core Fields

- `icon`, `background`: UI art paths. Reuse existing company backgrounds where possible.
- `category`: Ownership/company grouping such as `aristocrat_owned`, `bureaucrat_owned`, `shopkeeper_owned`, or other valid game-defined values used by vanilla and the mod.
- `flavored_company`: Use `yes` for a named historical company rather than a generic dynamic company.
- `uses_dynamic_naming`: Enable dynamic naming logic. Vanilla generic companies usually set this to `yes`.
- `dynamic_company_type_names`: Localization keys used for generated names.
- `building_types`: Main building types that qualify the company.
- `extension_building_types`: Buildings used for additional industry charters and related expansion logic.
- `replaces_company`: Redirect one company into another company definition when needed by design.
- `possible_prestige_goods`: Prestige goods the company may produce.
- `prosperity_modifier`: Modifier block applied when the company prospers.
- `ai_will_do`, `ai_construction_targets`, `ai_weight`: AI formation and construction guidance.

## Trigger Semantics

These three triggers split the company into hidden, potential, attainable, and available states:

- `potential`: Country scope. Failing this hides the company.
- `attainable`: Country scope. Passing `potential` but failing this shows the company as potential only.
- `possible`: Country scope. Passing all three makes the company available.

Additional scope rules:

- `can_establish_in`: State scope.
- `prestige_goods_trigger`: Company scope.
- `ai_construction_targets.state_trigger`: State scope.

Keep scopes explicit. Company definitions often fail silently when a valid trigger is written in the wrong scope.

## Mod Patterns

This mod already uses both additive company definitions and vanilla replacements:

```pdx
REPLACE:company_basic_oil = {
	...
}
```

Use `REPLACE:` only when intentionally overriding an existing company key. For a new company, define a normal `company_new_key = { ... }` block instead.

Keep file placement consistent with the current mod organization:
- regional sets like `ztr_companies_europe.txt`
- baseline overrides like `ztr_99_basic_companies.txt`
- industry sets like `ztr_tech_companies.txt`

## Localization

At minimum, provide the company name localization key:

```yaml
l_english:
 company_example: "Example Company"
```

If `uses_dynamic_naming = yes`, also verify the relevant dynamic localization keys exist. In this mod those often look like:

```yaml
l_english:
 generic_company_example_dynamic_name_tag_singular: "Example"
 generic_company_example_dynamic_name_tag_plural: "Examples"
```

Use UTF-8-BOM for `.yml` files.

## Template

```pdx
company_example = {
	icon = "gfx/interface/icons/company_icons/basic_oil.dds"
	background = "gfx/interface/icons/company_icons/company_backgrounds/comp_illu_oil_drills.dds"

	category = aristocrat_owned
	flavored_company = no
	uses_dynamic_naming = yes

	dynamic_company_type_names = {
		"dynamic_company_type_company"
		"dynamic_company_type_corporation"
	}

	building_types = {
		building_oil_rig
	}

	extension_building_types = {
		building_natural_gas_rig
	}

	possible = {
		any_scope_state = {
			any_scope_building = {
				is_building_type = building_oil_rig
				level >= 5
			}
		}
	}

	prosperity_modifier = {
		state_migration_pull_mult = 0.1
	}

	ai_will_do = {
		any_scope_state = {
			any_scope_building = {
				is_building_type = building_oil_rig
				level >= 3
			}
		}
	}
}
```

## Editing Guidance

- Prefer copying the closest vanilla or modded company and trimming it down over writing one from scratch.
- Keep `building_types` tightly focused on the company identity; move secondary sectors into `extension_building_types`.
- Do not leave `ai_construction_targets` with an empty `state_trigger`; vanilla notes that this leads to indiscriminate behavior.
- When using `replaces_company`, verify the replaced company is not still expected elsewhere for flavor or country-specific content.
- If a company is meant to be historical and unique, confirm `flavored_company`, localization, and country gating all align.
