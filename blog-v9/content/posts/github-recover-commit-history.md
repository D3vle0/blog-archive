---
title: "GitHub 잘못된 커밋 이메일 복구하기 (잔디 복구) 🌿"
date: 2026-04-06T13:21:52+09:00
draft: false
categories: ["Coding"]
tags: ["github", "commit", "email"]
ShowToc: true
TocOpen: true
---

## 자동화 스크립트

```sh
brew install git-filter-repo
```

```sh
git filter-repo --commit-callback '
if commit.author_name != b"dependabot[bot]":
    commit.author_name = b"D3vle0"
    commit.author_email = b"44903787+D3vle0@users.noreply.github.com"
if commit.committer_name != b"dependabot[bot]":
    commit.committer_name = b"D3vle0"
    commit.committer_email = b"44903787+D3vle0@users.noreply.github.com"
'
```

내 이름과 이메일이 아닌 커밋을 내 커밋으로 변경하는 스크립트다.

## 적용 결과
