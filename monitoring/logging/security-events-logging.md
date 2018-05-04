
## 8.2.2 보안 이벤트 로깅(Logging)

```
이 섹션에서는 Neo4j에서 지원하는 보안 이벤트 로깅에 대해 다룹니다. 
```

Neo4j는 모든 보안 이벤트를 기록하는 보안 이벤트 로깅을 지원합니다. 
 
네이티브 사용자 관리의 경우 다음 활동이 기록됩니다.:

+ 로그인 시도 -기본값마다 로그인 성공과 실패 모든 경우가 기록됩니다. 
+ 관리자 및 유저가 유저 자신의 패스워드 변경
+ 실패한 시도를 포험하여 유저 생성 및 삭제
+ 실패한 시도를 포함하여 고객 역할 생성 및 삭제 
+ 실패한 시도를 포함하여 유저 역할 배정 및 삭제
+ 유저 정지 및 활성화 
+ 유저와 역할을 리스트 하는데 실패한 비-관리 유저 

## 8.2.2.1. 로그 환경설정

로그 파일의 이름은 ```security.log```이고 이것은 *logs* 디렉토리에 있습니다. [섹션 3.1,"파일 위치"](../../configuration/file-locations.md)를 참조하면 됩니다. 

보안 이벤트 로그 회전은 neo4j.conf 설정 파일에서 수정할 수 있습니다. 아래는 이용가능 한 변수 입니다. 

| 변수 이름                                      | 기본 값 | 설명                                                         |
| ---------------------------------------------- | ------- | ------------------------------------------------------------ |
| ``` dbms.logs.security.rotation.size```        | 20M     | 보안 이벤트 로그가 자동으로 회전하도록 파일 사이즈 설정      |
| ``` dbms.logs.security.rotation.delay```       | 300s    | 마지막 로그 회전 발생 후, 다음 로그 회전 발생 전 최소 시간 간격 설정 |
| ``` dbms.logs.security.rotation.keep_number``` | 7       | 저장된 로그 파일의 개수 설정                                 |


LDAP를 인증 방법으로 사용할 때 LDAP 서버 통신 이벤트 및 실패뿐만 아니라 잘못 구성된 LDAP 일부 사례도 로그됩니다. 

REST를 사용하는 것과 같이 다양한 방식의 프로그래밍 상호작용이 예상되는 경우에는 로그인 성공 기록을 사용하지 않는 것이 좋습니다. 로그인 성공 기록은 ```neo4j.conf```파일의 ```dbms.security.log_successful_authentication``` 변수를 설정해서 비활성화할 수 있습니다.: 

```dbms.security.log_successful_authentication=false```

아래는 보안 로그의 예 입니다. 

```
2016-10-27 13:45:00.796+0000 INFO  [AsyncLog @ 2016-10-27 ...]  [johnsmith]: logged in
2016-10-27 13:47:53.443+0000 ERROR [AsyncLog @ 2016-10-27 ...]  [johndoe]: failed to log in: invalid principal or credentials
2016-10-27 13:48:28.566+0000 INFO  [AsyncLog @ 2016-10-27 ...]  [johnsmith]: created user `janedoe`
2016-10-27 13:48:32.753+0000 ERROR [AsyncLog @ 2016-10-27 ...]  [johnsmith]: tried to create user `janedoe`: The specified user ...
2016-10-27 13:49:11.880+0000 INFO  [AsyncLog @ 2016-10-27 ...]  [johnsmith]: added role `admin` to user `janedoe`
2016-10-27 13:49:34.979+0000 INFO  [AsyncLog @ 2016-10-27 ...]  [johnsmith]: deleted user `janedoe`
2016-10-27 13:49:37.053+0000 ERROR [AsyncLog @ 2016-10-27 ...]  [johnsmith]: tried to delete user `janedoe`: User 'janedoe' does ...
2016-10-27 14:00:02.050+0000 INFO  [AsyncLog @ 2016-10-27 ...]  [johnsmith]: created role `operator`
```
