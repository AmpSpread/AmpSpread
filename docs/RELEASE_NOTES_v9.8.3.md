# AmpSpread v9.8.3 — Dedicated PSU fan page and window fitting

Only MSI MPG Ai1600TS PCIE5 and MPG Ai1300TS PCIE5 GPU Safeguard+ PSUs connected by USB are supported.

This is a UI test build based on the supplied working v9.8.1. It does not incorporate the v9.8.2 fan-controller rewrite. The original v9.8.1 hardware protocol, register allowlist, mutex/fallback behavior, persistent writes and recovery implementation are unchanged. This is not a new hardware-safety certification or a fix for previously identified backend hazards.

## Changes

- Dedicated PSU fan page, available from the new Live monitor fan card and a Settings shortcut. Back returns to the originating page.
- MSI-inspired dark fan panel with product name, measured RPM, Zero Fan switch, Auto/Customized selector, red progress track and round slider thumb.
- Reset session moved into the top header between AmpSpread and its version. It is available across pages.
- Fine slider positions for smoother mouse movement, rounded to the same whole-number hardware targets as v9.8.1. Arrow keys change one target unit; Page Up/Down change five.
- Polling cannot reset the slider during a mouse/keyboard edit or while a save is pending. Duplicate thumb-release/end-track notifications no longer submit the same edit twice. Dragging changes the UI; the released target is submitted through the existing v9.8.1 save function.
- Labels say Target, not a claimed literal percentage. The original 13–100 raw target range is preserved.
- A shared fit transform now scales painting, controls, text and pointer coordinates together. Live, replay/history, Settings, fan, analyzer/report and update pages fit inside the client area without a top-level scrollbar.
- Minimum client size is 720 × 480 logical pixels, capped to the monitor work area. Smaller views shrink text; a dense report necessarily becomes smaller. History lists, dropdowns and long release notes can still scroll their contents.
- Extra fan inspection stops when leaving the fan page or minimizing the window. The Live fan card reuses normal PSU telemetry; no extra polling is added there.

## Trying the build

Extract the ZIP into its own folder, close the existing AmpSpread process, then run AmpSpread.exe. Existing settings and recordings under %LOCALAPPDATA%\AmpSpread are reused. Keep the v9.8.1 executable available. Do not run multiple versions or another PSU fan controller simultaneously.

Check the fan page while monitoring: RPM, Auto/Customized selection, a mouse drag and a keyboard change. Then resize Live, Settings, History/Replay and the analyzer/report to check fit and text on your display. Changing fan targets uses v9.8.1's persistent save behavior.

The executable is unsigned. Portable testing and Windows compilation do not verify native Windows rendering, mouse feel, DPI monitor transitions or physical PSU operation. This is a manual-download prerelease; normal in-app update checks skip it. v9.7.7 remains stable. The bundled documentation was prepared before publication.
