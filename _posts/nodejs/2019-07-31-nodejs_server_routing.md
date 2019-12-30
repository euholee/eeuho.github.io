---
layout: post
title: "NodeJS express를 이용한 간단한 서버 라우팅 예제"
data: 2019-07-31
categories: nodejs
comments: true
---

nodejs 모듈을 이용하여 서버를 구축하고 간단한 라우팅을 해보겠다.


우선 프로젝트 폴더를 하나 만들고 그 안에서 아래 명령어를 실행한다.

예시에서는 프로젝트 폴더를 'example'로 사용하겠다.

```$ npm install express --save```
 

위 명령어로 express 모듈을 설치하였고, package.json 파일을 업데이트 하였다.

이제 server.js 파일을 생성하여 아래 코드를 입력한다.

```
// example/server.js
var express = require('express');
var app = express();
var server = app.listen(3000, function(){
    console.log('hello world!');
})
```

저장한 후 아래 명령어를 실행하면, 

```$ node server.js```
 

express를 이용한 서버가 동작한다.

이것으로 express를 이용한 웹서버 구축은 끝났다.

 

이제 라우팅을 위한 라우터 폴더를 만들고 그 안에 main.js 파일을 작성한다.

예시에서는 라우터 폴더를 'router'로 사용하겠다.

```
// example/router/main.js
module.exports = function(app) {
     app.get('/',function(req,res){
        res.send('hello world!');
     });
};
```
 

이제 만든 라우터를 server.js 파일에 등록만 하면 된다.

```
// example/server.js
var express = require('express');
var app = express();
var router = require('./router/main')(app);
var server = app.listen(3000, function(){
    console.log('hello world!');
})
```

이제 웹 브라우저로 http://localhost:3000에 접속하면 'hello world!' 메시지를 볼 수 있다.

 

url을 하나 더 추가해보겠다.

```
// example/router/main.js
module.exports = function(app) {
     app.get('/',function(req,res){
        res.send('hello world!');
     });
     app.get('/another/',function(req,res){
        res.send('hello another world!');
     });
};
```

이제 웹 브라우저로 http://localhost:3000/another에 접속하면 'hello another world!' 메시지를 볼 수 있다.
