## 10.1. neo4j-admin
```
이 절에서는 [neo4j-admin]을 설명합니다.
```
`neo4j-admin`은 Neo4j 인스턴스를 관리하기 위한 기본 도구입니다. 제품과 같이 설치되는 명령-행 도구이며 여러 명령으로 실행됩니다. 일부 명령은 별도의 절에서 자세히 설명합니다.
### 10.1.1. 환경 변수
`neo4j-admin`은 다음의 환경 변수를 사용합니다:

| 환경 변수 | 설명 |
|---|---|
| NEO4J_DEBUG | 디버그 출력을 활성화할 때 지정. |
| NEO4J_HOME | Neo4j 홈 디렉터리. |
| NEO4J_CONF | neo4j.conf파일이 포함된 디렉터리. |
| HEAP_SIZE | 명령 실행시 JVM 최대 힙 크기 지정. 512m 처럼 숫자와 단위 기술. |

### 10.1.2. 종료(Exit) 코드
`neo4j-admin`이 정상 종료되면 종료 코드 0을 리턴합니다. 0이 아닌 종료 코드는 명령 실행 중 오류가 발생했음을 의미합니다. 0이 아닌 종료 코드는 백업 명령의 종료 코드와 같은 오류에 대한 추가 정보를 포함 할 수 있습니다

### 10.1.3. 문법과 명령
`neo4j-admin`은 bin 폴더에 위치하며 다음과 같이 실행합니다:
`neo4j-admin <command>`

#### 일반
* `check-consistency` Check the consistency of a database. For details, see the Consistency Checker.
* `help` This help text, or help for the command specified in <command>.
* `import` Import from a collection of CSV files or a pre-3.0 database. For details, see the import section.
* `memrec` Print Neo4j heap and pagecache memory settings recommendations. For details, see Memory recommendations.
* `store-info` Prints information about a Neo4j database store. For details, see Display store information.
#### 인증
* `set-default-admin` Sets the default admin user when no roles are present.
* `set-initial-password` Sets the initial password of the initial admin user ('neo4j').
#### 클러스터링
* `unbind` Removes cluster state data for the specified database. 자세한 명령은 [캐주얼 클러스터링](../clustering/causal-cluster.md) 을 참조합니다.
#### 오프라인 백업; 더 자세한 내용은 [dump 와 load 명령](./dump-and-load-databases.md)절을 참조합니다.
* `dump` Dump a database into a single-file archive.
* `load` Load a database from an archive created with the dump command.
#### 온라인 백업; 더 자세한 내용은 [백업](../back.md)절을 참조합니다.
* `backup` Perform an online backup from a running Neo4j enterprise server.
* `restore` Restore a backed up database.
더 자세한 내용은 `neo4j-admin help <command>`을 참조합니다.
