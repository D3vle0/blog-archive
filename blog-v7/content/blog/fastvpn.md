---
title: "FastVPN by namecheap"
date: 2022-09-13
draft: false
category: "Security"
---

# FastVPN이란?

FastVPN이란 도메인 구매 사이트인 Namecheap에서 운영하는 VPN 서비스다. NordVPN, ExpressVPN 등 세계적인 서비스 대신에 FastVPN을 리뷰하는 이유는 하나다. 매우 저렴한 가격.

# 획기적인 가격 정책

![Untitled](img/fastvpn/0.png)

가격정책은 매달 5.88 달러 (첫달 0.99달러) 매년 34.56달러 (첫해 12달러)로 타 서비스에 비해 저렴한 편이다. 먼저 0.99달러로 한 달만 체험해보고 괜찮으면 연간 플랜을 사용해야겠다.

# 연결 불가

![Untitled](img/fastvpn/1.png)

앱 스토어에서 FastVPN을 다운로드 받고 결제한 계정으로 로그인한다.

![Untitled](img/fastvpn/5.png)

Best Available 로케이션으로 연결하고 몇 초 지나지 않아서 연결이 끊어진다.

![Untitled](img/fastvpn/2.png)

이유를 알아보니… 아래 링크의 방법으로 macOS에서 IKEv2 VPN을 설정할 수 있다고 하는데, 문제는 macOS Ventura에서 해당 설정 메뉴가 없어서 설정이 불가하다는 것이다. 현재 macOS Ventura beta 6이 설치되어 있는데, macOS Monterey로 rollback 하는 수 밖에 없다.

[How to set up IKEv2 VPN connection on Mac - FastVPN - Namecheap.com](https://www.namecheap.com/support/knowledgebase/article.aspx/10406/2268/how-to-set-up-ikev2-vpn-connection-on-mac/)

![Untitled](img/fastvpn/3.png)

![Untitled](img/fastvpn/4.png)

IKEv2 VPN 추가 버튼을 눌러도 아무것도 없는 빈 창이 나온다.

# Monterey로 rollback 후 재시도

macOS 12.6으로 롤백을 진행하고 VPN을 연결했는데도 작동을 하지 않았다. 아래의 링크를 자세히 읽어보니, 연결이 되지 않을 경우 다른 네트워크 환경에서 연결을 시도해보라고 했다. 그 결과 학교 와이파이 대신 개인 네트워크를 사용하니 한 번에 해결되었다.

[Unable to connect to the Internet via your Namecheap VPN: How to fix - Apps - Namecheap.com](https://www.namecheap.com/support/knowledgebase/article.aspx/10118/2244/unable-to-connect-to-the-internet-via-your-namecheap-vpn-how-to-fix/)

Android 환경에서는 학교 와이파이로 접속해도 아무 문제 없어서 네트워크 문제일 것이라고 빨리 생각하지 못했다. Android는 OpenVPN 프로토콜을 사용하고, macOS에서는 IKEv2 프로토콜을 사용하는데 아마 학교에서 IKEv2 프로토콜을 막아놔서 이런 현상이 생긴 것 같다. 결국 macOS 버전과는 아무 상관이 없었다!