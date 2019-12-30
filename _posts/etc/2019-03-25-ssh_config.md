---
layout: post
title: "ssh config를 이용한 간편한 ssh 접속"
data: 2019-03-25 11:20
categories: etc
comments: true
---

ex)
```$ ssh root@domain.com```

ssh로 접속할마다 매번 위 명령어를 입력하기에는 번거롭다.

자주 사용하는 ssh 접속은 ~/.ssh/config 파일에 저장하여 호스트 입력을 간소화할 수 있다.

먼저 config 파일을 연다. 파일은 없다면 새로 생성만 하면 된다. 

```$ vi ~/.ssh/config```

아래 포맷으로 추가하고 저장하면 끝.

```
Host <단축된 host 이름>

    User <user 이름>

    HostName <host 이름>
```
root@google.com을 google로 간소화하고 싶다면

ex)
```
Host google

    User root

    HostName google.com
```

위와 같이 하면 된다. 만약 위와 같이 수정했다면,

ex)
```$ ssh google```

이제 위 접속은 이전의 ssh root@google.com 접속과 같다.

추가로, 만약 별도의 key 파일을 통해 접속을 인증하도록 설정되어있다면 config 파일에 아래 항목을 추가한다.
```
    IdentityFile <key 파일 위치>
```

단, 이 때 key 파일의 퍼미션도 400으로 설정되어야 한다.

```$ chmod 400 <key 파일 위치>```
