# Changelog

## 9.7.4 — Saved spread history cutoff

- Qualified spread replays now require a peak of at least **0.850 A**, inclusive,
  to be archived and shown in saved history and the replay picker.
- Existing recordings below that minimum are hidden, not deleted.
- The same minimum applies when monitoring stops and pending captures are flushed.
- Software, firmware and test alarm incidents remain available regardless of spread.
- Eligible replays retain their lower-spread pre-event and recovery samples.
- Live Top 5 qualification, software alarm settings and full-session CSV logging
  are unchanged.

## 9.7.3 — PSU-only power readings

- Removed direct NVIDIA telemetry, including NVML loading, initialization and polling.
- Replaced the headline GPU power reading with PSU-derived **GPU connector power**:
  measured +12V supply voltage multiplied by the six connector pin currents' sum.
- Kept total PSU output separate; connector power does not include PCIe slot power.
- Preserved compatibility with historical logs containing legacy GPU readings.

Both releases use the compact Meridian interface. Earlier development versions
are not described here as currently supported releases.
