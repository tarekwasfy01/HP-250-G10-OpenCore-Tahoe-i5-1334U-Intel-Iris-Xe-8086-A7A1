# HP 250 G10 — macOS Tahoe, accelerated Iris Xe, HDMI and internal microphone

Updated **22 November 2026**. On this HP 250 G10 with a Core i5-1334U, the owner confirmed a correctly colored desktop on an ASUS VX228 connected to the built-in HDMI port at **1920 × 1080, 60 Hz**. The internal display stayed usable. The test used OCTEST, the custom NootedGreen v32 build and the matching Tahoe 25G332 graphics stack. The owner also confirmed audible capture from the built-in microphone on 15 November.

Generate your own SMBIOS identity before booting. Follow the graphics installation section for the macOS-side dependencies.

## Hardware and status

| Component | Reference hardware |
| --- | --- |
| Laptop / board / BIOS | HP 250 15.6-inch G10 / HP 8B34 / F.26 |
| CPU | Core i5-1334U, Raptor Lake-U, 10 cores / 12 threads |
| Graphics / panel | Iris Xe 8086:A7A1 rev 04, 80 EUs / 1920 × 1080 internal panel |
| Audio | Intel 8086:51CA, subsystem 103C:8B34 / Realtek ALC236 |
| Internal microphone | Two digital microphones through Intel SOF/SST |
| Wireless | RTL8852BE 10EC:B85B / Bluetooth USB 0BDA:B85D |
| Software | Tahoe 26.7.2 **25G332**, OpenCore **1.0.8**, MacBookPro16,2 |

| Feature | Result on this laptop |
| --- | --- |
| Desktop startup | Working, owner confirmed with current OCTEST |
| Iris Xe acceleration | Working; Metal Supported and 1536 MB dynamic VRAM observed |
| Internal microphone | **Working, audible capture owner-confirmed on 15 November** |
| Speakers / audio output | Working, owner confirmed |
| Wi-Fi | Working, owner confirmed |
| USB-C Ethernet (WCH `1a86:5394`) | Verified after unplugging and reconnecting the adapter after login. The Ethernet interface negotiated 100BASE-TX full duplex, received a DHCP lease and reached the router with Wi-Fi still active. |
| Bluetooth | Discovery and Samsung Galaxy S21 pairing confirmed |
| Battery percentage | Working, owner confirmed |
| Internal panel brightness and F2/F3 | Working. The owner confirmed a visibly brighter panel at maximum after PWM calibration. |
| CPU Turbo Boost | 4600-MHz P-state previously observed |
| Keyboard / touchpad | Included in working setup; not exhaustively qualified |
| Camera | Working, owner confirmed |
| Hardware cursor | Rendering corruption remains; separate software arrow used |
| System sleep / lid resume | **Broken / unreliable** with this graphics stack |
| **External display via built-in HDMI** | **✓ Confirmed working:** ASUS VX228, 1920 × 1080 at 60 Hz, RGB 8-bit SDR; internal panel remained usable |
| USB-C display output, HDMI audio, other external modes, Bluetooth audio, long recording and mic sleep/wake | Not qualified |

A separate local sleep workaround keeps the laptop running when closed. It is
not installed by copying this EFI. Another panel, BIOS or macOS build needs its
own validation; these results are not a guarantee for every HP 250 variant.

### Using the built-in HDMI port

Use the included `EFI/OC/Kexts/NootedGreen.kext` together with the HDMI boot arguments in the public `EFI/OC/config.plist`. Keep your own SMBIOS values when updating an existing installation. Boot with the HDMI cable disconnected, wait for the desktop, then plug the monitor directly into the laptop. This is the sequence tested on the reference machine.

The HP build requests power for display pipe B and selects the legacy gamma mode for the external output. The preceding v31 build already produced a desktop, but gradients appeared as rainbow bands and a white window appeared black. To check the running build, run:

```sh
ioreg -lw0 | grep -E 'HPHDMITrialBuild|HPHDMIGammaB'
```

The tested build reports `hp-hdmi-v32-pipe-b-legacy-gamma`. The gamma property is published after the external modeset. Booting with HDMI attached and resuming an external display after sleep remain unverified.

## Included files

- [`EFI/`](EFI): sanitized current EFI, custom NootedGreen + WhateverGreen,
  audio output, Wi-Fi, Bluetooth, battery, brightness and **enabled HP250Dmic.kext**.
- [**HP250Dmic-HAL-0.1.0-25G332.pkg**](packages/HP250Dmic-HAL-0.1.0-25G332.pkg):
  actual microphone CoreAudio plug-in installer, with license notices.
- [`microphone/`](microphone/README.md): microphone kernel/HAL source, build
  script, Intel SOF firmware, pipeline definition and licenses.
- [`installer/`](installer/README.md): builder for a **private full graphics
  package from an existing matching local graphics installation**.
