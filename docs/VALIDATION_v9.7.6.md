# AmpSpread v9.7.6 validation

Build environment: Linux amd64; Go 1.27.1; Windows x64 cross-build.

## Automated gates

- Portable regression suite, updater tests and Go race detector passed.
- Replay tests cover 0.000, 0.125, 0.750, 0.849, 0.850 and 1.125 A peaks after
  their samples leave the rolling buffer; peak and post-event context remain.
- Replacement tests verify the evicted buffer is released, current Top 5 entries
  stay replayable, and saved archive bytes remain unchanged.
- A synthetic 24-hour event verifies the five-entry limit and the 600-sample
  bound, preserving start, updated peak and recent context through compaction.
- Tests cover reset/new-session cleanup, ordered duplicate/backward timestamps,
  independent view snapshots and concurrent reading during replacement.
- Linux go vet and Windows cross-vet passed. Windows vet excludes inherited
  unsafe-pointer checks for Win32 LPARAM callbacks (`-unsafeptr=false`).
- Windows x64 GUI EXE and Windows test EXE compiled. The application uses
  `-trimpath -buildvcs=false -ldflags="-H=windowsgui -s -w"`.
- PE inspection verified AMD64/GUI, exact v9.7.6 manifest, asInvoker execution,
  embedded icon resources and no direct NVIDIA telemetry entry points.

## Limits

The Windows test EXE was compiled, not executed. Native Windows rendering,
click-to-replay behavior, automatic return to Live on eviction, in-place update
restart and connected-PSU behavior remain untested in this Linux environment.
The updater's portable tests simulate Windows replacement and launch operations.
This release remains unsigned.

For a Windows smoke test, let a sub-0.850 A event enter Top 5, wait more than
one minute and replay it. Replace the fifth event while viewing its replay and
confirm the app returns to Live. Check that saved history remains present and
that reset/new-session clears temporary replays. From v9.7.5, check for updates,
download while monitoring, choose Close to continue, then reopen the updater
and choose Restart now; confirm v9.7.6, the changelog and preserved settings.
