# Structure

	max_audio_channels = <int> # Optional. Sets the max number of channels that FMOD will use (combination of real and virtual channels). If it's not specified it will default to 512
	virtual_vol0_threshold = <float> # Optional. Sets the volume threshold for virtual channels, anything below this value will be considered a virtual channel. If it's not specified it will default to 0.001
	enable_audio_5point1 = <bool> # Optional. If set to "yes", will initialize the FMOD audio system in 5.1 instead of the system default value.
	audio_profile_headphones_path = <string> Optional. If set to an FMOD path, the Headphones Profile audio option will run that snapshot as long as the option is selected. Example value: "snapshot:/Output/Headphones"
	audio_profile_tv_path = <string> Optional. If set to an FMOD path, the TV Profile audio option will run that snapshot as long as the option is selected. Example value: "snapshot:/Output/TV"