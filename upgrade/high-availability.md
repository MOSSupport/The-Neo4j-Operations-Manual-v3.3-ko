## 5.4. Neo4j HA클러스터 업그레이드

> 이 섹션에서는 Neo4j HA클러스터를 업그레이드하는 방법을 설명합니다.

Neo4j HA클러스터를 Neo4j 3.3.5로 업그레이드하려면서 클러스터가 일관되게 유지되고 업그레이드 후에 모든 클러스터 인스턴스가 클러스터에 가입하고 동기화할 수 있도록 하기 위해 다음과 같은 프로세스가 필요합니다.

Neo4j 3.3.5는 롤링 업그레이드를 지원하지 않습니다.

### 5.4.1. Neo4j 데이터베이스 백업합니다.

- 업그레이드 절차를 시작하기 전에 데이터베이스의 전체 백업을 만드는 것이 매우 중요합니다.
- Neo4j 데이터베이스 백업에 대한 자세한 지침은 [백업](../backup.md)의 내용을 참조하십시오.


### 5.4.2. 클러스터를 종료합니다.
  1. 슬레이브 인스턴스를 하나씩 차례로 종료합니다.
  2. 마지막으로 마스터를 종료합니다.

### 5.4.3. 마스터를 업그레이드 합니다.

1. 데이터베이스 디렉터리를 변경하지 않고 그대로 유지하면서 마스터 서버에 Neo4j 3.3.5를 설치합니다.
2. 구성에서 HA를 사용하지 않도록 _neo4j.conf_ 파일에서 `dbms.mode=SINGLE` 로  설정합니다.
3. [단일 인스턴스 업그레이](deployment-upgrading.md)드 지침에 따라 Neo4j를 업그레이드합니다.
4. 업그레이드가 완료되면 Neo4j를 다시 종료하십시오.
5. 구성에서 HA를 다시 사용하도록 _neo4j.conf_ 파일에서 `dbms.mode=HA` 로 설정합니다.
6. Neo4j 데이터베이스의 전체 백업을 만듭니다.<br><br>
업그레이드 전에 생성된 백업은 더 이상 증분 온라인 백업을 통해 업데이트할 수 없습니다. 따라서 이 시점에서 빈 대상 디렉터리를 사용하여 전체 백업을 수행하는 것이 중요합니다.

### 5.4.4. 슬레이브 인스턴스들을 업그레이드 합니다.
각 슬레이브에서 다음 절차를 수행합니다.

1. 데이터베이스 디렉터리를 삭제합니다.
2. Neo4j 3.3.5를 설치합니다.
3. 이전 설치의 구성 파일에서 설정을 검토하고 모든 사용자 정의 설정을 3.3.5 설치로 전송합니다. 버전 사이에 이름이 변경된 설정에 유의해야 합니다.
4. 데이터베이스를 _graph.db_ 라고 하지 않는 경우 _neo4j.conf_ 에서 `dbms.active_database` 를 데이터베이스 이름으로 설정합니다.
5. 보안 구성이 자동으로 전파되지 않으므로 해당하는 경우 마스터에서 보안 구성을 복사합니다.

> 이 시점에서 데이터베이스 디렉터리를 마스터에서 슬레이브로 수동으로 복사할 수 있습니다. 이렇게 하면 시작할 때 마스터에서 동기화할 필요가 없기에 대규모 데이터베이스를 업그레이드할 때 상당한 시간을 절약할 수 있습니다.

### 5.4.5. 클러스터를 다시 시작합니다.
  1. 마스터를 시작합니다.
  2. 슬레이브 인스턴스를 하나씩 차례로 시작합니다.

슬레이브가 클러스터에 가입하면 클러스터는 마스터 인스턴스의 데이터베이스를 동기화합니다.
