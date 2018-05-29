### 4.2.14. Causal 클러스터링 설정 참조 `Enterprise Edition`
> 이 장에서는 Neo4j Causal 클러스터 실행과 관련된 중요한 설정을 나열합니다.

#### 4.2.14.1. 공통 설정


| 매개 변수 이름 | 설명 |
|---------------|------|
| [`dbms.mode`](https://neo4j.com/docs/operations-manual/3.4/reference/configuration-settings/#config_dbms.mode) | 이 설정은 데이터베이스의 작동 모드를 구성합니다. Causal 클러스터링에는 두가지 모드가 있습니다: `CORE` 또는 `READ_REPLICA`.
예: `dbms.mode=READ_REPLICA`는 이 서버를 Read Replica로 정의합니다.|
