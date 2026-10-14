---
title: "How to put variable inside url_for in flask"
date: 2022-07-01T09:03:32+09:00
draft: false
category: Coding
---

잘못된 예시

```html
<img src="{{ url_for('static', filename='test/{{ variable[i] }}.png') }}" width="300px">`
```

올바른 예시

```html
<img src="{{ url_for('static', filename='test/') }}{{ variable[i] }}.png" width="300px">`
```
