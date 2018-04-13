##3.7. 다이나믹 설정

> 이 섹션에서는 Neo4j이 작동하는 동안, Neo4j설정을 변경하는 법과 변경가능한 설정에 대해 다룹니다.


Neo4j 기업용 배포판은 서비스 재시작 없이 일부 설정을 변경할 수 있습니다. 설정은 ```dbms.setConfigValue()```절차를 사용해서 변경됩니다. ```dbms.setConfigValue()```을 작동하려면 [administrator]("https://neo4j.com/docs/operations-manual/current/security/authentication-authorization/terminology/#term-administrator") 특권이 필요합니다. 


<span class="glyphicon glyphicon-info-sign" aria-hidden="true"></span> > 운영 시간동안 변경된 설정은 유지되지 않습니다. Neo4j 재시작할 때, 변경 사항된 사항이 누락되지 않으려면 [Neo4j 설정 파일]("https://neo4j.com/docs/operations-manual/current/configuration/file-locations/#file-locations-configuration")을 함께 업데이트 해야 됩니다.


##3.7.1. 다이나믹 설정 절차

#문법:

```CALL dbms.setConfigValue(setting, value)```

#반환 값:

성공 값 반환 안함.

#예외 사항:

```
알려지지 않았거나 옳바르지 않은 설정 이름
```

```
설정이 동적이 아니고 작동 시 변경될 수 없는 것
```

```
옳바르지 않은 설정 변수
```

#아래는 쿼리 로깅을 다이나믹하게 설정하는 예 입니다.

예 3.5. 환경 변수 설정

```CALL dbms.setConfigValue('dbms.logs.query.enabled', 'true')```

#옳지 않은 변수가 있다면, 에러 메시지를 띄울 것 입니다. 

예 3.6. 잘못된 구성값 설정

```CALL dbms.setConfigValue('dbms.logs.query.enabled', 'yes')```

```
Failed to invoke procedure `dbms.setConfigValue`: Caused by: org.neo4j.graphdb.config.InvalidSettingException: Bad value 'yes' for setting 'dbms.logs.query.enabled': must be 'true' or 'false'
```

#환경 변수 빈 스트링 값에 값 변수를 넣으면 기본으로 설정됩니다. 

예 3.7. 구성값 기본 설정
```
CALL dbms.setConfigValue('dbms.logs.query.enabled', '')
```


##3.7.2. 다이나믹 설정 참조

표 3.5. 다이나믹 설정 참조

```
이름	설명
dbms.checkpoint.iops.limit

Limit the number of IOs the background checkpoint process will consume per second.

dbms.logs.query.enabled

Log executed queries that take longer than the configured threshold, dbms.logs.query.threshold.

dbms.logs.query.rotation.keep_number

Maximum number of history files for the query log.

dbms.logs.query.rotation.size

The file size in bytes at which the query log will auto-rotate.

dbms.logs.query.threshold

If the execution of query takes more time than this threshold, the query is logged - provided query logging is enabled.

dbms.transaction.timeout

The maximum time interval of a transaction within which it should be completed.
```


