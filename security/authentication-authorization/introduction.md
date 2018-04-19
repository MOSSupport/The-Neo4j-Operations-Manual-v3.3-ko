## 7.1.1. 소개 <code>Enterprise Edition</code>

이 세션에서는 Neo4j 의 인증 및 권한 부여에 대한 개요를 설명합니다.

Neo4j의 보안은 인증 및 권한 부여에 의해 제어됩니다. 인증은 인증 된 사용자가 특정 작업을 수행할 수 있는지 여부를 확인하는 것과 관련하여 사용자가 올바른 사용자임을 확인하는 과정입니다.

권한부여는 역할 기반의 접속을 제어를 관리합니다(RBAC). Neo4j 보안 모델의 핵심은 네 가지 미리 정의 된 그래프 글로벌 데이터 액세스 역할(graph-global data-access roles)을 중심으로합니다: reader, publisher, architect and admin. 각 역할에는 Neo4j 데이터 그래프 및 스키마에서 허용되는 일련의 승인 된 작업이 포함됩니다. 사용자는 이러한 사용자 지정 역할 외에 하나 또는 하나 이상의 역할에 할당 될 수 있습니다. 

Neo4j는 사용자 인증 및 권한 부여를 수행 할 수있는 여러 인증 공급자를 지원합니다.

- 기본 인증 제공자(Native auth provider).
- LDAP 인증 제공자(LDAP auth provider).
- 사용자 작성 플러그인 인증 제공자(Custom-built plugin auth providers).
- SSO를 위한 케르베로스 인증(Kerberos authentication with single sign-on).

### 7.1.1.1. 기본 인증 제공자(Native auth provider)

Neo4j 기본 인증은 사용자 및 역할 정보를 로컬에 저장합니다. 이 옵션을 사용하면 7.1.4절 "기본 사용자 및 역할 관리"에 설명하는 절차에 따라 전체 사용자 관리를 사용할 수 있습니다.

### 7.1.1.2. LDAP 인증 제공자(LDAP auth provider)

인증 및 권한 부여를 제어하는 또 다른 방법은 내장 LDAP 커넥터를 통해 액세스 할 수 있는 Active Directory 또는 OpenLDAP같은 외부 보안 소프트웨어를 사용하는 것입니다. Active Directory를 사용하는 LDAP 플러그인에 대한 설명은 7.1.5절 "LDAP과의 통합"에서 확인 가능합니다.

### 7.1.1.3. 사용자 작성 플러그인 인증 제공자(Custom-built plugin auth providers)

네이티브 또는 LDAP에 만족하지 않는 특정 요구 사항을 가진 클라이언트의 경우 Neo4j는 사용자 정의 통합을 빌드하기위한 플러그인 옵션을 제공합니다. 이 옵션은 Neo4j Professional Services와 협의하여 사용자 지정 배달의 일부로 사용하는 것이 좋습니다. 플러그인은 Developer Manual → Plugins에 설명되어 있습니다.

### 7.1.1.4. SSO를 위한 케르베로스 인증(Kerberos authentication with single sign-on)

LDAP, 기본 및 사용자 작성 제공자 외에도 Neo4j는 인증 및 단일 로그온을 위한 Kerberos를 지원합니다. Kerberos 지원은 Neo4j Kerberos 애드온을 통해 제공됩니다.
