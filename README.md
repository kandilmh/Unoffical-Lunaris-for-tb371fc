# Lunaris for the Lenovo TB371FC (unofficial)

TB371fc용 롬이며 대부분의 기능들은 정상작동 합니다. 혹시 안되거나 하는거 있으면 알려주세요.
- AOD기능을 구현해뒀습니다 배터리타임을 위해 30프레임 고정, cpu제한을 걸어둬서 어느정도 대기가 가능하도록 구현했습니다. LCD 특성상 당연히 배터리가 빨리 줄어듭니다 
- AOD진입시 터치 칩셋이 초기화되지 않기때문에 6시간 마다 1.5초정도 화면이 실제로 꺼져서 터치 칩셋을 초기화 합니다. (켜두신다면 보통 자는시간에 초기화 될겁니다)
- 펜기능을 구현해뒀습니다 btb131말고 ap501 / 501u도 네이티브로 사용 가능하며 화면이 꺼지고 1분뒤 펜이 자동으로 절전모드로 진입하도록 해서 대기상태에서 배터리가 빨리 닳는것을 줄였습니다.
- 펜 호버, 팜리젝션, 실수로인한 터치방지 옵션 전부다 정상작동 합니다.

Lunaris AOSP 3.12(Android 16, 구글 앱 포함)를 Lenovo Xiaoxin Pad Pro 12.7(TB371FC,
Snapdragon 870)에 이식한 롬입니다. ZUI_16.0.474 순정 vendor 위에서 동작합니다.

- 현재 확인된 문제 디스플레이 터치가 순간 안먹는 경우가 간혈적으로 발생합니다 화면을 껏다 켜거나 재부팅, 펜이 있다면 팬을 사용하여 AOD 끄고 화면 잠궜다 풀면 풀립니다 현재 원인 파악중에 있으며 주 발생환경은 충전중 영상시청시 주로 발생 했습니다

개인 빌드이며 Lenovo, Lunaris 팀과는 무관합니다.

## 설치 전 확인

- **부트로더 언락 필수.** 잠긴 상태에서는 설치할 수 없고, 언락하면 기기 데이터가 모두
  초기화됩니다.
- 이 롬 사용 중에는 **부트로더를 다시 잠그지 마세요.** 부팅되지 않습니다.
- **DRM은 Widevine L3로 내려갑니다.** 언락된 기기에는 L1이 적용되지 않아 넷플릭스 등은 SD
  화질로 재생되며, 실행되지 않는 앱도 있습니다.
- 클린 설치 시 데이터가 모두 삭제되니 미리 백업하세요.
- 언락이 필요한 사람은 https://github.com/MlgmXyysd/lenovoubl 툴로 SN파일 만들고 언락법 검색해서 하시면 금방합니다.

## 설치 (윈도우)

1. 최신 릴리스에서 `lunaris-1.2-tb371fc.7z.001`, `.002`를 받아 같은 폴더에 둡니다.
2. 7-Zip으로 `.001` 파일을 열어 압축을 풉니다.
3. 태블릿을 USB로 연결합니다. 안드로이드(USB 디버깅 켜짐), 부트로더, fastbootd 어느 상태든
   됩니다.
4. 압축을 푼 폴더의 `install_lunaris_1.2.bat`를 실행하고 안내에 따라 `Y`를 누릅니다.

adb, fastboot(platform-tools)가 폴더에 없으면 스크립트가 구글 공식 주소에서 받아 옵니다.
첫 부팅은 몇 분 정도 걸리며, 부팅 시 나오는 주황색 언락 경고는 정상입니다.

## 수동 설치

스크립트를 쓸 수 없을 때는 압축을 푼 폴더의 `manual_install.txt`에 있는 명령을 차례로
실행합니다. 리눅스는 `7z x lunaris-1.2-tb371fc.7z.001`로 압축을 풀면 됩니다.

부트로더(`adb reboot bootloader`)에서:

```
fastboot --set-active=a
fastboot --slot=a flash boot boot.img
fastboot --slot=a flash dtbo dtbo.img
fastboot --slot=a flash recovery recovery.img
fastboot --slot=a flash vbmeta vbmeta.img
fastboot reboot fastboot
```

fastbootd(화면에 fastbootd 표시)에서:

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

`snapshot-update cancel`, `delete-logical-partition`에서 나오는 오류는 지울 대상이 없다는
뜻이므로 무시해도 됩니다.

## 업데이트

설정 > 시스템 > 시스템 업데이트에서 새 버전을 받아 설치합니다. 데이터, 앱, Magisk로 패치한
boot는 유지됩니다.

이전 릴리스는 정리되어 있어, 바로 전 버전이 아니면 수동으로 업데이트합니다(데이터 유지).

```
adb reboot fastboot
fastboot flash system lunaris.img
fastboot flash vbmeta_system vbmeta_system.img
fastboot reboot
```

OTA 직후에는 첫 부팅 후 몇 분 기다렸다가 수동 설치를 진행하세요.

## 부팅되지 않을 때

부트로더에서 다음을 실행합니다.

```
fastboot --disable-verity --disable-verification flash vbmeta vbmeta.img
fastboot reboot
```

그래도 부팅되지 않으면 클린 설치를 진행하세요. ZUI로 돌아가려면 순정 펌웨어 전체를 설치하면
됩니다.

## 릴리스 파일

| 파일 | 설명 |
| --- | --- |
| `lunaris-1.2-tb371fc.7z.001`, `.002` | 클린 설치용 이미지, 설치 스크립트, 수동 설치 명령 (7z 분할 압축) |
| `lunaris-tb371fc-<이전>-to-1.2.zip` | 바로 전 버전에서 올리는 OTA (업데이터용) |
| `lunaris-1.2-source.tar.gz` | 빌드 스크립트 및 소스 (서명 키 제외) |
| `SHA256SUMS.txt` | 릴리스 파일과 7z 내부 파일의 체크섬 |

7z의 `boot.img`, `dtbo.img`, `recovery.img`, `odm.img`는 ZUI_16.0.474 순정 그대로입니다.
`vendor.img`는 순정 vendor에서 Widevine만 L3로 바꾼 것이고, `vbmeta.img`는 이에 맞춰 다시
서명한 것입니다.

소스에는 서명 키가 없어, 직접 빌드한 이미지는 이 릴리스의 업데이트로 사용할 수 없습니다.

## 크레딧

- Lunaris AOSP 팀: 시스템
- lenovo-sm8650(lapis) 디바이스 트리: Lenovo 펜, 충전 기능 참고
