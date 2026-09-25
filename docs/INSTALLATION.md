# Install or update AmpSpread

1. Open the project's Releases page and expand **Assets**.
2. Download **AmpSpread-v9.7.4-Windows-x64.zip**. GitHub's automatic **Source code**
   ZIP contains this documentation repository, not the runnable application.
3. Extract the ZIP into a folder you can write to.
4. Close any older AmpSpread window, then run **AmpSpread-v9.7.4-Meridian.exe**.

The build is for Windows x64. No installer, NVIDIA library or bundled kernel
driver is included. Live measurements require a compatible PSU's USB telemetry
connection. Monitoring starts automatically when the application opens.

## Keep your recordings

Settings and recordings are stored under `%LOCALAPPDATA%\AmpSpread`.
Updating the application does not require deleting that folder. Back up that
folder if you want an independent copy of your settings and recordings.

## Verify a download

Each release includes `SHA256SUMS.txt`. In PowerShell, run:

```powershell
Get-FileHash .\AmpSpread-v9.7.4-Windows-x64.zip -Algorithm SHA256
```

Compare the result with the release's checksum file. The EXE checksum is also
listed inside the ZIP. A matching checksum detects changes against the
published copy; it is not a publisher identity certificate or a safety verdict.

## Windows security messages

v9.7.4 is not Authenticode-signed. SmartScreen reputation warnings and Smart App
Control blocks are different Windows protections. A legitimate unsigned app
can still be blocked, and a GitHub download does not bypass either system.
If Windows blocks this build, it may not be usable under your current policy.
There is no compatibility setting in AmpSpread that makes it trusted.

When reporting a block, include the exact message, AmpSpread version and Windows
version. Remove personal details from screenshots. Do not post security
credentials or private diagnostic data in a public issue.

Microsoft explains [SmartScreen reputation](https://learn.microsoft.com/en-us/windows/apps/package-and-deploy/smartscreen-reputation)
and [Smart App Control](https://learn.microsoft.com/en-us/windows/apps/develop/smart-app-control/overview).

## Troubleshooting a reading

- Check the PSU's USB telemetry connection and the supported model list.
- If another program reads the same PSU, try running the monitoring programs
  separately to isolate a possible access conflict. Concurrent access is not validated.
- GPU connector watts exclude PCIe slot power, so they need not match a GPU
  vendor's total board-power reading.
- A saved replay's selected sample may be below 0.850 A; the history cutoff
  applies to the event's peak, not every captured sample.
- Native Windows and hardware validation remain necessary. Removing direct
  NVIDIA telemetry has not established the cause of earlier display flicker.
