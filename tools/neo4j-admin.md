## 10.1. neo4j-admin
> 이 절에서는 [neo4j-admin]을 설명합니다.

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
`neo4j-admin`은 [bin](../configuration/file-locations.md) 폴더에 위치하며 다음과 같이 실행합니다:
`neo4j-admin <command>`

#### 일반
* `check-consistency` 데이터베이스의 일관성을 체크합니다. 더 자세한 내용은 [일관성 체커](./consistency-checker.md) 절을 참조합니다.
* `help` 지금 보는 도움말이 보여지거나, <command>를 지정한 경우, 특정 명령의 도움말이 보여집니다.
* `import` CSV 파일이나 3.0-이전 데이터베이스에서 임포트합니다. 더 자세한 내용은 [임포트](./import.md) 절을 참조합니다.
* `memrec` Neo4j 힙과 페이지캐시 설정 추천을 보여줍니다. 더 자세한 내용은 [메모리 추천](./neo4j-admin-memrec.md) 절을 참조합니다.
* `store-info` Neo4j 데이터베이스 저장소 정보를 보여줍니다. 더 자세한 내용은 [저장소 정보 디스플레이](./display-store-information.md) 절을 참조합니다.
#### 인증
* `set-default-admin` 역할이 없을 때 기본 관리자를 설정합니다.
* `set-initial-password` 최초 관리자의 초기 암호('neo4j')를 설정합니다.
#### 클러스터링
* `unbind` 특정 데이터베이스의 클러스터 상태 데이터를 제거합니다. 자세한 명령은 [캐주얼 클러스터링](../clustering/causal-cluster.md) 을 참조합니다.
#### 오프라인 백업; 더 자세한 내용은 [dump 와 load 명령](./dump-and-load-databases.md)절을 참조합니다.
* `dump` 단일 파일로 데이터베이스를 덤프합니다.
* `load` `dump`명령으로 생성한 아카이브에서 데이터베이스를 로드합니다.
#### 온라인 백업; 더 자세한 내용은 [백업](../back.md)절을 참조합니다.
* `backup` 실행 중인 Neo4j 엔터프라이즈 서버에서 온라인 백업을 실행합니다.
* `restore` 백업된 데이터베이스를 복원합니다.
더 자세한 내용은 `neo4j-admin help <command>`을 참조합니다.
