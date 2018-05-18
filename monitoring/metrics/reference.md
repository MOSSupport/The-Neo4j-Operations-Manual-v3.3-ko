
## 8.1.2. 매트릭스 참조

```
이 섹션에서는 이용가능한 메트릭스 리스트에 대해서 알아봅니다.
```

이 섹션에서 다음에 대해 다룹니다.:

+ 범용 메트릭스
+ 인과 관계 클러스터링에 특화된 메트릭스 


### 8.1.2.1. 범용 메트릭스 

**테이블 8.1. 체크포인트 메트릭스 데이터베이스**

| 이름                                          | 설명                                         |
| --------------------------------------------- | -------------------------------------------- |
| ``` neo4j.check_point.events```               | 현재까지 실행된  체크 포인트 이벤트 총 개수 |
| ``` neo4j.check_point.total_time```           | 현재가지 소요된  체크 포인트 총 시간          |
| ``` neo4j.check_point.check_point_duration``` | 체크 포인트 이벤트 지속 기간                 |


**테이블 8.2. 데이터베이스 데이터 메트릭스**

| 이름                                      | 설명                                                 |
| ----------------------------------------- | ---------------------------------------------------- |
| ``` neo4j.ids_in_use.relationship_type``` | 데이터 베이스에 저장된 서로 다른 관계 유형의 총 개수 |
| ``` neo4j.ids_in_use.property```          | 데이터 베이스에 사용된 다른 속성 이름의 총 개수      |
| ``` neo4j.ids_in_use.relationship```      | 데이터 베이스에 저장된 총 관계 개수                  |
| ``` neo4j.ids_in_use.node```              | 데이터 베이스에 저장된 총 노드 개수                  |


**테이블 8.3. 데이터베이스 페이지 캐시 메트릭스**

| 이름                                        | 설명                                                     |
| ------------------------------------------- | -------------------------------------------------------- |
| ``` neo4j.page_cache.eviction_exceptions``` | 축출하는 동안 페이지 캐시에서 보여지는 총 예외 처리 횟수 |
| ``` neo4j.page_cache.flushes```             | 페이지 캐시에서 실행되는 총 플러시 횟수                  |
| ``` neo4j.page_cache.unpins```              | 페이지 캐시에서 실행된 총 페이지 고정 해제 횟수          |
| ``` neo4j.page_cache.pins```                | 페이지 캐시에서 실행된 총 페이지 고정 횟수               |
| ``` neo4j.page_cache.evictions```           | 페이지 캐시에서 실행된 총 페이지 이탈 횟수               |
| ``` neo4j.page_cache.page_faults```         | 페이지 캐시에서 일어난 총 페이지 오류 횟수               |
| ``` neo4j.page_cache.hits```                | 페이지 캐시에서 발생한 총 페이지 히트 횟수               |
| ``` neo4j.page_cache.hit_ratio```           | 페이지 캐시에서 발생한 총 룩업 히트 비율                 |


**테이블 8.4. 데이터베이스 트랜잭션 메트릭스**

| 이름                                          | 설명                                           |
| --------------------------------------------- | ---------------------------------------------- |
| ```neo4j.transaction.started```               | 시작된 트랙잭션의 총 수                        |
| ```neo4j.transaction.peak_concurrent```       | 이 시스템에서 보여진 동시 트랜잭션 중  최대 값 |
| ```neo4j.transaction.active```                | 현재 활성화된 트랜잭션 수                      |
| ``` neo4j.transaction.active_read```          | 현재 활성화된 읽기 트랜잭션 수                 |
| ``` neo4j.transaction.active_write```         | 현재 활성화된 쓰기 트랜잭션 수                 |
| ``` neo4j.transaction.committed```            | 커밋(committed) 된 총 트랜잭션 수              |
| ``` neo4j.transaction.committed_read```       | 커밋(committed) 된 총 읽기 트랜잭션 수         |
| ``` neo4j.transaction.committed_write```      | 커밋(committed) 된 총 쓰기 트랜잭션 수         |
| ``` neo4j.transaction.rollbacks```            | 롤백(rolled back)된 총 트랜잭션 수             |
| ``` neo4j.transaction.rollbacks_read```       | 롤백(rolled back)된 총 읽기 트랜잭션 수        |
| ``` neo4j.transaction.rollbacks_write```      | 롤백(rolled back)된 총 쓰기 트랜잭션 수        |
| ``` neo4j.transaction.terminated```           | 종료된 총 트랜잭션 수                          |
| ``` neo4j.transaction.terminated_read```      | 종료된 총 읽기 트랜잭션 수                     |
| ``` neo4j.transaction.terminated_write```     | 종료된 총 쓰기 트랜잭션 수                     |
| ``` neo4j.transaction.last_committed_tx_id``` | 마지막으로 커밋(committed)된 트랜잭션 ID       |
| ```neo4j.transaction.last_closed_tx_id```    | 마지막으로 마감된 트랜잭션 ID                  |


