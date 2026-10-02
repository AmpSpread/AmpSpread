# Validation — AmpSpread v9.8.12

The release changes PSU output and temperature display to the current reading plus session minimum/maximum. Disconnected or invalid current readings show a dash while retaining valid session extrema; no samples show Not available. +12V averaging is unchanged.

TEST.txt records the full portable Go suite. TEST_RACE.txt records the full suite under the race detector. VET_WINDOWS.txt records Windows vet with existing Win32 unsafe-pointer checks excluded (-unsafeptr=false). BUILD_REPORT.json verifies the cross-compiled Windows x64 GUI executable, exact embedded manifest/version, icons, asInvoker execution and absence of NVIDIA entry points.

BASELINE_COMPARISON.json and CHANGES.diff identify changes from v9.8.11. All fan-control, USB, updater, replay and storage implementation files are byte-identical to that baseline; the only Go implementation edits are the version, current/min/max formatter and its two UI call sites.

The user reports the v9.8.11 curve working; an Ai1300TS tester previously reported basic fan control. No physical PSU, native Windows UI, Windows mutex/ACL, persistent-save endurance or gaming-performance tests were performed in this build environment. Automated tests use fake devices and do not certify hardware safety or zero gaming impact. The executable is unsigned.
