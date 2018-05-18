### 7.1.6. 하위 그래프 엑세스 제어  <code>Enterprise Edition</code>                

<div class="abstract">
	<p>이 절에서는 Neo4j에서 하위 그래프 액세스 제어를 구성하는 방법에 대해 설명합니다.
	</p>
</div>


그래프의 지정된 부분에 대한 사용자 액세스 및 후속 조치를 제한 할 수 있습니다. 예를 들어 사용자는 특정 레이블 및 특정 유형의 관계가있는 노드를 읽을 수는 있지만 작성할 수는 없습니다.

하위 그래프 액세스 제어를 구현하려면 다음 단계를 완료해야합니다.

1. 제어하려는 그래프 부분에서 읽기 및 쓰기를 수행하는 프로 시저 또는 함수를 배치하십시오. 이는 사내에서 개발되거나 타사 라이브러리로 제공 될 수 있습니다. 사용자 정의 프로 시저 및 함수 생성 및 사용에 대한 설명은 [Neo4j 개발자 설명서 → Neo4j 확장](https://neo4j.com/docs/developer-manual/current/extending-neo4j/)을 참조하십시오.
2. 위에서 설명한 절차를 실행할 사용자 역할을 하나 이상 만듭니다. 이러한 역할에는 나중에 관련 권한을 할당 할 수 있습니다.
3. 사용자 정의 역할을 가진 사용자가 실행할 수 있도록 프로시저를 구성하십시오.

아래 단계에서는 프로 시저 또는 함수가 이미 개발되어 설치되어 있다고 가정합니다.

#### 7.1.6.1. 사용자 지정 역할 만들기                     

기본 사용자 관리나 LDAP을 이용한 패터레이션 사용자 관리를 통해 사용자 정의 역할 및 관리를 생성합니다.

##### 기본 사용자 시나리오

기본 사용자 시나리오에서는 사용자 지정 역할이 만들어져 관련 사용자에게 할당됩니다.  

<div class="example">
예제 7.18. 기본 사용자 시나리오
<div class="example-contents">
이 예에서는 Cypher를 사용하여 사용자 정의 <code>accounting</code> 역할을 생성하고 기존 사용자인 <code>billsmith</code>에게 할당합니다.

<pre> 
<code>
`CALL dbms.security.createRole('accounting')
CALL dbms.security.addRoleToUser('accounting', 'billsmith')`                                       
</code>
</pre>
</div>
</div>

##### 패터레이션 사용자 시나리오(LDAP)

LDAP 시나리오에서 LDAP 사용자 그룹은 Neo4j의 사용자 지정 역할에 매핑됩니다.

<div class="example">
예제 7.19. 패터레이션 사용자 시나리오(LDAP)
<div class="example-contents">
이 예에서는 Cypher를 사용하여 사용자 정의 'accounting'역할을 만듭니다.
<p>
<code>CALL dbms.security.createRole('accounting')</code>

우리는 <code>accounting</code> 역할을 groupID가 <code>101</code> 인 LDAP 그룹에 매핑 할 것입니다.

<code>dbms.security.realms.ldap.authorization.group_to_role_mapping=101=accounting</code>

</div>
</div>

#### 7.1.6.2. 프로시저 권한 관리                     

표준 사용에서 [7.1.4.1 절 "기본 역할"](/security/native-user-and-role-management/native-roles.md)에서 설명한대로 표준 Cypher 문과 동일한 보안 규칙에 따라 프로시저와 함수가 실행됩니다. 예를 들어 `publisher`, `architect` 및 `admin` 기본 역할 중 하나가 할당 된 사용자는 `mode=WRITE`로 프로시저를 실행할 수 있지만 `reader` 역할만 할당 된 사용자는 프로시저를 실행할 수 없습니다.

하위 그래프 액세스 제어를 위해 특정 역할이 할당 된 기본 역할을 통해 액세스 할 수 없는 절차를 실행하도록 허용합니다. 프로시저의 실행 중에만 프로시저 모드와 함께 제공되는 권한이 사용자에게 부여됩니다. 다음 두 매개 변수는 원하는 동작을 구성하는데 사용됩니다.


- [**dbms.security.procedures.default_allowed**](https://neo4j.com/docs/operations-manual/current/reference/configuration-settings/#config_dbms.security.procedures.default_allowed)

`dbms.security.procedures.default_allowed` 설정은 `dbms.security.procedures.roles` 구성과 일치하지 않는 모든 프로 시저 또는 함수를 실행할 수있는 단일 역할을 정의합니다.


<div class="example">
예제 7.20. 프로시저 및 함수를 실행할 수있는 기본 역할 구성
<div class="example-contents">
다음과 같은 구성을 가지고 있다고 가정합니다.

<code>dbms.security.procedures.default_allowed=superAdmin</code>

다음과 같은 효과가 있습니다.

- <code>dbms.security.procedures.roles</code> 설정하지 않음으로 두면, <code>superAdmin</code> 역할은 모든 커스텀 프로시저와 함수를 실행할 수 있습니다.
- <code>dbms.security.procedures.roles</code>에 몇 가지 역할과 함수가 정의되어 있다면, <code>superAdmin</code> 롤은 <code>dbms.security.procedures.roles</code>에 의해 설정되지 않은 모든 커스텀 프로 시저와 함수를 실행할 수 있습니다.                            
</div>
</div>                                                                                                                 

- [**dbms.security.procedures.roles**](https://neo4j.com/docs/operations-manual/3.3/reference/configuration-settings/#config_dbms.security.procedures.roles)

`db.security.procedures.roles`설정으로 절차를 세밀하게 제어할 수 있습니다.

<div class="example">
예제 7.21. 특정 절차의 실행을 위한 역할 구성                                                   
<div class="example-contents">
다음과 같은 구성이 있다고 가정합니다.
<p>
<code>dbms.security.procedures.roles=apoc.convert.*:Converter;apoc.load.json.*:Converter,DataSource;apoc.trigger.add:TriggerHappy</code>
<p>
이 경우 다음과 같은 효과가 있습니다.
<p>
<ul>
<li> <code>Converter</code> 역할을 가진 모든 사용자는 <code>apoc.convert</code> 네임 스페이스의 모든 프로시저를 실행할 수 있습니다. </li>
<li> <code>Converter</code> 와 <code>DataSource</code> 역할을 가진 모든 사용자는 <code>apoc.load.json</code> 네임 스페이스에서 프로 시저를 실행할 수 있습니다.</li>
<li> <code>TriggerHappy</code> 역할을 가진 모든 사용자는 특정 프로 시저 <code>apoc.trigger.add</code>를 실행할 수 있습니다.
</ul>
</div>
</div>                                                                                                        

<span class="glyphicon glyphicon-info-sign" aria-hidden="true"></span> 모드를 위반하는 데이터베이스 작업을 실행하려고 하면 절차가 실패합니다. 예를 들어, `READ` 모드를 할당한 절차가 쓰기 작업을 수행하도록 설정된 경우 실패합니다. 이 문제는 사용자 또는 역할 구성에 관계 없이 발생합니다. 