**테이블 8.5. 사이퍼(Cypher) 메트릭스**

| 이름                                 | 설명                                               |
| ------------------------------------ | -------------------------------------------------- |
| ``` neo4j.cypher.replan_events```    | 사이퍼(Cypher)가 쿼리를 다시 짜기로 결정한 총 횟수 |
| ``` neo4j.cypher.replan_wait_time``` | 쿼리 재계획 사이 총 대기 시간 (초)               |


**테이블 8.6. 데이터베이스 로그 회전 메트릭스**

| 이름                                            | 설명                                             |
| ----------------------------------------------- | ------------------------------------------------ |
| ```neo4j.log_rotation.events```                 | 현재까지 실행된 트랜잭션 로그 회전의 총 횟수     |
| ``` neo4j.log_rotation.total_tim```             | 현재까지 트랜잭션 로그를 순환하는데 걸런 총 시간 |
| ``` neo4j.log_rotation.log_rotation_duration``` | 로그 회전 이벤트 지속 시간                       |


**테이블 8.7. 볼트(Bolt) 메트릭스**

| 이름                                          | 설명                                                         |
| --------------------------------------------- | ------------------------------------------------------------ |
| ``` neo4j.bolt.sessions_started```            | 인스턴스 시작 이후 시작된 총 볼트 세션 횟 수                 |
| ``` neo4j.bolt.messages_received```           | 인스턴스 시작 이후 볼트에서 수신된 총 메시지 수              |
| ``` neo4j.bolt.messages_started```            | 인스턴스 시작 이후 작업중인 총 메시지 수. 작업자 스레드가 카운터가 수신 된 메시지 중 얼마나 많은 메시지를 작업자 스레드가 사용했는지 추적한다는 점에서 수신 된 메시지와 다릅니다. |
| ``` neo4j.bolt.messages_done```               | 인스턴스 시작 이후 완성된 총 메시지 작업 수. 이것은 성공, 실패 그리고 무시된 볼트 메시지도 포함합니다. |
| ``` neo4j.bolt.accumulated_queue_time```      | 작업자 스레드를 대기하는 동안 축적된 시간 메시지             |
| ``` neo4j.bolt.accumulated_processing_time``` | 메시지 처리에 사용된 총 누적  작업자 스레드 시간             |


**테이블 8.8. 서버 메트릭스**

| 이름                                    | 설명                                                  |
| --------------------------------------- | ----------------------------------------------------- |
| ````neo4j.server.threads.jetty.idle```` | jetty 풀 내 총 유휴 스레드 개수                       |
| ````neo4j.server.threads.jetty.all````  | jetty 풀 내 총 스레드 개수(유휴 및 사용중인 것 모두 ) |


**테이블 8.9. 네트워크 메트릭스**

| 이름                                             | 설명                                                         |
| ------------------------------------------------ | ------------------------------------------------------------ |
| ```neo4j.network.slave_network_tx_writes```      | 트랙젝션  데이터의 종에서 마스터까지 커밋되기 위해 네트워크에 전송된 총 바이트 수 |
| ``` neo4j.network.master_network_store_writes``` | 한 머신에서 다른 머신으로 저장소를 복사할 때 네트워크에서 전송되는 바이트 수 |
| ``` neo4j.network.master_network_tx_writes```    | 커밋된 트랜잭션을 전하기 위해 트랜잭션 데이터를 포함하는 마스터에서 종으로 네트워크에 전송된 바이트 수 |


**테이블 8.10. 클러스터 메트릭스**

| 이름                                             | 설명                                                         |
| ------------------------------------------------ | ------------------------------------------------------------ |
| ```neo4j.cluster.slave_pull_updates```           | 인스턴스에서 실행된 총 업데이트 풀 개수                      |
| ````neo4j.cluster.slave_pull_update_up_to_tx```` | 인스턴스에 의해 마지막을 풀에서 업데이트된 가장 높은 트랜잭션 아이디 |
| ````neo4j.cluster.is_master````                  | 클러스터 내 인스턴스가 마스터인지 여부                       |
| ````neo4j.cluster.is_available````               | 클러스터 내에서 인스턴스가 이용가능한지 여부                 |

**테이블 8.11. 핵심 메트릭스**

