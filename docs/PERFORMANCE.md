# v9.8.77 single-process diagnostics

GPU readings now execute inside AmpSpread. Diagnostics and performance CSVs
measure this single process, including NVIDIA user-mode allocations and CPU.
Do not compare the v9.8.76 main process alone with this combined process; that
older build reported the GPU helper separately. Removing a process does not
establish lower total CPU or memory usage. No measured reduction is claimed.

Five-second sampling, constant-size aggregates, one log writer and four 8 MiB
CSV caps remain. Tests exercise cancellation, native cleanup lifetime and log
bounds. Windows driver allocations and a real long-session soak are unmeasured.
See VALIDATION_v9.8.77.json and PERFORMANCE_MONITOR.md.

## v9.8.78

The GPU timer supports 1–10 seconds and reuses its worker, timer and fixed-size
rate-change notification. Rate changes preserve statistics and do not queue
catch-up reads. Windows startup does no work in the polling loop. These changes
have not been benchmarked for a whole-app CPU reduction.
