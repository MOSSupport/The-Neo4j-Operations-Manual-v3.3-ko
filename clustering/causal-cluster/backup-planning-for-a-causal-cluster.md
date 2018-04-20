### 4.2.5. Causal 클러스터의 백업 계획 `Enterprise Edition`
> 이 장에서는 Neo4j Causal 클러스터의 백업 전략을 설계할 때 고려해야 할 사항에 대해 설명합니다.

Neo4j Causal Clustering 클러스터에서 Core Server와 Read Replica는 모두 [백업 프로토콜](./lifecycle.html#4.2.2.6.-backup-protocol)을 지원합니다. 두 역할의 서버는 모두 클러스터 백업에 사용할 수 있습니다. 이 장에서는 사용할 백업 전략을 결정하기 전에 고려해야 할 몇 가지 고려 사항에 대해 설명합니다. 백업 및 복구 명령에 대한 자세한 지침은 [백업 챕터](/backup.md)에서 설명합니다.

#### 4.2.5.1. Read replica 백업
일반적으로 우리는 전형적인 클러스터 배포에서 Core Server보다 훨씬 더 많기 때문에 Read Replica가 백업 공급자 역할을 하도록 선택하는 것을 선호합니다.

그러나 Read Replica는 Core Server에서 비동기적으로 복제되기 때문에 코어 클러스터와 관련하여 트랜잭션을 적용할 때 어느 정도 뒤처질 수 있습니다. Read replica가 Core Server에서 연결이 끊어지는 경우도 있으므로 해당 복제본의 내용이 꽤 오래 되었을 수도 있습니다. 여기에서 병리학적으로 나쁜 경우는 바로 이전의 백업보다 덜 최신으로 끝난 내용을 지금 당장 백업 하는 것입니다.
