


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

Neo4j 블록 통합 장치를 작동하려면 세 가지 변수를 반드시 neo4j.conf에 설정되어야합니다. 


+ CAPI 플래시 블럭 장치를 통합하기 위해 ```dbms.memory.pagecache.swapper=capi```를 설정해야합니다. 

+ ```dbms.memory.pagecache.swapper.capi.lib``` 라이브러리를 ```libcflsh_block.so```  경로에 설정하십시오.

+ ```dbms.memory.pagecache.swapper.capi.device```를 CAPI 플래시(관련된 불변의) 포트를 참조하고 해당 토플로지를 설명하는 장치
	 지정자로 설정하십시오. [장치 지정자와 CAPI 플래시 장치 토플로지 섹션]("https://neo4j.com/docs/operations-manual/current/installation/capi-flash/#capi-device-specifier")을 참조하십시오.

 
모든 설정이 완료되면, CAPI 플래시 장치를 포맷해야 합니다. 이는 ```neo4j-admin blockdev format``` 명령어로 설정합니다.:


```$neo4j-home> bin/neo4j-admin blockdev format```

Neo4j는 데이터를 파일에 저장해서, 블록 장치 통합 라이브러리가 DBFS라고 불리는 임베디드 파일 시스템과 제공될 수 있도록 합니다. 
장치를 포맷하면 DBFS가 작동하도록 필요한 메타 데이터가 기롭됩니다. 장치를 포맷하면 그 안의 모든 데이터가 제거되는 것을 유의해야 합니다.
이 작업은 취소할 수 없습니다. 

장치가 포맷된 후, Neo4j는 시작되고 CAPI 플래시를 저장소로 사용합니다. 



## 장치 지정자와 CAPI 플래시 장치 토플로지

블록 장치 통합 라이브러리는 여러 개의 물리적인 블록 장치로 구성된 가상 블록 장치를 나타냅니다. 이는 neo4j.conf 환경에서 ```dbms.memory.pagecache.swapper.capi.device``` 를 사용하여 설정할 수 있습니다. 가장 쉬운 장치 지정자 구성은 단일 물리적 블록 장치를 기반으로하는 구성입니다.

 In this case, the device specifier is simply the path to that physical device. 

이 경우, 장치 지정자는 단순 실제 장치에 대한 경로입니다. 예를 들면:
```
dbms.memory.pagecache.swapper.capi.device=/dev/sg0100
```


만약 두 가지 장치가 다른 물리 주소를 나타낸다면, 다른 LBA-저장소(Logical Block Addressing:논리 블록 주소) 가질 것 입니다. 다른 블록에 있는 LBA 0과 다른 것을 의미합니다. 이 경우 LBA0를 두 개 장치 용량을 가진 하나의 큰 장치로 묶을 수 있습니다. 장치 지정자에서 두 장치의 경로를 연속하는 두 개의경로 구분 문자로 구분해서 제공하여 이 작업을 수행합니다. 경로 구분 문자는 윈도우의 세미클른(;)과 다른 플랫폼의 콜른(:)입니다. 

```sg0100```과 ```sg0200```인 아래의 예는 다른 LBA-공간을 가지고 있고, 이들은 거대한 단일 논리 장치에 결합됩니다. 

```
dbms.memory.pagecache.swapper.capi.device=/dev/sg0100::/dev/sg0200
```

논리 장치는 각각의 장치 지정자가 지정한 순서대로 기본장치 별로 16MiB 스트라이프를 인터리빙하여 구성합니다. 이로써 기본 장치에 정의된 RAID-9 프트웨어를 효율적으로 생성합니다. 
 
여러 장치를 결합함으로써 - 필요한 만큼 - 논리 장치는 제공된 개별 장치의 어떤 용량보다도 많은 양의 용량을 생성할 수 있습니다. 구성된 기본 장치는 모두 같은 용량을 가져야함을 기억해야 합니다. 아래는 세 장치가 결합된 예 입니다. 

```dbms.memory.pagecache.swapper.capi.device=/dev/sg0600::/dev/sg0700::/dev/sg0800```