| 이름                                                         | 설명                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------ |
| ```neo4j.causal_clustering.core.append_index```              | RAFT의 로그 인덱스 추가                                |
| ``` neo4j.causal_clustering.core.commit_index```             | RAFT 로그 인덱스 커밋                                  |
| ``` neo4j.causal_clustering.core.term```                     | RAFT 서버의 용어                                       |
| ``` neo4j.causal_clustering.core.leader_not_found```         | 트랜잭션 커밋 실행하는동안 리더는 발견되지 않았습니다. |
| ``` neo4j.causal_clustering.core.tx_retries```               | 트랜잭션 재시도                                        |
| ``` neo4j.causal_clustering.core.is_leader```                | 이 서버가 리더입니까?                                  |
| ``` neo4j.causal_clustering.core.in_flight_cache.total_bytes``` | 인플라이트(In-flight) 캐시 총 바이트                   |
| ``` neo4j.causal_clustering.core.in_flight_cache.max_bytes``` | 인플라이트(In-flight) 캐시 최대 바이트                 |
| ``` neo4j.causal_clustering.core.in_flight_cache.element_count``` | 인플라이트(In-flight)  캐시 최대 요소 수               |
| ``` neo4j.causal_clustering.core.in_flight_cache.max_elements``` | 인플라이트(In-flight)  캐시 최대 요소                  |
| ```neo4j.causal_clustering.core.in_flight_cache.hits```      | 인플라이트(In-flight) 캐시 히트                        |
| ```neo4j.causal_clustering.core.in_flight_cache.misses```    | 인플라이트(In-flight) 캐시 미스                        |
| ``` neo4j.causal_clustering.core.message_processing_delay``` | RAFT 메시지 수신 및 과정 사이 지연                     |
| ``` neo4j.causal_clustering.core.message_processing_timer``` | RAFT 메시지 처리 시간                                  |


#### 자바 가상 머신 메트릭스

이 메트릭스는 환경에 따라 변할 수 있고 하드웨어 및 JVM 환경 설정에 따라 다릅니다. 일반적으로 이 메트릭스에는 가비지 컬렉션(예: 이벤트의 수 및 수집 소요시간), 메모리 폴과 버퍼 및 실행 중인 활성 스레드에 대한 정보를 나타냅니다. 

#### 8.1.2.2. 인과관계 클러스터에 특화된 메트릭스

코어 및 읽기 복제 역할은 다른 특성과 지원되는 프로토콜에 따라 다양한 메트릭스를 사용합니다. 코어 메트릭스는 Raft 분산 합의 포로토콜의 집합 상태 및 읽기 복제본에 보내진 트랜잭션 개수와 같은 중요한 세부 사항을 모니터링 합니다. 읽기 복제품 메트릭스는 코어 서버와 관련된 비동기 복제 상태를 추적하는 것보다 훨씬 간단합니다.  


#### 코어(Core)

코어 서버는 분산 Raft 결과 알고리즘에 관련된 광범위한 메트릭스를 추적합니다. 또한 읽기 복제본(온라인 코어 서버에 새롭게 업데이트된 것)과 관련된 로딩(트랜잭션 로그-전송 요청)을 추적합니다. 

**테이블 8.12. 핵심 메트릭스** 

 | 이름                                                         | 설명                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ``` neo4j.causal_clustering.core.commit_index```             | 이 서버의 Raft 커밋 인덱스는 Raft 로그에 안전하게 커밋된 트랜잭션의 개수  나타냅니다. |
| ``` neo4j.causal_clustering.core.append_index```             | 이 서버의 Raft 추가 인덱스는 Raft 로그에 추가된(반드시 커밋된 것은 제외) 트랜잭션 개수를 나타냅니다. |
| ``` neo4j.causal_clustering.core.term```                     | 이 서버에서 Raft 용어는 이곳에서 발생한 리더 선거 횟수를 보여줍니다. |
| ``` neo4j.causal_clustering.core.leader_not_found```         | 이 서버가 Raft 프로토콜 리더를 찾지 못한 횟수를 나타냅니다.  |
| ``` neo4j.causal_clustering.core.tx_pull_requests_received``` | 현재 서버에서 수행된 로그-이동 요청 트랜잭션 개수를 나타냅니다. |
| ``` neo4j.causal_clustering.core.tx_retries```               | 현재 서버에서 재 시도해야될 트랜잭션 개수를 나타냅니다.      |
| ``` neo4j.causal_clustering.core.is_leader```                | 현재 서버가 Raft 리더 역할 여부를 나타냅니다.                |
| ``` neo4j.causal_clustering.core.dropped_messages```         | 현재 서버에서 다른 코어 서버와 커뮤니케이션 실패로 발생한 메시지 개수를 나타냅니다. |
| ``` neo4j.causal_clustering.core.queue_sizes```              | 다른 코어 서버로 아웃바운드된 Raft 메시지의 총 큐 사이즈를 나타냅니다. |


#### 읽기 복제본

읽기 복제본 메트릭스는 윈도우 복제본을 코어 서버와 관련지어서 추적합니다.

**8.13. 코어 메트릭스**

| 이름                                       | 설명                                                         |
| ------------------------------------------ | ------------------------------------------------------------ |
| ```pull_updates```                         | 이 서버가 생성한 트랜잭션 동기화 업데이트 요청 개수를 나타냅니다. |
| ``` pull_update_highest_tx_id_requested``` | 코어 서버에서 수신된 것 중 가장 높은 트랜잭션 아이디를 나타냅니다. |
| ``` pull_update_highest_tx_id_received```  | 이 서버가 코어 서버에서 받은 마지막 트랜잭션 아이디를 나타냅니다. |
