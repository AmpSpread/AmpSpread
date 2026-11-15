# v9.8.77 execution change

The original fixed read-only query documented below now runs automatically on a
worker inside AmpSpread. There is no GPU helper, admin-reader action or GPU UAC
request. Previous helper/elevation references below describe historical builds.
Driver permissions still apply; normal-token hotspot is not guaranteed.

# Restored RTX 50 hotspot reader — v9.8.75

The old reader is enabled automatically for RTX 50 cards again. The v9.8.74
experimental setting is ignored on migration and no longer saved. This reader's
valid sub-degree result is preferred; documented NVML hottest-die is a fallback.

NvAPI_GPU_RegisterOp (interface 0x2EB3C140) is resolved only for RTX 50. Each
request contains six fixed 32-bit global reads at 0xAD0A90 through 0xAD0AA4,
flags 0x15, and zero write masks. No caller-selected address or operation enters
the driver. The underlying API can write registers; this fixed request does not.

Structure version/count, flags, offsets, zero write masks, per-operation status
and 32-bit width are validated. Partial transactions never produce a maximum.
The decoder retains valid bit 30, sign bit 16, bits 3..15 scaled by 1/32 degree C,
a plausible range and the highest valid channel. That is the old protocol
interpretation, not a new hardware calibration claim.

Temporary API failures and correctly shaped incomplete/empty readings retry at
30 seconds. Unsupported/version failures wait for a reader restart. Unexpected
structure/register metadata fails closed until a manual retry. GPU tools > Retry
reopens normal access; the separate admin GPU reader action can explicitly retry
with UAC approval for only the child process. Generic errors do not prove that
elevation helps, and a driver can reject access at either privilege level.

Native calls remain isolated in the timed helper. The API is undocumented and
future driver compatibility is unproven. No new hardware support is inferred
from compilation. No HWiNFO/PawnIO/LHM backend, service, kernel driver or generic
register interface is distributed. Windows/RTX 5090 testing is still needed.
