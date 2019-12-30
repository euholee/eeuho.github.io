---
layout: post
title: 'curl을 이용한 http request'
data: 2019-07-10 12:00
categories: etc
comments: true
---

그전에는 advanced rest api 툴을 이용하여 rest api를 테스트했지만, 그러던 중 curl 명령어를 알게 되었다.

```curl -v -X <Request-Method><Request-URL> -H <Request-Header> -F <Form-Data>```

ex)

```curl -v -X GET http://api.test.com/api/1/check/ -H "token: ashdfo893hfs9y9f99fs8yfd0f" -F "username=sample" -F "password=p@$$\0rd"```

헤더와 폼 파라미터 넣는 양식이 다르므로 주의!

