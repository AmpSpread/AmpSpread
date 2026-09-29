# AmpSpread v9.8.4 — PSU-only monitoring

**Only MSI MPG Ai1300TS PCIE5 and MPG Ai1600TS PCIE5 GPU Safeguard+ PSUs connected by USB are supported.**

This is the latest regular release, carrying forward v9.8.3's fan-control page and fitted layouts. The new version number lets existing v9.8.3 users receive the PSU-only changes through Check for updates.

## Changes

- Removed the NVIDIA driver integration completely: NVML loading and entry points, helper process, polling, IPC, retry/fault state, startup/shutdown hooks, settings toggle and live driver-power fields.
- All live hardware readings now come from the PSU. GPU connector watts are the six pin currents multiplied by the PSU's measured +12V supply voltage. This excludes motherboard PCIe slot power and is not whole-board GPU power.
- Old configurations that enabled NVIDIA power are accepted; the removed key is ignored and disappears when settings are saved. Existing recordings are not deleted. The former NVIDIA-specific power fields are ignored; older generic GPU columns remain offline file compatibility only and never query hardware.
- Keeps the dedicated MSI-inspired PSU fan page, live RPM, smoother committed-value slider, Live/Settings shortcuts, header Reset session and fitted pages from v9.8.3.
- An external tester reported working AmpSpread fan control on an Ai1300TS and approximately 2100–2200 maximum RPM. This is a user report, not a new independent protocol capture or validation of persistence, every target, recovery, or every PSU firmware revision.

## Fan-control scope

The fan backend and USB commands are byte-for-byte unchanged from v9.8.3, which used the author's working v9.8.1 backend. This release does not incorporate v9.8.2's safety rewrite; the previously documented v9.8.1 persistence/concurrency limitations remain. Targets are the original raw 13–100 values, not calibrated RPM percentages. Avoid concurrent MSI Center/Cooling Wizard fan writes, especially where the MSI mutex cannot be opened.

AmpSpread does not read or change GPU driver settings, GPU fan curves, clocks or limits. MSI Afterburner's GPU controls are outside this app's hardware path. This is not a blanket guarantee against conflicts from other applications polling the same PSU.

## Install / update

Use Settings → Check for updates, review the notes and download. Monitoring continues during download. Restart now installs the update; Close keeps using the current app. Or extract `AmpSpread-v9.8.4-Windows-x64.zip`, close the old instance and run `AmpSpread.exe`.

Data remains under `%LOCALAPPDATA%\AmpSpread`. No elevation is requested. The Windows executable remains unsigned. GitHub's automatic source archives contain the public documentation repository, not application source.

## Verification

Portable regression and race tests, Linux/Windows vet, Windows x64 build/test compilation, config migration and CSV checks, and static EXE/manifest/icon checks passed. Source and EXE scans reject NVML, NVAPI, nvidia-smi and the former helper argument. No native Windows UI, physical PSU or gaming benchmarks were run in this build environment; zero gaming impact is not claimed.
