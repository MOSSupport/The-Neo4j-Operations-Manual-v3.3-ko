## 2.2.2. RPM

> 이 섹션에서는 Neo4j RPM패키지를 사용하여 Neo4j를 Red Hat, CentOS, Fedora, 또는 Amazon 리눅스에서 배포하는 방법에 대해 알아봅니다. 


### 2.2.2.1. Red Hat/ CentOS / Fedora

 Red Hat, CentOS, Fedora, 그리고 Amazon 리눅스를 배포하는 경우 그 단계는 다음과 같습니다. 

 1.다음을 ```root```로 사용하여 저장소에 추가합니다. 

```
 rpm --import http://debian.neo4j.org/neotechnology.gpg.key
cat <<EOF>  /etc/yum.repos.d/neo4j.repo
[neo4j]
name=Neo4j RPM Repository
baseurl=http://yum.neo4j.org/stable
enabled=1
gpgcheck=1
EOF
```

2. Neo4j를 설치합니다.

+ Neo4j 커뮤니티 배포판을 ```root```로 설치:

```
yum install neo4j-3.3.5
```

+ Neo4j 기업용 배포판을 ```root```로 설치:

```
yum install neo4j-enterprise-3.3.5
```

### 비상호 Neo4j 기업용 배포판 설치

Neo4j 기업용 배포판을 설치할 때 설치가 완료되기 전에 라이센스 계약에 동의해야 합니다. 이것은 대화식 프롬프트 입니다. 

Neo4j 기업용 배포판을 비대화 형으로 설치할 경우 환경 변수 ```NEO4J_ACCEPT_LICENSE_AGREEMENT```을 ```yes```로 설정하여 계약을 읽고 동의했음을 나타낼 수 있습니다

```
NEO4J_ACCEPT_LICENSE_AGREEMENT=yes yum install neo4j-enterprise-3.3.5
```

### 2.2.2.2. SUSE

> SUSE는 상업용으로 인증되지 않았습니다. 이 내용은 비상업용으로 Neo4j를 사용하는 사람들을 위해 제공됩니다. 

SUSE-기반 배포 단계는 다음과 같습니다. 

1. 아래 내용을 저장소의 ```root```로 사용합니다. 
```
zypper addrepo --refresh http://yum.neo4j.org/stable neo4j-repository
```

2. Neo4j를 설치합니다. 

+ Neo4j 커뮤니티 버전을 ```root```로 설치:
```
zypper install neo4j-3.3.5
```

+ Neo4j 기업용 버전을 ```root```로 설치:
```
zypper install neo4j-enterprise-3.3.5
```

### 2.2.2.3. 업그레이드

 Neo4j의 3.x 버전을 Neo4j 3.3.5로 업그레이드 하려면 [챕터 5장 업그레이드](../../upgrade/upgrade-planning.md) 내용을 참조하면 됩니다. 
