# Third-party components

This repository preserves the active components of one HP 250 G10 setup. It does not claim authorship of the included drivers or bootloader. The root MIT license applies only to original documentation and helper code.

| Component | Upstream project / source |
| --- | --- |
| OpenCore, OpenRuntime, ResetNvramEntry | [acidanthera/OpenCorePkg](https://github.com/acidanthera/OpenCorePkg/tree/1.0.8) |
| OpenHfsPlus | [OpenCorePkg Legacy OpenHfsPlus](https://github.com/acidanthera/OpenCorePkg/tree/1.0.8/Legacy) |
| Lilu | [acidanthera/Lilu](https://github.com/acidanthera/Lilu) |
| WhateverXe | [b00t0x/WhateverXe 0.7.0](https://github.com/b00t0x/WhateverXe/tree/0.7.0) |
| AMFIPass Mod | [b00t0x/WhateverXe-Graphics](https://github.com/b00t0x/WhateverXe-Graphics), derived from AMFIPass by Dhinak G |
| VirtualSMC | [acidanthera/VirtualSMC](https://github.com/acidanthera/VirtualSMC) |
| NVMeFix | [acidanthera/NVMeFix](https://github.com/acidanthera/NVMeFix) |
| VoodooI2C, VoodooGPIO and VoodooI2CHID | [VoodooI2C/VoodooI2C](https://github.com/VoodooI2C/VoodooI2C) |
| VoodooInput | [acidanthera/VoodooInput](https://github.com/acidanthera/VoodooInput) |
| VoodooPS2Controller | [acidanthera/VoodooPS2](https://github.com/acidanthera/VoodooPS2) |
| AirPort_RTW89, front-end and Realtek firmware | [FranciumOS-org/AirPort_RTW89](https://github.com/FranciumOS-org/AirPort_RTW89); bundled license files are retained under its Resources directory |
| IOSkywalkFamily and IO80211FamilyLegacy compatibility payloads | Apple-origin Wi-Fi components distributed for compatibility through [Dortania/OpenCore-Legacy-Patcher](https://github.com/dortania/OpenCore-Legacy-Patcher); not covered by this repository's MIT license |
| Generated HP ACPI tables | Generated from the reference laptop's firmware using [lzhoang2801/OpCore-Simplify](https://github.com/lzhoang2801/OpCore-Simplify) and OpenCore-compatible SSDT patterns |

Upstream license texts are provided in `licenses/` where available. Existing notices in bundled files remain applicable. Upstream source links and the embedded versions in `docs/snapshot.json` identify the corresponding projects; this repository is not their official distribution channel.

No macOS installer, recovery image, TGL system graphics package or full firmware DSDT is included. Apple-origin components and Realtek firmware remain subject to their respective rights and terms; the original documentation license does not grant additional rights to them.

## NootedGreen HP profile (14 November 2026)

The current snapshot replaces WhateverXe with a custom NootedGreen build and
WhateverGreen. NootedGreen source lineage: [dssoftx/Hackintosh-X1Carbon-G11-IrisXe-Tahoe](https://github.com/dssoftx/Hackintosh-X1Carbon-G11-IrisXe-Tahoe),
reviewed revisions in `docs/nootedgreen-build.json`; local changes in
`docs/nootedgreen-hp.patch`. Its upstream license is reproduced verbatim in
`licenses/NootedGreen.txt`. HookCase's MIT license is in `licenses/HookCase.txt`;
HookCase and OS-specific system graphics payloads are not included in this EFI.
WhateverGreen upstream: https://github.com/acidanthera/WhateverGreen;
license in `licenses/WhateverGreen.txt`. Existing license files for previously
included components remain as historical attribution.


## Battery/audio/Bluetooth additions (14 November 2026)

- SMCBatteryManager: VirtualSMC project, same 1.3.9 generation as the retained VirtualSMC.
- ECEnabler 1.0.6: https://github.com/averycblack/ECEnabler (Lilu plugin).
- AppleALC 1.9.8: https://github.com/acidanthera/AppleALC.
- SimpleHDA 1.0.1: https://github.com/laobamac/SimpleHDA. Its payload restores AppleHDA through EFI; included upstream notices and licenses in licenses/SimpleHDA apply. No HAL installer package is included here.
- RealtekBluetoothFirmware 1.0.2: https://github.com/thegwchr/RealtekBluetoothFirmware. Local change: one USB matching personality for 0BDA:B85D, following upstream's documented matching procedure; executable and embedded firmware unchanged. Realtek firmware retains its upstream terms.
- BlueToolFixup 2.7.2: https://github.com/acidanthera/BrcmPatchRAM. Other Broadcom firmware kexts from that archive are not included.
- Native cursor trial: local narrowly scoped extension to the documented NootedGreen source lineage; see docs/nootedgreen-cursor-trial.patch. The original NootedGreen license remains applicable.

BrightnessKeys 1.0.3: https://github.com/acidanthera/BrightnessKeys, BSD-3-Clause; handles ACPI brightness notifications.

## HP250Dmic internal microphone (15 November 2026)

HP250Dmic is a local HP 250 G10 Raptor Lake port of
[TGLDmic](https://github.com/labrouss/TGLDmic), commit
`d13dafa48fe618db743a0b6fac77ad238fdaf3a1`. The minimal two-channel pipeline is
adapted from [LatSOFAudio](https://github.com/shubhambanekar/LatSOFAudio), commit
`41fda77bd5321464fef5ba71a44437f993215d55`. Both BSD-3-Clause copyright/license
texts are retained under `microphone/licenses/` and in the kext/HAL resources.
These derivative sources retain those notices, beyond the root MIT documentation
license. Intel firmware from [sof-bin](https://github.com/thesofproject/sof-bin)
has its own Intel license, retained beside the firmware and in both bundles.

The microphone .pkg distributes only this CoreAudio HAL plug-in and its notices.
It contains no Apple-origin graphics drivers, kernel collections, private
identities or audio recordings. Capture was owner-confirmed on the reference HP;
other machines and long-term operation are not qualified.


## ReYogaSMC HP fan telemetry (23 November 2026)

The EFI includes ReYogaSMC, based on upstream commit `61ba0f8179c9aa6f8b0915d4af0ed81dbfa3797c`, with an HP WMI read-only fan RPM change. The reference laptop reported one fan at 3302 RPM. The corresponding source patch and full GPL-2.0 license are included in `docs/reyogasmc-hp-wmi.patch` and `licenses/ReYogaSMC-GPL-2.0.txt`. `SMCSuperIO.kext` is present but disabled in this EFI; `SMCProcessor.kext` remains enabled, while CPU temperature values are not yet confirmed.
