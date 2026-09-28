# AmpSpread v9.8.2 — Guarded PSU fan control

**Only MSI MPG Ai1600TS PCIE5 and MPG Ai1300TS PCIE5 GPU Safeguard+ PSUs connected by USB are supported.**

This is a **prerelease for hardware validation**. Download the Windows x64 ZIP under Assets; normal in-app checks skip prereleases. v9.7.7 remains the stable release.

- Live PSU RPM, Auto, Customized static target, Zero Fan in Auto, and Restore automatic are available for both TS models without installing MSI Center.
- Session-only control is the default. Optional saving after exit is restricted to the captured Ai1600TS USB revision (`0DB0:808C`, bcdDevice `0200`). Ai1300TS and other revisions retain session control; permanent saving needs matching hardware evidence.
- Repaired persistent transactions: strict cross-process locking, validated write echoes, intermediate/readback checks, separate FC-acknowledged commits, and durable same-device recovery records.
- Missing ACKs and partial saves remain uncertain. Polling no longer falsely labels them saved. Failed saves attempt temporary Auto and require an explicit Save Auto to resolve persistent state.
- Restored an independent Auto recovery button. It works during stale telemetry or faults, without silently issuing a permanent commit.
- Added/repaired runtime cooling-demand, firmware, stale-data and zero-RPM guards; device-bound queued actions; restart/reconnect handling; race-safe state access; keyboard/drag commit handling; and minimized Settings polling.
- Slider labels use raw target units, not an unverified percentage conversion. The permitted range is 30–100 raw units as application policy.
- Existing Top 5 replays, saved history, optional isolated NVIDIA power and updater behavior remain. No case-fan/GPU-fan/voltage/protection-limit writes or elevation requirement are added.

Portable regression and race tests, fault-injection tests, Linux/Windows vet, Windows x64 compilation and static executable checks passed. This exact build has not been run on physical PSUs or in native Windows UI here. The original Ai1600TS captures support the protocol and reported reboot experiment, not a hardware certification of this patch. Forced exit, USB loss or a hung driver can prevent recovery. The executable remains unsigned.

Read `PSU_FAN_CONTROL.md` before using permanent manual control. Settings and recordings remain under `%LOCALAPPDATA%\AmpSpread`.
