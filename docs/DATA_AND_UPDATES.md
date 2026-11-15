## v9.8.75 GPU migration

The obsolete gpu_experimental_hotspot key is ignored and removed on the next
settings save. gpu_sensors_enabled still controls GPU monitoring. The old RTX 50
reader is enabled automatically; administrator access is an explicit session-only
helper action and is never stored in configuration. GPU-only retries preserve
session extrema and leave PSU sampling/recording active.

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
| `Alarm History\.pending-*.journal` | Bounded checkpoints for active alarms; recovered as Interrupted alarms after an interrupted run |
| `Sessions` | Full-session CSV recordings, only when enabled in Settings |
| `psu_fan_recovery.json` | Session fan recovery: hashed same-device identity and prior zero-fan preference; removed after confirmed restoration |
| `config.json` | Saved thresholds, capture/display settings |
| `Performance\\performance*.csv` | Optional five-second app resource logs; at most four 8 MiB files |
| `config.json.bak` | Previous valid settings, tried if the current file cannot be read |
| `psu_fan_recovery.json.persistent-unknown.json` | Same-device marker for an unconfirmed persistent fan save; cleared only after deliberate, acknowledged Auto recovery |
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
replay files. It retains at most five events. The per-event bound scales from 600 samples
at 1000 ms to 6000 samples at 100 ms, keeping the same time coverage.
After a live rate change the bound follows the fastest rate used since the
session started or was reset, including in capture-rate metadata.
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
are part of the updater. SHA-256 detects corruption relative to the GitHub asset.
Starting with v9.8.50, the ZIP must also contain `SIGNED_UPDATE.json`, signed by
the release owner's separate Ed25519 key and bound to the version and EXE digest.
AmpSpread verifies it before extraction and again before installation. This
build has no Windows Authenticode publisher certificate.

## PSU-only migration in v9.8.4

The removed NVIDIA enable key is ignored in old config.json files and omitted on the next settings save. The GPU reader/helper was absent in v9.8.4 through v9.8.52. Version 9.8.53 introduces a new, independent live GPU card controlled by gpu_sensors_enabled; the old nvidia_power_enabled key remains ignored. Old files are not deleted. NVIDIA-specific board-power fields are ignored by current replay/report code; older generic GPU fields are offline compatibility data only.

## PSU fan records

The v9.8.1 fan backend retained in v9.8.3/9.8.4 uses explicit persistent Auto/Customized/Zero Fan actions. Saved settings intentionally remain active when the app closes. Select Auto to restore automatic control. Existing session recovery uses psu_fan_recovery.json; preserve it while restoration is pending. Normal startup/monitoring does not apply a new persistent profile. See PSU_FAN_CONTROL.md for the unchanged backend limitations. v9.8.2's separate save/approved record scheme is specific to that older preview, not this release.

## Fan curves and guard in v9.8.5

Curve points are saved to %LOCALAPPDATA%\AmpSpread\psu_fan_recovery.json.curve.json on Apply Curve. They load as editable points, not an automatically activated profile. The identity-bound psu_fan_recovery.json records pending temporary-control recovery, its original target and Zero Fan state, and the strict-coordination requirement. Normal Stop/Exit restores Auto for temporary curves/overrides; abrupt crashes may leave the last target active. Preserve the recovery file until restoration succeeds. Selecting Auto arms the 55°C guard for the current monitoring session. These changes supersede the earlier description of an unchanged fan backend. See PSU_FAN_CONTROL.md for exact operation and limits.

## Peak/average metadata in v9.8.7

Live Top 5 accepts recorded peaks >0.400 A immediately. Avg uses available five-second pre-peak context and subsequent samples >=0.500 A, with seconds shown for that averaging window. See RELEASE_NOTES_v9.8.7.md for exact boundaries, gaps, and nearby-event grouping. Its sum/count are independent of replay compaction. Saved replay JSON adds average_spread, average_samples, average_start and average_end; older archives have no fabricated mean. The saved-history minimum remains 0.850 A.

