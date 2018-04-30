## 10.3. 데이터베이스의 dump와 load
> 이 절에서는 `neo4j-admin`의 dump와 load 명령을 설명합니다.

Neo4j 데이터베이스는 `neo4j-admin`의 dump와 load 명령을 사용하여 덤프와 로드됩니다:

`neo4j-admin dump --database=<데이터베이스-명> --to=<대상-경로>`
`neo4j-admin load --from=<아카이브-경로> --database=<데이터베이스-명> [--force]`

이 명령은 한 환경에서 다른 환경으로 데이터베이스를 이동하는데 유용합니다. 데이터베이스의 오프라인 백업에도 사용합니다. 온라인 오프라인 백업에 대한 설명은 6.1.1절 “온라인 오프라인 백업”을 참조합니다.

예제 10.6. neo4j-admin의 dump 명령  
***
graph.db라는 데이터베이스를 /backups/graph.db/2016-10-02.dump파일로 덤프합니다. 덤프 파일의 대상 경로는 /backups/graph.db이며, 명령 실행전에 해당 디렉터리가 반드시 존재해야 합니다.
```
$neo4j-home> bin/neo4j-admin dump --database=graph.db --to=/backups/graph.db/2016-10-02.dump
$neo4j-home> ls /backups/graph.db
$neo4j-home> 2016-10-02.dump
```
***
예제 10.7. neo4j-admin의 load 명령  
***
/backups/graph.db/2016-10-02.dump 파일에 포함된 백업 데이터베이스를 graph.db로 로드합니다. 데이터베이스가 실행 중이므로, 먼저 데이터베이스의 실행을 멈춥니다. --force 옵션을 사용하면, 기존 데이터베이스에 덮어쓰기됩니다.
```
$neo4j-home> bin/neo4j stop
Stopping Neo4j.. stopped
$neo4j-home> bin/neo4j-admin load --from=/backups/graph.db/2016-10-02.dump --database=graph.db --force
```
***
<span class="glyphicon glyphicon-info-sign" aria-hidden="true"> </span> Neo4j의 전문가는 데이터베이스 디렉토리의 내용과 기록 포맷(hostory format)을 알게 됩니다. 디렉토리를 복사-붙여 넣기와 같은 단축키는 데이터베이스를 편리하게 전송하고 시드(seed)하는데 사용됩니다. 그러나 데이터베이스 디렉토리의 형식과 내용은 시간이 지남에 따라 변경되므로, 단순한 파일 복사-붙여 넣기는 작동하지 않을 수도 있으며 문제가 될 수도 있습니다.
반대로 `neo4j-admin dump`와 `neo4j-admin load` 명령은 안전합니다. 파일을 내보내고 가져올 필요가 있는 파일과 가져올 수 없는 파일을 이해합니다. 따라서 서버간에 데이터베이스를 전송할 때 권장되는 방법입니다. 파일 시스템 수준에서 임의의 개입은 권장되지 않습니다.
