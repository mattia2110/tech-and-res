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

a# Structure

The input profile consists of input contexts and input actions.
Input contexts contain actions, which specify the mapping it has
to either a key on the keyboard, mouse button, or a button on a gamepad

# Structure

## Defines an input context

	input_context = {

		# The name for this input context (note that these names need to
		# be unique). There is a 'special' type of context, which can be
		# defined by using the name "common". This context behaves slightly
		# different from other contexts in that it is always 'active' (if
		# specified and enabled). The "common" context can be used for hotkeys
		# that need to be active in many contexts. An example would be pausing/resuming
		# the timer in a game or changing the gameplay speed
		name = "CONTEXT_NAME"

		# defines an input action
		input_action = {

			# The name for this input action. This parameter is required and
			# the name *MUST* be unique among all input actions. This means that
			# the same name *CANNOT* be shared for different actions in different
			# contexts.
			name = "ACTION_NAME"

			# Optional. A localization key can be specified here to get a localized
			# name for the action. This is used in the settings menu for instance.
			text = "LOC_KEY"

			# The scancode refers to the mapping that this action has to the keyboard. This
			# refers to the 'physical' location of the key, so for instance' Q' would be mapped
			# to 'Q' on a US keyboard and 'A' on an AZERTY keyboard. See the list below for the
			# specified values per key.
			#
			# NOTE: A maximum of 2 input mappings can be set per action (can be a combination
			# of 2 of any input type)
			scancode = number

			# The mouse_button is another type of input that can be mapped. The value is
			# given as a string value (see the list below for the available options).
			#
			# NOTE: A maximum of 2 input mappings can be set per action (can be a combination
			# of 2 of any input type)
			mouse_button = "MOUSE_BUTTON_NAME"

			# The gamepad is another type of input that can be mapped. The value is
			# given as a string value (see the list below for the available options).
			#
			# NOTE: A maximum of 2 input mappings can be set per action (can be a combination
			# of 2 of any input type)
			game_button = "GAMEPAD_BUTTON_NAME"
		}
	}

# Mouse button values

Currently, only the extra buttons (X1 and X2) on the mouse can be mapped.
The valid values are:

- "MOUSE_X1"
- "MOUSE_X2"

# Gamepad button values

The gamepad is modeled after an XInput gamepad. Values that can be mapped are:

- "GAMEPAD_A"
- "GAMEPAD_B"
- "GAMEPAD_X"
- "GAMEPAD_Y"
- "GAMEPAD_START"
- "GAMEPAD_BACK"
- "GAMEPAD_LSTICK" # Refers to 'clicking' the left stick
- "GAMEPAD_RSTICK" # Refers to 'clicking' the right stick
- "GAMEPAD_LSHOULDER"
- "GAMEPAD_RSHOULDER"
- "GAMEPAD_DPAD_UP"
- "GAMEPAD_DPAD_DOWN"
- "GAMEPAD_DPAD_LEFT"
- "GAMEPAD_DPAD_RIGHT"
- "GAMEPAD_LTRIGGER"
- "GAMEPAD_RTRIGGER"

# Keyboard scancode values

The scancodes are integer values and need to be specified as such.
The mapping here is based on the layout of the keys on a US keyboard:

- A = 4
- B = 5
- C = 6
- D = 7
- E = 8
- F = 9
- G = 10
- H = 11
- I = 12
- J = 13
- K = 14
- L = 15
- M = 16
- N = 17
- O = 18
- P = 19
- Q = 20
- R = 21
- S = 22
- T = 23
- U = 24
- V = 25
- W = 26
- X = 27
- Y = 28
- Z = 29

- 1 = 30
- 2 = 31
- 3 = 32
- 4 = 33
- 5 = 34
- 6 = 35
- 7 = 36
- 8 = 37
- 9 = 38
- 0 = 39

- Return/Enter = 40
- Escape = 41
- Backspace = 42
- Tab = 43
- Space = 44
- Minus = 45
- Equals = 46
- LeftBracket = 47
- RightBracket = 48
- Backslash = 49
- Semicolon = 51
- Apostrophe = 52
- Grave = 53 # Tilde key on a US keyboard. Produces the ` key
- Comma = 54
- Period = 55
- Slash = 56
- CapsLock = 57

- F1 = 58
- F2 = 59
- F3 = 60
- F4 = 61
- F5 = 62
- F6 = 63
- F7 = 64
- F8 = 65
- F9 = 66
- F10 = 67
- F11 = 68
- F12 = 69

- PrintScreen = 70
- ScrollLock = 71
- Pause = 72
- Insert = 73
- Home = 74
- PageUp = 75
- Delete = 76
- End = 77
- PageDown = 78

## Arrow keys

- Right = 79
- Left = 80
- Down = 81
- Up = 82

- NumLock = 83

- KeyPad_Divide = 84
- KeyPad_Multiply = 85
- KeyPad_Minus = 86
- KeyPad_Plus = 87
- KeyPad_Enter = 88 # On Windows this is the same as Return
- KeyPad_1 = 89
- KeyPad_2 = 90
- KeyPad_3 = 91
- KeyPad_4 = 92
- KeyPad_5 = 93
- KeyPad_6 = 94
- KeyPad_7 = 95
- KeyPad_8 = 96
- KeyPad_9 = 97
- KeyPad_0 = 98
- KeyPad_Period = 99

- LeftControl = 224
- LeftShift = 225
- LeftAlt = 226
- LeftOs = 227 # Windows key on Windows, Command on Mac, Super on Linux
- RightControl = 228
- RightShift = 229
- RightAlt = 230
- RightOs = 231 # Windows key on Windows, Command on Mac, Super on Linux

  	# Sorting priority. Used for sorting within the majors and minors.
  	# Higher priority shows first.
  	priority = 0

  	# App ID of the DLC on Steam.
  	steam_id = "2071470"

  	# Flag to mark this dlc as a theme provider, determines visibility in the theme selector
  	# and whether the dlc has a banner texture
  	theme_provider = yes
  }
