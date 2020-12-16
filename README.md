# hackintosh-deskmini-310-i7-8700-CLOVER

## Hardware
- Case & Mobo: ASRock DeskMini H310
- CPU: Intel i7-8700 w/ Noctua NH-L9i
- RAM: Corsair Memory Kit 16GB DDR4 2400MHz SODIMM Memory (CMSX16GX4M2A2400C16)
- SSD: Samsung 860 EVO 500 GB
- WiFi & Bluetooth: BCM94360CS2 w/ M.2 adapter
- Display: BenQ GW2480 (DisplayPort, VESA)

## BIOS
- v3.10: works out-of-box
- Beyond v3.10: DSDT patch required

    ```
    comment: Fix RTC _STA bug (fix asrock new bios failed to boot)
    Find: A00A9353 54415301
    Replace: A00A910A FF0BFFFF
    ```
- Settings
    1. Load UEFI Defaults
    2. Advanced
        - Onboard HD Audio: Enabled
        - USB Configuration - XHCI Hand-off: Enabled
        - Super IO Configuraton - Serial Port: Disabled

## Display Connectors
- *DisplayPort*: best option, has signal since start of boot and should work out-of-box; if your display only has HDMI connectors, a DisplayPort to HDMI cable is recommended, note that the only confirmed-working model so far is DP111 by Ugreen (PS176 chipset).
- *HDMI*: can get working with extra hack for multi-screen setup; only receives signal after entring MacOS.

## Clover
Version: v5112

