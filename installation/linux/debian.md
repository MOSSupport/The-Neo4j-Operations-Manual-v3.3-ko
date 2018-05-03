## 2.2.1. Deian

> 이 섹션에서는 Neo4j를 Debian에 설치하는 방법과 Debian 패키지를 사용하여 우분투와 같은 Deian기반 배포판을 설치하는 방법에 대해 알아봅니다. 

### 2.2.1.1. 설치

Neo4j를 Debian에 설치하려면 아래의 내용을 확인해야 합니다.:

+ Java 8 런타임 설치 완료.
+ 패키지 관리자는 Neo4j Debian 패키지를 가지고 있는 저장소를 알아야 합니다. 

### 전제 조건(우분투 14.04와 Debian 8만 가능함)

Neo4j 3.3은 Java 8 운영체제를 필요로 합니다. Java 8는 우분투 14.04 LTS나 Deian 8(jessie)에 포함되어 있지않고 아래 설명 된대로 이 Neo4j 3.3을 설치하거나 업그레이드 하기 전에 수동으로 설치해야 합니다. Debian 사용자는 [백 포트](https://packages.debian.org/jessie-backports/openjdk-8-jdk)에서 오픈JDK8을 찾을 수 있습니다. 

### Debian 8의 Java 8

+ ```deb http://httpredir.debian.org/debian jessie-backports main```라인을 ".list" 확장자가 있는 파일 /etc/apt/sources.list.d/에 추가해야합니다. 그 후 ```apt-get update```을 실행합니다. 

```
echo "deb http://httpredir.debian.org/debian jessie-backports main" | sudo tee -a /etc/apt/sources.list.d/jessie-backports.list
sudo apt-get update
```

+ Java 8을 Neo4j와 설치하려면, 먼저 ```ca-certificates-java``` 패키지를 설치해야 합니다.:

```
sudo apt-get -t jessie-backports install ca-certificates-java
```

이제 Neo4j 3.3.5를 설치할 수 있습니다. 설치되지 않은 경우 자동으로 Java 8을 설치합니다. [섹션 "설치된 여러 Java 버전 다루기"](linux/debian.md)를 참조하여 설치 후 Neo4j를 시작할 수 있는지 확인합니다. 

### 우분투 14.04의 자바 8

+ 우분투 14.04의 사용자는 WebUpd8을 통해서 오라클 Java 8을 추가할 수 있습니다. WebUp8 또는 다른 PPA에서 설치하려면 Neo4j 설치 전에 Java 8을 수동으로 설치해야 합니다. 그렇지 않으면 Neo4j와 호환되지 않는 Java 9이 설치될 수도 있습니다. 

```
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java8-installer
```

+ 설치되면 [섹션 "설치된 다양한 자바 버전 다루기"](linux/debian.md)를 참조하여 설치 후 Neo4j를 시작할 수 있는지 확인합니다. 

### 설치된 여러 Java 버전 다루기 

기본 자바 버전을 Java 8로 설정하는 것이 중요합니다. 그렇지 않으면, Neo4j 3.3.5는 실행되지 않을 것 입니다. 이 작업은 ```update-java-alternatives```명령어로 할 수 있습니다. 

+ 먼저 설치된 모든 Java 버전을 ```update-java-alternatives --list```로 리스트 합니다. 
결과는 다를 수 있지만 보통 다음과 같이 출력됩니다.

```
java-1.7.0-openjdk-amd64 1071 /usr/lib/jvm/java-1.7.0-openjdk-amd64
java-1.8.0-openjdk-amd64 1069 /usr/lib/jvm/java-1.8.0-openjdk-amd64
```

+ Java 8 버전을 먼저 확인해야 합니다. 이 경우에는 ```java-1.8.0-openjdk-amd64```입니다. 그 후 다음 기본값으로 설정하면 됩니다. (위에서 ```<java8name>```을 적당한 이름으로 대체합니다.)

```
sudo update-java-alternatives --jre --set <java8name>
```

###저장소 추가

+ Debian 패키지는 [https://debian.neo4j.org](https://debian.neo4j.org)에서 다운받을 수 있습니다. 저장소를 사용하려면 아래 단계를 따라야 합니다. 

```
wget -O - https://debian.neo4j.org/neotechnology.gpg.key | sudo apt-key add -
echo 'deb https://debian.neo4j.org/repo stable/' | sudo tee -a /etc/apt/sources.list.d/neo4j.list
sudo apt-get update
```

### 설치

Neo4j 커뮤니티 배포판 설치:

```
sudo apt-get install neo4j=1:3.3.5
```

Neo4j 기업용 배포판 설치:

```
sudo apt-get install neo4j-enterprise=1:3.3.5
```

> **패키지 버전.** Neo4j ```3.3.3```버전 이후로는 데비안 패키지 버전에 epoch 버전 구성 요소 (```1:```)가 포함되어 있습니다. 이 구성요소는 [Debian 버전 정책](https://www.debian.org/doc/debian-policy/#s-f-version)에 맞춰서 관리됩니다. Neo4j 3.1를 ```3.3.2```로 패치업하려면 epoch없이 버전을 지정하면 됩니다. 예시 : ```sudo apt-get install neo4j=3.3.2```.


Neo4j 기업용 배포판을 설치할 때, 사용권 계약에 동의하라는 메시지가 나타납니다. 라이센스에 동의하면 설치가 시작됩니다.  라인센스 동의에 대한 답변은 추후 같은 시스템에 설치를 위해 기억될 것 입니다. 
 
저장된 응답을 잊어 버리고 다음에 설치할 때 사용권 계약 프롬프트를 실행하려면 ```debconf-communicate```를 사용하여 저장된 응답을 제거해야 합니다. 

```
echo purge | sudo debconf-communicate neo4j-enterprise
```


### 비상호적인(non-interactive) Neo4j 기업용 배포판 설치

Neo4j 기업용 배포판의 경우 라이센스 계약은 대화식 프롬프트로 제공됩니다. Neo4j Enterprise Edition을 비대화 형으로 설치할 경우 ```debconf-set-selections```를 사용하여 사용권 계약을 읽고 동의했음을 나타낼 수 있습니다.

```
echo "neo4j-enterprise neo4j/question select I ACCEPT" | sudo debconf-set-selections
echo "neo4j-enterprise neo4j/license note" | sudo debconf-set-selections
```

### 2.2.1.2. 업그레이드

Neo4j 3.x 버전을 3.3.5로 업그레이드하려면 [쳅터 5. 업그레이드](../upgrade.md)지시 사항을 참조하면 됩니다. 
아래의 요약은 Neo4j 2.3을 3.3.5로 업그레이드할 때 필요합니다. 

### Neo4j 2.3에서 업그레이드

Neo4j 데비안/우분트를 설치할 때 3가지 단계가 있습니다. 

1. 구성 파일을 옮겨야 합니다. 
2. 데이터베이스는 임포트되어야 합니다. 
3. 데이터베이스 저장소 포맷을 업그레이드 해야 합니다. 


### 설정 파일 옮기기 

설정 파일은 Neo4j 3.3.5에서 변경되었습니다. 설정 파일을 수정하지 않았을 때 Debian 패키지는 더이상 필요하지 않은 파일을 삭제하고 이전 기본 파일을 새 파일로 대체합니다. 

2.3 설치에서 환경 변수 값을 변경했다면 제공된 구성 마이그레이션 도구를 사용할 수 있습니다. config migrator에는 소스와 대상에 관련된 conf / 디렉토리의 위치를 ​​알려주는 두 개의 인수가 제공됩니다. Debian 패키지 파일 시스템을 레이아웃하기 위해서 위 두개 모두 제공되어야 합니다. 

Neo4j파일과 디렉토리는 Debian의 ```neo4``` 유저와 ```adm```그룹이 소유하기 때문에 ```sudo```을 사용하여 사용 권한을 유지해야 합니다. 

```
sudo -u neo4j -g adm java -jar /usr/share/neo4j/bin/tools/2.x-config-migrator.jar /var/lib/neo4j /var/lib/neo4j
```

### Neo4j 2.3 데이터베이스를 Neo4j 3.3.5로 임포트

데이터 베이스 위치는 Neo4j3.3.5에서 변경되었습니다. Neo4j 2.3 데이터베이스는 Neo3j 3.3.5로 임포팅되어야 합니다. 이 작업은 ```neo4j-admin import```명령어를 사용하면 됩니다. 

예를 들어 데이터 베이스 ```graph.db```(Neo4j 2.3 기본 데이터 베이스 이름)를 임포트 하려면 아래 명령어를 사용하면 됩니다.:

```
sudo -u neo4j neo4j-admin import --mode=database --database=graph.db --from=/var/lib/neo4j/data/graph.db
```

이 명령어는 ```/var/lib/neo4j/data/graph.db```에 위치한 데이터베이스를 Neo4j 3.3.5로 임포트하고 ```graph.db```라고 합니다.

데이터 베이스를 가져오고 업그레이그가 되었다면 이전 데이터 베이스를 안전하게 제거할 수 있습니다.  

### Neo4j 2.3 데이터베이스를 Neo4j 3.3.5로 이동

이전 임포트 단계에서 데이터베이스가 이전 디스크 위치에서 새로운 곳으로 옮겨졌지만 저장소 형식은 업그레이드되지 않앗습니다. 
이 작업을하려면 데이터 베이스 포맷을 최신 버전으로 마이그레이션하는 옵션을 사용하여 데이터 베이스 서비스를 시작해야합니다.

*neo4j.conf*에서 ```dbms.allow_upgrade=true``` 옵션 주석처리를 제거해야 합니다. 아래 명령어를 사용하여 기본 구성으로 처리된 경우에는 해당 위치에서 줄을 변경할 수 있습니다. 

```
sudo sed -i 's/#dbms.allow_upgrade=true/dbms.allow_upgrade=true/' /etc/neo4j/neo4j.conf
```

데이터 베이스 서비스를 포맷 이동 옵션 활성화한 상태에서 시작하면 형식 이동이 바로 시작될 것 입니다. 

```
sudo service neo4j start
```

### 2.2.1.3. 파일 위치

모든 Neo4j 패키지의 파일 위치는 [이곳](../configuration/file-locations.md)에서 확인 가능합니다. 


### 2.2.1.4. 작동

대부분 Neo4j 환경 설정은 [neo4j.conf](../configuration/file-locations.md)에서 가능합니다. 일부 특화된 패키지 옵션은 /etc/default/neo4j에 설정되어 있습니다. 

| 환경 변수                | 기본 값 | 상세 사항                                                    |
| ------------------------ | ------- | ------------------------------------------------------------ |
| *NEO4J_SHUTDOWN_TIMEOUT* | *120*   | Neo4j가 멈출 때까지 기다리는 시간 (초) 보다 오래 걸리면 종료가 실패한 것으로 간주합니다. 시스템이 장기간 운영 트랜잭션을 제공 할 경우 값을 늘릴수도 있습니다. |
| *NEO4J_ULIMIT_NOFILE*    | *60000* | Neo4j 프로세스에서 열 수있는 최대 파일 핸들 수입니다.        |
