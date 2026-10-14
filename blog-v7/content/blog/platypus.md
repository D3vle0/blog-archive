---
title: "Bundling CLI app into Mac native app with Platypus + Custom URI Scheme"
date: 2022-05-26
draft: false
category: "macOS"
---

## 서문

웹브라우저에서 `file:///` 을 입력하면 로컬 파일을 볼 수 있고, `ftp://` 를 입력하면 ftp 서버에 연결할 수 있고, safari에서 `http://` 를 입력하면 localhost로 연결된다. 이 외에도 컴퓨터에 steam이 설치되어 있다면 `steam://` 프로토콜을 통해 steam 프로그램을 열 수 있고, firefox 브라우저는 `moz://` 프로토콜을 가지고 있다. 이렇게 특정 프로그램을 간편하게 조작하고 접근하기 위해 커스텀 프로토콜을 만드는 경우가 있다. 이 글에서는 macOS에서 커스텀 프로토콜을 만드는 방법에 대해 서술하겠다.

## Platypus

[Platypus 라는 프로그램이 있다.](https://sveinbjorn.org/platypus) Platypus는 터미널 상에서만 작동하는 cli 프로그램을 맥 네이티브 프로그램으로 변환해주는 툴이다. 쉘 스크립트 (sh, bash, tsh, tcsh, ksh, zsh, env, applescript) 는 물론이고 심지어 Python, Ruby, Tcl, Expect, PHP, Swift, AWK, JavaScript, node.js 프로그램도 지원한다.

![img](img/platypus/1.png)

실행 후 첫 화면이다. `print("Hello, World!")` 이 적힌 파이썬 파일을 올려보겠다.

![img](img/platypus/2.png)

현재 내 맥북에는 python이 `/usr/bin/python3`에 3.8.9버전, `/opt/homebrew/bin/python3`에 3.9.12가 설치되어 있어서 위 사진에 표시된 경로가 존재하지 않다고 빨갛게 표시된다. `/opt/homebrew/bin/python3`로 변경하겠다.

![img](img/platypus/3.png)

앱 이름 지정, 앱 아이콘은 원하는 사진으로 지정하고, 중간에 `Accept dropped items`를 체크하면 표시되는 Settings를 누른다.

![img](img/platypus/4.png)

오른쪽 아래 `Register as URI scheme handler` 를 체크하고 원하는 프로토콜을 작성한다.

![img](img/platypus/5.png)

Apply 하고 create app 버튼을 누르자.

![img](img/platypus/6.png)

Application 디렉토리에 넣으면 launchpad에서도 확인할 수 있다.

![img](img/platypus/7.png)

이렇게 Hello, World!가 제대로 출력되는 것을 볼 수 있다.

## Custom URI Scheme 사용

터미널에서 `open <프로토콜>://` 을 입력하거나, 브라우저에서 `<프로토콜>://`를 입력하면 된다. 일부 브라우저 (firefox) 에 한해서 `<프로토콜>:///`를 입력해야 할 수도 있다.

![img](img/platypus/8.png)

정상적으로 프로그램이 실행된다.

## 추후 공부할 것

Platypus를 사용하여 나만의 URI scheme을 만들면 단점이 변환된 네이티브 앱이 켜져있는 동안에만 다른 작업을 수행할 수 있다. 무슨 뜻이냐면 예를 들어 `tidal://` uri scheme을 만들어 `tidal://` 을 입력하면 tidal 음악 스트리밍 프로그램이 켜지도록 할 수 있는데, tidal이 켜져있는 동안에 비어있는 흰 터미널 창이 켜져있어야 한다는 것이다. 추후 applescript를 사용하여 정말로 나만의 프로토콜을 만들어보겠다.
