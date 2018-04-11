## 9.2. 가비지 컬렉터
```
이 절에서는 자바 가상 머신의 가비지 컬렉터가 Neo4j 성능에 미치는 영향을 알아봅니다.
```
힙은 올드/영 제너레이션으로 구분됩니다. 새로운 개체(objects)는 영 제너레이션에서 할당되고, 나중에 오랜 동안 사용(사용중인 경우)되면 올드 제너레이션으로 이동합니다. 한 제너레이션이 채워지면 가비지 켈렉터가 수집(collection)을 수행하며, 그 동안 해당 프로세스의 다른 모든 스레드(thread)는 일시 정지됩니다. 일시 중지 시간은 실시간 개체 집합과 상호 관련이 있고, 영 제너레이션의 크기와 무관하므로 영 제너레이션의 가비지 수집은 빠릅니다. 올드 제너레이션의 일시 중지 시간은 힙의 크기와 비례합니다. 이러한 이유로 올드 제너레이션에 트랜잭션과 쿼리 상태가 절대 만들어지지 않도록 힙의 크기를 조정해야합니다.  
힙 크기는 neo4j.conf 파일의 dbms.memory.heap.max_size(MB 단위)에서 설정합니다. 힙의 초기 크기는 dbms.memory.heap.initial_size 설정 값, 또는 -Xms${mem} 플래그로 지정되거나, 명시적인 설정값이 지정되지 않으면 JVM 자체에 휴리스틱(경험치에 의한) 설정으로 선택됩니다. JVM은 필요에 따라 자동으로 최대 크기까지 힙을 확장합니다. 힙을 늘리려면 가비지 컬렉션 주기가 필요합니다. 최초의 힙 크기와 최대 힙 크기를 같은 값으로 설정하는 것이 좋습니다. 이렇게 하면 가비지 컬렉터가 힙을 늘릴 때 발생하는 일시 중지를 피할 수 있습니다.  
힙의 올드와 영 제너레이션간의 크기 비율 -XX:NewRatio=N 플래그로 제어됩니다. N은 디폴트로 2와 8 사이 값입니다. 비율 2는 올드 제너레이션 크기를 영 제너레이션 크기로 나눈값이 2라는 의미입니다. 다시 말하면, 힙 메모리의 3분의 2는 올드 제너레이션이라는 의미입니다. 비율 3은 힙 메모리의 4분의 3은 올드 제너레이션이라는 의미이고, 비율 1은 두 제너레이션의 크기가 같다는 의미입니다.비율 1은 매우 공격적이지만 트랜잭션이 많은 데이터를 변경하는 경우 필요합니다. 큰 결과-셋의 정렬과 같이 많은 양의 데이터를 유지하는 Cypher 쿼리를 실행하는 경우, 영 제너레이션이 커야합니다.  
영 제너레이션의 크기가 너무 작으면 수명이 짧은 개체들이 너무 빨리 올드 제너레이션으로 옮겨집니다. 이를 조기 프로모션(premature promotion) 이라고 하며 올드 제너레이션의 가비지 컬렉션 주기의 빈도를 늘려 데이터베이스 속도를 저하시킵니다. 영 제너레이션의 크기가 너무 크면, 가비지 컬렉터는 영 제너레이션에서 올드 제너레이션으로 승진(promote)하는 모든 개체(onjects)를 수용하기에 충분한 공간이 없다고 결정할 수 있습니다. 이렇게되면 영 제너레이션의 가비지 콜렉션 사이클이 올드 제너레이션의 가비지 콜렉션 사이클로 바뀌어 데이터베이스가 다시 느려집니다. 더 많은 동시 스레드를 실행하면 주어진 시간 내에 더 많은 할당이 발생할 수 있으며, 특히 영 제너레이션에 대한 압박이 증가합니다.
> JVN의 Compressed OOP는 개체 참조(object references)가 32비트만 사용하도록 압축을 허용합니다. 이 특징은 많은 메모리를 절약하지만, 32GB이상의 힙에서는 사용할 수 없습니다. 따라서 32GB를 초과하는 힙 크기의 증가로 인한 이익은, 힙 크기가 64GB 이상보다 작은 경우, 오히려 적어집니다.  

Neo4j는 자바 프로세스의 수명주기 동안 올드 제너레이션에 머무르는 오랜-수명을 가진 많은 개체를 가집니다. 효율적으로 처리하고, 가비지 콜렉션 일시 정지 시간에 영향을 주지 않으려면, 동시 가비지 컬렉터를 사용하는 것이 좋습니다.
How to tune the specific garbage collection algorithm depends on both the JVM version and the workload. It is recommended to test the garbage collection settings under realistic load for days or weeks. Problems like heap fragmentation can take a long time to surface.

To gain good performance, these are the things to look into first:

Make sure the JVM is not spending too much time performing garbage collection. The goal is to have a large enough heap to make sure that heavy/peak load will not result in so called GC-trashing. Performance can drop as much as two orders of magnitude when GC-trashing happens. Having too large heap may also hurt performance so you may have to try some different heap sizes.
Use a concurrent garbage collector. We find that -XX:+UseG1GC works well in most use-cases.

The Neo4j JVM needs enough heap memory for the transaction state and query processing, plus some head-room for the garbage collector. Because the heap memory needs are so workload dependent, it is common to see configurations from 1 GB, up to 32 GBs of heap memory.
Start the JVM with the -server flag and a good sized heap.

The operating system on a dedicated server can usually make do with 1 to 2 GBs of memory, but the more physical memory the machine has, the more memory the operating system will need.
Edit the following properties:
