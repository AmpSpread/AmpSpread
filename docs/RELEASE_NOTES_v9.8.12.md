# AmpSpread v9.8.12 — Manual fan curves and live PSU readings

**Only for MSI MPG Ai1300TS PCIE5 and MPG Ai1600TS PCIE5 PSUs with GPU Safeguard+, connected through USB.**

## Changes

- PSU output and temperature now display **current / minimum / maximum**. The first number is the latest reading, not the session average. The compact row label is “now/min/max”; session minimums and maximums remain. +12V voltage average/min/max is unchanged.
- Includes the working v9.8.11 manual fan-curve implementation without changing its fan/USB code. Temperature thresholds select and save a target through the same path as Manual static. Saves happen on activation and changed targets, not on every sample. The user reports the curve is working.
- Fan graph points use whole-number temperatures and targets. Click the line to add a point, drag to move it, or right-click a point to delete it; edit the selected point numerically for precision. Apply curve activates it.
- Normal curve stop or exit restores and saves the previous Auto or Manual static setting when communication succeeds. Fault/crash recovery attempts Auto. Curves never automatically activate on startup.
- Auto has a session cooling guard at 55°C. Its coordination and recovery checks remain required; this is not an independent hardware thermal protection system.
- Live Top 5 cards separate peak spread, high/low pin readings at the peak, and average spread with its analyzed duration. Qualifying peaks exceed 0.4 A; averaging covers up to five seconds before the peak and the ongoing qualifying spread. Replays retain bounded context up to 600 samples per event.
- Zoomed live graphs follow the newest samples and pin current levels. Panning left pauses following; double-click restores the full live view.
- Replay Play/Pause is to the left of Previous and Next.
- PSU-driven monitoring only: no NVIDIA driver integration. No administrator elevation required. Existing recordings and updater behavior are preserved.

## Fan-control limits

Use one PSU controller at a time. The Manual static-compatible access fallback cannot exclude MSI Center/Cooling Wizard or another application when the named MSI mutex is unavailable. Curve changes perform persistent saves; PSU nonvolatile-memory write endurance is unknown. A crash, USB disconnect or failed restoration can leave the last saved target active. Curve points themselves are stored by AmpSpread, not as a graph in the PSU. Target values follow MSI's protocol scale and are not calibrated percentages of maximum RPM.

See PSU_FAN_CONTROL.md for acknowledgement/readback checks and recovery details. Do not remove a pending recovery file to bypass recovery.

## Download and update

Download AmpSpread-v9.8.12-Windows-x64.zip, extract it and run AmpSpread.exe. Existing versions with the updater can use Settings / Tools → Check for updates. Downloading keeps the application running; restart is requested after the update is ready.

This executable is unsigned. Automated Go tests, race tests and Windows static/build verification do not replace native Windows, connected-PSU or gaming-performance tests.
