# PSU fan control in v9.8.16

Supported hardware: MSI Safeguard+ Ai1300TS and Ai1600TS only.

- Auto saves the PSU firmware's automatic mode; AmpSpread does not implement MSI's firmware fan curve.
- Manual static saves a selected target when the slider is released. Target percentages are protocol settings, not percentages of maximum RPM.
- Zero Fan remains restricted to supported Auto states.
- Custom graph control, point persistence/loading and automated persistent curve saves have been removed.
- The existing 55°C guard is armed only after explicit Auto selection. It uses temporary manual commands, with a target floor of 75 and firmware-demand checks; it never commits persistent saves. Normal stop restores Auto after an active guard override.

Persistent saves retain their existing allowlist, F1/FC acknowledgement, readback checks and serialized USB worker. Monitoring, replay, updater and fonts are unchanged.

## Older recovery records

Old .curve.json point files are ignored. Version-3 pending recovery records are recognized solely for safe migration. They cannot resume a curve and never initiate persistent writes automatically. Select Auto to request one verified recovery transaction. Zero Fan ON restoration occurs only after Auto/Zero Fan OFF has first been saved and read back. Missing ACK, readback failure or wrong device retains the record. Failed saves are not automatically retried. Older temporary recovery records retain their existing recovery behavior.

Do not delete a pending record to pretend the hardware was restored. If Auto fails, independently restore and verify a safe setting with the manufacturer's controller. The close dialog permits an explicit user-confirmed exit while preserving the record; it does not claim recovery succeeded.

The removal and recovery paths have automated software tests. This build has not been executed on real PSU hardware here.
