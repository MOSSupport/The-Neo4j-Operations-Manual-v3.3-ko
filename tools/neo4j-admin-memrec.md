## 10.5. 메모리 권장사항
<div class="abstract">
이 절에서는 neo4j-admin의 memrec 명령을 설명합니다.
</div>
`neo4j-admin`의 `memrec` 명령을 사용하여,  command of to get an initial recommendation on how to configure memory parameters for Neo4j:
```
neo4j-admin memrec [--memory=<Neo4j만 사용하는 메모리>]
```
권장 사항은 neo4j.conf에 바로 복사, 붙여넣기 가느안 포맷으로 제공됩니다.

**옵션**

| 옵션 | 디폴트 | 설명 |
|---|---|---|
| --memory | 16g | Neo4j에 할당될 메모리의 크기. 유효한 단위: k, K, m, M, g, G. |

**고려 사항**

`neo4j-admin memrec` 명령은 Neo4j 메모리 설정의 유효한 시작 포인트를 계산해 줍니다. 유스 케이스마다 권장값 조정이 필요할 수 있습니다. 인덱싱 목적으로 필요한 스토리지가 높으면 운영 체제용으로 더 많은 메모리를 남겨 두는 것이 좋습니다. 반면, 작업 부하에 많은 동시 트랜잭션과 많은 업데이트가 있는 경우의 Neo4j는 더 많은 힙 메모리를 필요로합니다. Neo4j의 메모리 설정에 대한 설명은 9.1 절. [메모리 구성](../performance/memory-configuration.md)을 참조하십시오.
<div class="example">
예제 10.9. `neo4j-admin`의 `memrec` 명령 사용  
<div class="example-contents">
다음 예제는 `neo4j-admin memrec`이 16G 메모리에서 메모리 추천을 제공하는 방법을 보여줍니다:  
<code>
$neo4j-home> bin/neo4j-admin memrec --memory=16g

...
...
...
# Based on the above, the following memory settings are recommended:
dbms.memory.heap.initial_size=5g
dbms.memory.heap.max_size=5g
dbms.memory.pagecache.size=7g
</code></div></div>
<span class="glyphicon glyphicon-info-sign" aria-hidden="true"> </span> 이 기능은 Neo4j 3.3.3에서 처음 소개되었습니다.