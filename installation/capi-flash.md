


## 2.6. CAPI 플래시 <code>Enterprise Edition</code>

> 이 섹션에서는 Neo4j에서 CAPI 플래시를 저장소로 사용하는 법을 다룹니다. 


Neo4j는 파일 시스템을 사용하는 대신, 파일 저장용으로 CAPI 플래쉬를 사용할 수 있습니다. CAPI(Coherent Accelerator Processor Interface고유 가속기 프로세서 인터페이스)는 IBM의 기술 중 하나로, PCle(주변 장치 구성 요소별 연결 익스프레스)가 외장 카드 FGPA(Field Programmable Gate Array필트 프로그램 게이트 배열)에서 Power8 CPU와 일관된 메모리 뷰를 볼 수 있도록 공유합니다. CAPI 플래시는 CAPI카드에 내장되었거나 파이버 채널을 통해 플래시 저장 장치에 접속하는 기술을 응용한 어플리케이션 입니다. 


Neo4j CAPI 플래시를 통합하면 I/O 처리량과 I/O 로딩 확장성을 향상시킵니다. 또한, 저장된 파일 중복 캐싱을 막아서 메모리 활용 능력은 향상 시키고 블록이 찢어지는 현상을 방지합니다. 더 나아가, 파일 작성이 기존 저장 시스템에 블록 사이즈에 활당되어 있지 않을 때 발생하는 원 블록 사이즈 읽기/수정/쓰기 문제를 헤결합니다. 이런 장점은 특별히 읽기 작업이 많을 경우 Neo4j 성능이 향상되었습니다. 



Neo4j CAPI 플래시는 Neo4j기업용 에디션과 호환이되는 확장자 입니다. 다운로드는 [the Neo4j download site]("https://neo4j.com/download/other-releases/")에서 가능합니다. 



<span class="glyphicon glyphicon-info-sign" aria-hidden="true"></span> >호환관련 공지: Neo4j 3.1.0.또는 3.1.1버전을 최신 버전으로 업그레이드 할 때, 다음 절차를 반드시 따라야합니다. [섹션 2.6.4, "3.1.0또는 3.1.1버전 업그레이드"]("https://neo4j.com/docs/operations-manual/current/installation/capi-flash/#capi-upgrade-from-3.1.0-or-3.1.1")



## 2.6.1 CAPI 플래시 작동을 위한 Neo4j 환경설정 

Neo4j와 CAPI 플래시를 함께 작업하기 위해서 3단계에 걸친 환경설정이 필요합니다. :


1. Neo4j 접근이 CAPI 플래쉬 하드웨어에 접근할 수 있도록 올바르게 구성된 환경(Power8 시스템/ 구성)인지 확인해야 합니다. 
   더 많은 정보는 [섹션 2.6.1.1, "파워8 시스템 & CAPI 플래시 환경설정"]("https://neo4j.com/docs/operations-manual/current/installation/capi-flash/#capi-configuration-power8-capi-flash")에서 확인 가능합니다. 

2. CAPI 플래시에서 작동하는 Neo4j버전은 ```neo4j-blockdevice-VERSION.jar``` 파일, 

  
   더 많은 정보는 [섹션 2.6.1.2, "Neo4j 블록 장치 통합 라이브러리"]("https://neo4j.com/docs/operations-manual/current/installation/capi-flash/#capi-neo4j-block-device-integration-library")에서 확인 가능합니다.


3.  

   더 많은 정보는 [섹션 2.6.1.3, "Neo4j 블록 장치 환경설정"]("https://neo4j.com/docs/operations-manual/current/installation/capi-flash/#capi-neo4j-block-device-configuration")에서 확인 가능합니다.
 