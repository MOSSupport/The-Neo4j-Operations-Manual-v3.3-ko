## 9.2. 가비지 컬렉터
```
이 절에서는 자바 가상 머신의 가비지 컬렉터가 Neo4j 성능에 미치는 영향을 알아봅니다.
```
힙은 올드/영 제너레이션으로 구분됩니다. 새로운 개체(objects)는 영 제너레이션에서 할당되고, 나중에 오랜 동안 사용(사용중인 경우)되면 올드 제너레이션으로 이동합니다. 한 제너레이션이 채워지면 가비지 켈렉터가 수집(collection)을 수행하며, 그 동안 해당 프로세스의 다른 모든 스레드(thread)는 일시 정지됩니다. 일시 중지 시간은 실시간 개체 집합과 상호 관련이 있고, 영 제너레이션의 크기와 무관하므로 영 제너레이션의 가비지 수집은 빠릅니다. 올드 제너레이션의 일시 중지 시간은 힙의 크기와 비례합니다. 이러한 이유로 올드 제너레이션에 트랜잭션과 쿼리 상태가 절대 만들어지지 않도록 힙의 크기를 조정해야합니다.  
The heap size is configured with the dbms.memory.heap.max_size (in MBs) setting in the neo4j.conf file. The initial size of the heap is specified by the dbms.memory.heap.initial_size setting, or with the -Xms???m flag, or chosen heuristically by the JVM itself if left unspecified. The JVM will automatically grow the heap as needed, up to the maximum size. The growing of the heap requires a full garbage collection cycle. It is recommended to set the initial heap size and the maximum heap size to the same value. This way the pause that happens when the garbage collector grows the heap can be avoided.

The ratio of the size between the old generation and the new generation of the heap is controlled by the -XX:NewRatio=N flag. N is typically between 2 and 8 by default. A ratio of 2 means that the old generation size, divided by the new generation size, is equal to 2. In other words, two thirds of the heap memory will be dedicated to the old generation. A ratio of 3 will dedicate three quarters of the heap to the old generation, and a ratio of 1 will keep the two generations about the same size. A ratio of 1 is quite aggressive, but may be necessary if your transactions changes a lot of data. Having a large new generation can also be important if you run Cypher queries that need to keep a lot of data resident, for example when sorting big result sets.

If the new generation is too small, short-lived objects may be moved to the old generation too soon. This is called premature promotion and will slow the database down by increasing the frequency of old generation garbage collection cycles. If the new generation is too big, the garbage collector may decide that the old generation does not have enough space to fit all the objects it expects to promote from the new to the old generation. This turns new generation garbage collection cycles into old generation garbage collection cycles, again slowing the database down. Running more concurrent threads means that more allocations can take place in a given span of time, in turn increasing the pressure on the new generation in particular.

> The Compressed OOPs feature in the JVM allows object references to be compressed to use only 32 bits. The feature saves a lot of memory, but is not enabled for heaps larger than 32 GB. Gains from increasing the heap size beyond 32 GB can therefore be small or even negative, unless the increase is significant (64 GB or above).

Neo4j has a number of long-lived objects, that stay around in the old generation, effectively for the lifetime of the Java process. To process them efficiently, and without adversely affecting the garbage collection pause time, we recommend using a concurrent garbage collector.

How to tune the specific garbage collection algorithm depends on both the JVM version and the workload. It is recommended to test the garbage collection settings under realistic load for days or weeks. Problems like heap fragmentation can take a long time to surface.

To gain good performance, these are the things to look into first:

Make sure the JVM is not spending too much time performing garbage collection. The goal is to have a large enough heap to make sure that heavy/peak load will not result in so called GC-trashing. Performance can drop as much as two orders of magnitude when GC-trashing happens. Having too large heap may also hurt performance so you may have to try some different heap sizes.
Use a concurrent garbage collector. We find that -XX:+UseG1GC works well in most use-cases.

The Neo4j JVM needs enough heap memory for the transaction state and query processing, plus some head-room for the garbage collector. Because the heap memory needs are so workload dependent, it is common to see configurations from 1 GB, up to 32 GBs of heap memory.
Start the JVM with the -server flag and a good sized heap.

The operating system on a dedicated server can usually make do with 1 to 2 GBs of memory, but the more physical memory the machine has, the more memory the operating system will need.
Edit the following properties:
