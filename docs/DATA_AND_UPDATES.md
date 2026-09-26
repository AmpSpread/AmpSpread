# Data and update storage

Press **Win+R**, paste `%LOCALAPPDATA%\AmpSpread`, and press Enter. Normally this
is `C:\Users\<your Windows username>\AppData\Local\AmpSpread`. The AppData folder
is hidden by default; Win+R or **Settings / Tools → Open app data folder** opens
it directly. The source falls back to Windows' user config directory if the
LOCALAPPDATA environment variable is unavailable.

| Location inside AmpSpread | Contents |
|---|---|
| `Spread Replays\YYYY\YYYY-MM` | Archived qualified spread events and their captured samples |
| `Alarm History\YYYY\YYYY-MM` | Saved alarm incidents; optional companion incident CSVs |
| `Sessions` | Full-session CSV recordings, only when enabled in Settings |
| `config.json` | Saved thresholds, capture and display settings |
| `last_run.json` | Last-run/heartbeat information for recovery diagnostics |
| `crash.log`, `Spread Replays\archive_errors.log` | Error diagnostics, when an error occurs |
| `Updates\stage-*` | Downloaded executable, release notes and install plan; a helper copy is added when Restart now is chosen |
| `Updates\pending.json` | Points to the downloaded update waiting for your restart choice |
| `Updates\update_errors.log` | Update diagnostics, if an update fails |

Older versions can have recordings in `%LOCALAPPDATA%\AmpSpread\v9.5` or `v8.7`;
legacy history lookup also supports the earlier
`%LOCALAPPDATA%\HWiNFO 12V-2x6 Live Analyzer\Alarm History` folder.

“Existing lower-spread archives stay on disk” means old saved spread captures
with a peak below 0.850 A are excluded from the history/replay lists without
being erased. Starting in v9.7.4, new spread archives below that cutoff are not
written. Alarm incidents are still retained regardless of spread. Recordings
are not automatically pruned by age or this cutoff. Full-session recording is
optional and is off by default.

PNG reports and summary CSV exports go to the location selected in the Save
dialog; the suggested folder is beside the source CSV.

## Update files and backups

Updates download into the `Updates` folder while the application keeps running.
The ZIP is removed after successful verification and extraction. Failed downloads
are cleaned up when the operation returns normally; an interrupted process can
leave an incomplete staging folder. Pending downloads remain available for later.
Completed staging folders are removed on a later startup once more than 24 hours
old. Settings and recording folders are not included in that cleanup.

After installation, a uniquely named `.AmpSpread.exe.AmpSpread-stage-*.bak`
backup is retained **beside your installed EXE** (the name follows the EXE name
if you renamed it). Each installed update retains its previous EXE backup.
These backups are not automatically deleted. Keep them until you are satisfied
with the new version. Never delete or edit a staging folder while an update is
being downloaded or installed.

Only **Restart now** starts replacement. **Close** returns to the app; it does
not install the update or exit AmpSpread. A later ordinary app exit also leaves
the update staged. On a successful restart, the updater preserves the current
EXE filename so existing shortcuts continue to work.

Update checks send a normal HTTPS request to GitHub with the app version in its
User-Agent. Checks do not upload recordings, PSU measurements or configuration.
No automatic startup checks, private GitHub token, analytics or telemetry upload
are part of the updater. SHA-256 detects corruption relative to the GitHub asset;
it is not a substitute for publisher signing or protection against a compromised
publishing account. This release remains unsigned.
