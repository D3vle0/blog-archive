---
title: "Linux에서 Matrix Synapse 서버 설치하기"
date: 2025-10-30
draft: false
categories: ["Server"]
tags: ["linux", "docker", "matrix", "synapse"]
cover:
  image: img/linux-synapse/1.png
  caption: ""
ShowToc: true
TocOpen: true
---

## 개요

[이 글과 비슷한 내용이다.](/posts/synology-synapse/) 나는 홈서버를 Xpenology 환경을 사용할 때도 있고 일반 리눅스 환경을 사용할 때도 있어서 다시 정리하는 글이다. Synology NAS가 아닌 일반 Linux 서버 환경에서 Docker를 활용해 Matrix Synapse 서버를 설치하는 방법을 다룬다.

## Synapse Homeserver 생성

```sh
mkdir {data,db};sudo docker run --rm \
-v ./data:/data \
-e SYNAPSE_CONFIG_PATH=/data/homeserver.yaml \
-e SYNAPSE_SERVER_NAME=<서버 주소> \
-e SYNAPSE_REPORT_STATS=no \
matrixdotorg/synapse:latest generate
```

## homeserver.yaml 설정 수정

`data` 폴더 안의 `homeserver.yaml` 파일에서  `server_name: "..."` 밑에 다음 내용을 추가한다.

```yaml
enable_registration: true
enable_registration_without_verification: true
enable_group_creation: true
```

```yaml
database:
  name: sqlite3
  args:
    database: /data/homeserver.db
```

위의 내용을 삭제하고 아래 내용으로 replace 한다.

```yaml
database:
  name: psycopg2
  args:
    user: synapseuser
    password: synapsepass
    database: synapsedb
    host: synapse-db
    cp_min: 5
    cp_max: 10
```

![img](/img/linux-synapse/1.png)

이런 형태로 `homeserver.yaml` 파일이 완성된다.

## Docker Compose 작성

```yaml {linenos=true}
services:
  synapse-db:
    image: postgres:16
    container_name: Synapse-DB
    hostname: synapse-db
    security_opt:
      - no-new-privileges:true
    healthcheck:
      test: ["CMD", "pg_isready", "-q", "-d", "synapsedb", "-U", "synapseuser"]
      timeout: 45s
      interval: 10s
      retries: 10
    volumes:
      - ./db:/var/lib/postgresql/data:rw
    environment:
      - POSTGRES_DB=synapsedb
      - POSTGRES_USER=synapseuser
      - POSTGRES_PASSWORD=synapsepass
      - POSTGRES_INITDB_ARGS=--encoding=UTF-8 --lc-collate=C --lc-ctype=C
    restart: on-failure:5

  synapse:
    image: matrixdotorg/synapse:latest
    container_name: Synapse
    hostname: synapse
    security_opt:
      - no-new-privileges:true
    environment:
      - TZ=Asia/Seoul
      - SYNAPSE_CONFIG_PATH=/data/homeserver.yaml
    volumes:
      - ./data:/data:rw
    ports:
      - 8450:8008/tcp
    restart: on-failure:5
    depends_on:
      synapse-db:
        condition: service_started
```
`docker-compose.yml` 파일을 위와 같이 작성한다. `synapse-db` 서비스는 Postgres 데이터베이스를 생성하고, `synapse` 서비스는 Matrix Synapse 서버를 실행한다.

```bash
sudo docker-compose up -d
```

## SSL 설정

Nginx Proxy Manager에서 다음과 같이 reverse proxy 및 SSL 설정을 한다.

![img](/img/linux-synapse/2.png)

![img](/img/linux-synapse/3.png)

![img](/img/linux-synapse/4.png)

위 이미지처럼 커스텀 헤더를 추가하여 websocket 설정을 활성화한다.

```conf
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection "upgrade";
```

`proxy_set_header Connection $connection_upgrade;` 를 쓰지 않는 이유는 Nginx Proxy Manager의 내부 nginx.conf에는 다음 내용이 정의되어 있다.

```conf
map $http_upgrade $connection_upgrade {
    default upgrade;
    '' close;
}
```

일부 버전(특히 2.11 이전 버전)이나 custom template에서는 이 $connection_upgrade 변수가 정의되지 않은 컨텍스트에서 불러와지기 때문에 `nginx reload` 시 `unknown variable "$connection_upgrade"` 에러가 난다. 따라서 직접 `"upgrade"` 값을 넣어주는 것이다.