# v9.8.2 validation and remaining hardware scope

Built on Linux using Go 1.27.1 for Windows amd64, with trimpath, buildvcs=false and GUI/stripped linker flags. The binary build metadata confirms this compiler version.

- Full portable regression/race suite: `ok  	ampspread	34.341s`.
- 25 top-level fan tests passed, with additional per-command timeout/malformed-response subcases.
- Linux vet and Windows vet (`-unsafeptr=false`, inherited Win32 conversions) passed.
- Windows GUI build and Windows test-binary compilation passed; the test EXE was not run here.
- Static PE/manifest/icon/non-admin and unchanged limited NVML interface checks passed.
- New tests cover every boundary in the 12-command persistent transaction, missing/FE commit ACKs, malformed echo/readback, journal-before-write, partial recovery, restart without F1, Save Auto with stale telemetry, identity/state changes, persistence model gate, session restoration, corrupted journals, visibility/reconnect and concurrent new controller entry points.
- The runtime resumes guards for its acknowledged manual profile without reapplying manual mode or issuing F1 on startup. Disconnect recovery binds to the original unit.

Executable: 8418304 bytes. SHA-256: `39b05f122042c1dcf97da81c87fd7c1a05d97a6ccc5acc2e60e89bf2585a2355`.

The user-provided Ai1600TS USB captures were audited separately: separate Zero Fan and mode commits, FC responses, two FE responses, custom values 0D/2B/4D, Auto preserving 4D, and read-only post-reboot telemetry at roughly 1648–1664 RPM. These support the observed protocol, not a physical validation of this new executable. Persistent saves are enabled only for the captured Ai1600TS USB revision 0DB0:808C / bcdDevice 0200. Ai1300TS and other revisions retain session control.

Not performed: native Windows rendering/UI tests, new executable hardware tests on either model, thermal-load/zero-RPM fault injection on real hardware, true process-kill/power-loss trials, simultaneous native MSI access, or gaming/HWiNFO64 comparisons. Native cancellation can wait indefinitely for a hung driver; software cannot guarantee recovery while disconnected or after forced exit. The app is unsigned. This is a manual-download prerelease excluded from normal update checks.

The portable updater, replay/history and GPU-isolation tests pass. A live GitHub download test is opt-in and is not part of the default test run. Public releases contain only the Windows package/checksums and documentation, not private source or USB captures.