일부 장치는 한 개 이상의 포트를 나타냅니다. 이들은 동일한 LBA-저장소를 가지고 있음에도 불구하고, 운영 시스템과는 다른 개별 장치로 보입니다. 그 예로는 파이버 채널로 IBM 플래시 가전 장치에 연결된 CAPI 플래시 카드가 있습니다. 이 카드에는 CAPI 플래시 카드에서 나오는 두 개의 물리적 케이블 FC(Fire Channel:섬유채널) 포트가 있어 동일한 장치로 가고, 동일한 LBA-저장소을 노출하도록 구성할 수 있습니다. sg0100과 sg0200이 이 포트를 표현한다고 가정해봅시다. 이 경우에는 LBA 0이 sg0100나 sg0200으로 접근하는 것이 상관 없습니다. 포트 사용여부와 상관없이 동일한 물리 블록을 가질 것 입니다. 경로를 sg0100과 sg0200에 경로 구분자로 분리함으로써 장치 지정자 설치를 설명할 수 있습니다. 


```dbms.memory.pagecache.swapper.capi.device=/dev/sg1:/dev/sg2```

위 두 가지- 소프트웨어 RAID-0과 다중 포트 장치는 결합이 가능합니다. LBA-저장소를 공유하는 장치는 단일 구분 문자로 그룹화 될 것이고, 각 그룹은 결국에 두 개의 구분 문자로 구분될 것 입니다. 아래의 sg3와 sg4가 다른 LBA-저장소를 공유하는 동안, sg1과 sg2가 LBA-저장소를 공유하는 예입니다. 



```
dbms.memory.pagecache.swapper.capi.device=/dev/sg1:/dev/sg2::/dev/sg3:/dev/sg4
                                             ┌─┘       ┌┘         └┐       └─┐
                                        ┌────▼───┐┌────▼───┐  ┌────▼───┐┌────▼───┐
                                       ┌┤/dev/sg1├┤/dev/sg2├┐┌┤/dev/sg3├┤/dev/sg4├┐
                                       │└────────┘└────────┘││└────────┘└────────┘│
                                       │      device a      ││      device b      │
                                       └────────────────────┘└────────────────────┘

```

장치가 어떻게 결합되었는지와는 관계없이 모든 장치는 정확히 동일한 용량을 가져야 합니다. 또한, 위의 에는 다이어그램을 간결하게 유지하고자 임시 포트 이름만을 사용했습니다. 

##2.6.2. 제한 사항


Neoj4의 모든 요소가 CAPI 플래시를 저장소에 호환되는 것은 아닙니다. 아래는 지원되지 않는 항목 리스트 입니다. 


#2.6.2.1 Neo4j-admin 명령어의 덤프와 로드 기능

admin명령어 ```neo4j-admin dump```와 ```neo4j-admin load```는 현재 CAPI 플래시에 있는 데이터 베이스에서는
사용할 수 없습니다. 블록 장치 통합 라이브러리는 다른 명령어를 대안으로 사용할 수 있지만, 사용하기에 유용하지는 않습니다. 

#2.6.2.2. ```dbms.memory.pagecache.swapper``` 변수 변경

데이터 베이스가 특정 환경 설정으로 시작이 된다면, ```dbms.memory.pagecache.swapper``` 변수는 변경이 불가능합니다. 
그렇기 때문에 특정 설정으로 시작하려고 하면 데이터 베이스는 에러를 로딩하고 시작을 거부할 것 입니다. 

추가적으로, 블록 장치 통합 기술은 그 자체로 다음의 한계를 지니고 있습니다. 


```dbms.memory.pagecache.swapper.*```설정에서는 저장소 위치와 설정 요소 및 방법을 나타냅니다. Neo4j-admin 이후 블록 장치를 포맷하지 않고서는 이것과 장치 지정자의 장치 순서 조차 변경할 수 없습니다. 장치를 재설정하지 않고 환경 설정이 변경된다면, Neo4j는 에러를 로딩하고 시작을 거부할 것 입니다. 장치 지정자에 주어진 장치들은 반드시 같은 용량을 가져야 합니다. 
특별히, 장치들은 모두 동일한 블록 사이즈와 블록의 개수를 가져야 합니다. 이를 보장하는 가장 쉬운 방법은 모든 기기에 동일한 제조사 및 기기를 사용하는 것입니다.


##2.6.3 CAPI 플래시 Admin 명령어 

Neo4j 블록 장치 통합 라이브러리는 ```neo4j-admin ```속성에 다음의 명령어를 추가합니다. :

+ ```neo4j-admin blockdev help```는 모든 블록 장치 관련 admin명령에 대한 장치에 도움 메시지를 출력합니다. 

+ ```neo4j-admin blockdev format``` 는 설정된 블록을 DBFS와 포맷합니다. 이것은 장치의 모든 데이터를 제거합니다.

+ ```neo4j-admin blockdev ls```는 설정된 블록 장치에 저장되어 있는 파일 리스트를 나타냅니다.

