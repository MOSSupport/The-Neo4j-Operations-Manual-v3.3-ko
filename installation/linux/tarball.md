
## 2.2.4. Linux tarbell 설치 

> 이 섹션에서는 tarball에서 Linux에 Neo4j를 설치하고 콘솔 어플리케이션 또는 서비스로 운영하는 방법에 대해 알아봅니다. 


### 2.2.4.1. Unix 콘솔 어플리케이션

1. 최신 버전을 [https://neo4j.com/download/other-releases/#releases](https://neo4j.com/download/other-releases/#releases)에서 다운로드 합니다. 플랫폼에 알맞은 tar.gz 배포판을 선택합니다.

2. ```tar -xf <filename>```을 사용하여, 아카이브 내용 압축을 풉니다. 추출 된 최상위 디렉토리를 다음과 같이 참조합니다.: NEO4J_HOME


3. 디렉토리 변경 : ```$NEO4J_HOME```
   ```./bin/neo4j console``` 실행

4. 콘솔에 ```Ctrl-C```를 입력하여 서버를 중단합니다.


### 2.2.4.2. Linux 서비스

Neo4j를 시스템 서비스로 운영하려면, [Debian](debian.md) 또는 [RPM](rpm.md) 패키지 중 하나를 설치하면 됩니다. 

### 2.2.4.3. 오픈 파일 개수 설정

Linux 플랫폼은 유저가 열어 놓은 동시 파일 수 상한을 정합니다. 이 숫자는 ```ulimit -n``` 명령어를 사용하여 현재 사용자 및 세션을 함께 나타냅니다.:

```
user@localhost:~$ ulimit -n
1024
```

일반적으로 기본값 1024는 충분하지 않습니다. 특히 많은 인덱스가 사용되거나 서버 설치에 많이 연결되어 있을 때 그렇습니다. 네트워크 소켓 한계도 마찬가지입니다. 따라서 유저에게는 사용 패턴을 고려하여 한도를 40,000 또는 그 이상으로 늘릴 것을 권장합니다. ```ulimit```명령어를 사용하여 한계치를 설정할 수 있지만 root 사용자에게만 해당되고 현재 세션에만 적용됩니다. 시스템 전체 값을 설정하려면 플랫폼에 맞는 아래 지시 사항을 따르면 됩니다. 

다음은 Ubuntu 10.04 이후 버전에서 neo4j 유저에 대해 오픈 파일 설명서 한계를 40 000로 설정됩니다. 

> 만약 Neo4j 서비스를 다른 사용자로 사용한다면, 첫 필드를 2 단계와 같이 적절하게 변경해야 합니다. 


1. 이후의 모든 작업은 보호 된 시스템 파일을 편집해야하므로 루트가됩니다.

```
user@localhost:~$ sudo su -
Password:
root@localhost:~$
```


2. ```/etc/security/limits.conf```을 편집하고 다음 두 줄을 추가합니다. 

```
neo4j   soft    nofile  40000
neo4j   hard    nofile  40000
```

3. ```/etc/pam.d/su```을 편집하고 주석 처리를 제거하거나 다음 행을 추가합니다. 

```
session    required   pam_limits.so

```

4. 설정을 완료하려면 재시작해야 합니다. 

위 단계가 끝나면 Neo4j 유저는 40 000개의 유사한 오픈 파일을 가집니다. ```Too many open files``` 또는 ```Could not stat() directory```와 같은 예외처리가 밠애한다면, 한계치를 늘려야할 수도 있습니다. 

