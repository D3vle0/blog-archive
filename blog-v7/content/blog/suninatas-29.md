---
title: "SuNiNaTaS 29 Solution"
date: 2022-01-25T22:16:04+09:00
draft: false
category: "Security"
---

## 문제 풀이 요약

1. header 체크 후 `FTK Imager`로 분석
2. dns host 파일 체크
3. 키로거 프로그램 찾기
4. webcache를 통해 다운로드 기록 찾기

## 문제 풀이

유준혁은 PC가 고장나서 형 유성준에게 PC를 고쳐 달라고 했다.
그런데, 유성준은 동생의 PC를 고치면서 몇 가지 장난을 했다.
당신은 이 PC를 정상으로 돌려 놓아야 한다.

1. 웹 서핑은 잘 되는데, 네이버에만 들어가면 사이버 경찰청 차단 화면으로 넘어간다. 원인을 찾으면 Key가 보인다.
2. 유성준이 설치 해 놓은 키로거의 절대경로 및 파일명은?(모두 소문자) ex) c:\windows\notepad.exe
3. 키로거가 다운로드 된 시간은? ex) 2016-05-27_22:00:00 (yyyy-mm-dd_hh:mm:ss)
4. 키로거를 통해서 알아내고자 했던 내용은 무엇인가? 내용을 찾으면 Key가 보인다.

인증키 형식: lowercase(MD5(1번키+2번답+3번답+4번키))

### 문제 파일 분석

![img](img/suninatas-29/1.png)

header가 `45 47 47`인 것으로 보아 `.egg` 압축 파일일 것이다. 확장자 변경 후 반디집으로 압축을 풀면 `vmdk` 파일이 나오고 해당 파일을 FTK Imager 에 올린다.

![img](img/suninatas-29/2.png)

Windows 7 기반 시스템이다.

### 1번

네이버에만 들어가면 사이버 경찰청 차단 화면으로 넘어간다. 이런 현상이 일어나는 이유는 host 파일이 변조되었기 때문에 일어났을 것이다. 따라서 윈도우 시스템 상의 host 파일인 `C:\Windows\System32\drivers\etc\hosts` 파일을 확인한다.

```
# Copyright (c) 1993-2009 Microsoft Corp.
#
# This is a sample HOSTS file used by Microsoft TCP/IP for Windows.
#
# This file contains the mappings of IP addresses to host names. Each
# entry should be kept on an individual line. The IP address should
# be placed in the first column followed by the corresponding host name.
# The IP address and the host name should be separated by at least one
# space.
#
# Additionally, comments (such as these) may be inserted on individual
# lines or following the machine name denoted by a '#' symbol.
#
# For example:
#
#      102.54.94.97     rhino.acme.com          # source server
#       38.25.63.10     x.acme.com              # x client host
       121.189.57.82    naver.com
       121.189.57.82    www.naver.com
#
#
#      C0ngr4tur4ti0ns!! This is a Keeeeeeeeeeey : what_the_he11_1s_keey
#
#
# localhost name resolution is handled within DNS itself.
#	127.0.0.1       localhost
#	::1             localhost
```

> 1번키: `what_the_he11_1s_keey`

### 2번

C드라이브 아래에 수상한 이름의 폴더가 있다.

![img](img/suninatas-29/3.png)

![img](img/suninatas-29/4.png)

`C:\v196vv8\v1valv\Computer1\24052016 #training\ss\` 경로에 많은 사진 파일들이 있는데 추출해서 확인해보자.

`5.jpg` 이름을 가진 파일에 키로거 프로그램의 이름과 경로가 적혀있다.

![img](img/suninatas-29/5.png)

> 2번키: `c:\v196vv8\v1tvr0.exe`

### 3번

여러 사진들이 있던 바로 상위폴더인 `C:\v196vv8\v1valv\Computer1\24052016 #training\`에 `w1.dat`과 `z1.dat` 파일이 존재하는데 이를 추출해보자.

![img](img/suninatas-29/7.png)

`w1.dat`에 서버로부터 프로그램들을 다운받는 듯한 로그가 보이는데, 이 PC에는 IE 외에는 다른 브라우저가 설치되어 있지 않은 상태로 보아 브라우저 기록을 확인한다.

`C:\Users\training\Appdata\Local\Microsoft\Windows\History\History.IE5\index.dat`

![img](img/suninatas-29/8.png)

일반적인 텍스트 에디터로 보면 다 깨지기 때문에 `index.dat analyzer`을 이용해서 분석할 수 있다.

![img](img/suninatas-29/9.png)

파일명에 키로거라고 적힌 exe 파일이 다운로드 된 시각은 2016-05-24_04:25:06 이다.

> 3번키: `2016-05-24_04:25:06`

### 4번

나머지 파일 하나 `z1.dat`을 보자.

![img](img/suninatas-29/6.png)

> 4번키: `blackkey is a Good man`

### 최종 답

> md5(what_the_he11_1s_keeyc:\v196vv8\v1tvr0.exe2016-05-24_04:25:06blackkey is a Good man) = `970f891e3667fce147b222cc9a8699d4`
