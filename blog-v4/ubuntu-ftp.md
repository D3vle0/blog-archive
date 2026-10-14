# Ubuntu Ftp


```bash
apt update -y
apt-get install vsftpd
```

```bash
netstat -tlnp
```

로 21 포트가 정상적으로 작동하는지 확인

```bash
vi /etc/ftpusers
```

root 주석 제거

```bash
vi /etc/vsftpd.conf
```

`write_enable=YES` 로 변경

```bash
systemctl restart vsftpd
```




