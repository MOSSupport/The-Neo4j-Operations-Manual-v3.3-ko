## 10.4. 일관성 체커
<div class="abstract">
이 절에서는 Neo4j 일관성 체커에 대하여 설명합니다.
</div>
neo4j-admin을 이용하여 데이터베이스나 백업 DB에 대하여 일관성을 체크합니다.

### 10.4.1. 데이터베이스나 백업 DB에 대한 일관성 체크

neo4j-admin 도구는 bin 디렉터리에 위치합니다. 데이터베이스의 일관성을 체크하기 위해서는 check-consistency 매개변수로 neo4j-admin 도구를 실행합니다.

**문법**
```
neo4j-admin check-consistency [--database=<name>] [--backup=</path/to/backup>] [--verbose[=<true|false>]] [--report-dir=<directory>] [--check-graph[=<true|false>]] [--check-indexes[=<true|false>]] [--check-label-scan-store[=<true|false>]] [--check-property-owners[=<true|false>]]
```
**옵션**
| 옵션 | 디폴트 | 설명 |
|---|---|---|
| --database | graph.db | 데이터베이스 명 |
| --backup |  | 일관성을 검사하기위한 백업 경로. --database와 함께 사용 불가. |
| --verbose | 거짓(false) | 자세한 출력 여부를 지정. |
| --report-dir | . | 보고서 파일을 출력할 디렉터리. |
| --check-graph | 참(true) | 노드, 관계, 속성, 유형, 토큰 간의 체크 수행. |
| --check-indexes | 참(true) | 인덱스의 체크 수행. |
| --check-label-scan-store | 참(true) | 레이블(label) 스캔 저장소 체크 수행. |
| --check-property-owners | 거짓(false) | 속성 소유권에 관한 추가 체크 수행. 이 체크는 시간-메모리가 매우 소비됩니다. |

**제한 사항**

현재 사용중인 데이터베이스의 일관성을 체크할 수는 없습니다. 만약 실행중인 데이터베이스에 체크를 수행하면, 실행이 안되고 오류를 보여줍니다.

**출력**

일관성 체커가 오류를 찾지 못하면 일관성 체커는 오류없이 끝내고 보고서를 생성하지 않습니다. 일관성 체커 프로그램에서 오류를 발견하면 종료 코드 1로 종료하고 inconsistencies-YYYY-MM-DD.HH24.MI.SS.report 형식의 이름 가진 보고서 파일을 작성합니다. 보고서 파일의 위치는 현재 작업 디렉토리이거나 report-dir 옵션으로 지정됩니다.
<div class="example">
예제 10.8. 일관성 체커 실행  
<div class="example-contents">
데이터베이스 일관성 체크에는 --database 옵션울 사용합니다. 주: 먼저 데이터베이스의 실행을 중지한 뒤 실행합니다.
<code>
$neo4j-home> bin/neo4j stop
$neo4j-home> bin/neo4j-admin check-consistency --database=graph.db

2017-02-02 09:45:31.719+0000 INFO  [o.n.k.i.s.f.RecordFormatSelector] Selected RecordFormat:StandardV3_0[v0.A.7] record format from store /Users/maria/neo4j-enterprise-3.2.0-alpha03/data/databases/graph.db
2017-02-02 09:45:31.719+0000 INFO  [o.n.k.i.s.f.RecordFormatSelector] Format not configured. Selected format from the store: RecordFormat:StandardV3_0[v0.A.7]
2017-02-02 09:45:31.963+0000 INFO  [o.n.m.MetricsExtension] Initiating metrics...
....................  10%
....................  20%
....................  30%
....................  40%
....................  50%
....................  60%
....................  70%
....................  80%
....................  90%
...................Checking node and relationship counts
....................  10%
....................  20%
....................  30%
....................  40%
....................  50%
....................  60%
....................  70%
....................  80%
....................  90%
.................... 100%
</code>
백업 DB에 관한 일관성 체크에는 --backup 옵션을 사용합니다.
<code>
bin/neo4j-admin check-consistency --backup backup/graph.db-backup
</code></div></div>