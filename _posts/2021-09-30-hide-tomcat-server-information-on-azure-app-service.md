---
layout: post
title: Hide Tomcat Server Information on Azure App Service
date: 2021-09-30 17:10 +0530
---

### Hide Tomcat Server Information on Azure App Service

The default error page of Tomcat shows the Tomcat version:
![Tomcat-404]({{ site.baseurl }}/assets/tomcat-404.jpg)

You could hide the version information by adding a Valve to Tomcat's configuration file server.xml.

#### TOMCAT VALVES

> A Valve element represents a component that will be inserted into the request processing pipeline for the associated Catalina container (Engine, Host, or Context).

Know More: [Tomcat Valves](https://tomcat.apache.org/tomcat-7.0-doc/config/valve.html#Introduction)

Tomcat binaries on App Services are present in a read-only location, like: D:\Program Files\apache-tomcat-8.5.58. So, changing the configurations directly is not possible. However, you could follow the steps below to customize the configurations:

#### STEPS

- Go to the Kudu site of the WebApp: https://<<webappname>>.scm.azurewebsites.net/DebugConsole

- Run the following commands:

```bash
mkdir D:\home\tomcat\
cd D:\home\tomcat
cp -R "D:\Program Files\apache-tomcat-8.5.58\conf" .
```

- Add the following Valve in server.xml under D:\home\tomcat\conf -

```xml
<Valve className="org.apache.catalina.valves.ErrorReportValve" showReport="false" showServerInfo="false" />
```

It can be added in the following Host element:
[Keep the other elemnts as is]

```xml
...
<Host appBase="${site.appbase}" xmlBase="${site.xmlbase}" unpackWARs="${site.unpackwars}" workDir="${site.tempdir}" name="localhost" autoDeploy="true">
    <!-- SingleSignOn valve, share authentication between web applications
    Documentation at: /docs/config/valve.html -->
    <!--
<Valve className="org.apache.catalina.authenticator.SingleSignOn" />
-->
    <!-- Access log processes all example.
    Documentation at: /docs/config/valve.html
    Note: The pattern used is equivalent to using pattern="common" -->
    <Valve prefix="site_access_log.${catalina.instance.name}" pattern="%h %l %u %t &quot;%r&quot; %s %b %D %{x-arr-log-id}i" directory="${site.logdir}/http/RawLogs" className="org.apache.catalina.valves.AccessLogValve" suffix=".txt"/>
    <Valve className="org.apache.catalina.valves.ErrorReportValve" showReport="false" showServerInfo="false" />
</Host>
...
```

Now just restart the WebApp and you should not see the Tomcat Server version anymore.

![Tomcat-404-hidden]({{ site.baseurl }}/assets/tomcat-404-hidden.jpg)


#### NOTE

The recommended way to modify Tomcat Configuration files is by using XSLT, which is documented [HERE](https://docs.microsoft.com/en-us/azure/app-service/configure-language-java?pivots=platform-windows#transforms)

That's All! :v:

