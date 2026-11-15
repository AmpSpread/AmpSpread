<p align="center">
  <img src="https://raw.githubusercontent.com/AmpSpread/AmpSpread/main/assets/AmpSpread-Logo.png" width="80" alt="AmpSpread logo">
</p>

# AmpSpread

**See how current is shared across your six 12V-2×6 power pins.**

Two Windows tools: a complete monitoring app and a plugin that brings spread readings into MSI Afterburner.

## Choose your edition

| Edition | Best for | Download |
| --- | --- | --- |
| **[AmpSpread desktop app](https://github.com/AmpSpread/AmpSpread)** | Live pin currents, alarms, event replay and log analysis | **[v9.8.78 — latest release](https://github.com/AmpSpread/AmpSpread/releases/latest)** |
| **[AmpSpread Afterburner Edition](https://github.com/AmpSpread/AmpSpread-Afterburner-Edition)** | Spread graphs inside MSI Afterburner, without running the desktop app | **[v1.0.4 — latest release](https://github.com/AmpSpread/AmpSpread-Afterburner-Edition/releases/latest)** |

**Compatibility:** The desktop app supports **MSI MPG Ai1300TS PCIE5 and MPG Ai1600TS PCIE5** power supplies connected through USB telemetry. Afterburner Edition requires MSI's `PSU.dll` to expose six pin currents; it was developed using the Ai1600TS. Both run on Windows.

## AmpSpread desktop app

- **Live monitoring:** six pin currents, highest-minus-lowest spread, six-pin deviation and PSU telemetry; port 1 or 2.
- **Events and alarms:** configurable thresholds, Top 5 spread events, captured history and click-to-replay.
- **Graphs and reports:** timeline zoom/pan, CSV recording, offline log analysis and PNG/CSV exports.
- **GPU readings:** optional NVIDIA power, core, memory and supported hotspot readings; polling from **1–10 seconds**.
- **Everyday controls:** PSU fan controls, optional Windows startup, and CPU/memory/handle diagnostics.

PSU pin polling is adjustable from **100–1000 ms**. GPU support depends on the installed driver and permissions.

[Full app guide](https://github.com/AmpSpread/AmpSpread/blob/main/docs/APP_GUIDE.md) · [What's new in v9.8.78](https://github.com/AmpSpread/AmpSpread/releases/tag/v9.8.78)

## AmpSpread Afterburner Edition

Adds **Current Spread, Average Spread, Maximum Spread, Active Port and Data Complete** graphs to Afterburner's hardware monitor.

The plugin reads values already published by MSI's `PSU.dll`. It needs **no separate AmpSpread app or additional PSU polling**.

[Plugin setup and details](https://github.com/AmpSpread/AmpSpread-Afterburner-Edition#installation) · [Plugin changelog](https://github.com/AmpSpread/AmpSpread-Afterburner-Edition/blob/main/CHANGELOG.md)

## Get started

**Desktop:** download the Windows x64 ZIP, close the older app, extract the complete ZIP and run `AmpSpread.exe`. Monitoring starts automatically. Existing settings and recordings are retained.

**Afterburner:** download `ampspread.dll` from the plugin release, close Afterburner, and follow the [installation steps](https://github.com/AmpSpread/AmpSpread-Afterburner-Edition#installation).

Both tools provide monitoring; current balance alone cannot prove a connector safe. Downloads are unsigned by Windows. See [Windows security and installation notes](https://github.com/AmpSpread/AmpSpread/blob/main/docs/INSTALLATION.md).

<details>
<summary><strong>Documentation and technical details</strong></summary>

- [Desktop installation and updates](https://github.com/AmpSpread/AmpSpread/blob/main/docs/INSTALLATION.md)
- [GPU readings](https://github.com/AmpSpread/AmpSpread/blob/main/docs/GPU_TELEMETRY.md) · [PSU fan controls](https://github.com/AmpSpread/AmpSpread/blob/main/docs/PSU_FAN_CONTROL.md)
- [Windows startup and GPU polling](https://github.com/AmpSpread/AmpSpread/blob/main/docs/STARTUP_AND_GPU_POLLING.md)
- [Data and recordings](https://github.com/AmpSpread/AmpSpread/blob/main/docs/DATA_AND_UPDATES.md) · [Performance](https://github.com/AmpSpread/AmpSpread/blob/main/docs/PERFORMANCE.md)
- [Desktop changelog](https://github.com/AmpSpread/AmpSpread/blob/main/CHANGELOG.md) · [Build validation](https://github.com/AmpSpread/AmpSpread/blob/main/docs/VALIDATION_v9.8.78.json)

Completed desktop logs can be analyzed offline without a connected PSU. This repository distributes the desktop app and documentation; its Go source is private. Afterburner Edition has its own public source repository.

</details>

[Report a desktop issue](https://github.com/AmpSpread/AmpSpread/issues) · [Report a plugin issue](https://github.com/AmpSpread/AmpSpread-Afterburner-Edition/issues) · **[Support AmpSpread on Patreon](https://patreon.com/AmpSpread)**
