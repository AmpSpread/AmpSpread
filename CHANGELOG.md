# Changelog

## 9.8.78 — Windows startup, GPU polling and diagnostics

Windows x64 release for MSI MPG Ai1300TS PCIE5 and MPG Ai1600TS PCIE5 USB telemetry.
This changelog covers all changes since the previous public release, **v9.8.73**.

### Windows startup

- Added **Open AmpSpread when I sign in to Windows** in Settings / Tools.
- Off by default. **Save Settings** adds/removes AmpSpread's startup entry for
  the current Windows user, using the quoted current EXE path.
- Opens the normal app and starts monitoring at sign-in. No startup helper,
  service, scheduled task or administrator request is needed.
- Saving from a moved/renamed EXE updates the registered path. Failed settings
  saves restore the previous startup value. Windows startup disabling/policy is
  respected; the app does not recreate the entry on ordinary launch.

### Independent GPU polling

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

### Performance diagnostics and logging

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

### NVIDIA readings and recovery

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

### Timeline efficiency

- Calculates shared timeline X coordinates once per frame and reuses drawing
  scratch buffers, releasing them when leaving Live.
- Dense trace drawing preserves each screen column's first/last points and
  minima/maxima in time order. Sparse/zoomed views retain every drawing point.
- Limits ordinary visible markers while retaining current markers and all
  original alarm/recording samples. One reusable GPU timer handles cadence.
- These reduce drawing/allocation work; no whole-app CPU or gaming FPS gain is
  promised. No demonstrated native memory leak is claimed fixed.

### Retained behavior and release integrity

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

### Download and update

Download **AmpSpread-v9.8.78-Windows-x64.zip**, close the older app, extract the
complete ZIP and run **AmpSpread.exe**. Existing settings and recordings are
retained. Supported versions can also use **Settings / Tools → Check for updates**.
Keep the portable EXE in a stable folder if enabling Windows startup.

The standalone EXE and **SHA256SUMS.txt** are also supplied. GitHub's automatic
source archives contain the public documentation repository, not private Go source.

### Validation

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

## 9.8.66 — Port 2 support and clearer balance readings (2026-11-01)

- Adds selected-port 12V-2x6 monitoring for PSU socket 1 or 2; port 2 current readings are author-confirmed on an Ai1600TS.
- Adds current, session-average and session-maximum six-pin mean-absolute-deviation captions while keeping gap-based main spread figures and Top 5.
- Corrects selected-port firmware status using the original MSI plugin mapping; incomplete C1 replies stay unavailable.
- Separates the app Safeguard display from the PSU Firmware alarm row.
- Centres grey captions using measured font height and balanced spacing; the GPU connector footer shows only maximum wattage.
- Retains selected-port metadata in new CSV/JSON recordings and compatible older-log analysis.
- Carries forward NVIDIA power/core/memory/hotspot monitoring, fan controls, replay and authenticated updates from the existing builds.
- Promotes the unchanged tested 9.8.66 package. 297 automated test/subtest records passed with two optional skips; race, Windows build/vet and static/signature checks passed. Physical protection-trip testing is not claimed.

## 9.8.19 — Pin colors and replay layout

- Matches explicit pin labels, associated current readings, bars and maximum markers to timeline colors across Live, Top 5, replay, history and reports.
- Preserves yellow/red warning outlines and the red current-limit line.
- Expands replay detail rows and graph header clearance; adjusts tick density for compact plots.
- Retains readable resizing, redraw optimizations and supported-PSU HID filtering. Fan commands, recovery, polling and updater are unchanged.
- Custom fan curves and NVIDIA driver integration remain removed.
- Portable regression, pin-color and replay geometry checks, Windows vet, GUI build and static executable checks passed. Native Windows, physical PSU and gaming validation remain outstanding.

## 9.8.18 — Readable window resizing

- Keeps text at its normal scale while resizing supported windows. Minimum client area: 720 × 620 DIPs, with fallback fitting only when monitor space is smaller.
- Tightens cards and spacing and reduces graph space before text. Supporting text uses 12–13 DIPs; native controls stay at 13 DIPs during ordinary resizing.
- Fits settings, PSU fan controls, history and replay into compact layouts. Replay reduces tick density when its plot is short.
- Adds Overview, Spread events, Pin statistics and Power & alarms sections to saved-log report previews. Full PNG/CSV exports remain available.
- Preserves redraw caches, supported-PSU HID filtering, recording folders, PSU-only monitoring and existing fan behavior. Custom fan curves remain removed.
- Portable regression/race suites, Windows vet, GUI build and static executable checks passed. Native Windows visual, hardware and gaming tests remain outstanding.

## 9.8.17 — Redraw fixes and recording folder

