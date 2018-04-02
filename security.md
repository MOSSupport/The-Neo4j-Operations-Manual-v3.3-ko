# 7. 보안

> 이번 장은 Neo4j의 보안에 대해서 설명합니다. 


데이터를 보호하려면 먼저 서버 및 네트워크 보안과 관련된 업계 모범 사례에 따라 실제 데이터 보안을 확인하십시오. To protect your data, first ensure your physical data security by following industry best practices with regard to server and network security. Ensure that your Neo4j deployment adheres to your company’s information security guidelines by setting up the appropriate authentication and authorization rules.

이번 장은 아래와 같이 구성됩니다.:

* [인증 및 권한 부여](/security/authentication-authorization.md)

	* [소개](/security/authentication-authorization/introduction.md)
	* [Terminology](/security/authentication-authorization/terminology.md)
	* [인증 및 권한 부여 사용](/security/authentication-authorization/enable.md)
	* [기본 사용자 및 역할 관리](/security/authentication-authorization/native-user-role-management.md)

	  * [기본 역할](/security/authentication-authorization/native-user-role-management/native-roles.md)
	  * [맞춤 역할(Custom roles)](/security/authentication-authorization/native-user-role-management/custom-roles.md)
	  * [사용자와 역할 전파](/security/authentication-authorization/native-user-role-management/propagate-users-and-roles.md)
	  * [초기 사용자 및 역할 관리 절차](/security/authentication-authorization/native-user-role-management/procedures.md)

	* [LDAP와의 통합](/security/authentication-authorization/ldap-integration.md)
	* [하위 그래프 엑세스 제어](/security/authentication-authorization/subgraph-access-control.md)

* [Securing extensions](/security/securing-extensions.md)

* [Unified SSL framework](/security/ssl-framework.md)
* [Credentials handling in Neo4j Browser](/security/browser.md)
* [Security checklist](/security/checklist.md)

Additionally, logs can be useful for continuous analysis, or for specific investigations. Facilities are available for producing[security event logs](https://neo4j.com/docs/operations-manual/3.3/monitoring/logging/security-events-logging/)as well as[query logs](https://neo4j.com/docs/operations-manual/3.3/monitoring/logging/query-logging/)as described in[Chapter 8,_Monitoring_](https://neo4j.com/docs/operations-manual/3.3/monitoring/).

