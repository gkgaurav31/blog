---
layout: post
title: Log Originating Client IP on Linux App Service (Tomcat)
date: 2021-09-25 00:39 +0530
---

### Log Originating Client IP on Linux App Service (Tomcat)

> The X-Forwarded-For (XFF) header is a de-facto standard header for identifying the originating IP address of a client connecting to a web server through an HTTP proxy or a load balancer. When traffic is intercepted between clients and servers, server access logs contain the IP address of the proxy or load balancer only. To see the original IP address of the client, the X-Forwarded-For request header is used.

[MDN Web Docs: X-Forwarded-For](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Forwarded-For)

We can get the value of this header using multiple ways. One of the common ways is via code. Here is a sample __Java Code__ you could use:

```java
    private static String getClientIp(HttpServletRequest request) {
        String remoteAddr = "";
        if (request != null) {
            remoteAddr = request.getHeader("X-FORWARDED-FOR");
            if (remoteAddr == null || "".equals(remoteAddr)) {
                remoteAddr = request.getRemoteAddr();
            }
        }
        return remoteAddr;
    }
```

__Credits:__ [Mkyong: How to get client Ip Address in Java](https://mkyong.com/java/how-to-get-client-ip-address-in-java/)

In this blog, I will be covering how this can be done by configuring Tomcat so that we can get it directly from the Access logs of Tomcat.

#### Steps

1. Go to the [Kudu](https://docs.microsoft.com/en-us/azure/app-service/resources-kudu) and use [SSH](https://docs.microsoft.com/en-us/azure/app-service/configure-linux-open-ssh-session) into your application container. [The app needs to be running for this to work]
https://<<webappname>>.scm.azurewebsites.net/webssh/host

2. Copy default Tomcat configurations to __/home__.  
```bash
mkdir -p /home/tomcat  
cp -R /usr/local/tomcat/conf /home/tomcat/
```

3. Edit the __server.xml__ under __/home/tomcat/conf__  
Replace the following:

```xml
      <!-- In AppService below is customized to add : unpackWARs="${site.unpackWARs}" and workDir="${site.tempdir}-->
      <Host name="localhost" appBase="${site.root}"
            unpackWARs="${site.unpackWARs}" autoDeploy="true" workDir="${site.tempdir}">

        <!-- SingleSignOn valve, share authentication between web applications
             Documentation at: /docs/config/valve.html -->
        <!--
        <Valve className="org.apache.catalina.authenticator.SingleSignOn" />
        -->

        <!-- Access log processes all example.
             Documentation at: /docs/config/valve.html
             Note: The pattern used is equivalent to using pattern="common" -->
        <!-- In AppService prefix and pattern are customized-->
        <Valve className="org.apache.catalina.valves.AccessLogValve" directory="${site.logdir}/http/RawLogs"
               prefix="site_access_log.${catalina.instance.name}" suffix=".txt"
               pattern="%h %l %u %t &quot;%r&quot; %s %b %D %{x-arr-log-id}i" />

      </Host>
```

with:

```xml
      <!-- In AppService below is customized to add : unpackWARs="${site.unpackWARs}" and workDir="${site.tempdir}-->
      <Host name="localhost" appBase="${site.root}"
            unpackWARs="${site.unpackWARs}" autoDeploy="true" workDir="${site.tempdir}">

        <!-- SingleSignOn valve, share authentication between web applications
             Documentation at: /docs/config/valve.html -->
        <!--
        <Valve className="org.apache.catalina.authenticator.SingleSignOn" />
        -->
        <!-- Remote IP Valve -->
        <Valve className="org.apache.catalina.valves.RemoteIpValve" />
        <!-- Access log processes all example.
             Documentation at: /docs/config/valve.html
             Note: The pattern used is equivalent to using pattern="common" -->
        <!-- In AppService prefix and pattern are customized-->
        <Valve className="org.apache.catalina.valves.AccessLogValve" directory="${site.logdir}/http/RawLogs"
               prefix="site_access_log.${catalina.instance.name}" suffix=".txt"
               pattern="%{X-Forwarded-For}i %l %u %t &quot;%r&quot; %s %b %D %{x-arr-log-id}i" />

      </Host>
```

Now, restart the WebApp and you should see the originating client IP in the access logs available under __/home/LogFiles/http/RawLogs__

![Access Logs]({{ site.baseurl }}/assets/clientip-tomcat.jpg)

#### Know More

- [RemoteIpValve](https://tomcat.apache.org/tomcat-8.5-doc/api/org/apache/catalina/valves/RemoteIpValve.html)
- [Basic configuration to handle 'x-forwarded-for'](https://tomcat.apache.org/tomcat-9.0-doc/config/filter.html#Basic_configuration_to_handle_'x-forwarded-for')  

That's it! :v:
