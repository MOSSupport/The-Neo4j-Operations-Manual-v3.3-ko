##8.4. 인과 관계 클러스터 모니터링

```
이 섹션에서는 Neo4j 인과관계 클러스터를 모니터링하는 추가적인 방법에 대해 다룹니다.
```




> 이 섹션에서 다루는 것은 [인과 관계 클러스터 모드]("https://neo4j.com/docs/operations-manual/current/clustering/causal-clustering/")에서만 사용할 수 있습니다. 

###8.4.1. 인과 관계 클러스터 절차

```
이 섹션에서는 인과 관계 클러스터 Neo4j 
```



**예 8.9. 이 인스턴스의 역할 확인**
 
아래 예인 'FOLLOWER'은 현재 인스턴스의 역할을 찾을 때 쓰입니다. 

```
CALL dbms.cluster.role()
```


| 역할   |
| ------ |
| 팔로워 |


###8.4.1.2. 클러스터의 인스턴스 


*문법:*
```CALL dbms.cluster.overview()```

*반환 값:*

| 이름            | 타입         | 설명                                                         |
| --------------- | ------------ | ------------------------------------------------------------ |
| ```id```        | String       | 이것은 인스턴스의 아이디 입니다.                             |
| ```addresses``` | List<String> | 이것은 인스턴스의 모든 주소 목록입니다.                      |
| ```role```      | String       | 이것은 ```LEADER```,```FOLLOWER```또는 ```READ_REPLICA```가될 수 있는 인스턴스의 역할 입니다. |


*고려사항:*



**예 8.10. 전체적인 클러스터 개요 


```
CALL dbms.cluster.overview()
```


