### 7.1.4. 기본 사용자와 역할 관리 <code>Enterprise Edition</code> 

<div class="abstract">
	<p>이 절에서는 Neo4j의 기본 사용자와 역할관리에 대해서 설명합니다. 
	</p>
</div>
This section describes native user and role management in Neo4j.

기본 사용자 관리를 위해, Neo4j에서는 관리자를 위한 추가적인 작업을 제공합니다.
For managing native users, Neo4j offers additional actions for an administrator:

- 전체 사용자 및 역할 보기(사용자의 모든 역할, 모든 역할의 사용자 보기 포함)
- 다른 사용자 추가 및 삭제
- 사용자에게 역할 추가 및 제거
- 사용자 정의된 역할 추가 및 삭제
- 다른 사용자 일시 중지 및 활성화
- 모든 사용자의 비밀번호 변경

관리자가 다른 사용자를 일시 중지하거나 삭제하면 다음 규칙이 적용됩니다:

- 관리지는 다른 사용자(다른 관리자 포함)를 일시 중지 또는 삭제할 수 있지만 자신은 삭제할 수 없습니다.
- 사용자를 삭제하면 실행중인 모든 쿼리와 세션이 종료됩니다.
- 삭제된 사용자에 의해 현재  실행되는 쿼리는 롤백됩니다.
- 사용자가 더 이상 다시 로그인 할 수 없습니다.(일시 중지 된 경우 관리자가 다시 활성화 할 때까지)
- 사용자를 삭제하기 전에 사용자로부터 할당 된 역할을 제거 할 필요가 없습니다.


이 절은 아래 내용을 설명합니다.:

-   ######[기본 역할](./native-user-and-role-management/native-roles.md)
-   ######[사용자 역할](./native-user-and-role-management/custom-roles.md)
-   #####[사용자 및 역할 전파](./native-user-and-role-management/propagate-users-and-roles.md)
-   #####[기본 사용자 및 역할 관리 절차](./native-user-and-role-management/procedures-for-native-user-and-role-management.md)