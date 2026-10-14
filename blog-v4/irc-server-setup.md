# IRC 서버 구축하기


## IRC란?

Internet Relay Chatting의 약자로, 특정 topic을 가지고 사용자끼리 대화를 할 수 있습니다.

## Installation

먼저 방화벽에서 6667, 6697, 7000, 7001 포트를 열어줍니다.

```bash
iptables -I INPUT 1 -p tcp --dport 6667 -j ACCEPT
iptables -I INPUT 1 -p tcp --dport 6697 -j ACCEPT
iptables -I INPUT 1 -p tcp --dport 7000 -j ACCEPT
iptables -I INPUT 1 -p tcp --dport 7001 -j ACCEPT
```

또는 서버 방화벽 (보안 그룹) 페이지에서 해당 포트를 열어줄 수 있습니다.

```bash
sudo apt install docker
sudo apt install certbot
sudo apt install screen
```

도커, 인증서 발급 패키지, 백그라운드 실행 패키지를 설치합니다.

## 인증서 발급

IRC 서버를 운영하기 위해서는 인증서가 필요합니다.

```bash
sudo certbot certonly --standalone -d <domain>
```

## 도커 세팅

이제 도커를 이용해서 서비스를 시작해봅시다.

```bash
mkdir irc
cd irc
touch docker-compose.yml
mkdir config
```

`docker-compose.yml` 을 아래와 같이 작성합니다.

```yml
version: "3.5"
services:
    irc:
        container_name: irc_server
        image: inspircd/inspircd-docker:2.0.27
        ports:
            - "6667:6667"
            - "6697:6697"
        environment:
            - INSP_NET_SUFFIX=.irc.your.domain.com
            - INSP_ADMIN_NAME=
            - INSP_ADMIN_NICK=
            - INSP_ADMIN_EMAIL=
        volumes:
            - ./config:/inspircd/conf/
```

## 인증서 적용

```bash
sudo cp /etc/letsencrypt/live/<도메인>/fullchain.pem ./config/cert.pem
sudo cp /etc/letsencrypt/live/<도메인>/privkey.pem ./config/key.pem
sudo chown 10000 ./config/ -R
```

## 서비스 시작

```bash
screen -S irc
docker-compose up
```

`Ctrl + A, D` 를 눌러서 screen 에서 나온다.

```bash
screen -r
```

screen 에 들어갈 때는 위의 명령을 사용하면 된다.

## 서버 접속 방법

irc 클라이언트 중 하나인 irssi 를 사용하겠습니다.

```bash
sudo apt install irssi
irssi
```

irssi 가 실행이 되면 자신의 서버에 접속할 수 있습니다.

```bash
/connect -tls <도메인> 6697
/join <채널>
```

일일이 접속할 때 마다 위의 명령을 쳐야하기 때문에 zshrc 에 등록해줘도 됩니다.
아래의 명령어를 `~/.zshrc` 파일 끝에 추가합니다.

```bash
alias irc='irssi -c <도메인> -n <닉네임>'
```

## motd 변경

MOTD 란 Message Of The Day 의 약자로 IRC 서버에 접속할 때 보여주는 문자열입니다.
주로 서버 소개, 규칙 등을 적어놓습니다.

```bash
screen -r
cd config
```

이후 `docker.motd` 파일을 수정해서 사용하면 됩니다.

```motd
                        _        _
 ___  ___  ___ _ __ ___| |_     (_)_ __ ___
/ __|/ _ \/ __| '__/ _ \ __|____| | '__/ __|
\__ \  __/ (__| | |  __/ ||_____| | | | (__
|___/\___|\___|_|  \___|\__|    |_|_|  \___|



👋 환영합니다!

이곳은 Devleo 가 운영하는 비밀 IRC 서버입니다.

가볍고 빠른 성능의 IRC 서버에서 쾌적한 채팅을 경험하세요!

채팅 채널을 소개합니다.

#general: 잡담
#develop: 개발 관련
#hacking: 해킹 관련
```

