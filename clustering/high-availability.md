### 4.3.1. 고 가용성 `Enterprise Edition`
> 이 장에서는 Neo4j HA 클러스터의 아키텍처에 대해 설명합니다.

Neo4j 클러스터는 단일 마스터 인스턴스와 0 개 이상의 슬레이브 인스턴스로 구성됩니다. 클러스터의 모든 인스턴스는 로컬 데이터베이스 파일에 전체 데이터 복사본을 가지고 있습니다. 기본 클러스터 구성은 세 개의 인스턴스로 구성됩니다:

##### 그림 4.3.1. Neo4j 클러스터
![](./4.3.1.neo4j-cluster.png)
