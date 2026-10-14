---
title: "SuNiNaTaS 28 Solution"
date: 2022-01-25T21:57:59+09:00
draft: false
category: "Security"
---

## 문제 풀이 요약

1. zip 파일 구조 분석
2. flag bit 변조 후 압축 해제
3. base64 decode

## 문제 풀이

문제에서 zip 파일이 하나 주어진다.

![img](img/suninatas-28/1.png)

그러나 어떤 문자열을 입력해봐도 풀리지 않고, fcrackzip 과 같은 zip 크래킹 툴로도 해결되지 않는다. 이 파일은 비밀번호가 걸려 있지 않은데 걸린 척 위장되어 있는 파일이다.

![img](img/suninatas-28/2.png)

flag bit이 암호화를 담당하는 부분인데, 해당 값을 `00 00`으로 변조하면 된다.

![img](img/suninatas-28/3.png)

압축을 풀면 아래의 파일들이 나오고, `Am_I_Key.zip` 안의 `There_is_key.txt` 에 `dGE1dHlfSDR6M2xudXRfY29mZmVl` 가 나온다. 이 값을 base64 decode 하면 된다.

![img](img/suninatas-28/4.png)

> FLAG: `ta5ty_H4z3lnut_coffee`
