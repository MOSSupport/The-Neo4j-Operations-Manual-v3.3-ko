


## 2.6. CAPI 플래시 <code>Enterprise Edition</code>

> 이 섹션에서는 Neo4j에서 CAPI 플래시를 저장소로 사용하는 법을 다룹니다. 

Neo4j는 파일 시스템을 사용하는 대신, 파일 저장용으로 CAPI 플래쉬를 사용할 수 있습니다. CAPI(Coherent Accelerator Processor Interface고유 가속기 프로세서 인터페이스)는 IBM의 기술 중 하나로, PCle(주변 장치 구성 요소별 연결 익스프레스)가 외장 카드 FGPA(Field Programmable Gate Array필트 프로그램 게이트 배열)에서 Power8 CPU와 일관된 메모리 뷰를 볼 수 있도록 공유합니다. CAPI 플래시는 CAPI카드에 내장되었거나 파이버 채널을 통해 플래시 저장 장치에 접속하는 기술을 응용한 어플리케이션 입니다. 

Neo4j CAPI 플래시를 통합하면 I/O 처리량과 I/O 로딩 확장성을 향상시킵니다. 또한, 저장된 파일 중복 캐싱을 막아서 메모리 활용 능력은 향상 시키고 블록이 찢어지는 현상을 방지합니다. 더 나아가, 파일 작성이 기존 저장 시스템에 블록 사이즈에 활당되어 있지 않을 때 발생하는 원 블록 사이즈 읽기/수정/쓰기 문제를 헤결합니다. 이런 장점은 특별히 읽기 작업이 많을 경우 Neo4j 성능이 향상되었습니다. 

Neo4j CAPI 플래시는 Neo4j기업용 에디션과 호환이되는 확장자 입니다. 다운로드는 [the Neo4j download site]("https://neo4j.com/download/other-releases/")에서 가능합니다. 

<span class="glyphicon glyphicon-info-sign" aria-hidden="true"></span> >호환관련 공지: Neo4j 3.1.0.또는 3.1.1버전을 최신 버전으로 업그레이드 할 때, 다음 절차를 반드시 따라야합니다. [섹션 2.6.4, "3.1.0또는 3.1.1버전 업그레이드"]("https://neo4j.com/docs/operations-manual/current/installation/capi-flash/#capi-upgrade-from-3.1.0-or-3.1.1")


## 2.6.1 CAPI 플래시 작동을 위한 Neo4j 환경설정 

Neo4j와 CAPI 플래시를 함께 작업하기 위해서 3단계에 걸친 환경설정이 필요합니다. :


1. Neo4j 접근이 CAPI 플래쉬 하드웨어에 접근할 수 있도록 올바르게 구성된 환경(Power8 시스템/ 구성)인지 확인해야 합니다. 더 많은 정보는 [섹션 2.6.1.1, "파워8 시스템 & CAPI 플래시 환경설정"]("https://neo4j.com/docs/operations-manual/current/installation/capi-flash/#capi-configuration-power8-capi-flash")에서 확인 가능합니다. 

2. CAPI 플래시에서 작동하는 특정 Neo4j버전은 ```neo4j-blockdevice-VERSION.jar``` 파일이 필요합니다. 더 많은 정보는 [섹션 2.6.1.2, "Neo4j 블록 장치 통합 라이브러리"]("https://neo4j.com/docs/operations-manual/current/installation/capi-flash/#capi-neo4j-block-device-integration-library")에서 확인 가능합니다.


3. CAPI 플래시를 작동시키고 작동 방법 설정을 위해서 관련 환경설정을 반드시 [neo4j.conf]("https://neo4j.com/docs/operations-manual/current/configuration/file-locations/")에 추가해야합니다. 더 많은 정보는 [섹션 2.6.1.3, "Neo4j 블록 장치 환경설정"]("https://neo4j.com/docs/operations-manual/current/installation/capi-flash/#capi-neo4j-block-device-configuration")에서 확인 가능합니다.
 

 시작에 앞서, Neo4j는 꺼져있도록해야 합니다. Neo4j의 환경설정은 새로 설치되었을 때 이상적으로 작동합니다. 그러나,```neo4j-admin blockdev import```명령어를 사용해서 Neo4j 데이터 베이스를 CAPI 플래시 저장소에 옮기는 것이 가능합니다. 이를 실행하기 위한 자세한 내용은 [섹션 2.6.3 "CAPI 플래시의 어드민 명령어"]("https://neo4j.com/docs/operations-manual/current/installation/capi-flash/#capi-neo4j-admin-commands")를 참조하면 됩니다. 

## 2.6.1.1 파워8 시스템 & CAPI 플래시 환경 설정


먼저, CAPI 플래시 하드웨어가 설치되어 있고 바르게 작등하는지 확인하기 위해 문서를 확인하세요. 또한, 유저가 Neo4j 데이터베이스에 작동할하도록 접근, 일기, 쓰기가 가능하도록 CAPI 운영 시스템에서 노출되는 플래시 장치(일반적으로 /dev/sgX를 통과하는 경로, X:숫자)를 조정해야 합니다. 

일반적인 설치에서, CAPI 플래시 장치는 cxl 그룹의 모든 유저는 읽기/쓰기 권한이 있습니다. Neo4j가 전용 ```neo4j``` 사용자로 실행될 경우, 이 유저를```sudo usermod -a -G cxl neo4j``명령어를 이용해서 ```cxl```그룹에 추가할 수 있습니다. CAPI 플래시 소프트웨어가 ```/opt/ibm/capikv```디렉토리에 설치되었다고 가정했을 때, ```cxl```그룹의 유저는 어떤 장치가 이용 가능한지 확인할 수 있습니다. 

```
root:~$ /opt/ibm/capikv/bin/cxlfstatus
CXL Flash Device Status

