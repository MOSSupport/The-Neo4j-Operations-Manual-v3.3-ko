## 7.3. 통합 SSL framework                  

<div class="abstract">
	<p>이 절에서는 Neo4j에서 통신 채널 보안을 위한 SSL/TLS 통합에 대해 설명합니다. 
	</p>
</div>


### 7.3.1. SSL/TLS 에 대해서


Neo4j는 표준 SSL/TLS 기술을 사용하여 통신 채널의 보안을 지원합니다. SSL/TLS는 SSL/TLS를 간결하게하기 위해 SSL을 사용하는 것이 일반적이며 여기서는 구성에서 사용되는 방법입니다. 그러나 표준에서 현대적이고 안전한 버전은 모두 TLS이며 이는 Neo4j의 기본값이기도 합니다.


SSL을 통해 제공되는 보안은 다양한 수준의 무결성, 기밀성 및 인증을 제공하기 위해 다양한 구성을 통해 제공 될 수 있습니다. 다양한 암호 및 알고리즘이 키 교환, 암호화 및 메시지 다이제스트의 기반을 제공하는 반면 공개/개인 키 인프라는 인증의 기초를 제공합니다.


### 7.3.2. 용 어                     

다음 용어는 Neo4j의 SSL 지원과 관련이 있습니다:


##### 인증기관 - Certificate Authority (*CA*)

