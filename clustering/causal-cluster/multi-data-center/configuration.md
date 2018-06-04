### 4.2.9. 다중 데이터 센터 운영 구성 `Enterprise Edition`
> 이 장에서는 Neo4j 서버가 토폴로지/데이터 센터를 인식하도록 구성하는 방법을 보여줍니다. 이것은 확장 가능한 다중 데이터 센터 배포를 달성하는 데 필요한 정확한 구성을 설명합니다.

>> **다중 데이터 센터 운영 활성화**
>>
>> 다중 데이터 센터 기능은 별도로 라이센스가 부여되며 구체적으로 활성화해야 합니다. 자세한 내용은 ["다중 데이터 센터 운영을 위한 라이센스" 장](../multi-data-center.md#다중-데이터-센터-운영을-위한-라이센스)을 참조하십시오.

#### 4.2.9.1. 다중 데이터 센터 운영 활성화
다른 작업을 수행하기에 앞서, 우리는 다중 데이터 센터 기능을 활성화해야 합니다. 이는 ["다중 데이터 센터 운영을 위한 라이센스" 장](../multi-data-center.md#다중-데이터-센터-운영을-위한-라이센스)에서 설명합니다.

#### 4.2.9.2. 서버 그룹
우리의 특정 요구 사항에 따라 Causal 클러스터 서버의 사용을 최적화하기 위해 우리는 그들을 *서버 그룹*으로 정렬합니다. 서버 그룹 멤버십은 운영자 도메인의 데이터 센터, 가용 영역 또는 기타 중요한 토폴로지 요소에 매핑 할 수 있습니다. 서버 그룹은 또한 겹쳐질 수도 있습니다.

서버 그룹은 Causal 클러스터의 서버 세트에 매핑되는 키로 정의됩니다. 서버 그룹 멤버십은 *neo4j.conf*의 [`causal_clustering.server_groups`](https://neo4j.com/docs/operations-manual/3.4/reference/configuration-settings/#config_causal_clustering.server_groups) 매개 변수를 사용하여 각 서버에 정의됩니다. Causal 클러스터의 각 서버는 0개 또는 여러 개의 서버 그룹에 속할 수 있습니다.

##### 예제 4.12. 서버 그룹 멤버십 정의
-----------------------------
서버 그룹의 멤버십은 다음 예제와 같이 *neo4j.conf*에서 설정할 수 있습니다:

`````````````````
# Add the current instance to the groups `us` and `us-east`
causal_clustering.server_groups=us,us-east
`````````````````
````````````````
# Add the current instance into the group `london`
causal_clustering.server_groups=london
````````````````
````````````````
# Add the current instance into the group `eu`
causal_clustering.server_groups=eu
```````````````````

각 서버 그룹의 멤버십은 명시적이어야 합니다. 예를 들어, `gb-london` 그룹의 서버는 그 서버가 명시적으로 해당 그룹에 추가되지 않는 한 자동적으로 일부 `gb` 또는 `eu` 그룹의 일부가 아닙니다. 즉, 그룹 간의 (암시적인) 모든 관계는 해당 그룹이 업스트림 시스템에서 데이터를 요청하기 위한 기초로 사용될 때만 구체화됩니다.
-----------------------

서버 그룹은 필수는 아니지만, 존재하지 않으면 서버에 대한 특정 업스트림 트랜잭션 종속성을 설정할 수 없습니다. 지정된 서버 그룹이 없는 경우, 클러스터는 기본적으로 가장 비관적인 대체 동작을 수행합니다: 각 Read Replica는 임의의 코어 서버로부터 작동할 것입니다.

#### 4.2.9.3. 전략 플러그인
*전략 플러그인*은 트랜잭션 로그를 동기화하기 위해 클러스터의 Read Replica가 서버에 접속하는 방법을 정의하는 일련의 규칙입니다. Neo4j는 사전 정의된 전략 세트와 함께 제공되며, 설계 특정 언어, DSL을 제공하여 사용자 정의 전략을 유연하게 생성할 수 있습니다. 마지막으로, Neo4j는 고급 사용자가 업스트림 권장 사항을 향상시키는 데 사용할 수있는 API를 지원합니다.

일단 전략 플러그인이 만족스러운 업스트림 서버를 확인하면 트랜잭션을 가져 와서 로컬 Read Replica를 업데이트하여 단일 동기화를 수행하는 데 사용됩니다. 이후 업데이트의 경우, 이 절차가 반복되어 항상 가장 선호되는 사용 가능한 업스트림 서버가 확인됩니다.

#### 사전 정의된 전략을 사용하여 업스트림 선택 전략 구성하기
Neo4j에는 다음과 같은 사전 정의된 전략 플러그인이 포함되어 있습니다:
| 플러그인 이름 | 결과 동작 |
|--------------|----------|
|`connect-to-random-core-server`|현재 사용 가능한 것들 중에서 임의로 선택하여 **코어 서버**에 연결.|
|`typically-connect-to-random-read-replica`|사용 가능한 **Read Replica**에 연결하나, 시간의 약 10%는 임이의 코어 서버에 연결.|
|`connect-randomly-within-server-group`|`causal_clustering.server_groups`에 지정된 서버 그룹에서 사용 가능한 아무 인스턴스(코어 서버 *및* Read Replica)에 임의로 연결.|

사전 정의된 전략은 [`causal_clustering.upstream_selection_strategy`](https://neo4j.com/docs/operations-manual/3.4/reference/configuration-settings/#config_causal_clustering.upstream_selection_strategy) 옵션을 구성하여 사용됩니다. 이렇게 함으로써 우리는 트랜잭션 데이터의 업스트림 제공자를 확인하기 위한 전략의 순서 선호도를 지정할 수 있습니다. 우리는 목록의 앞부분에 선호하는 전략과 함께 쉼표로 구분된 전략 플러그인 이름의 목록을 제공합니다. 업스트림 전략은 목록 순서의 각 전략에 트랜잭션을 추출할 수 있는 업스트림 서버를 제공할 수 있는지 여부를 질문하여 선택되어 집니다.

##### 예제 4.13. 업스트림 선택 전략 정의
-------------------------------------------
다음 구성 예제를 고려하십시오:
````
causal_clustering.upstream_selection_strategy=connect-randomly-within-server-group,typically-connect-to-random-read-replica
```

이 구성을 사용하면 인스턴스는 먼저 `causal_clustering.server_groups`에 지정된 그룹의 다른 인스턴스에 연결을 시도합니다. 해당 그룹에서 라이브 인스턴스를 찾지 못하면 임의의 Read Replica에 연결합니다.

**그림 4.16. 전략의 첫 번째 만족스러운 응답이 사용됩니다.**
 ![pipeline-of-strategies](./pipeline-of-strategies.png)

 다운 스트림 서버가 업 스트림 장애가 발생한 경우에도 여전히 라이브 데이터에 액세스 할 수 있도록 하기 위해, 모든 인스턴스의 최후의 수단은 항상 임의의 코어 서버에 연결하는 것입니다. 이는 `causal_clustering.upstream_selection_strategy` 구성을 `connect-to-random-core-server`를 사용하여 종료하는 것과 동일합니다.
 ----------------------------------------------

#### 사용자 정의된 전략 구성하기
