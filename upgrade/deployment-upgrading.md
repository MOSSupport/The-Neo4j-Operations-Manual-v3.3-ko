## 5.1. 단일 인스턴스 업그레이드

```
이 섹션에서는 단일 Neo4j 인스턴스를 업그레이드하는 방법을 설명합니다.
```

Neo4j HA클러스터(Neo4j Enterprise Edition)를 업그레이드하려면 매우 구체적인 절차를 따라야 합니다. [섹션 5.4,"Neo4j HA클러스터 업그레이드"](./high-availability.md)를 참조하십시오.

### 5.2.1. 2.x에서 업그레이드

데비안을 사용하는 경우 [섹션 2.2.1.2"업그레이드"](../installation/linux/debian.md)를 참조하십시오.

  1. 데이터베이스가 실행 중이면 완전히 종료합니다.
  2. 데이터베이스 디렉터리와 [neo4j.conf](../configuration/file-locations.md)의 백업 복사본을 만듭니다.
