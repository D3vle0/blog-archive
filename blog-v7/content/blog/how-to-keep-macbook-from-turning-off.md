---
title: "How to keep macbook from turning off"
date: 2022-06-17T20:31:01+09:00
draft: false
category: "macOS"
---

맥북을 사용하다가 잠시 자리를 비우고 싶을 때 `cmd + control + q` 를 눌러 화면을 잠그는 경우가 있다. 그러나 이때 잠금 화면은 몇 초 동안만 켜져 있다가 화면이 꺼지는데, 화면이 꺼지지 않고 잠금 화면이 계속 켜져있도록 하고 싶을 수 있다. 또한 음악을 감상할 때는 화면을 덮어도 잠자기 모드로 들어가지 않게 하고 싶은 상황이 있을 수 있다. 이 두 상황을 해결할 수 있는 방법을 소개하겠다.

## Amphetamine

Amphetamine이란 맥북 화면이 꺼지지 않게 유지하는 프로그램으로 잘 알려져 있다. 몇 시간 몇 분 동안 화면이 꺼지지 않는 상태를 유지할 것인지, 특정 프로그램이 실행하고 있을 때 세션을 시작할 것인지 등 트리거 기능을 포함하여 매우 상세하게 설정이 가능하다. 만약 화면을 잠글 것이고 화면이 꺼지지 않게 유지하고 싶으면 Amphetamine에서 start new session을 하면 된다. 그러면 자리를 비우고 있어도 아름다운 잠금 화면이 유지가 될 것이다.

![a](img//how-to-keep-macbook-from-turning-off/1.png)

## 화면을 덮어도 꺼지지 않게 설정

```bash
sudo pmset -c disablesleep 1
```

화면을 덮어도 꺼지지 않게 설정하려면 disablesleep 값을 1로 하고,

```bash
sudo pmset -c disablesleep 0
```

다시 원상복구를 하고 싶으면 0으로 바꾸면 된다. 가방 속에 노트북을 넣고 음악을 감상할 수 있다!
