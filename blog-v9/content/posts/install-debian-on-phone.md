---
title: "스마트폰에 Debian 설치하기"
date: 2025-06-16T19:53:00+09:00
draft: false
categories: ["Server"]
tags: ["server", "debian", "linux", "android", "busybox"]
cover:
  image: img/install-debian-on-phone/6.png
  caption: ""
ShowToc: true
TocOpen: true
---

> 23.11.28에 작성된 글입니다.

## 준비물

- Android 스마트폰
- PC
- 케이블

## 주의사항

가이드를 정확하게 따라오지 않으면 **스마트폰이 부팅되지 않는 벽돌현상**이 발생할 수 있다.  

반드시 스마트폰의 **모든 데이터를 백업한 후** 진행해야 한다. 필자는 손실된 데이터에 대해 책임지지 않는다.  

삼성 스마트폰 사용자라면 삼성 KNOX 워런티가 깨진다. 순정으로 복구해도 삼성 헬스, 삼성 페이 등 KNOX 관련 앱들을 **영구적으로 사용할 수 없게 된다.  

## 스마트폰을 가볍게

이 작업은 선택사항이지만 더 가벼운 환경에서 서버를 사용하고 싶으면 진행할 것을 권장한다. 삼성의 OneUI, 샤오미의 MIUI (현 HyperOS), Oppo의 ColorOS 등의 사용자 인터페이스는 각 제조사 별 자체 기능과 개성을 더하기 위해 순정 Android를 개조하여 만든 것인데, 이 때문에 배터리가 빨리 닳거나 느려지는 현상이 발생할 수 있다.  

따라서 AOSP(Android Open Source Project)를 기반으로 하는 커스텀 롬을 사용하는 것이 좋은데, 구글 서비스가 완전히 삭제된 LineageOS (바닐라 빌드 버전), GrapheneOS를 추천하고 만약 호환되지 않는 기기라면 XDA에서 Unofficial 빌드를 찾아보거나 PixelExperience를 설치하는 것을 추천한다. 이 문서에서는 LineageOS를 기준으로 설명하며, 커스텀 롬을 플래시 하는 방식은 LineageOS 이외에 다른 펌웨어도 동일하다.

### LineageOS 설치

