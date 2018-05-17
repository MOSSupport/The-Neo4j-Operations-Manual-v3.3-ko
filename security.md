# 7. 보안

<div class="abstract">
	<p>이번 장은 Neo4j의 보안에 대해서 설명합니다.
	</p>
</div>

데이터를 보호하려면 먼저 서버 및 네트워크 보안과 관련된 업계 모범 사례에 따라 실제 데이터 보안을 확인하십시오. Neo4j 배포가 적절한 인증 및 권한 부여 규칙을 설정하여 귀사의 정보 보안 지침을 준수하는지 확인하십시오.

이번 장은 아래와 같이 구성됩니다.:

* [인증 및 권한 부여](/security/authentication-and-authorization.md)

	* [소개](/security/authentication-authorization/introduction.md)
	* [용어](/security/authentication-authorization/terminology.md)
	* [인증 및 권한 부여 사용](/security/authentication-authorization/enabling-authentication-and-authorization.md)
	* [기본 사용자 및 역할 관리](/security/authentication-authorization/native-user-and-role-management.md)

	  * [기본 역할](/security/authentication-authorization/native-user-role-management/native-roles.md)
	  * [맞춤 역할(Custom roles)](/security/authentication-authorization/native-user-role-management/custom-roles.md)
	  * [사용자와 역할 전파](/security/authentication-authorization/native-user-role-management/propagate-users-and-roles.md)
	  * [초기 사용자 및 역할 관리 절차](/security/authentication-authorization/procedures-for-native-user-role-management.md)

	* [LDAP와의 통합](/security/authentication-authorization/ldap-integration.md)
	* [하위 그래프 엑세스 제어](/security/authentication-authorization/subgraph-access-control.md)

* [확장 기능 보안](/security/securing-extensions.md)

* [통합 SSL framework](/security/ssl-framework.md)
* [Neo4j 브라우저에서 인증 정보 처리](/security/browser.md)
* [보안 점검목록](/security/checklist.md)

또한 로그는 지속적인 분석 또는 특정 조사에 유용 할 수 있습니다. [8장. _모니터링_](/monitoring.md)에 설명 된대로 [보안 이벤트 로그](/monitoring/logging/security-events-logging.md)와 [쿼리 로그](/monitoring/logging/query-logging.md)를 생성 할 수있는 기능이 있습니다.