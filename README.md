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

## What it does

- Monitor all six connector pin currents, total current and current/average/maximum spread.
- Keep the top five qualified spread events in a compact horizontal strip.
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

## PSU-only monitoring

Version 9.7.5 does **not** load NVML or directly poll NVIDIA GPU telemetry.
Its **GPU connector power** reading is calculated from PSU measurements:

`GPU connector power (W) = sum of six pin currents (A) × measured PSU +12V voltage (V)`

This is power through the monitored connector, **not total GPU board power**.
It excludes motherboard PCIe slot power and does not separately measure cable
voltage drop. Total PSU output is displayed separately.

Live telemetry uses the PSU's USB HID interface. The app has no PSU fan,
voltage, firmware-update or hardware-limit controls. Configurable alarm limits
are software monitoring limits.

## Compatibility

| Requirement | Details |
|---|---|
| Platform | Windows x64 |
| PSU support in code | MSI MPG Ai1600TS and Ai1300TS USB HID interfaces |
| Live connection | Compatible PSU connected through its USB telemetry interface |
| Offline analysis | Completed supported AmpSpread CSV log; no PSU connection required |
| GPU vendor software | No NVIDIA telemetry library required |

Ai1600TS operation has been reported by the developer during development.
Ai1300TS detection is implemented but has not been hardware-validated in this
release's build environment. Simultaneous access to the same PSU by other
monitoring applications has not been validated.

## Saved event history

Since v9.7.4, qualified spread archives require an event peak of **0.850 A or
higher**. Existing lower-spread archives stay on disk but are hidden from the
history list. Alarm incidents remain available regardless of spread. This
history cutoff is separate from the configurable software alarm threshold;
the live Top 5 and full-session CSV behavior are unchanged.

## Installation and updates

Download `AmpSpread-v9.7.5-Windows-x64.zip` from the release's **Assets** list,
extract it, close any older AmpSpread instance and run `AmpSpread.exe`.
Monitoring starts when the app opens. Settings and recordings use
`%LOCALAPPDATA%\AmpSpread` and are reused when upgrading.

This release is **unsigned**. Windows SmartScreen or Smart App Control may warn
or block it. Hosting it on GitHub does not remove that restriction or certify
the application. See [installation notes](docs/INSTALLATION.md).

Starting with v9.7.5, choose **Settings / Tools → Check for updates**. Review the
changelog, then choose **Download update**. AmpSpread keeps monitoring during the
download. When it is ready, choose **Restart now** to install or **Close** to keep
using the app and install later. After restarting, the app shows the changelog
and a link to that release. Checks happen only when requested; there is no
background polling. Older versions need one manual upgrade to v9.7.5.

Press **Win+R** and enter `%LOCALAPPDATA%\AmpSpread` to see the data, or use
**Settings / Tools → Open app data folder**. `Spread Replays` holds archived
spread captures, `Alarm History` holds alarm incidents, and `Sessions` holds
optional full-session CSVs. Lower-spread archives from earlier versions remain
in those folders even when hidden in the history list. See [storage details](docs/DATA_AND_UPDATES.md).

## Before relying on a reading

AmpSpread is a monitoring tool, not a certified hardware protection system.
Its one-second sampling cannot capture every brief electrical transient.
Automated tests and static build checks do not replace Windows and connected
hardware testing. See [validation notes](docs/VALIDATION_v9.7.5.md).

This repository provides downloads, documentation and an issue tracker. The
application's Go source is not included in this distribution repository.
Third-party license notices accompany the download. Compatibility names belong
to their respective owners and do not imply endorsement.
