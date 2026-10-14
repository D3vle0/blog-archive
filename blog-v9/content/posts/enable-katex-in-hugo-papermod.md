---
title: "hugo-papermod 테마에서 Katex 활성화하기 ✏️"
date: 2025-02-10T17:53:00+09:00
draft: false
categories: ["Blog"]
tags: ["hugo", "katex"]
ShowToc: true
TocOpen: true
---

`/layouts/partials/extend_head.html` 파일에 아래의 코드를 작성한다.  

```html {linenos=true}
{{ if or .Params.math .Site.Params.math }}
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/katex@0.12.0/dist/katex.min.css" integrity="sha384-AfEj0r4/OFrOo5t7NnNe46zW/tFgW6x/bCJG8FqQCEo3+Aro6EYUG4+cU+KJWu/X" crossorigin="anonymous">
<script defer src="https://cdn.jsdelivr.net/npm/katex@0.12.0/dist/katex.min.js" integrity="sha384-g7c+Jr9ZivxKLnZTDUhnkOnsh30B4H0rpLUpJ4jAIKs4fnJI+sEnkvrMWph2EDg4" crossorigin="anonymous"></script>
<script defer src="https://cdn.jsdelivr.net/npm/katex@0.12.0/dist/contrib/auto-render.min.js" integrity="sha384-mll67QQFJfxn0IYznZYonOWZ644AWYC+Pt2cHqMaRhXVrursRwvLnLaebdGIlYNa" crossorigin="anonymous"
    onload="renderMathInElement(document.body);"></script>
{{ end }}

<script>
  document.addEventListener("DOMContentLoaded", function() {
      renderMathInElement(document.body, {
          delimiters: [
              {left: "$$", right: "$$", display: true},
              {left: "$", right: "$", display: false}
          ]
      });
  });
</script>
```