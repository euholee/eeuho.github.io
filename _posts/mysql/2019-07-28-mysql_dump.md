---
layout: post
title: "Mysql dump 사용법 및 간단한 예시 (db 백업)"
data: 2019-07-28
categories: mysql
comments: true
---

mysql의 백업 파일(dump 파일) 생성 및 로드 순서는 크게 3개로 나눌 수 있다.

1. 백업하려는 db로부터 dump 파일 생성
2. 복구할 host의 mysql에서 dump 파일 로드
3. mysql 복구 실행

### 1. 백업하려는 db로부터 dump 파일 생성 (os command shell에서)

```mysqldump <dbname>  -h <host>  -u <user> -p > <dump_file_name>```
```mysqldump --databases <dbname_1> <dbname_2> <dbname_n>  -h <host>  -u <user> -p > <dump_file_name>```

ex)
```$ mysqldump sampledb -h localhost -u root -p > sample.sql```

***

### 2. 복구할 host의 mysql에서 dump 파일 로드 (os command shell에서)

```mysql -u <user> -p < <dump_filename>```

ex)
```$ mysql -u root -p < sample.sql```

***

### 3. mysql 복구 실행 (mysql 내에서)

```mysql> source <dump_file_name>```

ex)
```mysql> source sample.sql```
