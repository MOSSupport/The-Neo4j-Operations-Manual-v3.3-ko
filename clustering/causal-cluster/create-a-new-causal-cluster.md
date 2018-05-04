### 4.2.3. 새로운 Causal 클러스터 생성 `Enterprise Edition`
> 이 장에서는 새로운 Neo4j Causal 클러스터를 배포하는 방법을 설명합니다.

이 장에서는 세 개의 코어 인스턴스(내결함성 코어 클러스터에 필요한 최소 서버 수)로 구성된 새로운 Causal 클러스터를 설정하는 방법을 배우게 됩니다. 그런 다음 Read Replica 뿐만아니라 코어 서버를 더 추가하는 방법도 배웁니다. 이 기본 패턴으로부터 우리는 모든 크기의 클러스터를 만들기 위해 여기서 배운 것을 확장할 수 있습니다.

다음의 주제가 설명되어 있습니다:
* [코어 전용 클러스터 구성](#4231-코어-전용-클러스터-구성)
* [기존 클러스터에 코어 서버 추가](#4232-기존-클러스터에-코어-서버-추가)
* [기존 클러스터에 Read Replica 추가](#4233-기존-클러스터에-Read-Replica-추가)
* [`neo4j-admin unbind`를 사용하여 클러스터에서 코어 제거](#4234-neo4j-admin-unbind를-사용하여-클러스터에서-코어-제거)
* [팔로워 전용 인스턴스의 바이어스 클러스터 리더십](#4235-팔로워-전용-인스턴스의-바이어스-클러스터-리더십)

여기에서 소개되어지는 클러스터링 아키텍처 및 클러스터 개념에 대한 설명은 [Causal 클러스터링 소개](./architecture.md)를 참조하십시오. 우리는 기존 Neo4j 인스턴스에서 데이터를 가져오는 방법은 설명하지 않을 것입니다; 새로운 Causal 클러스터를 시드하기 위해 기존의 Neo4j 데이터베이스를 사용하는 것에 관한 도움말은 [Causal 클러스터 시드](./seed-a-cluster.md)를 참조하십시오.

로컬 시스템에 Causal 클러스터를 설정하길 원한다면, [Section B.1, "Set up a local Causal Cluster"](https://neo4j.com/docs/operations-manual/3.3/tutorial/local-causal-cluster/)을 참조하십시오.

#### 4.2.3.1. 코어 전용 클러스터 구성
이 장의 지침은 이미 [Neo4j 다운로드 사이트](https://neo4j.com/download/other-releases/#releases)에서 적절한 버전의 Neo4j Enterprise Edition을 다운로드하고 클러스터를 구성할 서버에 그것을 설치했다고 가정합니다.

Neo4j를 세개의 개별 시스템에서 실행할 때, 가장 기본적인 구성은 다섯가지 매개 변수의 변경을 필요로 합니다. 아래 설정은 [*neo4j.conf*](/configuration/file-locations.md)의 "Network connector configuration"이라는 헤더 아래에 있습니다.

[`dbms.connectors.default_listen_address`](https://neo4j.com/docs/operations-manual/3.3/reference/configuration-settings/#config_dbms.connectors.default_advertised_address)
  이 시스템이 들어오는 메시지를 수신하는 데 사용하는 주소 또는 네트워크 인터페이스. 이 행의 주석 처리를 제거하면 이 값이 Neo4j가 모든 네트워크 인터페이스에 바인딩 할 수 있도록 허용해주는 `0.0.0.0`으로 설정됩니다. `dbms.connectors.default_listen_address = 0.0.0.0`의 주석 처리를 제거하십시오.
[`dbms.connectors.default_advertised_address`]
  다른 시스템이 연결하도록 지시된 주소. 일반적인 경우 이것은 이 서버의 공용 IP 주소로 설정해야 합니다. 예를 들어, IP 주소가 33.44.55.66인 경우 이 설정은 다음과 같아야 합니다:
  `dbms.connectors.default_advertised_address=33.44.55.66`

아래의 설정은 [*neo4j.conf*](/configuration/file-locations.md)의 "Causal Clustering Configuration"이라는 헤더 아래에 있습니다.

[`dbms.mode`](https://neo4j.com/docs/operations-manual/3.3/reference/configuration-settings/#config_dbms.mode)
  단일 데이터베이스 인스턴스의 작동 모드. Causal 클러스터링에는 두 가지 가능한 모드가 있습니다: `CORE` 또는 `READ_REPLICA`. 세개의 인스턴스에서 우리는 `CORE`모드를 사용할 것입니다. 다음 행의 주석 처리를 제거하십시오:`#dbms.mode=CORE`.
[`causal_clustering.expected_core_cluster_size`](https://neo4j.com/docs/operations-manual/3.3/reference/configuration-settings/#config_causal_clustering.expected_core_cluster_size)
  시작시 초기 클러스터 크기. 이것은 초기 안정적인 멤버십 상태를 달성하고 이후 클러스터에 안전하게 쓰기 위해 필요합니다. 이 값은 클러스터의 일부로서 포함하려는 코어 인스턴스의 수입니다. 예를 들어, `causal_clustering.expected_core_cluster_size=3`은 클러스터에 세개의 코어 구성원이 있음을 지정합니다.
[`causal_clustering.initial_discovery_members`](https://neo4j.com/docs/operations-manual/3.3/reference/configuration-settings/#config_causal_clustering.initial_discovery_members)
  이 코어 또는 Read Replica 인스턴스를 부트 스트랩 하는 데 사용할 수 있는 초기 코어 클러스터 구성원 집합의 네트워크 주소. 이 값은 쉼표로 구분 된 주소/포트 쌍 목록으로 제공됩니다. 가장 간단한 경우에, 지정된 네트워크 주소 각각은 작동하는 Neo4j 코어 서버 인스턴스로 해석됩니다. 이 값을 사용하려면 클러스터의 Core 인스턴스 주소들을 알아야합니다. 기본 포트는 `:5000`입니다. 이 설정에는 또한 로컬 시스템의 주소도 포함시켜야 합니다.

이 설정들을 각 인스턴스의 각 구성 파일에 적용하십시오. 값은 각각 동일합니다.

평소처럼 Neo4j 서버를 시작하십시오. 시작 순서는 중요하지 않습니다.

**예제 4.1. 코어 전용 클러스터 시작하기**
  `shellserver-1$ ./bin/neo4j start`
  `shellserver-2$ ./bin/neo4j start`
  `shellserver-3$ ./bin/neo4j start`

>> **시작 시간**

  서버의 시작과 함께 이를 따르기를 원한다면 [*neo4j.log*](/configuration/file-locations.md)의 메시지를 따를 수 있습니다. 인스턴스가 클러스터에 참여하는 동안 서버는 사용 불가능으로 표시될 수 있습니다.

이제 세대의 서버에 액세스하여 그들의 상태를 확인할 수 있습니다. 아래 위치를 웹 브라우저에서 열고 다음 쿼리를 실행하십시오: `CALL dbms.cluster.overview()`. 이것은 클러스터의 상태와 클러스터의 각 구성원에 대한 정보를 보여줄 것입니다.
* `http://server-1:7474/`
* `http://server-2:7474/`
* `http://server-3:7474/`

이제 세개의 인스턴스로 구성된 Neo4j Causal 클러스터가 실행 중입니다.

#### 4.2.3.2. 기존 클러스터에 코어 서버 추가
코어 클러스터에 인스턴스를 추가하는 것은 [4.2.3.1장, "코어 전용 클러스터 구성"](#4231-코어-전용-클러스터-구성)에서 설명한대로 적절한 구성으로 새로운 데이터베이스 서버를 단순히 시작하기만 하면 됩니다. 이러한 지침에 따라 다음과 같이 새 코어 서버의 원하는 구성을 반영하도록 *neo4j.conf*를 변경해야 합니다:
* `dbms.mode=CORE`를 설정하십시오.
* 기존 코어 서버의 해당 매개 변수와 동일하도록 `causal_clustering.initial_discovery_members`을 설정하십시오.

이렇게 하고 서버를 시작하면 새 서버가 기존 클러스터와 통합됩니다. 인스턴스가 많은 양의 데이터가 있는 클러스터에 합류하는 경우, 새 인스턴스가 클러스터에서 데이터를 다운로드하여 사용 가능하게 될 때까지 몇 분이 걸릴 수 있습니다. 서버가 해당 피어로부터 그래프 데이터를 복사하면 사용 가능하게 됩니다.

#### 4.2.3.3. 기존 클러스터에 Read Replica 추가
초기 Read replica 구성은 *neo4j.conf*를 통해 코어 서버와 유사하게 제공됩니다. Read Replica는 클러스터 쿼럼 결정에 참여하지 않으므로 구성이 더 짧습니다. 그들은 단순히 discovery 프로토콜을 실행하기 위해 바인딩 할 수 있는 일부 코어 서버의 주소만 알면 됩니다(자세한 내용은 [4.2.2.1장, "Discovery 프로토콜"](./lifecycle.md#4221-discovery-프로토콜) 참조). 초기 탐색이 완료되면 Read replica는 사용 가능한 코어 서버를 인식하고 따라갈 적절한 서버를 선택할 수 있습니다(어떻게 그것이 일어나는 지는 [4.2.2.5장, "Catchup 프로토콜"](./lifecycle.md#4225-catchup-프로토콜) 참조).

"Causal 클러스터링 구성" 장의 neo4j.conf 파일에서 다음 설정을 변경해야 합니다:
* 작동 모드를 Read Replica로 설정하십시오: `dbms.mode=READ_REPLICA`.
* 코어 서버의 해당 매개 변수와 동일하게 되도록 `causal_clustering.initial_discovery_members` 매개 변수를 설정하십시오.

#### 4.2.3.4. `neo4j-admin unbind`를 사용하여 클러스터에서 코어 제거
코어 서버를 독립 실행형 인스턴스로 다운그레이드 하려는 경우, 종료 데이터베이스에서 다음 명령어를 사용하여 수행할 수 있습니다:
`neo4j-admin unbind [--database=<name>]`
서버가 클러스터에서 바인딩을 해제하면 저장소 파일은 Neo4j 독립형 인스턴스와 동일합니다. 이 시점부터 이러한 파일들은 `단일` 모드에서 다시 시작하여 독립 실행형 인스턴스를 실행하는 데 사용할 수 있습니다.

>> 코어 서버 인스턴스의 디스크상의 상태는 독립 실행형 서버 인스턴스의 상태와 다릅니다. 일단 인스턴스가 클러스터에서 바인딩 해제되면 해당 클러스터와 다시 통합될 수 없다는 것을 이해하는 것이 중요합니다. 이는 클러스터와 단일 인스턴스가 이제 서로 다르고 양립할 수 없는 쓰기가 적용된 별도의 데이터베이스이기 때문입니다. 기술적으로 클러스터는 Raft 로그에 항목을 기록하는 반면, 독립 실행형 인스턴스는 트랜잭션 로그에 직접 기록할 것입니다(독립 실행형 인스턴스에는 Raft 로그가 없으므로).

>> 만일 독립 실행형 인스턴스를 클러스터에 다시 삽입하려고 한다면 로그와 저장소가 일치하지 않게 될 것입니다. 운영자는 데이터가 복제 될 것이라는 낙관적인 희망으로 독립 실행형 데이터베이스를 클러스터에 병합하지 마십시오. 그것은 일어나지 않을 것이며 예측할 수 없는 클러스터 동작으로 이어질 수 있습니다.

#### 4.2.3.5. 팔로워 전용 인스턴스의 바이어스 클러스터 리더십
Causal 클러스터의 코어 서버는 일관성과 안전성을 보장하기 위해 뗏목 프로토콜을 사용합니다. Raft의 구현 세부 사항은 리더의 상태를 복제하는 *팔로워* 역할을 하는 다른 인스턴스들을 가지고 기본 로그에 순서를 지정하기 위하여 *리더* 역할을 사용한다는 것입니다. Neo4j 용어에서 이것은 데이터베이스에 대한 쓰기가 현재 리더 역할을 담당하는 코어 인스턴스에 의해 정렬된다는 것을 의미합니다.

상황에 따라 운영자는 일부 인스턴스가 리더 역할을 맡지 못하도록 적극적으로 방지하고자 할 수 있습니다. 예를 들어 다중 데이터 센터 시나리오에서 리더는 성능 또는 관리적인 이유로 선호되는 지리적 위치에 있길 원할 수 있습니다. Neo4j Causal 클러스터링에서는 *리더가 되기를 거부*하는 인스턴스를 구성할 수 있습니다. 이는 항상 팔로워로 남아있는 것과 동일합니다. 이는 `causal_clustering.refuse_to_be_leader` 설정을 구성하여 수행됩니다. 클러스터의 최우선 순위는 가용성을 최대화하기위한 것이므로 일반적으로 이 옵션의 사용을 권장하지는 않습니다. 가장 높은 가용성은 건강한 인스턴스가 병적인 장애에 대해 클러스터의 리더십을 확보하는 데서 비롯됩니다.

>> 피상적인 유사성에도 불구하고, 리더가 아닌 코어 인스턴스는 Read Replica와 동일하지 않습니다. Read Replica는 트랜잭션 처리에 참여하지 않으며 클러스터 토폴로지 관리에 관여할 수도 없습니다.

>> 반대로 팔로워 전용 코어 인스턴스는 일관성과 안전성을 보장하기 위해 Raft에 따라 여전히 트랜잭션과 클러스터 멤버십 요청을 처리할 것입니다.
