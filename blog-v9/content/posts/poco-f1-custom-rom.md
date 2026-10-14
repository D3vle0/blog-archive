---
title: "📱 Pocophone F1 커스텀 롬 설치 가이드"
date: 2025-07-11
draft: false
categories: ["Android"]
tags: ["custom rom", "pocophone"]
cover:
  image: img/poco-f1-custom-rom/2.png
  caption: ""
ShowToc: true
TocOpen: true
---

> 2021.03.05. 작성된 글입니다.

## 1. 설치 전 주의사항

### 1.1 서문
📢 본 노션 페이지는 Xiaomi Pocophone F1에 Android Custom ROM을 설치하는 방법에 대해 쉽게 기술한 페이지입니다. Poco F1 뿐만 아니라 다른 Xiaomi 스마트폰에도 비슷하게 적용될 수 있으나 필자는 Poco F1 사용자로, 해당 스마트폰에 최적화된 방법에 대해 다루고 있습니다.


### 1.2 주의사항
⚠️  Custom ROM 작업을 하다가 "벽돌" 상태가 되거나, 스마트폰이 정상적으로 작동하지 않는 상황에 대해 책임을 지지 않으며, warranty void가 됨을 미리 알려드립니다.


## 2. Poco F1을 위한 Custom ROM 추천

