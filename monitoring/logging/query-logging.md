
## 8.2.1. 쿼리 로깅(Query Logging)

```
이 섹션에서는 Neo4j가 지원하는 쿼리 로깅에 대해 다룹니다. 
```
 
Neo4j는 데이터 베이스에서 실행되는 로그 쿼리를 기록하도록 설정할 수 있습니다. 

쿼리 로깅은 ```dbms.logs.query.enabled```변수를 ```true```로 설정해서 활성화해야 됩니다. 변수 ```dbms.logs.query.threshold```는 쿼리 로깅을 위해서 임계 값을 결정합니다. 쿼리 실행 시간이 이 임계 값보다 오래걸리면 로깅됩니다. ```dbms.logs.query.threshold```을 ```0```으로 설정하면 모든 쿼리가 로깅될 것 입니다. 


### 8.2.1.1. 로그 환경 설정

로그 파일 이름은 ```query.log```이고, 이것은 *logs* 디렉토리에 있습니다. ([섹션 3.1,"파일 위치"](../../configuration/file-locations.md))를 참조하십시오.

쿼리 로그 회전은 neo4j.conf 설정 파일에서 설정할 수 있습니다. 이용가능한 변수는 다음과 같습니다.:

| 변수 명                                           | 기본 값     | 설명                 |
| ------------------------------------------------- | ----------- | ------------------------------------------------------------ |
| ``` dbms.logs.query.allocation_logging_enabled``` | ```false``` | 실행된 쿼리를 로그하기 위해서 할당된 바이트를 기록합니다.    |
| ``` dbms.logs.query.enabled```                    | ```false``` | 실행된 로그 쿼리는 설정 된 임계 값보다 오래 걸립니다. ``` dbms.logs.query.threshold``` |
| ``` dbms.logs.query.page_logging_enabled```       | ```false``` | 실행 중인 쿼리가 기록되는  로그 페이지의 히트 및 오류를 기록합니다. |
| ``` dbms.logs.query.parameter_logging_enabled```  | ```true```  | 실행된 쿼리 중 설정된 임계 값 보다 오래걸리는 변수를 기록합니다. |
| ``` dbms.logs.query.rotation.keep_number```       | ```7```     | 보관될 로그 파일 개수를 설정합니다.                         |
| ``` dbms.logs.query.rotation.size```              | ```2M```    | 쿼리 로그가 자동-회전할 파일 사이즈를 설정합니다.              |
| ``` dbms.logs.query.threshold```                  | ```0```     | 실행하는 쿼리가 이 임계 값보다 오래 걸릴경우 쿼리는 기록됩니다.(쿼리 로깅이 활성화 되었을 경우) |
| ``` dbms.logs.query.time_logging_enabled```       | ```false``` | 실행하는 쿼리가 기록될 때 상세한 시간 정보를 기록합니다. |

**예 8.1. 간단한 쿼리 로깅을 위한 환경 설정**

이 예에서는 쿼리 로깅을 설정하지만 나머지 쿼리 로그 변수는 기본값으로 남겨둡니다. 

```
dbms.logs.query.enabled=true
```

아래 예는 이 기본 설정을 사용하는 쿼리 로그입니다. 

```
2017-11-22 14:31 ... INFO  9 ms: bolt-session   bolt    johndoe neo4j-javascript/1.4.1      client/127.0.0.1:59167  ...
2017-11-22 14:31 ... INFO  0 ms: bolt-session   bolt    johndoe neo4j-javascript/1.4.1      client/127.0.0.1:59167  ...
2017-11-22 14:32 ... INFO  3 ms: server-session http    127.0.0.1   /db/data/cypher neo4j - CALL dbms.procedures() - {}
2017-11-22 14:32 ... INFO  1 ms: server-session http    127.0.0.1   /db/data/cypher neo4j - CALL dbms.showCurrentUs...
2017-11-22 14:32 ... INFO  0 ms: bolt-session   bolt    johndoe neo4j-javascript/1.4.1      client/127.0.0.1:59167  ...
2017-11-22 14:32 ... INFO  0 ms: bolt-session   bolt    johndoe neo4j-javascript/1.4.1      client/127.0.0.1:59167  ...
2017-11-22 14:32 ... INFO  2 ms: bolt-session   bolt    johndoe neo4j-javascript/1.4.1      client/127.0.0.1:59261  ...
```

**예 8.2. 세부정보가 포함된 쿼리 로깅 구성**

이 예에서는 쿼리 로깅을 설정하고 일부 추가 로깅을 활성화합니다. 

```
dbms.logs.query.parameter_logging_enabled=true
dbms.logs.query.time_logging_enabled=true
dbms.logs.query.allocation_logging_enabled=true
dbms.logs.query.page_logging_enabled=true
```

아래는 쿼리 기록 구성 변수가 활성화된 예 입니다. 

```
2017-11-22 12:38 ... INFO  3 ms: bolt-session   bolt    johndoe neo4j-javascript/1.4.1                         ...
2017-11-22 22:38 ... INFO  61 ms: (planning: 0, cpu: 58, waiting: 0) - 6164496 B - 0 page hits, 1 page faults  ...
2017-11-22 12:38 ... INFO  78 ms: (planning: 40, cpu: 74, waiting: 0) - 6347592 B - 0 page hits, 0 page faults ...
2017-11-22 12:38 ... INFO  44 ms: (planning: 9, cpu: 25, waiting: 0) - 1311384 B - 0 page hits, 0 page faults  ...
2017-11-22 12:38 ... INFO  6 ms: (planning: 2, cpu: 6, waiting: 0) - 420872 B - 0 page hits, 0 page faults -   ...
```

### 8.2.1.2. 쿼리에 메타데이터 첨부 

내장 프로 시저인 ```dbms.setTXMetaData```을 이용해서 프로메타데이터를 쿼리에 첨부하고 쿼리 기록을 출력할 수 있습니다. 일반적으로 프로그램별로 다르지만 ```cypher-shell```을 사용해서 다음과 같이 설명할 수 있습니다. 

**예 8.3. 쿼리에 메타데이터 추가**

트랙잭션을 시작하고 메타 데이터 리스트의 ```dbms.setTXMetaData```을 호출합니다. 

```
neo4j> :begin
neo4j# CALL dbms.setTXMetaData({ User: 'jsmith', AppServer: 'app03.dc01.company.com'});
neo4j# CALL dbms.procedures() YIELD name RETURN COUNT(name);
COUNT(name)
39
neo4j# :commit
```

아래는 쿼리 로그의 해당 결과입니다. 

```
... CALL dbms.setTXMetaData({ User: 'jsmith', AppServer: 'app03.dc01.company.com'}); - {} - {}
... CALL dbms.procedures() YIELD name RETURN COUNT(name); - {} - {User: 'jsmith', AppServer: 'app03.dc01.company.com'}
```

