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

디지털 개체의 신원을 확인할 수 있는 전자 문서를 발행하는 신뢰할 수 있는 엔터티입니다. 이 용어는 일반적으로 세계적으로 인정되는 CA를 나타내지만 조직 내부에서 신뢰할 수있는 내부 CA도 포함 할 수 있습니다. 전자 문서는 디지털 [인증서](/security/ssl-framework.md/#certificate)입니다. 이들은 안전한 통신의 핵심 부분이며 [Public Key Infrastructure](/security/ssl-framework.md/#Public-Key-Infrastructure-PKI)에서 중요한 역할을합니다.                

##### Certificate Revocation List (*CRL*)

In the event of a certificate being compromised, that certificate can be revoked.                     This is done by means of a list (located in one or several files) spelling out which certificates are revoked.                     The CRL is always issued by the [CA](https://neo4j.com/docs/operations-manual/current/security/ssl-framework/#term-ssl-certificate-authority) which issues the corresponding certificates.                  

##### cipher

    An algorithm for performing encryption or decryption.                     In the most general implementation of encryption of Neo4j communications, we make implicit use of ciphers that are included                     as part of the Java platform.                     The configuration of the SSL framework also allows for the explicit declaration of allowed ciphers.                  

##### communication channel

                         A means for communicating with the Neo4j database.                        Available channels are:                                                                                             Bolt client traffic                           HTTPS client traffic                           intra-cluster communication                                                               

##### cryptographic objects

    A term denoting the artifacts [private keys](https://neo4j.com/docs/operations-manual/current/security/ssl-framework/#term-ssl-private-key), [certificates](https://neo4j.com/docs/operations-manual/current/security/ssl-framework/#term-ssl-certificate) and [CRLs](https://neo4j.com/docs/operations-manual/current/security/ssl-framework/#term-ssl-certificate-revocation-list).                  

##### configuration parameters

    These are the parameters defined for a certain [ssl policy](https://neo4j.com/docs/operations-manual/current/security/ssl-framework/#term-ssl-policy) in *neo4j.conf*.                  

##### certificate

    SSL certificates are issued by a trusted [certificate authority (*CA*)](https://neo4j.com/docs/operations-manual/current/security/ssl-framework/#term-ssl-certificate-authority).                     The public key can be obtained and used by anyone to encrypt messages intended for a particular recipient.                     The certificate is commonly stored in a file named *<file name>.crt*.                     This is also referred to as the [public key](https://neo4j.com/docs/operations-manual/current/security/ssl-framework/#term-ssl-public-key).                  

##### SSL policy

    An SSL policy in Neo4j consists of  [a digital certificate](https://neo4j.com/docs/operations-manual/current/security/ssl-framework/#term-ssl-certificate) and a set of configuration parameters defined in *neo4j.conf*.                  

##### private key

    The private key ensures that encrypted messages can be deciphered only by the intended recipient.                     The private key is commonly stored in a file named *<file name>.key*.                     It is important to protect the private key to ensure the integrity of encrypted communication.                  

##### Public Key Infrastructure (*PKI*)

    A set of roles, policies, and procedures needed to create, manage, distribute, use, store, and revoke [digital certificates](https://neo4j.com/docs/operations-manual/current/security/ssl-framework/#term-ssl-certificate) and manage [public-key](https://neo4j.com/docs/operations-manual/current/security/ssl-framework/#term-ssl-public-key) encryption.                  

##### public key

    The public key can be obtained and used by anyone to encrypt messages intended for a particular recipient.                     This is also referred to as the [certificate](https://neo4j.com/docs/operations-manual/current/security/ssl-framework/#term-ssl-certificate).                  

##### TLS version

    A version of the [TLS protocol](https://neo4j.com/docs/operations-manual/current/security/ssl-framework/#term-ssl-tls-protocol).                  

##### TLS protocol

    The cryptographic protocol that provides communications security over a computer network.                     The Transport Layer Security (TLS) protocol and its predecessor, the Secure Sockets Layer (SSL) protocol are both frequently                     referred to as "SSL".                  

### 7.3.3. Communication channels                     

The Neo4j platform has the following communication channels or groups of channels:

-   Bolt client traffic
-   HTTPS client traffic
-   Intra-cluster communication and HTTPS client traffic

These can be secured independently from each other, according to required security practices.

### 7.3.4. Policy definition                     

SSL support in Neo4j is enabled by creating an [SSL policy](https://neo4j.com/docs/operations-manual/current/security/ssl-framework/#term-ssl-policy), which consists of an SSL certificate together with a set of parameters.               These policies can then be applied to the various [communication channels](https://neo4j.com/docs/operations-manual/current/security/ssl-framework/#term-ssl-channel).            

The policies are configured in *neo4j.conf* under a group-setting as `dbms.ssl.policy.<policy-name>.<setting-suffix>` where the `policy-name` can be chosen freely, and valid values for `setting-suffix` are described below.               A policy is configured using the following parameters:            

| Setting suffix       | Description                              | Default value                            |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| `base_directory`     | The base directory under which cryptographic objects are searched for by default. | No default. This value must be provided to define a new policy. |
| `private_key`        | The private key used for authenticating and securing this instance. | *private.key*                            |
| `public_certificate` | A public certificate matching the private key signed by a Certificate Authority (CA). | *public.crt*                             |
| `trusted_dir`        | A directory populated with certificates of trusted parties. | *trusted*                                |
| `revoked_dir`        | A directory populated with certificate revocation lists (CRLs). | *revoked*                                |

The only mandatory setting is the base directory defined by `dbms.ssl.policy.<policy-name>.base_directory`.               By defining the base directory, we *implicitly* tell Neo4j to define a policy with the name <policy-name>.               If no other settings for this policy have been defined, Neo4j will by default be looking for the private key and the certificate               file inside the base directory, as well as two subdirectories called *trusted* and *revoked*.               If other paths are preferred, all the default values can be overridden.               For reasons of security Neo4j will not attempt to automatically create any of these directories.               The creation of an SSL policy therefore requires the appropriate file system structure to be set up manually.               Note that the existence of the directories is mandatory, as well as the presence of the certificate file and the private key.               Ensure correct permissions are set on the private key, such that only the Neo4j user can read it.            

Additionally, the following parameters can be configured for a policy:

| Setting suffix         | Description                              | Default                                  |
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