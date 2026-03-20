Center piece of the power block statue

	hero_01 = {
		entity = "powerbloc_statue_hero_elephant_entity"

		default_choice_for_identities = {				# This statue piece can be used by AI for random generation for those identities
			power_bloc_identity_1
			power_bloc_identity_2
			power_bloc_identity_3
		}

		attach = {										# Attachments 
			locator = "first_head"						# Locator name should be the same as attachment points in the mesh
			accessory = accessory_01
			accessory = accessory_02
			accessory = {								# In case we need to specify the locator transform for specific accessory on the hero
				name = banner
				locator = {
					position = { 0.0 0.0 0.5 }
					rotation = { 19.62 0.0 1.0 }
					scale = 0.5
				}
			}
		}
	
		attach = {
			locator = "second_head"
			accessory = accessory_01
			accessory = accessory_02
		}
	}