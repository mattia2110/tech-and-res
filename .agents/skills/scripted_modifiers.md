	is_accepted_culture_and_religion = {
		if = {
			limit = {
				is_accepted_culture = no
			}
			factor = 0.20
		}
		if = {
			limit = {
				is_state_religion = no
			}
			factor = 0.20
		}
	}