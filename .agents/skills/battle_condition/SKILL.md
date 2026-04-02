---
name: Battle Condition
description: Guide and template structure for Battle Condition in Victoria 3.
---

# Battle Condition

Battle conditions are dynamic modifiers applied to each side (attacker and defender) of a battle when it begins. Each side receives its own independent condition, which is randomly rolled or triggered based on various factors like commander traits, terrain, and the current state of the battle.

## File Location
Battle conditions are defined in:
`common/battle_conditions/*.txt`

## Structure

A battle condition block supports the following keys:

- **icon**: Path to the icon (relative to the `game` directory).
  - Example: `icon = "gfx/interface/icons/battle_conditions/example.dds"`
- **modifier**: A block of modifier types that impact this side of the battle.
- **possible**: A trigger block determining if the condition can be rolled. If empty, it can only appear via `instant_switch`.
- **weight**: A scripted value (default 1) representing the likelihood of this condition being picked. 
- **instant_switch**: A trigger block that, if true, identifies that the side should immediately switch to this condition at the next check.

## Scopes and Links

The weight calculation and triggers have access to the `battle_side` scope.

### Available Links from `battle_side`:
- `battle`: The overall battle scope.
- `commander` (or `scope:character`): The commander in charge of this side. *Note: A side might not have a commander.*
- `province`: The province where the battle is fought.
- `state`: The state containing the province.
- `state_region`: The state region containing the province.
- `country`: The country involved in this side of the battle.

### Special Scopes:
- `scope:is_advancing_side`: Returns `yes` if this side is the attacker/advancer.
- `opposite_battle_side`: Links to the other side's `battle_side` scope.

## Localization

Battle conditions require two localization keys in a `.yml` file:

- `battle_condition_<key>`: The name of the condition.
- `battle_condition_<key>_desc`: The description/flavour text of the condition.

Example for `my_condition`:
```yaml
l_english:
 battle_condition_my_condition: "Brilliant Maneuver"
 battle_condition_my_condition_desc: "The commander has identified a gap in the enemy lines."
```

## Template Example

```pdx
battle_condition_example = {
	icon = "gfx/interface/icons/battle_conditions/battle_condition_example.dds"

	modifier = {
		unit_offense_mult = 0.1
		unit_defense_mult = 0.1
	}

	possible = {
		# Only available for the attacker
		scope:is_advancing_side = yes
		# Requires a commander
		exists = scope:character
		scope:character = {
			has_role = general
		}
	}

	weight = {
		value = 1
		
		# More likely if the commander is an expert in the terrain
		if = {
			limit = {
				scope:character = {
					has_trait = plains_commander
				}
				province = { has_terrain = plains }
			}
			add = 2
		}
		
		# Scale by commander modifiers
		multiply = {
			value = 1
			scope:character = {
				add = modifier:character_battle_condition_example_mult
			}
			min = 0
		}
	}

	instant_switch = {
		# Switch to this if the enemy is in a panicked retreat
		opposite_battle_side = {
			has_battle_condition = battle_condition_panicked_retreat
		}
	}
}
```

## Best Practices

1. **Terrain Specifics**: Use `province = { has_terrain = <type> }` or labels like `label_flat`, `label_elevated` for general terrain categories.
2. **Commander Synergy**: Use `modifier:character_battle_condition_<key>_mult` to allow commander traits to directly influence the probability.
3. **Dynamic Battles**: Use `instant_switch` to model conditions that change as the battle progresses (e.g., a retreat starting, or logistics breaking down).
4. **Safety Checks**: Always use `exists = scope:character` before accessing commander links, as a side might be unled.