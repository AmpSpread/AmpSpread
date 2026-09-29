# Validation — v9.8.4

Changes are limited to removing the optional GPU driver integration, corresponding data/UI paths, the version and documentation. No PSU fan protocol or locking change was made.

- Portable regression suite, including PSU power, history, Top 5 replays, fan state machines, updater, slider and viewport tests: passed.
- Race suite: passed.
- Linux vet and Windows vet (inherited Win32 unsafeptr exception): passed.
- Windows x64 executable and test executable compiled. Windows tests were not executed.
- Added regression tests for old enabled-driver configs and recordings: unrelated settings and PSU watts survive; obsolete driver fields cannot affect the live headline or be saved again.
- Full-session CSV round trip checks cover matching column counts, PSU source, valid zero watts and report statistics.
- Static source/EXE inspection requires absence of NVML/NVAPI, nvidia-smi and the removed helper command. Manifest 9.8.4.0, GUI subsystem, asInvoker, matching icon and updater launch probe verified. Executable unsigned.
- Fan/backend comparison recorded in BASELINE_COMPARISON.json. Fan controller, USB protocol, mutex policy, fan UI/slider and updater implementation unchanged from v9.8.3.

Ai1300TS fan control was reported by an external tester to the author. This does not establish persistence/recovery correctness across all hardware/firmware. Inherited v9.8.1 fan-backend limitations remain. No physical device, native Windows UI or gaming impact test was performed here.