- [Graphics Installer Tools.pkg](packages/HP250-Graphics-Installer-Tools-20261114.pkg):
  graphics builder and documentation only, **not the graphics payload**.
- [`docs/`](docs): hardware/component inventory and graphics source patches.
- [`SHA256SUMS`](SHA256SUMS): public release file hashes.

No private serials, pairing state, raw hardware dumps, audio recordings, kernel
collections or Apple-origin system graphics packages are published.

## Installation

### 1. Preserve a recovery boot route

Back up your working EFI and system modifications. Test from a separate FAT32
boot device/test EFI before replacing the normal boot volume. Do not delete
Windows boot files or another OS's EFI. These public packages do not repartition
or clear a disk, and do not automatically install the EFI.

Compare your hardware IDs with the reference above. The microphone installer
checks **8086:51CA / 103C:8B34** and **25G332**. Do not bypass those checks for a
different laptop or macOS build. Python 3 is required by the installer tools;
the microphone source build additionally requires Xcode command-line tools.

### 2. Prepare the matching graphics system stack

This EFI uses **custom NootedGreen + WhateverGreen, not WhateverXe**. It requires
matching TGL graphics/framebuffer, HookCase, GPUBundles and shader compiler/LLVM
bridge files inside macOS. Copying an EFI does not install them.

