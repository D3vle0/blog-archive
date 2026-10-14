---
title: "XAMPP error in m1 mac"
date: 2022-05-30
draft: false
category: "macOS"
---

## XAMPP

XAMPP는 크로스 플랫폼 웹 서버 자유 소프트웨어 꾸러미이다. 아파치 웹 서버, MariaDB, PHP, 펄을 포함하고 있다. GNU 일반 공중 사용 허가서로 배포되며 자유롭고 쓰기 쉬운 웹 서버이다. 마이크로소프트 윈도우, 리눅스, 솔라리스, 맥 오에스 텐 등에서 동작하며 주로 웹 개발에 사용된다. ([출처](https://ko.wikipedia.org/wiki/XAMPP)) XAMPP의 의미는 X (크로스 플랫폼), A (Apache), M (MariaDB), P (PHP), P (Perl) 이다.

## macOS에서 발생한 오류

[이곳에서](https://www.apachefriends.org/download.html) XAMPP for OS X 8.1.6을 다운받고 실행했는데 서버를 시작할 수 없다는 에러가 발생했다.

![img](img/xampp-error/1.png)

show details를 눌러보니 이런 에러 메세지가 떴다. 원인은 macOS 11.3으로 업데이트 되면서 개발자가 로컬 환경에서 작업할 수 없는 등 많은 문제가 발생했다고 한다.

```txt
cannot calculate MAC address: Using fd 9 for I/O notifications
hv_vm_create unknown error 0x00000004
```

<!-- ## 해결법

```sh
# bitnami 백업 파일 생성
$ cp -rp ~/.bitnami ~/.bitnami.back
# 새로운 hyperkit 라이브러리 다운로드
$ cd /tmp
$ curl -LJO "https://downloads.bitnami.com/files/hyperkit/hyperkit-testing-20210430"
# 새로운 hyperkit 바이너리로 대체
$ mv /tmp/hyperkit-testing-20210430 ~/.bitnami/stackman/helpers/hyperkit
$ chmod +x ~/.bitnami/stackman/helpers/hyperkit
``` -->

## 해결법

application 폴더에 있는 XAMPP를 copy하고 기존의 파일은 휴지통에 넣는다.

![img](img/xampp-error/2.png)

![img](img/xampp-error/3.png)

[이곳에서](https://sourceforge.net/projects/xampp/files/XAMPP%20Mac%20OS%20X/7.2.34/xampp-osx-7.2.34-2-installer.dmg/download) XAMPP 7.2.34 버전을 다운받는다.

![img](img/xampp-error/4.png)

![img](img/xampp-error/5.png)

제대로 작동한다. 한 가지 주의할 점은 필자는 기존에 MySQL이 설치되어 있는 상태였는데, XAMPP에서 구동되는 MySQL과 충돌을 일으킨다. 기존의 MySQL이 실행중이면 XAMPP의 MySQL이 실행이 안되고, 그 반대도 동일하다.

![img](img/xampp-error/6.png)
