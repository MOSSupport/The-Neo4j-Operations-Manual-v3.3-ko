## 5.1. 단일 인스턴스 업그레이드

```
이 섹션에서는 단일 Neo4j 인스턴스를 업그레이드하는 방법을 설명합니다.
```

Neo4j HA클러스터(Neo4j Enterprise Edition)를 업그레이드하려면 매우 구체적인 절차를 따라야 합니다. [섹션 5.4,"Neo4j HA클러스터 업그레이드"](./high-availability.md)를 참조하십시오.

### 5.2.1. 2.x에서 업그레이드

데비안을 사용하는 경우 [섹션 2.2.1.2"업그레이드"](../installation/linux/debian.md)를 참조하십시오.

  1. 데이터베이스가 실행 중이면 완전히 종료합니다.
  2. 데이터베이스 디렉터리와 [neo4j.conf](../configuration/file-locations.md)의 백업 복사본을 만듭니다.
  3. Neo4j EnterpriseEdition과 함께 제공되는 [온라인 백업 도구](../backup.md)를 사용하는 경우 백업이 성공적으로 완료되었는지 확인합니다.
  4. 이전 설치의 구성 파일에 있는 설정을 검토하고 모든 사용자 지정 설정을 3.3.5 설치에 적용합니다.
