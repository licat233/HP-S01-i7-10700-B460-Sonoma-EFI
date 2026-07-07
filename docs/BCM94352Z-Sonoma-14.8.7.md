# BCM94352Z / DW1560 on macOS Sonoma 14.8.7

This note documents the tested path that finally made BCM94352Z work on the HP S01 i7-10700 B460 Hackintosh.

## Tested Result

- macOS Sonoma 14.8.7, build 23J520
- OpenCore Legacy Patcher 2.4.1
- BCM94352Z / DW1560 Wi-Fi works
- Broadcom Bluetooth works
- Bluetooth headset audio is smooth
- USB flash drives work
- Mechanical HDD mounts normally

## Key Discovery

On Sonoma, BCM94352Z does not work by only adding old Broadcom kexts. Apple removed the legacy Broadcom Wi-Fi stack, so the working solution needs both:

- EFI-side kext injection
- OCLP `Modern Wireless` root patch

Before OCLP root patching, the Wi-Fi switch could turn on and the firmware appeared in System Information, but no hotspots were found. After OCLP `Modern Wireless` root patching, Wi-Fi scanned and connected normally.

## EFI Kexts

Wi-Fi / Sonoma legacy wireless stack:

- `IOSkywalkFamily.kext`
- `IO80211FamilyLegacy.kext`
- `IO80211FamilyLegacy.kext/Contents/PlugIns/AirPortBrcmNIC.kext`
- `AirportBrcmFixup.kext`
- `AirportBrcmFixup.kext/Contents/PlugIns/AirPortBrcmNIC_Injector.kext`

Bluetooth:

- `BlueToolFixup.kext`
- `BrcmFirmwareData.kext`
- `BrcmPatchRAM3.kext`

Do not load:

- `BrcmBluetoothInjector.kext`
- `AMFIPass.kext`
- `AirPortBrcm4360_Injector.kext`

## Kernel Block

`Kernel -> Block` contains:

```text
Identifier = com.apple.iokit.IOSkywalkFamily
Strategy   = Exclude
Enabled    = True
MinKernel  = 23.0.0
```

## NVRAM

Working boot arguments:

```text
alcid=11 darkwake=0 igfxonln=1 amfi=0x80 revpatch=sbvmm ipc_control_port_options=0
```

Bluetooth NVRAM data:

```text
bluetoothExternalDongleFailed = 00
bluetoothInternalControllerInfo = 0000000000000000000000000000
```

SIP / security settings:

```text
csr-active-config = 03080000
SecureBootModel = Disabled
```

## USB Map Note

BCM94352Z Bluetooth is a USB device. USB mapping was the difference between unstable and stable Bluetooth.

The stable result used the existing working `USBMap.kext`, then changed the Bluetooth HS port to internal:

```text
HS07 -> UsbConnector = 255
```

A newly generated `UTBMap.kext` made Bluetooth work but broke external USB ports on this machine, so it was not used for the final EFI.

## OCLP Root Patch

After EFI changes and booting macOS:

```text
OpenCore Legacy Patcher
Post-Install Root Patch
Networking: Modern Wireless
Start Root Patching
Reboot
```

If OCLP reports a system version mismatch, finish any pending macOS update first. In this test, Sonoma 14.8.5 had a pending 14.8.7 update, so OCLP refused to patch until the update completed.

## What Was Avoided

The final working setup did not need:

- Reset NVRAM
- `brcmfx-country=HK`
- `-lilubetaall`
- Wi-Fi DeviceProperties spoofing
- `AMFIPass.kext`

These may be useful in other builds, but they were deliberately left out here to keep the successful configuration minimal.

## Update Reminder

After any macOS update, run OCLP root patch again. Broadcom Wi-Fi may stop working until the `Modern Wireless` patch is re-applied.

