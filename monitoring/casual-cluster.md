
## 8.4. 인과 관계 클러스터 모니터링

```
이 섹션에서는 Neo4j 인과관계 클러스터를 모니터링하는 방법을 추가로 알아봅니다.
```

인과 관계 Neo4j 클러스터는 이전 섹션에서 다룬 특정 메트릭스 외에도 운영자가 모니터링하려는 인프라 및 전체 클러스터 상태를 관찰하는 성능을 제공합니다. 절차를 사용하여 클러스터 현재 상태를 확인 및 검사하고  토플러지를 이해할 수 있습니다. 추가적으로, HTTP 엔드포인트를 사용하여 상태를 확인할 수 있습니다. 


> 이 섹션에서 다루는 내용은 [인과 관계 클러스터 모드](../clustering/causal-cluster.md)에서만 사용할 수 있습니다. 


### 8.4.1. 인과 관계 클러스터 모니터링 절차

```
이 섹션에서는 인과 관계 클러스터 Neo4j 모니터링 절차에 대해 다룹니다.  
```

이 섹션에서는 다음에 대해 알아봅니다.:
+ 클러스터 멤버 역할 확인
+ 클러스터 내 인스턴스 개요
+ 라우팅 권장사항 가져오기

#### 8.4.1.1. 클러스터 멤버 역할 확인

```dbms.cluster.role()``` 프로시저를 호출하여 인과 관계 클러스터 내 모든 인스턴스 역할을 리턴할 수 있습니다. 

**문법**:

```CALL dbms.cluster.role()```

**반환 값:**

| 이름       | 종류   | 설명                                                         |
| ---------- | ------ | ------------------------------------------------------------ |
| ```role``` | String | 이것은 ```LEADER```,```FOLLOWER```,또는 ```READ_REPLICA```가 될 수 있는 현재 인스턴스의 역할 입니다. |


**고려사항 :**

+ 이 절차는 그 자체로도 유용하지만 더 강력한 모니터링 절차의 기반이 되기도합니다. 
 
**예 8.9. 이 인스턴스 역할 확인**
 
아래 예에서 보이는 **'FOLLOWER'**은 현재 인스턴스 역할을 찾을 때 쓰입니다. 

```
CALL dbms.cluster.role()
```


| 역할   |
| ------ |
| 팔로워 |


### 8.4.1.2. 클러스터 내 인스턴스 개요 

```dbms.cluster.overview()```절차는 모든 클러스터 인스턴스의 세부 정보를 리턴하여 클러스터 토플러지 개요를 제공합니다. 

**문법:**

```CALL dbms.cluster.overview()```

**반환 값:**

| 이름            | 타입         | 설명                                                         |
| --------------- | ------------ | ------------------------------------------------------------ |
| ```id```        | String       | 이것은 인스턴스의 아이디 입니다.                             |
| ```addresses``` | List<String> | 이것은 인스턴스의 모든 주소 목록입니다.                      |
| ```role```      | String       | 이것은 ```LEADER```,```FOLLOWER```또는 ```READ_REPLICA```가 될 수 있는 인스턴스 역할 입니다. |


**고려사항:**

+ 이 절차는 핵심 인스턴스만이 클러스터의 전체 개요를 제공하기 때문에 핵심 인스턴스에서만 호출할 수 있습니다. 

**예 8.10. 전체적인 클러스터 개요**

이 예는 클러스터 토플러지 탐색 방법을 나타냅니다.

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


### 8.4.1.3. 라우팅 권장사항 가져오기

어플리케이션 관점에서볼 때 클러스터에서 멤버의 역할에 대해 알면 흥미롭지 않습니다. 대신 어플케이션은 어떤 인스턴스가 원하는 서비스를 제공할 수 있는지 알아야합니다. ```dbms.cluster.routing.getServers()``` 절차에서 이와 관련된 정보를 확인할 수 있습니다.  

**문법:**

```CALL dbms.cluster.routing.getServers()```

**예 8.11. 라우팅 권장사항 가져오기**

아래 예시에서는 클러스터내 인스턴스가 제공하는 서비스를 발견하는 방법을 보여줍니다.

```CALL dbms.cluster.routing.getServers()```

