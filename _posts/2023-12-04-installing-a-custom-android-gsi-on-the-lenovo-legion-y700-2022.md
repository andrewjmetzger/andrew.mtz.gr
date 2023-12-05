---
title: "Installing the crDroid Android GSI on a 2022 Lenovo Legion Y700"
date: 2023-12-04T18:52:00-04:00
categories: [guides, android]
---



## Preparation

### On your PC
1. adb, fastboot,
2. Download and extract the latest crDroid `arm64-BgN-Unofficial.img` from <https://github.com/naz664/crDroid_gsi/releases>. The archive should contain one IMG file.
  
#### About GSI files

According to [Treble Info app (sideloaded apk)](https://f-droid.org/packages/tk.hack5.treblecheck/), the Y700 2022 is an arm64 A/B device. To determine whch GSI you need, look at the download names, `<ARCH>_xyZ.img.xz`. 

```
<ARCH> can be arm, a64, or arm64. 

*Note: The Y700 is arm64. A64 is the shorthand for arm32_64binder. It is basically 32bit for armv8 CPUs which are capable of 64bit. The software is 32bit with a 64bit binder for 64bit capable hardware*

x can be a or b
y can be v, o, g, or f
Z can be N or S

b = a/b
a = a-only

g = gapps
o = gapps-go
v = vanilla (no gapps included)
f = floss (free & open source apps instead of gapps)

N = no superuser
S = superuser included
```

### On the device

1. Go to Settings > My Device > tap "ZUI version" 10 times until you are a developer.
2. Go to Settings > General settings > Developer options. In the Developer options:
  1. Enable `USB debugging`
  2. Disable  `adb authorization timeout`
  3. Enable `Stay awake`
  4. Enable `OEM Unlock` and confirm it
3. Connect the device via USB and make sure it is visible to ADB.


## Unlock the bootloader, for real this time

1. In terminal, issue `adb reboot fastboot` (Or turn off the device and hold <kbd>POWER</kbd> + <kbd>VOLUME DOWN</kbd>). You will see a red "fastbootd" at the top of the screen.
2. Once the tablet is in fastboot mode, unlock the bootloader: `fastboot oem unlock-go`
3. You will reboot into a chinese language setup screen. The left option is YES for ADB authorization and check the box to remember this.
4. Confirm DEVICE STATE is "unlocked": `adb reboot bootloader`

## Flashing the GSI

1. Go back to fastboot: `adb reboot fastboot` (Or turn off the device and hold <kbd>POWER</kbd> + <kbd>VOLUME DOWN</kbd>). You will see a red "fastbootd" at the top of the screen.
2. Wipe the "system" partition before installing any GSI: `fastboot erase system`
  ```
  $ fastboot erase system   
  Erasing 'system_a'                                 OKAY [  0.101s]
  Finished. Total time: 0.109s
  ```
3. Flash the GSI you extracted before: `fastboot flash system .\crDroid-x.xx-arm64_bgN-Unofficial.img`. This takes about two minutes.
  ```
  $ fastboot flash system .\crDroid-9.10-arm64_bgN-Unofficial.img
  Resizing 'system_a'                                OKAY [  0.005s]
  Sending sparse 'system_a' 1/16 (262084 KB)         OKAY [  6.263s]
  Writing 'system_a'                                 OKAY [  1.542s]
  Sending sparse 'system_a' 2/16 (262104 KB)         OKAY [  6.228s]
  Writing 'system_a'                                 OKAY [  0.398s]
  Sending sparse 'system_a' 3/16 (261988 KB)         OKAY [  6.251s]
  Writing 'system_a'                                 OKAY [  0.402s]
  Sending sparse 'system_a' 4/16 (261646 KB)         OKAY [  6.256s]
  Writing 'system_a'                                 OKAY [  0.406s]
  Sending sparse 'system_a' 5/16 (261028 KB)         OKAY [  6.258s]
  Writing 'system_a'                                 OKAY [  0.399s]
  Sending sparse 'system_a' 6/16 (261418 KB)         OKAY [  6.272s]
  Writing 'system_a'                                 OKAY [  0.405s]
  Sending sparse 'system_a' 7/16 (261865 KB)         OKAY [  6.241s]
  Writing 'system_a'                                 OKAY [  0.395s]
  Sending sparse 'system_a' 8/16 (261052 KB)         OKAY [  6.234s]
  Writing 'system_a'                                 OKAY [  0.403s]
  Sending sparse 'system_a' 9/16 (260383 KB)         OKAY [  6.226s]
  Writing 'system_a'                                 OKAY [  0.427s]
  Sending sparse 'system_a' 10/16 (256347 KB)        OKAY [  6.171s]
  Writing 'system_a'                                 OKAY [  0.403s]
  Sending sparse 'system_a' 11/16 (262012 KB)        OKAY [  6.262s]
  Writing 'system_a'                                 OKAY [  0.399s]
  Sending sparse 'system_a' 12/16 (262088 KB)        OKAY [  6.227s]
  Writing 'system_a'                                 OKAY [  0.406s]
  Sending sparse 'system_a' 13/16 (262096 KB)        OKAY [  6.242s]
  Writing 'system_a'                                 OKAY [  0.393s]
  Sending sparse 'system_a' 14/16 (261631 KB)        OKAY [  6.280s]
  Writing 'system_a'                                 OKAY [  0.401s]
  Sending sparse 'system_a' 15/16 (257708 KB)        OKAY [  6.223s]
  Writing 'system_a'                                 OKAY [  0.408s]
  Sending sparse 'system_a' 16/16 (83196 KB)         OKAY [  1.987s]
  Writing 'system_a'                                 OKAY [  0.324s]
  Finished. Total time: 105.220s
  ```
4. Next, initialize it with the following command: `fastboot -w`
   ```
  $ fastboot -w
  Erasing 'userdata'                                 OKAY [  0.067s]
  Erase successful, but not automatically formatting.
  File system type raw not supported.
  wipe task partition not found: cache
  Erasing 'metadata'                                 OKAY [  0.003s]
  Erase successful, but not automatically formatting.
  File system type raw not supported.
  Finished. Total time: 0.085s
  ```

5. Restart after initialization is complete: `fastboot reboot`
6. After configuring Wi-Fi, Google account (optional), and PIN, you may get stuck at ythe face unlock screen. These commands should bypass the OOBE/setup wizard:
  ```
  $ adb shell settings put secure user_setup_complete 1
  $ adb shell settings put global device_provisioned 1
  $ adb shell pm disable-user --user 0 com.google.android.setupwizard 
  $ adb reboot
  ```