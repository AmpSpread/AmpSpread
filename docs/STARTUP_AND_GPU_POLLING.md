# Windows startup and GPU polling

Settings / Tools → **Open AmpSpread when I sign in to Windows**, then **Save
Settings**, registers the current EXE for this Windows user. Uncheck and save to
remove the entry. Startup uses the regular app flow, including automatic PSU/GPU
monitoring; startup logging is still off by default. It opens a normal window.
Tray/minimized startup is a future idea, not part of this version.

The setting uses only HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run,
value AmpSpread. It quotes the EXE path and uses no script, service, task, admin
launch or external application. The registry entry itself stores the choice.
No registry write occurs at ordinary launch. Saving after moving/renaming the EXE
updates the path. Keep the portable app in a stable folder. Windows can delay
startup or disable it through its own Settings, Task Manager or policy; this app
does not modify StartupApproved or force a disabled item back on.

**GPU polling (ms)** accepts 1000–10000 and defaults to 1000 for old/new settings.
This controls NVIDIA power, core, memory and hotspot queries together. Save
Settings changes the active timer in either direction after current native work.
It preserves extrema and leaves PSU polling independent. Query overruns do not
queue missed polls. Existing unsupported-query caching and 30-second recovery
backoff remain. A hung native call cannot safely be killed in-process.

Longer polling reduces query frequency and temporal detail; it may miss peaks.
Valid data retains its source timestamp. Display lifetime is the configured
interval plus five seconds (six seconds at the default interval); already-stale
provider data is not revived. No missing reading becomes zero. The next failed
query invalidates current data through the existing error path.

Performance CSV sampling remains every five seconds and appends GPUPollMS.
That column is a configured target, not a measured driver-call interval. Use GPU
tools query timing and observations on your PC to assess actual behavior.

Reference: https://learn.microsoft.com/en-us/windows/win32/setupapi/run-and-runonce-registry-keys
