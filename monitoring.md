# 8. 모니터링

<div class="abstract">
	<p>이번 장은 Neo4j의 모니터링에 대해서 설명합니다.
	</p>
</div>

Neo4j는 현재 실행중인 쿼리의 검사 및 관리는 물론 메트릭 출력을 통해 지속적인 분석 메커니즘을 제공합니다.

로그는 지속적인 분석 또는 특정 조사를 위해 수집할 수 있습니다. 보안 이벤트 로그와 쿼리 로그를 생성하는 데 사용할 수 있는 기능이 있습니다. 쿼리 관리 기능은 쿼리 성능에 대한 특정 조사를 위해 제공됩니다. 원인 클러스터의 임시 분석을 위해 모니터링 기능도 제공됩니다.

이 장에서는 다음 사항에 대해 설명합니다.

- [메트릭스(Metrics)](/monitoring/metrics.md)
  - [메트릭스 로깅 활성화](/monitoring/metrics.md/#811-메트릭스-로깅-활성화)
  - [매트릭스 참조](/monitoring/metrics/reference.md)
- [로깅(Logging)](/monitoring/logging.md)
  - [보안 이벤트 로깅(Logging)](/monitoring/logging/security-events-logging.md)
  - [쿼리 로깅(Query Logging)](/monitoring/logging/query-logging.md)
- [쿼리 관리](/monitoring/query-management.md)
  - [쿼리 관리 절차](/monitoring/query-management/procedures.md)
  - [트랜잭션 시간 초과](/monitoring/query-management.md/#831-트랜잭션-시간-초과)
- [인과 관계 클러스터 모니터링](/monitoring/causal-cluster.md)