# 8. 모니터링

<div class="abstract">
	<p>이번 장은 Neo4j의 모니터링에 대해서 설명합니다.
	</p>
</div>

Neo4j provides mechanisms for continuous analysis through the output of metrics as well as the inspection and management of currently-executing queries.

Logs can be harvested for continuous analysis, or for specific investigations. Facilities are available for producing security event logs as well as query logs. The query management functionality is provided for specific investigations into query performance. Monitoring features are also provided for ad-hoc analysis of a Causal Cluster.

This chapter describes the following:

- [Metrics](/monitoring/metrics.md)
  - [Enable metrics logging](/monitoring/metrics.md/#metrics-enable)
  - [Metrics reference](/monitoring/metrics/reference.md)
- [Logging](/monitoring/logging.md)
  - [Security events logging](/monitoring/logging/security-events-logging.md)
  - [Query logging](/monitoring/logging/query-logging.md)
- [Query management](/monitoring/query-management.md)
  - [Query management procedures](/monitoring/query-management/procedures.md)
  - [Transaction timeout](/monitoring/query-management.md/#transaction-timeout)
- [Monitoring of a Causal Cluster](/monitoring/causal-cluster.md)