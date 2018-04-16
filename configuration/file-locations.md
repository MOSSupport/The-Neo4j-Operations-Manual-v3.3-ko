# 3.1 파일 위치

```
이 섹션에서는 서로 다른 Neo4j 배포판 파일이 저장된 위치와 Neo4j 실행에 필요한 파일 권한을 확인합니다. 
```

중요한 파일은 기본적으로 다음 위치에서 확인할 수 있습니다. 

 
 | 패키지                   | 설정                                           | 데이터                              | 로그                                     | 메트릭스                                    | 임포팅                                     | 빈(Bin)                           | 립(Lib)                | 플러그인                              |
| :----------------------- | :--------------------------------------------- | :---------------------------------- | :--------------------------------------- | :------------------------------------------ | :----------------------------------------- | :-------------------------------- | :--------------------- | :------------------------------------ |
| 리눅스 또는 OS X tarbell | *<neo4j-home>/conf/neo4j.conf*                 | *<neo4j-home>/data*                 | *<neo4j-home>/logs*                      | *<neo4j-home>/metrics*                      | *<neo4j-home>/import*                      | *<neo4j-home>/bin*                | *<neo4j-home>/lib*     | *<neo4j-home>/plugins*                |
| 윈도우 zip               | *<neo4j-home>\conf\neo4j.conf*                 | *<neo4j-home>\data*                 | *<neo4j-home>\logs*                      | *<neo4j-home>\metrics*                      | *<neo4j-home>\import*                      | *<neo4j-home>\bin*                | *<neo4j-home>/lib*     | *<neo4j-home>/plugins*                |
| 데비안                   | */etc/neo4j/neo4j.conf*                        | */var/lib/neo4j/data*               | */var/log/neo4j*                         | */var/lib/neo4j/metrics*                    | */var/lib/neo4j/import*                    | */usr/bin*                        | */usr/share/neo4j/lib* | */var/lib/neo4j/plugins*              |
| RPM                      | */etc/neo4j/neo4j.conf*                        | */var/lib/neo4j/data*               | */var/log/neo4j*                         | */var/lib/neo4j/metrics*                    | */var/lib/neo4j/import*                    | */usr/bin*                        | */usr/share/neo4j/lib* | */var/lib/neo4j/plugins*              |
| 윈도우 데스크톱          | *%APPDATA%\Neo4j Community Edition\neo4j.conf* | *%APPDATA%\Neo4j Community Edition* | *%APPDATA%\Neo4j Community Edition\logs* | *%APPDATA%\Neo4j Community Edition\metrics* | *%APPDATA%\Neo4j Community Edition\import* | *%ProgramFiles%\Neo4j CE 3.3\bin* | 패키지 내부            | *%ProgramFiles%\Neo4j CE 3.3\plugins* |
| OS X 데스크톱            | *${HOME}/Documents/Neo4j/neo4j.conf*           | *${HOME}/Documents/Neo4j*           | *${HOME}/Documents/Neo4j/logs*           | *${HOME}/Documents/Neo4j/metrics*           | *${HOME}/Documents/Neo4j/import*           | 패키지 내부                       | 패키지 내부            | 패키지 내부                           |



데이터 위치는 Neo4j에 내부에 있으며 이 구조는 통보없이 다른 버전으로 변경될 수도 있습니다. 


##3.1.1. 로그 파일


| 파일 이름           | 설명                                                         |
| ------------------- | ------------------------------------------------------------ |
| *neo4j.log*         | 일반적인 Neo4j가 쓰여진 위치의 기본 로그. Debian과 RPM 패키지에 적혀지지 않는다. |
| *debug.log*         | Neo4 디버깅 문제를 다룰 때 유용한 정보                       |
| *http.log*          | HTTP API에 대한 로그 요청                                    |
| *gc.log*            | JVM이 제공하는 가비지 컬렉션                                 |
| *query.log*         | 지정된 임계 값보다 오래 걸리는 쿼리(기업용만 가능)           |
| *security.log*      | 보안 이벤트 로그(기업용만 가능)                              |
| *service-error.log* | 윈도우 서비스를 설치하거나 실행할 때 발생하는 로그 에러(윈도우만 가능) |



##3.1.2. 환경 설정

일부 경로는 ```dbms.directories.*``` 설정에서 변경합니다.: 자세한 정보는 [섹션 A.1, "환경 설정 세팅"]("https://neo4j.com/docs/operations-manual/current/reference/configuration-settings/")를 참조하세요.

<neo4j-home>의 위치와 conf는 다양한 환경 변수로 설정될 수 있습니다. 


| 위치           | 기본값              | 환경 변수         | 요점                                                       |
| -------------- | ------------------- | ----------------- | ---------------------------------------------------------- |
| *<neo4j-home>* | bin의 조상          | ``` NEO4J_HOME``` | bin이 서브경로가 아니면 반드시 명시해야합니다.             |
| *conf*         | *<neo4j-home>/conf* | ``` NEO4J_CONF``` | *<neo4j-home>*의 서브경로가 아니면 반드시 명시해야 합니다. |


##3.1.3. 권한
Neo4j를 사용하는 유저는 다음 권한이 필요합니다:

**읽기 권한**
+ conf
+ import
+ bin
+ lib
+ plugins


**읽기/쓰기 권한**
+ data
+ logs
+ metrics


**실행 권한**
+ bin의 모든 파일 
