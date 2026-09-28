# Performance evidence — v9.7.9 preview (v9.7.7 benchmark baseline)

AmpSpread is designed for low overhead, but this build has **not** been measured in a Windows game or compared against HWiNFO64 on the same PC. No honest measurement can guarantee that an active monitor will never affect frame timing on every system.

## Measures taken

- PSU sampling stays at one reading per second. Monitoring continues when minimized.
- A minimized window skips the live repaint and graph snapshot and pauses replay playback. Static pages do not request one-Hz live redraws.
- The default PSU power source never starts a NVIDIA reader or loads NVML.
- Opt-in board-power reads run in a separate below-normal-priority process, at most once every two seconds after completion. Slow/error responses stop future queries. No per-sample `nvidia-smi` process is used.
- Replay storage is capped at five temporary captures, each with at most 600 samples; its backing buffer can have 601 slots during compaction. Eviction releases that capture and any open view copy.
- Full-session logging is off by default. If enabled, a 64 KiB buffer flushes once a minute or when full/closed. Alarm and qualified saved-replay writes still occur. All persistent recordings follow their existing retention rules.
- Update checks and downloads run only on request. Launch preparation runs off the UI thread. An update download/check can consume network, CPU and disk resources while running.

## Portable CPU benchmarks

Linux amd64, AMD EPYC 9V74, Go 1.27.1, three one-second benchmark runs per case; median shown. Both versions used the same benchmark and environment. These do not run Windows, HID, GDI, NVML, disk recording or a game.

| Operation | v9.7.6 median | v9.7.7 median | v9.7.7 allocation result |
|---|---:|---:|---|
| Normal alarm-engine sample processing | 0.728 microseconds | 0.816 microseconds | 885 B/op amortized; allocs/op rounds to 0 |
| Snapshot plus full 600-point graph copy | 9.054 microseconds | 8.839 microseconds | 49,296 B/op; 2 allocs/op |
| Sustained alarm sample processing | 3.012 microseconds | 3.072 microseconds | about 4,160 B/op; 16 allocs/op |

Normal processing is about 0.09 microseconds slower in this run, with the added recording fields. These microbenchmarks describe a small part of total runtime cost; they cannot be converted into an app CPU percentage or an FPS guarantee. The optional native reader was not benchmarked on a GPU.

## Bounded sample memory

A synthetic one-week normal session retained the same sample-array capacities as after one hour:

| Buffer | Observed capacity / bytes |
|---|---|
| Shared ring plus 600-point graph | 66,248 bytes |
| One saturated live replay | 206,744 bytes |
| Five saturated live replay sample buffers | 1,033,720 bytes (about 0.99 MiB) |

`Sample` is 344 bytes and `GraphSample` is 72 bytes on this build. These are backing-array sizes, **not process working set**. They exclude strings/maps, Go runtime/GC, stacks, UI/GDI resources, an open replay's bounded copy, pending incident buffers and the optional helper. Saved history can continue growing on disk. Released memory is reusable; Task Manager need not immediately fall after a replay is evicted.

## What to use while gaming

For the fewest monitoring interactions, keep NVIDIA board power off, leave full-session recording off unless needed, and minimize AmpSpread. This still performs PSU reads and records eligible events.

To assess your PC, compare repeated runs of the same game scene with AmpSpread closed, minimized in PSU mode, then minimized in NVIDIA mode. Record frame-time percentiles and spikes as well as FPS, with the same frame-time recorder and game/settings each time. Compare HWiNFO64 separately at a matching polling interval and sensor scope. Watch CPU, private memory and disk activity for both AmpSpread processes when NVIDIA mode is enabled. Read-only telemetry can still wake a GPU or add driver work; process isolation does not remove that cost.

Native measurement is still required before claiming parity or no performance regression. No new background performance logger is enabled in the app.

## Fan preview overhead

Manual-off adds no fan USB requests except pending recovery. The live RPM label reuses the existing sample and updates at one-second intervals only in Settings; minimized windows skip the refresh. Manual-on adds two setting/duty readbacks per monitoring cycle. Writes occur on changes or restoration, not every sample. Failed recovery retries are limited to every 30 seconds while connected, or explicit request/reconnect. The earlier CPU benchmarks do not measure this hardware path. Windows whole-process/game measurements are still needed.


## v9.8.2 scope

Settings configuration reads stop when hidden/minimized; live RPM reuses telemetry. Session and previously acknowledged saved-manual guards run during monitoring. Native cancellation may still block on a hung driver. Earlier synthetic benchmarks do not measure this new hardware path. No gaming/no-stutter/HWiNFO equivalence claim is made.
