# Spotify를 더 예쁘게! Spicetify 사용법

## 설치

[이곳에서](https://github.com/khanhas/spicetify-cli) spicetify 프로그램을 받고, [이곳에서](https://github.com/morpheusthewhite/spicetify-themes/tree/master) spicetify 전용 테마를 다운받는다.

```bash
git clone https://github.com/khanhas/spicetify-cli
git clone https://github.com/morpheusthewhite/spicetify-themes
```

brew 를 통해서 spicetify-cli 를 받을 수도 있지만 테마 관리의 용이성을 위해 git clone 하는 것을 추천한다.

## 적용

```bash
cd spicetify
./spicetify
```

spicetify 바이너리를 실행하면 config 파일이 생긴다.

```bash
./spicetify backup apply enable-devtool
```

로 기본 순정상태로 백업을 해준다.  
이제 테마를 적용해야 하는데, 조금 전 테마를 다운받았던 레포에서 폴더 이름이 테마 이름, 해당 폴더 안의 이미지 파일 명이 색상 이름이다.

```bash
./spicetify config current_theme <테마이름> color_scheme <색상이름>
./spicetify apply
```

apply 명령을 입력해주면 적용이 되고, 자동으로 spotify가 실행된다.

## stuck in a blank screen

만약 빈 화면에서 멈추었다면 (특히 m1 맥에서 해당 증상이 일어남), 아래의 단계를 따른다.

1. [이곳에서](https://github.com/itsmeowForks/spicetify-cli/releases/tag/v2.5.0-patch2) 자신의 운영체제에 맞는 바이너리와 `spicetifyWrapper.js` 파일을 다운받는다.

2. 바이너리 파일을 `spicetify-cli` 폴더로 집어넣고 바이너리 이름을 `spicetify`로 바꾼 뒤 replace 한다.

3. `spicetifyWrapper.js` 파일을 `jsHelper` 폴더로 집어넣고 replace 한다.

4. `./spicetify config disable_sentry 0`

5. `./spicetify restore backup apply`

다시 테마를 적용해주면 된다.

## 적용 이미지

![img](https://cdn.discordapp.com/attachments/878061708122415125/878061770382643220/unknown.png)
![img](https://cdn.discordapp.com/attachments/878061708122415125/878833300213485578/unknown.png)
![img](https://cdn.discordapp.com/attachments/878061708122415125/878837289780908052/unknown.png)

