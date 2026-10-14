---
title: "맥에서 Tiling Window Manager 사용하기 (Yabai) / 고급스럽게 macOS 커스텀하기 🤩"
date: 2022-06-01
draft: false
categories: ["macOS"]
tags: ["yabai", "wm", "de", "Übersicht"]
cover:
  image: img/yabai/14.png
ShowToc: true
TocOpen: true
---

## 서문

macOS를 커스텀 해봤자 얼마나 할 수 있겠냐는 의문을 가진 사람에게 이 글을 추천한다. macOS 커스텀이 바탕화면 바꾸고, 바탕화면 아이콘 크기 조절하고, 독/메뉴바 숨기는 정도의 수준이라고 생각한다면 큰 오산이다. 이 글에서는 macOS에서 Linux의 느낌을 물씬 낼 수 있는 커스텀 방법을 소개하겠다.

## Window Manager

Window Manager란 그래픽 사용자 인터페이스 환경에서 데스크톱 상의 각 윈도를 관리할 목적으로 만들어진 소프트웨어를 가리킨다.

마이크로소프트 윈도우나 맥OS 등은 운영 체제의 일부로서 만들어져 있는 반면, X 윈도 시스템에서는 얼마든지 제3자가 만든 창 관리자를 사용할 수 있다. X 윈도용으로 잘 알려진 창 관리자로는 TWM, 플럭스박스 등이 있다. ([출처](https://ko.wikipedia.org/wiki/%EC%B0%BD_%EA%B4%80%EB%A6%AC%EC%9E%90))

Window Manager와 Desktop Environment이 개념적으로 어떻게 다른지 잘 알아야 한다. Desktop Environment는 전체적인 GUI 스타일과 환경을 담당하고, Window Manager는 창을 어떻게 관리할지를 담당하는 개념이다. 아래 사진을 보면 이해할 수 있을 것이다.

![img](/img/yabai/1.png)

이 환경은 KDE (Desktop Environment) 에 i3-gaps (Window Manager) 를 적용한 환경이라고 한다. 상태바를 보면 시계 형식, 데스크탑 간 전환 버튼, 아이콘 등을 보아 KDE인 것은 알 수 있지만 왼쪽 sublime text 창과 오른쪽 pdf 뷰어 창을 잘 보면 최소화, 최대화, 닫기 버튼이 없는데다가 두 창이 정확하게 같은 넓이로 반반씩 떠 있는 모습을 볼 수 있다. 이런 것이 바로 Window Manager가 하는 역할이다.

필자가 개인적으로 좋아하는 Window Manager는 `Fluxbox` 이다.

![img](/img/yabai/2.png)

바탕화면에서 마우스 오른쪽 클릭을 하면 모든 프로그램을 한번에 볼 수 있고 특히 순정 debian 에서는 상태바에 시계와 데스크탑 전환 버튼 외에 아무것도 없는 미니멀한 상태를 만들 수 있어서 좋다. 순정 상태에서는 90년대 컴퓨터 디자인이지만 물론 fluxbox 환경도 예쁘게 꾸밀 수 있다.

## Tiling Window Manager

> In computing, a tiling window manager is a window manager with an organization of the screen into mutually non-overlapping frames, as opposed to the more common approach of coordinate-based stacking of overlapping objects (windows) that tries to fully emulate the desktop metaphor. [출처](https://en.wikipedia.org/wiki/Tiling_window_manager)

Tiling Window Manager는 화면에 떠 있는 창을 서로 겹치지 않게 자동으로 창 위치와 크기를 조절하는 방식의 Window Manager 라고 할 수 있다. 예를 들어 3개의 창이 떠있으면 아래 사진처럼 자동으로 왼쪽에 넓게 하나, 오른쪽 위에 하나, 오른쪽 아래에 하나를 띄워주는 방식이다.

![img](/img/yabai/4.png)

만약 이 배치가 마음에 들지 않는다면 단축키를 이용해서 가로로 3분할, 세로로 3분할, 또는 원하는 창을 넓게 볼 수 있도록 지정할 수 있다. 일반적으로 사용하는 좌표 기반 스태킹 방식이 아니다 보니 원하는 창 크기로 세밀하게 조정하지 못하고 적응하기 매우 어렵다는 단점이 있지만 적응을 한다면 마우스를 사용하지 않고 창을 빠르게 이동할 수 있는 장점이 있다. 여기에 키보드로만 코딩할 수 있는 vim을 쓰는 환경이라면 그 품격이 더 올라간다고 할 수 있다. 여기서 **품격**이라는 단어를 선택한 이유는 컴퓨터 덕후, 특히 실사용에서도 리눅스를 쓰는 리눅스 덕후에게는 tiling window manager를 사용하는 것 자체가 멋이고 품격인데다가 자신의 개성을 뚜렷하게 나타낼 수 있는 수단이 되기 때문이다.

너무 다른 세상 이야기였는가? 여기까지가 서론이었고 지금부터는 macOS 사용자들도 리눅스에서만 보던 Tiling Window Manager 감성을 느낄 수 있는 프로그램을 소개하겠다.

## Yabai

[Yabai](https://github.com/koekeishiya/yabai)란 macOS 전용 Tiling Window Manager이다. m1 칩을 탑재한 맥을 기준으로 설명할 것이고, 설치에 앞서 먼저 시스템 무결성 보호를 해제해야 한다. 시스템 무결성 보호를 해제하면 `/system`, `/sbin`, `/bin`, `/usr` (/usr/local 제외), `/Applications` 디렉토리에 직접적으로 접근이 가능하다. **단 해제할 시 앱스토어에서 다운로드 받은 iOS, iPadOS 전용 앱을 실행할 수 없다.**

1.  > Shut Down
2. `Loading startup options` 가 나올 때 까지 전원 버튼을 계속 길게 누르고 있는다.
3. Options 클릭, 로그인
4. 메뉴 바에서 Utility - Terminal
5. `csrutil disable` 명령 입력
6. 재부팅

이제 Yabai를 다운로드 받는다.

```bash
brew install koekeishiya/formulae/yabai
brew service start yabai
```

시스템 환경설정의 Security & Privacy 항목에서 Yabai에 대한 Accessibility 권한을 주자. 아마 service 시작을 하면 자동으로 권한 설정창이 뜰 것이다.

```bash
sudo nvram boot-args=-arm64e_preview_abi
```

위 명령 입력 후 재부팅한다.

```bash
touch ~/.yabairc
chmod +x ~/.yabairc
nano ~/.yabairc
```

`~/.yabairc` 에는 다음과 같은 내용을 입력하자. `~/.yabairc`는 Yabai configuration 파일인데, github에서 제공하는 example 파일을 그대로 입력해보겠다.

```bash {linenos=true}
#!/usr/bin/env sh

# the scripting-addition must be loaded manually if
# you are running yabai on macOS Big Sur. Uncomment
# the following line to have the injection performed
# when the config is executed during startup.
#
# for this to work you must configure sudo such that
# it will be able to run the command without password
#
# see this wiki page for information:
#  - https://github.com/koekeishiya/yabai/wiki/Installing-yabai-(latest-release)
#
# sudo yabai --load-sa
# yabai -m signal --add event=dock_did_restart action="sudo yabai --load-sa"

# global settings
yabai -m config mouse_follows_focus          off
yabai -m config focus_follows_mouse          off
yabai -m config window_origin_display        default
yabai -m config window_placement             second_child
yabai -m config window_topmost               off
yabai -m config window_shadow                on
yabai -m config window_opacity               off
yabai -m config window_opacity_duration      0.0
yabai -m config active_window_opacity        1.0
yabai -m config normal_window_opacity        0.90
yabai -m config window_border                off
yabai -m config window_border_width          6
yabai -m config active_window_border_color   0xff775759
yabai -m config normal_window_border_color   0xff555555
yabai -m config insert_feedback_color        0xffd75f5f
yabai -m config split_ratio                  0.50
yabai -m config auto_balance                 off
yabai -m config mouse_modifier               fn
yabai -m config mouse_action1                move
yabai -m config mouse_action2                resize
yabai -m config mouse_drop_action            swap

# general space settings
yabai -m config layout                       bsp
yabai -m config top_padding                  12
yabai -m config bottom_padding               12
yabai -m config left_padding                 12
yabai -m config right_padding                12
yabai -m config window_gap                   06

echo "yabai configuration loaded.."
```

config 파일 적용하고 yabai service를 재시작하면 된다.

```bash
sudo yabai --install-sa
sudo yabai --load-sa
brew services restart yabai
```

당황하지 말자. yabai를 재시작하는 순간 열려있는 모든 창이 자동으로 크기와 위치가 정렬된다.

![img](/img/yabai/5.png)

그런데, 평소 리눅스에서 보던 그런 감성이 나오지 않는다. 아직 무언가 부족하다. 우리는 아래와 같은 예쁜 세팅을 원하는데 말이다.

![img](/img/yabai/3.jpeg)

이러한 감성을 내기 위해 simple-bar 라는 커스텀 메뉴 바 프로그램을 설치하겠다.

## simple-bar

simple-bar는 Yabai와 [Übersicht](http://tracesof.net/uebersicht/) 라는 맥 전용 위젯 프로그램과 같이 작동한다. Übersicht를 먼저 다운로드 받고 아래의 명령으로 simple-bar를 다운로드 받는다.

```bash
git clone https://github.com/Jean-Tinland/simple-bar $HOME/Library/Application\ Support/Übersicht/widgets/simple-bar
```

![img](/img/yabai/6.png)

메뉴바에서 Übersicht 아이콘을 클릭하고 `simple-bar-index-jsx` 가 들어왔는지 확인한다.

![img](/img/yabai/7.png)

바탕화면에서 화면 맨 위에 simple-bar가 표시되는데, 에러가 났다. 메뉴바에서 Übersicht 아이콘을 클릭하고 `show debug console`을 클릭하면 무엇이 에러를 발생시켰는지 확인할 수 있다.

![img](/img/yabai/8.png)

`/usr/local/bin/yabai` 가 존재하지 않는 경로인 듯 하다. 그렇다면 `open widgets folder` 로 simple-bar 위젯 경로를 열어 `init.sh` 스크립트 내용을 확인해야 한다. 스크립트 맨 윗줄에 `yabai_path=$1` 라는 구문이 있는데, 이것이 실제 Yabai가 설치된 경로가 아닌 다른 경로로 잘못 인식하고 있다. `yabai_path` 변수에 Yabai가 설치된 경로를 직접 하드코딩으로 집어 넣겠다.

```bash
where yabai
```

![img](/img/yabai/9.png)

![img](/img/yabai/11.png)

저장하고 `refresh all widgets` 를 클릭하니 simple-bar가 제대로 활성화되었다.

![img](/img/yabai/10.png)

세팅은 잘 된 것 같은데, 이 부분을 눌러도 아무 반응이 없다.

![img](/img/yabai/12.png)

debug console을 확인해보니 이번에도 역시 Yabai 경로가 잘못 지정되어 있었다. 위젯 디렉토리 안에 있는 `/usr/local/bin/yabai` 문자열을 모두 올바른 경로로 설정하고, local storage를 비운 뒤 refresh 하자.

```bash
grep -rn "/usr/local" *
```

![img](/img/yabai/13.png)

## 추가 설정

현재는 simple-bar가 바탕화면 맨 밑에 위치해 있기도 하고 창을 띄우면 yabai에 의해 위치가 겹치게 된다. `~/.yabairc` 에서 top padding 값을 40으로 바꾸고 yabai를 재시작 한다.

## 최종 세팅

![img](/img/yabai/14.png)

![img](/img/yabai/15.png)

## 여담

참 아이러니하게도 Windows와 Linux 환경을 사용할 때는 맥북이 갖고 싶어 macOS 처럼 커스텀하게 되고 (ubuntu + gnome, arch + kde 환경에서 커스텀을 해보았다), 막상 리얼맥을 갖게 되니 리눅스처럼 꾸미고 싶어진다. 역시 인간은 결핍을 추구하는 존재인 것인가. ~~다만 macOS에서 Windows처럼 커스텀하는 것은 시도조차 안했다. `*nix` 운영체제가 최고다.~~

아쉬운 점이 있다면 simple-bar를 조작할 때 숨김 처리 되어있던 macOS 기본 메뉴바가 튀어 나오는 불편함이 있다. 물론 기본 메뉴바에 앱 조작 화면과 블루투스 등 필요한 기능이 많기 때문에 기본 메뉴바가 없어서는 안된다. 맥에서 dock은 명령어를 사용해서 사이즈를 조절하거나 dock 방향으로 커서를 갖다댔을 때 dock이 올라오는 시간을 지정할 수가 있다. 메뉴바 방향으로 커서를 갔다댔을 때 3초 후 메뉴바가 나오는 것이 가상 이상적인 세팅이라고 생각하는데, 아쉽게도 OS X El Capitan 이상에서 메뉴바 automatically hide 여부를 설정하는 명령어밖에 찾지 못했다. 메뉴바도 dock처럼 시간 설정을 할 수 있으면 정말 좋을 것이다.
