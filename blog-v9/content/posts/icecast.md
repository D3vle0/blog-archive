---
title: "Icecast 서버로 인터넷 라디오 방송국 만들기 ❄️"
date: 2025-10-27
draft: false
categories: ["Server"]
tags: ["icecast", "radio", "streaming"]
cover:
  image: img/icecast/2.png
  caption: ""
ShowToc: true
TocOpen: true
---

## 서문
지금으로부터 약 3년 전, 딥 웹을 탐험하던 중 장르 별로 음악을 실시간 스트리밍할 수 있는 인터넷 라디오 방송국 사이트에 들어갔다. 딥 웹 특유의 음산한 느낌과 함께 희안한 장르의 음악을 들으니 나도 이런 서비스를 만들어보고 싶다는 생각이 들었다.  
그렇게 생각만 하다가 몇 개월 전 드디어 실행에 옮기게 되었다. 그때는 Icecast같은 실시간 송출 프로그램의 존재를 전혀 모른 채 nodejs로 이를 구현하려고 했다. [무언가 만들어지기는 했는데 (당시 커밋),](https://github.com/k-atusa/ASMR/tree/40d03cad5778d71dc05e82e83da45638c2101c9c) 청취자가 음악을 들을 수 없을 수준으로 소리가 끊겼다. 한계를 느껴 이 프로젝트는 잠시 제쳐두었던 중, 며칠 전에 vlc 플레이어에서 이런 기능을 발견했다.

![img](/img/icecast/1.png)

Icecast라는 프로그램을 활용해 만든 방송국들의 목록이 표시되는 것이다! Icecast가 무엇인지 알아봤고, 이를 활용하면 실시간 송출하는 로직을 내가 직접 설계하지 않고 간단하게 나만의 방송국을 만들 수 있다는 것을 알았다.

## Icecast 설치

```yaml {linenos=true}
services:
  icecast2:
    image: pltnk/icecast2
    container_name: icecast2
    restart: always
    ports:
      - 7000:8000
    volumes:
      - ./config/icecast.xml:/etc/icecast2/icecast.xml

  liquidsoap:
    image: savonet/liquidsoap:v2.2.3
    container_name: liquidsoap-player
    volumes:
      - ./config/liquidsoap.liq:/config/liquidsoap.liq
      - ./music:/music
      - ./logs:/var/log/liquidsoap
    depends_on:
      - icecast2
    command: liquidsoap /config/liquidsoap.liq
    restart: unless-stopped
```

`docker-compose.yml`를 위와 같이 작성한다.

```xml {linenos=true}
<icecast>
  <location>example</location>
  <admin>example@example.com</admin>

  <limits>
    <clients>100</clients>
    <sources>2</sources>
    <threadpool>5</threadpool>
    <queue-size>524288</queue-size>
  </limits>

  <authentication>
    <!-- admin 로그인 -->
    <admin-user>admin</admin-user>
    <admin-password>passwd</admin-password>
    <!-- 송출용 계정 -->
    <source-password>passwd</source-password>
    <!-- 리스너용 계정 -->
    <relay-password>passwd</relay-password>
  </authentication>

  <hostname>localhost</hostname>
  <listen-socket>
    <port>8000</port>
  </listen-socket>

  <fileserve>1</fileserve>

  <paths>
    <basedir>/usr/share/icecast2</basedir>
    <logdir>/var/log/icecast2</logdir>
    <webroot>/usr/share/icecast2/web</webroot>
    <adminroot>/usr/share/icecast2/admin</adminroot>
    <alias source="/" dest="/status.xsl"/>
  </paths>

  <logging>
    <accesslog>access.log</accesslog>
    <errorlog>error.log</errorlog>
    <loglevel>3</loglevel>
  </logging>

  <security>
    <chroot>0</chroot>
  </security>
</icecast>
```

`config/icecast.xml` 파일을 위와 같이 작성한다. `location`과 `admin` 값을 원하는 값으로 수정한다. `authentication` 태그 안에 있는 `admin-user`, `admin-password`, `source-password`, `relay-password`는 각각 관리자 로그인, 송출용 계정, 리스너용 계정의 아이디와 비밀번호를 설정하는 부분이다. 적절히 수정해준다.

```py {linenos=true}
# 로그 설정
set("log.file", "/var/log/liquidsoap/liquidsoap.log")
set("log.level", 3)

# 무음 파일을 fallback으로 설정 (비어 있을 때 끊기지 않게)
def silence()
  blank(duration=10.)
end

# 음악 파일들을 랜덤 재생 (폴더 안 mp3를 랜덤 재생)
radio = playlist(mode="random", reload=3600, "/music")

# playlist가 비어 있을 때 대비
radio = fallback(track_sensitive=false, [radio, silence()])

# Icecast 서버로 송출
output.icecast(
  %mp3, # 인코딩 포맷
  host = "icecast2", # docker-compose 서비스 이름
  port = 8000,
  password = "passwd", # icecast.xml의 source-password
  mount = "stream",
  name = "example radio", # 방송국 이름
  description = "An example radio station", # 방송국 설명
  genre = "Various",
  radio
)
```

`config/liquidsoap.liq` 파일을 위와 같이 작성한다. `output.icecast` 함수 안의 `host`, `port`, `password`, `mount`, `name`, `description`, `genre` 값을 적절히 수정해준다. `music` 폴더에 있는 mp3 파일들이 랜덤으로 재생된다.  

프로젝트 트리 구조는 다음과 같다.

```
.
├── config
│   ├── icecast.xml
│   └── liquidsoap.liq
├── docker-compose.yml
├── logs
└── music
    └── music.mp3
    └── ...
```

이제 `docker-compose up -d` 명령으로 컨테이너를 실행한다.

![img](/img/icecast/2.png)

`http://<서버 주소>:7000`에 접속하면 Icecast 관리 페이지가 뜬다. `Mounts` 탭에 들어가면 `stream` 마운트 포인트가 생성된 것을 볼 수 있다. 

![img](/img/icecast/3.png)

또한 Admin 페이지에서 현재 청취자 수와 송출 상태를 확인할 수 있다.


이제 `http://<서버 주소>:7000/stream`에 접속하면 음악이 재생되는 것을 확인할 수 있다!