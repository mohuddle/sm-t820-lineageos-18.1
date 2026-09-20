# Version snapshot

Taken from the live SM-T820 over ADB on **2026-09-20** after LineageOS 18.1 first boot and Play Store self-updates.

```
ro.lineage.version=18.1-20241010-UNOFFICIAL-gts3lwifi
ro.build.version.release=11
ro.build.version.sdk=30
ro.build.version.security_patch=2024-02-05
ro.build.tags=test-keys
ro.build.type=userdebug
getenforce=Permissive
ro.crypto.state=unencrypted
ro.boot.verifiedbootstate=orange
```

## Google / Play

| Package | Role | versionName | versionCode | path |
|---|---|---|---|---|
| `com.android.vending` | Play Store (running) | 53.0.27-29 [0] [PR] 973951861 | 85302720 | `/data/app/…` |
| `com.android.vending` | Play Store (ROM) | 20.4.33-all [0] [PR] 319051143 | 82043300 | `/system/product/priv-app/Phonesky` |
| `com.google.android.gms` | Play Services (running) | 26.34.36 (150400-981326859) | 263436022 | `/data/app/…` |
| `com.google.android.gms` | Play Services (ROM) | 20.45.16 (150400-344294571) | 204516046 | `/system/product/priv-app/PrebuiltGmsCore` |
| `com.google.android.gsf` | Google Services Framework | 11 | 30 | system |

MindTheGapps package flashed: `MindTheGapps-11.0.0-arm64-20230922_081122.zip`  
SHA-256 `a34e054b952b8d5117e5c011b807ef25be1f02d71f0d1ce11db42e89da2962ce`

## Lineage zip

`lineage-18.1-20241010-UNOFFICIAL-gts3lwifi.zip`  
SHA-256 `25c14c6d78776c07a88a975914451038ada4f5e8bc91d297345e768d8179dc57`  
Release: https://github.com/awesometic/android-ota-provider/releases/tag/gts3lwifi_lineage-18.1_1728573708

## Recovery

TWRP `3.5.2_9-0` (`ro.twrp.version=3.5.2_9-0`, product `omni_gts3lwifi`).

## User apps of note

| Package | versionName | versionCode | notes |
|---|---|---|---|
| `com.fluidtouch.noteshelf3` | 5.1.5 | 684 | Play Store; S-Pen OK |
| `com.backdrops.wallpapers` | 5.1.8 | 221 | Sideload; 6.5 PairIP blocked |
