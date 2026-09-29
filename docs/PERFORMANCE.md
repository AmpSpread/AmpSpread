# Performance scope — v9.8.4

- Live PSU telemetry remains once per second. No NVIDIA driver or GPU helper is loaded or polled.
- Minimizing skips live redraws and graph copies and pauses replay playback. Static pages do not need live one-Hz redraws.
- Live fan RPM reuses existing telemetry. Configuration inspection runs only while the fan page is visible; minimizing or leaving it stops optional inspection. Slider dragging updates local UI; only a committed changed target submits a write.
- Top 5 replay buffers remain bounded to five captures of at most 600 samples each. Replacement releases the old capture and any active replay view.
- Full-session logging remains off by default. Enabled sessions use a 64 KiB buffer, flushed once per minute or when full/closed.
- Update checks/downloads happen only on request and can use CPU, network and disk while active.

Removing GPU polling eliminates that monitoring work; it does not establish zero overhead for USB, Go runtime, rendering or recording. Windows gaming frame times and equivalence to HWiNFO64 have not been measured. Compare the same game scene with the app closed and minimized using identical settings before claiming performance parity. Historical benchmark numbers from previous releases are not whole-app measurements for v9.8.4.