| id                                   | 주소                                                       | 역할         |
| ------------------------------------ | ---------------------------------------------------------- | ------------ |
| 08eb9305-53b9-4394-9237-0f0d63bb05d5 | [bolt://neo20:7687, http://neo20:7474, https://neo20:7473] | 리더         |
| cb0c729d-233c-452f-8f06-f2553e08f149 | [bolt://neo21:7687, http://neo21:7474, https://neo21:7473] | 팔로워       |
| ded9eed2-dd3a-4574-bc08-6a569f91ec5c | [bolt://neo22:7687, http://neo22:7474, https://neo22:7473] | 팔로워       |
| 00000000-0000-0000-0000-000000000000 | [bolt://neo34:7687, http://neo34:7474, https://neo34:7473] | 복제본_ 읽기 |
| 00000000-0000-0000-0000-000000000000 | [bolt://neo28:7687, http://neo28:7474, https://neo28:7473] | 복제본_ 읽기 |
| 00000000-0000-0000-0000-000000000000 | [bolt://neo31:7687, http://neo31:7474, https://neo31:7473] | 복제본_ 읽기 |


## 8.4.1.4. 라우팅 


**예 8.11. 라우팅 권장사항 가져오기

```
CALL dbms.cluster.routing.getServers()
```


```
ttl: 300,
server: [
{
    addresses: [neo20:7687],
    role: WRITE
}, {
    addresses: [neo21:7687, neo22:7687, neo34:7687, neo28:7687, neo31:7687],
    role: READ
}, {
    addresses: [neo20:7687, neo21:7687, neo22:7687],
    role: ROUTE
}
]
```


##8.4.2. 상태 정보의 단점

###8.4.2.1. 소개


###8.4.2.2.단점

+ ```/db/manage/server/core/writable```
+ ```/db/manage/server/core/read-only```
+ ```/db/manage/server/core/available```


```/writable/``` 앤드포인트는 특정 인스턴스에 쓰기 트래픽을 지시할 때 사용합니다. 
```/read-only/``` 앤드포인트는 특정 인스턴스에 읽기 트래픽을 지시할 때 사용합니다. 
```/available/``` 앤드포인트는 일반적인 임의 요청 타입 읽기 트랜잭션을 가공할 대 인스턴스로 디렉팅으로 사용합니다. 



엔드 포인트는 읽기 트랜잭션 처리에 사용 가능한 인스턴스에 임의의 요청 유형을 지정하는 일반적인 경우에 존재합니다.


[읽기 복제본]("https://neo4j.com/docs/operations-manual/current/clustering/causal-clustering/introduction/#causal-clustering-read-replicas")은 한 개 앤트포인트로 옯니다. 이것은:


+ ```/db/manage/server/read-replica/available```

```/available/``` 

앤드포인트는 임의 요청 타입을 인스턴스로 일반적인 케이스입니다. 


읽기 트랜잭션을 처리합니다. 


앤드포인트를 사용하려면, HTTP ```GET```을 실행하면 됩니다. 그러면, 다음이 리턴 됩니다. 



**테이블 8.14. 핵심 HTTP 앤드포인트 응답**


|               앤드포인트                | 인스턴스 상태 | 반환  코드             | 본문        |
| :-------------------------------------: | ------------- | ---------------------- | ----------- |
|  ```db/manage/server/core/writable```   | 리더          | ```200 OK```           | ```true```  |
|  ```db/manage/server/core/writable```   | 팔로워        | ```404 찾을 수 없음``` | ```false``` |
| ``` /db/manage/server/core/read-only``` | 리더          | ```404 찾을 수 없음``` | ```false``` |
| ``` /db/manage/server/core/read-only``` | 팔로워        | ```200 OK```           | ```true```  |
| ``` /db/manage/server/core/available``` | 리더          | ```200 OK```           | ```true```  |
| ``` /db/manage/server/core/available``` | 팔로워        | ```200 OK```           | ```true```  |


**테이블 8.15. HTTP 앤드포인트 응답 복제본 읽기**

| 앤드포인트                                      | 리턴 코드     | 본문        |
| ----------------------------------------------- | ------------- | ----------- |
| ``` /db/manage/server/read-replica/available``` | ``` 200 OK``` | ``` true``` |


### 8.4.2.3. 예시

명령 줄(Command line)에서, 끝 점을 사용하는 방법은 ```curl```을 쓰는 것 입니다. 변수가 없는 경우, ```curl```은 제공된 URI에서 HTTP의 ```GET```을 수행하고 본문 내용이 있는 경우 그 내용을 출력할 것 입니다. 또한, 응답 코드를 받고 싶다면, ```-v``` 플래그를 자세한 출력에 추가하면 됩니다. 

아래는 관련된 예 입니다.:

+ ```writable``` 앤드포인트를 핵심 서버에서 요청한 것이 현재 장황화게 출력되어 있습니다. 



```
#> curl -v localhost:7474/db/manage/server/core/writable
* About to connect() to localhost port 7474 (#0)
*   Trying ::1...
* connected
* Connected to localhost (::1) port 7474 (#0)
> GET /db/manage/server/core/writable HTTP/1.1
> User-Agent: curl/7.24.0 (x86_64-apple-darwin12.0) libcurl/7.24.0 OpenSSL/0.9.8r zlib/1.2.5
> Host: localhost:7474
> Accept: */*
>
< HTTP/1.1 200 OK
< Content-Type: text/plain
< Access-Control-Allow-Origin: *
< Transfer-Encoding: chunked
< Server: Jetty(6.1.25)
<
* Connection #0 to host localhost left intact
true* Closing connection #0
```

 
만약 Neo4j서버 기초 보안이 활성화되어 있다면, 인과 관계 클러스터 상태 끝점에도 인증 자격 입증이 필요합니다. 일부 로드 발란스와 프록시 서버의 경우 요청에 응답하는 것은 옵션이 아닙니다. 이런 상황에서, neo4j.conf 설정 파일 내에 ```dbms.security.causal_clustering_status_auth_enabled=false```을 이용해서 CC상태 앤드포인트 인증 비활성화하는 것을 고려해야 합니다. 

