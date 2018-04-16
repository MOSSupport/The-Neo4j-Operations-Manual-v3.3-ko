
## 2.3 Mac OS 설치

> 이 섹션에서는 Mac OS에서 Neo4j가 어떻게 작용되는지 살펴봅니다. 


Neo4j는 콘솔 어플리케이션 또는 서비스로 운영됩니다. 
개발하는 경우, Neo4j는 Neo4j 데스크톱으로 작동되기도 합니다.

### 2.3.1 Neo4j 데스크톱

   [http://neo4j.com/download/]("https://neo4j.com/download/") 에 접속한 후, 컴퓨터에서 Neo4j 데스크톱 지시 사항을 따라 가십시오.

### 2.3.2 Unix 콘솔 어플리케이션

  1. 최근 출시된 버전을 [http://neo4j.com/download/]("https://neo4j.com/download/")에서 다운 받으십시오.
    개인 플랫폼에 맞는 tar.ge 배포판을 선택하십시오.

  2. ```tar _xf <filename>```을 사용해서 압축을 풉니다.
     추출된 상위 디렉토리는 다음과 같이 참조하세요. : NEO4J_HOME 

  3. 경로를 다음과 같이 바꾸세요 : ```$NEO4J_HOME```
      ```./bin/neo4j console```을 실행하세요.

  4. ```Ctrl-C```를 콘솔창에 입력하여, 서버를 멈추세요.

Neo4j가 콘솔 모드를일 때, 로그는 터미널에 찍힙니다. 

### 2.3.3 Mac OS 서비스

```neo4j``` 명령어 기반 서비스를 만들기 위해서 기본 Mac OS 시스템 도구를 사용하세요.

