---
title: "SuNiNaTaS 32 Solution"
date: 2022-01-26T21:04:28+09:00
draft: false
category: "Security"
---

## 문제 풀이 요약

1. USB 파일 시스템 복구
2. 파일 데이터 확인

## 문제 풀이

### 제시 문제

경찰청으로 부터 연쇄 테러 용의자로 부터 압수한 USB 이미지 분석을 의뢰 받았다.
최초 분석을 신입 직원에게 맡겼으나 Hex Editor로 여기 저기 둘러 보다 실수로 특정 부분이 손상되고 이미지가 인식되지 않는다.

당신은 포렌식 전문가의 자존심을 걸고 이미지를 살려 내고 다음 테러를 예방하는데 기여를 해야 한다.

1. 다음 테러 계획이 들어있는 문서의 수정 일시는? (UTC+9)
2. 다음 테러 장소는?

인증키 형식 : lowercase(MD5(YYYY-MM-DD_HH:MM:SS_장소)

예) lowercase(MD5(2016-03-28_13:00:00_Pink Lake)

### USB 파일 시스템 복구

문제 설명을 보면 USB 이미지 특정 부분이 손상되었다고 한다. 먼저 `hex fiend`로 헥스값을 확인해본다.

![img](img/suninatas-32/1.png)

FAT32 시스템의 boot sector 임을 확인할 수 있는데, 시그니처인 `55 AA`가 앞으로 많이 나와있는 모습이다. sector 하나의 크기는 512byte 이기 때문에 `55 AA`가 `0x1fe` 자리에 있어야 한다. 따라서 null 값을 임의로 삽입해주자.

![img](img/suninatas-32/2.png)

이미지가 복구되었고, 이제 FTK Imager를 통해 분석한다.

![img](img/suninatas-32/3.png)

테러 계획이 담긴 문서를 확인할 수 있다. 수정 날짜는 2016년 5월 30일 02:44:02 인데 UTC+9 형식에 맞추면 11:44:02가 된다.

![img](img/suninatas-32/4.png)

맥 finder에서는 수정일의 초 단위를 확인할 수 없으니 `stat` 명령을 활용한다.

```
➜  [root] stat -x 2차\ 테러\ 계획.hwp
  File: "2차 테러 계획.hwp"
  Size: 9728         FileType: Regular File
  Mode: (0644/-rw-r--r--)
  Uid: (  501/  devleo)
  Gid: (   20/   staff)
Device: 1,16
 Inode: 3488864
 Links: 1
Access: Wed Jan 26 20:53:55 2022
Modify: Mon May 30 11:44:02 2016
Change: Wed Jan 26 20:54:28 2022
```

11:44:02 인 것을 확실하게 확인했다.

![img](img/suninatas-32/5.png)

테러 장소는 `Rose Park`이다.

> FLAG: md5(2016-05-30_11:44:02_Rose Park) = `8ce84f2f0568e3c70665167d44e53c2a`