See [graphics installation and recovery](installer/README.md),
[source lineage/build metadata](docs/nootedgreen-build.json), and the companion
[HP 250 G10 NootedGreen fork for Iris Xe A7A1](https://github.com/tarekwasfy01/NootedGreen-for-Intel-Iris-Xe-on-Raptor-Lake-U-8086-A7A1-)
([v32 tested release](https://github.com/tarekwasfy01/NootedGreen-for-Intel-Iris-Xe-on-Raptor-Lake-U-8086-A7A1-/releases/tag/hp-a7a1-v32)). The public tools
package installs only the builder. On an installation already containing the
exact verified graphics source files, create a private full package:

```sh
python3 installer/build_local.py --source-root / --output "$HOME/Desktop/HP250-NootedGreen-SystemStack-25G332.pkg"
```

If installation of that locally generated package is needed on a matching system:

```sh
sudo installer -pkg "$HOME/Desktop/HP250-NootedGreen-SystemStack-25G332.pkg" -target /
```

Follow its backup/check instructions. **Do not reinstall graphics on the working
laptop merely to add the microphone.** The public repository does not provide a
self-contained fresh graphics installation: required Apple-origin system files
are deliberately not redistributed. `/Library` graphics changes affect every
EFI booting that macOS, so keep a paired system/EFI backup.

### 3. Personalize and validate the EFI

Generate a consistent **MacBookPro16,2** identity using
[GenSMBIOS](https://github.com/corpnewt/GenSMBIOS). In your private copy of
`EFI/OC/config.plist`, fill `PlatformInfo → Generic → SystemSerialNumber`, `MLB`,
`SystemUUID` and the six-byte `ROM`. The public template has empty serial/MLB,
zero UUID and zero ROM. **Never commit your personalized config.**

Check the unmodified public checkout:

```sh
python3 tools/validate_public.py
shasum -a 256 -c SHA256SUMS
```

Use OpenCore **1.0.8 ocvalidate** on your personalized EFI. The repository checker
intentionally rejects private nonblank identities, and the config checksum
changes after personalization. Firmware Secure Boot and OpenCore SecureBootModel
are separate settings; inspect the reference configuration and your firmware.

Copy the complete `EFI` directory to the root of the test boot volume, retaining
`EFI/BOOT/BOOTx64.efi` and `EFI/OC/OpenCore.efi`.

### 4. Install the microphone CoreAudio plug-in

Both pieces are required:

| Piece | Location | Role |
| --- | --- | --- |
| HP250Dmic.kext | `EFI/OC/Kexts`, enabled under `Kernel → Add` | SOF firmware and digital microphone capture |
| HP250Dmic.driver | `/Library/Audio/Plug-Ins/HAL` | Normal macOS recording input |

From the repository root:

```sh
sudo installer -pkg packages/HP250Dmic-HAL-0.1.0-25G332.pkg -target /
```

The package is **unsigned and not notarized**; its plug-in is ad-hoc signed.
It checks the tested HP/controller/build and backs up an existing HP250Dmic
plug-in before replacement. It installs only the HAL plug-in, including licenses.
It does not modify EFI, kernel collections, existing audio/graphics drivers,
default input or microphone permissions. It performs no recording and no automatic
reboot/CoreAudio restart.

Do not also install HP250Dmic.kext into `/Library/Extensions` when using EFI
injection. An older experiment already embedded in a kernel collection requires
a checked migration, not blindly deleting system files or rebuilding all collections.

### 5. Boot and test

On HP use **F9 → test boot device**, or Boot from EFI File →
`EFI/OC/OpenCore.efi`. After reaching the desktop, wait at least **30 seconds**
and reopen the recording app. The driver stays enabled but hardware initialization
is deferred until recording, avoiding early controller/power-tree interactions.

Select **HP 250 G10 Internal Microphone** under System Settings → Sound → Input.
Allow microphone access for the app under Privacy & Security. Speak, check the
level, record a short sample and listen to it. A visible input alone is not proof
of capture. Check speakers, graphics, wireless, battery and brightness before
promoting a test EFI to the normal boot volume.

## How the microphone works

The built-in digital mic uses the **Intel SOF DSP path**, rather than the silent
ALC236 analog input exposed by the previous AppleALC layout. AppleALC + SimpleHDA
remain responsible for the working analog output; HP250Dmic does not replace them.

The custom build targets the exact HP controller/subsystem, uses production-signed
Intel IPC3 `sof-rpl.ri`, and creates a two-channel **48-kHz S32** DMIC → buffer →
host pipeline. A 64-KiB ring is converted to stereo Float32 for CoreAudio. It uses
spare stream descriptors, polled IPC and only host DSP core 0. Generic SOF replies
and command-specific replies are validated separately. Startup performs no BAR
mapping/register polling or AppleHDA power-tree registration.

The working PDM0 stereo profile now has owner-confirmed audible capture. Other HP
wiring, beamforming, long recording stability and sleep/wake are not qualified.
See [source/build details](microphone/README.md).

Read status without recording:

```sh
ioreg -r -c HP250Dmic -l -w0
kmutil showloaded | grep HP250Dmic
```

During the successful test: `BootResult = OK FW_READY`, all nine setup IPC messages
accepted, `Streaming = Yes`, `DMAUnsafe = No`. Before the first recording request,
`Ready = No` is expected because initialization is lazy. `StartDeferred = Yes`
early after startup means the 30-second delay has not expired.

If capture aborts, check the matching EFI/HAL snapshot, actual boot EFI, selected
input, startup delay and app permissions. The old silent analog input is a separate
device. Do not change unrelated graphics files or add arbitrary audio layout IDs
to hide a DSP error. If a test EFI hangs, use the separate known-good boot route
and restore its private backup. Disabling only HP250Dmic's entry is a recovery
option; it does not repair the microphone.

## Other drivers and remaining limits

- **Graphics:** custom HP NootedGreen profile: 80 EUs, 10 native subslices,
  six L3 banks, 1250-MHz parameter and fingerprint-checked **60-MiB DVMT**
  correction. [HP patch](docs/nootedgreen-hp.patch).
- **Output:** AppleALC 1.9.8 + SimpleHDA 1.0.1, layout 55; no VoodooHDA.
- **USB-C Ethernet:** The bundled codeless `HP250USBECM.kext` matches configuration 2, interface 1 to Apple’s `AppleUSBECMData`. If Ethernet is missing after booting with the hub connected, unplug and reconnect the USB-C hub after login.
- **Bluetooth:** RealtekBluetoothFirmware 1.0.2 with measured USB match;
  BlueToolFixup 2.7.2. Bluetooth audio/reconnection remain unqualified.
- **Battery:** VirtualSMC/SMCBatteryManager 1.3.9 + ECEnabler 1.0.6.
- **Internal panel brightness:** F2/F3 use BrightnessKeys 1.0.3. The bundled NootedGreen v32-based PWM build uses `ngreenblmax=17741` to scale the brightness request to the firmware PWM period. Before calibration, maximum brightness on this HP measured 17,741 duty counts against a period of 96,000. The owner confirmed the improvement visually. This value is specific to the tested panel. If you already use the matching graphics stack, this change only requires the updated EFI.
- **Cursor:** [optional NootedGreen trial](docs/nootedgreen-cursor-trial.patch)
  does not establish correct cursor rendering; a separate software arrow is used.
- **Sleep:** lid resume remains unreliable. The local display-only workaround is
  not included in the EFI or microphone installer and keeps the laptop running.

## Backup, privacy and licensing

Keep a **private paired recovery copy** of your personalized EFI, matching graphics
payload, HAL and applicable kernel collections. GitHub is a sanitized source/binary
reference, not a complete personal recovery image or macOS installer. EFI backups
alone cannot undo modifications in `/Library`.

Serial, MLB, SystemUUID, ROM and saved Bluetooth/boot state are removed from the
public export. Private local recovery copies retain identity and stay outside Git.
Read [third-party notices](THIRD_PARTY_NOTICES.md), `licenses/` and
`microphone/licenses/`. HP250Dmic derives from
[TGLDmic](https://github.com/labrouss/TGLDmic) and the minimal stereo approach in
[LatSOFAudio](https://github.com/shubhambanekar/LatSOFAudio); BSD-3-Clause notices
are retained. Intel firmware has its separate Intel license. NootedGreen has
separate license restrictions. The root MIT license does not relicense third-party
code or binaries. This project is unaffiliated with Apple, HP and Intel.
