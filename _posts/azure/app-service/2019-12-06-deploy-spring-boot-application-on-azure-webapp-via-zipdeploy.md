---
layout: post
title: Deploy Spring Boot Application On Azure WebApp via ZipDeploy
date: 2019-12-06 02:29 +0530
permalink: '/webappspringboot'
---

# Here is one of the simplest ways to deploy a Spring Boot application (JAR with embedded Tomcat) to Azure WebApp (Windows)

__Steps:__

- Create zip file which contains the JAR file and a web.config
- Here is a sample web.config which you can use:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
 <system.webServer>
  <handlers>
    <add name="httpPlatformHandler" path="*" verb="*"   modules="httpPlatformHandler" resourceType="Unspecified" />
  </handlers>
  <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\app.jar&quot;" 
      startupRetryCount='10'>
  </httpPlatform>
 </system.webServer>
</configuration>
```

- When using zip deploy, the contents of the zip gets transferred to D:\home\site\wwwroot of the WebApp

- Now, just call the Zip Deploy API to deploy your app.

__Example (using cURL):__

```curl
curl -X POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

![snapshot](/assets/zipdeploy.jpg)

__Documentation:__: [https://docs.microsoft.com/en-us/azure/app-service/deploy-zip#rest](https://docs.microsoft.com/en-us/azure/app-service/deploy-zip#rest)