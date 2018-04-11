##3.6. Neo4j 커넥터 환경 설정 

> 이 섹션에서는 Neo4j 커넥터 환경 설정하는 방법을 확인합니다. 

Neo4j는 Bolt 이진 프로토콜이나 HTTP/HTTPS를 사용해 클라이언트를 서포트 합니다. 기본적으로 3 종류의 Neo4j 커넥터가 있습니다. 

+ ```bolt``` 커넥터
+ ```http``` 커넥터
+ ```https``` 커넥터


표 3.1. 기본 커넥터 및 포트

```
커넥터 이름 	프로토콜 	기본 포트 숫자
dbms.connector.bolt

Bolt

7687

dbms.connector.http

HTTP

7474

dbms.connector.https

HTTPS

7473
```
