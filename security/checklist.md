## 7.5. Security checklist                  

<div class="abstract">
	<p>이 절에서는 Neo4j의 인증 및 권한 부여에 대해서 설명합니다. 
	</p>
</div>
This section provides a summary of recommendations regarding security in Neo4j.

Below is a simple checklist highlighting the specific areas within Neo4j that may need some extra attention in order to ensure            the appropriate level of security for your application.         

1.  Deploy Neo4j on safe servers in safe networks:
    1.  Use subnets and firewalls.
    2.  Only open up for the necessary ports.                           For a list of relevant ports see [Section 3.2, “Ports”](https://neo4j.com/docs/operations-manual/current/configuration/ports/).                        
2.  Protect data-at-rest:
    1.  Use volume encryption (e.g. Bitlocker).
    2.  Manage access to database dumps (refer to [Section 10.3, “Dump and load databases”](https://neo4j.com/docs/operations-manual/current/tools/dump-load/)) and backups (refer to [Section 6.2, “Perform a backup”](https://neo4j.com/docs/operations-manual/current/backup/perform-backup/)).                           In particular, ensure that there is no external access to the port specified by the setting `dbms.backup.address` (this defaults to 6362).                           Failing to protect this port leaves a security hole open by which an unauthorized user can make a copy of the database onto                           a different machine.                        
    3.  Manage access to data files and transaction logs.                           Prohibit all operating system access to Neo4j files except as instructed in [Section 3.1.3, “Permissions”](https://neo4j.com/docs/operations-manual/current/configuration/file-locations/#file-locations-permissions).                        
3.  Protect data-in-transit:
    1.  For remote access to the Neo4j database, only open up for encrypted Bolt or HTTPS.
    2.  Use SSL certificates issued from a trusted Certificate Authority.
        1.  For configuring your Neo4j installation to use encrypted communication, refer to [Section 7.3, “Unified SSL framework”](https://neo4j.com/docs/operations-manual/current/security/ssl-framework/).                                 
        2.  For configuring your Bolt and/or HTTPS connectors, refer to [Section 3.6, “Configure Neo4j connectors”](https://neo4j.com/docs/operations-manual/current/configuration/connectors/).                                 
        3.  If using LDAP, configure your LDAP system with encryption via StartTLS; see [the section called “Use LDAP with encryption via StartTLS”](https://neo4j.com/docs/operations-manual/current/security/authentication-authorization/ldap-integration/#ldap-encrypted-starttls).                                 
4.  Be on top of the security for custom extensions:
    1.  Validate any custom code that you deploy (procedures and unmanaged extensions) and ensure that they do not expose any parts                           of the product or data unintentionally.                        
    2.  Survey the settings `dbms.security.procedures.unrestricted` and `dbms.security.procedures.whitelist` to ensure that they exclusively contain intentionally exposed extensions.                        
5.  Ensure the correct file permissions on the Neo4j files.                  Only the operating system user that Neo4j runs as should have permissions to those files.                  Refer to [Section 3.1.3, “Permissions”](https://neo4j.com/docs/operations-manual/current/configuration/file-locations/#file-locations-permissions) for instructions on permission levels.                  In particular, protect data files, transaction logs and database dumps from unauthorized read access.                  Protect against the execution of unauthorized extensions by restricting access to the *bin*, *lib*, and *plugins* directories.               
6.  If `LOAD CSV` is enabled, ensure that it does not allow unauthorized users to import data.                  How to configure `LOAD CSV` is described in [Developer Manual → `LOAD CSV`](https://neo4j.com/docs/developer-manual/3.2/cypher/clauses/load-csv/).               
7.  Do not turn off Neo4j authentication.                  Refer to [Section 7.1.3, “Enabling authentication and authorization”](https://neo4j.com/docs/operations-manual/current/security/authentication-authorization/enable/) for details on this setting.               
8.  Survey your *neo4j.conf* file (see [Section 3.1, “File locations”](https://neo4j.com/docs/operations-manual/current/configuration/file-locations/)) for ports relating to deprecated functions (such as neo4j-shell, controlled by the parameter `dbms.shell.port`) and remote JMX (controlled by the parameter setting `dbms.jvm.additional=-Dcom.sun.management.jmxremote.port=3637`).               
9.  Review [Section 7.4, “Browser credentials handling”](https://neo4j.com/docs/operations-manual/current/security/browser/) to determine whether the default credentials handling in Neo4j Browser complies with your security regulations.                  Follow the instructions to configure it if necessary.               
10.  Use the latest patch version of Neo4j.