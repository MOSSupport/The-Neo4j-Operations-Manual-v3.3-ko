## 10.4. 일관성 체커
> 이 절에서는 Neo4j 일관성 체커에 대하여 설명합니다.

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
| --verbose | 거짓(false) | 자세한 출력을 지정. |
| --report-dir | . | 보고서 파일을 출력할 디렉터리. |
| --check-graph | 참(true) | 노드, 관계, 속성, 유형, 토큰 간의 체크 수행. |
| --check-indexes | 참(true) | 인덱스의 체크 수행. |
| --check-label-scan-store | 참(true) | 레이블(label) 스캔 저장소 체크 수행. |
| --check-property-owners | 거짓(false) | 속성 소유권에 관한 추가 체크 수행. 이 체크는 시간-메모리가 매우 소비됩니다. |

**제한 사항**

The consistency checker cannot be used with a database which is currently in use. If used with a running database, it will stop and print an error.

**출력**

If the consistency checker does not find errors, it will exit cleanly and not produce a report. If the consistency checker finds errors, it will exit with an exit code of 1 and write a report file with a name on the format inconsistencies-YYYY-MM-DD.HH24.MI.SS.report. The location of the report file is the current working directory, or as specified by the parameter report-dir.

예제 10.8. Run the consistency checker
Run with the --database option to check the consistency of a database. Note that the database must be stopped first.

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
Run with the --backup option to check the consistency of a backup.

bin/neo4j-admin check-consistency --backup backup/graph.db-backup
