### 4.2.14. Causal 클러스터링 설정 참조 `Enterprise Edition`
> 이 장에서는 Neo4j Causal 클러스터 실행과 관련된 중요한 설정을 나열합니다.

#### 4.2.14.1. 공통 설정


| 매개 변수 이름 | 설명 |
|---------------|------|
| [`dbms.mode`](https://neo4j.com/docs/operations-manual/3.4/reference/configuration-settings/#config_dbms.mode) | 이 설정은 데이터베이스의 작동 모드를 구성합니다. Causal 클러스터링에는 두가지 모드가 있습니다: `CORE` 또는 `READ_REPLICA`.<br><br>예: `dbms.mode=READ_REPLICA`는 이 서버를 Read Replica로 정의합니다.|
|[`causal_clustering.minimum_core_cluster_size_at_formation`](https://neo4j.com/docs/operations-manual/3.4/reference/configuration-settings/#config_causal_clustering.minimum_core_cluster_size_at_formation)|초기 클러스터 형성시 기대되는 클러스터 크기. 안전한 클러스터를 형성하기 위한 최소 인스턴스 수는 2개이지만, 최소한의 작동 내결함성을 허용하기 위해 기본적으로 3개로 설정되어 있어 한 인스턴스가 손실되더라도 클러스터가 작동할 수 있습니다.<br><br>예: `causal_clustering.minimum_core_cluster_size_at_formation=3`은 클러스터가 세 개의 코어 멤버로 시작하도록 지정합니다.(세 개 중 두 개의 클러스터가 형성될 때 시작하는 것이 안전할 것입니다).|
