# v9.7.8 preview validation

Built on Linux with Go 1.27.1 for Windows amd64. No physical PSU or native Windows GUI was available.

Passed:

- Full portable test suite with Go race detection (`go test -race ./...`): ok  	ampspread	37.146s.
- Linux `go vet ./...`; Windows `go vet -unsafeptr=false ./...` (inherited Win32 LPARAM conversion exception).
- Windows test executable compilation and Windows GUI release build. Compilation does not mean native Windows execution.
- PE/icon/manifest checks, standard-user execution level, and the unchanged five-function NVML allowlist/no private GPU control checks.
- Fan tests: manual-off no I/O, 30% → 50% → automatic, no unchanged-target rewrites, lost ACK, failed readback, rising cooling demand, stale/invalid telemetry, firmware alarm/unknown status, external setting changes and stopped-fan guard.
- Same-device durable recovery after simulated restart, refusal to target a different PSU, recovery-file write gate, corrupt-record handling, restoration order before zero-fan, bounded retry intervals and concurrent requests/status reads.
- A queued manual request cannot bypass a newly latched fault. Exhaustive payload mode/percentage validation blocks unrelated registers and permanent save commands.
- Settings panel bounds at supported widths, including 560 DIPs. This is a geometry check, not a Windows visual test.

Executable SHA-256: `4d00fbff7ed3563dd4ab4d9334c051af86155da58a109b7636f39531184fd36f` (8,351,744 bytes).

The fan protocol was inspected in MSI Center 2.0.74.0 Power Supply Unit SDK 1.0.0.38 with explicit TS product IDs. Provenance is in the private source at `verification/v9.7.8/FAN_PROTOCOL.md`. No manufacturer binaries are distributed.

Not validated: native Windows UI behavior, actual fan response/settling, physical thermal override behavior, firmware persistence/watchdog behavior, forced-termination recovery, USB-driver hangs, coexistence with other hardware utilities, game frame times or HWiNFO64 equivalence. A crash, hung I/O or USB disconnect can leave manual settings active. This is a manual-download prerelease; v9.7.7 remains stable.

The executable is unsigned. Trusted publisher signing and Windows policy compatibility remain separate work.
