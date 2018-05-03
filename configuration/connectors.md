## 3.6. Neo4j 커넥터 환경 설정 

```
이 섹션에서는 Neo4j 커넥터 환경 설정하는 법을 다룹니다. 
```

Neo4j는 Bolt 이진 프로토콜이나 HTTP/HTTPS를 사용해 클라이언트를 지원합니다. 기본적으로 3 종류의 Neo4j 커넥터가 있습니다. 

+ ```bolt``` 커넥터
+ ```http``` 커넥터
+ ```https``` 커넥터


**표 3.1. 기본 커넥터 및 포트**

 | 커넥터 명                   | 프로토콜 | 기본 포트 번호 |
| --------------------------- | -------- | -------------- |
| ```dbms.connector.bolt```   | Bolt     | ```7687```     |
| ```dbms.connector.http```   | HTTP     | ```7474```     |
| ``` dbms.connector.https``` | HTTPS    | ```7473```     |

HTTPS 커넥터 환경설정할 떄, SSL 인증서 작업 방법에 대한 상세한 내용은 [섹션 7.3. "통합 SSL 프레임워크"](../security/ssl-framework.md)에서 확인 가능합니다. 


### 3.6.1. Neo4j 커넥터 추가 옵션 

커넥터에 대해 일부 추가 옵션이 이용 가능합니다. 이와 관련된 상세한 내용은 아래에서 확인 가능합니다. 


**테이블 3.2. 커넥터를 위한 환경 설정**

| 옵션 명                  | 기본 값                                   | 설명                                                   |
| ------------------------ | ----------------------------------------- | ------------------------------------------------------ |
| ```enabled```            | ```true```                                | 클라이언트 커넥터 활성화/비활성화를 결정합니다.        |
| ```listen_address```     | ``` 127.0.0.1:<connector-default-port>``` | 들어오는 커넥션의 주소                                 |
| ```advertised_address``` | ``` localhost:<connector-default-port>``` | 클라이언트가 연결을 위해 사용하는 주소              |
| ```tls_level```          | ``` OPTIONAL```                           | 커넥터가 암호화 또는 비암호화된 연결을 수용하도록 허락 |

**활성화**
```enabled``` 설정은 고객 커넥터를 활성화하거나 비활성화 합니다. 사비활성화되면, Neo4j는 관련 포트에서 요청되는 연결을 무시합니다. 예를 들어, 다음을 설정해서 HTTPS 연결을 비활성화 합니다.  

```dbms.connector.https.enabled=false```


> HTTP 커넥터를 비활성화하는 것은 불가능합니다. 
클라이언트가 HTTP에 연결하는 것을 막기위해서는 방화벽에서 HTTP 포트를 차단하면 됩니다. 또한, http 커넥터가 루프백 인터페이스인 (127.0.0.1)만 수신할 수 있도록 ```listen_address```을 구성해 원격 클라이언트 연결을 막아야합니다. 


**listen_address**

```listen_address``` 설정은 Neo4j가 연결에 어떻게 응답하는지 살펴봅니다. 이것은 두 가지 요소로 구성됩니다.; IP 주소(e.g. 127.0.0.1 또는 0.0.0.0)와 포트 번호(e.g. 7687)는 ```<ip-address>:<port-number>```형식으로 표현됩니다.


**예 3.2. 볼트 커넥터를 위한 ```listen_address```를 명시합니다.**

네트워크 인터페이스(0.0.0.0)와 포트 7000에 있는 볼트 커넥션을 수신하기 위해서, 볼트 커넥터를 위한 ```listen_address```를 설정하면 됩니다.:

```dbms.connector.bolt.listen_address=0.0.0.0:7000```

**advertised_address**

```advertised_address``` 설정에서 클라이언트가 커넥터를 사용하기 위한 주소를 명시합니다. 이것은 각 서버가 클러스터의 다른 주소를 바르게 알릴 수 있도록하기 때문에 인과 클러스터에서 유용합니다. 광고된 주소는 두 개로 구성되어 있습니다.; (정규화된 도메인 이름, 호스트 이름, 또는 IP주소)와 포트 번호(e.g. 7687)는 ```<address>:<port-number>```형식으로 표시됩니다.


