# AmpSpread v9.8.66 PSU fan control

For MSI MPG Ai1600TS and Ai1300TS Safeguard+ PSUs only.

Auto, saved Manual static and Zero Fan remain on the dedicated fan page. The slider saves an integer static target when released. Target percentage is a PSU setting; RPM and temperature are measured telemetry. Custom persistent fan curves remain removed.

Persistent saves require F1 acknowledgement and matching readback. The initial read, complete save sequence and final readback share one coordination lease. Explicit Auto, Manual static, Zero Fan changes and legacy recovery again support standard-user operation as in v9.8.19. When Windows denies access to MSI's optional shared coordinator, or no coordinator exists and it cannot be created, these explicit operations retain AmpSpread's process lock and single monitoring worker for the whole transaction. An accessible shared coordinator is still acquired; a busy coordinator or an unexpected error still refuses changes. The unattended cooling guard continues to require a usable shared coordinator. Use one PSU controller at a time: the process lock cannot coordinate another application that cannot share the MSI mutex.

Before a potentially effective persistent save, AmpSpread writes a pending-save marker. Failed acknowledgements or readbacks keep persistence UNKNOWN, retain the original failure message, block further manual/Zero Fan changes, and do not automatically retry persistent writes. The marker survives restarting the app. Connect the same PSU and explicitly select Auto to confirm recovery. A live mode read is never treated as proof of persistence.

The worker reads fan configuration once at startup without changing it. A saved/manual setting receives temperature/RPM checks from existing telemetry and calculated-demand checks about every five seconds while monitoring. The zero RPM warning requires fresh zero RPM readings spanning at least five continuous seconds, after the existing ten-second spin-up allowance. Positive RPM, invalid or interrupted readings, reconnecting, stopping monitoring, or selecting Auto resets the zero RPM timer. Rechecking a cached sample does not advance it. Demand above the target or temperature at/above 55°C still raises its own warning without waiting for the zero RPM timer. Warnings do not automatically overwrite a saved static target. Changes within the same warning category do not produce a prompt every sample. Checks stop when monitoring stops.

Selecting Auto explicitly arms the existing 55°C cooling guard for that session. Startup does not arm it. At 55°C+ with Auto output below 75%, the guard attempts a coordinated temporary target of at least 75% and the calculated demand, checks readback, and attempts Auto restoration on normal stop. This is an application policy, not a certified thermal safeguard or proof of a firmware fault. A crash, lost communication or stalled driver can prevent restoration.

Old saved-curve recovery records remain supported. They do not cause automatic persistent commits on startup, polling, reconnect or shutdown. Explicit recovery of the same PSU is required. Do not delete recovery records to bypass confirmation.

Software tests do not replace physical PSU testing. This build has not issued PSU commands in the build environment.
