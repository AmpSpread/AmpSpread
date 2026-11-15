<p align="center"><img src="assets/AmpSpread-Logo.png" width="112" alt="AmpSpread logo"></p>

# AmpSpread

> **Hardware compatibility: Live monitoring works only with MSI MPG Ai1300TS PCIE5 and MPG Ai1600TS PCIE5 power supplies featuring GPU Safeguard+. The PSU must be connected through its USB telemetry interface. Other PSU models are not supported.**
>
> Previously recorded AmpSpread logs can still be analyzed offline without a connected PSU.

**See how current is shared across your six 12V-2x6 power pins.**

AmpSpread is a compact Windows monitoring application for compatible MSI power
supplies. It displays individual pin currents, current imbalance, PSU telemetry
and recorded events in the Meridian interface.

**[Download the latest release](https://github.com/AmpSpread/AmpSpread/releases/latest)** ·
[Installation](docs/INSTALLATION.md) · [Changelog](CHANGELOG.md) ·
[Report a problem](https://github.com/AmpSpread/AmpSpread/issues)

## Latest: v9.8.78 — Startup, GPU polling and diagnostics

New since v9.8.73: Windows sign-in startup, independent 1000–10000 ms GPU polling,
process resource diagnostics and bounded performance CSVs, more efficient timeline
drawing, and improved NVIDIA recovery. GPU readings run inside AmpSpread, with
no GPU helper process or admin-reader button. The original automatic RTX 50
hotspot path remains with a public NVIDIA fallback; driver permissions still apply.

Save Settings to apply polling or startup changes. PSU pin sampling stays at
100–1000 ms; other PSU readings keep their one-second target. Spread formulas,
alarms, fan controls and Top 5/replay behavior are retained. Proposed Top 5 and
aggressive optimization ideas are not included in this release.

280 portable tests passed with race detection (425 including subtests). Windows
build/vet and packaged update authentication/tamper checks passed. Native Windows
startup/login, GUI and physical hardware verification remain outstanding.
See [all changes since v9.8.73](docs/RELEASE_NOTES_v9.8.78.md) and
[build validation](docs/VALIDATION_v9.8.78.json).

## What it does

- Monitor all six connector pin currents on the selected PSU port, total current and current/average/maximum spread.
- Compare current, session-average and session-maximum six-pin deviation in small grey captions.
- Optionally view NVIDIA board power, core/memory temperatures and supported hotspot readings.
- Keep the top five qualified spread events in a compact event list; click
  any listed event to replay it, regardless of spread.
- Switch between current bars and a timeline, and between normal and compact layouts.
- View PSU output, measured voltage and voltage statistics, efficiency, rail currents,
  temperature, fan speed and firmware alarm status.
- Record software alarm incidents and qualified spread replays, including samples
  before and after the event.
- Replay a capture with selected-point spread, high/low pin readings, playback,
  a sample slider, graph selection, zoom and pan.
- Analyze completed CSV logs offline and export a PNG report or summary CSV.
- Configure software thresholds, hysteresis, capture windows, consecutive samples,
  sound and optional CSV recording.

## Connector power and optional GPU telemetry

`GPU connector power (W) = sum of six pin currents (A) × measured PSU +12V voltage (V)`

This measures the monitored connector, excluding motherboard PCIe slot power. PSU-side voltage does not separately measure cable voltage drop. Total PSU output stays separate.

Optional GPU telemetry uses the installed NVIDIA driver through NVML/NVAPI on a
serialized worker inside AmpSpread. GPU board power is separate from PSU-measured
connector power. Missing sensors stay unavailable. The original automatic RTX 50
hotspot reader has a public hottest-die fallback; driver support and permissions
still apply. No admin-reader button, GPU helper process, extra sensor service or
kernel driver is used. A native NVIDIA fault can affect the app, and a permanently
stuck native call can hold its GPU worker until app exit. See
[GPU readings](docs/GPU_TELEMETRY.md).

MSI Afterburner's GPU fan, clock and power-limit settings are outside AmpSpread's hardware path. Avoid simultaneous fan writes from MSI Center/Cooling Wizard or other software accessing the same PSU. AmpSpread does not modify case fans, GPU fans, voltage, firmware or hardware protection limits.

## Compatibility

| Requirement | Details |
|---|---|
| Platform | Windows x64 |
| PSU support in code | MSI MPG Ai1600TS and Ai1300TS USB HID interfaces |
| Live connection | Compatible PSU connected through its USB telemetry interface |
| Offline analysis | Completed supported AmpSpread CSV log; no PSU connection required |
| GPU readings | Optional supported NVIDIA GPU and installed driver; PSU monitoring works independently |

Ai1600TS operation, including 12V-2x6 port 2 readings, was reported by the author; Ai1300TS fan control was reported by an external tester. These reports do not certify every firmware revision or failure case. Simultaneous access to the same PSU by other software has not been validated.

## Resource use

PSU pin sampling is selectable from 100–1000 ms. Other PSU readings target one
second. GPU polling is independently selectable from 1000–10000 ms (default
1000 ms). Diagnostics samples process resources every five seconds, with optional
logs capped at four 8 MiB files. Minimized windows avoid live redraws and graph
copies. See [performance guidance](docs/PERFORMANCE.md); no gaming FPS gain or
native leak repair is promised.

Enable **Open AmpSpread when I sign in to Windows** in Settings / Tools and save
to register the current portable EXE for your Windows user. Off by default.
Uncheck/save to remove it; moving the EXE requires saving from the new location.
See [startup and GPU polling](docs/STARTUP_AND_GPU_POLLING.md).

## Live Top 5 replays

Every event listed in the live Top 5 is replayable, including events below
0.850 A. Click an event's box to open it. Its samples stay available in a bounded
memory cache while that event remains in the Top 5. A larger replacement releases
the old replay; if it is open, the app returns to Live and clears that view too.
Resetting or starting a new session also clears the cache.

The cache holds at most five captures with at most 600 samples each at 1000 ms or 6000 at 100 ms. Long events
keep start, peak and recent windows, with omitted sections identified in replay.
A peak above 0.4 A qualifies even if brief. Each card shows the peak, its high/low
pins, and a separate average with analyzed duration. The average uses up to five
seconds before the peak and continues while spread stays at least 0.5 A; it does
not mean the peak was sustained for that duration.
These temporary replays are separate from saved event history below.

## Saved event history

Since v9.7.4, qualified spread archives require an event peak of **0.850 A or
higher**. Existing lower-spread archives stay on disk but are hidden from the
history list. Alarm incidents remain available regardless of spread. This
history cutoff is separate from the configurable software alarm threshold;
live Top 5 qualification and full-session CSV behavior are unchanged.

## Installation and updates

Download `AmpSpread-v9.8.78-Windows-x64.zip` from the release's **Assets** list,
extract it, close any older AmpSpread instance and run `AmpSpread.exe`.
Monitoring starts when the app opens. Settings stay in
`%LOCALAPPDATA%\AmpSpread`. Recordings use that folder by default, or the local
folder selected in Settings. Existing settings and recordings are retained.

This EXE has **no Windows Authenticode publisher certificate**. The package's Ed25519 signature authenticates supported app updates and is a separate mechanism. Windows SmartScreen or Smart App Control may warn
or block it. Hosting it on GitHub does not remove that restriction or certify
the application. See [installation notes](docs/INSTALLATION.md).

Starting with v9.7.5, choose **Settings / Tools → Check for updates**. Review the
changelog, then choose **Download update**. AmpSpread keeps monitoring during the
download. When it is ready, choose **Restart now** to install or **Close** to keep
using the app and install later. After restarting, the app shows the changelog
and a link to that release. Checks happen only when requested; there is no
background polling. Versions before v9.7.5 need one manual upgrade to the current release. Updaters from v9.8.50 onward also verify an Ed25519 signature bound to the version and EXE digest.

From v9.7.7, Restart now checks Windows launch in the background before stopping monitoring. A blocked check leaves the current app open. This does not grant Windows trust or guarantee permission at the final install path; rollback still applies. Older updaters cannot receive this safeguard until upgraded. See [Application Control guidance](docs/INSTALLATION.md#windows-security-messages).

Press **Win+R** and enter `%LOCALAPPDATA%\AmpSpread` to see the data, or use
**Settings / Tools → Open app data folder**. `Spread Replays` holds archived
spread captures, `Alarm History` holds alarm incidents, and `Sessions` holds
optional full-session CSVs. Lower-spread archives from earlier versions remain
in those folders even when hidden in the history list. See [storage details](docs/DATA_AND_UPDATES.md).

## Before relying on a reading

AmpSpread is a monitoring tool, not a certified hardware protection system.
Periodic sampling cannot capture every brief electrical transient.
Automated tests and static build checks do not replace Windows and connected
hardware testing. See [validation](https://github.com/AmpSpread/AmpSpread/releases/latest).

This repository provides downloads, documentation and an issue tracker. The
application's Go source is not included in this distribution repository.
Third-party license notices accompany the download. Compatibility names belong
to their respective owners and do not imply endorsement.

## Support AmpSpread

If AmpSpread is useful to you, you can [support its development on Patreon](https://patreon.com/AmpSpread).
