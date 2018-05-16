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

암호화 또는 암호 해독을 수행하기위한 알고리즘. Neo4j 통신의 가장 일반적인 구현에서 Java 플랫폼의 일부로 포함 된 암호를 암시적으로 사용합니다. SSL 프레임 워크의 구성은 허용 된 암호를 명시적으로 선언 할 수도 있습니다.

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

SSL 인증서는 신뢰할 수 있는 [인증기관 - CA](/security/ssl-framework.md/#인증기관---certificate-authority-ca)에서 발급합니다. 공개 키는 특정 수신자를 위한 메시지를 암호화 하기 위해 누구든지 획득하고 사용할 수 있습니다. 인증서는 일반적으로 *<file name>.crt* 라는 파일에 저장됩니다. 이것은 [공개 키](/security/ssl-framework.md/#공개-키)라고도 합니다.                

##### SSL 정책

Neo4j의 SSL 정책은 [디지털 인증서](/security/ssl-framework.md/#인증서---certificate)와 *neo4j.conf* 정의 된 일련의 구성 매개 변수로 구성됩니다. 

##### 개인 키

개인 키는 의도 된 수신자 만 암호화 된 메시지를 해독 할 수 있도록합니다. 개인 키는 일반적으로 *<file name>.key*라는 파일에 저장됩니다. 암호화 된 통신의 무결성을 보장하기 위해 개인 키를 보호하는 것이 중요합니다.              

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
| `base_directory`     | 암호화 객체가 기본적으로 검색되는 기본 디렉토리입니다. | 기본값이 없습니다. 이 값은 새 정책을 정의하기 위해 제공되어야합니다.  |
| `private_key`        | 이 인스턴스를 인증하고 보안하는 데 사용되는 개인 키입니다. | *private.key*                            |
| `public_certificate` | 인증 기관 (CA)이 서명 한 개인 키와 일치하는 공용 인증서입니다. | *public.crt*                             |
| `trusted_dir`        | 신뢰할 수있는 당사자의 인증서로 채워지는 디렉토리입니다. | *trusted*                                |
| `revoked_dir`        |  인증서 해지 목록 (CRLs)이 채워진 디렉토리입니다. | *revoked*                                |

The only mandatory setting is the base directory defined by `dbms.ssl.policy.<policy-name>.base_directory`.               By defining the base directory, we *implicitly* tell Neo4j to define a policy with the name <policy-name>.               If no other settings for this policy have been defined, Neo4j will by default be looking for the private key and the certificate               file inside the base directory, as well as two subdirectories called *trusted* and *revoked*.               If other paths are preferred, all the default values can be overridden.               For reasons of security Neo4j will not attempt to automatically create any of these directories.               The creation of an SSL policy therefore requires the appropriate file system structure to be set up manually.               Note that the existence of the directories is mandatory, as well as the presence of the certificate file and the private key.               Ensure correct permissions are set on the private key, such that only the Neo4j user can read it.            

Additionally, the following parameters can be configured for a policy:

| 접미사설정         | 설 명                              | 기본                                  |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| `client_auth`          | Whether or not clients must be authenticated.                                                              Setting this to `REQUIRE` effectively enables mutual authentication for servers.                                                              Available values to given to this setting are `NONE`, `OPTIONAL`, or `REQUIRE`. | `REQUIRE`                                |
| `ciphers`              | A list of ciphers which will be allowed during cipher negotiation. | Java platform default allowed cipher suites |
| `tls_versions`         | A list of TLS/SSL protocol versions which will be supported. | `TLSv1.2`                                |
| `allow_key_generation` | It is *strongly recommended* to keep this parameter at its default value of `false`.                                                              If set to `true`, it will enable the auto-generation of a *.key*/*.crt* file pair on startup.                                                              Additionally, the required directory structure will be generated automatically. | `false`                                  |
| `trust_all`            | It is *strongly recommended* to keep this parameter at its default value of `false`.                                                              Setting it to `true` means "trust anyone" and essentially disables authentication. | `false`                                  |

The combination of Neo4j and the Java platform will provide strong cipher suites and protocols.

Example 7.24. Define a policy

In this example we will define and create configuration for a policy called `example_policy`.                     As the simplest configuration possible, we define the base directory for this policy in  *neo4j.conf*:                  

```
dbms.ssl.policy.example_policy.base_directory=certificates/example_policy
```

Then create the mandatory directories:

```
$neo4j-home> mkdir certificates/example_policy
$neo4j-home> mkdir certificates/example_policy/trusted
$neo4j-home> mkdir certificates/example_policy/revoked
```

Finally place the files *private.key* and *public.crt* into the base directory.                     We will have the following listing:                  

```
$neo4j-home> ls certificates/example_policy
-r-------- ... private.key
-rw-r--r-- ... public.crt
drwxr-xr-x ... revoked
drwxr-xr-x ... trusted
```

### 7.3.5. Applying SSL policies                     

A communication channel or group of channels gets an SSL policy applied by binding it to an SSL policy name.               Causal Clustering, Bolt and HTTPS can be configured with SSL policies, using the configuration settings `causal_clustering.ssl_policy`, `bolt.ssl_policy` and `https.ssl_policy`.            

Example 7.25. Apply an SSL policy

The following example will configure a causal cluster to use the policy named `example_policy`:                  

```
causal_clustering.ssl_policy=example_policy
```

### 7.3.6. Certificate formats                     

All certificates need to be in the PEM format, and they can be combined into one file.               The private key is also required to be in the PEM format.               Multi-host and wildcard certificates are supported.               Such certificates are required if Neo4j has been configured with multiple connectors that bind to different interfaces.            

### 7.3.7. Legacy SSL system                     

In previous versions of Neo4j, SSL support for Bolt and HTTPS was provided using a different system, which we here call the               *legacy SSL system*.               It is expected that this legacy system is to be deprecated at some point in the future, so it is recommended to use the [standard SSL configuration](https://neo4j.com/docs/operations-manual/current/security/ssl-framework/) instead.               The legacy policy is available in the SSL framework under the special `legacy` policy name, but it does not allow the full flexibility of the framework.            

In order to configure the legacy SSL system with Neo4j, you must have your private key and certificate in files named *neo4j.key* and *neo4j.cert*, respectively.               Note that the key should be unencrypted.               Place the files into the assigned directory.               The default is a directory named *certificates*, which is located in the *neo4j-home* directory.               The directory can also be configured explicitly using [dbms.directories.certificates](https://neo4j.com/docs/operations-manual/current/reference/configuration-settings/#config_dbms.directories.certificates) in *neo4j.conf*.            

If started without any certificates installed, the Neo4j process will automatically generate a self-signed SSL certificate               and a private key in the default directory.               Using auto-generation of self-signed SSL certificates will not work if Neo4j has been configured with multiple [connectors](https://neo4j.com/docs/operations-manual/current/configuration/connectors/) that bind to different IP addresses.               If you need to use multiple IP addresses, please configure certificates manually and use multi-host or wildcard certificates               instead.            

The Legacy SSL system is essentially equivalent to the following SSL policy definition:

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

The HTTPS and Bolt servers do not support client authentication (a.k.a. *mutual authentication*).               As a result, `client_auth` has to be turned off explicitly by having `client_auth=NONE` while migrating HTTPS and Bolt servers to the new ssl policy.               When client authentication is disabled, values assigned to `trusted_dir`, `revoked_dir` or `trust_all` will be ignored as they are settings used in client authentication.            

The `tls_versions` and `ciphers` settings are supported in HTTPS and Bolt servers.               The `legacy` policy defaults to the TLS versions and cipher suites supported by the Java platform.            