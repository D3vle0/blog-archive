---
title: "Install IDA Pro 7.0 in M1 Mac"
date: 2022-02-21T23:13:42+09:00
draft: false
category: "Security"
---

## Mac에서 IDA Pro를 사용하는 방법

1. Parallels Windows에서 사용 (윈도우 크랙이 구하기 상대적으로 쉽다)
2. IDA Pro mac 크랙 버전을 구함
3. Windows 크랙 버전을 wine을 통해 구동

지금까지 m1 맥에서 맥 크랙을 받으면 ida는 잘 되는데 ida64가 작동을 하지 않았다. [이 글에 따라](https://iosre.com/t/topic/21033) 나도 wine을 통해 구동하려고 했으나 ida64가 잘 작동하는 맥 크랙을 찾게 되어 설치 파일과 에러 해결 방법을 공유하겠다.

## Installation

[이곳에서 IDA Pro 7.0](https://www.khow.me/blog/ida-pro-7.0-for-mac.html)을 다운받을 수 있고, 링크가 내려갈 때를 대비하여 [개인 서버에 백업해두었다.](http://prob.kro.kr/IDA.Pro.7.0.dmg)

sha256 해시는`d6c278e2615ea5ac82037dadf8bc6ee1db9feb2576dbee03cd11ad63c74fcfdd` 이다.

![img](img/m1-ida-pro/1.png)

IDA Pro 7.0 폴더를 통째로 Applications에 집어넣는다.

이 때 ida는 잘 작동하나 ida64를 실행하면 에러가 발생할 수 있는데,

```bash
sudo xattr -cr "/Applications/IDA Pro 7.0/ida64.app/"
```

이 명령을 터미널에서 입력하고 다시 실행하면 해결된다.

![img](img/m1-ida-pro/2.png)

decompile까지 잘 작동한다.
