# GPU readings in v9.8.66

The GPU card retains current/minimum/maximum for board power, core temperature, memory junction and hotspot, with a small grey Cur / Min / Max heading. GPU polling targets one second, including fast query time. Overruns skip missed polls rather than queueing a catch-up burst. GPU metrics remain independent of PSU alarms and replay data.

Board power uses NVIDIA NVML. Core uses NVML or NVIDIA thermal channels; memory junction uses NVML or the existing generation-specific thermal channel. RTX 30/40 hotspot retains its native driver queries. On RTX 50 this test uses only an internal **NvAPI_GPU_RegisterOp** reader for hotspot so comparison cannot silently come from another app.

No HWiNFO sharing, PawnIO, LHM or FanControl backend is present. All driver libraries load through the restricted System32 policy. No NVIDIA, HWiNFO or PawnIO binary is redistributed. The native request submits six fixed 32-bit global reads, offsets 0xAD0A90 through 0xAD0AA4, with no write masks. Request/response structure, metadata, operation statuses, raw width, validity, sign and plausible temperature are checked. A partially failed transaction is not reported as a complete maximum.

The uploaded HWiNFO 8.53-6080 executable checks bit 30, uses sign bit 16, converts bits 3..15 at 1/32 degree per step and takes the highest valid channel. This implementation mirrors that interpretation and displayed quantization. No calibrated physical-sensor accuracy or future-driver stability is implied. Missing values remain unavailable. The source line includes **NVAPI hotspot** when successful.

This NVIDIA API is undocumented. A normal launch does not request elevation. On drivers that deny register access, a real error code appears and the existing explicit administrator launcher can be used for comparison. Nothing installs a driver or service. The sensor helper inherits the main process's token; per-helper elevation is not implemented in this test.

The existing helper process runs below normal priority, serializes requests, is bounded by the three-second poll timeout and closes with the parent. It does not mutate GPU settings. Unsupported multi-GPU configurations remain refused. Four fixed-size session aggregates retain min/max and reset through the accepted session reset. No extra rendering timer or per-sample file write is added.

See HOTSPOT_READER.md for implementation facts. The 9.8.57 release records the author's RTX 5090/driver 617.42 comparison with HWiNFO. That report does not certify every GPU, driver or firmware. Native GPU testing was not performed in the build environment.
