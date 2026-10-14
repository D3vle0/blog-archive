---
title: "What is going on in my server"
date: 2022-05-22
draft: false
category: "Server"
---

[이 글에서 이어지는 내용이다.](/linux-login)

fail2ban을 통하여 ssh 비밀번호를 3번 틀리면 120분 밴하도록 설정했다. 그런데 해커들은 그것을 뚫고서 계속해서 공격을 한다. (?) 3일간 31357번의 로그인 시도가 존재해 일주일 간 총 64322번이다.

![img](img/what-is-going-on-in-my-server/1.png)

github에 검색을 해보니 [fail2ban을 우회할 수 있는 툴을 발견했다.](https://github.com/Neetx/sshdodge) 이론상 fail2ban을 우회할 수 있는 방법이 존재하는 듯 하며 fail2ban을 통한 해커 차단을 실패한 경우도 찾아볼 수 있었다.([1](https://www.howtoforge.com/community/threads/fail2ban-fails-to-ban.37659/) [2](https://blog.oneiroi.co.uk/hacking/linux/when-fail2ban-fails-to-ban-dissecting-the-hack/))

fail2ban 서비스를 내리고, 특정한 ip 대역 이외에는 서버에 접근조차 못하도록 하는 방식을 선택해야겠다. 그리고 실제로 기능이 잘 작동하는지 보기 위해 `/var/log/btmp`를 빈 파일로 만든다.

```bash
echo '' > /var/log/btmp
```

## 해결 방안

[이 글에서 설명했던](/posts/linux-login) 내용 중에 서버 관리자가 허용된 네트워크 바깥에서 접속을 시도하면 접속을 못하는 문제가 있을 수 있다고 했다.

> `hosts.allow` 와 `hosts.deny` 를 활용하면 간단하지만 보안적으로 뛰어난 효과를 볼 수 있다. 그런데 만약 서버 관리자가 허용된 네트워크 밖에서 접속을 시도한다면 어떻게 될까? 해커가 아닌 정말로 서버 관리자 본인이 맞는데도 불구하고 서버에 접근할 수 없는 상황이 온다.

이를 보안하기 위한 해결 방안을 생각해보았다. 3000, 5000, 8080 등 흔하게 쓸 수 있는 포트 이외에 웹서비스를 만든다. 이 웹서비스는 다음과 같은 기능을 한다.

1. 사이트에 비밀번호 입력
2. 현재 접속한 내 IP 표시 (편의 기능)
3. 허용하고 싶은 IP 주소 또는 대역을 입력하면 `hosts.allow` 파일에 `sshd: <입력값>` 형태로 추가됨

이렇게 하면 ssh 접속을 하지 않고서 자유롭게 `hosts.allow` 파일을 수정할 수 있으면서 동시에 서버 관리자만이 접근할 수 있을 것이다!
