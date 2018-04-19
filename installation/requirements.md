

## 2.1 시스템 요구사항

```
이 섹션에서는 Neo4j 인스턴스를 운영할 때 필요한 시스템 요구사항을 전체적으로 검토합니다.
```

### CPU
일반적으로 성능은 큰 그래프의 경우 메모리 또는 I/O 경계이며, 그래프 경계를 메모리에 맞도록 계산해줍니다. 


**최소**
  Intel Core i3


**권장 사항**
  
  Intel Core i7

  IBM POWER8


### 메모리

 메모리가 클수록 큰 그래프를 추가할 수 있지만, 가비지 콜렉션 작업은에 방해되지 않도록 환경설정 해야합니다. 
더 많은 내용은 [섹션 9.1,메모리 환경설정]("https://neo4j.com/docs/operations-manual/current/performance/memory-configuration/")을 참조하십시오. 


**최소**
  2GB

**권장 사항**
  16- 32GB 또는 그 이상

### 디스크

용량 외에도 저장소를 선택할 때, 디스크 특성별 퍼포먼스가 가장 중요한 요소입니다. Neo4j는 랜덤 읽기(random read) 작업에 큰 영향을 미칩니다. 평균 검색 시간이 적게 걸리는 미디어를 택하세요: 회전하는 SSD 디스크. 자세한 내용은 [섹션 9.5, 디스크,RAM과 다른 팁]("https://neo4j.com/docs/operations-manual/current/performance/disks-ram-and-other-tips/")을 참조하십시오.


**최소**
 
  10GB SATA

**권장 사항**

  SSD w/ SATA


### 파일 시스템

ACID가 적절하게 동작하기 위해서는 파일시스템이 플러시(fsync, fdatasync)을 지원해야 합니다. 리눅스 옵션 중 하나인 파일시스템 환경설정 방법은 [섹션 9.4, 리눅스 파일 시스템 튜닝]("https://neo4j.com/docs/operations-manual/current/performance/linux-file-system-tuning/")을 참고하십시오.


**최소**

  ext4 (또는 비슷)

**권장 사항**
  
  ext4, ZFS


### 소프트웨어

 Neo4j를 실행하려면 자바 가상머신(JVM)이 필요합니다. 커뮤니티 버전의 Windows와 Mac에는 편의상 JVM가 내장되어 있습니다. 기업용 Neo4j를 포함한 다른 배포판에는 이전 JVM 버전이 필요합니다. 


**Java**

 OpenJDK 8[^(1)] 
 \[^(1)] : Zulu OpenJDK or a Debian distribution.
 Oracle Java 8
 IBM Java 8


**운영 시스템**

  Ubuntu(우분투) 14.04, 16.04; Debian 8, CentOS 6, 7; Fedora, Red Hat, Amazon Linux.

  추가적으로 SUSE는 Neo4j에서 잘 작동하지만, 상업적으로는 사용되지 않습니다. 
  
  Window 서버 2012, 2016


**구조**

  x86
  
  openPOWER (POWER8)





















