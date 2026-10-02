# Changelog

## 9.8.12 — Manual fan curves and live PSU readings (latest regular release)

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

