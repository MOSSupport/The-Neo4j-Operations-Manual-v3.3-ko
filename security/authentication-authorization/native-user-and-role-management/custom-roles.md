#### 7.1.4.2. 사용자 역할 <code>Enterprise Edition</code>

<div class="abstract">
	<p>이 절에서는 Neo4j의 사용자 역할에 대해서 설명합니다. 
	</p>
</div>

사용자 지정 역할은 관리자가 만들고 삭제할 수 있습니다. 사용자 지정 역할은 특정 사용자 지정 개발 된 절차를 실행하는 기능을 제어하기위한 목적으로 만 만들어집니다. 기본 역할과 달리 사용자 정의 역할은 neo4j.conf에서 명시 적으로 허용 된 절차를 실행하는 것 이외의 권한을 갖지 않습니다.
Custom roles may be [created](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/procedures/#user-roles-create-role) and [deleted](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/procedures/#user-roles-delete-role) by an [administrator](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-administrator). Custom roles are created for the sole purpose of controlling the ability to execute certain custom developed procedures. In contrast to the native roles, a custom role will not have any permissions other than to execute procedures which have been explicitly permitted in [*neo4j.conf*](https://neo4j.com/docs/operations-manual/3.3/configuration/file-locations/).

하위 그래프 액세스 제어를 허용하기 위해 맞춤 역할을 사용하는 방법에 대한 자세한 내용은 [7.1.6절, "하위 그래프 액세스 제어"](/security/authentication-authorization/subgraph-access-control.html)에 나와 있습니다.
More details regarding how to use custom roles to allow for subgraph access control may be found in [Section 7.1.6, “Subgraph access control”](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/subgraph-access-control/).