## PSU statistics and temperature rules in v9.8.8

Live power/voltage shows +12V average/minimum/maximum in one row, efficiency immediately above PSU output, then output average/minimum/maximum. PSU temperature also has average/minimum/maximum. These are session aggregates from connected samples, separate from graph/replay storage; reset session clears them. Non-finite/out-of-range values are excluded; a valid zero counts.

Temperature graph points now select held static targets by threshold, with no firmware curve upload. Normal curve stop restores the prior supported setting; fault/crash recovery attempts Auto. See PSU_FAN_CONTROL.md for exact behavior and limits.

## 9.8.61 spread metrics

Current/AVG spread now use six-pin mean absolute deviation. MAX SPREAD, Top 5 events, existing alarms and stored JSON gap fields keep their previous highest-minus-lowest meaning. New CSVs distinguish Pin Gap A from the appended six-pin deviation column. Older input files are not rewritten. See SPREAD_CALCULATION.md.


## v9.8.62 selected PSU connector

The psu_connector setting selects port 1 or 2 and applies to a new monitoring
session after restarting the app. Each session retains its selected port
through reconnects. Current samples and spread-event metadata retain the port
in JSON; session and incident CSVs append PSU 12V-2x6 Port. A value of zero
means the old recording did not identify its port, not port 1. New analysis
rejects CSVs containing both recorded ports rather than combining their stats.
In v9.8.62/9.8.63, port 2 firmware alarm mapping was marked unverified; its
Firmware Valid column was false, distinct from a transport error. Software thresholds still
use the selected six currents and their highest-minus-lowest gap.


## v9.8.63 restored spread headlines

Current and average spread once again mean highest-minus-lowest pin gap, with
average taken across the session's per-sample gaps. The current six-pin mean
absolute deviation appears only as the small All 6 avg live-card caption.
Its explicitly named per-sample CSV column is retained for compatibility;
summary Average spread A returns to the gap definition. Existing JSON gap
meanings and the recorded PSU port are retained. No source recording is edited.

## v9.8.64 selected-port firmware alarm status

The original supplied MSI PSU plugin source confirms the two C1 status bytes.
New samples use the selected connector's status as Firmware State, and set
Firmware Valid only after a complete C1 response. Firmware State 2 retains the
raw port 2 state; it matches Firmware State when port 2 is selected. A valid
zero is normal, a nonzero value is an alarm, and a missing or incomplete reply
remains unavailable. Both raw states still reach the fan controller.
Existing CSV columns and JSON keys retain their positions/names. Earlier port 2
recordings marked invalid are not rewritten. See FIRMWARE_STATUS.md for the
protocol evidence and the limits of this verification.

## v9.8.65 deviation captions and separate safeguard display

Current, Avg and Max spread retain highest-minus-lowest headline values.
Their small grey captions now show Current deviation, Average deviation and
Max deviation across all six pins. The latter two are running aggregates of
valid per-sample mean absolute deviations since Start/Reset, independent of
the bounded graph buffer. Zero counts; non-finite deviations do not update
the aggregates. Existing CSV/JSON schemas and summary labels are unchanged.

Safeguard describes AmpSpread's own software limits and Test alarm. The PSU's
firmware alarm remains in its existing metric row and capture/recording path.
The GPU connector footer shows only its maximum wattage; selected-port settings
and recording metadata remain intact.


## v9.8.77 GPU configuration

The former gpu_device_id selection is ignored and omitted on save. NVIDIA
readings run inside the app. Performance logs contain a single main-process row
per sample. Logging remains session-only and is off at startup.

## v9.8.78 startup and GPU polling

Config stores gpu_poll_interval_ms (1000–10000, default 1000). Windows startup
is stored only in this user's Run value AmpSpread, not duplicated in config.
Save Settings adds/removes the quoted current EXE path. No launch-time registry
mutation occurs. Performance CSVs append GPUPollMS. See STARTUP_AND_GPU_POLLING.md.
