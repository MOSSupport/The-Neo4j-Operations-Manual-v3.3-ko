### 7.1.5. LDAP 통합                  

<div class="abstract">
	<p>이 절에서는 LDAP 시스템과의 통합을 위한 Neo4j 지원에 대해 설명합니다.
	</p>
</div>

#### 7.1.5.1. LDAP인증 프로바이더 구성                     

Neo4j는 Active Directory, OpenLDAP 또는 기타 LDAP 호환 인증 서비스와의 통합을 허용하는 LDAP프로토콜을 지원합니다. 페더레이션 사용자 관리가 LDAP 서비스로 연기되는 예제 구성을 보여 주며, 해당 서비스의 관리 기능을 사용합니다. 즉 기본 Neo4j 사용자와 역할 관리를 완전히 해제하고 LDAP 그룹을 Neo4j에 내장된 역할 (`reader`, `publisher`, `architect` 그리고 `admin`)과 사용자 정의 역할에 매핑합니다.

모든 설정은 기본 구성 파일 *neo4j.conf*에서 서버 시작 시 정의해야 합니다. 먼저 LDAP를 인증 및 권한 부여 제공자로 사용하도록 Neo4j를 구성하십시오.

```
# Turn on security
dbms.security.auth_enabled=true

# Choose LDAP connector as security provider for both authentication and authorization
dbms.security.auth_provider=ldap
```

##### Active Directory 구성

Active Directory의 구성 예제는 아래를 참조하십시오.

```
# Configure LDAP to point to the AD server
dbms.security.ldap.host=ldap://myactivedirectory.example.com

# Provide details on user structure within the LDAP system:
dbms.security.ldap.authentication.user_dn_template=cn={0},cn=Users,dc=example,dc=com
dbms.security.ldap.authorization.user_search_base=cn=Users,dc=example,dc=com
dbms.security.ldap.authorization.user_search_filter=(&(objectClass=*)(cn={0}))
dbms.security.ldap.authorization.group_membership_attributes=memberOf

# Configure the actual mapping between groups in the LDAP system and roles in Neo4j
dbms.security.ldap.authorization.group_to_role_mapping=\
  "cn=Neo4j Read Only,cn=Users,dc=neo4j,dc=com"      = reader       ;\
  "cn=Neo4j Read-Write,cn=Users,dc=neo4j,dc=com"     = publisher    ;\
  "cn=Neo4j Schema Manager,cn=Users,dc=neo4j,dc=com" = architect    ;\
  "cn=Neo4j Administrator,cn=Users,dc=neo4j,dc=com"  = admin        ;\
  "cn=Neo4j Procedures,cn=Users,dc=neo4j,dc=com"     = allowed_role

# In case defined users are not allowed to search for themselves,
# we can specify credentials for a user with read access to all users and groups:
# Note that this account only needs read-only access to the relevant parts of the LDAP directory
# and does not need to have access rights to Neo4j or any other systems.
#dbms.security.ldap.authorization.use_system_account=true
#dbms.security.ldap.authorization.system_username=cn=search-account,cn=Users,dc=example,dc=com
#dbms.security.ldap.authorization.system_password=secret
```

다음은 `sAMAccountName`을 사용하여 로그인 할 수있는 Active Directory의 대체 구성입니다.               

```
# Configure LDAP to point to the AD server
dbms.security.ldap.host=ldap://myactivedirectory.example.com

# Provide details on user structure within the LDAP system:
dbms.security.ldap.authorization.user_search_base=cn=Users,dc=example,dc=com
dbms.security.ldap.authorization.user_search_filter=(&(objectClass=*)(samaccountname={0}))
dbms.security.ldap.authorization.group_membership_attributes=memberOf

# Configure the actual mapping between groups in the LDAP system and roles in Neo4j
dbms.security.ldap.authorization.group_to_role_mapping=\
  "cn=Neo4j Read Only,cn=Users,dc=neo4j,dc=com"      = reader       ;\
  "cn=Neo4j Read-Write,cn=Users,dc=neo4j,dc=com"     = publisher    ;\
  "cn=Neo4j Schema Manager,cn=Users,dc=neo4j,dc=com" = architect    ;\
  "cn=Neo4j Administrator,cn=Users,dc=neo4j,dc=com"  = admin        ;\
  "cn=Neo4j Procedures,cn=Users,dc=neo4j,dc=com"     = allowed_role

# In case defined users are not allowed to search for themselves,
# we can specify credentials for a user with read access to all users and groups:
# Note that this account only needs read-only access to the relevant parts of the LDAP directory
# and does not need to have access rights to Neo4j or any other systems.
dbms.security.ldap.authorization.use_system_account=true
dbms.security.ldap.authorization.system_username=cn=search-account,cn=Users,dc=example,dc=com
dbms.security.ldap.authorization.system_password=secret

# Perform authentication with sAMAccountName instead of DN.
# Using this setting requires dbms.security.ldap.authorization.system_username and
# dbms.security.ldap.authorization.system_password to be used since there is no way to log in
# through ldap directly with the sAMAccountName.
# Instead the login name will be resolved to a DN that will be used to log in with.
dbms.security.ldap.authentication.use_samaccountname=true
```