이 절차에서는 특정 서비스, ```READ```,```WRITE```와 ```ROUTE```,그리고 이 서비스를 제공하는 인스턴스의 주소 사에에서 지도를 리턴합니다. 또한 정보의 TTL (Time To Live)을 반환합니다.

결과는 주로 사람이 소비하는 것이 아닙니다. 이것을 확대하면 결과는 아래와 같이 확인이 가능합니다. 

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

## 8.4.2. 상태 정보 앤드포인트

### 8.4.2.1. 소개

인과관계 클러스터는 클러스터 상태를 모니터링하는데 쓰이는 HTTP 앤드포인트를 나타냅니다. 이 섹션에서는 앤드포인트와 그 구성 요소에 대해 알아봅니다. 


### 8.4.2.2.앤드포인트

[핵심서버](../clustering/causal-cluster/architecture.md)에는 상태와 관련하여 3가지 앤드포인트가 있습니다. 그것들은:

+ ```/db/manage/server/core/writable```
+ ```/db/manage/server/core/read-only```
+ ```/db/manage/server/core/available```


```/writable/``` 앤드포인트는 특정 인스턴스에 쓰기 트래픽을 지시할 때 사용합니다. 
```/read-only/``` 앤드포인트는 특정 인스턴스에 읽기 트래픽을 지시할 때 사용합니다. 
```/available/``` 앤드 포인트는 임의 요청 타입을 지정하는 일반적인 경우에 트랜잭션을 처리하도록 사용합니다. 

[읽기 복제본](../clustering/causal-cluster/architecture.md)은 한 개의 앤트포인트로 옵니다. 이것은:

+ ```/db/manage/server/read-replica/available``` 
 
```/ available /``` 앤드포인트는 인스턴스(읽기 트랜잭션을 처리하는 데 사용 가능한)에 임의 요청 타입을 정하는 일반적인 케이스입니다. 

앤드포인트를 사용하려면 HTTP ```GET```을 실행하고 그 후에 아래와 같이 리턴 됩니다. 


**테이블 8.14. 핵심 HTTP 앤드포인트 응답**


|               앤드포인트                | 인스턴스 상태 | 반환  코드             | 본문        |
| :-------------------------------------: | ------------- | ---------------------- | ----------- |
|  ```db/manage/server/core/writable```   | 리더          | ```200 OK```           | ```true```  |
|  ```db/manage/server/core/writable```   | 팔로워        | ```404 찾을 수 없음``` | ```false``` |
| ``` /db/manage/server/core/read-only``` | 리더          | ```404 찾을 수 없음``` | ```false``` |
| ``` /db/manage/server/core/read-only``` | 팔로워        | ```200 OK```           | ```true```  |
| ``` /db/manage/server/core/available``` | 리더          | ```200 OK```           | ```true```  |
| ``` /db/manage/server/core/available``` | 팔로워        | ```200 OK```           | ```true```  |


**테이블 8.15. 복제본 HTTP 앤드포인트 응답 읽기**

| 앤드포인트                                      | 리턴 코드     | 본문        |
| ----------------------------------------------- | ------------- | ----------- |
| ``` /db/manage/server/read-replica/available``` | ``` 200 OK``` | ``` true``` |


### 8.4.2.3.예시

명령 줄(Command line)에서 끝 점을 사용하려면 ```curl```을 쓰면 됩니다. 변수가 없는 경우, ```curl```은 제공된 URI에서 HTTP의 ```GET```을 수행하고 본문 내용이 있는 경우 그 내용을 출력할 것 입니다. 또한, 응답 코드를 받고 싶다면 ```-v``` 플래그를 상세 출력문에 추가하면 됩니다. 아래는 관련 예시 입니다.:

+ 현재 코어 서버에서 ```writable``` 앤드포인트를 요청한 것이 상세하게 출력되어 있습니다. 

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

Neo4j서버 기초 보안을 사용할 수 있을 때는 인과 관계 클러스터 상태 앤드포인트에도 인증 자격을 입증해야합니다. 일부 로드 발란스와 프록시 서버의 경우 요청 응답은 옵션 사항이 아닙니다. 이럴 때는 neo4j.conf 설정 파일 내에 ```dbms.security.causal_clustering_status_auth_enabled=false```을 이용해서 CC상태 앤드포인트 인증 비활성화를 고려해야 합니다. 

