## 10.5. 메모리 추천
> 이 절에서는 neo4j-admin의 memrec 명령을 설명합니다.

`neo4j-admin`의 `memrec` 명령을 사용하여,  command of to get an initial recommendation on how to configure memory parameters for Neo4j:
```
neo4j-admin memrec [--memory=<Neo4j만 사용하는 메모리>]
```
The recommendations will be given in a format such that it can be copied and pasted straight into neo4j.conf.

**옵션**

Option	Default	Description
--memory 16g The amount of memory to allocate to Neo4j. Valid units are: k, K, m, M, g, G.

**고려 사항**

The neo4j-admin memrec command calculates a valid starting point for Neo4j memory settings, based on the provided memory. The specific conditions for your use case may warrant adjustment of these values. If the storage required for indexing purposes is high, then it might be advantageous to leave more memory for the operating system. On the other hand, if your workload has many concurrent transactions and many updates, Neo4j will need more heap memory. See also Section 9.1, “Memory configuration” for a description of the memory settings in Neo4j.

예제 10.9. `neo4j-admin`의 `memrec` 명령 사용  
다음 예제는 `neo4j-admin memrec`이 16G 메모리에서 메모리 추천을 제공하는 방법을 보여줍니다:  
```
$neo4j-home> bin/neo4j-admin memrec --memory=16g

...
...
...
# Based on the above, the following memory settings are recommended:
dbms.memory.heap.initial_size=5g
dbms.memory.heap.max_size=5g
dbms.memory.pagecache.size=7g
```
<span class="glyphicon glyphicon-info-sign" aria-hidden="true"> </span> 이 기능은 Neo4j 3.3.3에서 처음 소개되었습니다.