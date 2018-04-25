## 8.3. 쿼리 관리

```
이 섹션에서는 Neo4j의 쿼리 실행을 관리하는 방법에 대해 다룹니다.  

```

보안 또는 성능 측면에서 쿼리를 검사해야하는 경우가있을 수 있습니다. Neo4j는 쿼리를 검사하고 관리하는 다양한 방법을 제공합니다.


보안이나 수행하는 관점에서 쿼리를 발견하고, 

+ [트랜잭션 시간 초과]("https://neo4j.com/docs/operations-manual/current/monitoring/query-management/#transaction-timeout")
+ [쿼리 관리 절차]("https://neo4j.com/docs/operations-manual/current/monitoring/query-management/procedures/")
	+ [전문 용어]("https://neo4j.com/docs/operations-manual/current/monitoring/query-management/procedures/#query-management-terminology")
	+ [운영되는 모든 쿼리 리스트]("https://neo4j.com/docs/operations-manual/current/monitoring/query-management/procedures/#query-management-list-queries")
	+ [다수의 쿼리 종료]("https://neo4j.com/docs/operations-manual/current/monitoring/query-management/procedures/#query-management-terminate-multiple-queries")
	+ [단일 쿼리 종료]("https://neo4j.com/docs/operations-manual/current/monitoring/query-management/procedures/#query-management-terminate-single-query")


###8.3.1 트랜잭션 시간 초과


**예 8.4.실행 같이 설정**


```dbms.transaction.timeout```을 ```0```로 맞춥니다. - 기본 값, 가드(guard) 