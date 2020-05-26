---
layout: post
title: Custom JRE/JDK on Azure App Service - Windows
date: 2020-05-26 23:12 +0530
---

Here's how you can use Custom JRE/JDK for your Java Application deployed on Azure WebApp:

#### STEPS

- Upload your JRE/JDK via FTP or you can simply upload it via the Kudu Site. If you have a direct link to the zip file, you can use the curl command to download the zip directly on the WebApp:

Example:

![snapshot]({{ site.baseurl }}/assets/curl-jdk.png)

You can then extract the zip using the command: ```unzip file.zip```

- Create a web.config under D:\home\site\wwwroot with the following content:  

:exclamation: Modify the __JRE_HOME__ environment variable in web.config as per your configuration.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="httpplatformhandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified"/>
    </handlers>
    <httpPlatform processPath="D:\Program Files (x86)\apache-tomcat-8.5.37\bin\startup.bat">
      <environmentVariables>
        <environmentVariable name="JRE_HOME" value="D:\home\site\wwwroot\java11" />
      </environmentVariables>
    </httpPlatform>
  </system.webServer>
</configuration>
```

That's it!  :v:

You can verify that the WebApp is now using our Custom JDK by checking the __Properties__ of the Java Process from __Process Explorer__ tab in the kudu Console:

![snapshot]({{ site.baseurl }}/assets/kudu-java.png)

If you want more control over your application, you could also use a __Custom Tomcat!__  

Here is a Template which you can use: [Tomcat Template](https://github.com/azure-appservice-samples/TomcatTemplate)