1. [이곳에서 자신의 기기에 맞는 LineageOS를 다운로드 한다.](https://download.lineageos.org/changes) 
2. [이곳에서 자신의 기기에 맞는 TWRP를 다운로드 한다.](https://twrp.me/Devices/)
3. [이곳에서 PC의 운영체제에 맞는 Android SDK 플랫폼 도구(adb와 fastboot가 포함됨)를 다운로드 한다.](https://developer.android.com/studio/releases/platform-tools?hl=ko)
4. [이곳에서 Magisk zip 파일을 다운로드 한다.](https://download.magiskzip.com/download/magisk-zip-v26-4/)
5. [이곳에서 Root Checker APK 파일을 다운로드 한다.](https://root-checker.en.uptodown.com/android)
6. [이곳에서 Linux Deploy APK 파일을 다운로드 한다.](https://linux-deploy.kr.uptodown.com/android)
7. [이곳에서 Busybox APK 파일을 다운로드 한다.](https://busybox.kr.uptodown.com/android)
8. 스마트폰의 개발자 설정에 들어가서 USB 디버깅 설정을 켠다.
9. PC와 스마트폰을 연결 후 USB 디버깅 권한 팝업에서 허용을 누른다.
10. 스마트폰의 전원을 끈 후 `전원+볼륨 아래` 버튼을 길게 눌러 fastboot 모드에 진입한다.

### LineageOS 플래시

터미널을 실행하고 다음과 같은 명령어를 입력한다.

```sh
brew install --cask android-platform-tools
fastboot boot <twrp 이미지 경로>
```

만약 TWRP로 부팅이 안된다면 전원을 길게 눌러 강제 종료한 뒤 fastboot 모드에서 다음 명령어를 입력한다.

```sh
.\fastboot flash recovery <twrp 이미지 경로>
```

![](/img/install-debian-on-phone/1.png)

TWRP 진입 후 Unmodified System Partition 알림이 뜨면 하단의 슬라이드 바를 슬라이드 한다.
![](/img/install-debian-on-phone/2.png)

![](/img/install-debian-on-phone/3.png)

Wipe > Advanced Wipe 진입, `Dalvik/ART Cache`, `System`, `Cache`, `data`, `Vendor`, `Internal Storage` 체크 후 포맷  

![](/img/install-debian-on-phone/4.png)

이러한 에러 메세지가 나와도 무시한다.  

![](/img/install-debian-on-phone/5.png)

Wipe > Format Data 진입, yes 입력 후 포맷  

```sh
adb push <LineageOS 경로> /data/rom.zip
adb push <magisk 경로> /data/magisk.zip
```

Install > data 디렉토리 진입, rom.zip, magisk.zip 파일 선택 후 install

## 루팅

먼저 재부팅 한 후 USB 디버깅을 활성화한 뒤, APK 파일들을 스마트폰으로 옮겨준다.

```sh
adb push <root checker 경로> /sdcard/rootchecker.apk
adb push <Linux Deploy 경로> /sdcard/linuxdeploy.apk
adb push <Busybox 경로> /sdcard/busybox.apk
```

![](/img/install-debian-on-phone/6.png)

재부팅을 한번 더 하면 앱 서랍에 Magisk가 나올 것이다.

![](/img/install-debian-on-phone/7.png)

Magisk 아이콘을 눌러 설치하자.

![](/img/install-debian-on-phone/8.png)

첫 실행시 OK를 눌러 재부팅한다.

![](/img/install-debian-on-phone/9.png)

재부팅 후 다시 Magisk를 실행하여 나오는 팝업에 OK를 누른다.

![](/img/install-debian-on-phone/10.png)

Magisk 글자 옆 Install 버튼을 누르고, Option 옆 Next를 누르고, Method는 Direct Install을 선택 후 Let's go를 누른다.

![](/img/install-debian-on-phone/11.png)

설치 완료.

![](/img/install-debian-on-phone/12.png)

스마트폰으로 옮긴 APK 파일들을 모두 설치해준다.

![](/img/install-debian-on-phone/13.png)

Root Check 앱에서 Verify Boot 버튼을 누른다.

![](/img/install-debian-on-phone/14.png)

Forever 선택 후 Grant

![](/img/install-debian-on-phone/15.png)

체크 표시가 뜨면 성공적으로 루팅 작업에 성공한 것이다.

## Busybox 설치

![](/img/install-debian-on-phone/16.png)

Busybox를 실행하고 root 권한을 부여한다.

![](/img/install-debian-on-phone/17.png)

설치 경로를 `/system/bin` 으로 선택 후 Install 한다.

![](/img/install-debian-on-phone/18.png)

설치 완료.

## Linux Deploy 설치

![](/img/install-debian-on-phone/19.png)

화면 하단의 설정 버튼을 누른다.

![](/img/install-debian-on-phone/20.png)

Distribution은 `Debian`, Username을 원하는 닉네임으로 변경하고 User Password에서 비밀번호도 설정한다.

![](/img/install-debian-on-phone/21.png)

init enable 체크, ssh enable 체크, ssh는 원하는 포트를 지정한다.

![](/img/install-debian-on-phone/22.png)

Linux Deploy 설정에서 Lock screen, Lock Wifi, Wake Lock을 체크한다.

![](/img/install-debian-on-phone/23.png)

화면 상단 점 세개 클릭, Install을 눌러 Debian을 설치한다.

![](/img/install-debian-on-phone/24.png)

`<<< deploy` 글자가 보일때 까지 기다리고, 설치가 끝나면 start 버튼을 눌러 서버를 시작한다.

![](/img/install-debian-on-phone/25.png)
