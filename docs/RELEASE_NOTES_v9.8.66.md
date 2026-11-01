Official AmpSpread 9.8.66 release for Windows x64.

Live PSU monitoring supports MSI MPG Ai1300TS PCIE5 and Ai1600TS PCIE5 through their USB telemetry connection.

### Changes
- **PSU port 2 support:** select either 12V-2x6 socket. Port 2 current readings have been confirmed by the author on an Ai1600TS.
- **Six-pin balance captions:** Current deviation, Average deviation and Max deviation appear below the main spread readings. They use mean absolute deviation across all six pins; the average and maximum cover the session since Start/Reset.
- The main Current/Avg/Max spread figures and Top 5 retain highest-minus-lowest pin-gap calculations.
- **Separate alarm displays:** Safeguard shows AmpSpread's per-pin/gap limits and Test alarm. The Firmware alarm row shows the selected PSU port's own alarm status.
- Correct port 2 firmware-status mapping, checked against the original MSI plugin source. Missing or incomplete status replies remain unavailable.
- Grey card captions use measured font height and balanced spacing below the large readings.
- GPU connector footer shows only maximum wattage. New recordings retain the selected PSU port; older recordings remain readable.

### Choose your PSU port
In **Settings / Tools**, enable **PSU 12V-2x6 port 2 (off: port 1)** for socket 2, save settings and restart. Port 1 remains the default. The active port stays fixed through a monitoring session and reconnects.

### GPU readings
Retains NVIDIA power, core, memory and hotspot readings from 9.8.57. Hotspot may require the included **Run-AmpSpread-with-Hotspot.cmd** administrator launcher, depending on the GPU/driver. No additional sensor service, kernel driver or external monitoring app is installed.

### Download and validation
Download **AmpSpread-v9.8.66-Windows-x64.zip**, close the old copy, extract the complete ZIP and run **AmpSpread.exe**. Existing users can also use **Settings / Tools → Check for updates**.

This promotes the unchanged 9.8.66 package already supplied for testing. Included build documents may still describe its pre-publication/private status.

297 automated test/subtest records passed, including race checks; two optional checks were skipped. Windows vet, GUI/test compilation, static EXE checks and publisher-update signature verification passed. The author confirms port 2 current readings; physical PSU protection trips and all hardware/driver combinations have not been tested.

SHA-256 of the Windows ZIP:
`e2cbc9765836dbd512881840fa6c11356411a4990c21a4b386a5780c02d5be04`

The package's Ed25519 update signature is separate from Windows Authenticode signing. Smart App Control may still block this unsigned EXE. GitHub's automatic source archives contain this public documentation repository.

### Support AmpSpread

If AmpSpread is useful to you, you can [support its development on Patreon](https://patreon.com/AmpSpread).
