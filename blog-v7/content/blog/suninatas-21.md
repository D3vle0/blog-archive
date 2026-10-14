---
title: "SuNiNaTaS 21 Solution"
date: 2022-01-25T21:30:43+09:00
draft: false
category: "Security"
---

## 문제 풀이 요약

1. 헥스값 분석, 숨겨진 사진 파일 추출
2. 추출된 여러 파일을 이용해 답 유추

## 문제 풀이

![img](img/suninatas-21/monitor.jpeg)

위의 이미지가 주어진다.

![img](img/suninatas-21/1.png)

JPG header인 `FF D8`이 여러 번 검색되는 것을 보아, 문제에서 주어진 이미지의 footer 밑에 또 다른 JPG 파일들이 숨어있다는 것을 추측할 수 있다. binwalk로 추출해보자.

![img](img/suninatas-21/2.png)

`binwalk --dd='.*' monitor.jpeg`

여러 개의 파일이 추출되었다.

![img](img/suninatas-21/3.png)

![img](img/suninatas-21/4.png)

플래그의 일부가 공개되어 있는 사진 여러 장을 확인하며 조합하면 플래그가 된다.

> FLAG: `H4CC3R_IN_TH3_MIDD33_4TT4CK`
