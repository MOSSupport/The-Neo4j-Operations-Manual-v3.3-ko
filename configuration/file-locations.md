##3.1 파일 위치

> 이 섹션에서는 서로 다른 Neo4j 배포판 파일이 저장된 위치와 Neo4j 실행에 필요한 파일 권한을 확인합니다. 

중요한 파일은 기본적으로 다음 위치에서 확인할 수 있습니다. 


 
Package	환경 설정	 데이터 	로그 	메트릭스	Import	Bin	Lib	Plugins

 
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

```
Filename	Description
neo4j.log

The standard log, where general information about Neo4j is written. Not written for Debian and RPM packages. See relevant sections.

debug.log

Information useful when debugging problems with Neo4j.

http.log

Request log for the HTTP API.

gc.log

Garbage Collection logging provided by the JVM.

query.log

Log of executed queries that takes longer than a specified threshold. (Enterprise Edition only.)

security.log

Log of security events. (Enterprise Edition only.)

service-error.log

Log of errors encountered when installing or running the Windows service. (Windows only.)
```


##3.1.2. 환경 설정

일부 경로는 ```dbms.directories.*``` 설정에서 변경합니다.: 자세한 정보는 [섹션 A.1, "환경 설정 세팅"]("https://neo4j.com/docs/operations-manual/current/reference/configuration-settings/")를 참조하세요.

<neo4j-home>의 위치와 conf는 다양한 환경 변수로 설정될 수 있습니다. 


```

Location	Default	Environment variable	Notes
<neo4j-home>

parent of bin

NEO4J_HOME

Must be set explicitly if bin is not a subdirectory.

conf

<neo4j-home>/conf

NEO4J_CONF

Must be set explicitly if it is not a subdirectory of <neo4j-home>.

```


##3.1.3. 권한
Neo4j를 사용하는 유저는 다음 권한이 필요합니다.:

읽기 권한

+ conf
+ import
+ bin
+ lib
+ plugins

읽기/쓰기 권한

+ data
+ logs
+ metrics

실행 권한
+ bin의 모든 파일 
