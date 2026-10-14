---
title: "A Complete Guide to Building Your Own Home Server"
date: 2022-05-15T08:48:42+09:00
draft: false
category: "Server"
---

![a](img/homeserver-guide/1.jpeg)

## 서문

이 글은 홈서버를 구축하기 위한 완전한 가이드다. 홈서버의 개념, 장점, VPS와의 차이, 운영체제 설치와 각종 툴 설치에 대한 내용을 설명하고 있다. 아래에서 설명하는 내용은 반드시 중요 자료를 백업한 후에 진행해야 한다.

## 홈서버란?

홈서버란 집에 나만의 서버를 구축하는 것이다. 집에 서버를 구축하는 이유는 재미, 학습의 이유, 동영상 다운로드나 토렌트와 같은 반복적인 업무를 수행하기 위한 목적이 있다. 데이터 센터에서 VPS 서버를 대여하는 것이 네트워크 안정성, 화재에 대한 위험에 대한 측면으로는 훨씬 낫지만, 하드웨어 업그레이드가 용이, 무엇보다 재미가 있기 때문에 홈서버를 구축하는 사람들도 적지 않다.

## 장비

- 미니 PC
- 오래된 노트북
- Raspberry Pi
- 스마트폰

위에서 언급한 장비 모두 전력 소모량이 적은 장비다. 데이터 센터에서 서버를 대여하는 것 보다 이득을 보려면 당연히 전기세가 적게 나올수록 좋다. 따라서 전력 대비 성능이 괜찮은 장비를 구하는 것이 중요한데, 만약 리눅스 운영체제를 체험해보고 싶거나 정말 간단한 파일 백업 스토리지로 사용할 용도라면 스마트폰 (안드로이드가 리눅스 기반이다)도 좋고, 서버에서 개발을 하거나 웹서비스를 돌릴 용도라면 오래된 노트북을 추천한다. 만약 서버 장비를 설치할 공간이 정전이 잘 되거나 하루종일 전원을 공급받을 수 없는 장소라면 UPS 역할을 해주는 오래된 노트북이나 스마트폰을 두는 것이 좋다.

### 미니 PC

![a](img/homeserver-guide/2.jpeg)

20~30만원 대에 구할 수 있는 베어본 미니 PC는 리눅스 서버로 쓰기 무난한 CPU, 낮은 TDP를 가지고 있다. 깔끔한 세팅, 디자인을 신경 쓴다면 구매해도 좋다. 단 서버로 쓰일 PC인 만큼 중고로 싸게 사면 좋다.

### 오래된 노트북

![a](img/homeserver-guide/3.jpeg)

CPU 모델마다 상이하겠지만 일반적인 CPU가 탑재되어 있다면 위 네 가지 선택지 중에서는 전력 소모가 가장 클 것이다. 그래서 노트북을 서버로 사용하고 싶다면 Intel CPU 기준 U가 붙은 CPU가 탑재된 노트북을 선택하는 것이 좋다. 일반적인 데스크탑 CPU의 TDP는 65W 이상, 모바일 CPU는 25~35W 정도, 저전력 CPU는 20W 미만이다. 만약 남는 노트북을 가지고 있고 그것이 저전력 CPU가 탑재된 것이라면 고민하지 말고 바로 서버를 구성하자. 필자는 10년된 i5-2450M 노트북을 서버로 사용 중이고, 이 글에서도 오래된 노트북을 기준으로 설명하겠다.

### Raspberry Pi

![a](img/homeserver-guide/4.jpeg)

IoT 또는 하드웨어에 관심이 많은 사람이라면 라즈베리 파이에 리눅스를 올려서 사용하는 것도 방법이다. 전력 소모도 적어 전기세 걱정은 크게 필요 없는데, 가격이 저렴한 만큼 성능은 기대하기 힘들다. 성능을 중요시 하다면 그냥 10년 된 노트북을 사용하자.

### 스마트폰

![a](img/homeserver-guide/5.jpeg)

제대로 사용하려면 루팅이 필요하고, 복잡하고 불편하다. TDP는 2.5W 수준으로 전기세는 전혀 걱정할 필요가 없겠으나 추천하지는 않는다. 정말 단순한 단일 페이지를 호스팅 하기에는 나쁘지 않다.

## 리눅스 배포판 추천

