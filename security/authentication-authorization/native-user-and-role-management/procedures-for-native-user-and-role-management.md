#### 7.1.4.4. 기본 사용자 및 역할 관리 절차 

<div class="abstract">
	<p>이 절에서는 Neo4j Cluster의 기본 사용자 및 역할 관리하는 절차에 대해서 설명합니다. 
	</p>
</div>

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

Example 7.1. List all users

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
<p>

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

`CALL dbms.security.listRolesForUser(*username*)`

**Arguments:**

| Name       | Type   | Description                       |
| ---------- | ------ | --------------------------------- |
| `username` | String | This is the username of the user. |

**Returns:**

| Name    | Type   | Description                              |
| ------- | ------ | ---------------------------------------- |
| `value` | String | This returns all roles assigned to the requested user. |

**Exceptions:**

| The current user is not an administrator and the username does not match that of the current user. |
| ---------------------------------------- |
| The username does not exist in the system. |

**Considerations:**

-   This procedure may be invoked by the current user to view their roles, irrespective of whether or not the current user is an administrator.
-   This procedure may be invoked by an administrator to view the roles for another user.

Example 7.3. List all roles for a user

The following example lists all the roles for the user with username '**johnsmith**', who has the [roles](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/native-roles/) `reader` and `publisher`.

```
CALL dbms.security.listRolesForUser('johnsmith')
```

```
+-------------+
| value       |
+-------------+
| "reader"    |
| "publisher" |
+-------------+
2 rows
```

##### List all users for a role

An [administrator](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-administrator) is able to view all assigned [users](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-user) for a [role](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/native-roles/).

**Syntax:**

`CALL dbms.security.listUsersForRole(*roleName*)`

**Arguments:**

| Name       | Type   | Description                   |
| ---------- | ------ | ----------------------------- |
| `roleName` | String | This is the name of the role. |

**Returns:**

| Name    | Type   | Description                              |
| ------- | ------ | ---------------------------------------- |
| `value` | String | This returns all assigned users for the requested role. |

**Exceptions:**

| The current user is not an administrator. |
| ---------------------------------------- |
| The role name does not exist in the system. |

Example 7.4. List all users for a role

The following example lists all the assigned users - '**bill**' and '**anne**' - for the [role](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/native-roles/)`publisher`.

```
CALL dbms.security.listUsersForRole('publisher')
```

```
+--------+
| value  |
+--------+
| "bill" |
| "anne" |
+--------+
2 rows
```

##### Create a user

