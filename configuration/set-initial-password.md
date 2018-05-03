## 3.3. 초기 비밀번호 설정

```
초기 비밀번호 설정하는 방법을 확인합니다. 
```

```neo4j-admin```의 명령어인 ```set-initial-password``` 을 사용해서 ```neo4j```의 네이티브 유저 비밀번호를 정의 합니다. 이 작업은 처음으로 데이터 베이스를 실행하기 전에 해야합니다. 


**문법 :**

```
neo4j-admin set-initial-password <password>
```


**예시 3.1. neo4j-admin의 ```set-initial-password``` 명령어를 사용하십시오.**


데이터 베이스를 처음으로 설정하기 전에, ```neo4j```네이티브 유저 비밀번호를 'h6u4%kr'로 설정하세요. 

```
$neo4j-home> bin/neo4j-admin set-initial-password h6u4%kr
```



만약 비밀번호를 이 방법으로 명시하지 않으면, 기본암호는 ```neo4j```로 설정됩니다. 이 경우, 처음 로그인할 때 기본으로 설정된 암호를 변경하라는 메시지가 뜹니다. 





 
