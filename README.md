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
- Keep the top five qualified spread events in a compact horizontal strip; click
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

## GPU power source

**PSU connector power is the default.** It uses only PSU measurements:

`GPU connector power (W) = sum of six pin currents (A) × measured PSU +12V voltage (V)`

This measures the monitored connector, excluding motherboard PCIe slot power. PSU-side voltage does not separately measure cable voltage drop. Total PSU output stays separate.

To show the NVIDIA driver's **whole-board power**, open **Settings / Tools**, enable **Use NVIDIA board power (off: PSU connector)**, then **Save Settings**. The live headline changes to **GPU BOARD**. Connector watts remain in the power panel and recordings. The two measurements have independent peaks and labels; missing NVIDIA readings show unavailable. A valid zero remains zero.

The optional reader uses only NVIDIA's documented NVML board-power query, in a separate process at below-normal priority. It makes one query at most every two seconds, waits after completion and never catches up missed polls. It requires exactly one NVIDIA GPU and Windows' signed System32 `nvml.dll`; older non-DCH layouts and unsupported power queries stop with an unavailable status. Nothing is installed or downloaded for the driver.

Errors, invalid values, a response slower than 250 ms, a one-second request timeout or an eight-second startup timeout stop the reader. No automatic retry occurs while enabled; switch off, save, then on and save to try again after the old reader has fully exited. Readings expire after five seconds. A hung native call may leave the small helper process alive until it returns; a named mutex prevents another reader from attaching. The app never forcibly terminates a driver call or unloads the DLL from another thread. These precautions cannot contain a kernel-driver fault or prove freedom from driver conflicts.

There are no GPU temperature, generic field, clock, voltage, fan or power-limit queries/controls, no private NVAPI calls and no game hooks. PSU mode does not load NVML. NVIDIA describes board-power values on Ampere (except GA100) and newer GPUs as a one-second average; this is not a transient power measurement. [NVIDIA reference](https://docs.nvidia.com/deploy/nvml-api/api/group__nvmlDeviceQueries.html)

Live PSU telemetry uses USB HID. The app has no PSU fan, voltage, firmware-update or hardware-limit controls. Configurable alarm limits are software monitoring limits.

## Compatibility

| Requirement | Details |
|---|---|
| Platform | Windows x64 |
| PSU support in code | MSI MPG Ai1600TS and Ai1300TS USB HID interfaces |
| Live connection | Compatible PSU connected through its USB telemetry interface |
| Offline analysis | Completed supported AmpSpread CSV log; no PSU connection required |
| GPU vendor software | None needed in PSU mode; optional NVIDIA power requires signed System32 NVML and exactly one NVIDIA GPU |

Ai1600TS operation has been reported by the developer during development.
Ai1300TS detection is implemented but has not been hardware-validated in this
release's build environment. Simultaneous access to the same PSU by other
monitoring applications has not been validated.

## Resource use

PSU sampling remains once per second. Minimized windows avoid live redraws and graph copies and pause replay playback. NVIDIA polling is optional and slower than PSU sampling. The five replay buffers remain bounded. See [measured scope and performance guidance](docs/PERFORMANCE.md); zero gaming impact and HWiNFO64 equivalence have not been established.

## Live Top 5 replays

Every event listed in the live Top 5 is replayable, including events below
0.850 A. Click an event's box to open it. Its samples stay available in a bounded
memory cache while that event remains in the Top 5. A larger replacement releases
the old replay; if it is open, the app returns to Live and clears that view too.
Resetting or starting a new session also clears the cache.

The cache holds at most five captures with at most 600 samples each. Long events
keep start, peak and recent windows, with omitted sections identified in replay.
The existing event qualification rules still apply: peaks of at least 1 A qualify
immediately; smaller peaks must sustain the existing three-second duration rule.
These temporary replays are separate from saved event history below.

## Saved event history

Since v9.7.4, qualified spread archives require an event peak of **0.850 A or
higher**. Existing lower-spread archives stay on disk but are hidden from the
history list. Alarm incidents remain available regardless of spread. This
history cutoff is separate from the configurable software alarm threshold;
live Top 5 qualification and full-session CSV behavior are unchanged.

## Installation and updates

Download `AmpSpread-v9.7.7-Windows-x64.zip` from the release's **Assets** list,
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
background polling. Versions before v9.7.5 need one manual upgrade to the current release.

From v9.7.7, Restart now checks Windows launch in the background before stopping monitoring. A blocked check leaves the current app open. This does not grant Windows trust or guarantee permission at the final install path; rollback still applies. Older updaters cannot receive this safeguard until upgraded. See [Application Control guidance](docs/INSTALLATION.md#windows-security-messages).

Press **Win+R** and enter `%LOCALAPPDATA%\AmpSpread` to see the data, or use
**Settings / Tools → Open app data folder**. `Spread Replays` holds archived
spread captures, `Alarm History` holds alarm incidents, and `Sessions` holds
optional full-session CSVs. Lower-spread archives from earlier versions remain
in those folders even when hidden in the history list. See [storage details](docs/DATA_AND_UPDATES.md).

## Before relying on a reading

AmpSpread is a monitoring tool, not a certified hardware protection system.
Its one-second sampling cannot capture every brief electrical transient.
Automated tests and static build checks do not replace Windows and connected
hardware testing. See [validation notes](docs/VALIDATION_v9.7.7.md).

This repository provides downloads, documentation and an issue tracker. The
application's Go source is not included in this distribution repository.
Third-party license notices accompany the download. Compatibility names belong
to their respective owners and do not imply endorsement.
