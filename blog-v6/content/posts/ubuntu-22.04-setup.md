---
title: "Perfect way to set up Ubuntu 22.04"
date: 2022-09-05T09:01:39+09:00
draft: false
tags: ["linux", "server"]
---

## 서문

Windows 7을 약 2주 간 서버로 사용한 후 다시 Ubuntu 22.04로 돌아왔다. OBS 송출 테스트를 위해 Ubuntu Server 대신 Ubuntu를 설치했고, 완전한 순정 상태다. 이번 글에서는 Ubuntu 22.04를 서버로 사용하기 편리하게 세팅하는 방법을 소개하겠다. [이 글과 겹치는 내용이 있으나](/posts/homeserver-guide/) 추가적으로 업데이트 된 내용도 있다. 반드시 순정 상태에서, 홈 디렉토리에서 해야 문제가 생기지 않는다.

## ✅ 패키지 업데이트

```sh
sudo apt-get update -y
sudo apt-get upgrade -y
```

## 🪡 각종 인프라 유틸리티

```sh
sudo apt-get install htop net-tools neofetch cpufetch
```

## 👸 터미널을 예쁘게 만들기

```sh
sudo apt-get install zsh git curl -y
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ~/powerlevel10k
echo 'source ~/powerlevel10k/powerlevel10k.zsh-theme' >>! ~/.zshrc
source ~/.zshrc
```

## ⚒️ zsh 플러그인 설치

```sh
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
sed -i 's/plugins=(git)/plugins=(git zsh-autosuggestions)/g' ~/.zshrc
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
echo "source ~/.oh-my-zsh/custom/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh" >> ${HOME}/.zshrc
source ~/.zshrc
```

## 🔋 노트북 덮개 덮어도 전원 유지

```sh
sudo sed -i 's/#HandleLidSwitch=suspend/HandleLidSwitch=ignore/g' /etc/systemd/logind.conf
sudo systemctl restart systemd-logind
```

## 🔐 git credential 세팅

```sh
git clone <개인 repo>
cd <개인 repo>
git config --global credential.helper store
```

## 💻 nodejs 설치 및 개발 환경 세팅

```sh
sudo apt install nodejs -y
sudo apt install npm -y
sudo npm cache clean -f 
sudo npm install -g n
sudo n stable
sudo npm install -g yarn nodemon pm2 typescript ts-node
```

## 💻 pip 설치

```sh
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
python3 get-pip.py
PATH=$HOME/.local/bin:$PATH
echo "alias p='python3'" >> ~/.zshrc
source ~/.zshrc
```

## 📲 ftp 서버 구축

```sh
sudo apt install vsftpd -y
sudo sh -c "echo -e 'listen=YES\nlisten_ipv6=NO\nanonymous_enable=NO\nport_enable=NO\npasv_enable=YES\nlocal_enable=YES\nwrite_enable=YES\nuse_localtime=YES\nxferlog_enable=YES\nsecure_chroot_dir=/var/run/vsftpd/empty\npam_service_name=vsftpd\nssl_enable=YES\nrsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem\nrsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key\nlisten_port=21\npasv_min_port=60020\npasv_max_port=60030' > /etc/vsftpd.conf"
sudo systemctl enable vsftpd
sudo systemctl restart vsftpd
```

## 🎮 rdp 구축

```sh
sudo apt install xrdp -y
sudo adduser xrdp ssl-cert
sudo sh -c "sed -i '1 i\export XDG_CURRENT_DESKTOP=ubuntu:GNOME' /etc/xrdp/startwm.sh"
sudo sh -c "sed -i '1 i\export GNOME_SHELL_SESSION_MODE=ubuntu' /etc/xrdp/startwm.sh"
sudo sh -c "echo 'unset DBUS_SESSION_BUS_ADDRESS' >> /etc/xrdp/startwm.sh"
sudo sh -c "echo 'unset XDG_RUNTIME_DIR' >> /etc/xrdp/startwm.sh"
sudo service xrdp restart
```

## 💬 irc 구축

```sh
sudo apt-get install perl g++ make wget -y
wget https://github.com/inspircd/inspircd/archive/refs/tags/v3.14.0.tar.gz
tar xvf ./v3.14.0.tar.gz
cd inspircd-3.14.0
perl ./configure
make
make -j5 install
nano run/conf/inspircd.conf
```

다음과 같이 입력한다.

```conf
<config format="xml">
<define name="bindip" value="1.2.2.3">
<define name="localips" value="&bindip;/24">

####### SERVER CONFIGURATION #######

<server
        name="<호스트 네임>"
        description="<서버 설명>"
        id="<서버 코드 (첫번째는 숫자, 나머지 두 글자는 숫자 또는 영어 대문자)>"
        network="<호스트 네임>">


####### ADMIN INFO #######

<admin
       name="<관리자 이름>"
       nick="<관리자 닉네임>"
       email="<관리자 이메일>">

####### PORT CONFIGURATION #######

<bind
      address="<서버 IP>"
      port="6697"
      type="clients">
```

```sh
./inspircd start
```

## 👑 code-server

```sh
curl -fsSL https://code-server.dev/install.sh | sh
sudo systemctl enable --now code-server@$USER
echo "bind-addr: 0.0.0.0:<포트>" > ~/.config/code-server/config.yaml
echo "auth: password" >> ~/.config/code-server/config.yaml
echo "password: <비밀번호>" >> ~/.config/code-server/config.yaml
echo "cert: false" >> ~/.config/code-server/config.yaml
sudo systemctl restart --now code-server@$USER
```

## 🤩 exa (예쁜 ls)

```sh
sudo apt install exa -y
echo "alias ls='exa'" >> ~/.zshrc
source ~/.zshrc
```
