# v9.7.4 validation

Passed the full portable Go regression suite, including new checks for:
- Archiving exclusion at 0.125, 0.750 and 0.849 A; inclusion at 0.850, 0.938
  and 1.125 A after the existing qualification rules.
- The same cutoff on normal writes and stop-time flushes.
- An active event crossing the minimum becoming eligible, with its lower-spread
  pre-event samples retained.
- History scanning across current and legacy folders, keeping all software,
  firmware and test alarm incidents while filtering low-spread replay archives.
- Filtered recordings remaining on disk and the writer rejecting low-spread
  archives. NaN/infinite metadata does not qualify.
- The existing PSU-only calculation, CSV compatibility and no-GPU-reading tests.

Windows amd64 GUI release cross-compilation and Windows vet with
-unsafeptr=false passed. The disabled analyzer is for inherited Win32 LPARAM
conversions; this is not an unqualified full-vet pass. Executable inspection
checks the embedded version, icon and absence of NVML/NVAPI interfaces; details
are in Source/verification/release.json.

Native Windows rendering and connected hardware were not executed here. This
release does not establish a cause or fix for the previously reported flicker.
The executable is unsigned. Documents under Source/Historical describe older
releases and must not be treated as current validation.
