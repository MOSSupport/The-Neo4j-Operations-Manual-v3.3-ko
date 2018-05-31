## 4.2. Causal 클러스터 `Enterprise Edition`
> 이 장에서는 이론적 배경 및 아키텍처는 물론 구성 세부 정보 및 지침을 포함하여 Neo4j Causal 클러스터링에 대한 포괄적인 설명을 제공합니다.

이 장에서는 Causal 클러스터링에 대한 포괄적인 설명을 제공합니다. Causal 클러스터링의 개념에 대한 기본 소개와 고급 배포 옵션에 대한 프레젠테이션의 두 부분으로 나뉘어집니다.

#### 설치 및 구성
첫 번째 부분은 이론적 배경과 아키텍처에 대한 토론으로 시작됩니다. 그런 다음 명시적인 구성 세부 정보와 Causal 클러스터를 구성하고 조작하는 방법에 대한 지시 사항을 제공합니다.
* [아키텍처](./causal-cluster/architecture.md) - Causal 클러스터링 아키텍처의 개요
* [라이프사이클](./causal-cluster/lifecycle.md) - 클러스터의 수명 주기에 대한 설명
* [새로운 Causal 클러스터 생성](./causal-cluster/create-a-new-causal-cluster.md) — 새로운 Causal 클러스터를 만드는 방법
* [클러스터 시드](./causal-cluster/seed-a-cluster.md) - 기존 데이터베이스에서 Causal 클러스터를 만드는 방법
* [Causal 클러스터 백업 계획](./causal-cluster/backup-planning-for-a-causal-cluster.md) - Causal 클러스터의 백업 전략 설계시 고려 사항
* [클러스터 내 암호화](./causal-cluster/intra-cluster-encryption.md) - 클러스터 통신 보안 방법

Neo4j Causal 클러스터를 업그레이드하는 방법에 대한 지침은 [Causal 클러스터 업그레이드](../upgrade/causal-cluster.md)를 참조하십시오.

Neo4j Causal 클러스터를 모니터링하는 데 사용할 수 있는 기능에 대한 요약은 [8장, *모니터링*](../monitoring.md) (특히 [Causal 클러스터 모니터링](../monitoring/causal-cluster.md))을 참조하십시오.

#### 다중 데이터 센터 설계 및 운영
두 번째 부분은 다중 데이터 센터 운영을 위한 고급 배포 및 구성 옵션을 전담합니다.
* [소개](./causal-cluster/multi-data-center.md) - Neo4j 내의 다중 데이터 센터 기능 소개
* [다중 데이터 센터 설계](./causal-cluster/multi-data-center/design.md) - 다중 데이터 센터 배포 패턴
* [다중 데이터 센터 운영 구성](./causal-cluster/multi-data-center/configuration.md) - 다중 데이터 센터 배포를 위한 Neo4j 구성 옵션
* [다중 데이터 센터 시스템을 위한 로드 밸런싱](./causal-cluster/multi-data-center/load-balancing.md) - 클라이언트 애플리케이션이 다중 데이터 센터 토폴로지를 인식할 수있게 해주는 Neo4j 구성 옵션
* [데이터 센터 복구](./causal-cluster/multi-data-center/disaster-recovery.md) - 데이터 센터 손실 후 클러스터를 완전 가동 상태로 복구하는 방법

#### 설정 참조
* [설정 참조](./causal-cluster/settings.md) - 가장 중요한 Causal 클러스터 설정 요약
단일 시스템에서 테스트 클러스터를 로컬로 설정하려면 [how to set up a local Causal Cluster](https://neo4j.com/docs/operations-manual/3.3/tutorial/local-causal-cluster/)을 참조하십시오