오래된 노트북을 서버로 사용하기 때문에 당연히 리눅스를 사용하는 것이 필수적이다. 부팅되려면 한참 걸리고 바탕화면에서도 버벅거리던 윈도우 노트북에 리눅스를 올리면 메모리 점유율도 적고 속도가 굉장히 빨라진다. 그만큼 윈도우가 꽤 무거운 OS인 것을 알 수 있다.

만약 당신이 리눅스 서버를 처음 접한다면 그냥 **Ubuntu Server**로 선택하는 것이 좋다. 가장 보편화된 리눅스 배포판이기에 자료도 많다. 가볍고 미니멀한 세팅을 원한다면 Arch Linux, 서버의 정석 CentOS 등이 있지만 debian 기반 배포판에서 오는 익숙함을 버리기는 쉽지 않다.

여기서 Ubuntu Desktop과 Ubuntu Server의 차이를 설명해보자면, Ubuntu Desktop은 GUI가 탑재되어 있고 Firefox, gedit, LibreOffice 등 각종 응용 프로그램들이 기본적으로 설치되어 있다. Ubuntu Server는 서버 관리자를 위해서 GUI가 없고 (필요 시 설치 가능), 앞서 언급한 응용 프로그램들이 설치되어 있지 않다. 그렇게 떄문에 Ubuntu Server와 Desktop 버전 권장 사양도 다르다.

**Ubuntu Server**

- 512MB RAM
- 2.5GB HDD
- 1GHz CPU

**Ubuntu Desktop**

- 2GB RAM
- 10GB HDD
- 2GHz CPU

## Ubuntu Server 22.04 LTS 설치

### 이미지 플래시

짝수년도 4월마다 향후 5년간 각종 업데이트와 보안 패치를 지원하는 Long Term Support 버전이 출시되는데, 지난 4월 22.04 버전이 새롭게 출시되었다. 필자는 홈서버에 20.04 버전이 설치되어 있는데 이번 기회에 22.04로 업그레이드 (클린 설치) 하면서 글을 작성해보겠다.

