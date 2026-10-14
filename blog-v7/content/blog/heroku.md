---
title: "Setup Serverless Infrastructure Using Heroku"
date: 2022-05-29
draft: false
category: 'Server'
---

## Serverless란?

서버리스. 서버가 없는 것을 뜻한다. 개발자가 서버를 관리할 필요 없이 백엔드가 존재하는 node.js, python 등의 코드를 돌릴 수 있게 하는 것을 의미한다. 그래서 엄밀히 말하자면 서버리스라고 해서 서버가 정말 존재하지 않는 것은 아니고 클라우드 제공 업체에 있는 것이다. 이런 서버리스 구조를 PaaS (Platform as a Service) 라고 하는데, 만약 nodejs 웹어플리케이션을 배포한다고 가정했을 때 aws의 ec2, google의 gcp, oracle의 oracle cloud와 같은 IaaS (Infrastructure as a Service) 같은 경우에는 리눅스 VPS에서 직접 npm을 설치하고, git 세팅도 하는 등의 기본적인 세팅까지 해야 한다. 반면 PaaS는 코드를 저장소에 push 하기만 하면 자동으로 빌드되어 수 초 안으로 웹어플리케이션을 배포할 수 있게 된다. 이것이 가능한 이유는 PaaS에는 운영체제, 미들웨어, 런타임이 미리 구축된 상태로 제공되기 때문이다. (참고 [1](https://www.redhat.com/ko/topics/cloud-computing/what-is-paas), [2](https://library.gabia.com/contents/infrahosting/9105/)) 또한 IaaS와 다르게 코드를 사용할 때만, 사용자가 웹어플리케이션에 접속할 때만 과금이 되기 때문에 가벼운 사이드 프로젝트를 할 때는 IaaS보다 경제적이라고 할 수 있다.

## Heroku

[Heroku](https://www.heroku.com/home)는 Node.js, Ruby, Java, PHP, Python, Go, Scala, Clojure 언어로 된 코드를 쉽게 배포할 수 있는 서비스다. (그 외에 리눅스에서 작동 가능한 언어라면 모두 배포할 수 있다고 한다.)

![a](img/heroku/1.png)

![a](img/heroku/2.png)

이름, 이메일, 직업(?), 국가, 주 사용 언어 선택 후 가입을 한다. 비번 설정, 이메일 인증을 마치면 개인 dashboard에 들어가게 된다.

![a](img/heroku/3.png)

create new app을 누르고 원하는 웹어플리케이션의 이름을 입력한다. 서버 지역은 미국으로 한다.

### Heroku CLI 설치

heroku에 앱을 배포하는 방법은 heroku cli 프로그램을 이용하는 방법 또는 github 원격 저장소와 연동하는 방법이 있다. 이 글에서는 heroku cli 프로그램을 이용하겠다.

```bash
brew tap heroku/brew && brew install heroku
heroku login
```

### node.js 웹앱 배포

프로젝트 폴더를 하나 만들고 express로 간단한 웹서버를 여는 코드를 작성한다.

```bash
mkdir heroku-test
cd heroku-test
npm init
npm install express
nano index.js
```

```js
const express = require('express');
const app = express();
const port = process.env.PORT || 3000;

app.get('/', (req, res) => {
    res.send("<h1>Hello, Heroku!</h1>");
});

app.listen(port, () => {
    console.log("server start!");
});
```

package.json 파일에 node 프로그램을 실행할 때 사용할 start 스크립트를 선언해야 한다.

```json
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
},
```

이렇게 쓰여져 있는 것을 아래처럼 바꾼다.

```json
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "node index.js"
},
```

이제 heroku 저장소와 연결하고 배포한다.

```bash
git init
heroku git:remote -a devleo-test
git add .
git commit -am "first commit"
git push heroku master
```

`https://devleo-test.herokuapp.com/` 에 배포가 완료되었다.
