---
title: "CTFd Remove Default Banner"
date: 2021-10-31T14:30:23+09:00
draft: false
category: 'Security'
---

## 문제 발생

교내 자율동아리를 위한 CTFd 서버를 구축하다가 생긴 일이다.

```html
A cool CTF platform from ctfd.io

Follow us on social media:
Click here to login and setup your CTF
```

예전에는 기본 배너를 `admin panel > config > theme` 에서 없애줄 수 있었는데 업그레이드가 되며 안되는듯 하다.  
결국 소스코드를 분석하여 없애는 방법을 찾아냈다.

## grep 으로 문자열 검색

```bash
grep -r 'to login and setup your CTF' .
```

"to login and setup your CTF" 라는 문자열이 포함된 파일을 검색한다.

![img](https://cdn.discordapp.com/attachments/794556029555703832/904175566352179200/Screen_Shot_2021-10-31_at_10.10.47_AM.png)

`./CTFd/views.py` 의 index 변수를 수정해도 없어지지 않았다.  
바로 아래에 `./CTFd/ctfd.db` 파일에도 해당 문자열이 있다는데 sqlite3를 이용해 열어보자.

## sqlite table 수정

```bash
sqlite3 ctfd.db
```

```sql
.schema
SELECT * FROM pages;
```

```sql
1||index|<div class="row">
    <div class="col-md-6 offset-md-3">
        <img class="w-100 mx-auto d-block" style="max-width: 500px;padding: 50px;padding-top: 14vh;" src="/themes/core/static/img/logo.png?d=1d53abea" />
        <h3 class="text-center">
            <p>A cool CTF platform from <a href="https://ctfd.io">ctfd.io</a></p>
            <p>Follow us on social media:</p>
            <a href="https://twitter.com/ctfdio"><i class="fab fa-twitter fa-2x" aria-hidden="true"></i></a>&nbsp;
            <a href="https://facebook.com/ctfdio"><i class="fab fa-facebook fa-2x" aria-hidden="true"></i></a>&nbsp;
            <a href="https://github.com/ctfd"><i class="fab fa-github fa-2x" aria-hidden="true"></i></a>
        </h3>
        <br>
        <h4 class="text-center">
            <a href="admin">Click here</a> to login and setup your CTF
        </h4>
    </div>
</div>|0|||markdown
```

이렇게 생긴 table이 나온다.

```sql
CREATE TABLE pages (
        id INTEGER NOT NULL, 
        title VARCHAR(80), 
        route VARCHAR(128), 
        content TEXT, 
        draft BOOLEAN, 
        hidden BOOLEAN, 
        auth_required BOOLEAN, 
        format VARCHAR(80), 
        PRIMARY KEY (id), 
        UNIQUE (route), 
        CHECK (draft IN (0, 1)), 
        CHECK (hidden IN (0, 1)), 
        CHECK (auth_required IN (0, 1))
);
```

pages table 의 scheme 은 위와 같으므로 id가 1인 row 중에서 content 값을 수정하면 되겠다.

```sql
UPDATE pages SET content="aaaaa" WHERE id=1;
```

다시 CTFd 서버를 켜면...

![img](https://cdn.discordapp.com/attachments/794556029555703832/904178484132347904/Screen_Shot_2021-10-31_at_10.22.25_AM.png)

기본 banner 가 사라졌다!

참고로 위 테마는 [https://github.com/chainflag/ctfd-neon-theme](https://github.com/chainflag/ctfd-neon-theme) 이곳에서 다운받았다.
