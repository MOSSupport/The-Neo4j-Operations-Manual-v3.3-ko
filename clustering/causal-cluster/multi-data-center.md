### 4.2.7. 다중 데이터 센터 설계 및 운영 `Enterprise Edition`
> 이 장에서는 Neo4j의 다중 데이터 센터 기능을 소개합니다.

일부 사용 사례는 가용성, 중복성, 클라이언트 응용 프로그램의 지역성 또는 단순하게 확장성에 대한 높은 요구를 나타냅니다. 이러한 경우 클러스터가 물리적 토폴로지를 인식하여 작업 부하를 최적화 할 수 있다는 것이 중요합니다. 이를 통해 단일 클러스터를 구성하여 여러 데이터 센터에 걸쳐 필요한 제안을 구성할 수 있습니다.

다음 장에서는 Causal 클러스터의 다중 데이터 센터 운영의 다양한 측면을 설명합니다.

다음 주제들이 자세히 설명됩니다:
* [다중 데이터 센터 설계](./multi-data-center/design.md) - 다중 데이터 센터 배포를 위한 패턴
* [다중 데이터 센터 운영 구성](./multi-data-center/configuration.md) - 다중 데이터 센터 배치를 위한 Neo4j 구성 옵션
* [다중 데이터 센터 시스템을 위한 로드 밸런싱](./multi-data-center/load-balancing.md) - 클라이언트 애플리케이션이 다중 데이터 센터 토폴로지를 인식할 수있게 해주는 Neo4j 구성 옵션
* [데이터 센터 복구](./multi-data-center/disaster-recovery.md) - 데이터 센터 손실 후 클러스터를 완전 가동 상태로 복구하는 방법

#### 다중 데이터 센터 운영을 위한 라이센스
Neo4j의 대부분은 오픈 소스 소프트웨어이며, 공개되는 오픈 소스 라이센스의 제약 내에서 합법적으로 사용될 수 있습니다. 그러나 다중 데이터 센터 운영은 상용 데이터베이스 라이센스 하에서 일반적으로 작동하는 Neo4j의 매우 까다로운 사용자를 대상으로 합니다. 상업적 기반에서 다중 데이터 센터 기능은 단일 데이터 센터 Causal 클러스터링 기능과 별도로 라이센스가 부여됩니다.

적절한 라이센스 하에 작동하는지 확인하려면 [*neo4j.conf*](../../configuration/file-locations.md)에서 명시적으로 다음을 설정해야 합니다:
[`causal_clustering.multi_dc_license=true`](https://neo4j.com/docs/operations-manual/3.3/reference/configuration-settings/#config_causal_clustering.multi_dc_license)

이 구성을 사용하지 않으면 모든 다중 데이터 센터 기능이 비활성화 된 상태로 유지됩니다.