- 순정: miui ([다운로드 링크](https://xiaomifirmwareupdater.com/archive/miui/beryllium/))
    - miui 12
    - miui 11
    - miui 10
- pixel experience ([다운로드 링크](https://download.pixelexperience.org/beryllium))
    - android 11 based
    - android 10 based
- Lineage OS
- Evolution X

## 3. Xiaomi Unlock

[Apply for unlocking Mi devices](https://en.miui.com/unlock/)

이곳에서 Xiaomi 기기를 언락 툴을 다운받고 언락 절차를 진행하면 72시간 내에 완료됩니다.

## 4. 설치할 파일

### 4.1 Custom ROM 파일

2번에 링크 첨부했습니다.

### 4.2 adb

adb란 Android Debug Bridge 의 약자로 안드로이드 기기에서 디버깅 등의 작업을 할 수 있는 툴입니다.

- [Windows adb](https://dl.google.com/android/repository/platform-tools-latest-windows.zip?hl=ko)
- [Mac OS adb](https://dl.google.com/android/repository/platform-tools-latest-windows.zip?hl=ko)
- [Linux adb](https://dl.google.com/android/repository/platform-tools-latest-windows.zip?hl=ko)

### 4.3 TWRP

TWRP (Team Win Recovery Project) 는 안드로이드 기반 기기들을 위한 오픈 소스 소프트웨어 커스텀 복구 이미지입니다. (출처 위키백과)  TWRP 에서 기존의 롬을 새로운 커스텀 롬으로 변경시킬 수 있습니다.

[다운로드 링크](https://dl.twrp.me/beryllium/)

### 4.4 DisableForceEncryption

TWRP에 진입할 때, 시스템으로 부팅할 때 사용자가 지정한 패턴을 요구하는데 이때 생기는 오류를 방지하기 위해서 커스텀 롬과 함께 플래시 합니다.

[다운로드 링크](https://drive.google.com/file/d/1xsnBJoQA2t5_EMrY7-7mnh8XRnJbCwa5/view?usp=sharing)

## 5. Custom ROM 설치

⚠️ 이 과정을 진행하기 전에, 초보자분들은 반드시 내장 메모리의 중요 데이터를 백업하시고, SD 카드를 제거 후 진행하시기 바랍니다. 손실된 데이터에 대해 책임지지 않습니다.


### 5.1 usb 디버깅

1. 컴퓨터와 스마트폰을 케이블로 연결 후, 프로토콜을 "파일 전송 프로토콜" 로 설정해주세요. 
2. "휴대전화 정보" 에서 "빌드 번호" 를 연타하면 "개발자 도구" 메뉴가 활성화됩니다.
3. "개발자 도구" 에서 "usb 디버깅" 옵션을 켜주세요.
4. adb 폴더 내에서 아래와 같이 명령어를 입력합니다.

```bash
[Windows]
adb devices

[Mac]
./adb devices
```

그러면 스마트폰에서 이 컴퓨터에 대해 usb 디버깅을 허용할것인지 묻는데 확인을 눌러줍니다.

adb에서 스마트폰이 잘 인식되는지 확인합니다.

![](/img/poco-f1-custom-rom/1.png)
### 5.2 fastboot 진입

스마트폰의 전원을 끄고 "전원 + 볼륨 아래" 를 길게 누르면 fastboot 모드로 진입합니다. 

```bash
[Windows]
fastboot devices

[Mac]
./fastboot devices
```

fastboot 상태의 스마트폰이 잘 인식되는지 확인합니다. 만약 인식되는 장치가 없다면 5.2.1을 참고하세요.

### 5.2.1 fastboot 에서 device가 잡히지 않는 오류 (windows)

![](/img/poco-f1-custom-rom/2.png)

"장치 관리자" 에서 "기타 장치" 를 클릭하면 안드로이드 기기 항목에 느낌표가 떠있을겁니다. 드라이버를 최신 상태로 업데이트 해주어야 하는데, [이곳에서 최신 드라이버를 다운받으세요](https://drive.google.com/file/d/11xcwy925qaYO0JPw8NXvA8DOEkbuNoT7/view?usp=sharing).

자세한 드라이버 업데이트 방법은 아래 링크를 참고해주세요.

[Fixed: Fastboot not detecting your Android device](https://www.qasimtricks.com/2020/04/fixed-fastboot-not-detecting-your-android-device.html)

### 5.3 TWRP 진입

### 5.3.1 TWRP 플래시

위에서 다운로드 받았던 twrp 파일을 fastboot을 통해 플래시 해주어야합니다.

adb와 fastboot 바이너리가 있는 platform-tools 경로 안에 다운받은 파일들을 옮겨놓으면 편합니다.

```bash
[Windows]
fastboot boot twrp*.img

[Mac]
./fastboot boot twrp*.img
```

### 5.3.2 adb push

휴대폰이 TWRP로 부팅한 것이 확인되었다면, 컴퓨터로 다운받은 각종 파일들을 휴대폰으로 넣어줘야합니다.

작업하기 수월하도록 내장 메모리의 `Download` 폴더에 넣어보겠습니다.

```bash
[Windows]
adb push <파일> sdcard/Download

[Mac]
./adb push <파일> sdcard/Download
```

위의 명령어로 커스텀 롬, DisableForceEncryption 파일을 넣어줍니다.

### 5.3.3 wipe

TWRP 메인 화면에서 Wipe > Advanced Wipe 에 들어가서 아래 항목에 체크해주세요.

- Dalvik / ART Cache
- Cache
- System
- Vendor

슬라이더를 오른쪽으로 밀어서 초기화를 진행합니다.

로그에 `...done` 이 출력된 것을 보고 홈키를 눌러 다음 단계로 넘어갑니다.

### 5.3.4 Custom ROM 플래시

TWRP 홈에서 Install > `/sdcard/Download` 경로에 들어가서 다운로드 받은 커스텀 롬 파일을 클릭합니다. 아무것도 체크하지 마시고, 슬라이더를 오른쪽으로 밀어 플래시를 진행합니다.

아무런 에러가 나지 않았다면 [5.3.5](https://www.notion.so/Pocophone-F1-44e30de3dedd42e1a09189abcc2edb03?pvs=21) 로 진행해주시고, 에러가 났다면 [5.3.4.1](https://www.notion.so/Pocophone-F1-44e30de3dedd42e1a09189abcc2edb03?pvs=21) 을 참고해주세요.

### 5.3.4.1 Zip Treble compatibility error

Zip Treble compatibility error 문구가 뜬다면, 다운받은 커스텀 롬 파일의 압축을 풀고, compatibility.zip을 삭제하고 다시 압축시켜줍니다.

```bash
[Windows]
adb push <파일> sdcard/Download

[Mac]
./adb push <파일> sdcard/Download
```

adb push를 이용해 수정한 커스텀 롬 파일을 휴대폰에 넣어줍니다.

### 5.3.5 DisableForceEncryption 플래시

TWRP 홈에서 Install > `/sdcard/Download` 경로에 들어가서 다운로드 받은 `DisableForceEncryption.zip` 파일을 클릭합니다. 아무것도 체크하지 마시고, 슬라이더를 오른쪽으로 밀어 플래시를 진행합니다.

### 5.4 시스템으로 진입

TWRP 홈에서 Reboot > System 을 클릭합니다.

## 6. 글을 마치며...

지금까지 miui 9, 10, 11, 12 / pixel experience android 10, pixel experience plus android 10, 11 을 사용해본 사용자로서 수없이도 다양한 에러를 겪어왔습니다. 기회가 된다면 각각의 롬과 버전에 따라 어떤 불편한 점이나 버그가 있는지, 그리고 벤치마킹 결과를 업데이트 하겠습니다.
