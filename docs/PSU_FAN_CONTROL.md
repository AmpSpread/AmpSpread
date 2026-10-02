# AmpSpread 9.8.12 PSU fan control

Supports MSI MPG Ai1600TS and Ai1300TS Safeguard+ PSUs only.

## Manual static and manual fan curve

Manual static saves the selected target when the slider is released. Manual fan curve now calls that SAME save function when the temperature rule chooses a new target. The validated sequence is Zero Fan OFF, F1 save/FC acknowledgement, manual mode with target, then F1 save/FC acknowledgement. Final mode, target, Zero Fan and cooling demand are read back. Missing acknowledgements and mismatches are failures, never reported as success.

Each graph point sets a temperature threshold. Its target is held until the next point. Whole-number temperatures and targets are used, with 2–12 points and nondecreasing fan targets. Click the line to add, drag to move, right-click a point to delete. Editing is local until Apply curve; applying saves the curve points locally and activates control.

The existing worker evaluates available telemetry at most every two seconds, saves on activation and when the target changes, and does not repeatedly save unchanged targets. Downward changes require a stable lower target for at least five seconds. Target values are the MSI protocol scale, not literal percentages of maximum RPM. The firmware-demand floor and at-least-75 target at 55°C remain.

## Coordination

Curves now use the same access policy as explicit Manual static saves: use the named MSI mutex when accessible, otherwise allow the existing in-process fallback for access-denied/missing-mutex cases. A single worker and transaction lease serialize AmpSpread reads, saves and readback. Timeouts and unexpected errors still fail. Nested saves reuse that lease without reacquiring the nonrecursive process lock.

This fallback does NOT exclude another program from accessing the PSU. Keep other PSU controllers, including MSI Center/Cooling Wizard background control, inactive. This version does not stop services, change Windows permissions, request elevation or claim exclusive cross-process ownership when only the internal lock is available. The 55°C Auto guard and recovery of older temporary sessions retain their strict coordination policy.

## Saved state and restoration

A version-3, identity-bound recovery marker records the previous Auto/Manual static state before the first curve save. Normal stop/exit restores AND SAVES that prior state after readback checks. If its static target is below current firmware cooling demand, or restoration fails, Auto is attempted. Fault recovery and recovery after an unclean exit restore and save Auto. Missing save acknowledgement retains the recovery marker for a later attempt. Old version-1/2 records retain their original recovery behavior.

A crash, forced termination, lost USB access or failed restoration can leave the last SAVED curve target active, including after a PSU power cycle. Restoration requires AmpSpread and successful communication; there is no independently verified hardware watchdog. Curve points reload on startup but never activate automatically.

Recovery and curve files are under %LOCALAPPDATA%\AmpSpread. Do not delete a pending recovery marker merely to bypass recovery.

## Auto and Zero Fan

Auto explicitly saves the firmware's automatic mode. Selecting Auto arms the existing 55°C guard for that monitoring session. Startup does not arm the guard. Zero Fan changes remain restricted to validated Auto states. The guard still uses temporary manual commands, not persistent curve saves.

## Validation limits

The manual save protocol was observed in supplied MSI traffic; the user's Ai1300TS tester reported working basic fan control. The user reports the persistent temperature loop is working. Independent validation here is limited to software tests. PSU nonvolatile-memory write endurance for repeated curve saves is unknown; no lifetime claim is made. This release does not claim conflict-free operation, measured gaming impact or comprehensive thermal protection.
