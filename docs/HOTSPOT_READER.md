# Internal NVIDIA hotspot protocol

Independently implemented Go reader; no proprietary sensor engine or copied monitor code is linked. Protocol facts were checked against the supplied HWiNFO 8.53-6080 EXE (SHA256 8bd5c9a56f5e368394c08f5ab1068b54bedb080377798bf0077ac73aebd32761) and LACT's published NVIDIA register ABI.

- Interface: NvAPI_GPU_RegisterOp, query ID 0x2EB3C140.
- Request: version 0x11808; count 6; capacity 256 operations; 6152-byte C-compatible structure.
- Operation: 24 bytes; uint16 flags/status; uint32 offset; uint64 write mask/value.
- Flags: 0x15 = read, 32-bit, global; write mask always zero.
- Fixed offsets: AD0A90, AD0A94, AD0A98, AD0A9C, AD0AA0, AD0AA4.
- Fresh result: all six operation calls must succeed; only valid positive channels enter the maximum.
- HWiNFO interpretation: validity bit 30; sign bit 16; ((raw >> 3) & 0x1FFF) / 32.0; highest valid channel.

The register method runs only for matched RTX 50 GPU handles. No arbitrary offset/configuration is accepted from UI, files or helper input. A refused request stays unavailable and its code is displayed. The API's ability to perform other operations does not expand the requests submitted by AmpSpread.

Sources: https://github.com/ilya-zlobintsev/LACT/blob/master/lact-daemon/src/server/gpu_controller/nvidia/nvapi.rs ; https://github.com/namazso/PawnIO.Modules/blob/52a7e536dff3e53c96917a28caac5e0fa6510696/Nvidia.p ; https://www.igorslab.de/en/blackwell-hotspot-ibhe-estimation-register-findings-download-nvidia-question/2/ . The API is undocumented and native hardware verification is pending.
