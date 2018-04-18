### 4.3.5. 로드 밸런싱을 위한 HAProxy `Enterprise Edition`
> 이 장에서는 Neo4j HA 클러스터에서 로드 밸런싱을 위해 HAProxy를 구성하는 방법을 설명합니다.

Neo4j HA 아키텍처에서 클러스터는 일반적으로 로드 밸런서에 의해 전면에 배치됩니다. 이 장에서는 HA 클러스터 전체에서 로드 밸런싱을 수행하도록 HAProxy를 설정하는 방법에 대해 살펴보겠습니다.

이 튜토리얼에서는 HAProxy가 이미 설치된 Linux 환경을 가정합니다. 다운로드 및 설치 지침은 [http://www.haproxy.org/](http://www.haproxy.org/)를 참조하십시오.

#### 4.3.5.1. 볼트 프로토콜 용 HAProxy 구성
전형적인 HA 배포에서, HAProxy는 두 개의 열린 포트, 즉 마스터에 쓰기 작업을 라우팅하기 위한 한 포트와 슬레이브에 대한 로드 밸런싱 읽기 작업을 위한 한 포트로 구성됩니다. 각 응용 프로그램은 두 개의 드라이버 인스턴스를 가지며, 하나는 쓰기를 수행하는 마스터 포트에 연결되고 다른 하나는 읽기를 수행하기 위해 슬레이브 포트에 연결될 것입니다.

먼저 모드와 타임 아웃을 설정합니다. 아래 설정은 서버 또는 클라이언트가 2시간 이상 유휴 상태인 경우 연결을 끊을 것입니다. 장기 실행 쿼리는 시간이 더 오래 걸릴 수 있지만, HAProxy의 TCP 하트 비트 기능을 사용하여 이 작업을 처리 할 수 있습니다.

```
defaults
    mode        tcp

    timeout connect 30s

    timeout client 2h
    timeout server 2h
```

쓰기를 수행하려는 드라이버가 연결할 위치를 설정하십시오:

```
frontend neo4j-write
    bind *:7680
    default_backend current-master
```

이제 우리는 현재 마스터 인스턴스를 가리키는 백엔드를 설정합니다.

```
backend current-master
    option  httpchk HEAD /db/manage/server/ha/master HTTP/1.0

    server db01 10.0.1.10:7687 check port 7474
    server db02 10.0.1.11:7687 check port 7474
    server db03 10.0.1.12:7687 check port 7474
```

위의 예제에서 `httpchk`는 Neo4j에 대한 인증이 비활성화 된 경우 수행하는 방식으로 구성됩니다. 하지만 기본적으로 인증은 사용하도록 설정되어 있으므로 인증 헤더를 전달할 필요가 있습니다. 이것은 `option httpchk HEAD /db/manage/server/ha/master HTTP/1.0\r\nAuthorization:\ Basic\ bmVvNGo6bmVvNGo=`의 라인을 따를 것이며 마지막 부분은 사용자 이름 및 비밀번호를 위해 base64로 인코딩된 값으로 대체되어야 합니다.

읽기를 수행하려는 드라이버가 연결할 위치를 구성하십시오:

```
frontend neo4j-read
    bind *:7681
    default_backend slaves
```

마지막으로 라운드 로빈 방식으로 슬레이브를 가리키는 백엔드를 구성하십시오:

```
backend slaves
    balance roundrobin
    option  httpchk HEAD /db/manage/server/ha/slave HTTP/1.0

    server db01 10.0.1.10:7687 check port 7474
    server db02 10.0.1.11:7687 check port 7474
    server db03 10.0.1.12:7687 check port 7474
```

`slave` 백엔드의 서버는 `current-master` 백엔드와 동일한 방식으로 구성된다는 것을 주목하십시오.

그러면 위의 모든 구성을 하나의 파일에 넣음으로써, Bolt Protocol을 사용하는 응용 프로그램에 대한 로드 밸런싱을 수행 할 수 있는 기본 작동 가능한 HAProxy 구성을 얻습니다.

기본적으로 서버와 드라이버간에 암호화가 사용됩니다. 암호화 기능이 켜지면 위에서 생성된 HAProxy 구성은 HAProxy에 대한 TLS/SSL 관문 레이아웃에서 직접 작동하기 위해 변경할 필요가 없습니다. 그러나 채택된 드라이버 인증 전략에 따라 일부 특수 요구 사항이 서버 인증서에 적용될 수 있습니다.

첫 번째 사용 신뢰(trust-on-first-use) 인증 전략을 사용하는 드라이버의 경우, 각 드라이버는 연결되는 HAProxy 포트를 클러스터에서 수신한 첫 번째 인증서를 가지고 등록합니다. 그러면 모든 이후의 연결에 대해 드라이버는 등록된 것과 동일한 인증서가 있는 서버와만 연결할 것입니다. 따라서 드라이버가 클러스터의 모든 인스턴스와 연결할 수 있게 하려면 이 모드에서는 클러스터의 모든 인스턴스가 동일한 인증서를 공유해야 합니다.

드라이버가 신뢰할 수 있는 인증서 모드로 실행되도록 구성된 경우, 드라이버에 알려진 인증서는 클러스터의 서버에 설치된 모든 인증서의 루트 인증서여야 합니다. 또는 여러 인증서를 신뢰할 수 있는 인증서로 등록할 수 있는 Java 드라이버와 같은 드라이버의 경우, 드라이버는 클러스터에서 사용되는 서버 인증서가 모두 신뢰할 수 있는 인증서로 등록되어 있으면 클러스터에서도 잘 동작합니다.

HAProxy를 다른 암호화 레이아웃과 함께 사용하려면, 해당 웹 사이트의 전체 설명서를 참조하십시오.

#### 4.3.5.2. HTTP API를 위한 HAProxy 구성하기
HAProxy는 다양한 방법으로 구성될 수 있습니다. 전체 문서는 해당 웹 사이트에서 볼 수 있습니다.

이 예에서는 세 개의 HA 서버에 대한 밸런스 요청을 로드하기 위해 HAProxy를 구성합니다. 단순히 다음 구성을 */etc/haproxy/haproxy.cfg*에 기록하세요:

```
global
    daemon
    maxconn 256

defaults
    mode http
    timeout connect 5000ms
    timeout client 50000ms
    timeout server 50000ms

frontend http-in
    bind *:80
    default_backend neo4j

backend neo4j
    option httpchk GET /db/manage/server/ha/available
    server s1 10.0.1.10:7474 maxconn 32
    server s2 10.0.1.11:7474 maxconn 32
    server s3 10.0.1.12:7474 maxconn 32

listen admin
    bind *:8080
    stats enable
```

HAProxy는 이제 다음을 실행하여 시작할 수 있습니다:

```
/usr/sbin/haproxy -f /etc/haproxy/haproxy.cfg
```

http://<ha-proxy-ip>:8080/haproxy?stats에 연결하여 상태 대시보드를 볼 수 있습니다. 이 대시보드는 포트 80에서 실행되도록 이동할 수 있으며 인증을 추가할 수도 있습니다. 자세한 내용은 HAProxy 설명서를 참조하십시오.

#### 4.3.5.3. 읽기 및 쓰기 최적화하기
Neo4j는 HTTP 응답 코드를 사용하여 시스템을 구별하기 위해 HAProxy (또는 해당 로드 밸런서)가 사용할 수 있는 *상태 점검 URL* 카탈로그([4.3.4. "상태 정보의 엔드포인트"](./endpoints-for-status-information.md) 참조)를 제공합니다. 위의 예제에서는, 트랜잭션 처리를 위해 일반적으로 사용 가능한 시스템에 요청을 보내는(they are alive!) /available 엔드포인트를 사용했습니다. 

그러나 그것은 오직 슬레이브에만 또는 마스터에만 지시하는 요청을 가질 수 있습니다. 만일 쓰기 요청과 읽기 전용 요청 사이에서 응용 프로그램을 구별 할 수 있다면, 두 개의 (논리적) 로드 밸런서의 장점을 활용할 수 있습니다: 마스터에게 모든 쓰기를 보내는 하나와 슬레이브에게 모든 읽기 전용 요청을 보내는 하나. HAProxy에서는 다중 백엔드를 추가하여 논리적 로드 밸런서를 구축합니다.
