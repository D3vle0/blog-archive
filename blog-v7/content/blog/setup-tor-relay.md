---
title: "How to setup tor relay"
date: 2022-05-24
draft: false
category: "Server"
---

![img](img/setup-tor-relay/1.webp)

## Tor란?

기술적인 이야기는 최대한 빼고 쉽게 설명해보겠다. Tor는 The Onion Router의 약자로, 네트워크를 우회하여 사용할 수 있게 해주는 툴이다. Tor는 본래 미국 정부에서 인터넷 프라이버시를 지키기 위해, 국가에서 인터넷 감시를 당하는 사람을 위해 개발되었는데, 익명성을 유지할 수 있다는 Tor의 장점 때문에 비트코인 자금세탁, 마약, 총기, 위조 여권, 청부 살인 서비스가 운영되는 딥 웹 범죄 현장이 되기도 한다. 여기서 딥 웹은 일반적인 검색 엔진으로 검색되지 않고 Tor 브라우저를 통해서만 들어갈 수 있는 `.onion` 도메인을 가진 웹을 뜻한다. 앞서 작성한 [이 글의 '보안 4단계' 부분은](/posts/secret-hugo-blog) `.onion` 도메인을 가진 딥 웹에 블로그를 만드는 것이다. 그렇다면 Tor는 어떻게 익명성을 유지한 채로 인터넷을 사용할 수 있다는 것일까? 전세계에 분포하는 토르 라우터가 실행되고 있는 노드를 세 번 거쳐서 도달한다. 쉽게 설명해보자면 한국 -> 미국 -> 덴마크 -> 네덜란드를 거쳐 사이트에 접속하게 되는데, 최종적으로는 내가 네덜란드에서 접속한 것으로 인식하게 된다. 이러한 구조 덕분에 웹서핑을 할 때 나의 개인정보를 남기지 않고 보안 설정만 유의한다면 미국 NSA도 뚫기 힘든 수준의 익명성을 유지한 채로 웹서핑을 할 수 있다. [이 곳에 가면](https://tormap.void.gr/) 전세계 Tor 노드들 위치가 나와있다. Bridges, Other, Stable, Fast Stable, Exit, Fast Exit, Authority, Bad 모두 체크를 하고 지도를 본다면 어마어마하게 많은 위치에 핑이 찍혀 있을 것이다. 우리나라에 현재 운영중인 노드는 11개가 있다고 나오는데 실제로는 더 많을 것이다.
나 역시 인터넷 검열로부터 자유롭고 싶어하는 사람을 위해 나만의 Tor 노드를 만드는 것으로 기여하겠다.

## Tor 노드 구축하기

```bash
sudo apt-get install tor
sudo nano /etc/tor/torrc
```

tor를 설치하고 `/etc/tor/torrc` 끝부분에 아래의 내용을 추가한다.

```torrc
ORPort 443
Exitpolicy reject *:*
Nickname 라우터 이름
ContactInfo 연락처 (자유롭게 작성 가능)

AccountingMax 512 MBytes
RelayBandwidthRate 5120 KBytes
RelayBandwidthBurst 10240 KByte
```

이렇게 작성했으면 80, 443포트를 포트포워딩 해준다. VPS에서 운영중이라면 해당 VPS 업체 관리 페이지에서 inbound/outbound 규칙을 지정하거나 `iptables` 명령을 사용하고, 홈서버라면 공유기 관리자 페이지에서 포트포워딩 설정을 해준다. 80, 443 모두 inbound/outbound 동일한 포트로 설정하면 된다.

```bash
sudo service tor restart
```

이렇게 하면 세팅이 끝났다. 정말 간단하다.

```bash
sudo apt-get install tor-arm
sudo -u debian-tor nyx
```

위 명령을 사용하면 현재 나의 Tor 릴레이를 모니터링할 수 있다.

![img](img/setup-tor-relay/2.png)