다음은 Active Directory 속성 `sAMAccountName`을 사용하여 다른 조직 구성 단위의 사용자를 인증 할 수있는 Active Directory의 대체 구성입니다.

```
# Configure LDAP to point to the AD server
dbms.security.ldap.host=ldap://myactivedirectory.example.com

dbms.security.ldap.authentication.user_dn_template={0}@example.com
dbms.security.ldap.authorization.user_search_base=dc=example,dc=com
dbms.security.ldap.authorization.user_search_filter=(&(objectClass=user)(sAMAccountName={0}))

# Configure the actual mapping between groups in the LDAP system and roles in Neo4j
dbms.security.ldap.authorization.group_to_role_mapping=\
"cn=Neo4j Read Only,cn=Users,dc=example,dc=com" = reader ;\
"cn=Neo4j Read-Write,cn=Users,dc=example,dc=com" = publisher ;\
"cn=Neo4j Schema Manager,cn=Users,dc=example,dc=com" = architect ;\
"cn=Neo4j Administrator,cn=Users,dc=example,dc=com" = admin ;\
"cn=Neo4j Procedures,cn=Users,dc=example,dc=com" = allowed_role
```

`{0}@example.com` 패턴을 `user_dn_template`에 지정하면 루트도메인에서 인증을 시작할 수 있습니다. 트리 내에서의 위치에 관계없이 전체 트리를 검사하여 사용자를 찾습니다.

이 예제에서는 `dbms.security.ldap.authentication.use_samaccountname` 설정이 구성되어 있지 않습니다.

##### openLDAP 구성                        

openLDAP의 구성 예제는 아래를 참조하십시오.

```
# Configure LDAP to point to the OpenLDAP server
dbms.security.ldap.host=myopenldap.example.com

# Provide details on user structure within the LDAP system:
dbms.security.ldap.authentication.user_dn_template=cn={0},ou=users,dc=example,dc=com
dbms.security.ldap.authorization.user_search_base=ou=users,dc=example,dc=com
dbms.security.ldap.authorization.user_search_filter=(&(objectClass=*)(uid={0}))
dbms.security.ldap.authorization.group_membership_attributes=gidnumber

# Configure the actual mapping between groups in the OpenLDAP system and roles in Neo4j
dbms.security.ldap.authorization.group_to_role_mapping=\
  101 = reader       ;\
  102 = publisher    ;\
  103 = architect    ;\
  104 = admin        ;\
  105 = allowed_role

# In case defined users are not allowed to search for themselves,
# we can specify credentials for a user with read access to all users and groups:
# Note that this account only needs read-only access to the relevant parts of the LDAP directory
# and does not need to have access rights to Neo4j or any other systems.
#dbms.security.ldap.authorization.use_system_account=true
#dbms.security.ldap.authorization.system_username=cn=search-account,ou=users,dc=example,dc=com
#dbms.security.ldap.authorization.system_password=search-account-password
```

