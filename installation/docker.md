
## 2.5 도커

> 이 섹션에서는 도커 컨테이너에서 Neo4j가 어떻게 작동하는지 살펴봅니다. 


기본적으로 도커는 OS X나 Windows에서 실행되지 않습니다. 도커를 OS X와 Windows에서 작동시키기 위해서 도커 설명서를 참조하십시오. 


### 2.5.1 개요

기본적으로 도커 이미지는 세 가지 원격 접속 포트를 나타냅니다.

+ HTTP : ```7474``` 
+ HTTPS : ```7473```
+ Bolt : ```7687```


또한, 다음 볼륨도 나타냅니다. 호스트 디렉토리를 ```--volume``` 옵션을 사용하여 마운트할 수 있습니다. 다른 배포 Neo4j에서 기본적으로 사용하는 디렉토리의 상세내용은 [섹션 3.1, "File locations"]("https://neo4j.com/docs/operations-manual/current/configuration/file-locations/")에서 확인할 수 있습니다. 

+ /conf
+ /data
+ /import
+ /logs
+ /metrics
+ /plugins
+ /ssl


때로는 데이터베이스와 로그를 컨테이너 밖에 두는 것이 바람직합니다. 다음 명령어는 컨테이너에서 Bolt와 Http 포트를위해 사용될 것 입니다. 그리고, /data와 /logs 볼륨은 호스트 디렉토리에 매핑됩니다. 

```
docker run \
    --publish=7474:7474 --publish=7687:7687 \
    --volume=$HOME/neo4j/data:/data \
    --volume=$HOME/neo4j/logs:/logs \
    neo4j:3.3
```


리눅스의 ```http://localhost:7474```나 OS X의 ```http://$(docker-machine ip default):7474```에 접속하세요.
이 문서의 모든 볼륨은 모두 OS X에서 VM머신에 ```$HOME```이 자동적으로 마운트된 곳에서 작업하기 위해 ```$HOME```하위에 저장됩니다. 리눅스에서 볼륨은 어느 곳에든 저장할 수 있습니다. 

> 기본적으로, Neo4j는 첫 접속 시 새 패스워드를 설정하기 위해 인증이 필요하고 ```neo4j/neo4j```로 로그인 해야합니다. ```--env NEO4J_AUTH=neo4j/<password>```를 지정하여 Docker 컨테이너 비밀번호를 바로 설정할 수 있습니다. 대안으로 ```--env NEO4J_AUTH=none```을 명시하여 인증을 비활성화할 수 있습니다. 

#### 2.5.1.1. 비루트(non-root)로 실행

보안상의 이유로 컨테이너 내부 Neo4j는 ```neo4j``` 유저로 작동할 것 입니다. 도커와 ```--user``` 인수와 함께 실행하여 사용자를 지정할 수 있습니다. 예를 들어, 현재 사용자는 아래와 같이 Neo4j를 실행할 수 있습니다.
```
docker run \
    --publish=7474:7474 --publish=7687:7687 \
    --volume=$HOME/neo4j/data:/data \
    --volume=$HOME/neo4j/logs:/logs \
    --user="$(id -u):$(id -g)" \
    neo4j:3.3
```


## 2.5.2 Neo4j 에디션

테그는 Neo4j 커뮤니티와 엔터프라이즈 버전에서 모두 사용가능합니다. ```neo4j:3.3.0-enterprise```와 같이 특정 엔터프라이즈 버전 테그(tag)는 ```-enterprise```  접미어가 있습니다. ```neo4j:3.3.0```와 같은 커뮤니티 버전 테그는 접미어가 없습니다. 최근 Neo4j 버전은 ```neo4j:enterprise```로 사용할 수 있습니다. 


#### 2.5.2.1 Neo4j 에디션 라이센스

Neo4j 에디션을 이용하기 위해서 반드시 라이센스에 동의를 해야합니다. 

