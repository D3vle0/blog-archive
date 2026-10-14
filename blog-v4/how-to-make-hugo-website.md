# How to Make Hugo Website


## 🧑‍💻 정적 웹사이트 생성기

로그인, 회원가입 등의 동적인 작업이 필요 없고 고정된 컨텐츠만을 제공하는 사이트를 정적 웹사이트 (Static Site) 라고 한다.
정적 웹사이트를 생성해주는 툴은 여러가지가 있는데, Github Stars 순으로 대표 6개는 아래와 같다.

1. Next.js
    - Language: Javascript
    - Templates: React
2. Hugo
    - Language: Go
    - Templates: Go
3. Gatsby
    - Language: Javascript
    - Templates: React
4. Jekyll
    - Language: Ruby
    - Templates: Liquid
5. Nuxt
    - Language: Javascript
    - Templates: Vue
6. Hexo
    - Language: Javascript
    - Templates: EJS, Pug, Haml ...

## 👊 내가 Hugo 를 선택한 이유

Hugo는 Go 언어로 작성되어 있어 최신 트렌드 기술들이 반영된다. 또한 다른 정적 사이트 생성기에 비해 빌드 속도가 빠르고, Github 스타 랭킹 2위로 치고 올라올 만큼 굉장히 핫하고, 점점 관련 자료들도 많아지는 추세다. 다만 테마가 엄청나게 많은 편은 아니라 내 마음에 완벽히 맞는 테마는 없었고, 그나마 마음에 들어 선택한 테마가 [지금 사용중인 이 테마다](https://themes.gohugo.io/themes/loveit/).

### 내가 좋아하는 테마의 구성은?

- Light/Dark 모드 전환이 있어야 함
- 글에 태그를 붙일 수 있어야 함
- 카테고리는 없어도 상관 없음
- Search 기능이 있으면 좋겠다 (필수는 아님)
- 코드 포매팅

## Setup

### Hugo 설치

```bash
brew install hugo

hugo new site <블로그 이름>
```

### 테마 추가

```bash
cd <블로그 이름>

git init

git submodule add <테마 링크> themes/<테마 이름>
```

또는

```bash
cd <블로그 이름>

git init

git clone <테마 링크> themes/<테마 이름>
```

테마별로 주어진 config 파일을 수정하여 자신의 블로그 이름, 소개, 프로필 사진 등을 커스텀 한다.

## 글 작성

```bash
hugo new posts/<글 제목>.md
```

모든 글은 markdown으로 작성 후 빌드한다.

```md
---
title: "How to Make Hugo Website"
date: 2021-07-20T12:23:34+09:00
draft: false
---
```

빌드할 때는 `draft: false` 로 바꿔준다.

## 로컬에서 테스트

```bash
hugo server -D
```

`localhost:1313` 에서 작성한 글이 제대로 표시 되는지 확인 후 빌드한다.

## 빌드

```bash
hugo -D
```

HTML로 변환된 사이트는 `/public` 폴더에 기록된다. 이 파일을 Github 에 업로드하여 호스팅 해도 되고, 개인 서버에 올려 정적으로 호스팅을 해도 된다.

