## 10.6. 저장소 정보 디스플레이
<div class="abstract">
이 장에서는 `neo4j-admin`의 `store-info` 명령을 설명합니다.
</div>
`neo4j-admin`의 `store-info` 명령을 사용하여 Neo4j 데이터베이스의 버전이 디스플레이됩니다:  
```
neo4j-admin store-info --store=<path-to-store>
```
store-info는 매개변수로 Neo4j 데이터베이스나 백업 경로를 사용합니다. 결과 출력에서의 `Store format superseded in:` 메시지가 있으면, 이는 저장소 형식으로 인해 저장소가 백업 복원 중에 포맷 마이그레이션이 필요한 경우입니다.

<span class="glyphicon glyphicon-info-sign" aria-hidden="true"> </span> Neo4j Admin `store-info` 명령은 실행중인 데이터베이스에서 사용할 수 없습니다. 데이터베이스가 실행 중일 때 스토어는 보호를 위해 잠겨져 있으며 store-info 명령을 사용하면 이를 나타내는 오류 메시지와 함께 실패합니다.  
예제 10.10. 오래된 백업에서의 사용 예제   
Neo4j의 예전 백업을 /backups/graph-db.2016-11-11/ 폴더에 저장되어 있고 이를 복원한다고 가정합니다. 백업 시점의 Neo4j의 버전은 분명하지 않습니다. Neo4j 버전을 알아 내려면 다음을 실행합니다:
```
$neo4j-home> bin/neo4j-admin store-info --store=/backups/graph-db.2016-11-11
Store format version:         vE.H.0
Introduced in version:        3.0.0
Store format superseded in:   3.0.6
```
출력 결과는 데이터베이스가 high-limit 포맷, 즉,  엔터프라이즈 버전을 사용하도록 구성되었음을 알 수 있습니다. 즉, Neo4j Enterprise Edition에서만 백업될 수 있습니다.

출력 결과에 이 포맷은 Neo4j 3.0.0에 소개되었고, 더 새로운 포맷은 Neo4j 3.0.6에 출시되었음을 보여줍니다. 이 의미는 백업이 복원되려면 반드시 포맷 마이그레이션이 수행되어야함을 의미합니다.(자세한 내용은 [5장. 업그레이드](../upgrade.md)를 참조합니다.).

예제 10.11. 새로운 백업 사용 예제  
앞의 예제와 같이, Neo4j의 또 다른 백업이 /backups/graph-db.2016-11-11/ 있다고 가정합니다. 이 경우 출력 결과는 다음과 같습니다:
```
$neo4j-home> bin/neo4j-admin store-info --store=/backups/graph-db.2016-11-11
Store format version:    v0.A.7
Introduced in version:   3.0.0
```
명령의 출력 결과는 Neo4j의 모든 버전에서 지원되는 표준 저장소 형식으로 백업되었음을 나타냅니다. 또한 Neo4j의 가장 최신 버전입니다. 이는 백업을 복원하기 위해 형식 마이그레이션 할 필요가 없음을 의미합니다. 