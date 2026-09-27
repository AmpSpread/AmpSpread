# AmpSpread v9.7.7 validation

Build environment: Linux amd64, Go 1.27.1, Windows x64 cross-build.

## Executed checks

- Full portable regression suite with Go race detection: passed (32.622 seconds).
- New reader tests cover default/legacy config opt-out, saved opt-in, cadence/no overlap, stale/future values, measured zero, nonfinite/negative/implausible data, slow reads, timeout, error latch, disable/re-enable races and startup leases retained until the native process exits.
- Protocol tests reject missing/oversized handshakes, unknown commands, rapid polling and invalid power; they verify cleanup and no continued reads after errors.
- Recording/replay tests keep connector power, new NVIDIA board power and historical GPU power distinct, preserving zero-watt readings and timestamps.
- Update tests cover policy codes 577/1260/4551, wrapped error preservation, failed/cancelled probes, background preparation ownership, close-during-preparation cleanup and blocked network actions during preparation. Existing checksum, extraction, replacement, rollback and startup-confirmation tests remain passing.
- A focused race run after the final updater error-text/log change passed (2.423 seconds).
- Linux vet and Windows vet passed. Windows vet excludes inherited unsafe-pointer warnings for Win32 LPARAM callbacks (`-unsafeptr=false`).
- Windows GUI executable and Windows test executable compiled; resources and manifest regenerated for 9.7.7.
- PE inspection verifies AMD64/GUI, exact 9.7.7 manifest, asInvoker, source-matching icons, the exact five NVML lifecycle/discovery/power symbols, and absence of banned temperature/field/NVAPI/control and rendering-injection interfaces.
- CPU microbenchmarks and synthetic one-week retained-array measurements are recorded in [PERFORMANCE.md](PERFORMANCE.md). These are not whole-process or gaming measurements.

## Native Windows limits

The Windows test executable was compiled, not executed. This environment did not exercise Windows UI layout, USB PSU reads, NVML/driver calls, GPU coexistence, frame times, Application Control enforcement or a real in-place EXE restart. Portable tests use fake driver/read/launch operations; they cannot prove hardware or kernel-driver behavior.

The build is unsigned. The screenshot from v9.7.5 showed Windows Application Control refusing the new EXE; the updater restored the old EXE and monitoring was visible. The new launch probe catches a denied staging launch before old-app closure where possible. It does not establish publisher trust or guarantee the final path's policy decision. A v9.7.5/v9.7.6 updater lacks this probe during its first upgrade to 9.7.7.

The optional reader supports exactly one NVIDIA GPU with signed System32 NVML. Other layouts/device counts or unsupported calls stop without fallback to broader APIs. Native calls are not force-terminated; a hung call may retain the reader process, preventing another reader and delaying an in-app update until it exits.

## Required native acceptance before claiming hardware validation

Check both PSU models; default PSU mode with no NVML loaded; opt-in/opt-out and zero/unavailable display; actual NVIDIA board-power semantics; CSV/replay round trips; minimize/restore and narrow Settings layout; slow/lost GPU handling; co-running sensor software; repeated game frame times. Exercise update Download/Close/Restart, policy-denied launch, rollback and preserved data on Windows. Publisher signing should precede protected-machine distribution; enterprise policies can impose additional restrictions even on signed files.
