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
2. [페이지 캐시](#page-cache) — 페이지 캐시의 크기 결정.
3. [힙(Heap) 크기](#heap-size) — 힙(Heap)의 크기 결정.
4. 메모리 설정 확인 - 이용 가능한 RAM이 적절히 설정되었는지 확인합니다.   

각각의 단계에 대한 자세한 설명은 아래에서 계속 이어집니다.  
여기서는 서버에서 Neo4j만 실행한다고 가정합니다. 만약 동일 서버에서 다른 서비스도 실행 중이라면, 해당 메모리 요구 사항도 고려해야 합니다.

### OS 메모리
일부 메모리는 운영 체제(OS)를 실행하기 위해 예약되어야 합니다. 운영 체제(OS) 용으로 RAM의 크기를 명시적으로 설정할 수 없으므로 페이지 캐시와 힙 공간을 설정하면, 나머지 RAM을 OS에서 사용합니다. 그러나 OS용으로 충분한 공간을 두지 않으면, 디스크로 자주 스왑되어 성능이 크게 저하됩니다.

Neo4j만 실행되는 서버라면 1GB는 적절한 값입니다.

예제 9.1. OS 메모리 결정
```
OS용으로 예약할 메모리 크기를 정합니다. 이 값은 이 절의 마지막 예제에서 사용됩니다.
OS 예약으로 사용할 메모리의 크기 = 1GB.
```
혹은, 실제 서버의 RAM이 크다면, OS용으로 1GB 보다 더 큰 값을 예약합니다.

### 루씬 인덱스 캐시
Neo4j uses Apache Lucene for some of its indexing functionality. We should optimize index lookup performance by ensuring that the indexes fit into memory. Similar to the OS memory, the Lucene index cache can not be explicitly configured. Instead we estimate the memory needed, and add this number to the amount of memory reserved before configuring page cache and heap space.

We determine the total memory needed for the page cache by summing the sizes of all files with the following name patterns:

NEO4J_HOME/data/databases/<database-name>/index
NEO4J_HOME/data/databases/<database-name>/schema/index/*/*/lucene*
Example 9.2. Estimate resources to reserve for Lucene indexes
Assume that our database is named graph.db. We determine the index sizes by adding up the size of the index and schema/index/lucene directories, respectively.

$neo4j-home> ls data/databases/graph.db/index | du -ch | tail -1
500M    total
$neo4j-home> find data/databases/graph.db/schema/index -regex '.*/lucene.*' | du -hc | tail -1
500M    total
Amount of memory to reserve for indexes = 500MB + 500MB = 1GB.

### Page cache
The page cache is used to cache the Neo4j data as stored on disk. Ensuring that all, or at least most, of the graph data from disk is cached into memory will help avoid costly disk access and result in optimal performance.

Additionally, Neo4j’s native indexes will be cached in the page cache. As mentioned before, it is beneficial for performance to ensure that indexes are cached.

The parameter for specifying how much memory Neo4j is allowed to use for the page cache is: dbms.memory.pagecache.size.

The following are two methods for estimating the page cache size, depending on whether you are already running in production or planning for a future deployment:

For an existing production system, add up the file sizes (as described below), then multiply with some factor to allow for growth. For example, multiply by 1.2 to allow for 20% growth.
For a new Neo4j database it is useful to run an import with a fraction of the data, and then multiply the resulting store size by that fraction plus some percentage for growth. For example, for a total estimate of a database size, including 20% for growth, you could import 1/100th of the data and sum up the sizes of the resulting database files (as described below). You would then multiply that sum by 120 to allow for the 20% growth.
We determine the total memory needed for the page cache by summing up the sizes of all files with the following name patterns:

NEO4J_HOME/data/databases/<database-name>/*store.db*
NEO4J_HOME/data/databases/<database-name>/schema/index/*/*/native*
See below for examples on how to determine and configure the optimal page cache size for an existing and for a new database, respectively.

Example 9.3. Estimate the page cache for an existing Neo4j database
In this example we assume that the name of the database is the standard name graph.db.

We wish to estimate the total size of the database files. On a posix system we would run the following commands:

$neo4j-home> ls data/databases/graph.db/*store.db* | du -ch | tail -1
33G total
$neo4j-home> find data/databases/graph.db/schema/index -regex '.*/native.*' | du -hc | tail -1
2G  total
The sum of the above is: 33G + 2G = 35G. In our specific use case we estimate that 20% will provide sufficient head room for growth. We therefore add up the result of the commands above, and add 20% for growth:

dbms.memory.pagecache.size = 1.2 * (35GB) =  42GB

We configure the page cache by adding the following to neo4j.conf:

dbms.memory.pagecache.size=42GB
Example 9.4. Estimate the page cache for a new Neo4j database
In this example we assume that the database name is the standard graph.db.

For a new Neo4j database, it is useful to run an import with a fraction (e.g. 1/100th) of the data and then multiply the resulting store-size by that fraction (x 100). It is important to remember to allow for future growth of the database. In this specific use case we estimate that 20% will provide sufficient head room for growth.

Assume that we have already imported 1/100th of the data into a test database. The next step is to figure out the resulting database size. On a posix system we would run the following commands:

$neo4j-home> ls data/databases/graph.db/*store.db* | du -ch | tail -1
330M total
$neo4j-home> find data/databases/graph.db/schema/index -regex '.*/native.*' | du -hc | tail -1
20M total
The sum of the above is: 330M + 20M = 350M. Then we size up the result and additionally reserve 20% for growth:

dbms.memory.pagecache.size = 120 * (350 MB) =  42GB

We configure the page cache by adding the following to neo4j.conf:

dbms.memory.pagecache.size=42G
### Heap size
The heap space is used for query execution, transaction state, management of the graph etc. The size needed for the heap is very dependent on the nature of the usage of Neo4j. For example, long-running queries, or queries that are doing cartesian products between large sets of data, may require a larger heap than simpler queries. In case of performance issues we may have to tune our queries and monitor their memory usage in order to determine whether the heap needs to be increased.

The size of the available heap memory is an important aspect for the performance of Neo4j. Generally speaking, we want to configure a large enough heap to sustain concurrent operations. If the heap is sized significantly too small, this may lead to bad behavior.

The heap memory size is determined by the parameters dbms.memory.heap.initial_size and dbms.memory.heap.max_size. It is recommended to set these two parameters to the same value to avoid unwanted full garbage collection pauses.

Example 9.5. Configure the heap size
For many setups, a heap size between 8GB and 16GB is large enough to run Neo4j reliably.

For this example, we will set the heap size to 16 gigabytes. We will set the initial size and max size to the same value to avoid unwanted garbage collections.

dbms.memory.heap.initial_size=16G
dbms.memory.heap.max_size=16G
Verify memory configuration
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