Found 0601 0000:01:00.0 Slot2
  Device:  SCSI       Block    Mode       LUN WWID                           Persist
  sg2:     1:0:0:0,   sdb,     superpipe, 60025380025382462300035000000000,  sg0200      sd0200
  sg3:     1:1:0:0,   sdc,     superpipe, 60025380025382462300048000000000,  sg0210      sd0210

Found 0601 0005:01:00.0 Slot4
  Device:  SCSI       Block    Mode       LUN WWID                           Persist
  sg4:     2:0:0:0,   sdd,     superpipe, 60025380025382463300014000000000,  sg0400      sd0400
  sg5:     2:1:0:0,   sde,     superpipe, 60025380025382463300160000000000,  sg0510      sd0510

root:~$
```



이 결과로, 총 두 개에서 각각 SSDs가 두개 설치된 CAPI FlashGT 카드를 확인할 수 있습니다. ```sgX``` 장치는 ```/dev``` 디렉토리를 ```/dev/sgX```로 확인됩니다. ```Mode``` 필드는 ```legacy```나 ```superpipe```가 될수 있고 Neo4j가 장치에서 작동하기 위해서 ```Mode```는 반드시 ```superpipe```로 설정해야 합니다. 마지막 두 컬럼은 "변하지 않는 포트 이름"이고, 이들은 Neo4j 환경 설정 포트로 쓸 것을 권장합니다. 

Neo4j는 CAPI 플래시 장치에 독점 엑세스할 수 있어야하고, 한 번에 하나의 Neo4j 인스턴스 장치만 사용해야 합니다. 뿐만 아니라, Neo4j는 물리적 LUNs(Logical Unit Number논리 단위 넘버)에 엑세스할 수 있어야 됩니다. 즉, 가상 LUNs가 지원되지 않습니다. 
가상 LUNs는 CAPI 플래시를 확장 RAM처럼 사용하는 CAPI 플래시 작동 모드 입니다. Neo4j와 작동할 때, CAPI 플래시가 저장소로 사용될 경우 이는 적용되지 않습니다. 

다음은, CAPI 플래시 하드웨어 토폴로지(topology)에 대해 알아보겠습니다. ```/dev/sgX``` 장치는 포트라고 알려진 CAPI 플래시 하드웨어에 있습니다. 각 CAPI 플래시 카드는 한 개 이상의 포트와, 설치된 CAPI 플래시 카드보다 많은 시스템을 가질 수 있습니다. 뿐만 아니라 플래시에 붙여진 파이버 채널을 사용할 때, 다른 두가지 포트는 LBA(Logical Block Address:논리 블록 주소)를 참조합니다. [섹션 2.6.1.3,"Neo4j 블록 장치 환경 설정"]("https://neo4j.com/docs/operations-manual/current/installation/capi-flash/#capi-neo4j-block-device-configuration")에서  Neo4j에서 두 개 이상의 장치를 이용하는 방법을 살펴보겠습니다. 이 기능은 CAPI 플래시가 제공하는 높은 동시 I/O 처리량에 크게 기여합니다. 
 

CAPI 플래시를 쓰도록 Neo4j를 설정할 때, ```/dev /sg0200```와  ```dev/sg2```같은 영구 포트 이름을 장치 지정자로 사용하는 것이 중요합니다. 영구 포트는 시스템이 재시작 되더라도, 그 이름을 절대 바꾸지 않습니다. 즉, 모든 참조는 항상 같은 하드웨어 실제 저장소를 가리킵니다. 다른 (일시적인) 포트 이름은 시스템 재시작 사이에 다른 하드웨어 저장소를 가리키므로,다른 설정을 나타냅니다. 이 때 Neo4j는 에러를 로그하고 시작을 거부할 것 입니다. 
 

 마지막으로, CAPI 플래시 블록 라이브러리가 시스템에 설치되어 Neo4j에서 실행할 수 있어야합니다. 이 파일은 ```libcflsh_block.so```로 불리고, /opt/ibm/capikv/lib  디렉토리에 있습니다. 라이브러리는 Neo4j 유저가 읽고, 실행할 수 있어야합니다. 정확한 최소 사용 권한은 설정에 따라 다르지만, CAPI 장치 또한 권한을 요구하기에 라이브러리를 모두가 읽고/실행할수 있도록 체크하는 것이 안전합니다.  


## 2.6.1.2. Neo4j 블록 디바이스 통합 라이브러리 

Neo4j 블록 디바이스 통합 라이브러리는  Neo4j 버전 e.g. 3.1.0 및 숫자 페치 버전으로 구성된  ```neo4j-blockdevice-3.1.0.0.jar```와 같이 ```neo4j-blockdevice-VERSION.jar```파일로 배포되어 있습니다. 페치 버전은 주어진 Neo4j 버전을 둘 이상의 블록 장치 통합 라이브러리 버전으로 출시할 수 있게 합니다. 라이브러리는 주어진 Neo4j 버전에만 호환이 됩니다. 통합 jar 라이브러리 파일은  <neo4j-home>/lib 에 위치하고, 형제 jar 파일과 같은 접근 권한을 가집니다. 이는 라이브러리가 Neo4j 클래스 패스 일부인 것을 보장합니다. 


## 2.6.1.3 Neo4j 블록 디바이스 설정

Neo4j 블록 장치 세 가지 변수는 반드시 neo4j.conf에 설정되어야합니다. 


+ ```dbms.memory.pagecache.swapper=capi```

+ ```dbms.memory.pagecache.swapper.capi.lib```    ```libcflsh_block.so```

+ ```dbms.memory.pagecache.swapper.capi.device```


```neo4j-admin blockdev format```






```$neo4j-home> bin/neo4j-admin blockdev format```
