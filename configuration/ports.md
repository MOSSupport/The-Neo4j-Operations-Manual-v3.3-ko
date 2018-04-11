
## 3.2. 포트

>  Neo4j 설치 파일과 관련있는 포트에 대해 알아봅니다. 

이 섹션에서는 방화벽에서 어떤 Neo4j의 특정 포트가 개방되어야 하는지 결정하는 전체적인 개요를 제공합니다. 이러한 포트는 일반적인 네트워크 작동에 필요한 포트에 추가됩니다. 방화벽은 특정 조건을 기반으로 수행해야하므로 포트 개방과 관련하여 특정 권장 사항을 만들 수 없습니다. 


```
Name	Default port number	Related settings	Comments
Backups

6362-6372

dbms.backup.enabled=true dbms.backup.address=127.0.0.1:6362-6372

Backups are enabled by default. In production environments, external access to this port should be blocked by a firewall. See also Chapter 6, Backup.

HTTP

7474

See Section 3.6, “Configure Neo4j connectors”.

It is recommended to not open up this port for external access in production environments, since traffic is unencrypted. Used by the Neo4j Browser. Also used by REST API.

HTTPS

7473

See Section 3.6, “Configure Neo4j connectors”.

Also used by REST API.

Bolt

7687

See Section 3.6, “Configure Neo4j connectors”.

Used by Cypher Shell and by the Neo4j Browser.

Causal Cluster

5000, 6000, 7000

causal_clustering.discovery_listen_address=:5000 causal_clustering.transaction_listen_address=:6000 causal_clustering.raft_listen_address=:7000

The listed ports are the default ports in neo4j.conf. The ports are likely be different in a production installation; therefore the potential opening of ports must be modified accordingly. See also Section 4.2.12, “Causal Clustering settings reference”.

HA Cluster

5001, 6001

ha.host.coordination=127.0.0.1:5001 ha.host.data=127.0.0.1:6001

The listed ports are the default ports in neo4j.conf. The ports will most likely be different in a production installation; therefore the potential opening of ports must be modified accordingly. See also Section 4.3, “Highly Available cluster”.

Graphite monitoring

2003

metrics.graphite.server=localhost:2003

This is an outbound connection in order for the Neo4j database to communicate with the Graphite server. See also Section 8.1, “Metrics”.

JMX monitoring

3637

dbms.jvm.additional=-Dcom.sun.management.jmxremote.port=3637

This setting is for exposing the JMX. We are not promoting this way of inspecting the database. It is not enabled by default.

Neo4j-shell

1337

dbms.shell.port=1337

The neo4j-shell tool is being deprecated and it is recommended to discontinue its use. Supported tools that replace the functionality neo4j-shell are described under Chapter 10, Tools.
```



