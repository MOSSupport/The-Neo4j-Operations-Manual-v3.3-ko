

## 2.1 시스템 요구사항

> 이 챕터에서는 Neo4j 인스턴스를 운영할 때 필요한 시스템 요구사항을 전체적으로 검토합니다.


### CPU
 
일반적으로 성능은 큰 그래프를 위한 메모리 또는 I/O 기반이며, 그래프가 메모리에 맞도록 계산해줍니다. 


# 최소 
  Intel Core i3


# 권장 사항
  
  Intel Core i7

  IBM POWER8


### 메모리

 많은 메모리를 가진 환경에서는 큰 그래프를 추가할 수 있지만, 가비지 콜렉션 작업은 방해하지 않도록 환경설정 해야합니다. 
 더 많은 내용은 Section 9.1, “Memory configuration”을 참조하십시오. 


# 최소
  2GB

# 권장 사항
  16- 32GB 또는 그 이상

### 디스크

# 최소
 
  10GB SATA

# 권장 사항

  SSD w/ SATA

### 파일 시스템


# 최소

  ext4 (또는 비슷)

# 권장 사항
  
  ext4, ZFS


### 소프트웨어

 Neo4j를 실행하려면 자바 가상머신(JVM)이 필요합니다. Windows와 Mac에서 커뮤니티는 편의상 JVM가 되장되어있습니다. 
 Neo4j를 포함한다른 배포자는 JVM이전 버전이 필요합니다. 



# Java

 OpenJDK 8[^(1)] 
 \[^(1)] : Zulu OpenJDK or a Debian distribution.
 Oracle Java 8
 IBM Java 8


# 운영 시스템

  Ubuntu(우분투) 14.04, 16.04; Debian 8, CentOS 6, 7; Fedora, Red Hat, Amazon Linux.

  추가적으로, SUSE는 Neo4j에서 잘 작동하지만, 생산용으로는 사용되지 않습니다. 
  
  Window 서버 2012, 2016


# 구조

  x86
  openPOWER (POWER8)





















