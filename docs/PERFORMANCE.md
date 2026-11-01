# Performance scope — v9.8.66

- PSU polling waits about one second between completed cycles. Optional GPU telemetry targets one second and skips missed polls after overruns.
- Minimized windows avoid live redraws and graph copies and pause replay playback.
- Current, average and maximum six-pin deviations update from existing PSU samples using fixed-size running values. They add no sensor queries or growing history buffer.
- Live fan RPM reuses existing telemetry. Fan configuration inspection is limited to the relevant controls; dragging the static slider changes local UI until its target is committed.
- Top 5 replay buffers remain bounded. Replay graphs cache static content, and live traces batch the measured pin points.
- Full-session logging is optional and uses buffered writes. Update checks and downloads occur only on request.
- GPU telemetry uses the installed NVIDIA driver and a bounded helper. No additional sensor service or kernel driver is installed.

Automated component tests and static checks do not establish whole-app Windows
CPU/RAM use, gaming frame-time impact or equivalence to HWiNFO. Compare the same
workload with the app closed and minimized before making performance claims.
