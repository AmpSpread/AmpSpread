# v9.8.3 interface update

Fan controls are now on their own **PSU fan control** page. Open it from Live or the Settings shortcut. The original v9.8.1 hardware communication and persistent save behavior below are retained. UI targets are raw values, not a newly established percentage mapping. This UI build does not resolve the independent audit's backend safety findings.

---

# PSU fan control — v9.8.1

**Supported PSU fan writes are limited to MSI MPG Ai1300TS PCIE5 and MPG Ai1600TS PCIE5 with GPU Safeguard+, connected by USB.** MSI Center is not required for AmpSpread operation.

## UI and use

Open **Settings / Tools → PSU fan** while monitoring is running. The panel follows MSI Cooling Wizard's layout: PSU model and live RPM at the top, a Zero Fan switch, Automatic / Customized mode selector, then one horizontal target slider. AmpSpread additionally shows the slider's numeric percentage directly above it.

The persistent Customized slider is currently limited to **13–100%**. `13` is the lowest target directly observed from MSI Center on the physical Ai1600TS (`0x0D`); AmpSpread does not infer or probe lower targets. The slider is not a direct RPM command. Actual fan speed remains subject to the PSU's own protection logic.

Dragging the slider only changes the local preview. AmpSpread writes once when the drag ends, preventing a stream of nonvolatile save commands while the thumb is moving. Selecting **Customized** saves that target to the PSU. Selecting **Automatic** saves automatic control back to the PSU. **Zero Fan** is available only in Automatic mode.

Persistent settings intentionally remain active when AmpSpread closes and across a normal Windows reboot. To undo a persistent Customized setting, select **Automatic** while monitoring is running.

## Protocol validated from MSI Center USB captures

The Ai1600TS captures showed the following MSI Center sequence for each Apply operation:

- Zero Fan write: `50 43 00/01`
- save/commit request: `50 F1 00 ...`
- required PSU acknowledgement: `50 F1 FC ...`
- mode/target write: `50 41 01/03 00 <target>`
- second save/commit request and `FC` acknowledgement

Observed mode values are `01` Automatic and `03` Customized. Register `43` uses `00` for Zero Fan off and `01` for Zero Fan on. AmpSpread's persistent path is allow-listed to these fan registers plus the validated `F1` commit; unrelated PSU write registers remain blocked.

## Persistence validation

On the physical MPG Ai1600TS, a Customized `0x4D` target produced about **1648 RPM**. MSI Center was then closed, `MSI_Center_Service` and `MSI_Case_Service` were disabled/stopped, and Windows was rebooted. After reboot, the MSI services remained stopped and AmpSpread reported approximately the same fan RPM while performing read-only monitoring. The post-reboot capture contained no fan writes from AmpSpread. This validates persistence across a normal Windows reboot.

A full removal of AC power from the PSU has not been tested, so v9.8.1 does not claim persistence across complete loss of PSU standby power.

## Safety boundaries

- Only the existing monitoring worker sends PSU fan requests through the existing HID handle; the UI never performs device I/O directly.
- The full MSI-style write/commit sequence is serialized under AmpSpread's in-process PSU lock and MSI's `Global\\MSI_PSU_Mutex` when Windows permits access. If the existing MSI mutex ACL denies a standard-user handle, AmpSpread retains the v9.8.0 standalone fallback and does not request elevation.
- Fresh PSU telemetry and normal firmware alarm state are required before a saved change.
- A Customized target below the PSU-reported calculated cooling demand is rejected before the write.
- Every persistent save requires the validated `F1 → FC` acknowledgement and is followed by readback confirmation.
- Zero Fan is forced off when switching to Customized mode; AmpSpread does not use an unvalidated Customized + Zero Fan combination.
- Fan configuration is polled only while Settings is open, at most once per second. Normal monitoring outside Settings does not add these configuration reads.
- The old session-only recovery mechanism remains available internally so a pending recovery file from a previous preview can still restore safely. New MSI-style persistent operations do not create a recovery marker because persistence is deliberate.

Do not operate MSI Center/Cooling Wizard and AmpSpread fan writes simultaneously when cross-process mutex coordination is unavailable. This build does not change case fans, motherboard fans, GPU fans, PSU voltage/protection limits, or GPU power limits.
