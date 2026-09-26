# Changelog

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
