# AmpSpread v9.7.5 validation

Build environment: Linux amd64; Go 1.27.1; Windows x64 cross-build.

## Automated gates

- Portable regression suite and updater tests passed.
- Go race detector passed.
- Linux go vet and Windows cross-vet passed. Windows vet excludes the inherited
  unsafe-pointer checks used by Win32 LPARAM callbacks (`-unsafeptr=false`).
- Windows x64 GUI EXE compiled with `-trimpath -buildvcs=false -ldflags="-H=windowsgui -s -w"`.
- PE inspection checks AMD64/GUI type, exact v9.7.5 manifest, asInvoker execution,
  embedded logo/icon resources and no direct NVIDIA telemetry entry points.

Updater coverage includes numeric version ordering, same-version/downgrade
rejection, prerelease/draft rejection, exact repository and asset addresses,
HTTPS redirect policy, metadata size limits, invalid responses and rate limits,
checksum failure, truncated/cancelled downloads, ZIP path/architecture checks,
unchanged installed EXE while staging, pending-state recovery, replacement
failure, partial replacement recovery, failed-launch rollback and an unconfirmed
running process retaining its backup.

## Limits

The portable installer tests simulate the replacement/launch operations. They
do not execute ReplaceFileW or prove the Windows helper and Win32 UI work on a
real Windows machine. Native Windows UI/rendering, end-to-end in-place restart,
SmartScreen/Smart App Control handling and connected-PSU behavior remain untested
in this environment. No claim of hardware safety certification or resolution
of previously reported display flicker is made.

Before relying on the new updater, perform a Windows smoke test: check for
updates, read the changelog, download while monitoring, Close and continue,
reopen the update screen, Restart now, confirm the version/changelog, verify
settings/history and inspect the previous-EXE backup. Exercise a non-writable
app folder and failed network request without altering Windows protections.