디지털 개체의 신원을 확인할 수 있는 전자 문서를 발행하는 신뢰할 수 있는 엔터티입니다. 이 용어는 일반적으로 세계적으로 인정되는 CA를 나타내지만 조직 내부에서 신뢰할 수있는 내부 CA도 포함 할 수 있습니다. 전자 문서는 디지털 [인증서](/security/ssl-framework.md/#인증서---certificate)입니다. 이들은 안전한 통신의 핵심 부분이며 [공개키 인프라](/security/ssl-framework.md/#공개키-인프라---public-key-infrastructure-pki)에서 중요한 역할을합니다.                

##### 인증 해지 목록 - Certificate Revocation List (*CRL*)

인증서가 손상된 경우 해당 인증서를 해지 할 수 있습니다. 이는 하나 또는 여러 파일에 있는 해지 된 인증서를 나열한 목록을 통해 수행됩니다. CRL은 항상 해당 인증서를 발급하는 [CA](/security/ssl-framework.md/#인증기관---certificate-authority-ca)에서 발급합니다.

##### 암호 - cipher

암호화 또는 암호 해독을 수행하기위한 알고리즘. Neo4j 통신의 가장 일반적인 구현에서 Java 플랫폼의 일부로 포함 된 암호를 암묵적으로 사용합니다. SSL 프레임 워크의 구성은 허용 된 암호를 명시적으로 선언 할 수도 있습니다.

##### 통신 채널

Neo4j 데이터베이스와 통신하기 위한 수단을 의미합니다. 가능한 채널은 아래와 같습니다.

- Bolt 클라이언트 트래픽
- HTTPS 클라이언트 트래픽
- 내부 클러스터 통신

##### 암호 객체

요소를 나타내는 단어 [개인 키](/security/ssl-framework.md/#개인-키), [인증서](/security/ssl-framework.md/#인증서---certificate) 그리고 [CRLS](/security/ssl-framework.md/#인증-해지-목록---certificate-revocation-list-crl)입니다.

##### 구성 매개변수

*neo4j.conf*의 특정 [ssl 정책](/security/ssl-framework.md/#ssl-정책)에 대해 정의 된 매개 변수입니다.

##### 인증서 - certificate

SSL 인증서는 신뢰할 수 있는 [인증기관 - CA](/security/ssl-framework.md/#인증기관---certificate-authority-ca)에서 발급합니다. 공개 키는 특정 수신자를 위한 메시지를 암호화 하기 위해 누구든지 획득하고 사용할 수 있습니다. 인증서는 일반적으로 *&lt;file name&gt;.crt* 라는 파일에 저장됩니다. 이것은 [공개 키](/security/ssl-framework.md/#공개-키)라고도 합니다.                

##### SSL 정책

Neo4j의 SSL 정책은 [디지털 인증서](/security/ssl-framework.md/#인증서---certificate)와 *neo4j.conf* 정의 된 일련의 구성 매개 변수로 구성됩니다. 

##### 개인 키

개인 키는 의도 된 수신자 만 암호화 된 메시지를 해독 할 수 있도록합니다. 개인 키는 일반적으로 *&lt;file name&gt;.key*라는 파일에 저장됩니다. 암호화 된 통신의 무결성을 보장하기 위해 개인 키를 보호하는 것이 중요합니다.              

##### 공개키 인프라 - Public Key Infrastructure (*PKI*)

[디지털 인증서](/security/ssl-framework.md/#인증서---certificate)를 생성, 관리, 배포, 사용, 저장 및 해지하고 [공개 키](/security/ssl-framework.md/#공개-키) 암호화를 관리하는 필요한 일련의 역할, 정책 및 절차를 얘기합니다.

##### 공개 키

공개 키는 특정 수신자를위한 메시지를 암호화하기 위해 누구든지 획득하고 사용할 수 있습니다. 이것은 [인증서](/security/ssl-framework.md/#인증서---certificate)라고도 합니다.

##### TLS version

[TLS protocol](/security/ssl-framework.md/#tls-protocol)의 버전입니다..                  

##### TLS protocol

컴퓨터 네트워크를 통해 통신 보안을 제공하는 암호화 프로토콜입니다. TLS(Transport Layer Security) 프로토콜과 이전 버전 인 SSL(Secure Sockets Layer) 프로토콜은 모두 "SSL"이라고도합니다.

### 7.3.3. 통신 채널                     

Neo4j 플랫폼에는 다음과 같은 통신 채널 또는 채널 그룹이 있습니다.

- Bolt 클라이언트 트래픽
- HTTPS 클라이언트 트래픽
- 내부 클러스터 통신과 HTTPS 클라이언트 트래픽

이들은 필요한 보안 실무에 따라 서로 독립적으로 보안 될 수 있습니다.

### 7.3.4. 정책 정의                     

Neo4j의 SSL 지원은 SSL 인증서와 매개 변수 집합으로 구성되는 [SSL 정책](/security/ssl-framework.md/#ssl-정책)을 만들어 활성화 할 수 있습니다. 이러한 정책은 다양한 [통신 채널](/security/ssl-framework.md/#통신-채널)에 적용될 수 있습니다.

정책은 *neo4j.conf*에서 `policy-name`을 자유롭게 선택할 수있는 `dbms.ssl.policy.<policy-name>.<setting-suffix>` 그룹 설정에서 구성되며 유효한 값 `setting-suffix`에 대한 설명은 다음과 같습니다. 정책은 다음 매개 변수를 사용하여 구성됩니다.
  

| 접미사설정       | 설 명                              | 기본값                             |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| `base_directory`     | 암호화 객체가 기본적으로 검색되는 기본 디렉터리입니다. | 기본값이 없습니다. 이 값은 새 정책을 정의하기 위해 제공되어야합니다.  |
| `private_key`        | 이 인스턴스를 인증하고 보안하는 데 사용되는 개인 키입니다. | *private.key*                            |
| `public_certificate` | 인증 기관 (CA)이 서명 한 개인 키와 일치하는 공용 인증서입니다. | *public.crt*                             |
| `trusted_dir`        | 신뢰할 수있는 당사자의 인증서로 채워지는 디렉터리입니다. | *trusted*                                |
| `revoked_dir`        |  인증서 해지 목록 (CRLs)이 채워진 디렉터리입니다. | *revoked*                                |

유일한 필수 설정은 `dbms.ssl.policy.<policy-name>.base_directory`에 의해 정의 된 기본 디렉터리입니다. 기본 디렉터리를 정의함으로써, *암묵적*으로 Neo4j에 &lt;policy-name&gt; 이라는 이름으로 정책을 정의하도록 지시합니다. 이 정책에 대한 다른 설정이 정의되지 않은 경우, Neo4j는 기본적으로 *trusted* 및 *revoked*라는 두 개의 하위 디렉터리뿐만 아니라 기본 디렉터리 내에서 개인 키와 인증서 파일을 찾습니다. 다른 경로가 선호되는 경우 모든 기본값을 무시할 수 있습니다. 보안상의 이유로 Neo4j는 이러한 디렉터리를 자동으로 생성하지 않습니다. 따라서 SSL 정책을 작성하려면 적절한 파일 시스템 구조를 수동으로 설정해야합니다. 인증서 파일 및 개인 키의 존재는 물론 디렉터리의 존재가 필수임을 유의하십시오. Neo4j 사용자만 읽을 수 있도록 개인 키에 올바른 사용 권한이 설정되어 있는지 확인하십시오.

또한 정책에 대해 다음 매개 변수를 구성 할 수 있습니다.

| 접미사설정         | 설 명                              | 기본                                  |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| `client_auth`          | 클라이언트를 인증 여부. 이를 'REQUIRE'로 설정하면 실제로 서버에 대한 상호 인증이 가능해집니다. 이 설정에 사용할 수있는 값은 `NONE`, `OPTIONAL` 또는 `REQUIRE` 입니다. | `REQUIRE`                                |
| `ciphers`              | 암호 교환에 허용되는 암호 목록입니다. | Java 플랫폼의 기본 cipher suites. |
| `tls_versions`         | 지원 될 TLS / SSL 프로토콜 버전 목록입니다. | `TLSv1.2`                                |
| `allow_key_generation` | 이 매개 변수를 기본값인 `false`로 유지하는 것을 강력히 권장됩니다. `true`로 설정이 되면 시작할 때 자동으로 *.key*/*.crt* 파일 쌍이 생성이 됩니다. 또한 필요한 디렉터리 구조가 자동으로 생성됩니다.  | `false`                                  |
| `trust_all`            | 이 매개 변수를 기본값인 `false`로 유지하는 것을 강력히 권장됩니다. `true`로 설정하면 "아무나 신뢰"하고 기본적인 인증이 비활성화 됩니다. | `false`                                  |

Neo4j와 자바 플랫폼의 결합은 강력한 cipher suites와 프로토콜을 제공 할 것 입니다.

<div class="example">
예제 7.24. 정책 정의

<div class="example-contents">

이 예제에서 우리는 `example_policy`라는 정책에 대한 설정을 정의하고 생성합니다. 가능한 가장 단순한 구성으로, <i>neo4j.conf</i>에 이 정책의 기본 디렉터리를 정의합니다.:                  
<p>
<code>
dbms.ssl.policy.example_policy.base_directory=certificates/example_policy
</code>
<p>
그런 다음 필수 디렉터리를 만듭니다.
<p>

<pre>
<code>
$neo4j-home> mkdir certificates/example_policy
$neo4j-home> mkdir certificates/example_policy/trusted
$neo4j-home> mkdir certificates/example_policy/revoked
</code>
</pre>


마지막으로 <i>private.key</i> 및 <i>public.crt</i> 파일을 기본 디렉터리에 놓습니다. 다음과 같은 목록을 갖습니다:


<pre>
$neo4j-home> ls certificates/example_policy
-r-------- ... private.key
-rw-r--r-- ... public.crt
drwxr-xr-x ... revoked
drwxr-xr-x ... trusted
</pre>
</div>
</div>


### 7.3.5. SSL 정책 적용                     

통신 채널 또는 채널 그룹은 SSL정책 이름에 결합하여 적용되는 SSL 정책을 얻는다. Causal 클러스터, Bolt 및 HTTPS는 `causal_clustering.ssl_policy`, `bolt.ssl_policy` 및 `https.ssl_policy`를 구성 설정을 사용하여 SSL정책으로 구성 할 수 있습니다.


<div class="example">
예제 7.25. SSL 정책 적용
<div class="example-contents">
다음 예제는 Causal 클러스터가 <code>example_policy</code>라는 정책을 사용하도록 설정합니다:

<code>
causal_clustering.ssl_policy=example_policy
</code>
</div>
</div>

### 7.3.6. 인증서 형식                    

모든 인증서는 PEM 형식이어야하며 하나의 파일로 결합 될 수 있습니다. 개인 키는 PEM 형식이어야 합니다. 다중 호스트 및 와일드 카드 인증서가 지원됩니다. Neo4j가 다른 인터페이스에 바인드하는 다중 커넥터로 구성된 경우 이러한 인증서가 필요합니다.          

### 7.3.7. 레거시 SSL 시스템                     

Neo4j의 이전버전에서는 *레거시 SSL시스템*이라고 불리는 Bolt 및 HTTPS에 대해서 다른 시스템에서 SSL지원을 사용했습니다. 이 레거시 시스템은 향후 어느 시점에서 더 이상 사용되지 않을 것으로 예상되므로 [표준 SSL 구성](/security/ssl-framework.md)을 사용하는 것이 좋습니다. 레거시 정책은 특수한 `legacy` 정책 이름으로 SSL 프레임워크에서 사용 할 수 있지만, 프레임워크의 완전한 유연성을 허용하지 않습니다.        

Neo4j로 레거시 SSL 시스템을 구성하려면 개인 키와 인증서를 *neo4j.key* 및 *neo4j.cert* 파일에 각각 저장해야 합니다. 키는 암호화되지 않아야 합니다. 파일을 지정된 디렉터리에 위치하십시오. 기본 디렉터리 이름은 *certificates*이며 *neo4j-home* 에 위치합니다. 이 디렉터리는 또한 *neo4j.conf*의 [dbms.directories.certificates](https://neo4j.com/docs/operations-manual/current/reference/configuration-settings/#config_dbms.directories.certificates)를 사용하여 명시적으로 구성할 수도 있습니다.

인증서를 설치하지 않고 시작하면 Neo4j 프로세스는 자동으로 자체 서명된 SSL 인증서와 개인 키를 기본 디렉터리에 생성합니다. Neo4j가 다른 IP 주소에 바인드하는 여러 [커넥터](/configuration/connectors.md)로 구성된 경우 자체 서명된 SSL 인증서의 자동 생성을 사용하면 작동하지 않습니다. 여러 IP 주소를 사용해야 하는 경우 수동으로 인증서를 구성하고 대신 다중 호스트 또는 와일드카드 인증서를 사용하십시오.            

레거시 SSL 시스템은 본질적으로 다음 SSL 정책 정의와 같습니다:

```
bolt.ssl_policy=legacy
https.ssl_policy=legacy

dbms.ssl.policy.legacy.base_directory=certificates
dbms.ssl.policy.legacy.private_key=neo4j.key
dbms.ssl.policy.legacy.public_certificate=neo4j.cert
dbms.ssl.policy.legacy.allow_key_generation=true
dbms.ssl.policy.legacy.client_auth=NONE
dbms.ssl.policy.legacy.tls_versions="TLSv1.2, TLSv1.1, TLSv1"
```

HTTPS 및 Bolt 서버는 클라이언트 인증 (일명. *상호 인증*)을 지원하지 않습니다. 결과적으로 HTTPS와 Bolt 서버를 새로운 SSL 정책으로 마이그레이션하는 동안 `client_auth=NONE` 을 사용하여 `client_auth` 를 명시적으로 꺼야 합니다. 클라이언트 인증이 사용 불가능할 때 `trusted_dir`, `revoked_dir` 또는`trust_all`에 할당된 값은 클라이언트 인증에서 사용되는 설정이므로 무시됩니다.

`tls_versions` 와 `ciphers` 설정은 HTTPS와 Bolt 서버에서 지원됩니다. `legacy` 정책은 자바 플랫폼이 지원하는 TLS 버전과 cipher suites로 기본 설정됩니다.