+ ```neo4j-admin blockdev fsck```는 DBFS 파일 시스템 메타데이터 일관성을 체크합니다. 

+ ```neo4j-admin blockdev import <from-database-path>``` 주어진 데이터베이스를 블록 장치 구성에 임포트합니다. 

+ ```neo4j-admin blockdev dump <file>```는 주어진 블록 장치의 표준 출력을 위해서 파일 중 2진 표현 내용을 제거합니다.

+ ```neo4j-admin blockdev rename <source-path> <target-path>```는 주어진 소스 경로에서 블록 장치의 모든 내용을 목표 경로로 옮깁니다. 


<span class="glyphicon glyphicon-info-sign" aria-hidden="true"></span>```neo4j-admin```명령어 사용하도록 문서화 되어있지 않는 한 실행중인 데이터 베이스에서 절대 사용하지 마십시오. 



이들 명령어는 ```neo4j-blockdevice-*.jar```  파일이 ```<neo4j-install-dir>/lib``` 디렉토리에 설치되어 있어야 되고, neo4j.conf 파일이 CAPI 플래시를 사용할 수 있도록 적절하게 구성되어 있어야 합니다. 


If the block device integration has not been properly configured, then the following error message will be shown:

만약 블록 장치 통합히 적절하게 설정되어있지 않다면, 다음 에러 메시지가 출력될 것 입니다. 

```
$neo4j-home> bin/neo4j-admin blockdev help
neo4j-admin blockdev <sub-command> [options]

    Configure, inspect and administrate the Neo4j block-device integration.
    Use the 'help' sub-command for more information.

This database has not been configured to use custom block device storage
$neo4j-home>
```

블록 장치 통합 변수가 neo4j.conf에 설정되어 있다면, ```help```명령어어는 더욱 유용할 것 입니다.:

```
$neo4j-home> bin/neo4j-admin blockdev help
neo4j-admin blockdev <sub-command> [options]

    Configure, inspect and administrate the Neo4j block-device integration.
    Use the 'help' sub-command for more information.

The following sub-commands are available:

  * help
    Print this help message.

  * format
    Format the block device with a file system, erasing all data on it.

  * ls
    List the files stored on the block device, and their size.

  * fsck
    Check the consistency of the file system metadata on the block device.

  * import <from-database-path>
    Import an existing Neo4j database from the given path on the local file
    system, onto the block device at the same path.

  * dump <file>
    Dump the binary contents of the given file on the block device to standard
    out.

  * rename <source-path> <target-path>
    Move everything on device from source path to target path. The 'move' is
    effectively only a name change of the files. This is useful when performing
    a database-wide file move operation.
$neo4j-home>
```


##2.6.3.1 ```format``` 명령어

```format```명령은 Neo4j 블록 장치 통합 라이브러리에 포함된 파일 시스템인 DBFS와 함께 설정된 장치를 포맷합니다. 

이 명령어는 neo4j.conf에서 ```dbms.memory.pagecache.swapper*``` 설정을 완료 후 및 Neo4j시작 전에 사용되어야 합니다. 이 명령어는 설정 장치의 데이터를 효과적으로 제거하고, 데이터 베이스에 새로운 파일 시스템을 준비합니다. 

아래의 예는 결과를 나타냅니다.:

```
$neo4j-home> bin/neo4j-admin blockdev format
Done! Device has been formatted with DBFS: /dev/sg1::/dev/sg2
$neo4j-home>
```

## 2.6.3.2 ```ls``` 명령어 

```ls```명령어는 블록 장치에 설정된 파일 리스트를 나타냅니다. 포맷 후, 장치는 비워집니다. 

```
$neo4j-home> bin/neo4j-admin blockdev ls
Total: 0 files, 0 bytes
$neo4j-home>
```

데이터 베이스가 시작도니 후, 몇개의 파일이 생성된 것을 확인할 수 있습니다.:

