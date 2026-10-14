# Qemu in Server


## ssh를 통해 gui 띄우기

```bash
vi /etc/ssh/sshd_config
```

`# x11Forward no` 를 `x11Forward yes` 로 수정

```bash
sudo service sshd restart
```

저장 후 sshd 서비스를 재시작한다.  
서버 접속 시 ssh 접속 명령어에 `-X` 옵션을 붙이면 된다.

```bash
ssh -X username@host
```

## 패키지 설치

```bash
sudo apt install qemu nasm make
sudo apt-get install qemu-kvm qemu virt-manager virt-viewer libvirt-bin
```
