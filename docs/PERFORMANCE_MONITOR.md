# Windows resource monitoring

Open **Settings / Tools → Diagnostics**. Resource sampling starts with AmpSpread
and continues every five seconds, including while minimized. This measures the
AmpSpread process, including its in-process NVIDIA reader, not total system usage or NVIDIA
kernel-driver allocations. It does not launch Task Manager or external commands.

| Reading | Meaning |
| --- | --- |
| CPU / peak | Process CPU time divided by elapsed time and all logical processors; 100% means the computer's total logical CPU capacity. This is a time-based reading and can differ from Task Manager's frequency-adjusted display. |
| Private / peak | Current private committed memory and highest observed private memory. This helps distinguish persistent allocation growth from working-set changes. |
| Private change | Current private memory minus the baseline for that process. |
| Working set | Memory currently resident for the process; it can change with Windows memory management. |
| Handles / peak (Δ) | Current and highest observed kernel handle counts, plus change from baseline. |
| GDI / USER | Windows graphics and window-management object counts. These counts cover the app UI. |

The first CPU sample is unavailable until a second valid sample exists. Missing
or stale values are unavailable, not zero. Process IDs and creation times separate process lifetimes; Reset baseline
clears observed peaks and establishes new baselines. A counter access
failure does not invalidate unrelated categories.

## Record a long session

1. Open Diagnostics and click **Start performance log**. Logging is session-only
   and off again after closing/reopening AmpSpread.
2. Use the app normally: leave the live view open, minimize it, visit history,
   change polling rate, or play a game. Note when you change the workload.
3. After several hours, return to Diagnostics and click **Stop performance log**.
4. Click **Open logs** and keep the CSV files you want to compare.

The folder is `%LOCALAPPDATA%\AmpSpread\Performance` (the app's usual AppData
fallback applies if LOCALAPPDATA is unavailable). Files are `performance.csv`,
`performance.1.csv`, `performance.2.csv` and `performance.3.csv`, newest first.
Each is capped at 8 MiB; the oldest is replaced on rotation. Starting a new log
rotates the previous current file. Repeated starts therefore advance retention.
Copy logs elsewhere before another run if you need to keep them indefinitely.
These limits apply only to performance CSVs, not your normal event/session files.

Every sample writes one main-process row. CSVs include timestamps,
process identity, validity flags, logical CPU count, configured
PSU polling interval and monitoring/recording state. All counters belong to the single app process. No logs are uploaded automatically.

**Reset baseline** clears diagnostic peaks and baselines on the next worker
command, with fresh values at the next scheduled sample. It does not truncate
CSV logs or clear PSU/GPU session statistics. Performance CSVs contain current
measurements, not display baseline/peak columns. A log write error stops logging
and is shown on the page; it does not stop hardware monitoring.

## Interpreting growth

Warm-up allocations and larger replay buffers can increase memory before it
settles. Compare private memory, handles and GDI/USER counts across repeated
similar workloads. A rising working set alone does not establish a leak. CPU
readings average the elapsed sample interval, so brief spikes can be missed.
Five-second sample peaks are not the operating system's instantaneous peaks.

This build's portable tests cover resource math, lifecycle and bounded logging.
A real Windows long-session soak is still needed; no native leak fix or measured
whole-app CPU reduction is claimed from these tests.

## v9.8.78 GPU target

CSVs append GPUPollMS for the selected 1000–10000 ms target; resource samples
still occur every five seconds. Compare the same workload/settings before drawing
CPU conclusions. Reduced query frequency does not guarantee proportional CPU savings.
