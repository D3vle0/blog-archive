---
title: "Nginx Proxy Manager로 딥 웹(Tor)에 정적 사이트 서빙하기 🧅"
date: 2025-12-08
draft: false
categories: ["Server"]
tags: ["tor", "server", "linux", "NPM"]
cover:
  image: img/tor-proxy-npm/1.png
ShowToc: true
TocOpen: true
---

## Tor 설치 및 Nginx Proxy Manager 세팅

```sh
sudo dnf install tor
```

```yaml
services:
  app:
    image: 'docker.io/jc21/nginx-proxy-manager:latest'
    restart: unless-stopped
    ports:
      - '80:80'
      - '81:81'
      - '443:443'
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt
```
위와 같이 `docker-compose.yml`를 작성하고 컨테이너를 실행한다.  

`http://<host>:81` 에 접속해서 기본 세팅을 마무리한다.

## 정적 파일 서빙 할 컨테이너 세팅
```yml
services:
  hugo-static:
    image: nginx:alpine
    container_name: hugo-static
    volumes:
      - <정적 파일 디렉토리>:/usr/share/nginx/html:ro
    restart: unless-stopped
    networks:
      - npm_net
    ports:
      - "<외부 포트>:80"
networks:
  npm_net:
    external: true
```

```sh
docker compose up -d
```
## 리버스 프록시 생성

```sh
sudo -i
cat /var/lib/tor/hidden_service/hostname
```

`.onion` 도메인을 확인한 후, Nginx Proxy Manager 웹 UI에서 `Proxy Hosts` 메뉴로 이동 후 `Add Proxy Host` 버튼을 클릭한다.

![img](/img/tor-proxy-npm/1.png)


위와 같이 `.onion` 도메인이 <컨테이너명>:<외부 포트> 로 향하는 리버스 프록시를 만들어주면 된다.