```
$neo4j-home> bin/neo4j-admin blockdev ls
/neo4j-home/data/databases/graph.db/neostore.nodestore.db.labels            4096 bytes
/neo4j-home/data/databases/graph.db/neostore.nodestore.db                   4096 bytes
/neo4j-home/data/databases/graph.db/neostore.propertystore.db.index.keys    4096 bytes
/neo4j-home/data/databases/graph.db/neostore.propertystore.db.index         4096 bytes
/neo4j-home/data/databases/graph.db/neostore.propertystore.db.strings       4096 bytes
/neo4j-home/data/databases/graph.db/neostore.propertystore.db.arrays        24576 bytes
/neo4j-home/data/databases/graph.db/neostore.propertystore.db               16384 bytes
/neo4j-home/data/databases/graph.db/neostore.relationshipstore.db           12288 bytes
/neo4j-home/data/databases/graph.db/neostore.relationshiptypestore.db.names 4096 bytes
/neo4j-home/data/databases/graph.db/neostore.relationshiptypestore.db       4096 bytes
/neo4j-home/data/databases/graph.db/neostore.labeltokenstore.db.names       4096 bytes
/neo4j-home/data/databases/graph.db/neostore.labeltokenstore.db             4096 bytes
/neo4j-home/data/databases/graph.db/neostore.schemastore.db                 4096 bytes
/neo4j-home/data/databases/graph.db/neostore.relationshipgroupstore.db      4096 bytes
/neo4j-home/data/databases/graph.db/neostore                                4096 bytes
Total: 15 files, 102400 bytes
$neo4j-home>
```

```ls```명령어는 현재 작업 경로와는 관계없이 장치 내 모든 파일의 정확한 경로를 리스트할 것 입니다. 그 이유는 일반적인 파일 시스템과  파일 시스템의 운영 관련이 없고, 연결되어 있지 않기 때문 입니다. 


## 2.6.3.3. ```fsck``` 명령어

```fsck```명령어는 일관성을 확인하기위해서 DBFS 파일 시스템 메타데이터를 체크합니다. CAPI 플래시 데이터베이스 설치 중 일치하지 않은 별개의 데이터가 발견된다면, 그래프 데이터 자체 일관성 검사를 하기전에 이 명령어를 사용하는 것을 권장합니다. 이 ```fsck```명령어는 ```neo4j-admin check-consistency```과 비교했을 때 빠르고지만, ```fsck``` 명령이 실패한다면 의미가 없습니다. 

```fsck```는 다음과 같이 사용합니다.:


```
$neo4j-home> bin/neo4j-admin blockdev fsck
DBFS file system is consistent!
$neo4j-home>
```

```fsck```가 에러를 발견한다면 이는 불행이도 사용자가 고칠 수 없습니다. 대신에, ```fsck``` 결과를 neo4j.conf와 
Neo4j가 지원하는 debug.log를 함께 보내야 합니다. 


##2.6.3.4 ```import``` 명령어

```import```명령어는 존재하는 데이터베이스의 위치를 유지하면서 블록 장치 저장소에 임포트 합니다. 임포트된 파일이 일반적인 파일 시스템에서와 마찬가지로 블록 장치의 경로와 같은 파일 이름을 가지는 것을 의미합니다. 이로써 임포트된 직후에 Neo4j가 데이터 베이스를 시작할 수 있도록 합니다. ```graph.db```데이터비에스에 기본적으로 데이터가 있고, 블록 장치 저장소에 저장한다고 가정해봅시다. 


그러면, ```bin/neo4j-admin blockdev import data/databases/graph.db```명령어를 사용하여 블록 장치 저장소를 사용하도록 Neo4j를 설정한 후 임포트할 수 있습니다. 


```
$neo4j-home> bin/neo4j-admin blockdev import data/databases/graph.db
2016-11-24 17:53:21.994+0000 INFO  [o.n.i.p.PageCache] Configured dbms.memory.pagecache.swapper: capi
Importing...
neostore: 1/1. Done!
neostore.propertystore.db.arrays: 2/2. Done!
neostore.propertystore.db.index.keys: 1/1. Done!
neostore.labeltokenstore.db: 0/0. Done!
neostore.propertystore.db.strings: 1/1. Done!
neostore.nodestore.db: 2/2. Done!
neostore.relationshiptypestore.db.names: 1/1. Done!
neostore.propertystore.db.index: 0/0. Done!
neostore.labeltokenstore.db.names: 1/1. Done!
neostore.nodestore.db.labels: 0/0. Done!
neostore.relationshiptypestore.db: 0/0. Done!
neostore.schemastore.db: 0/0. Done!
neostore.relationshipgroupstore.db: 10/10. Done!
neostore.propertystore.db: 57/57. Done!
neostore.relationshipstore.db: 6074/6074. Done!
100%
Done, PT0.628S.
$neo4j-home>
```

임포트 명령어는 그것의 절차를 퍼센트와 블록으로 나타냅니다. 

##2.6.3.5 ```dump``` 명령어

```dump```명령어는 블록 장치 저장소에 있는 파일의 절대 경로를 사용하고, 이진 내용을 표준 출력 스트림에 기록합니다. 이는
기본적인 내보니기 기능으로 사용가능합니다. 출력을 파일로 입력하는 

 