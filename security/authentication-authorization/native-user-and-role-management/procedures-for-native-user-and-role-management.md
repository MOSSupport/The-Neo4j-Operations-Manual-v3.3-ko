#### 7.1.4.4. Procedures for native user and role management 

<div class="abstract">
	<p>이 절에서는 Neo4j Cluster의 기본 사용자와 역할 및 역할 할당을 전파하는 방법에 대해서 설명합니다. 
	</p>
</div>
This section describes procedures for native user and role management in Neo4j.


In Neo4j, native user and role management are managed by using built-in procedures through Cypher. This section gives a list of all the security procedures for user management along with some simple examples. Use the Neo4j Browser or the Neo4j Cypher Shell to run the examples provided.

The section describes the following:

-   [List all users](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/procedures/#userauth-list-all-users)
-   [List all roles](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/procedures/#user-roles-list-all-roles)
-   [List all roles for a user](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/procedures/#user-roles-list-all-roles-for-user)
-   [List all users for a role](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/procedures/#user-roles-list-all-users-for-role)
-   [Create a user](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/procedures/#userauth-add-user)
-   [Delete a user](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/procedures/#userauth-delete-user)
-   [Assign a role to a user](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/procedures/#user-roles-assign-role-to-user)
-   [Remove a role from a user](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/procedures/#user-roles-remove-role-from-user)
-   [Create a custom role](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/procedures/#user-roles-create-role)
-   [Delete a custom role](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/procedures/#user-roles-delete-role)
-   [Suspend a user](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/procedures/#userauth-suspend-user)
-   [Activate a user](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/procedures/#userauth-activate-user)
-   [Change a user’s password](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/procedures/#userauth-change-users-password)
-   [Change the current user’s password](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/procedures/#userauth-change-your-password)
-   [List roles per procedure](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/procedures/#list-roles-per-procedure)

##### List all users

An [administrator](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-administrator) is able to view the details of every [user](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-user) in the system.

**Syntax:**

`CALL dbms.security.listUsers()`

**Returns:**

| Name       | Type         | Description                              |
| ---------- | ------------ | ---------------------------------------- |
| `username` | String       | This is the user’s username.             |
| `roles`    | List<String> | This is a list of roles assigned to the user. |
| `flags`    | List<String> | This is a series of flags indicating whether the user is suspended or needs to change their password. |

**Exceptions:**

<div class="example">
Example 7.1. List all users
<div class="example-contents">
The following example shows, for each [user](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-user) in the system, the username, the [roles](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/native-roles/) assigned to the user, and whether the user is suspended or needs to change their password.

```
CALL dbms.security.listUsers()
```


```
+---------------------------------------------------------------------+
| username | roles                     | flags                        |
+---------------------------------------------------------------------+
| "neo4j"  | ["admin"]                 | []                           |
| "anne"   | []                        | ["password_change_required"] |
| "bill"   | ["reader"]                | ["is_suspended"]             |
| "john"   | ["architect","publisher"] | []                           |
+---------------------------------------------------------------------+
4 rows
```

</div>
</div>

##### List all roles

An [administrator](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-administrator) is able to view all assigned users for each role in the system.

**Syntax:**

`CALL dbms.security.listRoles()`

**Returns:**

| Name    | Type         | Description                              |
| ------- | ------------ | ---------------------------------------- |
| `role`  | String       | This is the name of the role.            |
| `users` | List<String> | This is a list of the usernames of all users who have been assigned the role. |

**Exceptions:**

<div class="example">
Example 7.2. List all roles
<div class="example-contents">
The following example shows, for each role in the system, the name of the role and the usernames of all assigned users.


<code>
CALL dbms.security.listRoles()
</code>

<pre>
<code>
+------------------------------+
| role        | users          |
+------------------------------+
| "reader"    | ["bill"]       |
| "architect" | []             |
| "admin"     | ["neo4j"]      |
| "publisher" | ["john","bob"] |
+------------------------------+
4 rows
</code>
</pre>
</div>
</div>

##### List all roles for a user

Any [active user](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-active-user) is able to view all of their assigned [roles](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/native-roles/). An [administrator](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-administrator) is able to view all assigned roles for any [user](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-user) in the system.

**Syntax:**