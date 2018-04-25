## 5.3. Neo4j Causal 클러스터 업그레이드

> 이 섹션에서는 Neo4j Causal 클러스터를 업그레이드하는 방법을 설명합니다.

Neo4j의 인스턴스를 최신 상태로 유지하는 것이 좋습니다. Neo4j Causal 클러스터를 Neo4j 3.3.5로 업그레이드하면 성능과 보안을 개선할 수 있을 뿐만 아니라 최신 버그에 대한 패치가 제공됩니다.

### 5.3.1. 업그레이드를 위한 중요한 사전 정보

모든 업그레이드는 세심한 계획과 테스트를 통해 진행되어야 합니다.

**사전 업그레이드 단계**

[섹션 5.1"업그레이드 계획"](upgrade-planning.md)을 자세히 읽고 여기에 나열된 모든 단계를 수행합니다.

**다운 타임**

Neo4j 3.3.5는 롤링 업그레이드를 지원하지 않으므로 업그레이드 프로세스중 전체 클러스터에 상당한 다운 타임이 발생할 수 있습니다.

기존 클러스터와 업그레이드된 클러스터를 동시에 실행할 수 있는 인프라가 있는 경우 다운 타임을 조금 줄일 수 있습니다.

**특정 버전의 변경 사항**

버전 변경에 대한 자세한 내용은 [릴리스 정보](https://neo4j.com/release-notes)에서 확인할 수 있습니다.

**롤백**

Neo4j 는 다운그레이드를 지원하지 않습니다. 단, 이 절에 설명한 단계는 비파괴(non-destructive)로 되어 있습니다. 어떤 이유로 업그레이드를 승인하지 않아 롤백을 수행해야 하는 경우 업그레이드되지 않은 데이터베이스를 `읽기/쓰기` 모드로 전환하고 클러스터를 다시 시작합니다.

### 5.3.2. Upgrade process

**가정**

- 기존 Causal 클러스터가 있습니다.
- 애플리케이션과 Neo4j 데이터베이스 사이에 로드 밸런싱 장치가 있습니다.
- 안전한 위치에 저장된 전체 백업이 있습니다.

**절차**

1. Prepare the new cluster:
2. Set your cluster to read-only. Note: This step involves downtime. 
3. 클러스터의 전체 백업을 수행합니다. 자세한 내용은 [섹션 6.2"백업 수행"](../backup/perform-backup.md)을 참조하십시오.
4. 새 클러스터의 서버 중 하나에서 업그레이드를 수행합니다.
<br>&nbsp;&nbsp;a. 새 클러스터에 있는 서버 중 하나의 임시 파일에 준비된 neo4j.conf를 저장합니다.
<br>&nbsp;&nbsp;&nbsp;&nbsp;```neo4j-home$ cp neo4j.conf neo4j.conf.save```
<br>&nbsp;&nbsp;b. _neo4j.conf_ 를 편집하여 이 서버를 독립 실행형 서버로 만들고 다음과 같이 업그레이드할 수 있도록 설정합니다.
<br>&nbsp;&nbsp;&nbsp;&nbsp;```dbms.mode=SINGLE```
<br>&nbsp;&nbsp;&nbsp;&nbsp;```dbms.allow_upgrade=true```
<br>&nbsp;&nbsp;c. 이 서버에 백업을 전송합니다.
<br>&nbsp;&nbsp;d.
<br>&nbsp;&nbsp;e.
<br>&nbsp;&nbsp;f.
<br>&nbsp;&nbsp;g.
5. 업그레이드된 데이터베이스를 사용하여 클러스터를 준비합니다. 자세한 내용은 [섹션 4.2.4 "Seed a Causal Cluster"](../clustering/causal-clustering/seed-cluster.md)를 참조하십시오.
<br>&nbsp;&nbsp;a. 마지막 백업을 새 클러스터의 다른 서버로 전송합니다.
<br>&nbsp;&nbsp;b. neo4j-admin을 사용하여 각 시스템의 백업을 복원합니다.
<br>&nbsp;&nbsp;&nbsp;&nbsp;```neo4j-home$ bin/neo4j-admin restore --from=/mnt/backup/graph.db-backup --database=graph.db --force```

6. 이전 클러스터의 서버를 종료합니다. **참고**:이 단계에서는 다운 타임이 발생합니다.
7. 로드 밸런서가 새 클러스터를 가리키도로록 설정합니다.
8. 클러스터의 서버를 시작하고 클러스터가 형성되어 트랜잭션을 수락하는지 확인합니다.
9. 좋은 실천 목적으로 업그레이드 후 즉시 전체 백업을 수행합니다.