- Reduces repeated control moves, label/font updates, list redraws and background erases. Keeps the backbuffer until its dimensions change and applies the dark title bar before showing the main window.
- Filters cached HID hardware IDs before opening supported PSU device handles.
- Adds a recordings-folder chooser with restart-based activation, write checks, fallback warnings and continued access to earlier recordings. Operational settings, recovery and update files stay in AppData.
- Confirms updater startup before optional storage or PSU dialogs.
- Custom fan curves remain removed; Auto, Manual static, Zero Fan and legacy recovery safeguards remain.
- Regression/race tests, vet and Windows executable checks passed. Native Windows, hardware and gaming validation remain outstanding.

## 9.8.16 — Custom fan curve removed

- Removed graph editing, saved curve points and automated persistent curve saves after reported failures.
- Keeps Auto, Manual static, Zero Fan and the separate 55°C Auto guard.
- Legacy saved-curve recovery requires explicit Auto; no automatic persistent recovery saves at startup, polling or shutdown. ACK and readback are required.
- PSU-only monitoring, live readings, replay and updater remain.
- Software tests and Windows cross-build passed; connected PSU testing was not performed here.

## 9.8.12 — Manual fan curves and live PSU readings (withdrawn: custom fan curve failures)

- PSU output and temperature now show current/min/max; +12V voltage retains avg/min/max.
- Includes manual fan curves that save changed temperature-based targets through the Manual static path, with whole-number points, line-click insertion and right-click deletion.
- Normal curve exit restores and saves the prior state when communication succeeds; fault/crash recovery attempts Auto. Curves do not activate on startup.
- Adds a session 55°C Auto cooling guard, clearer peak-versus-average Top 5 cards, live following while zoomed, and reordered replay controls.
- Fan/USB behavior is unchanged from the user's working v9.8.11 test build. No NVIDIA integration or admin requirement.
- User-reported curve success is not independent hardware validation. Persistent-save endurance, concurrent controllers and failed-restoration limits remain documented.
- See [release notes](docs/RELEASE_NOTES_v9.8.12.md), [validation](docs/VALIDATION_v9.8.12.md) and [fan controls](docs/PSU_FAN_CONTROL.md).

## 9.8.4 — PSU-only

- Removed all NVIDIA driver loading, helper/polling code, settings and live power fields. PSU connector power is the only live GPU-power source.
- Carries forward v9.8.3 fan UI, smooth slider and fitted layouts without changing PSU fan/USB behavior.
- External Ai1300TS fan-control success reported; not blanket persistence/recovery validation.
- Regression/race tests, both vet targets and Windows static build verification passed. See [release notes](docs/RELEASE_NOTES_v9.8.4.md) and [validation](docs/VALIDATION_v9.8.4.md).

## 9.8.3 — Dedicated fan page and window fitting (prerelease)

- MSI-inspired PSU fan page with live RPM, Zero Fan and Auto/Customized controls; shortcuts from Live and Settings.
- Smooth slider positions, pending-save protection and deduplicated release handling; unchanged integer hardware targets.
- Reset session moved into the header. Shared scaling keeps pages, controls and pointer coordinates aligned without whole-page scrolling.
- Based on the author's working v9.8.1 fan backend after the reported v9.8.2 disabled-controls regression. v9.8.2's safety rewrite and persistence restrictions are not included; inherited v9.8.1 backend limitations remain.
- Regression/race tests, geometry checks, Windows compilation and static checks passed. Native Windows UI and physical PSU tests remain outstanding.

Read [release notes](docs/RELEASE_NOTES_v9.8.3.md) and [validation](docs/VALIDATION_v9.8.3.md). Unsigned manual-download prerelease; v9.7.7 was the stable version at publication.

## 9.8.2 — Guarded PSU fan control (prerelease)

- Live RPM, Auto, Customized static target, Zero Fan in Auto and independent Auto recovery for Ai1600TS/Ai1300TS; session control is the default.
- Permanent saving restricted to captured Ai1600TS USB revision 0DB0:808C / bcdDevice 0200. Ai1300TS saving remains disabled pending matching protocol evidence.
- Strict MSI coordination mutex, atomic transactions, echo/readback validation, separate FC-acknowledged commits and durable same-device recovery records.
- Partial or uncertain saves attempt temporary Auto and require explicit Save Auto; startup and polling never issue permanent commits.
- Device-bound actions, saved-manual guards, reconnect/crash records, race fixes and slider commit handling. Raw target units replace unverified percentage labels.
- Regression/race and fault-injection tests, vet, Windows compilation and static executable checks passed. Native Windows, real hardware and gaming tests remain outstanding.

Read [release notes](docs/RELEASE_NOTES_v9.8.2.md), [fan controls](docs/PSU_FAN_CONTROL.md) and [validation](docs/VALIDATION_v9.8.2.md). This unsigned manual-download prerelease is excluded from normal update checks; v9.7.7 was the stable version at publication.

## 9.7.8 — PSU fan control preview (prerelease)

- Added Settings live RPM, session-only 30–100% manual control, Apply speed and Restore automatic.
- Added fan-only payload checks, readback, cooling-demand/fault restoration and device-bound recovery. Failed restoration blocks ordinary exit and update restart.
- Uses the existing PSU connection without MSI Center or case-fan controls.
- Automated portable/race tests and Windows compile/build checks pass. Physical hardware, Windows UI and gaming behavior remain untested.

