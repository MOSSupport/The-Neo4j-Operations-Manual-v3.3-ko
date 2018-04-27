#### 7.1.4.3. 사용자와 역할 전파

<div class="abstract">
	<p>이 절에서는 Neo4j Cluster의 기본 사용자와 역할 및 역할 할당을 전파하는 방법에 대해서 설명합니다. 
	</p>
</div>
This section describes how to propagate native users, roles, and role assignments across a Neo4j cluster.

기본 사용자, 역할 및 역할 할당은 `auth`와 `roles`라는 파일에 저장이 됩니다.

Native users, roles and role assignments are stored in files named `auth` and `roles`. The files are located in the *data*directory (see [Section 3.1, “File locations”](https://neo4j.com/docs/operations-manual/3.3/configuration/file-locations/)) in a subdirectory called *dbms*. Neo4j automatically reloads the stored users and assigned roles from disk every five seconds. Changes to users and roles are applied only to the Neo4j instance on which the commands are executed. This means that changes are not automatically propagated across a cluster of Neo4j instances, but have to be specifically provided for.

To propagate changes to native users, custom roles, and role assignments across a cluster, there are three options:

-   Manually copy users and roles files on disk to all other cluster instances
-   Use a shared network folder to store users and roles files
-   Create an automated process that synchronizes the stored data across the cluster using, for example, a combination of `rsync` and `crontab`
ㄹㅇㄹㄴㅇ
We note that the recommended solution for clustered security is to use the LDAP or plugin auth provider.