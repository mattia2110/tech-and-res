---
name: Buildings
description: Guide and template structure for Buildings in Victoria 3.
---

# Buildings

Buildings are the core of Victoria 3's economy and society. They employ pops, consume input goods, and produce output goods. Each building level represents a specific capacity for production and employment.

## File Location
Buildings are defined in:
`common/buildings/*.txt`

## Structure

### General Properties
- **building_group**: Reference to the building's category (e.g., `bg_light_industry`). Inherits many defaults from here.
- **icon**: Path to the texture used in the UI.
- **unique**: If `yes`, only one level can exist globally.
- **buildable / expandable / downsizeable**: Control the basic lifecycle of the building (default `yes`).
- **unique**: Only one level can exist in the world if `yes`.

### Triggers (Scope: State)
- **potential**: Determines if the building can *ever* exist in this state (e.g., `is_coastal = yes` for shipyards).
- **possible**: Determines if the building can be built/selected *right now* (e.g., checking country laws).
- **can_build**: Extra conditions for manual construction.
- **unlocking_technologies**: List of techs required to build it.

### Construction & Economy
- **required_construction**: Refers to a named constant like `construction_cost_high` (300 points) or `construction_cost_very_high` (450 points).
- **construction_points**: Direct integer value for construction cost (if not using constants).
- **ownership_type**: How it's owned (`self`, `other`, or `no_ownership`).
- **production_method_groups**: List of PM groups that define the building's operations.

### AI & Nationalization
- **ai_value**: Scripted value (state scope) for how much the AI prioritizes this building.
- **ai_nationalization_desire**: How much the AI wants to keep/take control of the building.
- **ai_privatization_desire**: How much the AI wants to sell it to the private sector.

### Graphics & Visuals
- **city_type**: Hub type (`city`, `farm`, `mine`, `port`, `wood`).
- **levels_per_mesh**: How many levels to add one visual building on the map.
- **background**: UI background for the building registry.

## Localization
Buildings require:
- `building_<key>`: Name of the building.
- `building_<key>_desc`: Flavor text.

Example:
```yaml
l_english:
 building_steel_mill: "Steel Mill"
 building_steel_mill_desc: "A massive facility for smelting iron into steel."
```

## Template Example: "Advanced Electronics Factory"

```pdx
building_advanced_electronics = {
	building_group = bg_heavy_industry
	icon = "gfx/interface/icons/building_icons/electrics_industry.dds"
	city_type = city
	levels_per_mesh = 25

	unlocking_technologies = {
		radio
	}

	production_method_groups = {
		pmg_base_electronics
		pmg_automation_advanced
	}

	required_construction = construction_cost_very_high
	ownership_type = self

	potential = {
		# Can only be built in states with a university
		any_scope_building = {
			is_building_type = building_university
		}
	}

	possible = {
		owner = {
			has_law = law_type:law_interventionism
		}
	}

	ai_value = {
		value = 1000
		if = {
			limit = {
				owner = { has_technology = tech_telephones }
			}
			add = 500
		}
	}

	background = "gfx/interface/icons/building_icons/backgrounds/building_panel_bg_heavy_industry.dds"
}
```

## Design Tips
1. **Inheritance**: Check the `building_group` file first. Many logic-heavy fields like `owners` or `economic_contribution` are usually set there to ensure consistency.
2. **Performance**: Avoid complex `ai_value` triggers if possible. The AI recalculates these frequently across all states.
3. **Auto-Expand**: Use `should_auto_expand` carefully. If an empty trigger is present, the UI will always show the auto-expand toggle.
4. **Residences**: Infrastructure/Urban buildings should use `generates_residences = yes` to contribute to urban hub growth.