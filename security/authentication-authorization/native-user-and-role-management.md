### 7.1.4. 기본 사용자와 역할 관리 <code>Enterprise Edition</code> 

This section describes native user and role management in Neo4j.

For managing native users, Neo4j offers additional actions for an administrator:

-   View all users and roles (including viewing all roles for a user and viewing all users for a role)
-   Create and delete any other user
-   Assign and remove a role from a user
-   Create and delete a custom-defined role
-   Suspend and activate any other user
-   Change the password for any user

When an administrator suspends or deletes another user, the following rules apply:

-   Administrators can suspend or delete any other user (including other administrators), but not themselves
-   Deleting a user terminates all of the user’s running queries and sessions
-   All queries currently running for the deleted user are rolled back
-   The user will no longer be able to log back in (until re-activated by an administrator if suspended)
-   There is no need to remove assigned roles from a user prior to deleting the user.

The section describes the following:

-   [Native roles](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/native-roles/)
-   [Custom roles](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/custom-roles/)
-   [Propagate users and roles](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/propagate-users-and-roles/)
-   [Procedures for native user and role management](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/procedures/)