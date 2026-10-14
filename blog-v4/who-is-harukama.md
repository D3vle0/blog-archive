# Github bug - Who Is Harukama


## 서론

제목 그대로 Github 에서 일어난 버그에 관련된 글이다.  
기존에 사용하던 vultr 서버를 다른 사양으로 변경 후 서버에서 작업을 하던 도중 일어난 일이다.

## who is harukama?

terminal 에서 `git config --global --edit` 을 입력하면 `~/.gitconfig` 파일이 텍스트 편집기로 열리는데,  
 `git config` 설정을 하지 않은 상태라면 아래와 같은 값이 나타날 것이다.

```conf
# This is Git's per-user configuration file.
[user]
# Please adapt and uncomment the following lines:
#       name = root
#       email = root@vultr.guest
```

이렇게 sample 정보들이 comment 처리가 되어있는데 이 상태로 `git commit` 및 `git push` 를 하면?

![](https://cdn.discordapp.com/attachments/794556029555703832/897990280060473405/unknown.png)

누구세요???

보통 `git config` 설정을 하지 않으면 아래와 같이 ubuntu, ghost, 또는 링크가 걸려있지 않은 자신의 닉네임으로 커밋이 되고, 해당 계정은 실존하지 않는다.

![](https://cdn.discordapp.com/attachments/794556029555703832/897991365269528596/unknown.png)
![](https://cdn.discordapp.com/attachments/794556029555703832/897991785819828244/unknown.png)
![](https://cdn.discordapp.com/attachments/794556029555703832/897991820171157534/unknown.png)
![](https://cdn.discordapp.com/attachments/794556029555703832/897991951683559444/unknown.png)

그런데 이런 경우는 처음이다.  
harukama 라는 사람은 실존한다.

https://github.com/HarukaMa  

github community를 보니 나와 같은 문제를 겪은 사람이 있는데, 답변은 아래와 같다.

![](https://cdn.discordapp.com/attachments/794556029555703832/897993280795602994/unknown.png)

vultr 서버만의 문제인지는 나중에 새 서버를 deploy 하면서 알아봐야겠다.
