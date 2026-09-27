# Install or update AmpSpread

1. Open the project's Releases page and expand **Assets**.
2. Download **AmpSpread-v9.7.7-Windows-x64.zip**. GitHub's automatic **Source code**
   ZIP contains this documentation repository, not the runnable application.
3. Extract the ZIP into a folder you can write to.
4. Close any older AmpSpread window, then run **AmpSpread.exe**.

The build is for Windows x64. No installer, NVIDIA library or bundled kernel
driver is included. Optional NVIDIA board power uses the signed driver library already installed in Windows System32. Live measurements require a compatible PSU's USB telemetry
connection. Monitoring starts automatically when the application opens.

## In-app updates (v9.7.5 onward)

Open **Settings / Tools → Check for updates**. The update screen displays the
GitHub changelog and the Windows x64 download size. Choose **Download update**;
monitoring continues while the ZIP downloads and its SHA-256 checksum is checked.

At **Update ready to install**, choose **Restart now** or **Close**. Close only
closes the update screen and keeps this version running. The staged update is
remembered across ordinary exits and can be installed later. Restart now flushes
recordings, exits normally, installs the verified EXE and opens the new version. From v9.7.7, an asynchronous hardware-free launch probe and helper readiness check run first while monitoring continues. A failed probe leaves the running app and installed EXE intact.
The updated app displays the release notes and **View release on GitHub**.

No update installs automatically on a normal exit. No GitHub login or private
access token is required. The updater changes the application EXE; it does not
replace local recordings or settings. Documentation and license files in the
extracted folder can be refreshed from the full ZIP when needed.

v9.7.4 and earlier require the manual steps above once. Future releases must
publish a stable `vMAJOR.MINOR.PATCH` tag and the matching Windows x64 ZIP as the
latest GitHub release to appear in the checker.

If the app folder is not writable, extract the app into a folder you own or use
a manual update. An update that Windows blocks must follow your existing
Windows policy. AmpSpread does not request elevation or change that policy.

## Keep your recordings

Settings and recordings are stored under `%LOCALAPPDATA%\AmpSpread`.
Updating the application does not require deleting that folder. Back up that
folder if you want an independent copy of your settings and recordings.

## Verify a download

Each release includes `SHA256SUMS.txt`. In PowerShell, run:

```powershell
Get-FileHash .\AmpSpread-v9.7.7-Windows-x64.zip -Algorithm SHA256
```

Compare the result with the release's checksum file. The EXE checksum is also
listed inside the ZIP. A matching checksum detects changes against the
published copy; it is not a publisher identity certificate or a safety verdict.

## Windows security messages

v9.7.7 is not Authenticode-signed. SmartScreen reputation warnings and Smart App
Control blocks are different Windows protections. A legitimate unsigned app
can still be blocked, and a GitHub download does not bypass either system.
If Windows blocks this build, it may not be usable under your current policy.
There is no compatibility setting in AmpSpread that makes it trusted.

If the message says **“An Application Control policy has blocked this file”**, this is a Windows launch-policy decision, not a failed network download. The updater keeps/restores the previous EXE when it can confirm launch failure. The current build remains unsigned; trusted publisher signing is the distribution fix, subject to your Windows policy. Manual extraction or downloading again does not grant trust.

The new preflight is implemented in v9.7.7. It cannot change the updater already running in v9.7.5/v9.7.6 for the first upgrade. Even a successful probe cannot guarantee a subsequent launch at a different installation path. No security policy, antivirus exclusion or elevation is changed by AmpSpread. Detailed failures go to `%LOCALAPPDATA%\AmpSpread\Updates\update_errors.log`.

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
- For the fewest GPU interactions, leave the NVIDIA option off. Opting in adds driver calls; process isolation cannot guarantee no display flicker or game impact.
- Native Windows and hardware validation remain necessary; see the validation and performance notes.
