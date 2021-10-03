---
layout: post
published: true
title: oracle jdbc drivers finally in maven central
date: '2020-03-22'
subtitle:
summary: finally oracle drivers are in maven central
---

finally oracle drivers are in maven central.

[https://repo1.maven.org/maven2/com/oracle/database/](https://repo1.maven.org/maven2/com/oracle/database/)

 com.oracle.database
 - ojdbc[N].jar
 - ucp.jar
 - ojdbc[N]dms.jar

 oracle.database.jdbc.debug
 - oraclepki.jar
 - osdt_cert.jar
 - osdt_core.jar

 oracle.database.ha
 - ons.jar
 - simplefan.jar

 oracle.database.nls
 - orai18n.jar

 oracle.database.xml
 - xdb.jar, xdb6.jar
 - xmlparserv2.jar

 sample maven dependency
 ```xml
 <dependency>
    <groupId>com.oracle.database.jdbc</groupId>
    <artifactId>ojdbc10</artifactId>
    <version>19.3.0.0</version>
</dependency>
```

**note that db6.jar is a legacy name, xdb.jar is the new name**