---
title: "SuNiNaTaS 18 Solution"
date: 2022-01-25T21:06:14+09:00
draft: false
category: "Security"
---

## 문제 풀이 요약

1. 주어진 문자열을 ascii code에서 문자열로 변경
2. base64 decode

## 문제 풀이

`86 71 57 107 89 88 107 103 97 88 77 103 89 83 66 110 98 50 57 107 73 71 82 104 101 83 52 103 86 71 104 108 73 69 70 49 100 71 104 76 90 88 107 103 97 88 77 103 86 109 86 121 101 86 90 108 99 110 108 85 98 50 53 110 86 71 57 117 90 48 100 49 99 109 107 104`

문제에는 위의 숫자들이 주어지는데, 모든 숫자들이 0부터 255 범위에 있는 것으로 보아 ascii code 임을 알 수 있다.  
문자열로 변경하면 `VG9kYXkgaXMgYSBnb29kIGRheS4gVGhlIEF1dGhLZXkgaXMgVmVyeVZlcnlUb25nVG9uZ0d1cmkh` 가 된다. 다시 이 값을 base64 decode 하면 key가 나온다.

```py
__import__("base64").b64decode("".join([chr(int(i)) for i in "86 71 57 107 89 88 107 103 97 88 77 103 89 83 66 110 98 50 57 107 73 71 82 104 101 83 52 103 86 71 104 108 73 69 70 49 100 71 104 76 90 88 107 103 97 88 77 103 86 109 86 121 101 86 90 108 99 110 108 85 98 50 53 110 86 71 57 117 90 48 100 49 99 109 107 104".split()])).decode('ascii')
```

![img](img/suninatas-18/1.png)

> FLAG: `VeryVeryTongTongGuri!`
