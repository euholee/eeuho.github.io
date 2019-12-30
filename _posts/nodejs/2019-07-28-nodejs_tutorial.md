---
layout: post
title: "NodeJS tutorial"
data: 2019-07-28
categories: nodejs
comments: true
---

phaser라는 웹게임 개발용 웹 프레임워크를 사용해보기 위해 간단한 웹 서버가 필요했다.

어떤 웹 서버를 사용할 지 고민하다가 가장 빠르고 간단하게 써볼 수 있을 것 같아 nodejs를 이용했다.

***

### 1. nodejs 설치

우분투 유저이므로 설치는 아주아주 간단했다.

```$ sudo apt-get install nodejs```

*** 

### 2. 웹 서버를 위한 server.js 작성

간단한 nodejs 예시는 인터넷에 많이 돌고 있다.

나는 아래 예시를 이용했다.

```
var http = require('http');	// 'http' npm 모듈 로드

http.createServer(function (req, res){
  res.writeHead(200, {'Content-Type': 'text/plain'});	// response.header 설정
  response.write('Hello nodejs');	// response.body 설정
  res.end();	// response return
}).listen(8080);
```

references: https://www.w3schools.com/nodejs/

위 예시는 localhost:8080에 request가 오면 'Hello nodejs' 만을 return한다.

만약 어떤 html 파일을 return 한다면 아래 예시를 입력하면 된다.

```
var http = require('http');
var fs = require('fs');
http.createServer(function (req, res) {
  fs.readFile('samplefile.html', function(err, data) {
    res.writeHead(200, {'Content-Type': 'text/html'});
    res.write(data);
    res.end();
  });
}).listen(8080);
```

구글링하다가 알게된 것이지만 사실 nodejs는 apache와 같은 웹서버가 아니다. nodejs는 런타임으로 자바스크립트를 실행시켜주는 라이브러리일 뿐이고, 위 예시의 경우는 npm의 'http'를 이용하여 웹 서버를 구현한 것이다.

***

### 3. 웹 서버 작동

작성한 server.js 파일을 nodejs를 이용하여 실행시키면 끝.

```$ node server.js```

 

이제 웹브라우저에서 http://localhost:8080으로 접속하면 'Hello nodejs' 메시지를 볼 수 있을 것이다.
