## 5.2. 단일 인스턴스 업그레이드

```
이 섹션에서는 단일 Neo4j 인스턴스를 업그레이드하는 방법을 설명합니다.
```

Neo4j HA클러스터(Neo4j Enterprise Edition)를 업그레이드하려면 매우 구체적인 절차를 따라야 합니다. [섹션 5.4,"Neo4j HA클러스터 업그레이드"](high-availability.md)를 참조하십시오.

### 5.2.1. 2.x에서 업그레이드

데비안을 사용하는 경우 [섹션 2.2.1.2"업그레이드"](../installation/linux/debian.md)를 참조하십시오.

  1. 데이터베이스가 실행 중이면 완전히 종료합니다.
  2. 데이터베이스 디렉터리와 [_neo4j.conf_](../configuration/file-locations.md)의 백업 복사본을 만듭니다.
  3. Neo4j EnterpriseEdition과 함께 제공되는 [온라인 백업 도구](../backup.md)를 사용하는 경우 백업이 성공적으로 완료되었는지 확인합니다.
  4. 이전 설치의 구성 파일에 있는 설정을 검토하고 모든 사용자 지정 설정을 3.3.5 설치에 적용합니다.
<br>&nbsp;&nbsp;&middot; Neo4j 2.x와 3.3.5 사이에 많은 설정이 변경되었으므로 2.x-config-migrator(tools 디렉터리에서 포함)를 사용하여 구성 파일을 마이 그레이션 하는 것이 좋습니다.
    예를 들어 다음과 같은 명령으로 2.x-config-migrator를 호출할 수 있습니다.
<br>&nbsp;&nbsp;```java -jar 2.x-config-migrator.jar path/to/neo4j2.3 path/to/neo4j3.3.5```.
<br>&nbsp;&nbsp;&middot; 출력된 경고를 메모하고 편집된 구성 파일을 수동으로 검토합니다.
    
  5. 다음 명령을 사용하여 이전 설치에서 데이터를 가져옵니다.
  
      ``` neo4j-admin import --mode=database --database=<database-name> --from=<source-directory> ```
  
  6. 데이터베이스를 graph.db라고 하지 않는 경우 _neo4j.conf_ 에서 ```dbms.active_database```를 데이터베이스 이름으로 설정합니다.
  7. 3.3.5 설치의 _neo4j.conf_ 에서 ```dbms.allow_format_migration=true```로 설정합니다. 이 설정을 안하면 Neo4j는 기동에 실패합니다.
  8. Neo4j 3.3.5.를 시작합니다. 데이터베이스 업그레이드는 시작할 때 수행됩니다.
  9. 업그레이드 및 진행률에 대한 자세한 내용은 [debug.log](../configuration/file-locations.md)에 기록됩니다.
  10. 업그레이드가 완료되면 ```dbms.allow_upgrade```옵션은 ```false```로 설정되거나 제거되어야 합니다.
  11. 업그레이드 후에는 즉시 전체 백업을 수행하는 것이 좋습니다.
  
  > **사이퍼 호환성**<br>
 Cypher언어는 Neo4j 버전 사이에서 진화할 수 있습니다. 이전 버전과의 호환성을 위해 Neo4j는 이전 Cypher언어 버전을 명시적으로 선택할 수 있는 지침을 제공합니다. 이 작업은 [Neo4j Developer Manual](https://neo4j.com/docs/developer-manual/3.4-preview/cypher/deprecations-additions-removals-compatibility/#cypher-compatibility)에 설명된 대로 전체 또는 개별 문장에 대해 적용할 수 있습니다.

### 5.2.2. 3.x에서 업그레이드

  1. 데이터베이스가 실행 중이면 완전히 종료합니다.
  2. 데이터베이스 디렉터리와 [_neo4j.conf_](../configuration/file-locations.md)의 백업 복사본을 만듭니다.
  <br>Neo4j Enterprise Edition와 함께 제공되는 [온라인 백업 도구](../backup.md)를 사용하는 경우 백업이 성공적으로 완료되었는지 확인합니다.
  3. 다음 방법 중 하나를 사용하여 Neo4j를 설치합니다.
  <br>&nbsp;&nbsp;a. 설치 시 tarball또는 zip파일을 사용하는 경우:
  <br>&nbsp;&nbsp;&nbsp;&nbsp;i. Neo4j 3.3.5의 압축을 풉니다.
  <br>&nbsp;&nbsp;&nbsp;&nbsp;ii. 이전 설치의 _neo4j.conf_ 에 있는 설정을 검토하고 모든 사용자 지정 설정을 3.3.5 설치로 전송합니다. 특히 `dbms.directories.data` 및 `dbms.active_database` 에 주의해야 합니다.
  <br>&nbsp;&nbsp;&nbsp;&nbsp;iii. 3.3.5 설치의 _neo4j.conf_ 에서 ```dbms.allow_format_migration=true```로 설정합니다. 이 설정을 안하면 Neo4j는 기동에 실패합니다.
  <br>&nbsp;&nbsp;&nbsp;&nbsp;iv. 이전 설치의 [_data_](../configuration/file-locations.md) 디렉터리를 새 설치로 복사합니다. `dbms.directories.data` 가 _NEO4J_HOME_ 외부의 디렉터리를 가리키는 경우에는 이 단계를 적용할수 없습니다.
  <br>&nbsp;&nbsp;b. Debian또는 RPM 배포를 사용하는 경우:
  <br>&nbsp;&nbsp;&nbsp;&nbsp;i. neo4j.conf에서 [`dbms.allow_upgrade=true`](https://neo4j.com/docs/operations-manual/current/reference/configuration-settings/#config_dbms.allow_upgrade)로 설정합니다.
  <br>&nbsp;&nbsp;&nbsp;&nbsp;ii. Neo4j 3.3.5를 설치합니다.
  <br>&nbsp;&nbsp;&nbsp;&nbsp;iii. 메시지가 표시되면 이전 버전과 Neo4j 3.3.5 의 _neo4j.conf_ 파일의 차이를 검토하고 사용자가 지정한 설정을 전송합니다. 위의 지침에 설정한 대로 ```dbms.allow_format_migration=true```로 설정합니다. 이 설정을 안하면 Neo4j는 기동에 실패합니다.
  4. Neo4j 3.3.5.를 시작합니다. 데이터베이스 업그레이드는 시작할 때 수행됩니다.
  5. 업그레이드 및 진행률에 대한 자세한 내용은 [_debug.log_](../configuration/file-locations.md)에 기록됩니다.
  6. 업그레이드가 완료되면 ```dbms.allow_upgrade```옵션은 ```false```로 설정되거나 제거되어야 합니다.
  7. 업그레이드 후에는 즉시 전체 백업을 수행하는 것이 좋습니다.
