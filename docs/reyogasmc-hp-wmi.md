# ReYogaSMC HP fan RPM telemetry

The public EFI includes a locally built ReYogaSMC kernel extension with an HP WMI fan-RPM read path. On the reference HP 250 G10, `smcread -s` reported `FNum = 01` and `F0Ac = 3398` (`fpe2`), which decodes to 3302 RPM. This confirms read-only RPM telemetry on the tested boot. It does not provide fan-speed control or a custom fan curve.

The source is based on [hoaug-tran/ReYogaSMC](https://github.com/hoaug-tran/ReYogaSMC) at upstream commit `61ba0f8179c9aa6f8b0915d4af0ed81dbfa3797c`. The HP changes are in [`reyogasmc-hp-wmi.patch`](reyogasmc-hp-wmi.patch), and the upstream GPL-2.0 license is included in [`licenses/ReYogaSMC-GPL-2.0.txt`](../licenses/ReYogaSMC-GPL-2.0.txt). Apply the patch to that exact source revision to reproduce the HP-specific changes.

On OCTEST, ReYogaSMC is enabled and SMCSuperIO is disabled to avoid competing fan providers. SMCProcessor remains enabled; CPU sensor readings still need separate confirmation.
