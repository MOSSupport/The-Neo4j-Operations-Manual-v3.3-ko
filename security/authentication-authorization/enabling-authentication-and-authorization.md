### 7.1.3. Enabling authentication and authorization  <code>Enterprise Edition</code>

<div class="abstract">
	<p>이 절에서는 Neo4j의 인증과 권한 부여에 활성화에 대해서 설명합니다. 
	</p>
</div>

인증 및 권한 부여는 기본 설정입니다. 모든 인증 및 권한 부여 기능을 끌 수 있습니다. 이 작업은 ```dbms.security.auth_enabled```을 설정합니다..

```
dbms.security.auth_enabled=false
```

이 설정은 악의적인 활동에 데이터베이스를 취약하게 만듭니다. 인증 및 권한 부여를 비활성화 하는 것은 추천하지 않습니다.
