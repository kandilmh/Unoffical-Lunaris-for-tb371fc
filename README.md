# Lunaris for the Lenovo TB371FC (unofficial)

[English](#english) | [한국어](#한국어)

## English

An unofficial port of Lunaris AOSP 3.12 (Android 16, with Google apps) to the Lenovo
Xiaoxin Pad Pro 12.7 (TB371FC, Snapdragon 870). The system image comes from the official
Lunaris build for vili and runs on the stock ZUI_16.0.474 vendor. The Lenovo pen, all four
speakers, Dolby Atmos, charging modes and battery protection, memory extension and the
tablet layouts work.

Personal build, not affiliated with Lenovo or the Lunaris team.

### Before installing

- **Unlocked bootloader required.** It cannot be flashed on a locked tablet, and
  unlocking erases all data.
- **Do not relock the bootloader** with this ROM installed. The tablet will not boot.
- **DRM drops to Widevine L3.** Unlocked devices do not get L1, so Netflix and similar
  apps play in SD, and some will not start.
- A clean install erases all data. Back up first.

### Release files

| File | Description |
| --- | --- |
| `lunaris-1.0-tb371fc.7z.001`, `.002` | full images for a clean install (split 7z) |
| `lunaris-tb371fc-<old>-to-1.0.zip` | OTA update from an older version (used by the Updater) |
| `TB371FC-*.json` | update info for the Updater |
| `lunaris-1.0-source.tar.gz` | build scripts and sources (signing keys not included) |
| `SHA256SUMS.txt` | checksums of the release files and of the images inside the 7z |

The 7z contains `lunaris.img` (system), `vbmeta_system.img`, and `boot.img`, `dtbo.img`,
`recovery.img` and `odm.img` from stock ZUI_16.0.474. `vendor.img` is the stock vendor
with Widevine set to L3, and `vbmeta.img` is re-signed for it. `boot.img` is unmodified
stock.

### Clean install

Requires [platform-tools](https://developer.android.com/tools/releases/platform-tools)
(adb and fastboot). Download both 7z parts and extract them: on Windows, open the `.001`
file with 7-Zip; on Linux, run `7z x lunaris-1.0-tb371fc.7z.001`. Open a terminal in
the extracted `lunaris-1.0-tb371fc` folder.

Boot into the bootloader (`adb reboot bootloader`, or Volume Down + Power with the tablet
off) and run:

```
fastboot getvar unlocked
fastboot --set-active=a
fastboot --slot=a flash boot boot.img
fastboot --slot=a flash dtbo dtbo.img
fastboot --slot=a flash recovery recovery.img
fastboot --slot=a flash vbmeta vbmeta.img
fastboot reboot fastboot
```

`getvar unlocked` must return `yes`. The last command reboots into fastbootd ("fastbootd"
is shown on screen). Continue with:

```
fastboot snapshot-update cancel
fastboot delete-logical-partition product_a
fastboot delete-logical-partition system_ext_a
fastboot delete-logical-partition product_b
fastboot delete-logical-partition system_ext_b
fastboot delete-logical-partition system_a-cow
fastboot delete-logical-partition vendor_a-cow
fastboot --slot=a flash vendor vendor.img
fastboot --slot=a flash odm odm.img
fastboot --slot=a flash system lunaris.img
fastboot --slot=a flash vbmeta_system vbmeta_system.img
fastboot -w
fastboot reboot
```

Errors from `snapshot-update cancel` and `delete-logical-partition` only mean there was
nothing to cancel or delete, and can be ignored. The first boot takes a few minutes. The
orange "unlocked" warning at startup is normal.

### Updates

The Updater (Settings > System > System update) checks this repository for new versions.
Updates install to the other slot in the background, and a reboot prompt appears when
done. Data, apps and a Magisk-patched boot are kept.

Manual update (data kept): reboot into fastbootd (`adb reboot fastboot`) and run:

```
fastboot flash system lunaris.img
fastboot flash vbmeta_system vbmeta_system.img
fastboot reboot
```

Right after an OTA, wait a few minutes after the first boot before flashing manually.
The update is still being finalized in the background.

### If it does not boot

From the bootloader, run:

```
fastboot --disable-verity --disable-verification flash vbmeta vbmeta.img
fastboot reboot
```

If it still does not boot, do a clean install. To return to ZUI, flash the full stock
firmware.

### Source

`lunaris-1.0-source.tar.gz` contains the build scripts, patches, overlays and
translations. Signing keys are not included, so self-built images are signed with
different keys and cannot be used as updates for this release.

### Credits

- Lunaris AOSP team: the system
- lenovo-sm8650 (lapis) device tree: reference for the Lenovo pen and charging features

## 한국어

TB371fc용 롬이며 대부분의 기능들은 정상작동 합니다.
혹시 안되거나 하는거 있으면 알려주세요.

개인 빌드이며 Lenovo, Lunaris 팀과는 무관합니다.

### 설치 전 확인

- **부트로더 언락 필수.** 잠긴 상태에서는 설치할 수 없고, 언락하면 기기 데이터가 모두
  초기화됩니다.
- 이 롬 사용 중에는 **부트로더를 다시 잠그지 마세요.** 부팅되지 않습니다.
- **DRM은 Widevine L3로 내려갑니다.** 언락된 기기에는 L1이 적용되지 않아 넷플릭스 등은 SD
  화질로 재생되며, 실행되지 않는 앱도 있습니다.
- 클린 설치 시 데이터가 모두 삭제되니 미리 백업하세요.
- 언락이 필요한 사람은 https://github.com/MlgmXyysd/lenovoubl 툴로 SN파일 만들고 언락법 검색해서 하시면 금방합니다.

### 릴리스 파일

| 파일 | 설명 |
| --- | --- |
| `lunaris-1.0-tb371fc.7z.001`, `.002` | 클린 설치용 전체 이미지 (7z 분할 압축) |
| `lunaris-tb371fc-<이전>-to-1.0.zip` | 이전 버전에서 올리는 OTA 파일 (업데이터용) |
| `TB371FC-*.json` | 업데이터용 업데이트 정보 |
| `lunaris-1.0-source.tar.gz` | 빌드 스크립트 및 소스 (서명 키 제외) |
| `SHA256SUMS.txt` | 릴리스 파일과 7z 내부 이미지의 체크섬 |

7z에는 `lunaris.img`(system), `vbmeta_system.img`와 ZUI_16.0.474 순정 `boot.img`,
`dtbo.img`, `recovery.img`, `odm.img`가 들어 있습니다. `vendor.img`는 순정 vendor에서
Widevine만 L3로 바꾼 것이고, `vbmeta.img`는 이에 맞춰 다시 서명한 것입니다. `boot.img`는 순정
그대로입니다.

### 클린 설치

[platform-tools](https://developer.android.com/tools/releases/platform-tools)(adb, fastboot)가
필요합니다. 7z 파일 두 개를 모두 받아 압축을 풉니다. 윈도우는 7-Zip으로 `.001` 파일을 열고,
리눅스는 `7z x lunaris-1.0-tb371fc.7z.001`을 실행하면 됩니다. 압축을 푼
`lunaris-1.0-tb371fc` 폴더에서 터미널을 엽니다.

부트로더 모드로 진입한 뒤(`adb reboot bootloader` 또는 전원이 꺼진 상태에서 볼륨 아래 + 전원)
다음 명령을 실행합니다.

```
fastboot getvar unlocked
fastboot --set-active=a
fastboot --slot=a flash boot boot.img
fastboot --slot=a flash dtbo dtbo.img
fastboot --slot=a flash recovery recovery.img
fastboot --slot=a flash vbmeta vbmeta.img
fastboot reboot fastboot
```

`getvar unlocked` 결과가 `yes`여야 합니다. 마지막 명령을 실행하면 fastbootd(화면에 fastbootd
표시)로 재시작됩니다. 이어서 다음을 실행합니다.

```
fastboot snapshot-update cancel
fastboot delete-logical-partition product_a
fastboot delete-logical-partition system_ext_a
fastboot delete-logical-partition product_b
fastboot delete-logical-partition system_ext_b
fastboot delete-logical-partition system_a-cow
fastboot delete-logical-partition vendor_a-cow
fastboot --slot=a flash vendor vendor.img
fastboot --slot=a flash odm odm.img
fastboot --slot=a flash system lunaris.img
fastboot --slot=a flash vbmeta_system vbmeta_system.img
fastboot -w
fastboot reboot
```

`snapshot-update cancel`, `delete-logical-partition`에서 나오는 오류는 취소하거나 삭제할 대상이
없다는 뜻이므로 무시해도 됩니다. 첫 부팅은 몇 분 정도 걸리며, 부팅 시 나오는 주황색 언락 경고는
정상입니다.

### 업데이트

업데이터(설정 > 시스템 > 시스템 업데이트)에서 이 저장소의 새 버전을 확인합니다. 업데이트는
백그라운드에서 다른 슬롯에 설치되고, 완료되면 재부팅 알림이 표시됩니다. 데이터, 앱, Magisk로
패치한 boot는 유지됩니다.

수동 업데이트(데이터 유지)는 fastbootd로 재시작(`adb reboot fastboot`)한 뒤 다음을 실행합니다.

```
fastboot flash system lunaris.img
fastboot flash vbmeta_system vbmeta_system.img
fastboot reboot
```

OTA 직후에는 첫 부팅 후 몇 분 정도 기다렸다가 수동 설치를 진행하세요. 백그라운드에서 업데이트
마무리 작업이 진행됩니다.

### 부팅되지 않을 때

부트로더에서 다음을 실행합니다.

```
fastboot --disable-verity --disable-verification flash vbmeta vbmeta.img
fastboot reboot
```

그래도 부팅되지 않으면 클린 설치를 진행하세요. ZUI로 돌아가려면 순정 펌웨어 전체를 설치하면
됩니다.

### 소스

`lunaris-1.0-source.tar.gz`에는 빌드 스크립트, 패치, 오버레이, 번역이 들어 있습니다. 서명
키는 포함되어 있지 않아, 직접 빌드한 이미지는 다른 키로 서명되며 이 릴리스의 업데이트로 사용할
수 없습니다.

### 크레딧

- Lunaris AOSP 팀: 시스템
- lenovo-sm8650(lapis) 디바이스 트리: Lenovo 펜, 충전 기능 참고
