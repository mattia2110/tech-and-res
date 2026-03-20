company_key = {
	icon = "texture path"
	background = "texture path"
	
	flavored_company = yes/no

	uses_dynamic_naming = yes/no
	dynamic_company_type_names = {
	}
	
	building_types = { 
	}
	
	# Those building types will be used for company additional industry charters
	extension_building_types = {
	}
	
	replaces_company = other_company_key
	
	possible_prestige_goods = {
		This is a list of the prestige goods types that this company can produce
	}

	prestige_goods_trigger = {
		This trigger is executed in Company scope and checks if the company can produce prestige goods
	}

	# The following three triggers are used to split companies into four different categories: hidden/potential/attainable/available
	# All of them are evaluated in country scope.
	# If a company fails the first trigger it is hidden, if it passes the first but fails the second it is shown as a potential company,
	# so and so forth until if it passes all three triggers then it is shown as an available company.
	potential = {
		# A trigger evaluated in country scope to determine if this company type should be shown to the player as a potential company
	}
	attainable = {
		# A trigger evaluated in country scope to determine if this company type should be shown to the player as an attainable company
	}
	possible = { 
		# A trigger evaluated in country scope to determine if this company type should be available to the player
	}
	can_establish_in = {
		# A trigger evaluated in state scope to determine if this company type can have its building located in the scoped state
	}
	
	prosperity_modifier = {
	}	
	
	ai_will_do = {
		 # If this trigger is true, then the AI will potentially select this company as the next company it will try to form. It will do this even if it does not meet the conditions to form the company, and will actively try to construct buildings etc to meet the further requirements to form the company.
		 # To check this trigger, the company must be considered at least 'attainable'
		 # If this trigger is not present, the AI never try to found this company
	}
	
	# While the AI is trying to form a company (that has passed the ai_will_do, potential and attainable triggers) it will have increased weight for certain building types in certain states, which buildings, which states (determined through a trigger) and up to which level are all specified here
	# Note that scripting this with an empty state trigger will do nothing, as it would otherwise result in indiscrimate construction in all states!
	ai_construction_targets = {
		building_iron_mine = {
			level = 5
			state_trigger = {
				state_region = s:STATE_NORRLAND
			}
		}
	}
	
	ai_weight = {
	}
}
