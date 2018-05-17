### 7.1.6. 하위 그래프 엑세스 제어                  

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

##### **기본 사용자 시나리오**

In the native users scenario, a custom role is created and assigned to the relevant user(s).                                             

<div class="example">
Example 7.18. Native users scenario                                                   
<div class="example-contents">
In this example, we will use Cypher to create a custom `accounting` role and assign it to a pre-existing user, `billsmith`.                     

<pre> 
<code>
`CALL dbms.security.createRole('accounting')
CALL dbms.security.addRoleToUser('accounting', 'billsmith')`                                       
</code>
</pre>
</div>
</div>

##### **Federated users scenario (LDAP)**

                         In the LDAP scenario, the LDAP user group is mapped to a custom role in Neo4j.                                             Example 7.19. Federated users scenario (LDAP)                                                   In this example, we will use Cypher to create a custom `accounting` role.                           `CALL dbms.security.createRole('accounting')`We will then map the `accounting` role to the LDAP group with groupID `101`.                           `dbms.security.realms.ldap.authorization.group_to_role_mapping=101=accounting`                                       

#### 7.1.6.2. Manage procedure permissions                     

In standard use, procedures and functions are executed according to the same security rules as regular Cypher statements,               as described in [Section 7.1.4.1, “Native roles”](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/native-roles/).               For example, users assigned any one of the native roles `publisher`, `architect` and `admin` will be able to execute a procedure with `mode=WRITE`, whereas a user assigned only the `reader` role will not be allowed to execute the procedure.            

For the purpose of subgraph access control, we allow specific roles to execute procedures that they would otherwise be prevented               from accessing through their assigned native roles.               The user is given the privilege that comes with the mode of the procedure, during the execution of the procedure only.               The following two parameters are used to configure the desired behavior:            

-   **dbms.security.procedures.default_allowed**

                         The setting `dbms.security.procedures.default_allowed` defines a single role that is allowed to execute any procedure or function that is not matched by the `dbms.security.procedures.roles` configuration.                                                                  Example 7.20. Configure a default role that can execute procedures and functions                                                   Assume that we have the following configuration:`dbms.security.procedures.default_allowed=superAdmin`This will have the following effects:                                                                                          If the setting `dbms.security.procedures.roles` is left unconfigured, the role `superAdmin` will be able to execute all custom procedures and functions.                                                                  If the setting `dbms.security.procedures.roles` has some roles and functions defined, the role `superAdmin` will be able to execute all custom procedures and functions that are *not* configured by `dbms.security.procedures.roles`.                                                                                                                                                         

-   **dbms.security.procedures.roles**

                         The `dbms.security.procedures.roles` setting provides fine-grained control over procedures.                                                                  Example 7.21. Configure roles for the execution of specific procedures                                                   Assume that we have the following configuration:`dbms.security.procedures.roles=apoc.convert.*:Converter;apoc.load.json.*:Converter,DataSource;apoc.trigger.add:TriggerHappy`This will have the following effects:                                                                                          All users with the role `Converter` will be able to execute all procedures in the `apoc.convert` namespace.                                                                  All users with the roles `Converter` and `DataSource` will be able to execute procedures in the `apoc.load.json` namespace.                                                                  All users with the role `TriggerHappy` will be able to execute the specific procedure `apoc.trigger.add`.                                                                                                                                                         

| **   | A procedure will fail if it attempts to execute database operations that violates its mode.                              For example, a procedure assigned the mode of `READ` will fail if it is programmed to do write actions.                              This will happen regardless of user or role configuration. |
| ---- | ---------------------------------------- |
|      |                                          |