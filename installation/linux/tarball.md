
## 2.2.4. Linux tarbell 설치 

> 이 섹션에서는 tarball로부터  Neo4j를 리눅스에 설치하는 방법과 콘솔 어플리케이션이나 서비스로 운영하는 방법에 대하여 알아봅니다. 


### 2.2.4.1. Unix 콘솔 어플리케이션

1. 최신 버전을 [https://neo4j.com/download/other-releases/#releases](https://neo4j.com/download/other-releases/#releases)에서 다운로드 합니다. 플랫폼에 알맞은 tar.gz 배포판을 선택합니다.

2. ```tar -xf <filename>```을 사용하여, 기록 내용을 압축합니다. 상위 레벨에 압축덴 디렉토리를 : NEO4J_HOME으로 가리킵니다.

3. 디렉토리 변경 : ```$NEO4J_HOME```
   ```./bin/neo4j console``` 시작

4. 콘솔에 ```Ctrl-C```를 입력해 서버를 중단시킵니다. 


### 2.2.4.2. Linux 서비스

Neo4j를 시스템 서비스로 운영하려면, [Debian](debian.md) 또는 [RPM](rpm.md) 패키지를 대신 설치하면 됩니다. 

### 2.2.4.3. 오픈 파일 개수 설정

Linux 플랫폼은 유저가 열어봤을 현재 파일에 대한 상위 한계 개수를 나타냅니다. 이 숫자는 현재 사용자와 ```ulimit -n``` 명령어의 세션을 함께 나타냅니다.:

```
user@localhost:~$ ulimit -n
1024
```

종종 기본 사용인 1024는 부족합니다. 특히 많은 인덱스가 사용되거나 서버 설치가 많이 연결되어 있을 때 그렇습니다. 네트워크 소켓 개수 한계치 또한 마찬가지입니다. 유저는 그래서 값을 사용 패턴을 고려하여 40 000또는 그 이상으로 늘릴 것을 권장됩니다. ```ulimit```명령어를 사용하여 한계치를 설정할 수 있지만, root 사용자에게만 해당하고 현재 세션에만 적용됩니다. 값을 시스템에 포괄적으로 적용하려면 플랫폼에 맞는 아래 지시를 따르면 됩니다. 

오픈 파일 기술어 한계를 유저를 Ubuntu 10.24 또는 이후 버전 위해 40 000로 설정됩니다. 


> 만약 Neo4j 서비스를 다른 사용자로 사용한다면, 첫 필드를 두번째 단계와 같이 변경해야 합니다. 


1. 

이후의 모든 작업은 보호 된 시스템 파일을 편집해야하므로 루트가됩니다.

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

