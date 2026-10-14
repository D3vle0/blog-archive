---
title: "How to mine dogecoin in m1 mac"
date: 2022-10-05
draft: false
category: "Blockchain"
---

# 거래소 가입

국내 거래소(빗썸, 업비트, 코인원 등)는 미성년자의 가입을 제한하고 있다. 코인 투자는 미숙한 경제 관념을 갖고 있는 미성년자에게 권장되지 않기 때문이다. 해외 거래소도 마찬가지지만 국내 거래소 보다는 상대적으로 덜 엄격한 듯 하다.

일단 본인은 binance 2단계 KYC 인증을 통과하여 정상적으로 바이낸스 해외 거래소를 이용할 수 있다. 만 18세 이상 (생일 지난 19살)은 대부분의 해외 거래소에서 가입 허가를 해준다.

# 지갑 주소 확인

![Untitled](img/dogecoin/1.png)

Wallet > Fiat and Spot 에 들어가서 도지코인 검색 후 Deposit 버튼을 누른다.

![Untitled](img/dogecoin/2.png)

이후 원하는 네트워크 선택 후 지갑 주소를 복사한다.

# 채굴 프로그램 설치

[XMRig](https://xmrig.com/)

이곳에서 자신의 PC 운영체제에 맞는 XMrig 프로그램을 설치한다. 압축을 해제하면 `config.json` 파일이 들어있는데, pools 부분을 다음과 같이 수정한다.

```json
"pools": [
        {
            "algo": "rx/0",
            "coin": null,
            "url": "rx.unmineable.com:3333",
            "user": "DOGE:지갑주소.채굴자닉네임",
            "pass": "x",
            "rig-id": null,
            "nicehash": false,
            "keepalive": false,
            "enabled": true,
            "tls": false,
            "tls-fingerprint": null,
            "daemon": false,
            "socks5": null,
            "self-select": null,
            "submit-to-origin": false
        }
    ],
```

이제 xmrig 프로그램을 실행하면 unmineable 채굴 풀에 연결, 정상적으로 도지 코인을 채굴하는 모습을 볼 수 있다!

![Untitled](img/dogecoin/3.png)

# 결과는?

평균적으로 1300H/s 의 해시레이트를 가진다. 

![Untitled](img/dogecoin/4.png)

unmineable 사이트 계산기에 대입해보니 24시간 내내 돌렸을 때 하루 0.39429468 도지, 한 달 11.8288404 도지를 얻을 수 있다고 나와있다.

![Untitled](img/dogecoin/5.png)

한 달에 1072원을 벌 수 있다. 물론 나는 수익성을 위해 채굴하는 것이 아니고 블록체인 공부와 해외 거래소 체험을 위해 하는 것이니 상관 없다.