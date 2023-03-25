---
title: 'Adventures in Replacing the NVidia Shield TV&#8217;s Launcher'
date: '2021-07-25T16:16:28-04:00'
categories: [guides, android, powershell]
---

## Background

When Shield Experience Update 8.2.3 rolled out to my Shield TVs, I was dismayed to see Google’s “Recommendations” for content atop the new home screen. Here’s one for *Loki* — woefully out of place on a premium set-top-box that retails for $150 USD.

![](https://9to5google.com/wp-content/uploads/sites/4/2021/06/SHIELD_TV_Discover_home-page.png)
_The new “Discovery UI” features promoted content atop the home screen. // Image credit: 9to5Google._

It’s fairly easy to suppress these with a custom launcher, though. I like **Sideload Channel Launcher 3 ([Google Play Store](https://play.google.com/store/apps/details?id=dxidev.sideloadchannel3), $3.99 USD)** for its blend of simplicity and customization. One enhancement I opted for was a combination clock/weather widget. This one comes from **Geometric Weather (FOSS, [Aptoide](https://wangdayeeeeee-weather.en.aptoide.com/app))**.

## Prerequisites

- An NVidia Shield TV with the latest updates
- A laptop or PC on the same network
- PowerShell with Administrator rights (if using the premade script)
- wget and the Android Debug Bridge: `choco install wget adb -y`

```powershell

$HasChocolatey = powershell choco -v

if (-not($HasChocolatey)) {
    Write-Output "Chocolatey is not installed yet, I'll go get it..."
    Set-ExecutionPolicy Bypass -Scope Process -Force; Invoke-Expression ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
    RefreshEnv
} else {
    Write-Output "Chocolatey version $testchoco is already installed"
}

$HasAdb = powershell adb --version
if (-not($HasAdb)) { 
    Write-Host "ADB not found, I'll install it now..."
    choco install adb -y 
}

New-Item -Path './adbtemp/' -Type Directory
Set-Location './adbtemp/'

$AdbIP = '192.168.1.162'
$AdbPort = '5555'

adb shell input keyevent 'KEYCODE_HOME'

adb connect $AdbIP`:$AdbPort

Read-Host -Prompt 'Accept the connection on the target device, then press ENTER'
adb connect $AdbIP`:$AdbPort


# Download SmartTubeNext
wget.exe 'https://github.com/yuliskov/SmartYouTubeTV/releases/download/beta/smarttube_beta.apk'
adb install '.\smarttube_beta.apk'


# Configure YouTube Account
adb shell am start -n 'com.liskovsoft.smarttubetv.beta/com.liskovsoft.smartyoutubetv2.tv.ui.main.SplashActivity'

# Open SETTINGS
adb shell input keyevent 'KEYCODE_DPAD_LEFT'
adb shell input keyevent 'KEYCODE_DPAD_DOWN'
adb shell input keyevent 'KEYCODE_DPAD_DOWN'
adb shell input keyevent 'KEYCODE_DPAD_DOWN'
adb shell input keyevent 'KEYCODE_DPAD_DOWN'
adb shell input keyevent 'KEYCODE_DPAD_DOWN'
adb shell input keyevent 'KEYCODE_DPAD_DOWN'
adb shell input keyevent 'KEYCODE_DPAD_DOWN'
adb shell input keyevent 'KEYCODE_DPAD_DOWN'
adb shell input keyevent 'KEYCODE_DPAD_CENTER'
adb shell input keyevent 'KEYCODE_DPAD_CENTER'

# Add account
adb shell input keyevent 'KEYCODE_DPAD_DOWN'
adb shell input keyevent 'KEYCODE_DPAD_DOWN'
adb shell input keyevent 'KEYCODE_DPAD_CENTER'

Start-Process 'https://youtube.com/activate'

Read-Host -Prompt 'wait for the settings menu to appear again, then press ENTER'
Pause

# Verify account
adb shell am force-stop 'com.liskovsoft.smarttubetv.beta'
adb shell am start -n 'com.liskovsoft.smarttubetv.beta/com.liskovsoft.smartyoutubetv2.tv.ui.main.SplashActivity'

# adb shell pm list packages -f -3
adb shell pm disable-user --user 0 com.google.android.tvlauncher

adb shell am start -a android.intent.action.VIEW -d 'market://details?id=dxidev.sideloadchannel3'
adb shell input keyevent 'KEYCODE_DPAD_CENTER'

Read-Host -Prompt 'wait for the app to install, then press ENTER'

Pause

adb shell input keyevent 'KEYCODE_HOME'
adb shell input keyevent 'KEYCODE_HOME'

adb shell appwidget grantbind --package dxidev.sideloadchannel3 --user 0
adb shell am force-stop 'dxidev.sideloadchannel3'



adb shell rm '/storage/emulated/0/Sideload\ Channel\ 3/*.slc3'
adb push SCL3.slc3 '/storage/emulated/0/Sideload Channel 3/SCL3.slc3'

# Open LAUNCHER SETTINGS
adb shell input keyevent 'KEYCODE_DPAD_DOWN'
adb shell input keyevent 'KEYCODE_DPAD_DOWN'
adb shell input keyevent 'KEYCODE_DPAD_RIGHT'
adb shell input keyevent 'KEYCODE_DPAD_CENTER'

# Go to APP SETTINGS
adb shell input keyevent 'KEYCODE_DPAD_DOWN'
adb shell input keyevent 'KEYCODE_DPAD_DOWN'
adb shell input keyevent 'KEYCODE_DPAD_CENTER'

# Go to IMPORT
adb shell input keyevent 'KEYCODE_DPAD_DOWN'
adb shell input keyevent 'KEYCODE_DPAD_DOWN'
adb shell input keyevent 'KEYCODE_DPAD_DOWN'
adb shell input keyevent 'KEYCODE_DPAD_DOWN'
adb shell input keyevent 'KEYCODE_DPAD_DOWN'
adb shell input keyevent 'KEYCODE_DPAD_DOWN'
adb shell input keyevent 'KEYCODE_DPAD_DOWN'
adb shell input keyevent 'KEYCODE_DPAD_DOWN'
adb shell input keyevent 'KEYCODE_DPAD_DOWN'
adb shell input keyevent 'KEYCODE_DPAD_CENTER'

# Load from disk
adb shell input keyevent 'KEYCODE_DPAD_DOWN'
adb shell input keyevent 'KEYCODE_DPAD_DOWN'
adb shell input keyevent 'KEYCODE_DPAD_DOWN'
adb shell input keyevent 'KEYCODE_DPAD_CENTER'

# Continue
adb shell input keyevent 'KEYCODE_DPAD_RIGHT'
adb shell input keyevent 'KEYCODE_DPAD_RIGHT'
adb shell input keyevent 'KEYCODE_DPAD_CENTER'

# Select configuration file on device
adb shell input keyevent 'KEYCODE_DPAD_CENTER'

adb shell input keyevent 'KEYCODE_DPAD_DOWN'
adb shell input keyevent 'KEYCODE_DPAD_DOWN'
adb shell input keyevent 'KEYCODE_DPAD_DOWN'
adb shell input keyevent 'KEYCODE_DPAD_DOWN'
adb shell input keyevent 'KEYCODE_DPAD_DOWN'
adb shell input keyevent 'KEYCODE_DPAD_DOWN'
adb shell input keyevent 'KEYCODE_DPAD_DOWN'
adb shell input keyevent 'KEYCODE_DPAD_DOWN'
adb shell input keyevent 'KEYCODE_DPAD_DOWN'
adb shell input keyevent 'KEYCODE_DPAD_DOWN'

adb shell input keyevent 'KEYCODE_DPAD_CENTER'
adb shell input keyevent 'KEYCODE_DPAD_CENTER'
```
