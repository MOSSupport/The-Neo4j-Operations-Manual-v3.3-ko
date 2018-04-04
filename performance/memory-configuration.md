## 9.1. 메모리 설정
> 이 절에서는 Neo4j 인스턴스를 위한 메모리 설정 방법을 설명합니다.

<span class="glyphicon glyphicon-info-sign" aria-hidden="true"> </span> 이 절에서는 Neo4j의 메모리 설정 값들에 대해 살펴 보겠습니다. 다른 조건인 경우, 이 설정 값들이 달라져야 합니다. 데이터-셋의 크기와 인덱스의 존재는 메모리 사용에 영향을 미칩니다. 여기의 예제는 설명을 위한 것입니다. Neo4j 설치시, 조건에 따라 메모리 설정 숫자 값은 달라져야만 합니다.

Consider the image below. As you can see, the RAM of the Neo4j server has three usage areas, with some sub areas:

##### 그림 9.1. Neo4j 메모리 관리
![](./9_1.png)

메모리 설정을 보통 다음의 순서에 따릅니다:

1. OS 예약:  
   * [OS 메모리](#os-memory) — 운영 체제(OS)에 필요한 리소스를 미리 예측하여 예약.
   * 루씬(Lucene) 인덱스 캐시 — 루씬(Lucene) 인덱스에 필요한 리소스를 미리 예측하여 예약.
2. 페이지 캐시 — 페이지 캐시의 크기 결정.
3. 힙(Heap) 크기 — 힙(Heap)의 크기 결정.
4. Verify memory configuration — Verify that the available RAM fits the configured memory.
We will describe this workflow in further detail in the sections below.

The process described here assumes that the server that we are configuring is exclusively reserved for Neo4j. If there are other services running on the same server, you must also account for their memory requirements.

### OS memory
Some memory must be reserved for running the processes of the operating system itself. It is not possible to explicitly configure the amount of RAM that should be reserved for the operating system, as this is what RAM remains available after configuring page cache and heap space. However, if we do not leave enough space for the OS it will start swapping to disk, which will heavily affect performance.

1GB is a good starting point for a server that is dedicated to running Neo4j.

Example 9.1. Determine resources to reserve for the OS
We simply determine how much to reserve for the OS. This value will be used in the final example of this section.

Amount of memory to reserve for the OS = 1GB.

There are cases where the amount reserved for the OS is significantly larger than 1GB. For example, on servers with exceptionally large RAM.

Lucene index cache
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

Page cache
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
Heap size
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
