### 7.1.2. 용 어

<div class="abstract">
	<p>이 절에서는 Neo4j에서 인증 및 권한 부여와 관련된 용어를 설명합니다. 
	</p>
</div>

다음 용어는 Neo4j에서 역할 기반 액세서 제어와 관련이 있습니다.


##### active user

시스템 내에서 활동 중이며 데이터의 지정된 역할에 의해 지정된 작업을 수행 할 수있는 [user](#user)입니다. [suspended user](#suspended-user)와는 대조적입니다.

##### administrator

[`관리자(admin`)](./native-user-role-management/native-roles.md/#admin-role) 역할에 할당된 [사용자](#user)입니다.

##### current user

이 장에서 설명하는 명령을 호출하는 현재 로그인한 [사용자](#user)입니다. 

##### password policy

암호정책은 유효한 암호를 구성하는 규칙의 집합입니다. Neo4j에는 다음 규칙이 적용됩니다.

* 암호는 빈 문자열 일 수 없습니다.
* 암호를 변경할 때, 새 암호는 이전 암호와 같을 수 없습니다.

##### role

역할은 자료에 대한 읽기 및 쓰기와 같은 권한에 대한 행동의 모음입니다. Neo4j에는 2가지 형태의 역할이 있습니다.

* 기본 역할에 대한 설명은 [7.1.4.1 절, "기본 역할"](./native-user-role-management/native-roles.md)에서 다룹니다.
* 사용자 역할에 대한 설명은 [7.1.4.2 절, "사용자 역할"](./native-user-role-management/custom-roles.md)에서 다룹니다.

##### suspended user

일시 중지 된 [사용자](#user)는 할당된 [역할](#role)에 관계없이 모든 가용한 데이터베이스에 접근 할 수 없습니다.

##### user

* 사용자는 사용자 이름과 자격 증명으로 구성되며, 자격증명은 암호화 같은 정보 단위로 사용자의 신원을 확인 합니다.
* 사용자는 사람, 어플리케이션 등을 나타냅니다.