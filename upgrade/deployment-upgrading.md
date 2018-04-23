## 5.1. 단일 인스턴스 업그레이드

```
이 섹션에서는 단일 Neo4j 인스턴스를 업그레이드하는 방법을 설명합니다.
```

Neo4j HA클러스터(Neo4j Enterprise Edition)를 업그레이드하려면 매우 구체적인 절차를 따라야 합니다. [섹션 5.4,"Neo4j HA클러스터 업그레이드"](./high-availability.md)를 참조하십시오.

### 5.2.1. 2.x에서 업그레이드

데비안을 사용하는 경우 [섹션 2.2.1.2"업그레이드"](../installation/linux/debian.md)를 참조하십시오.

  1. 데이터베이스가 실행 중이면 완전히 종료합니다.
  2. 데이터베이스 디렉터리와 [neo4j.conf](../configuration/file-locations.md)의 백업 복사본을 만듭니다.
  3. Neo4j EnterpriseEdition과 함께 제공되는 [온라인 백업 도구](../backup.md)를 사용하는 경우 백업이 성공적으로 완료되었는지 확인합니다.
  4. 이전 설치의 구성 파일에 있는 설정을 검토하고 모든 사용자 지정 설정을 3.3.5 설치에 적용합니다.
    - Neo4j 2.x와 3.3.5 사이에 많은 설정이 변경되었으므로 2.x-config-migrator(tools 디렉터리에서 포함)를 사용하여 구성 파일을 마이 그레이션 하는 것이 좋습니다.
    예를 들어 다음과 같은 명령으로 2.x-config-migrator를 호출할 수 있습니다.
    ```
    java -jar 2.x-config-migrator.jar path/to/neo4j2.3 path/to/neo4j3.3.5
    ```.
    
    - 출력된 경고를 메모하고 편집된 구성 파일을 수동으로 검토합니다.
    
  5. 다음 명령을 사용하여 이전 설치에서 데이터를 가져옵니다.
  
  ``` neo4j-admin import --mode=database --database=<database-name> --from=<source-directory> ```
  
  6. 데이터베이스를 graph.db라고 하지 않는 경우 _neo4j.conf_ 에서 ```dbms.active_database```를 데이터베이스 이름으로 설정합니다.
  
  7. 3.3.5 설치의 _neo4j.conf_ 에서 ```dbms.allow_format_migration=true```로 설정합니다. 이 설정을 안하면 Neo4j는 기동에 실패합니다.
