
## 8.1. 메트릭스(Metrics)  

```
이 섹션에서는 Neo4j 메트릭스 출력 기능을 이용해 다양한 메트릭스를 로그하고 표시하는 방법에 대해 설명합니다. 
```

이 섹션에서는 다음에 대해서 다룹니다.:
+ [메트릭스 로깅 활성화]("https://neo4j.com/docs/operations-manual/current/monitoring/metrics/#metrics-enable")
+ [사용가능한 메트릭스]("https://neo4j.com/docs/operations-manual/current/monitoring/metrics/reference/")
	+ [범용 메트릭스]("https://neo4j.com/docs/operations-manual/current/monitoring/metrics/reference/#metrics-general-purpose")
	+ [인과 관계 클러스터스터링 메트릭스]("https://neo4j.com/docs/operations-manual/current/monitoring/metrics/reference/#causal-clustering-metrics")

### 8.1.1. 메트릭스 로깅 활성화

Neo4j는 리포트를 보고할 때 두 가지 방법이 있습니다. 

+ 메트릭스를 [Graphite]("https://graphiteapp.org/")나 Graphite 프로토콜 기반의 아무 모니터링 툴에 전송합니다. 
+ 메트릭스를 CSV 파일로 보냅니다.

Neo4j는 다음 데이터 베이스 파트 일부로 보내질 수 있습니다. 
```
# Setting for enabling all supported metrics.
metrics.enabled=true

# Setting for enabling all Neo4j specific metrics.
metrics.neo4j.enabled=true

# Setting for exposing metrics about transactions; number of transactions started, committed, etc.
metrics.neo4j.tx.enabled=true

# Setting for exposing metrics about the Neo4j page cache; page faults, evictions, flushes and exceptions, etc.
metrics.neo4j.pagecache.enabled=true

# Setting for exposing metrics about approximately entities are in the database; nodes, relationships, properties, etc.
metrics.neo4j.counts.enabled=true

# Setting for exposing metrics about the network usage of the HA cluster component.
metrics.neo4j.network.enabled=true
```


###8.1.1.1. Graphite

Graphite와  통합하기 위해서 아래 내용을 neo4j.conf설정에 추가합니다. 

```
# Enable the Graphite integration. Default is 'false'.
metrics.graphite.enabled=true
# The IP and port of the Graphite server on the format <hostname or IP address>:<port number>.
# The default port number for Graphite is 2003.
metrics.graphite.server=localhost:2003
# How often to send data. Default is 3 seconds.
metrics.graphite.interval=3s
# Prefix for Neo4j metrics on Graphite server.
metrics.prefix=Neo4j_1
```

Neo4j 메트릭스를 모니터링 하기 위해서 Neo4j를 시작하고 웹 브라우저를 통해 Graphite에 연결하면 됩니다.   

> Graphite 서버가 호스트 이름 또는 DNS로 구성한다면 JVM이 호스트 이름을 IP주소로 연결하고 기본적으로 결과를 보안상의 이유로 잡습니다. 

Graphite 서버를 호스트 이름 또는 DNS 항목으로 구성하면 JVM이 호스트 이름을 IP 주소로 확인하고 기본적으로 보안상의 이유로 결과를 무기한 캐시한다는 사실을 알아야합니다.

이것은 JVM보안 속성 내에 있는 ```networkaddress.cache.ttl``` 

더 많은 정보는 [https://docs.oracle.com/javase/8/docs/technotes/guides/net/properties.html]("https://docs.oracle.com/javase/8/docs/technotes/guides/net/properties.html")에서 확인 가능합니다. 


####8.1.1.2. CSV 파일

메트릭스를 로컬 .CSV파일에 추가하기 위해서 아래 설정을 neo4j.conf파일에 

```
# Enable the CSV exporter. Default is 'false'.
metrics.csv.enabled=true
# Directory path for output files.
# Default is a "metrics" directory under NEO4J_HOME.
#dbms.directories.metrics='/local/file/system/path'
# How often to store data. Default is 3 seconds.
metrics.csv.interval=3s
```


> CSV 내보내기는 출력 파일을 자동으로 순환시키지 않습니다. CSV 내보내기를 활성화할 때, 주기적으로 파일을 압축하는 것을 권장합니다.
