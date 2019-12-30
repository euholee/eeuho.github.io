---
layout: post
title: "password 없이 ssh 접속 설정"
data: 2019-03-25 11:54
categories: etc
comments: true
---

패스워드 없이 ssh로 특정 호스트에 접근하도록 설정하는 방법은 굉장히 간단하다.

자신의 public key를 호스트에 등록만 하면 된다.

***

## 1. ssh key 생성

아직 ssh key가 없다면

```$ ssh-keygen```

위 명령어를 통해 키를 생성한다.

생성 시, 

```
Generating public/private rsa key pair.
Enter file in which to save the key (/home/user/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/user/.ssh/id_rsa.
Your public key has been saved in /home/user/.ssh/id_rsa.pub.
The key fingerprint is:
```

대략 위와 같은 문구가 하나씩 나오는데 ssh key들을 저장할 디렉토리를 정해주는 것이므로 그냥 엔터를 눌러 기본 디렉토리(~/.ssh)를 사용하도록 하기로 한다.

대부분 모든 개발자들이 기본 디렉토리를 사용할 것이다.

***

## 2. public key 복사

```$ ssh-copy-id <user>@<host name>```

예를 들어 root 이용자, google.com이라는 호스트라면 ssh-copy-id root@google.com 이렇게 입력한다.

입력한 후 해당 유저의 비밀번호를 입력하면 된다.

***

참조: https://www.linuxtrainingacademy.com/ssh-login-without-password/
