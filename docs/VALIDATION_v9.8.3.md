# AmpSpread v9.8.3 validation

This UI build starts from the user-supplied v9.8.1 source. The twelve backend files recorded in BASELINE_COMPARISON.json are unchanged, including fan control, USB protocol, mutex behavior, monitoring, NVIDIA access, archive policy and updater installation. v9.8.2 fan-controller changes are not included.

## Completed checks

- Full portable regression suite passed.
- Full race suite passed: ok  	ampspread	93.157s.
- New tests cover every page at 720x480, 800x600, 1280x540, 1280x720, 1920x1080, 3440x1440 and portrait dimensions; normal/compact modes; and pointer-coordinate mapping at 96, 120, 144, 192 and 240 DPI.
- Dedicated fan page controls remain inside their panel with no overlaps at tested design widths.
- Slider tests cover blocking stale readback during editing/saving, one commit per release sequence and fine UI position to integer hardware-target mapping.
- Linux vet and Windows vet passed. Windows vet retains the existing unsafeptr exclusion for native Win32 callbacks.
- Windows amd64 GUI application and Windows test executable compiled using Go 1.27.1. The Windows test executable was not run here.
- Static checks confirmed asInvoker operation, correct manifest/icon resources, unchanged restricted NVML interfaces and no forbidden interface strings.

Executable size: 8377856 bytes.
SHA-256: `f7d152249dd41c8d736830f4f74009fcc334a170e66709ed9d1b0240a55268e4`.

## Limits

Native Windows rendering, physical mouse feel, keyboard accessibility, monitor-to-monitor DPI transitions and physical PSU operation were not executed in this Linux environment. The new UI needs verification on the user's Windows PC. Automated geometry tests do not prove native painting is perfect.

Fan-control backend behavior, including previously identified v9.8.1 safety limitations, is inherited. This build does not claim to repair those findings or certify persistent fan control on either PSU model. The UI no longer describes raw targets as calibrated percentages.

The main page fits the window by scaling down where needed; smaller windows produce smaller text. The normal minimum client size is 720x480 logical pixels, capped to monitor work area. Long history lists, dropdowns and release notes retain their own scrolling. Native Windows gaming impact has not been measured. The application remains unsigned.

Published as a manual-download prerelease. Normal in-app checks skip prereleases; v9.7.7 remains stable.