프록시를 통해 라우팅이나 포트 매핑이 사용되고 있으면, 커넥터 별로 ```advertised_address```을 지정할 수 있습니다. 예를 들어, Neo4j 서버의 7687 포트가 외부 네트워크 9000 포트에서 매핑되는 경우, 볼트 커넥터에 ```advertised_address```를 명시해야 합니다. 

```dbms.connector.bolt.advertised_address=<server-name>:9000```


**tls_level**

```tls_level``` 설정은 ```bolt```커넥터에서만 가능합니다. 설정에서 암호화된 볼트 커넥터나 비암호화된 클라이언트 연결 어셉트 여부를 정의합니다. ```tls_level```설정이 수용하는 값은 아래 표에 나와있습니다.


**표 3.3. ```tls_level```에서 사용가능한 값**


| 이름           | 설명                                                         |
| -------------- | ------------------------------------------------------------ |
| ```REQUIRED``` | 이 연결에서는 암호화된 고객 연결만 허용합니다. 모든 비암호화된 연결은 거절됩니다. |
| ```OPTIONAL``` | 이 연결에서 암호화또는 비암호화된 클라이언트 연결을 허용합니다. |
| ```DISABLED``` | 이 연결에서는 암호화된 고객 연결만 허용합니다. 모든 암호화된 연결은 거절됩니다. |


### 3.6.2. 주소의 기본 값

```dbms.connectors.default_listen_address```와 ```dbms.connectors.default_advertised_address``` 두 가지 구성 설정은, IP주소와 ```listen_address```,```advertised_address``` 각각을 명시하는데 사용됩니다. 기본 값 구성은 특정 커넥터에 구성하지 않는 한 모든 커넥터에 적용됩니다. 


**표 3.4. 주소 기본 설정**

| 옵션 명                                           | 기본 값          | 설명                                              |
| ------------------------------------------------- | ---------------- | ------------------------------------------------- |
| ``` dbms.connectors.default_listen_address```     | ``` 127.0.0.1``` | 모든 커넥터 ``` listen_address```의 기본 IP 주소  |
| ``` dbms.connectors.default_advertised_address``` | ``` localhost``` | 모든 커넥터 ``` advertised_address```의 기본 주소 |


**default_listen_address**

수신 주소는 IP주소(e.g. 127.0.0.1 or 0.0.0.0)와 포트번호 (e.g. 7687) 두 부분으로 구성됩니다. ```listen_address``` IP 주소 포트가 명시되어 있지않으면, ```default_listen_address```공유된 설정에서 인터페이스는 상속됩니다.


**예 3.3. Bolt 커넥터를 위한 ```listen_address``` 명시화 작업**

Bolt 연결을 위해 모든 네트워크 인터페이스(0.0.0.0)와 포트 7000를 수신하려면, Bolt 연결을 위해 ```listen_address```을 설정해야 합니다. 

```dbms.connector.bolt.listen_address=0.0.0.0:7000```

```default_listen_address```을 사용해서 IP주소를 명시화하고, Bolt 커넥터를 위해  포트 넘버 명시화하는 것과 동일합니다. 

```
dbms.connectors.default_listen_address=0.0.0.0

dbms.connector.bolt.listen_address=:7000
```


### default_advertised_address

광고된 주소는 주소(정규화된 도메인 이름, 호스트 이름, IP주소)와 포트 번호(e.g. 7687) 두 부분으로 구성됩니다. ```advertised_address```의 주소 부분이 명시화되어 있지 않으면, 인터페이스는 ```default_advertised_address```에서 공유한 설정에서 상속받습니다. 


**예 3.4. 볼트 커넥터를 위한 ```advertised_address``` 명시화**

```dbms.connector.bolt.advertised_address=server1:9000```

이것은 ```default_advertised_address``` 설정을 사용해서 주소를 지정하고 다음 Bolt 커넥터 포트 번호를 명시화하는 것과 같습니다. 
 

```
dbms.connectors.default_advertised_address=server1

dbms.connector.bolt.advertised_address=:9000
```
