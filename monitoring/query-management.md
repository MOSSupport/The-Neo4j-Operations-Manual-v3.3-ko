## 8.3. 쿼리 관리

```
이 섹션에서는 Neo4j의 쿼리 실행 관리 방법에 대해 다룹니다.  
```

보안 또는 성능 측면에서 쿼리를 검사해야하는 경우가 있을 수 있습니다. Neo4j에서는 쿼리를 검사하고 관리하는 다양한 방법을 제공합니다.


[쿼리 로그]("https://neo4j.com/docs/operations-manual/current/monitoring/logging/query-logging/")는 지속적인 모니터링과 트러블슈팅에 사용됩니다. [트랜잭션 타임아웃]("https://neo4j.com/docs/operations-manual/current/monitoring/query-management/#transaction-timeout")은 운영자가 쿼리의 최대 실행 시간을 조정하는 안전 장치 입니다. [쿼리 운영 절차]("https://neo4j.com/docs/operations-manual/current/monitoring/query-management/procedures/")는 데이터베이스에서 실행되는 동안 쿼리를 검사하고 중지시킬 수 있습니다. 

이 섹션에서는 다음에 대해 다룹니다.:

+ [트랜잭션 시간 초과]("https://neo4j.com/docs/operations-manual/current/monitoring/query-management/#transaction-timeout")
+ [쿼리 관리 절차]("https://neo4j.com/docs/operations-manual/current/monitoring/query-management/procedures/")
	+ [전문 용어]("https://neo4j.com/docs/operations-manual/current/monitoring/query-management/procedures/#query-management-terminology")
	+ [운영되는 모든 쿼리 리스트]("https://neo4j.com/docs/operations-manual/current/monitoring/query-management/procedures/#query-management-list-queries")
	+ [다수의 쿼리 종료]("https://neo4j.com/docs/operations-manual/current/monitoring/query-management/procedures/#query-management-terminate-multiple-queries")
	+ [단일 쿼리 종료]("https://neo4j.com/docs/operations-manual/current/monitoring/query-management/procedures/#query-management-terminate-single-query")


###8.3.1 트랜잭션 시간 초과


*실행 가드*는 실행 시간이 설정된 제한 시간을 초과한 트랜잭션을 종료하는 기능입니다. 

*실행 가드*를 활성화하려면 ```dbms.transaction.timeout```을 양의 시간 간격 값(기본 트랜잭션 시간 초과를 나타내는)으로 설정하면 됩니다. 

**예 8.4.실행 가드 환경설정**

10초로 시간을 제한 합니다. 
```
dbms.transaction.timeout=10s
```

```dbms.transaction.timeout```을 기본 값 ```0```로 맞춰서 실행 가드를 비활성화 합니다. 

이 특징은 사용자 정의 시간 초과가  ```dbms.transaction.timeout```에 설정된 값을 (Java API를 통해) 오버라이드하기 때문에 사용자 정의 시간 초과로 실행되는 트랜잭션에 영향을 미치지 않습니다.  
