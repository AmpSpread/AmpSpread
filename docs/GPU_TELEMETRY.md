# NVIDIA telemetry — v9.8.78

NVIDIA power, core, memory and hotspot reads run inside the app on one background
worker. No GPU child process, service, external sensor app or elevation request
is used. The pre-selector NVML/NVAPI implementation is restored.

Core uses NVML with the original NVAPI fallback. Memory uses NVML field 82 with
its source timestamp and the original generation-specific NVAPI fallback. RTX50
hotspot automatically uses the six-register read-only query, with documented
NVML hottest-die fallback. Other supported generations retain their original
extended channels. Unknown generations never receive guessed hotspot channels.
See HOTSPOT_READER.md for register validation. System DLLs use restricted system
paths, and no NVIDIA write interface is loaded.

GPU updates use the saved 1000–10000 ms target (default 1000 ms). Unsupported optional queries are cached for the
reader lifetime; transient failures retry after 30 seconds. Missing, invalid or
stale values never become zero temperatures or another sensor's reading.

Normal access uses the app's existing token. No automatic UAC prompt is issued.
Removing the admin button cannot overcome driver access restrictions. Denied
sensors stay unavailable; GPU tools reports query details. Multiple NVIDIA GPUs
remain unsupported automatically, preventing cross-card readings.

One worker owns serialized native queries and teardown. Poll cancellation returns
to the controller without unloading active native code. A replacement waits for
that reader's cleanup. A driver call that never returns can hold the reader
until process exit. A native fault now affects the app because process isolation
was removed as requested. PSU work and UI do not wait on normal driver queries.

Diagnostics measures the single app process, including GPU work. No separate
helper counters are needed. See PERFORMANCE_MONITOR.md. Real Windows and physical
NVIDIA behavior were not executed in the build environment.

Polling changes re-arm the current worker timer without reopening native libraries
or resetting extrema. Display lifetime follows the configured cadence while
original timestamps remain intact. See STARTUP_AND_GPU_POLLING.md.