> (c) Network Engine for Objects in Lund AB. 2017. 무단 복제 금지. Neo4j 또는 상업 라이센스가 없는 경우 이 소포트웨어 사용이 금지됩니다. 
> 이메일 관련 문의 : [licensing@neo4j.com]("licensing@neo4j.com")
> 더 많은 정보 관련 문의 : [https://neo4j.com/licensing/]("https://neo4j.com/licensing/")

라이센스에 동의하려면,  ```NEO4J_ACCEPT_LICENSE_AGREEMENT=yes``` 환경 변수를 설정해야 합니다. 이를 통해 다음 도큐먼트 인수를 사용할 수 있습니다. :

```--env=NEO4J_ACCEPT_LICENSE_AGREEMENT=yes```


## 2.5.3 도커 환경 설정

이미지의 기본 환경설정은 Neo4j을 학습하기 위해서 제작되었지만, 생산용으로 적합하도록 수정해야 합니다. 특히, Neo4j에 할당된 메모리는 다수의 컨테이너가 같은 서버에서 시작되어야 하기에 제한적 입니다. (아래 ```NEO4J_CACHE_MEMORY```와 ```NEO4J_HEAP_MEMORY```를 참조하시오.) 자세한 내용은 [섹션 A.1, 설정 세팅]("https://neo4j.com/docs/operations-manual/current/reference/configuration-settings/")를 참조하시오. 

설정을 수정하는 세 가지 방법이 있습니다. 
+ 환경 변수 설정
+ /conf 볼륨 마운트
+ 새 이미지 빌드

이미지 커스터마이징 분량을 기준으로 선택합니다. 

#### 2.5.3.1 환경 변수 

실행시킬 때 환경 변수를 컨테이너에 전송하십시오.

```
docker run \
    --detach \
    --publish=7474:7474 --publish=7687:7687 \
    --volume=$HOME/neo4j/data:/data \
    --volume=$HOME/neo4j/logs:/logs \
    --env=NEO4J_dbms_memory_pagecache_size=4G \
    neo4j:3.3

```


모든 설정 값은 (참조[섹션 A.1 설정세팅]("https://neo4j.com/docs/operations-manual/current/reference/configuration-settings/")) 다음 명명 제도를 이용해 전송할 수 있습니다.  

+ ```NEO4J_``` 접두어.
+ ```_```는 반드시 두번 사용되어야 합니다. : ```_```는 ```__```로 대체.
+ 점은 _로 대체합니다. : ```.```는 ```_```로 대체.


예를 들어서 ```dbms.tx_log.rotation.size```는 다음 도커 값을 명시화해서 설정할 수 있습니다. 

```
--env=NEO4J_dbms_tx__log_rotation_size
```

```dbms_jvm_additional```와 같이 다양한 옵션을 택하는 변수는 한 번만 정의해야하고, 여러 값 연결을 포함해야 합니다. 
예를 들면:

```
--env=NEO4J_dbms_jvm_additional=-Dcom.sun.management.jmxremote.authenticate=true,-Dcom.sun.management.jmxremote.ssl=false,-Dcom.sun.management.jmxremote.password.file=$HOME/conf/jmx.password,-Dcom.sun.management.jmxremote.access.file=$HOME/conf/jmx.access,-Dcom.sun.management.jmxremote.port=3637
```

### Neo4j 앤터프라이즈 버전 

남은 섹션에서는 Neo4j 앤터프라이즈 버전에서 쓰는 제어 설정 컨트롤에 대해 다룹니다.  

+ ```NEO4J_dbms_mode```: 데이터베이스는 기본 ```SINGLE```모드로 설정되고, 일반적인 클러스터링으로는 ```CORE```이나 ```READ_REPLICA```로 설정되고, 하이 클러스터링은 ```HA```로 설정됩니다.

####일반적인 클러스터 설정

+ ```NEO4J_causal__clustering_expected__core__cluster__size``` : 초기 클러스터 사이즈(코어 인스턴스 개수).

+ ```NEO4J_causal__clustering_initial__discovery__members``` : 초기 핵심 클러스터 멤버의 네트워크 주소.

+ ```NEO4J_causal__clustering_discovery__advertised__address``` : 멤버 검색 관리 통신을 알려주는 호스트이름/IP 주소 및 포트 번호. 

+ ```NEO4J_causal__clustering_transaction__advertised__address``` : 트랜잭션 처리를 알려주는 호스트이름/IP 주소 및 포트 번호.

+ ```NEO4J_causal__clustering_raft__advertised__address``` : 클러스터 통신을 알려주는 호스트이름/IP 주소 및 포트 번호.


클러스팅 설정을 다루기 위한 주소와 포트는 아래를 참조하면 됩니다. 


####고 가용성 클러스터 설정

+ ```NEO4J_ha_server__id``` : 서버 아이디는 클러스터 에서 고유한 값이어야 딥니다. 

+ ```NEO4J_ha_host_coordination``` : HA 모드에서 클러스터 조정에 쓰이는 주소(포트를 포함하여)는, 모든 클러스터 멤버가 접근할 수 있어야 합니다. 

+ ```NEO4J_ha_host_data``` : HA 모드에서 데이터 전송에 쓰이는 주소는(포트 포함), 모든 클러스터 멤버가 접근할 수 있어야 합니다. 

+ ```NEO4J_ha_initial__hosts``` : 쉼표로 구분 된 클러스터의 다른  구성원 목록 
 


HA 클러스터 환경 설정의 예는 아래에서 볼 수 있습니다. 


####2.5.3.2 ./conf 볼륨

Neo4j 설정을 임의로 수정하려면, 컨테이너를 /conf와 함께 제공하면 됩니다. 

```
docker run \
    --detach \
    --publish=7474:7474 --publish=7687:7687 \
    --volume=$HOME/neo4j/data:/data \
    --volume=$HOME/neo4j/logs:/logs \
    --volume=$HOME/neo4j/conf:/conf \
    neo4j:3.3

```

/conf 볼륨의 설정 파일 이미지가 제공한 파일을 오버라이드할 것 입니다. 그렇기 때문에, 파일에서 하나의 값을 바꾸고 싶다면 나머지 파일이 완전한지 확인해야 합니다. 도커가 컨테이너에 전송한 환경 변수는 /conf 볼륨 내 설정 파일 값을 오버라이드할 것 입니다. 


> 환경 볼륨을 사용한다면, 모든 네트워크 인터페이스에서 수신 대기해야 합니다. 이것은 ```dbms.connectors.default_listen_address=0.0.0.0```을 이용해 설정할 수 있습니다. 


초기 환경 파일 묶음을 덤프하려면 ```dump-config``` 명령어를 이미지와 작동시키면 됩니다. 

```
  docker run --rm \
    --volume=$HOME/neo4j/conf:/conf \
    neo4j:3.3 dump-config
```


####2.5.3.3. 새 이미지 빌드

더욱 복잡한 사용자 정의를 이용하려면 아래를 토대로 새 이미지를 생성할 수 있습니다. 

```
FROM neo4j:3.3
```

개별 설정할 경우, 이를 스크렙트에서 적용할 수 있는 후크를 제공합니다. 

```
COPY extra_conf.sh /extra_conf.sh
```

```EXTENSION_SCRIPT``` 환경 변수전달하면 실행 중에 스크립트를 소스로 사용할 수 있습니다. 

```
docker run -e "EXTENSION_SCRIPT=/extra_conf.sh" cafe12345678
```

확장 스크립트가 소스로 변경되면 현재 작업 디렉토리는 Neo4j 설치 루트가됩니다.

####2.5.3.4. Neo4j 업그레이드

업그레이드를 하려면, ```NEO4J_dbms_allow__upgrade```을 ```참```으로 설정하면 됩니다. 업그레이드 관련 자세한 내용은 아래를 참고하면 됩니다.:

+ [싱글 인스턴스 업그레이드]("https://neo4j.com/docs/operations-manual/current/upgrade/deployment-upgrading/")
+ [Neo4j 일반 클러스터 업그레이드]("https://neo4j.com/docs/operations-manual/current/upgrade/causal-cluster/")
+ [Neo4j HA 클러스터 업그레이드]("https://neo4j.com/docs/operations-manual/current/upgrade/high-availability/")


###2.5.4. Neo4j 일반 클러스터 모드

Docker에서 CC모드로 Neo4j를 작동하려면 서로 통신하도록 클러스터 컨테이너를 연결해야 됩니다. 각 컨테이너에서 서로의 네트워크 루트 및 ```NEO4J_causal__clustering_expected__core__cluster__size```와 ```NEO4J_causal__clustering_initial__discovery__members```를 가져야되고 환경 변수 코어를 설정 해야합니다. 읽기 복제본은 ```NEO4J_causal__clustering_initial__discovery__members```을 유지해야 합니다. 

단일 Docker 호스트 내에서는 아래와 같이 실행됩니다. HTTP, HTTPS와 Bolt가 기본 포트 입니다. 각 컨테이너에서 이 포트들은 
Docker 호스트의 다른 포트에 매핑됩니다. 
```
docker network create --driver=bridge cluster

docker run --name=core1 --detach --network=cluster \
    --publish=7474:7474 --publish=7473:7473 --publish=7687:7687 \
    --env=NEO4J_dbms_mode=CORE \
    --env=NEO4J_causal__clustering_expected__core__cluster__size=3 \
    --env=NEO4J_causal__clustering_initial__discovery__members=core1:5000,core2:5000,core3:5000 \
    --env=NEO4J_ACCEPT_LICENSE_AGREEMENT=yes \
    neo4j:3.3-enterprise

docker run --name=core2 --detach --network=cluster \
    --publish=8474:7474 --publish=8473:7473 --publish=8687:7687 \
    --env=NEO4J_dbms_mode=CORE \
    --env=NEO4J_causal__clustering_expected__core__cluster__size=3 \
    --env=NEO4J_causal__clustering_initial__discovery__members=core1:5000,core2:5000,core3:5000 \
    --env=NEO4J_ACCEPT_LICENSE_AGREEMENT=yes \
    neo4j:3.3-enterprise

docker run --name=core3 --detach --network=cluster \
    --publish=9474:7474 --publish=9473:7473 --publish=9687:7687 \
    --env=NEO4J_dbms_mode=CORE \
    --env=NEO4J_causal__clustering_expected__core__cluster__size=3 \
    --env=NEO4J_causal__clustering_initial__discovery__members=core1:5000,core2:5000,core3:5000 \
    --env=NEO4J_ACCEPT_LICENSE_AGREEMENT=yes \
    neo4j:3.3-enterprise
```

추가 인스턴스는 임시 모드로 클러스터에 추가될 수 있습니다. 읽기 복제본은 아래와 같이 추가 가능합니다.:

```
docker run --name=read_replica1 --detach --network=cluster \
         --publish=10474:7474 --publish=10473:7473 --publish=10687:7687 \
         --env=NEO4J_dbms_mode=READ_REPLICA \
         --env=NEO4J_causal__clustering_initial__discovery__members=core1:5000,core2:5000,core3:5000 \
         --env=NEO4J_ACCEPT_LICENSE_AGREEMENT=yes \
         neo4j:3.3-enterprise
```

실제 컴퓨터에서 각 컨테이너가 실행 중이고 Docker 네트워크가 사용되지 않을 경우 컴퓨터간 통신할 수 있도록 주소를 정의해야 됩니다. 또한, 각 컨테이너를 호스트 네트워크에 연결해야 합니다. 

각 인스턴스는 다음과 비슷하게 호출됩니다.:

```
docker run --name=neo4j-core --detach \
         --network=host \
         --publish=7474:7474 --publish=7687:7687 \
         --publish=5000:5000 --publish=6000:6000 --publish=7000:7000 \
         --env=NEO4J_dbms_mode=CORE \
         --env=NEO4J_causal__clustering_expected__core__cluster__size=3 \
         --env=NEO4J_causal__clustering_initial__discovery__members=<core1-public-address>:5000,<core2-public-address>:5000,<core3-public-address>:5000 \
         --env=NEO4J_causal__clustering_discovery__advertised__address=<public-address>:5000 \
         --env=NEO4J_causal__clustering_transaction__advertised__address=<public-address>:6000 \
         --env=NEO4J_causal__clustering_raft__advertised__address=<public-address>:7000 \
         --env=NEO4J_dbms_connectors_default__advertised__address=<public-address> \
         --env=NEO4J_ACCEPT_LICENSE_AGREEMENT=yes \
         neo4j:3.3-enterprise
```

이는 ```<public-address>``` 가 공공 호스트 이름이거나 머신 ip-주소입니다. 더 많은 정보는[섹션 4.2.3, 새로운 일상 클러스터 제작]("https://neo4j.com/docs/operations-manual/current/clustering/causal-clustering/setup-new-cluster/")에서 확인할 수 있습니다. 
    

### 2.5.5 Neo4J 고 가용성 모드 

도커 내 Neo4j를 HA모드에서 작동하려면 서로 통신하도록 컨테이너 클러스터를 연결해야 합니다. 각 컨테이너는 다른 컨테이너에 대한 네트워크 경로와 ```NEO4J_ha_host_coordination```, ```NEO4J_ha_host_data``` 그리고 이에 맞는 환경 변수가 설정되어야 합니다. 

 단일 도커(Docker)는 호스트 내에서 다음과 같이 실행됩니다. 

```
docker network create --driver=bridge cluster

docker run --name=instance1 --detach --publish=7474:7474 --publish=7687:7687 --net=cluster --hostname=instance1 \
    --volume=$HOME/neo4j/logs1:/logs \
    --env=NEO4J_dbms_mode=HA --env=NEO4J_ha_server__id=1 \
    --env=NEO4J_ha_host_coordination=instance1:5001 --env=NEO4J_ha_host_data=instance1:6001 \
    --env=NEO4J_ha_initial__hosts=instance1:5001,instance2:5001,instance3:5001 \
    --env=NEO4J_ACCEPT_LICENSE_AGREEMENT=yes \
    neo4j:3.3-enterprise

docker run --name=instance2 --detach --publish=7475:7474 --publish=7688:7687 --net=cluster --hostname=instance2 \
    --volume=$HOME/neo4j/logs2:/logs \
    --env=NEO4J_dbms_mode=HA --env=NEO4J_ha_server__id=2 \
    --env=NEO4J_ha_host_coordination=instance2:5001 --env=NEO4J_ha_host_data=instance2:6001 \
    --env=NEO4J_ha_initial__hosts=instance1:5001,instance2:5001,instance3:5001 \
    --env=NEO4J_ACCEPT_LICENSE_AGREEMENT=yes \
    neo4j:3.3-enterprise

docker run --name=instance3 --detach --publish=7476:7474 --publish=7689:7687 --net=cluster --hostname=instance3 \
    --volume=$HOME/neo4j/logs3:/logs \
    --env=NEO4J_dbms_mode=HA --env=NEO4J_ha_server__id=3 \
    --env=NEO4J_ha_host_coordination=instance3:5001 --env=NEO4J_ha_host_data=instance3:6001 \
    --env=NEO4J_ha_initial__hosts=instance1:5001,instance2:5001,instance3:5001 \
    --env=NEO4J_ACCEPT_LICENSE_AGREEMENT=yes \
    neo4j:3.3-enterprise

```


Neo4j 고 가용성 모드에 대한 자세한 내용은 [섹션 B.2, "고 이용 클러스터 설정"]("https://neo4j.com/docs/operations-manual/current/tutorial/highly-available-cluster/")에서 확인할 수 있습니다. 


### 2.5.6. 사용자 정의 절차

 사용자 정의를 설치하기 위해서 jars를 포함하는 플러그인 볼륨을 추가하면 됩니다. 

```
docker run --publish=7474:7474 --publish=7687:7687 --volume=$HOME/neo4j/plugins:/plugins neo4j:3.3
```

 절차 관련 자세한 내용은 [개발자 메뉴얼 -> 절차]("https://neo4j.com/docs/developer-manual/3.4-preview/extending-neo4j/procedures/")을 확인하면 됩니다. 

### 2.5.7. 사이퍼 쉘(Cypher Shell)

 Neo4j 쉘은 다음 명령어는 부분적으로 사용할 수 있습니다. 

```
docker exec --interactive --tty <container> bin/cypher-shell
```

### 2.5.8 암호화 

도커 이미지는 Neo4j의 TLS 지원을 나타냅니다. 개별 키와 증명서를 사용하려면, /ssl 볼륨을 키와 증명서에 함께 제공하면 됩니다. 파일은 반드시 neo4j.key 와 neo4j.cert로 저장해야 합니다. 또한, HTTPS 앤드포인트 ```7043```포트에 연결해야 합니다.

```
 docker run --publish=7473:7473 --publish=7687:7687 --volume=$HOME/neo4j/ssl:/ssl neo4j:3.3
>>>>>>> 34b2045839a80e64ab414ab7611da444fd0a67f6
```