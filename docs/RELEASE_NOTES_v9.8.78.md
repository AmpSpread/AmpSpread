# AmpSpread v9.8.78

Windows x64 release for MSI MPG Ai1300TS PCIE5 and MPG Ai1600TS PCIE5 USB telemetry.
This changelog covers all changes since the previous public release, **v9.8.73**.

## Windows startup

- Added **Open AmpSpread when I sign in to Windows** in Settings / Tools.
- Off by default. **Save Settings** adds/removes AmpSpread's startup entry for
  the current Windows user, using the quoted current EXE path.
- Opens the normal app and starts monitoring at sign-in. No startup helper,
  service, scheduled task or administrator request is needed.
- Saving from a moved/renamed EXE updates the registered path. Failed settings
  saves restore the previous startup value. Windows startup disabling/policy is
  respected; the app does not recreate the entry on ordinary launch.

## Independent GPU polling

- Added **GPU polling (ms)**: **1000–10000 ms**, default **1000 ms**, covering
  NVIDIA board power, core, memory and hotspot readings.
- Save applies faster/slower settings to the existing reader without resetting
  GPU extrema, reopening the driver or changing PSU sampling.
- GPU freshness follows the selected cadence while preserving source timestamps
  and rejecting stale provider data. Longer polling can miss short peaks.
- Header and GPU tools show the GPU cadence. Performance CSVs append GPUPollMS.
- PSU pin sampling remains independently adjustable at **100–1000 ms**. Other
  PSU readings retain their one-second target; resource diagnostics sample every
  five seconds. Settings layout fits the added controls at minimum window size.

## Performance diagnostics and logging

- New **Settings / Tools → Diagnostics** page reports process CPU usage and peak,
  private memory/current/peak/change, working set, handles/current/peak/change,
  and GDI/USER object counts.
- CPU is normalized to total logical processor capacity. Missing/stale values
  remain unavailable; cached samples are not counted twice.
- **Start/Stop performance log**, **Open logs** and **Reset baseline** controls.
  Logging is optional, off on startup, and retained locally under
  `%LOCALAPPDATA%\AmpSpread\Performance` with at most four 8 MiB CSV files.
- Baseline reset affects diagnostic display aggregates only; it does not clear
  PSU/GPU session extrema or existing log records. Logging errors are reported
  without stopping hardware monitoring.

## NVIDIA readings and recovery

- GPU queries now run on a serialized background worker **inside AmpSpread**.
  Monitoring starts no separate GPU helper process. No multi-GPU selector or
  Admin GPU reader button is present.
- Retains the original automatic six-register RTX 50 hotspot reader and adds
  the documented NVML hottest-die fallback. Core/memory keep their NVML/NVAPI
  sources. Optional NVAPI fallback queries request only missing readings and
  do not replace a valid public reading.
- Unsupported/denied optional queries are cached for the reader lifetime;
  temporary failures retry after 30 seconds. More lost-driver/invalid-handle
  failures trigger reconnection. Malformed thermal structures fail closed.
- **GPU tools → Retry** reconnects only GPU access, preserving extrema while
  PSU monitoring, alarms and recording continue. Diagnostics show source/failure
  details, query time, data age and fixed-size reader counters.
- Native queries and cleanup stay serialized. NVIDIA DLL references are released
  after native work finishes. No overlapping replacement reader is started.

Driver permissions still determine which sensors work. No permission-free
hotspot guarantee is made. Moving GPU calls in-process removes crash isolation:
a native NVIDIA fault can affect AmpSpread, and a permanently stuck native call
can hold its GPU worker until app exit. No service or extra driver is installed.

## Timeline efficiency

- Calculates shared timeline X coordinates once per frame and reuses drawing
  scratch buffers, releasing them when leaving Live.
- Dense trace drawing preserves each screen column's first/last points and
  minima/maxima in time order. Sparse/zoomed views retain every drawing point.
- Limits ordinary visible markers while retaining current markers and all
  original alarm/recording samples. One reusable GPU timer handles cadence.
- These reduce drawing/allocation work; no whole-app CPU or gaming FPS gain is
  promised. No demonstrated native memory leak is claimed fixed.

## Retained behavior and release integrity

- Highest-minus-lowest spread calculations, six-pin deviation captions, Top 5
  ranking/replays, alarms, both PSU ports and recording formats retain their
  existing behavior. Proposed Top 5 redesigns/aggressive optimizations are not
  included in this release.
- Rectangle zoom, ordinary drag to pan, double-click return to Live, fan Apply,
  three-second fan cooldown and recovery behavior are retained. No fan protocol
  or PSU firmware protection setting was changed.
- Built with **Go 1.27.3** (previous public release: Go 1.27.2).
- Windows signing workflow is prepared in private source. This EXE remains
  **unsigned by Windows Authenticode**. The included verified Ed25519 updater
  signature is separate from Windows publisher trust.

## Download and update

Download **AmpSpread-v9.8.78-Windows-x64.zip**, close the older app, extract the
complete ZIP and run **AmpSpread.exe**. Existing settings and recordings are
retained. Supported versions can also use **Settings / Tools → Check for updates**.
Keep the portable EXE in a stable folder if enabling Windows startup.

The standalone EXE and **SHA256SUMS.txt** are also supplied. GitHub's automatic
source archives contain the public documentation repository, not private Go source.

## Validation

The tested v9.8.78 EXE is unchanged from the supplied test build. **280 portable
race tests passed (425 including subtests)**. Linux vet, Windows cross-vet
(excluding the existing unsafe-pointer callback check), Windows x64 build/test
compilation, static EXE checks and packaged update authentication/tamper rejection
passed. Public packaging updates documentation and preserves identical EXE bytes
and the updater signature; its package authentication is rechecked separately.

Native Windows startup/login, GUI/DPI and physical PSU/NVIDIA operation were not
executed in the build environment. See the included native-check list. Earlier
user-run performance logs are not a guarantee for every system or driver.

[Support AmpSpread on Patreon](https://patreon.com/AmpSpread)
