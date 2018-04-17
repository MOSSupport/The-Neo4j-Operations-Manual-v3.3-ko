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

쓰기를 수행하려는 드라이버가 연결될 위치를 설정하십시오.

```
frontend neo4j-write
    bind *:7680
    default_backend current-master
```
