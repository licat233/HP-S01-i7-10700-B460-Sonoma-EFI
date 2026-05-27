# HP S01-2020 Hackintosh EFI

OpenCore EFI for HP S01-2020 desktop.

## Hardware

- Model: HP S01-2020 desktop
- CPU: Intel Core i7-10700
- iGPU: Intel UHD Graphics 630
- Memory: 16 GB
- Storage: 256 GB SSD
- macOS: Sonoma 14.8.5
- SMBIOS: iMac20,1

## Status

- Boots macOS Sonoma
- UHD630 graphics works
- Audio layout: `alcid=11`
- HDMI/display wake after display sleep works with `igfxonln=1`
- USB mapped with `USBToolBox.kext` + `UTBMap.kext`
- CSR8510 A10 USB Bluetooth test enabled with `BlueToolFixup.kext`
- OpenCore picker uses `Builtin`, no OpenCanopy GUI
- Debug boot arguments removed
- Current boot args: `alcid=11 darkwake=0 igfxonln=1`

## Power Notes

For daily use on this desktop, system sleep is intentionally disabled and display sleep can be used instead. The tested macOS power baseline is:

```bash
sudo pmset -a sleep 0 disksleep 0 hibernatemode 0 standby 0 powernap 0 proximitywake 0 ttyskeepawake 0
```

If display wake is unstable on your monitor or HDMI port, keep `igfxonln=1` in `boot-args`.

## Bluetooth Notes

The onboard Realtek Bluetooth device from the RTL8821CE card is disabled in the USB map. This EFI is set up to test an external CSR8510 A10 USB Bluetooth adapter with `BlueToolFixup.kext`.

After booting, check System Settings or System Report. If Bluetooth still does not appear on Sonoma, this specific CSR8510 A10 adapter is likely not compatible with modern macOS.

## Important

Before using this EFI, generate your own SMBIOS values and replace these fields in `EFI/OC/config.plist`:

- `PlatformInfo -> Generic -> SystemSerialNumber`
- `PlatformInfo -> Generic -> MLB`
- `PlatformInfo -> Generic -> SystemUUID`
- `PlatformInfo -> Generic -> ROM`

Do not use the placeholder values as-is.

## Notes

This EFI is shared as a reference for the HP S01-2020 / Comet Lake / UHD630 platform. Review BIOS settings, USB mapping, network card, storage, and SMBIOS before using it on another machine.
