---
layout: post
title: DBCP on Azure App Service Linux (MySQL)
date: 2021-03-25 18:15 +0530
---

Many Apache projects support interaction with a relational database. Creating a new connection for each user can be time consuming (often requiring multiple seconds of clock time), in order to perform a database transaction that might take milliseconds. Opening a connection per user can be unfeasible in a publicly-hosted Internet application where the number of simultaneous users can be very large. Accordingly, developers often wish to share a "pool" of open connections between all of the application's current users. The number of users actually performing a request at any given time is usually a very small percentage of the total number of active users, and during request processing is the only time that a database connection is required. The application itself logs into the DBMS, and handles any user account issues internally.

[DBCP DOCUMENTATION](https://commons.apache.org/proper/commons-dbcp/)

You can configure DBCP on Azure App Service Linux by following the below steps:  

- COPY tomcat from __/usr/local/tomcat__ to __/home/tomcat__:  
mkdir -p /home/tomcat  
cp -a /usr/local/tomcat/ /home/  

- Edit __/home/tomcat/conf/context.xml__:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--
  Licensed to the Apache Software Foundation (ASF) under one or more
  contributor license agreements.  See the NOTICE file distributed with
  this work for additional information regarding copyright ownership.
  The ASF licenses this file to You under the Apache License, Version 2.0
  (the "License"); you may not use this file except in compliance with
  the License.  You may obtain a copy of the License at

      http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License.
-->
<!-- The contents of this file will be loaded for each web application -->
<Context>

    <!-- Default set of monitored resources. If one of these changes, the    -->
    <!-- web application will be reloaded.                                   -->
    <WatchedResource>WEB-INF/web.xml</WatchedResource>

    <!-- In AppService we customize WatchedResource to /home/site/deployments/active -->
    <WatchedResource>/home/site/deployments/active</WatchedResource>

    <!-- Uncomment this to disable session persistence across Tomcat restarts -->
    <!-- In AppService we need to disable session persistence across Tomcat restarts-->
    <Manager pathname="" />

    <Resource name="mydatasource" type="javax.sql.DataSource"
                url="jdbc:mysql://gaukmysql.mysql.database.azure.com:3306/testdb?serverTimezone=UTC"
                driverClassName="com.mysql.cj.jdbc.Driver" username="gauk@gaukmysql"
                password="xxxxx" />

</Context>
```

- Upload the mysql connector JAR:

```bash
b48fe95eccb7:/home/tomcat/lib# ls mysql-connector-java-8.0.21.jar
mysql-connector-java-8.0.21.jar
```

__LINK:__ [https://dev.mysql.com/downloads/connector/j/](https://dev.mysql.com/downloads/connector/j/)

- You would also need to give permission to the user:

```sql
GRANT ALL PRIVILEGES ON testdb.* TO 'gauk'@'%' IDENTIFIED BY 'xxxxx';  
FLUSH PRIVILEGES;
```

That's all. :v:
