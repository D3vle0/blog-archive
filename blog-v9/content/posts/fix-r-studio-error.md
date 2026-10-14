---
title: "맥북에서 R Studio 설치 오류 해결 💡"
date: 2025-06-18
draft: false
categories: ["macOS"]
tags: ["macos", "rstudio", "r"]
ShowToc: true
TocOpen: true
---

> 2022.03.08. 작성된 글입니다.

## R 설치
[이곳에서](https://cran.r-project.org/bin/macosx/) 모든 버전을 볼 수 있고, [여기서](https://cran.r-project.org/bin/macosx/big-sur-arm64/) m1 맥을 위한 R을 설치할 수 있다.

## R Studio 설치
R 언어를 쉽게 다룰 수 있도록 R Studio를 설치할 것이다.
[이곳에서](https://www.rstudio.com/products/rstudio/download/#download) 맥 버전을 다운받을 수 있고, Intel 기반이라 m1에서는 rosetta 위에서 돌아가게 된다.

## R Studio 설치 에러 해결
`dyld[12119]: terminating because inserted dylib '/Library/Frameworks/R.framework/Resources/lib/libR.dylib' could not be loaded:`

이런 식의 에러가 난다면 아래의 명령어를 통해 해결할 수 있다.
```sh
cd /Applications/Rstudio.app/Contents/MacOS
mv rsession rsession-x86
ln -s rsession-arm64 rsession
xattr -r -d com.apple.quarantine /Applications/RStudio.app
```
