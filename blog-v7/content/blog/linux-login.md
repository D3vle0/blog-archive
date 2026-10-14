---
title: "How to improve SSH security in Ubuntu 22.04"
date: 2022-05-19
draft: false
category: "Server"
---

## 로그인 시도 기록 확인

```bash
sudo lastb
```

lastb 파일은 `/var/log/lastb` 파일을 뜻하는데, 그냥 `sudo cat /var/log/lastb`를 하면 이상한 문자들까지 같이 나와서 보기가 상당히 어렵다. 따라서 위의 명령어를 사용해야 한다.  
그런데 로그인 시도 기록이 너무 많아서 터미널에서 한번에 보기 어렵다면 `less` 명령을 활용하자. 방향키로 탐색하며 로그를 볼 수 있다.

```bash
sudo lastb | less
```

로그인 시도 기록이 몇 건인지 확인하고 싶으면 줄 수를 알려주는 명령인 `wc`를 활용하자.

```bash
sudo lastb | wc -l
```

## SSH 접속 화이트리스트/블랙리스트 설정

```bash
sudo nano /etc/hosts.allow
```

```txt
# /etc/hosts.allow

sshd: 123.123.123.123
sshd: 123.10.0.
```

`hosts.allow` 파일에 위와 같이 작성하면 `123.123.123.123`과 `123.10.0.XXX` 대역에서만 로그인할 수 있다.

```bash
sudo nano /etc/hosts.deny
```

```txt
# /etc/hosts.deny

sshd: ALL
```

일반적으로 `hosts.allow` 에서 기록한 IP 외에 모든 IP는 차단하는 화이트리스트 방식으로 운영하기 때문에 `hosts.deny` 는 이렇게 작성한다. 특정 IP 대역만 차단하고 싶다면 `hosts.allow` 처럼 똑같이 작성하면 된다.

허용되지 않은 IP에서 서버에 접속을 시도하면 에러가 뜬다.

![img](img/linux-login/1.png)

## fail2ban 설치

`hosts.allow` 와 `hosts.deny` 를 활용하면 간단하지만 보안적으로 뛰어난 효과를 볼 수 있다. 그런데 만약 서버 관리자가 허용된 네트워크 밖에서 접속을 시도한다면 어떻게 될까? 해커가 아닌 정말로 서버 관리자 본인이 맞는데도 불구하고 서버에 접근할 수 없는 상황이 온다. 이런 상황에서 쓸만한 프로그램이 바로 `fail2ban` 이다.

```bash
sudo apt-get install fail2ban
sudo systemctl start fail2ban
sudo systemctl status fail2ban
sudo systemctl enable fail2ban
```

fail2ban 서비스를 시작했는데 아래의 에러가 발생했다.

![img](img/linux-login/2.png)

```bash
sudo /usr/bin/fail2ban-client -x start
```

![img](img/linux-login/3.png)

`Failed during configuration: Have not found any log file for sshd jail` 라고 한다. 이는 sshd jail을 위한 로그 파일이 없어서 생기는 에러다.

```bash
sudo touch /var/log/auth.log
```

![img](img/linux-login/4.png)

놀랍게도 잘 작동한다. 구글링을 하다 보니 fail2ban 에러가 발생하는 경우는 이 외에도 다양하게 있는데, 그 중에서 apache2와 충돌을 일으키는 에러도 발생할 수 있는 것을 보았다. 그때는 apache2 웹서버를 끄거나 configuration 파일에서 문제가 될만한 부분을 수정해보자.

## fail2ban 설정

이제 fail2ban 설정을 해보겠다. 설정할 수 있는 항목들 중 일부를 살펴보자.

```bash
sudo nano /etc/fail2ban/jail.conf
```

- `ignoreself` : 자기 자신 (localhost) 밴 대상에서 제외 여부 (true/false)
- `ignoreip` : 밴 대상에서 제외할 네트워크 대역
- `bantime` : 밴 시간
- `maxretry` : 밴 당하기 전까지 최대 로그인 시도 횟수
- `bantime.increment` : 실패 횟수가 많아질수록 밴 시간이 증가 (true/false)
- `bantime.rndtime` : 밴 시간을 랜덤으로 지정 (밴 시간이 일정하면 봇이 그것을 감지할 수 있음)
- `bantime.maxtime` : 최대 밴 시간
- `bantime.factor` : 밴 시간이 몇 배 만큼 증가하는지 설정
- `bantime.formula` : 밴 시간을 수식으로 표현

설정을 변경했으면 fail2ban service를 restart 한다.

## 소감

새롭게 ubuntu 22.04로 클린 설치한 이후로 4일이 지났는데 그동안 32965번의 로그인 시도가 있었다. 특정 열린 포트를 찾아 bruteforce 공격을 하는 해커들이 정말 많다는 것을 깨닫고 이 글을 작성해보았다. fail2ban 기본 설정에서 밴 시간만 늘려놓은 상태로 며칠을 또 지내보고 후기를 남기겠다.
