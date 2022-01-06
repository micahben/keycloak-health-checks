# Keycloak Health Checks

A collection of health-checks for Keycloak subsystems.

## Supported Checks

1. Filesystem (Instance Level)
1. Database (Instance Level)
1. Infinispan Cluster state (Instance Level)
1. LDAP User Federation (Realm Level)

## Requirements

* KeyCloak 16.1.0

## Build

`mvn install`

## Installation

After the extension has been built, install it as a JBoss/WildFly module via `jboss-cli`:

```

$ bin/jboss-cli.sh 
You are disconnected at the moment. Type 'connect' to connect to the server or 'help' for the list of supported commands.
[disconnected /] connect
[standalone@localhost:9990 /] 

[standalone@localhost:9990 /] module add --name=com.github.thomasdarimont.keycloak.extensions.keycloak-health-checks --resources=/home/tom/dev/repos/gh/thomasdarimont/keycloak-dev/keycloak-health-checks/target/keycloak-health-checks.jar --dependencies=org.keycloak.keycloak-core,org.keycloak.keycloak-services,org.keycloak.keycloak-server-spi,org.keycloak.keycloak-server-spi-private,org.keycloak.keycloak-ldap-federation,org.keycloak.keycloak-kerberos-federation,org.jboss.resteasy.resteasy-jaxrs,org.apache.httpcomponents,com.google.guava,javax.api,javax.ws.rs.api,com.fasterxml.jackson.core.jackson-core,com.fasterxml.jackson.core.jackson-databind,com.fasterxml.jackson.core.jackson-annotations,org.jboss.logging,org.infinispan,org.infinispan.commons
```

Alternatively, create `$KEYCLOAK_HOME/modules/com/github/thomasdarimont/keycloak/extensions/keycloak-health-checks/main/module.xml` and copy the .jar file next to the module.xml file:

```xml
<?xml version='1.0' encoding='UTF-8'?>

<module xmlns="urn:jboss:module:1.1" name="com.github.thomasdarimont.keycloak.extensions.keycloak-health-checks">

    <resources>
        <resource-root path="keycloak-health-checks.jar"/>
    </resources>

    <dependencies>
        <module name="org.keycloak.keycloak-core"/>
        <module name="org.keycloak.keycloak-services"/>
        <module name="org.keycloak.keycloak-server-spi"/>
        <module name="org.keycloak.keycloak-server-spi-private"/>
        <module name="org.keycloak.keycloak-ldap-federation"/>
        <module name="org.keycloak.keycloak-kerberos-federation"/>
        <module name="org.jboss.resteasy.resteasy-jaxrs"/>
        <module name="org.apache.httpcomponents"/>
        <module name="com.google.guava"/>
        <module name="javax.api"/>
        <module name="javax.ws.rs.api"/>
        <module name="com.fasterxml.jackson.core.jackson-core"/>
        <module name="com.fasterxml.jackson.core.jackson-databind"/>
        <module name="com.fasterxml.jackson.core.jackson-annotations"/>
        <module name="org.jboss.logging"/>
        <module name="org.infinispan"/>
        <module name="org.infinispan.commons"/>
    </dependencies>
</module>
```

## Configuration

Edit the wildfly `standalone.xml` or `standalone-ha.xml`
`$KEYCLOAK_HOME/standalone/configuration/standalone.xml`:

```xml
...
        <subsystem xmlns="urn:jboss:domain:keycloak-server:1.1">
            <web-context>auth</web-context>
            <providers>
                <provider>
                    classpath:${jboss.home.dir}/providers/*
                </provider>
                <provider>module:com.github.thomasdarimont.keycloak.extensions.keycloak-health-checks</provider>
            </providers>
...
```

... or register the provider via the module via `jboss-cli`:
```
/subsystem=keycloak-server:list-add(name=providers,value=module:com.github.thomasdarimont.keycloak.extensions.keycloak-health-checks)
```

## Uninstall

To uninstall the provider just remove the ... from `standalone.xml` or `standalone-ha.xml`.
To uninstall the module just remove the `com/github/thomasdarimont...` directory in your `modules` folder.

## Disabling a Health-Check

A healh-check can be disabled via the jboss-cli.

To disable the `filesystem-health` check, one can use the following command:
```
/subsystem=keycloak-server/spi=health/:add
/subsystem=keycloak-server/spi=health/provider=filesystem-health/:add(enabled=false)
```

## Running example

Start Keycloak and browse to: `http://localhost:8080/auth/realms/master/health/check`

You should now see something like with `HTTP Status 200 OK`

```
curl -v http://localhost:8080/auth/realms/master/health/check | jq -C .
...
< HTTP/1.1 200 OK
< Connection: keep-alive
< Content-Type: application/json
< Content-Length: 1090
< Date: Wed, 06 Feb 2019 19:09:42 GMT
```

