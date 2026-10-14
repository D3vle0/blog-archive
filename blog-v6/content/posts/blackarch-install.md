---
title: "Install BlackArch Linux in m1 mac"
date: 2022-04-10T20:01:40+09:00
draft: false
tags: ["linux"]
---

## What is BlackArch Linux?

BlackArch Linux란 해커 또는 pentester들을 위한 리눅스 distro로 Arch Linux를 기반으로 하고 있으며, 주로 Kali Linux의 비교 대상이다.

## Kali Linux vs BlackArch Linux

### Kali Linux

- 사용하기 쉽다
- 400개의 해킹 툴 포함
- debian 기반
- 설치가 쉽다

### BlackArch Linux

- 사용자 친화적이지 못함
- 많은 리눅스 사용 경험 필요
- 2300개 이상의 해킹 툴 포함
- 설치하기 어렵다

한가지 특이한 점이 있다면, 기존의 Arch Linux 사용자들은 `pacman -S blackarch` 명령으로 해킹 툴을 다운받을 수 있다.

## Installation

완전한 cli 환경에서 모든 작업을 수행해야 하는 Arch Linux와는 다르게 다행히 BlackArch Linux에서는 `blackarch-install` 명령을 통해 (물론 cli 환경이긴 하지만) 쉽게 설치할 수 있다.
[이곳에서 BlackArch Linux iso 미러 사이트를 제공해주는데,](https://blackarch.org/downloads.html) 한국에서 가장 가까운 중국 미러를 이용하자. 속도가 매우 빨라서 18GB나 되는 파일이지만 금방 다운로드 받을 수 있다.  

m1 맥에서 qemu 기반으로 가상 머신을 돌릴 수 있는 UTM 이라는 프로그램을 이용하겠다. [이곳에서 UTM을 다운받고 실행한다.](https://mac.getutm.app/)

![img](/blackarch-install/1.png)

**새 가상머신 만들기**를 누르고 emulate를 선택한다. 운영체제는 리눅스, boot ISO image 선택 버튼을 눌러 앞서 다운받은 BlackArch Linux 이미지를 선택한다.

![img](/blackarch-install/2.png)

필자는 위와 같이 2코어 1기가 사양으로 할당했고, 다음으로 넘어간다. 스토리지는 적당히 64기가 정도로 잡겠다.

![img](/blackarch-install/3.png)

첫번째 옵션을 선택하면 부팅이 진행되면서 live os 환경이 시작된다. 놀랍게도 이 상태에서 모든 해킹 툴을 사용해볼 수 있고 firefox와 chromium도 내장되어 있다. 아무런 로그를 남기지 않게 하고 싶거나, 미니멀한 환경을 좋아하는 해커에게는 괜찮은 설계라고 생각된다. 이러한 이유로 다른 distro와 다르게 초기 iso 파일 용량이 꽤 큰 것이라고 생각할 수 있겠다.

![img](/blackarch-install/4.png)

유저는 `root`, 비번은 `blackarch`를 입력하면 된다.

![img](/blackarch-install/5.png)

상당히 미니멀하다. fluxbox wm 환경에서 돌아가는 모습을 확인할 수 있다. 마우스 우클릭으로 터미널을 열고 `blackarch-install`을 입력한다.  

와이파이가 연결된 상태에서 차례대로 1,2,1,엔터,1,엔터,호스트 이름 입력,1,화면에 보이는 네트워크 인터페이스 입력,y,sda,n,y,y,gpt 선택을 차례로 하면 아래의 화면이 나온다.

![img](/blackarch-install/6.png)

여기서 아래의 과정을 따른다.  

1. New 선택
2. 512M 입력
3. Type 선택
4. EFI System 선택
5. Free Space로 이동한 후 New 선택
6. 2G 입력
7. Type 선택
8. Linux swap 선택
9. Free Space로 이동한 후 New 선택
10. 엔터
11. Type 선택
12. Linux filesystem 선택
13. Write 선택
14. yes 입력
15. Quit 선택

![img](/blackarch-install/7.png)

여기서부터 y,`/dev/sda1`,`ext4`,`/dev/sda3`,`ext4`,`/dev/sda2`,y,y를 누르면 아래의 화면이 나온다.

![img](/blackarch-install/8.png)

누군가가 이 가상머신 디스크를 추출해가도 내용을 확인할 수 없도록 LUKS 암호화를 거는 것인데 암호화 할때 사용할 비밀번호를 입력하는 것이다. 나만의 비밀번호를 입력하고 verify 후 넘어간다.
