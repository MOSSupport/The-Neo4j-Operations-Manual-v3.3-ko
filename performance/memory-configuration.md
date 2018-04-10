## 9.1. 메모리 설정
> 이 절에서는 Neo4j 인스턴스를 위한 메모리 설정 방법을 설명합니다.  

<span class="glyphicon glyphicon-info-sign" aria-hidden="true"> </span> 이 절에서는 Neo4j의 메모리 설정 값들에 대해 살펴 보겠습니다. 다른 조건인 경우, 이 설정 값들이 달라져야 합니다. 데이터-셋의 크기와 인덱스의 존재는 메모리 사용에 영향을 미칩니다. 여기의 예제는 설명을 위한 것입니다. Neo4j 설치시, 조건에 따라 메모리 설정 숫자 값은 달라져야만 합니다.  

그림 9-1 Neo4j 메모리 관리와 같이, Neo4j 서버의 RAM에는 세가지 영역- OS 예약, 페이지 캐시, 힙(Heap) 공간의 세 개의 사용 영역이 있습니다:  
##### 그림 9.1. Neo4j 메모리 관리
![](./9_1.png)

메모리 설정은 보통 다음의 단계를 따릅니다:

1. OS 예약:  
   * [OS 메모리](#os-메모리) — 운영 체제(OS)에 필요한 리소스를 미리 예측하여 예약.
   * [루씬(Lucene) 인덱스 캐시](#루씬-인덱스-캐시) — 루씬(Lucene) 인덱스에 필요한 리소스를 미리 예측하여 예약.
2. [페이지 캐시](#페이지-캐시) — 페이지 캐시의 크기 결정.
3. [힙(Heap) 크기](#힙-크기) — 힙(Heap)의 크기 결정.
4. [메모리 설정 확인](#메모리-설정-확인) — 이용 가능한 RAM이 적절히 설정되었는지 확인합니다.   

각각의 단계에 대한 자세한 설명은 아래에서 계속 이어집니다.

여기서는 서버에서 Neo4j만 실행한다고 가정합니다. 만약 동일 서버에서 다른 서비스도 실행 중이라면, 해당 메모리 요구 사항도 고려해야 합니다.

### OS 메모리
운영 체제(OS)를 실행하기 위해 일부 메모리를 예약합니다. 운영 체제(OS) 용으로 RAM의 크기를 명시적으로 설정할 수 없으므로, 페이지 캐시와 힙 공간을 설정하면, 나머지 RAM을 OS에서 사용합니다. OS용으로 충분한 메모리를 남겨 두지 않으면, Neo4j 실행시 디스크로 자주 스왑되어 성능이 크게 저하됩니다.

Neo4j만 실행되는 서버라면 1GB는 적절한 값입니다.

예제 9.1. OS 메모리 결정
```
OS용으로 예약할 메모리 크기를 정합니다. 이 값은 이 절의 마지막 예제에서 사용됩니다.
OS 예약으로 사용할 메모리의 크기 = 1GB.
```
만약, 실제 서버의 RAM이 크다면, OS용으로 1GB 보다 더 큰 값을 예약합니다.

### 루씬 인덱스 캐시
Neo4j는 인덱싱 기능 중 일부에 [아파치 루씬(Apache Lucene)](https://lucene.apache.org/)을 사용합니다. 인덱스에 적절한 메모리를 할당하여, 인덱스 룩업 성능을 최적화합니다. OS 메모리와 같이, 루씬(Lucene) 인덱스 캐시는 명시적으로 설정할 수 없습니다. 대신 필요한 메모리를 예측하여, 페이지 캐시와 힙(Heap) 공간을 설정하기 전에 필요한 메모리 양을 예약합니다.  

다음 패턴과 같은 모든 파일의 크기를 합산하여, 페이지 캐시에 필요한 총 메모리를 결정합니다:
* NEO4J_HOME/data/databases/<데이터베이스 명>/index
* NEO4J_HOME/data/databases/<database-name>/schema/index/\*/\*/lucene\*

예제 9.2. 루씬 인덱스에 예약할 메모리 측정
```
데이터베이스 명은 graph.db로 가정합니다. 인덱스 크기는 인덱스 크기에 schema/index/lucene 디렉토리의 크기를 합산합니다.  
$neo4j-home> ls data/databases/graph.db/index | du -ch | tail -1
500M    total
$neo4j-home> find data/databases/graph.db/schema/index -regex '.*/lucene.*' | du -hc | tail -1
500M    total
인덱스를 위해 예약될 메모리 용량 = 500MB + 500MB = 1GB.
```
### 페이지 캐시
페이지 캐시는 디스크에 저장되는 Neo4j 데이터의 캐시에 사용됩니다. 디스크의 모든 그래프 데이터, 또는 적어도 대부분의 그래프 데이터가 메모리에 캐시되면, 값비싼 디스크 액세스를 하지 않아, 최적의 성능을 얻을 수 있습니다.  

또한 Neo4j의 네이티브 인덱스는 페이지 캐시에 캐시됩니다. 앞서 언급했듯이 인덱스가 캐시되면 성능이 좋아집니다.  

Neo4j가 페이지 캐시에 사용할 수 있는 메모리 양을 지정하는 설정 변수는 `dbms.memory.pagecache.size` 입니다:

다음은 프로덕션 환경에서 이미 실행 중인 경우와 향후 배포를 계획 중인지에 따른 페이지 캐시를 계산하는 두 가지 방법입니다.

1. 이미 사용 중인 프로덕션 시스템인 경우, 파일 크기(아래 설명 참고)를 합한 다음, 향후 DB 크기 증가를 고려한 값을 곱합니다. 예를 들어, 20% 크기가 증가한다면, 1.2를 곱합니다.
2. 새로운 Neo4j 데이터베이스의 경우, 데이터의 일부만 가져 오기(import)하여, 임포트 크기의 전체에서의 비율과 향후 DB 크기 증가 비율을 곱하면 유용합니다. 예를 들어, DB 크기 증가율 20%를 포함한 전체 데이터베이스 크기를 구하는 경우, 데이터의 1/100을 가져 왔다면, 전체 데이터베이스 파일의 크기를 대략적으로 구할 수 있습니다(아래 설명 참고). 20%의 증가를 허용하려면, 그 합계에 120(120=1/100x100x1.20)을 곱합니다.

다음 패턴과 같은 모든 파일의 크기를 합산하여, 페이지 캐시에 필요한 총 메모리를 결정합니다::

* NEO4J_HOME/data/databases/<database-name>/\*store.db\*
* NEO4J_HOME/data/databases/<database-name>/schema/index/\*/\*/native\*

사용 중인 프로덕션 데이터베이스와 새로운 데이터베이스의 최적 페이지 캐시 크기를 결정하고 설정하는 방법의 예제는 아래를 참고합니다.

예제 9.3. 사용 중인 Neo4j 데이터베이스의 페이지 캐시 예측
```
이 예제의 데이터베이스 명은 표준 이름인 graph.db라고 가정합니다.
데이터베이스 파일의 전체 크기를 예측하려고 합니다. Posix 시스템에서 다음 명령을 실행합니다:
$neo4j-home> ls data/databases/graph.db/*store.db* | du -ch | tail -1
33G total
$neo4j-home> find data/databases/graph.db/schema/index -regex '.*/native.*' | du -hc | tail -1
2G  total
두 용량의 합은 33G + 2G = 35G 입니다. 이 예제에서는 데이터베이스가 향후 20% 증가할 것을 예상하여 여유 공간을 둡니다. 결과에 다음과 같이 20% 증가분을 포함시킵니다:

dbms.memory.pagecache.size = 1.2 * (35GB) =  42GB

We configure the page cache by adding the following to neo4j.conf 파일에 다음을 추가하여 페이지 캐시를 설정합니다:

dbms.memory.pagecache.size=42GB
```
예제 9.4. 새로운 Neo4j 데이터베이스 페이지 캐시 예측
```
이 예제의  데이터베이스 명은 표준 이름인 graph.db라고 가정합니다.
새로운 Neo4j 데이터베이스database, it is useful to run an import with a fraction (예를 들면 1/100 ) of the data and then multiply the resulting store-size by that fraction (x 100). It is important to remember to allow for future growth of the database. In this specific use case we estimate that 20% will provide sufficient head room for growth.

Assume that we have already imported 1/100th of the data into a test database. The next step is to figure out the resulting database size. On a posix system we would run the following commands:

$neo4j-home> ls data/databases/graph.db/*store.db* | du -ch | tail -1
330M total
$neo4j-home> find data/databases/graph.db/schema/index -regex '.*/native.*' | du -hc | tail -1
20M total
The sum of the above is: 330M + 20M = 350M. Then we size up the result and additionally reserve 20% for growth:

dbms.memory.pagecache.size = 120 * (350 MB) =  42GB

We configure the page cache by adding the following to neo4j.conf:

dbms.memory.pagecache.size=42G
```
### 힙 크기
힙(Heap) 공간은 쿼리 실행, 트랜젝션 상태, 그래프 관리 등에 사용됩니다. The size needed for the heap is very dependent on the nature of the usage of Neo4j. For example, long-running queries, or queries that are doing cartesian products between large sets of data, may require a larger heap than simpler queries. In case of performance issues we may have to tune our queries and monitor their memory usage in order to determine whether the heap needs to be increased.

The size of the available heap memory is an important aspect for the performance of Neo4j. Generally speaking, we want to configure a large enough heap to sustain concurrent operations. If the heap is sized significantly too small, this may lead to bad behavior.

The heap memory size is determined by the parameters dbms.memory.heap.initial_size and dbms.memory.heap.max_size. It is recommended to set these two parameters to the same value to avoid unwanted full garbage collection pauses.

Example 9.5. Configure the heap size
For many setups, a heap size between 8GB and 16GB is large enough to run Neo4j reliably.

For this example, we will set the heap size to 16 gigabytes. We will set the initial size and max size to the same value to avoid unwanted garbage collections.

dbms.memory.heap.initial_size=16G
dbms.memory.heap.max_size=16G
### 메모리 설정 확인
After configuring the parameters for the page cache and the heap as described in the sections above, we should do a control check that there is actually enough memory left for the operating system and index caching. Do the following sanity check:

Actual memory left for the OS = RAM available on the machine - index cache size - page cache - heap size.

Example 9.6. Verify memory configuration
For the sake of this example, let’s say that our machine has 64GB of RAM. We assume that we have configured our memory settings according to the previous examples in this section.

We have:

RAM available on the machine = 64GB
Minimum amount of memory we wish to reserve for the OS = 1GB
Lucene index cache size = 1GB
Page cache = 42GB
Heap size = 16GB
We now do the check:

Actual memory left for the OS = 64GB - 1 GB - 16GB - 42GB = 4GB > 1GB.

The above calculation shows that we indeed do have enough memory left for the operating system, and therefore avoid the risk of swapping.

It is always recommended to define the page cache and heap size parameters explicitly in neo4j.conf in order to have good control over your system’s behavior. If these parameters are not explicitly defined, some heuristic settings will be computed at startup based on available system resources.
