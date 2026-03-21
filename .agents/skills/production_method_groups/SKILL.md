---
name: Production Method Groups
description: Guide to configuring Production Method Groups in Victoria 3.
---

# Production Method Groups

Production Method Groups (PMGs) categorize production methods inside a building. A building can only have one active PM from each PMG at any given time. PMGs are defined in the `common/production_method_groups/` directory.

## Naming Conventions
- Standard PMG keys typically use the `pmg_` prefix.
- They are often explicitly named after the building and group type (e.g., `pmg_base_building_uranium_mine`, `pmg_automation_building_water_plant`, `pmg_selective_mineral_extraction_coal_mine`).

## Structure and Properties

```txt
pmg_key = {
	# A list of Production Methods contained in this group. One and only one must be active at any one time.
	production_methods = {
		pm_bakery
		pm_sweeteners
		pm_baking_powder
	}
	
	# Affects how the AI chooses between PMs. 
	# Current options: most_profitable, most_productive. 
	# Usually omitted since 'most_profitable' is the default. Use 'most_productive' when you want the AI to prioritize throughput/output over sheer financial balance.
	ai_selection = most_productive 
	
	# Whether PMs in this group should be visible at all if they cannot currently be switched to.
	is_hidden_when_unavailable = no
	
	# The icon displayed on the Building panel when it uses a mixed configuration.
	texture = "gfx/interface/icons/generic_icons/mixed_icon_base.dds"
}
```

## Standard Textures
Depending on the category of the Production Method Group, specific textures are typically used in this mod:

*   **Base Processes/General Operations**: `"gfx/interface/icons/generic_icons/mixed_icon_base.dds"`
*   **Automation/Transport (Railways, Pumps, Robots)**: `"gfx/interface/icons/generic_icons/mixed_icon_automation.dds"`
*   **Refining/Chemical Processes (Chemicals, Oil, Medicines)**: `"gfx/interface/icons/generic_icons/mixed_icon_refining.dds"`
*   **Environmental/Recycling Focus**: `"gfx/interface/icons/generic_icons/environment_icon_i_o.dds"`