```json
{
  "details": {
    "database": {
      "connection": "established",
      "state": "UP"
    },
    "filesystem": {
      "freebytes": 425570316288,
      "state": "UP"
    },
    "infinispan": {
      "clusterName": "ejb",
      "healthStatus": "HEALTHY",
      "numberOfNodes": 1,
      "nodeNames": [
        "neumann"
      ],
      "cacheDetails": [
        {
          "cacheName": "realms",
          "healthStatus": "HEALTHY"
        },
        {
          "cacheName": "authenticationSessions",
          "healthStatus": "HEALTHY"
        },
        {
          "cacheName": "sessions",
          "healthStatus": "HEALTHY"
        },
        {
          "cacheName": "authorizationRevisions",
          "healthStatus": "HEALTHY"
        },
        {
          "cacheName": "work",
          "healthStatus": "HEALTHY"
        },
        {
          "cacheName": "clientSessions",
          "healthStatus": "HEALTHY"
        },
        {
          "cacheName": "keys",
          "healthStatus": "HEALTHY"
        },
        {
          "cacheName": "users",
          "healthStatus": "HEALTHY"
        },
        {
          "cacheName": "loginFailures",
          "healthStatus": "HEALTHY"
        },
        {
          "cacheName": "authorization",
          "healthStatus": "HEALTHY"
        },
        {
          "cacheName": "offlineClientSessions",
          "healthStatus": "HEALTHY"
        },
        {
          "cacheName": "realmRevisions",
          "healthStatus": "HEALTHY"
        },
        {
          "cacheName": "offlineSessions",
          "healthStatus": "HEALTHY"
        },
        {
          "cacheName": "actionTokens",
          "healthStatus": "HEALTHY"
        },
        {
          "cacheName": "userRevisions",
          "healthStatus": "HEALTHY"
        }
      ],
      "state": "UP"
    },
    "ldap": {
      "ldapStatus": {
        "ldap1": {
          "providerName": "ldap1",
          "status": "OK"
        }
      },
      "state": "UP"
    }
  },
  "name": "keycloak",
  "state": "UP"
}
```

In case a check fails, you should get a response with `HTTP Status 503 SERVICE UNAVAILABLE` with a body like:
```json
{
   "details":{
      "filesystem":{
         "state":"UP"
      },
      "database":{
         "message":"javax.resource.ResourceException: IJ000453: Unable to get managed connection for java:jboss/datasources/KeycloakDS",
         "state":"DOWN"
      },
      "infinispan": {
         "numberOfNodes": 1,
         "state": "UP",
         "healthStatus": "HEALTHY",
         "nodeNames": [],
         "cacheDetails": [
         {
           "cacheName": "realms",
           "healthStatus": "HEALTHY"
         },
         {
           "cacheName": "authenticationSessions",
           "healthStatus": "HEALTHY"
         },
         {
           "cacheName": "sessions",
           "healthStatus": "HEALTHY"
         },
         {
           "cacheName": "authorizationRevisions",
           "healthStatus": "HEALTHY"
         },
         {
           "cacheName": "clientSessions",
           "healthStatus": "HEALTHY"
         },
         {
           "cacheName": "work",
           "healthStatus": "HEALTHY"
         },
         {
           "cacheName": "keys",
           "healthStatus": "HEALTHY"
         },
         {
           "cacheName": "users",
           "healthStatus": "HEALTHY"
         },
         {
           "cacheName": "loginFailures",
           "healthStatus": "HEALTHY"
         },
         {
           "cacheName": "offlineClientSessions",
           "healthStatus": "HEALTHY"
         },
         {
           "cacheName": "authorization",
           "healthStatus": "HEALTHY"
         },
         {
           "cacheName": "realmRevisions",
           "healthStatus": "HEALTHY"
         },
         {
           "cacheName": "offlineSessions",
           "healthStatus": "HEALTHY"
         },
         {
           "cacheName": "actionTokens",
           "healthStatus": "HEALTHY"
         },
         {
           "cacheName": "userRevisions",
           "healthStatus": "HEALTHY"
         }],
         "clusterName": "ISPN"
        }
   },
   "ldap": {
     "ldapStatus": {
       "ldap1": {
         "providerName": "ldap1",
         "status": "ERROR",
         "errorMessage": "LDAP Query failed",
         "hint": "Connection refused (Connection refused): localhost:13891"
       }
     },
     "state": "DOWN"
   },
   "name":"keycloak",
   "state":"DOWN"
}
```

You can also query the health-checks individually by appending the name of the check to the end of `/health` endpoint URL. 

The following health-checks are currently available:
* `database`
* `filesystem` 
* `infinispan` 

```
$ curl -s http://localhost:8080/auth/realms/master/health/check/database | jq -C .
{
  "state": "UP",
  "details": {
    "connection": "established",
    "state": "UP"
  },
  "name": "database"
}

```

## Securing the health endpoint

The health endpoint should not be directly exposed to the internet. There are multiple ways to properly secure Keycloak endpoints
like firewalls, reverse-proxies, or JBoss / wildfly specific configuration options.

The keycloak documentation provides additional information about [securing admin endpoints](https://github.com/keycloak/keycloak-documentation/blob/master/server_admin/topics/threat/admin.adoc#port-restriction). The same mechanism
can be used to protect the health-endpoints. 
