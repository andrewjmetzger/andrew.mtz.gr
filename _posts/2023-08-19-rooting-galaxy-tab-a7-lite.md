---
title: Rooting a Samsung Galaxy Tab A7 Lite
---

# Prerequisites

- Windows 11
- ADB and fastboot (`winget install Google.PlatformTools`)
- [Galaxy Tab A7 Lite USB drivers][samsung-windows-drivers]
- [Odin with 3B patch, v3.1.4.1][samsung-odin]

General info:

- [XDA forum for Samsung Galaxy Tab A7 Lite][xda]

# Unlock Bootloader

## Getting "OEM Unlock" to appear

As with all Android devices, further modifications require the bootloader to be unlocked. If the bootloader cannot be unlocked using the Developer Options menu, follow these convoluted steps to make the setting appear:

1. Skip everything on the first boot setup including Wi-Fi settings (you'll connect to the Internet later)
2. Enable developer options and disable automatic system updates
3. Connect to a Wi-Fi network. 
4. Go to Settings > Software update, and disable automatic software updates.
5. Go to date and time settings and select for the time to be set manually. 
6. Set the date to be 10 days BEHIND the current date.
7. Go to Settings > Developer Options and turn the developer options off completely.
8. Now go to Settings > Software update and check for a manual update, but do not install it. After an update is shown restart your device. 
9. Go to date and time again but this time set it to be 10 days AHEAD of the current date.
10. Check for a software update, and reboot once finished
11. After reboot, go to date and time and add 8 more days AHEAD (you should be 18 days ahead of your actual date at this point). 
12. Perform a software update check once again and reboot.
13. As a last step after the final reboot, enable developer options again. 
14. Now, OEM unlock should be available!

Note: If this didn't work for you, wait a day or two for the toggle to show up. If you still don't see OEM unlock after those 48 hours, start over and try again.

## Unlocking the bootloader

1. Enable developer options by going to Settings > About Tablet > Software information > tap "build number" 7 times
2. Go to Developer options > enable OEM Unlock
3. Turn off the device. 
4. Connect it to the computer while holding Volume Up and Volume Down keys (at the same time) until you see Download Mode.
5. Long-press Volume Up key to enter Device Unlock mode
6. Press Volume Up key to confirm and unlock the bootloader

# Enable ADB, fastboot, and USB debugging

```
PS C:\Users\andrew\Downloads\SM-T220\> adb devices
* daemon not running; starting now at tcp:5037
* daemon started successfully
List of devices attached
R83W70G77AT     device

PS C:\Users\andrew\Downloads\SM-T220\> fastboot devices
R83W70G77AT      fastboot
```

## If fastboot is not working

Issue the command `adb reboot bootloader`, and wait until the device shows "Entering Fastboot mode...".

The `fastboot devices` command might show nothing.

1. Open devmgmt.msc and find the unknown Samsung USB device. 
2. Go to Windows Update > Advanced Options > Optional updates (in the Additional Options section). 
3. Install any Samsung, MediaTek, or Android Bootloader Interface updates.
4. Run `fastboot devices` again and the tablet should appear.

```
PS C:\Users\andrew\Downloads\SM-T220\> fastboot devices
R83W70G77AT      fastboot
```

# Custom recovery

Issue `adb reboot download`.

Flash [the latest PitchBlack Recovery image][pbrp] via ODIN using "BL" mode.

Next, wipe the /data/ partition to make it usable with PBRP by removing the factory encryption:

1. Boot to Recovery  (Power + Volume Up)
2. Settings
3. Format Data
4. Type "yes"
5. Done




<!-- 
TODO: May not need bifrost if using Magisk recovery?

# Bifrost to download and patch OTA

Download: [Bifrost APK][bifrost]

Install APK on tablet

Device: SM-T220
Region: XAR

Current latest: SM-T220_XAR_T220XXS3CWD1_fac

Takes a long time. 
-->

# Magisk

Download: [Magisk APK][magisk-apk]

Copy APK to ZIP by changing the file extension.

```
PS C:\Users\andrew\Downloads\SM-T220\> cp ./Magisk-v26.1.apk ./Magisk-v26.1.zip
PS C:\Users\andrew\Downloads\SM-T220\> adb push ./Magisk-v26.1.zip /sdcard/
```
Install ZIP via recovery.

Install the APK via ADB.

```
PS C:\Users\andrew\Downloads\SM-T220\> adb install ./Magisk-v26.1.apk
```

Open the Magisk app and complete install. Select the **Direct Install (Recommended)** option.

Patching should take a minute or so.


## Modules 

- Systemless Hosts: Enable under Magisk Home > Settings > Magisk. Required for AdAway.
- [Lsposed][lsposed]: LSposed framework.
- [Play Integrity Fix][lsposed-play-integrity-fix]: Hide root.
- [Built-In BusyBox][busybox-module]: UNIX/Linux utilities.
- [ToyBox-Ext][busybox-toybox]: Additional utilities for BusyBox.


# Other apps

- [Bitwarden][bitwarden]: Password manager.
- [Droid-ify][droid-ify]: F-Droid client.
- [Ad-Away][ad-away]: System-wide adblock.
- [Mull][mull]: Privacy-oriented build of Firefox.
- [Bromite][bromite-repo]: Privacy-Oriented Chromium (add repository to Droid-ify).
- [ReVanced][youtube-revanced]: YouTube patches

<!-- Links -->

[xda]: https://forum.xda-developers.com/f/samsung-galaxy-tab-a7-lite.12329/

[samsung-windows-drivers]: https://www.samsung.com/us/support/downloads/?model=N0053679&modelCode=SM-T220NZAAXAR
[samsung-odin]: https://forum.xda-developers.com/attachments/odin3-v3-14-1_3b_patched-zip.5158507/
[bifrost]: https://github.com/zacharee/SamloaderKotlin/releases

[pbrp]: https://github.com/R0GUEEE/android_device_samsung_gta7litewifi/releases/tag/PBRP/latest

[magisk-apk]: https://github.com/topjohnwu/Magisk/releases/latest
[lsposed]: https://github.com/LSPosed/LSPosed/releases/latest

[lsposed-play-integrity-fix]: https://forum.xda-developers.com/attachments/playintegrityfix_v7-4-zip.5981951/
[busybox-module]: https://github.com/Magisk-Modules-Alt-Repo/BuiltIn-BusyBox/releases/latest
[busybox-toybox]: https://github.com/Magisk-Modules-Alt-Repo/ToyBox-Ext/releases/latest

[bitwarden]: https://github.com/bitwarden/mobile/releases/latest
[droid-ify]:https://github.com/Droid-ify/client/releases/latest
[ad-away]: https://github.com/AdAway/AdAway/releases/latest
[mull]: https://f-droid.org/en/packages/us.spotco.fennec_dos/
[bromite-repo]: https://www.bromite.org/fdroid
[youtube-revanced]: https://revanced.app/download