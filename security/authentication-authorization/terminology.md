### 7.1.2. 용 어

<div class="abstract">
	<p>이 절에서는 Neo4j에서 인증 및 권한 부여와 관련된 용어를 나열합니다. 
	</p>
</div>
This section lists the relevant terminology related to authentication and authorization in Neo4j.

The following terms are relevant to role-based access control within Neo4j:

active user

A

[user](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-user)

who is active within the system and can perform actions prescribed by any assigned

[roles](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-role)

on the data. This is in contrast to a

[suspended user](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-suspended-user)

.

administrator

This is a

[user](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-user)

who has been assigned the

[`admin`](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/native-roles/#admin-role)

role.

current user

This is the currently logged-in

[user](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-user)

invoking the commands described in this chapter.

password policy

The password policy is a set of rules of what makes up a valid password. For Neo4j, the following rules apply:

* The password cannot be the empty string.
* When changing passwords, the new password cannot be the same as the previous password.

role

This is a collection of actions — such as read and write — permitted on the data. There are two types of roles in Neo4j:

* Native roles are described in
  [Section 7.1.4.1, “Native roles”](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/native-roles/)
  .
* Custom roles are described in
  [Section 7.1.4.2, “Custom roles”](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/custom-roles/)
  .

suspended user

A

[user](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-user)

who has been suspended is not able to access the database in any capacity, regardless of any assigned

[roles](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-role)

.

user

* A user is composed of a username and credentials, where the latter is a unit of information, such as a password, verifying the identity of a user.
* A user may represent a human, an application etc.



