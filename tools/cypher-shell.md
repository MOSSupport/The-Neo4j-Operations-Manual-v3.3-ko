## 10.7. Cypher Shell
> 이 절은 Neo4j Cypher Shell에 대해 설명합니다.

Cypher shell은 제품의 일부로 설치되는 명령-줄 도구입니다. 이 도구는 Neo4j 데이터베이스에 연결하고 Cypher를 사용하여 데이터 쿼리, 스키마 정의 또는 관리 작업을 수행 할 수 있습니다. Cypher Shell은 명시적으로 트랜잭션을 노출하여 여러 작업을 그룹화하고 적용 또는 롤백 할 수 있습니다. Cypher Shell은 암호화된 바이너리 프로토콜인 Bolt를 통해 통신합니다.

### 10.7.1. Cypher Shell 호출하기
Cypher Shell은 `bin` 디렉터리에 위치하며 다음과 같이 호출됩니다. 
```
cypher-shell [-h] [-a ADDRESS] [-u USERNAME] [-p PASSWORD] [--encryption {true,false}] [--format {verbose,plain}] [--debug] [--non-interactive] [-v] [--fail-fast | --fail-at-end] [cypher]
```
**매개 변수**
| **위치적인 매개변수:** | |
|---|---|
| cypher | 실행하고 종료할 선택적 cypher 문자열입니다. |
| **옵션적인 매개변수:** |  |
| -h, --help | 도움말 메시지를 보여주고 종료. |
| --fail-fast | 파일에서 읽을 때, 첫 번째 오류가 발생하면 종료하고 실패를 보고(기본 동작). |
| --fail-at-end | 파일 읽기가 끝난 후 종료하고 실패를 보고. |
| --format {auto,verbose,plain} | 원하는 출력 포맷. auto는  인터렉티브 쉘에서는 테이블 포맷으로, 스크립팅을 사용 중이면 최소 포맷으로 디스플레이합니다. verbose는 결과는 테이블 포맷으로 디스플레이하고 통계값도 출력하고, plain은 데이터를 최소 포맷으로 디스플레이합니다.(디폴트: auto). |
| --debug | 추가 디버그 정보를 출력합니다.(디폴트: false). |
| --non-interactive | 비-인터렉티브 모드로 강제; auto-detection 실패시에만 유용 (디폴트: false). |
| -v, --version | cypher-shell의 버전 출력하고 종료 and exit (디폴트: false). |
| **연결 매개변수:** | |
| -a ADDRESS, --address ADDRESS | 연결에 사용될 주소와 포트 (디폴트: bolt://localhost:7687). |
| -u USERNAME, --username USERNAME | 연결에 사용될 사용자명. 환경 변수 NEO4J_USERNAME로 명시 가능 (디폴트: ). |
| -p PASSWORD, --password PASSWORD | 연결에 사용될 암호.  환경 변수 NEO4J_PASSWORD로 명시 가능 (디폴트: ). |
| --encryption {true,false} | Neo4j 연결의 암호화 여부  반드시 암호화합니다.; Neo4j의 설정과 일치해야 합니다.(디폴트: true). |

예제 10.12. 사용자명과 암호로 Cypher Shell 호출  
***
```
$neo4j-home> bin/cypher-shell -u johndoe -p secret

Connected to Neo4j at bolt://localhost:7687 as user neo4j.
Type :help for a list of available commands or :exit to exit the shell.
Note that Cypher queries must end with a semicolon.
neo4j>
```
***
예제 10.13. Cypher Shell 내에서 도움말 호출   
***
```
neo4j> :help
Available commands:
  :begin    Open a transaction
  :commit   Commit the currently open transaction
  :exit     Exit the logger
  :help     Show this help message
  :history  Print a list of the last commands executed
  :param    Set the value of a query parameter
  :params   Prints all currently set query parameters and their values
  :rollback Rollback the currently open transaction

For help on a specific command type:
    :help command
```    
***
예제 10.14. Cypher Shell 내에서 쿼리 실행   
***
```
neo4j> MATCH (n) RETURN n;
+-----------------------------------------------------------------+
| n                                                               |
+-----------------------------------------------------------------+
| (:Person {name: "Bruce Wayne", alias: "Batman"})                |
| (:Person {name: "Selina Kyle", alias: ["Catwoman", "The Cat"]}) |
+-----------------------------------------------------------------+  
```
***
예제 10.15. 명령-줄에서 Cypher 스크립트와 함께 Cypher Shell 호출   
***
다음은 examples.cypher라는 파일의 내용입니다:
```
MATCH (n) RETURN n;
MATCH (batman:Person {name: 'Bruce Wayne'}) RETURN batman;
```
명령-줄에서 'examples.cypher' 스크립트를 호출합니다. 이 절의 나머지 부분의 모든 예제는 간단한 출력을 위해`--format plain` 플래그를 사용할 것입니다. 
```
$neo4j-home> cat examples.cypher | bin/cypher-shell -u neo4j -p secret --format plain
n
(:Person {name: "Bruce Wayne", alias: "Batman"})
(:Person {name: "Selina Kyle", alias: ["Catwoman", "The Cat"]})
batman
(:Person {name: "Bruce Wayne", alias: "Batman"})
```
***
### 10.7.2. 쿼리 매개변수
Cypher Shell은 매개변수에 의한 쿼리를 지원합니다. 종종 스크립팅에서  사용됩니다.  

예제 10.16. Cypher Shell에서 매개변수 사용  
***
':param' 키워드를 사용하여 매개변수 'thisAlias'에 'Robin'을 지정합니다. ':params' 키워드를 사용하여 매개변수를 체크합니다.
```
neo4j> :param thisAlias 'Robin'
neo4j> :params
thisAlias: Robin
```
이제 Cypher 쿼리에서 'thisAlias' 매개변수를 사용합니다. 결과를 검증합니다.
```
neo4j> CREATE (:Person {name : 'Dick Grayson', alias : {thisAlias} });
Added 1 nodes, Set 2 properties, Added 1 labels
neo4j> MATCH (n) RETURN n;
n
(:Person {name: "Bruce Wayne", alias: "Batman"})
(:Person {name: "Selina Kyle", alias: ["Catwoman", "The Cat"]})
(:Person {name: "Dick Grayson", alias: "Robin"})
```
***
Cypher Shell 1.1.4 부터는 매개변수 문법이 변경되었고 매개변수 출력 리스팅도 약간 변경되었습니다.
```
neo4j> :param thisAlias => 'Robin'
neo4j> :params
:param thisAlias => 'Robin'
```
### 10.7.3. 트랜젝션  
Cypher Shell 명시적인 트랜젝션을 지원합니다. 트랜젝션은 키워드, :begin, :commit, :rollback:으로 제어합니다:
  
예제 10.17. 세밀한 트랜잭션 제어 사용  
***
첫번째 Cypher Shell 세션에서 트랜젝션을 시작합니다:
```
neo4j> MATCH (n) RETURN n;
n
(:Person {name: "Bruce Wayne", alias: "Batman"})
(:Person {name: "Selina Kyle", alias: ["Catwoman", "The Cat"]})
(:Person {name: "Dick Grayson", alias: "Robin"})
neo4j> :begin
neo4j# CREATE (:Person {name : 'Edward Mygma', alias : 'The Riddler' });
Added 1 nodes, Set 2 properties, Added 1 labels
```
이제 두번째 Cypher Shell 세션을 오픈하면,  최근의 CREATE 문의 변화가 없음을 알 수 있습니다:
```
neo4j> MATCH (n) RETURN n;
n
(:Person {name: "Bruce Wayne", alias: "Batman"})
(:Person {name: "Selina Kyle", alias: ["Catwoman", "The Cat"]})
(:Person {name: "Dick Grayson", alias: "Robin"})
```
이제 첫번째 세션으로 가서 해당 트랜젝션을 커밋합니다:
```
neo4j# :commit
neo4j> MATCH (n) RETURN n;
n
(:Person {name: "Bruce Wayne", alias: "Batman"})
(:Person {name: "Selina Kyle", alias: ["Catwoman", "The Cat"]})
(:Person {name: "Dick Grayson", alias: "Robin"})
(:Person {name: "Edward Mygma", alias: "The Riddler"})
neo4j>
```
***
### 10.7.4. 프로시저
Cypher Shell은 현재 사용자에게 권한이 부여된 모든 프로시저 실행을 지원합니다. 여기서는 빌트-인 프로시저 `dbms.showCurrentUser()`를 사용합니다.

예제 10.18. Cypher Shell에서 프로시저 호출하기
```
***
neo4j> CALL dbms.showCurrentUser();
username, roles, flags
"johndoe", ["admin"], []
neo4j> :exit
```
***