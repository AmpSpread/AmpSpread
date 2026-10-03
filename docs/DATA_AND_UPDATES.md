# Data and update storage

In v9.8.17, Settings / Tools includes a Recordings folder field, Browse, Use AppData and Open recordings. Choose a local folder, press Save Settings, then restart AmpSpread. A blank field uses `%LOCALAPPDATA%\AmpSpread`.

Only `Alarm History`, `Sessions` and `Spread Replays` follow this choice. The active folder is fixed for each run. Existing recordings are not moved or deleted; the app searches its default and remembered previous folders. Up to 32 previous locations are retained. Network locations are not supported.

Startup creates and removes small write-test files in the recording subfolders. If the chosen folder is unavailable or unwritable, the app warns and uses AppData for that launch. If that is also unavailable, it warns that recording may fail. A drive failure during a run is reported through the existing recording-error paths; it does not silently switch active folders.

Configuration, recovery records, last-run/crash diagnostics and update staging remain in AppData. Use Open recordings for the active recording folder, or Open app data folder for operational files.

The table below describes the default layout. When a custom folder is active, the three recording subfolders and their archive error log are under that chosen root.


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
| `psu_fan_recovery.json` | Session fan recovery: hashed same-device identity and prior zero-fan preference; removed after confirmed restoration |
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

## Temporary live Top 5 replays

Starting with v9.7.6, every listed live Top 5 event has replay samples held in
app memory, including peaks below 0.850 A. This cache does not write additional
replay files. It retains at most five events, up to 600 samples per event.
Long captures retain start, peak and recent windows and identify omitted samples.

When a larger event replaces an entry, the app releases that entry's replay
sample buffer. If its replay is open, the view also clears and returns to Live.
Leaving a temporary replay clears its view copy. A reset or new session clears
the cache. Released memory becomes available to Go's memory manager for reuse;
Windows Task Manager need not show an immediate decrease in the process total.

The normal shared alarm ring and live graph still retain their own bounded
recent measurements. A replaced Top 5 replay cannot be reopened from those
buffers. Separately saved spread archives (peaks at least 0.850 A), alarm
incidents and optional session CSVs follow their existing disk retention rules.
Replacing a live Top 5 replay does not erase those saved recordings.

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

## PSU-only migration in v9.8.4

The removed NVIDIA enable key is ignored in old config.json files and omitted on the next settings save. No GPU reader, helper or NVIDIA diagnostic log is created. Old files are not deleted. NVIDIA-specific board-power fields are ignored by current replay/report code; older generic GPU fields are offline compatibility data only.

## PSU fan records

The v9.8.1 fan backend retained in v9.8.3/9.8.4 uses explicit persistent Auto/Customized/Zero Fan actions. Saved settings intentionally remain active when the app closes. Select Auto to restore automatic control. Existing session recovery uses psu_fan_recovery.json; preserve it while restoration is pending. Normal startup/monitoring does not apply a new persistent profile. See PSU_FAN_CONTROL.md for the unchanged backend limitations. v9.8.2's separate save/approved record scheme is specific to that older preview, not this release.

## Fan curves and guard in v9.8.5

Curve points are saved to %LOCALAPPDATA%\AmpSpread\psu_fan_recovery.json.curve.json on Apply Curve. They load as editable points, not an automatically activated profile. The identity-bound psu_fan_recovery.json records pending temporary-control recovery, its original target and Zero Fan state, and the strict-coordination requirement. Normal Stop/Exit restores Auto for temporary curves/overrides; abrupt crashes may leave the last target active. Preserve the recovery file until restoration succeeds. Selecting Auto arms the 55°C guard for the current monitoring session. These changes supersede the earlier description of an unchanged fan backend. See PSU_FAN_CONTROL.md for exact operation and limits.

## Peak/average metadata in v9.8.7

Live Top 5 accepts recorded peaks >0.400 A immediately. Avg uses available five-second pre-peak context and subsequent samples >=0.500 A, with seconds shown for that averaging window. See RELEASE_NOTES_v9.8.7.md for exact boundaries, gaps, and nearby-event grouping. Its sum/count are independent of replay compaction. Saved replay JSON adds average_spread, average_samples, average_start and average_end; older archives have no fabricated mean. The saved-history minimum remains 0.850 A.

## PSU statistics and temperature rules in v9.8.8

Live power/voltage shows +12V average/minimum/maximum in one row, efficiency immediately above PSU output, then output average/minimum/maximum. PSU temperature also has average/minimum/maximum. These are session aggregates from connected samples, separate from graph/replay storage; reset session clears them. Non-finite/out-of-range values are excluded; a valid zero counts.

Temperature graph points now select held static targets by threshold, with no firmware curve upload. Normal curve stop restores the prior supported setting; fault/crash recovery attempts Auto. See PSU_FAN_CONTROL.md for exact behavior and limits.
