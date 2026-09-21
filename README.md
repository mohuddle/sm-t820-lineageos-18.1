# Galaxy Tab S3 Wi-Fi (SM-T820) — Stock Android 9 to LineageOS 18.1

Notes from a **2026-09-20** install on Linux (Omarchy/Arch) using **Heimdall**, not Odin.

This is an **unofficial** LineageOS port. The Tab S3 is not on [wiki.lineageos.org](https://wiki.lineageos.org/). Trees and builds are from [Awesometic](https://xdaforums.com/m/awesometic.8447959/).

| | |
|---|---|
| Device | Samsung Galaxy Tab S3 Wi-Fi, **SM-T820**, `gts3lwifi` |
| Starting OS | Stock **Android 9 Pie**, `T820XXU3CTD5` (last official, March 2020 patch, CSC XAR, bootloader binary 3) |
| Target | Unofficial **LineageOS 18.1** (Android 11), build `lineage-18.1-20241010-UNOFFICIAL-gts3lwifi` |
| Host | Linux, `heimdall` 2.2.2, `adb` from `android-tools` |

US carrier LTE tablets (**SM-T827V**, **SM-T827R4**) usually have no OEM Unlock. This procedure is for the international Wi-Fi **SM-T820** (and should be similar for international LTE **SM-T825** / `gts3llte` with the matching zips).

---

## Why 18.1 instead of 20/21

| ROM | Android | Daily-driver notes |
|---|---|---|
| **18.1** | 11 | Most complete: S-Pen, LTE (T825), OTG, hardware encryption available |
| 19.1 | 12L | LTE still works |
| 20 | 13 | LTE, USB OTG, and data encryption broken |
| 21 | 14 | Same regressions; early builds did not boot |

Stock firmware never went past Pie. There is no official Android 10+ from Samsung.

---

## Current software (after Play Store self-update)

Snapshot **2026-09-20** on this tablet, after first boot and Play updates finished:

| Component | Package | Running now | What the ROM zip shipped |
|---|---|---|---|
| LineageOS | — | `18.1-20241010-UNOFFICIAL-gts3lwifi` | same |
| Android | — | 11 (API 30) | same |
| AOSP security patch | — | **2024-02-05** | same |
| Play Store | `com.android.vending` | **53.0.27-29** (`versionCode` 85302720, targetSdk 37) | MindTheGapps Phonesky **20.4.33** (82043300) |
| Play Services | `com.google.android.gms` | **26.34.36** (`versionCode` 263436022, minSdk 30, targetSdk 37) | PrebuiltGmsCore **20.45.16** (204516046) |
| Google Services Framework | `com.google.android.gsf` | **11** (`versionCode` 30) | same |
| MindTheGapps zip | — | flashed once | `MindTheGapps-11.0.0-arm64-20230922_081122.zip` |
| Noteshelf 3 | `com.fluidtouch.noteshelf3` | **5.1.5** (`versionCode` 684) | not in the ROM |
| Backdrops | `com.backdrops.wallpapers` | **5.1.8** (`versionCode` 221), sideloaded | Play 6.5 is blocked / PairIP |

System copies of Play Store 20.4.33 and GMS 20.45.16 remain under `/system/product/priv-app/` as the ROM baseline. Play then overlays newer versions under `/data/app/`.

Other Google packages present after MindTheGapps + Play: Quick Search Box, TTS, TalkBack, Markup, Calendar/Contacts sync, Exchange, Pixel migrate, Play Books, ARCore, Play Protect verifier, Gearhead, Setup Wizard (disabled-user after first-run loop).

---

## Files used (with hashes)

Staging dir on the PC: `~/Downloads/lineage-gts3lwifi`

| File | Source | SHA-256 |
|---|---|---|
| `lineage-18.1-20241010-UNOFFICIAL-gts3lwifi.zip` (663 MiB, 695089605 bytes) | [awesometic/android-ota-provider](https://github.com/awesometic/android-ota-provider/releases/tag/gts3lwifi_lineage-18.1_1728573708) | `25c14c6d78776c07a88a975914451038ada4f5e8bc91d297345e768d8179dc57` |
| `MindTheGapps-11.0.0-arm64-20230922_081122.zip` | [MindTheGapps/11.0.0-arm64](https://github.com/MindTheGapps/11.0.0-arm64/releases/tag/MindTheGapps-11.0.0-arm64-20230922_081122) | `a34e054b952b8d5117e5c011b807ef25be1f02d71f0d1ce11db42e89da2962ce` |
| `twrp-3.5.2_9-0-gts3lwifi.img` | Awesometic TWRP archive (Google Drive) | flashed; 3.5.2 is the 18.1-recommended recovery |
| `twrp-3.7.0_9.0-no_encryption-gts3lwifi.img` | same archive, Drive id `1Zd6bfH1gTFDtNT9SBjUGk75YmCabL8vn` | kept as spare (needed for LOS 20+ unencrypted data) |
| Backdrops 5.1.8 APKM | [APKMirror 5.1.8](https://www.apkmirror.com/apk/backdrops/backdrops-wallpapers/backdrops-wallpapers-5-1-8-release/) | `a78a921faaed97903afd76f110af7a7b8e405b71d3b8e9b74cef1419c7bf3fad` |

Backdrops signing cert (official): SHA-1 `d6d7c455eae4b164065cb8005d2d0497c7b158f2` (CN=Samer Zayer, Backdrops).

Also copy the Lineage + GApps zips onto a microSD **before** unlocking. Unlock wipes internal storage; the SD card survives.

---

## Host setup (Linux)

```bash
# Arch / Omarchy
omarchy pkg add android-tools android-udev heimdall
# user must be in group adbusers, then log out/in

heimdall version   # 2.2.2 on this install
adb version
```

USB IDs:

- Stock / TWRP MTP+ADB: `04e8:6860`
- Download Mode: `04e8:685d`

---

## Procedure (what actually worked)

### 1. Backup on stock

- Export Noteshelf notebooks (app export / Drive). App-private data is not a simple folder copy without root.
- Copy anything else off `/sdcard`.
- Know the Google account (FRP after wipe).
- Charge; keep USB plugged in through flash. This unit was flashed around 13% battery on USB power — not ideal.

### 2. Enable OEM unlocking

On stock Pie:

1. Settings → About → tap Build number 7 times
2. Developer options → **OEM unlocking** + **USB debugging**
3. Authorize the PC (`adb devices` shows `device`)

Confirm model:

```bash
adb shell getprop ro.product.model    # SM-T820
adb shell getprop ro.product.device   # gts3lwifi
adb shell getprop ro.build.display.id # T820XXU3CTD5
```

On this tablet, `sys.oem_unlock_allowed=1` plus flashing TWRP was enough. `ro.boot.flash.locked` was still `1` / verified boot green until TWRP landed. No extra Download-Mode “Device unlock” confirm was required. Knox warranty bit goes from 0 → tripped permanently.

### 3. Download Mode and TWRP

```bash
adb reboot download
heimdall detect
heimdall flash --RECOVERY twrp-3.5.2_9-0-gts3lwifi.img --no-reboot
```

**Do not let it boot Samsung.** Stock recovery will overwrite TWRP.

From the turquoise Downloading screen:

1. Hold **Volume Down + Home + Power** until the screen goes black
2. Immediately **Volume Up + Home + Power** through the Samsung logo until TWRP

First TWRP boot can be slow. Swipe to allow modifications. ADB should show `recovery` / `product:omni_gts3lwifi`.

Do **not** `twrp mount /data` on still-encrypted userdata; that dropped ADB on this unit. Format instead.

### 4. Format and flash in TWRP

Zips on microSD (`/external_sd`):

```bash
adb shell twrp format data     # USB may drop; wait for recovery to reappear
adb shell twrp wipe cache
adb shell twrp wipe system
adb shell twrp install /external_sd/lineage-18.1-20241010-UNOFFICIAL-gts3lwifi.zip
adb shell twrp install /external_sd/MindTheGapps-11.0.0-arm64-20230922_081122.zip
adb reboot
```

Install GApps in the same TWRP session, before the first system boot.

`format data` removes stock FDE. Lineage 18.1 *can* encrypt again later (CryptKeeper in Settings). This install was left **unencrypted**.

First boot: Lineage animation for a few minutes, then setup wizard. `sys.boot_completed=1` was about 40 seconds after ADB showed `device` here.

---

## After first boot

### Setup wizard loop (OK Google ↔ fingerprint)

MindTheGapps + Lineage setup wizards can loop: skip OK Google → fingerprint/pattern → OK Google again.

Break it over ADB (keeps any lock already set):

```bash
adb shell settings put global device_provisioned 1
adb shell settings put secure user_setup_complete 1
adb shell settings put global setup_wizard_has_run 1
adb shell pm disable-user --user 0 com.google.android.setupwizard
adb shell pm disable-user --user 0 org.lineageos.setupwizard
adb shell am force-stop com.google.android.setupwizard
adb shell am force-stop org.lineageos.setupwizard
adb shell am force-stop com.google.android.googlequicksearchbox
adb shell am start -a android.intent.action.MAIN -c android.intent.category.HOME
```

Enroll fingerprint later under **Settings → Security**. Leave **OEM unlocking** on forever or the tablet can refuse to boot.

### Play Store crash / “GMS frozen”

Play Store will try to update Play Services. During the overlay install, `com.google.android.gms` can be **frozen** (`SecurityException: Package com.google.android.gms is currently frozen!`) and the store closes instantly.

If it is stuck mid-update:

```bash
adb shell pm uninstall com.google.android.gms   # reverts to ROM 20.45.16
adb shell pm enable com.google.android.gms
adb shell pm enable com.google.android.gsf
adb shell pm clear com.android.vending
```

On this tablet the 26.34.36 overlay **did finish** and then worked on Android 11. Paid-app licenses that looked “not owned” during the freeze came back after a reboot. Do not re-purchase.

This ROM is **not Play Protect certified** (`userdebug`, test-keys, unlocked). Some Play listings (including Backdrops 6.5) show “not compatible.”

### LineageOS Trust (red triangle)

Informational, not a pending OTA. Expected flags:

- SELinux **Permissive** (not fixed in this unofficial tree)
- Unofficial / **test-keys**
- Data **unencrypted** (unless you encrypt in Settings)
- USB debugging on
- Security patch stuck at **2024-02-05**

Do not flash random “security patch” zips or Magisk to clear it. Magisk makes Play licensing worse.

Optional: turn USB debugging off when the PC is not needed; encrypt via Settings if you want the lost-tablet case covered (TWRP will then need the PIN).

---

## Why it feels faster than stock Pie

Same hardware: Snapdragon 820, Adreno 530, 4 GB RAM, 9.7″ 2048×1536 **60 Hz**. No CPU upgrade and no higher refresh rate. Stock Pie was starving the tablet; 18.1 is not. After this install, Brave opens and paints pages quickly, and Noteshelf ink lands smoothly.

**Samsung software was the tax.** Last official firmware was One UI–era Pie (`T820XXU3CTD5`, March 2020). Knox, Samsung account, Device Care, Air View, S Pen services, and a pile of always-on daemons sat on 4 GB of RAM. Apps got killed, launches were cold, scrolling hitching was normal. Lineage is nearly stock AOSP: almost none of that is running, so Brave and Noteshelf stay in memory instead of reloading.

**The kernel is newer in the ways that matter for “snappy.”** Awesometic’s tree is tagged **EAS** (Energy Aware Scheduling): WALT, schedutil, SchedTune, ZRAM+LZ4 (2 GB on this 4 GB device), BFQ, AdrenoBoost, `CONFIG_HZ=300`. Stock used older HMP-style scheduling. EAS is better at “user just touched the screen, give the big cores to this app now.” XDA reports on this same ROM were already “much faster than even tweaked stock.”

**Android 11’s app/runtime stack is two generations ahead of Pie.** ART, HWUI/Skia, the input/vsync path, and Chromium/WebView all improved in 10 and 11. Brave on 11 is a current browser engine. On Pie you were on an old WebView and a bloated Samsung Internet/Chrome. Same GPU, much less software in the way.

**Storage is lighter.** Stock used full-disk encryption. This install was left **unencrypted**, so notebook files and Brave cache hit the eMMC without an extra crypto pass. That shows up as faster app start and less hitch when ink or a page commits.

**S-Pen ink is a shorter path.** On Samsung, hover/Air View, the S Pen framework, and app SDKs sat between the digitizer and the page. On this ROM the panel’s Wacom EMR node (`sec_e-pen`, pressure 0–4095, hover, tilt) goes kernel → Android InputReader → Noteshelf’s normal stylus API. Palm rejection is in the kernel (since 2021-07-14), not a Samsung service. Fewer layers, less jitter, strokes land closer to display vsync. Noteshelf 3 on Android 11 can also use newer batched motion events than Noteshelf 2 could on Pie.

---

## S-Pen and Noteshelf

The Tab S3 S-Pen is Wacom EMR in the panel (no Bluetooth). Lineage 18.1 exposes `sec_e-pen` with pressure 0–4095, hover, tilt, `BTN_STYLUS`. Palm rejection is in the kernel.

Noteshelf 3 needs Android 11+, so stock Pie hid it in Play. On 18.1, **Noteshelf 3 5.1.5** installs and works with stylus pressure enabled in the app.

Vendor `sec_e-pen.idc` is only `touch.orientationAware = 1`. A fuller idc can improve light-press drawing if it ever feels stiff.

Gone with Samsung: Air Command, screen-off memo, Samsung Notes, handwriting keyboard.

### S-Pen pointer (blue circle)

A small **cyan ring** follows the S-Pen the whole time it is in range: hovering over the glass **and** while the tip is down writing. It is system-wide — home screen, empty wallpaper, Settings, Noteshelf, any app — not something Noteshelf (or any other app) draws. It does not hide on contact.

This ROM is driving the system pointer from the digitizer. Awesometic replaced the usual mouse arrow with a 30×30 cyan donut, so the same cursor stays on screen from hover through the stroke, everywhere:

| | |
|---|---|
| Overlay | [gts3l-common `pointer_arrow.png`](https://github.com/awesometic/android_device_samsung_gts3l-common/blob/lineage-18.1/overlay/frameworks/base/core/res/res/drawable-xhdpi/pointer_arrow.png) (xhdpi, 30×30 RGBA) |
| Hotspot | `pointer_arrow_icon.xml`: `hotSpotX/Y = 8.0dip` |
| Changelog | 2021-07-04 “Added new pointer arrow icon for S-Pen”; 2021-08-13 “Change the hotspot location of the pointer arrow” |
| Digitizer IDC | `/vendor/usr/idc/sec_e-pen.idc` (stub: `touch.orientationAware = 1`) |

On stock Pie this was Samsung **Air view** (Settings → Advanced features → S Pen). Lineage has no Air view. Android 11 also has no later AOSP toggle (`Settings → Stylus → Show pointer while hovering` / `stylus_pointer_icon_enabled` is Android 14+).

The ring sitting a bit below the physical tip is the overlay hotspot vs the angled nib, not a broken digitizer.

**This install (2026-09-20):** confirmed `sec_e-pen` is `Touch Input Mapper (mode - pointer)` / `DeviceType: pointer` with Show taps already off. The stock donut was too loud while writing, so `res/drawable-xhdpi-v4/pointer_arrow.png` inside `/system/framework/framework-res.apk` was replaced with a thinner ~40% alpha cyan donut (same 30×30, hotspot unchanged). **Kept:** still a useful “where will the pen land” hover helper; once ink is down it reads much fainter against the darker stroke. Original APK + PNG are on the tablet at `/sdcard/spen-pointer-backup/` and on the PC at `~/Downloads/lineage-gts3lwifi/spen-pointer-backup/`.

Needs `adb root` (Developer options → **Rooted debugging**), remount, then reboot. Do not Magisk just for this.

To restore the original loud ring:

```bash
adb root
adb remount
adb push /sdcard/spen-pointer-backup/framework-res.apk /system/framework/framework-res.apk
adb shell chmod 644 /system/framework/framework-res.apk
adb shell chcon u:object_r:system_file:s0 /system/framework/framework-res.apk
adb reboot
```

To hide it entirely later, classify the pen as a touchscreen so it no longer uses `pointer_arrow` for hover or contact. USB mouse cursor stays. Needs reboot:

```bash
adb root
adb remount
adb pull /vendor/usr/idc/sec_e-pen.idc /tmp/sec_e-pen.idc.bak
# write a new idc, then:
adb push sec_e-pen.idc /vendor/usr/idc/sec_e-pen.idc
adb shell chmod 644 /vendor/usr/idc/sec_e-pen.idc
adb reboot
```

Example `sec_e-pen.idc` (also the usual pressure-calibration tweak):

```
touch.deviceType = touchScreen
touch.orientationAware = 1
touch.pressure.calibration = physical
touch.pressure.scale = 0.000244
touch.size.calibration = none
```

AOSP 11 only honors `touchScreen` / `touchPad` / `pointer` / `default` for `touch.deviceType`. The value `stylus` seen in some XDA posts is ignored.

Fallback: replace `pointer_arrow.png` with a fully transparent 30×30 PNG of the same name (same hotspot). That also hides an OTG mouse cursor. On this build the PNG lives at `res/drawable-xhdpi-v4/pointer_arrow.png` inside `/system/framework/framework-res.apk`.

Hover events and Noteshelf pressure keep working either way; only the on-screen mark goes away. To undo, restore the backed-up idc (or the original PNG) and reboot.

---

## Backdrops sideload

Play Store 6.5 is an APK bundle with Google PairIP (`com.pairip.licensecheck.LicenseActivity`). Sideloading 6.5 immediately bounced to Play Store and died.

Official **5.1.8** (same Backdrops Dev LLC signature, no PairIP) launches. Device density on this T820 is **320** (xhdpi):

```bash
adb uninstall com.backdrops.wallpapers   # if 6.5 was installed
# unzip APKM, then:
adb install-multiple -r -d base.apk split_config.xhdpi.apk split_config.en.apk
adb shell am start -n com.backdrops.wallpapers/.WelcomeActivity
```

Sign in with the same Google account and use Restore purchases / Go Pro. Resplash remains a fallback wallpaper app.

---

## Reboot to TWRP later

Power off, then **Volume Up + Home + Power** until TWRP.

---

## What we did not do

- Did not flash newer Lineage 19/20/21 (feature loss vs 18.1)
- Did not Magisk/root
- Did not hide the S-Pen pointer ring entirely; swapped it for a thinner/fainter donut in `framework-res.apk` (original backed up)
- Did not encrypt userdata
- Did not rebuild 18.1 for a newer ASB (possible later; Linux 3.18 trees)
- Did not fix SELinux enforcing

---

## Sources

- [LOS 18.1 XDA thread (EOL 2024-10-10)](https://xdaforums.com/t/eol-rom-unofficial-11-eas-signature-spoofing-ota-sm-t820-sm-t825-2024-10-10-lineageos-18-1-for-galaxy-tab-s3.4293069/)
- [LOS 20 XDA](https://xdaforums.com/t/eol-rom-unofficial-13-eas-ota-sm-t820-sm-t825-2024-10-12-lineageos-20-0-for-galaxy-tab-s3.4510785/)
- [LOS 21 XDA](https://xdaforums.com/t/rom-unofficial-14-eas-ota-sm-t820-sm-t825-2024-3-9-lineageos-21-for-galaxy-tab-s3.4660924/)
- Device: [gts3l-common](https://github.com/awesometic/android_device_samsung_gts3l-common), [gts3lwifi](https://github.com/awesometic/android_device_samsung_gts3lwifi)
- Kernel: [android_kernel_samsung_msm8996](https://github.com/awesometic/android_kernel_samsung_msm8996) (`lineage-18.1-caf`, Linux 3.18)
- Vendor: [proprietary_vendor_samsung](https://github.com/awesometic/proprietary_vendor_samsung)

ROM, kernel, and TWRP credit: **Awesometic**. GApps: MindTheGapps. Heimdall: grimler fork.