An [administrator](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-administrator) is able to create a new [user](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-user). This action ought to be followed by assigning a [role](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/native-roles/) to the user, which is described [here](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/procedures/#user-roles-assign-role-to-user).

**Syntax:**

`CALL dbms.security.createUser(*username*, *password*, *requirePasswordChange*)`

**Arguments:**

| Name                    | Type    | Description                              |
| ----------------------- | ------- | ---------------------------------------- |
| `username`              | String  | This is the user’s username.             |
| `password`              | String  | This is the user’s password.             |
| `requirePasswordChange` | Boolean | This is optional, with a default of `true`. If this is `true`, (i) the user will be forced to change their password when they log in for the first time, and (ii) until the user has changed their password, they will be forbidden from performing any other operation. |

**Exceptions:**

| The current user is not an administrator. |
| ---------------------------------------- |
| The username either contains characters other than the ASCII characters between `!` and `~`, or contains `:`and `,`. |
| The username is already in use within the system. |
| The password is the empty string.        |

Example 7.5. Create a user

The following example creates a [user](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-user) with the username '**johnsmith**' and password '**h6u4%kr**'. When the user '**johnsmith**' logs in for the first time, he will be required to [change his password](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/procedures/#userauth-change-your-password).

```
CALL dbms.security.createUser('johnsmith', 'h6u4%kr')
```

##### Delete a user

An [administrator](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-administrator) is able to delete permanently a [user](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-user) from the system. It is not possible to undo this action, so, if in any doubt, consider [suspending the user](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/procedures/#userauth-suspend-user) instead.

**Syntax:**

`CALL dbms.security.deleteUser(*username*)`

**Arguments:**

| Name       | Type   | Description                              |
| ---------- | ------ | ---------------------------------------- |
| `username` | String | This is the username of the user to be deleted. |

**Exceptions:**

| The current user is not an administrator. |
| ---------------------------------------- |
| The username does not exist in the system. |
| The username matches that of the current user (i.e. deleting the current user is not permitted). |

**Considerations:**

-   It is not necessary to remove any assigned [roles](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/native-roles/) from the user prior to deleting the user.
-   Deleting a user will terminate with immediate effect all of the user’s sessions and roll back any running transactions.
-   As it is not possible for the current user to delete themselves, there will always be at least one administrator in the system.

Example 7.6. Delete a user

The following example deletes a [user](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-user) with the username '**janebrown**'.

```
CALL dbms.security.deleteUser('janebrown')
```

##### Assign a role to a user

An [administrator](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-administrator) is able to assign a [role](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/native-roles/) to any [user](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-user) in the system, thus allowing the user to perform a series of actions upon the data.

**Syntax:**

`CALL dbms.security.addRoleToUser(*roleName*, *username*)`

**Arguments:**

| Name       | Type   | Description                              |
| ---------- | ------ | ---------------------------------------- |
| `roleName` | String | This is the name of the role to be assigned to the user. |
| `username` | String | This is the username of the user who is to be assigned the role. |

**Exceptions:**

| The current user is not an administrator. |
| ---------------------------------------- |
| The username does not exist in the system. |
| The username contains characters other than alphanumeric characters and the ‘_’ character. |
| The role name does not exist in the system. |
| The role name contains characters other than alphanumeric characters and the ‘_’ character. |

**Considerations:**

-   This is an idempotent procedure.

Example 7.7. Assign a role to a user

The following example assigns the [role](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/native-roles/) `publisher` to the user with username '**johnsmith**'.

```
CALL dbms.security.addRoleToUser('publisher', 'johnsmith')
```

##### Remove a role from a user

An [administrator](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-administrator) is able to remove a [role](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/native-roles/) from any [user](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-user) in the system, thus preventing the user from performing upon the data any actions prescribed by the role.

**Syntax:**

`CALL dbms.security.removeRoleFromUser(*roleName*, *username*)`

**Arguments:**

| Name       | Type   | Description                              |
| ---------- | ------ | ---------------------------------------- |
| `roleName` | String | This is the name of the role which is to be removed from the user. |
| `username` | String | This is the username of the user from which the role is to be removed. |

**Exceptions:**

| The [current user](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-current-user) is not an administrator. |
| ---------------------------------------- |
| The username does not exist in the system. |
| The role name does not exist in the system. |
| The username is that of the current user and the role is `admin`. |

**Considerations:**

-   If the username is that of the current user and the role name provided is `admin`, an error will be thrown; i.e. the current user may not be demoted from being an administrator.
-   As it is not possible for the current user to remove the `admin` role from themselves, there will always be at least one administrator in the system.
-   This is an idempotent procedure.

Example 7.8. Remove a role from a user

The following example removes the [role](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/native-roles/) `publisher` from the user with username '**johnsmith**'.

```
CALL dbms.security.removeRoleFromUser('publisher', 'johnsmith')
```

##### Create a custom role

An [administrator](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-administrator) is able to create custom roles in the system.

**Syntax:**

`CALL dbms.security.createRole(*roleName*)`

**Arguments:**

| Name       | Type   | Description                              |
| ---------- | ------ | ---------------------------------------- |
| `roleName` | String | This is the name of the role to be created. |

**Exceptions:**

| The current user is not an administrator. |
| ---------------------------------------- |
| The role name already exists in the system. |
| The role name is empty.                  |
| The role name contains characters other than alphanumeric characters and the ‘_’ character. |
| The role name matches one of the native roles: `reader`, `publisher`, `architect`, and `admin`. |

Example 7.9. Create a new custom role

The following example creates a new custom role.

```
CALL dbms.security.createRole('operator')
```

##### Delete a custom role

An [administrator](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-administrator) is able to delete custom roles from the system. The native roles `reader`, `publisher`, `architect`, and `admin` (see [Section 7.1.4.1, “Native roles”](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/native-roles/)) cannot be deleted.

**Syntax:**

`CALL dbms.security.deleteRole(*roleName*)`

**Arguments:**

| Name       | Type   | Description                              |
| ---------- | ------ | ---------------------------------------- |
| `roleName` | String | This is the name of the role to be deleted. |

**Exceptions:**

| The current user is not an administrator. |
| ---------------------------------------- |
| The role name does not exist in the system. |
| The role name matches one of the native roles: `reader`, `publisher`, `architect`, and `admin`. |

**Considerations:**

-   Any role assignments will be removed.

Example 7.10. Delete a custom role

The following example deletes the custom role '**operator**' from the system.

```
CALL dbms.security.deleteRole('operator')
```

##### Suspend a user

An [administrator](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-administrator) is able to suspend a [user](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-user) from the system. The suspended user may be [activated](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/procedures/#userauth-activate-user) at a later stage.

**Syntax:**

`CALL dbms.security.suspendUser(*username*)`

**Arguments:**

| Name       | Type   | Description                              |
| ---------- | ------ | ---------------------------------------- |
| `username` | String | This is the username of the user to be suspended. |

**Exceptions:**

| The current user is not an administrator. |
| ---------------------------------------- |
| The username does not exist in the system. |
| The username matches that of the current user (i.e. suspending the current user is not permitted). |

**Considerations:**

-   Suspending a user will terminate with immediate effect all of the user’s sessions and roll back any running transactions.
-   All of the suspended user’s attributes — assigned [roles](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/native-roles/) and password — will remain intact.
-   A suspended user will not be able to log on to the system.
-   As it is not possible for the current user to suspend themselves, there will always be at least one active administrator in the system.
-   This is an idempotent procedure.

Example 7.11. Suspend a user

The following example suspends a [user](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-user) with the username '**billjones**'.

```
CALL dbms.security.suspendUser('billjones')
```

##### Activate a user

An [administrator](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-administrator) is able to activate a suspended [user](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-user) so that the user is once again able to access the data in their original capacity.

**Syntax:**

`CALL dbms.security.activateUser(*username*, *requirePasswordChange*)`

**Arguments:**

| Name                    | Type    | Description                              |
| ----------------------- | ------- | ---------------------------------------- |
| `username`              | String  | This is the username of the [user](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-user) to be activated. |
| `requirePasswordChange` | Boolean | This is optional, with a default of `true`. If this is `true`, (i) the user will be forced to change their password when they next log in, and (ii) until the user has changed their password, they will be forbidden from performing any other operation. |

**Exceptions:**

| The current user is not an administrator. |
| ---------------------------------------- |
| The username does not exist in the system. |
| The username matches that of the current user (i.e. activating the current user is not permitted). |

**Considerations:**

-   This is an idempotent procedure.

Example 7.12. Activate a user

The following example activates a [user](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-user) with the username '**jackgreen**'. When the user '**jackgreen**' next logs in, he will be required to [change his password](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/procedures/#userauth-change-your-password).

```
CALL dbms.security.activateUser('jackgreen')
```

##### Change a user’s password

An [administrator](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-administrator) is able to change the password of any [user](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-user) within the system. Alternatively, the [current user](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-current-user) may change their own password.

**Syntax:**

`CALL dbms.security.changeUserPassword(*username*, *newPassword*, *requirePasswordChange*)`

**Arguments:**

| Name                    | Type    | Description                              |
| ----------------------- | ------- | ---------------------------------------- |
| `username`              | String  | This is the username of the user whose password is to be changed. |
| `newPassword`           | String  | This is the new password for the user.   |
| `requirePasswordChange` | Boolean | This is optional, with a default of `true`. If this is `true`, (i) the user will be forced to change their password when they next log in, and (ii) until the user has changed their password, they will be forbidden from performing any other operation. |

**Exceptions:**

| The current user is not an administrator and the username does not match that of the current user. |
| ---------------------------------------- |
| The username does not exist in the system. |
| The password is the empty string.        |
| The password is the same as the user’s previous password. |

**Considerations:**

-   This procedure may be invoked by the current user to change their own password, irrespective of whether or not the current user is an administrator.
-   This procedure may be invoked by an administrator to change another user’s password.
-   In addition to changing the user’s password, this will terminate with immediate effect all of the user’s sessions and roll back any running transactions.

Example 7.13. Change a user’s password

The following example changes the password of the [user](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-user) with the username '**joebloggs**' to '**h6u4%kr**'. When the user '**joebloggs**' next logs in, he will be required to [change his password](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/procedures/#userauth-change-your-password).

```
CALL dbms.security.changeUserPassword('joebloggs', 'h6u4%kr')
```

##### Change the current user’s password

Any [active user](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-active-user) is able to change their own password at any time.

**Syntax:**

`CALL dbms.security.changePassword(*password*, *requirePasswordChange*)`

**Arguments:**

| Name                    | Type    | Description                              |
| ----------------------- | ------- | ---------------------------------------- |
| `password`              | String  | This is the new password for the [current user](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-current-user). |
| `requirePasswordChange` | Boolean | This is optional, with a default of `false`. If this is `true`, (i) the current user will be forced to change their password when they next log in, and (ii) until the current user has changed their password, they will be forbidden from performing any other operation. |

**Exceptions:**

| The password is the empty string.        |
| ---------------------------------------- |
| The password is the same as the current user’s previous password. |

Example 7.14. Change the current user’s password

The following example changes the password of the current user to '**h6u4%kr**'.

```
CALL dbms.security.changePassword('h6u4%kr')
```

##### List roles per procedure

Any [active user](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-active-user) is able to view all procedures in the system, including which role(s) have the privilege to execute them.

**Syntax:**

`CALL dbms.procedures()`

**Returns:**

| Name          | Type         | Description                              |
| ------------- | ------------ | ---------------------------------------- |
| `name`        | String       | This is the name of the procedure.       |
| `signature`   | String       | This is the signature of the procedure.  |
| `description` | String       | This is a description of the procedure.  |
| `roles`       | List<String> | This is a list of roles having the privilege to execute the procedure. |

Example 7.15. List role per procedure

The following example shows, for four of the security procedures, the procedure name, the description, and which roles have the privilege to execute the procedure.

```
CALL dbms.procedures()
YIELD name, signature, description, roles
WITH name, description, roles
WHERE name contains 'security'
RETURN name, description, roles
ORDER BY name
LIMIT 4
+--------------------------------------------------------------------------------------------------------------+
|name                              |description                          |roles                                |
+--------------------------------------------------------------------------------------------------------------+
|"dbms.security.activateUser"      |"Activate a suspended user."         | ["admin"]                           |
|"dbms.security.addRoleToUser"     |"Assign a role to the user."         | ["admin"]                           |
|"dbms.security.changePassword"    |"Change the current user's password."| ["reader","editor","publisher", ... |
|"dbms.security.changeUserPassword"|"Change the given user's password."  | ["admin"]                           |
+--------------------------------------------------------------------------------------------------------------+
4 rows
```



