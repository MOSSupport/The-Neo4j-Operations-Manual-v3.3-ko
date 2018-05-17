## 7.5. 보안 점검목록                 

<div class="abstract">
	<p>이 절에서는 Neo4j의 보안과 관련된 권장 사항을 요약하여 설명합니다.
	</p>
</div>

다음은 Neo4j 내의 특정 영역을 강조하는 간단한 체크리스트입니다.이 체크리스트는 애플리케이션에 적절한 보안 수준을 보장하기 위해 몇 가지 주의가 필요할 수 있습니다.

1.  안전한 네트워크의 안전한 서버에 Neo4j 배포 :
    1. 서브넷과 방화벽을 사용하십시오.
    2. 필요한 포트만 열어야합니다. 관련 포트 목록은 [3.2 절. "포트"](/configuration/ports.md)를 참조하십시오.                        
2.  비활성데이터 보호 :
    1.  볼륨 암호화 사용 (예. Bitlocker).
    2.  데이터베이스 덤프 접속 관리 (참조 [10.3절, "데이터베이스의 dump와 load"](/tools/dump-load.md)) 와 백업 (참조 [6.2절, "백업 수행"](/backup/perform-backup.md))을 확인합니다.. 특히 `dbms.backup.address` 설정에 지정된 포트에 대한 외부 액세스가 없는지 확인하십시오 (기본값은 6362 임). 이 포트를 보호하지 못하면 인증되지 않은 사용자가 다른 컴퓨터에 데이터베이스 복사본을 만들 수있는 보안 구멍이 생깁니다.                    
    3.  데이터 파일 및 트랜잭션 로그에 대한 액세스를 관리합니다. [3.1.3절, "권한"](/configuration/file-locations.md/#313-권한)의 지시 사항을 제외하고 Neo4j 파일에 대한 모든 운영 체제 액세스를 금지하십시오Manage access to data files and transaction logs.                           
3.  전송 중 데이터 보호:
    1.  Neo4j 데이터베이스에 원격 액세스하려면 암호화 된 볼트 또는 HTTPS 만 열어 둡니다.
    2.  신뢰할 수있는 인증 기관에서 발급 한 SSL 인증서를 사용하십시오.
        1.  암호화 된 통신을 사용하도록 Neo4j 설치를 구성하려면 [7.3절, "통합 SSL 프레임워크"](/security/ssl-framework.md)을 참조하십시오.
        2.  Bolt 및 HTTPS 커넥터 구성에 대해서는 [3.6절, "Neo4j 커넥터 환경 설정"](/configuration/connectors.md)을 참조하십시오.
        3.  LDAP를 사용하는 경우 StartTLS를 통해 LDAP 시스템을 암호화로 구성하십시오. [StartTLS를 통한 암호화가 있는 LDAP 사용] 절을 참조하십시오.(/security/authentication-authorization/ldap-integration.md/#starttls를-통한-암호화가-있는-ldap-사용).                                 
4.  Be on top of the security for custom extensions:
    1.  Validate any custom code that you deploy (procedures and unmanaged extensions) and ensure that they do not expose any parts                           of the product or data unintentionally.                        
    2.  Survey the settings `dbms.security.procedures.unrestricted` and `dbms.security.procedures.whitelist` to ensure that they exclusively contain intentionally exposed extensions.                        
5.  Ensure the correct file permissions on the Neo4j files.                  Only the operating system user that Neo4j runs as should have permissions to those files.                  Refer to [Section 3.1.3, “Permissions”](https://neo4j.com/docs/operations-manual/current/configuration/file-locations/#file-locations-permissions) for instructions on permission levels.                  In particular, protect data files, transaction logs and database dumps from unauthorized read access.                  Protect against the execution of unauthorized extensions by restricting access to the *bin*, *lib*, and *plugins* directories.               
6.  If `LOAD CSV` is enabled, ensure that it does not allow unauthorized users to import data.                  How to configure `LOAD CSV` is described in [Developer Manual → `LOAD CSV`](https://neo4j.com/docs/developer-manual/3.2/cypher/clauses/load-csv/).               
7.  Do not turn off Neo4j authentication.                  Refer to [Section 7.1.3, “Enabling authentication and authorization”](https://neo4j.com/docs/operations-manual/current/security/authentication-authorization/enable/) for details on this setting.               
8.  Survey your *neo4j.conf* file (see [Section 3.1, “File locations”](https://neo4j.com/docs/operations-manual/current/configuration/file-locations/)) for ports relating to deprecated functions (such as neo4j-shell, controlled by the parameter `dbms.shell.port`) and remote JMX (controlled by the parameter setting `dbms.jvm.additional=-Dcom.sun.management.jmxremote.port=3637`).               
9.  Review [Section 7.4, “Browser credentials handling”](https://neo4j.com/docs/operations-manual/current/security/browser/) to determine whether the default credentials handling in Neo4j Browser complies with your security regulations.                  Follow the instructions to configure it if necessary.               
10.  Use the latest patch version of Neo4j.