## 7.1.1. Introduction 
This section provides an overview of authentication and authorization in Neo4j.
Security in Neo4j is controlled by authentication and authorization. Authentication is the process of ensuring that a user is who the user claims to be, while authorization pertains to checking whether the authenticated user is allowed to perform a certain action.

Authorization is managed using role-based access control (RBAC). The core of the Neo4j security model is centred around the four predefined graph-global data-access roles: reader, publisher, architect and admin. Each role includes a set of authorized actions permitted on the Neo4j data graph and its schema. A user can be assigned to none, one or more of these roles, as well as other custom roles.

Neo4j has supports multiple auth providers that can perform user authentication and authorization:

Native auth provider.
LDAP auth provider.
Custom-built plugin auth providers.
Kerberos authentication with single sign-on.

### 7.1.1.1. Native auth provider

Neo4j provides a native auth provider that stores user and role information locally on disk. With this option, full user management is available as procedures described in Section 7.1.4, “Native user and role management”.

### 7.1.1.2. LDAP auth provider

Another way of controlling authentication and authorization is through external security software such as Active Directory or OpenLDAP, which is accessed via the built-in LDAP connector. A description of the LDAP plugin using Active Directory is available in Section 7.1.5, “Integration with LDAP”.

### 7.1.1.3. Custom-built plugin auth providers

For clients with specific requirements not satisfied with either native or LDAP, Neo4j provides a plugin option for building custom integrations. It is recommended that this option is used as part of a custom delivery as negotiated with Neo4j Professional Services. The plugin is described in Developer Manual → Plugins.

### 7.1.1.4. Kerberos authentication and single sign-on

In addition to LDAP, Native and custom providers, Neo4j supports Kerberos for authentication and single sign-on. Kerberos support is provided via the Neo4j Kerberos Add-On.