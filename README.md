# OpenCore EFI for ASUS X543UA

Multi-boot configuration for macOS Tahoe 26 / Windows 11 / Ubuntu Linux.

## Hardware

- **Model**: ASUS VivoBook X543UA
- **CPU**: Intel Core i3-8130U (Kaby Lake-R)
- **GPU**: Intel UHD Graphics 620
- **Audio**: Realtek ALC256
- **WiFi**: Intel Wireless (itlwm)
- **Bluetooth**: Intel Bluetooth
- **SMBIOS**: MacBookPro14,3

## What Works

- ✅ macOS Tahoe 26.x boot
- ✅ Intel UHD 620 graphics acceleration
- ✅ WiFi (itlwm v2.3.0)
- ✅ Bluetooth (with Tahoe-compatible fork v2.5.0-d2)
- ✅ Trackpad (VoodooI2C + VoodooI2CHID)
- ✅ Keyboard with brightness keys
- ✅ Battery status
- ✅ USB ports
- ✅ Multi-boot (Windows/macOS/Linux via OpenCore)

## What Needs Manual Setup

### Audio (Speakers + Microphone)

**Issue**: AppleHDA.kext was removed from macOS Tahoe beta 2+. VoodooHDA is installed but has limited functionality with ALC256 (no mic, speakers unreliable).

**Solution**: Manually reinstall AppleHDA using MyKextInstaller or SimpleLoader.

#### Steps to Fix Audio:

1. **Download MyKextInstaller**:
   - Get it from: https://github.com/Mirone/MyKextInstaller
   - Or use SimpleLoader: https://github.com/perez987/AppleHDA-back-on-macOS-26-Tahoe

2. **Run the app inside macOS Tahoe**:
   - MyKextInstaller will download the KDK automatically
   - Click "Install AppleHDA.kext"
   - Reboot

3. **After reboot**:
   - Audio should work through AppleALC (layout-id 66 already configured)
   - Speakers and microphone should both work
   - You can uninstall VoodooHDA if desired

**Config already set**:
- `csr-active-config`: `03080000` ✓
- `AppleALC.kext`: Present but disabled (enable after AppleHDA install)
- `layout-id`: 66 in DeviceProperties ✓

### Ubuntu Boot Entry

**Issue**: Ubuntu wasn't appearing in OpenCore picker.

**Fix Applied**: Added `flags=0x0F` to `OpenLinuxBoot.efi` driver arguments to scan Linux root/data partitions.

**Status**: Should work after reboot. Ubuntu will boot directly without GRUB chainloading.

### Camera

**Status**: USB-based (UVC), should work natively. If not detected, check System Report → USB.

## Boot Arguments

```
debug=0x100 keepsyms=1 -amfipassbeta -igfxblt -vi2c-force-polling -ibtcompatbeta
```

- `-ibtcompatbeta`: Required for Intel Bluetooth on macOS 26
- `-igfxblt`: Intel backlight fix
- `-vi2c-force-polling`: VoodooI2C polling mode for trackpad
- `-amfipassbeta`: AMFI bypass for root patches

## NVRAM Settings

- **SIP**: `03080000` (partially disabled for AppleHDA install)
- **Bluetooth NVRAM**: `bluetoothExternalDongleFailed` and `bluetoothInternalControllerInfo` configured

## Drivers

- `OpenRuntime.efi`: OpenCore runtime
- `HfsPlus.efi`: HFS+ filesystem
- `apfs_aligned.efi`: APFS filesystem
- `ext4_x64.efi`: Linux ext4 filesystem (LoadEarly=true)
- `OpenLinuxBoot.efi`: Auto-detect Linux distros
- `ResetNvramEntry.efi`: NVRAM reset tool

## Kexts

### Essential
- `Lilu.kext` v1.7.3
- `WhateverGreen.kext` v1.7.1 (iGPU)
- `VirtualSMC.kext` v1.3.8 + sensors
- `AppleALC.kext` v1.9.8 (disabled until AppleHDA restored)

### Intel WiFi/BT
- `itlwm.kext` v2.3.0
- `IntelBluetoothFirmware.kext` v2.5.0-d2 (Tahoe fork)
- `IntelBTPatcher.kext` v2.5.0-d2 (Tahoe fork)
- `BlueToolFixup.kext` v2.7.3

### Input
- `VoodooPS2Controller.kext` v2.3.7 (keyboard)
- `VoodooI2C.kext` v2.9.1 + VoodooI2CHID (trackpad)
- `BrightnessKeys.kext` v1.0.4

### USB
- `USBToolBox.kext` v1.2.0
- `UTBMap.kext` v1.1 (custom USB map)

### Other
- `AMFIPass.kext` v1.4.1 (AMFI bypass)
- `ECEnabler.kext` v1.0.6 (embedded controller)
- `RestrictEvents.kext` v1.1.7
- `ForgedInvariant.kext` v1.5.0

## Known Issues

1. **Audio**: Requires manual AppleHDA reinstall (see above)
2. **Microphone**: Won't work until AppleHDA is restored
3. **USB Bluetooth port**: May need Internal(255) connector type in UTBMap for Tahoe

## Credits

- [Acidanthera](https://github.com/acidanthera) - OpenCore, Lilu, AppleALC, WhateverGreen, VirtualSMC
- [OpenIntelWireless](https://github.com/OpenIntelWireless) - itlwm, IntelBluetoothFirmware
- [lshbluesky](https://github.com/lshbluesky/IntelBluetoothFirmware) - Tahoe-compatible Bluetooth fork
- [VoodooI2C Team](https://github.com/VoodooI2C) - Trackpad support
- [Dortania](https://dortania.github.io) - OpenCore guides
- [perez987](https://github.com/perez987/AppleHDA-back-on-macOS-26-Tahoe) - AppleHDA restore guide
- [Mirone](https://github.com/Mirone/MyKextInstaller) - MyKextInstaller tool

## Repository

https://github.com/Megh-Rana/OPENCORE_ASUS_X543UA
