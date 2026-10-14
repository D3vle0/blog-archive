---
title: "Top 5 macOS productivity apps"
date: 2022-05-11T22:33:44+09:00
draft: false
category: "macOS"
---

## 서문

macOS, 큰 마음 먹고 맥북을 구매했으나 적응하지 못해 되파는 경우도 있을 정도로 Windows에 비해 사용하기 어렵고 unix 기반 운영체제 작동 방식을 이해해야 하는 운영체제다. 조금 더 쉽고 똑똑하게 macOS를 사용하기 위하여 필자가 2021년 2월부터 m1 맥북을 사용해 오면서 경험한 다양한 생산성 프로그램을 소개해보려고 한다.

## WineSkin (무료)

![img](img/top5-utilities-mac/1.png)

WineSkin이란 윈도우 exe 파일을 맥에서 구동할 수 있게 해주는 프로그램이다. 사용 방법은 [이곳을 참고](https://m.blog.naver.com/erke2000/220159821199)하면 되겠다. 필자는 디스크 이미지 분석 포렌식 툴인 `FTK Imager`를 WineSkin에서 구동하고 있다. 물론 맥에서 FTK Imager가 존재하긴 하지만 cli 기반이라 사용하기 매우 불편하다.

![img](img/top5-utilities-mac/2.png)

FTP Imager가 잘 구동된다.

## RDM (무료)

맥에서 해상도를 쉽게 조절할 수 있는 프로그램이다.

![img](img/top5-utilities-mac/3.png)

그냥 겉보기에는 EasyRes 등 여러 유틸리티와 차이가 없어보이지만 RDM에는 중요한 기능이 있다. 바로 내가 원하는 해상도에서 **Retina를 활성화 시킬 수 있다!** EasyRes에서 13인치 맥북 native resolution인 2560*1600으로 바꾸면 글자가 매우 흐리게 나온다. 그러나 RDM에서는 아래와 같이 해상도를 입력하고 Retina, HiDPI, Unknown1 체크 박스에 체크를 해주면 내가 원하는 해상도에서 Retina를 사용할 수 있게 된다. 이에 따라 시스템 설정의 디스플레이 항목은 해상도 조절을 할 수 있도록 바뀐다.

![img](img/top5-utilities-mac/4.png)

![img](img/top5-utilities-mac/5.png)

일반 해상도와 Retina가 적용된 해상도 차이를 해보면 아래와 같다.

![img](img/top5-utilities-mac/6.png)

![img](img/top5-utilities-mac/7.png)

[RDM 다운로드](https://github.com/avibrazil/RDM)

## Multitouch

트랙패드에서 더 많은 제스처를 할당할 수 있는 프로그램이다. 단 시스템 기본 제스처랑 겹치치 않도록 주의해야 한다. 필자의 경우에는 아래와 같은 세팅으로 밝기, 소리, 창 전환, 캡쳐를 쉽게 할 수 있도록 했다.

![img](img/top5-utilities-mac/8.png)

[Multitouch 다운로드 (30일 트라이얼, 영구 라이선스는 19466원)](https://multitouch.app/)

## ClipboardManager

클립보드에 복사한 기록을 볼 수 있는 프로그램이다. 윈도우에서 `Windows + V`의 상위호환 느낌. 필자는 `command + shift + v`을 누르면 클립보드 히스토리가 나오게 했다.

![img](img/top5-utilities-mac/9.png)

![img](img/top5-utilities-mac/10.png)

복사하고 싶은 내용에 커서를 갖다 대고 트랙패드를 오른쪽으로 밀면 복사가 된다.

[ClipboardManager 다운로드 (3900원)](https://apps.apple.com/kr/app/clipboard-manager/id1116697975?l=en&mt=12)

## Memory Clean 3

![img](img/top5-utilities-mac/11.png)

메모리 최적화 프로그램. activity monitor에서 memory pressure가 노란색인 것을 확인하면 바로 메모리 클린을 돌려주자. 지금 실행중인 프로그램만 20개가 되는데 클린을 돌려주면?

![img](img/top5-utilities-mac/12.png)

편안해진다.

[Memory Clean 3 다운로드 (트라이얼, 영구 라이선스는 12973원)](https://fiplab.com/apps/memory-clean-3-for-mac)
