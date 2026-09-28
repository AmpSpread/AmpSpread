# PSU fan control preview — v9.7.8

**Only MSI MPG Ai1300TS PCIE5 and MPG Ai1600TS PCIE5 with GPU Safeguard+ are supported.** Connect the PSU's USB cable. MSI Center is not required.

This is a preview. Code and simulated failures have been tested; actual fan response, firmware overrides, crash behavior and coexistence with other Windows hardware tools have not been tested on a physical PSU here. v9.7.7 remains the latest stable release; the normal updater skips prereleases.

## Use

1. Start monitoring, open **Settings / Tools**, then scroll to **PSU fan**.
2. The live counter uses the existing PSU RPM reading. A stopped fan shows **0 RPM**; missing or expired telemetry, or stopped monitoring, shows **unavailable**.
3. Enter a whole-number target from **30 to 100**, then enable **Manual static PSU fan speed**. The initial target is 50%. The lower bound is AmpSpread policy, not a claimed MSI minimum.
4. To change 30% to 50%, enter **50** and press **Apply speed**. The status reports confirmed settings; the separate measured RPM counter shows physical fan response. Percent and RPM are different measurements.
5. Uncheck manual control or press **Restore automatic** to release AmpSpread's control. Fan changes apply separately from **Save Settings**.

Manual mode lasts for this monitoring session and is never enabled automatically after restart. AmpSpread temporarily disables zero-fan mode for manual operation and restores the previous zero-fan preference with automatic mode. It does not send the PSU's permanent save-settings command. If the PSU already has manual control from another program, first restore automatic mode through that program. AmpSpread refuses to take over an external manual setting.

## Safeguards and recovery

- Only the existing PSU worker sends fan requests through its existing USB handle. The UI does not call hardware. No case-fan, motherboard, GPU-fan, firmware, voltage, protection-limit or power-limit controls are added.
- Every fan payload is validated. Only mode/duty reads, automatic/manual mode and the zero-fan switch are permitted. Fan operations require the MSI coordination mutex; access errors stop control without requesting elevation. Software that ignores this mutex can still interfere. Avoid running another controller for the same PSU.
- Changes require fresh, alarm-free PSU telemetry and confirmed readback. The selected target is not rewritten on every sample.
- Manual mode adds two fan-setting reads to each normal monitoring cycle. If reported calculated cooling demand exceeds the manual target, AmpSpread attempts to return to automatic mode. This software check does not replace hardware thermal protection.
- Missing/stale/invalid telemetry, firmware alarm or unknown status, changed settings, failed readback, or zero RPM after ten seconds trigger the same restoration attempt. A fault latches manual mode off until **Restore automatic** explicitly resets it. A queued manual change cannot bypass a new fault.
- Before the first write, `%LOCALAPPDATA%\AmpSpread\psu_fan_recovery.json` stores a hashed device identity and the prior zero-fan preference. It contains no recordings. It is removed only after automatic mode and the prior zero-fan preference are confirmed. Do not delete it while recovery is pending. An unreadable record blocks manual control and is preserved.
- Recovery only targets the same PSU. Restarting monitoring or reconnecting it permits another attempt. Retries while connected occur at most every 30 seconds; **Restore automatic** requests an immediate attempt. A failed restore keeps the record.
- Normal stop/exit attempts restoration. Ordinary close and update restart are blocked if restoration remains unconfirmed; reconnect the same PSU, start monitoring and restore automatic mode first. Automatic mode is confirmed before zero-fan is re-enabled.

**Forced termination, an OS crash, hung USB I/O or an unplugged USB cable can prevent restoration. Manual settings may remain active until the PSU can be reached again. No firmware watchdog is guaranteed.** Keep automatic mode for unattended use until the preview has been validated on the hardware. For an initial check, use an idle PC and confirm 30% → 50% → automatic and the live RPM response before relying on this while gaming.

## Resource use

Manual-off adds no fan USB requests unless recovery is pending. RPM reuses the existing once-per-second sample. A Settings-only timer updates changed native labels, skips work when minimized, and is removed when leaving Settings. Manual-on adds the readback checks above. No MSI service, extra driver, continuous fan log or GPU call is installed. Whole-process Windows gaming overhead and HWiNFO64 parity have not been measured.
