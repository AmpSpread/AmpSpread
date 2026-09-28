# PSU fan control — v9.8.2

**Only MSI MPG Ai1600TS PCIE5 and MPG Ai1300TS PCIE5 with GPU Safeguard+ and a USB connection are supported.** MSI Center is not required. This is a hardware-validation prerelease, not a certified protection system.

| Control | Ai1600TS | Ai1300TS |
|---|---|---|
| Live measured RPM | Yes | Yes |
| Session Auto / Customized target | Yes | Yes |
| Session Zero Fan, only in Auto | Yes | Yes |
| Restore automatic, without permanent save | Yes | Yes |
| Explicit save in PSU | Captured USB revision `0DB0:808C`, bcdDevice `0200` only | Disabled pending model-specific persistence evidence |

The supplied Ai1600TS captures establish the observed write/commit behavior, including successful FC and rejected/other FE replies. They do not physically validate this new executable, every target, thermal override, AC power loss, driver hangs or other firmware revisions. Ai1300TS session commands use the existing TS SDK protocol support; they have not been physically validated in this build environment. Other Ai1600TS revisions retain session control rather than assuming permanent-write compatibility.

## Use

1. Start monitoring, open Settings / Tools, and scroll to PSU fan.
2. Leave **Save in PSU after closing** unchecked for session control. It starts unchecked and is not saved in app preferences.
3. Select a target, then Customized. While Customized is active, releasing the slider commits the selected target. Mouse movement and keyboard repeats do not perform a write on every movement. The separate RPM counter displays measured response.
4. **Auto** returns to Auto with Zero Fan OFF. **Zero Fan** can be toggled only with a fresh, confirmed Auto state.
5. On the validated Ai1600TS revision, explicitly check **Save in PSU after closing** before selecting Auto/Customized or changing Zero Fan. The Auto button becomes **Save Auto**. A saved manual setting remains after AmpSpread closes. To change a running session-only manual setting to persistent operation, first Restore automatic.
6. **Restore automatic** is a separate recovery action: it sets temporary Auto and Zero Fan OFF, sends no F1, and does not require fresh telemetry. It remains available during faults or unreadable recovery records, but still requires the same connected PSU and usable USB coordination.

The target range is **30–100 raw control units**. These values are not calibrated percentages of RPM, PWM duty, or MSI slider travel. The floor is conservative application policy, not a manufacturer's minimum or an all-load safety guarantee. Values `0D`, `2B` and `4D` were observed in MSI captures; the UI does not invent a percentage conversion. The application does not offer fan curves, case-fan control, GPU-fan control, PSU voltage or protection-limit writes.

## Saving and recovery

- All writes require MSI's cross-process coordination mutex. Access denied, failure to establish coordination or timeout blocks the operation; there is no process-local fallback for fan control and no elevation request. Close other PSU controllers and their background components before using these controls. Programs that ignore MSI's mutex can still interfere.
- A single existing USB worker owns reads and writes. A transaction keeps coordination across preconditions, write echoes, intermediate readbacks, separate F1 commits and final readback. The register allowlist is 41/43/F1 for fan writes and 41/42 for fan reads.
- Both F1 responses must be FC. FE, missing or malformed replies fail the operation. Ordinary polling can report current device state but cannot promote an uncertain save to confirmed.
- Before a persistent mutation, a synced, device-bound unfinished-save record is created. If a partial save fails, AmpSpread attempts **temporary** Auto/OFF and keeps the record. Reconnect/startup recovery does not repeat a persistent write.
- If the status reports an unfinished save, press Restore automatic; on the validated revision, enable Save in PSU and press Save Auto to explicitly resolve permanent state. Ordinary close/update restart remains blocked while restoration is pending. Do not delete recovery records to suppress the warning.
- Session manual control retains a separate recovery record and attempts to restore Auto plus the original Zero Fan preference on Stop/normal exit. Recovery records bind to a hashed PSU identity. A queued action cannot follow a replacement PSU.
- A saved manual profile records only the user's last acknowledged manual target/device. It resumes monitoring safeguards after reopening AmpSpread; it never reapplies manual control or sends F1 at startup. Changed/faulted/stale telemetry, excess calculated demand or zero RPM after the grace period cause temporary Auto recovery and require explicit resolution of permanent state.
- Session and saved manual guards run while monitoring, even outside Settings. They cannot run while the application/PC is off. A crash, disconnected cable or hung driver can prevent recovery. No firmware watchdog or guaranteed thermal override is claimed. Use Auto for unattended operation until the hardware behavior is validated.

## Files written on the PC

All records are under `%LOCALAPPDATA%\AmpSpread` (use Settings / Tools → Open app data folder):

| File | Purpose |
|---|---|
| `psu_fan_recovery.json` | Session-only recovery: hashed device identity and prior Zero Fan preference |
| `psu_fan_recovery.json.save` | Unfinished/uncertain persistent operation; retained until explicit confirmed Save Auto resolves it |
| `psu_fan_recovery.json.approved` | Last acknowledged manual target/device; used only to resume monitoring safeguards |
| `*.invalid-<timestamp>` | Preserved unreadable recovery records, quarantined only after an explicit successful Auto recovery |

No fan telemetry log is continuously written. Existing settings/history/recording paths and retention rules remain unchanged.

## Resource use

Live RPM reuses normal PSU telemetry. Optional Settings fan reads stop when minimized or on another page, and read failures back off. Active manual safety checks continue during monitoring. Fan changes run only on committed user actions or temporary safety recovery; permanent saves are never periodic. Normal telemetry is processed by the alarm engine before optional fan work.

Ordinary read/lock waits are bounded, but Windows cancellation must still wait for native completion to protect I/O buffers. A hung driver can block the hardware worker. Gaming frame times, whole-app Windows overhead and equivalence with HWiNFO64 have not been measured; zero impact cannot be promised.
