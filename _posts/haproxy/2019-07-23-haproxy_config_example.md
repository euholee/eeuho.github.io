---
layout: post
title: "HAProxy에 대한 간단한 설명 및 config 파일 예시"
data: 2019-07-23
categories: haproxy
comments: true
---

HAProxy란 L4/L7 스위치를 소프트웨어적으로 구현한 오픈소스 기반 소프트웨어로 간단히 설명하면 서버에 오는 http request를 여러 서버로 분산시키기 위해 사용하는 소프트웨어이다.

어렵게 생각했으나 HAProxy는 굉장히 간단했다. 설치 및 실행이야 검색하면 1분 안으로 가능할 것이고 config 파일을 작성하여 서비스만 실행시키면 끝이다. (기타 방화벽 설정, 포트포워딩 등은 예외.)

haproxy 설정 파일의 위치는 /etc/haproxy/haproxy.cfg 이고, 기본 양식은 아래와 같다.
 
```
global

    # global settings here

defaults

    # defaults here

frontend

    # a frontend that accepts requests from clients

backend

    # servers that fulfill the requests
 ```

global 예시:

```
global

    maxconn 50000
    log /dev/log local0
    user haproxy
    group haproxy
    stats socket /run/haproxy/admin.sock user haproxy group haproxy mode 660 level admin
    nbproc 2
    nbthread 4
    cpu-map auto:1/1-4 0-3
    ssl-default-bind-ciphers ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256
    ssl-default-bind-options ssl-min-ver TLSv1.2 no-tls-tickets
``` 

|maxconn|haproxy가 수용할 최대 커넥션의 갯수.|
|user / group|리눅스 시스템에서 1024 이하의 포트에서는 기본적으로 루트 권한을 요구한다. 이 부분을 공란으로 두면 haproxy는 계속 루트 권한을 요구할 것인데, 루트 권한으로 haproxy를 이용하는 것은 보안상 좋지 않다.|
|stats socket|런타임 API 실행을 위한 소켓. 런타임 API로는 헬스를 체크, 로드 밸런싱 weight 수정 등이 있다.(https://www.haproxy.com/blog/dynamic-configuration-haproxy-runtime-api/)|
|nbproc / nbthread|haproxy에 사용될 프로세스와 스레드의 수.|

defaults 예시:

```
defaults

    timeout connect 10s
    timeout client 30s
    timeout server 30s
    log global
    mode http
    option httplog
    maxconn 3000
    timeout connect 10s
    timeout client 30s
    timeout server 30s
    log global
    mode http
    option httplog
    maxconn 3000
 
```

frontend 예시:

``` 
frontend www.mysite.com

    bind 10.0.0.3:80
    bind 10.0.0.3:443 ssl crt /etc/ssl/certs/mysite.pem
    http-request redirect scheme https unless { ssl_fc }
    use_backend api_servers if { path_beg /api/ }
    default_backend web_servers
    # a frontend that accepts requests from clients
``` 

|bind|해당 IP에 대한 리스너를 설정한다. 정규식도 가능하다.|
|http-request redirect|주로 잘못된 URL을 올바른 URL로 redirection할 때 사용한다. 예시에서는 http request를 모두 https로 redirection한다.|
|use_backend|백엔드 풀 서버 설정. 만약 "if path_beg /api/" 과 같은 acl statement가 오면 조건문이 추가되어 URL에 따라 백엔드 풀을 설정하도록 할 수 있다.|
|default_backend|use_backend에 속하지 않는 http_request에 대한 처리. use_backend와 default_backend 둥 중 어디에도 연결되지 않으면 503:Service Unavailable 오류 응답코드를 리턴한다.|


backend 예시:

```
backend web_servers

    balance roundrobin
    cookie SERVERUSED insert indirect nocache
    option httpchk HEAD /
    default-server check maxconn 20
    server server1 10.0.1.3:80 cookie server1 check inter 5000 rise 2 fall 3
    server server2 10.0.1.4:80 cookie server2 check inter 5000 rise 2 fall 3
```

|balance|로드밸런싱 방법을 설정한다. 대체로 roundrobin / leastconn 두가지 방식이 있다. roundrobin으로 하게 되면 그저 밸런스 리스트에서 번갈아가면서 커넥션을 분산시키고 leastconn으로 하면 active session이 가장 적은 노드로 밸런싱한다.|
|server|이 항목이 HAProxy를 사용하는 목적으로 Load Balancing 리스트에 해당 서버를 추가한다. IP주소 또는 도메인 모두 가능하다. 덧붙여서 예시에서는 헬스체크하여 2번 성공하면 Load Balancing 풀에 서버를 추가하고, 3번 성공하면 서버를 풀에서 제외한다.|

references: https://www.haproxy.com/blog/the-four-essential-sections-of-an-haproxy-configuration/

이후 HAProxy 서비스만 실행시키면 된다.