설정 예제에서 몇 가지 세부 사항에 주의를 기울이고 싶습니다. LDAP 구성 옵션에 대한 포괄적인 개요는 [A.1절, "구성 설정"](https://neo4j.com/docs/operations-manual/current/reference/configuration-settings/)에서 볼 수 있습니다.

| Parameter name                           | Default value                        | Description                              |
| ---------------------------------------- | ------------------------------------ | ---------------------------------------- |
| [dbms.security.ldap.authentication.user_dn_template](https://neo4j.com/docs/operations-manual/3.3/reference/configuration-settings/#config_dbms.security.ldap.authentication.user_dn_template) | `uid={0},ou=users,dc=example,dc=com` | Converts usernames into LDAP-specific fully qualified names required for logging in. |
| [dbms.security.ldap.authorization.user_search_base](https://neo4j.com/docs/operations-manual/3.3/reference/configuration-settings/#config_dbms.security.ldap.authorization.user_search_base) | `ou=users,dc=example,dc=com`         | Sets the base object or named context to search for user objects. |
| [dbms.security.ldap.authorization.user_search_filter](https://neo4j.com/docs/operations-manual/3.3/reference/configuration-settings/#config_dbms.security.ldap.authorization.user_search_filter) | `(&(objectClass=*)(uid={0}))`        | Sets up an LDAP search filter to search for a user principal. |
| [dbms.security.ldap.authorization.group_membership_attributes](https://neo4j.com/docs/operations-manual/3.3/reference/configuration-settings/#config_dbms.security.ldap.authorization.group_membership_attributes) | `[memberOf]`                         | Lists attribute names on a user object that contains groups to be used for mapping to roles. |
| [dbms.security.ldap.authorization.group_to_role_mapping](https://neo4j.com/docs/operations-manual/3.3/reference/configuration-settings/#config_dbms.security.ldap.authorization.group_to_role_mapping) |                                      | Lists an authorization mapping from groups to the pre-defined built-in roles `admin`, `architect`, `publisher` and `reader`, or to any other custom-defined roles. |

#### 7.1.5.2. 'ldapsearch'를 사용하여 구성 확인                    

LDAP 명령 행 도구 `ldapsearch`를 사용하여 구성이 올바른지 그리고 LDAP 서버가 실제로 응답하는지 확인할 수 있습니다. LDAP 구성 설정 값을 포함하는 검색 명령을 수행합니다.

예제 검색은 인증(`간단한` 메커니즘 사용)과 사용자 'john'의 인증을 모두 확인합니다. 보다 진보 된 사용법과 SASL 인증 메커니즘을 사용하는 방법에 대해서는 `ldapsearch` 문서를 보십시오.

`dbms.security.ldap.authorization.use_system_account=false` (기본값):            

```
#ldapsearch -v -H ldap://<dbms.security.ldap.host> -x -D <dbms.security.ldap.authentication.user_dn_template : replace {0}> -W -b <dbms.security.ldap.authorization.user_search_base> "<dbms.security.ldap.authorization.user_search_filter : replace {0}>" <dbms.security.ldap.authorization.group_membership_attributes>
ldapsearch -v -H ldap://myactivedirectory.example.com:389 -x -D cn=john,cn=Users,dc=example,dc=com -W -b cn=Users,dc=example,dc=com "(&(objectClass=*)(cn=john))" memberOf
```

`dbms.security.ldap.authorization.use_system_account=true`:            

```
#ldapsearch -v -H ldap://<dbms.security.ldap.host> -x -D <dbms.security.ldap.authorization.system_username> -w <dbms.security.ldap.authorization.system_password> -b <dbms.security.ldap.authorization.user_search_base> "<dbms.security.ldap.authorization.user_search_filter>" <dbms.security.ldap.authorization.group_membership_attributes>
ldapsearch -v -H ldap://myactivedirectory.example.com:389 -x -D cn=search-account,cn=Users,dc=example,dc=com -w secret -b cn=Users,dc=example,dc=com "(&(objectClass=*)(cn=john))" memberOf
```

그런 다음 성공적인 응답을 받았는지와 반환 된 멤버십 속성의 값이 `dbms.security.ldap.authorization.group_to_role_mapping`의 롤에 매핑 된 그룹인지 확인하십시오.

```
# extended LDIF
#
# LDAPv3
# base <cn=Users,dc=example,dc=com> with scope subtree
# filter: (cn=john)
# requesting: memberOf
#

# john, Users, example.com
dn: CN=john,CN=Users,DC=example,DC=com
memberOf: CN=Neo4j Read Only,CN=Users,DC=example,DC=com

# search result
search: 2
result: 0 Success

# numResponses: 2
# numEntries: 1
```

#### 7.1.5.3. 인증 캐시                    

*인증 캐시*는 성능을 돕기 위해 Neo4j가 LDAP 서버를 통한 인증 결과를 캐시하는 메커니즘입니다. `dbms.security.ldap.authentication.cache_enabled` 와 `dbms.security.auth_cache_ttl` 매개변수로 설정됩니다.

```
# Turn on authentication caching to ensure performance
dbms.security.ldap.authentication.cache_enabled=true
dbms.security.auth_cache_ttl=10m
```

| Parameter name                           | Default value   | Description                              |
| ---------------------------------------- | --------------- | ---------------------------------------- |
| [dbms.security.ldap.authentication.cache_enabled](https://neo4j.com/docs/operations-manual/current/reference/configuration-settings/#config_dbms.security.ldap.authentication.cache_enabled) | `true`          | Determines whether or not to cache the result of authentication via the LDAP server. Whether authentication caching should be enabled or not must be considered in view of your company’s security guidelines. It should be noted that when using the REST API, disabling authentication caching will result in re-authentication and possibly re-authorization of users on every request, which may severely impact performance on production systems, and put heavy load on the LDAP server.  |
| [dbms.security.auth_cache_ttl](https://neo4j.com/docs/operations-manual/current/reference/configuration-settings/#config_dbms.security.auth_cache_ttl) | `10000 minutes` | Is the time to live (TTL) for cached authentication and authorization info. Setting the TTL to 0 will disable all auth caching. A short ttl will require more frequent re-authentication and re-authorization, which can impact performance. A very long ttl will also mean that changes to the users settings on an LDAP server may not be reflected in the Neo4j authorization behavior in a timely manner. |

관리자는 인증 캐시를 삭제하여 페더레이션 인증 공급자 시스템에서 인증 및 권한 부여 정보를 다시 쿼리하도록 할 수 있습니다.

<div class="example">
예제 7.16. 인증 캐시 삭제
<div class="example-contents">

이 문장을 실행하려면 Neo4j Browser 또는 Neo4j Cypher Shell을 사용하십시오.
<p>
<code>
CALL dbms.security.clearAuthCache()
</code>
</div>
</div>

#### 7.1.5.4. 사용 가능한 암호화 방법
                   
`dbms.security.ldap.host` 매개 변수를 지정하는 다음의 모든 방법이 유효합니다. 이렇게하면 암호화없이 LDAP를 사용하여 구성됩니다. 프로토콜이나 포트를 지정하지 않으면 디폴트 포트 `389` 보다 `ldap`이 사용됩니다.

```
dbms.security.ldap.host=myactivedirectory.example.com
dbms.security.ldap.host=myactivedirectory.example.com:389
dbms.security.ldap.host=ldap://myactivedirectory.example.com
dbms.security.ldap.host=ldap://myactivedirectory.example.com:389
```

##### StartTLS를 통한 암호화와 LDAP 사용                        

StartTLS를 통한 암호화로 Active Directory를 구성하려면 다음 매개 변수를 설정하십시오.

```
dbms.security.ldap.use_starttls=true
dbms.security.ldap.host=ldap://myactivedirectory.example.com
```

##### 암호화 LDAPS와 LDAP 사용                       

암호화 된 LDAPS로 Active Directory를 구성하려면 `dbms.security.ldap.host`를 다음 중 하나로 설정하십시오. 포트를 지정하지 않으면 기본 포트 인 `636` 보다 `ldaps`가 사용됩니다.

```
dbms.security.ldap.host=ldaps://myactivedirectory.example.com
dbms.security.ldap.host=ldaps://myactivedirectory.example.com:636
```

이 Active Directory 보안 방법은 더 이상 사용되지 않으므로 권장하지 않습니다. 대신 Active Directory를 [StartTLS](/security/authentication-authorization/ldap-integration.md/#starttls를-통한-암호화와-ldap-사용)를 통한 암호화와 함께 사용하십시오.

#### 7.1.5.5. 테스트 환경에서 자체 서명 된 인증서 사용                     

운영환경에서는 항상 LDAP 서버에 대한 보안 액세스를 위해 인증 기관에서 발행 한 SSL 인증서를 사용해야합니다. 그러나 테스트 환경과 같이 LDAP 서버에서 자체 서명 된 인증서를 사용하려는 시나리오가 있습니다. 이 시나리오에서는 Neo4j에 로컬 인증서를 알려주어야 합니다. *neo4j.conf*에 `dbms.jvm.additional`을 사용하여 인증서의 세부 사항을 입력하면 됩니다.

<div class="example">
예제 7.17. LDAP 서버에서 자체 서명 된 인증서에 대한 세부 정보 지정
<div class="example-contents">

이 예는 LDAP 서버의 자체 서명 인증서에 대한 세부 사항을 지정하는 방법을 보여줍니다. 인증서 파일인 `MyCert.jks`에 대한 경로는 Neo4j 서버의 절대 경로입니다.

<p>
<pre>
<code>
dbms.jvm.additional=-Djavax.net.ssl.keyStore=/path/to/MyCert.jks
dbms.jvm.additional=-Djavax.net.ssl.keyStorePassword=secret
dbms.jvm.additional=-Djavax.net.ssl.trustStore=/path/to/MyCert.jks
dbms.jvm.additional=-Djavax.net.ssl.trustStorePassword=secret
</code>
</pre>
</div>
</div>