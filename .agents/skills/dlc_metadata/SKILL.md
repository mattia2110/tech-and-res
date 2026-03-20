Database key, please use the same name as the dlc file name.
Localized name and description of the DLC will be derived from this key as follows:
dlc001: "Victoria 2 Remastered Soundtrack"
dlc001_desc: "Description of the preorder DLC"
Icon and banner texture path will be derived from the key as follows:  
icon:   gfx/interface/icons/dlc_icons/key.dds
banner: gfx/interface/banners/dlc_banners/key.dds  
Important note: Banners are loaded only for theme_provider dlc's

	dlc001 = {
		# DLC Name. Should be the same as the name field in the dlc file.
		name = "Victoria 2 Remastered Soundtrack"

		# The type of the DLC. Can be either major or minor.
		# Generally, expansion packs should be marked as major and other DLC's should be marked as minor.
		# Used for sorting, majors shows before minors
		type = minor|major

		# Sorting priority. Used for sorting within the majors and minors.
		# Higher priority shows first.
		priority = 0

		# App ID of the DLC on Steam.
		steam_id = "2071470"

		# Flag to mark this dlc as a theme provider, determines visibility in the theme selector
		# and whether the dlc has a banner texture
		theme_provider = yes
	}