## config.plist
- ACPI
    - DSDT
        - Change SAT0 to SATA
        - Change EHC1 to EH01
        - Change EHC2 to EH02
        - Change XHCI to XHC
    - SDST
        - Generate
            - PluginType: True

                > This enables native power management.

    - DropTables
        - DMAR

            > This prevents some issues with Vt-d; which is PCI passthrough for VMs, and not very functional (if at all?) on Hackintoshes. ([Reference](https://hackintosh.gitbook.io/-r-hackintosh-vanilla-desktop-guide/config.plist-per-hardware/coffee-lake#drop-tables))

        - MATS

            > With High Sierra on up this table is parsed, and can sometimes contain unprintable characters that can lead to a kernel panic. ([Reference](https://hackintosh.gitbook.io/-r-hackintosh-vanilla-desktop-guide/config.plist-per-hardware/coffee-lake#drop-tables))

- Boot
    - Arguments
        - `slide=0`

            > This works hand in hand with `EmuVariableUEFI-64.efi` to solve sleep/wake-up issue. Remember to `Install RC Scripts to Target Volume` when installing Clover, otherwise your Clover boot screen might have trouble counting down automatically. ([Reference](https://www.tonymacx86.com/threads/success-ongoing-status-of-designare-z390-with-i7-9700k.266065/))

        - `darkwake=0`

            > This enables wake from sleep with one keystroke. ([Reference](https://www.tonymacx86.com/threads/success-ongoing-status-of-designare-z390-with-i7-9700k.266065/))

        - `dart=0`

            > This is just an extra layer of protection against Vt-d issues. ([Reference](https://hackintosh.gitbook.io/-r-hackintosh-vanilla-desktop-guide/config.plist-per-hardware/coffee-lake#arguments))

        - `shikigva=40`

            > This flag is specific to the iGPU. It enables a few Shiki settings that do the following (found here):

            > 8 - AddExecutableWhitelist - ensures that processes in the whitelist are patched.

            > 32 - ReplaceBoardID - replaces board-id used by AppleGVA by a different board-id.

            > ([Reference](https://hackintosh.gitbook.io/-r-hackintosh-vanilla-desktop-guide/config.plist-per-hardware/coffee-lake#arguments))
- Devices
    - Audio
        - Inject: 3
    - Properties
        - PciRoot(0x0)/Pci(0x1f,0x3)
            - device-id: cKEAAA==
            - layout-id: HAAAAA==
        - PciRoot(0x0)/Pci(0x2,0x0)
            
            > Enable HDMI output and resolve other iGPU issues.

            - AAPL,ig-platform-id: BwCbPg==
            - device-id: mz4AAA==
            - enable-hdmi20: AQAAAA==
            - framebuffer-con0-enable: AQAAAA==
            - framebuffer-con0-pipe: EgAAAA==
            - framebuffer-con1-busid: AgAAAA==
            - framebuffer-con1-enable: AQAAAA==
            - framebuffer-con1-pipe: EgAAAA==
            - framebuffer-con1-type: AAgAAA==
            - framebuffer-con2-enable: AQAAAA==
            - framebuffer-con2-index: /////w==
            - framebuffer-fbmem: AACQAA==
            - framebuffer-patch-enable: AQAAAA==
            - framebuffer-portcount: AgAAAA==
            - framebuffer-unifiedmem: AAAAgA==

- GUI
    - Hide
        - Preboot
        - VM
    - Scan
        - Entries: True
        - Tool: True
- KernelAndKextPatches
    - KernelPm: True
    - KextsToPatch
        - External icons patch
- RtVariables
    - MLB: *REPLACE WITH YOUR OWN VALUE.*
- SMBIOS
    - BoardSerialNumber: *REPLACE WITH YOUR OWN VALUE.*
    - SerialNumber: *REPLACE WITH YOUR OWN VALUE.*
    - SmUUID: *REPLACE WITH YOUR OWN VALUE.*
- SystemParameters
    - InjectKexts: Yes
    - InjectSystemID: True

## kexts

- Other

> All of the following kexts are available on this [repo](https://1drv.ms/f/s!AiP7m5LaOED-m-J8-MLJGnOgAqnjGw) courtesy of Goldfish64. Each kext is auto-built whenever a new commit is made. ([Reference](https://hackintosh.gitbook.io/-r-hackintosh-vanilla-desktop-guide/gathering-kexts))

Kext | Version | Purpose
---- | ------- | -------
AppleALC.kext | 1.5.1 | Driver for Realtek ALC233
IntelMausiEthernet.kext | 2.5.0d14 | Driver for Intel® Gigabit I219V
Lilu.kext | 1.4.5 | Fundation kext for many other kexts
WhateverGreen.kext | 1.4.0 | A composite kext that addresses graphics related issues, requires `Lilu.kext`
VirtualSMC.kext | 1.1.4 | SMC emulator, vital to booting hackintosh
SMCProcessor.kext | 1.1.4 | Companion kext for `VirtualSMC.kext`
SMCSuperIO.kext | 1.1.4 | Companion kext for `VirtualSMC.kext`
USBPorts.kext | - | Customized kext to work around USB port limit

## drivers/UEFI

> Install via `Clover` or `Clover Configurator`

Driver | Purpose
------ | -------
ApfsDriverLoader.efi | Allows Clover to see and boot from APFS volumes by loading apfs.efi from ApfsContainer located on block device (if using AptioMemoryFix as well, requires R21 or newer) ([Reference](https://hackintosh.gitbook.io/-r-hackintosh-vanilla-desktop-guide/clover-setup#installing-clover))
HFSPlus.efi | Required for Clover to see and boot HFS+ volumes ([Reference](https://hackintosh.gitbook.io/-r-hackintosh-vanilla-desktop-guide/clover-setup#installing-clover))
AptioMemoryFix.efi | The new hotness that includes NVRAM fixes, as well as better memory management ([Reference](https://hackintosh.gitbook.io/-r-hackintosh-vanilla-desktop-guide/clover-setup#installing-clover))
EmuVariableUefi.efi | Enable nvram on mobos without native support
FSInject.efi | Responsible for Clover's kext injection into kernelcache
OsxFatBinaryDrv.efi | Responsible to override for fat binary loading on UEFI where fat binary support is not present
CsmVideoDxe.efi | Responsible to unlock VBIOS and make available "hidden" resolutions while UEFI booting