Read [fan control limits](docs/PSU_FAN_CONTROL.md) and [validation](docs/VALIDATION_v9.7.8.md). A crash, USB loss or hung I/O can prevent restoration. No firmware watchdog is promised. v9.7.7 remains stable; this prerelease is a manual download and remains unsigned.

## 9.7.7 — Optional NVIDIA power and update launch checks

- Added **Settings / Tools → Use NVIDIA board power (off: PSU connector)**. Choose it and press **Save Settings**. It is off in new and existing configurations unless explicitly enabled.
- PSU mode calculates connector watts from six pin currents × measured PSU supply voltage. NVIDIA mode shows whole-board power from the installed NVIDIA driver. Connector and board readings, peaks, recordings and replay labels stay separate.
- The optional reader runs in a separate, below-normal-priority process, querying only board power at most once every two seconds after each completed read. It requires exactly one NVIDIA GPU and a signed `nvml.dll` in Windows System32.
- Driver errors, invalid values, responses over 250 ms and timeouts stop polling. There is no automatic retry while that reader remains enabled. Readings expire after five seconds. A stuck native call blocks another reader from attaching; AmpSpread does not terminate the call, unload its library concurrently, reset the GPU or change driver settings.
- Minimized windows skip live redraws and graph copies; replay playback pauses. Existing one-second PSU sampling and bounded Top 5 replay storage remain.
- Added an asynchronous, hardware-free update launch check **before closing the current app**. Policy/signature blocks receive a clear message and diagnostic log. Replacement and rollback remain behind a successful check and helper readiness.
- Closing the app during update preparation aborts installation. Downloads still keep the app running; **Close** on the update page returns to the app, while **Restart now** requests installation.

This release remains unsigned. Windows policy blocks still require a trusted publisher/build. Launch preflight applies to updates initiated from v9.7.7 onward. Native Windows hardware and gaming performance are not validated.

## 9.7.6 — Every live Top 5 event is replayable

- Every event currently listed in the live **Top 5** has a viewable replay, including peaks below 0.850 A and zero-spread events that qualify.
- A dedicated in-memory cache keeps those replays available after the short rolling buffer expires.
- When a larger event replaces a Top 5 entry, its temporary replay samples are released. If that replay is open, the app returns to Live and clears the view's copy.
- The cache holds at most five captures, each bounded to 600 samples. Long captures retain start, peak and recent windows; omitted sections are identified in replay.
- Leaving a temporary replay clears its view copy. Resetting or starting a new session clears all five cached replays.
- The separate saved-history cutoff remains **0.850 A**. Alarm incidents and existing saved recordings are preserved; this change does not delete files from disk.

## 9.7.5 — In-app updates and data folder access

- Added **Settings / Tools → Check for updates** using the public AmpSpread GitHub Releases API. Checks happen only when requested.
- Shows installed/available versions, release notes, download progress and **View release on GitHub**.
- Downloads and verifies the Windows x64 ZIP while the app and monitoring keep running.
- After download, **Restart now** flushes recordings, closes the old process, replaces the executable and opens the updated app. **Close** returns to the app and keeps the update ready for later.
- Keeps a backup of the previous executable beside the app. A failed launch triggers a restore when the new process is confirmed to have exited; an unconfirmed running process is left alone with its backup retained.
- Reopens the changelog and exact GitHub release link after a confirmed update startup.
- Added **Open app data folder**, and documented recording, settings and update-cache locations.
- Highlighted the MSI MPG Ai1300TS / Ai1600TS GPU Safeguard+ hardware requirement.
- PSU polling, software alarm behavior, the 0.850 A saved-spread cutoff and existing recordings are unchanged.

**First upgrade:** v9.7.4 and earlier do not have an updater. Download and extract v9.7.5 manually once, then run `AmpSpread.exe` to enable in-app updates for future releases.

## 9.7.4 — Saved spread history cutoff

- Qualified spread replays now require a peak of at least **0.850 A**, inclusive,
  to be archived and shown in saved history and the replay picker.
- Existing recordings below that minimum are hidden, not deleted.
- The same minimum applies when monitoring stops and pending captures are flushed.
- Software, firmware and test alarm incidents remain available regardless of spread.
- Eligible replays retain their lower-spread pre-event and recovery samples.
- Live Top 5 qualification, software alarm settings and full-session CSV logging
  are unchanged.

## 9.7.3 — PSU-only power readings

- Removed direct NVIDIA telemetry, including NVML loading, initialization and polling.
- Replaced the headline GPU power reading with PSU-derived **GPU connector power**:
  measured +12V supply voltage multiplied by the six connector pin currents' sum.
- Kept total PSU output separate; connector power does not include PCIe slot power.
- Preserved compatibility with historical logs containing legacy GPU readings.

These releases use the compact Meridian interface. Earlier development versions
are not described here as currently supported releases.

