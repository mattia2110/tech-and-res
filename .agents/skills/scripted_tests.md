# Info
Every file is a "test suite", consisting of different "test cases". You should use different files to group relevant tests with similar desired "last_date".
The "success" trigger is checked first. If both the "success" and "fail" triggers were to fire on the same exact day, the fail trigger will be ignored and the test will pass.
If "last_date" is reached without either the "success" or "fail" trigger is reached, the test is considered "skipped". You can utilize this to avoid running tests when they would not be valid.

# Scripted tests usage
You can enable scripted tests in two ways:
- Command line arguments

There's three command lines arguments you can use:
- run_tests : this enables the scripted tests
- no_save_after_failed_test : this disables creating saves the day after a failed test. (otherwise enabled by default)
- save_before_failed_test : this disables creating saves the day after a test fails. This is very slow and not generally recommended.

## In-game console command
To enable scripted tests in-game simply use the command scripted_tests. Additionally you might specify if you want saves to be created before, after (default) or not at all
e.g.: 
- scripted_tests before
- scripted_tests none
- scripted_tests

Running the command again will disable tests.

# Output
At the end of the tests a text file with the results will be created in the Documents folder. Additionally, an XML file will be created in the game's binaries folder.

# Structure

last_date = "1879.11.21" # How long the tests in this file will run for. Once this date is reached, the "test suite" defined by this file is considered done

        tests = {
	        testcase_1 = { # the key acts as the name of the test
	
                acceptable_fail_rate = 0.0 # acceptable_fail_rate is output as metadata for use by test analysis, it does not impact the test behavior at all
                # Omitting this field will default the value to 0.0
		
		        run_count = -1 # the amount of times a test will run. Only counts runs that either fail or pass. A value less than 0 will cause the test to run indefinitely.
                # Each run overwrites the previous result. So if you have run_count = 3 and get results PASS, PASS, FAIL. It will count as a fail.
                # Omitting this field will default the value to 1
		
                success = { # a trigger block that is checked every day. If it passes, the test is marked as passed.
                    ...
                }
                fail = { # a trigger block that is checked every day. If it passes, the test is marked as failed.
                    ...
                }
            }
        }