[이곳에서 Ubuntu Server 22.04를 설치할 수 있다.](https://releases.ubuntu.com/22.04) 사이트에 들어가서 `ubuntu-22.04-live-server-amd64.iso` 를 다운받자.

![a](img/homeserver-guide/6.png)

또한 iso 이미지를 usb에 구워야 하기 떄문에 [BalenaEtcher라는 프로그램도 설치한다.](https://www.balena.io/etcher/)

![a](img/homeserver-guide/7.png)

먼저 노트북에 USB를 꽂는다. BalenaEtcher에서 `Flash from file`을 눌러 ubuntu server 22.04를 넣고, `Select Target`에서 USB를 선택한다.

![a](img/homeserver-guide/8.png)

![a](img/homeserver-guide/9.png)

![a](img/homeserver-guide/10.png)

iso 파일을 USB에 플래시하는데 성공했다.

### 서버 노트북에 부팅

서버 노트북에 USB를 꽂고 BIOS에 진입한다. 필자가 가지고 있는 삼성 노트북 기준, F2로 BIOS (Phoenix SecureCore Tiano Setup) 에 진입할 수 있다. 그 다음 `Boot` 메뉴에서 `Boot Device Priority` 를 선택하고 연결한 USB를 맨 위로 올린다. 재부팅을 하면 GRUB이 등장하는데, `Try or Install Ubuntu Server`를 선택한다.

### 본격적인 세팅

2~3분 정도 기다리면 이런 화면이 뜰 것이다.

![a](img/homeserver-guide/11.png)

언어 선택 화면인데, 한국어는 없으니 영어를 선택하고 엔터를 누르자. 키보드 레이아웃은 건드리지 말고 Done을 선택한다.

![a](img/homeserver-guide/12.png)

일반 설치 또는 미니멀 설치를 할 것인가를 물어보는 항목인데, 필요한 패키지는 추후에 설치해도 되고 지금은 가벼운 리눅스 운영 체제를 접하고 싶기 때문에 미니멀 설치로 하겠다.

다음은 네트워크 설정인데, 노트북에 랜선이 꽂혀 있고 랜카드가 정상적으로 작동한다면 DHCP에 의해 자동으로 IP를 할당받게 된다. 노트북에서 와이파이도 같이 사용하고 싶다면 아마 무선랜에 대한 네트워크 인터페이스가 하나 더 뜰 것인데, `wlan not connected` 를 선택하고 `Edit Wifi`, `Choose a visible network`를 누른다. 그러면 사용 가능한 와이파이가 검색되고, 원하는 와이파이를 선택하여 비밀번호를 입력하면 된다. 필자의 경우 유선은 `enp3s0`이 `192.168.219.107`을, 무선은 `wlp2s0`이 `192.168.219.108`을 할당한 상태이다. 완료되면 Done을 눌러 진행한다.

![a](img/homeserver-guide/13.png)

프록시는 필요 없으니 넘어간다.

![a](img/homeserver-guide/14.png)

다음은 미러사이트를 설정하는 항목인데 미러사이트는 각종 우분투 패키지를 모아놓은 저장소라고 생각하면 된다. 이 작업을 한국에서 진행하고 있다면 기본값으로 `http://kr.archive.ubuntu.com/ubuntu`가 나올 것인데 필자는 카카오 미러로 변경하겠다. 입력창의 텍스트를 지우고 `http://mirror.kakao.com/ubuntu`로 변경한다.

다음은 스토리지 설정인데, 여기서 실수하지 말고 잘 따라야 한다. 필자는 이미 설치되어 있던 20.04 데이터를 삭제하고 다시 세팅을 하기 위해 Custom Layout Storage를 선택하겠다. 화면 아래쪽의 Reset을 눌러 초기화 시키고 시작한다. `Available Devices` 맨 위에 하드디스크 이름이 나오는데, 그곳에 엔터를 누르고 `Reformat` 을 한다. 그러면 `Available Devices`에 하드디스크랑 Free Space만 뜨는 것을 볼 수 있다. 하드디스크 선택 후 `Use As Boot Device`를 누르면 아래쪽 `USED DEVICE` 항목에 partition 1이 들어가있는데 이 용량을 `512M` 으로 조정한다. 그리고 Free Space > Add GPT Partition을 누르고 Size `8G`, Format `SWAP`, Create 한다. 다시 Free Space > Add GPT Partition, Size를 비워두고 Format은 `ext4`, Mount `/`, Create 한다. 이때 `FILE SYSTEM SUMMARY` 에 다음과 같은 표가 만들어진다.

|MOUNT POINT|SIZE|TYPE|
|---|---|---|
|/|457.259G|new ext4|
|/boot/efi|512.000M|new fat32|
|SWAP|8.000G|new swap|

Done, Continue로 계속한다.

![a](img/homeserver-guide/15.png)

Your name은 사용자 이름, Your server's name은 호스트 (컴퓨터) 이름, Pick a username은 사용자 계정 이름 (Your name이랑 동일하게 입력하는 것이 편하다), Choose a password에는 해당 계정 비밀번호를 입력하고 Done을 누른다.

![a](img/homeserver-guide/16.png)

원격 접속을 위한 ssh 서버를 설치하자. `Install OpenSSH Server` 에 체크하고 Done. 드라이버를 찾았다고 하면 다운로드 하자.

![a](img/homeserver-guide/17.png)

원하는 개발 관련 소프트웨어를 설치하라고 하는데, 필자는 Docker만 설치하고 넘어가겠다. Done을 누르면 설치 로그가 나오고, 몇 분 기다리면 설치가 끝나며 Reboot Now 버튼이 활성화 된다. 재부팅을 하다보면 어느 순간 화면이 멈추며 `Please remove the installation medium, then press ENTER!` 문구가 나오는데 이는 USB를 빼고 엔터를 누르라는 뜻이다.

## 설치 완료

```txt
Ubuntu 22.04 LTS <호스트 이름> tty1

<호스트 이름> login:
```

이런 글자가 뜨면 성공한 것이다. 지금부터는 실사용 하는 노트북으로 서버에 원격접속을 할 수 있다. 아까 DHCP 설정에서 할당받았던 아이피를 기억하는가?

```sh
ssh 유저이름@호스트이름
```

비밀번호를 입력하면 접속을 할 수 있다.

![a](img/homeserver-guide/18.png)

원래 사용하던 20.04를 초기화하고 22.04를 클린 설치한 만큼 세팅 할 것이 많은데, 지금부터 아래의 내용들을 세팅하며 그 방법을 설명하겠다.

- 패키지 설치
- 노트북 덮개 설정
- 쉘 설정
- 백업한 것 서버로 복사
- git 설정
- docker 세팅 (ctf 문제 서버)
- nodejs 설치
- ftp 서버 설정
- irc 서버 설정
- 보안 설정

## 패키지 설치

앞서 설명했듯 이 리눅스는 미니멀 설치 버전이기 때문에 그 흔한 vim, htop, git도 없다. 따라서 기본적인 패키지들은 먼저 설치하고 가겠다.

```sh
sudo apt-get update -y
sudo apt-get upgrade -y
sudo apt-get install vim nano htop git net-tools
```

## 노트북 덮개 설정

현재 상태에서는 노트북 덮개를 덮으면 전원이 꺼진다. 리눅스에 절전 모드 따위는 없다. 오래된 노트북을 서버로 사용하기에 모니터를 직접 볼 일은 거의 없어서 노트북 덮개를 닫아놓고 있어야 하는데, 이때 덮개를 닫아도 전원이 꺼지지 않게 설정하는 방법이 있다.

```sh
sudo vi /etc/systemd/logind.conf
```

`HandleLidSwitch=ignore`로 변경하고

```sh
sudo systemctl restart systemd-logind
```

를 하면 된다.

## 쉘 설정

우분투 기본의 bash 쉘은 매우 불편하기 때문에 oh-my-zsh + p10k 환경을 설정할 것이다.

```sh
sudo apt-get install zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/themes/powerlevel10k
```

`~/.zshrc` 의 `robbyrussell` 부분을 `powerlevel10k/powerlevel10k`로 변경한다.

```sh
source ~/.zshrc
```

![a](img/homeserver-guide/19.png)

## 백업한 것 서버로 복사

```sh
scp -r file-server/ devleo@192.168.219.107:/home/devleo/file-server
```

## git 설정

private repo를 clone 하고 ID/Token을 입력한 후에 아래의 명령을 입력하면 토큰이 서버에 저장되어서 pull, clone, push 할때 토큰을 직접 입력하지 않아도 된다.

```sh
git config credential.helper store
```

## docker 세팅 (ctf 문제 서버)

```sh
sudo apt install python3-pip netcat
pip3 install pwn
```

![a](img/homeserver-guide/20.png)

![a](img/homeserver-guide/21.png)

## nodejs 설치

```sh
sudo apt install npm
```

![a](img/homeserver-guide/22.png)

```sh
npm cache clean -f
sudo npm install -g n
sudo n lts
sudo npm i -g npm
```

![a](img/homeserver-guide/23.png)

## ftp 서버 설정

```sh
sudo apt install vsftpd
sudo nano /etc/vsftpd.conf
```

`/etc/vsftpd.conf` 내용을 아래의 내용으로 바꾼다.

```conf
listen=YES
listen_ipv6=NO
anonymous_enable=NO
port_enable=NO
pasv_enable=YES
local_enable=YES
write_enable=YES
use_localtime=YES
xferlog_enable=YES
#chroot_local_user=YES
#allow_writeable_chroot=YES
secure_chroot_dir=/var/run/vsftpd/empty
pam_service_name=vsftpd
ftpd_banner=This is Devleo's FTP server.
ssl_enable=YES
rsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem
rsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key
listen_port=21
pasv_min_port=60020
pasv_max_port=60030
```

```sh
sudo systemctl enable vsftpd
sudo systemctl restart vsftpd
```

![a](img/homeserver-guide/24.png)

## irc 서버 설정

[이 글을 참고하면 된다.](https://devleo.wtf/posts/irc/)

## 후기

세팅하는 내내 단 하나의 에러도 직면하지 않았다. 18.04처럼 매우 순조로웠으며, 각종 에러 투성이었던 20.04와 다르게 세팅하기도 쉬웠다. nodejs 관련 에러도 나지 않고 20.04에서 사용하던 서비스와 세팅들 모두 conflict 없이 잘 작동했다. 기본 탑재된 python 버전이 3.10.4인 만큼 python을 업데이트 할 필요도 없어 편하다. 현재 서버에서 docker 컨테이너 2개, nodejs 웹앱 1개 (더 늘어날 예정), ftp 서버까지 돌아가고 있는데 메모리 사용량은 6G 중에 455M 밖에 되지 않는다. 아무것도 돌리지 않던 순정 상태에서는 약 200M의 메모리를 사용했다. 앞으로는 gnome desktop을 설치하여 20.04와 GUI 환경도 비교해볼 생각이다.
