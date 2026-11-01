# PSU firmware alarm reader verification — v9.8.65

"Firmware unknown" refers to the availability of the PSU's alarm status.
It does not mean an unknown firmware version, a failed firmware update or a
confirmed PSU fault.

## Source mapping

The original MSI PSU plugin source supplied with this project contains
CMSIPSU::Get12VHPWR1FirmwareAlarm and CMSIPSU::Get12VHPWR2FirmwareAlarm.
Both call GetOCPState, using indices 0 and 21 respectively. GetOCPState reads
the payload after the two command bytes. The plugin's HIDAPI receive buffer
omits the report ID; AmpSpread's normalized receive buffer includes it.

| Field | MSI OCP payload position (1-based) | AmpSpread packet offset (0-based) |
| --- | --- | --- |
| Port 1 firmware alarm status | 1 | 3 |
| Port 2 firmware alarm status | 22 | 24 |

Both are read using the existing 0x51/0xC1 status query. AmpSpread now requires
the command echo and at least 25 normalized packet bytes, so both status bytes
must actually be present. Previously the parser could substitute zero for an
absent second status byte. Incomplete replies are now unavailable, including
for the fan controller, which checks both connectors.

## Status interpretation

The supplied source documents these values for both connectors:

| Code | Meaning |
| --- | --- |
| 0 | Normal, or recovered according to the PSU's own recovery rule |
| 1 | Overcurrent point warning |
| 2 | Current difference warning |
| 3 | Instant overcurrent warning |

The source treats any nonzero value as an active firmware alarm. AmpSpread
retains that behavior: an unfamiliar nonzero code raises an alarm and displays
its numeric value as unknown, rather than being treated as clear. The source
does not establish the exact current thresholds and delays for codes 1 and 2.

The selected port's state feeds the Firmware alarm row, incident capture, JSON
and the existing Firmware State CSV column. Firmware State 2 retains the raw
port 2 state for compatibility; when port 2 is selected those two recorded
values are the same. Firmware Valid means a complete status reply was received.
The raw transport snapshot retains both connector states for the existing fan
controller. A warning on the other connector cannot be mislabeled as the
selected connector's warning; the fan controller still checks both.

The Safeguard card describes only AmpSpread's software per-pin/gap limits and
the Test alarm. Firmware status is displayed separately; a missing C1 reply
does not suppress software limit detection or imply a normal firmware state.

## Evidence and limits

- The supplied MSIPSU.cpp and MSIPSU.h source files establish the protocol
  mapping. Their complete source is not redistributed in this package.
- Twenty user-captured E0/C1 pairs, retained in the private source test fixtures,
  identify port 2 currents and report status zero for both connectors.
- Simulated packets test all combinations of codes 0, 1, 2, 3 and an unfamiliar
  positive code across both ports. These are software tests, not PSU captures.
- Further tests reject truncated or unrelated replies, preserve unavailable
  status after a failed read, keep an incident open until a valid clear reply,
  and retain selected-port warnings through JSON and CSV analysis.
- No physical firmware alarm transition or electrical protection trip has
  been tested. Normal captured status is not proof of protection behavior.

Earlier 9.8.62/9.8.63 builds deliberately marked port 2 unverified because
normal-only captures could not establish the mapping independently. Checking
the original source resolves that mapping uncertainty; a physical fault test
is not required to choose the documented status field. Old recordings are not
rewritten or retroactively labeled verified.

**Test alarm** injects a software test sample and exercises AmpSpread's capture
path. It cannot change the PSU's C1 reply or verify a physical protection trip.
Do not deliberately overload or disturb a connector to test this reader.
