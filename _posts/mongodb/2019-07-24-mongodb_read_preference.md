---
layout: post
title: "mongodb read preference 설정"
data: 2019-07-24
categories: mongodb
comments: true
---

기본적으로 몽고디비는 주 데이터베이스(Primary)에서만 쿼리를 수행한다.
하지만 read preference mode를 이용하여, read operation의 경우는 설정에 따라 보조 데이터베이스(replication, Secondary Database)에서도 query를 수행하도록 설정할 수 있다.

설정은 총 5가지가 있다. 

|primary|주 데이터베이스에서만 읽기 권한 수행 (default)|
|primaryPreferred|주 데이터베이스에서 읽기 권한을 수행하되, 읽기가 불가능한 경우 보조 데이터베이스에서 읽기 권한 수행|
|secondary|보조 데이터베이스에서만 읽기 권한 수행|
|secondaryPreferred|보조 데이터베이스에서 읽기 권한을 수행하되, 읽기가 불가능한 경우 주 데이터베이스에서 읽기 권한 수행|
|nearest|network latency가 가장 낮은 데이터베이스에서 읽기 권한 수행|

설정은 매우 간단하다.

ex)
```db.getMongo().setReadPref('primaryPreferred')```
```db.getMongo().setReadPref('primaryPreferred', [ { "dc": "east" } ] )```

단, 여기서 고려해야할 점이 주 데이터베이스에 새로 업데이트 된 내용이 보조 데이터베이스에 아직 반영되어있지 않아 최신이 아닌 데이터를 가져올 수 있다. 따라서 위 설정을 하기 위해서는 write concern을 고려해야한다.

write concern이란 데이터베이스에서 쓰기 쿼리를 수행할 때 replication을 얼마나 만든 후 쿼리 결과를 주는지를 말한다.
write concern이 0이면 쓰기 쿼리를 수행할 때 주 데이터베이스를 업데이트하고 결과를 클라이언트에게 준 뒤, replication을 만든다.
write concern이 n이면 쓰기 쿼리를 수행할 때 주 데이터베이스와 n개의 보조 데이터베이스를 업데이트 한 뒤, 결과를 클라이언트에게 준다.
 
즉, 위 read preference를 primary 이외로 설정하기 위해서는 write concern을 보조 데이터베이스 갯수로 설정해야한다.


https://docs.mongodb.com/v3.2/core/read-preference/
https://docs.mongodb.com/v3.2/reference/write-concern/
