# Windows checks — v9.8.78

- Open Settings / Tools at minimum window size and your normal DPI. Confirm all
  controls are readable. Leave startup unchecked for initial polling tests.
- Set GPU polling to 10000, save and verify GPU tools/header. Observe actual
  reading changes; values should remain present between polls. GPU min/max must
  not reset. Change to 1000 and back; cadence should change after current work,
  without another app process, driver overlap or stopping PSU samples/recording.
- Reject 999, 10001, empty and fractional millisecond inputs. Restart and verify
  a valid setting persists. The old configuration defaults to 1000 ms.
- Use 100 ms PSU polling with 10000 ms GPU polling. Compare performance logs;
  GPUPollMS should reflect the selected target and PSUPollMS remain independent.
  GPU short peaks may be missed at slow cadence. Check data expiry after failures.
- From a stable EXE path, enable startup and Save Settings. Inspect the AmpSpread
  entry in the current user's Run key, sign out/in or reboot, and verify one app
  instance with monitoring. Windows may defer launch. No UAC should be requested.
- Disable startup, save, and verify its entry is removed. Other startup entries
  must be unchanged. Move/rename the app, save from that location and verify its
  path updates. Windows Task Manager disabling/policy must not be bypassed.
- Check read/write failures are reported rather than shown as a successful save.
  Unit tests exercise rollback; don't make configuration unwritable on your live
  installation without a backup.

Native Windows / physical hardware checks remain outstanding. The in-process
NVIDIA worker retains its existing native-crash/stalled-call limitations.
