---
title: "Custom Domain for Tor Website"
date: 2022-05-26
draft: false
category: "Server"
---

![img](img/tor-custom-domain/1.jpg)

## 서문

Tor 브라우저로 딥웹을 서핑하다 보면 어떤 사이트는 정말 랜덤한 56자리의 도메인 (onion v3 기준) 을 가진 곳이 있고, 어떤 사이트는 랜덤한 문자열 앞에 의미가 있는 단어를 포함한 경우도 있는 것을 보았다. 예를 들어 한 Q&A 사이트는 도메인이 `answer`로 시작하고, 호스팅 사이트는 `dhosting` 으로 시작하는 사이트가 있다. 심지어 페이스북 딥웹 사이트는 onion v2 때 랜덤한 문자열이 전혀 없었고 `Facebook's Core WWW Infrastructure` 를 뜻하는 `facebookcorewwwi.onion` 을 가지고 있었다. 도대체 이러한 도메인은 어떻게 만드는 것일까 연구를 해보다 한글 자료가 거의 없어서 직접 정리를 해본다.

## .onion 도메인 원리

[이 글에서 언급했듯이](/posts/secret-hugo-blog) tor를 설치하고 서비스를 실행시키면 `/var/lib/tor/hidden_service/hostname` 에 자신의 onion 도메인이 생성된다. 직접 해보면 알겠지만 이때 생성되는 주소는 56자리의 랜덤한 문자열이다. `.onion` 도메인은 각 자리마다 영어 소문자 a-z, 숫자 2-7까지 총 32가지의 경우의 수가 존재한다. 만약 d로 시작하는 주소를 얻고 싶다면 최대 32번, de로 시작하는 주소를 얻고 싶다면 최대 32^2번, dev로 시작하는 주소를 얻고 싶다면 최대 32^3번 bruteforce가 필요하다. [이 곳에 글자 수에 따른 bruteforce 예상 시간이 나와있는데](https://pastebin.com/hdB8QU6z) 원리대로 한 글자씩 증가할 때 마다 시간이 32배가 된다. 그러나 [이곳에 따르면](https://leastwastefulcities.com/page-91/tor-7/#i-2) 소요 시간이 조금 다른 것을 볼 수 있다. 정확히 어떤 자료가 정확한 지는 모르겠지만, 확실한 것은 컴퓨팅 성능에 따라 bruteforce 시간은 크게 다르고, 6~7글자가 넘어가면 굉장히 오랜 시간을 기다려야 한다는 것이다. 필자는 VPS에서 사용 중인 tor 웹사이트를 위해 6글자 bruteforce를 해보겠다.

## mkp224o

mkp224o는 onion v3 주소를 생성하는 것을 도와주는 프로그램이다.

### debian

debian 계열 리눅스 (ubuntu, kali linux, linux mint 등) 에서는 아래와 같이 설치하면 된다.

```bash
sudo apt install gcc libsodium-dev make autoconf git
git clone https://github.com/cathugger/mkp224o
cd mkp224o/
./autogen.sh
./configure
make
```

### macOS

필자는 ubuntu 22.04 VPS 서버에 적용할 용도지만, 현재 사용 중인 m1 맥북이 성능이 훨씬 좋으니 로컬에서 작업하고 생성된 인증서를 VPS 쪽으로 옮기는 방식으로 하겠다. [참고](https://github.com/cathugger/mkp224o/issues/43)

```bash
brew install autoconf libsodium
git clone https://github.com/cathugger/mkp224o.git
cd mkp224o
./autogen.sh
CPPFLAGS="-I/opt/homebrew/Cellar/libsodium/1.0.18_1/include" LDFLAGS="-L/opt/homebrew/Cellar/libsodium/1.0.18_1/lib" ./configure
make
```

### 사용법

```bash
./mkp224o <원하는 글자 후보 1> <원하는 글자 후보 2> <원하는 글자 후보 3> ...
```

이것이 무슨 뜻이냐면, 만약 `./mkp224o apple hack` 명령을 하면 `apple` 로 시작하는 도메인, `hack` 으로 시작하는 도메인을 생성한다는 것이다.

```bash
./mkp224o -d <경로> <원하는 글자 후보 1> <원하는 글자 후보 2> <원하는 글자 후보 3> ...
```

`-d` 옵션에 경로를 지정할 수 있는데 이는 생성된 도메인에 대한 인증서를 저장할 경로를 의미한다.

![img](img/tor-custom-domain/2.png)

그런데 막상 작업을 해보니 생각보다 오래 걸리지는 않는다. 6글자 bruteforce를 해보았는데 2분만에 도메인 하나가 나왔고, 5분 동안 7개나 나왔다. 역시 m1이다. 마음에 드는 도메인이 있으면 `^C` 로 중단하자.

![img](img/tor-custom-domain/3.png)

## 도메인 적용

앞서 mkp224o 디렉토리 안에 res 디렉토리를 만들어서 그곳에 인증서를 저장시키게 했다. res 디렉토리 안에 생성된 도메인 이름으로 디렉토리가 생성되어 있고, 각각의 디렉토리 내부에 해당 도메인의 인증서가 들어있다.

![img](img/tor-custom-domain/5.png)

![img](img/tor-custom-domain/4.png)

이제 VPS 서버로 옮겨서 도메인 세팅을 해주자. `scp` 명령으로 로컬에서 서버로 파일들을 전송한다.

```bash
scp -i <ssh 인증서 경로> -r <tor 인증서들이 들어있는 경로 (onion 도메인)> <사용자>@<서버 ip>:/home/<사용자>
```

tor 인증서가 들어갈 최종 경로는 sudo 권한을 이용해야 해서 먼저 홈 디렉토리로 옮긴 후 진행한다.

```bash
# 서버
cd ~/<onion 도메인>
sudo su
cp -r . /var/lib/tor/hidden_service/
exit
sudo service tor restart
```

이제 tor 브라우저를 통해 변경한 도메인에 접속하면 된다.
