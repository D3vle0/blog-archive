---
title: "정체불명의 글자?"
date: 2025-03-02T15:34:15+09:00
draft: false
categories: ["Coding"]
tags: ["unicode", "punycode"]
cover:
  image: img/mysterious-character/6.png
ShowToc: true
TocOpen: true
---

## 사건의 발단

고등학교 때 만든 발표 자료들을 살표보던 중, 수학 시간에 Fast Fourier Transform에 대해 발표한 자료를 보다가 사건이 시작되었다.

![img](/img/mysterious-character/1.png)

대략적으로 푸리에 급수, 변환, 활용 예시, DFT, FFT에 대해 설명하고 FFT의 원리를 활용한 프로그램을 개발한 것이 내용이다.

![img](/img/mysterious-character/2.png)

FFT의 원리를 활용해 간단한 Audio Visualizer를 웹으로 구현했고, 발표 후반부에 직접 시연을 하기 위해 위와 같이 링크를 첨부했다.  

분명히 내 기억으로는 해당 repository의 github pages가 활성화가 되어있는데 링크를 접속해보니 `404`가 떴다.

![img](/img/mysterious-character/3.png)

![img](/img/mysterious-character/4.png)

그러나 github pages는 당연하게도 활성화가 되어있었고 github을 통해서 사이트에 들어가면 정상적으로 접속이 되었다.

## 원인

![img](/img/mysterious-character/5.png)

이게 말이 되는가? 학교에서 발표를 했을 당시에도 링크 접속에는 전혀 문제가 없었다. 이해할 수 없는 상황에 고민하다가, `404`가 뜬 탭과 정상적으로 접속된 탭의 url을 유심히 비교해 본 결과...

![img](/img/mysterious-character/6.png)

`404`가 뜨는 url(아래쪽)의 fft 글자 자간이 비교적 짧다는 것을 발견했다. 잘못된 url은 사실 `fft`가 아니라 `ﬀt`였고 이는 ff가 하나로 붙어있는 기괴한 글자다.  
`ﬀ`의 정체가 무엇인지 알아내기 위해 구글에 `ff`와 `ﬀ`를 각각 검색해 보았지만, 검색 결과는 둘 다 `ff`의 결과가 출력되었다.

![img](/img/mysterious-character/7.png)

유니코드 문자를 검색해보니 이는 U+FB00에 할당된 LATIN SMALL LIGATURE FF이다. 정말로 소문자 f(U+0066) 두개를 합쳐 만든 글자였다.  
발표했을 당시에는 이런 문제가 없다가 갑자기 이런 현상이 일어난 이유가 궁금해져서 여러 테스트를 진행했다.

![img](/img/mysterious-character/8.png)

finder에서 pdf 파일을 더블클릭해서 연 상태일 때는 문제가 생기지 않는데, space를 누른 '미리보기' 상태일 때는 `ff`를 `ﬀ`로 렌더링하고 있었다. 링크를 눌러보지 않는 한 겉으로 봤을 때는 전혀 차이를 느끼지 못했기 때문에 더 혼란스러웠다.

## 잠재적인 보안 문제

URL의 특수문자를 punycode로 변환할 때는 IDNA(Internationalized Domain Names for Applications) 규격에 따라 처리가 이루어진다. 이 과정에서는 [Unicode® Technical Standard #46](https://unicode.org/reports/tr46/)에 정의된 정규화 절차(NFKC)가 적용되는데, 이 정규화 과정에서 유니코드 호환 문자인 `ﬀ`(U+FB00)는 두 개의 `f`로 분해된다. 즉 `ﬀ`가 들어간 도메인은 정규화 후 순수한 ASCII 문자열 `ff`가 되므로 추가적인 punycode 변환 대상이 되지 않는다.  

Punycode가 [homograph attack을 예방하기 위한 수단이 되기도 하는데](https://www.blazeinfosec.com/post/homographs-attack/) 내가 겪은 경우는 아직 대응이 안 되어 있다는 사실을 깨달았다. 따라서 macOS의 '미리보기'에서 문자를 렌더링하는 방식에 수정이 필요해 보이고, 더 많은 특수문자들이 punycode로 변환될 수 있도록 웹이 변화되어야 할 것이다. 일반 사용자 입장에서는 모르는 url을 클릭할 때 double check, triple check 해야 한다.


