---
layout: post
title: "Debian에서 유저 생성"
data: 2019-07-30
categories: etc
comments: true
---

```$ sudo useradd <username> -m -s /bin/bash```

|-m|home 디렉토리 생성|
|-s|기본 쉘 설정 (꼭 /bin/bash로 설정할 것! 이것 때문에 포스팅했다..)|

***

이후 패스워드 설정
```$ sudo passwd <username>```
