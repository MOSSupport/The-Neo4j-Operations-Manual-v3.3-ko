# 7. 보안

<i class="fa fa-info-circle" style="font-size:24px;color:blue"></i> 이번 장은 Neo4j의 보안에 대해서 설명합니다. 


데이터를 보호하려면 먼저 서버 및 네트워크 보안과 관련된 업계 모범 사례에 따라 실제 데이터 보안을 확인하십시오. To protect your data, first ensure your physical data security by following industry best practices with regard to server and network security. Ensure that your Neo4j deployment adheres to your company’s information security guidelines by setting up the appropriate authentication and authorization rules.

This chapter includes the following:

* [Authentication and authorization](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/)

  * [Introduction](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/introduction/)
  * [Terminology](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/)
  * [Enabling authentication and authorization](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/enable/)
  * [Native user and role management](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/)

    * [Native roles](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/native-roles/)
    * [Custom roles](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/custom-roles/)
    * [Propagate users and roles](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/propagate-users-and-roles/)
    * [Procedures for native user and role management](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/procedures/)

  * [Integration with LDAP](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/ldap-integration/)

  * [Subgraph access control](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/subgraph-access-control/)

* [Securing extensions](https://neo4j.com/docs/operations-manual/3.3/security/securing-extensions/)

* [Unified SSL framework](https://neo4j.com/docs/operations-manual/3.3/security/ssl-framework/)
* [Credentials handling in Neo4j Browser](https://neo4j.com/docs/operations-manual/3.3/security/browser/)
* [Security checklist](https://neo4j.com/docs/operations-manual/3.3/security/checklist/)

Additionally, logs can be useful for continuous analysis, or for specific investigations. Facilities are available for producing[security event logs](https://neo4j.com/docs/operations-manual/3.3/monitoring/logging/security-events-logging/)as well as[query logs](https://neo4j.com/docs/operations-manual/3.3/monitoring/logging/query-logging/)as described in[Chapter 8,_Monitoring_](https://neo4j.com/docs/operations-manual/3.3/monitoring/).

