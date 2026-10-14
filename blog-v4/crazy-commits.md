# 하루에 100만 커밋 하는 법


1. github 부계정을 만든다.
2. repo 를 생성하고 clone 한다.
3. 파일 변경 사항과 커밋 메세지가 없어도 커밋이 되는 명령이 있다.
4. 쉘 스크립트를 사용한다.
5. 실행한다. 경이로운 광경을 경험할 수 있을 것이다.

```bash
#!/bin/bash
cd 1
for ((i=0;;i++))
do
        git commit --allow-empty --allow-empty-message -am ""
done
```

