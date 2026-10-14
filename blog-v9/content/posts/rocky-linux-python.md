---
title: "Rocky Linux 9에서 Python 3.14 설치"
date: 2025-12-25T12:11:00+09:00
draft: false
cover:
  image: img/rocky-linux-python/1.jpg
  caption: ""
categories: ["Coding"]
tags: ["server", "linux", "python", "rocky"]
ShowToc: true
TocOpen: true
---

```sh
sudo dnf update -y
sudo dnf upgrade -y
sudo dnf groupinstall "Development Tools" -y
sudo dnf install tar curl gcc openssl-devel bzip2-devel libffi-devel zlib-devel wget make findutils ncurses-devel xz-devel sqlite-devel readline-devel openssl-devel libuuid-devel -y
cd ~
wget https://www.python.org/ftp/python/3.14.3/Python-3.14.3.tar.xz
tar -xf Python-3.14.3.tar.xz
cd ~/Python-3.14.3
./configure --prefix=/usr/local --enable-shared LDFLAGS="-Wl,-rpath /usr/local/lib"
make -j $(nproc)
sudo make altinstall
python3.14 --version
```
