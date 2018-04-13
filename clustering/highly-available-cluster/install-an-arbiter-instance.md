### 4.3.3. Arbiter 인스턴스 `Enterprise Edition`
> 이 장에서는 Neo4j HA 클러스터의 arbiter 인스턴스를 구성하는 방법에 대해 설명합니다.
Neo4j의 일반적인 배치는 내결함성과 읽기 확장성을 제공하기 위해 세 대의 시스템 클러스터를 사용합니다.
마스터가 사용할 수 없게 된 경우 장애 조치가 발생하려면 적어도 세 대의 인스턴스가 필요하지만, 모든 인스턴스가 전체 Neo4j 스택을 실행할 필요는 없습니다. 대신에, arbiter 인스턴스라고 하는 것이 배치될 수 있습니다. 선출 과정에서 유대 관계를 깨뜨리는 단일 목적으로 마스터 선출에 참여하는 것이 그들의 역할이라는 점에서 그들은 클러스터 참여자로 간주됩니다. 따라서 두 대의 Neo4j 데이터베이스 인스턴스와 추가적인 arbiter 인스턴스로 구성된 클러스터를 만들 수 있고, 그것은 세 인스턴스 중 하나의 단일 장애에 대한 내성을 여전히 가질 수 있습니다.
arbiter 인스턴스는 표준 Neo4j 클러스터 멤버와 동일한 설정을 사용하여 neo4j.conf에서 구성됩니다. 이 인스턴스는 [표 A.148, "dbms.mode"](https://neo4j.com/docs/operations-manual/3.3/reference/configuration-settings/#config_dbms.mode) 옵션을 ARBITER로 설정하여 'arbiter'로 구성됩니다.
