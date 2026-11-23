# OpenCore test EFI — WhateverXe graphics preparation

This copy is based on the separate OCTEST partition. The original TAHOE-BOOT EFI was not modified.

## Prepared in this copy
- WhateverXe 0.7.0 and AMFIPass 1.4.1 Mod are in EFI/OC/Kexts.
- Lilu remains first; WhateverXe and the matching AMFIPass Mod follow it.
- WhateverGreen is disabled in this test config only. Its folder remains for rollback; do not enable it alongside WhateverXe.
- WEG-only framebuffer/device spoof properties were removed. Generic HDA audio association was retained.
- Test boot arguments include -allow3d -disablegfxfirmware and omit -igfxvesa.
- Test csr-active-config is 03080000 (0x803); full SIP is not supported by WhateverXe.
- WhateverXe Graphics 0.4.0 Tahoe installer is downloaded separately under ~/Downloads/WhateverXe-Test/Graphics.pkg. It has not been installed; install only after booting this test EFI.

## Still required before GPU test
1. OCTEST is currently Microsoft Basic Data, not an EFI System Partition. Change only partition 6's GPT type to EFI (EF00) with a GPT-aware tool, without formatting it or touching partitions 1–5. Confirm the partition number and disk before writing changes.
2. Reboot using OCTEST explicitly. The copied OpenCore EFI was not validated by ocvalidate in this environment.
3. Check BIOS DVMT Pre-Allocated memory. WhateverXe documentation says 96 MB needs no extra DVMT properties; if fixed at 60 MB, add its documented -igfxdvmt and stolen-memory configuration. This value is not known yet.
4. Once the test EFI boots, install the Tahoe WhateverXe Graphics package from ~/Downloads/WhateverXe-Test/Graphics.pkg and reboot. The installer changes the Tahoe CoreMedia setting and rebuilds AuxKC.

GPU ID 8086:9A49 is implemented for Tiger Lake in WhateverXe, but this exact laptop/panel configuration has not been confirmed on upstream hardware test coverage. Keep the original EFI available for recovery.
